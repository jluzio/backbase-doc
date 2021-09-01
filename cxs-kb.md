# CXS

## Modules
- Portal: object model
- Audit: auditing user experience manager actions
- Content: static resources
- Thumbnail: generate thumbnail
- Provisioning: import widgets
- Rendition
- Targeting: targeting info (e.g. campaigns) for users with specific data

## Object model hierarchy
- Page
  - App
   - Container
    - Container
    - Widget
   - Widget

## enable mocks on experiences
localStorage.setItem('enableMocks', true)

## object model for peachtree bank
http://localhost:8080/gateway/api/portals/peachtree-bank.json

## pages
http://localhost:8080/gateway/cxp-manager
- after defining Peachtree Bank
http://localhost:8080/gateway/peachtree-bank
http://localhost:8080/gateway/api/portals/peachtree-bank.json

## permissions
Permissions are inherited of parent, by default.
Can customize by turning it off, then add permissions if needed.

## install data
- cx6-targeting
mvn clean install -Pclean-database
- statics
mvn bb:provision && mvn bb:import-experiences && mvn bb:import-packages
(imports FE components from outside)

## update data
- statics
mvn bb:provision && mvn bb:import-experiences && mvn bb:import-packages
q: does it reset associated info?
