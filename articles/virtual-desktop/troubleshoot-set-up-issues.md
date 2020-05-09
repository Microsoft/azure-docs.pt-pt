---
title: Windows Virtual Desktop ambiente anfitrião criação de piscina - Azure
description: Como resolver problemas e resolver problemas de piscina de inquilinos e anfitriões durante a configuração de um ambiente de ambiente de trabalho virtual windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 01/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 65a61babe58e1cb9438262186a7f4cf37cb10a34
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612600"
---
# <a name="host-pool-creation"></a>Criação de piscina anfitriã

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização da primavera de 2020 com os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows. Se estiver a utilizar o lançamento do Windows Virtual Desktop Fall 2019 sem objetos do Gestor de Recursos Azure, consulte [este artigo](./virtual-desktop-fall-2019/troubleshoot-set-up-issues-2019.md).
>
> A atualização Do Windows Virtual Desktop Spring 2020 encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-la para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Este artigo aborda problemas durante a configuração inicial do inquilino do Windows Virtual Desktop e a infraestrutura de piscina de hospedas de sessão relacionada.

## <a name="provide-feedback"></a>Enviar comentários

Visite o [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para discutir o serviço de desktop virtual windows com a equipa de produtos e membros ativos da comunidade.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Adquirir a imagem multi-sessão do Windows 10 Enterprise

Para utilizar a imagem multissessão do Windows 10 Enterprise, vá ao Mercado Azure, selecione **Start Microsoft** > **Windows 10** > e [Windows 10 Enterprise multi-sessão, Versão 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

## <a name="issues-with-using-the-azure-portal-to-create-host-pools"></a>Problemas com a utilização do portal Azure para criar piscinas de acolhimento

### <a name="error-create-a-free-account-appears-when-accessing-the-service"></a>Erro: "Criar uma conta gratuita" aparece ao aceder ao serviço

![Uma imagem mostrando o portal Azure exibindo a mensagem "Criar uma conta gratuita"](media/create-new-account.png)

**Causa**: Não existem subscrições ativas na conta com a qual assinou no Azure, ou a conta não tem permissões para visualizar as subscrições. 

**Correção**: Inscreva-se na subscrição onde irá implementar as máquinas virtuais anfitriãs da sessão (VMs) com uma conta que tem pelo menos acesso ao nível dos contribuintes.

### <a name="error-exceeding-quota-limit"></a>Erro: "Exceder o limite de quota"

Se a sua operação ultrapassar o limite de quota, pode fazer uma das seguintes coisas: 

- Crie uma nova piscina de anfitriões com os mesmos parâmetros, mas menos VMs e núcleos VM.

- Abra o link que vê no campo statusMessage num browser para submeter um pedido para aumentar a quota para a sua subscrição Azure para o SKU VM especificado.

## <a name="azure-resource-manager-template-errors"></a>Erros de modelo do Gestor de Recursos Azure

Siga estas instruções para resolução de problemas de implementações mal sucedidas de modelos do Gestor de Recursos Azure e do PowerShell DSC.

1. Reveja os erros na implementação utilizando as operações de implantação do View com o [Gestor de Recursos Azure](../azure-resource-manager/resource-manager-deployment-operations.md).
2. Se não houver erros na implementação, reveja os erros no registo de atividade utilizando registos de atividade do [View para auditar ações sobre recursos](../azure-resource-manager/resource-group-audit.md).
3. Uma vez identificado o erro, utilize a mensagem de erro e os recursos em Troubleshoot erros comuns de implementação do [Azure com o Gestor de Recursos Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md) para resolver o problema.
4. Elimine quaisquer recursos criados durante a implementação anterior e volte a implementar o modelo novamente.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Erro: A sua implementação falhou....\<nome de anfitrião>/joindomain

![A sua imagem falhada de implantação.](media/failure-joindomain.png)

Exemplo de erro bruto:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Causa 1:** As credenciais previstas para a adesão de VMs ao domínio estão incorretas.

**Correção 1:** Ver que o erro das "credenciais incorretas" para os VMs não está unido ao domínio na [configuração VM do anfitrião](troubleshoot-vm-configuration.md)da sessão .

**Causa 2:** O nome de domínio não resolve.

**Correção 2:** Ver [Erro: O nome](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve) do domínio não resolve na [configuração VM do anfitrião](troubleshoot-vm-configuration.md)da sessão .

**Causa 3:** A configuração DNS da rede virtual (VNET) está definida para **Padrão**.

Para corrigir isto, faça as seguintes coisas:

1. Abra o portal Azure e vá ao separador **redes Virtuais.**
2. Encontre o seu VNET e, em seguida, selecione **servidores DNS**.
3. O menu de servidores DNS deve aparecer no lado direito do ecrã. Nesse menu, selecione **Custom**.
4. Certifique-se de que os servidores DNS listados em Custom correspondem ao seu controlador de domínio ou ao domínio ative directory. Se não vir o seu servidor DNS, pode adicioná-lo introduzindo o seu valor no campo de **servidor DNS Add.**

### <a name="error-your-deployment-failedunauthorized"></a>Erro: A sua implementação falhou...\Não autorizada

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Causa:** A subscrição que está a usar é um tipo que não consegue aceder às funcionalidades necessárias na região onde o cliente está a tentar implementar. Por exemplo, as subscrições de MSDN, Free ou Education podem mostrar este erro.

**Correção:** Altere o seu tipo de subscrição ou região para aquele que pode aceder às funcionalidades necessárias.

### <a name="error-vmextensionprovisioningerror"></a>Erro: VMExtensionProvisioningError

![Screenshot da sua implantação falhou com o estado de provisionamento terminal falhado.](media/failure-vmextensionprovisioning.png)

**Causa 1:** Erro transitório com o ambiente de ambiente de trabalho virtual do Windows.

**Causa 2:** Erro transitório com ligação.

**Correção:** Confirme que o ambiente de trabalho virtual do Windows é saudável ao iniciar sessão utilizando o PowerShell. Termine o registo VM manualmente em [Criar uma piscina de anfitriões com powerShell](create-host-pools-powershell.md).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Erro: O nome de utilizador do Administrador especificado não é permitido

![A imagem da sua implantação falhou na qual um administrador especificado não é permitido.](media/failure-username.png)

Exemplo de erro bruto:

```Error
 { …{ "provisioningOperation": 
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId": 
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message": 
 "The Admin Username specified is not allowed.", "target": "adminUsername" } … }
```

**Causa:** A palavra-passe fornecida contém substrings proibidos (administrador, administrador, raiz).

**Correção:** Atualizar o nome de utilizador ou utilizar diferentes utilizadores.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Erro: VM reportou uma falha no processamento de extensão

![Screenshot da operação de recursos concluída com estado de provisionamento terminal na sua implementação falhou.](media/failure-processing.png)

Exemplo de erro bruto:

```Error
{ … "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code": 
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'. 
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message: 
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] … }
```

**Causa:** A extensão DoDSC powerShell não foi capaz de obter acesso administrativo no VM.

**Correção:** Confirme o nome de utilizador e a palavra-passe ter acesso administrativo na máquina virtual e executar novamente o modelo do Gestor de Recursos Azure.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Erro: Implementação Falhada – Configuração 'FirstSessionHost' de configuração do PowerShell DSC concluída com erro(s)

![Falha na falha de implementação com configuração do DSC powerShell 'FirstSessionHost' concluída com Error(s).](media/failure-dsc.png)

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

**Causa:** A extensão DoDSC powerShell não foi capaz de obter acesso administrativo no VM.

**Correção:** Confirme o nome de utilizador e a palavra-passe fornecida têm acesso administrativo na máquina virtual e executem novamente o modelo do Gestor de Recursos Azure.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Erro: Implementação Falhada – Referência de Recursos Inválidos

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

**Causa:** Parte do nome do grupo de recursos é usado para certos recursos criados pelo modelo. Devido ao nome que combina com os recursos existentes, o modelo pode selecionar um recurso existente de um grupo diferente.

**Correção:** Ao executar o modelo do Gestor de Recursos Azure para implementar VMs de anfitrião da sessão, torne os dois primeiros caracteres únicos para o seu nome de grupo de recursos de subscrição.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Erro: Implementação Falhada – Referência de Recursos Inválidos

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

**Causa:** Este erro deve-se ao facto de o NIC criado com o modelo do Gestor de Recursos Azure ter o mesmo nome que outro NIC já no VNET.

**Correção:** Use um prefixo de hospedeiro diferente.

### <a name="error-deploymentfailed--error-downloading"></a>Error: DeploymentFailed – Error download

Exemplo de erro bruto:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Causa:** Este erro deve-se a uma rota estática, regra da firewall ou NSG bloqueando o download do ficheiro zip ligado ao modelo do Gestor de Recursos Azure.

**Correção:** Remova o bloqueio da rota estática, regra da firewall ou NSG. Opcionalmente, abra o ficheiro json modelo do Gestor de Recursos Do Azure num editor de texto, leve o link para o ficheiro zip e descarregue o recurso para uma localização permitida.

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral sobre a resolução de problemas do Windows Virtual Desktop e as faixas de escalada, consulte a [visão geral, feedback e suporte](troubleshoot-set-up-overview.md)de Resolução de Problemas.
- Para resolver problemas ao configurar uma máquina virtual (VM) no Windows Virtual Desktop, consulte a [configuração virtual](troubleshoot-vm-configuration.md)do anfitrião da sessão .
- Para resolver problemas com as ligações do cliente do Windows Virtual Desktop, consulte [as ligações](troubleshoot-service-connection.md)de serviço do Windows Virtual Desktop .
- Para resolver problemas com clientes do Desktop Remoto, consulte [Troubleshoot o cliente Remote Desktop](troubleshoot-client.md)
- Para resolver problemas ao utilizar o PowerShell com o Windows Virtual Desktop, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Para saber mais sobre o serviço, consulte o ambiente de ambiente de [trabalho virtual do Windows](environment-setup.md).
- Para passar por um tutorial de resolução de [problemas, consulte Tutorial: Implementações](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)do modelo de gestor de recursos de resolução de problemas .
- Para conhecer as ações de auditoria, consulte operações de [auditoria com o Gestor de Recursos.](../azure-resource-manager/management/view-activity-logs.md)
- Para aprender sobre as ações para determinar os erros durante a implementação, consulte as operações de [implantação do View](../azure-resource-manager/templates/deployment-history.md).
