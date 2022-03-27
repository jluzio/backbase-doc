Direct Integration service
==========================

What is?
--------
Objective:
Facilitates simple integration between widget and source system

When to use:
- source system provides all functionalities widget expects
- responds in timely manner
- no need to persist data to achieve the desired functionality
For other scenarios use DBS


Integration services:
- don't require authentication since the meant to be invoked in trusted environments
- useful for ingestion and other operations


## Implement

* Generate the specification

      mvn archetype:generate \
       -DarchetypeArtifactId=raml-specifications-archetype \
       -DarchetypeGroupId=com.backbase.archetype \
       -DarchetypeVersion=11.2.1 \
       -DserviceName=atm-location-specification \
       -DservicePackageName=location


* Define the specification content

      #%RAML 1.0
      ---
      title: Locations
      version: v1
      baseUri: "{version}"
      mediaType:  application/json
      protocols: [ HTTP, HTTPS ]
      #===============================================================
      # API resource definitions
      #===============================================================
      /locations:
        displayName: Locations
        get:
          responses:
            200:
              body:
                application/json:
                  type: !include schemas/locations-get-response.json


* Generate the service

      mvn archetype:generate \
          -DarchetypeArtifactId=core-service-archetype \
          -DarchetypeGroupId=com.backbase.archetype \
          -DarchetypeVersion=11.2.1 \
          -DserviceName=atm-location-service \
          -Dpackage=com.backbase.training


* Include other SSDK libraries or other specifications if necessary        

* Create the REST controller implementing the API
  ```java
  @RestController
  public class AtmLocationsController implements LocationsApi {
  }
  ```