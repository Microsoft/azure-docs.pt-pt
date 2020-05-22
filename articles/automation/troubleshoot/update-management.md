---
title: Problemas de resolução de problemas Problemas Azure Automation Update Management
description: Este artigo diz como resolver problemas e resolver problemas com a Azure Automation Update Management.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/17/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 35049e148af09376667a55e2f0bb4a28cf728245
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83735893"
---
# <a name="troubleshoot-update-management-issues"></a>Problemas problemas de Gestão de Atualização de Problemas

Este artigo discute questões que poderá encontrar ao implementar a funcionalidade 'Gestão de Actualizações' nas suas máquinas. Há um agente que desarma problemas para o agente híbrido runbook worker para determinar o problema subjacente. Para saber mais sobre o problema, consulte problemas de agente de [atualização do Windows troubleshoot](update-agent-issues.md) e problemas com o agente de [atualização Troubleshoot Linux](update-agent-issues-linux.md). Para outros problemas de implementação de funcionalidades, consulte problemas de implementação de [funcionalidades Troubleshoot](onboarding.md).

>[!NOTE]
>Se tiver problemas ao implementar a Gestão de Atualizações num VM, verifique o registo do **Gestor de Operações** em registos de **aplicações e serviços** na máquina local. Procure eventos com ID 4502 do evento e detalhes do evento que contenham `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` .

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a>Cenário: Recebe o erro "Falhou em ativar a solução Update"

### <a name="issue"></a>Problema

Quando tenta ativar a Gestão de Atualizações na sua conta De automatização, obtém-se o seguinte erro:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Causa

Este erro pode ocorrer pelas seguintes razões:

* Os requisitos de firewall de rede para o agente Log Analytics podem não estar corretamente configurados. Esta situação pode fazer com que o agente falhe na resolução dos URLs DNS.

* O targeting de Gestão de Atualizações está mal configurado e a máquina não está a receber atualizações como esperado.

* Pode também notar que a máquina mostra um estado de `Non-compliant` **conformidade**. Ao mesmo tempo, o **Agente Desktop Analytics** informa o agente como `Disconnected` .

### <a name="resolution"></a>Resolução

