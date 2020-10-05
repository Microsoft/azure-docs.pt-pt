---
title: Quickstart:Criar um perfil para HA de aplicações - Portal Azure - Azure Traffic Manager
description: Este artigo de início rápido descreve como criar um perfil do Gestor de Tráfego para compilar aplicações Web de elevada disponibilidade.
services: traffic-manager
author: duongau
manager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: duau
ms.openlocfilehash: 7a347d5cd72fcf955dae0aa8319632fdb43d3bf7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89400267"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Quickstart: Criar um perfil de Gestor de Tráfego utilizando o portal Azure

Este quickstart descreve como criar um perfil de Gestor de Tráfego que oferece alta disponibilidade para a sua aplicação web.

Neste arranque rápido, você vai ler sobre dois casos de uma aplicação web. Cada um deles está a correr numa região diferente do Azure. Você vai criar um perfil de Gestor de Tráfego baseado na [prioridade do ponto final](traffic-manager-routing-methods.md#priority-traffic-routing-method). O perfil direciona o tráfego do utilizador para o site primário que executa a aplicação web. O Gestor de Tráfego monitoriza continuamente a aplicação web. Se o site principal não estiver disponível, fornece falha automática no site de reserva.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Pré-requisitos

Para este arranque rápido, você precisará de duas instâncias de uma aplicação web implantada em duas regiões diferentes de Azure (*Leste dos EUA* e Europa *Ocidental).* Cada um servirá como ponto final primário e de saída para o Gestor de Tráfego.

1. No lado superior esquerdo do ecrã, selecione **Criar uma**Web App de recursos  >  **Web**  >  **Web App**.

1. Na **Criação de uma Aplicação Web,** escreva ou selecione os seguintes valores no **separador Básicos:**

   - **Assinatura**  >  **Grupo de Recursos**: Selecione **Criar novo** e, em seguida, digitar **myResourceGroupTM1**.
   - **Detalhes da**  >  instância **Nome**: *Digite myWebAppEastUS*.
   - **Detalhes da**  >  instância **Publicar**: Selecionar **código**.
   - **Detalhes da**  >  instância **Pilha de tempo de execução**: Selecione **ASP.NET V4.7**
   - **Detalhes da**  >  instância **Sistema operativo**: Selecione **Windows**.
   - **Detalhes da**  >  instância **Região**: Selecione **East US**.
   - Plano de **Serviço de Aplicações**  >  **Plano Windows (Leste dos EUA)**: Selecione **Criar novo** e, em seguida, **digitar myAppServicePlanEastUS**
   - Plano de **Serviço de Aplicações**  >  **Sku e tamanho**: Selecione **Standard S1**.
   
3. Selecione o **separador Monitorar** ou selecione **Seguinte:Monitorização**.  Em **Monitorização**, definir **Insights de Aplicação**Permitir insights de  >  **aplicação** para **não**.

4. Selecione **Rever e criar**

5. Reveja as definições e, em seguida, clique em **Criar**.  Quando a Web App implementa com sucesso, cria um web site predefinido.

6. Siga os passos para criar uma segunda Web App chamada *myWebAppWestEurope,* com um nome de Grupo de **Recursos** do *myResourceGroupTM2*, uma **região** da *Europa Ocidental*, um nome de Plano de Serviço de **Aplicação** do **myAppServicePlanWestEurope**, e todas as outras configurações iguais às *do myWebAppEastUS*.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego

Crie um perfil de Gestor de Tráfego que direcione o tráfego do utilizador com base na prioridade do ponto final.

1. No lado superior esquerdo do ecrã, **selecione Criar um**perfil de Gestor de Tráfego  >  **de Rede de**Recursos  >  **Traffic Manager profile**.
2. No **perfil 'Criar Gestor de Tráfego',** insira ou selecione estas definições:

    | Definição | Valor |
    | --------| ----- |
    | Nome | Insira um nome único para o seu perfil de Gestor de Tráfego.|
    | Método de encaminhamento | Selecione **Prioridade**.|
    | Subscrição | Selecione a subscrição a que pretende aplicar o perfil do gestor de tráfego. |
    | Grupo de recursos | Selecione *myResourceGroupTM1*.|
    | Localização |Esta definição refere-se à localização do grupo de recursos. Não tem qualquer efeito no perfil do Gestor de Tráfego que será implementado globalmente.|

3. Selecione **Criar**.

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos finais do Gestor de Tráfego

Adicione o Website em *E.U.A. Leste* como o ponto final principal para encaminhar todo o tráfego do utilizador. Adicione o site na *Europa Ocidental* como um ponto final de falha. Quando o ponto final principal não está disponível, o tráfego liga-se automaticamente para o ponto final de saída.

1. Na barra de pesquisa do portal, insira o nome de perfil do Gestor de Tráfego que criou na secção anterior.
2. Selecione o perfil a partir dos resultados da pesquisa.
3. No **perfil de Gestor de Tráfego**, na secção **Definições,** selecione **Pontos de Final**e, em seguida, selecione **Adicionar**.
4. Introduza ou selecione estas definições:

    | Definição | Valor |
    | ------- | ------|
    | Tipo | Selecione **ponto final Azure**. |
    | Name | Insira *o meuPrimaryEndpoint*. |
    | Tipo de recurso de destino | Selecione **o Serviço de Aplicações**. |
    | Recurso de destino | **Selecione Escolha um serviço de aplicações**Leste  >  **EUA.** |
    | Prioridade | Selecione **1**. Todo o tráfego vai para este ponto final quando é saudável. |

    ![Screenshot de onde adiciona um ponto final ao seu perfil de Gestor de Tráfego.](./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png)

5. Selecione **OK**.
6. Para criar um ponto final de falha para a sua segunda região de Azure, repita os passos 3 e 4 com estas definições:

    | Definição | Valor |
    | ------- | ------|
    | Tipo | Selecione **ponto final Azure**. |
    | Name | Insira *o meu Ponto Desemover.* |
    | Tipo de recurso de destino | Selecione **o Serviço de Aplicações**. |
    | Recurso de destino | **Selecione Escolha um serviço de aplicações**West  >  **Europe**. |
    | Prioridade | Selecione **2**. Todo o tráfego vai para este ponto final se o ponto final principal não for saudável. |

7. Selecione **OK**.

Quando terminar de adicionar os dois pontos finais, eles são exibidos no **perfil de Gestor de Tráfego.** Note que o seu estado de monitorização está **online** agora.

## <a name="test-traffic-manager-profile"></a>Testar o perfil do Gestor de Tráfego

Nesta secção, você verificará o nome de domínio do seu perfil de Gestor de Tráfego. Também configurará o ponto final principal para não estar disponível. Finalmente, pode ver que a aplicação web ainda está disponível. É porque o Gerente de Tráfego envia o tráfego para o ponto final de falha.

### <a name="check-the-dns-name"></a>Verifique o nome DNS

1. Na barra de pesquisa do portal, procure o nome do **perfil do Gestor de Tráfego** que criou na secção anterior.
2. Selecione o perfil do gestor de tráfego. A **visão geral** aparece.
3. O **Perfil do Gestor de Tráfego** mostra o nome DNS do perfil que acabou de criar.
  
   ![Screenshot da localização do nome DNS do seu Gestor de Tráfego](./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Ver o Gestor de Tráfego em ação

1. Num navegador web, insira o nome DNS do seu perfil de Gestor de Tráfego para ver o site padrão da sua Web App.

    > [!NOTE]
    > Neste cenário de arranque rápido, todos os pedidos de rota para o ponto final principal. Está definido para **a Prioridade 1**.

    ![Screenshot da página web para confirmar a disponibilidade do perfil do Gestor de Tráfego](./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png)

2. Para ver o Traffic Manager failover em ação, desative o seu site principal:
    1. Na página 'Perfil do Gestor de Tráfego', a partir da secção **Overview,** selecione **myPrimaryEndpoint**.
    2. No *myPrimaryEndpoint*, selecione **Disabled**  >  **Disabled Save**.
    3. Feche **o meuPrimaryEndpoint**. Note que o estado está **desativado** agora.
3. Copie o nome DNS do seu Perfil de Gestor de Tráfego a partir do passo anterior para ver o site numa nova sessão de navegador web.
4. Verifique se a aplicação web ainda está disponível.

O ponto final principal não está disponível, por isso foste encaminhado para o ponto final de falha.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar, elimine os grupos de recursos, aplicações web e todos os recursos relacionados. Para tal, selecione cada item individual do seu painel de instrumentos e **selecione Delete** no topo de cada página.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um perfil de Gestor de Tráfego. Permite-lhe direcionar o tráfego de utilizadores para aplicações web de alta disponibilidade. Para saber mais sobre o tráfego de encaminhamento, continue para os tutoriais do Traffic Manager.

> [!div class="nextstepaction"]
> [Traffic Manager tutorials](tutorial-traffic-manager-improve-website-response.md) (Tutoriais do Gestor de Tráfego)
