---
title: Registos de diagnóstico do Azure | Documentos da Microsoft
description: Cliente pode ativar a análise de registos para CDN do Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: magattus
ms.openlocfilehash: edb2bbee8df24dc9f9747ff13f5e3526192d91b2
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56874023"
---
# <a name="azure-diagnostic-logs"></a>Registos de diagnóstico do Azure

Com os registos de diagnóstico do Azure, pode ver a análise de núcleo e salvá-los num ou mais destinos, incluindo:

 - Conta de armazenamento do Azure
 - Azure Event Hubs
 - [Área de trabalho do Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Esta funcionalidade está disponível nos pontos finais CDN para todos os escalões de preço. 

Registos de diagnóstico do Azure permitem-lhe exportar métricas de utilização básica do ponto final da CDN para uma variedade de origens, para que pode usá-los em modo personalizado. Por exemplo, pode efetuar os seguintes tipos de exportação de dados:

- Exporte dados para armazenamento de BLOBs, exportar para CSV e gerar gráficos no Excel.
- Exportar dados para os Hubs de eventos e correlacionar com dados de outros serviços do Azure.
- Exportar dados para o Azure Monitor registos e ver os dados na sua própria área de trabalho do Log Analytics

O diagrama seguinte mostra uma exibição de análise de núcleo CDN típica de dados.

![Portal – registos de diagnóstico](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Figura 1 – o modo de exibição do CDN core analytics*

Para obter mais informações sobre os registos de diagnóstico, consulte [registos de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="enable-logging-with-the-azure-portal"></a>Ativar o registo com o portal do Azure

Siga estes ativar passos logs com análise de principal CDN:

Inicie sessão no [portal do Azure](http://portal.azure.com). Se não já tiver ativado o CDN para seu fluxo de trabalho, [criar um perfil de CDN do Azure e o ponto final](cdn-create-new-endpoint.md) antes de continuar.

1. No portal do Azure, navegue até **perfil da CDN**.

2. No portal do Azure, procure por um perfil da CDN ou selecionar um partir do seu dashboard. Em seguida, selecione o ponto final da CDN para a qual pretende ativar os registos de diagnóstico.

    ![Portal – registos de diagnóstico](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Selecione **registos de diagnóstico** na secção monitorização.

   O **registos de diagnóstico** é apresentada a página.

    ![Portal – registos de diagnóstico](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Ativar o registo com o armazenamento do Azure

Para utilizar uma conta de armazenamento para armazenar os registos, siga estes passos:
    
1. Para **nome**, introduza um nome para as definições de registo de diagnóstico.
 
2. Selecione **arquivo para uma conta de armazenamento**, em seguida, selecione **CoreAnalytics**. 

2. Para **retenção (dias)**, escolha o número de dias de retenção. A retenção de zero dias armazena os logs de indefinidamente. 

    ![Portal – registos de diagnóstico](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Selecione **conta de armazenamento**.

    O **Selecione uma conta de armazenamento** é apresentada a página.

4. Selecione uma conta de armazenamento na lista pendente, em seguida, selecione **OK**.

    ![Portal – registos de diagnóstico](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. Depois de terminar de realizar as suas definições de registo de diagnóstico, selecione **guardar**.

### <a name="logging-with-azure-monitor"></a>Iniciar sessão com o Azure Monitor

Para utilizar o Azure Monitor para armazenar os registos, siga estes passos:

1. Partir do **registos de diagnóstico** página, selecione **enviar para o Log Analytics**. 

    ![Portal – registos de diagnóstico](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Selecione **configurar** para configurar o registo do Azure Monitor. 

   O **áreas de trabalho do Log Analytics** é apresentada a página.

    >[!NOTE] 
    >As áreas de trabalho do OMS são agora referidas como áreas de trabalho do Log Analytics.

    ![Portal – registos de diagnóstico](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Selecione **criar nova área de trabalho**.

    O **área de trabalho do Log Analytics** é apresentada a página.

    >[!NOTE] 
    >As áreas de trabalho do OMS são agora referidas como áreas de trabalho do Log Analytics.

    ![Portal – registos de diagnóstico](./media/cdn-diagnostics-log/07_Create-new.png)

4. Para **área de trabalho do Log Analytics**, introduza um nome de área de trabalho do Log Analytics. O nome de área de trabalho do Log Analytics tem de ser exclusivos e conter apenas letras, números e hífenes; não são permitidos espaços e carateres de sublinhado. 

5. Para **subscrição**, selecione uma subscrição existente na lista pendente. 

6. Para **grupo de recursos**, crie um novo grupo de recursos ou selecione um existente.

7. Para **localização**, selecione uma localização da lista.

8. Selecione **afixar ao dashboard** se pretender guardar a configuração de registo ao seu dashboard. 

9. Selecione **OK** para concluir a configuração.

10. Depois de sua área de trabalho é criada, forem retornados para o **registos de diagnóstico** página. Certifique-se o nome da sua nova área de trabalho do Log Analytics.

    ![Portal – registos de diagnóstico](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Selecione **CoreAnalytics**, em seguida, selecione **guardar**.

12. Para ver a nova área de trabalho do Log Analytics, selecione **Core analytics** da sua página de ponto final CDN.

    ![Portal – registos de diagnóstico](./media/cdn-diagnostics-log/cdn-core-analytics-page.png) 

    A área de trabalho do Log Analytics está agora pronta para registos de dados. Para poder consumir esses dados, tem de utilizar um [registos do Azure Monitor solução](#consuming-diagnostics-logs-from-a-log-analytics-workspace), cobertas neste artigo.

Para obter mais informações sobre a atrasos de dados de registo, consulte [atrasos de dados de registo](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>Ativar registo com o PowerShell

O exemplo seguinte mostra como ativar os registos de diagnóstico através de Cmdlets do PowerShell do Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Ativar diagnóstico registos numa conta de armazenamento

1. Inicie sessão e selecione uma subscrição:

    Connect-AzAccount 

    Select-AzureSubscription -SubscriptionId 

2. Para ativar registos de diagnóstico numa conta de armazenamento, introduza este comando:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. Para ativar os registos de diagnóstico numa área de trabalho do Log Analytics, introduza este comando:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Consumir registos de diagnóstico do armazenamento do Azure
Esta secção descreve o esquema de análise de principal CDN, como ele é organizado dentro de uma conta de armazenamento do Azure e fornece o código de exemplo para transferir os registos num arquivo CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Utilizar o Explorador de armazenamento do Microsoft Azure
Antes de poder aceder os dados de análise de principais de uma conta de armazenamento do Azure, terá primeiro de uma ferramenta para acessar o conteúdo numa conta de armazenamento. Embora haja várias ferramentas disponíveis no mercado, que recomendamos é o Microsoft Azure Storage Explorer. Para transferir a ferramenta, consulte [Explorador de armazenamento do Azure](http://storageexplorer.com/). Depois de baixar e instalar o software, configure-o para utilizar a mesma conta de armazenamento do Azure que foi configurada como um destino para os registos de diagnóstico de CDN.

1.  Abra **Explorador de armazenamento do Microsoft Azure**
2.  Localize a conta de armazenamento
3.  Expanda a **contentores de BLOBs** esta conta de armazenamento no nó.
4.  Selecione o contentor com o nome *insights-logs-coreanalytics*.
5.  Resultados mostram a cópia de segurança no painel da direita, começando com o primeiro nível, como *resourceId =*. Continuar a selecionar cada nível até encontrar o ficheiro *PT1H.json*. Para obter uma explicação do caminho, consulte [formato de caminho do Blob](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  Cada blob *PT1H.json* arquivo representa os registos de análise de uma hora para um ponto de final CDN específico ou o seu domínio personalizado.
7.  O esquema do conteúdo deste ficheiro de JSON é descrito o esquema da seção de registos de análise de núcleo.


#### <a name="blob-path-format"></a>Formato de caminho de blob

Registos de análise de núcleo são gerados a cada hora e os dados são recolhidos e armazenados dentro de um único Azure blob como um payload JSON. Uma vez que a ferramenta do Explorador de armazenamento interpreta '/' como um separador de diretório e mostra a hierarquia, o caminho para o blob do Azure aparece como se existe uma estrutura hierárquica e representa o nome do blob. O nome do blob segue a Convenção de nomenclatura seguinte:   

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Descrição de campos:**

|Value|Descrição|
|-------|---------|
|ID da subscrição    |ID da subscrição do Azure no formato Guid.|
|Nome do Grupo de Recursos |Nome do grupo de recursos ao qual pertencem os recursos do CDN.|
|Profile Name (Nome do Perfil) |Nome do perfil da CDN|
|Nome do ponto final |Nome do ponto final da CDN|
|Ano|  Representação de quatro dígitos do ano, por exemplo, de 2017|
|Mês| Representação de dois dígitos do número do mês. 01 = Janeiro... 12 = Dezembro|
|Dia|   Representação de dois dígitos do dia do mês|
|PT1H.json| Onde estão armazenados os dados de análise real do ficheiro JSON|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Exportar os dados de análise básicos para um ficheiro CSV

Para que seja fácil de aceder ao core analytics, o código de exemplo para uma ferramenta é fornecido. Essa ferramenta permite transferir os ficheiros JSON para um formato de ficheiro simples separados por vírgulas, que pode ser utilizado para criar gráficos ou outras agregações.

Eis como pode usar a ferramenta:

1.  Visite a ligação do GitHub: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Baixe o código.
3.  Siga as instruções para compilar e configurar.
4.  Execute a ferramenta.
5.  O ficheiro CSV resultante mostra os dados de análise de uma hierarquia simples simple.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Consumir registos de diagnóstico a partir de uma área de trabalho do Log Analytics
Monitor do Azure é um serviço do Azure que monitoriza a sua cloud e ambientes para manter a disponibilidade e desempenho no local. Recolhe dados gerados por recursos nos seus ambientes na cloud e no local e de outras ferramentas de monitorização, para disponibilizar análises relativas a várias origens. 

Para utilizar o Azure Monitor, terá [ativar o registo](#enable-logging-with-azure-storage) para a área de trabalho do Log Analytics do Azure, que é abordado anteriormente neste artigo.

### <a name="using-the-log-analytics-workspace"></a>Utilizar a área de trabalho do Log Analytics

 O diagrama seguinte mostra a arquitetura das entradas e saídas do repositório:

![Área de trabalho do Log Analytics](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Figura 3 – repositório do Log Analytics*

Pode exibir os dados numa variedade de formas usando soluções de gestão. Pode obter soluções de gestão a partir da [do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Pode instalar soluções de gestão a partir do Azure marketplace, selecionando o **obter agora** link na parte inferior de cada solução.

### <a name="add-a-azure-monitor-cdn-management-solution"></a>Adicionar uma solução de gestão de CDN do Azure Monitor

Siga estes passos para adicionar uma solução de gestão do Azure Monitor:

1.   Inicie sessão no portal do Azure com a sua subscrição do Azure e aceda ao seu dashboard.
    ![Dashboard do Azure](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. Na **New** página, em **Marketplace**, selecione **monitorização + gestão**.

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. Na **monitorização + gestão** página, selecione **ver tudo**.

    ![Ver tudo](./media/cdn-diagnostics-log/15_See-all.png)

4. Pesquisa de CDN na caixa de pesquisa.

    ![Ver tudo](./media/cdn-diagnostics-log/16_Search-for.png)

5. Selecione **a CDN do Azure Core Analytics**. 

    ![Ver tudo](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. Depois de selecionar **criar**, é-lhe perguntado para criar uma nova área de trabalho do Log Analytics ou utilize um já existente. 

    ![Ver tudo](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Selecione a área de trabalho que criou anteriormente. Em seguida, precisa adicionar uma conta de automatização.

    ![Ver tudo](./media/cdn-diagnostics-log/19_Add-automation.png)

8. O ecrã seguinte mostra o formulário de conta de automatização que terá de preencher. 

    ![Ver tudo](./media/cdn-diagnostics-log/20_Automation.png)

9. Assim que tiver criado a conta de automatização, está pronto para adicionar a sua solução. Selecione o botão **Criar**.

    ![Ver tudo](./media/cdn-diagnostics-log/21_Ready.png)

10. Sua solução agora foi adicionada à sua área de trabalho. Regresse ao dashboard do portal do Azure.

    ![Ver tudo](./media/cdn-diagnostics-log/22_Dashboard.png)

    Selecione a área de trabalho do Log Analytics que criou para aceder à sua área de trabalho. 

11. Selecione o **Portal do OMS** mosaico para ver a nova solução.

    ![Ver tudo](./media/cdn-diagnostics-log/23_workspace.png)

12. Seu portal deverá agora ser semelhante ao seguinte ecrã:

    ![Ver tudo](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Selecione um dos mosaicos para ver os vários modos de exibição sobre os seus dados.

    ![Ver tudo](./media/cdn-diagnostics-log/25_Interior-view.png)

    Pode rolar esquerda ou direita para ver mais mosaicos que representam exibições individuais para os dados. 

    Selecione um dos mosaicos para ver mais detalhes sobre os seus dados.

     ![Ver tudo](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Os escalões de preços e ofertas

Pode ver, ofertas e os escalões de preços para soluções de gestão [aqui](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions).

### <a name="customizing-views"></a>Personalizar vistas

Pode personalizar a exibição sobre os seus dados ao utilizar o **estruturador de vistas**. Para começar a projetar, aceda à sua área de trabalho do Log Analytics e selecione o **estruturador de vistas** mosaico.

![Estruturador de Vista](./media/cdn-diagnostics-log/27_Designer.png)

Arrastar e largar os tipos de gráficos e preencha os dados de detalhes pretende analisar.

![Estruturador de Vista](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Atrasos de dados de registo

A tabela seguinte mostra o registo de atrasos de dados para **CDN Standard do Microsoft Azure**, **CDN do Azure Standard da Akamai**, e **Azure CDN Standard/Premium da Verizon**.

Atrasos de dados de registo de Microsoft | Atrasos de dados de registo da Verizon | Atrasos de dados de registo do Akamai
--- | --- | ---
Adiado por 1 hora. | Adiado por 1 hora e pode levar até 2 horas para começar a aparecer após a conclusão da propagação de ponto final. | Adiado 24 horas; Se ele foi criado há mais de 24 horas, demora até 2 horas para começar a aparecer. Se tiver sido recentemente criado, pode demorar até 25 horas para os registos para começar a aparecer.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Tipos de registo de diagnóstico para análise de principal CDN

A Microsoft oferece atualmente o core analytics registos apenas, que contêm as métricas que mostra estatísticas de resposta HTTP e estatísticas de saída como visto de CDN POPs/bordas.

### <a name="core-analytics-metrics-details"></a>Detalhes de métricas do Core analytics
A tabela seguinte mostra uma lista de métricas disponíveis no Centro de registos de análise para **CDN Standard do Microsoft Azure**, **CDN do Azure Standard da Akamai**, e **Azure CDN Standard/Premium da Verizon**. Nem todas as métricas estão disponíveis a partir todos os fornecedores, embora essas diferenças são mínimas. A tabela apresenta, também, se uma determinada métrica está disponível de um fornecedor. As métricas estão disponíveis apenas esses pontos finais da CDN que tenham o tráfego nas mesmas.


|Métrica                     | Descrição | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Número total de acertos de pedido durante este período. | Sim | Sim |Sim |
| RequestCountHttpStatus2xx | Contagem de todas as solicitações que resultaram num código HTTP 2xx (por exemplo, 200, 202). | Sim | Sim |Sim |
| RequestCountHttpStatus3xx | Contagem de todas as solicitações que resultaram num código HTTP 3xx (por exemplo, 300, 302). | Sim | Sim |Sim |
| RequestCountHttpStatus4xx | Contagem de todas as solicitações que resultaram num código HTTP 4xx (por exemplo, 400, 404). | Sim | Sim |Sim |
| RequestCountHttpStatus5xx | Contagem de todas as solicitações que resultaram num código HTTP 5xx (por exemplo, 500, 504). | Sim | Sim |Sim |
| RequestCountHttpStatusOthers | Contagem de todos os outros códigos HTTP (fora 2xx-5xx). | Sim | Sim |Sim |
| RequestCountHttpStatus200 | Contagem de todas as solicitações que resultaram numa resposta de código HTTP 200. | Sim | Não  |Sim |
| RequestCountHttpStatus206 | Contagem de todas as solicitações que resultaram numa resposta de código HTTP 206. | Sim | Não  |Sim |
| RequestCountHttpStatus302 | Contagem de todas as solicitações que resultaram numa resposta de código HTTP 302. | Sim | Não  |Sim |
| RequestCountHttpStatus304 | Contagem de todas as solicitações que resultaram numa resposta de código 304 do HTTP. | Sim | Não  |Sim |
| RequestCountHttpStatus404 | Contagem de todas as solicitações que resultaram numa resposta de código HTTP 404. | Sim | Não  |Sim |
| RequestCountCacheHit | Vou até a contagem de todas as solicitações que resultaram numa Cache. O recurso tiver sido servido diretamente a partir do POP ao cliente. | Sim | Sim | Não  |
| RequestCountCacheMiss | Contagem de todas as solicitações que resultaram numa falha de acerto na Cache. Uma falha de acerto na Cache significa que o recurso não foi encontrado no POP de mais próximo para o cliente e, portanto, foi obtido da origem. | Sim | Sim | Não |
| RequestCountCacheNoCache | Contagem de todos os pedidos para um recurso que são impedidos de serem colocados em cache devido a uma configuração de utilizador no edge. | Sim | Sim | Não |
| RequestCountCacheUncacheable | Contagem de todos os pedidos para os recursos que são impedidos de serem colocados em cache, Cache-Control e cabeçalhos de Expires, que indica que ele deve não ser armazenados em cache num POP ou pelo cliente HTTP do recurso. | Sim | Sim | Não |
| RequestCountCacheOthers | Contagem de todos os pedidos com o estado da cache não abrangido por acima. | Não | Sim | Não  |
| EgressTotal | Transferência de dados de saída em GB | Sim |Sim |Sim |
| EgressHttpStatus2xx | Dados de saída transferência * para respostas com 2xx códigos de estado HTTP em GB. | Sim | Sim | Não  |
| EgressHttpStatus3xx | Transferência de dados de saída para respostas com códigos de estado HTTP 3xx em GB. | Sim | Sim | Não  |
| EgressHttpStatus4xx | Transferência de dados de saída para respostas com códigos de estado HTTP 4xx em GB. | Sim | Sim | Não  |
| EgressHttpStatus5xx | Transferência de dados de saída para respostas com códigos de estado HTTP 5xx em GB. | Sim | Sim | Não |
| EgressHttpStatusOthers | Transferência de dados de saída para respostas com outros códigos de estado HTTP em GB. | Sim | Sim | Não  |
| EgressCacheHit | Saída de transferência de dados para as respostas que foram entregues diretamente a partir da cache CDN na CDN POPs/limites. | Sim | Sim | Não |
| EgressCacheMiss. | Transferência de dados de saída para as respostas que não foram encontradas no servidor POP mais próximo e obtidas a partir do servidor de origem. | Sim | Sim | Não |
| EgressCacheNoCache | Transferência de dados de saída para ativos que são impedidos de serem colocados em cache devido a uma configuração de utilizador no edge. | Sim | Sim | Não |
| EgressCacheUncacheable | Transferência de dados de saída para os recursos que são impedidos de serem colocados em cache pelo Cache-Control e/ou os cabeçalhos de Expires do recurso. Indica que ele deve não ser armazenados em cache num POP ou pelo cliente HTTP. | Sim | Sim | Não |
| EgressCacheOthers | Transferências de dados de saída para outros cenários de cache. | Não | Sim | Não |

* Transferência de dados saída refere-se ao tráfego entregue a partir de servidores de POP da CDN ao cliente.


### <a name="schema-of-the-core-analytics-logs"></a>Esquema de registos de análise de núcleo 

Todos os registos são armazenados no formato JSON e cada entrada tem campos de cadeia de caracteres, de acordo com o esquema seguinte:

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Em que *tempo* representa a hora de início do limite de hora para o qual as estatísticas é comunicada. Quando uma métrica não é suportada por um fornecedor CDN, em vez de um valor double ou de número inteiro, há um valor nulo. Esse valor nulo indica a ausência de uma métrica e é diferente de um valor de 0. Existe um conjunto destas métricas por domínio configurado no ponto final.

Propriedades de exemplo:

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>Recursos adicionais

* [Registos de diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Análise principal através do portal suplementar do CDN do Azure](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Registos de Monitor do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [API de REST do Azure Log Analytics](https://docs.microsoft.com/rest/api/loganalytics)







