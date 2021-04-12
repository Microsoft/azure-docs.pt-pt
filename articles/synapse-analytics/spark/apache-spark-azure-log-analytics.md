---
title: Utilize a Azure Log Analytics para recolher e visualizar métricas e registos (Pré-visualização)
description: Saiba como ativar o conector Azure Log Analytics incorporado na Synapse para recolher e enviar as métricas e registos da aplicação Apache Spark para o seu espaço de trabalho Azure Log Analytics.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 03/25/2021
ms.custom: references_regions
ms.openlocfilehash: 243618192593d93bba9d5229e7becfb2af62ce32
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108566"
---
# <a name="tutorial-use-azure-log-analytics-to-collect-and-visualize-metrics-and-logs-preview"></a>Tutorial: Use Azure Log Analytics para recolher e visualizar métricas e registos (Pré-visualização)

Neste tutorial, você aprenderá como ativar o conector Azure Log Analytics incorporado para recolher e enviar as métricas e registos da aplicação Apache Spark para o seu [espaço de trabalho Azure Log Analytics](/azure/azure-monitor/logs/quick-create-workspace). Em seguida, pode aproveitar um livro de monitorização Azure para visualizar as métricas e registos.

## <a name="configure-azure-log-analytics-workspace-information-in-synapse-studio"></a>Configure Azure Log Analytics Informações de workspace no Synapse Studio

### <a name="step-1-create-an-azure-log-analytics-workspace"></a>Passo 1: Criar um espaço de trabalho Azure Log Analytics

