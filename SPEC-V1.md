# Portable Countdown V1

Status: Draft (Version 1)  
Container: JSON (UTF-8)  
File Extension: `.cntdwn` (recommended)  

## Overview

This document specifies a JSON format for exporting/importing countdowns. The core is intentionally small and app-neutral. Optional, namespaced vendor extensions allow apps to carry extra hints without breaking interoperability.

- All fields and objects defined here are **normative** unless marked "advisory".
- Parsers **MUST** gracefully handle unknown fields and unknown vendor namespaces.
- Timestamps **MUST** be encoded as `ISO-8601 / RFC-3339` strings

## Data Model (TypeScript)

```typescript
export type PortableCountdownV1 = {

	/** Version must be 1 */
	version: 1

	/**
	 * The unique countdown identifier.
	 *
	 * @example "2d49df56-4462-4ee0-acff-f670430e2750"
	 */
	id: string

	/**
	 * The creation date.
	 *
	 * @example "2025-03-15T12:00:00Z"
	 */
	createdAt: Date

	/**
	 * The last modification/update date.
	 *
	 * @example "2025-04-01T12:00:00Z"
	 */
	updatedAt?: Date

	/**
	 * The countdown data in the default language.
	 *
	 * This is used as a fallback when {@link PortableCountdownV1#localizedData} does not contain the requested language,
	 * and also as the default localization for when {@link PortableCountdownV1#localizedData} is not provided.
	 */
	data: PortableCountdownV1.Types.LocalizedData

	/**
	 * The countdown data localized into different languages.
	 *
	 * The key should be a BCP-47 language tag.
	 */
	localizedData?: Record<string, PortableCountdownV1.Types.LocalizedData>

	/**
	 * The target date for the countdown.
	 *
	 * JavaScript/TypeScript automatically encodes {@link Date} objects correctly, but for other languages,
	 * this **MUST** be a combined date-time string in ISO 8601 format.
	 *
	 * @example "2029-08-21T00:00:00Z"
	 */
	targetDate: Date

	/**
	 * The time zone of the countdown, if relevant.
	 *
	 * This **MUST** be an IANA time zone identifier.
	 *
	 * @example "America/New_York"
	 */
	timeZone?: string

	/**
	 * The source of the countdown, such as the tool used to create or export it.
	 *
	 * @example "CursedCountdowns"
	 */
	source?: string

	/**
	 * The author of the countdown.
	 *
	 * @example "SplittyDev"
	 */
	author?: string

	/**
	 * Vendor-specific extensions to the countdown.
	 *
	 * While there is no fixed format for the key, we highly encourage vendors to pick a
	 * uniquely identifiable key relevant to their application, such as `com.example.MyApp`.
	 */
	vendorExtensions: Record<string, unknown>
}

namespace PortableCountdownV1.Types {
	export type LocalizedData = {

		/**
		 * The countdown title.
		 *
		 * This **SHOULD** be shown as part of the countdown.
		 */
		title: string

		/**
		 * A short summary or subtitle.
		 *
		 * This **SHOULD** be shown as part of the countdown, but **CAN** be omitted
		 * if the available space or countdown design does not allow for it.
		 *
		 * For aesthetic reasons, and to maximize compatibility with apps, this **SHOULD** be a
		 * reasonably short string, such as a single sentence.
		 */
		summary?: string

		/**
		 * A detailed description of the countdown.
		 *
		 * The purpose of this is to provide additional information (usually as part of a detail view)
		 * and **SHOULD NOT** be shown as a regular part of the countdown interface.
		 */
		details?: string
	}
}
```
