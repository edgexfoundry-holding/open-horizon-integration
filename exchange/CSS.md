# Configuration file for the syncronization service

 Sync service configuration parameters can be provided using a
 configuration file (sync.conf by default) and/or environment variables.  
 If the same parameter appears in both the value specified by the environment variable is used.
 Most of the parameters are optional and their default value can be used. 
 Mandatory parameters are marked as must be provided by the user.   

# Basic Configuration

## NodeType specifies whether this node is a CSS or ESS
 Possible values are: CSS, ESS 
 Defaults to CSS
 Environment variable: NODE_TYPE


## DestinationType specifies the destination type of this ESS
 Not used (ignored) on the CSS (DestinationType set internally)
 Environment variable: DESTINATION_TYPE
 This parameter must be provided by the user on an ESS

## DestinationId specifies the destination id of this ESS
 The combination of DestinationType and DestinationId must uniquely identify the node (within the organization)
 Not used (ignored) on the CSS (DestinationID set internally)
 Environment variable: DESTINATION_ID
 This parameter must be provided by the user on an ESS

## OrgID specifies the organization ID of this node
 Environment variable: ORG_ID
 Defaults to blank (not set)
 For ESS an OrgID must be provided by the user
 For CSS OrgID is optional. If specified the CSS will support only a single organization
 For a CSS to support multiple organizations OrgID must be blank (not set) and organizations should be added/removed via the API

## ListeningType specifies whether the server is
 listening securely, unsecurely, both securely and unsecurely, or using Unix sockets
 Possible values are secure, unsecure, both, unix.
 unix can only be specified if the NodeType is ESS.
 Defaults to unsecure on a CSS and secure on an ESS
 Environment variable: LISTENING_TYPE

## ListeningAddress specifies the address to listen on for HTTP/HTTPS API requests
 If the ListeningType is unix, this property specifies the socket file to be used.
 The file will be erased and recreated, if it already exists. The filename is relative
 to the PersistenceRootPath configuration property if it doesn't start with a slash (/).
 Defaults to blank (all addresses on this node)
 Environment variable: LISTENING_ADDRESS

## SecureListeningPort specifies the port to listen on for API requests via HTTPS
 Defaults to 8443
 Environment variable: SECURE_LISTENING_PORT

## UnsecureListeningPort specifies the port to listen on for API requests via HTTP 
 Defaults to 8080
 Environment variable: UNSECURE_LISTENING_PORT

## CommunicationProtocol is a comma separated list of protocols to be used for communication between CSS and ESS
 The elements of the list can be 'http', 'mqtt', and 'wiotp'
 wiotp indicates MQTT communication via the Watson IoT Platform and mqtt indicates direct MQTT communication to a broker
 The list must not include both wiotp and mqtt (only one mode of MQTT communication is allowed)
 For ESS only a single protocol is allowed
 The default is mqtt
 Environment variable: COMMUNICATION_PROTOCOL


## ServerCertificate specifies the Cloud Server side certificate to use to serve as HTTPS
 This value can either be the certificate itself or the path of a file containing
 the certificate. If it is a path of a file, then it is relative to the
 PersistenceRootPath configuration property if it doesn't start with a slash (/).
 
 NOTE: On the edge side, if a certificate is provided as a value, it will be used. Otherwise,
 the edge side uses a self signed certificate which will be located at sync/certs/cert.pem
 relative to the PersistenceRootPath configuration property.

## Environment variable: SERVER_CERTIFICATE
### .crt

 ServerKey specifies the Cloud Server side key to use to serve as HTTPS.  This value can
 either be the key itself or the path of a file containing the key.  If it is a path of
 a file, then it is relative to the PersistenceRootPath configuration property if it
 doesn't start with a slash (/).
 
 NOTE: On the edge side, if a key is provided as a value, it will be used. Otherwise,
 the edge side uses a locally generated key which will be located at sync/certs/key.pem
 relative to the PersistenceRootPath configuration property.

 Environment variable: SERVER_KEY
### .key

 PersistenceRootPath is the root path for storing persisted data.
 The information stored under PersistenceRootPath may include user data.
 It is recommended to set PersistenceRootPath to an encrypted partition.
 Default value: /var/wiotp-edge/persist
 Environment variable: PERSISTENCE_ROOT_PATH

# MQTT Communication Settings

## MQTTClientId contains the MQTT client id
 If not provided a random clientId is generated 
 Environment variable: MQTT_CLIENT_ID

## MQTTUserName contains the MQTT user name (used in CONNECT messages)
 Default is not to include user name in CONNECT messages 
 Environment variable: MQTT_USER_NAME

## MQTTPassword contains the MQTT password (used in CONNECT messages)
 Default is not to include password in CONNECT messages 
 Environment variable: MQTT_PASSWORD

## MQTTUseSSL specifies whether or not to use ssl connection with the MQTT broker
 Defaults to true
 Environment variable: MQTT_USE_SSL

