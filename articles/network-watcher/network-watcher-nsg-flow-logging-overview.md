---
title: Grupos de introdução ao registo do fluxo de segurança de rede com o observador de rede do Azure | Microsoft Docs
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
ms.openlocfilehash: c6a24fbca37d6aa1d775a70c708a139dfb70b813
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182430"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Introdução ao registo do fluxo de grupos de segurança de rede

Registos de fluxo de grupo (NSG) de segurança de rede são uma funcionalidade do observador de rede que permite-lhe ver informações sobre o tráfego IP de entrada e de saída através de um NSG. Registos de fluxo são escritos no formato json e mostram fluxos de saída e entrados numa base por regra, o fluxo aplica-se a interface de rede (NIC), informações de 5 cadeias de identificação sobre o fluxo (IP de origem/destino, porta de origem/destino e protocolo) e se o tráfego tiver sido permitido ou negado.

![Descrição geral de registos de fluxo](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

Enquanto o fluxo de registos NSGs de destino, não são apresentados os mesmos como os outros registos. Registos de fluxo são armazenados apenas dentro de uma conta de armazenamento e siga o caminho de registo apresentado no exemplo seguinte:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

As políticas de retenção mesmo visualizadas para outros registos aplicam-se nos registos de fluxo. Pode definir políticas de retenção do registo de 1 dia, a 365 dias. Se não definir uma política de retenção, os registos são mantidos para sempre.

## <a name="log-file"></a>Ficheiro de registo

Registos de fluxo incluem as seguintes propriedades:

* **tempo** - tempo quando o evento foi registado
* **systemId** -ID de recurso do grupo de segurança de rede
* **categoria** -a categoria do evento. A categoria é sempre **NetworkSecurityGroupFlowEvent**
* **ResourceId** -o recurso Id o NSG
* **operationName** -sempre NetworkSecurityGroupFlowEvents
* **propriedades** -uma coleção de propriedades do fluxo
    * **Versão** -número da versão do esquema de eventos de registo de fluxo
    * **fluxos** -uma coleção de fluxos. Esta propriedade tem várias entradas de regras diferentes
        * **regra** -de regra para que os fluxos estão listados
            * **fluxos** -uma coleção de fluxos
                * **Mac** -endereço MAC o NIC para a VM em que o fluxo foi recolhido
                * **flowTuples** -uma cadeia que contém várias propriedades para a cadeia de identificação de fluxo no formato de valores separados por vírgulas
                    * **Carimbo de hora** -este valor é o carimbo de hora do quando o fluxo ocorreu no formato UNIX ÉPOCA
                    * **IP de origem** -o IP de origem
                    * **Destino IP** -o IP de destino
                    * **Porta de origem** -a porta de origem
                    * **Porta de destino** -o porta de destino
                    * **Protocolo** -o protocolo do fluxo. Os valores válidos são **T** para TCP e **U** para UDP
                    * **Fluxo de tráfego** -a direção do fluxo de tráfego. Os valores válidos são **posso** para entrada e **Nã** para saída.
                    * **Tráfego** - se a tráfego foi permitido ou negado. Os valores válidos são **A** para permitidos e **D** para negado.

O texto que se segue é um exemplo de um registo de fluxo. Como pode ver, existem vários registos que se seguem a lista de propriedades descrita na secção anterior.

> [!NOTE]
> Os valores na **flowTuples* propriedade são uma lista separada por vírgulas.
 
```json
{
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

## <a name="next-steps"></a>Passos Seguintes

- Para saber como ativar registos de fluxo, consulte o artigo [o registo de fluxo de ativar o NSG](network-watcher-nsg-flow-logging-portal.md).
- Para saber mais sobre o registo de NSG, consulte [Iniciar análise para grupos de segurança de rede (NSGs)](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Para determinar se o tráfego é permitido ou negado de ou para uma VM, consulte [diagnosticar um problema de filtro de tráfego de rede VM](diagnose-vm-network-traffic-filtering-problem.md)