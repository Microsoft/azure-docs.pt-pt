---
title: 'Azure Active Directory Domain Services: Solucionar problemas de entidades de serviço | Microsoft Docs'
description: Solucionando problemas de configuração de entidade de serviço para Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: 9e5fa8c84f5e7ca58117666846b603a118826150
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234132"
---
# <a name="troubleshoot-invalid-service-principal-configurations-for-azure-active-directory-domain-services"></a>Solucionar problemas de configurações de entidade de serviço inválidas para Azure Active Directory Domain Services

Este artigo ajuda você a solucionar problemas e resolver erros de configuração relacionados à entidade de serviço que resultam na seguinte mensagem de alerta:

## <a name="alert-aadds102-service-principal-not-found"></a>AADDS102 de alerta: Entidade de serviço não encontrada

**Mensagem de alerta:** *Uma entidade de serviço necessária para que o Azure AD Domain Services funcione corretamente foi excluída do seu diretório do Azure AD. Essa configuração afeta a capacidade da Microsoft de monitorar, gerenciar, aplicar patches e sincronizar seu domínio gerenciado.*

As [entidades de serviço](../active-directory/develop/app-objects-and-service-principals.md) são aplicativos que a Microsoft usa para gerenciar, atualizar e manter seu domínio gerenciado. Se eles forem excluídos, ele interromperá a capacidade da Microsoft de atender ao seu domínio.


## <a name="check-for-missing-service-principals"></a>Verificar entidades de serviço ausentes
Use as seguintes etapas para determinar quais entidades de serviço precisam ser recriadas:

1. Navegue até a página [aplicativos empresariais – todos os aplicativos](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) na portal do Azure.
2. Na lista suspensa **Mostrar** , selecione **todos os aplicativos** e clique em **aplicar**.
3. Usando a tabela a seguir, pesquise cada ID do aplicativo colando a ID na caixa de pesquisa e pressionando ENTER. Se os resultados da pesquisa estiverem vazios, você deverá recriar a entidade de serviço seguindo as etapas na coluna "resolução".

| ID da aplicação | Resolução |
| :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Recriar uma entidade de serviço ausente com o PowerShell](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Registrar novamente no namespace Microsoft. AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Registrar novamente no namespace Microsoft. AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Registrar novamente no namespace Microsoft. AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |

## <a name="recreate-a-missing-service-principal-with-powershell"></a>Recriar uma entidade de serviço ausente com o PowerShell
Siga estas etapas se uma entidade de serviço com a ```2565bd9d-da50-47d4-8b85-4c97f669dc36``` ID estiver ausente no seu diretório do Azure AD.

**Resolução:** Você precisa do Azure AD PowerShell para concluir essas etapas. Para obter informações sobre como instalar o PowerShell do Azure AD, consulte [Este artigo](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Para resolver esse problema, digite os seguintes comandos em uma janela do PowerShell:
1. Instale o módulo do PowerShell do Azure AD e importe-o.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Verifique se a entidade de serviço necessária para Azure AD Domain Services está ausente no diretório executando o seguinte comando do PowerShell:

    ```powershell
    Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
    ```

3. Crie a entidade de serviço digitando o seguinte comando do PowerShell:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

4. Depois de criar a entidade de serviço ausente, aguarde duas horas e verifique a integridade do seu domínio gerenciado.


## <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Registre novamente no namespace do Microsoft AAD usando o portal do Azure
Siga estas etapas se uma entidade de serviço com a ```443155a6-77f3-45e3-882b-22b3a8d431fb``` ID ```abba844e-bc0e-44b0-947a-dc74e5d09022``` ou ```d87dcbc6-a371-462e-88e3-28ad15ec4e64``` ou estiver ausente do seu diretório do Azure AD.

**Resolução:** Use as etapas a seguir para restaurar os serviços de domínio em seu diretório:

1. Navegue até a página [assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure.
2. Escolha a assinatura da tabela que está associada ao seu domínio gerenciado
3. Usando a navegação do lado esquerdo, escolha **provedores de recursos**
4. Pesquise "Microsoft. AAD" na tabela e clique em **registrar novamente**
5. Para garantir que o alerta seja resolvido, exiba a página de integridade do seu domínio gerenciado em duas horas.


## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>AADDS105 de alerta: O aplicativo de sincronização de senha está desatualizado

**Mensagem de alerta:** A entidade de serviço com a ID de aplicativo "d87dcbc6-a371-462e-88e3-28ad15ec4e64" foi excluída e recriada. A recreação deixa por trás das permissões inconsistentes em Azure AD Domain Services recursos necessários para atender ao domínio gerenciado. A sincronização de senhas em seu domínio gerenciado pode ser afetada.


**Resolução:** Você precisa do Azure AD PowerShell para concluir essas etapas. Para obter informações sobre como instalar o PowerShell do Azure AD, consulte [Este artigo](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Para resolver esse problema, digite os seguintes comandos em uma janela do PowerShell:
1. Instale o módulo do PowerShell do Azure AD e importe-o.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```
2. Exclua o aplicativo e o objeto antigos usando os seguintes comandos do PowerShell

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```
3. Depois de ter excluído os dois, o sistema se corrigirá e recriará os aplicativos necessários para a sincronização de senha. Para garantir que o alerta foi corrigido, aguarde duas horas e verifique a integridade do seu domínio.


## <a name="contact-us"></a>Contacte-nos
Entre em contato com a equipe de produto Azure Active Directory Domain Services para [compartilhar comentários ou para obter suporte](contact-us.md).
