---
title: Início Rápido do Azure - criar um perfil do Front Door para elevada disponibilidade de aplicações através do portal do Azure
description: Este artigo de início rápido descreve como criar um Front Door para a sua aplicação Web global de elevada disponibilidade e elevado desempenho.
services: front-door
documentationcenter: ''
author: sharad4u
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2018
ms.author: sharadag
ms.openlocfilehash: 6bcd5bcc2463ec1ab9dcc97644d5046c31bfc78b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61461991"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Início rápido: Criar uma porta de entrada para um aplicativo web global de elevada disponibilidade

Este início rápido descreve como criar um perfil do Front Door que proporciona elevada disponibilidade e elevado desempenho à sua aplicação Web global. 

O cenário descrito no início rápido inclui duas instâncias de uma aplicação Web em execução em regiões do Azure diferentes. Uma configuração do Front Door com base em [back-ends ponderados iguais e com a mesma prioridade](front-door-routing-methods.md) é criada para ajudar a direcionar o tráfego para o conjunto mais próximo de back-ends locais que executam a aplicação. O Front Door monitoriza continuamente a aplicação Web e proporciona ativação pós-falha automática ao próximo back-end disponível quando o local mais próximo não está disponível.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure 
Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="prerequisites"></a>Pré-requisitos
Este início rápido requer que tenha implementado duas instâncias de uma aplicação Web em execução em regiões do Azure diferentes (*E.U.A. Leste* e *Europa Ocidental*). As duas instâncias da aplicação Web são executadas no modo Ativo/Ativo, ou seja, qualquer uma delas pode passar o tráfego em qualquer altura, ao contrário de uma configuração Ativo/Modo de espera, em que uma delas funciona como uma ativação pós-falha.

1. No canto superior esquerdo do ecrã, selecione **Criar um recurso** > **Web** > **Aplicação Web** > **Criar**.
2. Em **Aplicação Web**, introduza ou selecione as informações seguintes e introduza predefinições nas definições que estão vazias:

     | Definição         | Valor     |
     | ---              | ---  |
     | Name           | Introduza um nome exclusivo para a aplicação Web  |
     | Grupo de recursos          | Selecione **Novo** e escreva *myResourceGroupFD1* |
     | Plano do Serviço de Aplicações/Localização         | Selecione **Novo**.  No plano do Serviço de Aplicações, introduza *myAppServicePlanEastUS* e selecione **OK**. 
     |      Location  |   EUA Leste        |
    |||

3. Selecione **Criar**.
4. É criado um Website predefinido quando a aplicação Web for implementada com êxito.
5. Repita os passos 1 a 3 para criar um segundo Website noutra região do Azure com as seguintes definições:

     | Definição         | Valor     |
     | ---              | ---  |
     | Name           | Introduza um nome exclusivo para a aplicação Web  |
     | Grupo de recursos          | Selecione **Novo** e escreva *myResourceGroupFD2* |
     | Plano do Serviço de Aplicações/Localização         | Selecione **Novo**.  No plano do Serviço de Aplicações, introduza *myAppServicePlanWestEurope* e selecione **OK**. 
     |      Location  |   Europa Ocidental      |
    |||


## <a name="create-a-front-door-for-your-application"></a>Criar um Front Door para a sua aplicação
### <a name="a-add-a-frontend-host-for-front-door"></a>R. Adicionar um anfitrião de front-end ao Front Door
Crie uma configuração do Front Door que direciona o tráfego de utilizador com base na latência mais baixa entre os dois back-ends.

1. No canto superior esquerdo do ecrã, selecione **Criar um recurso** > **Rede** > **Front Door** > **Criar**.
2. Em **Criar um Front Door**, comece por adicionar as informações básicas e indique a subscrição onde pretende que o Front Door seja configurado. Da mesma forma, como qualquer outro recurso do Azure, também tem de fornecer um ResourceGroup e uma região do Grupo de Recursos, se estiver a criar um novo. Por último, tem de fornecer um nome para o Front Door.
3. Assim que as informações básicas estiverem preenchidas, o primeiro passo a definir é o **anfitrião de front-end** para a configuração. O resultado deve ser um nome de domínio válido como `myappfrontend.azurefd.net`. Este nome de anfitrião tem de ser globalmente exclusivo, mas o Front Door encarrega-se dessa validação. 

