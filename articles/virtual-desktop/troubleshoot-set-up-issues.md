---
title: Área de Trabalho Virtual do Windows inquilino e o anfitrião de criação do conjunto - Azure
description: Como resolver o conjunto de inquilino e anfitrião problemas durante a configuração de um ambiente de inquilino de área de Trabalho Virtual do Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 88e843c410a750387ecf58497dec79586e2a59d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523333"
---
# <a name="tenant-and-host-pool-creation"></a>Criação de inquilinos e conjuntos de anfitriões

Este artigo aborda problemas durante a configuração inicial do inquilino a área de Trabalho Virtual do Windows e a infraestrutura de conjunto de host de sessão relacionados.

## <a name="provide-feedback"></a>Enviar comentários

Estamos atualmente não estão a demorar incidentes de suporte, enquanto a área de Trabalho Virtual do Windows está em pré-visualização. Visite o [Comunidade tecnológica da área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para discutir o serviço de área de Trabalho Virtual do Windows com a equipe do produto e membros da Comunidade de Active Directory.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Adquirir a imagem de várias sessões do Windows 10 Enterprise

Para utilizar a imagem de várias sessões do Windows 10 Enterprise, vá para o Azure Marketplace, selecione **começar** > **10 do Microsoft Windows** > e [Windows 10 Enterprise para Pré-visualização de áreas de trabalho virtuais, versão 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

![Uma captura de ecrã da seleção do Windows 10 Enterprise para a pré-visualização de áreas de Trabalho Virtual, 1809 de versão.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Criar o inquilino de área de Trabalho Virtual do Windows

Esta secção abrange os potenciais problemas ao criar o inquilino de área de Trabalho Virtual do Windows.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Erro: O utilizador não está autorizado a consultar o serviço de gestão

![Janela de captura de ecrã do PowerShell em que um utilizador não está autorizado a consultar o serviço de gestão.](media/UserNotAuthorizedNewTenant.png)

Exemplo de erro bruto:

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

**Causa:** O utilizador que tenha sessão iniciado não tenha sido atribuído a função de TenantCreator no seu Azure Active Directory.

**CORREÇÃO:** Siga as instruções em [atribuir a função de aplicação TenantCreator a um utilizador no seu inquilino do Azure Active Directory](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory#assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant). Depois de seguir as instruções, terá um utilizador atribuído à função TenantCreator.

![Captura de ecrã de TenantCreator função atribuída.](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Criação de VMs do host de sessão de área de Trabalho Virtual do Windows

Anfitrião de sessões de VMs pode ser criada de várias maneiras, mas as equipes de remoto Desktop Windows/serviços de área de Trabalho Virtual só suportam problemas relacionados com o modelo Azure Resource Manager de aprovisionamento da VM. O modelo Azure Resource Manager está disponível no [do Azure Marketplace](https://azuremarketplace.microsoft.com/) e [GitHub](https://github.com/).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problemas com o Windows Virtual Desktop – aprovisionar uma oferta de Azure Marketplace do conjunto de anfitrião

A área de trabalho de Virtual do Windows – aprovisionar um modelo de agrupamento do anfitrião está disponível no Azure Marketplace.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Erro: Ao utilizar a ligação a partir do GitHub, a mensagem "Criar uma conta gratuita" é apresentada

![Captura de ecrã para criar uma conta gratuita.](media/be615904ace9832754f0669de28abd94.png)

**Fazer com que 1:** Não existem subscrições ativas na conta utilizada para iniciar sessão no Azure ou a conta utilizada não tem permissões para ver as subscrições.

**Corrigi 1:** Inicie sessão com uma conta que tenha acesso de contribuinte (no mínimo) para a subscrição em que vai ser implementado anfitrião de sessões de VMs.

**Causa 2:** A subscrição que está a ser utilizada é parte de um inquilino do fornecedor de serviços Cloud (CSP) da Microsoft.

**Corrigi 2:** Vá para a localização do GitHub para **criar e aprovisionar conjunto de anfitrião do ambiente de Trabalho Virtual do Windows nova** e siga estas instruções:

1. Com o botão direito no **implementar no Azure** e selecione **endereço da ligação de cópia**.
2. Open **bloco de notas** e cole a ligação.
3. Antes do caractere #, insira o nome de inquilino do cliente do CSP final.
4. Abra a nova ligação num navegador e o portal do Azure irá carregar o modelo.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Modelo Azure Resource Manager e erros de PowerShell Desired State Configuration (DSC)

Siga estas instruções para resolver implementações sem êxito de modelos Azure Resource Manager e PowerShell DSC.

1. Reveja os erros na implementação utilizando [ver as operações de implementação Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
2. Se não existirem erros na implementação, reveja os erros na através do registo de atividade [ver registos de atividades para auditar as ações em recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
3. Depois do erro for identificado, usar a mensagem de erro e os recursos [resolver erros comuns de implementação do Azure com o Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors) para resolver o problema.
4. Elimine todos os recursos criados durante a implementação anterior e repita novamente a implementar o modelo.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Erro: A implementação falhou...<hostname>/joindomain

![A captura de ecrã a implementação falhou.](media/e72df4d5c05d390620e07f0d7328d50f.png)

Exemplo de erro bruto:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. 
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Fazer com que 1:** Credenciais fornecidas para adicionar VMs ao domínio estão incorretas.

**Corrigi 1:** Consulte o erro "Credenciais incorretas" para VMs não estão associadas ao domínio [configuração de VM de anfitrião de sessão](troubleshoot-vm-configuration.md).

**Causa 2:** Nome de domínio não resolve.

**Corrigi 2:** Consulte o erro "não resolve o nome de domínio" para VMs não estão associadas ao domínio [configuração de VM de anfitrião de sessão](troubleshoot-vm-configuration.md).

### <a name="error-vmextensionprovisioningerror"></a>Erro: VMExtensionProvisioningError

![Falha na captura de ecrã da sua implementação falhou com o estado de aprovisionamento de terminal.](media/7aaf15615309c18a984673be73ac969a.png)

**Fazer com que 1:** Erro transitório com o ambiente de área de Trabalho Virtual do Windows.

**Causa 2:** Erro transitório com ligação de rede.

**CORREÇÃO:** Certifique-se o ambiente de área de Trabalho Virtual do Windows está em bom estado ao iniciar sessão com o PowerShell. Concluído o registo VM manualmente em [criar um conjunto de anfitriões com o PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Erro: O nome de utilizador de Admin especificado não é permitida

![Captura de ecrã da sua implementação falha em que um administrador especificado não é permitido.](media/f2b3d3700e9517463ef88fa41875bac9.png)

Exemplo de erro bruto:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Causa:** Palavra-passe fornecida contém subcadeias de carateres proibidas (admin, administrador, raiz).

**CORREÇÃO:** Atualizar o nome de utilizador ou utilizar diferentes utilizadores.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Erro: VM reportou uma falha durante o processamento de extensão

![Captura de ecrã da operação de recursos foi concluída com o estado de aprovisionamento de terminal na sua implementação falhou.](media/49c4a1836a55d91cd65125cf227f411f.png)

Exemplo de erro bruto:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-hostpool4-preview-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'. 
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few: 
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message: 
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource": 
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft. 
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

**Causa:** Extensão DSC do PowerShell não foi possível obter o acesso de administrador na VM.

**CORREÇÃO:** Confirme o nome de utilizador e palavra-passe têm acesso administrativo na máquina virtual e execute novamente o modelo Azure Resource Manager.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Erro: DeploymentFailed – configuração de DSC de PowerShell "FirstSessionHost' foi concluída com erros

![Captura de ecrã da implementação falhar com a configuração de DSC de PowerShell "FirstSessionHost' foi concluída com erros.](media/64870370bcbe1286906f34cf0a8646ab.png)

Exemplo de erro bruto:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list 
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",  
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**Causa:** Extensão DSC do PowerShell não foi possível obter o acesso de administrador na VM.

**CORREÇÃO:** Confirme o nome de utilizador e palavra-passe fornecidos têm acesso administrativo na máquina virtual e execute novamente o modelo Azure Resource Manager.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Erro: DeploymentFailed – InvalidResourceReference

Exemplo de erro bruto:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Causa:** Parte do nome do grupo de recursos é utilizado para determinados recursos a ser criados pelo modelo. Devido ao nome correspondente a recursos existentes, o modelo pode selecionar um recurso existente de um grupo diferente.

**CORREÇÃO:** Ao executar o modelo Azure Resource Manager para implementar o anfitrião de sessões de VMs, que os dois primeiros caracteres exclusivo para o seu nome de grupo de recursos de subscrição.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Erro: DeploymentFailed – InvalidResourceReference

Exemplo de erro bruto:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**Causa:** Este erro ocorre porque o NIC criado com o modelo Azure Resource Manager tem o mesmo nome que outra NIC já na VNET.

**CORREÇÃO:** Utilize um prefixo de anfitrião diferente.

### <a name="error-deploymentfailed--error-downloading"></a>Erro: DeploymentFailed – erro ao transferir

Exemplo de erro bruto:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-hostpool-3-preview-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-private-preview-
1/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Causa:** Este erro é devido a uma rota estática, uma regra de firewall ou um NSG a bloquear o download do arquivo zip vinculado ao modelo do Azure Resource Manager.

**CORREÇÃO:** Remova o bloqueio de rota estática, regra de firewall ou NSG. Opcionalmente, abra o ficheiro de json do modelo do Azure Resource Manager num editor de texto, efetuar a ligação para o ficheiro zip e transferir o recurso para uma localização permitida.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Erro: O utilizador não está autorizado a consultar o serviço de gestão

Exemplo de erro bruto:

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00", 
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing 
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s). 
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n 
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

**Causa:** O administrador de inquilino de área de Trabalho Virtual do Windows especificado não tem uma atribuição de função válido.

**CORREÇÃO:** O utilizador que criou o inquilino de área de Trabalho Virtual do Windows tem de iniciar sessão no Windows PowerShell de ambiente de Trabalho Virtual e atribua o utilizador tentado uma atribuição de função. Se estiver a executar os parâmetros de modelo do GitHub do Azure Resource Manager, siga estas instruções utilizar comandos do PowerShell:

```PowerShell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName “RDS Contributor” -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Erro: Utilizador requer a Azure multi-factor Authentication (MFA)

![Captura de ecrã da implementação falhou devido à falta de multi-factor Authentication (MFA)](media/MFARequiredError.png)

Exemplo de erro bruto:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Causa:** O administrador de inquilino de área de Trabalho Virtual do Windows especificado requer o Azure multi-factor Authentication (MFA) para iniciar sessão.

**CORREÇÃO:** Criar um principal de serviço e atribua-uma função para o seu inquilino de área de Trabalho Virtual do Windows ao seguir os passos no [Tutorial: Criar principais de serviço e as atribuições de funções com o PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell). Depois de verificar que pode iniciar sessão área de Trabalho Virtual do Windows com o principal de serviço, execute novamente a oferta do Azure Marketplace ou do modelo de GitHub do Azure Resource Manager, dependendo de qual o método que está a utilizar. Siga as instruções abaixo para introduzir os parâmetros corretos para o seu método.

Se estiver a executar a oferta do Azure Marketplace, forneça valores para os seguintes parâmetros autenticar corretamente para a área de Trabalho Virtual do Windows:

- Proprietário de RDS de inquilino de área de Trabalho Virtual do Windows: Principal de serviço
- ID da aplicação: A identificação de aplicação do principal de serviço novo que criou
- Palavra-passe/Confirmar palavra-passe: O segredo de palavra-passe gerada para o principal de serviço
- ID de inquilino do Azure AD: O ID de inquilino do Azure AD do principal de serviço que criou

Se estiver a executar o modelo do GitHub do Azure Resource Manager, forneça valores para os seguintes parâmetros autenticar corretamente para a área de Trabalho Virtual do Windows:

- Nome principal do inquilino administrador utilizador (UPN) ou ID da aplicação: A identificação de aplicação do principal de serviço novo que criou
- Palavra-passe de administrador de inquilino: O segredo de palavra-passe gerada para o principal de serviço
- IsServicePrincipal: **true**
- AadTenantId: O ID de inquilino do Azure AD do principal de serviço que criou

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral na área de Trabalho Virtual do Windows e as faixas de escalonamento de resolução de problemas, consulte [descrição geral, comentários e suporte de resolução de problemas](troubleshoot-set-up-overview.md).
- Para resolver problemas ao configurar uma máquina virtual (VM) na área de Trabalho Virtual do Windows, consulte [configuração de máquina virtual do anfitrião de sessão](troubleshoot-vm-configuration.md).
- Para resolver problemas com ligações de cliente de área de Trabalho Virtual do Windows, consulte [ligações de cliente de ambiente de trabalho remoto](troubleshoot-client-connection.md).
- Para resolver problemas ao utilizar o PowerShell com a área de Trabalho Virtual do Windows, consulte [Windows PowerShell de ambiente de Trabalho Virtual](troubleshoot-powershell.md).
- Para saber mais sobre o serviço de pré-visualização, veja [ambiente de pré-visualização do Windows Desktop](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Para seguir um tutorial de resolução de problemas, consulte [Tutorial: Resolver problemas de implementações de modelo do Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Para saber mais sobre a auditoria de ações, veja [auditar operações com o Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Para saber mais sobre as ações para determinar os erros durante a implementação, veja [ver as operações de implementação](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).