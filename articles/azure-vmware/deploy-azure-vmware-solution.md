---
title: Implementar e configurar a solução VMware Azure
description: Saiba como utilizar as informações recolhidas na fase de planeamento para implementar a nuvem privada Azure VMware Solution.
ms.topic: tutorial
ms.author: tredavis
ms.date: 11/09/2020
ms.openlocfilehash: 264ad99b21150f391c367eba2da31f0d08f4ab08
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94336340"
---
# <a name="deploy-and-configure-azure-vmware-solution"></a>Implementar e configurar a solução VMware Azure

Neste artigo, utilizará as informações da secção de [planeamento](production-ready-deployment-steps.md) para implementar a Solução VMware Azure. Se ainda não definiu a informação, volte à [secção de planeamento](production-ready-deployment-steps.md) antes de continuar.

## <a name="register-the-resource-provider"></a>Registar o fornecedor de recursos

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="deploy-azure-vmware-solution"></a>Implementar o Azure VMware Solution

Utilize as informações recolhidas no artigo [de implementação do Planeamento da Solução VMware Azure:](production-ready-deployment-steps.md)

>[!NOTE]
>Para implementar a Solução Azure VMware, tem de estar ao nível mínimo de contribuinte na subscrição.

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

>[!NOTE]
>Para uma visão geral deste passo, consulte a [Solução Azure VMware: Deployment](https://www.youtube.com/embed/1JLB3L2WDWI) video. 

## <a name="create-the-jump-box"></a>Criar a caixa de salto

>[!IMPORTANT]
>Se deixou a opção **Rede Virtual** em branco durante o passo inicial de provisionamento no ecrã Criar uma **Nuvem Privada,** preencha a rede de configuração para o seu tutorial [de nuvem privada VMware](tutorial-configure-networking.md) **antes** de prosseguir com esta secção.  

Depois de implementar a Solução VMware Azure, irá criar a caixa de salto da rede virtual que se conecta ao vCenter e ao NSX. Depois de configurar os circuitos ExpressRoute e ExpressRoute Global Reach, a caixa de salto não é necessária.  Mas é útil chegar ao vCenter e NSX na sua Solução Azure VMware.  

:::image type="content" source="media/pre-deployment/jump-box-diagram.png" alt-text="Crie a caixa de salto Azure VMware Solution" border="false" lightbox="media/pre-deployment/jump-box-diagram.png":::

Para criar uma máquina virtual (VM) na rede virtual que [identificou ou criou como parte do processo de implantação,](production-ready-deployment-steps.md#azure-virtual-network-to-attach-azure-vmware-solution)siga estas instruções: 

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-a-virtual-network-with-expressroute"></a>Ligue-se a uma rede virtual com o ExpressRoute

Se não definiu uma rede virtual no passo de implementação e a sua intenção é ligar o ExpressRoute da Azure VMware Solution a um Gateway ExpressRoute existente, siga os passos abaixo.

Se já definiu uma rede virtual no ecrã de implementação em Azure, então salte para a secção seguinte.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]

## <a name="verify-network-routes-advertised"></a>Verificar as rotas de rede anunciadas

A caixa de salto encontra-se na rede virtual onde a Azure VMware Solution se conecta através do seu circuito ExpressRoute.  Em Azure, vá à interface de rede da caixa de salto e [veja as rotas eficazes.](../virtual-network/manage-route-table.md#view-effective-routes)

Na lista de rotas eficazes, deverá ver as redes criadas como parte da implementação da Solução VMware Azure. Você verá várias redes que foram derivadas da [ `/22` rede que definiu](production-ready-deployment-steps.md#ip-address-segment) durante o [passo de implementação](#deploy-azure-vmware-solution) mais cedo neste artigo.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-effective-routes.png" alt-text="Verifique as rotas de rede anunciadas da Solução VMware Azure para a Rede Virtual Azure" lightbox="media/pre-deployment/azure-vmware-solution-effective-routes.png":::

Neste exemplo, a rede 10.74.72.0/22 foi a entrada durante a implantação deriva das redes /24.  Se vir algo semelhante, pode ligar-se ao vCenter na Solução VMware Azure.

## <a name="connect-and-sign-in-to-vcenter-and-nsx-t"></a>Conecte-se e inscreva-se no vCenter e no NSX-T

Inicie sessão na caixa de salto que criou no passo anterior. Uma vez iniciado o login, abra um navegador web e navegue para e inicie sessão na consola de administração vCenter e NSX-T.  

Pode identificar os endereços IP e credenciais ip da consola de administração VSX-T no portal Azure.  Selecione a sua nuvem privada e, em seguida, na **vista geral,** selecione **Identidade > Padrão**. 

## <a name="create-a-network-segment-on-azure-vmware-solution"></a>Criar um segmento de rede na Solução VMware Azure

Utiliza o NSX-T para criar novos segmentos de rede no seu ambiente Azure VMware Solution.  Definiu a(s) rede(s) que pretende criar na [secção de planeamento](production-ready-deployment-steps.md).  Se ainda não os definiu, volte para a [secção de planeamento](production-ready-deployment-steps.md) antes de prosseguir.

>[!IMPORTANT]
>Certifique-se de que o bloco de endereços de rede CIDR que definiu não se sobrepõe a nada nos ambientes Azure ou no local.  

Siga o [segmento de rede Criar NSX-T no tutorial Azure VMware Solution](tutorial-nsx-t-network-segment.md) para criar um segmento de rede NSX-T na Solução VMware Azure.

## <a name="verify-advertised-nsx-t-segment"></a>Verifique o segmento NSX-T anunciado

Volte para as rotas de rede Verificar o passo [anunciado.](#verify-network-routes-advertised) Verá uma rota adicional na lista que representa o(s) segmento(s) de rede que criou no passo anterior.  

Para máquinas virtuais, irá atribuir o segmento(s) que criou no [segmento de rede criar no passo Azure VMware Solution.](#create-a-network-segment-on-azure-vmware-solution)  

Como o DNS é necessário, identifique o servidor DNS que pretende utilizar.  

- Se tiver o ExpressRoute Global Reach configurado, utilize qualquer servidor DNS que utilizaria no local.  
- Se tiver um servidor DNS em Azure, use-o.  
- Se não tiver nenhum dos dois, use o servidor DNS que a sua caixa de salto está a usar.

>[!NOTE]
>Este passo é identificar o servidor DNS, e não são feitas configurações neste passo.

## <a name="optional-provide-dhcp-services-to-nsx-t-network-segment"></a>(Opcional) Fornecer serviços DHCP para o segmento de rede NSX-T

Se pretender utilizar o DHCP no seu segmento NSX-T, continue com esta secção. Caso contrário, salte para o Add a VM na secção [de segmento de rede NSX-T.](#add-a-vm-on-the-nsx-t-network-segment)  

Agora que criou o seu segmento de rede NSX-T, pode criar e gerir o DHCP na Azure VMware Solution de duas formas:

* Se estiver a utilizar o NSX-T para hospedar o seu servidor DHCP, terá de [criar um servidor DHCP](manage-dhcp.md#create-a-dhcp-server) e [retransmitir para esse servidor.](manage-dhcp.md#create-dhcp-relay-service) 
* Se estiver a utilizar um servidor DHCP externo de terceiros na sua rede, terá de criar um serviço de [retransmissão DHCP](manage-dhcp.md#create-dhcp-relay-service).  Para esta opção, [faça apenas a configuração do retransmissor](manage-dhcp.md#create-dhcp-relay-service).


## <a name="add-a-vm-on-the-nsx-t-network-segment"></a>Adicione um VM no segmento de rede NSX-T

No seu Azure VMware Solution vCenter, implemente um VM e use-o para verificar a conectividade da sua rede de solução VMware Azure para:

- A internet
- Redes Virtuais do Azure
- No local.  

Desdobre o VM como faria em qualquer ambiente vSphere.  Fixe o VM a um dos segmentos de rede que criou anteriormente em NSX-T.  

>[!NOTE]
>Se configurar um servidor DHCP, obtém a configuração da rede para o VM a partir dele (não se esqueça de configurar o âmbito).  Se vai configurar estáticamente, então configuure-se como normalmente faria.

## <a name="test-the-nsx-t-segment-connectivity"></a>Teste a conectividade do segmento NSX-T

Inicie sessão no VM criado no passo anterior e verifique a conectividade;

1. Ping um IP na Internet.
2. Aceda a um site da Internet através de um navegador web.
3. Ping a caixa de salto que fica na Rede Virtual Azure.

>[!IMPORTANT]
>Neste momento, a Azure VMware Solution está em funcionamento, e você estabeleceu com sucesso conectividade de e para a Rede Virtual Azure e a internet.

## <a name="next-steps"></a>Passos seguintes

Na secção seguinte, irá ligar a Solução Azure VMware à sua rede no local via ExpressRoute.
> [!div class="nextstepaction"]
> [Ligue a Solução Azure VMware ao seu ambiente no local](azure-vmware-solution-on-premises.md)
