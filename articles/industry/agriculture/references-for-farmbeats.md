---
title: Referências para FarmBeats
description: ''
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 057037807a75e50eb2305bfab19d1fcff7fe77ce
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889599"
---
# <a name="references"></a>Referências

Veja abaixo uma coleção de notas e instruções que descrevem as APIs FarmBeats do Azure.

## <a name="rest-api"></a>API REST

As APIs FarmBeats do Azure fornecem agricultural Business com uma interface RESTful padronizada com respostas baseadas em JSON e isso o ajudará a aproveitar os recursos do Azure FarmBeats:

- API para obter sensor, câmera, drone, clima, satélite e dados de aterramento.
- Normalização/concontexto de dados entre provedores de dados comuns.
- Esquematizados os recursos de acesso e consulta em todos os dados ingeridos.
- Geração automática de metadados que podem ser consultados, com base nos recursos do agronomic.  
- Agregações de sequência de tempo geradas automaticamente para criação rápida de modelos.
- Mecanismo integrado de Azure Data Factory (ADF) para criar facilmente pipelines de processamento de dados personalizados.

## <a name="application-development"></a>Programação de aplicações

As APIs contêm a documentação técnica do Swagger. Consulte [Swagger](https://aka.ms/FarmBeatsDatahubSwagger) para obter informações sobre todas as APIs e suas solicitações/respostas correspondentes.

Este é um resumo de todos os objetos/recursos no Hub de dados FarmBeats:

Farm | O farm corresponde a um local físico de interesse no sistema FarmBeats. Cada Farm tem um nome de farm e uma ID de farm exclusiva.
--- | ---|
Dispositivo  | O dispositivo corresponde a um dispositivo físico presente no farm. Cada dispositivo tem uma ID de dispositivo exclusiva. O dispositivo normalmente é provisionado em um farm com uma ID de farm.
deviceModel  | DeviceModel corresponde aos metadados do dispositivo, como o fabricante, o tipo do dispositivo ou o gateway ou o nó.
Sensores  | O sensor corresponde a um sensor físico que registra valores. Um sensor normalmente é conectado a um dispositivo com uma ID de dispositivo.
SensorModel  | SensorModel corresponde aos metadados do sensor, como o fabricante, tipo de sensor analógico ou digital, medida de sensor como temperatura ambiente, pressão etc.
Telemetria  | A telemetria fornece a capacidade de ler mensagens de telemetria para um determinado sensor e intervalo de tempo.
Tarefa  | O trabalho corresponde a qualquer Workflow de atividades, que são executadas no sistema FarmBeats para obter uma saída desejada. Cada trabalho é associado a uma ID de trabalho e tipo de trabalho.
JobType  | JobType corresponde a diferentes tipos de trabalho com suporte no sistema. Isso inclui o sistema definido & tipos de trabalho definidos pelo usuário.
ExtendedType  | O extendetype corresponde à lista de & tipos definidos pelo usuário no sistema. Isso ajuda a configurar um novo sensor ou cena ou tipo de cena no sistema FarmBeats.
Partner  | O parceiro corresponde ao parceiro de integração de sensor/imagens para FarmBeats
Cena  | A cena corresponde a qualquer saída gerada no contexto de um farm. Cada cena tem uma ID de cena, origem da cena, tipo de cena e ID de farm associada a ela. Cada ID de cena pode ter vários arquivos de cena associados a ela.
Scenefile |O scenefile corresponde a todos os arquivos, que são gerados para uma única cena. Uma única ID de cena pode ter várias IDs de Scenefile associadas a ela.
Regra  |A regra corresponde a uma condição para que os dados relacionados ao farm disparem um alerta. Cada regra estará no contexto dos dados de um farm.
Alerta  | O alerta corresponde a uma notificação, que é gerada quando uma condição de regra é atendida. Cada alerta estará no contexto de uma regra.
RoleDefinition  | RoleDefinition define ações permitidas e não autorizadas para uma função.
RoleAssignment  |RoleAssignment corresponde à atribuição de uma função a um usuário ou a uma entidade de serviço.

**Formato de dados**

O JSON (JavaScript Object Notation) é um formato de dados comum e independente de linguagem que fornece uma representação de texto simples de estruturas de dados arbitrárias. Para obter mais informações, consulte json.org.

## <a name="authentication-and-authorization"></a>Autenticação e autorização

As solicitações HTTP para a API REST são protegidas com Azure Active Directory (Azure AD).
Para fazer uma solicitação autenticada para as APIs REST, o código do cliente requer autenticação com credenciais válidas para que você possa chamar a API. A autenticação é coordenada entre os vários atores pelo Azure AD e fornece ao cliente um token de acesso como prova de autenticação. O token é então enviado no cabeçalho de autorização HTTP de solicitações da API REST. Para saber mais sobre a autenticação do Azure AD, consulte [Azure Active Directory](https://portal.azure.com) para desenvolvedores.

O token de acesso precisa ser enviado em solicitações de API subsequentes na seção de cabeçalho como:

```
headers = {"Authorization": "Bearer " + **access_token**}
```

**Cabeçalhos de solicitação HTTP**

Aqui estão os cabeçalhos de solicitação mais comuns que precisam ser especificados ao fazer uma chamada à API para o Hub de dados FarmBeats do Azure:


**Cabeçalho** | **Descrição e exemplo**
--- | ---
Tipo de conteúdo  | O formato de solicitação (tipo de conteúdo: aplicativo/<format>) para o formato de APIs do hub de dados FarmBeats do Azure é JSON. Tipo de conteúdo: aplicativo/JSON
Autorização  | Especifica o token de acesso necessário para fazer uma chamada à API. **Autorização: portador de acesso < token >**
aceitar | O formato de resposta. Para APIs do hub de dados FarmBeats do Azure, o formato é JSON **Accept: aplicativo/JSON**

**Solicitações de API**

Para fazer uma solicitação de API REST, você combina o método HTTP (GET, POST, PUT ou DELETE), a URL para o serviço de API, o URI para um recurso para consultar, enviar dados para, atualizar ou excluir e um ou mais cabeçalhos de solicitação HTTP.

A URL para o serviço de API é a URL do hub de dados https://\<yourdatahub-site-Name >. azurewebsites. net opcionalmente, você pode incluir parâmetros de consulta em GET calls para filtrar, limitar o tamanho e classificar os dados nas respostas.

A solicitação de exemplo abaixo é obter a lista de dispositivos:

```azurepowershell-interactive
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

A maioria das chamadas GET, POST e PUT exigem um corpo de solicitação JSON.

A solicitação de exemplo abaixo é criar um dispositivo (isso tem um JSON de entrada com o corpo da solicitação).

```json
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

**Parâmetros de consulta**

Para chamadas **Get** de REST, você pode filtrar, limitar o tamanho e classificar os dados em uma resposta de API, incluindo um ou mais parâmetros de consulta no URI de solicitação. Para os parâmetros de consulta, consulte a documentação da API e veja as chamadas GET individuais.
Por exemplo, ao consultar a lista de dispositivos (obter chamada em/Device), os seguintes parâmetros de consulta podem ser especificados:  

![Batidas no farm de projetos](./media/for-references/query-parameters-device-1.png)

**Processamento de erros**

A API do hub de dados do Azure FarmBeats retorna os erros HTTP padrão. Os códigos de erro mais comuns são os seguintes:

 |Código de erro             | Descrição |
 |---                    | --- |
 |200                    | Êxito |
 |201                    | Criar (post) êxito |
 |400                    | Solicitação inadequada. Há um erro na solicitação |
 |401                    | Não autorizado. O chamador da API não está autorizado a acessar o recurso |
 |404                    | Recurso não encontrado |
 |5XX                    | Erro interno do servidor. Os códigos de erro que começam com 5XX significam que há algum erro no servidor. Consulte os logs do servidor e a seção a seguir para obter mais detalhes. |


Além dos erros HTTP padrão, as APIs do hub de dados FarmBeats do Azure também retornam erros internos no formato abaixo:

    ```
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
    ```

Exemplo: ao criar um farm, um campo obrigatório "Name" não foi especificado na carga de entrada. A mensagem de erro resultante seria:

    ```json
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
    ```

## <a name="adding-users-or-app-registrations-to-azure-active-directory"></a>Adicionando usuários ou registros de aplicativo a Azure Active Directory

As APIs FarmBeats do Azure podem ser acessadas por um usuário ou um registro de aplicativo no Azure Active Directory. Para criar um registro de aplicativo em seu Azure Active Directory, siga as etapas abaixo:  

1. Vá para [portal do Azure](https://portal.azure.com) **Azure Active Directory, registros de aplicativo**> **novo registro**. Como alternativa, você pode usar uma conta existente.
2. Para uma nova conta, certifique-se de concluir o seguinte:

    - Introduzir um nome
    - Selecionar **contas neste diretório organizacional somente (locatário único)**
    - Os valores padrão no restante dos campos
    - Selecionar **registro**

3. No registro de aplicativo novo/existente, **visão geral**, conclua o seguinte

    - Capture a **ID do cliente** e a **ID do locatário**.
    - Vá para **certificados e segredos** para gerar um novo segredo do cliente e capturar o **segredo do cliente**.
    - Volte para visão geral e clique no link ao lado de **gerenciar aplicativo no diretório local**
    - Ir para **Propriedades** para capturar a **ID do objeto**

4. Vá para o [Swagger do hub de dados](https://<yourdatahub>.azurewebsites.net/swagger/index.html) e execute as seguintes etapas:
    - Navegue até a **API do RoleAssignment**
    - Execute uma **postagem** para criar um RoleAssignment para a **ID de objeto** que você acabou de criar. – O JSON de entrada é:

  > [!NOTE]
  > Para obter mais informações sobre como adicionar usuários e registro do AD, consulte [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) .

Depois de concluir as etapas acima, seu registro de aplicativo (cliente) pode chamar as APIs FarmBeats do Azure usando um token de acesso por meio da autenticação de portador.  

Use o token de acesso para enviá-lo em solicitações de API subsequentes na seção de cabeçalho como:

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
