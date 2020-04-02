---
title: 'Quickstart: Criar perfil porta da frente para alta disponibilidade de aplicações'
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
ms.openlocfilehash: 2b44c0cdbe2d955efe20a5f9473a29bc9f500a07
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521457"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Início Rápido: criar um Front Door para uma aplicação Web global de elevada disponibilidade

Este início rápido descreve como criar um perfil do Front Door que proporciona elevada disponibilidade e elevado desempenho à sua aplicação Web global. 

O cenário descrito no início rápido inclui duas instâncias de uma aplicação Web em execução em regiões do Azure diferentes. Uma configuração do Front Door com base em [back-ends ponderados iguais e com a mesma prioridade](front-door-routing-methods.md) é criada para ajudar a direcionar o tráfego para o conjunto mais próximo de back-ends locais que executam a aplicação. O Front Door monitoriza continuamente a aplicação Web e proporciona ativação pós-falha automática ao próximo back-end disponível quando o local mais próximo não está disponível.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure 
Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="prerequisites"></a>Pré-requisitos
Este início rápido requer que tenha implementado duas instâncias de uma aplicação Web em execução em regiões do Azure diferentes (*E.U.A. Leste* e *Europa Ocidental*). As duas instâncias da aplicação Web são executadas no modo Ativo/Ativo, ou seja, qualquer uma delas pode passar o tráfego em qualquer altura, ao contrário de uma configuração Ativo/Modo de espera, em que uma delas funciona como uma ativação pós-falha.

1. No lado superior esquerdo do ecrã, selecione **Criar uma** > **Aplicação****Web** > de recursos .
2. Em **Aplicação Web**, introduza ou selecione as informações seguintes e introduza predefinições nas definições que estão vazias:

     | Definição         | Valor     |
     | ---              | ---  |
     | Grupo de recursos          | Selecione **Novo** e escreva *myResourceGroupFD1* |
     | Nome           | Introduza um nome exclusivo para a aplicação Web  |
     | Pilha de tempo de execução          | Selecione uma stack Runtime para a sua aplicação |
     |      Região  |   E.U.A. Oeste        |
     | Plano do Serviço de Aplicações/Localização         | Selecione **New**.  No plano do Serviço de Aplicações, introduza *myAppServicePlanEastUS* e selecione **OK**.| 
     |SKU e tamanho  | Selecione **Alterar Tamanho** Selecione **ACU total S1 100, 1.75 GB de memória** |
     
3. Selecione **Rever + Criar**.
4. Reveja as informações sumárias para a Web App. Selecione **Criar**.
5. Após aproximadamente 5 minutos, um site predefinido é criado quando a Web App é implementada com sucesso.
6. Repita os passos 1 a 3 para criar um segundo Website noutra região do Azure com as seguintes definições:

     | Definição         | Valor     |
     | ---              | ---  |
     | Grupo de recursos          | Selecione **Novo** e escreva *myResourceGroupFD2* |
     | Nome           | Introduza um nome exclusivo para a aplicação Web  |
     | Pilha de tempo de execução          | Selecione uma stack Runtime para a sua aplicação |
     |      Região  |   Europa ocidental      |
     | Plano do Serviço de Aplicações/Localização         | Selecione **New**.  No plano do Serviço de Aplicações, introduza *myAppServicePlanWestEurope* e selecione **OK**.|   
     |SKU e tamanho  | Selecione **Alterar Tamanho** Selecione **ACU total S1 100, 1.75 GB de memória** |
    
## <a name="create-a-front-door-for-your-application"></a>Criar um Front Door para a sua aplicação
### <a name="a-add-a-frontend-host-for-front-door"></a>R. Adicionar um anfitrião de front-end ao Front Door
Crie uma configuração do Front Door que direciona o tráfego de utilizador com base na latência mais baixa entre os dois back-ends.

1. No lado superior esquerdo do ecrã, selecione **Criar uma** > **porta frontal**de**rede** > de recursos .
2. Em **Criar uma Porta Frontal,** introduza ou selecione as seguintes informações e introduza as definições predefinidas sempre que nenhuma esteja especificada:

     | Definição         | Valor     |
     | ---              | ---  |
     |Subscrição  | Selecione a subscrição que deseja a Porta Da Frente criada.|
     | Grupo de recursos          | Selecione **Novo**, e, em seguida, digite *myResourceGroupFD0* |
     | Localização do grupo de recursos  |   E.U.A. Central        |
     
     > [!NOTE]
     > Não precisa de criar um novo Grupo de Recursos para implantar a Porta da Frente.  Se também pode selecionar um Grupo de Recursos existente.
     
