---
title: Monitor de registos para firewall de aplicação web Azure
description: Saiba como ativar e gerir registos e para firewall de aplicação web azure
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1b807908c9fb54ecf15de6d44a04760659196a31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980976"
---
# <a name="diagnostic-logs-for-azure-web-application-firewall"></a>Registos de diagnóstico para firewall de aplicação web Azure

Pode monitorizar os recursos firewall da Aplicação Web utilizando registos. Pode poupar desempenho, acesso e outros dados ou consumi-lo a partir de um recurso para fins de monitorização.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="diagnostic-logs"></a>Registos de diagnósticos

Pode utilizar diferentes tipos de registos em Azure para gerir e resolver problemas nos gateways de aplicações. Pode aceder a alguns destes registos através do portal. Todos os registos podem ser extraídos do armazenamento da Blob Azure e vistos em diferentes ferramentas, tais como [registos Do Monitor Azure,](../../azure-monitor/insights/azure-networking-analytics.md)Excel e Power BI. Pode saber mais sobre os diferentes tipos de registos da seguinte lista:

* **Registo de atividades**: Pode utilizar registos de atividade do [Azure](../../azure-resource-manager/management/view-activity-logs.md) (anteriormente conhecidos como registos operacionais e registos de auditoria) para visualizar todas as operações submetidas à sua subscrição Azure e o seu estado. As entradas de registos de atividades são recolhidas por predefinição e pode visualizá-las no portal do Azure.
* **Registo de acesso:** Pode utilizar este registo para visualizar os padrões de acesso do Application Gateway e analisar informações importantes. Isto inclui o IP do chamador, URL solicitado, latência de resposta, código de devolução e bytes dentro e fora. Um registo de acesso é recolhido a cada 300 segundos. Este registo contém um registo por instância de Gateway de Aplicação. A instância De gateway de aplicação é identificada pela propriedade instanceId.
* **Registo de desempenho:** Pode utilizar este registo para ver como as instâncias do Gateway da Aplicação estão a funcionar. Este registo captura informações de desempenho para cada instância, incluindo pedidos totais servidos, entrada em bytes, pedidos totais servidos, contagem de pedidos falhado, e contagem de instâncias saudáveis e pouco saudáveis. Um registo de desempenho é recolhido a cada 60 segundos. O registo performance está disponível apenas para o V1 SKU. Para o V2 SKU, utilize [métricas](../../application-gateway/application-gateway-metrics.md) para dados de desempenho.
* **Registo de firewall**: Pode utilizar este registo para visualizar os pedidos que são registados através do modo de deteção ou prevenção de um gateway de aplicação configurado com a firewall da aplicação web.

> [!NOTE]
> Os registos estão disponíveis apenas para recursos implantados no modelo de implementação do Gestor de Recursos Azure. Não é possível utilizar registos para recursos no modelo clássico de implantação. Para uma melhor compreensão dos dois modelos, consulte a implementação do Understanding Resource Manager e o artigo [de implantação clássico.](../../azure-resource-manager/management/deployment-models.md)

Tem três opções para armazenar os registos:

* **Conta de armazenamento**: as contas de armazenamento são ideais para os registos quando estes são armazenados durante um período mais longo e revistos quando necessário.
* Centros de **eventos**: Os hubs de eventos são uma ótima opção para integrar com outras ferramentas de informação de segurança e gestão de eventos (SIEM) para obter alertas sobre os seus recursos.
* **Registos do Monitor Azure:** Os registos do Monitor Azure são melhor utilizados para a monitorização geral em tempo real da sua aplicação ou para analisar as tendências.

### <a name="enable-logging-through-powershell"></a>Ativar o loglogging através do PowerShell

O registo de atividades é ativado automaticamente para todos os recursos do Resource Manager. Tem de permitir que o acesso e o registo de desempenho comecem a recolher os dados disponíveis através desses registos. Para ativar a exploração madeireira, utilize os seguintes passos:

1. Anote o ID de recurso da conta de armazenamento, onde os dados de registo são armazenados. Este valor é do formulário: /subscriptions/\<subscriptionId\>/resourceGroups/\<nome do grupo de recursos\>/providers/Microsoft.Storage/storageAccounts/\<nome da conta de armazenamento\>. Pode utilizar qualquer conta de armazenamento na sua subscrição. Pode utilizar o portal do Azure para encontrar estas informações.

    ![Portal: ID de recursos para conta de armazenamento](../media/web-application-firewall-logs/diagnostics1.png)

