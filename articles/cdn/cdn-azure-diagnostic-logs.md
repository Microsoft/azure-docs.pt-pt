---
title: Troncos de diagnóstico azure / Microsoft Docs
description: O cliente pode ativar a análise de registo para o Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: allensu
ms.openlocfilehash: 35d028a38e6ac19f270abcc8708a532b3749eb39
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81254806"
---
# <a name="azure-diagnostic-logs"></a>Registos de diagnóstico do Azure

Com registos de diagnóstico Azure, pode ver a análise do núcleo e guardá-las em um ou mais destinos, incluindo:

 - conta de Armazenamento do Azure
 - Azure Event Hubs
 - [Área de trabalho do Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Esta funcionalidade está disponível em pontos finais da CDN para todos os níveis de preços. 

Os registos de diagnóstico azure permitem-lhe exportar métricas básicas de utilização do seu ponto final cdN para uma variedade de fontes para que possa consumi-las de forma personalizada. Por exemplo, pode fazer os seguintes tipos de exportação de dados:

- Exportar dados para armazenamento de bolhas, exportar para CSV, e gerar gráficos em Excel.
- Exportar dados para Centros de Eventos e correlacionar com dados de outros serviços do Azure.
- Exportar dados para registos do Monitor Azure e ver dados no seu próprio espaço de trabalho log analytics

O diagrama seguinte mostra uma visão típica do núcleo de Análise cDN dos dados.

![portal - Registos de diagnóstico](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Figura 1 - Vista de análise do núcleo cdN*

Para obter mais informações sobre registos de diagnóstico, consulte [Registos de Diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-logging-with-the-azure-portal"></a>Ativar o registo com o portal do Azure

Siga estes passos, permitir o login com aanálise do núcleo cdN:

Inicie sessão no [portal do Azure](https://portal.azure.com). Se ainda não tiver ativado o CDN para o seu fluxo de trabalho, [crie um perfil De CDN Azure e um ponto final](cdn-create-new-endpoint.md) antes de continuar.

1. No portal Azure, navegue para **o perfil CDN**.

2. No portal Azure, procure um perfil CDN ou selecione um do seu painel de instrumentos. Em seguida, selecione o ponto final do CDN para o qual pretende ativar os registos de diagnóstico.

    ![portal - Registos de diagnóstico](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Selecione **registos de diagnóstico** na secção MONITORAmento.

   Aparece a página de **registos** de diagnósticos.

    ![portal - Registos de diagnóstico](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Ativar a exploração madeireira com armazenamento Azure

Para utilizar uma conta de armazenamento para armazenar os registos, siga estes passos:
    
1. Para **Nome,** introduza um nome para as definições de registo de diagnóstico.
 
2. Selecione **Archive para uma conta**de armazenamento e, em seguida, selecione **CoreAnalytics**. 

2. Para **retenção (dias)**, escolha o número de dias de retenção. A retenção de zero dias armazena os registos indefinidamente. 

    ![portal - Registos de diagnóstico](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Selecione **a conta de armazenamento**.

    Aparece a página de conta de **armazenamento Select.**

4. Selecione uma conta de armazenamento na lista de drop-down e, em seguida, selecione **OK**.

    ![portal - Registos de diagnóstico](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. Depois de terminar de fazer as definições de registo de diagnóstico, selecione **Guardar**.

### <a name="logging-with-azure-monitor"></a>Sessão de sessão com monitor Azure

Para utilizar o Monitor Azure para armazenar os registos, siga estes passos:

1. A partir da página de registos de **diagnósticos,** selecione **Enviar para Registar Análises**. 

    ![portal - Registos de diagnóstico](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. **Selecione Configure** para configurar a exploração madeireira do Monitor Azure. 

   Aparece a página de espaços de **trabalho Log Analytics.**

    >[!NOTE] 
    >As áreas de trabalho do OMS são agora referidas como áreas de trabalho do Log Analytics.

    ![portal - Registos de diagnóstico](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Selecione **Criar novo espaço de trabalho**.

    Aparece a página do **espaço de trabalho Log Analytics.**

    >[!NOTE] 
    >As áreas de trabalho do OMS são agora referidas como áreas de trabalho do Log Analytics.

    ![portal - Registos de diagnóstico](./media/cdn-diagnostics-log/07_Create-new.png)

4. Para log Analytics espaço de **trabalho,** insira um nome de espaço de trabalho Log Analytics. O nome do espaço de trabalho Log Analytics deve ser único e conter apenas letras, números e hífenes; espaços e sublinhados não são permitidos. 

5. Para **subscrição,** selecione uma subscrição existente na lista de drop-down. 

6. Para **o grupo Recursos,** crie um novo grupo de recursos ou selecione um existente.

7. Para **localização,** selecione uma localização da lista.

8. Selecione **Pin para dashboard** se pretender guardar a configuração do registo para o seu painel de instrumentos. 

9. Selecione **OK** para completar a configuração.

10. Após a criação do seu espaço de trabalho, é devolvido à página de registos de **diagnóstico.** Confirme o nome do seu novo espaço de trabalho Log Analytics.

    ![portal - Registos de diagnóstico](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Selecione **CoreAnalytics**e, em seguida, selecione **Guardar**.

12. Para ver o novo espaço de trabalho do Log Analytics, selecione **Core analytics** a partir da sua página final do CDN.

    ![portal - Registos de diagnóstico](./media/cdn-diagnostics-log/cdn-core-analytics-page.png) 

    O seu espaço de trabalho log Analytics está agora pronto para registar dados. Para consumir esses dados, deve utilizar uma solução de [registos do Monitor Azure,](#consuming-diagnostics-logs-from-a-log-analytics-workspace)abordada mais tarde neste artigo.

Para obter mais informações sobre os atrasos dos dados de registo, consulte [os atrasos dos dados](#log-data-delays)do Registo .

## <a name="enable-logging-with-powershell"></a>Ativar registo com o PowerShell

O exemplo que se segue mostra como ativar os registos de diagnóstico através dos Cmdlets Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Habilitar os registos de diagnóstico numa conta de armazenamento

1. Faça login e selecione uma subscrição:

    Connect-AzAccount 

    Select-AzureSubscription -SubscriptionId 

2. Para ativar registos de diagnóstico numa conta de Armazenamento, introduza este comando:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. Para permitir o registo de diagnósticos num espaço de trabalho do Log Analytics, introduza este comando:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Consumir registos de diagnóstico do Armazenamento do Microsoft Azure
Esta secção descreve o esquema da análise do núcleo cdN, como está organizado dentro de uma conta de armazenamento Azure, e fornece código de amostra para descarregar os registos num ficheiro CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Usando o Microsoft Azure Storage Explorer
Antes de poder aceder aos dados de análise de base a partir de uma conta de armazenamento Azure, primeiro precisa de uma ferramenta para aceder aos conteúdos numa conta de armazenamento. Apesar de existirem várias ferramentas disponíveis no mercado, a que recomendamos é o Microsoft Azure Storage Explorer. Para descarregar a ferramenta, consulte [o Azure Storage Explorer](https://storageexplorer.com/). Depois de descarregar e instalar o software, configure-o para utilizar a mesma conta de armazenamento Azure que foi configurada como um destino para os Registos de Diagnóstico da CDN.

1.  Abra o **Microsoft Azure Storage Explorer**
2.  Localizar a conta de armazenamento
3.  Expanda o nó **blob containers** sob esta conta de armazenamento.
4.  Selecione o recipiente denominado *insights-logs-coreanalytics*.
5.  Os resultados aparecem no painel da direita, começando pelo primeiro nível, como *recursoId=*. Continue a selecionar cada nível até encontrar o ficheiro *PT1H.json*. Para uma explicação do caminho, consulte o [formato do caminho Blob.](cdn-azure-diagnostic-logs.md#blob-path-format)
6.  Cada ficheiro *BLOB PT1H.json* representa os registos de análise durante uma hora para um ponto final específico do CDN ou o seu domínio personalizado.
7.  O esquema do conteúdo deste ficheiro JSON é descrito na secção esquema dos registos de análise do núcleo.


#### <a name="blob-path-format"></a>Formato de caminho blob

Os registos de análise core são gerados a cada hora e os dados são recolhidos e armazenados dentro de uma única bolha Azure como uma carga útil JSON. Como a ferramenta do explorador de armazenamento interpreta '/' como um separador de diretórios e mostra a hierarquia, o caminho para a bolha Azure aparece como se houvesse uma estrutura hierárquica e representa o nome blob. O nome da bolha segue a seguinte convenção de nomeação:   

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Descrição dos campos:**

|Valor|Descrição|
|-------|---------|
|ID da subscrição    |ID da assinatura Azure em formato Guid.|
|Nome do grupo de recursos |Nome do grupo de recursos a que pertencem os recursos da CDN.|
|Profile Name (Nome do Perfil) |Nome do perfil cdn|
|Nome do ponto final |Nome do Ponto Final da CDN|
|Ano|  Representação de quatro dígitos do ano, por exemplo, 2017|
|Mês| Representação de dois dígitos do número do mês. 01=janeiro ... 12=Dezembro|
|Dia|   Representação de dois dígitos do dia do mês|
|PT1H.json| Arquivo JSON real onde os dados de análise são armazenados|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Exportação dos dados de análise do núcleo para um ficheiro CSV

Para facilitar o acesso à análise do núcleo, é fornecido código de amostra para uma ferramenta. Esta ferramenta permite o download dos ficheiros JSON num formato de ficheiro separado por vírpostas planas, que pode ser usado para criar gráficos ou outras agregações.

Eis como pode usar a ferramenta:

1.  Visite o link GitHub:[https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Descarregue o código.
3.  Siga as instruções para compilar e configurar.
4.  Executar a ferramenta.
5.  O ficheiro CSV resultante mostra os dados de análise numa simples hierarquia plana.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Consumir registos de diagnóstico de uma área de trabalho do Log Analytics
O Azure Monitor é um serviço Azure que monitoriza os ambientes de nuvem e no local para manter a sua disponibilidade e desempenho. Recolhe dados gerados por recursos nos seus ambientes na cloud e no local e de outras ferramentas de monitorização, para disponibilizar análises relativas a várias origens. 

Para utilizar o Monitor Azure, tem de ativar o [loging](#enable-logging-with-azure-storage) no espaço de trabalho Azure Log Analytics, que é discutido anteriormente neste artigo.

### <a name="using-the-log-analytics-workspace"></a>Usando o espaço de trabalho Log Analytics

 O diagrama seguinte mostra a arquitetura das inputs e saídas do repositório:

![Área de trabalho do Log Analytics](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Figura 3 - Log Analytics Repositório*

Pode apresentar os dados de várias formas utilizando soluções de gestão. Pode obter Soluções de Gestão no [Mercado Azure.](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions)

Pode instalar soluções de monitorização a partir do mercado Azure selecionando o link **Get agora** na parte inferior de cada solução.

### <a name="add-an-azure-monitor-cdn-monitoring-solution"></a>Adicione uma solução de monitorização CDN do Monitor Azure

Siga estes passos para adicionar uma solução de monitorização do Monitor Azure:

1.   Inscreva-se no portal Azure utilizando a sua assinatura Azure e vá ao seu painel de instrumentos.
    ![Dashboard do Azure](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. Na **página Nova,** no âmbito do **Marketplace,** **selecione Monitorização + gestão.**

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. Na página **de monitorização + gestão,** selecione **Ver tudo**.

    ![Ver todos](./media/cdn-diagnostics-log/15_See-all.png)

4. Procure o CDN na caixa de pesquisa.

    ![Ver todos](./media/cdn-diagnostics-log/16_Search-for.png)

5. Selecione **Azure CDN Core Analytics**. 

    ![Ver todos](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. Depois de selecionar **Criar,** é-lhe pedido que crie um novo espaço de trabalho log Analytics ou utilize um existente. 

    ![Ver todos](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Selecione o espaço de trabalho que criou anteriormente. Em seguida, precisa adicionar uma conta de automação.

    ![Ver todos](./media/cdn-diagnostics-log/19_Add-automation.png)

8. O ecrã seguinte mostra o formulário de conta de automação que deve preencher. 

    ![Ver todos](./media/cdn-diagnostics-log/20_Automation.png)

9. Depois de ter criado a conta de automação, está pronto para adicionar a sua solução. Selecione o botão **Criar**.

    ![Ver todos](./media/cdn-diagnostics-log/21_Ready.png)

10. A sua solução foi agora adicionada ao seu espaço de trabalho. Volte ao seu portal Azure dashboard.

    ![Ver todos](./media/cdn-diagnostics-log/22_Dashboard.png)

    Selecione o espaço de trabalho log Analytics que criou para ir ao seu espaço de trabalho. 

11. Selecione o azulejo **Portal OMS** para ver a sua nova solução.

    ![Ver todos](./media/cdn-diagnostics-log/23_workspace.png)

12. O seu portal deve agora parecer o seguinte ecrã:

    ![Ver todos](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Selecione um dos azulejos para ver várias vistas nos seus dados.

    ![Ver todos](./media/cdn-diagnostics-log/25_Interior-view.png)

    Pode deslocar-se para a esquerda ou para a direita para ver mais azulejos que representem vistas individuais para os dados. 

    Selecione um dos azulejos para ver mais detalhes sobre os seus dados.

     ![Ver todos](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Ofertas e níveis de preços

Pode ver ofertas e níveis de preços para soluções de gestão [aqui](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions).

### <a name="customizing-views"></a>Visualizações personalizadas

Pode personalizar a vista para os seus dados utilizando o **View Designer**. Para começar a desenhar, vá ao seu espaço de trabalho Log Analytics e selecione o azulejo **View Designer.**

![Estruturador de Vista](./media/cdn-diagnostics-log/27_Designer.png)

Arraste e deixe cair os tipos de gráficos e preencha os dados que pretende analisar.

![Estruturador de Vista](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Registar atrasos de dados

A tabela seguinte mostra atrasos de dados de registo para **o Azure CDN Standard da Microsoft,** **O Padrão CDN Azure da Akamai**e o **Azure CDN Standard/Premium da Verizon**.

Atrasos nos dados de registo da Microsoft | Atrasos nos dados de registo de Verizon | Atrasos nos dados de registo da Akamai
--- | --- | ---
Atrasado por 1 hora. | Atrasado por 1 hora e pode demorar até 2 horas para começar a aparecer após a conclusão da propagação do ponto final. | Adiado por 24 horas; se foi criado há mais de 24 horas, leva até 2 horas para começar a aparecer. Se foi recentemente criado, pode levar até 25 horas para os registos começarem a aparecer.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Tipos de registo de diagnóstico para análise de núcleo cdn

A Microsoft atualmente oferece apenas registos de análise principal, que contêm métricas que mostram estatísticas de resposta HTTP e estatísticas de saída como visto a partir dos POPs/bordas do CDN.

### <a name="core-analytics-metrics-details"></a>Detalhes das métricas de análise do núcleo
A tabela seguinte mostra uma lista de métricas disponíveis nos registos de análise core para **O Padrão CDN Azure da Microsoft**, **Azure CDN Standard da Akamai**, e **Azure CDN Standard/Premium da Verizon**. Nem todas as métricas estão disponíveis de todos os fornecedores, embora tais diferenças sejam mínimas. A tabela também mostra se uma determinada métrica está disponível a partir de um fornecedor. As métricas estão disponíveis apenas para os pontos finais da CDN que têm tráfego sobre eles.


|Métrica                     | Descrição | Microsoft | Verizon | Rio Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Número total de pedidos de acesso durante este período. | Sim | Sim |Sim |
| RequestCountHttpStatus2xx | Contagem de todos os pedidos que resultaram num código 2xx HTTP (por exemplo, 200, 202). | Sim | Sim |Sim |
| RequestCountHttpStatus3xx | Contagem de todos os pedidos que resultaram num código HTTP 3xx (por exemplo, 300, 302). | Sim | Sim |Sim |
| RequestCountHttpStatus4xx | Contagem de todos os pedidos que resultaram num código 4xx HTTP (por exemplo, 400, 404). | Sim | Sim |Sim |
| RequestCountHttpStatus5xx | Contagem de todos os pedidos que resultaram num código 5xx HTTP (por exemplo, 500, 504). | Sim | Sim |Sim |
| RequestCountHttpstatusOthers | Contagem de todos os outros códigos HTTP (fora de 2xx-5xx). | Sim | Sim |Sim |
| RequestCountHttpStatus200 | Contagem de todos os pedidos que resultaram numa resposta de código 200 HTTP. | Sim | Não  |Sim |
| RequestCountHttpStatus206 | Contagem de todos os pedidos que resultaram numa resposta de código http 206. | Sim | Não  |Sim |
| RequestCountHttpStatus302 | Contagem de todos os pedidos que resultaram numa resposta de código HTTP 302. | Sim | Não  |Sim |
| RequestCountHttpStatus304 | Contagem de todos os pedidos que resultaram numa resposta de código 304 HTTP. | Sim | Não  |Sim |
| RequestCountHttpStatus404 | Contagem de todos os pedidos que resultaram numa resposta de código 404 HTTP. | Sim | Não  |Sim |
| RequestCountCacheHit | Contagem de todos os pedidos que resultaram num golpe de Cache. O ativo foi servido diretamente do POP para o cliente. | Sim | Sim | Não  |
| RequestCountCachemiss | Contagem de todos os pedidos que resultaram numa falta de Cache. Uma falha cache significa que o ativo não foi encontrado no POP mais próximo do cliente, e, portanto, foi recuperado da Origem. | Sim | Sim | Não |
| RequestCountCachenocache | Conte todos os pedidos a um ativo que esteja impedido de ser cache devido a uma configuração do utilizador na borda. | Sim | Sim | Não |
| RequestCountCacheUncacheable | Contagem de todos os pedidos a ativos que são impedidos de serem cached pelos cabeçalhos cache-control e expirados do ativo, que indicam que não deve ser cacheem em um POP ou pelo cliente HTTP. | Sim | Sim | Não |
| RequestCountcacheOutros | Contagem de todos os pedidos com estatuto de cache não coberto por acima. | Não | Sim | Não  |
| EgressTotal | Transferência de dados de saída em GB | Sim |Sim |Sim |
| EgressHttpStatus2xx | Transferência de dados de saída* para respostas com códigos de estado 2xx HTTP em GB. | Sim | Sim | Não  |
| EgressHttpStatus3xx | Transferência de dados de saída para respostas com códigos de estado 3xx HTTP em GB. | Sim | Sim | Não  |
| EgressHttpStatus4xx | Transferência de dados de saída para respostas com códigos de estado 4xx HTTP em GB. | Sim | Sim | Não  |
| EgressHttpStatus5xx | Transferência de dados de saída para respostas com códigos de estado 5xx HTTP em GB. | Sim | Sim | Não |
| EgressHttpStatusOthers | Transferência de dados de saída para respostas com outros códigos de estado HTTP em GB. | Sim | Sim | Não  |
| EgressCacheHit | Transferência de dados de saída para respostas que foram entregues diretamente a partir da cache CDN nos POPs/Bordas cDN. | Sim | Sim | Não |
| EgressCacheMiss. | Transferência de dados de saída para respostas que não foram encontradas no servidor POP mais próximo, e recuperadas do servidor de origem. | Sim | Sim | Não |
| EgressCacheNoCache | Transferência de dados de saída para ativos que são impedidos de ser protegidos devido a uma configuração do utilizador na borda. | Sim | Sim | Não |
| EgressCacheUncacheable | Transferência de dados de saída para ativos que são impedidos de ser cached pelos cabeçalhos de Cache-Control e/ou Expirados do ativo. Indica que não deve ser colocado em cache num POP ou pelo cliente HTTP. | Sim | Sim | Não |
| EgressCacheOthers | Transferências de dados de saída para outros cenários de cache. | Não | Sim | Não |

*A transferência de dados de saída refere-se ao tráfego entregue a partir de servidores POP CDN para o cliente.


### <a name="schema-of-the-core-analytics-logs"></a>Esquema dos registos de análise do núcleo 

Todos os registos são armazenados em formato JSON e cada entrada tem campos de cordas de acordo com o seguinte esquema:

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

Onde o *tempo* representa o tempo de início do limite de hora para o qual as estatísticas são reportadas. Quando uma métrica não é suportada por um fornecedor de CDN, em vez de um valor duplo ou inteiro, há um valor nulo. Este valor nulo indica a ausência de uma métrica, e é diferente de um valor de 0. Há um conjunto destas métricas por domínio configurado no ponto final.

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

* [Troncos de diagnóstico azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Análise de núcleo através do portal suplementar Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Registos do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Azure Log Analytics REST API](https://docs.microsoft.com/rest/api/loganalytics)







