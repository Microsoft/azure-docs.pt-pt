---
title: Tutorial - Ambientes de peer on-in para uma nuvem privada
description: Saiba como criar o ExpressRoute Global Reach olhando para uma nuvem privada numa Solução VMware Azure.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 4d10972a693f7c4c3ae25a5bc986f6c15e978294
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912502"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Tutorial: Ambientes de pares no local para uma nuvem privada

O ExpressRoute Global Reach liga o seu ambiente no local à sua nuvem privada Azure VMware Solution. A ligação ExpressRoute Global Reach é estabelecida entre o circuito expressRoute em nuvem privada e uma ligação ExpressRoute existente aos seus ambientes no local. 

O circuito ExpressRoute que utiliza quando [configura a rede de nuvem Azure-to-private](tutorial-configure-networking.md) requer que crie e utilize chaves de autorização.  Já terá usado uma chave de autorização do circuito ExpressRoute, e neste tutorial, criará uma segunda para espreitar com o circuito ExpressRoute no local.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma segunda chave de autorização para _o circuito 2_ , o circuito expressRoute em nuvem privada
> * Utilize o [portal Azure](#azure-portal-method) ou o [CLI Azure num método Cloud Shell](#azure-cli-in-a-cloud-shell-method) na subscrição do circuito _1_ para permitir o espreitamento do ExpressRoute Global Reach nas instalações para a nuvem privada ExpressRoute Global Reach


## <a name="before-you-begin"></a>Antes de começar

Antes de permitir a conectividade entre dois circuitos ExpressRoute utilizando o ExpressRoute Global Reach, reveja a documentação sobre como permitir a [conectividade em diferentes subscrições do Azure](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  


## <a name="prerequisites"></a>Pré-requisitos

- Estabeleceu conectividade de e para uma nuvem privada Azure VMware Solution com o seu circuito ExpressRoute espreitado com uma porta de entrada ExpressRoute numa rede virtual Azure (VNet) – que é _o circuito 2_ a partir dos procedimentos de observação.  
- Um circuito ExpressRoute separado e funcional usado para ligar ambientes no local ao Azure – que é o _circuito 1_ do ponto de vista dos procedimentos de perspíria.
- Um bloco de [endereços](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) de rede não sobreposto /29 para o olho do ExpressRoute Global Reach.

> [!TIP]
> No contexto destes pré-requisitos, o circuito ExpressRoute no local é _o circuito 1_ , e o circuito expressRoute em nuvem privada está num circuito de subscrição e de marca _2_ . 


## <a name="create-an-expressroute-authorization-key-in-the-private-cloud"></a>Criar uma chave de autorização ExpressRoute na nuvem privada

1. A partir da nuvem privada **Overview** , em Gestão, selecione **Conectividade > ExpressRoute > Solicite uma chave de autorização** .

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Selecione Conectividade > ExpressRoute > Solicite uma chave de autorização para iniciar um novo pedido.":::

2. Introduza o nome para a chave de autorização e selecione **Criar** . 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Selecione Conectividade > ExpressRoute > Solicite uma chave de autorização para iniciar um novo pedido.":::

   Uma vez criada, a nova chave aparece na lista de chaves de autorização para a nuvem privada. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Selecione Conectividade > ExpressRoute > Solicite uma chave de autorização para iniciar um novo pedido.":::

3. Tome nota da chave de autorização e do ID ExpressRoute, juntamente com o bloco de endereços /29. Vais usá-los no próximo passo para completar o olhar. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Nuvem privada de pares para as instalações usando a chave de autorização

Agora que criou uma chave de autorização para o circuito expressRoute em nuvem privada, pode perscrutar com o circuito ExpressRoute no local.  O espreitamento é feito do ponto de vista do circuito ExpressRoute no [portal Azure](#azure-portal-method) ou utilizando o [CLI Azure numa Cloud Shell](#azure-cli-in-a-cloud-shell-method). Com ambos os métodos, utiliza o ID de recurso e a chave de autorização do seu circuito ExpressRoute em nuvem privada para terminar o espreitamento.

### <a name="azure-portal-method"></a>Método do portal Azure

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando a mesma subscrição que o circuito ExpressRoute no local.

1. A partir da nuvem privada **Overview** , under Manage, **selecione Conectividade > ExpressRoute Global Reach > Add** .

   :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Selecione Conectividade > ExpressRoute > Solicite uma chave de autorização para iniciar um novo pedido.":::

1. Pode criar uma ligação em nuvem no local fazendo uma destas opções:

   - Selecione o circuito ExpressRoute da lista.
   - Se tiver uma identificação de circuito, copie-o e cole-o.

1. Selecione **Ligar** . A nova ligação mostra-se na lista de ligações em nuvem no local.  

>[!TIP]
>Pode eliminar ou desligar uma ligação da lista selecionando **Mais** .  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Selecione Conectividade > ExpressRoute > Solicite uma chave de autorização para iniciar um novo pedido.":::

### <a name="azure-cli-in-a-cloud-shell-method"></a>Azure CLI em um método Cloud Shell

Aumentámos os [comandos do CLI](../expressroute/expressroute-howto-set-global-reach-cli.md) com detalhes e exemplos específicos para o ajudar a configurar o ExpressRoute Global Reach a espreitar entre ambientes no local para uma nuvem privada Azure VMware Solution.  

> [!TIP]  
> Para a brevidade na saída do comando Azure CLI, estas instruções podem usar um [ `–query` argumento para executar uma consulta JMESPath apenas para mostrar os resultados necessários](/cli/azure/query-azure-cli).


1. Inscreva-se no portal Azure usando a mesma subscrição que o circuito ExpressRoute no local e abra uma Cloud Shell. Deixe a concha como Bash.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Selecione Conectividade > ExpressRoute > Solicite uma chave de autorização para iniciar um novo pedido.":::
 
2. Introduza o comando Azure CLI para criar o espreitamento. Utilize o seu ID de informação e recursos específicos, a chave de autorização e o bloco de rede /29 CIDR. 

   A imagem mostra um exemplo do comando que vai usar e da saída indicando um olhar bem sucedido. O comando exemplo baseia-se no comando utilizado no [passo 3 de "Permitir a conectividade entre circuitos ExpressRoute em diferentes subscrições Azure".](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions)

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Selecione Conectividade > ExpressRoute > Solicite uma chave de autorização para iniciar um novo pedido.":::
 
   Pode ligar-se dos ambientes no local à sua nuvem privada sobre o ExpressRoute Global Reach.

> [!TIP]
> Pode eliminar o espreguiçadamento que acabou de criar seguindo a [conectividade de desativação entre as](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) instruções das redes no local.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar uma segunda chave de autorização para o circuito expressRoute em nuvem privada. Também aprendeu a permitir o olhar do ExpressRoute Global Reach no local. 

Continue até ao próximo tutorial para aprender a implementar e configurar a solução VMware HCX para a sua nuvem privada Azure VMware Solution.

> [!div class="nextstepaction"]
> [Implementar e configurar o VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->