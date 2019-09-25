---
title: Resolver alertas de entidade de serviço no Azure AD Domain Services | Microsoft Docs
description: Saiba como solucionar problemas de alertas de configuração da entidade de serviço para Azure Active Directory Domain Services
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
ms.openlocfilehash: 175bfe63176b78c5aeafc7147c46dd5ab1110325
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257965"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>Problemas conhecidos: Alertas de entidade de serviço no Azure Active Directory Domain Services

As [entidades de serviço](../active-directory/develop/app-objects-and-service-principals.md) são aplicativos que a plataforma Azure usa para gerenciar, atualizar e manter um domínio gerenciado do Azure AD DS. Se uma entidade de serviço for excluída, a funcionalidade no domínio gerenciado AD DS do Azure será afetada.

Este artigo ajuda você a solucionar problemas e resolver alertas de configuração relacionados à entidade de serviço.

## <a name="alert-aadds102-service-principal-not-found"></a>AADDS102 de alerta: Entidade de serviço não encontrada

### <a name="alert-message"></a>Mensagem de alerta

*Uma entidade de serviço necessária para que o Azure AD Domain Services funcione corretamente foi excluída do seu diretório do Azure AD. Essa configuração afeta a capacidade da Microsoft de monitorar, gerenciar, aplicar patches e sincronizar seu domínio gerenciado.*

Se uma entidade de serviço necessária for excluída, a plataforma do Azure não poderá executar tarefas de gerenciamento automatizadas. O Azure AD DS domínio gerenciado pode não aplicar atualizações corretamente ou fazer backups.

### <a name="check-for-missing-service-principals"></a>Verificar entidades de serviço ausentes

Para verificar qual entidade de serviço está ausente e precisa ser recriada, conclua as seguintes etapas:

1. Na portal do Azure, selecione **Azure Active Directory** no menu de navegação à esquerda.
1. Selecione **aplicativos empresariais**. Escolha *todos os aplicativos* no menu suspenso **tipo de aplicativo** e, em seguida, selecione **aplicar**.
1. Pesquise cada uma das IDs de aplicativo. Se nenhum aplicativo existente for encontrado, siga as etapas de *resolução* para criar a entidade de serviço ou registrar novamente o namespace.

    | ID da aplicação | Resolução |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Recriar uma entidade de serviço ausente](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [Registrar novamente o namespace Microsoft. AAD](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [Registrar novamente o namespace Microsoft. AAD](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Registrar novamente o namespace Microsoft. AAD](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>Recriar uma entidade de serviço ausente

Se a ID do aplicativo *2565bd9d-DA50-47d4-8b85-4c97f669dc36* estiver ausente do seu diretório do Azure AD, use o PowerShell do Azure ad para concluir as etapas a seguir. Para obter mais informações, consulte [instalar o PowerShell do Azure ad](/powershell/azure/active-directory/install-adv2).

1. Instale o módulo do PowerShell do Azure AD e importe-o da seguinte maneira:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. Agora recrie a entidade de serviço usando o cmdlet [New-AzureAdServicePrincipal][New-AzureAdServicePrincipal] :

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

A integridade do domínio gerenciado do AD DS do Azure se atualiza automaticamente dentro de duas horas e remove o alerta.

### <a name="re-register-the-microsoft-aad-namespace"></a>Registrar novamente o namespace do Microsoft AAD

Se a ID do aplicativo *443155a6-77f3-45e3-882b-22b3a8d431fb*, *abba844e-bc0e-44b0-947a-dc74e5d09022*ou *D87dcbc6-a371-462e-88e3-28ad15ec4e64* estiver ausente no diretório do Azure AD, conclua as etapas a seguir para Registre novamente o provedor de recursos *Microsoft. AAD* :

1. No portal do Azure, procure e selecione **assinaturas**.
1. Escolha a assinatura associada ao seu domínio gerenciado AD DS do Azure.
1. Na navegação à esquerda, escolha provedores de **recursos**.
1. Pesquise *Microsoft. AAD*e, em seguida, selecione **registrar novamente**.

A integridade do domínio gerenciado do AD DS do Azure se atualiza automaticamente dentro de duas horas e remove o alerta.

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>AADDS105 de alerta: O aplicativo de sincronização de senha está desatualizado

### <a name="alert-message"></a>Mensagem de alerta

*A entidade de serviço com a ID de aplicativo "d87dcbc6-a371-462e-88e3-28ad15ec4e64" foi excluída e recriada. A recreação deixa por trás das permissões inconsistentes em Azure AD Domain Services recursos necessários para atender ao domínio gerenciado. A sincronização de senhas em seu domínio gerenciado pode ser afetada.*

O Azure AD DS sincroniza automaticamente as contas de usuário e as credenciais do Azure AD. Se houver um problema com o aplicativo do Azure AD usado para esse processo, a sincronização de credenciais entre o Azure AD DS e o Azure AD falhará.

### <a name="resolution"></a>Resolução

Para recriar o aplicativo do Azure AD usado para sincronização de credenciais, use o PowerShell do Azure AD para concluir as etapas a seguir. Para obter mais informações, consulte [instalar o PowerShell do Azure ad](/powershell/azure/active-directory/install-adv2).

1. Instale o módulo do PowerShell do Azure AD e importe-o da seguinte maneira:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Agora exclua o aplicativo e o objeto antigo usando os seguintes cmdlets do PowerShell:

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```

Depois de excluir ambos os aplicativos, a plataforma do Azure os recria automaticamente e tenta retomar a sincronização de senha. A integridade do domínio gerenciado do AD DS do Azure se atualiza automaticamente dentro de duas horas e remove o alerta.

## <a name="next-steps"></a>Passos seguintes

Se você ainda tiver problemas, [abra uma solicitação de suporte do Azure][azure-support] para obter assistência de solução de problemas adicional.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
