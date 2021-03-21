---
title: Sync conteúdo de uma pasta de nuvem
description: Saiba como implementar a sua aplicação no Azure App Service através de sincronização de conteúdos a partir de uma pasta de nuvem, incluindo OneDrive ou Dropbox.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 02/25/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: bfee320c7a8b4cbe8439c376350d1234b393bfb5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051244"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Sync conteúdo de uma pasta de nuvem para Azure App Service
Este artigo mostra-lhe como sincronizar o seu conteúdo com [o Azure App Service](./overview.md) da Dropbox e oneDrive. 

Com a abordagem de sincronização de conteúdos, o seu trabalho com o seu código de aplicação e conteúdo numa pasta de nuvem designada para se certificar de que está num estado de funcionação pronto e, em seguida, sincronizar com o Serviço de Aplicações com o clique de um botão. 

Devido às diferenças subjacentes nas APIs, o **OneDrive for Business** não é suportado neste momento.

> [!NOTE]
> A página **do Centro de Desenvolvimento (Clássico)** no portal Azure, que é a antiga experiência de implantação, será depretada em março de 2021. Esta alteração não afetará quaisquer definições de implementação existentes na sua aplicação, podendo continuar a gerir a implementação de aplicações na página **do Centro de Implementação.**

## <a name="enable-content-sync-deployment"></a>Permitir a implementação de sincronização de conteúdos

1. No [portal Azure,](https://portal.azure.com)navegue para a página de gestão da sua aplicação App Service.

1. A partir do menu esquerdo, clique em **Definições do Centro de**  >  **Implementação**. 

1. Em **Fonte**, selecione **OneDrive** ou **Dropbox**.

1. Clique **em Autorizar** e siga as indicações de autorização. 

    ![Mostra como autorizar o OneDrive ou o Dropbox no Centro de Implementação no portal Azure.](media/app-service-deploy-content-sync/choose-source.png)

    Só precisa de autorizar com o OneDrive ou o Dropbox uma vez para a sua conta Azure. Para autorizar uma conta OneDrive ou Dropbox diferente para uma aplicação, clique na **conta Change**.

1. Na **Pasta,** selecione a pasta para sincronizar. Esta pasta é criada sob o seguinte caminho de conteúdo designado no OneDrive ou dropbox. 
   
    * **OneDrive:**`Apps\Azure Web Apps`
    * **Caixa postal:**`Apps\Azure`
    
1. Clique em **Guardar**.

## <a name="synchronize-content"></a>Sincronizar conteúdo

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

1. No [portal Azure,](https://portal.azure.com)navegue para a página de gestão da sua aplicação App Service.

1. A partir do menu esquerdo, clique em **Deployment Center**  >  **Redeploy/Sync**. 

    ![Mostra como sincronizar a sua pasta em nuvem com o Serviço de Aplicações.](media/app-service-deploy-content-sync/synchronize.png)
   
1. Clique **em OK** para confirmar a sincronização.

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Inicie uma sincronização executando o seguinte comando e substituindo \<group-name> \<app-name> e:

```azurecli-interactive
az webapp deployment source sync –-resource-group <group-name> –-name <app-name>
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Inicie uma sincronização executando o seguinte comando e substituindo \<group-name> \<app-name> e:

```azurepowershell-interactive
Invoke-AzureRmResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action sync -ApiVersion 2019-08-01 -Force –Verbose
```

-----

## <a name="disable-content-sync-deployment"></a>Desativar a implementação do sincronização de conteúdos

1. No [portal Azure,](https://portal.azure.com)navegue para a página de gestão da sua aplicação App Service.

1. A partir do menu esquerdo, clique em **'Definições do Centro de**  >  **Implementação' Desconecte** as  >  **definições**. 

    ![Mostra como desligar a sincronização da pasta cloud com a sua aplicação De Serviço de Aplicações no portal Azure.](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Implantação a partir de git repo local](deploy-local-git.md)