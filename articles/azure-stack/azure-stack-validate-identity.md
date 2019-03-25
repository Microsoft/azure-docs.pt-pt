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
ms.date: 03/23/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 3dfb87e5f6c231831cd9c007b19ad001e1fce326
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403192"
---
# <a name="validate-azure-identity"></a>Validar a identidade do Azure

Utilize a ferramenta de verificação de preparação do Azure Stack (**AzsReadinessChecker**) para validar que o Azure Active Directory (Azure AD) está pronto para utilizar com o Azure Stack. Valide a sua solução de identidade do Azure antes de iniciar uma implementação do Azure Stack.  

Valida o Verificador de preparação:

- Azure Active Directory (Azure AD) como um fornecedor de identidade para o Azure Stack.
- A conta do Azure AD que planeia utilizar pode iniciar sessão como administrador global do seu Azure Active Directory.

A validação garante que o ambiente está preparado para o Azure Stack armazenar informações sobre utilizadores, aplicações, grupos e principais de serviço do Azure Stack no seu Azure AD.

## <a name="get-the-readiness-checker-tool"></a>Obter a ferramenta de verificação de preparação

Transferir a versão mais recente da ferramenta Verificador de preparação do Azure Stack (AzsReadinessChecker) a partir da [galeria do PowerShell](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Pré-requisitos

São necessários os seguintes pré-requisitos:

**O computador no qual a ferramenta é executada:**

- Windows 10 ou Windows Server 2016, com ligação à internet.
- PowerShell 5.1 ou posterior. Para verificar a sua versão, execute o seguinte comando do PowerShell e, em seguida, reveja os **principais** versão e **pequenas** versões:  

  ```powershell
  $PSVersionTable.PSVersion
  ```

- [PowerShell configurado para o Azure Stack](azure-stack-powershell-install.md).
- A versão mais recente do [Verificador de preparação do Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker) ferramenta.

**Ambiente do Active Directory do Azure:**

- Identifica a conta do Azure AD para utilizar para o Azure Stack e certifique-se de que é um administrador global do Azure Active Directory.
- Identificar o nome do seu inquilino do Azure AD. O nome do inquilino tem de ser o nome de domínio primário para o Azure Active Directory; Por exemplo, **contoso.onmicrosoft.com**.
- Identifica o ambiente do Azure que irá utilizar. Os valores suportados para o parâmetro de nome de ambiente são **AzureCloud**, **AzureChinaCloud**, ou **AzureUSGovernment**, consoante a subscrição do Azure que utiliza.

## <a name="steps-to-validate-azure-identity"></a>Passos para validar a identidade do Azure

1. Num computador que cumpra os pré-requisitos, abra uma linha de comandos elevada do PowerShell e, em seguida, execute o seguinte comando para instalar **AzsReadinessChecker**:  

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. A partir da linha de comandos do PowerShell, execute o seguinte comando para definir **$serviceAdminCredential** como o administrador de serviço para o seu inquilino do Azure AD.  Substitua **serviceadmin\@contoso.onmicrosoft.com** pelo nome da sua conta e de inquilino:

   ```powershell
   $serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter credentials for service administrator of Azure Active Directory tenant"
   ```

3. A partir da linha de comandos do PowerShell, execute o seguinte comando para iniciar a validação do seu Azure AD:

   - Especifique o valor de nome de ambiente para **AzureEnvironment**. Os valores suportados para o parâmetro de nome de ambiente são **AzureCloud**, **AzureChinaCloud**, ou **AzureUSGovernment**, consoante a subscrição do Azure que utiliza.
   - Substitua **contoso.onmicrosoft.com** com o nome do seu inquilino do Azure Active Directory.

   ```powershell
   Invoke-AzsAzureIdentityValidation -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment <environment name> -AADDirectoryTenantName contoso.onmicrosoft.com
   ```

4. Depois da ferramenta é executada, reveja o resultado. Confirme o estado está **OK** para requisitos de instalação. É apresentada uma validação com êxito semelhante à imagem seguinte:

   ```shell
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

Estes ficheiros podem ajudá-lo a compartilhar o estado de validação antes de implementar o Azure Stack ou investigar problemas de validação. Ambos os arquivos de mantêm os resultados de cada verificação de validação subsequentes. O relatório fornece a sua confirmação da equipe de implementação da configuração da identidade. O ficheiro de registo pode ajudar a sua equipa de suporte de implementação ou investigar problemas de validação.

Por predefinição, ambos os ficheiros são escritos **C:\Users\<nome de utilizador > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json**.  

- Utilize o **- OutputPath** ***&lt;caminho&gt;*** parâmetro no final da linha de comandos de execução para especificar uma localização diferente do relatório.
- Utilize o **- CleanReport** parâmetro no final do comando de execução para limpar informações sobre execuções anteriores da ferramenta de **AzsReadinessCheckerReport.json**.

Para obter mais informações, consulte [relatório de validação do Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Falhas de validação

Se uma verificação de validação falhar, exibir detalhes sobre a falha na janela do PowerShell. A ferramenta também regista informações para o ficheiro de AzsReadinessChecker.log.

Os exemplos seguintes fornecem orientação sobre falhas de validação comuns.

### <a name="expired-or-temporary-password"></a>Palavra-passe expirada ou temporário

```shell
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

**Resolução** - no PowerShell, execute o seguinte comando e, em seguida, siga as instruções para repor a palavra-passe:

```powershell
Login-AzureRMAccount
```

Em alternativa, inicie sessão para o [portal do Azure](https://portal.azure.com) como o proprietário da conta e o utilizador serão forçado a alterar a palavra-passe.

### <a name="unknown-user-type"></a>Tipo de utilizador desconhecido

```shell
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

**Causa** -a conta não pode iniciar sessão no especificado do Azure Active Directory (**AADDirectoryTenantName**). Neste exemplo, **AzureChinaCloud** está especificado como o **AzureEnvironment**.

**Resolução** -confirme que a conta é válida para o ambiente do Azure especificado. No PowerShell, execute o seguinte comando para verificar se que a conta é válida para um ambiente específico:

```powershell
Login-AzureRmAccount –EnvironmentName AzureChinaCloud
```

### <a name="account-is-not-an-administrator"></a>Conta não é um administrador

```shell
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

**Causa** -embora a conta conseguido iniciar sessão, a conta não é um administrador do Azure Active Directory (**AADDirectoryTenantName**).  

**Resolução** -iniciar sessão no [portal do Azure](https://portal.azure.com) como o proprietário da conta, aceda a **do Azure Active Directory**, em seguida, **utilizadores**, em seguida, **selecionar o Usuário**, em seguida, **função de diretório**e, em seguida, certifique-se o utilizador é um **Administrador Global**. Se a conta for uma **usuário**, aceda a **Azure Active Directory** > **nomes de domínio personalizado**e confirme que o nome que forneceu para  **AADDirectoryTenantName** está marcado como o nome de domínio principal para este diretório. Neste exemplo, que é **contoso.onmicrosoft.com**.

O Azure Stack requer que o nome de domínio é o nome de domínio primário.

## <a name="next-steps"></a>Próximos Passos

[Validar o registo do Azure](azure-stack-validate-registration.md)  
[Ver o relatório de preparação](azure-stack-validation-report.md)  
[Considerações sobre a integração geral do Azure Stack](azure-stack-datacenter-integration.md)  
