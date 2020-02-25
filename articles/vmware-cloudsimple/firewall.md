---
title: Solução Azure VMware by CloudSimple - Configurar tabelas e regras de firewall
description: Descreve como configurar tabelas e regras de firewall Private Cloud para restringir o tráfego em subredes e VLANs.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 986f4b0da7254ebac3725a704f32af785c72fbcc
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565031"
---
# <a name="set-up-firewall-tables-and-rules-for-private-clouds"></a>Configurar tabelas e regras de firewall para nuvens privadas

As tabelas de firewall e as regras associadas permitem especificar restrições ao tráfego para aplicar a determinadas subredes e VLANs.

* Uma sub-rede pode ser associada a uma mesa de firewall.
* Uma tabela de firewall pode ser associada a várias subredes.

## <a name="add-a-new-firewall-table"></a>Adicione uma nova tabela de firewall

1. [Aceda ao portal CloudSimple](access-cloudsimple-portal.md) e selecione **Rede** no menu lateral.
2. Selecione **Tabelas de Firewall**.
3. Selecione **Criar tabela de firewall**.

    ![Página VLAN/sub-rede](media/firewall-tables-page.png)

4. Insira um nome para a mesa.
5. Uma regra padrão para a tabela está listada. Clique em **Criar Nova Regra** para criar uma regra adicional. Consulte o seguinte procedimento para obter mais detalhes.
6. Clique **em Feito** para salvar a tabela de firewall.

> [!IMPORTANT]
> Você pode criar até duas mesas firewall por Nuvem Privada.

## <a name="firewall-rules"></a>Regras da firewall

As regras da firewall determinam como a firewall trata tipos específicos de tráfego. O separador **Regras** para uma tabela de firewall selecionada lista todas as regras associadas.

![Tabela de regras de firewall](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>Criar uma regra de firewall

1. Mostrar as definições para criar uma regra de firewall de cada uma destas formas:
    * Clique em **Adicionar Regra** ao criar uma tabela de firewall.
    * Selecione uma tabela de firewall específica na página **Network > Firewall Tables** e clique em Criar nova regra de **firewall**.
2. Estabeleça a regra da seguinte forma:
    * **Nome**. Dê um nome à regra.
    * **Prioridade.** Atribuir uma prioridade à regra. As regras com números mais baixos são executadas primeiro.
    * **Tipo de tráfego.** Selecione se a regra é para o tráfego privado de cloud, Internet ou VPN (apátrida) ou para um endereço IP público (apátrida).
    * **Protocolo**. Selecione o protocolo abrangido pela regra (TCP, UDP ou qualquer protocolo).
    * **Direção.** Selecione se a regra é para tráfego de entrada ou saída. Deve definir regras separadas para o tráfego de entrada e saída.
    * **Ação.** Selecione a ação a tomar se a regra corresponder (permitir ou negar).
    * **Fonte.** Especifique as fontes abrangidas pela regra (bloco CIDR, interna ou qualquer fonte).
    * **Gama de porta**de origem. Especifique o leque de portas sujeitas à regra.
    * **Direção.** Selecione entrada ou saída.
    * **Destino**. Especifique os destinos abrangidos pela regra (bloco CIDR, interno ou qualquer fonte).
    * **Gama de porta**de origem. Especifique o leque de portas sujeitas à regra.

    ![Regra de adição de tabela firewall](media/firewall-rule-create.png)

3. Clique em **Feito** para salvar a regra e adicioná-la à lista de regras para a tabela de firewall.

> [!IMPORTANT]
> Cada tabela firewall pode ter até 10 regras de entrada e 20 regras de saída. Estes limites podem ser aumentados [contactando](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)o suporte .

## <a name="attach-vlans-subnet"></a>Anexar VLANs/subredes

Depois de definir uma tabela de firewall, pode especificar as subredes que estão sujeitas às regras da tabela.

1. Na página Tabelas de **Firewall** ** > rede,** selecione uma tabela de firewall.
2. Abra o separador **VLANs/Subnet anexo.**
3. Clique **em ligar a uma VLAN/Subnet**.
4. Selecione a Nuvem Privada e vLAN. O nome da sub-rede associado e o bloco CIDR são mostrados.
5. Clique em **Submeter**.
