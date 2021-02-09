---
title: Criar Monitor de Conexão - Modelo ARM
titleSuffix: Azure Network Watcher
description: Saiba como criar o Monitor de Ligação utilizando o ARMClient.
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
ms.openlocfilehash: 46bdaf932d4224bf97b46e7713d49d815ca1bcdd
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833002"
---
# <a name="create-a-connection-monitor-using-the-arm-template"></a>Criar um monitor de ligação utilizando o modelo ARM

> [!IMPORTANT]
> A partir de 1 de julho de 2021, não poderá adicionar novos testes num espaço de trabalho existente ou permitir um novo espaço de trabalho no Network Performance Monitor. Também não poderá adicionar novos monitores de ligação no Connection Monitor (clássico). Pode continuar a utilizar os testes e monitores de ligação criados antes de 1 de julho de 2021. Para minimizar a perturbação do serviço nas suas cargas de trabalho atuais, [migrar os seus testes do Monitor de Desempenho da Rede ](migrate-to-connection-monitor-from-network-performance-monitor.md) ou  [migrar do Connection Monitor (clássico)](migrate-to-connection-monitor-from-connection-monitor-classic.md) para o novo Monitor de Ligação no Observador de Rede Azure antes de 29 de fevereiro de 2024.

Saiba como criar o Connection Monitor para monitorizar a comunicação entre os seus recursos utilizando o ARMClient. Suporta implantações de nuvem híbrida e Azure.


## <a name="before-you-begin"></a>Antes de começar 

Nos monitores de ligação que cria no Connection Monitor, pode adicionar tanto as máquinas no local como os VMs Azure como fontes. Estes monitores de ligação também podem monitorizar a conectividade com os pontos finais. Os pontos finais podem estar no Azure ou em qualquer outro URL ou IP.

O Monitor de Ligação inclui as seguintes entidades:

* **Recurso de monitor** de ligação - Um recurso Azure específico da região. Todas as seguintes entidades são propriedades de um recurso de monitor de ligação.
* **Ponto final** – Uma fonte ou destino que participa em verificações de conectividade. Exemplos de pontos finais incluem VMs Azure, agentes no local, URLs e IPs.
* **Configuração de teste** - Uma configuração específica do protocolo para um teste. Com base no protocolo que escolheu, pode definir a porta, limiares, frequência de teste e outros parâmetros.
* **Grupo de teste** – O grupo que contém pontos finais de origem, pontos finais de destino e configurações de teste. Um monitor de ligação pode conter mais de um grupo de teste.
* **Teste** – A combinação de um ponto final de origem, ponto final de destino e configuração de teste. Um teste é o nível mais granular em que os dados de monitorização estão disponíveis. Os dados de monitorização incluem a percentagem de verificações que falharam e o tempo de ida e volta (RTT).

    ![Diagrama mostrando um monitor de ligação, definindo a relação entre grupos de teste e testes](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-sample-arm-template"></a>Passos para criar com modelo de ARM de amostra

Utilize o seguinte código para criar um monitor de ligação utilizando o ARMClient.

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "https://management.azure.com"

$SUB = "subscriptions/<subscription id 1>;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_<region>"

$body =

"{

location: '<region>',

properties: {

endpoints: [{

name: 'endpoint_workspace_machine',

type: 'MMAWorkspaceMachine',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

//Example 1: Choose a machine

address : '<non-Azure machine FQDN>'
}

//Example 2: Select IP from chosen machines

address : '<non-Azure machine FQDN>

"scope": {
      "include": [
            {
                  "address": "<IP belonging to machine chosen above>"  
        }
       ]
      }
   }    
   
name: 'endpoint_workspace_network',

type: 'MMAWorkspaceNetwork',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

 coverage level : 'high', //Optional
 
 //Include subnets. You can also exclude IPs from subnet to exclude from monitoring
 
 scope: {
      "include": [
            {
                  "address": "<subnet 1 mask>" // Eg: 10.10.1.0/28
            },
            {
                  "address": "<subnet 2 mask>" 
            }
      ],
      "exclude": [
            { 
            "address" : "<ip-from-included-subnets-that-should-be-excluded>"
        }
      ]
     }
},

//Use a Azure VM as an endpoint
{

name: 'endpoint_virtualmachine',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

//Use an Azure VNET or Subnet as an endpoint

 {

name: 'endpoint_vnet_subnet',

resourceId: '<resource id of VNET or subnet'
coverage level: 'high' //Optional

//Scope is optional.

  "scope": {
      "include": [
            {
                  "address": "<subnet 1 mask>" // Eg: 10.10.1.0/28 .This subnet should match with any existing subnet in vnet
            }
      ],
    "exclude": [
            {
                  "address": "<ip-from-included-subnets-that-should-be-excluded>" // If used with include, IP should be part of the subnet defined above. Without include, this could be any address within vnet range or any specific subnet range as a whole.
            }
      ]
  }
   },

//Endpoint as a URL
{

name: 'azure portal'

address: '<URL>'

   },

//Endpoint as an IP 
 {

    name: 'ip',

     address: '<IP>'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

   // Choose your protocol
   
    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: <threshold for checks failed %>,

     roundTripTimeMs: <threshold for RTT>

    }

   }, {

    name: 'https',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    httpConfiguration: {
    
     port: '<port of choice>'
  
    preferHTTPS: true // If port chosen is not 80 or 443
    
    method: 'GET', //Choose GET or POST
    
    path: '/', //Specify path for request
         
    requestHeaders: [
            {
              "name": "Content-Type",
              "value": "appication/json"
            }
          ],
          
    validStatusCodeRanges: [ "102", "200-202", "3xx" ], //Samples
          
    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, 
   {

    name: 'tcpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }

  ]

 }

} "
```

Aqui está o comando de implantação:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

## <a name="description-of-properties"></a>Descrição dos Imóveis

* conexãoMonitorName - Nome do recurso do monitor de ligação

* SUB - ID de assinatura da subscrição onde pretende criar monitor de ligação

* NW - ID de recursos do Observador de Rede no qual CM será criado 

* localização - Região em que o monitor de ligação será criado

* Pontos Finais
    * nome – Nome único para cada ponto final
    * resourceId – Para pontos finais do Azure, o ID de recursos refere-se ao ID de recursos do Azure Resource Manager para máquinas virtuais. No caso de pontos finais não-Azure, o ID de recursos refere-se ao ID de recursos do Azure Resource Manager para o espaço de trabalho Log Analytics ligado a agentes não-Azure.
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
    
        * prefereHTTPS - Especificar se deve utilizar HTTPS em HTTP, quando a porta utilizada não é 80 nem 443
        * porta - Especifique o porto de destino à sua escolha.
        * desativarTraceRoute - Isto aplica-se a configurações de teste cujo protocolo é TCP ou ICMP. Impede que as fontes descubram a topologia e o hop-by-hop RTT.
        * método - Isto aplicado às configurações de teste cujo protocolo é HTTP. Selecione o método de pedido HTTP-OU GET ou POST
        * caminho - Especifique os parâmetros do caminho para anexar ao URL
        * validEsscos - Escolha os códigos de estado aplicáveis. Se o código de resposta não corresponder a esta lista, receberá uma mensagem de diagnóstico
        * solicitadoresHeaders - Especifique as cordas do cabeçalho de pedido personalizado que farão passar para o destino
        
    * sucessoThreshold - Pode definir limiares nos seguintes parâmetros de rede:
        * checksFailedPercent - Desefie a percentagem de verificações que podem falhar quando as fontes verificam a conectividade com os destinos utilizando os critérios especificados. No que diz o protocolo TCP ou ICMP, a percentagem de controlos falhados pode ser equiparada à percentagem de perda de pacotes. Para o protocolo HTTP, este campo representa a percentagem de pedidos HTTP que não receberam resposta.
        * roundTripTimeMs - Deslote o RTT em milissegundos para saber quanto tempo as fontes podem demorar a ligar-se ao destino sobre a configuração do teste.

## <a name="scale-limits"></a>Limites de escala

Os monitores de ligação têm os seguintes limites de escala:

* Monitores de ligação máximo por subscrição por região: 100
* Grupos de teste máximos por monitor de ligação: 20
* Fontes e destinos máximos por monitor de ligação: 100
* Configurações máximas de teste por monitor de ligação: 20 via ARMClient

## <a name="next-steps"></a>Passos seguintes

* Saiba [como analisar dados de monitorização e definir alertas](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts)
* Saiba [como diagnosticar problemas na sua rede](./connection-monitor-overview.md#diagnose-issues-in-your-network)
