---
title: Criar monitor de conexão - PowerShell
titleSuffix: Azure Network Watcher
description: Saiba como criar o Monitor de Ligação utilizando o PowerShell.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: fa8b2d967a336343d23c5f6aa4477ebcf2396407
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949042"
---
# <a name="create-a-connection-monitor-using-powershell"></a>Criar um monitor de ligação utilizando o PowerShell

Saiba como criar o Connection Monitor para monitorizar a comunicação entre os seus recursos utilizando o PowerShell.

## <a name="before-you-begin"></a>Before you begin 

Nos monitores de ligação que cria no Connection Monitor, pode adicionar tanto as máquinas no local como os VMs Azure como fontes. Estes monitores de ligação também podem monitorizar a conectividade com os pontos finais. Os pontos finais podem estar no Azure ou em qualquer outro URL ou IP.

O Monitor de Ligação inclui as seguintes entidades:

* **Recurso de monitor** de ligação - Um recurso Azure específico da região. Todas as seguintes entidades são propriedades de um recurso de monitor de ligação.
* **Ponto final** – Uma fonte ou destino que participa em verificações de conectividade. Exemplos de pontos finais incluem VMs Azure, agentes no local, URLs e IPs.
* **Configuração de teste** - Uma configuração específica do protocolo para um teste. Com base no protocolo que escolheu, pode definir a porta, limiares, frequência de teste e outros parâmetros.
* **Grupo de teste** – O grupo que contém pontos finais de origem, pontos finais de destino e configurações de teste. Um monitor de ligação pode conter mais de um grupo de teste.
* **Teste** – A combinação de um ponto final de origem, ponto final de destino e configuração de teste. Um teste é o nível mais granular em que os dados de monitorização estão disponíveis. Os dados de monitorização incluem a percentagem de verificações que falharam e o tempo de ida e volta (RTT).

    ![Diagrama mostrando um monitor de ligação, definindo a relação entre grupos de teste e testes](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-powershell"></a>Passos para criar com o PowerShell

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

* conexãoMonitorName - Nome do recurso do monitor de ligação

* SUB - ID de assinatura da subscrição onde pretende criar monitor de ligação

* NW - ID de recursos do Observador de Rede no qual CM será criado 

* localização - Região em que o monitor de ligação será criado

* Pontos Finais
    * nome – Nome único para cada ponto final
    * resourceId – Para pontos finais do Azure, o ID de recursos refere-se ao ID de recursos do Azure Resource Manager para máquinas virtuais. No caso de pontos finais não-Azure, o ID de recursos refere-se ao ID de recursos do gestor de recursos Azure para o espaço de trabalho Log Analytics ligado a agentes não-Azure.
    * endereço – Aplicável apenas quando o ID de recurso não for especificado ou se o ID de recurso for espaço de trabalho Log Analytics. Se usado com o ID do recurso Log Analytics, este refere-se ao FQDN do agente que pode ser usado para monitorização. Se for utilizado sem identificação de recursos, este pode ser o URL ou IP de qualquer ponto final público.
    * filtro – Para pontos finais não-Azure, utilize o filtro para selecionar agentes do espaço de trabalho Log Analytics que serão utilizados para monitorização no recurso monitor de ligação. Se os filtros não estiverem definidos, todos os agentes pertencentes ao espaço de trabalho Log Analytics podem ser utilizados para monitorização
        * tipo – Definir tipo como "Endereço de Agente"
        * endereço – Definir o endereço como FQDN do seu agente no local

* Grupos de teste
    * nome - Nomeie o seu grupo de teste.
    * configurações de teste - Configurações de teste com base nas quais os pontos finais de origem se ligam aos pontos finais do destino
    * fontes - Escolha entre pontos finais criados acima. Os pontos finais de origem baseados em Azure precisam de ter a extensão do Azure Network Watcher instalada e os pontos finais não baseados emAzure precisam de ter o agente Doazure Log Analytics instalado. Para instalar um agente para a sua fonte, consulte [os agentes de monitorização da Instalação](./connection-monitor-overview.md#install-monitoring-agents).
    * destinos - Escolha entre pontos finais criados acima. Pode monitorizar a conectividade com os VMs Azure ou qualquer ponto final (um IP público, URL ou FQDN) especificando-os como destinos. Num único grupo de teste, pode adicionar VMs Azure, URLs Office 365, Dynamics 365 URLs e pontos finais personalizados.
    * desativação - Utilize este campo para desativar a monitorização de todas as fontes e destinos que o grupo de ensaio especifica.

* Configurações de teste
    * nome - Nome da configuração do teste.
    * testFrequencySec - Especifique com que frequência as fontes irão pingar destinos no protocolo e na porta que especificou. Pode escolher 30 segundos, 1 minuto, 5 minutos, 15 minutos ou 30 minutos. As fontes testarão a conectividade com destinos com base no valor que escolher. Por exemplo, se selecionar 30 segundos, as fontes verificarão a conectividade com o destino pelo menos uma vez num período de 30 segundos.
    * protocolo - Pode escolher TCP, ICMP, HTTP ou HTTPS. Dependendo do protocolo, pode fazer algumas configurações específicas do protocolo
        * prefereHTTPS - Especificar se deve utilizar HTTPS em HTTPS
        * porta - Especifique o porto de destino à sua escolha.
        * desativarTraceRoute - Isto aplica-se a grupos de teste cujo protocolo é TCP ou ICMP. Impede que as fontes descubram a topologia e o hop-by-hop RTT.
    * sucessoThreshold - Pode definir limiares nos seguintes parâmetros de rede:
        * checksFailedPercent - Desefie a percentagem de verificações que podem falhar quando as fontes verificam a conectividade com os destinos utilizando os critérios especificados. No que diz o protocolo TCP ou ICMP, a percentagem de controlos falhados pode ser equiparada à percentagem de perda de pacotes. Para o protocolo HTTP, este campo representa a percentagem de pedidos HTTP que não receberam resposta.
        * roundTripTimeMs - Deslote o RTT em milissegundos para saber quanto tempo as fontes podem demorar a ligar-se ao destino sobre a configuração do teste.

## <a name="scale-limits"></a>Limites de escala

Os monitores de ligação têm os seguintes limites de escala:

* Monitores de ligação máximo por subscrição por região: 100
* Grupos de teste máximos por monitor de ligação: 20
* Fontes e destinos máximos por monitor de ligação: 100
* Configurações máximas de teste por monitor de ligação: 20

## <a name="next-steps"></a>Passos seguintes

* Saiba [como analisar dados de monitorização e definir alertas](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts)
* Saiba [como diagnosticar problemas na sua rede](./connection-monitor-overview.md#diagnose-issues-in-your-network)