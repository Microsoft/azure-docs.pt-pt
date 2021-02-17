---
title: Tutorial - Ambientes de peer on-in para uma nuvem privada
description: Saiba como criar o ExpressRoute Global Reach olhando para uma nuvem privada numa Solução VMware Azure.
ms.topic: tutorial
ms.date: 01/27/2021
ms.openlocfilehash: 04be94849f1cd78ff915ca271845b283c8cfc64c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558818"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Tutorial: Ambientes de pares no local para uma nuvem privada

O ExpressRoute Global Reach liga o seu ambiente no local à sua nuvem privada Azure VMware Solution. A ligação ExpressRoute Global Reach é estabelecida entre o circuito expressRoute em nuvem privada e uma ligação ExpressRoute existente aos seus ambientes no local. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Utilize o portal Azure para permitir o espreitamento do ExpressRoute Global Reach em nuvem no local.


## <a name="before-you-begin"></a>Antes de começar

Antes de permitir a conectividade entre dois circuitos ExpressRoute utilizando o ExpressRoute Global Reach, reveja a documentação sobre como permitir a [conectividade em diferentes subscrições do Azure](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  


## <a name="prerequisites"></a>Pré-requisitos

- Um circuito ExpressRoute separado e funcional usado para ligar ambientes no local a Azure.
- Certifique-se de que todos os gateways, incluindo o serviço do prestador ExpressRoute, suportam o Número do Sistema Autónomo de 4 byte (ASN). A Azure VMware Solution utiliza ASNs públicos de 4 byte para rotas publicitárias.


## <a name="create-an-expressroute-authorization-key-in-the-on-premises-expressroute-circuit"></a>Criar uma chave de autorização ExpressRoute no circuito ExpressRoute no local

1. A partir da lâmina dos **circuitos ExpressRoute,** em Definições, selecione **Autorizações**.

2. Introduza o nome da chave de autorização e **selecione Guardar**.

    :::image type="content" source="media/expressroute-global-reach/start-request-auth-key-on-premises-expressroute.png" alt-text="Selecione Autorizações e introduza o nome para a chave de autorização.":::
  
     Uma vez criada, a nova chave aparece na lista de chaves de autorização para o circuito.
 
 4. Tome nota da chave de autorização e do ID ExpressRoute. Vais usá-los no próximo passo para completar o olhar.
 
 
 ## <a name="peer-private-cloud-to-on-premises"></a>Nuvem privada de pares para as instalações

1. A partir da nuvem privada **Overview**, under Manage, **selecione Conectividade > ExpressRoute Global Reach > Add**.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="A partir do menu, selecione Conectividade, o separador ExpressRoute Global Reach e, em seguida, Adicione.":::

2. Introduza o ID ExpressRoute e a chave de autorização criada na secção anterior.

    :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="Introduza o ID ExpressRoute e a chave de autorização e, em seguida, selecione Criar.":::

3. Selecione **Criar**. A nova ligação mostra-se na lista de ligações em nuvem no local.  

>[!TIP]
>Pode eliminar ou desligar uma ligação da lista selecionando **Mais**.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Desligar ou apagar uma ligação no local":::


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a permitir o olhar para o ExpressRoute Global Reach no local. 

Continue até ao próximo tutorial para aprender a implementar e configurar a solução VMware HCX para a sua nuvem privada Azure VMware Solution.

> [!div class="nextstepaction"]
> [Implementar e configurar o VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
