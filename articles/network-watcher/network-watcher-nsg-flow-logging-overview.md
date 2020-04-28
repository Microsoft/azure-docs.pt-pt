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
ms.openlocfilehash: ed14d3fb1cd3d9d8af37088811ce62b050778a95
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82189808"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Introdução à exploração de fluxos de exploração de grupos de segurança da rede

## <a name="introduction"></a>Introdução

[Os](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) registos de fluxo do grupo de segurança de rede (NSG) são uma característica do Azure Network Watcher que permite registar informações sobre o tráfego IP que flui através de um NSG. Os dados de fluxo são enviados para as contas do Armazenamento Azure de onde pode aceder a ele, bem como exportá-los para qualquer ferramenta de visualização, SIEM ou IDS à sua escolha.

![visão geral dos registos de fluxo](./media/network-watcher-nsg-flow-logging-overview/homepage.jpg)

## <a name="why-use-flow-logs"></a>Porquê usar os Registos de Fluxo?

É vital monitorizar, gerir e conhecer a sua própria rede para segurança, conformidade e desempenho descomprometidos. Conhecer o seu próprio ambiente é da maior importância para protegê-lo e otimizar. Muitas vezes é preciso saber o estado atual da rede, que está a ligar-se, de onde estão a ligar-se, quais as portas abertas à internet, comportamento de rede esperado, comportamento irregular da rede e aumentos repentinos de tráfego.

Os registos de fluxo são a fonte da verdade para toda a atividade da rede no seu ambiente de nuvem. Quer seja uma startup próxima a tentar otimizar recursos ou uma grande empresa a tentar detetar intrusões, os registos flow são a sua melhor aposta. Pode usá-lo para otimizar fluxos de rede, monitorizar a entrada, verificar a conformidade, detetar intrusões e muito mais.

## <a name="common-use-cases"></a>Casos de utilização comuns

**Monitorização da rede**: Identifique o tráfego desconhecido ou indesejado. Monitorize os níveis de tráfego e o consumo de largura de banda. Filtrar os registos de fluxo por IP e porta para entender o comportamento da aplicação. Export Flow Logs para ferramentas de análise e visualização à sua escolha para configurar dashboards de monitorização.

**Monitorização e otimização de utilização:** Identifique os melhores falantes da sua rede. Combine com os dados do GeoIP para identificar o tráfego transversal. Compreender o crescimento do tráfego na previsão de capacidade. Utilize dados para eliminar regras de tráfego excessivamente restritivas.

**Conformidade**: Utilizar dados de fluxo para verificar o isolamento da rede e o cumprimento das regras de acesso à empresa

**Análise forense da rede & Análise de Segurança**: Analise os fluxos de rede de IPs comprometidos e interfaces de rede. Exportar registos de fluxo para qualquer ferramenta SIEM ou IDS à sua escolha.

## <a name="how-logging-works"></a>Como funciona a exploração madeireira

**Propriedades-chave**

