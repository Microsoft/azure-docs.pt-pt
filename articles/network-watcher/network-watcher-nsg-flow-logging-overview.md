---
title: Introdução à exploração de fluxos de registo para NSGs
titleSuffix: Azure Network Watcher
description: Este artigo explica como usar a funcionalidade de registos de fluxo NSG do Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: fb4a55b9757748581e26f3d6594f9be2139658cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228256"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Introdução à exploração de fluxos de exploração de grupos de segurança da rede

Os registos do fluxo do grupo de segurança de rede (NSG) são uma funcionalidade do Observador de Rede que lhe permite visualizar informações sobre a entrada e saída de tráfego IP através de um NSG. Os registos do fluxo são escritos no formato JSON e mostram os fluxos de entrada e saída por regra, a interface de rede (NIC) à qual o fluxo se aplica, informações de cinco cadeias de identificação sobre o fluxo (IP de origem/destino, porta de origem/destino e protocolo), se o tráfego foi permitido ou negado e, na Versão 2, informações de débito (Bytes e Pacotes).


![visão geral dos registos de fluxo](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

Enquanto os registos de fluxo visam nsgs, não são apresentados os mesmos que os outros troncos. Os registos de fluxo são armazenados apenas dentro de uma conta de armazenamento e seguem o caminho de exploração de madeira mostrado no seguinte exemplo:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
Pode analisar os registos de fluxo e obter informações sobre o tráfego da sua rede utilizando [análises](traffic-analytics.md)de tráfego .

As mesmas políticas de retenção observadas para outros registos aplicam-se aos registos de fluxo. Pode definir a política de retenção de registos de 1 dia a 365 dias. Se uma política de retenção não for definida, os registos são mantidos para sempre.

## <a name="log-file"></a>Ficheiro de registo

Os registos de fluxo incluem as seguintes propriedades:

* **tempo** - Tempo em que o evento foi registado
* **systemId** - Network Security Group resource Id.
* **categoria** - A categoria do evento. A categoria é sempre **NetworkSecurityGroupFlowEvent**
* **resourceid** - O recurso Id do NSG
* **operaçãoNome** - Sempre NetworkSecurityGroupFlowEvents
* **propriedades** - Uma coleção de propriedades do fluxo
    * **Versão** - Número de versão do esquema do evento Flow Log
    * **fluxos** - Uma coleção de fluxos. Esta propriedade tem múltiplas entradas para diferentes regras
        * **regra** - Regra para a qual os fluxos estão listados
            * **fluxos** - uma coleção de fluxos
                * **mac** - O endereço MAC do NIC para o VM onde o fluxo foi recolhido
                * **flowTuples** - Uma cadeia que contém múltiplas propriedades para o fluxo tuple em formato separado de vírgula
                    * **Carimbo** de tempo - Este valor é o carimbo de quando o fluxo ocorreu em formato de época UNIX
                    * **FONTE IP** - A fonte IP
                    * **Ip de destino** - O destino IP
                    * **Porta fonte** - A porta de origem
                    * **Porto** de destino - O Porto de Destino
                    * **Protocolo** - O protocolo do fluxo. Valores válidos são **T** para TCP e **U** para UDP
                    * **Fluxo** de Tráfego - A direção do fluxo de tráfego. Valores válidos são **eu** para entrada e **O** para saída.
                    * **Decisão do Tráfego** - Se o tráfego foi permitido ou negado. Valores válidos são **A** para permitido e **D** para negado.
                    * **Flow State - Apenas versão 2** - Captura o estado do fluxo. Os estados possíveis são **B:** Comece, quando um fluxo é criado. As estatísticas não são fornecidas. **C:** Continuar para um fluxo contínuo. As estatísticas são fornecidas em intervalos de 5 minutos. **E:** Terminar, quando um fluxo é terminado. As estatísticas são fornecidas.
                    * **Pacotes - Fonte para destino - Versão 2 Apenas** O número total de pacotes TCP ou UDP enviados de origem para destino desde a última atualização.
                    * **Bytes enviados - Fonte para destino - Versão 2 Apenas** O número total de bytes de pacotes TCP ou UDP enviados de origem para destino desde a última atualização. Os bytes do pacote incluem o cabeçalho do pacote e a carga útil.
                    * **Pacotes - Destino para fonte - Versão 2 Apenas** O número total de pacotes TCP ou UDP enviados de destino para fonte desde a última atualização.
                    * **Bytes enviados - Destino para fonte - Versão 2 Apenas** O número total de bytes de pacotes TCP e UDP enviados de destino para fonte desde a última atualização. Os bytes do pacote incluem cabeçalho de pacote e carga útil.

## <a name="nsg-flow-logs-version-2"></a>NsG fluxo logs versão 2

A versão 2 dos registos introduz o estado de fluxo. Pode configurar qual a versão dos registos de fluxo que recebe. Para aprender a ativar os registos de fluxo, consulte o registo de [fluxo snSG de habilitação](network-watcher-nsg-flow-logging-portal.md).

O estado de fluxo *B* é registado quando um fluxo é iniciado. Estado *de* fluxo C e estado de fluxo *E* são estados que marcam a continuação de uma rescisão de fluxo e fluxo, respectivamente. Tanto os estados *C* como *E* contêm informações de largura de banda de tráfego.

**Exemplo**: Fluxo de tuples de uma conversa tCP entre 185.170.185.105:35370 e 10.2.0.4:23:

"1493763938.185.170.185.105,10.2.0.4.35370,23,T,A,B,,,," "1493695838.185.170.185.105,10.2.0.4.35370,23,T,A,1021.588096.8005.4610880" "1493696138.185.170.185.105,105,10.2.0.4.35370,23,T,I,E,52.29952,47.27.27072"

Para a continuação dos estados de fluxo *C* e final *E,* as contagens de byte e pacote são contagens agregadas a partir do tempo do anterior recorde de tuple de fluxo. Referenciando a conversa de exemplo anterior, o número total de pacotes transferidos é 1021+52+8005+47 = 9125. O número total de bytes transferidos é 588096+29952+4610880+27072 = 5256000.

O texto que se segue é um exemplo de um registo de fluxo. Como pode ver, existem vários registos que seguem a lista de propriedades descrita na secção anterior.

## <a name="nsg-flow-logging-considerations"></a>Considerações de exploração de fluxo de NSG

**Considerações sobre a conta de armazenagem:** 

- Localização: A conta de armazenamento utilizada deve estar na mesma região que o NSG.
- Rotação da chave de auto-gestão: Se alterar/rodar as teclas de acesso da sua conta de armazenamento, os Registos de Fluxo NSG deixarão de funcionar. Para corrigir este problema, tem de desativar e, em seguida, reativar os registos de fluxo nsg.

Ativar o **registo de fluxo nsg em todos os NSGs ligados a um recurso**: O registo de fluxo saqueem em Azure está configurado no recurso NSG. Um fluxo só será associado a uma regra nsg. Nos cenários em que são utilizados vários NSGs, recomendamos que o registo de fluxo sNG esteja ativado em todos os NSGs aplicados a subnet ou interface de rede de um recurso para garantir que todo o tráfego é registado. Para mais informações, consulte [como o tráfego é avaliado](../virtual-network/security-overview.md#how-traffic-is-evaluated) em Grupos de Segurança da Rede.

**Custos de exploração de**fluxo : A exploração de fluxo sonífica saneada é faturada no volume de registos produzidos. Um elevado volume de tráfego pode resultar num grande volume de registo de fluxo e nos custos associados. Os preços do registo nsg Flow não incluem os custos subjacentes ao armazenamento. Utilizar a funcionalidade de política de retenção com o NSG Flow Logging significa incorrer em custos de armazenamento separados por longos períodos de tempo. Se não necessitar da funcionalidade de política de retenção, recomendamos que detetete este valor para 0. Para mais informações, consulte os preços do Observador da [Rede](https://azure.microsoft.com/pricing/details/network-watcher/) e os preços de [armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) para obter mais detalhes.

**Fluxos de entrada registados desde os Ip sem IPs públicos**: VMs que não possuam um endereço IP público atribuído através de um endereço IP público associado ao NIC como um IP público de nível de exemplo, ou que fazem parte de um pool de back-end de um equilibrista de carga básico, usam [SNAT padrão](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) e têm um endereço IP atribuído pela Azure para facilitar a conectividade de saída. Como resultado, pode ver entradas de registo de fluxo para fluxos de endereços IP da Internet, se o fluxo estiver destinado a uma porta na gama de portas atribuídas para SNAT. Embora o Azure não permita estes fluxos para o VM, a tentativa é registada e aparece no registo de fluxo NSG do Network Watcher por design. Recomendamos que o tráfego indesejado de entrada na Internet seja explicitamente bloqueado com o NSG.

**Byte incorreto e contagem de pacotes para fluxos apátridas**: Grupos de [Segurança da Rede (NSGs)](https://docs.microsoft.com/azure/virtual-network/security-overview) são implementados como uma [firewall imponente](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true). Por muito que muitas regras de incumprimento/internas que controlem o fluxo de tráfego sejam implementadas de forma apátrida. Devido às limitações da plataforma, os bytes e os pacotes contam não são registados para fluxos apátridas (isto é, fluxos de tráfego que passam por regras apátridas), são registados apenas para fluxos apátridas. Consequentemente, o número de bytes e pacotes relatados nos Registos de Fluxo nsg (e na Análise de Tráfego) poderia ser diferente dos fluxos reais. Esta limitação deverá ser fixada até junho de 2020.

## <a name="sample-log-records"></a>Registos de registos de amostras

O texto que se segue é um exemplo de um registo de fluxo. Como pode ver, existem vários registos que seguem a lista de propriedades descrita na secção anterior.


> [!NOTE]
> Os valores na propriedade **flowTuples* são uma lista separada de vírgula.
 
### <a name="version-1-nsg-flow-log-format-sample"></a>Amostra de formato de registo de fluxo NSG da versão 1
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
### <a name="version-2-nsg-flow-log-format-sample"></a>Amostra de formato de registo de fluxo NSG da versão 2
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

- Para aprender a ativar os registos de fluxo, consulte o registo de [fluxo snSG de habilitação](network-watcher-nsg-flow-logging-portal.md).
- Para aprender a ler os registos de fluxo, consulte os registos de [fluxo da NSG](network-watcher-read-nsg-flow-logs.md).
- Para saber mais sobre a exploração madeireira NSG, consulte [os registos do Monitor Azure para grupos de segurança de rede (NSGs)](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Para determinar se o tráfego é permitido ou negado de ou para um VM, consulte Diagnosticar um problema de filtro de tráfego de [rede VM](diagnose-vm-network-traffic-filtering-problem.md)
