---
title: 'Azure ExpressRoute: Configure ExpressRoute Direct: portal'
description: Esta página ajuda-o a configurar o ExpressRoute Direct utilizando o portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: b133f1cce4af07d8d5e50e04670741fcf7c936a4
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097079"
---
# <a name="create-expressroute-direct-using-the-azure-portal"></a>Criar ExpressRoute Direct usando o portal Azure

Este artigo mostra-lhe como criar ExpressRoute Direct usando o portal Azure.
O ExpressRoute Direct permite-lhe ligar-se diretamente à rede global da Microsoft em locais de observação estrategicamente distribuídos em todo o mundo. Para obter mais informações, veja [Sobre o ExpressRoute Direct](expressroute-erdirect-about.md).

## <a name="before-you-begin"></a><a name="before"></a>Antes de começar

Antes de utilizar o ExpressRoute Direct, tem primeiro de inscrever a sua subscrição. Para se inscrever, por favor faça o seguinte através da Azure PowerShell:
1.  Faça o súmis no Azure e selecione a subscrição que deseja inscrever.

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. Registe a sua subscrição para Visualização Pública utilizando o seguinte comando:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

Uma vez inscrito, verifique se o fornecedor de recursos **Microsoft.Network** está registado na sua subscrição. O registo de um fornecedor de recursos configura a sua subscrição para trabalhar com o fornecedor de recursos.

1. Aceda às definições de subscrição descritas nos [fornecedores e tipos de recursos Azure](../azure-resource-manager/management/resource-providers-and-types.md).
1. Na sua subscrição, para **Fornecedores de Recursos,** verifique se o fornecedor **Microsoft.Network** apresenta um estado **registado.** Se o fornecedor de recursos Microsoft.Network não estiver presente na lista de fornecedores registados, adicione-o.

## <a name="create-expressroute-direct"></a><a name="create-erdir"></a>Criar ExpressRoute Direct

