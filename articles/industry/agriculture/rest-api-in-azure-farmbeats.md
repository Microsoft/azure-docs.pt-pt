---
title: Azure FarmBeats APIs
description: Conheça as APIs Azure FarmBeats, que fornecem às empresas agrícolas uma interface RESTful padronizada com respostas baseadas em JSON.
author: sunasing
ms.topic: article
ms.date: 11/04/2019
ms.author: sunasing
ms.openlocfilehash: 292374bcce90da042dc6bd672feaad3750759aa9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92674509"
---
# <a name="azure-farmbeats-apis"></a>Azure FarmBeats APIs

Este artigo descreve as APIs Azure FarmBeats. As APIs Azure FarmBeats proporcionam às empresas agrícolas uma interface RESTful padronizada com respostas baseadas em JSON para ajudá-lo a tirar partido das capacidades do Azure FarmBeats, tais como:

- APIs para obter sensor, câmara, drone, tempo, satélite, e dados de terra curados.
- Normalização e contextualização de dados em todos os fornecedores comuns de dados.
- Capacidades de acesso e consulta schematizadas em todos os dados ingeridos.
- Geração automática de metadados que podem ser consultados, com base em características agronómicas.
- A sequência de tempo gerada automaticamente agrega os agregados de sequência de tempo para a construção rápida do modelo.
- Motor integrado da Azure Data Factory para construir facilmente oleodutos de processamento de dados personalizados.

## <a name="application-development"></a>Programação de aplicações