- Os registos de fluxo operam na [Camada 4](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_Layer) e registam todos os fluxos ip que entram e saem de um NSG
- Os registos são recolhidos através da plataforma Azure e não afetam de forma alguma os recursos dos clientes ou o desempenho da rede.
- Os registos são escritos no formato JSON e mostram fluxos de saída e de entrada numa base de regra NSG.
- Cada registo de registo contém a interface de rede (NIC) que o fluxo se aplica a informações de 5-tuple, a informação de passagem da decisão de tráfego & (apenas versão 2). Consulte o _Formato de Registo_ abaixo para mais detalhes.
- Os Registos de Fluxo têm uma funcionalidade de retenção que permite eliminar automaticamente os registos até um ano após a sua criação. **NOTA:** A retenção só está disponível se utilizar contas de [armazenamento v2 (GPv2)](https://docs.microsoft.com/azure/storage/common/storage-account-overview#types-of-storage-accounts). 

**Conceitos-chave**

- As redes definidas pelo software são organizadas em torno de Redes Virtuais (VNETs) e subnets. A segurança destes VNets e subredes pode ser gerida usando um NSG.
- Um grupo de segurança de rede (NSG) contém uma lista de regras de _segurança_ que permitem ou negam o tráfego de rede nos recursos a que está ligado. Os NSGs podem ser associados a subredes, VMs individuais ou interfaces de rede individuais (NIC) anexadas a VMs (Gestor de Recursos). Para mais informações, consulte a visão geral do grupo de [segurança da rede](https://docs.microsoft.com/azure/virtual-network/security-overview?toc=%2Fazure%2Fnetwork-watcher%2Ftoc.json).
- Todos os fluxos de tráfego da sua rede são avaliados usando as regras do NSG aplicável.
- O resultado destas avaliações são os Registos de Fluxo NSG. Os registos de fluxo são recolhidos através da plataforma Azure e não requerem qualquer alteração aos recursos do cliente.
- Os Registos de Fluxo NSG são escritos em contas de armazenamento de onde podem ser acedidos.
- Pode exportar, processar, analisar e visualizar os Flow Logs utilizando ferramentas como TA, Splunk, Grafana, Stealthwatch, etc.

## <a name="log-format"></a>Formato de log

Os registos de fluxo incluem as seguintes propriedades:

* **tempo** - Tempo em que o evento foi registado
* **systemId** - Network Security Group iD.
* **categoria** - A categoria do evento. A categoria é sempre **NetworkSecurityGroupFlowEvent**
* **resourceid** - O ID de recurso do NSG
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


**Registos de fluxo NSG Versão 2 (vs Versão 1)** 

A versão 2 dos registos introduz o conceito de estado de fluxo. Pode configurar qual a versão dos registos de fluxo que recebe.

O estado de fluxo _B_ é registado quando um fluxo é iniciado. Estado _de_ fluxo C e estado de fluxo _E_ são estados que marcam a continuação de uma rescisão de fluxo e fluxo, respectivamente. Tanto os estados _C_ como _E_ contêm informações de largura de banda de tráfego.

### <a name="sample-log-records"></a>Registos de registos de amostras

O texto que se segue é um exemplo de um registo de fluxo. Como pode ver, existem vários registos que seguem a lista de propriedades descrita na secção anterior.

> [!NOTE]
> Os valores na propriedade **flowTuples* são uma lista separada de vírgula.
 
**Amostra de formato de registo de fluxo NSG da versão 1**
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
        
        
```
**Amostra de formato de registo de fluxo NSG da versão 2**
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
        }
        
```
**Estple de log explicado**

![visão geral dos registos de fluxo](./media/network-watcher-nsg-flow-logging-overview/tuple.png)

**Cálculo da largura de banda da amostra**

Fluxo de tuples de uma conversa de TCP entre 185.170.185.105:35370 e 10.2.0.4:23:

"1493763938.185.170.185.105,10.2.0.4.35370,23,T,A,B,,,," "1493695838.185.170.185.105,10.2.0.4.35370,23,T,A,1021.588096.8005.4610880" "1493696138.185.170.185.105,105,10.2.0.4.35370,23,T,I,E,52.29952,47.27.27072"

Para a continuação dos estados de fluxo _C_ e final _E,_ as contagens de byte e pacote são contagens agregadas a partir do tempo do anterior recorde de tuple de fluxo. Referenciando a conversa de exemplo anterior, o número total de pacotes transferidos é 1021+52+8005+47 = 9125. O número total de bytes transferidos é 588096+29952+4610880+27072 = 5256000.


## <a name="enabling-nsg-flow-logs"></a>Ativar registos de fluxo NSG

Utilize o link relevante a partir de baixo para obter guias sobre a ativação de registos de fluxo.

- [Portal do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)
- [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-powershell)
- [CLI](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-cli)
- [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-rest)
- [Azure Resource Manager](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-azure-resource-manager)

## <a name="updating-parameters"></a>Atualização dos parâmetros

**Portal do Azure**

No portal Azure, navegue para a secção de registos de fluxo NSG no Observador da Rede. Em seguida, clique no nome do NSG. Isto irá elevar o painel de definições para o registo Flow. Altere os parâmetros que deseja e bata **Save** para implementar as alterações.

**PS/CLI/REST/ARM**

Para atualizar os parâmetros através de ferramentas de linha de comando, utilize o mesmo comando utilizado para ativar os Registos de Fluxo (de cima), mas com parâmetros atualizados que pretende alterar.

## <a name="working-with-flow-logs"></a>Trabalhar com registos de fluxo

*Registos de fluxo de leitura e exportação*

- [Descarregue &amp; a visualização de Fluxos de Registos do portal](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal#download-flow-log)
- [Ler registos de fluxo utilizando funções PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs)
- [Exportar registos de fluxo de NSG para Splunk](https://www.splunk.com/en_us/blog/tips-and-tricks/splunking-microsoft-azure-network-watcher-data.html)

Enquanto os registos de fluxo visam nsgs, não são apresentados os mesmos que os outros troncos. Os registos de fluxo são armazenados apenas dentro de uma conta de armazenamento e seguem o caminho de exploração de madeira mostrado no seguinte exemplo:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

*Visualizar registos de fluxo*

- [A Azure Traffic analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) é um serviço nativo azure para processar registos de fluxo, extrai insights e visualiza os registos de fluxo. 
- [[Tutorial] Visualizar registos de fluxo NSG com Power BI](https://docs.microsoft.com/azure/network-watcher/network-watcher-visualize-nsg-flow-logs-power-bi)
- [[Tutorial] Visualizar os registos de fluxo nsg com pilha elástica](https://docs.microsoft.com/azure/network-watcher/network-watcher-visualize-nsg-flow-logs-open-source-tools)
- [[Tutorial] Gerir e analisar registos de FLUXO NSG usando Grafana](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-grafana)
- [[Tutorial] Gerir e analisar os registos de fluxo nsg usando graylog](https://docs.microsoft.com/azure/network-watcher/network-watcher-analyze-nsg-flow-logs-graylog)


## <a name="nsg-flow-logging-considerations"></a>Considerações de exploração de fluxo de NSG

**Considerações sobre a conta de armazenagem:** 

- Localização: A conta de armazenamento utilizada deve estar na mesma região que o NSG.
- Rotação da chave de auto-gestão: Se alterar/rodar as teclas de acesso da sua conta de armazenamento, os Registos de Fluxo NSG deixarão de funcionar. Para corrigir este problema, tem de desativar e, em seguida, reativar os registos de fluxo nsg.

**Custos de exploração de**fluxo : A exploração de fluxo sonífica saneada é faturada no volume de registos produzidos. Um elevado volume de tráfego pode resultar num grande volume de registo de fluxo e nos custos associados. Os preços do registo nsg Flow não incluem os custos subjacentes ao armazenamento. Utilizar a funcionalidade de política de retenção com o NSG Flow Logging significa incorrer em custos de armazenamento separados por longos períodos de tempo. Se não necessitar da funcionalidade de política de retenção, recomendamos que detetete este valor para 0. Para mais informações, consulte os preços do Observador da [Rede](https://azure.microsoft.com/pricing/details/network-watcher/) e os preços de [armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) para obter mais detalhes.

**Fluxos de entrada registados desde os Ip sem IPs públicos**: VMs que não possuam um endereço IP público atribuído através de um endereço IP público associado ao NIC como um IP público de nível de exemplo, ou que fazem parte de um pool de back-end de um equilibrista de carga básico, usam [SNAT padrão](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) e têm um endereço IP atribuído pela Azure para facilitar a conectividade de saída. Como resultado, pode ver entradas de registo de fluxo para fluxos de endereços IP da Internet, se o fluxo estiver destinado a uma porta na gama de portas atribuídas para SNAT. Embora o Azure não permita estes fluxos para o VM, a tentativa é registada e aparece no registo de fluxo NSG do Network Watcher por design. Recomendamos que o tráfego indesejado de entrada na Internet seja explicitamente bloqueado com o NSG.

**Byte incorreto e contagem de pacotes para fluxos apátridas**: Grupos de [Segurança da Rede (NSGs)](https://docs.microsoft.com/azure/virtual-network/security-overview) são implementados como uma [firewall imponente](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true). Por muito que muitas regras de incumprimento/internas que controlem o fluxo de tráfego sejam implementadas de forma apátrida. Devido às limitações da plataforma, os bytes e os pacotes contam não são registados para fluxos apátridas (isto é, fluxos de tráfego que passam por regras apátridas), são registados apenas para fluxos apátridas. Consequentemente, o número de bytes e pacotes relatados nos Registos de Fluxo nsg (e na Análise de Tráfego) poderia ser diferente dos fluxos reais. Esta limitação deverá ser fixada até junho de 2020.

## <a name="best-practices"></a>Melhores práticas

**Ativar em VNETs/Subnets críticos**: Os registos de fluxo devem ser ativados em todos os VNETs/subredes críticos na sua subscrição como uma prática de auditoria e segurança. 

Ativar o **registo de fluxo nsg em todos os NSGs ligados a um recurso**: O registo de fluxo saqueem em Azure está configurado no recurso NSG. Um fluxo só será associado a uma regra nsg. Nos cenários em que são utilizados vários NSGs, recomendamos que os registos de fluxo nsg em todos os NSGs apliquem uma subnet ou interface de rede de um recurso para garantir que todo o tráfego é registado. Para mais informações, consulte [como o tráfego é avaliado](../virtual-network/security-overview.md#how-traffic-is-evaluated) em Grupos de Segurança da Rede.

**Fornecimento de armazenamento**: O armazenamento deve ser aprovisionado em sintonia com o volume esperado do Flow Log.

## <a name="troubleshooting-common-issues"></a>Resolução de problemas comuns

**Não conseguir ativar Registos do Fluxo do NSG**

- **Microsoft.Insights** fornecedor de recursos não está registado

Se recebeu um erro _AuthorizationFailed_ ou _GatewayAuthenticationFailed_, talvez não tenha ativado o fornecedor de recursos do Microsoft Insights na sua subscrição. [Siga as instruções](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal#register-insights-provider) para ativar o fornecedor Microsoft Insights.

**Ativei os Registos do Fluxo do NSG, mas não vejo dados na minha conta de armazenamento**

- **Tempo de configuração**

Os Registos do Fluxo do NSG podem demorar até 5 minutos a aparecer na conta de armazenamento (se configurado corretamente). Será apresentado um ficheiro PT1H.json, que poderá aceder [conforme descrito aqui](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal#download-flow-log).

- **Nenhum Tráfego nos NSGs**

Por vezes, poderá não ver os registos porque as VMs não estão ativas ou porque existem filtros de origem num Gateway de Aplicação ou noutros dispositivos que estão a bloquear o tráfego para os NSGs.

**Quero automatizar os Registos de Fluxo do NSG**

O suporte para a automatização através de modelos ARM não está disponível atualmente para os Registos de Fluxo do NSG. Leia o anúncio da [funcionalidade](https://azure.microsoft.com/updates/arm-template-support-for-nsg-flow-logs/) para mais informações.

## <a name="faq"></a>FAQ

**O que faz o NSG Flow Logs?**

Os recursos da rede Azure podem ser combinados e geridos através de Grupos de Segurança da [Rede (NSGs)](https://docs.microsoft.com/azure/virtual-network/security-overview). Os registos de fluxo NSG permitem-lhe registar informações de fluxo de 5 tuple sobre todo o tráfego através dos seus NSGs. Os registos de fluxo bruto são escritos numa conta de Armazenamento Azure de onde podem ser processados, analisados, consultados ou exportados conforme necessário.

**A utilização de Registos de Fluxo afeta a minha latência ou desempenho da rede?**

Os dados de registos de fluxo são recolhidos fora do caminho do tráfego da sua rede, pelo que não afeta a entrada ou a latência da rede. Pode criar ou eliminar registos de fluxo sem qualquer risco de impacto no desempenho da rede.

**Como uso registos de fluxo NSG com uma conta de armazenamento atrás de uma firewall?**

Para utilizar uma conta de Armazenamento atrás de uma firewall, tem de fornecer uma exceção para que os Serviços Microsoft Fifiados acedam à sua conta de armazenamento:

- Navegue na conta de armazenamento digitando o nome da conta de armazenamento na pesquisa global no portal ou na [página Contas](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts) de Armazenamento
- Na secção **DEFINIÇÕES,** selecione **Firewalls e redes virtuais**
- Em **Permitir o acesso a partir de**, selecione redes **selecionadas**. Em seguida, em **exceções,** marque a caixa ao lado de *****Permitir que os serviços fidedignos da Microsoft acedam a esta conta de armazenamento****
- Se já estiver selecionada, não é preciso alterar nada.
- Localize o seu NSG alvo na página de visão geral dos Registos de [Fluxo nsg](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) e ative os Registos de Fluxo NSG com a conta de armazenamento acima selecionada.

Pode verificar os registos de armazenamento após alguns minutos; deve ver um carimbo de data/hora atualizado ou um novo ficheiro JSON criado.

**Como uso os Registos de Fluxo NSG com uma conta de Armazenamento atrás de um ponto final de serviço?**

Os registos de fluxo NSG são compatíveis com os Pontos Finais do Serviço sem necessitar de qualquer configuração extra. Consulte o tutorial sobre a ativação de [pontos finais](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint) de serviço na sua rede virtual.

**Qual é a diferença entre as versões de registos de fluxo 1 & 2?**

A versão 2 do Flow Logs introduz o conceito de _Flow State_ & armazena informações sobre bytes e pacotes transmitidos. [Ler mais](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file)

## <a name="pricing"></a>Preços

Os registos de fluxo NSG são cobrados por GB de registos recolhidos e vêm com um nível livre de 5 GB/mês por subscrição. Para obter os preços atuais na sua região, consulte a página de [preços do Observador](https://azure.microsoft.com/pricing/details/network-watcher/)de Rede .

O armazenamento de registos é cobrado separadamente, consulte a página de [preços blob do Bloco](https://azure.microsoft.com/pricing/details/storage/blobs/) de Armazenamento Azure para preços relevantes.
 
