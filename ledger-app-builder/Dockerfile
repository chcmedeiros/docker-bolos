FROM ghcr.io/ledgerhq/ledger-app-builder/ledger-app-builder:latest

RUN apt-get update && apt-get install -y bear clang libclang-dev && rm -rf /var/lib/apt/lists/* # allows compile_commands.json generation

# The `clang` installed just above is Debian's (clang-19 on the trixie base) and ships
# no lld; it shadows the upstream clang-N as the driver that links the app. The secure
# SDKs link with `clang -fuse-ld=lld` (e.g. /opt/apex-secure-sdk Makefile.rules_generic),
# and Debian clang finds no `ld.lld` on its search path (only the upstream `ld.lld-N`
# exists, unversioned at /usr/bin/ld.lld is gone since the trixie/versioned-LLVM move),
# so the link fails with "invalid linker name in argument '-fuse-ld=lld'". The pre-trixie
# image had /usr/bin/ld.lld on PATH; restore it (version-agnostically) so clang+LLD links.
RUN set -eu; \
    target="$(ls /usr/bin/ld.lld-* 2>/dev/null | sort -V | tail -n1)"; \
    test -n "$target"; \
    update-alternatives --install /usr/bin/ld.lld ld.lld "$target" 100; \
    ld.lld --version

# for rust-based apps
RUN rustup target add thumbv6m-none-eabi
RUN rustup target add thumbv8m.main-none-eabi
RUN rustup component add rust-src

ADD entrypoint.sh /tmp/entrypoint.sh
ENTRYPOINT ["/tmp/entrypoint.sh"]
