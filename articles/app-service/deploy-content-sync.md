---
title: Sincronizar o conteúdo de uma pasta de nuvem
description: Saiba como implantar seu aplicativo no serviço de Azure App por meio da sincronização de conteúdo de uma pasta de nuvem, incluindo o OneDrive ou o dropbox.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 12/03/2018
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: c55894bff9501d3ffb9aa843a9eaa240a213180e
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671739"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Sincronizar o conteúdo de uma pasta de nuvem para o serviço Azure App
Este artigo mostra como sincronizar seu conteúdo para [Azure app serviço](https://go.microsoft.com/fwlink/?LinkId=529714) do Dropbox e do onedrive. 

A implantação de sincronização de conteúdo sob demanda é alimentada pelo [mecanismo de implantação kudu](https://github.com/projectkudu/kudu/wiki)do serviço de aplicativo. Você pode trabalhar com o código do aplicativo e o conteúdo em uma pasta de nuvem designada e, em seguida, sincronizar com o serviço de aplicativo com o clique de um botão. A sincronização de conteúdo usa o servidor de Build kudu. 

## <a name="enable-content-sync-deployment"></a>Habilitar implantação de sincronização de conteúdo

Para habilitar a sincronização de conteúdo, navegue até a página do aplicativo do serviço de aplicativo no [portal do Azure](https://portal.azure.com).

No menu à esquerda, clique em **central de implantação** > **OneDrive** ou **Dropbox** > **autorizar**. Siga os prompts de autorização. 

![](media/app-service-deploy-content-sync/choose-source.png)

Você só precisa autorizar com o OneDrive ou o Dropbox uma vez. Se você já estiver autorizado, basta clicar em **continuar**. Você pode alterar a conta do OneDrive ou Dropbox autorizada clicando em **alterar conta**.

![](media/app-service-deploy-content-sync/continue.png)

Na página **Configurar** , selecione a pasta que você deseja sincronizar. Essa pasta é criada no caminho de conteúdo designado a seguir no OneDrive ou no dropbox. 
   
* **Onedrive**: `Apps\Azure Web Apps`
* **Dropbox**: `Apps\Azure`

Quando terminar, clique em **continuar**.

Na página **Resumo** , verifique suas opções e clique em **concluir**.

## <a name="synchronize-content"></a>Sincronizar conteúdo

Quando você quiser sincronizar o conteúdo em sua pasta de nuvem com o serviço de aplicativo, volte para a página **centro de implantação** e clique em **sincronizar**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > Devido às diferenças subjacentes nas APIs, o **onedrive for Business** não tem suporte no momento. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Desabilitar implantação de sincronização de conteúdo

Para desabilitar a sincronização de conteúdo, navegue até a página do aplicativo do serviço de aplicativo no [portal do Azure](https://portal.azure.com).

No menu à esquerda, clique em **centro de implantação** > **Desconectar**.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Implantar do repositório git local](deploy-local-git.md)
