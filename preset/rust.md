---
title: Rust Preset
permalink: /preset/rust/
---
[Preset](../) / Rust

## Cargo.toml

Rustc lints, plus Clippy lints taken from [zero-postgres](https://github.com/elbaro/zero-postgres/blob/main/Cargo.toml).
In a workspace, rename the tables to `[workspace.lints.rust]` and
`[workspace.lints.clippy]`, and add `[lints] workspace = true` to each member.

```toml
[lints.rust]
non_ascii_idents = "forbid"
missing_docs = "warn"
trivial_numeric_casts = "warn"
unnameable_types = "warn"
unreachable_pub = "warn"
unused_import_braces = "warn"
unused_lifetimes = "warn"
unused_macro_rules = "warn"
unused_qualifications = "warn"

[lints.clippy]
all = { level = "deny", priority = -1 }
alloc_instead_of_core = "warn"
allow_attributes = "warn"
as_pointer_underscore = "warn"
as_underscore = "warn"
assertions_on_result_states = "warn"
cfg_not_test = "warn"
clone_on_ref_ptr = "warn"
cognitive_complexity = "warn"
create_dir = "warn"
dbg_macro = "warn"
decimal_literal_representation = "warn"
default_union_representation = "warn"
disallowed_script_idents = "warn"
doc_include_without_cfg = "warn"
empty_drop = "warn"
empty_enum_variants_with_brackets = "warn"
empty_structs_with_brackets = "warn"
exit = "warn"
extra_unused_lifetimes = "allow"
filetype_is_file = "warn"
float_cmp_const = "warn"
fn_to_numeric_cast_any = "warn"
if_then_some_else_none = "warn"
infinite_loop = "warn"
inline_asm_x86_att_syntax = "warn"
inline_asm_x86_intel_syntax = "warn"
iter_over_hash_type = "warn"
large_include_file = "warn"
lossy_float_literal = "warn"
map_err_ignore = "warn"
map_with_unused_argument_over_ranges = "warn"
mem_forget = "warn"
missing_asserts_for_indexing = "warn"
mixed_read_write_in_expression = "warn"
multiple_inherent_impl = "warn"
multiple_unsafe_ops_per_block = "warn"
mutex_atomic = "warn"
mutex_integer = "warn"
needless_raw_strings = "warn"
non_ascii_literal = "warn"
non_zero_suggestions = "warn"
panic = "warn"
panic_in_result_fn = "warn"
pathbuf_init_then_push = "warn"
precedence_bits = "warn"
pub_without_shorthand = "warn"
rc_buffer = "warn"
rc_mutex = "warn"
redundant_test_prefix = "warn"
redundant_type_annotations = "warn"
ref_patterns = "warn"
renamed_function_params = "warn"
rest_pat_in_fully_bound_structs = "warn"
return_and_then = "warn"
same_name_method = "warn"
self_named_module_files = "warn"
semicolon_inside_block = "warn"
shadow_same = "warn"
shadow_unrelated = "warn"
string_add = "warn"
string_lit_chars_any = "warn"
suspicious_xor_used_as_pow = "warn"
todo = "warn"
undocumented_unsafe_blocks = "warn"
unimplemented = "warn"
unnecessary_cast = "allow"
unnecessary_safety_comment = "warn"
unnecessary_safety_doc = "warn"
unnecessary_self_imports = "warn"
unneeded_field_pattern = "warn"
unreachable = "warn"
unused_result_ok = "warn"
verbose_file_reads = "warn"
```

## deny.toml

[cargo-deny](https://github.com/EmbarkStudios/cargo-deny) config, taken from [zero-postgres](https://github.com/elbaro/zero-postgres/blob/main/deny.toml).
Adjust `licenses.allow` to the licenses your dependencies use.

```toml
[graph]
all-features = true

[licenses]
version = 2
allow = [
    "BlueOak-1.0.0",
    "Apache-2.0",
    "MIT",
    "ISC",
    "BSD-3-Clause",
    "Unicode-3.0",
    "Zlib",
    "CDLA-Permissive-2.0",
]

[advisories]
version = 2

[bans]
multiple-versions = "warn"
wildcards = "deny"

[sources]
unknown-registry = "deny"
unknown-git = "deny"
```

```sh
cargo deny check
```

## .github/workflows/cargo-deny.yml

Runs cargo-deny with the `deny.toml` above, taken from [zero-postgres](https://github.com/elbaro/zero-postgres/blob/main/.github/workflows/cargo-deny.yml).

```yaml
name: cargo deny

on:
  push:
    branches: [main]
    paths:
      - '**/Cargo.toml'
      - '**/Cargo.lock'
  pull_request:
  merge_group:
  schedule:
    - cron: '0 3 * * 1'

jobs:
  audit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v7
      - uses: EmbarkStudios/cargo-deny-action@v2.1.1
```

## .github/workflows/release.yml

Opens release PRs and publishes to crates.io with [release-plz](https://release-plz.dev/), taken from [zero-postgres](https://github.com/elbaro/zero-postgres/blob/main/.github/workflows/release.yml).
`id-token: write` is for crates.io trusted publishing, so no registry token is stored.

{% raw %}
```yaml
name: Release-plz

on:
  push:
    branches:
      - main

permissions:
  id-token: write
  contents: write
  pull-requests: write

concurrency:
  group: release-plz-${{ github.ref }}
  cancel-in-progress: false

jobs:
  release-plz:
    name: Release-plz
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v7
        with:
          fetch-depth: 0
          persist-credentials: false
      - name: Install Rust toolchain
        uses: dtolnay/rust-toolchain@stable
      - name: Run release-plz
        uses: release-plz/action@v0.5
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```
{% endraw %}