3. Clique em **seguinte: Configuração**.
4. Clique no ícone '+' no cartão Frontends/domínios.  Para o `<Your Initials>frontend` **nome anfitrião** insira . Este nome de anfitrião tem de ser globalmente único, a Porta da Frente cuidará da validação.
5. Clique em **Adicionar**.

### <a name="b-add-application-backend-and-backend-pools"></a>B. Adicionar back-end da aplicação e conjuntos de back-ends

Em seguida, você precisa configurar os seus Frontends/domínios e backend(s) de aplicação em uma piscina de backend para a Porta Frontal para saber onde a sua aplicação reside. 

1. Clique no ícone '+' no cartão de piscinas Backend para adicionar uma `myBackendPool`piscina de backend para o **nome** para a sua piscina de backend, insira .
2. Em seguida, clique em **Adicionar um backend** para adicionar os seus websites criados anteriormente.
3. Selecione o tipo de **anfitrião Backend** como 'App Service', selecione a subscrição na qual criou o web site e, em seguida, escolha o primeiro web site a partir do dropdown do nome do **anfitrião Backend.**
4. Por enquanto, deixe os restantes campos na predefinição e clique em **Adicionar**.
5. Selecione o tipo de **anfitrião Backend** como 'App Service', selecione a subscrição na qual criou o web site e, em seguida, escolha o **segundo** web site a partir do dropdown do nome do **anfitrião Backend.**
6. Por enquanto, deixe os restantes campos na predefinição e clique em **Adicionar**.
7. Pode optar opcionalmente por atualizar as sondas de saúde e as definições de equilíbrio de carga para o pool backend, mas os valores predefinidos também devem funcionar. Em qualquer dos casos, clique em **Adicionar**.


### <a name="c-add-a-routing-rule"></a>C. Adicionar uma regra de encaminhamento
1. Por último, clique no ícone '+' no cartão de regras de encaminhamento para configurar uma regra de encaminhamento. Esta é necessária para mapear o seu anfitrião de front-end para o conjunto de back-ends que, basicamente, está a configurar que, se chegar um pedido ao `myappfrontend.azurefd.net`, é reencaminhado para o conjunto de back-ends `myBackendPool`. 
2. Para **nome** introduza 'LocationRule'.
3. Clique em **Adicionar** para adicionar a regra de encaminhamento para o Front Door. 
4. Clique em **Rever e Criar**.
5. Reveja as definições para a criação da Porta Da Frente. Clique em **Criar**

>[!WARNING]
> **Tem** de certificar-se de que cada anfitrião de front-end no Front Door tem uma regra de encaminhamento com um caminho predefinido (“/\*”) associado ao mesmo. Ou seja, em todas as regras de encaminhamento tem de existir, pelo menos, uma regra de encaminhamento para cada anfitrião de front-end definida no caminho predefinido (“/\*”). Se não o fizer, o tráfego de utilizador final poderá não ser encaminhado corretamente.

## <a name="view-front-door-in-action"></a>Ver o Front Door em ação
Depois de criar um Front Door, irá demorar alguns minutos para que a configuração seja implementada globalmente em todos os lugares. Depois de concluir, aceda ao anfitrião de front-end que criou, ou seja, aceda a um browser e clique no URL `myappfrontend.azurefd.net`. O seu pedido é automaticamente encaminhado para o back-end mais próximo dos back-ends especificados no conjunto de back-ends. 

### <a name="view-front-door-handle-application-failover"></a>Ver o Front Door a processar a ativação pós-falha da aplicação
Se pretender testar a ativação pós-falha global instantânea do Front Door em ação, pode aceder a um dos sites que criou e pará-lo. Com base na definição de Sonda de Estado de Funcionamento definida para o conjunto de back-ends, será feita instantaneamente a ativação pós-falha do tráfego para a implementação do outro site. Também pode testar o comportamento, ao desativar o back-end na configuração do conjunto de back-ends para o Front Door. 

## <a name="clean-up-resources"></a>Limpar recursos
Quando já não for necessário, elimine os grupos de recursos **myResourceGroupFD1**, **myResourceGroupFD2**e **myResourceGroupFD0:**

## <a name="next-steps"></a>Passos seguintes
Neste início rápido, criou um Front Door que permite direcionar o tráfego de utilizador para aplicações Web que requerem elevada disponibilidade e desempenho máximo. Para saber mais sobre o encaminhamento de tráfego, leia os [Métodos de Encaminhamento](front-door-routing-methods.md) utilizados pelo Front Door.
