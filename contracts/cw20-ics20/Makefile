.PHONY: check
check:
	cargo check

.PHONY: clippy
clippy:
	cargo clippy

.PHONY: build
build: src/*.rs Cargo.toml Cargo.lock
	RUSTFLAGS='-C link-arg=-s' cargo build --release --target wasm32-unknown-unknown --locked
	@# The following line is not necessary, may work only on linux (extra size optimization)
	wasm-opt -Oz ./target/wasm32-unknown-unknown/release/cw20_ics20.wasm -o ./contract.wasm

.PHONY: build-reproducible
build-reproducible:
	docker run --rm -v "$$(pwd)":/contract \
		--mount type=volume,source="$$(basename "$$(pwd)")_cache",target=/code/target \
		--mount type=volume,source=registry_cache,target=/usr/local/cargo/registry \
		enigmampc/secret-contract-optimizer:1.0.9

.PHONY: clean
clean:
	cargo clean
	rm -f ./contract.wasm

.PHONY: run-localsecret kill-localsecret test build-hermes

run-localsecret:
	docker compose -f test/docker-compose.yml up

kill-localsecret:
	docker compose -f test/docker-compose.yml stop 
	docker compose -f test/docker-compose.yml rm -f 

test: build
	(cd test && yarn && npx jest --forceExit)