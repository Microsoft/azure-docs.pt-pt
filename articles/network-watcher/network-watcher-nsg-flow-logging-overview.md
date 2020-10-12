---
title: Introdução à exploração de fluxos para NSGs
titleSuffix: Azure Network Watcher
description: Este artigo explica como utilizar a funcionalidade de registos de fluxo NSG do Azure Network Watcher.
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
ms.openlocfilehash: c0001add9ddbafb67dc7ac305c5fc171a8e24a51
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89070586"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Introdução ao registo de fluxos para grupos de segurança da rede

## <a name="introduction"></a>Introdução

[Os](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) registos de fluxo do grupo de segurança da rede (NSG) são uma característica do Azure Network Watcher que permite registar informações sobre o tráfego IP que flui através de um NSG. Os dados de fluxo são enviados para as contas de Armazenamento Azure de onde pode acessá-lo, bem como exportá-lo para qualquer ferramenta de visualização, SIEM ou IDS à sua escolha.

![visão geral dos registos de fluxo](./media/network-watcher-nsg-flow-logging-overview/homepage.jpg)

## <a name="why-use-flow-logs"></a>Porquê utilizar Registos de Fluxo?

É vital monitorizar, gerir e conhecer a sua própria rede para segurança, conformidade e desempenho descompromissados. Conhecer o seu próprio ambiente é da maior importância protegê-lo e otimizá-lo. É frequente saber o estado atual da rede, quem está a ligar-se, de onde estão a ligar-se, quais as portas abertas à internet, o comportamento esperado da rede, o comportamento irregular da rede e o aumento súbito do tráfego.

Os registos de fluxo são a fonte da verdade para toda a atividade da rede no seu ambiente em nuvem. Quer seja uma próxima startup a tentar otimizar recursos ou uma grande empresa a tentar detetar intrusões, os registos flow são a sua melhor aposta. Pode usá-lo para otimizar fluxos de rede, monitorizar a produção, verificar a conformidade, detetar intrusões e muito mais.

## <a name="common-use-cases"></a>Casos de utilização comuns

**Monitorização da rede:** Identifique tráfego desconhecido ou não existente. Monitorize os níveis de tráfego e o consumo de largura de banda. Filtrar os registos de fluxo por IP e porta para entender o comportamento da aplicação. Fluxo de exportação para ferramentas de análise e visualização à sua escolha para configurar painéis de monitorização.

**Monitorização e otimização de utilização:** Identifique os melhores falantes da sua rede. Combine com os dados da GeoIP para identificar o tráfego entre regiões. Compreender o crescimento do tráfego para a previsão de capacidade. Utilize dados para remover regras de tráfego excessivamente restritivas.

**Conformidade:** Utilize dados de fluxo para verificar o isolamento da rede e o cumprimento das regras de acesso à empresa

Análise de segurança da rede **forense &:** Analise os fluxos de rede de IPs comprometidos e interfaces de rede. Registos de fluxo de exportação para qualquer ferramenta SIEM ou IDS à sua escolha.

## <a name="how-logging-works"></a>Como funciona o registo

**Propriedades-chave**

- Os registos de fluxo operam na [Camada 4](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_Layer) e registam todos os fluxos IP que entram e saem de um NSG
- Os registos são recolhidos através da plataforma Azure e não afetam de forma alguma os recursos do cliente ou o desempenho da rede.
- Os registos são escritos no formato JSON e mostram fluxos de saída e entrada numa base de regra de NSG.
- Cada registo de registo contém a interface de rede (NIC) que o fluxo se aplica a informações de 5 tuple, a decisão de tráfego & (apenas versão 2). Consulte _o Formato de Registo_ abaixo para mais detalhes.
- Os Registos de Fluxo têm uma funcionalidade de retenção que permite eliminar automaticamente os registos até um ano após a sua criação. 

