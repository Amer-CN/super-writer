# Super Writer v0.3.0-rc1-hotfix.2 Test Report

## 1. Version Info

- **VERSION**: 0.3.0-rc1-hotfix.2
- **Date**: 2026-07-20
- **Test Count**: 180
- **HTML Integration**: Not started

## 2. Hotfix.2 Changes

### 2.1 Missing source_anchor must fail (H2.1)

- All non-signature roles must provide `source_anchor` with `exact_text` or `start_text`/`end_text`
- Empty/missing anchor → ERROR: `role '{role}' requires source_anchor`
- Only `article_signature` is exempt

### 2.2 evidence-map required (H2.2)

- When blocks contain `fact`, `statistic`, or `case` roles, `--evidence-map` is mandatory
- Missing evidence-map → ERROR: `evidence-map is required when blocks contain evidence-backed roles`
- Non-evidence articles (e.g., pure opinion) without evidence-map → PASS

### 2.3 start/end uniqueness within section (H2.3)

- `start_text` appearing >1 times within heading_path section → ERROR (even with heading_path)
- `end_text` appearing multiple times after start_text within section → ERROR (even with heading_path)
- Same start/end in different sections with correct heading_path → PASS

### 2.4 component_policy must be complete (H2.4)

- `component_policy` missing entirely → ERROR
- `component_policy: null` → ERROR
- `component_policy: []` (not dict) → ERROR
- Missing any of 6 required fields → ERROR
- No silent defaults; all fields must be explicitly present

### 2.5 URL provenance (H2.5)

- URLs no longer automatically exempt from provenance
- URL must be either:
  - A. Found in source span (article text), OR
  - B. Declared in `author_assets`/`external_assets` with `provenance: author_input` or `supplied_material`
- `provenance: generated` or `provenance: inferred` → ERROR
- Applies to: `article_cta`, `resource_list`, `image`, `gallery`, `video`, `media_text`, `long_image`

### 2.6 Delivery package cleanup (H2.6)

- Deleted `MANIFEST.sha256.tmp`
- Deleted `v0.3.0-rc1-test-integration-report.md` (old report with 117 tests)
- `package_zip.py` now excludes `*.tmp` files and old test reports
- ZIP integrity check verifies no `.tmp` files in package

## 3. Test Results

### 3.1 Full test suite

```
python -m pytest tests/ -q
```

**Result**: 180 passed

### 3.2 Test breakdown

| Category | Count | Status |
|----------|-------|--------|
| Original tests (test_01 ~ test_22) | 22 | All pass |
| H1 Role validation | 4 | All pass |
| H2 Template validation | 3 | All pass |
| H3 Formatter Root | 3 | All pass |
| H4 Deep Schema | 8 | All pass |
| H5 Anchor/Evidence | 3 | All pass |
| H6 Policy | 5 | All pass |
| H7 Fixture | 3 | All pass |
| H8 YAML error handling | 3 | All pass |
| Other | 2 | All pass |
| Calibration tests | 36 | All pass |
| Structure tests | 53 | All pass |
| F1 Content provenance (hotfix.1) | 8 | All pass |
| F1 URL provenance (hotfix.2) | 4 | All pass |
| F2 Anchor uniqueness (hotfix.1) | 4 | All pass |
| F3 Delivery package (hotfix.1) | 4 | All pass |
| **H2.1 Missing anchor (hotfix.2)** | **3** | **All pass** |
| **H2.2 Evidence-map required (hotfix.2)** | **4** | **All pass** |
| **H2.3 Section uniqueness (hotfix.2)** | **4** | **All pass** |
| **H2.4 Component policy complete (hotfix.2)** | **4** | **All pass** |
| **Total** | **180** | **All pass** |

### 3.3 Hotfix.2 new test details

