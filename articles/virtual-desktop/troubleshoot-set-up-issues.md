---
title: Windows Virtual Desktop ambiente anfitrião criação de piscina - Azure
description: Como resolver problemas e resolver problemas de inquilinos e piscinas durante a configuração de um ambiente de desktop virtual do Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: c31a32b32a685087c53198ec52af1188d0071cab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652450"
---
# <a name="host-pool-creation"></a>Criação de conjuntos de anfitriões

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/troubleshoot-set-up-issues-2019.md).

Este artigo cobre problemas durante a configuração inicial do inquilino virtual do Windows Desktop e da infraestrutura de piscina de anfitrião de sessão relacionada.

## <a name="provide-feedback"></a>Enviar comentários

Visite a [Comunidade Virtual desktop tech do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para discutir o serviço de desktop virtual do Windows com a equipa de produtos e membros da comunidade ativa.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Aquisição da imagem multi-sessão do Windows 10 Enterprise

Para utilizar a imagem multi-sessão empresarial do Windows 10, vá ao Azure Marketplace, **selecione Get Start** Microsoft Windows  >  **10** > e Windows [10 Enterprise multi-sessão, Versão 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

## <a name="issues-with-using-the-azure-portal-to-create-host-pools"></a>Problemas com a utilização do portal Azure para criar piscinas hospedeiras

### <a name="error-create-a-free-account-appears-when-accessing-the-service"></a>Erro: "Criar uma conta gratuita" aparece ao aceder ao serviço

> [!div class="mx-imgBorder"]
> ![Uma imagem mostrando o portal Azure exibindo a mensagem "Criar uma conta gratuita"](media/create-new-account.png)

**Causa**: Não existem subscrições ativas na conta com a qual assinou com o Azure, ou a conta não tem permissões para visualizar as subscrições.

**Correção**: Faça o sessão onde irá implantar as máquinas virtuais (VMs) do anfitrião da sessão com uma conta que tenha pelo menos acesso ao nível do contribuinte.

### <a name="error-exceeding-quota-limit"></a>Erro: "Exceder o limite de quota"

Se a sua operação ultrapassar o limite de quota, pode fazer uma das seguintes coisas:

- Crie uma nova piscina hospedeira com os mesmos parâmetros, mas menos VMs e núcleos VM.

- Abra o link que vê no campo statusMessage num browser para submeter um pedido de aumento da quota para a sua subscrição Azure para o VM SKU especificado.

### <a name="error-cant-see-user-assignments-in-app-groups"></a>Erro: Não é possível ver atribuições de utilizadores em grupos de aplicações.

**Causa:** Este erro geralmente acontece depois de ter mudado a subscrição de 1 Azure Ative Directory (AD) inquilino para outro. Se as suas antigas missões ainda estiverem ligadas ao antigo inquilino da AD Azure, o portal Azure perderá o rasto deles.

**Correção**: Terá de reatribuir os utilizadores a grupos de aplicações.

### <a name="i-only-see-us-when-setting-the-location-for-my-service-objects"></a>Só nos vejo quando defini a localização dos meus objetos de serviço

**Causa**: A Azure não suporta atualmente aquela região para o serviço de desktop virtual do Windows. Para saber quais as geografias que apoiamos, confira [as localizações dos Dados.](data-locations.md) Se o Windows Virtual Desktop suportar a localização, mas ainda não aparecer quando está a tentar selecionar uma localização, isso significa que o seu fornecedor de recursos ainda não foi atualizado.

**Correção**: Para obter a última lista de regiões, re-registe o fornecedor de recursos:

1. Vá a **Subscrições** e selecione a subscrição relevante.
2. Selecione **Fornecedor de Recursos**.
3. Selecione **Microsoft.DesktopVirtualization** e, em seguida, **selecione Re-registrar-se** no menu de ação.

Quando voltar a registar o fornecedor de recursos, não verá nenhum feedback específico da UI ou estados de atualização. O processo de reins registo também não interferirá com os seus ambientes existentes.

## <a name="azure-resource-manager-template-errors"></a>Erros do modelo do Gestor de Recursos Azure

Siga estas instruções para resolver implementações infrutíferas dos modelos do Gestor de Recursos Azure e do PowerShell DSC.

1. Rever erros na implementação utilizando [operações de implementação do View com o Azure Resource Manager](../azure-resource-manager/templates/deployment-history.md).
2. Se não houver erros na implementação, reveja os erros no registo de atividades utilizando [registos de atividades do View para auditar ações sobre recursos](../azure-resource-manager/management/view-activity-logs.md).
3. Assim que o erro for identificado, utilize a mensagem de erro e os recursos em [erros comuns de implementação do Azure com o Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md) para resolver o problema.
4. Elimine quaisquer recursos criados durante a implementação anterior e volte a tentar implementar o modelo novamente.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Erro: A sua implantação falhou.... \<hostname> /joindomain

> [!div class="mx-imgBorder"]
> ![A sua imagem falhada de implantação falhou.](media/failure-joindomain.png)

Exemplo de erro bruto:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Causa 1:** As credenciais previstas para a junção de VMs ao domínio estão incorretas.

**Correção 1:** Consulte o erro de "credenciais incorretas" para VMs não se junta ao domínio na [configuração VM do anfitrião da Sessão](troubleshoot-vm-configuration.md).

**Causa 2:** O nome de domínio não resolve.

**Correção 2:** Ver [Erro: O nome de domínio não se resolve](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve) na [configuração VM do anfitrião da Sessão](troubleshoot-vm-configuration.md).

**Causa 3:** A configuração DE DNS da sua rede virtual (VNET) está definida como **Padrão**.

Para corrigir isto, faça as seguintes coisas:

1. Abra o portal Azure e vá ao separador **redes Virtuais.**
2. Encontre o seu VNET e, em seguida, selecione **servidores DNS**.
3. O menu de servidores DNS deve aparecer no lado direito do seu ecrã. Nesse menu, selecione **Custom**.
4. Certifique-se de que os servidores DNS listados no Custom correspondem ao seu controlador de domínio ou ao domínio do Ative Directory. Se não vir o servidor DNS, pode adicioná-lo introduzindo o seu valor no campo do **servidor Add DNS.**

### <a name="error-your-deployment-failedunauthorized"></a>Erro: Falha na implementação...\Não autorizado

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Causa:** A subscrição que está a usar é um tipo que não consegue aceder às funcionalidades necessárias na região onde o cliente está a tentar implementar. Por exemplo, as assinaturas MSDN, Free ou Education podem mostrar este erro.

**Correção:** Altere o tipo de subscrição ou região para um que possa aceder às funcionalidades necessárias.

### <a name="error-vmextensionprovisioningerror"></a>Erro: VMExtensionProvisioningError

> [!div class="mx-imgBorder"]
> ![A screenshot da sua implantação falhou com o estado de provisionamento terminal falhado.](media/failure-vmextensionprovisioning.png)

**Causa 1:** Erro transitório com o ambiente de trabalho virtual do Windows.

**Causa 2:** Erro transitório com ligação.

**Correção:** Confirme que o ambiente de ambiente de trabalho virtual do Windows é saudável ao iniciar sessão com o PowerShell. Termine manualmente o registo VM em [Criar uma piscina de anfitriões com PowerShell](create-host-pools-powershell.md).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Erro: O Nome de Utilizador do Administrador especificado não é permitido

> [!div class="mx-imgBorder"]
> ![A imagem da sua implantação falhou na qual não é permitido um administrador especificado.](media/failure-username.png)

Exemplo de erro bruto:

```Error
 { …{ "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } … }
```

**Causa:** A palavra-passe fornecida contém sublamentos proibidos (administrador, administrador, raiz).

**Correção:** Atualizar o nome de utilizador ou utilizar diferentes utilizadores.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Erro: A VM reportou uma falha durante o processamento da extensão

> [!div class="mx-imgBorder"]
> ![Screenshot da operação de recursos concluída com estado de provisionamento terminal na Sua Implementação Falhou.](media/failure-processing.png)

Exemplo de erro bruto:

```Error
{ … "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'.
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message:
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] … }
```

**Causa:** A extensão DSC da PowerShell não foi capaz de obter acesso administrativo no VM.

**Correção:** Confirme o nome de utilizador e a palavra-passe têm acesso administrativo na máquina virtual e execute novamente o modelo do Gestor de Recursos Azure.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Erro: ImplementaçãoFailed – Configuração DShell 'FirstSessionHost' concluída com Erro(s)

> [!div class="mx-imgBorder"]
> ![Screenshot de falha de implementação com configuração DSC 'FirstSessionHost' completada com Erro(s).](media/failure-dsc.png)

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

**Causa:** A extensão DSC da PowerShell não foi capaz de obter acesso administrativo no VM.

**Correção:** Confirme o nome de utilizador e a palavra-passe fornecidas têm acesso administrativo na máquina virtual e execute novamente o modelo do Gestor de Recursos Azure.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Erro: ImplementaçãoFailed – InvalidResourceReference

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

**Causa:** Parte do nome do grupo de recursos é usado para certos recursos criados pelo modelo. Devido ao nome que corresponde aos recursos existentes, o modelo pode selecionar um recurso existente de um grupo diferente.

**Correção:** Ao executar o modelo Azure Resource Manager para implementar VMs de anfitrião de sessão, torne os dois primeiros caracteres únicos para o nome do grupo de recursos de subscrição.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Erro: ImplementaçãoFailed – InvalidResourceReference

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

**Causa:** Este erro deve-se ao facto de o NIC criado com o modelo Azure Resource Manager ter o mesmo nome que outro NIC já no VNET.

**Correção:** Use um prefixo de hospedeiro diferente.

### <a name="error-deploymentfailed--error-downloading"></a>Erro: ImplementaçãoFailed – Transferência de erros

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

**Causa:** Este erro deve-se a uma rota estática, regra de firewall ou NSG bloqueando o descarregamento do ficheiro zip ligado ao modelo Azure Resource Manager.

**Correção:** Remova a rota estática de bloqueio, a regra da firewall ou a NSG. Opcionalmente, abra o ficheiro json do modelo Azure Resource Manager num editor de texto, pegue o link para o ficheiro zip e descarregue o recurso para uma localização permitida.

### <a name="error-cant-delete-a-session-host-from-the-host-pool-after-deleting-the-vm"></a>Erro: Não é possível apagar um anfitrião da sessão da piscina anfitriã depois de eliminar o VM

**Causa:** Tem de eliminar o anfitrião da sessão antes de eliminar o VM.

**Correção:** Coloque o anfitrião da sessão no modo de drenagem, inscreva todos os utilizadores do anfitrião da sessão e, em seguida, elimine o anfitrião.

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral sobre a resolução de problemas do Windows Virtual Desktop e as faixas de escalada, consulte [a visão geral da resolução de problemas, o feedback e o suporte](troubleshoot-set-up-overview.md).
- Para resolver problemas enquanto configura uma máquina virtual (VM) no Windows Virtual Desktop, consulte a [configuração da máquina virtual do anfitrião da Sessão](troubleshoot-vm-configuration.md).
- Para resolver problemas relacionados com o agente virtual do Windows desktop ou conectividade de sessão, consulte [problemas comuns](troubleshoot-agent.md)de Computador de Secretária virtual do Windows .
- Para resolver problemas com as ligações do cliente virtual do Windows Desktop, consulte [as ligações do serviço de desktop virtual do Windows](troubleshoot-service-connection.md).
- Para resolver problemas com clientes de ambiente de trabalho remoto, consulte [Troubleshoot o cliente Remote Desktop](troubleshoot-client.md)
- Para resolver problemas ao utilizar o PowerShell com o Windows Virtual Desktop, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Para saber mais sobre o serviço, consulte o [ambiente de ambiente de trabalho virtual do Windows.](environment-setup.md)
- Para passar por um tutorial de resolução de [problemas, consulte Tutorial: Implementações de modelos do Gestor de Recursos de Resolução de Problemas](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Para conhecer as ações de auditoria, consulte [as operações de Auditoria com o Gestor de Recursos.](../azure-resource-manager/management/view-activity-logs.md)
- Para obter ações para determinar os erros durante a implementação, consulte [as operações de implantação](../azure-resource-manager/templates/deployment-history.md)da visualização .
