---
title: Monitorização, métricas e troncos brutos para a Azure CDN
description: Este artigo descreve como configurar e usar a monitorização, métricas e troncos brutos do Azure CDN.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/23/2020
ms.author: yuajia
ms.openlocfilehash: 8a4b48586c564ee5d14a0768156b0477e2935ccf
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575444"
---
# <a name="real-time-monitoring-metrics-and-access-logs-for-azure-cdn"></a>Monitorização, métricas e registos de acesso em tempo real para Azure CDN
Com o Azure CDN da Microsoft, pode monitorizar os recursos das seguintes formas para ajudá-lo a resolver problemas, rastrear e depurar problemas. 

* Os registos brutos fornecem informações ricas sobre todos os pedidos que a CDN recebe. Os registos brutos diferem dos registos de atividade. Os registos de atividade dão visibilidade às operações es feitas nos recursos da Azure.
* Métricas, que exibem quatro métricas-chave no CDN, incluindo Byte Hit Ratio, Request Count, Response Size e Total Latency. Também fornece diferentes dimensões para decompor as métricas.
* Alerta, que permite ao cliente configurar alerta para métricas chave
* Métricas adicionais, que permitem aos clientes utilizar o Azure Log Analytics para permitir métricas adicionais de valor. Também fornecemos amostras de consulta para algumas outras métricas no Azure Log Analytics.

> [!IMPORTANT]
> A funcionalidade de registos em bruto HTTP está disponível para Azure CDN da Microsoft.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="configuration---azure-portal"></a>Configuração - Portal Azure

Para configurar registos Raw para o seu Azure CDN a partir do perfil da Microsoft: 

1. A partir do menu do portal Azure, selecione **Todos os**  >>  **\<your-CDN-profile>** Recursos.

2. Em **Monitorização**, selecione **Definições de diagnóstico**.

3. **Selecione + Adicione a definição de diagnóstico**.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-01.png" alt-text="Adicione a definição de diagnóstico para o perfil de CDN." border="true":::
    
    > [!IMPORTANT]
    > Os registos brutos só estão disponíveis no nível de perfil, enquanto os registos de código de estado http agregados estão disponíveis no nível do ponto final.

4. Nas **definições de Diagnóstico,** introduza um nome para a definição de diagnóstico no **nome de definições de Diagnóstico**.

5. Selecione o **AzureCdnAccessLog** e deslocá-lo em dias.

6. Selecione os **detalhes do Destino.** As opções de destino são:
    * **Enviar para o Log Analytics**
        * Selecione o espaço de trabalho **subscrição** e **Log Analytics**.
    * **Arquivar para uma conta de armazenamento**
        * Selecione a **Subscrição** e a **Conta de Armazenamento.**
    * **Transmitir em fluxo para um hub de eventos**
        * Selecione o espaço de nome do centro de **eventos,** **nome do hub do evento (opcional)** e nome de política do centro de **eventos**. 

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-02.png" alt-text="Configure o destino para as definições de registo." border="true":::

7. Selecione **Guardar**.

## <a name="configuration---azure-powershell"></a>Configuração - Azure PowerShell

Utilize [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) para configurar a definição de diagnóstico para troncos brutos.

Os dados de retenção são definidos pela opção **-RetentionInDays** no comando.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>Ativar registos de diagnóstico numa conta de armazenamento

1. Inscreva-se no Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. Para ativar os Registos de Diagnóstico numa conta de armazenamento, insira estes comandos. Substitua as variáveis pelos seus valores:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Ativar registos de diagnóstico para log analytics espaço de trabalho

1. Inscreva-se no Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Para ativar registos de diagnóstico para um espaço de trabalho log analytics, insira estes comandos. Substitua as variáveis pelos seus valores:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>Ativar registos de diagnóstico para o espaço de nome do centro de eventos

1. Inscreva-se no Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Para ativar registos de diagnóstico para um espaço de nomes de centro de eventos, insira estes comandos. Substitua as variáveis pelos seus valores:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $evthubnamespace = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $eventhub = Get-AzEventHubNamespace -ResourceGroupName $rsg -Name $eventhubname

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhub.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

