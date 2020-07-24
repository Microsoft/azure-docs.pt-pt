---
title: Par no local ambientes para uma nuvem privada
description: Neste tutorial Azure VMware Solution (AVS), cria-se o ExpressRoute Global Reach a espreitar para uma nuvem privada num AVS.
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: a9a002eab3219a0db74062570d31595bfcc0d6a3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101919"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Tutorial: Ambientes de pares no local para uma nuvem privada

O ExpressRoute Global Reach liga o seu ambiente no local às suas nuvens privadas. A ligação ExpressRoute Global Reach é estabelecida entre um circuito expressRoute em nuvem privada e uma ligação ExpressRoute existente aos seus ambientes no local.  Existem instruções para configurar o ExpressRoute Global Reach com Azure CLI e PowerShell, e nós aumentamos os [comandos CLI](../expressroute/expressroute-howto-set-global-reach-cli.md) com detalhes e exemplos específicos para ajudá-lo a configurar o ExpressRoute Global Reach espreitando entre ambientes no local para uma nuvem privada Azure VMware Solution (AVS).   

Antes de permitir a conectividade entre dois circuitos ExpressRoute utilizando o ExpressRoute Global Reach, reveja a documentação sobre como permitir a [conectividade em diferentes subscrições do Azure](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  O circuito ExpressRoute que utiliza quando [configura a rede de nuvem Azure-to-private](tutorial-configure-networking.md) requer que crie e utilize chaves de autorização quando espreita para gateways ExpressRoute ou com outros circuitos ExpressRoute usando o Global Reach. Já terá usado uma chave de autorização do circuito ExpressRoute, e criará uma segunda para espreitar com o circuito ExpressRoute no local.

> [!TIP]
> No contexto destas instruções, o circuito ExpressRoute no local é _o circuito 1,_ e o circuito ExpressRoute em nuvem privada está num circuito de subscrição e de marca _2_. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Aproveite as instruções existentes para gerir os circuitos ExpressRoute e os olhos do ExpressRoute Global Reach
> * Criar uma chave de autorização para _o circuito 2_, o circuito expressRoute em nuvem privada
> * Utilize o CLI Azure numa Cloud Shell na subscrição do _circuito 1_ para permitir o espreitamento do ExpressRoute Global Reach no local para o uso da nuvem privada ExpressRoute Global Reach

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para este tutorial são:
- Uma nuvem privada com o seu circuito ExpressRoute espreitado com uma porta de entrada ExpressRoute numa rede virtual Azure (VNet) – este é o _circuito 2_ na perspetiva dos procedimentos de espreitar.
- Um circuito ExpressRoute separado e funcional usado para ligar ambientes no local ao Azure – este é o _circuito 1_ do ponto de vista dos procedimentos de observação.
- Um bloco de [endereços](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) de rede não sobreposto /29 para o olho do ExpressRoute Global Reach.

## <a name="create-an-expressroute-authorization-key-in-the-avs-private-cloud"></a>Criar uma chave de autorização ExpressRoute na nuvem privada AVS

1. A partir da nuvem privada **Overview**, em Gestão, selecione **Conectividade > ExpressRoute > Solicite uma chave de autorização**.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Selecione conectividade > ExpressRoute > Solicite uma chave de autorização para iniciar um novo pedido":::

2. Introduza o nome para a chave de autorização e selecione **Criar**. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Clique em Criar para criar uma nova chave de autorização.":::

   Uma vez criada, a nova chave aparece na lista de chaves de autorização para a nuvem privada. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Confirme que a nova chave de autorização aparece na lista de chaves para a nuvem privada.":::

3. Tome nota da chave de autorização e do ID ExpressRoute, juntamente com o bloco de endereços /29. Vais usá-los no próximo passo para completar o olhar. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Nuvem privada de pares para as instalações usando a chave de autorização

Agora que criou uma chave de autorização para o circuito expressRoute em nuvem privada, pode perscrutar com o circuito ExpressRoute no local.  O perspero é feito do ponto de vista do circuito ExpressRoute no local utilizando o CLI Azure numa Cloud Shell e iD de recursos e chave de autorização do seu circuito ExpressRoute em nuvem privada, que foi criado nos passos anteriores.

> [!TIP]  
> Para a brevidade na saída do comando Azure CLI, estas instruções podem usar um [ `–query` argumento para executar uma consulta JMESPath apenas para mostrar os resultados necessários](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).


1. Inscreva-se no portal Azure usando a mesma subscrição que o circuito ExpressRoute no local e abra uma Cloud Shell. Deixe a concha como Bash.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Inscreva-se no portal Azure e abra uma Cloud Shell.":::
 
2. Na linha de comando, insira o comando Azure CLI para criar o estorte, utilizando o seu ID de informação e recursos específicos, a chave de autorização e o bloco de rede /29 CIDR. 

   Segue-se um exemplo do comando que irá utilizar e da saída que indica um olhar bem sucedido. O comando exemplo baseia-se no comando utilizado no [passo 3 de "Permitir a conectividade entre circuitos ExpressRoute em diferentes subscrições Azure".](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions)

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Crie o ExpressRoute Global Reach a espreitar com o comando Azure CLI numa Cloud Shell.":::
 
   Você deve agora ser capaz de conectar de ambientes no local para a sua nuvem privada sobre o ExpressRoute Global Reach espreitando.

> [!TIP]
> Pode eliminar o espreguiçadamento que acabou de criar seguindo a [conectividade de desativação entre as](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) instruções das redes no local.


## <a name="next-steps"></a>Passos seguintes

Se planeia utilizar a Extensão de Nuvem Híbrida (HCX) para migrar cargas de trabalho VM para a sua nuvem privada, utilize o procedimento [installar HCX para solução VMware Azure.](hybrid-cloud-extension-installation.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
