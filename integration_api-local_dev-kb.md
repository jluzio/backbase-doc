# to use apis easier
configuration can be something like:

backbase:
  security:
    mtls:
      enabled: false
    csrf:
      enabled: false
    public:
      paths:
        - /integration-api/**