## <a name="raw-logs-properties"></a>Propriedades de troncos crus

A Azure CDN do Microsoft Service fornece atualmente registos Raw. Os registos brutos fornecem pedidos individuais de API com cada entrada com o seguinte esquema: 

| Propriedade  | Descrição |
| ------------- | ------------- |
| BackendHostname | Se o pedido for encaminhado para um backend, este campo representa o nome de anfitrião do backend. Este campo ficará em branco se o pedido for redirecionado ou reencaminhado para uma cache regional (quando o cache for ativado para a regra de encaminhamento). |
| CacheStatus | Para cenários de cache, este campo define o cache hit/miss no POP |
| ClientIp | O endereço IP do cliente que fez o pedido. Se houve um cabeçalho X-Forwarded-For no pedido, então o IP do Cliente é escolhido do mesmo. |
| ClientPort | A porta IP do cliente que fez o pedido. |
| HttpMethod | Método HTTP utilizado pelo pedido. |
| HttpStatusCode | O código de estado HTTP devolvido do representante. |
| HttpStatusDetails | Estado resultante do pedido. O significado deste valor de cadeia pode ser encontrado numa tabela de referência de Estado. |
| HttpVersion | Tipo de pedido ou ligação. |
| POP | Nome curto da borda onde o pedido aterrou. |
| PedidoBytes | O tamanho da mensagem de pedido HTTP em bytes, incluindo os cabeçalhos de pedido e o corpo de pedido. |
| RequestUri | URI do pedido recebido. |
| RespostaBytes | Bytes enviados pelo servidor backend como resposta.  |
| RoutingRuleName | O nome da regra de encaminhamento que o pedido correspondia. |
| RegrasSEngineMatchNames | Os nomes das regras que o pedido correspondia. |
| SecurityProtocol | A versão do protocolo TLS/SSL utilizada pelo pedido ou nula se não houver encriptação. |
| SentToOriginShield </br> (depreciado) * **Ver notas sobre depreciação na secção seguinte.**| Se for verdade, significa que o pedido foi respondido a partir da cache do escudo de origem em vez do pop de borda. O escudo de origem é uma cache dos pais usada para melhorar a relação de cache. |
| isReceivedFromClient | Se for verdade, significa que o pedido veio do cliente. Se for falso, o pedido é uma falha na borda (criança POP) e é respondido a partir do escudo de origem (pai POP). |
| TimeTaken | O tempo de primeiro byte de pedido para a Porta frontal para durar a byte de resposta para fora, em segundos. |
| TrackingReference | A cadeia de referência única que identifica um pedido servido pela Porta da Frente, também enviada como cabeçalho X-Azure-Ref para o cliente. Necessário para pesquisar detalhes nos registos de acesso para um pedido específico. |
| UserAgent | O tipo de navegador que o cliente usou. |
| ErrorInfo | Este campo contém o tipo específico de erro para reduzir a área de resolução de problemas. </br> Valores possíveis incluem: </br> **NoError**: Indica que não foram encontrados erros. </br> **CertificadoError**: Erro genérico do certificado SSL.</br> **CertificadoNameCheckFailed**: O nome de anfitrião no certificado SSL é inválido ou não corresponde. </br> **ClientDisconnected**: Solicitar falha por causa da ligação à rede do cliente. </br> **Não especificadoClientError**: Erro genérico do cliente. </br> **InvalidRequest**: Pedido inválido. Pode ocorrer por causa de cabeçalho, corpo e URL mal formados. </br> **DNSFailure**: Falha do DNS. </br> **DNSNameNotResolved**: O nome ou endereço do servidor não pôde ser resolvido. </br> **OriginConnectionAborted**: A ligação com a origem foi interrompida abruptamente. </br> **OriginConnectionError**: Erro de ligação de origem genérica. </br> **OriginConnectionRefused**: A ligação com a origem não foi capaz de estabelecer. </br> **OriginError**: Erro de origem genérica. </br> **OriginInvalidResponse**: A origem devolveu uma resposta inválida ou não reconhecida. </br> **OriginTimeout**: O prazo de validade do pedido de origem expirou. </br> **ResponseHeaderTooBig**: A origem voltou demasiado grande de um cabeçalho de resposta. </br> **Restrição:** O pedido foi bloqueado devido a um período de inquérito restrito. </br> **SSLHandshakeError**: Incapaz de estabelecer ligação com a origem devido à falha do aperto de mão SSL. </br> **Não especificadoError**: Ocorreu um erro que não se enquadrava em nenhum dos erros da tabela. |
| TimeToFirstByte | O tempo em milissegundos a partir do momento em que a Microsoft CDN recebe o pedido até ao momento em que o primeiro byte é enviado ao cliente. O tempo é medido apenas a partir do lado da Microsoft. Os dados do lado do cliente não são medidos. |
> [!NOTE]
> Os registos podem ser visualizados no perfil de Log Analytics executando uma consulta. Uma consulta de amostra pareceria:
    ```
    AzureDiagnostics | where Category == "AzureCdnAccessLog"
    ```

