# Treasure map

The purpose of this document is to provide security researchers with context about assets in scope for our bug bounty program. By providing this information we want researchers to have a better understanding of the assets that are more relevant to Tools For Humanity and World, with the aim of helping them discover more and better vulnerabilities. This document will guide researchers through our applications, APIs, and infrastructure, highlighting key areas for testing.

We strongly recommend subscribing to notifications for this repository to stay updated on any changes to the treasure map.

## Web Assets

### World App Backend

#### Description

The World App backend is a REST API that interfaces with a set of services responsible for powering the core functionality of the World App. Some of this functionality includes handling authentication through [SIWE](https://eips.ethereum.org/EIPS/eip-4361), processing users’ data deletion requests, integrating with different fraud prevention and monitoring components, handling user invites and referrals, integrating with third party cryptocurrency on-ramps. The World App backend is powered by AWS Lambdas.

#### Areas to explore

If you’re aiming for high severity, high impact vulnerabilities we recommend starting with our SIWE implementation. Any issues affecting the robustness of this component could potentially lead to critical outcomes in the logic of the different microservices consumed by World App, as this is the first layer for user authentication against the backend.

### World ID

#### Description

World ID is a digital identity that allows users to demonstrate that they are a specific unique human to a third party without compromising their privacy, thanks to Zero Knowledge Proofs (ZKPs). To achieve this, an identity commitment (a commitment derived from the private key, details available in the [Semaphore Docs](https://docs.semaphore.pse.dev/)) is created during signup. If this identity commitment is unique, it’s added as a leaf in a Merkle tree. Then, when users want to verify ownership of their identity commitment without revealing which one, they generate a ZKP. This ZKP demonstrates that they know the private key corresponding to an identity commitment within the Merkle tree. The ZKP is then passed to a verifier smart contract, which checks the proof against the Merkle root to confirm membership and validity.

World ID also works as an OIDC provider, acting as a bridge between the World protocol and third-party apps to simplify integration efforts.

A detailed explanation of the protocol is available, we highly recommend reading the whitepaper available [here](https://whitepaper.world.org/#overall-architecture-and-user-flow) to have a better understanding of the project.

#### Areas to explore

Issues related to the consistency and uniqueness of identity commitments and the integrity of the Merkle tree and ZKPs to prevent duplication, collision, or unauthorized alterations

[Personal Custody Protocol (PCP)](https://world.org/blog/announcements/worldcoin-implements-personal-custody) keeps World ID users’ sensitive data stored exclusively on their devices. Through multi-layer encryption, data remains secure in transit and decryptable solely on the user’s device, ensuring no third party can access it, including Worldcoin or Tools for Humanity. Being able to access or tamper with data packages from PCP is an area of high interest for our bug bounty program.

Additional points of focus include the robustness of authentication flows, token handling, and the protection of sensitive information from World ID’s OIDC provider implementation.

### Developer Portal

#### Description

The World Developer Portal contains a set of tools to enable developers build and manage integrations with World ID (external applications) and World App (Mini Apps). Developers can use the developer portal to manage incognito actions, external applications and Mini Apps,  or use the simulator to debug their integrations. Additionally, the Developer Portal provides a public REST API to facilitate verification of Zero Knowledge Proofs, retrieval of JWK keys for JSON signature verification and retrieval of metadata for incognito actions. Additional documentation is avaliable [here](https://docs.world.org/world-id).


#### Areas to explore

The Developer Portal’s REST API supports ZKP validation, a key mechanism for secure user identity verification without revealing private details. Ensuring that ZKPs are accurately verified and consistently protected through this API is crucial. Potential issues in this area, such as improper validation logic or exposure of verification details, could compromise privacy and weaken the security guarantees ZKPs are designed to provide.

We are interested in learning about any ways for unauthorized changes to be made to applications belonging to other developers or to unauthorizedly access their data. Such issues could include modifying application configuration, accessing application credentials, or altering application settings such as the number of unique verifications per user.