## MQTTCACertificate specifies the CA certificate that was used to sign the server certificates
 used by the MQTT broker. This value can either be the CA certificate itself or the path of 
 a file containing the CA certificate. If it is a path of a file, then it is relative to the
 PersistenceRootPath configuration property if it doesn't start with a slash (/).
 This parameter must be provided when communicating with the broker securely over TLS
 and the broker's certificate wasn't signed by a well known certificate authority (i.e. a
 self signed certificate).
 Default value: /broker/ca/ca.cert.pem
 Environment variable: MQTT_CA_CERTIFICATE 
MQTTCACertificate 

## MQTTSSLCert specifies the SSL client certificate of the X509 key pair used to communicate
 with the MQTT broker. This value can either be the certificate itself or the path of a file 
 containing the certificate. If it is a path of a file, then it is relative to the
 PersistenceRootPath configuration property if it doesn't start with a slash (/).
 The default is not to use a SSL client certificate
 Environment variable: MQTT_SSL_CERT

## MQTTSSLKey specifies the SSL client key of the X509 key pair used to communicate with the
 the MQTT broker. This value can either be the key itself or the path of a file containing 
 the key. If it is a path of a file, then it is relative to the PersistenceRootPath
 configuration property if it doesn't start with a slash (/).
 The default is not to use a SSL client key
 Environment variable: MQTT_SSL_KEY

## MQTTAllowInvalidCertificates specifies that the MQTT client will not attempt to validate the server certificates
 Please only set this for development purposes! It makes using TLS pointless and is never the right answer.
 Defaults to false
 Environment variable: MQTT_ALLOW_INVALID_CERTIFICATES
 MQTTAllowInvalidCertificates

## BrokerAddress specifies the address to connect to for the MQTT broker or
 a list of server URIs for environments with multiple MQTT brokers
 If BrokerAddress is a json array, it contains multiple broker addresses and
 the messaging group name. Otherwise it contains a single broker address
 Defaults to localhost
 Environment variable: BROKER_ADDRESS

## BrokerPort specifies the port to connect to for the MQTT broker
 Defaults to 1883
 Environment variable: BROKER_PORT

## MaxDataChunkSize specifies the maximum size of data that can be sent in one MQTT message
 Defaults to 120KB
 Environment variable: MAX_DATA_CHUNK_SIZE
 MaxDataChunkSize 122880

# HTTP Communication Settings

## HTTPPollingInterval specifies the frequency in seconds of ESS HTTP polling for updates
 Environment variable: HTTP_POLLING_INTERVAL
 HTTPPollingInterval 10

## HTTPCSSHost specifies on the ESS, the CSS host for HTTP communication
 ESS only parameter, ignored on CSS
 This parameter must be provided when CommunicationProtocol is set to http  
 Environment variable: HTTP_CSS_HOST
 HTTPCSSHost

## HTTPCSSPort specifies on the ESS, the CSS host for HTTP communication
 ESS only parameter, ignored on CSS
 This parameter must be provided when CommunicationProtocol is set to http  
 Environment variable: HTTP_CSS_PORT
 HTTPCSSPort

## HTTPCSSUseSSL specifies whether or not to use ssl connection with the CSS
 ESS only parameter, ignored on CSS
 This parameter must be provided when CommunicationProtocol is set to http and
 you want to communicate to the CSS securely using TLS.
 Defaults to false
 Environment variable: HTTP_CSS_USE_SSL
 HTTPCSSUseSSL false

## HTTPCSSCACertificate specifies the CA certificate that was used to sign the server certificate
 used by the CSS. This value can either be the CA certificate itself or the path of a file containing
 the CA certificate. If it is a path of a file, then it is relative to the
 PersistenceRootPath configuration property if it doesn't start with a slash (/).
 This parameter must be provided when CommunicationProtocol is set to http,
 you want to communicate to the CSS securely using TLS, and the CSS's certificate wasn't
 signed by a well known certificate authority (i.e. a self signed certificate).
 Default value: none
 Environment variable: HTTP_CSS_CA_CERTIFICATE

# Logging Parameters

## LogLevel specifies the logging level in string format
 Possible values are: NONE, STATUS, FATAL, ERROR, WARNING, INFO, DEBUG, TRACE
 Defaults to INFO
 Environment variable: LOG_LEVEL

## LogRootPath specifies the root path for the log files
 Defaults to /var/edge-sync-service/log
 Environment variable: LOG_ROOT_PATH

## LogTraceDestination is a comma separated list of destinations for the logging and tracing
 The elements of the list can be `file`, `stdout`, and `syslog`
 Defaults to `file`
 Environment variable: LOG_TRACE_DESTINATION

## LogFileName specifies the name of the log file
 Defaults to sync-service
 Environment variable: LOG_FILE_NAME

## TraceLevel specifies the tracing level in string form (NONE, STATUS, FATAL, ERROR, WARNING, INFO, DEBUG, TRACE)
 Defaults to INFO
 Environment variable: TRACE_LEVEL

