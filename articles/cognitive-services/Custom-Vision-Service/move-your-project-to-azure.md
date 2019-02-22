---
title: Mover um projeto de avaliação limitado para o Azure
titlesuffix: Azure Cognitive Services
description: Saiba como mover um projeto de versão de avaliação limitada para o Azure.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: 76207e83e31ba3919da80e4ecc99435e88c76c66
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56654590"
---
# <a name="how-to-move-your-limited-trial-project-to-azure-using-the-customvisionai-site"></a>Como mover o seu projeto de versão de avaliação limitada para o Azure com o site de CustomVision.ai


Como o serviço de visão personalizada está agora na [pré-visualização do Azure](https://azure.microsoft.com/services/preview/), descontinuar o suporte a projetos de versão de avaliação limitada fora do Azure. Este documento irá ensiná-lo utilizar o [Web site de visão personalizada](https://customvision.ai) para mover o seu projeto de versão de avaliação limitada a ser associado um recurso do Azure. 

> [!NOTE]
> Quando move seus projetos de visão personalizada para um recurso do Azure, eles herdar subjacente [permissões]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) desse recurso do Azure. Se a outros utilizadores na sua organização são proprietários do recurso do Azure está de seu projeto no, poderão aceder ao seu projeto no [Web site de visão personalizada](https://customvision.ai). Da mesma forma, a eliminar os recursos de mensagens em fila irá eliminar o seus projetos.  


Para obter uma introdução a conceitos do Azure das subscrições e recursos, consulte o [Guia do programador do Azure.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)


## <a name="prerequisites"></a>Pré-requisitos

Precisará de uma subscrição do Azure válida associada com a mesma conta Microsoft ou a conta do Azure Active Directory (AAD) utiliza para iniciar sessão na [Web site de visão personalizada](https://customvision.ai). 

Se não tiver uma conta do Azure, [criar uma conta](https://azure.microsoft.com/free/) gratuitamente.


## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Criar recursos de visão personalizada no portal do Azure
Para utilizar o serviço de visão personalizada com o Azure, terá de criar recursos de formação de visão personalizada e predição no [portal do Azure](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

 Para mover o seu projeto através desta [Web site de visão personalizada](https://customvision.ai) experiência, tem de criar seus recursos da região Centro-Sul, uma vez que todos os projetos de versão de avaliação limitada são alojados no centro-Sul. 

Vários projetos podem ser associados a um recurso único. Mais detalhes sobre [preços e limites](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) está disponível. Para continuar a utilizar gratuitamente o serviço de visão personalizada, pode selecionar o escalão de F0 no portal do Azure. 


## <a name="move-your-limited-trial-project-to-an-azure-resource"></a>Mover o seu projeto de versão de avaliação limitada para um recurso do Azure

1.  No seu browser, navegue para o [Web site de visão personalizada](https://customvision.ai) e selecione __iniciar sessão__. Abra o projeto que pretende migrar para uma conta do Azure. 
2.  Abra a página de definições para o seu projeto clicando no ícone de engrenagem no canto superior direita do ecrã. 

    ![Definições do projeto é o ícone de engrenagem no canto superior direito da página do projeto.](./media/move-your-project-to-azure/settings-icon.png)


3. Clique em __mover para o Azure__.

    ![Mudança para o botão do Azure está no canto inferior esquerdo da página Definições do projeto.](./media/move-your-project-to-azure/move-to-azure.jpg)


4. Na lista pendente na __mover para o Azure__ botão, selecione o recurso do Azure, deseja mover o seu projeto para. Clique em __mover__. 

5. Se não vir o recurso do Azure que criou anteriormente para o serviço de visão personalizada, pode ser noutro diretório. Para mover o seu projeto para um recurso noutro diretório, siga as instruções abaixo. 

    ![Janela de migração do projeto.](./media/move-your-project-to-azure/Project_Migration_Window.jpg)


## <a name="move-your-limited-trial-project-to-an-azure-resource-in-another-directory"></a>Mover o seu projeto de versão de avaliação limitada para um recurso do Azure noutro diretório 

> [!NOTE]
> No portal do Azure tanto CustomVision.ai, pode selecionar o seu diretório no menu pendente de utilizador no canto superior direito da tela.   


1. Identifique o diretório que seu recurso do Azure está em. Pode encontrar o diretório listado em seu nome de utilizador no canto superior direito da barra de menu do portal do Azure. 

    ![O diretório está listado em seu nome de utilizador no canto superior direito da barra de menu do portal do Azure. .](./media/move-your-project-to-azure/identify_directory.jpg)

2. Localize o ID de recurso do seu recurso de formação de visão personalizada. Pode encontrar isto no portal do Azure abrindo o seu recurso de formação de visão personalizada e selecionando "Propriedades" na seção "Gerenciamento de recursos". O ID de recurso estará lá. 

    ![Encontre o seu ID de recurso no portal do Azure abrindo o seu recurso de formação de visão personalizada e selecionando "Propriedades" na seção "Gerenciamento de recursos".](./media/move-your-project-to-azure/resource_ID_azure_portal.jpg)


3. Em alternativa, pode encontrar o ID de recurso do seu recurso de visão personalizada diretamente no site de visão personalizada [página de definições]( https://www.customvision.ai/projects#/settings). Terá de mudar para o seu recurso do Azure está no mesmo diretório.

    ![O ID de recurso está listado para cada recurso na sua página de definições no site da visão personalizada.](./media/move-your-project-to-azure/resource_ID_CVS_portal.jpg)

4. Agora que tem o seu ID de recurso, retorne ao projeto de visão personalizada que está a tentar mover de uma versão de avaliação limitada para um recurso do Azure. Lembrete, poderá ter de voltar para o diretório original para encontrá-lo. Siga as instruções fornecidas [acima](#move-your-limited-trial-project-to-an-azure-resource) para abrir a página de definições do projeto e selecione __mover para o Azure__. 


5. Na mudança para a janela do Azure, selecione a caixa para "Mover para outro diretório do Azure?". Selecione o diretório que pretende mover o seu projeto para e introduza o ID de recurso do recurso que está a mover o seu projeto para. Clique em __mover__. 



5. Lembre-se de que seu projeto está agora num diretório diferente. Para encontrar seu projeto, terá de mudar para o mesmo diretório no portal web de visão personalizada que pertença a seu projeto. No portal do Azure e o [Web site de visão personalizada](https://customvision.ai), pode selecionar o seu diretório no menu de conta de baixo no canto superior direito da tela. 