2. Note o ID de recurso do seu portal de aplicação para o qual o registo está ativado. Este valor é do formulário:\<\>/subscrições/ subscriçõesId /resourceGroups/\<resource group name\>/providers/Microsoft.Network/applicationGateways/ applicationGateways/\<application gateway name\>. Pode utilizar o portal para encontrar estas informações.

    ![Portal: ID de recursos para gateway de aplicação](../media/web-application-firewall-logs/diagnostics2.png)

3. Ative o registo de diagnósticos com o seguinte cmdlet do PowerShell:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```

> [!TIP]
>Os registos de atividade não requerem uma conta de armazenamento separada. A utilização do armazenamento para registo do acesso e do desempenho incorre em encargos de serviços.

### <a name="enable-logging-through-the-azure-portal"></a>Ativar o registo através do portal do Azure

1. No portal Azure, encontre o seu recurso e selecione **definições de Diagnóstico**.

   Para gateway de aplicação, estão disponíveis três registos:

   * Registo de acesso
   * Registo de desempenho
   * Registo de firewall

2. Para começar a recolher dados, selecione **Ligar os diagnósticos**.

   ![Ligar diagnósticos][1]

3. A página **Definições de diagnóstico** fornece as definições para os registos de diagnóstico. Neste exemplo, o Log Analytics armazena os registos. Também pode utilizar os hubs de eventos e uma conta de armazenamento para guardar os registos de diagnóstico.

   ![Iniciar o processo de configuração][2]

5. Digite um nome para as definições, confirme as definições e selecione **Guardar**.

### <a name="activity-log"></a>Registo de atividades

O Azure gera o registo de atividade por defeito. Os registos são conservados durante 90 dias na loja de registos de eventos Azure. Saiba mais sobre estes registos lendo os [eventos de Visualização e](../../azure-resource-manager/management/view-activity-logs.md) o artigo de registo de atividades.

### <a name="access-log"></a>Registo de acesso

O registo de acesso só é gerado se o tiver ativado em cada instância de Gateway de aplicação, conforme detalhado nos passos anteriores. Os dados são armazenados na conta de armazenamento que especificou quando ativou a exploração madeireira. Cada acesso ao Application Gateway é registado no formato JSON, como mostra o seguinte exemplo para v1:

|Valor  |Descrição  |
|---------|---------|
|instanceId     | Instância de gateway de aplicação que serviu o pedido.        |
|clientIP     | Originando IP para o pedido.        |
|clientPorto     | Porta de origem para o pedido.       |
|httpMethod     | Método HTTP utilizado pelo pedido.       |
|requestUri     | URI do pedido recebido.        |
|RequestQuery     | **Server-Routed**: Instância de piscina de back-end que foi enviada o pedido.</br>**X-AzureApplicationGateway-LOG-ID**: Id de correlação utilizado para o pedido. Pode ser usado para resolver problemas de tráfego nos servidores de back-end. </br>**SERVIDOR-STATUS**: Código de resposta HTTP que o Gateway de Aplicação recebeu na parte de trás.       |
|UserAgent     | Agente de utilizador do cabeçalho de pedido HTTP.        |
|httpStatus     | Código de estado HTTP devolvido ao cliente a partir do Gateway de Aplicação.       |
|httpVersão     | VERSÃO HTTP do pedido.        |
|recebidoBytes     | Tamanho do pacote recebido, em bytes.        |
|sentBytes| Tamanho do pacote enviado, em bytes.|
|tempo Tomada| O tempo (em milissegundos) que leva para que um pedido seja processado e a sua resposta seja enviada. Isto é calculado como o intervalo a partir do momento em que o Gateway de aplicação recebe o primeiro byte de um pedido HTTP para o momento em que a resposta envia a operação termina. É importante notar que o campo Time-Taken geralmente inclui o tempo que os pacotes de pedido e resposta estão viajando sobre a rede. |
|sslEnabled| Se a comunicação para as piscinas traseiras usava SSL. Valores válidos estão dentro e fora.|
|anfitrião| O nome de anfitrião com o qual o pedido foi enviado para o servidor backend. Se o nome de anfitrião está a ser ultrapassado, este nome refletirá isso.|
|anfitrião original| O nome de anfitrião com o qual o pedido foi recebido pelo Gateway de Aplicação do cliente.|
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
Para o Application Gateway e waf v2, os registos mostram um pouco mais de informação:

|Valor  |Descrição  |
|---------|---------|
|instanceId     | Instância de gateway de aplicação que serviu o pedido.        |
|clientIP     | Originando IP para o pedido.        |
|clientPorto     | Porta de origem para o pedido.       |
|httpMethod     | Método HTTP utilizado pelo pedido.       |
|requestUri     | URI do pedido recebido.        |
|UserAgent     | Agente de utilizador do cabeçalho de pedido HTTP.        |
|httpStatus     | Código de estado HTTP devolvido ao cliente a partir do Gateway de Aplicação.       |
|httpVersão     | VERSÃO HTTP do pedido.        |
|recebidoBytes     | Tamanho do pacote recebido, em bytes.        |
|sentBytes| Tamanho do pacote enviado, em bytes.|
|tempo Tomada| O tempo (em milissegundos) que leva para que um pedido seja processado e a sua resposta seja enviada. Isto é calculado como o intervalo a partir do momento em que o Gateway de aplicação recebe o primeiro byte de um pedido HTTP para o momento em que a resposta envia a operação termina. É importante notar que o campo Time-Taken geralmente inclui o tempo que os pacotes de pedido e resposta estão viajando sobre a rede. |
|sslEnabled| Se a comunicação para as piscinas traseiras usava SSL. Valores válidos estão dentro e fora.|
|sslCipher| Suíte cifra a ser utilizada para comunicação SSL (se o SSL estiver ativado).|
|sslProtocol| O protocolo SSL está a ser utilizado (se o SSL estiver ativado).|
|servidorRouted| O servidor backend que aplica o gateway de aplicação encaminha o pedido para.|
|servidorStatus| Código de estado HTTP do servidor backend.|
|servidorResponseLatency| Latência da resposta do servidor de backend.|
|anfitrião| Endereço listado no cabeçalho do anfitrião do pedido.|
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

### <a name="performance-log"></a>Registo de desempenho

O registo de desempenho só é gerado se o tiver ativado em cada instância de Gateway de aplicação, conforme detalhado nos passos anteriores. Os dados são armazenados na conta de armazenamento que especificou quando ativou a exploração madeireira. Os dados do registo de desempenho são gerados em intervalos de 1 minuto. Está disponível apenas para o V1 SKU. Para o V2 SKU, utilize [métricas](../../application-gateway/application-gateway-metrics.md) para dados de desempenho. Os seguintes dados são registados:


|Valor  |Descrição  |
|---------|---------|
|instanceId     |  Aplicativo Gateway exemplo para os dados de desempenho que estão sendo gerados. Para um gateway de aplicação de múltiplas instâncias, há uma linha por exemplo.        |
|saudáveisHostCount     | Número de anfitriões saudáveis na piscina de trás.        |
|NãoHealthyHostCount     | Número de anfitriões insalubres na piscina de trás.        |
|pedidoCount     | Número de pedidos servidos.        |
|latência | Latência média (em milissegundos) de pedidos da instância para a parte de trás que serve os pedidos. |
|failedRequestCount| Número de pedidos falhados.|
|de transferência de dados| A produção média desde o último registo, medido em bytes por segundo.|

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
> A latência é calculada a partir do momento em que o primeiro byte do pedido HTTP é recebido no momento em que o último byte da resposta HTTP é enviado. É a soma do tempo de processamento do Application Gateway mais o custo da rede para a parte de trás, mais o tempo que a parte de trás leva para processar o pedido.

### <a name="firewall-log"></a>Registo de firewall

O registo de firewall só é gerado se o tiver ativado para cada gateway de aplicação, conforme detalhado nos passos anteriores. Este registo também requer que a firewall da aplicação web esteja configurada num gateway de aplicação. Os dados são armazenados na conta de armazenamento que especificou quando ativou a exploração madeireira. Os seguintes dados são registados:


|Valor  |Descrição  |
|---------|---------|
|instanceId     | Aplicativo Gateway exemplo para os dados de firewall que estão sendo gerados. Para um gateway de aplicação de múltiplas instâncias, há uma linha por exemplo.         |
|clienteIp     |   Originando IP para o pedido.      |
|clientPorto     |  Porta de origem para o pedido.       |
|requestUri     | URL do pedido recebido.       |
|regraSetType     | Tipo de conjunto de regras. O valor disponível é OWASP.        |
|regraSetVersion     | Versão definida de regras usada. Os valores disponíveis são 2.2.9 e 3.0.     |
|regraId     | Identificação da regra do evento de desencadeamento.        |
|message     | Mensagem fácil de utilizar para o evento de desencadeamento. Mais detalhes são fornecidos na secção de detalhes.        |
|action     |  Ação tomada a pedido. Os valores disponíveis são bloqueados e permitidos.      |
|site     | Local para o qual o tronco foi gerado. Atualmente, apenas a Global está listada porque as regras são globais.|
|detalhes     | Detalhes do evento de desencadeamento.        |
|detalhes.mensagem     | Descrição da regra.        |
|detalhes.dados     | Dados específicos encontrados a pedido que correspondem à regra.         |
|detalhes.arquivo     | Ficheiro de configuração que continha a regra.        |
|detalhes.line     | Número de linha no ficheiro de configuração que desencadeou o evento.       |
|nome de anfitrião   | Nome de anfitrião ou endereço IP do Gateway de Aplicação.    |
|transaçãoId  | Identificação única para uma determinada transação que ajuda a agrupar várias violações de regras que ocorreram dentro do mesmo pedido.   |
|políticaId   | Identificação única da Política de Firewall associada ao Gateway de Aplicação, Ouvinte ou Caminho.   |
|políticaScope    | A localização da política - valores podem ser "Global", "Ouvinte" ou "Localização".   |
|policyScopeName   | O nome do objeto onde a política é aplicada.    |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.147",
      "clientPort": "0",
      "requestUri": "/",
      "ruleSetType": "OWASP",
      "ruleSetVersion": "3.0",
      "ruleId": "920350",
      "ruleGroup": "920-PROTOCOL-ENFORCEMENT",
      "message": "Host header is a numeric IP address",
      "action": "Matched",
      "site": "Global",
      "details": {
        "message": "Warning. Pattern match \"^[\\\\d.:]+$\" at REQUEST_HEADERS:Host ....",
        "data": "127.0.0.1",
        "file": "rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf",
        "line": "791"
      },
      "hostname": "127.0.0.1",
      "transactionId": "16861477007022634343",
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/perListener",
      "policyScope": "Listener",
      "policyScopeName": "httpListener1"
    }
  }
}

```