Pode seguir os documentos abaixo para criar um espaço de trabalho Log Analytics:
- [Criar uma área de trabalho do Log Analytics no portal do Azure](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace)
- [Criar um espaço de trabalho Log Analytics com Azure CLI](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace-cli)
- [Criar e configurar um espaço de trabalho Log Analytics no Monitor Azure utilizando o PowerShell](https://docs.microsoft.com/azure/azure-monitor/logs/powershell-workspace-configuration)

### <a name="step-2-prepare-a-spark-configuration-file"></a>Passo 2: Preparar um ficheiro de configuração spark

#### <a name="option-1-configure-with-azure-log-analytics-workspace-id-and-key"></a>Opção 1. Configure com ID e Chave do Espaço de Trabalho Azure Log Analytics 

Copie a seguinte configuração de Faísca, guarde-a como **"spark_loganalytics_conf.txt"** e preencha os parâmetros:

   - `<LOG_ANALYTICS_WORKSPACE_ID>`: ID do espaço de trabalho Azure Log Analytics.
   - `<LOG_ANALYTICS_WORKSPACE_KEY>`: Chave Azure Log Analytics: **Azure portal > espaço de trabalho Azure Log Analytics > a gestão de agentes > chave primária**

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.secret <LOG_ANALYTICS_WORKSPACE_KEY>
```

#### <a name="option-2-configure-with-an-azure-key-vault"></a>Opção 2. Configutura com um cofre de chaves Azure

> [!NOTE]
>
> Tem de conceder permissão secreta aos utilizadores que submeterão as aplicações Spark. Consulte [o acesso às chaves, certificados e segredos do Key Vault com um controlo de acesso baseado em funções Azure](https://docs.microsoft.com/azure/key-vault/general/rbac-guide)

Para configurar um Cofre de Chaves Azure para armazenar a chave do espaço de trabalho, siga os passos:

1. Crie e navegue até ao cofre chave no portal Azure
2. Nas páginas de definições do Cofre de Chaves, selecione **Secrets**.
3. Clique em **Gerar/Importar**.
4. No ecrã **Criar um segredo**, selecione os seguintes valores:
   - **Nome**: Digite um nome para o segredo, escreva `"SparkLogAnalyticsSecret"` como padrão.
   - **Valor:** Escreva o **<LOG_ANALYTICS_WORKSPACE_KEY>** para o segredo.
   - Deixe as outras opções com os valores predefinidos. Clique em **Criar**.
5. Copie a seguinte configuração de Faísca, guarde-a como **"spark_loganalytics_conf.txt"** e preencha os parâmetros:

   - `<LOG_ANALYTICS_WORKSPACE_ID>`: ID do espaço de trabalho Azure Log Analytics.
   - `<AZURE_KEY_VAULT_NAME>`: O nome Azure Key Vault que configuraste.
   - `<AZURE_KEY_VAULT_SECRET_KEY_NAME>` (Opcional): O nome secreto no Cofre da Chave Azure para a chave do espaço de trabalho, predefinido: "SparkLogAnalyticsSecret".

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
```

> [!NOTE]
>
> Também pode armazenar o id do espaço de trabalho Log Analytics para o cofre da Chave Azure. Consulte os passos acima e guarde o id do espaço de trabalho com nome `"SparkLogAnalyticsWorkspaceId"` secreto. Ou use o config `spark.synapse.logAnalytics.keyVault.key.workspaceId` para especificar o nome secreto do espaço de trabalho no cofre da Chave Azure.

#### <a name="option-3-configure-with-an-azure-key-vault-linked-service"></a>Opção 3. Configure com um serviço ligado a Azure Key Vault

> [!NOTE]
>
> Tens de conceder permissão secreta ao espaço de trabalho da Sinapse. Consulte [o acesso às chaves, certificados e segredos do Key Vault com um controlo de acesso baseado em funções Azure](https://docs.microsoft.com/azure/key-vault/general/rbac-guide)

Para configurar um serviço ligado ao Azure Key Vault no Synapse Studio para armazenar a chave do espaço de trabalho, siga os passos:

1. Siga todos os passos da `Option 2. Configure with an Azure Key Vault` secção.
2. Crie um serviço ligado ao cofre Azure Key no Synapse Studio:

    a. Navegue para **os serviços synapse Studio > Manage > Linked**, clique em **Novo** botão.

    b. Procure **a Azure Key Vault** na caixa de pesquisa.

    c. Digite um nome para o serviço ligado.

    d. Escolha o cofre da chave Azure. Clique em **Criar**.

3. Adicione um `spark.synapse.logAnalytics.keyVault.linkedServiceName` item à configuração Spark.

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
spark.synapse.logAnalytics.keyVault.linkedServiceName <LINKED_SERVICE_NAME>
```

#### <a name="available-spark-configuration"></a>Configuração de faísca disponível

| Nome de configuração                                  | Valor Predefinido                | Description                                                                                                                                                                                                |
| --------------------------------------------------- | ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| spark.synapse.logAnalytics.enabled                  | false                        | Para ativar o aussío Azure Log Analytics para as aplicações Spark, é verdade. Caso contrário, falso.                                                                                                                  |
| spark.synapse.logAnalytics.workspaceId              | -                            | O destino Azure Log Analytics workspace ID                                                                                                                                                          |
| spark.synapse.logAnalytics.secret                   | -                            | O destino Azure Log Analytics segredo do espaço de trabalho.                                                                                                                                                      |
| spark.synapse.logAnalytics.keyVault.linkedServiceName   | -                            | Azure Key cofre ligado nome de serviço para o ID do espaço de trabalho Azure Log Analytics e chave                                                                                                                       |
| spark.synapse.logAnalytics.keyVault.name            | -                            | Nome do cofre da chave Azure para o ID e chave Azure Log Analytics                                                                                                                                                |
| spark.synapse.logAnalytics.keyVault.key.workspaceId | SparkLogAnalyticsWorkspaceId | Nome secreto do cofre Azure Key para o ID do espaço de trabalho Azure Log Analytics                                                                                                                                       |
| spark.synapse.logAnalytics.keyVault.key.secret      | SparkLogAnalyticsSecret      | Nome secreto do cofre Azure Key para a chave do espaço de trabalho Azure Log Analytics                                                                                                                                      |
| spark.synapse.logAnalytics.keyVault.uriS suléix       | ods.opinsights.azure.com     | O destino Azure Log Analytics [workspace URI sufixo][uri_suffix]. Se o seu Espaço de Trabalho Azure Log Analytics não estiver no Azure global, tem de atualizar o sufixo URI de acordo com a respetiva nuvem. |

> [!NOTE]  
> - Para as nuvens de Azure China, o parâmetro "spark.synapse.logAnalytics.keyVault.uriSuffix" deve ser "ods.opinsights.azure.cn". 
> - Para as nuvens de Azure Gov, o parâmetro "spark.synapse.logAnalytics.keyVault.uriSuffix" deve ser "ods.opinsights.azure.us". 

[uri_suffix]: https://docs.microsoft.com/azure/azure-monitor/logs/data-collector-api#request-uri


### <a name="step-3-upload-your-spark-configuration-to-a-spark-pool"></a>Passo 3: Faça o upload da sua configuração Spark para uma piscina spark
Pode fazer o upload do ficheiro de configuração para a sua piscina Synapse Spark no Synapse Studio.

   1. Navegue até à sua piscina Apache Spark no Azure Synapse Studio (Gerencie -> piscinas Apache Spark)
   2. Clique no botão **"..."** à direita da sua piscina Apache Spark
   3. Selecione configuração de Faísca Apache 
   4. Clique **em Upload** e escolha o **"spark_loganalytics_conf.txt"** criado.
   5. Clique **em Upload** e **Aplique**.

      > [!div class="mx-imgBorder"]
      > ![configuração de piscina de faísca](./media/apache-spark-azure-log-analytics/spark-pool-configuration.png)

> [!NOTE] 
>
> Toda a aplicação Spark submetida à piscina Spark acima utilizará a definição de configuração para empurrar as métricas e registos da aplicação Spark para o seu espaço de trabalho específico Azure Log Analytics.

## <a name="submit-a-spark-application-and-view-the-logs-and-metrics-in-azure-log-analytics"></a>Envie uma aplicação Spark e veja os registos e métricas no Azure Log Analytics

 1. Pode submeter uma aplicação Spark à piscina Spark configurada no passo anterior, utilizando uma das seguintes formas:
    - Executar um caderno do Estúdio Synapse. 
    - Submeta um trabalho de lote Synapse Apache Spark através da definição de trabalho spark.
    - Executar um pipeline que contenha atividade spark.

 2. Vá ao espaço de trabalho Azure Log Analytics especificado e, em seguida, veja as métricas e registos de aplicação quando a aplicação Spark começar a ser executada.

## <a name="use-the-sample-azure-log-analytics-workbook-to-visualize-the-metrics-and-logs"></a>Utilize o livro de trabalho sample Azure Log Analytics para visualizar as métricas e registos

1. [Descarregue o livro aqui.](https://aka.ms/SynapseSparkLogAnalyticsWorkbook)
2. Abra e **copie** o conteúdo do ficheiro do livro.
3. Navegue para o livro de trabalho Azure Log Analytics[(portal Azure](https://portal.azure.com/) > Log Analytics espaço de trabalho > Livros)
4. Abra o livro de trabalho **"Empty"** Azure Log Analytics, no modo **"Editor Avançado"** (prima o ícone </>).
5. **Cole** sobre qualquer json que exista.
6. Em seguida, **pressione Aplicar** e depois **Fazer a Edição**.

    > [!div class="mx-imgBorder"]
    > ![novo livro](./media/apache-spark-azure-log-analytics/new-workbook.png)

    > [!div class="mx-imgBorder"]
    > ![livro de importação](./media/apache-spark-azure-log-analytics/import-workbook.png)

Em seguida, submeta o seu pedido Apache Spark para a piscina de Faíscas configurada. Depois de a aplicação ir para o estado de funcionamento, escolha a aplicação de execução na lista de abandono do livro.

> [!div class="mx-imgBorder"]
> ![imange livro](./media/apache-spark-azure-log-analytics/workbook.png)

E pode personalizar o livro de trabalho por alertas de consulta e configuração de Kusto.

> [!div class="mx-imgBorder"]
> ![consulta kusto e alertas](./media/apache-spark-azure-log-analytics/kusto-query-and-alerts.png)

## <a name="sample-kusto-queries"></a>Experimente consultas kusto

1. Exemplo de eventos de consulta Spark.

   ```kusto
   SparkListenerEvent_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | order by TimeGenerated desc
   | limit 100 
   ```

2. Consulta O condutor da aplicação Spark e os executores registam o exemplo.

   ```kusto
   SparkLoggingEvent_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | order by TimeGenerated desc
   | limit 100
   ```

3. Consulta Spark exemplo métricas.

   ```kusto
   SparkMetrics_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | where name_s endswith "jvm.total.used"
   | summarize max(value_d) by bin(TimeGenerated, 30s), executorId_s
   | order by TimeGenerated asc
   ```

## <a name="create-and-manage-alerts-using-azure-log-analytics"></a>Criar e gerir alertas usando a Azure Log Analytics

Os alertas do Azure Monitor permitem que os utilizadores utilizem uma consulta log Analytics para avaliar métricas e registos de cada frequência definida, e disparam um alerta com base nos resultados.

Para obter mais informações, consulte [Criar, ver e gerir alertas de registo utilizando o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/alerts/alerts-log).

## <a name="limitation"></a>Limitação

 - O espaço de trabalho Azure Synapse Analytics com [rede virtual gerida](https://docs.microsoft.com/azure/synapse-analytics/security/synapse-workspace-managed-vnet) ativado não é suportado.
 - As seguintes regiões não são atualmente apoiadas:
   - E.U.A. Leste 2
   - Leste da Noruega
   - Uae Norte

## <a name="next-steps"></a>Passos seguintes

 - Saiba como utilizar a [piscina Apache Spark sem servidor no Synapse Studio](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-apache-spark-pool-studio).
 - Saiba como [executar uma aplicação Spark no caderno.](https://docs.microsoft.com/azure/synapse-analytics/spark/apache-spark-development-using-notebooks)
 - Saiba como criar a definição de [emprego apache spark no Estúdio Synapse.](https://docs.microsoft.com/azure/synapse-analytics/spark/apache-spark-job-definitions)