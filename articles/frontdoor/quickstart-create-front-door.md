---
title: 'Quickstart: Configurar alta disponibilidade com o serviço Azure Front Door'
description: Este quickstart descreve como usar o Azure Front Door Service para a sua aplicação web global altamente disponível e de alto desempenho.
services: front-door
documentationcenter: ''
author: duongau
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2020
ms.author: duau
ms.openlocfilehash: ab59de49b7cf625220f8a803aab9d2b9a2ae5937
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89398772"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Início Rápido: criar um Front Door para uma aplicação Web global de elevada disponibilidade

Começa com a Porta Frontal Azure utilizando o portal Azure para configurar uma alta disponibilidade para uma aplicação web.

Neste quickstart, a Azure Front Door junta dois casos de uma aplicação web que funciona em diferentes regiões do Azure. Cria-se uma configuração da Porta frontal baseada em apoios iguais ponderados e as mesmas prioridades. Esta configuração direciona o tráfego para o local mais próximo que executa a aplicação. A porta frontal Azure monitoriza continuamente a aplicação web. O serviço fornece falha automática para o próximo site disponível quando o site mais próximo está indisponível.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-two-instances-of-a-web-app"></a>Criar duas instâncias de uma aplicação web

Este quickstart requer duas instâncias de uma aplicação web que funciona em diferentes regiões do Azure. Ambas as instâncias de aplicação web são executadas no modo *Ative/Ative,* para que qualquer um possa tomar o tráfego. Esta configuração difere de uma configuração *Ative/Stand-By,* onde se atua como um failover.

Se ainda não tiver uma aplicação web, use os seguintes passos para definir exemplo de aplicações web.

1. Inicie sessão no portal do Azure em https://portal.azure.com.

1. A partir da página inicial ou do menu Azure, **selecione Criar um recurso**.

1. Selecione **Web** > **Aplicação Web**.

   ![Criar uma aplicação Web no portal do Azure](media/quickstart-create-front-door/create-web-app-azure-front-door.png)

1. Na **Web App,** selecione a **Subscrição** a utilizar.

1. Para **o Grupo de Recursos**, selecione Criar **novos**. Introduza *FrontDoorQS_rg1* para o **Nome** e selecione **OK**.

1. Em **Detalhes de Exemplo,** insira um **nome** único para a sua aplicação web. Este exemplo utiliza *o WebAppContoso-1*.

1. Selecione uma **pilha de tempo de execução,** neste exemplo, *.NET Core 2.1 (LTS)*.

1. Selecione uma região, como *a Central US.*

1. Para **o Plano do Windows**, selecione Criar **novos**. Insira *o myAppServicePlanCentralUS* para **nome** e selecione **OK**.

1. Certifique-se de que o **Sku e** o tamanho **são normais S1 100 total ACU, memória de 1,75 GB**.

1. Selecione **Rever + criar,** rever o **Resumo**e, em seguida, selecione **Criar**. Pode levar alguns minutos para a colocação estar completa.

   ![Resumo de revisão para aplicação web](media/quickstart-create-front-door/web-app-summary-azure-front-door.png)

Depois da sua implementação estar concluída, crie uma segunda aplicação web. Utilizar o mesmo procedimento com os mesmos valores, com exceção dos seguintes valores:

| Definição          | Valor     |
| ---              | ---  |
| **Grupo de recursos**   | Selecione **Novo** e *introduza FrontDoorQS_rg2* |
| **Nome**             | Insira um nome único para a sua Web App, neste exemplo, *WebAppContoso-2*  |
| **Região**           | Uma região diferente, neste exemplo, *South Central US* |
| **Plano de Serviço de**  >  Aplicações **Plano Windows**         | Selecione **Novo** e insira *myAppServicePlanSouthCentralUS,* e depois selecione **OK** |

## <a name="create-a-front-door-for-your-application"></a>Criar um Front Door para a sua aplicação

Configure a Porta Frontal Azure para direcionar o tráfego do utilizador com base na latência mais baixa entre os dois servidores de aplicações web. Para começar, adicione um anfitrião frontal para a Porta frontal Azure.

1. A partir da página inicial ou do menu Azure, **selecione Criar um recurso**. **Selecione porta**frontal de rede  >  **Front Door**.

1. In **Create a Front Door**, selecione uma **subscrição**.

1. Para **o grupo de recursos**, selecione **New**, em seguida, *introduza FrontDoorQS_rg0* e selecione **OK**.  Em vez disso, pode utilizar um grupo de recursos existente.

1. Se criou um grupo de recursos, selecione uma **localização do grupo de Recursos**e, em seguida, selecione **Seguinte: Configuração**.

1. Em **Frontends/domínios,** selecione **+** para abrir Adicione um **anfitrião frontend**.

1. Para **o nome hospedeiro,** insira um nome de anfitrião globalmente único. Este exemplo utiliza *contoso-frontend*. Selecione **Add** (Adicionar).

   ![Adicione um anfitrião frontal para Azure Front Door](media/quickstart-create-front-door/add-frontend-host-azure-front-door.png)

