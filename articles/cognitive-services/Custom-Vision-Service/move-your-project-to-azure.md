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
ms.openlocfilehash: 6fac6531ea0a39796de13f95aee33b30dc91f131
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59056882"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>Como mover o seu projeto de versão de avaliação limitada para o Azure

Como o serviço de visão personalizada conclui sua mudança para o Azure, suporte a projetos de versão de avaliação limitada fora do Azure está a terminar. Este documento mostram-lhe como utilizar as APIs de visão personalizada para copiar o seu projeto de versão de avaliação limitada para um recurso do Azure.

Suporte para a visualização de projetos de versão de avaliação limitada no [Web site de visão personalizada](https://customvision.ai) terminou a 25 de Março de 2019. Este documento agora mostra-lhe como utilizar as APIs de visão personalizada com um [script de python de migração](https://github.com/Azure-Samples/custom-vision-move-project) no GitHub) para duplicar o seu projeto para um recurso do Azure.

Para obter mais detalhes, incluindo prazos importantes no processo de preterição avaliação limitada,. veja a [notas de versão](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019) ou para comunicações por e-mail enviadas para os proprietários de projetos de avaliação limitados.

O [script de migração](https://github.com/Azure-Samples/custom-vision-move-project) permite-lhe recriar um projeto ao transferir e, em seguida, carregar todas as etiquetas, regiões e imagens em sua iteração atual. Ele irá deixá-lo com um novo projeto na sua nova subscrição, que, em seguida, pode treinar.

## <a name="prerequisites"></a>Pré-requisitos

- Precisará de uma subscrição do Azure válida associada com a conta Microsoft ou a conta do Azure Active Directory (AAD) que pretende utilizar para iniciar sessão no [Web site de visão personalizada](https://customvision.ai). 
    - Se não tiver uma conta do Azure, [criar uma conta](https://azure.microsoft.com/free/) gratuitamente.
    - Para obter uma introdução a conceitos do Azure das subscrições e recursos, consulte a [Guia do programador do Azure.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions).
-  [Python](https://www.python.org/downloads/)
- [Pip](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Criar recursos de visão personalizada no portal do Azure

Para utilizar o serviço de visão personalizada com o Azure, terá de criar recursos de formação de visão personalizada e predição no [portal do Azure](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

Vários projetos podem ser associados a um recurso único. Mais detalhes sobre [preços e limites](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) está disponível. Para continuar a utilizar gratuitamente o serviço de visão personalizada, pode selecionar o escalão de F0 no portal do Azure. 

> [!NOTE]
> Ao mover o seu projeto de visão personalizada para um recurso do Azure, ele herda subjacente [permissões]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) desse recurso do Azure. Se a outros utilizadores na sua organização são proprietários do recurso do Azure está de seu projeto no, poderão aceder ao seu projeto no [Web site de visão personalizada](https://customvision.ai). Da mesma forma, a eliminar os recursos de mensagens em fila irá eliminar o seus projetos.  

## <a name="find-your-limited-trial-project-information"></a>Localizar as informações do projeto de avaliação limitada

Para mover o seu projeto, terá do _ID do projeto_ e _chave de treinamento_ para o projeto está a tentar migrar. Se não tiver estas informações, visite [ https://limitedtrial.customvision.ai/projects ](https://limitedtrial.customvision.ai/projects) para obter o ID e a chave para cada um dos seus projetos. 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>Utilize o código de exemplo do Python para copiar o seu projeto para o Azure

Siga os [instruções de código de exemplo](https://github.com/Azure-Samples/custom-vision-move-project), utilizando a sua chave de avaliação limitada e ID do projeto como os materiais de "origem" e a chave do novo recurso do Azure que criou como o "destino".

Por predefinição, todos os projetos de versão de avaliação limitada estão alojados na região Central-do Azure do Sul.

## <a name="next-steps"></a>Passos Seguintes

O projeto agora foi movido para um recurso do Azure. Terá de atualizar as chaves de formação e predição em qualquer aplicativo que escreveu.

Para ver o seu projeto sobre o [Web site de visão personalizada](https://customvision.ai), inicie sessão com a mesma conta utilizada para iniciar sessão no portal do Azure. Se não vir o seu projeto, confirme que está no mesmo diretório no [Web site de visão personalizada](https://customvision.ai) como o diretório onde os seus recursos estão localizados no portal do Azure. No portal do Azure tanto CustomVision.ai, pode selecionar o seu diretório no menu pendente de utilizador no canto superior direito da tela.