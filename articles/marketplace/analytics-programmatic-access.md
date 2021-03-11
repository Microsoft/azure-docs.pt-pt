---
title: Paradigma de acesso programático
description: Compreenda o fluxo de alto nível do padrão de chamada da API para análise programática. As APIs para aceder a relatórios de análise no mercado comercial da Microsoft também estão abrangidas.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8e0b94a46e96dd8ba16040e16b421520eb67de19
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584087"
---
# <a name="programmatic-access-paradigm"></a>Paradigma de acesso programático

Este diagrama mostra o padrão de chamada da API usado para criar um novo modelo de relatório, agendar o relatório personalizado e recuperar dados de falha.

[ ![ Ilustra o padrão de chamada da API usado para criar um novo modelo de relatório, agendar o relatório personalizado e recuperar dados de falha.](./media/analytics-programmatic-access/analytics-high-level-flow.png)](./media/analytics-programmatic-access/analytics-high-level-flow.png#lightbox) 
 ***Figura 1: Fluxo de alto nível do padrão de chamada API***

Esta lista fornece mais detalhes sobre a Figura 1.

1. A Aplicação do Cliente pode definir o esquema/modelo de relatório personalizado, chamando a [API de Consulta de Relatório de Criação](#create-report-query-api). Alternadamente, pode utilizar um modelo de relatório `QueryId` () da [lista de consultas do sistema.](analytics-system-queries.md)
1. No sucesso, a API do Modelo de Relatório criar retorna o `QueryId` .
1. A aplicação do cliente chama então a [API do Relatório de Criação](#create-report-api) utilizando `QueryID` a data de início do relatório, Repeat Interval, Recurrence e um URI de retorno opcional.
1. Sobre o sucesso, a [API do Relatório create](#create-report-api) devolve o `ReportID` .
1. A aplicação do cliente é notificada na URI de retorno assim que os dados do relatório estiverem prontos para download.
1. A aplicação do cliente utiliza então a [API get report execuções](#get-report-executions-api) para consultar o estado do relatório com o `Report ID` intervalo e data.
1. Com o sucesso, o link de descarregamento do relatório é devolvido e a aplicação pode iniciar o download dos dados.

## <a name="report-query-language-specification"></a>Especificação da linguagem de consulta de relatório

Enquanto fornecemos [consultas de sistema](analytics-system-queries.md) que pode usar para criar relatórios, também pode criar as suas próprias consultas com base nas necessidades do seu negócio. Para saber mais sobre consultas personalizadas, consulte [a especificação de consulta personalizada.](analytics-custom-query-specification.md)

## <a name="create-report-query-api"></a>Criar consulta de relatório API

Esta API ajuda a criar consultas personalizadas que definem o conjunto de dados a partir do qual as colunas e métricas precisam de ser exportadas. A API fornece a flexibilidade para criar um novo modelo de reporte baseado nas necessidades do seu negócio.

Também pode utilizar as [consultas do sistema](analytics-system-queries.md) que fornecemos. Quando não forem necessários modelos de relatórios personalizados, pode ligar diretamente para a [API do Relatório de Criação](#create-report-api) utilizando as [Consultas](analytics-system-queries.md) das consultas do sistema que fornecemos.

O exemplo a seguir mostra como criar uma consulta personalizada para obter _o Uso Normalizado e Os Encargos Financeiros Estimados para SKUs pagos_ do conjunto de [dados ISVUsage](analytics-make-your-first-api-call.md#programmatic-api-call) para o mês passado.

*Solicitar sintaxe*

| Método | URI do pedido |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries` |
|||

*Cabeçalho de pedido*

| Cabeçalho | Tipo | Description |
| ------------- | ------------- | ------------- |
| Autorização | string | Obrigatório. O azure Ative Directory (Azure AD) acesso ao token. O formato é `Bearer <token>`. |
| Content-Type | `string` | `application/JSON` |
||||

*Parâmetro do caminho*

Nenhum

*Parâmetro de consulta*

Nenhum

*Solicite o exemplo da carga útil*

```json
{
    "Name": "ISVUsageQuery",
    "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
    "Query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"
}
```

*Glossário*

Esta tabela fornece as definições-chave dos elementos na carga útil do pedido.

| Parâmetro | Obrigatório | Descrição | Valores permitidos |
| ------------ | ------------- | ------------- | ------------- |
| `Name` | Yes | Nome amigável da consulta | cadeia (de carateres) |
| `Description` | No | Descrição do que a consulta retorna | string |
| `Query` | Yes | Cadeia de consulta de relatório | Tipo de dados: cadeia<br>[Consulta personalizada](analytics-sample-queries.md) baseada na necessidade de negócio |
|||||

> [!NOTE]
> Para amostras de consulta personalizada, consulte [exemplos de consultas de amostras](analytics-sample-queries.md).

*Resposta à amostra*

A carga útil de resposta é estruturada da seguinte forma:

Códigos de resposta: 200, 400, 401, 403,500

Exemplo de carga útil de resposta:

```json
{
  "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": " ISVUsageQuery",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs”,
            "query": " SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34Z"
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

*Glossário*

Esta tabela fornece as definições-chave dos elementos na resposta.

| Parâmetro | Descrição |
| ------------ | ------------- |
| `QueryId` | Identificador universalmente único (UUID) da consulta que criou |
| `Name` | Nome amigável dado à consulta na carga útil do pedido |
| `Description` | Descrição dada durante a criação da consulta |
| `Query` | Consulta de relatório passada como entrada durante a criação de consulta |
| `Type` | Definir para `userDefined` |
| `User` | ID do utilizador usado para criar a consulta |
| `CreatedTime` | TEMPO UTC A consulta foi criada neste formato: yyyy-MM-ddTHH:mm:ssZ |
| `TotalCount` | Número de conjuntos de dados na matriz de valor |
| `StatusCode` | Código do Resultado<br>Os valores possíveis são 200, 400, 401, 403, 500 |
| `message` | Mensagem de estado da execução da API |
|||

## <a name="create-report-api"></a>Criar relatório API

Ao criar um modelo de relatório personalizado com sucesso e receber o `QueryID` como parte da resposta Create Report [Query,](#create-report-query-api) esta API pode ser chamada para agendar uma consulta a ser executada a intervalos regulares. Pode definir uma frequência e um horário para que o relatório seja entregue. Para consultas de sistema que fornecemos, a API do Relatório de Criação também pode ser chamada com [Consulta.](analytics-sample-queries.md)

*Solicitar sintaxe*

| Método | URI do pedido |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport` |
|||

*Cabeçalho de pedido*

| Cabeçalho | Tipo | Description |
| ------ | ---- | ----------- |
| Autorização | string | Obrigatório. O azure Ative Directory (Azure AD) acesso ao token. O formato é `Bearer <token>`. |
| Tipo de Conteúdo | string | `application/JSON` |
||||

*Parâmetro do caminho*

Nenhum

*Parâmetro de consulta*

Nenhum

*Solicite o exemplo da carga útil*

```json
{
  "ReportName": "ISVUsageReport",
  "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
  "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c ",
  "StartTime": "2021-01-06T19:00:00Z ",
  "RecurrenceInterval": 48,
  "RecurrenceCount": 20,
  "Format": "csv",
  "CallbackUrl": "https://<SampleCallbackUrl>"
}
```

*Glossário*

Esta tabela fornece as definições-chave dos elementos na carga útil do pedido.

| Parâmetro | Obrigatório | Descrição | Valores permitidos |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | Yes | Nome atribuído ao relatório | cadeia (de carateres) |
| `Description` | No | Descrição do relatório criado | string |
| `QueryId` | Yes | ID de consulta de relatório | string |
| `StartTime` | Yes | Utc Timestamp em que a geração do relatório começará.<br>O formato deve ser: yyy-MM-ddTHH:mm:ssZ | string |
| `RecurrenceInterval` | Yes | Frequência em horas em que o relatório deve ser gerado.<br>O valor mínimo é 4 e o valor máximo é de 90. | número inteiro |
| `RecurrenceCount` | No | Número de relatórios a serem gerados. | número inteiro |
| `Format` | No | Formato de ficheiro do ficheiro exportado.<br>O formato predefinido é . CSV. | CSV/TSV |
| `CallbackUrl` | No | URL acessível publicamente que pode ser configurado opcionalmente como o destino de retorno. | String (http URL) |
| `ExecuteNow` | No | Este parâmetro deve ser usado para criar um relatório que será executado apenas uma vez. `StartTime`, `RecurrenceInterval` e `RecurrenceCount` são ignorados se isto estiver definido para `true` . O relatório é executado imediatamente de forma assíncronea. | verdadeiro/falso |
| `QueryStartTime` | No | Opcionalmente especifica a hora de início para a consulta extração dos dados. Este parâmetro é aplicável apenas por um relatório de execução de tempo que `ExecuteNow` tenha definido para `true` . O formato deve ser yyy-MM-ddTHH:mm:ssZ | Tempotam como corda |
| `QueryEndTime` | No | Opcionalmente especifica o tempo final para a consulta extração dos dados. Este parâmetro é aplicável apenas por um relatório de execução de tempo que `ExecuteNow` tenha definido para `true` . O formato deve ser yyy-MM-ddTHH:mm:ssZ | Tempotam como corda |
|||||

*Resposta de amostra*

A carga útil de resposta é estruturada da seguinte forma:

Código de resposta: 200, 400, 401, 403, 404,500

Carga útil de resposta:

```json
{
  "Value": [
    {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVUsageReport",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": "https://<SampleCallbackUrl>",
            "format": "csv"    
    }
  ],
  "TotalCount": 1,
  "Message": "Report created successfully",
  "StatusCode": 200
}
```

*Glossário*

Esta tabela fornece as definições-chave dos elementos na resposta.

| Parâmetro | Descrição |
| ------------ | ------------- |
| `ReportId` | Identificador universalmente único (UUID) do relatório que criou |
| `ReportName` | Nome dado ao relatório na carga útil do pedido |
| `Description` | Descrição dada durante a criação do relatório |
| `QueryId` | Consulta ID passou no momento em que criou o relatório |
| `Query` | Texto de consulta que será executado para este relatório |
| `User` | ID do utilizador usado para criar o relatório |
| `CreatedTime` | TEMPO UTC O relatório foi criado neste formato: yyyy-MM-ddTHH:mm:ssZ |
| `ModifiedTime` | UTC Tempo o relatório foi modificado pela última vez neste formato: yyyy-MM-ddTHH:mm:ssZ |
| `StartTime` | UTC Tempo a execução do relatório começará neste formato: yyyy-MM-ddTHH:mm:ssZ |
| `ReportStatus` | Estado da execução do relatório. Os valores possíveis são **Pausados,** **Ativos** e **Inativos.** |
| `RecurrenceInterval` | Intervalo de recorrência fornecido durante a criação do relatório |
| `RecurrenceCount` | Contagem de recorrência fornecida durante a criação do relatório |
| `CallbackUrl` | URL de retorno fornecido no pedido |
| `Format` | Formato dos ficheiros do relatório. Os valores possíveis são CSV ou TSV. |
| `TotalCount` | Número de conjuntos de dados na matriz de valor |
| `StatusCode` | Código do Resultado<br>Os valores possíveis são 200, 400, 401, 403, 500 |
| `message` | Mensagem de estado da execução da API |
|||

## <a name="get-report-executions-api"></a>Obtenha relatório execuções API

Pode utilizar este método para consultar o estado de execução de um relatório utilizando o `ReportId` recebido da API do [Relatório de Criação](#create-report-api). O método devolve o link de descarregamento do relatório se o relatório estiver pronto para download. Caso contrário, o método devolve o estado. Você também pode usar esta API para obter todas as execuções que aconteceram para um dado relatório.

> [!IMPORTANT]
> Esta API tem parâmetros de consulta predefinidos definidos para `executionStatus=Completed` e  `getLatestExecution=true` . Por isso, chamar a API antes da primeira execução bem sucedida do relatório devolverá 404. Execuções pendentes podem ser obtidas por definição `executionStatus=Pending` .

*Solicitar sintaxe*

| Método | URI do pedido |
| ------------ | ------------- |
| Get | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/{reportId}?executionId={executionId}&executionStatus={executionStatus}&getLatestExecution={getLatestExecution}` |
|||

*Cabeçalho de pedido*

| Cabeçalho | Tipo | Description |
| ------ | ------ | ------ |
| Autorização | string | Obrigatório. O azure Ative Directory (Azure AD) acesso ao token. O formato é `Bearer <token>`. |
| Tipo do conteúdo | string | `application/json` |
||||

*Parâmetro do caminho*

Nenhum

*Parâmetro de consulta*

| Nome do parâmetro | Necessário | Tipo | Descrição |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Sim | string | Filtrar para obter detalhes de execução de apenas relatórios `reportId` com dado neste argumento. O `reportIds` múltiplo pode ser especificado separando-os com um ponto e vírgula ";". |
| `executionId` | No | string | Filtre para obter detalhes de apenas relatórios `executionId` com dado neste argumento. O `executionIds` múltiplo pode ser especificado separando-os com um ponto e vírgula ";". |
| `executionStatus` | No | string/enum | Filtre para obter detalhes de apenas relatórios `executionStatus` com dado neste argumento.<br>Valores válidos são: `Pending` `Running` , e `Paused``Completed` <br>O valor predefinido é `Completed`. Vários estados podem ser especificados separando-os com um ponto e vírgula ";". |
| `getLatestExecution` | No | boolean | A API devolverá detalhes da última execução do relatório.<br>Por predefinição, este parâmetro é definido para `true` . Se optar por passar o valor deste parâmetro como `false` , então a API devolverá os últimos 90 dias de execução. |
|||||

*Solicitar carga útil*

Nenhum

*Resposta de amostra*

A carga útil de resposta é estruturada da seguinte forma:

Códigos de resposta: 200, 400, 401, 403, 404.500

Exemplo de carga útil de resposta:

```json
{
    "value": [
        {
            "executionId": "a0bd78ad-1a05-40fa-8847-8968b718d00f",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 4,
            "recurrenceCount": 10,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Completed",
            "reportAccessSecureLink": "https://<path to report for download>",
            "reportExpiryTime": null,
            "reportGeneratedTime": "2021-01-13T14:40:46Z"
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

Uma vez concluída a execução do relatório, o estado de execução `Completed` é mostrado. Pode descarregar o relatório selecionando o URL no `reportAccessSecureLink` .

*Glossário*

Definições-chave de elementos na resposta.

| Parâmetro | Descrição |
| ------------ | ------------- |
| `ExecutionId` | Identificador universalmente único (UUID) da instância de execução |
| `ReportId` | ID do relatório associado à instância de execução |
| `RecurrenceInterval` | Intervalo de recorrência fornecido durante a criação do relatório |
| `RecurrenceCount` | Contagem de recorrência fornecida durante a criação do relatório |
| `CallbackUrl` | URL de retorno associado à instância de execução |
| `Format` | Formato do ficheiro gerado no final da execução |
| `ExecutionStatus` | Estado da instância de execução do relatório.<br>Valores válidos são: `Pending` `Running` , e `Paused``Completed` |
| `ReportAccessSecureLink` | Link através do qual o relatório pode ser acedido de forma segura |
| `ReportExpiryTime` | Tempo UTC após o qual a ligação de relatório expirará neste formato: yyyy-MM-ddTHH:mm:ssZ |
| `ReportGeneratedTime` | Tempo UTC em que o relatório foi gerado neste formato: yyyy-MM-ddTHH:mm:ssZ |
| `TotalCount` | Número de conjuntos de dados na matriz de valor |
| `StatusCode` | Código do Resultado <br>Os valores possíveis são 200, 400, 401, 403, 404 e 500 |
| `message` | Mensagem de estado da execução da API |
|||

## <a name="next-steps"></a>Passos seguintes
- Você pode experimentar as APIs através do [URL da API swagger](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html)
- Começar com acesso programático a dados de análise
