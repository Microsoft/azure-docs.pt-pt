---
title: Integridade de back-end e logs de diagnóstico
titleSuffix: Azure Application Gateway
description: Saiba como habilitar e gerenciar logs de acesso e logs de desempenho para Aplicativo Azure gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/22/2019
ms.author: victorh
ms.openlocfilehash: bfae540af1c501c09ec026b97ac11e8a14b177a9
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326551"
---
# <a name="back-end-health-and-diagnostic-logs-for-application-gateway"></a>Integridade de back-end e logs de diagnóstico para o gateway de aplicativo

Você pode monitorar Aplicativo Azure recursos de gateway das seguintes maneiras:

* [Integridade de back-end](#back-end-health): o gateway de aplicativo fornece a capacidade de monitorar a integridade dos servidores nos pools de back-ends por meio do portal do Azure e do PowerShell. Você também pode encontrar a integridade dos pools de back-end por meio dos logs de diagnóstico de desempenho.

* [Logs](#diagnostic-logging): os logs permitem que o desempenho, o acesso e outros dados sejam salvos ou consumidos de um recurso para fins de monitoramento.

* [Métricas](application-gateway-metrics.md): o gateway de aplicativo tem várias métricas que ajudam a verificar se o sistema está sendo executado conforme o esperado.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="back-end-health"></a>Integridade de back-end

O gateway de aplicativo fornece a capacidade de monitorar a integridade de membros individuais dos pools de back-end por meio do portal, do PowerShell e da CLI (interface de linha de comando). Você também pode encontrar um resumo de integridade agregado de pools de back-end por meio dos logs de diagnóstico de desempenho. 

O relatório de integridade de back-end reflete a saída da investigação de integridade do gateway de aplicativo para as instâncias de back-end. Quando a investigação é bem-sucedida e o back-end pode receber tráfego, ele é considerado íntegro. Caso contrário, ele é considerado não íntegro.

> [!IMPORTANT]
> Se houver um NSG (grupo de segurança de rede) em uma sub-rede de gateway de aplicativo, abra os intervalos de porta 65503-65534 para SKUs v1 e 65200-65535 para SKUs v2 na sub-rede do gateway de aplicativo para o tráfego de entrada. Esse intervalo de portas é necessário para a comunicação de infraestrutura do Azure. Estão protegidas (bloqueadas) pelos certificados do Azure. Sem os certificados apropriados, as entidades externas, incluindo os clientes desses gateways, não poderão iniciar nenhuma alteração nesses pontos de extremidade.


### <a name="view-back-end-health-through-the-portal"></a>Exibir a integridade do back-end por meio do portal

No portal, a integridade do back-end é fornecida automaticamente. Em um gateway de aplicativo existente, selecione **monitoramento** > **integridade de back-end**. 

Cada membro no pool de back-ends é listado nesta página (seja uma NIC, um IP ou um FQDN). Nome do pool de back-end, porta, nome de configurações HTTP de back-end e status de integridade são mostrados. Os valores válidos para o status de integridade são **íntegros**, não **íntegros**e **desconhecidos**.

> [!NOTE]
> Se você vir um status de integridade de back-end **desconhecido**, verifique se o acesso ao back-end não está bloqueado por uma regra NSG, uma UDR (rota definida pelo usuário) ou um DNS personalizado na rede virtual.

![Integridade de back-end][10]

### <a name="view-back-end-health-through-powershell"></a>Exibir a integridade do back-end por meio do PowerShell

O código do PowerShell a seguir mostra como exibir a integridade do back-end usando o cmdlet `Get-AzApplicationGatewayBackendHealth`:

```powershell
Get-AzApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli"></a>Exibir a integridade do back-end por meio do CLI do Azure

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>Resultados

O trecho a seguir mostra um exemplo da resposta:

```json
{
"BackendAddressPool": {
    "Id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/appGatewayBackendPool"
},
"BackendHttpSettingsCollection": [
    {
    "BackendHttpSettings": {
        "Id": "/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
    },
    "Servers": [
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        },
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        }
    ]
    }
]
}
```

## <a name="diagnostic-logging"></a>Logs de diagnóstico

Você pode usar diferentes tipos de logs no Azure para gerenciar e solucionar problemas de gateways de aplicativo. Pode aceder a alguns destes registos através do portal. Todos os logs podem ser extraídos do armazenamento de BLOBs do Azure e exibidos em diferentes ferramentas, como [logs de Azure monitor](../azure-monitor/insights/azure-networking-analytics.md), Excel e Power bi. Você pode saber mais sobre os diferentes tipos de logs na lista a seguir:

* **Log de atividades**: você pode usar os [logs de atividades do Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (anteriormente conhecidos como logs operacionais e logs de auditoria) para exibir todas as operações que são enviadas à sua assinatura do Azure e seu status. As entradas de registos de atividades são recolhidas por predefinição e pode visualizá-las no portal do Azure.
* **Log de acesso**: você pode usar esse log para exibir padrões de acesso do gateway de aplicativo e analisar informações importantes. Isso inclui o IP do chamador, a URL solicitada, a latência de resposta, o código de retorno e os bytes de entrada e saída. Um log de acesso é coletado a cada 300 segundos. Esse log contém um registro por instância do gateway de aplicativo. A instância do gateway de aplicativo é identificada pela propriedade instanceId.
* **Log de desempenho**: você pode usar esse log para exibir como as instâncias do gateway de aplicativo estão sendo executadas. Esse log captura informações de desempenho para cada instância, incluindo total de solicitações atendidas, taxa de transferência em bytes, total de solicitações atendidas, contagem de solicitações com falha e contagem de instâncias de back-end íntegras e não íntegras. Um log de desempenho é coletado A cada 60 segundos. O log de desempenho está disponível apenas para a SKU v1. Para a SKU v2, use [métricas](application-gateway-metrics.md) para dados de desempenho.
* **Log de firewall**: você pode usar esse log para exibir as solicitações que são registradas por meio do modo de detecção ou prevenção de um gateway de aplicativo configurado com o Firewall do aplicativo Web.

> [!NOTE]
> Os logs estão disponíveis somente para os recursos implantados no modelo de implantação Azure Resource Manager. Você não pode usar logs para recursos no modelo de implantação clássico. Para uma melhor compreensão dos dois modelos, consulte o artigo [entendendo a implantação do Resource Manager e a implantação clássica](../azure-resource-manager/resource-manager-deployment-model.md) .

Tem três opções para armazenar os registos:

* **Conta de armazenamento**: as contas de armazenamento são ideais para os registos quando estes são armazenados durante um período mais longo e revistos quando necessário.
* **Hubs de eventos**: os hubs de eventos são uma ótima opção para integração com outras ferramentas de Siem (gerenciamento de eventos e informações de segurança) para obter alertas sobre seus recursos.
* **Logs de Azure monitor**: os logs de Azure monitor são mais bem usados para o monitoramento geral em tempo real de seu aplicativo ou a análise de tendências.

### <a name="enable-logging-through-powershell"></a>Habilitar o log por meio do PowerShell

O registo de atividades é ativado automaticamente para todos os recursos do Resource Manager. Você deve habilitar o log de desempenho e acesso para começar a coletar os dados disponíveis por meio desses logs. Para habilitar o registro em log, use as seguintes etapas:

1. Anote o ID de recurso da conta de armazenamento, onde os dados de registo são armazenados. Esse valor está no formato:/subscriptions/\<SubscriptionId\>/resourceGroups/\<nome do grupo de recursos\>/providers/Microsoft.Storage/storageAccounts/\<nome da conta de armazenamento\>. Pode utilizar qualquer conta de armazenamento na sua subscrição. Pode utilizar o portal do Azure para encontrar estas informações.

    ![Portal: ID de recurso da conta de armazenamento](./media/application-gateway-diagnostics/diagnostics1.png)

2. Anote a ID de recurso do gateway de aplicativo para a qual o log está habilitado. Esse valor está no formato:/subscriptions/\<SubscriptionId\>/resourceGroups/\<nome do grupo de recursos\>/providers/Microsoft.Network/applicationGateways/\<nome do gateway de aplicativo\>. Pode utilizar o portal para encontrar estas informações.

    ![Portal: ID de recurso para o gateway de aplicativo](./media/application-gateway-diagnostics/diagnostics2.png)

3. Ative o registo de diagnósticos com o seguinte cmdlet do PowerShell:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>Os logs de atividades não exigem uma conta de armazenamento separada. A utilização do armazenamento para registo do acesso e do desempenho incorre em encargos de serviços.

### <a name="enable-logging-through-the-azure-portal"></a>Ativar o registo através do portal do Azure

1. No portal do Azure, localize o recurso e selecione **configurações de diagnóstico**.

   Para o gateway de aplicativo, três logs estão disponíveis:

   * Log de acesso
   * Log de desempenho
   * Log de firewall

2. Para começar a coletar dados, selecione **Ativar diagnóstico**.

   ![Ativando o diagnóstico][1]

3. A página **Definições de diagnóstico** fornece as definições para os registos de diagnóstico. Neste exemplo, Log Analytics armazena os logs. Também pode utilizar os hubs de eventos e uma conta de armazenamento para guardar os registos de diagnóstico.

   ![Iniciando o processo de configuração][2]

5. Digite um nome para as configurações, confirme as configurações e selecione **salvar**.

### <a name="activity-log"></a>Registo de atividades

O Azure gera o log de atividades por padrão. Os logs são preservados por 90 dias no repositório de logs de eventos do Azure. Saiba mais sobre esses logs lendo o artigo [Exibir eventos e log de atividades](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

### <a name="access-log"></a>Log de acesso

O log de acesso será gerado somente se você o tiver habilitado em cada instância do gateway de aplicativo, conforme detalhado nas etapas anteriores. Os dados são armazenados na conta de armazenamento que você especificou quando habilitou o registro em log. Cada acesso do gateway de aplicativo é registrado no formato JSON, conforme mostrado no exemplo a seguir para V1:

|Valor  |Descrição  |
|---------|---------|
|instanceId     | Instância do gateway de aplicativo que serviu a solicitação.        |
|clientIP     | IP de origem para a solicitação.        |
|clientPort     | Porta de origem para a solicitação.       |
|httpMethod     | Método HTTP usado pela solicitação.       |
|requestUri     | URI da solicitação recebida.        |
|RequestQuery     | **Servidor roteado**: instância de pool de back-end que recebeu a solicitação.</br>**X-AzureApplicationGateway-log-ID**: ID de correlação usada para a solicitação. Ele pode ser usado para solucionar problemas de tráfego nos servidores back-end. </br>**Servidor-status**: código de resposta http que o gateway de aplicativo recebeu do back-end.       |
|UserAgent     | Agente do usuário do cabeçalho de solicitação HTTP.        |
|httpStatus     | Código de status HTTP retornado ao cliente do gateway de aplicativo.       |
|httpVersion     | Versão HTTP da solicitação.        |
|receivedBytes     | Tamanho do pacote recebido, em bytes.        |
|sentBytes| Tamanho do pacote enviado, em bytes.|
|timeTaken| Período de tempo (em milissegundos) necessário para que uma solicitação seja processada e sua resposta seja enviada. Isso é calculado como o intervalo desde o momento em que o gateway de aplicativo recebe o primeiro byte de uma solicitação HTTP até a hora em que a operação de envio de resposta é concluída. É importante observar que o campo time-taken geralmente inclui a hora em que os pacotes de solicitação e resposta estão viajando pela rede. |
|sslEnabled| Se a comunicação com os pools de back-end usava o SSL. Os valores válidos são on e off.|
|host| O nome do host com o qual a solicitação foi enviada para o servidor de back-end. Se o nome de host de back-end estiver sendo substituído, este deverá refletir isso.|
|originalHost| O nome do host com o qual a solicitação foi recebida pelo gateway de aplicativo do cliente.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "host": "www.contoso.com",
        "originalHost": "www.contoso.com"
    }
}
```
Para o gateway de aplicativo e o WAF v2, os logs mostram um pouco mais de informações:

|Valor  |Descrição  |
|---------|---------|
|instanceId     | Instância do gateway de aplicativo que serviu a solicitação.        |
|clientIP     | IP de origem para a solicitação.        |
|clientPort     | Porta de origem para a solicitação.       |
|httpMethod     | Método HTTP usado pela solicitação.       |
|requestUri     | URI da solicitação recebida.        |
|UserAgent     | Agente do usuário do cabeçalho de solicitação HTTP.        |
|httpStatus     | Código de status HTTP retornado ao cliente do gateway de aplicativo.       |
|httpVersion     | Versão HTTP da solicitação.        |
|receivedBytes     | Tamanho do pacote recebido, em bytes.        |
|sentBytes| Tamanho do pacote enviado, em bytes.|
|timeTaken| Período de tempo (em **segundos**) necessário para que uma solicitação seja processada e sua resposta seja enviada. Isso é calculado como o intervalo desde o momento em que o gateway de aplicativo recebe o primeiro byte de uma solicitação HTTP até a hora em que a operação de envio de resposta é concluída. É importante observar que o campo time-taken geralmente inclui a hora em que os pacotes de solicitação e resposta estão viajando pela rede. |
|sslEnabled| Se a comunicação com os pools de back-end usava o SSL. Os valores válidos são on e off.|
|sslCipher| Conjunto de codificação que está sendo usado para comunicação SSL (se o SSL estiver habilitado).|
|sslProtocol| Protocolo SSL/TLS que está sendo usado (se o SSL estiver habilitado).|
|serverRouted| O servidor back-end para o qual o gateway de aplicativo roteia a solicitação.|
|serverStatus| Código de status HTTP do servidor de back-end.|
|serverResponseLatency| Latência da resposta do servidor de back-end.|
|host| Endereço listado no cabeçalho do host da solicitação.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "appgw_1",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "sslCipher": "",
        "sslProtocol": "",
        "serverRouted": "104.41.114.59:80",
        "serverStatus": "200",
        "serverResponseLatency": "0.023",
        "host": "www.contoso.com",
    }
}
```

### <a name="performance-log"></a>Log de desempenho

O log de desempenho será gerado somente se você o tiver habilitado em cada instância do gateway de aplicativo, conforme detalhado nas etapas anteriores. Os dados são armazenados na conta de armazenamento que você especificou quando habilitou o registro em log. Os dados de log de desempenho são gerados em intervalos de 1 minuto. Ele está disponível apenas para a SKU v1. Para a SKU v2, use [métricas](application-gateway-metrics.md) para dados de desempenho. Os seguintes dados são registrados em log:


|Valor  |Descrição  |
|---------|---------|
|instanceId     |  Instância do gateway de aplicativo para a qual os dados de desempenho estão sendo gerados. Para um gateway de aplicativo de várias instâncias, há uma linha por instância.        |
|healthyHostCount     | Número de hosts íntegros no pool de back-ends.        |
|unHealthyHostCount     | Número de hosts não íntegros no pool de back-ends.        |
|requestCount     | Número de solicitações atendidas.        |
|MOLAP | Latência média (em milissegundos) de solicitações da instância para o back-end que atende às solicitações. |
|failedRequestCount| Número de solicitações com falha.|
|Taxa de transferência| Taxa de transferência média desde o último log, medida em bytes por segundo.|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> A latência é calculada a partir do momento em que o primeiro byte da solicitação HTTP é recebido até a hora em que o último byte da resposta HTTP é enviado. É a soma do tempo de processamento do gateway de aplicativo mais o custo de rede para o back-end, mais o tempo que o back-end leva para processar a solicitação.

### <a name="firewall-log"></a>Log de firewall

O log do firewall será gerado somente se você o tiver habilitado para cada gateway de aplicativo, conforme detalhado nas etapas anteriores. Esse log também exige que o Firewall do aplicativo Web esteja configurado em um gateway de aplicativo. Os dados são armazenados na conta de armazenamento que você especificou quando habilitou o registro em log. Os seguintes dados são registrados em log:


|Valor  |Descrição  |
|---------|---------|
|instanceId     | Instância do gateway de aplicativo para a qual os dados de firewall estão sendo gerados. Para um gateway de aplicativo de várias instâncias, há uma linha por instância.         |
|clientIp     |   IP de origem para a solicitação.      |
|clientPort     |  Porta de origem para a solicitação.       |
|requestUri     | URL da solicitação recebida.       |
|ruleSetType     | Tipo de conjunto de regras. O valor disponível é OWASP.        |
|ruleSetVersion     | Versão do conjunto de regras usada. Os valores disponíveis são 2.2.9 e 3,0.     |
|ruleId     | ID da regra do evento de gatilho.        |
|message     | Mensagem amigável para o evento de disparo. Mais detalhes são fornecidos na seção de detalhes.        |
|action     |  Ação executada na solicitação. Os valores disponíveis são MATCHED e blocked.      |
|locais     | Site para o qual o log foi gerado. Atualmente, somente global é listado porque as regras são globais.|
|details     | Detalhes do evento de disparo.        |
|details.message     | Descrição da regra.        |
|details.data     | Dados específicos encontrados na solicitação que corresponderam à regra.         |
|detalhes. arquivo     | Arquivo de configuração que continha a regra.        |
|detalhes. linha     | Número de linha no arquivo de configuração que disparou o evento.       |
|nome do host   | Nome do host ou endereço IP do gateway de aplicativo.    |
|transactionId  | ID exclusiva para uma determinada transação que ajuda a agrupar várias violações de regra que ocorreram na mesma solicitação.   |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
    "hostname": "40.90.218.100", 
    "transactionId": "AYAcUqAcAcAcAcAcASAcAcAc"
  }
} 

```