### <a name="sent-to-origin-shield-deprecation"></a>Enviado para a deprecação do escudo de origem
A propriedade de log cru **isSentToOriginShield** foi depreciada e substituída por um novo campo **éReceivedFromClient**. Use o novo campo se já estiver a utilizar o campo deprecado. 

Os registos brutos incluem troncos gerados tanto a partir da borda cdN (child POP) como do escudo de origem. O escudo de origem refere-se aos nós dos pais que estão estrategicamente localizados em todo o mundo. Estes nós comunicam com servidores de origem e reduzem a carga de tráfego na origem. 

Para cada pedido que vai para o escudo de origem, existem entradas de 2 registos:

* Um para os nosdes de borda
* Um para o escudo de origem. 

Para diferenciar a saída ou respostas dos nós de borda vs. escudo de origem, pode utilizar o campo **éReceivedFromClient** para obter os dados corretos. 

Se o valor for falso, significa que o pedido é respondido do escudo de origem aos nós de borda. Esta abordagem é eficaz para comparar registos brutos com dados de faturação. As cargas não são incorridos por saídas do escudo de origem para os nós de borda. As taxas são cobradas por saídas dos nós de borda para os clientes. 

**Amostra de consulta de Kusto para excluir registos gerados no escudo de origem em Log Analytics.**

```kusto
AzureDiagnostics 
| where OperationName == "Microsoft.Cdn/Profiles/AccessLog/Write" and Category == "AzureCdnAccessLog"  
| where isReceivedFromClient == true

```

> [!IMPORTANT]
> A funcionalidade de registos HTTP Raw está disponível automaticamente para quaisquer perfis criados ou atualizados após **25 de fevereiro de 2020**. Para os perfis cdN criados anteriormente, deve-se atualizar o ponto final do CDN após a configuração do registo. Por exemplo, pode-se navegar para a geo filtragem sob os pontos finais da CDN e bloquear qualquer país/região que não seja relevante para a sua carga de trabalho e salvar.


## <a name="metrics"></a>Métricas
O Azure CDN da Microsoft está integrado com o Azure Monitor e publica quatro métricas de CDN para ajudar a rastrear, resolver problemas e depurar problemas. 

As Métricas são apresentadas em gráficos e acessíveis através de PowerShell, CLI e API. As métricas cdn são gratuitas.

O Azure CDN da Microsoft mede e envia as suas métricas em intervalos de 60 segundos. As métricas podem levar até 3 minutos para aparecer no portal. 

Para obter mais informações, consulte as [métricas do Monitor Azure.](../azure-monitor/essentials/data-platform-metrics.md)

**Métricas suportadas pela Azure CDN da Microsoft**

