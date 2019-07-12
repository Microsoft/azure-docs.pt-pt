---
title: Sincronização de conteúdo de uma pasta de cloud - App Service do Azure
description: Saiba como implementar a aplicação no App Service do Azure através de sincronização de conteúdo de uma pasta de cloud.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2018
ms.author: cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 60caadf58a8acdc7cecb0074e189fd1656032327
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67617091"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Sincronização de conteúdo de uma pasta de cloud para o serviço de aplicações do Azure
Este artigo mostra-lhe como sincronizar o conteúdo do seu [App Service do Azure](https://go.microsoft.com/fwlink/?LinkId=529714) da Dropbox e OneDrive. 

A implementação de sincronização de conteúdo a pedido com tecnologia do serviço de aplicações [motor de implementação Kudu](https://github.com/projectkudu/kudu/wiki). Pode, trabalhar com o código de aplicação e o conteúdo numa pasta designada de nuvem e, em seguida, sincronizadas com o serviço de aplicações com o clique de um botão. Sincronização de conteúdo utiliza o servidor de compilação Kudu. 

## <a name="enable-content-sync-deployment"></a>Ativar a implementação de sincronização de conteúdo

Para ativar a sincronização de conteúdo, navegue até à página da aplicação do serviço de aplicações na [portal do Azure](https://portal.azure.com).

No menu à esquerda, clique em **Deployment Center** > **OneDrive** ou **Dropbox** > **autorizar**. Siga as instruções de autorização. 

![](media/app-service-deploy-content-sync/choose-source.png)

Apenas terá de autorizar com o OneDrive ou Dropbox, uma vez. Se já estiver autorizado, basta clicar **continuar**. Pode alterar a conta do OneDrive ou Dropbox autorizada clicando **alterar conta**.

![](media/app-service-deploy-content-sync/continue.png)

Na **configurar** página, selecione a pasta que pretende sincronizar. Esta pasta é criada no seguinte caminho de conteúdo designado no OneDrive ou Dropbox. 
   
* **OneDrive**: `Apps\Azure Web Apps`
* **Dropbox**: `Apps\Azure`

Quando terminar, clique em **continuar**.

Na **resumo** página, verifique as suas opções e clique em **concluir**.

## <a name="synchronize-content"></a>Sincronizar o conteúdo

Quando quiser sincronizar o conteúdo na sua pasta de cloud com o serviço de aplicações, volte para o **Deployment Center** página e clique em **sincronização**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > Devido às diferenças subjacentes nas APIs, **OneDrive para empresas** não é suportada neste momento. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Desativar a implementação de sincronização de conteúdo

Para desativar a sincronização de conteúdo, navegue até à página da aplicação do serviço de aplicações na [portal do Azure](https://portal.azure.com).

No menu à esquerda, clique em **Deployment Center** > **desligar**.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Implemente a partir do repositório de Git local](deploy-local-git.md)
