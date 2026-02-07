
## Product Choice

Telegram
link : <https://web.telegram.org/>
Telegram is a cross-platform messenger. The app allows to exchange text, voice, and video messages, as well as stickers, photos, and files in many formats.

## Main components

![Telegram Component Diagram](docs\diagrams\out\telegram\component-diagram\Component%20Diagram.svg)

[Telegram Component Diagram Code](docs\diagrams\src\telegram\component-diagram.puml)

Client - Компонент, представляющий клиентское приложение, которое взаимодействует с сервером Telegram через API. Он обрабатывает пользовательский интерфейс и отправляет/получает сообщения.

Server - Основной серверный компонент, который обрабатывает все запросы от клиентов, управляет сессиями и координирует передачу сообщений между пользователями.

Message Storage - Компонент для хранения сообщений и истории чатов. Обеспечивает сохранность данных и быстрый доступ к ним.

Authentication Service - Сервис, отвечающий за аутентификацию пользователей, проверку учетных данных и управление сессиями безопасности.

External APIs - Компонент, обеспечивающий интеграцию с внешними сервисами и API, позволяя Telegram использовать сторонние функции и услуги.

## Data flow

[Telegram Sequence Diagram](docs\diagrams\out\telegram\sequence-diagram\Sequence%20Diagram.svg)

[Telegram Sequence Diagram Code](docs\diagrams\src\telegram\sequence-diagram.puml)

Media Upload (Encrypted Parts)
This group represents the initial phase of sending a media message in Telegram, focusing on securely uploading the media file before sending the actual message.

The user selects a photo in the mobile app and initiates sending it to another user (Bob)
The mobile app uploads the media file in encrypted chunks using the saveFilePart RPC method
Each chunk is processed by the Media Service and stored in the Distributed File System (DFS)
File metadata is saved in the database with ownership information
The process repeats until the entire file is uploaded in parts
Components That Talk to Each Other:
Mobile App - MTProto Gateway:

Data exchanged: Encrypted file bytes and file ID via saveFilePart RPC call
Purpose: Secure transmission of media chunks from client to server
MTProto Gateway ↔ Media Service:

Data exchanged: Streamed file data from gateway to service
Purpose: Forwarding the file data to the appropriate service for processing
Media Service ↔ Distributed File System (DFS):

Data exchanged: File chunks written to storage, checksums returned
Purpose: Storing the actual media data in a distributed manner
Media Service ↔ Sharded Database:

Data exchanged: File metadata (ownership, file properties) saved and acknowledged
Purpose: Maintaining records of who owns the file and its properties
Media Service - MTProto Gateway → Mobile App:

Data exchanged: ACKnowledgments confirming each part was saved
Purpose: Providing feedback to the client about upload progress

## Deployment

[Telegram Deployment Diagram](docs\diagrams\out\telegram\deployment-diagram\Deployment%20Diagram.svg)

[Telegram Deployment Diagram Code](docs\diagrams\src\telegram\deployment-diagram.puml)

Telegram’s client applications are deployed on user devices, while its server-side components are distributed across data centers. The system operates through secure internet connections that route encrypted communications between clients and servers. All message data and user information are processed and stored across this distributed infrastructure to ensure global availability and security.

## Assumptions

I assume the cloud storage system implements deduplication to optimize storage costs for shared media files

I assume the Telegram architecture implements a distributed microservices model with asynchronous event-driven communication via Kafka to enable scalable and resilient message handling across different service components.

## Open questions

How does the data flow look like in secret chats?

How does the encryption works for media?
