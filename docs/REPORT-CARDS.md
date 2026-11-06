# School-designed report templates and bulk generation

## Product decision

There is no browser-based report-card designer in the baseline. Schools supply their design, or the team helps prepare it externally. Teachers or authorised class heads enter marks in the application; approved records populate a versioned school template. Staff generate, download and print reports individually or by class/cohort. Template layout and academic calculation are separate concerns.

## Format and preparation

Use DOCX as the initial programmable template format, prepared in Word or LibreOffice Writer. An arbitrary supplied PDF/image is a design reference, not automatically a usable template: prepare an approved DOCX equivalent or explicitly scope a later rendering adapter. Avoid promising pixel-identical conversion from every format.

Define a small documented placeholder schema for required permanent platform matricule, optional distinctly labelled national/school matricules, student identity, school/year/term, repeated subject rows, mark states, coefficients, totals, averages, comments and approval labels. Choose and pin the actual template engine in step 10 after checking licence, supported loops and optional paid modules. Basic table/text generation must not require a hosted document API. Embedded static logos/signatures can be prepared within the approved template; do not assume dynamic images are free in every engine.

The template uses computed values from the approved academic snapshot. Do not execute uploaded formulas, macros, scripts, arbitrary expressions or external data links. Unsupported placeholders fail validation rather than printing blank marks silently. Templates are private, school-owned artifacts; application users cannot substitute a template belonging to another school.

## Ordered workflow

1. School provides design requirements and editable template/reference. Agree page size, language, subjects, signature policy and required fields.
2. Authorised template preparer adds the documented placeholders and repeated subject row, then uploads a draft template for that school.
3. Validate file safety, placeholder schema, required fields and supported template version; render synthetic samples with long names and differing subject counts.
4. School reviews the actual converted print PDF and approves the template version. Assign effective class/level, academic year and term scope with deterministic fallback; ambiguous/missing assignments block generation.
5. Subject teachers enter their marks; an authorised class head may enter marks across assigned subjects if explicitly granted. Neither role silently gains publishing authority.
6. Run academic validation/review. Approved result snapshot and template version become the immutable inputs to a generation batch.
7. Staff choose class/cohort and output formats/order, review completeness and submit one idempotent batch request. Show progress and per-student failures.
8. Workers create individual DOCX outputs and print PDFs, validate artifacts, then prepare a ZIP of individual files and an optional merged PDF with deliberate student page breaks.
9. Staff download an authorised preview batch. A complete export is separate from publication to parents; school policy controls when reviewed reports become visible.
10. Corrections create linked revisions. Existing generated/issued files retain their original inputs and template version; regeneration never silently overwrites history.

## Output policy

PDF is the print/distribution format. DOCX is an optional editable export for authorised staff, not the authoritative academic record: external edits do not change stored marks or published reports. Keep a generation manifest with school, result snapshot IDs, template hash/version, renderer version, font set, output hashes and generation time. Restrict template/signature editing and editable downloads.

Headless LibreOffice on self-hosted infrastructure is the planned DOCX-to-PDF conversion route, subject to fixture-based evaluation. Run untrusted document processing in a low-privilege isolated process/container with restricted network/filesystem access, independent temporary profiles, timeouts and CPU/memory limits. A worker process is not itself a security sandbox. Install/pin licensed fonts and conversion versions. Validate resulting files, page counts and required-field completion; do not rely on process exit status alone.

## Bulk scale and failure handling

- Create a durable batch and bounded per-student jobs; don't render an entire school in an HTTP request or one unbounded memory operation.
- Batch input is a fixed approved roster/snapshot/template. Mid-run mark/template/enrolment changes require a new batch or explicit revision, not mixed output.
- Isolate report rendering from short notification jobs. Use per-school concurrency/size quotas and fair scheduling; allow large schools to progress without starving small schools.
- Retry transient failures by student. Mark partial batches explicitly; never label missing students as a complete class export. Partial download requires an explicit user choice and a manifest of omissions.
- Stream large archives or build them in background storage. Escape filenames, include stable student IDs to avoid name collisions, and split oversized downloads by documented limits.
- Batch cancellation stops unstarted work and safely accounts for running jobs. Retry uses stable output identities; no duplicate publication/notifications.
- Expiring private download links recheck permission. A guardian downloads only their child's published artifact, never the class ZIP or merged PDF.
- Clean temporary documents after jobs; retain published artifacts and source templates under school retention policy. Backup template versions, fonts/config references and manifests alongside result snapshots.

## Required cases and acceptance

Add A11–A18 and I10 from EDGE-CASES to step 10. Verify the same platform matricule appears after a transfer and required identity fields cannot be silently blank. Verify two different school layouts, repeating rows, names with accents, many subjects, missing optional fields, invalid required placeholders, template replacement mid-batch, duplicate names, failed conversion, partial retry and cross-tenant access. School approves printed sample output before live publication. DOCX-to-PDF typography can differ from Word; reject or revise templates that do not pass that review.

Technical references (check current versions at implementation):
- https://docxtemplater.com/docs/
- https://help.libreoffice.org/latest/en-GB/text/shared/guide/start_parameters.html
- https://help.libreoffice.org/latest/en-US/text/shared/guide/pdf_params.html
