---
title: Referências
description: ''
author: sunasing
ms.topic: article
ms.date: 11/04/2019
ms.author: sunasing
ms.openlocfilehash: 46c2bad6aa4e5c2e72a7e46ed944e2ca8d25bddc
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77050424"
---
# <a name="references"></a>Referências

Este artigo descreve as APIs FarmBeats do Azure.

## <a name="rest-api"></a>API REST

As APIs do Azure FarmBeats fornecem aos agricultural empresas uma interface RESTful padronizada com respostas baseadas em JSON para ajudá-lo a aproveitar os recursos do Azure FarmBeats, como:

- APIs para obter os dados de sensor, câmera, drone, clima, satélite e terra organizada.
- Normalização e concontexto de dados entre provedores de dados comuns.
- Esquematizados os recursos de acesso e consulta em todos os dados ingeridos.
- Geração automática de metadados que podem ser consultados, com base nos recursos do agronomic.
- Agregações de sequência de tempo geradas automaticamente para criação rápida de modelos.
- O mecanismo de Azure Data Factory integrado para criar facilmente pipelines de processamento de dados personalizados.

## <a name="application-development"></a>Programação de aplicações

As APIs FarmBeats contêm documentação técnica do Swagger. Para obter informações sobre todas as APIs e os seus correspondentes pedidos ou respostas, consulte [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

A tabela a seguir resume todos os objetos e recursos no FarmBeats Datahub.

| Objetos e recursos | Descrição
--- | ---|
Farm | O farm corresponde a um local físico de interesse no sistema FarmBeats. Cada Farm tem um nome de farm e uma ID de farm exclusiva. |
Dispositivo  | O dispositivo corresponde a um dispositivo físico presente no farm. Cada dispositivo tem uma ID de dispositivo exclusiva. Um dispositivo é normalmente provisionado em um farm com uma ID de farm.
DeviceModel  | DeviceModel corresponde aos metadados do dispositivo, como o fabricante e o tipo de dispositivo, que é o gateway ou o nó.
Sensores  | O sensor corresponde a um sensor físico que registra valores. Um sensor normalmente é conectado a um dispositivo com uma ID de dispositivo.
SensorModel  | SensorModel corresponde aos metadados do sensor, como o fabricante, o tipo de sensor, que é analógico ou digital, e a medição do sensor, como temperatura ambiente e pressão.
Telemetria  | A telemetria fornece a capacidade de ler mensagens de telemetria para um determinado sensor e intervalo de tempo.
Tarefa  | O trabalho corresponde a qualquer Workflow de atividades executadas no sistema FarmBeats para obter uma saída desejada. Cada trabalho é associado a uma ID de trabalho e tipo de trabalho.
JobType  | JobType corresponde a diferentes tipos de trabalho com suporte no sistema. Os tipos de trabalho definidos pelo sistema e definidos pelo usuário estão incluídos.
ExtendedType  | O extendetype corresponde à lista de tipos definidos pelo usuário e pelo sistema no sistema. O Extended ajuda a configurar um novo tipo de arquivo de sensor, cena ou cena no sistema FarmBeats.
Partner  | O parceiro corresponde ao parceiro de integração de sensor e imagens para FarmBeats.
Cena  | A cena corresponde a qualquer saída gerada no contexto de um farm. Cada cena tem uma ID de cena, origem da cena, tipo de cena e ID de farm associada a ela. Cada ID de cena pode ter vários arquivos de cena associados a ela.
Scenefile |O scenefile corresponde a todos os arquivos que são gerados para uma única cena. Uma única ID de cena pode ter várias IDs de Scenefile associadas a ela.
Regra  |A regra corresponde a uma condição para que os dados relacionados ao farm disparem um alerta. Cada regra está no contexto dos dados de um farm.
Alerta  | O alerta corresponde a uma notificação, que é gerada quando uma condição de regra é atendida. Cada alerta está no contexto de uma regra.
RoleDefinition  | RoleDefinition define ações permitidas e não autorizadas para uma função.
RoleAssignment  |RoleAssignment corresponde à atribuição de uma função a um usuário ou a uma entidade de serviço.

### <a name="data-format"></a>Formato de dados

JSON é um formato de dados comum independente de linguagem que fornece uma representação de texto simples de estruturas de dados arbitrárias. Para mais informações, consulte o site da [JSON.](https://www.json.org/)

## <a name="authentication-and-authorization"></a>Autenticação e autorização

As solicitações HTTP para a API REST são protegidas com Azure Active Directory (Azure AD).
Para fazer uma solicitação autenticada para as APIs REST, o código do cliente requer autenticação com credenciais válidas para que você possa chamar a API. A autenticação é coordenada entre os vários atores pelo Azure AD. Ele fornece ao cliente um token de acesso como prova de autenticação. O token é então enviado no cabeçalho de autorização HTTP de solicitações da API REST. Para saber mais sobre a autenticação da Azure AD, consulte [o Azure Ative Directory](https://portal.azure.com) para programadores.

O token de acesso deve ser enviado em solicitações de API subsequentes, na seção de cabeçalho, como:

```
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>Cabeçalhos de solicitação HTTP

Aqui estão os cabeçalhos de solicitação mais comuns que você deve especificar ao fazer uma chamada à API para o Azure FarmBeats Datahub.


**Cabeçalho** | **Descrição e exemplo**
--- | ---
Tipo de conteúdo  | O formato de pedido (Tipo de conteúdo: aplicação/<format>). Para APIs do Azure FarmBeats Datahub, o formato é JSON. Tipo de conteúdo: aplicativo/JSON
Autorização  | Especifica o token de acesso necessário para fazer uma chamada à API. Autorização: portador de acesso < token >
Aceitar | O formato de resposta. Para APIs do Azure FarmBeats Datahub, o formato é JSON. Aceitar: aplicativo/JSON

### <a name="api-requests"></a>Solicitações de API

Para fazer uma solicitação de API REST, você combina o método HTTP (GET, POST, PUT ou DELETE), a URL para o serviço de API, o URI para um recurso para consultar, enviar dados para, atualizar ou excluir e, em seguida, adicionar um ou mais cabeçalhos de solicitação HTTP.

O URL do serviço API é o seu URL Datahub, por exemplo, https://\<o nome do site datahub>.azurewebsites.net.

Opcionalmente, você pode incluir parâmetros de consulta em GET calls para filtrar, limitar o tamanho e classificar os dados nas respostas.

A seguinte solicitação de exemplo é usada para obter a lista de dispositivos:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

A maioria das chamadas GET, POST e PUT exigem um corpo de solicitação JSON.

A solicitação de exemplo a seguir cria um dispositivo. Esta solicitação tem um JSON de entrada com o corpo da solicitação.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>Parâmetros de consulta

Para chamadas GET de REST, você pode filtrar, limitar o tamanho e classificar os dados em uma resposta de API, incluindo um ou mais parâmetros de consulta no URI de solicitação. Para os parâmetros de consulta, consulte a documentação da API e as chamadas GET individuais.
Por exemplo, quando você consulta a lista de dispositivos (GET Call on/device), os seguintes parâmetros de consulta podem ser especificados:

![Lista de dispositivos](./media/references-for-azure-farmbeats/query-parameters-device-1.png)

### <a name="error-handling"></a>Processamento de erros

As APIs do Azure FarmBeats Datahub retornam os erros HTTP padrão. Os códigos de erro mais comuns são os seguintes:

 |Código de erro             | Descrição |
 |---                    | --- |
 |200                    | Êxito |
 |201                    | Criar (post) êxito |
 |400                    | Solicitação inadequada. Há um erro na solicitação. |
 |401                    | Não autorizado. O chamador da API não está autorizado a acessar o recurso. |
 |404                    | Recurso não encontrado |
 |5XX                    | Erro interno do servidor. Os códigos de erro que começam com 5XX significam que há algum erro no servidor. Consulte os logs do servidor e a seção a seguir para obter mais detalhes. |


Além dos erros HTTP padrão, as APIs do Datahub do Azure FarmBeats também retornam erros internos no seguinte formato:

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

Neste exemplo, quando um farm foi criado, o campo obrigatório "Name" não foi especificado na carga de entrada. A mensagem de erro resultante seria:

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>Adicionar usuários ou registros de aplicativo a Azure Active Directory

As APIs FarmBeats do Azure podem ser acessadas por um usuário ou um registro de aplicativo no Azure Active Directory. Para criar um registro de aplicativo no Azure Active Directory, siga estas etapas.

1. Vá ao [portal Azure](https://portal.azure.com), e selecione **Registos** de > App **Do Diretório Ativo do Azure** > **Nova inscrição.** Como alternativa, você pode usar uma conta existente.
2. Para uma nova conta, faça o seguinte:

    - Insira um nome.
    - Selecione **Contas apenas neste diretório organizacional (Inquilino Único)** .
    - Use os valores padrão no restante dos campos.
    - Selecione **Registar**.

3. No novo e existente painel **de visão geral** da aplicação, faça o seguinte:

    - Capture o ID do **Cliente** e o ID do **Inquilino**.
    - Vá a **Certificados e Segredos** para gerar um novo segredo de cliente e capture o **Segredo do Cliente.**
    - Volte à **visão geral**e selecione o link ao lado da Manage Application no **diretório local**.
    - Vá a **Propriedades** para capturar o ID do **Objeto**.

4. Vá ao seu [Datahub Swagger](https://<yourdatahub>.azurewebsites.net/swagger/index.html) e faça o seguinte:
    - Vá para a **RoleAssignment API.**
    - Execute um POST para criar um objeto **roleAssignment** para o ID do **objeto** que acabou de criar.

  > [!NOTE]
  > Para obter mais informações sobre como adicionar utilizadores e registo de Diretório Ativo, consulte [o Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Depois de concluir as etapas anteriores, seu registro de aplicativo (cliente) pode chamar as APIs FarmBeats do Azure usando um token de acesso por meio da autenticação de portador.

Use o token de acesso para enviá-lo em solicitações de API subsequentes na seção de cabeçalho como:

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
