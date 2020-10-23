---
title: 'Quickstart: Criar um perfil para HA de aplicações - Portal Azure - Azure Traffic Manager'
description: Este artigo de arranque rápido descreve como criar um perfil de Gestor de Tráfego para construir uma aplicação web altamente disponível usando o portal Azure.
services: traffic-manager
author: duongau
manager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2020
ms.author: duau
ms.openlocfilehash: 45489d3500a4a744f2aeb34dc21122d180797133
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92101333"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Quickstart: Criar um perfil de Gestor de Tráfego utilizando o portal Azure

Este quickstart descreve como criar um perfil de Gestor de Tráfego que oferece alta disponibilidade para a sua aplicação web.

Neste arranque rápido, você vai ler sobre dois casos de uma aplicação web. Cada um deles está a correr numa região diferente do Azure. Você vai criar um perfil de Gestor de Tráfego baseado na [prioridade do ponto final](traffic-manager-routing-methods.md#priority-traffic-routing-method). O perfil direciona o tráfego do utilizador para o site primário que executa a aplicação web. O Gestor de Tráfego monitoriza continuamente a aplicação web. Se o site principal não estiver disponível, fornece falha automática no site de reserva.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

## <a name="prerequisites"></a>Pré-requisitos

Para este arranque rápido, você precisará de duas instâncias de uma aplicação web implantada em duas regiões diferentes de Azure (*Leste dos EUA* e Europa *Ocidental).* Cada um servirá como ponto final primário e de saída para o Gestor de Tráfego.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No lado superior esquerdo do ecrã, selecione **Criar um recurso**. Procure por **Web App** e selecione **Criar.**

1. Na **Criação de uma Aplicação Web,** escreva ou selecione os seguintes valores no **separador Básicos:**

    | Definição                 | Valor |
    | ---                     | --- |
    | Subscrição            | Selecione a sua subscrição. |    
    | Grupo de recursos          | **Selecione Criar novo** e insira o *myResourceGroupTM1* na caixa de texto.|
    | Nome                    | Introduza um **Nome** único para a sua aplicação web. Este exemplo utiliza *o myWebAppEastUS*. |
    | Publicar                 | Selecione **Código**. |
    | Pilha de tempo de execução           | Selecione **ASP.NET V4.7**. |
    | Sistema Operativo        | Selecione **Windows**. |
    | Região                  | Selecione **East US**. |
    | Plano do Windows            | Selecione **Criar novo** e insira *o myAppServicePlanEastUS* na caixa de texto. |
    | SKU e tamanho            | Selecione **Standard S1 100 total ACU, memória de 1,75 GB**. |
   
1. Selecione o **separador Monitoring** ou selecione **Seguinte: Monitorização**.  Em **Monitorização**, definir **Insights de Aplicação**Permitir insights de  >  **aplicação** para **não**.

1. Selecione **Rever e criar**.

1. Reveja as definições e, em seguida, **selecione Criar**.  Quando a Web App implementa com sucesso, cria um web site predefinido.

1. Siga os passos 1-6 para criar uma segunda Aplicação Web chamada *myWebAppWestEurope*. O nome **do Grupo de Recursos** é *myResourceGroupTM2,* com **Região** da *Europa Ocidental,* e o nome do Plano de Serviço de **Aplicações** do **myAppServicePlanWestEurope**. Todas as outras definições são as mesmas *que o myWebAppEastUS*.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego

Crie um perfil de Gestor de Tráfego que direcione o tráfego do utilizador com base na prioridade do ponto final.

1. No lado superior esquerdo do ecrã, selecione **Criar um recurso**. Em seguida, procure o **perfil de Gestor de Tráfego** e selecione **Criar**.
1. No **perfil 'Criar Gestor de Tráfego',** insira ou selecione estas definições:

    | Definição | Valor |
    | --------| ----- |
    | Nome | Insira um nome único para o seu perfil de Gestor de Tráfego.|
    | Método de encaminhamento | Selecione **Prioridade**.|
    | Subscrição | Selecione a subscrição a que pretende aplicar o perfil do gestor de tráfego. |
    | Grupo de recursos | Selecione *myResourceGroupTM1*.|
    | Localização |Esta definição refere-se à localização do grupo de recursos. Não tem qualquer efeito no perfil do Gestor de Tráfego que será implementado globalmente.|

1. Selecione **Criar**.

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos finais do Gestor de Tráfego

Adicione o Website em *E.U.A. Leste* como o ponto final principal para encaminhar todo o tráfego do utilizador. Adicione o site na *Europa Ocidental* como um ponto final de falha. Quando o ponto final principal não está disponível, o tráfego liga-se automaticamente para o ponto final de saída.

1. Na barra de pesquisa do portal, insira o nome de perfil do Gestor de Tráfego que criou na secção anterior.
1. Selecione o perfil a partir dos resultados da pesquisa.
1. No **perfil de Gestor de Tráfego**, na secção **Definições,** selecione **Pontos de Final**e, em seguida, selecione **Adicionar**.

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-endpoint-menu.png" alt-text="Definições de ponto final no perfil do Gestor de Tráfego":::

1. Introduza ou selecione estas definições:

    | Definição | Valor |
    | ------- | ------|
    | Tipo | Selecione **ponto final Azure**. |
    | Nome | Insira *o meuPrimaryEndpoint*. |
    | Tipo de recurso de destino | Selecione **o Serviço de Aplicações**. |
    | Recurso de destino | **Selecione Escolha um serviço de aplicações**Leste  >  **EUA.** |
    | Prioridade | Selecione **1**. Todo o tráfego vai para este ponto final quando é saudável. |

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png" alt-text="Definições de ponto final no perfil do Gestor de Tráfego":::
    
1. Selecione **OK**.
1. Para criar um ponto final de falha para a sua segunda região de Azure, repita os passos 3 e 4 com estas definições:

    | Definição | Valor |
    | ------- | ------|
    | Tipo | Selecione **ponto final Azure**. |
    | Nome | Insira *o meu Ponto Desemover.* |
    | Tipo de recurso de destino | Selecione **o Serviço de Aplicações**. |
    | Recurso de destino | **Selecione Escolha um serviço de aplicações**West  >  **Europe**. |
    | Prioridade | Selecione **2**. Todo o tráfego vai para este ponto final se o ponto final principal não for saudável. |

1. Selecione **OK**.

Quando terminar de adicionar os dois pontos finais, eles são exibidos no **perfil de Gestor de Tráfego.** Note que o seu estado de monitorização está **online** agora.

## <a name="test-traffic-manager-profile"></a>Testar o perfil do Gestor de Tráfego

Nesta secção, você verificará o nome de domínio do seu perfil de Gestor de Tráfego. Também configurará o ponto final principal para não estar disponível. Finalmente, pode ver que a aplicação web ainda está disponível. É porque o Gerente de Tráfego envia o tráfego para o ponto final de falha.

### <a name="check-the-dns-name"></a>Verifique o nome DNS

1. Na barra de pesquisa do portal, procure o nome do **perfil do Gestor de Tráfego** que criou na secção anterior.
1. Selecione o perfil do gestor de tráfego. A **visão geral** aparece.
1. O **Perfil do Gestor de Tráfego** mostra o nome DNS do perfil que acabou de criar.
  
    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png" alt-text="Definições de ponto final no perfil do Gestor de Tráfego":::

### <a name="view-traffic-manager-in-action"></a>Ver o Gestor de Tráfego em ação

1. Num navegador web, insira o nome DNS do seu perfil de Gestor de Tráfego para ver o site padrão da sua Web App.

    > [!NOTE]
    > Neste cenário de arranque rápido, todos os pedidos de rota para o ponto final principal. Está definido para **a Prioridade 1**.

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png" alt-text="Definições de ponto final no perfil do Gestor de Tráfego":::

1. Para ver o Traffic Manager failover em ação, desative o seu site principal:
    1. Na página 'Perfil do Gestor de Tráfego', a partir da secção **Overview,** selecione **myPrimaryEndpoint**.
    1. No *myPrimaryEndpoint*, selecione **Disabled**  >  **Disabled Save**.
    1. Feche **o meuPrimaryEndpoint**. Note que o estado está **desativado** agora.
1. Copie o nome DNS do seu Perfil de Gestor de Tráfego a partir do passo anterior para ver o site numa nova sessão de navegador web.
1. Verifique se a aplicação web ainda está disponível.

O ponto final principal não está disponível, por isso foste encaminhado para o ponto final de falha.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar, elimine os grupos de recursos, aplicações web e todos os recursos relacionados. Para tal, selecione cada item individual do seu painel de instrumentos e **selecione Delete** no topo de cada página.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um perfil de Gestor de Tráfego. Permite-lhe direcionar o tráfego de utilizadores para aplicações web de alta disponibilidade. Para saber mais sobre o tráfego de encaminhamento, continue para os tutoriais do Traffic Manager.

> [!div class="nextstepaction"]
> [Traffic Manager tutorials](tutorial-traffic-manager-improve-website-response.md) (Tutoriais do Gestor de Tráfego)