> [!NOTE]
> A retenção só está disponível se utilizar [as contas de armazenamento v2 (GPv2) para fins gerais ..](https://docs.microsoft.com/azure/storage/common/storage-account-overview#types-of-storage-accounts) 

**Conceitos-chave**

- As redes definidas por software são organizadas em torno de Redes Virtuais (VNETs) e sub-redes. A segurança destes VNets e sub-redes pode ser gerida através de um NSG.
- Um grupo de segurança de rede (NSG) contém uma lista de _regras_ de segurança que permitem ou negam o tráfego de rede em recursos a que está ligado. Os NSGs podem ser associados com sub-redes, VMs individuais ou interfaces de rede individuais (NIC) anexadas a VMs (Gestor de Recursos). Para obter mais informações, consulte [a visão geral do grupo de segurança da rede.](https://docs.microsoft.com/azure/virtual-network/security-overview?toc=%2Fazure%2Fnetwork-watcher%2Ftoc.json)
- Todos os fluxos de tráfego da sua rede são avaliados usando as regras do NSG aplicável.
- O resultado destas avaliações são os Registos de Fluxo NSG. Os registos de fluxo são recolhidos através da plataforma Azure e não requerem qualquer alteração aos recursos do cliente.
- Nota: As regras são de dois tipos - terminando & não terminantes, cada uma com diferentes comportamentos de registo.
- - As regras da NSG Deny estão a terminar. A NSG negando que o tráfego irá registrá-lo em registos flow e o processamento neste caso iria parar depois de qualquer NSG negar o tráfego. 
- - NSG Permitir que as regras não sejam terminantes, o que significa que mesmo que um NSG o permita, o processamento continuará para o próximo NSG. O último NSG que permite o tráfego registará o tráfego nos registos do Flow.
- Os Registos de Fluxo NSG são escritos para contas de armazenamento a partir de onde podem ser acedidos.
- Você pode exportar, processar, analisar e visualizar Flow Logs usando ferramentas como TA, Splunk, Grafana, Stealthwatch, etc.

## <a name="log-format"></a>Formato de registo

Os registos de fluxo incluem as seguintes propriedades:

* **tempo** - Tempo em que o evento foi registado
* **systemId** - ID do sistema de segurança de rede.
* **categoria** - A categoria do evento. A categoria é sempre **NetworkSecurityGroupFlowEvent**
* **resourceid** - O ID de recursos do NSG
* **operaçãoName** - Sempre NetworkSecurityGroupFlowEvents
* **propriedades** - Uma coleção de propriedades do fluxo
    * **Versão** - Número de versão do esquema do evento Flow Log
    * **fluxos** - Uma coleção de fluxos. Esta propriedade tem múltiplas entradas para diferentes regras
        * **regra** - Regra para a qual os fluxos são listados
            * **fluxos** - uma coleção de fluxos
                * **mac** - O endereço MAC do NIC para o VM onde o fluxo foi recolhido
                * **flowTuples** - Uma cadeia que contém múltiplas propriedades para o tuple de fluxo em formato separado em vírgula
                    * **Time Stamp** - Este valor é o carimbo de tempo de quando o fluxo ocorreu no formato de época UNIX
                    * **Origem IP** - O IP de origem
                    * **Destination IP** - O destino IP
                    * **Porto fonte** - A porta de origem
                    * **Porto de Destino** - O Porto de destino
                    * **Protocolo** - O protocolo do fluxo. Valores válidos são **T** para TCP e **U** para UDP
                    * **Fluxo de Tráfego** - A direção do fluxo de tráfego. Valores válidos são **I** para entrada e **O** para saída.
                    * **Decisão de Trânsito** - Se o tráfego foi permitido ou negado. Valores válidos são **A** para permitido e **D** para negado.
                    * **Flow State - Versão 2 Apenas** - Captura o estado do fluxo. Os estados possíveis são **B:** Comece, quando um fluxo é criado. As estatísticas não são fornecidas. **C:** Continuando para um fluxo contínuo. As estatísticas são fornecidas em intervalos de 5 minutos. **E**: Fim, quando um fluxo é interrompido. As estatísticas são fornecidas.
                    * **Pacotes - Fonte de destino - Versão 2 Apenas** O número total de pacotes TCP ou UDP enviados de origem para destino desde a última atualização.
                    * **Bytes enviados - Fonte para destino - Versão 2 Apenas** O número total de bytes de pacotes TCP ou UDP enviados de origem para destino desde a última atualização. Os bytes de pacote incluem o cabeçalho do pacote e a carga útil.
                    * **Pacotes - Destino à fonte - Versão 2 Apenas** O número total de pacotes TCP ou UDP enviados de destino para fonte desde a última atualização.
                    * **Bytes enviados - Destino à fonte - Versão 2 Apenas** O número total de bytes de pacotes TCP e UDP enviados de destino a fonte desde a última atualização. Os bytes de pacote incluem cabeçalho de pacote e carga útil.


**Registos de fluxo NSG Versão 2 (vs Versão 1)** 

A versão 2 dos registos introduz o conceito de estado de fluxo. Pode configurar qual a versão dos registos de fluxo que recebe.

O estado _de fluxo B_ é registado quando um fluxo é iniciado. O estado _de fluxo C_ e o estado de fluxo _E_ são estados que marcam a continuação de uma terminação do fluxo e do fluxo, respectivamente. Tanto os estados _C_ como os estados _E_ contêm informações de largura de banda de tráfego.

### <a name="sample-log-records"></a>Registos de registos de amostras

O texto que se segue é um exemplo de um registo de fluxo. Como pode ver, existem vários registos que seguem a lista de propriedades descrita na secção anterior.

> [!NOTE]
> Os valores na propriedade *flowTuples* são uma lista separada por vírgulas.
 
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
**Amostra de formato de registo de fluxo NSG versão 2**
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
**Tuple log explicado**

![tuple de troncos de fluxo](./media/network-watcher-nsg-flow-logging-overview/tuple.png)

**Cálculo da largura de banda da amostra**

Tpláss em duas conversas de TCP entre 185.170.185.105:35370 e 10.2.0.4:23:

"1493763938.185.170.185.105.10.2.0.4.35370,23,T,I,A,B,,,," "1493695838,185.170.185.105.105.10.2.0.4.35370,23,23,23,23,23,23,23,23,,23,23,,23,23,,23,,23,,23,,23,,23,,23,,23,,,, T,I,C,1021.588096.8005.4610880" "1493696138.185.170.185.105.10.2.0.4.35370,23,T,I,A,E,52.29952.47.27072"

Para a continuação dos estados de fluxo _C_ e _end E,_ as contagens de byte e pacotes são contagens agregadas do tempo do registo anterior do fluxo tuple. Referindo-se à conversa de exemplo anterior, o número total de pacotes transferidos é 1021+52+8005+47 = 9125. O número total de bytes transferidos é 588096+29952+4610880+27072 = 5256000.


## <a name="enabling-nsg-flow-logs"></a>Ativar registos de fluxo NSG

Utilize o link relevante a partir de baixo para obter guias sobre a ativação dos registos de fluxo.

- [Portal do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)
- [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-powershell)
- [CLI](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-cli)
- [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-rest)
- [Azure Resource Manager](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-azure-resource-manager)

## <a name="updating-parameters"></a>Atualização de parâmetros

**Portal do Azure**

No portal Azure, navegue para a secção de Registos de Fluxo NSG no Observador de Rede. Em seguida, clique no nome do NSG. Isto irá trazer o painel de definições para o registo flow. Altere os parâmetros que deseja e bata **Para** implementar as alterações.

**PS/CLI/REST/ARM**

Para atualizar parâmetros através de ferramentas de linha de comando, utilize o mesmo comando utilizado para ativar Registos de Fluxo (a partir de cima) mas com parâmetros atualizados que pretende alterar.

## <a name="working-with-flow-logs"></a>Trabalhar com registos flow

*Ler e Exportar registos de fluxos*

- [Ver &amp; registos de fluxo de descarregamento a partir do portal](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal#download-flow-log)
- [Ler registos de fluxo usando funções PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs)
- [Exportar registos de fluxo NSG para Splunk](https://www.splunk.com/en_us/blog/tips-and-tricks/splunking-microsoft-azure-network-watcher-data.html)

Embora os registos de fluxo direcionem os NSGs, não são apresentados da mesma forma que os outros registos. Os registos de fluxo são armazenados apenas numa conta de armazenamento e seguem o caminho de registo indicado no seguinte exemplo:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

*Visualizar registos de fluxo*

- [A azure Traffic analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) é um serviço nativo Azure para processar registos de fluxo, extratos insights e visualizar registos de fluxo. 
- [[Tutorial] Visualizar os registos do NSG Flow com o Power BI](https://docs.microsoft.com/azure/network-watcher/network-watcher-visualize-nsg-flow-logs-power-bi)
- [[Tutorial] Visualizar os registos do fluxo NSG com a Pilha Elástica](https://docs.microsoft.com/azure/network-watcher/network-watcher-visualize-nsg-flow-logs-open-source-tools)
- [[Tutorial] Gerir e analisar registos NSG Flow usando Grafana](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-grafana)
- [[Tutorial] Gerir e analisar registos NSG Flow usando Graylog](https://docs.microsoft.com/azure/network-watcher/network-watcher-analyze-nsg-flow-logs-graylog)


## <a name="nsg-flow-logging-considerations"></a>Considerações de registo de fluxos NSG

**Considerações da conta de armazenamento:** 

- Localização: A conta de armazenamento utilizada deve estar na mesma região que o NSG.
- Rotação da chave de auto-gestão: Se alterar/rodar as teclas de acesso à sua conta de armazenamento, os Registos de Fluxo NSG deixarão de funcionar. Para corrigir este problema, tem de desativar e, em seguida, voltar a ativar os Registos de Fluxo NSG.

**Custos de registo do fluxo**: A exploração de fluxo NSG é faturada no volume de registos produzidos. O elevado volume de tráfego pode resultar num grande volume de registo de fluxo e nos custos associados. O preço do registo do NSG Flow não inclui os custos subjacentes de armazenamento. A utilização da função de política de retenção com a NSG Flow Logging significa incorrer em custos de armazenamento separados por longos períodos de tempo. Se não necessitar da função de política de retenção, recomendamos que desemote este valor para 0. Para mais informações, consulte [o Preço do Observador de Rede](https://azure.microsoft.com/pricing/details/network-watcher/) e os Preços de Armazenamento [Azure](https://azure.microsoft.com/pricing/details/storage/) para obter mais detalhes.

**Problemas com as regras de entrada definidas pelo utilizador**: [Os Grupos de Segurança da Rede (NSGs)](https://docs.microsoft.com/azure/virtual-network/security-overview) são implementados como uma firewall [stateful](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true). No entanto, devido às limitações atuais da plataforma, as regras definidas pelo utilizador que afetam os fluxos de TCP de entrada são implementadas de forma apátrida. Devido a isso, os fluxos afetados pelas regras de entrada definidas pelo utilizador tornam-se não-terminantes. Adicionalmente, as contagens de byte e pacotes não são registadas para estes fluxos. Consequentemente, o número de bytes e pacotes relatados em Registos de Fluxo NSG (e Traffic Analytics) poderia ser diferente dos números reais. Uma bandeira de opt-in que corrige estas questões está prevista para dezembro de 2020. Entretanto, os clientes que enfrentam problemas graves devido a este comportamento podem solicitar a opt-in via Support, por favor, levantem um pedido de suporte no âmbito do Network Watcher > NSG Flow Logs.  

**Fluxos de entrada registados de IPs de internet para VMs sem IPs públicos**: VMs que não têm um endereço IP público atribuído através de um endereço IP público associado ao NIC como um IP público de nível de instância, ou que fazem parte de um pool de back-end do balancer de carga básico, usam [SNAT padrão](../load-balancer/load-balancer-outbound-connections.md) e têm um endereço IP atribuído pela Azure para facilitar a conectividade de saída. Como resultado, pode ver entradas de registo de fluxo para fluxos a partir de endereços IP da Internet, se o fluxo estiver destinado a uma porta na gama de portas atribuídas ao SNAT. Embora o Azure não permita estes fluxos para o VM, a tentativa é registada e aparece no registo de fluxo NSG do Observador de Rede por design. Recomendamos que o tráfego de internet de entrada indesejada seja explicitamente bloqueado com o NSG.

## <a name="best-practices"></a>Melhores práticas

**Ativar em VNETs/Subnetas críticas**: Os registos de fluxo devem ser ativados em todos os VNETs/subnets críticos da sua subscrição como uma auditoria e as melhores práticas de segurança. 

**Ativar o registo de fluxo NSG em todos os NSGs ligados a um recurso**: A exploração de fluxo em Azure está configurada no recurso NSG. Um fluxo só será associado a uma regra NSG. Em cenários em que vários NSGs são utilizados, recomendamos que os registos de fluxo NSG em todos os NSGs apliquem a sub-rede ou interface de rede de um recurso para garantir que todo o tráfego seja registado. Para mais informações, consulte [como o tráfego é avaliado](../virtual-network/network-security-group-how-it-works.md) em Grupos de Segurança de Rede.

**Fornecimento de armazenamento**: O armazenamento deve ser a provisionado em sintonia com o volume esperado do Registo de Fluxo.

## <a name="troubleshooting-common-issues"></a>Resolução de problemas comuns

**Não conseguir ativar Registos do Fluxo do NSG**

- O fornecedor de recursos **Microsoft.Insights** não está registado

Se recebeu um erro _AuthorizationFailed_ ou _GatewayAuthenticationFailed_, talvez não tenha ativado o fornecedor de recursos do Microsoft Insights na sua subscrição. [Siga as instruções](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal#register-insights-provider) para ativar o fornecedor Microsoft Insights.

**Ativei os Registos do Fluxo do NSG, mas não vejo dados na minha conta de armazenamento**

- **Tempo de configuração**

Os Registos do Fluxo do NSG podem demorar até 5 minutos a aparecer na conta de armazenamento (se configurado corretamente). Será apresentado um ficheiro PT1H.json, que poderá aceder [conforme descrito aqui](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal#download-flow-log).

- **Nenhum Tráfego nos NSGs**

Por vezes, poderá não ver os registos porque as VMs não estão ativas ou porque existem filtros de origem num Gateway de Aplicação ou noutros dispositivos que estão a bloquear o tráfego para os NSGs.

**Quero automatizar os Registos de Fluxo do NSG**

O suporte para a automatização através de modelos ARM não está disponível atualmente para os Registos de Fluxo do NSG. Leia o anúncio da [funcionalidade](https://azure.microsoft.com/updates/arm-template-support-for-nsg-flow-logs/) para mais informações.

## <a name="faq"></a>FAQ

**O que faz os Registos de Fluxo NSG?**

Os recursos da rede Azure podem ser combinados e geridos através de Grupos de Segurança de [Rede (NSGs)](https://docs.microsoft.com/azure/virtual-network/security-overview). Os Registos de Fluxo NSG permitem registar informações de fluxo de 5 tuple sobre todo o tráfego através dos seus NSGs. Os registos de fluxo bruto são escritos numa conta de Armazenamento Azure de onde podem ser processados, analisados, consultados ou exportados conforme necessário.

**A utilização de Registos de Fluxo afeta a minha latência ou desempenho na rede?**

Os dados de registos de fluxo são recolhidos fora da trajetória do tráfego da sua rede, pelo que não afetam a produção ou latência da rede. Pode criar ou eliminar registos de fluxo sem qualquer risco de impacto no desempenho da rede.

**Como uso registos de fluxo NSG com uma conta de armazenamento atrás de uma firewall?**

Para utilizar uma conta de Armazenamento por trás de uma firewall, tem de providenciar uma exceção para que os Serviços De Confiança da Microsoft acedam à sua conta de armazenamento:

- Navegue para a conta de armazenamento digitando o nome da conta de armazenamento na pesquisa global no portal ou na [página contas de armazenamento](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)
- Na secção  **DEFINIÇÕES,**  selecione  **Firewalls e redes virtuais**
- Em **Permitir o acesso a partir de**redes **selecionadas.** Em seguida, em  **exceções,** marque a caixa ao lado de ****Permitir que serviços fidedignos da Microsoft acedam a esta conta de armazenamento****
- Se já estiver selecionada, não é preciso alterar nada.
- Localize o seu NSG alvo na página de visão geral dos [Registos de Fluxo NSG](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) e ative os Registos de Fluxo NSG com a conta de armazenamento acima selecionada.

Pode verificar os registos de armazenamento após alguns minutos; deve ver um carimbo de data/hora atualizado ou um novo ficheiro JSON criado.

**Como uso registos de fluxo NSG com uma conta de armazenamento atrás de um ponto de serviço?**

Os Registos de Fluxo NSG são compatíveis com os pontos finais de serviço sem necessitar de qualquer configuração extra. Consulte o [tutorial sobre como ativar os pontos finais de serviço](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint) na sua rede virtual.

**Qual é a diferença entre as versões de registos de fluxo 1 & 2?**

A versão 2 do Flow Logs introduz o conceito de _Flow State_ & armazena informações sobre bytes e pacotes transmitidos. [Saiba mais](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file)

## <a name="pricing"></a>Preços

Os Registos de Fluxo NSG são cobrados por GB de registos recolhidos e vêm com um nível gratuito de 5 GB/mês por subscrição. Para obter os preços atuais na sua região, consulte a [página de preços do Observador de Rede](https://azure.microsoft.com/pricing/details/network-watcher/).

O armazenamento de registos é cobrado separadamente, consulte [a página de preços do bloco de armazenamento Azure](https://azure.microsoft.com/pricing/details/storage/blobs/) para preços relevantes.
 
