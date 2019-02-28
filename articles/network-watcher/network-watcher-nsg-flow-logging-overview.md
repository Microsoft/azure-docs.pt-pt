---
title: Introdução ao registo do fluxo para segurança de rede de grupos com o observador de rede do Azure | Documentos da Microsoft
description: Este artigo explica como utilizar a funcionalidade de registos de fluxo NSG do observador de rede do Azure.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 62b526950367987e26c1c67394bc0720ae895fa6
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56983800"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Introdução ao registo do fluxo para grupos de segurança de rede

Registos de fluxo de grupo (NSG) de segurança de rede são uma funcionalidade do observador de rede permite-lhe ver informações sobre o tráfego IP de entrada e de saída através de um NSG. Registos de fluxo são escritos no formato JSON e mostram saídos e fluxos de entrada numa base por regra, a interface de rede (NIC) o fluxo se aplica, informações de 5 cadeias de identificação sobre o fluxo (IP de origem/destino, porta de origem/destino e protocolo), se o tráfego foi permitido ou negado e na versão 2, informações de débito (Bytes e pacotes).


![Descrição geral de registos de fluxo](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

Embora os registos de fluxo NSGs de destino, não são apresentados os mesmos como os outros registos. Os registos de fluxo são armazenados apenas dentro de uma conta de armazenamento e siga o caminho de registo mostrado no exemplo a seguir:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
Pode analisar os registos de fluxo e obter informações sobre o tráfego de rede através de [análise de tráfego](traffic-analytics.md).

As mesmas políticas de retenção vistas para outros registos aplicam-se aos registos de fluxo. Pode definir a política de retenção do registo de 1 dia para dias 2147483647. Se não definir uma política de retenção, os registos são mantidos para sempre.

> [!NOTE] 
> Utilizar a funcionalidade de política de retenção com o registo de fluxo de NSG pode resultar num grande volume de operações de armazenamento e os custos associados. Se não requerem a funcionalidade de política de retenção, recomendamos que defina este valor como 0.


## <a name="log-file"></a>Ficheiro de registo

Os registos de fluxo incluem as seguintes propriedades:

* **tempo** - hora a que o evento foi registado
* **systemId** -ID de recurso do grupo de segurança de rede
* **categoria** -a categoria do evento. A categoria é sempre **NetworkSecurityGroupFlowEvent**
* **ResourceId** -o Id de recurso do NSG
* **operationName** -NetworkSecurityGroupFlowEvents sempre
* **propriedades** -uma coleção de propriedades do fluxo
    * **Versão** -número de versão do esquema de eventos de registo de fluxo
    * **fluxos** -uma coleção de fluxos. Esta propriedade tem múltiplas entradas para diferentes regras
        * **regra** -da regra para que os fluxos são listados
            * **fluxos** -uma coleção de fluxos
                * **Mac** -endereço do MAC da NIC da VM em que o fluxo foi recolhido
                * **flowTuples** -uma cadeia que contém várias propriedades para a cadeia de identificação de fluxo no formato separados por vírgulas
                    * **Carimbo de hora** -este valor é o carimbo de data / hora de quando o fluxo ocorreu no formato da ÉPOCA do UNIX
                    * **IP de origem** -o IP de origem
                    * **Destino IP** -o IP de destino
                    * **Porta de origem** -a porta de origem
                    * **Porta de destino** -a porta de destino
                    * **Protocolo** -o protocolo do fluxo. Os valores válidos são **T** para TCP e **U** para UDP
                    * **Fluxo de tráfego** -a direção do fluxo de tráfego. Os valores válidos são **eu** de entrada e **s** para saída.
                    * **Decisão de tráfego** - se o tráfego foi permitido ou negado. Os valores válidos são **uma** para permitido e **1!d** para negado.
                    * **Estado do fluxo - versão 2 apenas** -captura o estado do fluxo. Estados possíveis **B**: Iniciar, quando um fluxo é criado. Estatísticas não são fornecidas. **C**: Continuar para um fluxo em curso. As estatísticas são fornecidas em intervalos de 5 minutos. **E**: Fim, quando um fluxo é terminado. As estatísticas são fornecidas.
                    * **Pacotes de - de origem para destino - versão 2 apenas** o número total de pacotes TCP ou UDP enviados da origem para destino, desde a última atualização.
                    * **Bytes enviados - origem para destino - versão 2 apenas** o número total de bytes de pacotes TCP ou UDP enviados da origem para destino, desde a última atualização. Bytes de pacote incluem o cabeçalho de pacote e o payload.
                    * **Pacotes - destino para origem - versão 2 apenas** o número total de pacotes TCP ou UDP enviadas do destino para origem, desde a última atualização.
                    * **Bytes enviados - destino para origem - versão 2 apenas** o número total de bytes de pacotes TCP e UDP enviadas do destino para origem, desde a última atualização. Bytes de pacote incluem o cabeçalho de pacote e o payload.

## <a name="nsg-flow-logs-version-2"></a>Versão 2 de registos de fluxo NSG

Versão 2 dos logs de apresenta o estado do fluxo. Pode configurar qual é a versão dos registos de fluxo de recebe. Para saber como ativar os registos de fluxo, veja [registo do fluxo do NSG ativar](network-watcher-nsg-flow-logging-portal.md).

Estado do fluxo *B* é registado quando um fluxo é iniciado. Estado do fluxo *C* e o estado do fluxo *i* são Estados que marcam a continuação de um fluxo e a terminação de fluxo, respectivamente. Ambos *C* e *i* Estados contêm informações de largura de banda do tráfego.

Para continuação *C* e de fim *i* Estados de fluxo, as contagens de byte e pacotes são agregadas contagens desde o momento do registo anterior para a cadeia de identificação de fluxo. O número total de pacotes transferidos referenciar a conversação do exemplo anterior, é 1021 + 52 + 8005 + 47 = 9125. É o número total de bytes transferidos 588096 + 29952 + 4610880 + 27072 = 5256000.

**Exemplo**: Cadeias de identificação do fluxo de uma conversa de TCP entre 185.170.185.105:35370 e 10.2.0.4:23:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

Para continuação *C* e de fim *i* Estados de fluxo, as contagens de byte e pacotes são agregadas contagens desde o momento do registo anterior para a cadeia de identificação de fluxo. O número total de pacotes transferidos referenciar a conversação do exemplo anterior, é 1021 + 52 + 8005 + 47 = 9125. É o número total de bytes transferidos 588096 + 29952 + 4610880 + 27072 = 5256000.

O texto que se segue é um exemplo de um registo de fluxo. Como pode ver, existem vários registos que se seguem a lista de propriedades descrita na secção anterior.

## <a name="nsg-flow-logging-considerations"></a>Considerações de registo do fluxo NSG

**Ativar NSG fluxo o registo em todos os NSGs anexados a um recurso**: Fluxo de início de sessão do Azure está configurado no recurso NSG. Um fluxo só pode ser associado a uma regra de NSG. Em cenários em que são utilizados vários NSGs, recomendamos que o registo do fluxo do NSG está ativado em todos os NSGs aplicada a interface de rede ou sub-rede de um recurso para garantir que todo o tráfego é registrado. Ver [como o tráfego é avaliado](../virtual-network/security-overview.md#how-traffic-is-evaluated) para obter mais informações sobre grupos de segurança de rede. 

**Os custos de registo do fluxo**: Registo do fluxo do NSG é faturado o volume de registos produzidos. Volume de tráfego elevado pode resultar num volume de registo de fluxo de grandes dimensões e os custos associados. Preços do log de fluxo de NSG não incluem os custos subjacentes de armazenamento. Utilizar a funcionalidade de política de retenção com o registo de fluxo de NSG pode resultar num grande volume de operações de armazenamento e os custos associados. Se não requerem a funcionalidade de política de retenção, recomendamos que defina este valor como 0. Ver [preços de observador de rede](https://azure.microsoft.com/en-us/pricing/details/network-watcher/) e [preços de armazenamento do Azure](https://azure.microsoft.com/en-us/pricing/details/storage/) para obter mais detalhes.

## <a name="sample-log-records"></a>Registros de log de exemplo

O texto que se segue é um exemplo de um registo de fluxo. Como pode ver, existem vários registos que se seguem a lista de propriedades descrita na secção anterior.


> [!NOTE]
> Os valores no **flowTuples* propriedade são uma lista separada por vírgulas.
 
### <a name="version-1-nsg-flow-log-format-sample"></a>Exemplo de formato de registo de fluxo NSG de versão 1
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
### <a name="version-2-nsg-flow-log-format-sample"></a>Exemplo de formato de registo de fluxo NSG de versão 2
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

## <a name="next-steps"></a>Passos Seguintes

- Para saber como ativar os registos de fluxo, veja [registo do fluxo do NSG ativar](network-watcher-nsg-flow-logging-portal.md).
- Para saber como ler os registos de fluxo, veja [registos de fluxo de NSG de leitura](network-watcher-read-nsg-flow-logs.md).
- Para saber mais sobre o registo de NSG, consulte [registos do Azure Monitor para grupos de segurança de rede (NSGs)](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Para determinar se o tráfego é permitido ou negado para ou a partir de uma VM, veja [diagnosticar um problema de filtragem de tráfego de rede VM](diagnose-vm-network-traffic-filtering-problem.md)