## TraceRootPath specifies the root path for the trace files
 Defaults to /var/edge-sync-service/trace
 Environment variable: TRACE_ROOT_PATH

## TraceFileName specifies the name of the trace file
 Defaults to sync-service
 Environment variable: TRACE_FILE_NAME

## Maximal size of a trace/log file in kilo bytes.
 Default value: 20000
 Environment variable: LOG_TRACE_FILE_SIZE_KB

## The limit on the number of compressed files of trace/log.
 Default value: 50
 Environment variable: MAX_COMPRESSED_LOG_TRACE_FILES_NUMBER

## LogTraceMaintenanceInterval specifies the frequency in seconds of log and trace maintenance (memory consumption, etc.)
 Default value: 60
 Environment variable: LOG_TRACE_MAINTENANCE_INTERVAL

# Storage Configuration (CSS only)

## MongoAddressCsv specifies one or more addresses of the mongo database
 Defaults to localhost:27017
 Environment variable: MONGO_ADDRESS_CSV


## MongoAuthDbName specifies the name of the database used to establish credentials and privileges
 Defaults to admin
 Environment variable: MONGO_AUTH_DB_NAME


## MongoDbName specifies the name of the database to use
 Defaults to d_edge
 Environment variable: MONGO_DB_NAME
 MongoDbName d_edge

## MongoUsername specifies the username of the mongo database
 Default is empty string 
 Environment variable: MONGO_USERNAME
 MongoUsername

## MongoPassword specifies the password of the mongo database
 Default is empty string 
 Environment variable: MONGO_PASSWORD
 MongoPassword

## MongoUseSSL specifies whether or not to use ssl when connecting to the database
 Defaults to false
 Environment variable: MONGO_USE_SSL
 MongoUseSSL

## MongoCACertificate specifies the CA certificate that was used to sign the server certificate
 used by the MongoDB server. This value can either be the CA certificate itself or the path of a
 file containing the CA certificate. If it is a path of a file, then it is relative to the
 PersistenceRootPath configuration property if it doesn't start with a slash (/).
 This parameter must be provided when communicating with the MongoDb server securely over TLS
 and the MongoDB server's certificate wasn't signed by a well known certificate authority (i.e.
 a self signed certificate).
 Environment variable: MONGO_SSL_CA_FILE
 MongoCACertificate

## MongoAllowInvalidCertificates specifies that the mongo driver will not attempt to validate the server certificates
 Please only set this for development purposes! It makes using TLS pointless and is never the right answer.
 Defaults to false
 Environment variable: MONGO_ALLOW_INVALID_CERTIFICATES
 MongoAllowInvalidCertificates

## StorageMaintenanceInterval specifies the frequency in seconds of storage checks (for expired objects, etc.)
 The default value is 30 seconds
 Environment variable: STORAGE_MAINTENANCE_INTERVAL
 StorageMaintenanceInterval

# Advanced Settings

## ResendInterval specifies the frequency in seconds of checks to resend unacknowledged notifications
 ESS resends register notification with this interval
 Other notifications are resent with frequency equal to ResendInterval*6
 Defaults to 5
 Environment variable: RESEND_INTERVAL
 ResendInterval 5

## LeadershipTimeout is the timeout for leadership updates in seconds
 Defaults to 30
 Environment variable: LEADERSHIP_TIMEOUT

## ObjectActivationInterval specifies the frequency in seconds of checking if there are inactive objects
 that are ready to be activated
 Defaults to 30
 Environment variable: OBJECT_ACTIVATION_INTERVAL
 ObjectActivationInterval

## DatabaseConnectTimeout specifies the timeout in seconds of database connection attempts on startup
 Default is 300
 Environment variable: DATABASE_CONNECT_TIMEOUT
 DatabaseConnectTimeout

## MQTTBrokerConnectTimeout specifies the timeout in seconds of attempts to connect to the MQTT broker on startup
 Default value 300
 Environment variable: MQTT_BROKER_CONNECT_TIMEOUT
 MQTTBrokerConnectTimeout

# Performance Tuning Settings

## MQTTParallelMode specifies the parallelism mode by which incoming MQTT messages are processed
 Possible values: 'none', 'small', 'medium', 'large'
 Default is none (or empty string), which means no parallel processing
 It is recommended to change the default if the CSS is expected to process updates at a high rate
 The choice between small, medium, or large should be based on the resources available on the CSS machine
 and the expected rate of updates 
 Environment variable: PARALLEL_MQTT_MODE
 MQTTParallelMode

## MaxInflightChunks defines how many in-flight chunks are allowed when transferring large objects
 When transferring lrge objects over it is recommended to set MaxInflightChunks to a value between 10 and 100
 Default is 1
 Environment variable: MAX_INFLIGHT_CHUNKS
 MaxInflightChunks

## MongoSessionCacheSize specifies the number of MongoDB session copies to use
 To handle high update rate it is recommended to use a value between 32 and 512
 Default is 1
 Environment variable: MONGO_SESSION_CACHE_SIZE
 MongoSessionCacheSize

