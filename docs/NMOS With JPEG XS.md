# AMWA BCP-006-01: NMOS With JPEG XS
{:.no_toc}

* A markdown unordered list which will be replaced with the ToC, excluding the "Contents header" from above
{:toc}

_(c) AMWA 2021, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_

[JPEG XS][JPEG-XS] is a technology standardized in ISO/IEC 21122 for video contribution at low latency with very high video quality.
A companion RTP payload format specification was developed through the IETF Payloads working group, IETF [RFC 9134][RFC-9134].

The [Video Services Forum][VSF] developed Technical Recommendation [TR-08][], which covers the end-to-end application use of JPEG XS compression for video, alongside uncompressed audio and VANC, using the SMPTE ST 2110 suite of protocols.
TR-08 mandates the use of the AMWA [IS-04][] and [IS-05][] NMOS Specifications in TR-08 compliant systems.  

AMWA IS-04 and IS-05 already have support for RTP transport and can signal the media type `video/jxsv` as defined in RFC 9134.  

## Use of Normative Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY",
and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119][RFC-2119].

## Definitions

The NMOS terms 'Node', 'Source', 'Flow', 'Sender', 'Receiver' are used as defined in the [NMOS Glossary](https://specs.amwa.tv/nmos/main/docs/Glossary.html).

## JPEG XS IS-04 Receivers

Nodes capable of receiving JPEG XS video streams MUST have a Receiver resource in the IS-04 Node API, which lists `video/jxsv` in the `media_types` array within the `caps` object.
This has been permitted since IS-04 v1.1.

Nodes implementing [BCP-004-01][] Receiver Capabilities use the existing `constraint_sets` parameter within the `caps` object, describing combinations of frame rates, width and height, and other parameters which the receiver can support, using the parameter constraints defined in the [Capabilities register](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/) of the [NMOS Parameter Registers][].

Receivers are RECOMMENDED to use the following parameter constraints:

- [Frame Width](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#frame-width)
- [Frame Height](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#frame-width)
- [Color Sampling](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#color-sampling)
- [Component Depth](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#component-depth)
- [Bits Per Pixel](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#bits-per-pixel)
- [Profile](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#profile)

If the Receiver supports streams meeting the traffic shaping and delivery timing requirements for ST 2110-22, it SHOULD use the [ST 2110-21 Sender Type](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#st-2110-21-sender-type) parameter constraint.

An example Receiver resource is provided in the [Examples](../examples/).

## JPEG XS IS-04 Senders, Flows and Sources

Nodes capable of transmitting JPEG XS video streams MUST have Sender, Flow and Source resources in the IS-04 Node API.

### Senders

The Sender resource MUST indicate `urn:x-nmos:transport:rtp` or one of its subclassifications for the `transport` attribute.
Sender resources provide no indication of media type or format, since this is described by the associated Flow resource.

The SDP file at the `manifest_href` MUST comply with the requirements of RFC 9134.
If the Sender meets the traffic shaping and delivery timing requirements specified for ST 2110-22, the SDP file MUST also comply with the provisions of ST 2110-22.

### Flows

The Flow resource MUST indicate `video/jxsv` in the `media_type` attribute, and `urn:x-nmos:format:video` for the `format`.
This has been permitted since IS-04 v1.1.

Nodes implementing IS-04 v1.3 or higher MUST indicate the color (sub-)sampling in the Flow resource using the `components` attribute defined in the [Flow Attributes register](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#components) of the NMOS Parameter Registers.
The `components` array value corresponds to the `sampling`, `width` and `height` values in the SDP format-specific parameters defined by RFC 9134.

Nodes implementing IS-04 v1.3 or higher MUST indicate the coding tools in use, and the compression ratio of the stream, in the Flow resource using the `profile` and `bits_per_pixel` attributes also defined in the Flow Attributes register.

An example Flow resource is provided in the [Examples](../examples/).

### Sources

The Source resource MUST indicate `urn:x-nmos:format:video` for the `format`.
Source resources can be associated with many Flows at the same time.
The Source is therefore unaffected by the use of JPEG XS compression.

## JPEG XS IS-05 Senders and Receivers

Connection Management using IS-05 proceeds in exactly the same manner as for any other stream format carried within RTP.
The SDP file at the **/transportfile** endpoint on Senders MUST comply with the requirements of RFC 9134 and, if appropriate, ST 2110-22.

An SDP file provided in the `transport_file` attribute of a `PATCH` request on the **/staged** endpoint of Receivers MUST also comply with RFC 9134 and, if appropriate, ST 2110-22.

An example SDP file is provided in the [Examples](../examples/).

[BCP-004-01]: https://specs.amwa.tv/bcp-004-01/ "AMWA BCP-004-01 NMOS Receiver Capabilities"
[JPEG-XS]: https://jpeg.org/jpegxs/ "Overview of JPEG XS"
[RFC-2119]: https://tools.ietf.org/html/rfc2119 "Key words for use in RFCs"
[RFC-9134]: https://tools.ietf.org/html/rfc9134 "RTP Payload Format for ISO/IEC 21122 (JPEG XS)"
[IS-04]: https://specs.amwa.tv/is-04/ "AMWA IS-04 NMOS Discovery and Registration Specification"
[IS-05]: https://specs.amwa.tv/is-05/ "AMWA IS-05 NMOS Device Connection Management Specification"
[NMOS Parameter Registers]: https://specs.amwa.tv/nmos-parameter-registers/ "Common parameter values for AMWA NMOS Specifications"
[TR-08]: https://vsf.tv/download/technical_recommendations/VSF_TR-08_2021-08-09.pdf "Transport of JPEG XS Video in ST 2110-22"
[VSF]: https://vsf.tv/ "Video Services Forum"
