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
ms.date: 07/12/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2192c472e00d123780ec6bc5574e7b9fe326258b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75495303"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Cmdlets do Azure AD PowerShell para relatórios

> [!NOTE] 
> Atualmente, estes cmdlets Powershell funcionam apenas com o Módulo [de Pré-Visualização AD Azure.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing) Tenha em atenção que o módulo de pré-visualização não é sugerido para utilização na produção. 

Para instalar o lançamento de pré-visualização pública, utilize o seguinte. 

```powershell
Install-module AzureADPreview
```
Para obter mais informações sobre como ligar-se ao Azure AD usando a powershell , consulte o artigo [Azure AD Powershell para gráfico](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).  

Com relatórios do Azure Ative Directory (Azure AD), pode obter detalhes sobre todas as atividades em torno de todas as operações de escrita na sua direção (registos de auditoria) e dados de autenticação (registos de login). Embora a informação esteja disponível utilizando a API do Ms Graph, agora pode recuperar os mesmos dados utilizando os cmdlets Azure AD PowerShell para reporte.

Este artigo dá-lhe uma visão geral dos cmdlets PowerShell para usar para registos de auditoria e registos de login.

## <a name="audit-logs"></a>Registos de auditoria

[Os registos de auditoria](concept-audit-logs.md) fornecem rastreabilidade através de registos para todas as alterações efetuadas por várias funcionalidades dentro do Azure AD. Exemplos de registos de auditoria incluem alterações feitas a quaisquer recursos dentro do AD Azure, como adicionar ou remover utilizadores, apps, grupos, papéis e políticas.

Obtém acesso aos registos de auditoria utilizando o cmdlet 'Get-AzureADAuditDirectoryLogs.


| Scenario                      | Comando do PowerShell |
| :--                           | :--                |
| Nome do display da aplicação      | Get-AzureADAuditDirectoryLogs -Filtro "iniciado Por/app/displayName eq 'Azure AD Cloud Sync'" |
| Categoria                      | Get-AzureADAuditDirectoryLogs -Filtrar "categoria eq 'Gestão de aplicações'" |
| Data de Data de Atividade            | Get-AzureADAuditDirectoryLogs -Filtrar "atividadeDateTime gt 2019-04-18" |
| Todos os anteriores              | Get-AzureADAuditDirectoryLogs -Filtro "iniciado Por/app/displayName eq 'Azure AD Cloud Sync' e categoria eq 'Application Management' e atividadeDateTime gt 2019-04-18"|


A imagem que se segue mostra um exemplo para este comando. 

![O botão "Resumo de Dados"](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Registos de início de sessão

Os [registos de login](concept-sign-ins.md) fornecem informações sobre o uso de aplicações geridas e atividades de login do utilizador.

Obtém acesso aos registos de entrada utilizando o cmdlet 'Get-AzureADAuditSignInLogs.


| Scenario                      | Comando do PowerShell |
| :--                           | :--                |
| Nome de exibição do utilizador             | Get-AzureADAuditSignInLogs -Filtro "userDisplayName eq 'Timothy Perkins'" |
| Criar hora da data              | Get-AzureADAuditSignInLogs -Filtro "createdDateTime gt 2019-04-18T17:30:00.0Z" (Tudo desde as 17h30 do dia 4/18) |
| Estado                        | Get-AzureADAuditSignInLogs -Filtrar "status/errorCode eq 50105" |
| Nome do display da aplicação      | Get-AzureADAuditSignInLogs -Filter "appDisplayName eq 'StoreFrontStudio [wsfed enabled]'" |
| Todos os anteriores              | Get-AzureADAuditSignInLogs -Filtro "userDisplayName eq 'Timothy Perkins' e status/errorCode ne 0 e appDisplayName eq 'StoreFrontStudio [wsfed enabled]". |


A imagem que se segue mostra um exemplo para este comando. 

![O botão "Resumo de Dados"](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>Próximos passos

- [AZure AD reporta uma visão geral](overview-reports.md).
- [Relatório de registos de auditoria.](concept-audit-logs.md) 
- [Acesso programático aos relatórios AD do Azure](concept-reporting-api.md)
