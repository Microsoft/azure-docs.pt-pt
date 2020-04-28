---
title: Cmdlets Azure AD PowerShell para reportagem Microsoft Docs
description: Referência dos cmdlets De PowerShell da AD Azure para reportagem.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75495303"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Cmdlets do Azure AD PowerShell para relatórios

> [!NOTE] 
> Estes cmdlets Powershell atualmente apenas funcionam com o Módulo de [Pré-visualização Azure AD.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing) Por favor, note que o módulo de pré-visualização não é sugerido para utilização da produção. 

Para instalar o lançamento de pré-visualização pública, utilize o seguinte. 

```powershell
Install-module AzureADPreview
```
Para obter mais informações sobre como ligar-se ao Azure AD utilizando powershell, consulte o artigo [Azure AD Powershell para Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).  

Com os relatórios do Azure Ative Directory (Azure AD), pode obter detalhes sobre as atividades em torno de todas as operações de escrita na sua direção (registos de auditoria) e dados de autenticação (registos de login). Embora a informação esteja disponível utilizando a API do Gráfico MS, agora pode recuperar os mesmos dados utilizando os cmdlets De PowerShell da AD Azure para reportagem.

Este artigo dá-lhe uma visão geral dos cmdlets PowerShell para usar para registos de auditoria e registos de login.

## <a name="audit-logs"></a>Registos de auditoria

[Os registos](concept-audit-logs.md) de auditoria fornecem rastreabilidade através de registos para todas as alterações efetuadas por várias funcionalidades dentro do Azure AD. Exemplos de registos de auditoria incluem alterações feitas a quaisquer recursos dentro do Azure AD, como adicionar ou remover utilizadores, apps, grupos, papéis e políticas.

Tem acesso aos registos de auditoria utilizando o cmdlet 'Get-AzureADAuditDirectoryLogs.


| Cenário                      | Comando do PowerShell |
| :--                           | :--                |
| Nome do display da aplicação      | Get-AzureADAuditDirectoryLogs -Filter "iniciadoBy/app/displayName eq 'Azure AD Cloud Sync'" |
| Categoria                      | Get-AzureADAuditDirectoryLogs -Filter "category eq 'Application Management'" |
| Hora da data da atividade            | Get-AzureADAuditDirectoryLogs -Filter "activityDateTime gt 2019-04-18" |
| Todos os anteriores              | Get-AzureADAuditDirectoryLogs -Filter "iniciadoBy/app/displayName eq 'Azure AD Cloud Sync' e categoria eq 'Gestão de Aplicações' e atividadeDateTime gt 2019-04-18"|


A imagem que se segue mostra um exemplo para este comando. 

![O botão "Resumo de Dados"](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Registos de início de sessão

Os [registos de login](concept-sign-ins.md) fornecem informações sobre a utilização de aplicações geridas e atividades de login do utilizador.

Tem acesso aos registos de entrada utilizando o cmdlet 'Get-AzureADAuditSignInLogs.


| Cenário                      | Comando do PowerShell |
| :--                           | :--                |
| Nome do exibição do utilizador             | Get-AzureADAuditSignInLogs -Filter "userDisplayName eq 'Timothy Perkins'" |
| Criar hora da data              | Get-AzureADAuditSignInLogs -Filter "createdDateTime gt 2019-04-18T17:30:00.0Z" (Tudo desde as 17h30 em 4/18) |
| Estado                        | Get-AzureADAuditSignInLogs -Filter "status/errorCode eq 50105" |
| Nome do display da aplicação      | Get-AzureADAuditSignInLogs -Filter "appDisplayName eq 'StoreFrontStudio [wsfed enabled]". |
| Todos os anteriores              | Get-AzureADAuditSignInLogs -Filter "userDisplayName eq 'Timothy Perkins' and status/errorCode ne 0 and appDisplayName eq 'StoreFrontStudio [wsfed enableed]'" |


A imagem que se segue mostra um exemplo para este comando. 

![O botão "Resumo de Dados"](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>Passos seguintes

- [A AD Azure reporta uma visão geral.](overview-reports.md)
- [Relatório de registos de auditoria](concept-audit-logs.md). 
- [Acesso programático aos relatórios da AD Azure](concept-reporting-api.md)
