---
title: Resolver os principais alertas de serviço nos Serviços de Domínio da AD do Azure Microsoft Docs
description: Saiba como resolver os principais alertas de configuração do serviço para os Serviços de Domínio de Diretório Ativo Azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/20/2019
ms.author: iainfou
ms.openlocfilehash: f72e98213977a09b97cab9966ec69194cd8439e8
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83845972"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>Questões conhecidas: Principais alertas de serviço nos Serviços de Domínio de Diretório Ativo do Azure

[Os principais de serviço](../active-directory/develop/app-objects-and-service-principals.md) são aplicações que a plataforma Azure utiliza para gerir, atualizar e manter um domínio gerido pelo Azure AD DS. Se um diretor de serviço for eliminado, a funcionalidade no domínio gerido pelo Azure AD DS é impactada.

Este artigo ajuda-o a resolver problemas e a resolver alertas de configuração relacionados com o serviço.

## <a name="alert-aadds102-service-principal-not-found"></a>Alerta AADDS102: Diretor de serviço não encontrado

### <a name="alert-message"></a>Mensagem de alerta

*Um diretor de serviço necessário para que os Serviços de Domínio AD Azure funcionem corretamente foi eliminado do seu diretório Azure AD. Esta configuração afeta a capacidade da Microsoft de monitorizar, gerir, corrigir e sincronizar o seu domínio gerido.*

Se um diretor de serviço necessário for eliminado, a plataforma Azure não pode executar tarefas de gestão automatizadas. O domínio gerido pelo Azure AD DS pode não aplicar corretamente atualizações ou receber cópias de segurança.

### <a name="check-for-missing-service-principals"></a>Verifique se faltam os diretores de serviço

Para verificar qual o diretor de serviço que falta e precisa de ser recriado, complete os seguintes passos:

1. No portal Azure, selecione **Azure Ative Diretório** a partir do menu de navegação à esquerda.
1. Selecione **aplicações Enterprise**. Escolha *todas as aplicações* do menu drop-down do Tipo de **Aplicação** e, em seguida, selecione **Aplicar**.
1. Procure cada uma das identificações de aplicação. Se não for encontrada nenhuma aplicação existente, siga as medidas de *Resolução* para criar o diretor de serviço ou reregistar o espaço de nome.

    | ID da Aplicação | Resolução |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Recriar um diretor de serviço desaparecido](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [Re-registe o espaço de nome microsoft.AAD](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [Re-registe o espaço de nome microsoft.AAD](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Re-registe o espaço de nome microsoft.AAD](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>Recriar um diretor de serviço desaparecido

Se a aplicação ID *2565bd9d-da50-47d4-8b85-4c97f669dc36* estiver ausente do seu diretório Azure AD, utilize a Azure AD PowerShell para completar os seguintes passos. Para mais informações, consulte a [instalação de PowerShell Azure AD](/powershell/azure/active-directory/install-adv2).

1. Instale o módulo Azure AD PowerShell e importe-o da seguinte forma:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. Agora recrie o diretor de serviço utilizando o cmdlet [New-AzureAdServicePrincipal:][New-AzureAdServicePrincipal]

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

O Azure AD DS gerido a saúde do domínio atualiza-se automaticamente dentro de duas horas e remove o alerta.

### <a name="re-register-the-microsoft-aad-namespace"></a>Re-registe o espaço de nome da Microsoft AAD

Se a aplicação ID *443155a6-77f3-45e3-882b-22b3a8d431fb*, *abba844e-bc0e-44b0-947a-dc74e5d09022*, ou *d87dcbc6-a371-462e-88e3-28ad15ec4e64,* completar os seguintes passos para voltar a registar o fornecedor de recursos *Microsoft.AAD:*

1. No portal Azure, procure e selecione **Subscrições.**
1. Escolha a subscrição associada ao seu domínio gerido pelo Azure AD DS.
1. A partir da navegação à esquerda, escolha **Fornecedores**de Recursos .
1. Procure *microsoft.AAD*, em seguida, **selecione Re-registar**.

O Azure AD DS gerido a saúde do domínio atualiza-se automaticamente dentro de duas horas e remove o alerta.

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Alerta AADDS105: Aplicação de sincronização de senha está desatualizada

### <a name="alert-message"></a>Mensagem de alerta

*O diretor de serviço com o ID da aplicação "d87dcbc6-a371-462e-88e3-28ad15ec4e64" foi eliminado e recriado. A recreação deixa para trás permissões inconsistentes nos recursos dos Serviços de Domínio Da AD Azure necessários para servir o seu domínio gerido. A sincronização de senhas no seu domínio gerido pode ser afetada.*

O Azure AD DS sincroniza automaticamente as contas de utilizador e credenciais da Azure AD. Se houver algum problema com a aplicação Azure AD utilizada para este processo, a sincronização credencial entre o Azure AD DS e o Azure AD falha.

### <a name="resolution"></a>Resolução

Para recriar a aplicação Azure AD utilizada para a sincronização credencial, utilize a Azure AD PowerShell para completar os seguintes passos. Para mais informações, consulte a [instalação de PowerShell Azure AD](/powershell/azure/active-directory/install-adv2).

1. Instale o módulo Azure AD PowerShell e importe-o da seguinte forma:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Agora elimine a aplicação antiga e o objeto utilizando os seguintes cmdlets PowerShell:

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $spObject
    ```

Depois de eliminar ambas as aplicações, a plataforma Azure recria-as automaticamente e tenta retomar a sincronização da palavra-passe. O Azure AD DS gerido a saúde do domínio atualiza-se automaticamente dentro de duas horas e remove o alerta.

## <a name="next-steps"></a>Passos seguintes

Se ainda tiver problemas, abra um pedido de [apoio azure][azure-support] para assistência adicional para resolução de problemas.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
