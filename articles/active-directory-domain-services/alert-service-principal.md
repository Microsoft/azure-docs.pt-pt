---
title: Resolver alertas principais de serviço em Azure AD Domain Services | Microsoft Docs
description: Saiba como resolver os alertas principais de configuração do serviço para os Serviços de Domínio do Diretório Ativo Azure
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 15dba02d3db45d663b8ba0aa7eb93bbc3f388532
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645879"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>Questões conhecidas: Alertas principais de serviço nos Serviços de Domínio do Diretório Ativo Azure

[Os principais serviços](../active-directory/develop/app-objects-and-service-principals.md) são aplicações que a plataforma Azure utiliza para gerir, atualizar e manter um domínio gerido por Azure Ative Directory Domain Services (Azure AD DS). Se um principal de serviço for eliminado, a funcionalidade no domínio gerido é impactada.

Este artigo ajuda-o a resolver problemas e a resolver alertas de configuração principais do serviço.

## <a name="alert-aadds102-service-principal-not-found"></a>Alerta AADDS102: Diretor de serviço não encontrado

### <a name="alert-message"></a>Mensagem de alerta

*Um diretor de serviço necessário para que os Serviços de Domínio AZure AD funcionem corretamente foi eliminado do seu diretório AD Azure. Esta configuração tem impacto na capacidade da Microsoft de monitorizar, gerir, corrigir e sincronizar o seu domínio gerido.*

Se um principal de serviço necessário for eliminado, a plataforma Azure não pode executar tarefas de gestão automatizadas. O domínio gerido pode não aplicar corretamente atualizações ou fazer backups.

### <a name="check-for-missing-service-principals"></a>Verifique se faltam os principais de serviço

Para verificar qual o principal de serviço que falta e deve ser recriado, complete os seguintes passos:

1. No portal Azure, selecione **Azure Ative Directory** a partir do menu de navegação à esquerda.
1. Selecione **aplicações da Enterprise**. Escolha *todas as aplicações* do menu drop-down do Tipo de **Aplicação** e, em seguida, selecione **Aplicar**.
1. Procure cada um dos seguintes IDs de aplicação. Se não for encontrada nenhuma aplicação existente, siga as etapas de *Resolução* para criar o principal de serviço ou re-registar o espaço de nome.

    | ID da Aplicação | Resolução |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Recriar um diretor de serviço desaparecido](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [Re-registrar o espaço de nomes Microsoft.AAD](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [Re-registrar o espaço de nomes Microsoft.AAD](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Re-registrar o espaço de nomes Microsoft.AAD](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>Recriar um diretor de serviço desaparecido

Se faltar o ID da aplicação *2565bd9d-da50-47d4-8b85-4c97f669dc36* do seu diretório AD Azure, utilize a Azure AD PowerShell para completar os seguintes passos. Para mais informações, consulte [a Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

1. Se necessário, instale o módulo Azure AD PowerShell e importe-o da seguinte forma:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. Agora recrie o principal de serviço usando o [cmdlet New-AzureAdServicePrincipal:][New-AzureAdServicePrincipal]

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

A saúde do domínio gerido atualiza-se automaticamente dentro de duas horas e remove o alerta.

### <a name="re-register-the-microsoft-aad-namespace"></a>Re-registrar o espaço de nomes Microsoft AAD

Se o ID *443155a6-77f3-45e3-882b-22b3a8d431fb*, *abba844e-bc0e-44b0-94 7a-dc74e5d09022,* ou *d87dcbc6-a371-462e-88e3-28ad15ec4e64,* completar as seguintes etapas para voltar a registar o fornecedor de recursos *Microsoft.AAD:*

1. No portal Azure, procure e selecione **Subscrições.**
1. Escolha a subscrição associada ao seu domínio gerido.
1. A partir da navegação à esquerda, escolha **Fornecedores de Recursos.**
1. Procure por *Microsoft.AAD* e, em seguida, **selecione Re-registrar**.

A saúde do domínio gerido atualiza-se automaticamente dentro de duas horas e remove o alerta.

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Alerta AADDS105: Aplicação de sincronização de palavras-passe está desatualizada

### <a name="alert-message"></a>Mensagem de alerta

*O principal do serviço com o ID da aplicação "d87dcbc6-a371-462e-88e3-28ad15ec4e64" foi apagado e depois recriado. A recreação deixa para trás permissões inconsistentes nos recursos dos Serviços de Domínio AD Azure necessários para o serviço ao seu domínio gerido. A sincronização de palavras-passe no seu domínio gerido pode ser afetada.*

O Azure AD DS sincroniza automaticamente as contas e credenciais dos utilizadores do Azure AD. Se houver um problema com a aplicação AD Azure usada para este processo, a sincronização credencial entre Azure AD DS e Azure AD falha.

### <a name="resolution"></a>Resolução

Para recriar a aplicação AD AD Azure utilizada para a sincronização de credenciais, utilize o Azure AD PowerShell para completar os seguintes passos. Para obter mais informações, consulte [a instalação Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

1. Se necessário, instale o módulo Azure AD PowerShell e importe-o da seguinte forma:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Agora elimine a aplicação e o objeto antigos utilizando os seguintes cmdlets PowerShell:

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $spObject
    ```

Depois de eliminar ambas as aplicações, a plataforma Azure recria-as automaticamente e tenta retomar a sincronização da palavra-passe. A saúde do domínio gerido atualiza-se automaticamente dentro de duas horas e remove o alerta.

## <a name="next-steps"></a>Passos seguintes

Se ainda tiver problemas, abra um pedido de [apoio ao Azure][azure-support] para assistência adicional à resolução de problemas.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
