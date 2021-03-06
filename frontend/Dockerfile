FROM golang:1.15 as builder
ENV USER=appuser \
    UID=10001 \
    GO111MODULE=on \
    CGO_ENABLED=0 \
    GOOS=linux \
    GOARCH=amd64
RUN adduser \
    --disabled-password \
    --gecos "" \
    --home "/nonexistent" \
    --shell "/sbin/nologin" \
    --no-create-home \
    --uid "${UID}" \
    "${USER}"
WORKDIR /app
RUN mkdir -p /app/cmd
COPY ./cmd /app/cmd/
RUN mkdir -p /app/pkg
COPY ./pkg /app/pkg/
COPY go.* /app/
ENV TZ=Asia/Kolkata
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && \
    echo $TZ > /etc/timezone
RUN cd /app \
    && go build -a -o frontend-server /app/cmd/server

FROM scratch
COPY --from=builder /etc/passwd /etc/passwd
COPY --from=builder /etc/group /etc/group
COPY --from=builder /etc/ssl/certs/ /etc/ssl/certs/
COPY --from=builder /etc/localtime /etc/localtime
COPY --from=builder /etc/timezone /etc/timezone
WORKDIR /app
USER ${USER}:${USER}
COPY --from=builder /app/frontend-server .
ENV USER=appuser \
    APP_PORT=8000
EXPOSE ${APP_PORT}
CMD ["/app/frontend-server"]
