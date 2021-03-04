---
title: 'Quickstart: Criar e configurar o Servidor de Rota utilizando o portal Azure'
description: Neste arranque rápido, aprende-se a criar e configurar um Servidor de Rota utilizando o portal Azure.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/03/2021
ms.author: duau
ms.openlocfilehash: ef48f7623aee1256ad1f889d2e70b5a2fdb8e6bf
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102108880"
---
# <a name="quickstart-create-and-configure-route-server-using-the-azure-portal"></a>Quickstart: Criar e configurar o Servidor de Rota utilizando o portal Azure

Este artigo ajuda-o a configurar o Azure Route Server para espreitar com um Aparelho Virtual de Rede (NVA) na sua rede virtual utilizando o portal Azure. O Azure Route Server aprenderá as rotas a partir do NVA e programá-las-á nas máquinas virtuais da rede virtual. O Azure Route Server também anunciará as rotas de rede virtuais para o NVA. Para mais informações, leia [o Azure Route Server](overview.md).

> [!IMPORTANT]
> O Azure Route Server (Preview) encontra-se atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Reveja os limites de [serviço para O Servidor de Rota Azure](route-server-faq.md#limitations).

## <a name="create-a-route-server"></a>Criar um servidor de rota

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Inicie sessão na sua conta do Azure e selecione a sua subscrição.

Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

### <a name="create-a-route-server"></a>Criar um servidor de rota

1. Aceda a https://aka.ms/routeserver.

1. Selecione **+ Criar novo servidor de rotas**.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-landing-page.png" alt-text="Screenshot da página de aterragem do Route Server."::: 

1. Na página **'Criar um Servidor de Rota',** insira ou selecione as informações necessárias.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/create-route-server-page.png" alt-text="Screenshot da página do Servidor de Rota.":::     

    | Definições | Valor |
    |----------|-------|
    | Subscrição | Selecione a subscrição Azure que pretende utilizar para implementar o Servidor de Rota. |
    | Grupo de recursos | Selecione um grupo de recursos para criar o Servidor de Rota em. Se não tiver um grupo de recursos existente, pode criar um novo. |
    | Name | Insira um nome para o Servidor de Rota. |
    | Region | Selecione a região em que o Servidor de Rota será criado. Selecione a mesma região que a rede virtual que criou anteriormente para ver a rede virtual no drop-down. |
    | Rede Virtual | Selecione a rede virtual na qual o Servidor de Rota será criado. Pode criar uma nova rede virtual ou utilizar uma rede virtual existente. Se estiver a utilizar uma rede virtual existente, certifique-se de que a rede virtual existente tem espaço suficiente para um mínimo de uma sub-rede /27 para acomodar o requisito da sub-rede Do Servidor de Rota. Se não vir a sua rede virtual a partir do dropdown, certifique-se de que selecionou o Grupo ou região de Recursos corretos. |
    | Sub-rede | Uma vez criado ou selecionado uma rede virtual, o campo de sub-redes aparecerá. Esta sub-rede é dedicada apenas ao Route Server. **Selecione Gerir a configuração da sub-rede** e criar a sub-rede Azure Route Server. Selecione **+ Sub-rede** e crie uma sub-rede utilizando as seguintes diretrizes:</br><br>- A sub-rede deve chamar-se *RouteServerSubnet*.</br><br>- A sub-rede deve ser no mínimo de /27 ou maior.</br> |

1. Selecione **Rever + criar,** rever o resumo e, em seguida, selecione **Criar**. 

    > [!NOTE]
    > A implementação do Servidor de Rota levará cerca de 20 minutos.

## <a name="set-up-peering-with-nva"></a>Configurar olhando com NVA

A secção irá ajudá-lo a configurar o BGP a espreitar com o seu NVA.

1. Vá ao [Servidor de Rota](https://aka.ms/routeserver) no portal Azure e selecione o Servidor de Rota que pretende configurar.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/select-route-server.png" alt-text="Screenshot da lista do Servidor de Rota."::: 

1. Selecione **Pares** em *Definições* no painel de navegação esquerdo. Em seguida, **selecione + Adicione** para adicionar um novo par.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/peers-landing-page.png" alt-text="Screenshot da página de aterragem dos pares."::: 

1. Insira as seguintes informações sobre o seu par NVA.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/add-peer-page.png" alt-text="Screenshot da página de adicionar peer.":::

    | Definições | Valor |
    |----------|-------|
    | Nome | Dê um nome para o espreitamento entre o seu Servidor de Rota e o NVA. |
    | ASN |  Introduza o Número de Sistemas Autónomos (ASN) do seu NVA. |
    | Endereço IPv4 | Introduza o endereço IP do NVA com o que o Servidor de Rota comunicará para estabelecer bGP. |

1. **Selecione Adicionar** para adicionar este par.

## <a name="complete-the-configuration-on-the-nva"></a>Complete a configuração no NVA

Você precisará dos IPs e ASN do Azure Route Server para completar a configuração no seu NVA para estabelecer uma sessão de BGP. Pode obter estas informações a partir da página geral do seu Servidor de Rota.

:::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-overview.png" alt-text="Screenshot da página geral do Route Server.":::

## <a name="configure-route-exchange"></a>Configure troca de rotas

Se tiver um gateway ExpressRoute e/ou gateway VPN e quiser que troquem rotas com o Servidor de Rota, pode ativar a troca de rotas.

1. Vá ao [Servidor de Rota](https://aka.ms/routeserver) no portal Azure e selecione o Servidor de Rota que pretende configurar.

1. Selecione **Configuração** em *Definições* no painel de navegação esquerdo.

1. **Selecione Ativar** para a definição **de ramo-a-ramo** e, em seguida, selecione **Guardar**.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/enable-route-exchange.png" alt-text="Screenshot de como ativar a troca de rotas.":::

## <a name="next-steps"></a>Passos seguintes

Depois de criar o Azure Route Server, continue a aprender como o Azure Route Server interage com o ExpressRoute e o VPN Gateways: 

> [!div class="nextstepaction"]
> [Suporte Azure ExpressRoute e Azure VPN](expressroute-vpn-support.md)