Em seguida, crie uma piscina de backend que contenha as suas duas aplicações web.

1. Ainda em **Criar uma Porta frontal,** em **piscinas backend,** selecione **+** para abrir Adicione uma piscina de **backend**.

1. Para **nome**, insira *myBackendPool*.

1. **Selecione Adicione um backend**. Para **o tipo de anfitrião Backend**, selecione App *Service*.

1. Selecione a sua subscrição e, em seguida, escolha a primeira aplicação web que criou a partir do nome de **anfitrião Backend.** Neste exemplo, a aplicação web era *WebAppContoso-1*. Selecione **Add** (Adicionar).

1. **Selecione Adicione novamente um backend.** Para **o tipo de anfitrião Backend**, selecione App *Service*.

1. Selecione a sua subscrição, novamente, e escolha a segunda aplicação web que criou a partir do nome de **anfitrião Backend.** Selecione **Add** (Adicionar).

   ![Adicione um anfitrião de backend à sua Porta da Frente](media/quickstart-create-front-door/add-backend-host-pool-azure-front-door.png)

Finalmente, adicione uma regra de encaminhamento. Uma regra de encaminhamento mapeia o seu anfitrião frontal para a piscina de backend. A regra remete um pedido `contoso-frontend.azurefd.net` para **o myBackendPool**.

1. Ainda em **Criar uma Porta frontal**, nas **regras de encaminhamento**, selecione **+** para configurar uma regra de encaminhamento.

1. In **Adicionar uma regra**, para **nome,** *insira o LocalizaçãoRule*. Aceite todos os valores predefinidos e, em seguida, **selecione Adicione** para adicionar a regra de encaminhamento.

   >[!WARNING]
   > **Deve** certificar-se de que cada um dos anfitriões frontais da porta da frente tem uma regra de encaminhamento com um caminho predefinido `\*` () associado a ela. Ou seja, em todas as suas regras de encaminhamento deve haver pelo menos uma regra de encaminhamento para cada um dos seus anfitriões frontend definidos no caminho padrão `\*` (). Se não o fizer, poderá resultar em que o tráfego do utilizador final não seja corretamente encaminhado.

1. Selecione **Rever + Criar**e, em seguida, **Criar**.

   ![Porta frontal Azure configurada](media/quickstart-create-front-door/configuration-azure-front-door.png)

## <a name="view-azure-front-door-in-action"></a>Ver Azure Front Door em ação

Uma vez que você cria uma porta frontal, leva alguns minutos para a configuração ser implantada globalmente. Uma vez concluído, aceda ao anfitrião frontal que criou. Em um navegador, vá a `contoso-frontend.azurefd.net` . O seu pedido será automaticamente encaminhado para o servidor mais próximo para si a partir dos servidores especificados na piscina de backend.

Se criou estas aplicações neste arranque rápido, verá uma página de informação.

Para testar o failover global instantâneo em ação, experimente os seguintes passos:

1. Abra um browser, como descrito acima, e vá para o endereço frontal: `contoso-frontend.azurefd.net` .

1. No portal Azure, procure e selecione *os serviços de Aplicação.* Desloque-se para baixo para encontrar uma das suas aplicações web, **WebAppContoso-1** neste exemplo.

1. Selecione a sua aplicação web e, em seguida, **selecione Stop**e **Yes** para verificar.

1. Atualize o seu browser. Devia ver a mesma página de informação.

   >[!TIP]
   >Há um pouco de atraso para estas ações. Talvez precises de voltar a refrescar-te.

1. Encontre a outra aplicação web, e pare-a também.

1. Atualize o seu browser. Desta vez, deve ver uma mensagem de erro.

   ![Ambas as instâncias da aplicação web pararam](media/quickstart-create-front-door/web-app-stopped-message.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de terminar, pode remover todos os itens que criou. A eliminação de um grupo de recursos também elimina o seu conteúdo. Se não pretender usar esta Porta frontal, deve remover recursos para evitar cargas desnecessárias.

1. No portal Azure, procure e selecione **grupos de Recursos,** ou selecione **grupos** de Recursos a partir do menu do portal Azure.

1. Filtre ou desloque-se para baixo para encontrar um grupo de recursos, como **FrontDoorQS_rg0**.

1. Selecione o grupo de recursos e, em seguida, **selecione Eliminar grupo de recursos**.

   >[!WARNING]
   >Esta ação é irreversível.

1. Digite o nome do grupo de recursos para verificar e, em seguida, **selecione Delete**.

Repita o procedimento para os outros dois grupos.

## <a name="next-steps"></a>Próximos passos

Avance para o próximo artigo para aprender a adicionar um domínio personalizado à sua Porta da Frente.
> [!div class="nextstepaction"]
> [Adicionar um domínio personalizado](front-door-custom-domain.md)

Para saber mais sobre o tráfego de encaminhamento, consulte [os métodos de encaminhamento da Porta frontal](front-door-routing-methods.md).
