---
title: Restaurar aplicações eliminadas
description: Saiba como restaurar uma aplicação eliminada no Serviço de Aplicações Azure. Evite a dor de cabeça de uma aplicação apagada acidentalmente.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: 296c8e2dfe99e3b0aea66f364ac6f6d9b2f60a1a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81272496"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Restaurar a aplicação do Serviço de Aplicações eliminada com o PowerShell

Se por acaso apagar acidentalmente a sua aplicação no Serviço de Aplicações Azure, pode restaurá-la utilizando os comandos do [módulo Az PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

> [!NOTE]
> As aplicações eliminadas são expurgadas do sistema 30 dias após a eliminação inicial. Uma vez que uma aplicação foi expurgada, não pode ser recuperada.
>

## <a name="re-register-app-service-resource-provider"></a>Re-registe o fornecedor de recursos do Serviço de Aplicações
Alguns clientes podem encontrar um problema em que a recuperação da lista de aplicações apagadas falha. Para resolver o problema, executar o seguinte comando:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Lista de aplicações eliminadas

Para obter a coleção de aplicações apagadas, pode utilizar. `Get-AzDeletedWebApp`

Para mais detalhes sobre uma aplicação específica eliminada pode utilizar:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

As informações detalhadas incluem:

- **EliminadoSiteId**: Identificador único para a aplicação, utilizado para cenários onde várias aplicações com o mesmo nome foram eliminadas
- **SubscriptionID**: Subscrição contendo o recurso eliminado
- **Localização**: Localização da aplicação original
- **Nome do Grupo de Recursos**: Nome do grupo de recursos originais
- **Nome**: Nome da aplicação original.
- **Ranhura**: o nome da ranhura.
- **Tempo de eliminação**: Quando foi a aplicação eliminada  

## <a name="restore-deleted-app"></a>Restaurar a aplicação eliminada

Uma vez identificada a aplicação que pretende restaurar, `Restore-AzDeletedWebApp`pode restaurá-la utilizando .

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> As ranhuras de implementação não são restauradas como parte da sua aplicação. Se precisar restaurar uma ranhura de `-Slot <slot-name>` preparação, use a bandeira.
>

As inputs para o comando são:

- **Grupo de Recursos**: Grupo de recursos-alvo onde a app será restaurada
- **Nome**: Nome para a aplicação, deve ser globalmente único.
- **TargetAppServicePlanName**: Plano de serviço de aplicações ligado à app

Por `Restore-AzDeletedWebApp` padrão, restaurará tanto a configuração da sua aplicação como um conteúdo. Se quiser apenas restaurar o `-RestoreContentOnly` conteúdo, utilize a bandeira com este comando.

> [!NOTE]
> Se a aplicação foi hospedada e depois eliminada de um Ambiente de Serviço de Aplicações, então só pode ser restaurada se o ambiente de serviço de aplicação correspondente ainda existir.
>

Pode encontrar aqui a referência completa do commandlet: [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
