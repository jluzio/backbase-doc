# IPS

## Token Converter
- OIDC
Used for identity
- BAS
Used for custom authentication service

## OutOfMemory issues
Change platform/.mvn/jvm.config
from: -Xss256K -XX:ReservedCodeCacheSize=48M -XX:MaxDirectMemorySize=10M -XX:MaxMetaspaceSize=256000K -Xmx1G
to:   -Xss256K -XX:ReservedCodeCacheSize=48M -XX:MaxDirectMemorySize=10M -XX:MaxMetaspaceSize=1G -Xmx1G

# References
https://community.backbase.com/documentation/platform-services/1-11-7/reference
https://community.backbase.com/documentation/platform-services/1-11-7/jwt_properties
https://community.backbase.com/documentation/platform-services/1-11-7/http_client_configuration
(etc...)
