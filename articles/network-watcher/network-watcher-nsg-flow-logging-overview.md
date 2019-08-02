---
title: Introdução ao log de fluxo para grupos de segurança de rede com o observador de rede do Azure | Microsoft Docs
description: Este artigo explica como usar o recurso de logs de fluxo NSG do observador de rede do Azure.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 5c156e30f4fa0270082cd1108958c3472130a460
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640831"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Introdução ao log de fluxo para grupos de segurança de rede

Os logs de fluxo do NSG (grupo de segurança de rede) são um recurso do observador de rede que permite exibir informações sobre o tráfego IP de entrada e saída por meio de um NSG. Os logs de fluxo são gravados no formato JSON e mostram os fluxos de entrada e saída por regra, a NIC (interface de rede) à qual o fluxo se aplica, informações de 5 tuplas sobre o fluxo (IP de origem/destino, porta de origem/destino e protocolo), se o tráfego tiver sido permitido ou negado, e na versão 2, informações de taxa de transferência (bytes e pacotes).


![Visão geral dos logs de fluxo](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

Embora os logs de fluxo tenham como destino NSGs, eles não são exibidos da mesma forma que os outros logs. Os logs de fluxo são armazenados somente dentro de uma conta de armazenamento e seguem o caminho de log mostrado no exemplo a seguir:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
Você pode analisar os logs de fluxo e obter informações sobre o tráfego de rede usando a [análise de tráfego](traffic-analytics.md).

As mesmas políticas de retenção vistas para outros logs se aplicam aos logs de fluxo. Você pode definir a política de retenção de log de 1 dia para 2147483647 dias. Se não definir uma política de retenção, os registos são mantidos para sempre.

> [!NOTE] 
> Usar o recurso de política de retenção com o log de fluxo do NSG pode resultar em um alto volume de operações de armazenamento e os custos associados. Se você não precisar do recurso de política de retenção, recomendamos que você defina esse valor como 0.


## <a name="log-file"></a>Ficheiro de registo

Os logs de fluxo incluem as seguintes propriedades:

* **hora** em que o evento foi registrado
* **SystemId** -ID de recurso do grupo de segurança de rede.
* **categoria** -a categoria do evento. A categoria é sempre **NetworkSecurityGroupFlowEvent**
* ResourceId-a ID de recurso do NSG
* **operationName** -sempre NetworkSecurityGroupFlowEvents
* **Propriedades** – uma coleção de propriedades do fluxo
    * **Versão** -número de versão do esquema de evento de log de fluxo
    * **fluxos** – uma coleção de fluxos. Esta propriedade tem várias entradas para regras diferentes
        * **regra** -regra para a qual os fluxos são listados
            * **fluxos** -uma coleção de fluxos
                * **Mac** -o endereço MAC da NIC para a VM em que o fluxo foi coletado
                * **flowTuples** -uma cadeia de caracteres que contém várias propriedades para a tupla de fluxo em formato separado por vírgulas
                    * **Carimbo de data/hora** -esse valor é o carimbo de data/hora de quando o fluxo ocorreu no formato de época do UNIX
                    * **IP de origem** -o IP de origem
                    * **IP de destino** -o IP de destino
                    * **Porta de origem** -a porta de origem
                    * **Porta de destino** -a porta de destino
                    * **Protocolo** -o protocolo do fluxo. Os valores válidos são **T** para TCP e **U** para UDP
                    * **Fluxo de tráfego** -a direção do fluxo de tráfego. Os valores válidos são **I** para entrada e **O** para saída.
                    * **Decisão de tráfego** -se o tráfego foi permitido ou negado. Os valores válidos são **a** para permitido e **D** para negado.
                    * **Estado do fluxo – somente versão 2** – captura o estado do fluxo. Os Estados possíveis são **B**: Comece, quando um fluxo é criado. As estatísticas não são fornecidas. **C**: Continuando um fluxo em andamento. As estatísticas são fornecidas em intervalos de 5 minutos. **E**: Terminar, quando um fluxo for encerrado. As estatísticas são fornecidas.
                    * **Pacotes-origem para destino-somente versão 2** O número total de pacotes TCP ou UDP enviados da origem para o destino desde a última atualização.
                    * **Bytes enviados-origem para destino-versão 2 somente** O número total de bytes de pacotes TCP ou UDP enviados da origem para o destino desde a última atualização. Os bytes de pacote incluem o cabeçalho e a carga do pacote.
                    * **Pacotes-destino para origem-versão 2 somente** O número total de pacotes TCP ou UDP enviados do destino para a origem desde a última atualização.
                    * **Bytes enviados-destino para origem-versão 2 somente** O número total de bytes de pacotes TCP e UDP enviados do destino para a origem desde a última atualização. Bytes de pacote incluem cabeçalho e carga de pacote.

## <a name="nsg-flow-logs-version-2"></a>Logs de fluxo NSG versão 2

A versão 2 dos logs apresenta o estado de fluxo. Você pode configurar qual versão dos logs de fluxo você recebe. Para saber como habilitar os logs de fluxo, consulte Habilitando o [log de fluxo do NSG](network-watcher-nsg-flow-logging-portal.md).

O estado de fluxo *B* é registrado quando um fluxo é iniciado. O estado de fluxo *C* e estado de fluxo *e* são Estados que marcam a continuação de um fluxo e término de fluxo, respectivamente. Ambos os Estados *C* e *e* contêm informações de largura de banda de tráfego.

**Exemplo**: As tuplas de fluxo de uma conversa TCP entre 185.170.185.105:35370 e 10.2.0.4:23:

"1493763938, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, B,,,," "1493695838, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, C, 1021, 588096, 8005, 4610880" "1493696138, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, E, 52, 29952, 47, 27072"

Para os Estados de continuação *C* e End *e* Flow, as contagens de bytes e pacotes são contagens de agregação a partir do momento do registro de tupla de fluxo anterior. Fazendo referência à conversa de exemplo anterior, o número total de pacotes transferidos é 1021 + 52 + 8005 + 47 = 9125. O número total de bytes transferidos é 588096 + 29952 + 4610880 + 27072 = 5256000.

O texto a seguir é um exemplo de um log de fluxo. Como você pode ver, há vários registros que seguem a lista de propriedades descrita na seção anterior.

## <a name="nsg-flow-logging-considerations"></a>Considerações de log de fluxo NSG

**Habilitar o log de fluxo NSG em todos os NSGs anexados a um recurso**: O log de fluxo no Azure é configurado no recurso NSG. Um fluxo só será associado a uma regra NSG. Em cenários em que vários NSGs são utilizados, é recomendável que o log de fluxo do NSG esteja habilitado em todos os NSGs aplicados à sub-rede ou interface de rede de um recurso para garantir que todo o tráfego seja registrado. Veja [como o tráfego é avaliado](../virtual-network/security-overview.md#how-traffic-is-evaluated) para obter mais informações sobre grupos de segurança de rede. 

**Custos de log de fluxo**: O log de fluxo NSG é cobrado no volume de logs produzidos. O alto volume de tráfego pode resultar em volume de log de fluxo grande e nos custos associados. O preço do log de fluxo NSG não inclui os custos subjacentes de armazenamento. Usar o recurso de política de retenção com o log de fluxo do NSG pode resultar em um alto volume de operações de armazenamento e os custos associados. Se você não precisar do recurso de política de retenção, recomendamos que você defina esse valor como 0. Consulte [preços](https://azure.microsoft.com/pricing/details/network-watcher/) do observador de rede e [preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) para obter detalhes adicionais.

**Fluxos de entrada registrados de IPS de Internet para VMs sem IPS públicos**: As VMs que não têm um endereço IP público atribuído por meio de um endereço IP público associado à NIC como um IP público em nível de instância, ou que fazem parte de um pool de back-end do balanceador de carga básico, usam [SNAT padrão](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) e têm um endereço IP atribuído pelo Azure para facilitar conectividade de saída. Como resultado, você poderá ver entradas de log de fluxo de fluxos de endereços IP da Internet, se o fluxo for destinado a uma porta no intervalo de portas atribuídas para SNAT. Embora o Azure não permita esses fluxos para a VM, a tentativa é registrada e aparece no log de fluxo do NSG do observador de rede por design. Recomendamos que o tráfego de Internet de entrada indesejado seja explicitamente bloqueado com NSG.

## <a name="sample-log-records"></a>Registros de log de exemplo

O texto a seguir é um exemplo de um log de fluxo. Como você pode ver, há vários registros que seguem a lista de propriedades descrita na seção anterior.


> [!NOTE]
> Os valores na propriedade **flowTuples* são uma lista separada por vírgulas.
 
### <a name="version-1-nsg-flow-log-format-sample"></a>Exemplo de formato de log de fluxo NSG versão 1
```json
{
    "records": [
        {
            "time": "2017-02-16T22:00:32.8950000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A",
                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A",
                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A",
                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2017-02-16T22:01:32.8960000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",
                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",
                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
    "records":
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        ,
        ...
```
### <a name="version-2-nsg-flow-log-format-sample"></a>Versão 2 exemplo de formato de log de fluxo NSG
```json
 {
    "records": [
        {
            "time": "2018-11-13T12:00:35.3899262Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                                    "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                                    "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "DefaultRule_AllowInternetOutBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                                    "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                                    "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                                    "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2018-11-13T12:01:35.3918317Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110437,125.64.94.197,10.5.16.4,59752,18264,T,I,D,B,,,,",
                                    "1542110475,80.211.72.221,10.5.16.4,37433,8088,T,I,D,B,,,,",
                                    "1542110487,46.101.199.124,10.5.16.4,60577,8088,T,I,D,B,,,,",
                                    "1542110490,176.119.4.30,10.5.16.4,57067,52801,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        ...
```

## <a name="next-steps"></a>Passos seguintes

- Para saber como habilitar os logs de fluxo, consulte Habilitando o [log de fluxo do NSG](network-watcher-nsg-flow-logging-portal.md).
- Para saber como ler os logs de fluxo, consulte [ler logs de fluxo do NSG](network-watcher-read-nsg-flow-logs.md).
- Para saber mais sobre o log de NSG, consulte [logs de Azure monitor para grupos de segurança de rede (NSGs)](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Para determinar se o tráfego é permitido ou negado para ou de uma VM, consulte [diagnosticar um problema de filtro de tráfego de rede VM](diagnose-vm-network-traffic-filtering-problem.md)
