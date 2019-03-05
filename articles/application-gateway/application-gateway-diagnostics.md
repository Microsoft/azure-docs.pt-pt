---
title: Aceder a registos do monitor, registos de desempenho, o estado de funcionamento do back-end e as métricas de Gateway de aplicação do Azure
description: Saiba como ativar e gerir registos de acesso e registos de desempenho para o Gateway de aplicação do Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 1/11/2019
ms.author: amitsriva
ms.openlocfilehash: 14b99f648bb1d7e1926aa9d5dd9926e267ba9709
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57309133"
---
# <a name="back-end-health-diagnostic-logs-and-metrics-for-application-gateway"></a>Estado de funcionamento do back-end, registos de diagnóstico e métricas para o Gateway de aplicação

Ao utilizar o Gateway de aplicação do Azure, pode monitorizar os recursos das seguintes formas:

* [Estado de funcionamento do back-end](#back-end-health): Gateway de aplicação fornece a capacidade de monitorizar o estado de funcionamento dos servidores nos agrupamentos de back-end por meio do portal do Azure e PowerShell. Também pode encontrar o estado de funcionamento dos agrupamentos de back-end por meio dos registos de diagnóstico de desempenho.

* [Registos](#diagnostic-logging): Permitir que os registos de desempenho, acesso e outros dados a ser guardado ou consumidos a partir de um recurso para efeitos de monitorização.

* [Métricas](#metrics): Gateway de aplicação atualmente tem sete métricas para ver os contadores de desempenho.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="back-end-health"></a>Estado de funcionamento do back-end

Gateway de aplicação fornece a capacidade de monitorizar o estado de funcionamento dos membros individuais dos agrupamentos de back-end através do portal, PowerShell e a interface de linha de comandos (CLI). Também pode encontrar um Estado de funcionamento agregado resumo dos conjuntos de back-end por meio dos registos de diagnóstico de desempenho. 

O relatório de estado de funcionamento do back-end reflete a saída da sonda de estado de funcionamento do Gateway de aplicação para as instâncias de back-end. Quando a investigação for concluída com êxito e o back-end pode receber tráfego, ele é considerado em bom estado. Caso contrário, é considerado incorreto.

> [!IMPORTANT]
> Se existir um grupo de segurança de rede (NSG) numa sub-rede de Gateway de aplicação, abra a intervalos de portas 65503 65534 na sub-rede de Gateway de aplicação para tráfego de entrada. Este intervalo de porta é necessário para a comunicação de infraestrutura do Azure. Estão protegidas (bloqueadas) pelos certificados do Azure. Sem os certificados adequados, as entidades externas, incluindo os clientes desses gateways, não será capazes de iniciar quaisquer alterações nesses pontos finais.


### <a name="view-back-end-health-through-the-portal"></a>Ver estado de funcionamento do back-end através do portal

No portal do Estado de funcionamento do back-end é fornecido automaticamente. Num gateway de aplicação existente, selecione **monitorização** > **estado de funcionamento do back-end**. 

Cada membro do conjunto de back-end está listado nesta página (quer se trate de uma NIC, IP ou FQDN). Nome do conjunto de back-end, porta, nome de definições de HTTP de back-end e o estado de funcionamento são apresentadas. Valores válidos para o estado de funcionamento são **bom estado de funcionamento**, **mau estado de funcionamento**, e **desconhecido**.

> [!NOTE]
> Se vir um Estado de funcionamento do back-end **desconhecido**, certifique-se de que o acesso ao back-end não está bloqueado por uma regra de NSG, uma rota definida pelo utilizador (UDR) ou um DNS personalizado na rede virtual.

![Estado de funcionamento do back-end][10]

### <a name="view-back-end-health-through-powershell"></a>Ver estado de funcionamento do back-end através do PowerShell

O código de PowerShell seguinte mostra como ver o estado de funcionamento do back-end utilizando o `Get-AzApplicationGatewayBackendHealth` cmdlet:

```powershell
Get-AzApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli"></a>Ver estado de funcionamento do back-end através da CLI do Azure

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>Resultados

O fragmento seguinte mostra um exemplo da resposta:

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

## <a name="diagnostic-logging"></a>Registos de diagnóstico

Pode utilizar diferentes tipos de registos no Azure para gerir e resolver problemas de gateways de aplicação. Pode aceder a alguns destes registos através do portal. Todos os registos podem ser extraídos de armazenamento de Blobs do Azure e visualizados em diferentes ferramentas, como [registos do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md), Excel e o Power BI. Pode saber mais sobre os diferentes tipos de registos da lista seguinte:

* **Registo de atividades**: Pode usar [registos de atividades do Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (anteriormente conhecida como registos operacionais e registos de auditoria) para ver todas as operações que são submetidas para a sua subscrição do Azure e o respetivo estado. As entradas de registos de atividades são recolhidas por predefinição e pode visualizá-las no portal do Azure.
* **Registo de acesso**: Pode utilizar este registo para ver os padrões de acesso de Gateway de aplicação e analisar informações importantes. Isto inclui o chamador IP, URL solicitada, latência de resposta, código de retorno e bytes e reduzir. Um registo de acesso é recolhido de 300 segundos. Este registo contém um registo por instância de Gateway de aplicação. A instância de Gateway de aplicação é identificada pela propriedade instanceId.
* **Registo de desempenho**: Pode utilizar este registo para ver o desempenho das instâncias de Gateway de aplicação. Este registo captura informações de desempenho para cada instância, incluindo o total de pedidos servidos, débito em bytes, total de pedidos servidos, contagem de pedidos com falha e a contagem de instâncias de back-end com e sem integridade. Um registo de desempenho é recolhido de 60 em 60 segundos.
* **Log do firewall**: Pode utilizar este registo para ver os pedidos que são registados através do modo de deteção ou prevenção de um gateway de aplicação que está configurado com a firewall de aplicações web.

> [!NOTE]
> Os registos estão disponíveis apenas para recursos implementados no modelo de implementação Azure Resource Manager. Não é possível utilizar registos para os recursos no modelo de implementação clássica. Para uma melhor compreensão dos dois modelos, consulte a [implementação do Gestor de recursos de compreensão e a implementação clássica](../azure-resource-manager/resource-manager-deployment-model.md) artigo.

Tem três opções para armazenar os registos:

* **Conta de armazenamento**: Contas de armazenamento são melhor utilizadas para os registos quando os registos são armazenados durante um período mais longo e revisados quando necessário.
* **Os hubs de eventos**: Os hubs de eventos são uma ótima opção para integrar com outras ferramentas de gestão (SEIM) de informações e eventos da segurança para obter alertas sobre seus recursos.
* **Registos de Monitor do Azure**: Registos de Monitor do Azure melhor é utilizada para monitorização em tempo real gerais da sua aplicação ou ao procurar tendências.

### <a name="enable-logging-through-powershell"></a>Ativar o registo através do PowerShell

O registo de atividades é ativado automaticamente para todos os recursos do Resource Manager. Tem de ativar o acesso e de registo para iniciar a recolha de dados disponíveis por meio desses registos de desempenho. Para ativar o registo, utilize os seguintes passos:

1. Anote o ID de recurso da conta de armazenamento, onde os dados de registo são armazenados. Este valor é o formato: /subscriptions/\<subscriptionId\>/resourceGroups/\<nome do grupo de recursos\>/providers/Microsoft.Storage/storageAccounts/\<denomedecontadearmazenamento\>. Pode utilizar qualquer conta de armazenamento na sua subscrição. Pode utilizar o portal do Azure para encontrar estas informações.

    ![Portal: ID de recurso conta de armazenamento](./media/application-gateway-diagnostics/diagnostics1.png)

2. Tenha em atenção o ID de recurso do gateway de aplicação para o qual o registo está ativado. Este valor é o formato: /subscriptions/\<subscriptionId\>/resourceGroups/\<nome do grupo de recursos\>/providers/Microsoft.Network/applicationGateways/\<nome do gateway de aplicação \>. Pode utilizar o portal para encontrar estas informações.

    ![Portal: ID de recurso de gateway de aplicação](./media/application-gateway-diagnostics/diagnostics2.png)

3. Ative o registo de diagnósticos com o seguinte cmdlet do PowerShell:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>Registos de Atividades não necessitam de uma conta de armazenamento separada. A utilização do armazenamento para registo do acesso e do desempenho incorre em encargos de serviços.

### <a name="enable-logging-through-the-azure-portal"></a>Ativar o registo através do portal do Azure

1. No portal do Azure, encontre o seu recurso e clique em **registos de diagnóstico**.

   Para o Gateway de aplicação, três logs estão disponíveis:

   * Registo de acesso
   * Registo de desempenho
   * Log do firewall

2. Para iniciar a recolha de dados, clique em **Ativar os diagnósticos**.

   ![Ativar diagnósticos][1]

3. O **as definições de diagnóstico** painel fornece as definições para os registos de diagnóstico. Neste exemplo, o Log Analytics armazena os registos. Clique em **configurar** sob **do Log Analytics** para configurar a sua área de trabalho. Também pode utilizar os hubs de eventos e uma conta de armazenamento para guardar os registos de diagnóstico.

   ![Iniciar o processo de configuração][2]

4. Escolha uma área de trabalho do Log Analytics existente ou crie um novo. Este exemplo utiliza um já existente.

   ![Opções para áreas de trabalho do Log Analytics][3]

5. Confirme as definições e clique em **guardar**.

   ![Painel de definições de diagnóstico com seleções][4]

### <a name="activity-log"></a>Registo de atividades

Azure gera o registo de atividades, por predefinição. Os registos são mantidos durante 90 dias no armazenamento de registos de eventos do Azure. Saiba mais sobre estes registos, lendo o [ver o registo de atividades e eventos](../monitoring-and-diagnostics/insights-debugging-with-events.md) artigo.

### <a name="access-log"></a>Registo de acesso

O registo de acesso é gerado apenas se tiver habilitado em cada instância de Gateway de aplicação, conforme especificado nos passos anteriores. Os dados são armazenados na conta de armazenamento que especificou quando ativou o registo. Cada acesso de Gateway de aplicação é registado no formato JSON, conforme mostrado no exemplo a seguir:


|Value  |Descrição  |
|---------|---------|
|instanceId     | Instância de Gateway de aplicação que serviu o pedido.        |
|clientIP     | IP de origem para o pedido.        |
|clientPort     | Porta de origem para o pedido.       |
|httpMethod     | Método HTTP usado pelo pedido.       |
|requestUri     | URI do pedido recebido.        |
|RequestQuery     | **Encaminhado por servidor**: Instância de conjunto de back-end que foi enviada o pedido.</br>**X-AzureApplicationGateway-LOG-ID**: ID de correlação utilizado para o pedido. Ele pode ser usado para resolver problemas de tráfego nos servidores de back-end. </br>**SERVER-STATUS**: Código de resposta HTTP que o Gateway de aplicação recebido do back-end.       |
|UserAgent     | Agente de utilizador do cabeçalho de pedido HTTP.        |
|httpStatus     | Código de estado HTTP devolvido para o cliente do Gateway de aplicação.       |
|httpVersion     | Versão HTTP do pedido.        |
|receivedBytes     | Tamanho do pacote recebido, em bytes.        |
|sentBytes| Tamanho do pacote enviado, em bytes.|
|timeTaken| Período de tempo (em milissegundos) que leva um pedido para serem processados e a sua resposta seja enviado. Isso é calculado como o intervalo de tempo quando o Gateway de aplicação recebe o primeiro byte de um pedido HTTP para o tempo quando a resposta enviar a conclusão da operação. É importante observar que o campo Time-Taken normalmente inclui o tempo que os pacotes de solicitação e resposta são em trânsito através da rede. |
|sslEnabled| Se a comunicação com os conjuntos de back-end utilizados SSL. Valores válidos são e desativar.|
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
        "sslEnabled": "off"
    }
}
```

### <a name="performance-log"></a>Registo de desempenho

O registo de desempenho é gerado apenas se está ativado em cada instância de Gateway de aplicação, conforme especificado nos passos anteriores. Os dados são armazenados na conta de armazenamento que especificou quando ativou o registo. Os dados de registo de desempenho são gerados em intervalos de 1 minuto. Os seguintes dados são registados:


|Value  |Descrição  |
|---------|---------|
|instanceId     |  Instância de Gateway de aplicação para o desempenho de dados está a ser gerados. Para um gateway de aplicação de várias instâncias, existe uma linha por instância.        |
|healthyHostCount     | Número de anfitriões em bom estado no conjunto de back-end.        |
|unHealthyHostCount     | Número de anfitriões de mau estado de funcionamento no conjunto de back-end.        |
|requestCount     | Número de pedidos servidos.        |
|latência | Latência média (em milissegundos) de pedidos da instância para o back-end que serve os pedidos. |
|failedRequestCount| Número de pedidos falhados.|
|Taxa de transferência| Débito médio desde o último log, medido em bytes por segundo.|

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
> Latência é calculada a partir da hora de quando o primeiro byte da solicitação HTTP é recebido para a hora do último byte de resposta HTTP é enviado. É a soma do tempo de processamento de Gateway de aplicação mais o custo de rede para o back-end, além do tempo que demora de back-end para processar o pedido.

### <a name="firewall-log"></a>Log do firewall

O log do firewall é gerado apenas se está ativado para cada gateway de aplicação, conforme especificado nos passos anteriores. Este registo também requer que a firewall de aplicações web está configurada num gateway de aplicação. Os dados são armazenados na conta de armazenamento que especificou quando ativou o registo. Os seguintes dados são registados:


|Value  |Descrição  |
|---------|---------|
|instanceId     | Instância de Gateway de aplicação para o qual firewall dados está a ser gerados. Para um gateway de aplicação de várias instâncias, existe uma linha por instância.         |
|clientIp     |   IP de origem para o pedido.      |
|clientPort     |  Porta de origem para o pedido.       |
|requestUri     | URL do pedido recebido.       |
|ruleSetType     | Regra de definir o tipo. O valor disponível é OWASP.        |
|ruleSetVersion     | Regra de definir a versão utilizada. Valores disponíveis são 2.2.9 e 3.0.     |
|ruleId     | ID de regra do evento acionadora.        |
|message     | Obter mensagem amigável para o evento acionadora. São fornecidos mais detalhes na secção de detalhes.        |
|action     |  Ação executada na solicitação. Valores disponíveis são bloqueado e permitidos.      |
|site     | Site para o qual o registo foi gerado. Atualmente, apenas Global está listado porque as regras são globais.|
|detalhes     | Detalhes do evento acionadora.        |
|details.message     | Descrição da regra.        |
|details.data     | Dados específicos encontrados no pedido que correspondem a regra.         |
|details.file     | Ficheiro de configuração que continha a regra.        |
|details.line     | Número de linha no ficheiro de configuração que disparou o evento.       |

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
  }
} 

```

### <a name="view-and-analyze-the-activity-log"></a>Ver e analisar o registo de atividades

Pode ver e analisar os dados de registo de atividades através de um dos seguintes métodos:

* **Ferramentas do Azure**: Obter informações de registo de Atividades através do Azure PowerShell, a CLI do Azure, a API de REST do Azure ou o portal do Azure. As instruções passo-a-passo para cada método estão detalhadas no artigo [Operações de atividades com o Resource Manager](../azure-resource-manager/resource-group-audit.md).
* **Power BI**: Se ainda não tiver uma [Power BI](https://powerbi.microsoft.com/pricing) conta, pode experimentar gratuitamente. Ao utilizar o [pacote de conteúdos de Registos de Atividades do Azure para o Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), pode analisar os seus dados com dashboards pré-configurados que podem ser utilizados tal como estão ou personalizados.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Ver e analisar o acesso, desempenho e registos de firewall

[Registos de Monitor do Azure](../azure-monitor/insights/azure-networking-analytics.md) pode recolher os ficheiros de registo de eventos e contadores de sua conta de armazenamento de Blobs. Inclui visualizações e capacidades de pesquisa poderosas para analisar os seus registos.

Também pode ligar à sua conta de armazenamento e obter as entradas de registo JSON para os registos de acesso e desempenho. Depois de transferir os ficheiros JSON, pode convertê-los em CSV e visualizá-los no Excel, Power BI ou qualquer outra ferramenta de visualização de dados.

> [!TIP]
> Se estiver familiarizado com os conceitos básicos do Visual Studio para alterar os valores de constantes e variáveis em C#, pode utilizar as [ferramentas de conversor de registo](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no GitHub.
> 
> 

#### <a name="analyzing-access-logs-through-goaccess"></a>Analisar registos de acesso através de GoAccess

Publicamos um modelo do Resource Manager que instala e executa o popular [GoAccess](https://goaccess.io/) log analyzer para aceder aos registos do Gateway de aplicação. GoAccess fornece valiosas estatísticas de tráfego HTTP, como visitantes exclusivos, arquivos de pedido, anfitriões, sistemas operacionais, navegadores, códigos de estado de HTTP e muito mais. Para obter mais detalhes, consulte a [ficheiro Leia-me na pasta de modelo do Resource Manager no GitHub](https://aka.ms/appgwgoaccessreadme).

## <a name="metrics"></a>Métricas

As métricas são uma funcionalidade para determinados recursos do Azure, onde pode ver contadores de desempenho no portal. Para o Gateway de aplicação, as métricas seguintes estão disponíveis:

- **Ligações atuais**
- **Pedidos falhados**
- **Contagem de anfitriões em bom estado**

   Pode filtrar um por base de conjunto de back-end para mostrar os anfitriões em bom estado/mau estado de funcionamento de um conjunto de back-end específicas.


- **Estado da resposta**

   A distribuição de código de estado de resposta pode ser categorizada ainda mais para mostrar as respostas na 2xx, 3xx, 4xx e 5xx categorias.

- **Débito**
- **Total de pedidos**
- **Contagem de anfitriões de mau estado de funcionamento**

   Pode filtrar um por base de conjunto de back-end para mostrar os anfitriões em bom estado/mau estado de funcionamento de um conjunto de back-end específicas.

Navegue para um gateway de aplicação, em **monitorização** clique em **métricas**. Para ver os valores disponíveis, selecione a lista pendente **MÉTRICA**.

Na imagem seguinte, verá um exemplo com três métricas apresentadas nos últimos 30 minutos:

[![](media/application-gateway-diagnostics/figure5.png "Vista de métrica")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Para ver uma lista atual de métricas, veja [suportado métricas com o Azure Monitor](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules"></a>Regras de alerta

Pode começar com base em métricas de um recurso de regras de alerta. Por exemplo, um alerta pode chamar um webhook ou um administrador de e-mail, se o débito do gateway de aplicação é acima, abaixo ou com um limiar durante um período especificado.

O exemplo seguinte explica como criar uma regra de alerta que envia um e-mail para um administrador após falhas de débito um limiar:

1. Clique em **Adicionar alerta de métrica** para abrir o **Adicionar regra** painel. Também pode aceder este painel a partir do painel de métricas.

   ![Botão "Adicionar alerta de métrica"][6]

2. Sobre o **Adicionar regra** painel, preencha o nome, a condição, notificar secções e clique em **OK**.

   * Na **condição** Seletor, selecione um dos quatro valores: **Superior a**, **maior que ou igual**, **inferior**, ou **inferior ou igual a**.

   * Na **período** Seletor, selecione um período de cinco minutos a seis horas.

   * Se selecionou **proprietários, contribuidores e leitores do E-Mail**, a mensagem de e-mail pode ser dinâmica com base em que os utilizadores que têm acesso a esse recurso. Caso contrário, pode fornecer uma lista separada por vírgulas de utilizadores no **adicionais do administrador email(s)** caixa.

   ![Adicionar o painel de regra][7]

Se o limiar é quebrado, chega uma mensagem de e-mail que é semelhante ao mostrado na imagem seguinte:

![E-mail para o limiar infringido][8]

É apresentada uma lista de alertas, depois de criar um alerta de métrica. Ele fornece uma descrição geral de todas as regras de alerta.

![Lista de regras e alertas][9]

Para obter mais informações sobre notificações de alerta, consulte [receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Para compreender melhor os webhooks e como pode usá-los com alertas, visite [configurar um webhook num alerta de métrica do Azure](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>Passos Seguintes

* Visualizar o contador e registos de eventos utilizando [registos do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).
* [Visualizar o registo de atividades do Azure com o Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) postagem de blog.
* [Ver e analisar registos de atividades do Azure no Power BI e muito mais](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) postagem de blog.

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