* Execute o resolução de problemas para [Windows](update-agent-issues.md#troubleshoot-offline) ou [Linux](update-agent-issues-linux.md#troubleshoot-offline), dependendo do SISTEMA.

* Vá à [configuração da Rede](../automation-hybrid-runbook-worker.md#network-planning) para saber quais os endereços e portas que devem ser autorizados a atualizar a Gestão.  

* Aceda à [configuração da Rede](../../azure-monitor/platform/log-analytics-agent.md#network-requirements) para saber quais os endereços e portas que devem ser autorizados a trabalhar para o agente Log Analytics.

* Verifique se há problemas de configuração de âmbito. [A configuração do âmbito](../automation-onboard-solutions-from-automation-account.md#scope-configuration) determina quais as máquinas configuradas para gestão de atualizações. Se a sua máquina estiver a aparecer no seu espaço de trabalho, mas não no portal de Gestão de Atualizações, tem de definir a configuração de âmbito para direcionar as máquinas. Para saber mais sobre a configuração do âmbito, consulte [Máquinas Ativar no espaço](../automation-onboard-solutions-from-automation-account.md#enable-machines-in-the-workspace)de trabalho .

* Retire a configuração do trabalhador seguindo os passos em Remover o Trabalhador do [Livro Híbrido de um computador Windows no local](../automation-windows-hrw-install.md#remove-windows-hybrid-runbook-worker) ou remova o Trabalhador do Livro Híbrido de um computador [Linux no local](../automation-linux-hrw-install.md#remove-linux-hybrid-runbook-worker). 

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Cenário: Atualização supersedindicada indicada como desaparecida na Gestão de Atualizações

### <a name="issue"></a>Problema

Atualizações antigas estão a aparecer para uma conta de Automação como desaparecida, apesar de terem sido substituídos. Uma atualização supersed é uma que não precisa de instalar porque uma atualização posterior que corrige a mesma vulnerabilidade está disponível. A Atualização Management ignora a atualização sobresedada e torna-a não aplicável a favor da atualização de superseding. Para obter informações sobre um problema relacionado, consulte [a Atualização é substituído](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer).

### <a name="cause"></a>Causa

As atualizações supersed não são corretamente indicadas como recusadas para que possam ser consideradas não aplicáveis.

### <a name="resolution"></a>Resolução

Quando uma atualização supersed se torna 100% não aplicável, deve alterar o estado de aprovação dessa atualização para `Declined` . Para alterar o estado de aprovação para todas as suas atualizações:

1. Na conta Automation, selecione **Update Management** para ver o estado da máquina. Ver [Ver avaliações](../manage-update-multi.md#view-an-update-assessment)de atualização .

2. Verifique a atualização supersed para se certificar de que não é aplicável a 100%. 

3. Marque a atualização como recusada, a menos que tenha alguma dúvida sobre a atualização. 

4. Selecione **Computadores** e, na coluna **Compliance,** force uma rescanpara o cumprimento. Ver [Gerir atualizações para várias máquinas](../manage-update-multi.md).

5. Repita os passos acima para outras atualizações supersed.

6. Execute o assistente de limpeza para eliminar ficheiros das atualizações recusadas. 

7. Para os Serviços de Atualização do Servidor do Windows (WSUS), limpe manualmente todas as atualizações supersed para refrescar a infraestrutura.

8. Repita este procedimento regularmente para corrigir o problema do visor e minimizar a quantidade de espaço em disco utilizado para a gestão da atualização.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Cenário: Máquinas não aparecem no portal sob Gestão de Atualizações

### <a name="issue"></a>Problema

As suas máquinas têm os seguintes sintomas:

* A sua máquina mostra a partir da visão de `Not configured` Gestão de Atualização de um VM.

* Faltam as suas máquinas na visão de Gestão de Atualização da sua conta Azure Automation.

* Tens máquinas que mostram como `Not assessed` em **Conformidade.** No entanto, você vê dados de batimentos cardíacos em registos do Monitor Azure para o Trabalhador do Livro Híbrido, mas não para Gestão de Atualizações.

### <a name="cause"></a>Causa

Este problema pode ser causado por problemas de configuração locais ou por configuração de âmbito configurada indevidamente. Possíveis causas específicas são:

* Pode ter de voltar a registar-se e reinstalar o Trabalhador híbrido do livro.

* Pode ter definido uma quota no seu espaço de trabalho que foi atingida e que está a impedir o armazenamento de dados.

### <a name="resolution"></a>Resolução

1. Execute o resolução de problemas para [Windows](update-agent-issues.md#troubleshoot-offline) ou [Linux](update-agent-issues-linux.md#troubleshoot-offline), dependendo do SISTEMA.

2. Certifique-se de que a sua máquina está a reportar ao espaço de trabalho correto. Para obter orientações sobre como verificar este aspeto, consulte verificar a conectividade do [agente para registar o Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Certifique-se também de que este espaço de trabalho está ligado à sua conta Azure Automation. Para confirmar, vá à sua conta de Automação e selecione **Linked workspace** em **Recursos Relacionados**.

3. Certifique-se de que as máquinas aparecem no espaço de trabalho log Analytics ligado à sua conta Deautomação. Faça a seguinte consulta no espaço de trabalho log Analytics.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

4. Se não vir a sua máquina nos resultados da consulta, não fez o check-in recentemente. Há provavelmente um problema de configuração local e deve [reinstalar o agente.](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows) 

5. Se a sua máquina aparecer nos resultados da consulta, verifique se há problemas de configuração de âmbito. A [configuração](../automation-scope-configurations-update-management.md) de âmbito determina quais as máquinas configuradas para gestão de atualizações. 

6. Se a sua máquina estiver a aparecer no seu espaço de trabalho, mas não na Atualização, tem de configurar a configuração de âmbito para direcionar a máquina. Para aprender a fazê-lo, consulte [Máquinas Ativas no espaço de trabalho](../automation-onboard-solutions-from-automation-account.md#enable-machines-in-the-workspace).

7. No seu espaço de trabalho, execute esta consulta.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

8. Se obtém um resultado, a quota definida no seu espaço de `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` trabalho foi atingida, o que impediu que os dados fossem guardados. No seu espaço de trabalho, vá à **gestão** do volume de dados sob **utilização e custos estimados,** e altere ou remova a quota.

9. Se o seu problema ainda não estiver resolvido, siga os passos de [implantação](../automation-windows-hrw-install.md) de um Trabalhador do Livro Híbrido do Windows para reinstalar o Trabalhador Híbrido para Windows. Para linux, siga os passos em [Deploy um Linux Hybrid Runbook Worker](../automation-linux-hrw-install.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Cenário: Incapaz de registar fornecedor de recursos automation para subscrições

### <a name="issue"></a>Problema

Quando trabalha com implementações de funcionalidades na sua conta Automation, ocorre o seguinte erro:

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>Causa

O fornecedor de recursos automation não está registado na subscrição.

### <a name="resolution"></a>Resolução

Para registar o fornecedor de recursos Automation, siga estes passos no portal Azure.

1. Na lista de serviços Azure na parte inferior do portal, selecione **Todos os serviços**e, em seguida, selecione **Subscrições** no grupo de serviço Geral.

2. Selecione a sua subscrição.

3. Em **Definições,** selecione **Fornecedores de Recursos**.

4. A partir da lista de fornecedores de recursos, verifique se o fornecedor de recursos da Microsoft.Automation está registado.

5. Se não estiver listado, registe o fornecedor Microsoft.Automation seguindo os passos no [Resolve erros para o registo](/azure/azure-resource-manager/resource-manager-register-provider-errors)do fornecedor de recursos .

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="scheduled-update-missed-machines"></a>Cenário: Atualização programada com um horário dinâmico falhou algumas máquinas

### <a name="issue"></a>Problema

As máquinas incluídas numa pré-visualização de atualização nem todas aparecem na lista de máquinas remendadas durante uma execução programada.

### <a name="cause"></a>Causa

Esta questão pode ter uma das seguintes causas:

* As subscrições definidas no âmbito de uma consulta dinâmica não estão configuradas para o fornecedor de recursos de Automação registado.

* As máquinas não estavam disponíveis ou não tinham etiquetas apropriadas quando o horário foi executado.

### <a name="resolution"></a>Resolução

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>Assinaturas não configuradas para fornecedor de recursos de Automação registado

Se a sua subscrição não estiver configurada para o fornecedor de recursos Automation, não pode consultar ou recolher informações sobre máquinas nessa subscrição. Utilize os seguintes passos para verificar o registo da subscrição.

1. No [portal Azure,](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal)aceda à lista de serviços Do Azure.

2. Selecione **Todos os serviços**e, em seguida, selecione **Subscrições** no grupo de serviço geral. 

3. Encontre a subscrição definida no âmbito para a sua implementação.

4. Em **Definições,** escolha **Fornecedores de Recursos**.

5. Verifique se o fornecedor de recursos da Microsoft.Automation está registado.

6. Se não estiver listado, registe o fornecedor Microsoft.Automation seguindo os passos no [Resolve erros para o registo](/azure/azure-resource-manager/resource-manager-register-provider-errors)do fornecedor de recursos .

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>Máquinas não disponíveis ou não etiquetadas corretamente quando o horário executado

Utilize o seguinte procedimento se a sua subscrição estiver configurada para o fornecedor de recursos automation, mas executar o calendário de atualização com os [grupos dinâmicos](../automation-update-management-groups.md) especificados perdeu algumas máquinas.

1. No portal Azure, abra a conta Automation e selecione **Update Management**.

2. Verifique o histórico de Gestão de [Atualizações](https://docs.microsoft.com/azure/automation/manage-update-multi#view-results-of-an-update-deployment) para determinar a hora exata da execução da atualização. 

3. Para máquinas que suspeite ter sido perdidapela Atualização, utilize o Azure Resource Graph (ARG) para [localizar as alterações](https://docs.microsoft.com/azure/governance/resource-graph/how-to/get-resource-changes#find-detected-change-events-and-view-change-details)da máquina . 

4. Procure alterações durante um período considerável, como um dia, antes da implementação da atualização.

5. Verifique os resultados da pesquisa para quaisquer alterações sistémicas, tais como alterações de eliminação ou atualização, nas máquinas neste período. Estas alterações podem alterar o estado da máquina ou as etiquetas para que as máquinas não sejam selecionadas na lista de máquinas quando as atualizações forem implementadas.

6. Ajuste as máquinas e as definições de recursos conforme necessário para corrigir o estado da máquina ou problemas de etiqueta.

7. Reexecutar o calendário da atualização para garantir que a implantação com os grupos dinâmicos especificados inclua todas as máquinas.

## <a name="scenario-expected-machines-dont-appear-in-preview-for-dynamic-group"></a><a name="machines-not-in-preview"></a>Cenário: Máquinas esperadas não aparecem na pré-visualização do grupo dinâmico

### <a name="issue"></a>Problema

VMs para âmbitos selecionados de um grupo dinâmico não estão a aparecer na lista de pré-visualização do portal Azure. Esta lista é constituída por todas as máquinas recuperadas por uma consulta arg para os âmbitos selecionados. Os âmbitos são filtrados para máquinas que tenham Trabalhadores híbridos de runbook instalados e para os quais você tem permissões de acesso. 

### <a name="cause"></a>Causa
 
Aqui estão possíveis causas para esta questão:

* Não tem o acesso correto nos âmbitos selecionados.
* A consulta do ARG não recupera as máquinas esperadas.
* O Trabalhador híbrido do livro não está instalado nas máquinas.

### <a name="resolution"></a>Resolução 

#### <a name="incorrect-access-on-selected-scopes"></a>Acesso incorreto nos âmbitos selecionados

O portal Azure apenas exibe máquinas para as quais tem acesso por escrito num dado âmbito. Se não tiver o acesso correto para um âmbito, consulte [Tutorial: Conceda ao utilizador o acesso aos recursos Azure utilizando o RBAC e o portal Azure](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

#### <a name="arg-query-doesnt-return-expected-machines"></a>A consulta do ARG não devolve as máquinas esperadas

Siga os passos abaixo para saber se as suas consultas estão a funcionar corretamente.

1. Execute uma consulta ARG formatada como mostrado abaixo na lâmina exploradora do Graph de Recursos no portal Azure. Esta consulta imita os filtros selecionados quando criou o grupo dinâmico em Gestão de Atualizações. Ver [Utilizar grupos dinâmicos com Gestão](https://docs.microsoft.com/azure/automation/automation-update-management-groups)de Atualizações . 

    ```kusto
    where (subscriptionId in~ ("<subscriptionId1>", "<subscriptionId2>") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "<Windows/Linux>" and resourceGroup in~ ("<resourceGroupName1>","<resourceGroupName2>") and location in~ ("<location1>","<location2>") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" and tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "All" option selected for tags
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" or tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "Any" option selected for tags
    | project id, location, name, tags
    ```

   Segue-se um exemplo:

    ```kusto
    where (subscriptionId in~ ("20780d0a-b422-4213-979b-6c919c91ace1", "af52d412-a347-4bc6-8cb7-4780fbb00490") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "Windows" and resourceGroup in~ ("testRG","withinvnet-2020-01-06-10-global-resources-southindia") and location in~ ("australiacentral","australiacentral2","brazilsouth") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("ms-resource-usage")] =~ "azure-cloud-shell" and tags[tolower("temp")] =~ "temp")
    | project id, location, name, tags
    ```
 
2. Verifique se as máquinas que procura estão listadas nos resultados da consulta. 

3. Se as máquinas não estiverem listadas, há provavelmente um problema com o filtro selecionado no grupo dinâmico. Ajuste a configuração do grupo conforme necessário.

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>Trabalhador de livro híbrido não instalado em máquinas

As máquinas aparecem nos resultados da consulta ARG, mas ainda não aparecem na pré-visualização dinâmica do grupo. Neste caso, as máquinas podem não ser designadas como trabalhadores híbridos e, portanto, não podem executar trabalhos de Automação e Gestão de Atualizações. Para garantir que as máquinas que espera ver são configuradas como Trabalhadores híbridos do livro:

1. No portal Azure, vá à conta da Automação para uma máquina que não apareça corretamente.

2. Selecione **grupos de trabalhadores híbridos** no âmbito **da Automatização de Processos.**

3. Selecione o separador **de grupos de trabalhadores híbridos system.**

4. Valide que o trabalhador híbrido está presente para a máquina.

5. Se a máquina não estiver configurada como um trabalhador híbrido, faça ajustes utilizando instruções [nos recursos automate no seu datacenter ou nuvem utilizando](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)o Hybrid Runbook Worker .

6. Junte-se à máquina ao grupo Híbrido Runbook Worker.

7. Repita os passos acima para todas as máquinas que não tenham sido exibidas na pré-visualização.

## <a name="scenario-update-management-components-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Cenário: Componentes de Gestão de Atualização ativados, enquanto vM continua a mostrar como sendo configurado

### <a name="issue"></a>Problema

Continua a ver a seguinte mensagem num VM 15 minutos após o início da implantação:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Causa

Este erro pode ocorrer pelas seguintes razões:

* A comunicação com a conta Automation está a ser bloqueada.

* Há um nome de computador duplicado com diferentes iDs de computador de origem. Este cenário ocorre quando um VM com um nome de computador particular é criado em diferentes grupos de recursos e está reportando ao mesmo espaço de trabalho do Agente Logístico na subscrição.

* A imagem VM que está a ser implementada pode vir de uma máquina clonada que não foi preparada com a Preparação do Sistema (sysprep) com o agente Log Analytics para windows instalado.

### <a name="resolution"></a>Resolução

Para ajudar a determinar o problema exato com o VM, faça a seguinte consulta no espaço de trabalho log Analytics que está ligado à sua conta Deautomação.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Conta de comunicação com automação bloqueada

Vá à [Rede planejando](../automation-update-management.md#ports) saber quais endereços e portas devem ser autorizados a atualizar gestão para funcionar.

#### <a name="duplicate-computer-name"></a>Duplicar o nome do computador

Mude o nome dos seus VMs para garantir nomes únicos no seu ambiente.

#### <a name="deployed-image-from-cloned-machine"></a>Imagem implantada da máquina clonada

Se estiver a usar uma imagem clonada, diferentes nomes de computador têm o mesmo ID de computador de origem. Neste caso:

1. No seu espaço de trabalho Log Analytics, retire o VM da pesquisa guardada para a configuração de `MicrosoftDefaultScopeConfig-Updates` âmbito se for mostrado. Pesquisas guardadas podem ser encontradas sob **o General** no seu espaço de trabalho.

2. Executar o seguinte cmdlet.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Corra para reiniciar o serviço de `Restart-Service HealthService` saúde. Esta operação recria a chave e gera um novo UUID.

4. Se esta abordagem não funcionar, faça a sysprep na imagem primeiro e, em seguida, instale o MMA.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Cenário: Recebe um erro de subscrição ligado quando cria uma implementação de atualização para máquinas noutro inquilino do Azure

### <a name="issue"></a>Problema

Encontra-se o seguinte erro quando tenta criar uma implementação de atualização para máquinas noutro inquilino azure:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Causa

Este erro ocorre quando cria uma implementação de atualização que tem VMs Azure em outro inquilino que está incluído numa implementação de atualização.

### <a name="resolution"></a>Resolução

Utilize a seguinte suver para agendar estes itens. Pode utilizar o cmdlet [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationschedule?view=azps-3.7.0) com o `ForUpdateConfiguration` parâmetro para criar um horário. Em seguida, utilize o [cmdlet New-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) e passe as máquinas do outro inquilino para o `NonAzureComputer` parâmetro. O exemplo que se segue mostra como fazê-lo:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Cenário: Reboots inexplicáveis

### <a name="issue"></a>Problema

Apesar de ter definido a opção **De controlo de reiniciar** para Never **Reboot,** as máquinas continuam a reiniciar após a instalação das atualizações.

### <a name="cause"></a>Causa

O Windows Update pode ser modificado por várias teclas de registo, qualquer uma das quais pode modificar o comportamento de reboot.

### <a name="resolution"></a>Resolução

Reveja as teclas de registo listadas em [Configurar Atualizações Automáticas, editando as](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) teclas de registo e [registo utilizadas para gerir](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) o reinício para se certificar de que as suas máquinas estão corretamente configuradas.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Cenário: Máquina mostra "Falhou no arranque" numa implementação de atualização

### <a name="issue"></a>Problema

Uma máquina mostra um `Failed to start` estado. Quando visualiza os detalhes específicos da máquina, vê o seguinte erro:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Causa

Este erro pode ocorrer por um dos seguintes motivos:

* A máquina já não existe.
* A máquina está desligada e inacessível.
* A máquina tem um problema de conectividade de rede, pelo que o trabalhador híbrido na máquina é inacessível.
* Houve uma atualização para o MMA que alterou o ID do computador de origem.
* A sua atualização foi acelerada se atingir o limite de 2000 postos de trabalho simultâneos numa conta de Automação. Cada implantação é considerada um trabalho, e cada máquina numa atualização conta como um trabalho. Qualquer outro trabalho de automação ou implementação de atualização atualmente em execução na sua conta Automation conta para o limite de trabalho simultâneo.

### <a name="resolution"></a>Resolução

Quando aplicável, utilize [grupos dinâmicos](../automation-update-management-groups.md) para as suas implementações de atualização. Além disso, pode tomar os seguintes passos.

1. Verifique se a máquina ainda existe e está acessível. 
2. Se a máquina não existir, edite a sua implantação e retire a máquina.
3. Consulte a secção de planeamento da [rede](../automation-update-management.md#ports) para obter uma lista de portas e endereços que são necessários para gestão de atualizações e, em seguida, verifique se a sua máquina cumpre estes requisitos.
4. Verifique a conectividade com o Trabalhador do Livro híbrido utilizando o agente de trabalho de runbook híbrido. Para saber mais sobre o problema, consulte problemas de agente de [atualização da Troubleshoot](update-agent-issues.md).
5. Faça a seguinte consulta no Log Analytics para encontrar máquinas no seu ambiente para as quais o ID do computador de origem mudou. Procure computadores com o mesmo `Computer` valor, mas com um `SourceComputerId` valor diferente.

   ```kusto
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

6. Depois de encontrar as máquinas afetadas, edite as implementações de atualização que visam essas máquinas e, em seguida, remova e leia-as de modo `SourceComputerId` a refletir o valor correto.

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Cenário: As atualizações são instaladas sem uma implementação

### <a name="issue"></a>Problema

Ao inscrever uma máquina windows na Atualização, vê atualizações instaladas sem uma implementação.

### <a name="cause"></a>Causa

No Windows, as atualizações são instaladas automaticamente assim que estão disponíveis. Este comportamento pode causar confusão se não tiver agendado uma atualização para ser implementada na máquina.

### <a name="resolution"></a>Resolução

A chave do `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` registo não se aplica a uma definição de 4: `auto download and install` .

Para clientes de Gestão de Atualizações, recomendamos que esta chave seja de 3: `auto download but do not auto install` .

Para mais informações, consulte [Configurar Atualizações Automáticas](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Cenário: Máquina já está registada numa conta diferente

### <a name="issue"></a>Problema

Recebe a seguinte mensagem de erro:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Causa

A máquina já foi implantada para outro espaço de trabalho para gestão de atualizações.

### <a name="resolution"></a>Resolução

1. Siga os [passos em Machines não apareça no portal sob Gestão](#nologs) de Atualização para se certificar de que a máquina está reportando ao espaço de trabalho correto.
2. Limpe os artefactos na [máquina, apagando o grupo de livros híbridos](../automation-windows-hrw-install.md#remove-a-hybrid-worker-group)e tente novamente.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Cenário: Máquina não pode comunicar com o serviço

### <a name="issue"></a>Problema

Recebe uma das seguintes mensagens de erro:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>Causa

Um proxy, gateway ou firewall pode estar bloqueando a comunicação da rede. 

### <a name="resolution"></a>Resolução

Reveja a sua rede e certifique-se de que as portas e endereços apropriados são permitidos. Consulte [os requisitos](../automation-hybrid-runbook-worker.md#network-planning) de rede para uma lista de portas e endereços que são exigidos pela Update Management e pelos Trabalhadores híbridos do livro de corridas.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Cenário: Incapaz de criar certificado auto-assinado

### <a name="issue"></a>Problema

Recebe uma das seguintes mensagens de erro:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Causa

O Trabalhador híbrido do livro não podia gerar um certificado auto-assinado.

### <a name="resolution"></a>Resolução

Verifique se a conta do sistema leu o acesso à pasta **C:\ProgramData\Microsoft\Crypto\RSA** e tente novamente.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Cenário: A atualização programada falhou com um erro de ManutençãoWindowExceeded

### <a name="issue"></a>Problema

A janela de manutenção predefinida para atualizações é de 120 minutos. Pode aumentar a janela de manutenção para um máximo de 6 horas, ou 360 minutos.

### <a name="resolution"></a>Resolução

Editar quaisquer implementações de atualizações programadas falhadas e aumentar a janela de manutenção.

Para obter mais informações sobre as janelas de manutenção, consulte [Instalar atualizações](../automation-tutorial-update-management.md#schedule-an-update-deployment).

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Cenário: Máquina mostra como "Não avaliada" e mostra uma exceção ao HRESULT

### <a name="issue"></a>Problema

* Tem máquinas que mostram como `Not assessed` em **Conformidade**, e vê uma mensagem de exceção abaixo delas.
* Vê um código de erro HRESULT no portal.

### <a name="cause"></a>Causa

O Agente de Atualização (Windows Update Agent on Windows; o gestor de pacotes para uma distribuição Linux) não está configurado corretamente. A Atualização Management conta com o Agente de Atualização da máquina para fornecer as atualizações que são necessárias, o estado do patch e os resultados dos patches implantados. Sem esta informação, a Atualização não pode reportar adequadamente sobre os patches que são necessários ou instalados.

### <a name="resolution"></a>Resolução

Tente efetuar atualizações localmente na máquina. Se esta operação falhar, normalmente significa que existe um erro de configuração do agente de atualização.

Este problema é frequentemente causado por problemas de configuração de rede e firewall. Utilize os seguintes controlos para corrigir o problema.

* Para o Linux, consulte a documentação adequada para se certificar de que pode chegar ao ponto final da rede do seu repositório de pacotes.

* Para windows, verifique a configuração do seu agente tal como listado nas [Atualizações não está a ser descarregada a partir do ponto final da intranet (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).

  * Se as máquinas estiverem configuradas para a Atualização do Windows, certifique-se de que pode chegar aos pontos finais descritos em [Problemas relacionados com HTTP/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Se as máquinas estiverem configuradas para os Serviços de Atualização do Servidor do Windows (WSUS), certifique-se de que pode chegar ao servidor WSUS configurado pela chave de [registo WUServer](/windows/deployment/update/waas-wu-settings).

Se vir um HRESULT, clique duas vezes na exceção exibida a vermelho para ver toda a mensagem de exceção. Reveja o quadro seguinte para potenciais resoluções ou ações recomendadas.

|Exceção  |Resolução ou ação  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Procure o código de erro relevante na lista de códigos de erro da [atualização do Windows](https://support.microsoft.com/help/938205/windows-update-error-code-list) para encontrar detalhes adicionais sobre a causa da exceção.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Estes indicam problemas de conectividade da rede. Certifique-se de que a sua máquina tem conectividade de rede com a Atualização de Gestão. Consulte a secção de planeamento da [rede](../automation-update-management.md#ports) para obter uma lista de portas e endereços necessários.        |
|`0x8024001E`| A operação de atualização não terminou porque o serviço ou o sistema estavam a desligar-se.|
|`0x8024002E`| O serviço De atualização do Windows está desativado.|
|`0x8024402C`     | Se estiver a utilizar um servidor WSUS, certifique-se de que os valores de registo para `WUServer` e sob a chave de registo `WUStatusServer` `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` especificam o servidor WSUS correto.        |
|`0x80072EE2`|Há um problema de conectividade de rede ou um problema em falar com um servidor WSUS configurado. Verifique as definições da WSUS e certifique-se de que o serviço está acessível a partir do cliente.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Certifique-se de que o serviço De atualização do Windows (wuauserv) está em funcionamento e não está desativado.        |
|`0x80070005`| Um erro negado de acesso pode ser causado por qualquer um dos seguintes erros:<br> Computador infetado<br> Definições de Atualização do Windows não configuradas corretamente<br> Erro de permissão de ficheiro com a pasta %WinDir%\SoftwareDistribution<br> Espaço de disco insuficiente na unidade do sistema (C:).
|Qualquer outra exceção genérica     | Procure na internet possíveis resoluções e trabalhe com o seu suporte de TI local.         |

Rever o ficheiro **%Windir%\Windowsupdate.log** também pode ajudá-lo a determinar possíveis causas. Para mais informações sobre como ler o registo, consulte [Como ler o ficheiro Windowsupdate.log](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file).

Também pode descarregar e executar o resoluto de [problemas do Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) para verificar quaisquer problemas com o Windows Update na máquina.

> [!NOTE]
> A documentação de resolução de [problemas do Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) indica que é para utilização em clientes windows, mas também funciona no Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Cenário: Atualização retorna estado falhado (Linux)

### <a name="issue"></a>Problema

Uma atualização começa, mas encontra erros durante a corrida.

### <a name="cause"></a>Causa

Causas possíveis:

* O gestor do pacote não é saudável.
* O Agente de Atualização (WUA para Windows, gestor de pacotes específico para o Linux) está mal configurado.
* Pacotes específicos estão interferindo com remendos baseados em nuvem.
* A máquina é inalcançável.
* As atualizações tinham dependências que não estavam resolvidas.

### <a name="resolution"></a>Resolução

Se ocorrerem falhas durante uma atualização após o início com sucesso, [verifique a saída](../manage-update-multi.md#view-results-of-an-update-deployment) de trabalho da máquina afetada durante a execução. Pode encontrar mensagens de erro específicas das suas máquinas que pode pesquisar e tomar medidas. A Update Management exige que o gestor do pacote seja saudável para implementações de atualização bem sucedidas.

Se patches, pacotes ou atualizações específicos forem vistos imediatamente antes de o trabalho falhar, pode tentar [excluir](../automation-tutorial-update-management.md#schedule-an-update-deployment) estes itens da próxima implementação da atualização. Para recolher informações de registo a partir do Windows Update, consulte [os ficheiros](/windows/deployment/update/windows-update-logs)de registo do Windows Update .

Se não conseguir resolver um problema de correção, faça uma cópia do ficheiro **/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log** e preserve-o para fins de resolução de problemas antes do início da próxima implementação da atualização.

## <a name="patches-arent-installed"></a>Patches não são instalados

### <a name="machines-dont-install-updates"></a>As máquinas não instalam atualizações

Experimente executar as atualizações diretamente na máquina. Se a máquina não puder aplicar as atualizações, consulte a [lista de potenciais erros no guia de resolução de problemas](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).

Se as atualizações forem executadas localmente, tente remover e reinstalar o agente na máquina seguindo a orientação em [Remover um VM da Atualização](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources)de Gestão .

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Sei que as atualizações estão disponíveis, mas não aparecem como disponíveis nas minhas máquinas.

Isto acontece frequentemente se as máquinas estiverem configuradas para obter atualizações do WSUS ou do Microsoft Endpoint Configuration Manager, mas o WSUS e o Gestor de Configuração não aprovaram as atualizações.

Pode verificar se as máquinas estão configuradas para WSUS e SCCM, cruzando a chave de registo das teclas de `UseWUServer` registo nas [Atualizações Automáticas de Configuração, editando a secção de registo deste artigo](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).

Se as atualizações não forem aprovadas no WSUS, não estão instaladas. Pode verificar se há atualizações não aprovadas no Log Analytics executando a seguinte consulta.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>As atualizações aparecem como tendo sido instaladas, mas não as encontro na minha máquina

Muitas vezes, as atualizações são substituídas por outras. Para mais informações, consulte a Atualização no guia de resolução de [problemas](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) do Windows Update.

### <a name="installing-updates-by-classification-on-linux"></a>Instalar atualizações por classificação no Linux

A implementação de atualizações no Linux por classificação ("atualizações críticas e de segurança") tem limitações importantes, sobretudo para o CentOS. Estas limitações estão documentadas na página de visão geral da Gestão de [Atualizações.](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2)

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 está sempre ausente

KB2267602 é a [atualização de definições do Windows Defender](https://www.microsoft.com/wdsi/definitions). É atualizado diariamente.

## <a name="next-steps"></a>Passos seguintes

Se não vir o seu problema ou não conseguir resolver o seu problema, experimente um dos seguintes canais para obter apoio adicional.

* Obtenha respostas de especialistas do Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) a conta oficial do Microsoft Azure para melhorar a experiência do cliente.
* Arquiva um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