### <a name="view-and-analyze-the-activity-log"></a>Ver e analisar o registo de atividades

Pode ver e analisar os dados de registo de atividades através de um dos seguintes métodos:

* **Ferramentas do Azure**: recuperar informações de registo de atividades através do Azure PowerShell, a CLI do Azure, a API REST do Azure ou o portal do Azure. As instruções passo-a-passo para cada método estão detalhadas no artigo [Operações de atividades com o Resource Manager](../../azure-resource-manager/management/view-activity-logs.md).
* **Power BI**: se ainda não tiver uma conta do [Power BI](https://powerbi.microsoft.com/pricing), pode experimentá-lo gratuitamente. Ao utilizar as aplicações de [modelo Power BI,](https://docs.microsoft.com/power-bi/service-template-apps-overview)pode analisar os seus dados.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Ver e analisar os registos de acesso, desempenho e firewall

Os [registos do Monitor Azure](../../azure-monitor/insights/azure-networking-analytics.md) podem recolher os ficheiros de registo do balcão e do evento na sua conta de armazenamento Blob. Inclui visualizações e capacidades de pesquisa poderosas para analisar os seus registos.

Também pode ligar à sua conta de armazenamento e obter as entradas de registo JSON para os registos de acesso e desempenho. Depois de transferir os ficheiros JSON, pode convertê-los em CSV e visualizá-los no Excel, Power BI ou qualquer outra ferramenta de visualização de dados.

> [!TIP]
> Se estiver familiarizado com os conceitos básicos do Visual Studio para alterar os valores de constantes e variáveis em C#, pode utilizar as [ferramentas de conversor de registo](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no GitHub.
>
>

#### <a name="analyzing-access-logs-through-goaccess"></a>Analisar registos de acesso através do GoAccess

Publicamos um modelo de Gestor de Recursos que instala e executa o popular analisador de log [GoAccess](https://goaccess.io/) para registos de acesso a gateway de aplicação. O GoAccess fornece estatísticas de tráfego http valiosas, tais como Visitantes Únicos, Ficheiros Solicitados, Anfitriões, Sistemas Operativos, Navegadores, códigos de Estado HTTP e muito mais. Para mais detalhes, consulte o ficheiro Readme na pasta do modelo Do Gestor de [Recursos no GitHub](https://aka.ms/appgwgoaccessreadme).

## <a name="next-steps"></a>Passos seguintes

* Visualizar os registos de contadores e eventos utilizando [registos do Monitor Azure](../../azure-monitor/insights/azure-networking-analytics.md).
* [Visualize o seu log](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) de atividade do Azure com a publicação de blog Power BI.
* Ver e analisar registos de atividade do [Azure no Power BI e mais posts](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) de blog.

[1]: ../media/web-application-firewall-logs/figure1.png
[2]: ../media/web-application-firewall-logs/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
