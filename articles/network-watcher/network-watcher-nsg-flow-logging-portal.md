---
title: Registar o fluxo de tráfego de rede de/para uma VM – tutorial – portal do Azure | Microsoft Docs
description: Saiba como registar o fluxo de tráfego de rede de/para uma VM com a capacidade dos registos de fluxo do NSG do Observador de Rede.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to log the network traffic to and from a VM so I can analyze it for anomalies.
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 124ac79971ca2ee1e7b5ad085a73aceb8f5d8841
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57191395"
---
# <a name="tutorial-log-network-traffic-to-and-from-a-virtual-machine-using-the-azure-portal"></a>Tutorial: Registar tráfego de rede de e para uma máquina virtual utilizando o portal do Azure

Um grupo de segurança de rede (NSG) permite filtrar o tráfego de entrada e o tráfego de saída numa máquina virtual (VM). Pode registar o tráfego de rede que passa através de um NSG com a capacidade dos registos de fluxo do NSG do Observador de Rede. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma VM com um grupo de segurança de rede
> * Ativar o Observador de Rede e registar o fornecedor do Microsoft.Insights
> * Ativar um registo de fluxo de tráfego para um NSG, com a capacidade de registo de fluxo do NSG do Observador de Rede
> * Transferir dados registados
> * Ver dados registados

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-vm"></a>Criar uma VM

1. Selecione **+ Criar um recurso**, disponível no canto superior esquerdo do Portal do Azure.
2. Selecione **computação**e, em seguida, selecione **do Windows Server 2016 Datacenter** ou uma versão de **Ubuntu Server**.
3. Introduza ou selecione as seguintes informações, aceite as predefinições para as restantes definições e, em seguida, selecione **OK**:

    |Definição|Valor|
    |---|---|
    |Name|myVm|
    |Nome de utilizador| Introduza um nome de utilizador à sua escolha.|
    |Palavra-passe| Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Subscrição| Selecione a sua subscrição.|
    |Grupo de recursos| Selecione **Criar novo** e introduza **myResourceGroup**.|
    |Localização| Selecione **E.U.A. Leste**|

4. Escolha um tamanho para a VM e selecione **Selecionar**.
5. Em **Definições**, aceite todas as predefinições e selecione **OK**.
6. Em **Criar** no **Resumo**, selecione **Criar** para iniciar a implementação da VM. A implementação da VM demora alguns minutos. Aguarde que a VM conclua a implementação antes de continuar com os restantes passos.

A criação da VM demora alguns minutos. Não continue com os restantes passos até que a VM tenha sido totalmente criada. Enquanto o portal cria a VM, também cria um grupo de segurança de rede com o nome **myVm nsg** e associa-o à interface de rede da VM.

## <a name="enable-network-watcher"></a>Ativar o Observador de Rede

