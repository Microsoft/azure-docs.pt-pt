---
title: Como utilizar a funcionalidade IP pública na Solução VMware Azure
description: Este artigo explica como utilizar a funcionalidade IP pública em Azure Virtual WAN.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 794e24e531d464adf58d5a06b5a411ada18c4a60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023661"
---
# <a name="how-to-use-the-public-ip-functionality-in-azure-vmware-solution"></a>Como utilizar a funcionalidade IP pública na Solução VMware Azure

O IP público é uma nova funcionalidade na conectividade Azure VMware Solution. Torna os recursos, tais como servidores web, máquinas virtuais (VMs) e anfitriões acessíveis através de uma rede pública. 

Permite o acesso público à Internet de duas formas. 

- As aplicações podem ser hospedadas e publicadas no equilibrador de carga do Gateway de Aplicação para tráfego HTTP/HTTPS.
- Publicado através de funcionalidades de IP públicas em Azure Virtual WAN.

Como parte da implementação de nuvem privada Azure VMware Solution, ao permitir a funcionalidade IP pública, os componentes necessários com automação são criados e habilitados:

-  WAN Virtual

-  Hub WAN virtual com conectividade ExpressRoute

-  Serviços Azure Firewall com IP público

Este artigo detalha como pode utilizar a funcionalidade IP pública em Virtual WAN.

## <a name="prerequisites"></a>Pré-requisitos

- Ambiente de solução Azure VMware
- Um webserver em execução em ambiente Azure VMware Solution.
- Uma nova gama de IP não sobreposta para a implantação do hub Virtual WAN, tipicamente a `/24` .

## <a name="reference-architecture"></a>Arquitetura de referência

:::image type="content" source="media/public-ip-usage/public-ip-architecture-diagram.png" alt-text="Diagrama de arquitetura IP público" border="false" lightbox="media/public-ip-usage/public-ip-architecture-diagram.png":::

O diagrama de arquitetura mostra um servidor web hospedado no ambiente Azure VMware Solution e configurado com endereços IP privados RFC1918.  O serviço web é disponibilizado para a internet através da funcionalidade IP público Virtual WAN.  O IP público é tipicamente um destino NAT traduzido em Azure Firewall. Com as regras do DNAT, a política de firewall traduz pedidos de endereço IP públicos para um endereço privado (webserver) com uma porta.

Os pedidos dos utilizadores atingem a firewall num IP público que, por sua vez, é traduzido para IP privado usando regras de DNAT no Azure Firewall. A firewall verifica a tabela NAT, e se o pedido corresponder a uma entrada, encaminha o tráfego para o endereço traduzido e porta no ambiente Azure VMware Solution.

O servidor web recebe o pedido e responde com as informações ou páginas solicitadas para a firewall, e em seguida, a firewall reencaminha as informações para o utilizador no endereço IP público.

## <a name="test-case"></a>Caso de teste
Neste cenário, publicará o webserver do IIS na internet. Utilize a função IP pública na Solução VMware Azure para publicar o website num endereço IP público.  Também configurará as regras da NAT na firewall e acederá ao recurso Azure VMware Solution (VMs com um servidor web) com IP público.

>[!TIP]
>Para ativar o tráfego de saídas, tem de definir a configuração de segurança > tráfego de Internet para **a Azure Firewall**.

## <a name="deploy-virtual-wan"></a>Implementar a WAN Virtual.

1. Inscreva-se no portal Azure e, em seguida, procure e selecione **Azure VMware Solution**.

1. Selecione a nuvem privada Azure VMware Solution.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-resource.png" alt-text="Screenshot da nuvem privada Azure VMware Solution." border="true" lightbox="media/public-ip-usage/avs-private-cloud-resource.png":::

1. Em **Gestão**, selecione **Conectividade**.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-manage-menu.png" alt-text="Screenshot da secção de Conectividade." border="true" lightbox="media/public-ip-usage/avs-private-cloud-manage-menu.png":::

1. Selecione o **separador IP público** e, em seguida, selecione **Configurar**.

   :::image type="content" source="media/public-ip-usage/connectivity-public-ip-tab.png" alt-text="Screenshot que mostra onde começar a configurar o IP público" border="true" lightbox="media/public-ip-usage/connectivity-public-ip-tab.png":::

1. Aceite os valores predefinidos ou altere-os e, em seguida, **selecione Criar**.

   - Grupo de recursos VIRTUAL WAN

   - Nome WAN virtual

   - Bloco de endereços de hub virtual (utilizando uma nova gama IP não sobreposta)

   - Número de IPs públicos (1-100)

