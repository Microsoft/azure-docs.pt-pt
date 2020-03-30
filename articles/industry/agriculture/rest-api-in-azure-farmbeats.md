---
title: Referências
description: ''
author: sunasing
ms.topic: article
ms.date: 11/04/2019
ms.author: sunasing
ms.openlocfilehash: 318b3e3f774a4381434fd56154f4c0d95c28c7a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479520"
---
# <a name="references"></a>Referências

Este artigo descreve as APIs Azure FarmBeats.

## <a name="rest-api"></a>API REST

As APIs Azure FarmBeats fornecem às empresas agrícolas uma interface restful padronizada com respostas baseadas em JSON para ajudá-lo a tirar partido das capacidades azure FarmBeats, tais como:

- APIs para obter dados de sensores, câmara, drone, tempo, satélite e curadoria.
- Normalização e contextualização de dados entre fornecedores comuns de dados.
- Acesso schematizado e capacidades de consulta em todos os dados ingeridos.
- Geração automática de metadados que podem ser consultados, com base em características agronómicas.
- Agregações de sequência de tempo geradas automaticamente para a construção rápida de modelos.
- Motor Azure Data Factory integrado para construir facilmente gasodutos de processamento de dados personalizados.

## <a name="application-development"></a>Programação de aplicações

As APIs FarmBeats contêm documentação técnica swagger. Para obter informações sobre todas as APIs e os seus correspondentes pedidos ou respostas, consulte [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

A tabela seguinte resume todos os objetos e recursos no FarmBeats Datahub:

| Objetos e recursos | Descrição
--- | ---|
Quinta | A exploração corresponde a uma localização física de interesse dentro do sistema FarmBeats. Cada quinta tem um nome de fazenda e uma identificação única da fazenda. |
Dispositivo  | O dispositivo corresponde a um dispositivo físico presente na quinta. Cada dispositivo tem um ID de dispositivo único. Um dispositivo é normalmente provisionado para uma fazenda com uma identificação agrícola.
DeviceModel  | O DeviceModel corresponde aos metadados do dispositivo, como o fabricante e o tipo de dispositivo, que é gateway ou nó.
Sensor  | O sensor corresponde a um sensor físico que regista valores. Um sensor é normalmente ligado a um dispositivo com um ID do dispositivo.
Modelo de Sensores  | O SensorModel corresponde aos metadados do sensor, como o fabricante, o tipo de sensor, que é analógico ou digital, e a medição do sensor, como temperatura ambiente e pressão.
Telemetria  | A telemetria fornece a capacidade de ler mensagens de telemetria para um determinado sensor e intervalo de tempo.
Tarefa  | O trabalho corresponde a qualquer fluxo de trabalho de atividades que são executadas no sistema FarmBeats para obter uma saída desejada. Cada trabalho está associado a uma identificação de trabalho e tipo de trabalho.
Tipo de Trabalho  | JobType corresponde a diferentes tipos de trabalho suportados pelo sistema. Estão incluídos os tipos de trabalho definidos pelo sistema e definidos pelo utilizador.
Tipo alargado  | ExtendedType corresponde à lista de tipos definidos pelo sistema e pelo utilizador no sistema. ExtendedType ajuda a configurar um novo sensor, cena ou tipo de ficheiro de cena no sistema FarmBeats.
Partner  | O parceiro corresponde ao parceiro de integração de sensores e imagens para farmBeats.
Cena  | A cena corresponde a qualquer produção gerada no contexto de uma quinta. Cada cena tem uma identificação de cena, fonte de cena, tipo de cena e identificação da fazenda associada a ele. Cada identificação da cena pode ter vários ficheiros de cena associados.
CenaFile |O SceneFile corresponde a todos os ficheiros gerados para uma única cena. Um único ID de cena pode ter vários IDs do SceneFile associados a ele.
Regra  |A regra corresponde a uma condição para que os dados relacionados com a exploração desencadeiem um alerta. Cada regra está no contexto dos dados de uma fazenda.
Alerta  | O alerta corresponde a uma notificação, que é gerada quando uma condição de regra é satisfeita. Cada alerta está no contexto de uma regra.
Definição de papéis  | RoleDefinition define ações permitidas e desautorizadas para um papel.
Atribuição de Funções  |A RoleAssignment corresponde à atribuição de uma função a um utilizador ou a um diretor de serviço.

### <a name="data-format"></a>Formato de dados

JSON é um formato de dados independente da linguagem comum que fornece uma representação simples de texto de estruturas de dados arbitrárias. Para mais informações, consulte o site da [JSON.](https://www.json.org/)

## <a name="authentication-and-authorization"></a>Autenticação e autorização

Os pedidos http para a REST API estão protegidos com o Azure Ative Directory (Azure AD).
Para fazer um pedido autenticado às APIs rest, o código do cliente requer autenticação com credenciais válidas antes de poder ligar para a API. A autenticação é coordenada entre os vários atores pela Azure AD. Fornece ao seu cliente um sinal de acesso como prova da autenticação. O símbolo é então enviado no cabeçalho de autorização http dos pedidos da API REST. Para saber mais sobre a autenticação da Azure AD, consulte [o Azure Ative Directory](https://portal.azure.com) para programadores.

O sinal de acesso deve ser enviado em pedidos subsequentes de API, na secção cabeçalho, como:

```
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>HTTP solicitar cabeçalhos

Aqui estão os cabeçalhos de pedido mais comuns que deve especificar quando faz uma chamada aPi para O Datahub Azure FarmBeats.


**Cabeçalho** | **Descrição e exemplo**
--- | ---
Content-Type  | O formato de pedido (Tipo de conteúdo: aplicação/<format>). Para a Azure FarmBeats Datahub APIs, o formato é JSON. Tipo de conteúdo: aplicação/json
Autorização  | Especifica o sinal de acesso necessário para fazer uma chamada aPi. Autorização:> de acesso ao <do portador
Aceitar | O formato de resposta. Para a Azure FarmBeats Datahub APIs, o formato é JSON. Aceitar: aplicação/json

### <a name="api-requests"></a>Pedidos da API

Para fazer um pedido de API REST, combina o método HTTP (GET, POST, PUT ou DELETE), o URL ao serviço API, o URI a um recurso para consultar, submeter dados, atualizar ou excluir, e, em seguida, adicionar um ou mais cabeçalhos de pedido HTTP.

O URL do serviço API é o seu\<URL Datahub, por exemplo, https:// o nome do site datahub>.azurewebsites.net.

Opcionalmente, pode incluir parâmetros de consulta nas chamadas GET para filtrar, limitar o tamanho e classificar os dados nas respostas.

O seguinte pedido de amostra é utilizado para obter a lista de dispositivos:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

A maioria das chamadas GET, POST e PUT requerem um corpo de pedido da JSON.

O seguinte pedido de amostra cria um dispositivo. Este pedido tem entrada JSON com o órgão de pedido.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>Parâmetros de consulta

Para chamadas REST GET, pode filtrar, limitar o tamanho e classificar os dados numa resposta DaPI, incluindo um ou mais parâmetros de consulta no pedido URI. Para os parâmetros de consulta, consulte a documentação da API e as chamadas get individuais.
Por exemplo, quando se consulta a lista de dispositivos (GET call on /Device), podem ser especificados os seguintes parâmetros de consulta:

![Lista de dispositivos](./media/references-for-azure-farmbeats/query-parameters-device-1.png)

### <a name="error-handling"></a>Processamento de erros

Azure FarmBeats Datahub APIs devolvem os erros padrão http. Os códigos de erro mais comuns são os seguintes:

 |Código de erro             | Descrição |
 |---                    | --- |
 |200                    | Êxito |
 |201                    | Criar (Post) Sucesso |
 |400                    | Mau pedido. Há um erro no pedido. |
 |401                    | Não autorizado. O autor da API não está autorizado a aceder ao recurso. |
 |404                    | Recurso não encontrado |
 |5XX                    | Erro do Servidor Interno. Os códigos de erro a partir do 5XX significam que há algum erro no servidor. Consulte os registos do servidor e a seguinte secção para obter mais detalhes. |


Além dos erros padrão do HTTP, as APIs do Datahub Azure FarmBeats também devolvem erros internos no seguinte formato:

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

Neste exemplo, quando uma exploração foi criada, o campo obrigatório "Nome" não foi especificado na carga útil do input. A mensagem de erro resultante seria:

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>Adicione registos de utilizadores ou aplicativos ao Azure Ative Directory

As APIs Azure FarmBeats podem ser acedidas por um utilizador ou por uma inscrição de aplicações no Diretório Ativo Azure. Para criar uma inscrição de aplicativo no Azure Ative Directory, siga estes passos:

1. Vá ao [portal Azure](https://portal.azure.com)e selecione Registos de Aplicações de **Diretório** > Ativo Azure Novas**inscrições.** > **New registration** Em alternativa, pode utilizar uma conta existente.
2. Para uma nova conta, faça o seguinte:

    - Insira um nome.
    - Selecione **Contas apenas neste diretório organizacional (Inquilino Único)**.
    - Utilize os valores predefinidos no resto dos campos.
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

Depois de terminar os passos anteriores, o registo da sua aplicação (cliente) pode ligar para as APIs Azure FarmBeats utilizando um sinal de acesso através da autenticação ao portador.

Utilize o sinal de acesso para enviá-lo em pedidos subsequentes de API na secção cabeçalho como:

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