As APIs FarmBeats contêm documentação técnica da Swagger. Para obter informações sobre todas as APIs e os respetivos pedidos ou respostas, consulte [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

A tabela seguinte resume todos os objetos e recursos no FarmBeats Datahub:

| Objetos e recursos | Description
--- | ---|
Quinta | A quinta corresponde a uma localização física de interesse dentro do sistema FarmBeats. Cada quinta tem um nome de fazenda e uma identificação única da fazenda. |
Dispositivo  | O dispositivo corresponde a um dispositivo físico presente na quinta. Cada dispositivo tem um ID de dispositivo único. Um dispositivo é normalmente a provisionado para uma fazenda com uma identificação agrícola.
DeviceModel  | O DeviceModel corresponde aos metadados do dispositivo, como o fabricante e o tipo de dispositivo, que é um gateway ou nó.
Sensor  | O sensor corresponde a um sensor físico que regista valores. Um sensor é normalmente ligado a um dispositivo com um ID do dispositivo.
SensorModel  | SensorModel corresponde aos metadados do sensor, como o fabricante, o tipo de sensor, que é analógico ou digital, e a medição do sensor, como temperatura ambiente e pressão.
Telemetria  | A telemetria fornece a capacidade de ler mensagens de telemetria para um determinado sensor e intervalo de tempo.
Tarefa  | O trabalho corresponde a qualquer fluxo de trabalho de atividades que são executadas no sistema FarmBeats para obter uma saída desejada. Cada trabalho está associado a uma identificação de trabalho e tipo de trabalho.
JobType  | JobType corresponde a diferentes tipos de trabalho suportados pelo sistema. Os tipos de trabalho definidos pelo sistema e definidos pelo utilizador estão incluídos.
Tipo Estendido  | O Sistema Alargado corresponde à lista de tipos definidos pelo sistema e pelo utilizador no sistema. O ExtendedType ajuda a configurar um novo tipo de sensor, cena ou ficheiro de cena no sistema FarmBeats.
Parceiro  | O parceiro corresponde ao parceiro de integração de sensores e imagens da FarmBeats.
Cena  | A cena corresponde a qualquer produção gerada no contexto de uma quinta. Cada cena tem uma identificação de cena, fonte de cena, tipo cena, e identificação de fazenda associada a ela. Cada ID de cena pode ter vários ficheiros de cena associados a ele.
CenaFile |O SceneFile corresponde a todos os ficheiros gerados para uma única cena. Um ID de cena único pode ter vários IDs sceneFile associados a ele.
Regra  |A regra corresponde a uma condição para que os dados relacionados com a exploração desencadeiem um alerta. Cada regra está no contexto dos dados de uma quinta.
Alerta  | O alerta corresponde a uma notificação, que é gerada quando uma condição de regra é cumprida. Cada alerta está no contexto de uma regra.
Interpretação Definição  | RoleDefinition define ações permitidas e proibidas para um papel.
Assinatura de Papéis  |FunAatribuição corresponde à atribuição de uma função a um utilizador ou a um principal de serviço.

### <a name="data-format"></a>Formato de dados

JSON é um formato comum de dados independente da linguagem que fornece uma simples representação de texto de estruturas de dados arbitrárias. Para mais informações, consulte o site da [JSON.](https://www.json.org/)

## <a name="authentication-and-authorization"></a>Autenticação e autorização

Os pedidos HTTP à API REST estão protegidos com diretório Azure Ative (Azure AD).
Para escrutinar um pedido autenticado às APIs REST, o código do cliente requer autenticação com credenciais válidas antes de poder ligar para a API. A autenticação é coordenada entre os vários atores da Azure AD. Fornece ao seu cliente um token de acesso como prova da autenticação. O token é então enviado no cabeçalho de autorização HTTP dos pedidos da API REST. Para saber mais sobre a autenticação Azure AD, consulte [o Azure Ative Directory](https://portal.azure.com) para programadores.

O token de acesso deve ser enviado em pedidos de API subsequentes, na secção de cabeçalho, como:

```http
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>HTTP solicitam cabeçalhos

Aqui estão os cabeçalhos de pedido mais comuns que deve especificar quando es faz uma chamada da API para o Azure FarmBeats Datahub.


**Cabeçalho** | **Descrição e exemplo**
--- | ---
Content-Type  | O formato de pedido (Tipo de Conteúdo: aplicação/ <format> ). Para a Azure FarmBeats Datahub APIs, o formato é JSON. Tipo de conteúdo: aplicação/json
Autorização  | Especifica o token de acesso necessário para fazer uma chamada da API. Autorização: <Access-Token> portador
Aceitar | O formato de resposta. Para a Azure FarmBeats Datahub APIs, o formato é JSON. Aceitar: candidatura/json

### <a name="api-requests"></a>Pedidos de API

Para esclartear um pedido de API DE REST, combina o método HTTP (GET, POST, PUT ou DELETE), o URL ao serviço API, o URI a um recurso para consultar, submeter dados para, atualizar ou apagar, e depois adicionar um ou mais cabeçalhos de pedido HTTP.

O URL do serviço API é o seu URL Datahub, por exemplo, https:// \<yourdatahub-website-name> .azurewebsites.net.

Opcionalmente, pode incluir parâmetros de consulta em chamadas GET para filtrar, limitar o tamanho e ordenar os dados nas respostas.

É utilizado o seguinte pedido de amostra para obter a lista de dispositivos:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

A maioria das chamadas GET, POST e PUT requerem um corpo de pedido JSON.

O seguinte pedido de amostra cria um dispositivo. Este pedido tem entrada JSON com o corpo de pedido.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>Parâmetros de consulta

Para chamadas REST GET, pode filtrar, limitar o tamanho e classificar os dados numa resposta API, incluindo um ou mais parâmetros de consulta sobre o pedido URI. Para os parâmetros de consulta, consulte a documentação da API e as chamadas GET individuais.
Por exemplo, quando consulta a lista de dispositivos (chamada GET em /Dispositivo), podem ser especificados os seguintes parâmetros de consulta:

![Lista de dispositivos](./media/references-for-azure-farmbeats/query-parameters-device-1.png)

### <a name="error-handling"></a>Processamento de erros

Azure FarmBeats Databeats Datahub APIs devolve os erros HTTP padrão. Os códigos de erro mais comuns são os seguintes:

 |Código de erro             | Description |
 |---                    | --- |
 |200                    | Com êxito |
 |201                    | Criar (Post) Sucesso |
 |400                    | Mau pedido. Há um erro no pedido. |
 |401                    | Não autorizado. O chamador da API não está autorizado a aceder ao recurso. |
 |404                    | Recurso não encontrado |
 |5XX                    | Erro do Servidor Interno. Os códigos de erro que começam com o 5XX significa que há algum erro no servidor. Consulte os registos do servidor e a seguinte secção para obter mais detalhes. |


Além dos erros HTTP padrão, a Azure FarmBeats Datahub APIs também devolve erros internos no seguinte formato:

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

Neste exemplo, quando uma quinta foi criada, o campo obrigatório "Nome" não foi especificado na carga útil de entrada. A mensagem de erro resultante seria:

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>Adicionar registos de utilizadores ou aplicações ao Azure Ative Directory

As APIs Azure FarmBeats podem ser acedidas por um utilizador ou por um registo de aplicações no Azure Ative Directory. Para criar um registo de aplicações no Azure Ative Directory, siga estes passos:

1. Aceda ao [portal Azure,](https://portal.azure.com)e selecione **Azure Ative Directory**  >  **App registro**  >  **Novas inscrições**. Em alternativa, pode utilizar uma conta existente.
2. Para uma nova conta, faça o seguinte:

    - Insira um nome.
    - Selecione **Contas neste diretório organizacional apenas (inquilino único)**.
    - Utilize os valores predefinidos no resto dos campos.
    - Selecione **Registar**.

3. No novo painel de **visão geral** do registo de aplicações, faça o seguinte:

    - Capture a **identificação do cliente** e **o ID do inquilino.**
    - Vá a **Certificados e Segredos** para gerar um novo segredo de cliente e capturar o **Segredo de Clientes.**
    - Volte ao **Overview**, e selecione o link ao lado **da Aplicação Manage no diretório local.**
    - Vá a **Propriedades** para capturar o **ID do objeto.**

4. Vá ao seu Datahub Swagger (https:// <yourdatahub> .azurewebsites.net/swagger/index.html) e faça o seguinte:
    - Vá para a **API de assinatura de papéis.**
    - Execute um POST para criar um objeto **roleAssignment** para o **ID do objeto** que acabou de criar.
 
```json
{
  "roleDefinitionId": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
  "objectId": "objectId from step 3 above",
  "objectIdType": "ServicePrincipalId",
  "tenantId": "tenant id of your Azure subscription"
}
```

  > [!NOTE]
  > Para obter mais informações sobre como adicionar utilizadores e registo de Diretório Ativo, consulte [o Diretório Ativo Azure](../../active-directory/develop/howto-create-service-principal-portal.md).

Depois de terminar os passos anteriores, o registo da sua aplicação (cliente) pode ligar para as APIs Azure FarmBeats utilizando um token de acesso através da autenticação do portador.

Utilize o token de acesso para enviá-lo em pedidos de API subsequentes na secção de cabeçalho como:

```http
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```