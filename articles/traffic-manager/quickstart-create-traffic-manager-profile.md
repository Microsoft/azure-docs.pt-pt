---
title: Quickstart:Criar um perfil para HA de aplicações - Portal Azure - Gestor de Tráfego Azure
description: Este artigo de início rápido descreve como criar um perfil do Gestor de Tráfego para compilar aplicações Web de elevada disponibilidade.
services: traffic-manager
author: rohinkoul
manager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: rohink
ms.openlocfilehash: 559ed0a134bb6db78d1e89634138b4025e04152b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76934774"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Quickstart: Criar um perfil de Gestor de Tráfego utilizando o portal Azure

Este quickstart descreve como criar um perfil de Gestor de Tráfego que proporciona alta disponibilidade para a sua aplicação web.

Neste arranque rápido, você vai ler sobre duas instâncias de uma aplicação web. Cada um deles está a funcionar numa região de Azure diferente. Criará um perfil de Gestor de Tráfego baseado na [prioridade do ponto final.](traffic-manager-routing-methods.md#priority-traffic-routing-method) O perfil direciona o tráfego do utilizador para o site principal que executa a aplicação web. O Gestor de Tráfego monitoriza continuamente a aplicação web. Se o site principal não estiver disponível, fornece falha automática ao site de backup.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Pré-requisitos

Para este arranque rápido, você precisará de dois casos de uma aplicação web implantada em duas regiões azure diferentes *(Leste dos EUA* e *Europa Ocidental).* Cada um servirá como pontos finais primários e falhados para o Gestor de Tráfego.

1. No lado superior esquerdo do ecrã, selecione **Criar uma** > **Aplicação****Web** > de recursos .

1. Em **Criar uma Aplicação Web,** escreva ou selecione os **seguintes valores** no separador Basics:

   - **Subscription** > **Grupo de recursos**de subscrição : Selecione **Criar novo** e, em seguida, digitar o **meu ResourceGroupTM1**.
   - **Nome dos detalhes** > **da**instância : Digite *myWebAppEastUS*.
   - **Caso Sem** > **detalhes Publicação**: Selecione **Código**.
   - **Detalhes da** > ocorrência Stack de tempo de**execução**: Selecione **ASP.NET V4.7**
   - **Detalhes da** > ocorrência**Sistema operativo**: Selecione **Windows**.
   - **Região de Detalhes** > **Region**de Instância : Selecione **East US**.
   - **Plano** > de serviço de aplicações**Plano Windows Plan (East US)**: Selecione **Criar novo** e, em seguida, digitar **myAppServicePlanEastUS**
   - **Plano** > de serviço de aplicações**Sku e tamanho**: Selecione **Standard S1**.
   
3. Selecione o separador **monitoramento** ou selecione **Next:Monitoring**.  Em **monitorização,** detete insights **de** > aplicação que permitem insights de**aplicação** para **nº**.

4. Selecione **Rever e criar**

5. Reveja as definições e, em seguida, clique em **Criar**.  Quando a Web App implementa com sucesso, cria um web site predefinido.

6. Siga os passos para criar uma segunda Aplicação Web chamada *myWebAppWestEurope*, com um nome de **Grupo** de Recursos do *myResourceGroupTM2*, uma **região** da *Europa Ocidental*, um nome de Plano de Serviço de **Aplicações** do **myAppServicePlanWestEurope**, e todas as outras configurações como *myWebAppEastUS*.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego

Crie um perfil de Gestor de Tráfego que direcione o tráfego do utilizador com base na prioridade do ponto final.

1. No lado superior esquerdo do ecrã, selecione **Criar um** > perfil de Gestor de**Tráfego**em**Rede** > de recursos .
2. No **perfil Criar Gestor de Tráfego,** introduza ou selecione estas definições:

    | Definição | Valor |
    | --------| ----- |
    | Nome | Introduza um nome único para o seu perfil de Gestor de Tráfego.|
    | Método de encaminhamento | Selecione **Prioridade**.|
    | Subscrição | Selecione a subscrição a que deseja aplicar o perfil do gestor de tráfego. |
    | Grupo de recursos | Selecione *myResourceGroupTM1*.|
    | Localização |Esta definição refere-se à localização do grupo de recursos. Não tem qualquer efeito no perfil do Gestor de Tráfego que será implantado globalmente.|

3. Selecione **Criar**.

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos finais do Gestor de Tráfego

Adicione o Website em *E.U.A. Leste* como o ponto final principal para encaminhar todo o tráfego do utilizador. Adicione o site na *Europa Ocidental* como um ponto final de falha. Quando o ponto final principal não está disponível, o tráfego automaticamente se encaminha para o ponto final de falha.

1. Na barra de pesquisa do portal, introduza o nome de perfil do Gestor de Tráfego que criou na secção anterior.
2. Selecione o perfil a partir dos resultados da pesquisa.
3. No **perfil do Gestor de Tráfego**, na secção **Definições,** selecione **Pontos Finais**, e, em seguida, selecione **Adicionar**.
4. Insira ou selecione, estas definições:

    | Definição | Valor |
    | ------- | ------|
    | Tipo | Selecione **ponto final Azure**. |
    | Nome | Introduza *o meu Ponto final primário.* |
    | Tipo de recurso de destino | Selecione **Serviço de Aplicações**. |
    | Recurso de destino | **Selecione Escolha um serviço** > de aplicações East**US**. |
    | Prioridade | Selecione **1**. Todo o tráfego vai para este ponto final quando é saudável. |

    ![Screenshot de onde adiciona um ponto final ao seu perfil de Gestor de Tráfego.](./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png)

5. Selecione **OK**.
6. Para criar um ponto final de failover para a sua segunda região Azure, repita os passos 3 e 4 com estas definições:

    | Definição | Valor |
    | ------- | ------|
    | Tipo | Selecione **ponto final Azure**. |
    | Nome | Introduza *o myFailoverEndpoint*. |
    | Tipo de recurso de destino | Selecione **Serviço de Aplicações**. |
    | Recurso de destino | **Selecione Escolha um serviço** > de aplicações West**Europe**. |
    | Prioridade | Selecione **2**. Todo o tráfego vai para este ponto final falhado se o ponto final primário não for saudável. |

7. Selecione **OK**.

Quando terminar de adicionar os dois pontos finais, são exibidos no **perfil do Gestor**de Tráfego . Note que o seu estado de monitorização está **online** agora.

## <a name="test-traffic-manager-profile"></a>Testar o perfil do Gestor de Tráfego

Nesta secção, você verificará o nome de domínio do seu perfil de Gestor de Tráfego. Também configurará o ponto final principal para não estar disponível. Finalmente, pode ver que a aplicação da web ainda está disponível. É porque o Gestor de Tráfego envia o tráfego para o ponto final do fracasso.

### <a name="check-the-dns-name"></a>Verifique o nome DNS

1. Na barra de pesquisa do portal, procure o nome de perfil do Gestor de **Tráfego** que criou na secção anterior.
2. Selecione o perfil do gestor de tráfego. A **visão geral** aparece.
3. O **Perfil do Gestor de Tráfego** mostra o nome DNS do perfil que acabou de criar.
  
   ![Screenshot da localização do seu nome DNS do Gestor de Tráfego](./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Ver o Gestor de Tráfego em ação

1. Num navegador web, introduza o nome DNS do seu perfil de Traffic Manager para ver o website padrão da sua Web App.

    > [!NOTE]
    > Neste cenário de arranque rápido, todos os pedidos de rota para o ponto final primário. Está definido para a **Prioridade 1**.

    ![Screenshot da página web para confirmar a disponibilidade do perfil do Gestor de Tráfego](./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png)

2. Para ver o Traffic Manager falhar em ação, desative o seu site principal:
    1. Na página perfil do Gestor de Tráfego, a partir da secção **'Visão Geral',** selecione **myPrimaryEndpoint**.
    2. No *myPrimaryEndpoint,* selecione **Desativado** > **Save**.
    3. Feche **o meu Ponto final primário.** Note que o estado está **desativado** agora.
3. Copie o nome DNS do seu Perfil de Gestor de Tráfego a partir do passo anterior para visualizar o website numa nova sessão de navegador web.
4. Verifique se a aplicação da web ainda está disponível.

O ponto final primário não está disponível, por isso foste encaminhado para o ponto final.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar, elimine os grupos de recursos, aplicações web e todos os recursos relacionados. Para isso, selecione cada item individual do seu painel de instrumentos e selecione **Eliminar** na parte superior de cada página.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um perfil de Gestor de Tráfego. Permite-lhe direcionar o tráfego do utilizador para aplicações web de alta disponibilidade. Para saber mais sobre o tráfego de encaminhamento, continue para os tutoriais do Gestor de Tráfego.

> [!div class="nextstepaction"]
> [Traffic Manager tutorials](tutorial-traffic-manager-improve-website-response.md) (Tutoriais do Gestor de Tráfego)
