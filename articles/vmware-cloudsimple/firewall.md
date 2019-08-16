---
title: Solução do Azure VMware por CloudSimple-configure as tabelas e regras de firewall
description: Descreve como configurar tabelas e regras de firewall de nuvem privada para restringir o tráfego em sub-redes e VLANs.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c56d469360814cb663b4c5c11689961225eebb2d
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69544672"
---
# <a name="set-up-firewall-tables-and-rules-for-private-clouds"></a>Configurar tabelas e regras de firewall para nuvens privadas

As tabelas de firewall e as regras associadas permitem que você especifique as restrições de tráfego a serem aplicadas a sub-redes e VLANs específicas.

* Uma sub-rede pode ser associada a uma tabela de firewall.
* Uma tabela de firewall pode ser associada a várias sub-redes.

## <a name="add-a-new-firewall-table"></a>Adicionar uma nova tabela de firewall

1. [Acesse o portal do CloudSimple](monitor-activity.md) e selecione **rede** no menu lateral.
2. Selecione **tabelas de firewall**.
3. Selecione **criar tabela de firewall**.

    ![Página VLAN/sub-rede](media/firewall-tables-page.png)

4. Insira um nome para a tabela.
5. Uma regra padrão para a tabela é listada. Clique em **criar nova regra** para criar uma regra adicional. Consulte o procedimento a seguir para obter detalhes.
6. Clique em **concluído** para salvar a tabela de firewall.

## <a name="firewall-rules"></a>Regras de firewall

As regras de firewall determinam como o firewall trata tipos específicos de tráfego. A guia **regras** para uma tabela de firewall selecionada lista todas as regras associadas.

![Tabela de regras de firewall](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>Criar uma regra de firewall

1. Exiba as configurações para criar uma regra de firewall de uma das seguintes maneiras:
    * Clique em **Adicionar regra** ao criar uma tabela de firewall.
    * Selecione uma tabela de firewall específica na página **tabelas de firewall > Rede** e clique em **criar nova regra de firewall**.
2. Configure a regra da seguinte maneira:
    * **Nome**. Dê um nome à regra.
    * **Prioridade**. Atribua uma prioridade à regra. As regras com números inferiores são executadas primeiro.
    * **Tipo de tráfego**. Selecione se a regra é para o tráfego de nuvem privada, Internet ou VPN (sem estado) ou para um endereço IP público (com estado).
    * **Protocolo**. Selecione o protocolo coberto pela regra (TCP, UDP ou qualquer protocolo).
    * **Direção**. Selecione se a regra é para o tráfego de entrada ou de saída. Você deve definir regras separadas para o tráfego de entrada e de saída.
    * **Ação**. Selecione a ação a ser tomada se a regra corresponder (permitir ou negar).
    * **Origem**. Especifique as fontes cobertas pela regra (bloco CIDR, interno ou qualquer fonte).
    * **Intervalo de portas de origem**. Especifique o intervalo de portas sujeitas à regra.
    * **Direção**. Selecione entrada ou saída.
    * **Destino**. Especifique os destinos cobertos pela regra (bloco CIDR, interno ou qualquer fonte).
    * **Intervalo de portas de origem**. Especifique o intervalo de portas sujeitas à regra.

    ![Tabela de firewall-Adicionar regra](media/firewall-rule-create.png)

3. Clique em **concluído** para salvar a regra e adicioná-la à lista de regras para a tabela de firewall.

## <a name="attach-vlanssubnets"></a>Anexar VLANs/sub-redes

Depois de definir uma tabela de firewall, você pode especificar as sub-redes que estão sujeitas às regras na tabela.

1. Na página**tabelas de firewall** de **rede** > , selecione uma tabela de firewall.
2. Abra a guia **VLANs/sub-rede anexadas** .
3. Clique em **anexar a uma VLAN/sub-rede**.
4. Selecione a nuvem privada e a VLAN. O nome de sub-rede e o bloco CIDR associados são mostrados.
5. Clique em **Submit** (Submeter).
