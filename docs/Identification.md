# Identification

NCA defines the following framework identifiers (a "framework" identifier is an identifier that is used by the basic NCA framework, irrespective of application). `Table 2` lists the identifiers NCA uses.

| **Identifier** **Datatype** | **Scope** | **Lifespan** | **Format** | **Description** |
| --- | --- | --- | --- | --- |
| **Class ID** _ncaClassID_ | NCA control model | Permanent | See [Appendix A](Appendix%20A%20-%20Class%20ID%20Format.md) | Uniquely identifies a class and its revision level. |
| **Object ID** _ncaOID_ | Device | See 9.1 below | Uint32 | Compact read-only handle that uniquely identifies an object within its device. Every class inherits the **Object ID** property, and this property has a valid value for every object. |
| **Name** _ncaOName_ | Block | Permanent | String | Static, read-only text property that names an object within the block to which it belongs. Unique within the block. Every class inherits the **Name** property, and this property has a valid value for every object. _Name_ is analogous to the control labels silk-screened onto traditional physical control panels. "Input Gain", "Output Select", etcetera., or to the filename in a hierarchical file system. |
| **Object Path** or just **Path** _ncaOPath_ | Device | Permanent | Array of strings | Unique identifier of an object within a device, consisting of the ordered concatenation of the **Name** values of all the object's containing blocks, starting from the **Root Block** , ending with the object's own **Name** value. Note that while the object ID may change over time due to reconfiguration, the object path is a persistent identifier of a specific function or property within a device. |
| **User Label** _ncaString_ | n/a | Can change at any time | String | Read/write string that users may set to identify an object's operational purpose in a particular application context. Analogous to the texts written on mixing console scribble strips, labels, and bits of paper tape affixed to equipment in use. "Elvis's Vocal Mic", "First violins #3", etcetera.This label need not be unique in any scope. |
| **Product GUID** _ncaGUID_ | Universe | Permanent | GUID | Read-only GUID that uniquely identifies the particular product that constitutes the Device. Property of the class **NCA deviceManager**. |
| **Schema ID** _ncaGSID_ | Global or local, as declared | Permanent | TBD | identifier of a schema - see [Schema Reuse](Device%20Model.md#schema-reuse). The schema ID definition will contain both a "namespace" part and an identifier, and the identifier part will be unique within the namespace. A special namespace identifier exists to define "device-local" schemas if needed. |
| **Property ID** **Method ID** **Event ID** _ncaPropertyID_ _ncaMethodID_ _ncaEventID_ | Object | Permanent | See [Appendix B](Appendix%20B%20-%20Property%2C%20Method%2C%20Event%20IDs.md) | Static identifiers that identify the properties, methods, and events of control classes. Values are defined by the class specification in the control model, and do not change over time. Unique within each class. |
| **Connection Management Identifiers** | future | TBD | TBD | stream identifiers, connection endpoint identifiers, media transport session identifiers, and others. Compatible with NMOS IS-04 and -05 and the JT-NM Reference Architecture. This is a future NCA topic, not defined in NCA 1.0. |

*Table 2. NCA Identifiers*

## Object ID management

Object IDs can change from time to time within a device, based on dynamic configuration and other events. Each block object maintains an object ID revision timestamp that indicates the last time the object IDs of its members block have changed.

## NCA-NMOS identity mapping

NCA's **Touchpoint** feature allows applications to attach a list of _touchpoints_ to any NCA object. A touchpoint is a defined relation between an entity in NCA and an entity in a different namespace.

This document defines the mapping to NMOS IS-xx resources; other mappings may be defined in the future, through specifications or by implementers as necessary.

The touchpoints feature is implemented by the **Touchpoints** property of **ncaRoot**. **Touchpoints** is a list of zero or more **touchpoint descriptors**. For a given nca object, each touchpoint descriptor identifies a foreign namespace and a list of one or more identifiers of entities within the foreign namespace.

Because **Touchpoints** is defined in **ncaRoot** , it is inherited by every NCA object and may be used as required.

### NMOS Namespaces

Namespaces for NMOS are as follows:

- IS-04: **x-nmos** (includes the identities which link to IS-05 and IS-07)
- IS-08: **x-nmos/channelmapping** (special touchpoints for IS-08 Input and Output resources)

#### Touchpoint Resources for NMOS

The resources are provided as an array of objects. The NMOS touchpoint resources need to include:

- Resource type type of resource linked
- ID identity of the resource in the given namespace

For the **x-nmos** namespace, **ncaTouchpointResourceNmos** is defined and supports the following resource types:

- Node
- Device
- Source
- Flow
- Sender
- Receiver

For the **x-nmos/channelmapping** namespace, **ncaTouchpointNmos_is_08** is defined, and supports the following resource types, and the touchpoint resource id should be that of the underlying NMOS device.

- Inputs
- Outputs

YANG models for the touchpoints are defined as part of the overall ncaRoot definition in the control model.

## NCA discoverability in IS-04

Compliant NCA protocols will need to be advertised through existing IS-04 practices inside the controls array of an NMOS Device. An example is illustrated in `Code Block 1` below. The specific type tag for each protocol will be defined by the protocol defining document.

```json
{ 
  ...
    "senders": [
        "a65c15a4-a52e-4960-8cd2-e05c31196e5f",
        "68f519a3-5523-4b2c-b72d-ec23cc80207d"
    ],
    "receivers": [
        "8a7bb1c1-4a82-4fd9-a4fb-96f68f560831",
        "ab450c07-ce54-44da-9ea9-c3e62e7b06d0"
    ],
    "controls": [
        {
            "type": "urn:x-nmos:control:nca/v1.0",
            "href": "transport scheme"
        }
    ],
    "tags": {},
    "type": "urn:x-nmos:device:generic",
    "label": "NMOS Device",
    "version": "1529676926:000000000",
    "node_id": "d1713110-7343-4d9e-b3f4-456c8f6ce765",
    "id": "58f6b536-ca4c-43fd-880a-9df2501fc125",
    "description": "NMOS Device"
  ...
}
```

*Code Block 1. NMOS Controls array with NCA entry*