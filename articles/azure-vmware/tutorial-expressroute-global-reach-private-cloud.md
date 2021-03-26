---
title: Tutorial - Ambientes de peer on-in para uma nuvem privada
description: Saiba como criar o ExpressRoute Global Reach olhando para uma nuvem privada numa Solução VMware Azure.
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 59ddc2dd4aed850328807a4543d2fabd6f5a99f1
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564505"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Tutorial: Ambientes de pares no local para uma nuvem privada

O ExpressRoute Global Reach liga o seu ambiente no local à sua nuvem privada Azure VMware Solution. A ligação ExpressRoute Global Reach é estabelecida entre o circuito expressRoute em nuvem privada e uma ligação ExpressRoute existente aos seus ambientes no local. 

O circuito ExpressRoute que utiliza quando [configura a rede para a sua nuvem privada VMware em Azure](tutorial-configure-networking.md) requer que crie e utilize chaves de autorização.  Já terá usado uma chave de autorização do circuito ExpressRoute, e neste tutorial, criará uma segunda chave de autorização para espreitar com o circuito ExpressRoute no local.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma segunda chave de autorização para o _circuito 2,_ o circuito expressRoute em nuvem privada.
> * Utilize o portal Azure ou o CLI Azure num método Cloud Shell na subscrição do _circuito 1_ para permitir o observamento do ExpressRoute Global Reach em nuvem no local.


## <a name="before-you-begin"></a>Antes de começar

Antes de permitir a conectividade entre dois circuitos ExpressRoute utilizando o ExpressRoute Global Reach, reveja a documentação sobre como permitir a [conectividade em diferentes subscrições do Azure](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  

## <a name="prerequisites"></a>Pré-requisitos

- Estabeleceu conectividade de e para uma nuvem privada Azure VMware Solution com o seu circuito ExpressRoute espreitado com uma porta de entrada ExpressRoute numa rede virtual Azure (VNet) – que é o circuito 2 a partir dos procedimentos de observação.
- Um circuito ExpressRoute separado e funcional usado para ligar ambientes no local ao Azure – que é o circuito 1 do ponto de vista dos procedimentos de perspíria.
- Um bloco de [endereços](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) de rede não sobreposto /29 para o olho do ExpressRoute Global Reach.
- Certifique-se de que todos os gateways, incluindo o serviço do prestador ExpressRoute, suportam o Número do Sistema Autónomo de 4 byte (ASN). A Azure VMware Solution utiliza ASNs públicos de 4 byte para rotas publicitárias.

>[!IMPORTANT]
>No contexto destes pré-requisitos, o circuito ExpressRoute no local é _o circuito 1_, e o circuito expressRoute em nuvem privada está num circuito de subscrição e de marca _2_.

## <a name="create-an-expressroute-authorization-key-in-the-private-cloud-expressroute-circuit"></a>Criar uma chave de autorização ExpressRoute no circuito expressRoute em nuvem privada

[!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]
 
## <a name="peer-private-cloud-to-on-premises-with-authorization-key"></a>Nuvem privada de pares para as instalações com chave de autorização
Agora que criou uma chave de autorização para o circuito expressRoute em nuvem privada, pode perscrutar com o circuito ExpressRoute no local. O espreitamento é feito do ponto de vista do circuito ExpressRoute no **portal Azure** ou utilizando o **CLI Azure numa Cloud Shell**. Com ambos os métodos, utiliza o ID de recurso e a chave de autorização do seu circuito ExpressRoute em nuvem privada para terminar o espreitamento.

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando a mesma subscrição que o circuito ExpressRoute no local.

1. A partir da nuvem privada **Overview**, under Manage, **selecione Connectivity**  >  **ExpressRoute Global Reach**  >  **Add**.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Screenshot mostrando o separador ExpressRoute Global Reach na nuvem privada Azure VMware Solution.":::

1. Crie uma ligação de nuvem no local. Faça um dos seguintes e, em seguida, **selecione Criar**:

   - Selecione o **circuito ExpressRoute** da lista, ou
   - Se tiver o ID do circuito, cole-o no campo e forneça a chave de autorização.

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="Introduza o ID ExpressRoute e a chave de autorização e, em seguida, selecione Criar.":::   
   
   A nova ligação mostra-se na lista de ligações em nuvem no local.

>[!TIP]
>Pode eliminar ou desligar uma ligação da lista selecionando **Mais**.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Desligar ou apagar uma ligação no local":::

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Aumentámos os [comandos do CLI](../expressroute/expressroute-howto-set-global-reach-cli.md) com detalhes e exemplos específicos para o ajudar a configurar o ExpressRoute Global Reach a espreitar entre ambientes no local para uma nuvem privada Azure VMware Solution.

>[!TIP]
>Para a brevidade na saída do comando Azure CLI, estas instruções podem usar um [ `–query` argumento](/cli/azure/query-azure-cli) para executar uma consulta JMESPath apenas para mostrar os resultados necessários.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando a mesma subscrição que o circuito ExpressRoute no local. 

1. Abra uma Cloud Shell e deixe a concha como uma festa.

   :::image type="content" source="media/expressroute-global-reach/azure-portal-cloud-shell.png" alt-text="Screenshot mostrando o portal Azure Cloud Shell.":::

1. Crie o espreitamento contra o circuito 1, passando na chave de identificação de recursos do circuito 2 e da chave de autorização. 

   ```azurecli-interactive
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Screenshot mostrando o comando e os resultados de um olhar bem sucedido entre o circuito 1 e o circuito 2.":::

Pode ligar-se dos ambientes no local à sua nuvem privada sobre o ExpressRoute Global Reach.

>[!TIP]
>Pode eliminar o espreguiçadamento seguindo as instruções [de desativação entre as](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) instruções das redes no local.


---

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a permitir o olhar para o ExpressRoute Global Reach no local. 

Continue até ao próximo tutorial para aprender a implementar e configurar a solução VMware HCX para a sua nuvem privada Azure VMware Solution.

> [!div class="nextstepaction"]
> [Implementar e configurar o VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->