Open points
- TLS 1.2 vs TLS 1.3 What are the changes done?
- Multiplexing in HTTP2. What is supported? 

<h2> Topics </h2>
1. Intro
	1. Prior version of HTTP
	2. QUIC protocol
2. Overview of HTTP/3
3. Connection setup & Managment
	1. Discovery
	2. Establishing connection
	3. Connection reuse
4. Semantics in HTTP/3
	1. Frames
	2. Fields
	3. Control data
5. Connection closure
6. Stream
7. 

Summary
- QUIC Connection supports protocol negotiation & multiplexing over streams (Multiplexing is the ability to open multiple TCP connections over the same socket.)
- Server push is introduced in HTTP/2. Server push is a mechanism where server can reach to a client & initiate communication. This was designed to save latencies & trade off here is network congestion
- Frame is the smallest unit of communication in a stream. Frame consists of few bits. 
- 













ref - https://www.rfc-editor.org/rfc/rfc9114.pdf

