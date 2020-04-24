---
title: 'Quickstart: Configurar alta disponibilidade com o Serviço de Porta Frontal Azure'
description: Este quickstart descreve como usar o Serviço de Porta Frontal Azure para a sua aplicação web global altamente disponível e de alto desempenho.
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
ms.date: 04/27/2020
ms.author: sharadag
ms.openlocfilehash: e7e500f0459c0f5fd4039acf316d9469e1567a09
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116987"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Início Rápido: criar um Front Door para uma aplicação Web global de elevada disponibilidade

Inicie-se com a Porta Frontal Azure utilizando o portal Azure para configurar uma alta disponibilidade para uma aplicação web.

Neste arranque rápido, a Azure Front Door junta dois casos de uma aplicação web que corre em diferentes regiões de Azure. Cria-se uma configuração da Porta Da Frente baseada em apoios iguais ponderados e prioritários. Esta configuração direciona o tráfego para o local mais próximo que executa a aplicação. A Porta Frontal Azure monitoriza continuamente a aplicação web. O serviço fornece falha automática ao próximo site disponível quando o site mais próximo não estiver disponível.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-two-instances-of-a-web-app"></a>Criar duas instâncias de uma aplicação web

Este quickstart requer dois casos de uma aplicação web que executa em diferentes regiões do Azure. Ambas as instâncias de aplicação web funcionam no modo *Ative/Ative,* para que qualquer um possa tomar tráfego. Esta configuração difere de uma configuração *Ative/Stand-By,* onde se funciona como uma falha.

Se ainda não tem uma aplicação web, use os seguintes passos para configurar aplicações web exemplo.

1. Inicie sessão no portal do Azure em https://portal.azure.com.

1. A partir da página inicial ou do menu Azure, selecione **Criar um recurso**.

1. Selecione **Web** > **Web App**.

   ![Criar uma aplicação Web no portal do Azure](media/quickstart-create-front-door/create-web-app-for-front-door.png)

1. Na **Aplicação Web,** selecione a **Subscrição** para utilizar.

1. Para **o Grupo de Recursos,** selecione Criar **novo**. Introduza *FrontDoorQS_rg1* para o **nome** e selecione **OK**.

1. Em **Detalhes de Instância**, introduza um **nome** único para a sua aplicação web. Este exemplo utiliza *o WebAppContoso-1*.

1. Selecione uma **stack Runtime**, neste exemplo, *.NET Core 2.1 (LTS)*.

1. Selecione uma região, como o *Centro dos EUA.*

1. Para **o Plano Windows,** selecione **Criar novo**. Introduza *myAppServicePlanCentralUS* para **nome** e selecione **OK**.

1. Certifique-se de que o **Sku e** o tamanho são **Standard S1 100 total ACU, 1.75 GB de memória**.

1. Selecione **Rever + criar,** rever o **Resumo,** e depois selecione **Criar**. Pode levar alguns minutos para a implantação ser concluída.

   ![Resumo de revisão para web app](media/quickstart-create-front-door/summary-for-web-app-for-front-door.png)

Depois da sua implementação estar concluída, crie uma segunda aplicação web. Utilize o mesmo procedimento com os mesmos valores, com exceção dos seguintes valores:

| Definição          | Valor     |
| ---              | ---  |
| **Grupo de recursos**   | Selecione **Novo** e *introduza FrontDoorQS_rg2* |
| **Nome**             | Insira um nome único para a sua Web App, neste exemplo, *WebAppContoso-2*  |
| **Região**           | Uma região diferente, neste exemplo, *Centro-Sul dos EUA* |
| **Plano** > de serviço de aplicações**Plano Windows**         | Selecione **Novo** e introduza *o myAppServicePlanSouthCentralUS*, e depois selecione **OK** |

## <a name="create-a-front-door-for-your-application"></a>Criar um Front Door para a sua aplicação

Configure a Porta Frontal do Azure para direcionar o tráfego do utilizador com base na latência mais baixa entre os dois servidores de aplicações web. Para começar, adicione um anfitrião frontal para a Porta da Frente Azure.

1. A partir da página inicial ou do menu Azure, selecione **Criar um recurso**. Selecione**porta frontal** **de** > rede .

1. Em **Criar uma porta da frente,** selecione uma **Subscrição**.

1. Para **o grupo Recursos**, selecione **New,** em seguida, *introduza FrontDoorQS_rg0* e selecione **OK**.  Em vez disso, pode utilizar um grupo de recursos existente.

1. Se criou um grupo de recursos, selecione uma localização do **grupo Resource,** e, em seguida, selecione **Seguinte: Configuração**.

1. Em **Frontends/domínios,** selecione **+** para abrir Adicionar um hospedeiro **frontal**.

1. Para **nome anfitrião,** insira um nome de anfitrião globalmente único. Este exemplo utiliza *contoso-frontend*. Selecione **Adicionar**.

   ![Adicione um anfitrião frontend para a porta da frente de Azure](media/quickstart-create-front-door/add-frontend-host-for-front-door.png)

