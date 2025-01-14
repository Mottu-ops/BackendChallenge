# BackendChallenge
Teste técnico de back-end da Mottu!

### **Instruções**

- O desafio é válido para diversos níveis, portanto não se preocupe se não conseguir resolver por completo.
- A aplicação só será avaliada se estiver rodando, se necessário crie um passo a passo para isso.
- Faça um clone do repositório em seu git pessoal para iniciar o desenvolvimento e não cite nada relacionado a Mottu.
- Após teste realizado, favor encaminha-lo via Link abaixo: Link: [Formulário - Mottu - Desafio Backend](https://forms.office.com/r/25yMPCax5S)

### **Requisitos não funcionais**

- A aplicação deverá ser construida com .Net utilizando C#.
- Utilizar apenas os seguintes bancos de dados (Postgress, MongoDB)
    - Não utilizar PL/pgSQL
- Escolha o sistema de mensageria de sua preferencia( RabbitMq, Sqs/Sns , Kafka, Gooogle Pub/Sub ou qualquer outro)

### **Aplicação a ser desenvolvida**

Seu objetivo é criar uma aplicação para gerenciar veiculos e seus documentos.

Siga os contratos e rotas a risca, iremos fazer a avaliação de forma automatizada, garanta que os jsons estejam exatamente como abaixo.

### Endpoints

1. **Authentication:**
    - `POST /api/auth/login`
        - **Description**: Autenticar o usuário
        Deve vir com dois usuarios pré configurados
            - email: admin@acme.com
            senha: admin123
            roles: vehicle-read, vehicle-admin
            - email: analista@acme.com
            senha: analista123
            roles: vehicle-read
        - **Payload**:
            
            ```json
            {
              "username": "admin@acme.com",
              "password": "admin123"
            }
            ```
            
        - **Response**: 200
            
            ```json
            {
            	"errors": [],
            	"result" : {
            	  "token": "<JWT Token>",
            	  "roles": [ "vehicle-read", "vehicle-admin" ]
            }
            ```
            
2. Vehicle
    - `GET /api/vehicles`
        - **Description**: Lista os veículos cadastrados. Requer role de leitura.
        - **Optional Query Parameters:** plate, ids (more than one)
        - **Response**: 200
            
            ```json
            {
                "errors": [],
                "result": [{
                        "id": 1,
                        "make": "Toyota",
                        "model": "Corolla",
                        "year": 2020,
                        "plate": "abc1234"
                    }, {
                        "id": 2,
                        "make": "Ford",
                        "model": "Focus",
                        "year": 2019,
                        "plate": "zzz1234"
                    }
                ]
            }
            ```
            
    - `POST /api/vehicles`
        - **Description**: Adiciona um novo veículo. Requer role de admin.
            - `make`: Opcional
            - `model`: Obrigatório
            - `year`: Obrigatório. Tem que ser ≥2020
            - `plate`: Obrigatório. Min de 7 caracteres. Alphanumérico.
        - **Payload**:
            
            ```json
            {
              "make": "Honda",
              "model": "Civic",
              "year": 2021,
              "plate": "xxx1234"
            }
            ```
            
        - **Response**: 201
            
            ```json
            {
            	"errors": [],
            	"result" : {
                      "id": 3,
                      "make": "Honda",
                      "model": "Civic",
                      "year": 2021,
                      "plate": "xxx1234"
                  }
            }
            ```
            
    - `PUT /api/vehicles/{id}`
        - **Description**:  Atualiza um veículo existente. Requer role de admin.
            - `make`: Opcional
            - `model`: Obrigatório
            - `year`: Obrigatório. Tem que ser ≥2020
            - `plate`: Obrigatório. Min de 7 caracteres. Alphanumérico.
        - **Payload**:
            
            ```json
            {
              "id": 3,
              "make": "Honda",
              "model": "Civic",
              "year": 2021,
              "plate": "xxx1234"
            }
            ```
            
        - **Response**: 200
            
            ```json
            {
            	"errors": [],
            	"result" : {
                      "id": 3,
                      "make": "Honda",
                      "model": "Civic",
                      "year": 2021,
                      "plate": "xxx1234"
                  }
            }
            ```
            
    - `DELETE /api/vehicles/{id}`
        - **Description**: Deleta um veículo existente. Requer role de admin.
        - **Response**: 204
    - `POST /api/vehicles/{id}/file`
        - **Description**: Adiciona um novo arquivo para o veículo. Requer role de admin
        - Junior
            
            O arquivo deve ser salvo no disco local, Amazon S3, MinIO ou outros.
            
            Não pode ser utilizado o banco de dados
            
            Aceita apenas arquivos dos tipos: .pdf, .png, .jpg, .jpeg
            
            - **Payload**:
                
                ```json
                {
                  "fileName": "crlv-2025.pdf",
                  "fileMimetype": "application/pdf",
                  "file": "base64"
                }
                ```
                
            - **Response**: 201
                
                ```json
                {
                	"errors": [],
                	"result" : {
                          "id": 1,
                          "vehicleId": 3,
                          "createdAt": "2025-01-01T01:01:01",
                				  "fileName": "crlv-2025.pdf",
                				  "fileMimetype": "application/pdf",
                				  "file": "base64"
                      }
                }
                ```
                
        - Pleno/Senior
            - **Payload**:
                
                ```json
                {
                  "fileName": "crlv-2025.pdf",
                  "fileMimetype": "application/pdf"
                }
                ```
                
            - Response: 200
                
                ```json
                {
                  "fileName": "crlv-2025.pdf",
                  "fileMimetype": "application/pdf",
                  "uploadUrl": "https://my-upload-url.acme.com/upload?file=?????"
                }
                ```
                
            ![image](https://github.com/user-attachments/assets/3785cd86-9ba9-4756-a265-2a5993cc4071)

            > Caso tenha algum código aplicado fora do repo: explique como foi implementado dentro do [readme.md](http://readme.md)
            > 
            - Storage
                - O arquivo deve ser salvo utilizando MinIO ou alguma solução cloud (Amazon S3, Azure Blob, Google Storage Bucket ou outros).
                - Não pode ser utilizado o banco de dados ou disco local
            - Queue
                - Algum serviço de mensageria da sua escolha: RabbitMQ, Kafka, SNS e etc
    - `GET /api/vehicles/{id}/file`
        - **Description**: Lista todos os arquivos do veículo. Requer role de leitura.
        - **Response**: 200
        - Junior
            
            ```json
            {
            	"errors": [],
            	"result" : [{
                      "id": 3,
                      "vehicleId": 3,
                      "createdAt": "2025-01-01T01:01:01",
            				  "fileName": "crlv-2025.pdf",
            				  "fileMimetype": "application/pdf",
            				  "file": "base64", // Ou a URL
            				  "fileUrl": "https://my-upload-url.acme.com/upload?file=?????" 
            	},{
                      "id": 4,
                      "vehicleId": 3,
                      "createdAt": "2025-01-01T01:01:01",
            				  "fileName": "crlv-2025.pdf",
            				  "fileMimetype": "application/pdf",
            				  "file": "base64", // Ou a URL
            				  "fileUrl": "https://my-upload-url.acme.com/upload?file=?????" 
            	}]
            }
            ```
            
        - Pleno/Senior
            
            ```json
            {
            	"errors": [],
            	"result" : [{
                      "id": 3,
                      "vehicleId": 3,
                      "createdAt": "2025-01-01T01:01:01",
            				  "fileName": "crlv-2025.pdf",
            				  "fileMimetype": "application/pdf",
            				  "fileUrl": "https://my-upload-url.acme.com/upload?file=?????"      
            	},{
                      "id": 4,
                      "vehicleId": 3,
                      "createdAt": "2025-01-01T01:01:01",
            				  "fileName": "crlv-2025.pdf",
            				  "fileMimetype": "application/pdf",
            				  "fileUrl": "https://my-upload-url.acme.com/upload?file=?????"      
            	}]
            }
            ```
            

Colocar as strings de mensagens de erros amigaveis quando acontecer na propriedade `errors`.

Explore as situações e respostas que podem ocorrer.

### **Diferenciais 🚀**

- Testes unitários
- Testes de integração
- EntityFramework e/ou Dapper
- Docker e Docker Compose
- Design Patterns
- Documentação
- Tratamento de erros
- Arquitetura e modelagem de dados
- Código escrito em língua inglesa
- Código limpo e organizado
- Logs bem estruturados
- Seguir convenções utilizadas pela comunidade
