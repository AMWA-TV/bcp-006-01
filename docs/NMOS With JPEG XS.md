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

The NMOS terms 'Controller', 'Node', 'Source', 'Flow', 'Sender', 'Receiver' are used as defined in the [NMOS Glossary](https://specs.amwa.tv/nmos/main/docs/Glossary.html).

## JPEG XS IS-04 Sources, Flows and Senders

Nodes capable of transmitting JPEG XS video streams MUST have Source, Flow and Sender resources in the IS-04 Node API.

Nodes MUST support IS-04 v1.3 to implement all aspects of this specification.
Partial implementation can be achieved using IS-04 v1.2 and earlier.

### Sources

The Source resource MUST indicate `urn:x-nmos:format:video` for the `format`.
Source resources can be associated with many Flows at the same time.
The Source is therefore unaffected by the use of JPEG XS compression.

### Flows

The Flow resource MUST indicate `video/jxsv` in the `media_type` attribute, and `urn:x-nmos:format:video` for the `format`.
This has been permitted since IS-04 v1.1.

For Nodes implementing IS-04 v1.3 or higher, the following additional requirements on the Flow resource apply.

In addition to those attributes defined in IS-04 for all coded video Flows, the following attributes defined in the [Flow Attributes register](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/) of the [NMOS Parameter Registers][] are used for JPEG XS.

These attributes provide information for Controllers and Users to evaluate stream compatibility between Senders and Receivers.

- [Components](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#components)  
  The Flow resource MUST indicate the color (sub-)sampling using the `components` attribute.
  The `components` array value corresponds to the `sampling`, `width` and `height` values in the SDP format-specific parameters defined by RFC 9134.
- [Profile](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#profile)  
  The Flow resource MUST indicate the JPEG XS profile, which defines limits on the required algorithmic features and parameter ranges used in the codestream.
  The permitted `profile` values are strings, defined as per RFC 9134.
  The Unrestricted profile is indicated by omitting this attribute.
- [Level](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#level)  
  The Flow resource MUST indicate the JPEG XS level, which defines a lower bound on the required throughput for a decoder in the image (or decoded) domain.
  The permitted `level` values are strings, defined as per RFC 9134.
  The Unrestricted level is indicated by omitting this attribute.
- [Sublevel](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#sublevel)  
  The Flow resource MUST indicate the JPEG XS sublevel, which defines a lower bound on the required throughput for a decoder in the codestream (or coded) domain.
  The permitted `sublevel` values are strings, defined as per RFC 9134.
  The Unrestricted sublevel is indicated by omitting this attribute.
- [Bit Rate](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#bit-rate)  
  The Flow resource MUST indicate the target bit rate (kilobits/second) of the codestream.
  The `bit_rate` integer value is expressed in units of 1000 bits per second, rounding up.

An example Flow resource is provided in the [Examples](../examples/).

### Senders

For Nodes transmitting JPEG XS using the RTP payload mapping defined by RFC 9134, the Sender resource MUST indicate `urn:x-nmos:transport:rtp` or one of its subclassifications for the `transport` attribute.
Sender resources provide no indication of media type or format, since this is described by the associated Flow resource.

The SDP file at the `manifest_href` MUST comply with the requirements of RFC 9134.
Additionally, the SDP file needs to convey, so far as the defined parameters allow, the same information about the stream as conveyed by the Source, Flow and Sender attributes (or their defaults, when omitted) defined by this specification and IS-04.
Therefore:

- Each of the `profile`, `level` and `sublevel` format-specific parameters MUST be included with the correct value unless it is Unrestricted.
- The correct `sampling`, `depth`, `width`, `height`, `exactframerate`, `colorimetry` and `TCS` MUST be included in all cases.
- The `interlace` and `segmented` parameters MUST be included or omitted to correctly indicate the scanning/interlace mode.

If the Sender meets the traffic shaping and delivery timing requirements specified for ST 2110-22, the SDP file MUST also comply with the provisions of ST 2110-22.

For Nodes implementing IS-04 v1.3 or higher, the following additional requirements on the Sender resource apply.

In addition to those attributes defined in IS-04 for Senders, the following attributes defined in the [Sender Attributes register](https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/) of the NMOS Parameter Registers are used for JPEG XS.

- [Bit Rate](https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/#bit-rate)  
  The Sender resource MUST indicate the bit rate (kilobits/second) including the RTP transport overhead.
  The `bit_rate` integer value is expressed in units of 1000 bits per second, rounding up.
  The value is for the IP packets, so for the RTP payload format per RFC 9134, that includes the RTP, UDP and IP packet headers and the payload.  
  Informative note: This definition is consistent with the definition of the bit rate attribute required by ST 2110-22 in the SDP media description.
- [Packet Transmission Mode](https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/#packet-transmission-mode)  
  If the Sender is using the slice packetization mode, it MUST include the `packet_transmission_mode` attribute.
  Since the default value of this attribute is `codestream`, the Sender MAY omit this attribute when using codestream packetization.
- [ST 2110-21 Sender Type](https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/#st-2110-21-sender-type)  
  If the Sender complies with the traffic shaping and delivery timing requirements for ST 2110-22, it MUST include the `st2110_21_sender_type` attribute.

An example Sender resource is provided in the [Examples](../examples/).

## JPEG XS IS-04 Receivers

Nodes capable of receiving JPEG XS video streams MUST have a Receiver resource in the IS-04 Node API, which lists `video/jxsv` in the `media_types` array within the `caps` object.
This has been permitted since IS-04 v1.1.

If the Receiver has limitations on or preferences regarding the JPEG XS video streams that it supports, the Receiver resource MUST indicate constraints in accordance with the [BCP-004-01][] Receiver Capabilities specification.
The Receiver SHOULD express its constraints as precisely as possible, to allow a Controller to determine with a high level of confidence the Receiver's compatibility with the available streams.
It is not always practical for the constraints to indicate every type of stream that a Receiver can or cannot consume successfully; however, they SHOULD describe as many of its commonly used operating points as practical and any preferences among them.

The `constraint_sets` parameter within the `caps` object can be used to describe combinations of frame rates, width and height, and other parameters which the Receiver can support, using the parameter constraints defined in the [Capabilities register](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/) of the NMOS Parameter Registers.

The following parameter constraints can be used to express limits specifically defined by ISO/IEC 21122 and RFC 9134 for JPEG XS decoders:

- [Profile](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#profile)
- [Level](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#level)
- [Sublevel](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#sublevel)  
- [Packet Transmission Mode](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#packet-transmission-mode)  

When the JPEG XS decoder supports the Unrestricted profile, level or sublevel, the Receiver can indicate that the parameter is unconstrained, as described in BCP-004-01.
When the decoder does not support Unrestricted but supports a range of profiles, levels or sublevels, the `enum` Constraint Keyword can be used to indicate the acceptable values.

Other existing parameter constraints, such as the following, are also appropriate to express limitations on supported JPEG XS video streams:

- [Frame Width](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#frame-width)
- [Frame Height](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#frame-height)
- [Color Sampling](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#color-sampling)
- [Component Depth](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#component-depth)
- [Format Bit Rate](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#format-bit-rate)
- [Transport Bit Rate](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#transport-bit-rate)
- [ST 2110-21 Sender Type](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#st-2110-21-sender-type)

An example Receiver resource is provided in the [Examples](../examples/) that demonstrates how to represent VSF TR-08 interoperability points using these parameter constraints.

## JPEG XS IS-05 Senders and Receivers

Connection Management using IS-05 proceeds in exactly the same manner as for any other stream format carried within RTP.

The SDP file at the **/transportfile** endpoint on an IS-05 Sender MUST comply with the same requirements described for the SDP file at the IS-04 Sender `manifest_href`.

A `PATCH` request on the **/staged** endpoint of an IS-05 Receiver can contain an SDP file in the `transport_file` attribute.
The SDP file for a JPEG XS stream is expected to comply with RFC 9134 and, if appropriate, ST 2110-22.
It need not comply with the additional requirements specified for SDP files at Senders.

If the Receiver is not capable of consuming the stream described by the SDP file, it SHOULD reject the request.
If it is unable to assess the stream compatibility, for example when the SDP file does not include some of the optional parameters defined by RFC 9134, it MAY accept the request.

An example SDP file is provided in the [Examples](../examples/).

## Controllers

Controllers MUST use IS-04 to discover JPEG XS Senders and Receivers and IS-05 to manage connections between them.
Controllers MUST support IS-04 v1.3 to implement all aspects of this specification.
Partial implementation can be achieved using IS-04 v1.2 and earlier.

Controllers MUST support the BCP-004-01 Receiver Capabilities mechanism and all the parameter constraints listed in this specification in order to evaluate the stream compatibility between JPEG XS Senders and Receivers.

[BCP-004-01]: https://specs.amwa.tv/bcp-004-01/ "AMWA BCP-004-01 NMOS Receiver Capabilities"
[JPEG-XS]: https://jpeg.org/jpegxs/ "Overview of JPEG XS"
[RFC-2119]: https://tools.ietf.org/html/rfc2119 "Key words for use in RFCs"
[RFC-9134]: https://tools.ietf.org/html/rfc9134 "RTP Payload Format for ISO/IEC 21122 (JPEG XS)"
[IS-04]: https://specs.amwa.tv/is-04/ "AMWA IS-04 NMOS Discovery and Registration Specification"
[IS-05]: https://specs.amwa.tv/is-05/ "AMWA IS-05 NMOS Device Connection Management Specification"
[NMOS Parameter Registers]: https://specs.amwa.tv/nmos-parameter-registers/ "Common parameter values for AMWA NMOS Specifications"
[TR-08]: https://vsf.tv/download/technical_recommendations/VSF_TR-08_2022-04-20.pdf "Transport of JPEG XS Video in ST 2110-22"
[VSF]: https://vsf.tv/ "Video Services Forum"
