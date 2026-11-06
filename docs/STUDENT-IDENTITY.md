# Permanent student identity, matricules and transfers

## Goal

One platform-wide student identity follows the same person across participating schools and school years. A transfer does not issue a new platform matricule. School records remain isolated and owned by their source school; identity continuity is not unrestricted history sharing.

## What the references establish

University of Buea registration guidance uses a student's matriculation number during registration; its admission guidance provides a student number on the student letter. These sources establish institutional identification, not an exact numbering algorithm or proof that UB identifiers are portable across universities. Do not copy an undocumented UB numbering pattern.

Cameroon's CarteScolaire FAQ describes a national matricule identifying a pupil throughout collège and lycée, and a MINESEC 2025 notice refers to allocation of the matricule unique. Our identifier must not pretend to replace, issue or verify that government identifier. No official API or integration permission is assumed.

Sources checked 2026-11-06:
- https://www.ubuea.cm/wp-content/uploads/2015/09/course_regfm.pdf (historical registration guidance; do not assume every old operational instruction remains current)
- https://www.ubuea.cm/index.php/faculties-schools/apply-to-university-of-buea/
- https://cartescolaire.cm/faq
- https://www.minesec.gov.cm/web/index.php/fr/communiques/item/1805-fermeture-de-la-plateforme-d-obtention-du-matricule-unique-pour-le-compte-de-l-annee-scolaire-2024-2025

## Three identifiers, different responsibilities

| Identifier | Scope / lifecycle | Rule |
|---|---|---|
| Internal student identity UUID | Global, permanent record key | Generated server-side; never school/year based |
| Platform matricule | Human-readable, globally unique and stable across participating schools | Required for verified platform identities; not a secret or login credential |
| National matricule | External identifier assigned by its authority | Optional until supplied/verified; keep namespace, original/canonical value, provenance and verification status |
| School admission/local matricule | School-owned identifier | Optional; unique within the relevant school namespace; preserve previous values/history |

The internal UUID is not a fourth user-facing number. Both the UUID and platform matricule have global database uniqueness. Never scope the platform matricule constraint by school ID. Store national identifiers separately so changes in external rules do not change our primary identity. Do not infer that an official matricule is globally valid merely because a user typed it.

## Platform matricule design

Use a neutral platform prefix plus a cryptographically random, human-readable body from an unambiguous alphabet and an error-detecting check character, formatted in groups. At implementation fix/document the alphabet, canonical uppercase normalisation, checksum and length; target at least 80 random bits before the check character. Use PostgreSQL global unique constraints plus bounded collision retries, not probability alone. Do not use student names, sex, birthday, phone, school code or current class in the permanent value. Do not expose a simple count through sequential numbers.

A check character detects input mistakes, not identity fraud. Knowing a matricule grants neither account recovery nor student-history access. Rate-limit/audit exact-match lookup; no broad public name search or platform-wide autocomplete directory. Printable IDs and any optional QR code contain only minimal identification data or an authenticated URL, never a bearer credential or embedded private marks.

Never reuse an issued matricule for a different person. Corrected/duplicate identities use preserved aliases with status/reason; do not silently rename existing reports. Account deletion/retention policy needs a restricted, minimal retired-identifier mechanism rather than keeping all child data forever.

## Logical entities and ownership

- Global student_identities: internal UUID, canonical platform matricule, lifecycle status and minimal identity metadata under dedicated policies. Keep biographical fields private; no unrestricted global student table access for school requests.
- external_student_identifiers: authority namespace, canonical/original identifier, evidence/verification status and correction history. Verified bindings are unique per authority namespace; unverified conflicting claims enter review and cannot take ownership of an existing identity. Do not reserve global verified bindings from unchecked imports.
- school_student_profiles: tenant-owned student details, linked to an approved global identity. Unique school/identity link where appropriate; enrolment history lives separately.
- identity_links/claims, identity_aliases, identity_reviews: provenance, approved matching, duplicate corrections and identity disputes.
- student_transfers, transfer_approvals, transfer_record_grants and transfer_manifest_items: source/destination, student identity, requested purpose, effective dates, approval, exact data scope, snapshots, expiry and audit.

School-owned marks, attendance, fees, messages and files continue to reference the tenant-local profile/enrolment with same-school constraints. Global identity does not create ordinary cross-school joins. Identity services use narrow operations/projections, separate from general school data access. Future database shards keep identity issuance/global uniqueness in a consistent central registry; shard-local uniqueness is insufficient.

## New registration and existing identity