### <a name="b-add-application-backend-and-backend-pools"></a>B. Adicionar back-end da aplicação e conjuntos de back-ends

Em seguida, terá de configurar os back-ends da aplicação num conjunto de back-ends para o Front Door saber onde reside a aplicação. 

1. Clique no ícone de “+” para adicionar um conjunto de back-ends e, em seguida, especifique um **nome** para o conjunto de back-ends, por exemplo, `myBackendPool`.
2. Em seguida, clique em Adicionar Back-ends para adicionar os sites que criou anteriormente.
3. Selecione **Tipo de anfitrião de destino** como "Serviço de Aplicações", selecione a subscrição na qual criou o site e, em seguida, escolha o primeiro site do **Nome do anfitrião de destino**, ou seja, *myAppServicePlanEastUS.azurewebsites.net*.
4. Por enquanto, deixe os restantes campos na predefinição e clique em **Adicionar**.
5. Repita os passos 2 a 4 para adicionar o outro site, ou seja, *myAppServicePlanWestEurope.azurewebsites.net*
6. Pode optar por atualizar as definições de Sondas do Estado de Funcionamento e Balanceamento de Carga do conjunto de back-ends, mas os valores predefinidos também devem funcionar. Clique em **Adicionar**.


### <a name="c-add-a-routing-rule"></a>C. Adicionar uma regra de encaminhamento
Por último, clique no ícone de “+” em Regras de encaminhamento para configurar uma regra de encaminhamento. Esta é necessária para mapear o seu anfitrião de front-end para o conjunto de back-ends que, basicamente, está a configurar que, se chegar um pedido ao `myappfrontend.azurefd.net`, é reencaminhado para o conjunto de back-ends `myBackendPool`. Clique em **Adicionar** para adicionar a regra de encaminhamento para o Front Door. Deve agora estar pronto para criar o Front Door: clique em **Rever e Criar**.

>[!WARNING]
> **Tem** de certificar-se de que cada anfitrião de front-end no Front Door tem uma regra de encaminhamento com um caminho predefinido (“/\*”) associado ao mesmo. Ou seja, em todas as regras de encaminhamento tem de existir, pelo menos, uma regra de encaminhamento para cada anfitrião de front-end definida no caminho predefinido (“/\*”). Não o fazer pode resultar no tráfego de utilizador final não ser encaminhado corretamente.

## <a name="view-front-door-in-action"></a>Ver o Front Door em ação
Depois de criar um Front Door, irá demorar alguns minutos para que a configuração seja implementada globalmente em todos os lugares. Depois de concluir, aceda ao anfitrião de front-end que criou, ou seja, aceda a um browser e clique no URL `myappfrontend.azurefd.net`. O seu pedido é automaticamente encaminhado para o back-end mais próximo dos back-ends especificados no conjunto de back-ends. 

### <a name="view-front-door-handle-application-failover"></a>Ver o Front Door a processar a ativação pós-falha da aplicação
Se pretender testar a ativação pós-falha global instantânea do Front Door em ação, pode aceder a um dos sites que criou e pará-lo. Com base na definição de Sonda de Estado de Funcionamento definida para o conjunto de back-ends, será feita instantaneamente a ativação pós-falha do tráfego para a implementação do outro site. Também pode testar o comportamento, ao desativar o back-end na configuração do conjunto de back-ends para o Front Door. 

## <a name="clean-up-resources"></a>Limpar recursos
Quando já não for necessário, elimine os grupos de recursos, as aplicações Web e todos os recursos relacionados.

## <a name="next-steps"></a>Passos Seguintes
Neste início rápido, criou um Front Door que permite direcionar o tráfego de utilizador para aplicações Web que requerem elevada disponibilidade e desempenho máximo. Para saber mais sobre o encaminhamento de tráfego, leia os [Métodos de Encaminhamento](front-door-routing-methods.md) utilizados pelo Front Door.