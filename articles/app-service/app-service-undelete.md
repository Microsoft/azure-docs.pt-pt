---
title: Restaurar aplicativos eliminados
description: Saiba como restaurar uma aplicação eliminada no Azure App Service. Evite a dor de cabeça de uma aplicação acidentalmente eliminada.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: 04e496806f2c388eb3a69df1b4cc3897b8132f6c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962915"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Restaurar a aplicação do Serviço de Aplicações eliminada com o PowerShell

Se por acaso eliminar acidentalmente a sua aplicação no Azure App Service, pode restaurá-la utilizando os comandos do [módulo Az PowerShell](/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

> [!NOTE]
> - As aplicações eliminadas são expurgadas do sistema 30 dias após a eliminação inicial. Depois de uma aplicação ser purgada, não pode ser recuperada.
> - A funcionalidade undelete não é suportada para o plano de consumo.
> - Aplicações O Serviço de Aplicações que está a ser gerido num Ambiente de Serviço de Aplicações não suportam instantâneos. Por isso, a funcionalidade undelete e a funcionalidade clone não são suportadas para aplicações do Serviço de Aplicações que estão a ser executando num Ambiente de Serviço de Aplicações.
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
> `Restore-AzDeletedWebApp` não é suportado para aplicações de função.

Uma vez identificada a aplicação que pretende restaurar, pode restaurá-la `Restore-AzDeletedWebApp` utilizando.

```powershell
Restore-AzDeletedWebApp -TargetResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> As ranhuras de implementação não são restauradas como parte da sua aplicação. Se precisar de restaurar uma ranhura de preparação, use a `-Slot <slot-name>`  bandeira.
>

As entradas para o comando são:

- **Target Resource Group**: Grupo de recursos-alvo onde a app será restaurada
- **Nome**: Nome para a aplicação, deve ser globalmente único.
- **TargetAppServicePlanName**: Plano de Serviço de Aplicações ligado à app

Por `Restore-AzDeletedWebApp` predefinição, restaurará tanto a configuração da sua aplicação como qualquer conteúdo. Se quiser apenas restaurar o conteúdo, utilize a `-RestoreContentOnly` bandeira com este comando.

> [!NOTE]
> Se a aplicação foi hospedada e depois eliminada de um Ambiente de Serviço de Aplicações, só pode ser restaurada se o ambiente de serviço de aplicações correspondente ainda existir.
>

Pode encontrar aqui a referência completa do comando: [Restore-AzDeletedWebApp](/powershell/module/az.websites/restore-azdeletedwebapp).