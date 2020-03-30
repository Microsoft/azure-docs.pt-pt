---
title: Sincronizar conteúdo de uma pasta de nuvem
description: Saiba como implementar a sua aplicação para o Azure App Service através de sincronização de conteúdo a partir de uma pasta na nuvem, incluindo oneDrive ou Dropbox.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 12/03/2018
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 9f18eef607a5f655aecc0b6ebe1c85a239b34192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77482977"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Sincronizar conteúdo de uma pasta de nuvem para o Serviço de Aplicações Azure
Este artigo mostra-lhe como sincronizar o seu conteúdo com o Serviço de [Aplicações Azure](https://go.microsoft.com/fwlink/?LinkId=529714) a partir do Dropbox e do OneDrive. 

A implantação de sincronização de conteúdo sonoro a pedido é alimentada pelo motor de implantação Do Serviço de Aplicação [Kudu](https://github.com/projectkudu/kudu/wiki). Pode trabalhar com o seu código de aplicações e conteúdo numa pasta de nuvem designada e, em seguida, sincronizar para o Serviço de Aplicações com o clique de um botão. A sincronização de conteúdo utiliza o servidor de construção Kudu. 

## <a name="enable-content-sync-deployment"></a>Ativar a implementação da sincronização de conteúdos

Para ativar a sincronização de conteúdos, navegue para a página da sua aplicação Do Serviço de Aplicações no [portal Azure](https://portal.azure.com).

No menu esquerdo, clique no **Centro** > de Implementação**OneDrive** ou **Dropbox** > **Autorizar**. Siga os pedidos de autorização. 

![](media/app-service-deploy-content-sync/choose-source.png)

Só precisa de autorizar com o OneDrive ou o Dropbox uma vez. Se já estiver autorizado, basta clicar em **Continuar**. Pode alterar a conta OneDrive ou Dropbox autorizada clicando na **conta Change**.

![](media/app-service-deploy-content-sync/continue.png)

Na página **Configure,** selecione a pasta que pretende sincronizar. Esta pasta é criada sob o seguinte caminho de conteúdo designado no OneDrive ou Dropbox. 
   
* **OneDrive**:`Apps\Azure Web Apps`
* **Dropbox**:`Apps\Azure`

Quando terminar, clique **em Continuar**.

Na página **Resumo,** verifique as suas opções e clique em **Terminar**.

## <a name="synchronize-content"></a>Sincronizar conteúdo

Quando pretender sincronizar conteúdo na sua pasta de nuvem com o Serviço de Aplicações, volte à página do Centro de **Implementação** e clique em **Sync**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > Devido às diferenças subjacentes nas APIs, o **OneDrive for Business** não é suportado neste momento. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Desativar a implementação da sincronização de conteúdos

Para desativar a sincronização de conteúdos, navegue para a página da sua aplicação do App Service no [portal Azure](https://portal.azure.com).

No menu esquerdo, clique em > **Desligar**o **Centro de Implantação**.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Desdobre-se a partir de Git repo local](deploy-local-git.md)
