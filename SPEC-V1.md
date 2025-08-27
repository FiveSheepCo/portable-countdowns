# Portable Countdown V1

| Key						| Value									|
| :------------------------ | :------------------------------------ |
| Status 					| Draft 								|
| Version					| 1 									|
| Container 				| JSON									|
| Encoding					| UTF-8									|
| File Extension			| `.cntdwn` (recommended)				|
| MIME Type					| `application/prs.portable-countdown`	|
| Uniform Type Identifier	| `co.fivesheep.portable-countdown`		|

## Abstract

This document specifies a JSON-based format for exporting/importing countdowns. The core is intentionally small and app-neutral. Optional, namespaced vendor extensions allow apps to carry extra hints without breaking interoperability.

## Terminology

The key words **“MUST”**, **“MUST NOT”**, **“REQUIRED”**, **“SHALL”**, **“SHALL NOT”**, **“SHOULD”**, **“SHOULD NOT”**, **“RECOMMENDED”**, **“MAY”**, and **“OPTIONAL”** in this document are to be interpreted as described in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119.html) and [RFC 8174](https://www.rfc-editor.org/rfc/rfc8174.html) when, and only when, they appear in all capitals, as shown here.

- **MUST / REQUIRED / SHALL**: This requirement is an absolute; implementations are not compliant if they ignore it.  
- **MUST NOT / SHALL NOT**: This is an absolute prohibition; implementations are not compliant if they do this.  
- **SHOULD / RECOMMENDED**: There may be valid reasons in particular circumstances to ignore this item, but the full implications must be understood and carefully weighed before choosing a different course.  
- **SHOULD NOT / NOT RECOMMENDED**: There may exist valid reasons in particular circumstances when the behavior is acceptable, but the full implications should be understood and the case carefully weighed before implementation.  
- **MAY / OPTIONAL**: This item is truly optional. An implementation can include it or leave it out, but it should remain interoperable either way.  

## Requirements

### Identifiers

- The `id` field **SHOULD** be encoded as a UUIDv4.

Example: `2d49df56-4462-4ee0-acff-f670430e2750`

### Timestamps

- Timestamp fields (such as `createdAt`, `updatedAt`, and `targetDate`) **MUST** be encoded as **ISO 8601** strings.

Example: `2029-08-21T00:00:00Z`

### Vendor Extensions

- Implementations **MUST** parse documents successfully even when `vendorExtensions` contains unknown keys. This ensures forward compatibility with new extensions.
- Implementations **SHOULD** preserve and re-serialize unknown keys within `vendorExtensions`. While not a strict requirement, this practice enhances interoperability between different applications by ensuring that vendor-specific data is not lost during processing.

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
	createdAt: string

	/**
	 * The last modification/update date.
	 *
	 * @example "2025-04-01T12:00:00Z"
	 */
	updatedAt?: string

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
	 * This **MUST** be a string in ISO 8601 format.
	 *
	 * @example "2029-08-21T00:00:00Z"
	 */
	targetDate: string

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
	vendorExtensions?: Record<string, unknown>
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

## JSON Schema

The following JSON Schema provides a formal definition of the `PortableCountdownV1` format. Implementations **SHOULD** use this schema to validate imported data.

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "Portable Countdown v1",
  "description": "A portable, JSON-based format for countdowns.",
  "type": "object",
  "properties": {
    "version": {
      "description": "The specification version.",
      "type": "integer",
      "const": 1
    },
    "id": {
      "description": "A unique identifier for the countdown, preferably a UUIDv4.",
      "type": "string"
    },
    "createdAt": {
      "description": "The creation timestamp in ISO 8601 format.",
      "type": "string",
      "format": "date-time"
    },
    "updatedAt": {
      "description": "The last modification timestamp in ISO 8601 format.",
      "type": "string",
      "format": "date-time"
    },
    "data": {
      "$ref": "#/definitions/LocalizedData"
    },
    "localizedData": {
      "description": "Localized countdown data, keyed by BCP-47 language tags.",
      "type": "object",
      "additionalProperties": {
        "$ref": "#/definitions/LocalizedData"
      }
    },
    "targetDate": {
      "description": "The target timestamp in ISO 8601 format.",
      "type": "string",
      "format": "date-time"
    },
    "timeZone": {
      "description": "The IANA time zone identifier.",
      "type": "string"
    },
    "source": {
      "description": "The source application or tool that created the countdown.",
      "type": "string"
    },
    "author": {
      "description": "The author of the countdown.",
      "type": "string"
    },
    "vendorExtensions": {
      "description": "An object for vendor-specific extensions.",
      "type": "object",
      "additionalProperties": true
    }
  },
  "required": [
    "version",
    "id",
    "createdAt",
    "data",
    "targetDate"
  ],
  "definitions": {
    "LocalizedData": {
      "type": "object",
      "properties": {
        "title": {
          "description": "The main title of the countdown.",
          "type": "string"
        },
        "summary": {
          "description": "A short summary or subtitle.",
          "type": "string"
        },
        "details": {
          "description": "A more detailed description.",
          "type": "string"
        }
      },
      "required": ["title"]
    }
  }
}
```
