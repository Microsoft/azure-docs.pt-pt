---
title: Azure VMware Solution by CloudSimple - Configurar tabelas e regras de firewall
description: Descreve como configurar tabelas e regras de firewall Private Cloud para restringir o tráfego em sub-redes e VLANs.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 32dcfb0c6cd26b5f34afad328db8f383fa1c2a6f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895636"
---
# <a name="set-up-firewall-tables-and-rules-for-private-clouds"></a>Configurar tabelas e regras de firewall para Nuvens Privadas

As tabelas de firewall e as regras associadas permitem especificar restrições ao tráfego para aplicar a sub-redes e VLANs específicos.

* Uma sub-rede pode ser associada a uma tabela de firewall.
* Uma tabela de firewall pode ser associada a várias sub-redes.

## <a name="add-a-new-firewall-table"></a>Adicione uma nova tabela de firewall

1. [Aceda ao portal CloudSimple](access-cloudsimple-portal.md) e selecione **Rede** no menu lateral.
2. Selecione **Tabelas de Firewall**.
3. Selecione **Criar tabela de firewall**.

    ![Página VLAN/sub-rede](media/firewall-tables-page.png)

4. Insira um nome para a mesa.
5. Uma regra padrão para a tabela está listada. Clique **em Criar Nova Regra** para criar uma regra adicional. Consulte o seguinte procedimento para mais detalhes.
6. Clique **em Fazer** para guardar a mesa de firewall.

> [!IMPORTANT]
> Pode criar até duas tabelas firewall por Nuvem Privada.

## <a name="firewall-rules"></a>Regras da firewall

As regras de firewall determinam como a firewall trata tipos específicos de tráfego. O separador **Regras** para uma tabela de firewall selecionada lista todas as regras associadas.

![Tabela de regras de firewall](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>Criar uma regra de firewall

1. Mostrar as definições para criar uma regra de firewall de qualquer uma destas formas:
    * Clique **em Adicionar Regra** ao criar uma tabela de firewall.
    * Selecione uma tabela de firewall específica na página **'Tabelas de firewall' 'Rede >'** e clique em **Criar nova regra de firewall**.
2. Configurar a regra da seguinte forma:
    * **Nome**. Dê um nome à regra.
    * **Prioridade**. Atribua uma prioridade à regra. As regras com números mais baixos são executadas primeiro.
    * **Tipo de tráfego.** Selecione se a regra é para o tráfego Private Cloud, Internet ou VPN (apátrida) ou para um endereço IP público (stateful).
    * **Protocolo**. Selecione o protocolo abrangido pela regra (TCP, UDP ou qualquer protocolo).
    * **Direção**. Selecione se a regra é para o tráfego de entrada ou saída. Deve definir regras separadas para o tráfego de entrada e saída.
    * **Ação**. Selecione a ação a tomar se a regra corresponder (permitir ou negar).
    * **Fonte**. Especificar as fontes abrangidas pela regra (bloco CIDR, interno ou qualquer fonte).
    * **Gama de porta de origem**. Especificar o alcance das portas sujeitas à regra.
    * **Direção**. Selecione entrada ou saída.
    * **Destino**. Especificar os destinos abrangidos pela regra (bloco CIDR, interno ou qualquer fonte).
    * **Gama de porta de origem**. Especificar o alcance das portas sujeitas à regra.

    ![Regra de adicionar tabela de firewall](media/firewall-rule-create.png)

3. Clique **em Fazer** para guardar a regra e adicioná-la à lista de regras para a tabela de firewall.

> [!IMPORTANT]
> Cada tabela Firewall pode ter até 10 regras de entrada e 20 regras de saída. Estes limites podem ser aumentados [através do contacto com o suporte.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="attach-vlanssubnets"></a><a name="attach-vlans-subnet"></a>Anexar VLANs/sub-redes

Depois de definir uma tabela de firewall, pode especificar as sub-redes que estão sujeitas às regras da tabela.

1. Na   >  página **'Tabelas de Firewall'** de rede, selecione uma tabela de firewall.
2. Abra o **separador VLANs/Sub-rede anexado.**
3. Clique **em Anexar a uma VLAN/Sub-rede**.
4. Selecione a Nuvem Privada e VLAN. O nome da sub-rede associada e o bloco CIDR são mostrados.
5. Clique **em Submeter.**
