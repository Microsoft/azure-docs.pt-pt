---
title: 'Quickstart: Criar uma configuração any-to-any usando um modelo ARM'
titleSuffix: Azure Virtual WAN
description: Este quickstart mostra-lhe como criar uma configuração Any-to-Any utilizando um modelo de Gestor de Recursos Azure (modelo ARM).
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: quickstart
ms.date: 02/02/2021
ms.author: cherylmc
ms.custom: subject-armqs
ms.openlocfilehash: d31f490baec49e8e0b6fcf89caa8c19202fdf763
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102431385"
---
# <a name="quickstart-create-an-any-to-any-configuration-using-an-arm-template"></a>Quickstart: Criar uma configuração any-to-any usando um modelo ARM

Este quickstart descreve como usar um modelo de Gestor de Recursos Azure (modelo ARM) para criar um cenário any-to-any onde qualquer fala pode chegar a outro falado.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f201-virtual-wan-with-all-gateways%2fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Os dados de certificados de chave pública são necessários para esta configuração. Os dados da amostra são fornecidos no artigo. No entanto, os dados da amostra são fornecidos apenas para satisfazer os requisitos do modelo, a fim de criar um gateway P2S. Após o fim do modelo e os recursos, tem de atualizar este campo com os seus próprios dados de certificado para que a configuração funcione. Consulte [os certificados VPN do utilizador](certificates-point-to-site.md#cer).

## <a name="review-the-template"></a><a name="review"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/201-virtual-wan-with-all-gateways). O modelo para este artigo é muito longo para mostrar aqui. Para ver o modelo, consulte [azuredeploy.js.](https://github.com/Azure/azure-quickstart-templates/blob/master/201-virtual-wan-with-all-gateways/azuredeploy.json)

Neste arranque rápido, irá criar uma implementação multi-hub Azure Virtual WAN, incluindo todos os gateways e ligações VNet. A lista de parâmetros de entrada foi mantida propositadamente no mínimo. O esquema de endereçamento IP pode ser alterado modificando as variáveis dentro do modelo. O cenário é explicado ainda mais no artigo [de qualquer cenário.](scenario-any-to-any.md)

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="Arquitetura de implantação":::

Este modelo cria um ambiente Azure Virtual WAN totalmente funcional com os seguintes recursos:

* Dois centros distintos em diferentes regiões
* Quatro redes virtuais Azure (VNet)
* Duas ligações VNet para cada hub VWAN
* Uma porta VPN ponto-a-local (P2S) em cada centro
* Uma porta VPN site-to-site (S2S) em cada hub
* Uma porta de entrada ExpressRoute em cada hub

Os recursos Azure múltiplos são definidos no modelo:

* [**Microsoft.Network/virtualwans**](/azure/templates/microsoft.network/virtualwans)
* [**Microsoft.Network/virtualhubs**](/azure/templates/microsoft.network/virtualhubs)
* [**Microsoft.Network/virtualnetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Ligações microsoft.Network/hubvirtualnetwork**](/azure/templates/microsoft.network/virtualhubs/hubvirtualnetworkconnections)
* [**Microsoft.Network/p2svpngateways**](/azure/templates/microsoft.network/p2svpngateways)
* [**Microsoft.Network/vpngateways**](/azure/templates/microsoft.network/vpngateways) 
* [**Microsoft.Network/expressroutegateways**](/azure/templates/microsoft.network/expressroutegateways)
* [**Microsoft.Network/vpnserverconfigurations**](/azure/templates/microsoft.network/vpnserverconfigurations)

>[!NOTE]
> Este modelo ARM não cria os recursos do lado do cliente necessários para a conectividade híbrida. Depois de implementar o modelo, ainda precisa de criar e configurar os clientes P2S VPN, os balcões VPN (Sites Locais) e ligar os circuitos ExpressRoute.
>

Para encontrar mais modelos, consulte [os modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a><a name="deploy"></a>Implementar o modelo

Para implementar este modelo corretamente, deve utilizar o botão para implementar para o botão Azure e o portal Azure, em vez de outros métodos, pelas seguintes razões:

* Para criar a configuração P2S, é necessário carregar os dados do certificado raiz. O campo de dados não aceita os dados do certificado ao utilizar o PowerShell ou o CLI.
* Este modelo não funciona corretamente usando a Cloud Shell devido ao upload de dados de certificado.
* Além disso, pode modificar facilmente o modelo e os parâmetros do portal para acomodar intervalos de endereços IP e outros valores.

1. Clique em **Implementar no Azure**.

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f201-virtual-wan-with-all-gateways%2fazuredeploy.json)
1. Para ver o modelo, clique no **modelo de edição.** Nesta página, pode ajustar alguns dos valores, como o espaço de endereço ou o nome de determinados recursos. **Guarde** para guardar as suas alterações ou **deite fora**.
1. Na página do modelo, insira os valores. Para este modelo, são necessários os dados do certificado público P2S. Se estiver a utilizar este artigo como exercício, pode utilizar os seguintes dados deste ficheiro .cer como dados de amostra para ambos os centros. Uma vez que o modelo é executado e a implementação está concluída, para utilizar a configuração P2S, deve substituir esta informação pelos [dados](certificates-point-to-site.md#cer) do certificado de chave pública para a sua própria implantação.

   ```certificate-data
   MIIC5zCCAc+gAwIBAgIQGxd3Av1q6LJDZ71e3TzqcTANBgkqhkiG9w0BAQsFADAW
   MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0yMDExMDkyMjMxNTVaFw0yMTExMDky
   MjUxNTVaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
   AAOCAQ8AMIIBCgKCAQEA33fFra/E0YmGuXLKmYcdvjsYpKwQmw8DjjDkbwhE9jcc
   Dp50e7F1P6Rxo1T6Hm3dIhEji+0QkP4Ie0XPpw0eW77+RWUiG9XJxGqtJ3Q4tyRy
   vBfsHORcqMlpV3VZOXIxrk+L/1sSm2xAc2QGuOqKaDNNoKmjrSGNVAeQHigxbTQg
   zCcyeuhFxHxAaxpW0bslK2hEZ9PhuAe22c2SHht6fOIDeXkadzqTFeV8wEZdltLr
   6Per0krxf7N2hFo5Cfz0KgWlvgdKLL7dUc9cjHo6b6BL2pNbLh8YofwHQOQbwt6H
   miAkEnx1EJ5N8AWuruUTByR2jcWyCnEAUSH41+nk4QIDAQABozEwLzAOBgNVHQ8B
   Af8EBAMCAgQwHQYDVR0OBBYEFJMgnJSYHH5AJ+9XB11usKRwjbjNMA0GCSqGSIb3
   DQEBCwUAA4IBAQBOy8Z5FBd/nvgDcjvAwNCw9h5RHzgtgQqDP0qUjEqeQv3ALeC+
   k/F2Tz0OWiPEzX5N+MMrf/jiYsL2exXuaPWCF5U9fu8bvs89GabHma8MGU3Qua2x
   Imvt0whWExQMjoyU8SNUi2S13fnRie9ZlSwNh8B/OIUUEtVhQsd4OfuZZFVH4xGp
   ibJMSMe5JBbZJC2tCdSdTLYfYJqrLkVuTjynXOjmz2JXfwnDNqEMdIMMjXzlNavR
   J8SNtAoptMOK5vAvlySg4LYtFyXkl0W0vLKIbbHf+2UszuSCijTUa3o/Y1FoYSfi
   eJH431YTnVLuwdd6fXkXFBrXDhjNsU866+hE
   ```

1. Quando terminar de introduzir valores, selecione **Review + create**.
1. Na página **'Rever + criar',** após passes de validação, selecione **Criar**.
1. Leva cerca de 75 minutos para a colocação ser concluída. Pode ver o progresso na página **de visão geral** do modelo.  Se fechar o portal, a implantação continuará.

   :::image type="content" source="./media/quickstart-any-to-any-template/template.png" alt-text="Exemplo de implantação completa":::

## <a name="validate-the-deployment"></a><a name="validate"></a>Validar a implementação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **grupos** de recursos do painel esquerdo.
1. Selecione o grupo de recursos que criou na secção anterior. Na página **'Visão Geral',** verá algo semelhante a este exemplo: :::image type="content" source="./media/quickstart-any-to-any-template/resources.png" alt-text="Exemplo de recursos" lightbox="./media/quickstart-any-to-any-template/resources.png":::

1. Clique no WAN virtual para ver os centros. Na página WAN virtual, clique em cada hub para visualizar ligações e outras informações do hub.
   :::image type="content" source="./media/quickstart-any-to-any-template/hub.png" alt-text="Exemplo de centros" lightbox="./media/quickstart-any-to-any-template/hub.png":::

## <a name="complete-the-hybrid-configuration"></a><a name="complete"></a>Complete a configuração híbrida

O modelo não configura todas as definições necessárias para uma rede híbrida. Tem de completar as seguintes configurações e configurações, dependendo dos seus requisitos.

* [Configure as filiais VPN - locais](virtual-wan-site-to-site-portal.md#site)
* [Complete a configuração P2S VPN](virtual-wan-point-to-site-portal.md)
* [Ligue os circuitos ExpressRoute](virtual-wan-expressroute-portal.md)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar dos recursos que criou, elimine-os. Alguns dos recursos DE WAN virtuais devem ser eliminados numa determinada ordem devido a dependências. Apagar pode levar cerca de 30 minutos para ser concluído.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Complete a configuração P2S VPN](virtual-wan-point-to-site-portal.md)

> [!div class="nextstepaction"]
> [Configure as filiais VPN - locais](virtual-wan-site-to-site-portal.md#site)

> [!div class="nextstepaction"]
> [Ligue os circuitos ExpressRoute](virtual-wan-expressroute-portal.md)