Em seguida, crie um backend pool que contenha as suas duas aplicações web.

1. Ainda em **Criar uma Porta da Frente,** em **piscinas backend,** selecione **+** para abrir Adicione uma piscina de **backend**.

1. Para **Nome**, insira *myBackendPool*.

1. Selecione **Adicionar um backend**. Para o tipo de **anfitrião Backend,** selecione *App Service*.

1. Selecione a sua subscrição e, em seguida, escolha a primeira aplicação web que criou a partir do nome do **anfitrião Backend**. Neste exemplo, a aplicação web foi *WebAppContoso-1*. Selecione **Adicionar**.

1. Selecione **Adicionar um backend** novamente. Para o tipo de **anfitrião Backend,** selecione *App Service*.

1. Selecione a sua subscrição, novamente, e escolha a segunda aplicação web que criou a partir do nome de **anfitrião Backend**. Selecione **Adicionar**.

   ![Adicione um anfitrião de backend à sua porta da frente](media/quickstart-create-front-door/add-backend-host-to-pool-for-front-door.png)

Finalmente, adicione uma regra de encaminhamento. Uma regra de encaminhamento mapeia o seu anfitrião frontal para a piscina de backend. A regra reme `contoso-frontend.azurefd.net` um pedido para **o myBackendPool.**

1. Ainda em **Criar uma Porta da Frente**, nas regras de **Encaminhamento,** selecione **+** para configurar uma regra de encaminhamento.

1. Em **Adicionar uma regra**, para **nome**, introduza A Regra *de Localização*. Aceite todos os valores predefinidos e, em seguida, selecione **Adicionar** para adicionar a regra de encaminhamento.

   >[!WARNING]
   > Deve **must** certificar-se de que cada um dos anfitriões frontais da`\*`sua Porta da Frente tem uma regra de encaminhamento com um caminho padrão () associado a ela. Isto é, em todas as suas regras de encaminhamento deve haver pelo menos uma regra`\*`de encaminhamento para cada um dos seus anfitriões frontais definidos no caminho padrão ( ). Se não o fizer, o tráfego de utilizadores finais não é corretamente encaminhado.

1. Selecione **Rever + Criar,** e depois **Criar**.

   ![Porta da frente azure configurada](media/quickstart-create-front-door/configuration-of-front-door.png)

## <a name="view-azure-front-door-in-action"></a>Ver porta da frente azure em ação

Uma vez criado uma Porta Da Frente, leva alguns minutos para que a configuração seja implantada globalmente. Uma vez concluído, aceda ao anfitrião frontal que criou. Num navegador, vá `contoso-frontend.azurefd.net`para . O seu pedido será automaticamente encaminhado para o servidor mais próximo a partir dos servidores especificados na piscina de backend.

Se criou estas aplicações neste arranque rápido, verá uma página de informação.

Para testar a falha global instantânea em ação, experimente os seguintes passos:

1. Abra um navegador, como descrito acima, e `contoso-frontend.azurefd.net`vá para o endereço frontal: .

1. No portal Azure, procure e selecione *serviços de Aplicações.* Percorra para baixo para encontrar uma das suas aplicações web, **WebAppContoso-1** neste exemplo.

1. Selecione a sua aplicação web e, em seguida, selecione **Stop**, e **Sim** para verificar.

1. Atualize o seu browser. Devia ver a mesma página de informação.

   >[!TIP]
   >Há um pequeno atraso para estas ações. Talvez precise refrescar-se.

1. Encontre a outra aplicação web e pare-a também.

1. Atualize o seu browser. Desta vez, deve ver uma mensagem de erro.

   ![Ambos os casos da aplicação web pararam](media/quickstart-create-front-door/service-has-been-stopped.png)

## <a name="clean-up-resources"></a>Limpar recursos

Depois de terminar, pode remover todos os itens que criou. A eliminação de um grupo de recursos também elimina o seu conteúdo. Se não pretende usar esta Porta da Frente, deve remover recursos para evitar encargos desnecessários.

1. No portal Azure, procure e selecione **grupos de Recursos,** ou selecione **grupos de recursos** do menu do portal Azure.

1. Filtrar ou rolar para baixo para encontrar um grupo de recursos, como **FrontDoorQS_rg0**.

1. Selecione o grupo de recursos e, em seguida, **selecione Eliminar o grupo de recursos**.

   >[!WARNING]
   >Esta ação é irreversível.

1. Digite o nome do grupo de recursos para verificar e, em seguida, **selecione Eliminar**.

Repita o procedimento para os outros dois grupos.

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para aprender a adicionar um domínio personalizado à sua Porta da Frente.
> [!div class="nextstepaction"]
> [Adicionar um domínio personalizado](front-door-custom-domain.md)

Para saber mais sobre o tráfego de encaminhamento, consulte os métodos de [encaminhamento da Porta Da Frente](front-door-routing-methods.md).
