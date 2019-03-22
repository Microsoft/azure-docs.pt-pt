---
title: 'Azure Active Directory Domain Services: Resolução de problemas de Principal de serviço de configuração | Documentos da Microsoft'
description: Resolução de problemas de Principal de serviço de configuração para o Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ergreenl
ms.openlocfilehash: 5bed5dd19f6b736aca7ed2d551c8be0e6f03a810
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58094546"
---
# <a name="troubleshoot-invalid-service-principal-configuration-for-your-managed-domain"></a>Resolver problemas de configuração de Principal de serviço inválido para o seu domínio gerido

Este artigo ajuda-o a resolver erros de configuração relacionadas com o principal de serviço que fazer com a seguinte mensagem de alerta:

## <a name="alert-aadds102-service-principal-not-found"></a>Alerta AADDS102: Principal de serviço não encontrado

**Mensagem de alerta:** *Um Principal de serviço necessárias para o Azure AD Domain Services funcionar corretamente foi eliminado do diretório do Azure AD. Esta configuração afeta a capacidade da Microsoft para monitorizar, gerir, corrigir e sincronizar o domínio gerido.*

[Principais de serviço](../active-directory/develop/app-objects-and-service-principals.md) são aplicações que a Microsoft utiliza para gerir, atualizar e manter o seu domínio gerido. Se eles forem excluídos, interrompe a capacidade da Microsoft para atender a seu domínio.


## <a name="check-for-missing-service-principals"></a>Procurar em falta principais de serviço
Utilize os seguintes passos para determinar qual serviço tem de ser recriadas principais:

1. Navegue para o [aplicações empresariais - todas as aplicações](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) página no portal do Azure.
2. Na **mostrar** menu pendente, selecione **todos os aplicativos** e clique em **aplicar**.
3. Pesquisa para cada ID de aplicação, colar o ID na caixa de pesquisa e prima utilizando a tabela seguinte, introduza. Se os resultados da pesquisa estiverem vazios, tem de recriar o principal de serviço ao seguir os passos na coluna "Resolução".

| ID da aplicação | Resolução |
| :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Recriar um principal de serviço em falta com o PowerShell](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Voltar a registar para o espaço de nomes de Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Voltar a registar para o espaço de nomes de Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Voltar a registar para o espaço de nomes de Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |

## <a name="recreate-a-missing-service-principal-with-powershell"></a>Recriar um Principal de serviço em falta com o PowerShell
Siga estes passos, se um principal de serviço com o ID de ```2565bd9d-da50-47d4-8b85-4c97f669dc36``` está em falta no diretório do Azure AD.

**Resolução:** É necessário o Azure AD PowerShell para concluir estes passos. Para obter informações sobre como instalar o Azure AD PowerShell, consulte [este artigo](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Para resolver este problema, escreva os seguintes comandos numa janela do PowerShell:
1. Instalar o módulo Azure AD PowerShell e importe-o.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Verifique se o principal de serviço necessário para o Azure AD Domain Services está em falta no seu diretório, executando o seguinte comando do PowerShell:

    ```powershell
    Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
    ```

3. Crie o principal de serviço, escrevendo o seguinte comando do PowerShell:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

4. Depois de criar o serviço em falta principal, aguarde duas horas e verifique o estado de funcionamento do seu domínio gerido.


## <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Voltar a registar para o espaço de nomes do Microsoft AAD com o portal do Azure
Siga estes passos, se um principal de serviço com o ID ```443155a6-77f3-45e3-882b-22b3a8d431fb``` ou ```abba844e-bc0e-44b0-947a-dc74e5d09022``` ou ```d87dcbc6-a371-462e-88e3-28ad15ec4e64``` está em falta no diretório do Azure AD.

**Resolução:** Utilize os seguintes passos para restaurar os serviços de domínio no seu diretório:

1. Navegue para o [subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) página no portal do Azure.
2. Escolha a subscrição da tabela que está associada ao seu domínio gerido
3. Usando o painel de navegação esquerda, escolha **fornecedores de recursos**
4. Procure "Microsoft.AAD" na tabela e clique em **voltar a registar**
5. Para garantir que o alerta é resolvido, ver a página de estado de funcionamento para o seu domínio gerido nas duas horas.


## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Alerta AADDS105: Aplicação de sincronização de palavra-passe está desatualizada

**Mensagem de alerta:** O principal de serviço com o ID da aplicação "d87dcbc6-a371-462e-88e3-28ad15ec4e64" foi eliminado e, em seguida, recriado. A recriação de deixa para trás inconsistentes permissões em recursos de serviços de domínio do Azure AD necessários para atender a seu domínio gerido. Sincronização de palavras-passe no seu domínio gerido poderá ser afetada.


**Resolução:** É necessário o Azure AD PowerShell para concluir estes passos. Para obter informações sobre como instalar o Azure AD PowerShell, consulte [este artigo](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Para resolver este problema, escreva os seguintes comandos numa janela do PowerShell:
1. Instalar o módulo Azure AD PowerShell e importe-o.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```
2. Eliminar a aplicação antiga e o objeto com os seguintes comandos do PowerShell

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```
3. Depois de ter eliminado ambos, o sistema irá remediar em si e recrie aos aplicativos necessários para a sincronização de palavra-passe. Para garantir que o alerta foi remediado, aguarde duas horas e verifique o estado de funcionamento do seu domínio.


## <a name="contact-us"></a>Contacte-nos
Contacte a equipa de produto do Azure Active Directory Domain Services para [partilhar comentários ou para o suporte](active-directory-ds-contact-us.md).
