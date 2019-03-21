---
title: Validar a identidade do Azure para o Azure Stack | Documentos da Microsoft
description: Utilize o Verificador de preparação do Azure Stack para validar a identidade do Azure.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/04/2018
ms.openlocfilehash: b43fb3ff158a7df609d7a828192815db6b15963b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57850065"
---
# <a name="validate-azure-identity"></a>Validar a identidade do Azure 
Utilize a ferramenta de Verificador de preparação do Azure Stack (AzsReadinessChecker) para validar que o Azure Active Directory (Azure AD) está pronto para utilizar com o Azure Stack. Valide a sua solução de identidade do Azure antes de iniciar uma implementação do Azure Stack.  

Valida o Verificador de preparação:
 - Azure Active Directory (Azure AD) como um fornecedor de identidade para o Azure Stack.
 - A conta do Azure AD que planeja usar pode iniciar sessão como um administrador global do seu Azure Active Directory. 

A validação garante que o ambiente está preparado para o Azure Stack armazenar informações sobre utilizadores, aplicações, grupos e principais de serviço do Azure Stack no seu Azure AD.

## <a name="get-the-readiness-checker-tool"></a>Obter a ferramenta de verificação de preparação
Transferir a versão mais recente da ferramenta Verificador de preparação do Azure Stack (AzsReadinessChecker) a partir da [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Pré-requisitos
Os seguintes pré-requisitos devem ser cumpridos.

**O computador onde a ferramenta é executada:**
 - Windows 10 ou Windows Server 2016, com ligação à internet.
 - PowerShell 5.1 ou posterior. Para verificar a sua versão, escreva o comando PowerShell seguinte e, em seguida, reveja os *principais* versão e *pequenas* versões:  

   > `$PSVersionTable.PSVersion`
 - Configurar [PowerShell para o Azure Stack](azure-stack-powershell-install.md). 
 - A versão mais recente do [Verificador de preparação do Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker) ferramenta.

**Ambiente do Active Directory do Azure:**
 - Identifica a conta do Azure AD irá utilizar para o Azure Stack e certifique-se de que é um administrador do Azure Active Directory Global.
 - Identificar o seu nome de inquilino do Azure AD. O nome do inquilino tem de ser o *primário* nome de domínio para o Azure Active Directory. Por exemplo, *contoso.onmicrosoft.com*. 
 - Identifique o AzureEnvironment irá utilizar. Os valores suportados para o parâmetro de nome de ambiente são AzureCloud, AzureChinaCloud ou AzureUSGovernment consoante a subscrição do Azure que estiver a utilizar.

## <a name="validate-azure-identity"></a>Validar a identidade do Azure 
1. Num computador que cumpra os pré-requisitos, abra uma linha de comandos do PowerShell administrativa e, em seguida, execute o seguinte comando para instalar o AzsReadinessChecker:  

   > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. A partir da linha do PowerShell, execute o seguinte para definir *$serviceAdminCredential* como administrador do serviço para o seu inquilino do Azure AD.  Substitua *serviceadmin\@contoso.onmicrosoft.com* com sua conta e inquilino. 
   > `$serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant"` 

3. Prompt do PowerShell, execute o seguinte para iniciar a validação do seu Azure AD. 
   - Especifique o valor de nome de ambiente para AzureEnvironment. Os valores suportados para o parâmetro de nome de ambiente são AzureCloud, AzureChinaCloud ou AzureUSGovernment consoante a subscrição do Azure que estiver a utilizar.  
   - Especifique o Azure Active Directory nome do seu inquilino para substituir *contoso.onmicrosoft.com*. 

   > `Invoke-AzsAzureIdentityValidation -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment <environment name> -AADDirectoryTenantName contoso.onmicrosoft.com`
4. Depois da ferramenta é executada, reveja o resultado. Confirme o estado está **OK** para requisitos de instalação. É apresentada uma validação com êxito semelhante à imagem seguinte: 
 
```PowerShell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: OK

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```


## <a name="report-and-log-file"></a>Arquivo de log e relatório
Cada validação de tempo de execução, regista os resultados para **AzsReadinessChecker.log** e **AzsReadinessCheckerReport.json**. A localização dos ficheiros é apresentada com os resultados de validação no PowerShell.

Estes ficheiros podem ajudá-lo a compartilhar o estado de validação antes de implementar o Azure Stack ou investigar problemas de validação.  Ambos os arquivos de mantêm os resultados de cada verificação de validação subsequentes. O relatório fornece a sua confirmação da equipe de implementação da configuração da identidade. O ficheiro de registo pode ajudar a sua equipa de suporte de implementação ou investigar problemas de validação. 

Por predefinição, ambos os ficheiros são escritos *C:\Users\<nome de utilizador > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Utilize o **- OutputPath** ***&lt;caminho&gt;*** parâmetro no final da linha de comandos de execução para especificar uma localização diferente do relatório.   
 - Utilize o **- CleanReport** parâmetro no final do comando de execução para limpar as informações de *AzsReadinessCheckerReport.json*.  sobre execuções anteriores da ferramenta. 

Para obter mais informações, [relatório de validação do Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Falhas de validação
Se uma verificação de validação falhar, exibir detalhes sobre a falha na janela do PowerShell. A ferramenta também regista informações o AzsReadinessChecker.log.

Os exemplos seguintes fornecem orientação sobre falhas de validação comuns.

### <a name="expired-or-temporary-password"></a>Palavra-passe expirada ou temporário 
 
```PowerShell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail 
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The password for account  has expired or is a temporary password that needs to be reset before continuing. Run Login-AzureRMAccount, login with  credentials and follow the prompts to reset.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```
**Causa** -a conta não pode iniciar sessão porque a palavra-passe a expirou ou é temporária.     

**Resolução** - no PowerShell, execute o seguinte e, em seguida, siga as instruções para repor a palavra-passe.  
> `Login-AzureRMAccount`

Em alternativa, inicie sessão no https://portal.azure.com como a conta e o utilizador serão forçados a alterar a palavra-passe.
### <a name="unknown-user-type"></a>Tipo de utilizador desconhecido 
 
```PowerShell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail 
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
Unknown user type detected. Check the account  is valid for AzureChinaCloud
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```
**Causa** -a conta não pode iniciar sessão no Azure Active Directory diretório especificado (AADDirectoryTenantName). Neste exemplo, *AzureChinaCloud* está especificado como o *AzureEnvironment*.

**Resolução** -confirme que a conta é válida para o ambiente do Azure especificado. No PowerShell, execute o seguinte para verificar se que a conta é válida para um ambiente específico:   Login-AzureRmAccount-EnvironmentName AzureChinaCloud 
### <a name="account-is-not-an-administrator"></a>Conta não é um administrador 
 
```PowerShell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail 
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The Service Admin account you entered 'admin@contoso.onmicrosoft.com' is not an administrator of the Azure Active Directory tenant 'contoso.onmicrosoft.com'.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Causa** -embora a conta com êxito pode iniciar sessão, a conta não é um administrador do Azure Active Directory (AADDirectoryTenantName).  

**Resolução** -inicie sessão no https://portal.azure.com como a conta, aceda à **Azure Active Directory** > **utilizadores** > *selecione o utilizador*  >  **Função de diretório**e, em seguida, certifique-se o utilizador é um **Administrador Global**.  Se a conta for um utilizador, aceda a **do Azure Active Directory** > **Custom domain** nomes e confirme que o nome que forneceu para *AADDirectoryTenantName* é marcado como o nome de domínio principal para este diretório.  Neste exemplo, que é *contoso.onmicrosoft.com*. 

O Azure Stack requer que o nome de domínio é o nome de domínio primário.

## <a name="next-steps"></a>Próximos Passos
[Validar o registo do Azure](azure-stack-validate-registration.md)  
[Ver o relatório de preparação](azure-stack-validation-report.md)  
[Considerações sobre a integração geral do Azure Stack](azure-stack-datacenter-integration.md)  

