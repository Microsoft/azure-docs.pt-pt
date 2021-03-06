---
title: Monitor de registos para firewall de aplicação web Azure
description: Saiba como ativar e gerir registos e para firewall de aplicação web Azure
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 116c12900f015c849c9492ed67bc11d116286c43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102611934"
---
# <a name="resource-logs-for-azure-web-application-firewall"></a>Registos de recursos para firewall de aplicação web Azure

Pode monitorizar os recursos de Firewall de aplicação web utilizando registos. Pode guardar desempenho, acesso e outros dados ou consumi-lo a partir de um recurso para fins de monitorização.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="diagnostic-logs"></a>Registos de diagnósticos

Pode utilizar diferentes tipos de registos em Azure para gerir e resolver os serviços de resolução de problemas. Pode aceder a alguns destes registos através do portal. Todos os registos podem ser extraídos do armazenamento do Azure Blob e vistos em diferentes ferramentas, tais como [registos Azure Monitor,](../../azure-monitor/insights/azure-networking-analytics.md)Excel e Power BI. Pode saber mais sobre os diferentes tipos de registos da seguinte lista:

* **Início de atividade**: Pode utilizar [registos de atividades do Azure](../../azure-resource-manager/management/view-activity-logs.md) para visualizar todas as operações que são submetidas à sua subscrição Azure e ao seu estado. As entradas de registos de atividades são recolhidas por predefinição e pode visualizá-las no portal do Azure.
* **Início de recurso de acesso**: Pode utilizar este registo para visualizar padrões de acesso do Gateway de Aplicação e analisar informações importantes. Isto inclui o IP do chamador, URL solicitado, latência de resposta, código de devolução e bytes dentro e fora. Um registo de acesso é recolhido a cada 300 segundos. Este registo contém um registo por instância do Gateway de Aplicação. A instância do Gateway de Aplicação é identificada pela propriedade instanceId.
* **Registo de recursos de desempenho**: Pode utilizar este registo para ver como as instâncias do Gateway de Aplicação estão a executar. Este registo captura informações de desempenho para cada instância, incluindo o total de pedidos servidos, o produção de bytes, o total de pedidos servidos, a contagem de pedidos falhadas e a contagem saudável e pouco saudável de instâncias de back-end. Um registo de desempenho é recolhido a cada 60 segundos. O registo de desempenho está disponível apenas para o V1 SKU. Para o v2 SKU, utilize [métricas](../../application-gateway/application-gateway-metrics.md) para dados de desempenho.
* **Registo de recursos de firewall**: Pode utilizar este registo para visualizar os pedidos que são registados através do modo de deteção ou prevenção de um gateway de aplicações configurado com a firewall da aplicação web.

> [!NOTE]
> Os registos estão disponíveis apenas para recursos implantados no modelo de implementação do Gestor de Recursos Azure. Não é possível utilizar registos de recursos no modelo clássico de implantação. Para uma melhor compreensão dos dois modelos, consulte a implementação do Understanding Resource Manager e o artigo [de implementação clássico.](../../azure-resource-manager/management/deployment-models.md)

Tem três opções para armazenar os registos:

* **Conta de armazenamento**: as contas de armazenamento são ideais para os registos quando estes são armazenados durante um período mais longo e revistos quando necessário.
* **Centros de eventos**: Os centros de eventos são uma ótima opção para integrar com outras ferramentas de informação de segurança e gestão de eventos (SIEM) para obter alertas sobre os seus recursos.
* **Registos do Monitor Azure**: Os registos do Monitor Azure são mais utilizados para monitorizar em tempo real geral a sua aplicação ou analisar tendências.

### <a name="enable-logging-through-powershell"></a>Ativar o registo através do PowerShell

O registo de atividades é ativado automaticamente para todos os recursos do Resource Manager. Tem de permitir que o registo de acesso e desempenho comece a recolher os dados disponíveis através desses registos. Para ativar o registo, utilize os seguintes passos:

1. Anote o ID de recurso da conta de armazenamento, onde os dados de registo são armazenados. Este valor é do formulário: /subscrições/ \<subscriptionId\> /resourceGroups/ \<resource group name\> /providers/Microsoft.Storage/storageAccounts/ \<storage account name\> . Pode utilizar qualquer conta de armazenamento na sua subscrição. Pode utilizar o portal do Azure para encontrar estas informações.

    ![Portal: ID de recurso para conta de armazenamento](../media/web-application-firewall-logs/diagnostics1.png)

