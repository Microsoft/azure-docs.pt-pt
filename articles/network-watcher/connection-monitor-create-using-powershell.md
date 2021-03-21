---
title: Criar um monitor de ligação - PowerShell
titleSuffix: Azure Network Watcher
description: Aprenda a criar um monitor de ligação utilizando o PowerShell.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 7f175d82b650871437a506ea4513f0ae28360f68
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99833036"
---
# <a name="create-a-connection-monitor-by-using-powershell"></a>Criar um monitor de ligação utilizando o PowerShell

> [!IMPORTANT]
> A partir de 1 de julho de 2021, não poderá adicionar novos testes num espaço de trabalho existente ou permitir um novo espaço de trabalho no Network Performance Monitor. Também não poderá adicionar novos monitores de ligação no Connection Monitor (clássico). Pode continuar a utilizar os testes e monitores de ligação criados antes de 1 de julho de 2021. Para minimizar a perturbação do serviço nas suas cargas de trabalho atuais, [migrar os seus testes do Monitor de Desempenho da Rede ](migrate-to-connection-monitor-from-network-performance-monitor.md) ou  [migrar do Connection Monitor (clássico)](migrate-to-connection-monitor-from-connection-monitor-classic.md) para o novo Monitor de Ligação no Observador de Rede Azure antes de 29 de fevereiro de 2024.


Saiba como utilizar a funcionalidade De Monitor de Ligação do Observador de Redes Azure para monitorizar a comunicação entre os seus recursos.


## <a name="before-you-begin"></a>Antes de começar

Nos monitores de ligação que cria com o Connection Monitor, pode adicionar tanto as máquinas no local como as máquinas virtuais Azure (VMs) como fontes. Estes monitores de ligação também podem monitorizar a conectividade com os pontos finais. Os pontos finais podem estar no Azure ou em qualquer outro URL ou IP.

Um monitor de ligação inclui as seguintes entidades:

