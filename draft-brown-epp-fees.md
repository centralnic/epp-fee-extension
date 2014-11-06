  ---------------
  [ TOC ](#toc)
  ---------------

+--------------------------------------------------------------------------+
|   --------------------------------- ----------------------               |
|   Internet Engineering Task Force   G. Brown                             |
|   Internet-Draft                    CentralNic Group plc                 |
|   Intended status: Experimental     September 2014                       |
|   Expires: March 5, 2015                                                 |
|   --------------------------------- ----------------------               |
+--------------------------------------------------------------------------+

\
Registry Fee Extension for the Extensible Provisioning Protocol (EPP)\
draft-brown-epp-fees-03
======================================================================

### Abstract

This document describes an Extensible Provisioning Protocol (EPP)
extension mapping for registry fees.

### Status of this Memo

This Internet-Draft is submitted in full conformance with the provisions
of BCP 78 and BCP 79.

Internet-Drafts are working documents of the Internet Engineering Task
Force (IETF). Note that other groups may also distribute working
documents as Internet-Drafts. The list of current Internet-Drafts is at
http://datatracker.ietf.org/drafts/current/.

Internet-Drafts are draft documents valid for a maximum of six months
and may be updated, replaced, or obsoleted by other documents at any
time. It is inappropriate to use Internet-Drafts as reference material
or to cite them other than as “work in progress.”

This Internet-Draft will expire on March 5, 2015.

### Copyright Notice

Copyright (c) 2014 IETF Trust and the persons identified as the document
authors. All rights reserved.

This document is subject to BCP 78 and the IETF Trust's Legal Provisions
Relating to IETF Documents (http://trustee.ietf.org/license-info) in
effect on the date of publication of this document. Please review these
documents carefully, as they describe your rights and restrictions with
respect to this document. Code Components extracted from this document
must include Simplified BSD License text as described in Section 4.e of
the Trust Legal Provisions and are provided without warranty as
described in the Simplified BSD License.

\

------------------------------------------------------------------------

### Table of Contents

[1.](#anchor1)  Introduction\
     [1.1.](#anchor2)  Conventions Used in This Document\
 [2.](#anchor3)  Extension Elements\
     [2.1.](#anchor4)  Client Commands\
     [2.2.](#currency-codes)  Currency Codes\
     [2.3.](#validity-periods)  Validity Periods\
     [2.4.](#fees-and-credits)  Fees and Credits\
         [2.4.1.](#anchor5)  Refunds\
         [2.4.2.](#anchor6)  Grace Periods\
         [2.4.3.](#anchor7)  Applicability\
     [2.5.](#account-balance)  Account Balance\
     [2.6.](#credit-limit)  Credit Limit\
     [2.7.](#classification)  Classification of Domain Names\
 [3.](#anchor8)  Server Handling of Fee Information\
 [4.](#anchor9)  EPP Command Mapping\
     [4.1.](#anchor10)  EPP Query Commands\
         [4.1.1.](#anchor11)  EPP \<check\> Command\
         [4.1.2.](#anchor12)  EPP \<info\> Command\
         [4.1.3.](#anchor13)  EPP Transfer Query Command\
     [4.2.](#anchor14)  EPP Transform Commands\
         [4.2.1.](#anchor15)  EPP \<create\> Command\
         [4.2.2.](#anchor16)  EPP \<delete\> Command\
         [4.2.3.](#anchor17)  EPP \<renew\> Command\
         [4.2.4.](#anchor18)  EPP \<transfer\> Command\
         [4.2.5.](#anchor19)  EPP \<update\> Command\
     [4.3.](#anchor20)  Formal Syntax\
 [5.](#Security)  Security Considerations\
 [6.](#IANA)  IANA Considerations\
 [7.](#anchor21)  Acknowledgements\
 [8.](#anchor22)  Change History\
     [8.1.](#anchor23)  Changes from 00 to 01\
     [8.2.](#anchor24)  Changes from 01 to 02\
     [8.3.](#anchor25)  Changes from 02 to 03\
     [8.4.](#anchor26)  Changes from 03 to 04\
 [9.](#rfc.references1)  Normative References\
 [§](#rfc.authors)  Author's Address\

\
\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 1.  Introduction

Historically, domain name registries have applied a simple fee structure
for billable transactions, namely a basic unit price applied to domain
\<create\>, \<renew\>, \<transfer\> and [RGP<span> (</span><span
class="info">Hollenbeck, S., “Domain Registry Grace Period Mapping for
the Extensible Provisioning Protocol (EPP),”
September 2004.</span><span>)</span>](#RFC3915) [RFC3915] restore
commands. Given the relatively small number of EPP servers to which EPP
clients have been required to connect, it has generally been the case
that client operators have been able to obtain details of these fees
out-of-band by contacting the server operators.

Given the present expansion of the DNS namespace, and the proliferation
of novel business models, it is now desirable to provide a method for
EPP clients to query EPP servers for the fees and credits associated
with certain commands and specific objects.

This document describes an extension mapping for version 1.0 of the
[Extensible Provisioning Protocol (EPP)<span> (</span><span
class="info">Hollenbeck, S., “Extensible Provisioning Protocol (EPP),”
August 2009.</span><span>)</span>](#RFC5730) [RFC5730]. This EPP mapping
provides a mechanism by which EPP clients may query the fees and credits
associated with various billable transactions, and also obtain their
current account balance.

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 1.1.  Conventions Used in This Document

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
"SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in [RFC 2119<span>
(</span><span class="info">Bradner, S., “Key words for use in RFCs to
Indicate Requirement Levels,”
March 1997.</span><span>)</span>](#RFC2119) [RFC2119].

XML is case sensitive. Unless stated otherwise, XML specifications and
examples provided in this document MUST be interpreted in the character
case presented in order to develop a conforming implementation.

"fee" is used as an abbreviation for "urn:ietf:params:xml:ns:fee-0.6".
The XML namespace prefix "fee" is used, but implementations MUST NOT
depend on it and instead employ a proper namespace-aware XML parser and
serializer to interpret and output the XML documents.

The XML namespace prefix above contains a version number, specifically
"0.6". This version number will increment with successive versions of
this document, and will reach 1.0 if and when this document is published
as an RFC. This permits clients to distinguish which version of the
extension a server has implemented.

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 2.  Extension Elements

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 2.1.  Client Commands

The \<fee:command\> element is used in EPP \<check\> and \<info\>
commands to determine the fee which is applicable to the given command.

The element values permitted by the server is a matter of repository
policy, but MUST include as a minimum the following values:

-   "create" indicating a \<domain:create\> command;
-   "renew" indicating a \<domain:renew\> command;
-   "transfer" indicating a \<domain:transfer\> command;

If the server supports the [Registry Grace Period Mapping<span>
(</span><span class="info">Hollenbeck, S., “Domain Registry Grace Period
Mapping for the Extensible Provisioning Protocol (EPP),”
September 2004.</span><span>)</span>](#RFC3915) [RFC3915], then the
server MUST also support the "restore" value.

The \<fee:command\> element MAY have an OPTIONAL "phase" attribute
specifying a launch phase as described in
[[draft‑ietf‑eppext‑launchphase]<span> (</span><span class="info">Gould,
J., Tan, W., and G. Brown, “Launch Phase Mapping for the Extensible
Provisioning Protocol (EPP),”
2014.</span><span>)</span>](#draft-ietf-eppext-launchphase). It may also
contain an OPTIONAL "subphase" attribute identifying the custom or
sub-phase as described in [[draft‑ietf‑eppext‑launchphase]<span>
(</span><span class="info">Gould, J., Tan, W., and G. Brown, “Launch
Phase Mapping for the Extensible Provisioning Protocol (EPP),”
2014.</span><span>)</span>](#draft-ietf-eppext-launchphase).

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 2.2.  Currency Codes

The \<fee:currency\> element is used to indicate which currency fees are
charged in. This value of this element MUST be a three-character
currency code from [[ISO4217]<span> (</span><span
class="info">International Organization for Standardization, “ISO
4217:2008, Codes for the representation of currencies and funds,”
2008.</span><span>)</span>](#ISO4217).

Note that ISO 4217 provides the special "XXX" code, which MAY be used if
the server uses a non-currency based system for assessing fees, such as
a system of credits.

The use of \<fee:currency\> elements in commands is OPTIONAL: if a
\<fee:currency\> element is not present in a command, the server MUST
determine the currency based on the client's account settings which MUST
be agreed by the client and server via an out-of-band channel. However,
\<fee:currency\> element MUST be present in responses.

Servers SHOULD NOT perform a currency conversion if a client uses an
incorrect currency code. Servers SHOULD return a 2004 error instead.

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 2.3.  Validity Periods

The \<fee:period\> element is used to indicate the units to be added to
the registration period of domain names by the \<create\>, \<renew\> and
\<transfer\> commands. This element is derived from the
\<domain:period\> element described in [[RFC5731]<span> (</span><span
class="info">Hollenbeck, S., “Extensible Provisioning Protocol (EPP)
Domain Name Mapping,” August 2009.</span><span>)</span>](#RFC5731).

The \<fee:period\> element is OPTIONAL in commands: if omitted, the
server MUST determine the fee(s) using a validity period of 1 year. The
\<fee:period\> element MUST be present in responses.

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 2.4.  Fees and Credits

Servers which implement this extension will include elements in
responses which provide information about the fees and/or credits
associated with a given billable transaction.

The \<fee:fee\> and \<fee:credit\> elements are used to provide this
information. The presence of a \<fee:fee\> element in a response
indicates a debit against the client's account balance; a \<fee:credit\>
element indicates a credit. A \<fee:fee\> element MUST have a
non-negative value. A \<fee:credit\> element MUST have a negative value.

A server MAY respond with multiple \<fee:fee\> and \<fee:credit\>
elements in the same response. In such cases, the net fee or credit
applicable to the transaction is the arithmetic sum of the values of
each of the \<fee:fee\> or \<fee:credit\> elements. This amount applies
to the total additional validity period applied to the domain (where
applicable) rather than to any incremental unit.

\<fee:fee\> and \<fee:credit\> elements MAY have an OPTIONAL
"description" attribute which provides a human-readable description of
the fee. Servers SHOULD provide documentation on the possible values of
this attribute, and their meanings.

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 2.4.1.  Refunds

\<fee:fee\> elements MAY have an OPTIONAL "refundable" attribute which
takes a boolean value. Fees may be refunded under certain circumstances,
such as when a domain application is rejected (as described in
[[draft‑ietf‑eppext‑launchphase]<span> (</span><span class="info">Gould,
J., Tan, W., and G. Brown, “Launch Phase Mapping for the Extensible
Provisioning Protocol (EPP),”
2014.</span><span>)</span>](#draft-ietf-eppext-launchphase)) or when a
domain name is deleted during the relevant Grace Period (see below). If
the "refundable" attribute is omitted, then the default value of "1"
should be assumed (i.e. the fee is refundable).

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 2.4.2.  Grace Periods

[[RFC3915]<span> (</span><span class="info">Hollenbeck, S., “Domain
Registry Grace Period Mapping for the Extensible Provisioning Protocol
(EPP),” September 2004.</span><span>)</span>](#RFC3915) describes a
system of "grace periods", which are time periods following a billable
transaction during which, if a domain name is deleted, the client
receives a refund.

The "grace-period" attribute MAY be used to indicate the relevant grace
period for a fee. If a server implements the Registry Grace Period
extension, it MUST specify the grace period for all relevant
transactions. If no "grace-period" attribute value is provided, then no
grace period is applicable.

If a \<fee:fee\> element has a "grace-period" attribute then it MUST
also be refundable. If the "refundable" attribute of a \<fee:fee\>
element is false then it MUST NOT have a "grace-period" attribute.

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 2.4.3.  Applicability

Fees may be applied immediately upon receipt of a command from a client,
or may only be applied once an out-of-band process (such as the
processing of applications at the end of a launch phase) has taken
place.

The "applied" attribute of the \<fee:fee\> element allows servers to
indicate whether a fee will be applied immediately, or whether it will
be applied at some point in the future. This attribute takes two
possible values: "immediate" (which is the default) or "delayed".

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 2.5.  Account Balance

The \<fee:balance\> element is an OPTIONAL element which MAY be included
in server responses to transform commands. If present, it can be used by
the client to determine the remaining credit at the server.

Whether or not the \<fee:balance\> is included in responses is a matter
of server policy. However, if a server chooses to offer support for this
element, it MUST be included in responses to all domain "transform"
commands (ie \<create\>, \<renew\>, \<update\>, \<delete\>, \<transfer
op="request"\>).

The value of the \<fee:balance\> MAY be negative. A negative balance
indicates that the server has extended a line of credit to the client
(see below).

When a server includes a \<fee:balance\> element in response to
transform commands, the value of the element MUST reflect the client's
account balance after any fees or credits associated with that command
have been applied.

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 2.6.  Credit Limit

As described above, if a server returns a response containing a
\<fee:balance\> with a negative value, then the server has extended a
line of credit to the client. A server MAY also include a
\<fee:creditLimit\> element in responses which indicates the maximum
credit available to a client. A server MAY reject certain transactions
if the absolute value of the \<fee:balance\> is equal to or exceeds the
value of the \<fee:creditLimit\> element.

Whether or not the \<fee:creditLimit\> is included in responses is a
matter of server policy. However, if a server chooses to offer support
for this element, it MUST be included in responses to all domain
"transform" commands (ie \<create\>, \<renew\>, \<update\>, \<delete\>,
\<transfer op="request"\>).

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 2.7.  Classification of Domain Names

Domain names may be assigned to a particular class, category, or tier,
each of which has a particular fee or set of fees associated with it.
The \<fee:class\> element which appears in \<check\> and \<info\>
responses is used to indicate the classification of a domain name.

If a server makes use of this element, it MUST provide clients with a
list of all the values that the element may take via an out-of-band
channel. Servers MUST NOT use values which do not appear on this list.

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 3.  Server Handling of Fee Information

Depending on server policy, a client MAY be required to include the
extension elements described in this document for certain transform
commands. Servers MUST provide clear documentation to clients about the
circumstances in which this extension must be used.

If a server receives a command from a client which does not include the
extension elements required by the server for that command, then it MUST
response with a 2003 "Required parameter missing" error.

If the currency and total fee provided by the client do not agree with
the server's own calculation of the fee for that command, then the
server MUST respond in one of the following ways:

-   Reject the command with a 2004 "Parameter value range" error; or
-   Accept the command, but return a 1001 "Action Pending" response, and
    queue the requested command for offline review. If the server
    chooses this option, it MUST provide notification of the result of
    the review using the EPP message queue.

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 4.  EPP Command Mapping

A detailed description of the EPP syntax and semantics can be found in
[[RFC5730]<span> (</span><span class="info">Hollenbeck, S., “Extensible
Provisioning Protocol (EPP),”
August 2009.</span><span>)</span>](#RFC5730).

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 4.1.  EPP Query Commands

This extension does not add any elements to the EPP \<poll\> command or
response.

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 4.1.1.  EPP \<check\> Command

This extension defines additional elements for the EPP \<check\>
command.

The command MAY contain an \<extension\> element which MAY contain a
\<fee:check\> element. The \<fee:check\> element MUST contain at least
one \<fee:domain\> element, with the following child elements:

-   A \<fee:name\> element. The domain name need not correspond to a
    \<domain:name\> element that appears in the \<domain:check\> element
    of the main body of the frame.
-   An OPTIONAL \<fee:currency\> element;
-   A \<fee:command\> element;
-   An OPTIONAL \<fee:period\> element.

Example \<check\> command:

<div
style="display: table; width: 0; margin-left: 3em; margin-right: auto">

    C: <?xml version="1.0" encoding="utf-8" standalone="no"?>
    C: <epp xmlns="urn:ietf:params:xml:ns:epp-1.0">
    C:   <command>
    C:     <check>
    C:       <domain:check
    C:         xmlns:domain="urn:ietf:params:xml:ns:domain-1.0">
    C:         <domain:name>example.com</domain:name>
    C:         <domain:name>example.net</domain:name>
    C:         <domain:name>example.org</domain:name>
    C:       </domain:check>
    C:     </check>
    C:     <extension>
    C:       <fee:check
    C:         xmlns:fee="urn:ietf:params:xml:ns:fee-0.6">
    C:         <fee:domain>
    C:           <fee:name>example.com</fee:name>
    C:           <fee:currency>USD</fee:currency>
    C:           <fee:command phase="sunrise">create</fee:command>
    C:           <fee:period unit="y">1</fee:period>
    C:         </fee:domain>
    C:         <fee:domain>
    C:           <fee:name>example.net</fee:name>
    C:           <fee:currency>EUR</fee:currency>
    C:           <fee:command phase="claims" subphase="landrush">
    C:           create</fee:command>
    C:           <fee:period unit="y">2</fee:period>
    C:         </fee:domain>
    C:         <fee:domain>
    C:           <fee:name>example.org</fee:name>
    C:           <fee:currency>EUR</fee:currency>
    C:           <fee:command>transfer</fee:command>
    C:         </fee:domain>
    C:         <fee:domain>
    C:           <fee:name>example.xyz</fee:name>
    C:           <fee:command>restore</fee:command>
    C:           <fee:period unit="y">1</fee:period>
    C:         </fee:domain>
    C:       </fee:check>
    C:     </extension>
    C:     <clTRID>ABC-12345</clTRID>
    C:   </command>
    C: </epp>

</div>

When the server receives a \<check\> command that includes the extension
elements described above, its response MUST (subject to the exception
described below) contain an \<extension\> element, which MUST contain a
child \<fee:chkData\> element. The \<fee:chkData\> element MUST contain
a \<fee:cd\> element for each \<fee:domain\> element contained in the
command. The \<fee:cd\> MUST contain the following child elements:

-   A \<fee:name\> element that contains a domain name which matches the
    name in the corresponding \<fee:domain\> element.
-   A \<fee:currency\> element that contains same currency code that
    appeared in the corresponding \<fee:domain\> element. If no
    \<fee:currency\> element appeared in the corresponding
    \<fee:domain\> element, then the client's default billing currency
    should be used.
-   A \<fee:command\> element that contains same command that appeared
    in the corresponding \<fee:domain\> element. This element MAY have
    the OPTIONAL "phase" and "subphase" elements, which MUST match the
    same attributes in the corresponding \<fee:domain\> element.
-   An OPTIONAL \<fee:period\> element that contains same unit that
    appeared in the corresponding \<fee:domain\> element. If the value
    of the preceeding \<fee:command\> element is "restore", this element
    MUST NOT be included. Otherwise it MUST be included. If no
    \<fee:period\> appeared in the corresponding \<fee:domain\> element,
    then this element MUST have a value of 1 year.
-   Zero or more \<fee:fee\> elements.
-   An OPTIONAL \<fee:class\> element.

If no \<fee:fee\> elements are present in a \<fee:cd\> element, this
indicates that the requested command is not permitted by server policy.

The presence of a \<fee:fee\> with a value of "0.00" (ie a zero amount)
in a \<fee:cd\> element indicates that no fee will be assessed by the
server for this command.

Example \<check\> response:

<div
style="display: table; width: 0; margin-left: 3em; margin-right: auto">

    S: <?xml version="1.0" encoding="utf-8" standalone="no"?>
    S: <epp xmlns="urn:ietf:params:xml:ns:epp-1.0">
    S:   <response>
    S:     <result code="1000">
    S:       <msg>Command completed successfully</msg>
    S:     </result>
    S:     <resData>
    S:       <domain:chkData
    S:         xmlns:domain="urn:ietf:params:xml:ns:domain-1.0">
    S:         <domain:cd>
    S:           <domain:name avail="1">example.com</domain:name>
    S:         </domain:cd>
    S:         <domain:cd>
    S:           <domain:name avail="0">example.net</domain:name>
    S:           <domain:reason>In use</domain:reason>
    S:         </domain:cd>
    S:         <domain:cd>
    S:           <domain:name avail="0">example.org</domain:name>
    S:           <domain:reason>In use</domain:reason>
    S:         </domain:cd>
    S:       </domain:chkData>
    S:     </resData>
    S:     <extension>
    S:       <fee:chkData
    S:         xmlns:fee="urn:ietf:params:xml:ns:fee-0.6">
    S:         <fee:cd>
    S:           <fee:name>example.com</fee:name>
    S:           <fee:currency>USD</fee:currency>
    S:           <fee:command phase="sunrise">create</fee:command>
    S:           <fee:period unit="y">1</fee:period>
    S:           <fee:fee description="Application Fee"
    S:             refundable="0">5.00</fee:fee>
    S:           <fee:fee description="Registration Fee"
    S:             refundable="1">5.00</fee:fee>
    S:         </fee:cd>
    S:         <fee:cd>
    S:           <fee:name>example.net</fee:name>
    S:           <fee:currency>EUR</fee:currency>
    S:           <fee:command phase="claims" subphase="landrush">
    S:           create</fee:command>
    S:           <fee:period unit="y">2</fee:period>
    S:           <fee:fee>5.00</fee:fee>
    S:         </fee:cd>
    S:         <fee:cd>
    S:           <fee:name>example.org</fee:name>
    S:           <fee:currency>EUR</fee:currency>
    S:           <fee:command>transfer</fee:command>
    S:           <fee:period unit="y">2</fee:period>
    S:           <fee:fee description="Transfer Fee">2.50</fee:fee>
    S:           <fee:fee description="Renewal Fee">10.00</fee:fee>
    S:         </fee:cd>
    S:         <fee:cd>
    S:           <fee:name>example.xyz</fee:name>
    S:           <fee:currency>GBP</fee:currency>
    S:           <fee:command>restore</fee:command>
    S:           <fee:period unit="y">1</fee:period>
    S:           <fee:fee description="Restore Fee">25</fee:fee>
    S:           <fee:fee description="Renewal Fee">5.00</fee:fee>
    S:           <fee:class>premium-tier1</fee:class>
    S:         </fee:cd>
    S:       </fee:chkData>
    S:     </extension>
    S:     <trID>
    S:       <clTRID>ABC-12345</clTRID>
    S:       <svTRID>54322-XYZ</svTRID>
    S:     </trID>
    S:   </response>
    S: </epp>

</div>

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 4.1.2.  EPP \<info\> Command

This extension defines additional elements for the EPP \<info\> command.

The command MAY contain an \<extension\> element which MAY contain a
\<fee:info\> element with the following child elements:

-   An OPTIONAL \<fee:currency\> element;
-   A \<fee:command\> element;
-   An OPTIONAL \<fee:period\> element.

Example \<info\> command:

<div
style="display: table; width: 0; margin-left: 3em; margin-right: auto">

    C: <?xml version="1.0" encoding="utf-8" standalone="no"?>
    C: <epp xmlns="urn:ietf:params:xml:ns:epp-1.0">
    C:   <command>
    C:     <info>
    C:       <domain:info
    C:         xmlns:domain="urn:ietf:params:xml:ns:domain-1.0">
    C:         <domain:name hosts="all">example.com</domain:name>
    C:       </domain:info>
    C:     </info>
    C:     <extension>
    C:       <fee:info xmlns:fee="urn:ietf:params:xml:ns:fee-0.6">
    C:         <fee:currency>USD</fee:currency>
    C:         <fee:command phase="sunrise">create</fee:command>
    C:         <fee:period unit="y">1</fee:period>
    C:       </fee:info>
    C:     </extension>
    C:     <clTRID>ABC-12345</clTRID>
    C:   </command>
    C: </epp>

</div>

When the server receives an \<info\> command that includes the extension
element described above, its response MUST (subject to the exception
described below) contain an \<extension\> element, which MUST contain a
child \<fee:infData\> element. The \<fee:infData\> MUST contain the
following child elements:

-   A \<fee:currency\> element that contains same currency code that
    appeared in the \<fee:currency\> element element of the command. If
    no \<fee:currency\> element appeared in the command, then the
    client's default billing currency should be used.
-   A \<fee:command\> element that contains same command that appeared
    in the \<fee:command\> element of the command. This element MAY have
    the OPTIONAL "phase" and "subphase" elements, which MUST match the
    same attributes in the \<fee:command\> element of the command.
-   An OPTIONAL \<fee:period\> element that contains same unit that
    appeared in the corresponding \<fee:period\> element of the command.
    If the value of the preceeding \<fee:command\> element is "restore",
    this element MUST NOT be included. Otherwise it MUST be included. If
    no \<fee:period\> appeared in the corresponding \<fee:domain\>
    element, then this element MUST have a value of 1 year.
-   One or more \<fee:fee\> elements.
-   An OPTIONAL \<fee:class\> element.

Depending on policy, the server MAY choose to omit the \<fee:infData\>
element and return a 2201 authorisation error instead.

Example \<info\> response:

<div
style="display: table; width: 0; margin-left: 3em; margin-right: auto">

    S: <?xml version="1.0" encoding="utf-8" standalone="no"?>
    S: <epp xmlns="urn:ietf:params:xml:ns:epp-1.0">
    S:   <response>
    S:     <result code="1000">
    S:       <msg>Command completed successfully</msg>
    S:     </result>
    S:     <resData>
    S:       <domain:infData
    S:         xmlns:domain="urn:ietf:params:xml:ns:domain-1.0">
    S:         <domain:name>example.com</domain:name>
    S:         <domain:roid>EXAMPLE1-REP</domain:roid>
    S:         <domain:status s="ok" />
    S:         <domain:registrant>jd1234</domain:registrant>
    S:         <domain:contact type="admin">sh8013</domain:contact>
    S:         <domain:contact type="tech">sh8013</domain:contact>
    S:         <domain:ns>
    S:           <domain:hostObj>ns1.example.com</domain:hostObj>
    S:           <domain:hostObj>ns1.example.net</domain:hostObj>
    S:         </domain:ns>
    S:         <domain:host>ns1.example.com</domain:host>
    S:         <domain:host>ns2.example.com</domain:host>
    S:         <domain:clID>ClientX</domain:clID>
    S:         <domain:crID>ClientY</domain:crID>
    S:         <domain:crDate>1999-04-03T22:00:00.0Z</domain:crDate>
    S:         <domain:upID>ClientX</domain:upID>
    S:         <domain:upDate>1999-12-03T09:00:00.0Z</domain:upDate>
    S:         <domain:exDate>2005-04-03T22:00:00.0Z</domain:exDate>
    S:         <domain:trDate>2000-04-08T09:00:00.0Z</domain:trDate>
    S:         <domain:authInfo>
    S:           <domain:pw>2fooBAR</domain:pw>
    S:         </domain:authInfo>
    S:       </domain:infData>
    S:     </resData>
    S:     <extension>
    S:       <fee:infData xmlns:fee="urn:ietf:params:xml:ns:fee-0.6">
    S:         <fee:currency>USD</fee:currency>
    S:         <fee:command phase="sunrise">create</fee:command>
    S:         <fee:period unit="y">1</fee:period>
    S:         <fee:fee refundable="1" grace-period="P5D">10.00</fee:fee>
    S:         <fee:class>premium-tier1</fee:class>
    S:       </fee:infData>
    S:     </extension>
    S:     <trID>
    S:       <clTRID>ABC-12345</clTRID>
    S:       <svTRID>54322-XYZ</svTRID>
    S:     </trID>
    S:   </response>
    S: </epp>

</div>

Example \<info\> response where the domain name is not registered:

<div
style="display: table; width: 0; margin-left: 3em; margin-right: auto">

    S: <?xml version="1.0" encoding="utf-8" standalone="no"?>
    S: <epp xmlns="urn:ietf:params:xml:ns:epp-1.0">
    S:   <response>
    S:     <result code="2303">
    S:       <msg>Object does not exist.</msg>
    S:     </result>
    S:     <extension>
    S:       <fee:infData
    S:         xmlns:fee="urn:ietf:params:xml:ns:fee-0.6">
    S:         <fee:currency>USD</fee:currency>
    S:         <fee:command phase="sunrise">create</fee:command>
    S:         <fee:period unit="y">1</fee:period>
    S:         <fee:fee>10.00</fee:fee>
    S:       </fee:infData>
    S:     </extension>
    S:     <trID>
    S:       <clTRID>ABC-12345</clTRID>
    S:       <svTRID>54322-XYZ</svTRID>
    S:     </trID>
    S:   </response>
    S: </epp>

</div>

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 4.1.3.  EPP Transfer Query Command

This extension does not add any elements to the EPP \<transfer\> query
command, but does include elements in the response, when the extension
has been selected during a \<login\> command.

When the \<transfer\> query command has been processed successfully, the
client selected the extension when it logged in, and the client is
authorised by the server to view information about the transfer, the
server MAY include in the \<extension\> section of the EPP response a
\<fee:trnData\> element that contains the following child elements:

-   A \<fee:currency\> element.
-   A \<fee:period\> element.
-   one or more \<fee:fee\> elements containing the fees associated with
    the transfer.

If no \<fee:trnData\> element is included in the response, then no fee
will be assessed by the server for the transfer.

Example \<transfer\> query response:

<div
style="display: table; width: 0; margin-left: 3em; margin-right: auto">

    S: <?xml version="1.0" encoding="utf-8" standalone="no"?>
    S: <epp xmlns="urn:ietf:params:xml:ns:epp-1.0">
    S:   <response>
    S:     <result code="1001">
    S:       <msg>Command completed successfully; action pending</msg>
    S:     </result>
    S:     <resData>
    S:       <domain:trnData
    S:         xmlns:domain="urn:ietf:params:xml:ns:domain-1.0">
    S:         <domain:name>example.com</domain:name>
    S:         <domain:trStatus>pending</domain:trStatus>
    S:         <domain:reID>ClientX</domain:reID>
    S:         <domain:reDate>2000-06-08T22:00:00.0Z</domain:reDate>
    S:         <domain:acID>ClientY</domain:acID>
    S:         <domain:acDate>2000-06-13T22:00:00.0Z</domain:acDate>
    S:         <domain:exDate>2002-09-08T22:00:00.0Z</domain:exDate>
    S:       </domain:trnData>
    S:     </resData>
    S:     <extension>
    S:       <fee:trnData xmlns:fee="urn:ietf:params:xml:ns:fee-0.6">
    S:         <fee:currency>USD</fee:currency>
    S:         <fee:period unit="y">1</fee:period>
    S:         <fee:fee>5.00</fee:fee>
    S:       </fee:trnData>
    S:     </extension>
    S:     <trID>
    S:       <clTRID>ABC-12345</clTRID>
    S:       <svTRID>54322-XYZ</svTRID>
    S:     </trID>
    S:   </response>
    S: </epp>

</div>

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 4.2.  EPP Transform Commands

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 4.2.1.  EPP \<create\> Command

This extension adds elements to both the EPP \<create\> command and
response, when the extension has been selected during a \<login\>
command.

When submitting a \<create\> command to the server, the client MAY
include in the \<extension\> element a \<fee:create\> element which
includes the following child elements:

-   An OPTIONAL \<fee:currency\> element;
-   One or more \<fee:fee\> elements.

When the \<create\> command has been processed successfully, and the
client selected the extension when it logged in, and a fee was assessed
by the server for the transaction, the server MUST include in the
\<extension\> section of the EPP response a \<fee:creData\> element that
contains the following child elements:

-   A \<fee:currency\> element;
-   One or more \<fee:fee\> elements;
-   An OPTIONAL \<fee:balance\> element;
-   An OPTIONAL \<fee:creditLimit\> element.

If no \<fee:creData\> element is included in the response, then no fee
has been assessed by the server for this transaction.

Example \<create\> command:

<div
style="display: table; width: 0; margin-left: 3em; margin-right: auto">

    C: <?xml version="1.0" encoding="utf-8" standalone="no"?>
    C: <epp xmlns="urn:ietf:params:xml:ns:epp-1.0">
    C:   <command>
    C:     <create>
    C:       <domain:create
    C:         xmlns:domain="urn:ietf:params:xml:ns:domain-1.0">
    C:         <domain:name>example.com</domain:name>
    C:         <domain:period unit="y">2</domain:period>
    C:         <domain:ns>
    C:           <domain:hostObj>ns1.example.net</domain:hostObj>
    C:           <domain:hostObj>ns2.example.net</domain:hostObj>
    C:         </domain:ns>
    C:         <domain:registrant>jd1234</domain:registrant>
    C:         <domain:contact type="admin">sh8013</domain:contact>
    C:         <domain:contact type="tech">sh8013</domain:contact>
    C:         <domain:authInfo>
    C:           <domain:pw>2fooBAR</domain:pw>
    C:         </domain:authInfo>
    C:       </domain:create>
    C:     </create>
    C:     <extension>
    C:       <fee:create xmlns:fee="urn:ietf:params:xml:ns:fee-0.6">
    C:         <fee:currency>USD</fee:currency>
    C:         <fee:fee>5.00</fee:fee>
    C:       </fee:create>
    C:     </extension>
    C:     <clTRID>ABC-12345</clTRID>
    C:   </command>
    C: </epp>

</div>

Example \<create\> response:

<div
style="display: table; width: 0; margin-left: 3em; margin-right: auto">

    S: <?xml version="1.0" encoding="utf-8" standalone="no"?>
    S: <epp xmlns="urn:ietf:params:xml:ns:epp-1.0">
    S:   <response>
    S:     <result code="1000">
    S:       <msg>Command completed successfully</msg>
    S:     </result>
    S:     <resData>
    S:       <domain:creData
    S:         xmlns:domain="urn:ietf:params:xml:ns:domain-1.0">
    S:         <domain:name>example.com</domain:name>
    S:         <domain:crDate>1999-04-03T22:00:00.0Z</domain:crDate>
    S:         <domain:exDate>2001-04-03T22:00:00.0Z</domain:exDate>
    S:       </domain:creData>
    S:     </resData>
    S:     <extension>
    S:       <fee:creData xmlns:fee="urn:ietf:params:xml:ns:fee-0.6">
    S:         <fee:currency>USD</fee:currency>
    S:         <fee:fee grace-period="P5D">5.00</fee:fee>
    S:         <fee:balance>-5.00</fee:balance>
    S:         <fee:creditLimit>1000.00</fee:creditLimit>
    S:       </fee:creData>
    S:     </extension>
    S:     <trID>
    S:       <clTRID>ABC-12345</clTRID>
    S:       <svTRID>54321-XYZ</svTRID>
    S:     </trID>
    S:   </response>
    S: </epp>

</div>

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 4.2.2.  EPP \<delete\> Command

This extension does not add any elements to the EPP \<delete\> command,
but does include elements in the response, when the extension has been
selected during the \<login\> command.

When the \<delete\> command has been processed successfully, and the
client selected the extension when it logged in, the server MAY include
in the \<extension\> section of the EPP response a \<fee:delData\>
element that contains the following child elements:

-   A \<fee:currency\> element;
-   One or more \<fee:credit\> elements;
-   An OPTIONAL \<fee:balance\> element;
-   An OPTIONAL \<fee:creditLimit\> element.

If no \<fee:delData\> element is included in the response, then no
credit has been assessed by the server for this transaction.

Example \<delete\> response:

<div
style="display: table; width: 0; margin-left: 3em; margin-right: auto">

    S: <?xml version="1.0" encoding="utf-8" standalone="no"?>
    S: <epp xmlns="urn:ietf:params:xml:ns:epp-1.0">
    S:   <response>
    S:     <result code="1000">
    S:       <msg>Command completed successfully</msg>
    S:     </result>
    S:     <extension>
    S:       <fee:delData
    S:         xmlns:fee="urn:ietf:params:xml:ns:fee-0.6">
    S:         <fee:currency>USD</fee:currency>
    S:         <fee:credit description="AGP Credit">-5.00</fee:credit>
    S:         <fee:balance>1005.00</fee:balance>
    S:       </fee:delData>
    S:     </extension>
    S:     <trID>
    S:       <clTRID>ABC-12345</clTRID>
    S:       <svTRID>54321-XYZ</svTRID>
    S:     </trID>
    S:   </response>
    S: </epp>

</div>

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 4.2.3.  EPP \<renew\> Command

This extension adds elements to both the EPP \<renew\> command and
response, when the extension has been selected during a \<login\>
command.

When submitting a \<renew\> command to the server, the client MAY
include in the \<extension\> element a \<fee:renew\> element which
includes the following child elements:

-   A \<fee:currency\> element;
-   One or more \<fee:fee\> elements.

When the \<renew\> command has been processed successfully, and the
client selected the extension when it logged in, the server MAY include
in the \<extension\> section of the EPP response a \<fee:renData\>
element that contains the following child elements:

-   A \<fee:currency\> element;
-   One or more \<fee:fee\> elements;
-   An OPTIONAL \<fee:balance\> element;
-   An OPTIONAL \<fee:creditLimit\> element.

If no \<fee:renData\> element is included in the response, then no fee
has been assessed by the server for this transaction.

Example \<renew\> command:

<div
style="display: table; width: 0; margin-left: 3em; margin-right: auto">

    C: <?xml version="1.0" encoding="utf-8" standalone="no"?>
    C: <epp xmlns="urn:ietf:params:xml:ns:epp-1.0">
    C:   <command>
    C:     <renew>
    C:       <domain:renew
    C:         xmlns:domain="urn:ietf:params:xml:ns:domain-1.0">
    C:         <domain:name>example.com</domain:name>
    C:         <domain:curExpDate>2000-04-03</domain:curExpDate>
    C:         <domain:period unit="y">5</domain:period>
    C:       </domain:renew>
    C:     </renew>
    C:     <extension>
    C:       <fee:renew xmlns:fee="urn:ietf:params:xml:ns:fee-0.6">
    C:         <fee:currency>USD</fee:currency>
    C:         <fee:fee>5.00</fee:fee>
    C:       </fee:renew>
    C:     </extension>
    C:     <clTRID>ABC-12345</clTRID>
    C:   </command>
    C: </epp>

</div>

Example \<renew\> response:

<div
style="display: table; width: 0; margin-left: 3em; margin-right: auto">

    S: <?xml version="1.0" encoding="utf-8" standalone="no"?>
    S: <epp xmlns="urn:ietf:params:xml:ns:epp-1.0">
    S:   <response>
    S:     <result code="1000">
    S:       <msg>Command completed successfully</msg>
    S:     </result>
    S:     <resData>
    S:       <domain:renData
    S:         xmlns:domain="urn:ietf:params:xml:ns:domain-1.0">
    S:         <domain:name>example.com</domain:name>
    S:         <domain:exDate>2005-04-03T22:00:00.0Z</domain:exDate>
    S:       </domain:renData>
    S:     </resData>
    S:     <extension>
    S:       <fee:renData xmlns:fee="urn:ietf:params:xml:ns:fee-0.6">
    S:         <fee:currency>USD</fee:currency>
    S:         <fee:fee grace-period="P5D">5.00</fee:fee>
    S:         <fee:balance>1000.00</fee:balance>
    S:       </fee:renData>
    S:     </extension>
    S:     <trID>
    S:       <clTRID>ABC-12345</clTRID>
    S:       <svTRID>54322-XYZ</svTRID>
    S:     </trID>
    S:   </response>
    S: </epp>

</div>

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 4.2.4.  EPP \<transfer\> Command

This extension adds elements to both the EPP \<transfer\> command and
response, when the value of the "op" attribute of the \<transfer\>
command element is "request", and the extension has been selected during
the \<login\> command.

When submitting a \<transfer\> command to the server, the client MAY
include in the \<extension\> element a \<fee:transfer\> element which
includes the following child elements:

-   A \<fee:currency\> element;
-   One or more \<fee:fee\> elements.

When the \<transfer\> command has been processed successfully, and the
client selected the extension when it logged in, the server MAY include
in the \<extension\> section of the EPP response a \<fee:trnData\>
element that contains the following child elements:

-   A \<fee:currency\> element;
-   One or more \<fee:fee\> elements;
-   An OPTIONAL \<fee:balance\> element;
-   An OPTIONAL \<fee:creditLimit\> element.

If no \<fee:trnData\> element is included in the response, then no fee
has been assessed by the server for this transaction.

Example \<transfer\> command:

<div
style="display: table; width: 0; margin-left: 3em; margin-right: auto">

    C: <?xml version="1.0" encoding="utf-8" standalone="no"?>
    C: <epp xmlns="urn:ietf:params:xml:ns:epp-1.0">
    C:   <command>
    C:     <transfer op="request">
    C:       <domain:transfer
    C:         xmlns:domain="urn:ietf:params:xml:ns:domain-1.0">
    C:         <domain:name>example.com</domain:name>
    C:         <domain:period unit="y">1</domain:period>
    C:         <domain:authInfo>
    C:           <domain:pw roid="JD1234-REP">2fooBAR</domain:pw>
    C:         </domain:authInfo>
    C:       </domain:transfer>
    C:     </transfer>
    C:     <extension>
    C:       <fee:transfer xmlns:fee="urn:ietf:params:xml:ns:fee-0.6">
    C:         <fee:currency>USD</fee:currency>
    C:         <fee:fee>5.00</fee:fee>
    C:       </fee:transfer>
    C:     </extension>
    C:     <clTRID>ABC-12345</clTRID>
    C:   </command>
    C: </epp>

</div>

Example \<transfer\> response:

<div
style="display: table; width: 0; margin-left: 3em; margin-right: auto">

    S: <?xml version="1.0" encoding="utf-8" standalone="no"?>
    S: <epp xmlns="urn:ietf:params:xml:ns:epp-1.0">
    S:   <response>
    S:     <result code="1001">
    S:       <msg>Command completed successfully; action pending</msg>
    S:     </result>
    S:     <resData>
    S:       <domain:trnData
    S:         xmlns:domain="urn:ietf:params:xml:ns:domain-1.0">
    S:         <domain:name>example.com</domain:name>
    S:         <domain:trStatus>pending</domain:trStatus>
    S:         <domain:reID>ClientX</domain:reID>
    S:         <domain:reDate>2000-06-08T22:00:00.0Z</domain:reDate>
    S:         <domain:acID>ClientY</domain:acID>
    S:         <domain:acDate>2000-06-13T22:00:00.0Z</domain:acDate>
    S:         <domain:exDate>2002-09-08T22:00:00.0Z</domain:exDate>
    S:       </domain:trnData>
    S:     </resData>
    S:     <extension>
    S:       <fee:trnData xmlns:fee="urn:ietf:params:xml:ns:fee-0.6">
    S:         <fee:currency>USD</fee:currency>
    S:         <fee:fee grace-period="P5D">5.00</fee:fee>
    S:       </fee:trnData>
    S:     </extension>
    S:     <trID>
    S:       <clTRID>ABC-12345</clTRID>
    S:       <svTRID>54322-XYZ</svTRID>
    S:     </trID>
    S:   </response>
    S: </epp>

</div>

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 4.2.5.  EPP \<update\> Command

This extension adds elements to both the EPP \<update\> command and
response, when the extension has been selected during a \<login\> has
been selected during the \<login\> command.

When submitting a \<update\> command to the server, the client MAY
include in the \<extension\> element a \<fee:update\> element which
includes the following child elements:

-   A \<fee:currency\> element;
-   One or more \<fee:fee\> elements.

When the \<update\> command has been processed successfully, and the
client selected the extension when it logged in, the server MAY include
in the \<extension\> section of the EPP response a \<fee:upData\>
element that contains the following child elements:

-   A \<fee:currency\> element;
-   One or more \<fee:fee\> elements;
-   An OPTIONAL \<fee:balance\> element;
-   An OPTIONAL \<fee:creditLimit\> element.

If no \<fee:upData\> element is included in the response, then no fee
has been assessed by the server for this transaction.

Example \<update\> command:

<div
style="display: table; width: 0; margin-left: 3em; margin-right: auto">

    C: <?xml version="1.0" encoding="utf-8" standalone="no"?>
    C: <epp xmlns="urn:ietf:params:xml:ns:epp-1.0">
    C:   <command>
    C:     <update>
    C:       <domain:update
    C:         xmlns:domain="urn:ietf:params:xml:ns:domain-1.0">
    C:         <domain:name>example.com</domain:name>
    C:         <domain:chg>
    C:           <domain:registrant>sh8013</domain:registrant>
    C:         </domain:chg>
    C:       </domain:update>
    C:     </update>
    C:     <extension>
    C:       <fee:update xmlns:fee="urn:ietf:params:xml:ns:fee-0.6">
    C:         <fee:currency>USD</fee:currency>
    C:         <fee:fee>5.00</fee:fee>
    C:       </fee:update>
    C:     </extension>
    C:     <clTRID>ABC-12345</clTRID>
    C:   </command>
    C: </epp>

</div>

Example \<update\> response:

<div
style="display: table; width: 0; margin-left: 3em; margin-right: auto">

    S: <?xml version="1.0" encoding="utf-8" standalone="no"?>
    S: <epp xmlns="urn:ietf:params:xml:ns:epp-1.0">
    S:   <response>
    S:     <result code="1000">
    S:       <msg>Command completed successfully</msg>
    S:     </result>
    S:     <extension>
    S:       <fee:updData xmlns:fee="urn:ietf:params:xml:ns:fee-0.6">
    S:         <fee:currency>USD</fee:currency>
    S:         <fee:fee>5.00</fee:fee>
    S:       </fee:updData>
    S:     </extension>
    S:     <trID>
    S:       <clTRID>ABC-12345</clTRID>
    S:       <svTRID>54321-XYZ</svTRID>
    S:     </trID>
    S:   </response>
    S: </epp>

</div>

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 4.3.  Formal Syntax

An EPP object mapping is specified in XML Schema notation. The formal
syntax presented here is a complete schema representation of the object
mapping suitable for automated validation of EPP XML instances.

Copyright (c) 2014 IETF Trust and the persons identified as authors of
the code. All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are
met:

-   Redistributions of source code must retain the above copyright
    notice, this list of conditions and the following disclaimer.
-   Redistributions in binary form must reproduce the above copyright
    notice, this list of conditions and the following disclaimer in the
    documentation and/or other materials provided with the distribution.
-   Neither the name of Internet Society, IETF or IETF Trust, nor the
    names of specific contributors, may be used to endorse or promote
    products derived from this software without specific prior written
    permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS
IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED
TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A
PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER
OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL,
EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO,
PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR
PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF
LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING
NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

<div
style="display: table; width: 0; margin-left: 3em; margin-right: auto">

    BEGIN
    <?xml version="1.0" encoding="utf-8"?>
    <schema xmlns="http://www.w3.org/2001/XMLSchema"
      xmlns:fee="urn:ietf:params:xml:ns:fee-0.6"
      xmlns:eppcom="urn:ietf:params:xml:ns:eppcom-1.0"
      xmlns:domain="urn:ietf:params:xml:ns:domain-1.0"
      targetNamespace="urn:ietf:params:xml:ns:fee-0.6"
      elementFormDefault="qualified">

      <import namespace="urn:ietf:params:xml:ns:eppcom-1.0" />
      <import namespace="urn:ietf:params:xml:ns:domain-1.0" />

      <annotation>
        <documentation>Extensible Provisioning Protocol
        v1.0 domain name extension schema for
        fee information.</documentation>
      </annotation>

      <!--
      Child elements found in EPP commands and responses
      -->
      <element name="check" type="fee:checkType" />
      <element name="chkData" type="fee:chkDataType" />
      <element name="info" type="fee:infoType" />
      <element name="infData" type="fee:infDataType" />
      <element name="create" type="fee:transformCommandType" />
      <element name="creData" type="fee:transformResultType" />
      <element name="renew" type="fee:transformCommandType" />
      <element name="renData" type="fee:transformResultType" />
      <element name="transfer" type="fee:transformCommandType" />
      <element name="trnData" type="fee:transferResultType" />
      <element name="update" type="fee:transformCommandType" />
      <element name="updData" type="fee:transformResultType" />
      <element name="delData" type="fee:deleteDataType" />

      <!--
      client <check> command
      -->
      <complexType name="checkType">
        <sequence>
          <element name="domain" type="fee:domainCheckType"
            maxOccurs="unbounded" />
        </sequence>
      </complexType>

      <complexType name="domainCheckType">
        <sequence>
          <element name="name" type="eppcom:labelType" />
          <element name="currency" type="fee:currencyType"
            minOccurs="0" />
          <element name="command" type="fee:commandType" />
          <element name="period" type="domain:periodType"
            minOccurs="0" />
        </sequence>
      </complexType>


      <!--
      server <check> result
      -->
      <complexType name="chkDataType">
        <sequence>
          <element name="cd" type="fee:domainCDType"
            maxOccurs="unbounded" />
        </sequence>
      </complexType>

      <complexType name="domainCDType">
        <sequence>
          <element name="name" type="eppcom:labelType" />
          <element name="currency" type="fee:currencyType" />
          <element name="command" type="fee:commandType" />
          <element name="period" type="domain:periodType"
            minOccurs="0" maxOccurs="1" />
          <element name="fee" type="fee:feeType"
             minOccurs="0" maxOccurs="unbounded" />
          <element name="class" type="token" minOccurs="0" />
        </sequence>
      </complexType>

      <!--
        client <info> command
      -->
      <complexType name="infoType">
        <sequence>
          <element name="currency" type="fee:currencyType"
            minOccurs="0" />
          <element name="command" type="fee:commandType" />
          <element name="period" type="domain:periodType"
            minOccurs="0" />
        </sequence>
      </complexType>

      <!--
        server <info> result
      -->
      <complexType name="infDataType">
        <sequence>
          <element name="currency" type="fee:currencyType" />
          <element name="command" type="fee:commandType" />
          <element name="period" type="domain:periodType"
            minOccurs="0" maxOccurs="1" />
          <element name="fee" type="fee:feeType"
            maxOccurs="unbounded" />
          <element name="class" type="token" minOccurs="0" />
        </sequence>
      </complexType>

      <!--
        general transform (create, renew, update, transfer) command
      -->
      <complexType name="transformCommandType">
        <sequence>
          <element name="currency" type="fee:currencyType" />
          <element name="fee" type="fee:feeType"
            maxOccurs="unbounded" />
        </sequence>
      </complexType>

      <!--
        general transform (create, renew, update) result
      -->
      <complexType name="transformResultType">
        <sequence>
          <element name="currency" type="fee:currencyType" />
          <element name="fee" type="fee:feeType"
            maxOccurs="unbounded" />
          <element name="balance" type="fee:balanceType"
            minOccurs="0" />
          <element name="creditLimit" type="fee:creditLimitType"
            minOccurs="0" />
        </sequence>
      </complexType>

      <!--
        transfer result
      -->
      <complexType name="transferResultType">
        <sequence>
          <element name="currency" type="fee:currencyType" />

          <!-- only used op="query" responses -->
          <element name="period" type="domain:periodType"
            minOccurs="0" />

          <element name="fee" type="fee:feeType"
            maxOccurs="unbounded" />
          <element name="balance" type="fee:balanceType"
            minOccurs="0" />
          <element name="creditLimit" type="fee:creditLimitType"
            minOccurs="0" />
        </sequence>
      </complexType>

      <!--
        delete result
      -->
      <complexType name="deleteDataType">
        <sequence>
          <element name="currency" type="fee:currencyType" />
          <element name="credit" type="fee:creditType"
            maxOccurs="unbounded" />
          <element name="balance" type="fee:balanceType"
            minOccurs="0" />
          <element name="creditLimit" type="fee:creditLimitType"
            minOccurs="0" />
        </sequence>
      </complexType>

      <!--
        common types
      -->
      <simpleType name="currencyType">
        <restriction base="string">
          <pattern value="[A-Z]{3}" />
        </restriction>
      </simpleType>

      <complexType name="commandType">
        <simpleContent>
          <extension base="fee:commandTypeValue">
            <attribute name="phase" type="token" />
            <attribute name="subphase" type="token" />
          </extension>
        </simpleContent>
      </complexType>

      <simpleType name="commandTypeValue">
        <restriction base="token">
          <minLength value="3"/>
          <maxLength value="16"/>
        </restriction>
      </simpleType>

      <simpleType name="nonNegativeDecimal">
        <restriction base="decimal">
          <minInclusive value="0" />
        </restriction>
      </simpleType>

      <simpleType name="negativeDecimal">
        <restriction base="decimal">
          <maxInclusive value="0" />
        </restriction>
      </simpleType>

      <complexType name="feeType">
        <simpleContent>
          <extension base="fee:nonNegativeDecimal">
            <attribute name="description"/>
            <attribute name="refundable" type="boolean"
              default="1" />
            <attribute name="grace-period" type="duration"
              default="P0D" />
            <attribute name="applied" default="immediate">
              <simpleType>
                <restriction base="token">
                  <enumeration value="immediate" />
                  <enumeration value="delayed" />
                </restriction>
              </simpleType>
            </attribute>
          </extension>
        </simpleContent>
      </complexType>

      <complexType name="creditType">
        <simpleContent>
          <extension base="fee:negativeDecimal">
            <attribute name="description"/>
          </extension>
        </simpleContent>
      </complexType>

      <simpleType name="balanceType">
        <restriction base="decimal" />
      </simpleType>

      <simpleType name="creditLimitType">
        <restriction base="decimal" />
      </simpleType>

    </schema>

    END

</div>

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 5.  Security Considerations

The mapping extensions described in this document do not provide any
security services beyond those described by [EPP<span> (</span><span
class="info">Hollenbeck, S., “Extensible Provisioning Protocol (EPP),”
August 2009.</span><span>)</span>](#RFC5730) [RFC5730], the [EPP domain
name mapping<span> (</span><span class="info">Hollenbeck, S.,
“Extensible Provisioning Protocol (EPP) Domain Name Mapping,”
August 2009.</span><span>)</span>](#RFC5731) [RFC5731], and protocol
layers used by EPP. The security considerations described in these other
specifications apply to this specification as well.

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 6.  IANA Considerations

This document uses URNs to describe XML namespaces and XML schemas
conforming to a registry mechanism described in [[RFC3688]<span>
(</span><span class="info">Mealling, M., “The IETF XML Registry,”
January 2004.</span><span>)</span>](#RFC3688). Two URI assignments were
requested and have been registered by the IANA.

Registration request for the digital signature namespace:

URI: urn:ietf:params:xml:ns:fee-0.6

Registrant Contact: See the "Author's Address" section of this document.

XML: None. Namespace URIs do not represent an XML specification.

Registration request for the EPP fee XML schema:

URI: urn:ietf:params:xml:ns:fee-0.6

Registrant Contact: See the "Author's Address" section of this document.

XML: See the "Formal Syntax" section of this document.

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 7.  Acknowledgements

The author wishes to thank the following persons for their feedback and
suggestions:

-   James Gould of Verisign
-   Luis Muñoz of ISC
-   Michael Young of Architelos
-   Ben Levac and Jeff Eckhaus of Demand Media
-   Seth Goldman of Google
-   Klaus Malorny and Michael Bauland of Knipp
-   Jody Kolker and Roger Carney of Go Daddy
-   Michael Holloway of Com Laude

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 8.  Change History

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 8.1.  Changes from 00 to 01

1.  Restore the \<check\> command extension; either \<check\> or
    \<info\> can be used.
2.  added extension elements for \<create\>, \<renew\>, \<transfer\> and
    \<update\> so that the server can reject the command if the fee is
    incorrect.

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 8.2.  Changes from 01 to 02

1.  Use Internet-Draft version number rather than XML namespace version
    number in this section.
2.  Support for multiple \<fee:fee\> and \<fee:credit\> elements.
3.  Added the "description" attribute to \<fee:fee\> and \<fee:credit\>
    elements.
4.  Added the \<fee:balance\> element.
5.  Added the \<fee:creditLimit\> element.
6.  Updated reference to [[draft‑ietf‑eppext‑launchphase]<span>
    (</span><span class="info">Gould, J., Tan, W., and G. Brown, “Launch
    Phase Mapping for the Extensible Provisioning Protocol (EPP),”
    2014.</span><span>)</span>](#draft-ietf-eppext-launchphase).
7.  Use \<fee:command\> instead of \<fee:action\>.
8.  Use a single child element of \<fee:chkData\> instead of multiple
    elements for each domain. This also requires using a different name
    (\<fee:name\>) for the domain name.
9.  Added the "refundable" attribute to \<fee:fee\> elements.
10. Added the "grace-period" attribute to \<fee:fee\> elements.

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 8.3.  Changes from 02 to 03

1.  Added the "applied" attribute to to \<fee:fee\> elements.
2.  Simplified the wording in relation to when a server can return an
    error for extended \<info\> commands.
3.  Added the \<fee:period\> element to transfer query responses.
4.  Removed wording about how servers behave when receiving incorrect
    fee information from transform commands, and put it into a single
    section at the top of the document.
5.  Allow servers to omit \<fee:fee\> elements from \<fee:cd\> elements
    if the command specified by the client is forbidden.

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 8.4.  Changes from 03 to 04

1.  As per suggestion from Michael Bauland, the \<fee:period\> element
    is no longer included in \<check\> and \<info\> responses for
    "restore" commands. It's still mandatory for all other commands.

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### 9. Normative References

  --------------------------------- ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  [ISO4217]                         International Organization for Standardization, “ISO 4217:2008, Codes for the representation of currencies and funds,” 2008 ([XML](http://www.currency-iso.org/dam/downloads/table_a1.xml), [HTML](http://www.iso.org/iso/home/standards/currency_codes.htm)).
  [RFC2119]                         [Bradner, S.](mailto:sob@harvard.edu), “[Key words for use in RFCs to Indicate Requirement Levels](http://tools.ietf.org/html/rfc2119),” BCP 14, RFC 2119, March 1997 ([TXT](http://www.rfc-editor.org/rfc/rfc2119.txt), [HTML](http://xml.resource.org/public/rfc/html/rfc2119.html), [XML](http://xml.resource.org/public/rfc/xml/rfc2119.xml)).
  [RFC3688]                         Mealling, M., “[The IETF XML Registry](http://tools.ietf.org/html/rfc3688),” BCP 81, RFC 3688, January 2004 ([TXT](http://www.rfc-editor.org/rfc/rfc3688.txt)).
  [RFC3915]                         Hollenbeck, S., “[Domain Registry Grace Period Mapping for the Extensible Provisioning Protocol (EPP)](http://tools.ietf.org/html/rfc3915),” RFC 3915, September 2004 ([TXT](http://www.rfc-editor.org/rfc/rfc3915.txt)).
  [RFC5730]                         Hollenbeck, S., “[Extensible Provisioning Protocol (EPP)](http://tools.ietf.org/html/rfc5730),” STD 69, RFC 5730, August 2009 ([TXT](http://www.rfc-editor.org/rfc/rfc5730.txt)).
  [RFC5731]                         Hollenbeck, S., “[Extensible Provisioning Protocol (EPP) Domain Name Mapping](http://tools.ietf.org/html/rfc5731),” STD 69, RFC 5731, August 2009 ([TXT](http://www.rfc-editor.org/rfc/rfc5731.txt)).
  [draft-ietf-eppext-launchphase]   Gould, J., Tan, W., and G. Brown, “[Launch Phase Mapping for the Extensible Provisioning Protocol (EPP)](http://tools.ietf.org/html/draft-ietf-eppext-launchphase),” 2014.
  --------------------------------- ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

\

------------------------------------------------------------------------

  ---------------
  [ TOC ](#toc)
  ---------------

### Author's Address

  --------- ------------------------------
            Gavin Brown
            CentralNic Group plc
            35-39 Moorgate
            London, England EC2R 6AR
            GB
  Phone:    +44 20 33 88 0600
  Email:    <gavin.brown@centralnic.com>
  URI:      <https://www.centralnic.com>
  --------- ------------------------------