### <a name="view-and-analyze-the-activity-log"></a>Ver e analisar o registo de atividades

Pode ver e analisar os dados de registo de atividades através de um dos seguintes métodos:

* **Ferramentas do Azure**: recuperar informações de registo de atividades através do Azure PowerShell, a CLI do Azure, a API REST do Azure ou o portal do Azure. As instruções passo-a-passo para cada método estão detalhadas no artigo [Operações de atividades com o Resource Manager](../azure-resource-manager/resource-group-audit.md).
* **Power BI**: se ainda não tiver uma conta do [Power BI](https://powerbi.microsoft.com/pricing), pode experimentá-lo gratuitamente. Usando os [aplicativos de modelo de Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview), você pode analisar seus dados.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Exibir e analisar os logs de acesso, desempenho e firewall

Os [logs de Azure monitor](../azure-monitor/insights/azure-networking-analytics.md) podem coletar o contador e os arquivos de log de eventos da sua conta de armazenamento de BLOBs. Inclui visualizações e capacidades de pesquisa poderosas para analisar os seus registos.

Também pode ligar à sua conta de armazenamento e obter as entradas de registo JSON para os registos de acesso e desempenho. Depois de transferir os ficheiros JSON, pode convertê-los em CSV e visualizá-los no Excel, Power BI ou qualquer outra ferramenta de visualização de dados.

> [!TIP]
> Se estiver familiarizado com os conceitos básicos do Visual Studio para alterar os valores de constantes e variáveis em C#, pode utilizar as [ferramentas de conversor de registo](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no GitHub.
> 
> 

#### <a name="analyzing-access-logs-through-goaccess"></a>Analisando logs de acesso por meio do GoAccess

Publicamos um modelo do Resource Manager que instala e executa o popular [GoAccess](https://goaccess.io/) log Analyzer para logs de acesso do gateway de aplicativo. O GoAccess fornece estatísticas valiosas de tráfego HTTP, como visitantes exclusivos, arquivos solicitados, hosts, sistemas operacionais, navegadores, códigos de status HTTP e muito mais. Para obter mais detalhes, consulte o [arquivo Leiame na pasta de modelos do Resource Manager no GitHub](https://aka.ms/appgwgoaccessreadme).

## <a name="next-steps"></a>Passos seguintes

* Visualize logs de contador e de eventos usando [logs de Azure monitor](../azure-monitor/insights/azure-networking-analytics.md).
* [Visualize o log de atividades do Azure com](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) a postagem de blog Power bi.
* [Exiba e analise os logs de atividades do Azure em Power bi e mais](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) postagens no blog.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