Se já tiver um observador de rede ativado na região EUA Leste, avance para [Registar o fornecedor do Insights](#register-insights-provider).

1. No portal, selecione **Todos os serviços**. Na caixa **Filtro**, introduza *Observador de Rede*. Quando a opção **Observador de Rede** aparecer nos resultados, selecione-a.
2. Selecione **Regiões**, para expandir e, em seguida, selecione **...** à direita de **E.U.A. Leste**, conforme apresentado na seguinte imagem:

    ![Ativar o Observador de Rede](./media/network-watcher-nsg-flow-logging-portal/enable-network-watcher.png)

3. Selecione **Ativar Observador de Rede**.

## <a name="register-insights-provider"></a>Registar o fornecedor do Insights

O registo de fluxo do NSG precisa do fornecedor do **Microsoft.Insights**. Para registar o fornecedor, conclua os seguintes passos:

1. No canto superior esquerdo do portal, selecione **Todos os serviços**. Na caixa Filtro, escreva *Subscrições*. Quando o termo **Subscrições** aparecer nos resultados da pesquisa, selecione-o.
2. Na lista de subscrições, selecione a subscrição que pretende ativar para o fornecedor.
3. Selecione **Fornecedores de recursos**, em **DEFINIÇÕES**.
4. Confirme que o **ESTADO** do fornecedor do **microsoft.insights** é **Registado**, conforme mostrado na imagem que se segue. Se o estado for **Não registado**, selecione **Registar**, à direita do fornecedor.

    ![Registar o fornecedor](./media/network-watcher-nsg-flow-logging-portal/register-provider.png)

## <a name="enable-nsg-flow-log"></a>Ativar o registo de fluxo do NSG

1. Os dados de registo de fluxo do NSG são escritos numa conta de Armazenamento do Microsoft Azure. Para criar uma conta de Armazenamento do Microsoft Azure, selecione **+ Criar um recurso** no canto superior esquerdo do portal.
2. Selecione **Armazenamento** e, em seguida, **Conta de Armazenamento – blob, ficheiro, tabela, fila**.
3. Introduza ou selecione as seguintes informações, aceite as predefinições restantes e selecione **Criar**.

    | Definição        | Valor                                                        |
    | ---            | ---   |
    | Name           | 3 a 24 carateres de comprimento, só pode conter letras minúsculas e números e tem de ser exclusivo em todas as contas de Armazenamento do Microsoft Azure.                                                               |
    | Localização       | Selecione **E.U.A. Leste**                                           |
    | Grupo de recursos | Selecione **Utilizar existente** e, em seguida, **myResourceGroup** |

    A criação da conta de armazenamento pode demorar cerca de um minuto. Não continue com os restantes passos até que a conta de armazenamento seja criada. Se utilizar uma conta de armazenamento existente em vez de criar uma nova, confirme que seleciona uma conta de armazenamento que tenha a opção **Todas as redes** (predefinição) selecionada para **Firewalls e redes virtuais**, em **DEFINIÇÕES** da conta de armazenamento.
4. No canto superior esquerdo do portal, selecione **Todos os serviços**. Na **caixa Filtro**, escreva *Observador de Rede*. Quando o **Observador de Rede** aparecer nos resultados de pesquisa, selecione-o.
5. Em **REGISTOS**, selecione **Registos de fluxo do NSG**, conforme mostrado na imagem seguinte:

    ![NSGs](./media/network-watcher-nsg-flow-logging-portal/nsgs.png)

6. Na lista de NSGs, selecione o NSG denominado **myVm nsg**.
7. Em **Definições dos registos de fluxo**, selecione **Ligado**.
8. Selecione a versão de registo do fluxo. Versão 2 contém estatísticas de sessão de fluxo (Bytes e pacotes)

   ![Selecione a versão de registos de fluxo](./media/network-watcher-nsg-flow-logging-portal/select-flow-log-version.png)

9. Selecione a conta de armazenamento que criou no passo 3.
10. Defina **Retenção (dias)** como 5 e, em seguida, selecione **Guardar**.

## <a name="download-flow-log"></a>Transferir o registo de fluxo

1. No Observador de Rede, no portal, selecione **Registos de fluxo do NSG** em **REGISTOS**.
2. Selecione **Pode transferir registos de fluxo a partir de contas de armazenamento configuradas**, conforme mostrado na imagem seguinte:

   ![Transferir os registos de fluxo](./media/network-watcher-nsg-flow-logging-portal/download-flow-logs.png)

3. Selecione a conta de armazenamento que configurou no passo 2 da secção [Ativar o registo de fluxo do NSG](#enable-nsg-flow-log).
4. Sob **serviço Blob**, selecione **Blobs**e, em seguida, selecione o **insights-logs-networksecuritygroupflowevent** contentor.
5. No contentor, navegue na hierarquia de pasta até chegar a um ficheiro PT1H.json, conforme mostrado na imagem que se segue. Ficheiros de registo são escritos para uma hierarquia de pastas que se segue a seguinte convenção de nomenclatura: https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/ RESOURCEGROUPS/{resourceGroupName}/Providers/Microsoft.Network/NETWORKSECURITYGROUPS/{nsgName}/y={Year}/m={Month}/d={Day}/h={Hour}/m=00/macAddress={macAddress}/PT1H.JSON

   ![Registo de fluxo](./media/network-watcher-nsg-flow-logging-portal/log-file.png)

6. Selecione **...** à direita do ficheiro PT1H.json e, em seguida, **Transferir**.

## <a name="view-flow-log"></a>Ver o registo de fluxo

O seguinte json é um exemplo do que verá no ficheiro PT1H.json para cada fluxo para o qual foram registados dados:

### <a name="version-1-flow-log-event"></a>Registo de eventos de fluxo de versão 1
```json
{
    "time": "2018-05-01T15:00:02.1713710Z",
    "systemId": "<Id>",
    "category": "NetworkSecurityGroupFlowEvent",
    "resourceId": "/SUBSCRIPTIONS/<Id>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MYVM-NSG",
    "operationName": "NetworkSecurityGroupFlowEvents",
    "properties": {
        "Version": 1,
        "flows": [
            {
                "rule": "UserRule_default-allow-rdp",
                "flows": [
                    {
                        "mac": "000D3A170C69",
                        "flowTuples": [
                            "1525186745,192.168.1.4,10.0.0.4,55960,3389,T,I,A"
                        ]
                    }
                ]
            }
        ]
    }
}
```
### <a name="version-2-flow-log-event"></a>Registo de eventos de fluxo de versão 2
```json
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
}
```

O valor de **mac** na saída anterior é o endereço MAC da interface de rede que foi criada quando a VM foi criada. As informações separadas por vírgulas para **flowTuples** são as seguintes:

| Dados de exemplo | O que representam os dados   | Explicação                                                                              |
| ---          | ---                    | ---                                                                                      |
| 1542110377   | Carimbo de data/hora             | O carimbo de data/hora quando o fluxo ocorreu, no formato ÉPOCA UNIX. No exemplo anterior, a data é convertida para 1 de maio de 2018 às 14:59:05 GMT.                                                                                    |
| 10.0.0.4  | Endereço IP de origem      | O endereço IP de origem do qual teve origem o fluxo. 10.0.0.4 é o endereço IP privado da VM que criou em [Criar uma VM](#create-a-vm).
| 13.67.143.118     | Endereço IP de destino | O endereço IP de destino para o qual se destinava o fluxo.                                                                                  |
| 44931        | Porta de origem            | A porta de origem da qual teve origem o fluxo.                                           |
| 443         | Porta de destino       | A porta de destino à qual se destinava o fluxo. Uma vez que o tráfego foi destinado à porta 443, a regra com o nome **UserRule_default-allow-rdp**, no registo de ficheiros processados o fluxo.                                                |
| T            | Protocolo               | Indica se o protocolo do fluxo era TCP (T) ou UDP (U).                                  |
| O            | Direção              | Indica se o tráfego era de entrada (I) ou de saída (O).                                     |
| A            | Ação                 | Indica se o tráfego era permitido (I) ou proibido (O).  
| C            | Estado do fluxo de **apenas a versão 2** | Captura o estado do fluxo. Estados possíveis **B**: Iniciar, quando um fluxo é criado. Estatísticas não são fornecidas. **C**: Continuar para um fluxo em curso. As estatísticas são fornecidas em intervalos de 5 minutos. **E**: Fim, quando um fluxo é terminado. As estatísticas são fornecidas. |
| 30 | Pacotes enviados - origem para destino **apenas 2 da versão** | O número total de pacotes TCP ou UDP enviados da origem para destino, desde a última atualização. |
| 16978 | Bytes enviados - origem para destino **apenas 2 da versão** | O número total de bytes de pacotes TCP ou UDP enviados da origem para destino, desde a última atualização. Bytes de pacote incluem o cabeçalho de pacote e o payload. | 
| 24 | Pacotes enviados - destino para origem **apenas 2 da versão** | O número total de pacotes TCP ou UDP enviadas do destino para origem, desde a última atualização. |
| 14008| Bytes enviados - destino para origem **apenas 2 da versão** | O número total de bytes de pacotes TCP e UDP enviadas do destino para origem, desde a última atualização. Bytes de pacote incluem o cabeçalho de pacote e o payload.| |

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a ativar o registo de fluxo do NSG para um NSG. Também aprendeu como transferir e ver os dados registados num ficheiro. Os dados não processados no ficheiro json podem ser difíceis de interpretar. Para visualizar os dados, pode utilizar a [análise de tráfego](traffic-analytics.md) do Observador de Rede, o Microsoft [Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md) e outras ferramentas.
