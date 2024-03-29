FROM rustlang/rust:nightly as builder
RUN update-ca-certificates

# Create appuser
ENV USER=interpreter
ENV UID=10001

RUN adduser \
    --disabled-password \
    --gecos "" \
    --home "/nonexistent" \
    --shell "/sbin/nologin" \
    --no-create-home \
    --uid "${UID}" \
    "${USER}"


WORKDIR /interpreter

COPY ./ .

# We no longer need to use the x86_64-unknown-linux-musl target
RUN cargo build --release

####################################################################################################
## Final image
####################################################################################################
FROM debian:buster-slim

# Import from builder.
COPY --from=builder /etc/passwd /etc/passwd
COPY --from=builder /etc/group /etc/group

WORKDIR /interpreter

# Copy our build
COPY --from=builder /interpreter/target/release/interpreter ./

# Use an unprivileged user.
USER interpreter:interpreter

EXPOSE 3000
CMD ["/interpreter/interpreter"]

# docker build -t interpreter:debian -f Dockerfile .
