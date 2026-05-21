# PQC status for Microsoft Internet Information Services (IIS)

## PQC available
No, not without putting a PQC capable reverse proxy in front of the IIS server.

## More information
IIS gets its TLS capabilities from Schannel which in turn uses crypto modules through the CNG API. Even though CNG has PQC capabilities these are not exposed through Schannel as of yet (2026-05-21).

## References
https://learn.microsoft.com/en-us/windows-server/security/tls/tls-ssl-schannel-ssp-overview
https://techcommunity.microsoft.com/blog/microsoft-security-blog/post-quantum-cryptography-apis-now-generally-available-on-microsoft-platforms/4469093
https://www.microsoft.com/en-us/security/blog/2025/08/20/quantum-safe-security-progress-towards-next-generation-cryptography/
