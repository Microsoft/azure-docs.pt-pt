---
title: Cmdlets Azure AD PowerShell para reportagem Microsoft Docs
description: Referência dos cmdlets Azure AD PowerShell para reporte.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9ff923d0231a1b00493a54996c2fcd489012bbe7
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862042"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Cmdlets do Azure AD PowerShell para relatórios

> [!NOTE] 
> Atualmente, estes cmdlets PowerShell funcionam apenas com o Módulo [de Pré-visualização AZURE AD.](/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing) Tenha em atenção que o módulo de pré-visualização não é sugerido para utilização na produção. 

Para instalar o lançamento de pré-visualização pública, utilize o seguinte. 

```powershell
Install-module AzureADPreview
```

Para obter mais informações sobre como ligar-se ao Azure AD usando o PowerShell, consulte o artigo [Azure AD PowerShell para gráfico](/powershell/azure/active-directory/install-adv2).  

Com relatórios do Azure Ative Directory (Azure AD), pode obter detalhes sobre todas as atividades em torno de todas as operações de escrita na sua direção (registos de auditoria) e dados de autenticação (registos de login). Embora a informação esteja disponível utilizando a API do Ms Graph, agora pode recuperar os mesmos dados utilizando os cmdlets Azure AD PowerShell para reporte.

Este artigo dá-lhe uma visão geral dos cmdlets PowerShell para usar para registos de auditoria e registos de login.

## <a name="audit-logs"></a>Registos de auditoria

[Os registos de auditoria](concept-audit-logs.md) fornecem rastreabilidade através de registos para todas as alterações efetuadas por várias funcionalidades dentro do Azure AD. Exemplos de registos de auditoria incluem alterações feitas a quaisquer recursos dentro do AD Azure, como adicionar ou remover utilizadores, apps, grupos, papéis e políticas.

Obtém acesso aos registos de auditoria utilizando o cmdlet 'Get-AzureADAuditDirectoryLogs.


| Cenário                      | Comando do PowerShell |
| :--                           | :--                |
| Nome do display da aplicação      | Get-AzureADAuditDirectoryLogs -Filtro "iniciadoBy/app/displayName eq 'Azure AD Cloud Sync'" |
| Categoria                      | Get-AzureADAuditDirectoryLogs -Filtrar "categoria eq 'ApplicationManagement'" |
| Data de Data de Atividade            | Get-AzureADAuditDirectoryLogs -Filtrar "atividadeDateTime gt 2019-04-18" |
| Todas as opções acima              | Get-AzureADAuditDirectoryLogs -Filter "iniciadoBy/app/displayName eq 'Azure AD Cloud Sync' e categoria eq 'ApplicationManagement' e atividadeDateTime gt 2019-04-18"|


A imagem que se segue mostra um exemplo para este comando. 

![A screenshot mostra o resultado do comando Get-Azure A D Audit Directory Logs.](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Registos de início de sessão

Os [registos de login](concept-sign-ins.md) fornecem informações sobre o uso de aplicações geridas e atividades de login do utilizador.

Obtém acesso aos registos de entrada utilizando o cmdlet 'Get-AzureADAuditSignInLogs.


| Cenário                      | Comando do PowerShell |
| :--                           | :--                |
| Nome de exibição do utilizador             | Get-AzureADAuditSignInLogs -Filtro "userDisplayName eq 'Timothy Perkins'" |
| Criar hora da data              | Get-AzureADAuditSignInLogs -Filtro "createdDateTime gt 2019-04-18T17:30:00.0Z" (Tudo desde as 17h30 do dia 4/18) |
| Estado                        | Get-AzureADAuditSignInLogs -Filtrar "status/errorCode eq 50105" |
| Nome do display da aplicação      | Get-AzureADAuditSignInLogs -Filtro "appDisplayName eq 'StoreFrontStudio [wsfed enabled]'" |
| Todas as opções acima              | Get-AzureADAuditSignInLogs -Filter "userDisplayName eq 'Timothy Perkins' e status/errorCode ne 0 e appDisplayName eq 'StoreFrontStudio [wsfed enabled]'" |


A imagem que se segue mostra um exemplo para este comando. 

![A screenshot mostra o resultado do Get-Azure o comando de sinal de auditoria D em registos.](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>Passos seguintes

- [AZure AD reporta uma visão geral](overview-reports.md).
- [Relatório de registos de auditoria.](concept-audit-logs.md) 
- [Acesso programático aos relatórios AD do Azure](concept-reporting-api.md)