* **Recurso do monitor** de ligação : Um recurso Azure específico da região. Todas as seguintes entidades são propriedades do recurso do monitor de ligação.
* **Ponto final**: Uma fonte ou destino que participe em verificações de conectividade. Exemplos de pontos finais incluem VMs Azure, agentes no local, URLs e IPs.
* **Configuração do teste**: Uma configuração específica do protocolo para um teste. Com base no protocolo que escolher, pode definir a porta, limiares, frequência de teste e outros parâmetros.
* **Grupo de teste**: O grupo que contém pontos finais de origem, pontos finais de destino e configurações de teste. Um monitor de ligação pode conter mais de um grupo de teste.
* **Ensaio**: A combinação de um ponto final de origem, ponto final de destino e configuração de teste. Um teste é o nível mais granular em que os dados de monitorização estão disponíveis. Os dados de monitorização incluem a percentagem de verificações que falharam e o tempo de ida e volta (RTT).

    ![Diagrama mostrando um monitor de ligação, definindo a relação entre grupos de teste e testes.](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-a-connection-monitor"></a>Passos para criar um monitor de ligação

Utilize os seguintes comandos para criar um monitor de ligação utilizando o PowerShell.

```powershell

//Connect to your Azure account with the subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId <your-subscription>
//Select region
$nw = "NetworkWatcher_centraluseuap"
//Declare endpoints like Azure VM below. You can also give VNET,Subnet,Log Analytics workspace
$sourcevmid1 = New-AzNetworkWatcherConnectionMonitorEndpointObject -Name MyAzureVm -ResourceID /subscriptions/<your-subscription>/resourceGroups/<your resourceGroup>/providers/Microsoft.Compute/virtualMachines/<vm-name>
//Declare endpoints like URL, IPs
$bingEndpoint = New-AzNetworkWatcherConnectionMonitorEndpointObject -name Bing -Address www.bing.com # Destination URL
//Create test configuration.Choose Protocol and parametersSample configs below.

$IcmpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -IcmpProtocol
$TcpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -TcpProtocol -Port 80
$httpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -HttpProtocol -Port 443 -Method GET -RequestHeader @{Allow = "GET"} -ValidStatusCodeRange 2xx, 300-308 -PreferHTTPS
$httpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name http-tc -TestFrequencySec 60 -ProtocolConfiguration $httpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
$icmpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name icmp-tc -TestFrequencySec 30 -ProtocolConfiguration $icmpProtocolConfiguration -SuccessThresholdChecksFailedPercent 5 -SuccessThresholdRoundTripTimeMs 500
$tcpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name tcp-tc -TestFrequencySec 60 -ProtocolConfiguration $TcpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
//Create Test Group
$testGroup1 = New-AzNetworkWatcherConnectionMonitorTestGroupObject -Name testGroup1 -TestConfiguration $httpTestConfiguration, $tcpTestConfiguration, $icmpTestConfiguration -Source $sourcevmid1 -Destination $bingEndpoint,
$testname = "cmtest9"
//Create Connection Monitor
New-AzNetworkWatcherConnectionMonitor -NetworkWatcherName $nw -ResourceGroupName NetworkWatcherRG -Name $testname -TestGroup $testGroup1

```

## <a name="description-of-properties"></a>Descrição dos imóveis

* **Nome de conexãoMonitorName**: Nome do recurso do monitor de ligação.

* **SUB**: ID de assinatura da subscrição onde pretende criar um monitor de ligação.

* **NW**: ID de recurso do Observador de Rede no qual é criado um monitor de ligação.

* **Localização**: Região em que é criado um monitor de ligação.

* **Pontos Finais**
    * **Nome**: Nome único para cada ponto final.
    * **ID de recurso**: Para pontos finais Azure, o ID de recursos refere-se ao ID de recursos do Azure Resource Manager para VMs. No caso de pontos finais não-Azure, o ID de recursos refere-se ao ID de recursos do Azure Resource Manager para o espaço de trabalho Log Analytics ligado a agentes não-Azure.
    * **Endereço**: Aplicável apenas quando o ID do recurso não for especificado ou se o ID do recurso estiver no espaço de trabalho do Log Analytics. Se for utilizado sem identificação de recurso, este pode ser o URL ou IP de qualquer ponto final público. Se for utilizado com um ID de recurso Log Analytics, este refere-se ao FQDN do agente de monitorização.
    * **Filtro**: Para pontos finais não-Azure, utilize filtros para selecionar agentes de monitorização do espaço de trabalho Log Analytics no recurso do monitor de ligação. Se os filtros não estiverem definidos, todos os agentes pertencentes ao espaço de trabalho Log Analytics podem ser utilizados para monitorização.
        * **Tipo**: Definir como **Endereço de Agente**.
        * **Endereço**: Definir como FQDN do seu agente no local.

* **Grupos de teste**
    * **Nome:** Nomeie o seu grupo de teste.
    * **Fontes**: Escolha entre os pontos finais que criou anteriormente. Os pontos finais de origem baseados em Azure precisam de ter a extensão do Observador da Rede Azure instalada; Os pontos finais de origem não-Azure precisam de ter um agente Azure Log Analytics instalado. Para instalar um agente para a sua fonte, consulte [os agentes de monitorização da Instalação](./connection-monitor-overview.md#install-monitoring-agents).
    * **Destinos**: Escolha entre os pontos finais que criou anteriormente. Pode monitorizar a conectividade com os VMs Azure ou qualquer ponto final (um IP público, URL ou FQDN) especificando-os como destinos. Num único grupo de teste, pode adicionar VMs Azure, URLs Office 365, Dynamics 365 URLs e pontos finais personalizados.
    * **Desativação**: Desativar a monitorização de todas as fontes e destinos que o grupo de ensaio especifica.

* **Configurações de teste**
    * **Nome**: Nomeie a configuração do teste.
    * **TestFrequencySec**: Especifique com que frequência as fontes de destinos de ping no protocolo e na porta que especificou. Pode escolher 30 segundos, 1 minuto, 5 minutos, 15 minutos ou 30 minutos. Fontes testam conectividade com destinos com base no valor que escolhe. Por exemplo, se selecionar 30 segundos, as fontes verificam a conectividade com o destino pelo menos uma vez num período de 30 segundos.
    * **Protocolo**: Escolha TCP, ICMP, HTTP ou HTTPS. Dependendo do protocolo, também pode selecionar as seguintes configurações específicas do protocolo:
        * **prefereHTTPS**: Especifique se deve utilizar HTTPS em HTTP.
        * **porta**: Especifique o porto de destino à sua escolha.
        * **desativarTraceRoute**: Impedir que as fontes descubram a topologia e o hop-by-hop RTT. Isto aplica-se aos grupos de ensaio com TCP ou ICMP.
        * **método**: Selecione o método de pedido HTTP (GET ou POST). Isto aplica-se às configurações de teste com HTTP.
        * **caminho**: Especifique os parâmetros do caminho para anexar ao URL.
        * **validEsscos:** Escolha os códigos de estado aplicáveis. Se o código de resposta não corresponder, uma mensagem de diagnóstico aparece.
        * **solicitadoresHeaders**: Especifique as cordas do cabeçalho de pedido personalizado que passam para o destino.
    * **Limiar de sucesso**: Fixar limiares nos seguintes parâmetros de rede:
        * **checksFailedPercent**: Desafie a percentagem de verificações que podem falhar quando as fontes verificam a conectividade com os destinos utilizando os critérios especificados. No que diz o protocolo TCP ou ICMP, a percentagem de controlos falhados pode igualar a percentagem de perda de pacotes. Para o protocolo HTTP, este campo representa a percentagem de pedidos HTTP que não receberam resposta.
        * **roundTripTimeMs**: Desajuste o tempo que as fontes podem demorar a ligar-se ao destino sobre a configuração do teste em milissegundos.

## <a name="scale-limits"></a>Limites de escala

Os monitores de ligação têm os seguintes limites de escala:

* Monitores de ligação máximo por subscrição por região: 100
* Grupos de teste máximos por monitor de ligação: 20
* Fontes e destinos máximos por monitor de ligação: 100
* Configurações máximas de teste por monitor de ligação: 20

## <a name="next-steps"></a>Passos seguintes

* Saiba [como analisar os dados de monitorização e definir alertas.](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts)
* Saiba [como diagnosticar problemas na sua rede.](./connection-monitor-overview.md#diagnose-issues-in-your-network)