1. No menu [do portal Azure,](https://portal.azure.com) ou na página **Inicial,** selecione **Criar um recurso**.

1. Na página **Nova,** no campo **_Search the Marketplace_*_, tipo _* ExpressRoute Direct,** em seguida, selecione **Enter** para obter os resultados da pesquisa.

1. A partir dos resultados, selecione **ExpressRoute Direct**.

1. Na página **Direct ExpressRoute,** selecione **Criar** para abrir a página **Create ExpressRoute Direct.**

1. Comece por completar os campos na página **Basics.**

    :::image type="content" source="./media/how-to-expressroute-direct-portal/basics.png" alt-text="Página básica":::

    * **Subscrição**: A subscrição Azure que pretende utilizar para criar um novo ExpressRoute Direct. Os circuitos ExpressRoute Direct e ExpressRoute devem estar na mesma subscrição.
    * **Grupo de Recursos**: O grupo de recursos Azure no qual será criado o novo recurso ExpressRoute Direct. Se não tiver um grupo de recursos existente, pode criar um novo.
    * **Região**: Região Azure que o recurso será criado.
    * **Nome**: O nome do novo recurso ExpressRoute Direct.

1. Em seguida, preencha os campos na página **de Configuração.**

    :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration.png" alt-text="Screenshot que mostra a página 'Create ExpressRoute Direct' com o separador 'Configuração' selecionado.":::

    * **Localização de espreitar**: O local de observação onde se ligará ao recurso ExpressRoute Direct. Para obter mais informações sobre as localizações de observação, [reveja as Localizações ExpressRoute](expressroute-locations-providers.md).
   * **Largura de banda**: A largura de banda do par de portas que pretende reservar. O ExpressRoute Direct suporta opções de largura de banda de 10 Gb e 100 Gb. Se a largura de banda desejada não estiver disponível no local de observação especificado, [abra um Pedido de Apoio no portal Azure](https://aka.ms/azsupt).
   * **Encapsulamento**: ExpressRoute Direct suporta encapsulamento QinQ e Dot1Q.
     * Se o QinQ for selecionado, cada circuito ExpressRoute será atribuído dinamicamente a uma S-Tag e será único em todo o recurso ExpressRoute Direct.
     *  Cada C-Tag no circuito deve ser único no circuito, mas não através do ExpressRoute Direct.
     * Se a encapsulação do Dot1Q for selecionada, deve gerir a singularidade da C-Tag (VLAN) em todo o recurso ExpressRoute Direct.
     >[!IMPORTANT]
     >ExpressRoute Direct só pode ser um tipo de encapsulamento. A encapsulação não pode ser alterada após a criação do ExpressRoute Direct.
     >

1. Especifique quaisquer tags de recursos e, em seguida, selecione **Review + create** para validar as definições de recursos ExpressRoute Direct.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/validate.png" alt-text="Screenshot que mostra a página 'Create ExpressRoute' com o separador 'Rever + criar' selecionado.":::

1. Selecione **Criar**. Verá uma mensagem a dizer-lhe que a sua implantação está em curso. O estado será exibido nesta página à medida que os recursos forem criados. 

## <a name="generate-the-letter-of-authorization-loa"></a><a name="authorization"></a>Gerar a Carta de Autorização (LOA)

Gerar a carta de autorização não está disponível a partir do portal neste momento. Utilize **[a Azure PowerShell](expressroute-howto-erdirect.md#authorization)** para obter a carta de autorização.

## <a name="change-admin-state-of-links"></a><a name="state"></a>Alterar Estado de Administração de ligações

Este processo deve ser utilizado para realizar um teste da Camada 1, certificando-se de que cada ligação cruzada está corretamente remendada em cada router para o primário e secundário.

1. Na página **'Visão Geral'** do recurso ExpressRoute Direct, na secção **Links,** selecione **link1**.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/link.png" alt-text="Ligação 1" lightbox="./media/how-to-expressroute-direct-portal/link-expand.png":::

1. Alterne a definição **de Estado** de administração para **Ativar** e, em seguida, selecione **Guardar**.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/state.png" alt-text="Estado de Administração":::

    >[!IMPORTANT]
    >A faturação começará quando o estado de administração estiver ativado em qualquer uma das ligações.
    >

1. Repita o mesmo processo para **a ligação2**.

## <a name="create-a-circuit"></a><a name="circuit"></a>Criar um circuito

Por predefinição, pode criar 10 circuitos na subscrição onde se encontra o recurso ExpressRoute Direct. Este número pode ser aumentado através do suporte. É responsável por rastrear a largura de banda de Provisioned e Used. Largura de banda aprovisionada é a soma da largura de banda de todos os circuitos no recurso ExpressRoute Direct. A largura de banda utilizada é o uso físico das interfaces físicas subjacentes.

* Existem larguras de banda de circuitos adicionais que podem ser utilizadas no ExpressRoute Direct apenas para suportar os cenários acima descritos. Estes são: 40 Gbps e 100 Gbps.

* SkuTier pode ser Local, Standard ou Premium.

* SkuFamily deve ser apenas MedidodData. Ilimitado não é suportado no ExpressRoute Direct.

Os seguintes passos ajudam-no a criar um circuito ExpressRoute a partir do fluxo de trabalho ExpressRoute Direct. Se preferir, também pode criar um circuito utilizando o fluxo de trabalho regular do circuito, embora não haja vantagem em utilizar os passos regulares de fluxo de trabalho do circuito para esta configuração. Consulte [criar e modificar um circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md).

1. Na secção **Definições Diretas** ExpressRoute, selecione **Circuitos** e, em seguida, selecione **+Adicionar**. 

    :::image type="content" source="./media/how-to-expressroute-direct-portal/add.png" alt-text="A screenshot mostra as Definições ExpressRoute com circuitos selecionados e Add em destaque." lightbox="./media/how-to-expressroute-direct-portal/add-expand.png":::

1. Configure as definições na página **De Configuração.**

   :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration2.png" alt-text="Página de configuração - ExpressRoute Direct":::

1. Especifique quaisquer tags de recursos, o **selecione Review + Create** para validar os valores antes de criar o recurso.

   :::image type="content" source="./media/how-to-expressroute-direct-portal/review.png" alt-text="Rever e criar - ExpressRoute Direct":::

1. Selecione **Criar**. Verá uma mensagem a dizer-lhe que a sua implantação está em curso. O estado será exibido nesta página à medida que os recursos forem criados. 

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o ExpressRoute Direct, consulte a [Visão Geral.](expressroute-erdirect-about.md)