2. Note o ID de recursos do seu gateway de aplicação para o qual a sessão está ativada. Este valor é do formulário: /subscrições/ \<subscriptionId\> /resourceGroups/ \<resource group name\> /providers/Microsoft.Network/applicationGateways/ \<application gateway name\> . Pode utilizar o portal para encontrar estas informações.

    ![Portal: ID de recurso para gateway de aplicações](../media/web-application-firewall-logs/diagnostics2.png)

3. Ativar a tomada de registo de recursos utilizando o seguinte cmdlet PowerShell:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```

> [!TIP]
>Os registos de atividade não requerem uma conta de armazenamento separada. A utilização do armazenamento para registo do acesso e do desempenho incorre em encargos de serviços.

### <a name="enable-logging-through-the-azure-portal"></a>Ativar o registo através do portal do Azure

1. No portal Azure, encontre o seu recurso e selecione **definições de Diagnóstico**.

   Para o Gateway de Aplicações, estão disponíveis três registos:

   * Registo de acesso
   * Registo de desempenho
   * Log de firewall

2. Para começar a recolher dados, selecione **Ligue os diagnósticos**.

   ![Ligando os diagnósticos][1]

3. A página **de definições de Diagnóstico** fornece as definições para os registos de recursos. Neste exemplo, o Log Analytics armazena os registos. Também pode usar centros de eventos e uma conta de armazenamento para guardar os registos de recursos.

   ![Iniciar o processo de configuração][2]

5. Digite um nome para as definições, confirme as definições e **selecione Guardar**.

### <a name="activity-log"></a>Registo de atividades

O Azure gera o registo de atividade por predefinição. Os registos são preservados durante 90 dias na loja de registos de eventos Azure. Saiba mais sobre estes registos lendo os [eventos e o artigo de registo de atividades.](../../azure-resource-manager/management/view-activity-logs.md)

### <a name="access-log"></a>Registo de acesso

O registo de acesso só é gerado se o tiver ativado em cada instância do Gateway de Aplicação, conforme detalhado nos passos anteriores. Os dados são armazenados na conta de armazenamento que especificou quando ativou a sessão. Cada acesso do Gateway de Aplicações é registado no formato JSON, como mostra o seguinte exemplo para v1:

|Valor  |Descrição  |
|---------|---------|
|instanceId     | Aplicação Gateway instância que serviu o pedido.        |
|clientIP     | IP originária do pedido.        |
|clientPort     | Porta originária do pedido.       |
|httpMethod     | Método HTTP utilizado pelo pedido.       |
|requestUri     | URI do pedido recebido.        |
|PedidoQuery     | **Server-Routed**: Instância de piscina de back-end que foi enviada o pedido.</br>**X-AzureApplicationGateway-LOG-ID**: ID de correlação utilizado para o pedido. Pode ser usado para resolver problemas de tráfego nos servidores de back-end. </br>**ESTADO DO SERVIDOR**: Código de resposta HTTP que o Gateway de aplicação recebeu a partir da parte de trás.       |
|UserAgent     | Agente utilizador do cabeçalho de pedido HTTP.        |
|httpStatus     | O código de estado HTTP devolvido ao cliente a partir do Application Gateway.       |
|httpVersão     | Versão HTTP do pedido.        |
|recebeuBytes     | Tamanho do pacote recebido, em bytes.        |
|sentBytes| Tamanho do pacote enviado, em bytes.|
|timeTaken| Duração do tempo (em milissegundos) que leva para que um pedido seja processado e a sua resposta seja enviada. Isto é calculado como o intervalo a partir do momento em que o Application Gateway recebe o primeiro byte de um pedido HTTP até ao momento em que termina a operação de envio de resposta. É importante notar que o campo Time-Taken geralmente inclui o tempo que os pacotes de pedido e resposta estão viajando através da rede. |
|sslEnabled| Se a comunicação para as piscinas traseiras usou TLS/SSL. Os valores válidos estão ligados e desligados.|
|anfitrião| O nome de anfitrião com o qual o pedido foi enviado para o servidor backend. Se o nome de hospedeiro está a ser ultrapassado, este nome refletirá isso.|
|originalHost| O nome de anfitrião com o qual o pedido foi recebido pelo Gateway de Aplicação do cliente.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "timestamp": "2017-04-26T19:27:38Z",
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
Para o Gateway de Aplicações e WAF v2, os registos mostram um pouco mais de informação:

|Valor  |Descrição  |
|---------|---------|
|instanceId     | Aplicação Gateway instância que serviu o pedido.        |
|clientIP     | IP originária do pedido.        |
|clientPort     | Porta originária do pedido.       |
|httpMethod     | Método HTTP utilizado pelo pedido.       |
|requestUri     | URI do pedido recebido.        |
|UserAgent     | Agente utilizador do cabeçalho de pedido HTTP.        |
|httpStatus     | O código de estado HTTP devolvido ao cliente a partir do Application Gateway.       |
|httpVersão     | Versão HTTP do pedido.        |
|recebeuBytes     | Tamanho do pacote recebido, em bytes.        |
|sentBytes| Tamanho do pacote enviado, em bytes.|
|timeTaken| Duração do tempo (em milissegundos) que leva para que um pedido seja processado e a sua resposta seja enviada. Isto é calculado como o intervalo a partir do momento em que o Application Gateway recebe o primeiro byte de um pedido HTTP até ao momento em que termina a operação de envio de resposta. É importante notar que o campo Time-Taken geralmente inclui o tempo que os pacotes de pedido e resposta estão viajando através da rede. |
|sslEnabled| Se a comunicação com as piscinas traseiras usou TLS. Os valores válidos estão ligados e desligados.|
|sslCipher| Suíte cifrada a ser utilizada para a comunicação TLS (se o TLS estiver ativado).|
|sslProtocol| Protocolo TLS a ser utilizado (se o TLS estiver ativado).|
|serverrouted| O servidor backend que o gateway de aplicação encaminha o pedido para.|
|serverStatus| Código de estado HTTP do servidor backend.|
|servidorReseLatency| Latência da resposta do servidor de backend.|
|anfitrião| Endereço listado no cabeçalho anfitrião do pedido.|
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

O registo de desempenho só é gerado se o tiver ativado em cada instância do Gateway de Aplicação, conforme detalhado nos passos anteriores. Os dados são armazenados na conta de armazenamento que especificou quando ativou a sessão. Os dados do registo de desempenho são gerados em intervalos de 1 minuto. Está disponível apenas para o V1 SKU. Para o v2 SKU, utilize [métricas](../../application-gateway/application-gateway-metrics.md) para dados de desempenho. Os seguintes dados são registados:


|Valor  |Descrição  |
|---------|---------|
|instanceId     |  Aplicação Gateway exemplo para o qual os dados de desempenho estão a ser gerados. Para um gateway de aplicação de várias instâncias, há uma linha por exemplo.        |
|saudávelHostCount     | Número de anfitriões saudáveis na piscina de fundo.        |
|unHealthyHostCount     | Número de anfitriões insalubres na piscina de trás.        |
|pedidosoconselho     | Número de pedidos servidos.        |
|Latência | Latência média (em milissegundos) de pedidos da instância até ao fim que serve os pedidos. |
|Recatado falhou RequestCount| Número de pedidos falhados.|
|de transferência de dados| Produção média desde o último log, medido em bytes por segundo.|

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
> A latência é calculada a partir do momento em que o primeiro byte do pedido HTTP é recebido até ao momento em que o último byte da resposta HTTP é enviado. É a soma do tempo de processamento do Application Gateway mais o custo da rede para a parte de trás, mais o tempo que a parte de trás leva para processar o pedido.

### <a name="firewall-log"></a>Log de firewall

O registo de firewall só é gerado se o tiver ativado para cada porta de entrada de aplicação, conforme detalhado nos passos anteriores. Este registo também requer que a firewall da aplicação web esteja configurada num gateway de aplicações. Os dados são armazenados na conta de armazenamento que especificou quando ativou a sessão. Os seguintes dados são registados:


|Valor  |Descrição  |
|---------|---------|
|instanceId     | Aplicação Gateway exemplo para o qual os dados de firewall estão a ser gerados. Para um gateway de aplicação de várias instâncias, há uma linha por exemplo.         |
|clienteIp     |   IP originária do pedido.      |
|clientPort     |  Porta originária do pedido.       |
|requestUri     | URL do pedido recebido.       |
|RuleSetType     | Tipo de definição de regra. O valor disponível é OWASP.        |
|regrasPartever     | Versão definida por regras utilizada. Os valores disponíveis são 2.2.9 e 3.0.     |
|ruleId     | ID de regra do evento de desencadeamento.        |
|message     | Mensagem fácil de utilizar para o evento de desencadeamento. Mais detalhes são fornecidos na secção de detalhes.        |
|ação     |  Medidas tomadas no pedido. Os valores disponíveis são bloqueados e permitidos (para regras personalizadas), compatíveis (quando uma regra corresponde a uma parte do pedido) e Detetados e Bloqueados (estes são ambos para regras obrigatórias, dependendo se o WAF está em modo de deteção ou prevenção).      |
|site     | Local para o qual o registo foi gerado. Atualmente, apenas a Global está listada porque as regras são globais.|
|detalhes     | Detalhes do evento de desencadeamento.        |
|detalhes.mensagem     | Descrição da regra.        |
|detalhes.dados     | Dados específicos encontrados no pedido que correspondam à regra.         |
|detalhes.arquivo     | Ficheiro de configuração que continha a regra.        |
|detalhes.linha     | Número de linha no ficheiro de configuração que desencadeou o evento.       |
|nome anfitrião   | Nome de anfitrião ou endereço IP do Gateway de aplicações.    |
|transactionId  | ID exclusivo para uma determinada transação que ajuda agrupar múltiplas violações de regras que ocorreram dentro do mesmo pedido.   |
|policyId   | ID exclusivo da Política de Firewall associado ao Gateway de Aplicação, Ouvinte ou Caminho.   |
|policyScope    | A localização da política - valores podem ser "Global", "Ouvinte", ou "Localização".   |
|nome doscope policy   | O nome do objeto onde a apólice é aplicada.    |

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
* **Power BI**: se ainda não tiver uma conta do [Power BI](https://powerbi.microsoft.com/pricing), pode experimentá-lo gratuitamente. Ao utilizar as [aplicações do modelo Power BI,](/power-bi/service-template-apps-overview)pode analisar os seus dados.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Ver e analisar os registos de acesso, desempenho e firewall

[Os registos do Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md) podem recolher os ficheiros de registo de contador e de eventos na sua conta de armazenamento Blob. Inclui visualizações e capacidades de pesquisa poderosas para analisar os seus registos.

Também pode ligar à sua conta de armazenamento e obter as entradas de registo JSON para os registos de acesso e desempenho. Depois de transferir os ficheiros JSON, pode convertê-los em CSV e visualizá-los no Excel, Power BI ou qualquer outra ferramenta de visualização de dados.

> [!TIP]
> Se estiver familiarizado com os conceitos básicos do Visual Studio para alterar os valores de constantes e variáveis em C#, pode utilizar as [ferramentas de conversor de registo](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no GitHub.
>
>

#### <a name="analyzing-access-logs-through-goaccess"></a>Analisar registos de acesso através do GoAccess

Publicámos um modelo de Gestor de Recursos que instala e executa o popular analisador [de registos GoAccess](https://goaccess.io/) para Registos de Acesso gateway de aplicações. O GoAccess fornece valiosas estatísticas de tráfego HTTP, tais como Visitantes Exclusivos, Ficheiros Solicitados, Anfitriões, Sistemas Operativos, Navegadores, códigos de Estado HTTP e muito mais. Para mais detalhes, consulte o [ficheiro Readme na pasta do modelo Do Gestor de Recursos no GitHub](https://aka.ms/appgwgoaccessreadme).

## <a name="next-steps"></a>Passos seguintes

* Visualizar registos de contador e evento utilizando [registos do Monitor Azure](../../azure-monitor/insights/azure-networking-analytics.md).
* [Visualize o seu registo de atividades Azure com o](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) post de blog power BI.
* [Ver e analisar registos de atividades do Azure no Power BI e mais](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) post de blog.

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
