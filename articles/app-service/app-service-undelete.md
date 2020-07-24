---
title: Restaurar aplicativos eliminados
description: Saiba como restaurar uma aplicação eliminada no Azure App Service. Evite a dor de cabeça de uma aplicação acidentalmente eliminada.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: c3c79944aa4add0a32dbb584b13606e32e146a1a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050294"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Restaurar a aplicação do Serviço de Aplicações eliminada com o PowerShell

Se por acaso eliminar acidentalmente a sua aplicação no Azure App Service, pode restaurá-la utilizando os comandos do [módulo Az PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

> [!NOTE]
> As aplicações eliminadas são expurgadas do sistema 30 dias após a eliminação inicial. Uma vez que uma aplicação foi purgada, não pode ser recuperada.
>

> [!NOTE]
> A funcionalidade Undelete não é suportada para o plano de Consumo.
>

## <a name="re-register-app-service-resource-provider"></a>Re-registrar fornecedor de recursos do Serviço de Aplicações
Alguns clientes podem deparar-se com um problema em que a recuperação da lista de aplicações eliminadas falha. Para resolver o problema, executar o seguinte comando:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Lista de aplicativos eliminados

Para obter a recolha de aplicações eliminadas, pode usar `Get-AzDeletedWebApp` .

Para mais detalhes sobre uma aplicação específica eliminada, pode utilizar:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

As informações detalhadas incluem:

- **DeletedSiteId**: Identificador exclusivo para a aplicação, usado para cenários onde várias aplicações com o mesmo nome foram eliminadas
- **SubscriçãoID**: Assinatura que contém o recurso eliminado
- **Localização**: Localização da aplicação original
- **Nome do Grupo de Recursos**: Nome do grupo de recursos original
- **Nome**: Nome da aplicação original.
- **Ranhura**: o nome da ranhura.
- **Tempo de Eliminação**: Quando a aplicação foi eliminada  

## <a name="restore-deleted-app"></a>Restaurar app eliminada
>[!NOTE]
> `Restore-AzDeletedWebApp`não é suportado para aplicações de função.

Uma vez identificada a aplicação que pretende restaurar, pode restaurá-la `Restore-AzDeletedWebApp` utilizando.

```powershell
Restore-AzDeletedWebApp -TargetResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> As ranhuras de implementação não são restauradas como parte da sua aplicação. Se precisar de restaurar uma ranhura de paragem, utilize a `-Slot <slot-name>` bandeira.
>

As entradas para o comando são:

- **Target Resource Group**: Grupo de recursos-alvo onde a app será restaurada
- **Nome**: Nome para a aplicação, deve ser globalmente único.
- **TargetAppServicePlanName**: Plano de Serviço de Aplicações ligado à app

Por `Restore-AzDeletedWebApp` predefinição, restaurará tanto a configuração da sua aplicação como qualquer conteúdo. Se quiser apenas restaurar o conteúdo, utilize a `-RestoreContentOnly` bandeira com este comando.

> [!NOTE]
> Se a aplicação foi hospedada e depois eliminada de um Ambiente de Serviço de Aplicações, então só pode ser restaurada se o ambiente de serviço de aplicações correspondente ainda existir.
>

Pode encontrar aqui a referência completa do comando: [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