1. Registrar asks for an existing platform matricule, official identifier if available and appropriate school-approved evidence. A supplied national number alone is not proof of ownership.
2. Exact lookup returns only the minimal permitted next action; an arbitrary school cannot fetch personal details or previous-school names by guessing IDs. A one-time, short-lived school-bound transfer/claim invitation can authorise a limited preview after verification.
3. If identity already exists, create a pending school link/transfer request. Never let the registrar claim it simply by presenting the number.
4. Verify identity and required guardian/student authority under school/platform policy; record evidence without collecting unnecessary sensitive documents.
5. If no identity exists and registration is verified, issue global identity/matricule transactionally with a unique operation key; create school-local profile/enrolment.
6. If uncertain, maintain a clearly labelled provisional application/profile. Do not automatically merge by name, birthday, phone or document similarity. Link it through reviewed identity resolution later; avoid issuing duplicate permanent IDs just to complete an import.

Concurrent first registrations can produce separate identity candidates when no verified shared identifier is available. A unique random matricule prevents number collisions, not two records for the same person. Flag evidence-based candidates privately for review; do not claim technical uniqueness alone guarantees one record per human.

## Authorised inter-school transfer

1. Destination registrar initiates a school-bound request against the claimed identity; platform verifies requester authority and evidence.
2. Verify student/guardian authority and record-sharing basis; source registrar reviews release of the specified records. Identifiers/identity linking and historical-record release are separate decisions.
3. Source supplies an approved manifest: for example student identification, enrolment dates and selected issued report cards/transcript/attendance summary. Private chats, raw discipline notes, guardian contact history and finance are excluded by default.
4. Destination registrar reviews and accepts. Validate effective dates, identity and intended class/year. No automatic promotion or equivalence of grades across grading schemes.
5. Atomically finalise transfer state, activate destination link/enrolment and close the specified source enrolment at the agreed effective date where required. Preserve a legitimate overlap only through explicit policy/approval.
6. Retain the same global platform matricule. Source retains its own original records; destination gets only approved, provenance-labelled snapshots via its grant, not blanket access to source tables.
7. Reverify guardian links/roles for the destination. Notify authorised participants and record completion idempotently; reconnect/revalidate live permissions.
8. Revocation/expiry stops future grant access; already lawfully exported copies follow documented retention obligations and cannot be remotely recalled.

States: requested → verification_pending → awaiting_release → awaiting_acceptance → approved_scheduled → completed; rejected/cancelled/expired are explicit alternatives. Finalisation checks approved versions and resolves conflicting concurrent requests. Failed/expired transfers do not silently withdraw a child or create active destination enrolment. A transfer request is not a payment or fee-debt settlement.

If the former school is unavailable, refuses release, leaves the platform or the student comes from outside the platform, route to documented registrar/identity-review appeal with verified authority and evidence. Don't invent consent, expose withheld records or permanently block legitimate enrolment just because the source cannot respond. A new school may admit through a reviewed identity link while historical release stays unavailable. Academic records are not automatically withheld because of an unpaid balance; school policy and applicable requirements need explicit review.

## Reports and exports

Report-card template schema includes required platform matricule plus optional labelled national matricule and school admission number. Required identity fields are snapshotted for generation. Legacy/provisional records block official batch generation until resolved or use an explicitly approved provisional label; never silently print another student's identifier.

Print the stable platform matricule on each report page where the school-approved layout requires student identification. Preserve historical reports after identity corrections; new editions reference the correction and canonical identity. Batch filenames/manifests use stable unique identifiers safely without publicly exposing class rosters.

## Duplicate correction and mistakes

Review candidate duplicates using more than name/phone; require privileged dual review for merges/splits according to defined policy. Freeze disputed identity linkage if needed, not unrelated access for every school. Preserve source-specific records, aliases and a reversible correction plan. Merge does not grant one guardian access to another child's records automatically. Never rewrite posted finance, published reports or source provenance. Erroneous merge/split has a controlled compensating correction and audit; no destructive bulk reassignment.

## Acceptance

Implement issuance/registry and protected identity linking in foundation/intake steps 02–03, before student data accumulates. Implement transfer review/manifest/grants in 03 with fixtures, then integrate reports in 10. Required cases: I01–I12. Test same student A→B retaining ID, A's data still private, duplicate first registrations, spoofed claim, revoked grant, unavailable source, external school, erroneous merge and national-ID conflict. Define local identity/record-sharing rules before real pupil transfers are enabled. No government integration is required for baseline continuity.
