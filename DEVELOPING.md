# Working on the Ockam Balena Block

The Ockam Balena Block is a containerized Rust project that utilizes the Ockam inlet and outlet features.

## Rust Project

The Ockam Balena Block rust project is located in the `rust` directory. It uses the `ockam` crate as a dependency, and
specifies a few flags to shrink the release build.

The program creates either an Ockam inlet or outlet, depending on environment configuration. The details of the 
environment settings are in `README.md`.  This code should eventually be replaced with the Ockam Command CLI.

## Targets and dist directory

The `build.sh` script in the `rust` directory runs the build for multiple architectures. Currently, it attempts to build
for:
- x86_64-unknown-linux-musl
- aarch64-unknown-linux-musl

The development environment attempting to build the block should pre-install these targets. There has been some interest
in adding additional targets, such as arm7. This should be straight forward and done in the same fashion as the two
existing targets. The `.cargo/config.toml` file will need to also be updated to point to the new target's linker.

After building, the `build.sh` script will `strip` the binary to reduce the code size. The binaries are then copied
into the `dist` directory at the project root.

## Dockerfile

Balena uses a templated Dockerfile, namely `Dockerfile.template` which contains some Balena specific variables that
are populated at build time. The most important variables are:

- BALENA_MACHINE_NAME - the type of machine being targeted such as intel or raspberry pi
- BALENA_ARCH - Machine architecture. This block currently supports `aarch64` and `amd64`

## Publishing the Block to Balena Hub

### balena.yml

The `balena.yml` file contains the necessary metadata that describes the block contents and organization.

### Publishing with the fleet hack
According to Balena Support, as of the end of 2021:

- Create a fleet on balenaCloud
- Deploy the block into that fleet on balenaCloud
- Add the Github URL of the block on the Fleet Settings > Hub
- Copy your fleet ID (on the URL)
- Once this done, open your browser Developer Console on balenaCloud and then go to Console and type:

```javascript
fleetId = YOUR_FLEET_ID;
await sdk.pine.patch({
    resource: 'application',
    id: fleetId,
    body: {
        is_of__class: "block"
    }
});
```

NOTE: This requires at least one device associated to a fleet.