Leva cerca de uma hora para completar a colocação de todos os componentes. Esta implementação só tem de ocorrer uma vez para suportar todos os futuros IPs públicos para este ambiente Azure VMware Solution.  

>[!TIP]
>Pode monitorizar o estado a partir da área de **Notificação.** 

## <a name="view-and-add-public-ip-addresses"></a>Ver e adicionar endereços IP públicos

Podemos verificar e adicionar mais endereços IP públicos seguindo os passos abaixo.

1. No portal Azure, procure e selecione **Firewall**.

1. Selecione uma firewall implantada e, em seguida, **selecione Visit Azure Firewall Manager para configurar e gerir esta firewall**.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="Screenshot que mostra a opção de configurar e gerir a firewall" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. Selecione **centros virtuais seguros** e, a partir da lista, selecione um hub virtual.

   :::image type="content" source="media/public-ip-usage/select-virtual-hub.png" alt-text="Screenshot do Gestor de Firewall" lightbox="media/public-ip-usage/select-virtual-hub.png":::

1. Na página do hub virtual, selecione **a configuração IP pública** e para adicionar mais endereço IP público e, em seguida, selecione **Adicionar**. 

   :::image type="content" source="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png" alt-text="Screenshot de como adicionar uma configuração IP pública no Gestor de Firewall" border="true" lightbox="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png":::

1. Forneça o número de IPs necessários e selecione **Adicionar**.

   :::image type="content" source="media/public-ip-usage/add-number-of-ip-addresses-required.png" alt-text="Screenshot para adicionar um número especificado de configurações IP públicas" border="true":::


## <a name="create-firewall-policies"></a>Criar políticas de firewall

Uma vez implantados todos os componentes, pode vê-los no grupo de Recursos adicionados. O próximo passo é adicionar uma política de firewall.

1. No portal Azure, procure e selecione **Firewall**.

1. Selecione uma firewall implantada e, em seguida, **selecione Visit Azure Firewall Manager para configurar e gerir esta firewall**.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="Screenshot que mostra a opção de configurar e gerir a firewall" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. Selecione **Azure Firewall Policies** e, em seguida, selecione **Create Azure Firewall Policy**.

   :::image type="content" source="media/public-ip-usage/create-firewall-policy.png" alt-text="Screenshot de como criar uma política de firewall no Gestor de Firewall" border="true" lightbox="media/public-ip-usage/create-firewall-policy.png":::

1. No **separador Básicos,** forneça os detalhes necessários e selecione **Seguinte: Definições DNS**. 

1. No separador **DNS,** **selecione Desativar** e, em seguida, selecione **Seguinte: Regras**.

1. **Selecione Adicione uma coleção de regras,** forneça os detalhes abaixo e selecione **Adicionar** e, em seguida, selecione **Next: Threat intelligence**.

   -  Name
   -  Tipo de recolha de regras - DNAT
   -  Prioridade
   -  Ação de recolha de regras – Permitir
   -  Nome da regra
   -  Fonte Tipo- **IPaddress**
   -  Fonte - **\***
   -  Protocolo – **TCP**
   -  Porto de destino – **80**
   -  Tipo de destino – **Endereço IP**
   -  Destino – **Endereço IP Público**
   -  Endereço traduzido - Endereço IP privado do **Azure VMware Solution Web Server**
   -  Porta traduzida - **Porta Azure VMware Solution Web Server**

1. Deixe o valor predefinido e, em seguida, selecione **Seguinte: Hubs**.

1. Selecione **o centro virtual Associado**.

1. Selecione um hub da lista e **selecione Adicionar**.

   :::image type="content" source="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png" alt-text="Screenshot que mostra os hubs selecionados que serão convertidos para Secured Virtual Hubs." border="true" lightbox="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png":::

1. Selecione **Seguinte: Tags**. 

1. (Opcional) Crie pares de nomes e valor para categorizar os seus recursos. 

1. Selecione **Seguinte: Rever + criar** e, em seguida, selecionar **Criar**.

## <a name="limitations"></a>Limitações

Pode ter 100 IPs públicos por nuvem privada.

## <a name="next-steps"></a>Passos seguintes

Agora que cobriu como utilizar a funcionalidade IP pública na Solução VMware Azure, talvez queira saber mais sobre:

- Utilização de endereços IP públicos com [Azure Virtual WAN](../virtual-wan/virtual-wan-about.md).
- [Criação de um túnel IPSec para a Solução VMware Azure](create-ipsec-tunnel.md).