| Test | Scenario |
|------|----------|
| test_h2_paragraph_empty_anchor_reports_error | paragraph + source_anchor:{} → ERROR |
| test_h2_fact_missing_source_anchor_reports_error | fact + no source_anchor key → ERROR |
| test_h2_signature_empty_anchor_passes | article_signature + source_anchor:{} → PASS |
| test_h2_fact_without_evidence_map_reports_error | fact + no --evidence-map → ERROR |
| test_h2_statistic_without_evidence_map_reports_error | statistic + no --evidence-map → ERROR |
| test_h2_case_without_evidence_map_reports_error | case + no --evidence-map → ERROR |
| test_h2_paragraph_without_evidence_map_passes | paragraph + no --evidence-map → PASS |
| test_h2_start_text_duplicate_within_section_reports_error | start_text ×2 in section → ERROR |
| test_h2_end_text_duplicate_within_section_reports_error | end_text ×2 in section → ERROR |
| test_h2_same_start_end_different_sections_disambiguated | Different sections → PASS |
| test_h2_same_start_end_duplicate_in_same_section_reports_error | Same section ×2 → ERROR |
| test_h2_component_policy_missing_reports_error | No component_policy → ERROR |
| test_h2_component_policy_null_reports_error | component_policy: null → ERROR |
| test_h2_component_policy_list_reports_error | component_policy: [] → ERROR |
| test_h2_component_policy_missing_field_reports_error | Missing one field → ERROR |
| test_f1_payload_url_not_in_article_reports_error | URL not in span/assets → ERROR |
| test_f1_url_in_source_span_passes | URL in article → PASS |
| test_f1_url_in_author_assets_passes | URL in author_assets → PASS |
| test_f1_url_author_assets_generated_provenance_reports_error | provenance=generated → ERROR |

## 4. Fixture Validation

### All three fixtures: 0 error / 0 warning

| Fixture | Type | Blocks | Evidence roles | Result |
|---------|------|--------|---------------|--------|
| Fixture A (simple) | Opinion | 10 | None | 0 error, 0 warning |
| Fixture B (tutorial) | Tutorial | 16 | None | 0 error, 0 warning |
| Fixture C (analysis) | Analysis | 15 | fact + case | 0 error, 0 warning |

Fixture C uses `--evidence-map` and passes evidence-map requirement check.

## 5. Delivery Package

### 5.1 ZIP

- **Filename**: super-writer-v0.3.0-rc1-hotfix.2.zip
- **File count**: 69 (68 manifest files + MANIFEST.sha256)
- **Integrity**: ZIP check PASSED
- **No .tmp files**: PASSED
- **No old reports**: PASSED

### 5.2 MANIFEST.sha256

```
python scripts/check_manifest.py MANIFEST.sha256
```

**Result**: OK: 68, FAIL: 0

### 5.3 Python compilation

```
python -m compileall -q scripts tests
```

**Result**: All files compiled successfully

## 6. Validator Security Checklist

| Check | Status |
|-------|--------|
| Schema validation | ✅ |
| Role vocabulary (41 roles) | ✅ |
| Source anchor required (non-signature) | ✅ |
| Source anchor locatable in article | ✅ |
| Source anchor unique (exact_text) | ✅ |
| start/end unique within section | ✅ |
| heading_path hierarchy validation | ✅ |
| Content provenance (payload ← source span) | ✅ |
| URL provenance (source span or author_assets) | ✅ |
| evidence-map required for fact/statistic/case | ✅ |
| evidence_id lookup against evidence-map | ✅ |
| Formatter candidate registration check | ✅ |
| Formatter candidate compatibility check | ✅ |
| component_policy must exist as dict | ✅ |
| component_policy all 6 fields required | ✅ |
| key_statement ≤ 5 | ✅ |
| Advanced components ≤ max | ✅ |
| article_signature ≤ 1, must be last | ✅ |
| No HTML/CSS/hex color | ✅ |
| CTA URL must be HTTPS | ✅ |
| Resource URLs must be HTTPS | ✅ |
| Malformed YAML → structured error | ✅ |

## 7. Conclusion

Hotfix.2 closes all 4 remaining validation gaps from hotfix.1 review:
1. Missing anchor now fails for all content blocks
2. Evidence-map is mandatory when evidence-backed roles are present
3. start/end uniqueness is enforced within sections (not just globally)
4. component_policy must be a complete dict with all 6 required fields

Additionally, URL provenance is now enforced (no automatic exemption), and the delivery package is cleaned of stale files.

**Ready for HTML integration phase.**
