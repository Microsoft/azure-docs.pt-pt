---
title: Sync conteúdo de uma pasta de nuvem
description: Saiba como implementar a sua aplicação no Azure App Service através de sincronização de conteúdos a partir de uma pasta de nuvem, incluindo OneDrive ou Dropbox.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 12/03/2018
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 880edff95bb548ec5328c543a542ea5dfcfc362f
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150302"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Sync conteúdo de uma pasta de nuvem para Azure App Service
Este artigo mostra-lhe como sincronizar o seu conteúdo com [o Azure App Service](./overview.md) da Dropbox e oneDrive. 

A implementação da sincronização de conteúdos a pedido é alimentada pelo motor de implantação do Serviço [de Aplicações Kudu.](https://github.com/projectkudu/kudu/wiki) Pode trabalhar com o seu código de aplicação e conteúdo numa pasta de nuvem designada e, em seguida, sincronizar com o Serviço de Aplicações com o clique de um botão. A sincronização de conteúdos utiliza o servidor de construção Kudu. 

## <a name="enable-content-sync-deployment"></a>Permitir a implementação de sincronização de conteúdos

Para ativar a sincronização de conteúdos, navegue para a sua página de aplicações do Serviço de Aplicações no [portal Azure.](https://portal.azure.com)

No menu esquerdo, clique em **Deployment Center**  >  **OneDrive** ou **Dropbox**  >  **Authorize**. Siga as indicações de autorização. 

![Mostra como autorizar o OneDrive ou o Dropbox no Centro de Implementação no portal Azure.](media/app-service-deploy-content-sync/choose-source.png)

Só é necessário autorizar com o OneDrive ou o Dropbox uma vez. Se já está autorizado, basta clicar em **Continuar.** Pode alterar a conta oneDrive ou Dropbox autorizada clicando na **conta Change**.

![Mostra como alterar a conta oneDrive ou Dropbox autorizada no Centro de Implementação no portal Azure.](media/app-service-deploy-content-sync/continue.png)

Na página **Configurar,** selecione a pasta que pretende sincronizar. Esta pasta é criada sob o seguinte caminho de conteúdo designado no OneDrive ou dropbox. 
   
* **OneDrive:**`Apps\Azure Web Apps`
* **Caixa postal:**`Apps\Azure`

Quando terminar, clique **em Continuar.**

Na página **Resumo,** verifique as suas opções e clique em **Terminar**.

## <a name="synchronize-content"></a>Sincronizar conteúdo

Quando pretender sincronizar o conteúdo na sua pasta de nuvem com o Serviço de Aplicações, volte para a página **do Centro de Implementação** e clique em **Sync**.

![Mostra como sincronizar a sua pasta em nuvem com o Serviço de Aplicações.](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > Devido às diferenças subjacentes nas APIs, o **OneDrive for Business** não é suportado neste momento. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Desativar a implementação do sincronização de conteúdos

Para desativar a sincronização de conteúdos, navegue para a sua página de aplicações do Serviço de Aplicações no [portal Azure.](https://portal.azure.com)

No menu esquerdo, clique em **'Desligar o Centro de**  >  **Implementação'.**

![Mostra como desligar a sincronização da pasta cloud com a sua aplicação De Serviço de Aplicações no portal Azure.](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Implantação a partir de git repo local](deploy-local-git.md)