| Métricas  | Description | Dimensões |
| ------------- | ------------- | ------------- |
| Rácio bytes Hit* | A percentagem de saídas da cache CDN, calculada contra a saída total. | Ponto final |
| PedidoCount | O número de pedidos de clientes servidos pela CDN. | Ponto final </br> País cliente. </br> Região de clientes. </br> Estado de HTTP. </br> Grupo de status HTTP. |
| Tamanho das respostas | O número de bytes enviados como respostas da borda da CDN aos clientes. |Ponto final </br> País cliente. </br> Região de clientes. </br> Estado de HTTP. </br> Grupo de status HTTP. |
| TotalLatency | O tempo total do pedido de cliente recebido pela CDN **até à última resposta byte enviar da CDN para o cliente**. |Ponto final </br> País cliente. </br> Região de clientes. </br> Estado de HTTP. </br> Grupo de status HTTP. |

***Bytes Hit Ration = (saída da borda - saída da origem)/saída da borda**

Cenários excluídos no cálculo do rácio de impacto dos bytes:

* Você explicitamente configurar nenhuma cache, quer através do Regimento de Regras, quer do comportamento de caching de cordas de consulta.
* Configura explicitamente a diretiva relativa ao controlo de caches com cache sem loja ou cache privado.

### <a name="metrics-configuration"></a>Configuração de métricas

1. A partir do menu do portal Azure, selecione **Todos os**  >>  **\<your-CDN-profile>** Recursos.

2. Em **Monitorização,** selecione **Métricas:**

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-03.png" alt-text="Métricas para o perfil de CDN." border="true":::

3. Selecione **Adicionar métrica,** selecione a métrica para adicionar:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-04.png" alt-text="Adicione e selecione a métrica para o perfil cdN." border="true":::

4. **Selecione Adicionar filtro** para adicionar um filtro:
    
    :::image type="content" source="./media/cdn-raw-logs/raw-logs-05.png" alt-text="Aplique o filtro na métrica." border="true":::

5. Selecione **Aplicar** divisão para ver tendência por diferentes dimensões:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-06.png" alt-text="Aplicar divisão na métrica." border="true":::

6. Selecione **novo gráfico** para adicionar um novo gráfico:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-07.png" alt-text="Adicione um novo gráfico à vista métrica." border="true":::

### <a name="alerts"></a>Alertas

Pode configurar alertas no Microsoft CDN selecionando Alertas **de Monitorização**  >>  .

Selecione **nova regra de alerta** para as métricas listadas na secção Métricas:

:::image type="content" source="./media/cdn-raw-logs/raw-logs-08.png" alt-text="Configure os alertas para o ponto final do CDN." border="true":::

O alerta será carregado com base no Monitor Azure. Para obter mais informações sobre alertas, consulte [os alertas do Azure Monitor](../azure-monitor/alerts/alerts-overview.md).

### <a name="additional-metrics"></a>Métricas Adicionais
Pode ativar métricas adicionais usando Azure Log Analytics e troncos crus por um custo adicional.

1. Siga os passos acima para permitir que os diagnósticos enviem registos brutos para registar análises.

2. Selecione o espaço de trabalho Log Analytics que criou:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-09.png" alt-text="Selecione log analytics espaço de trabalho" border="true":::   

3. Selecione **Registos** em **Geral** no espaço de trabalho de análise de registo.  Em seguida, **selecione Começar:**

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-10.png" alt-text="Log analytics recursos espaço de trabalho." border="true":::   
 
4. Selecione **perfis CDN**.  Selecione uma consulta de exemplo para RUN ou feche o ecrã de exemplo para introduzir uma consulta personalizada:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-11.png" alt-text="Tela de consulta de exemplo." border="true":::   

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-12.png" alt-text="Execução de consultas." border="true":::   

4. Para ver dados por gráfico, selecione **Gráfico**.  Selecione **Pin para painel de instrumentos** para fixar a tabela no painel Azure:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-13.png" alt-text="Gráfico pin para painel." border="true"::: 

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, ativou registos em bruto HTTP para o serviço Microsoft CDN.

Para obter mais informações sobre a Azure CDN e os outros serviços Azure mencionados neste artigo, consulte:

* [Analisar](cdn-log-analysis.md) Padrões de utilização de Azure CDN.

* Saiba mais sobre [o Azure Monitor.](../azure-monitor/overview.md)

* Configurar [o Log Analytics no Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md).