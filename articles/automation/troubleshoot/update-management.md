---
title: Problemas com a Azure Automation Update Management
description: Este artigo diz como resolver problemas e resolver problemas com a Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 01/13/2021
ms.topic: troubleshooting
ms.openlocfilehash: c16b032502401b633532ab0fcf9518aa85a1b8d6
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579737"
---
# <a name="troubleshoot-update-management-issues"></a>Resolver problemas da Gestão de Atualizações

Este artigo discute questões que poderá encontrar ao implementar a funcionalidade de Gestão de Atualização nas suas máquinas. Há um agente que resolvia problemas para o agente híbrido runbook para determinar o problema subjacente. Para saber mais sobre o resolução de [problemas, consulte problemas de agente de atualização do Windows](update-agent-issues.md) de resolução de problemas e [problemas de agente de atualização do Linux](update-agent-issues-linux.md)de resolução de problemas . Para outros problemas de implementação de funcionalidades, consulte [problemas de implementação da funcionalidade De resolução de problemas](onboarding.md).

>[!NOTE]
>Se tiver problemas ao implementar a Gestão de Atualização numa máquina Windows, abra o Visualizador de Eventos do Windows e verifique o registo de eventos **do Gestor de Operações** nos **Registos de Aplicações e Serviços** da máquina local. Procure eventos com iD 4502 de eventos e detalhes do evento que `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` contenham.

## <a name="scenario-linux-updates-shown-as-pending-and-those-installed-vary"></a><a name="updates-linux-installed-different"></a>Cenário: As atualizações do Linux apresentadas como pendentes e as instaladas variam

### <a name="issue"></a>Problema

Para a sua máquina Linux, a Update Management mostra atualizações específicas disponíveis em **segurança** de classificação e **outras**. Mas quando um calendário de atualização é executado na máquina, por exemplo para instalar apenas atualizações correspondentes à classificação **de Segurança,** as atualizações instaladas são diferentes ou um subconjunto das atualizações mostradas anteriormente correspondentes a essa classificação.

### <a name="cause"></a>Causa

Quando uma avaliação das atualizações do SO pendentes para a sua máquina Linux é feita, os ficheiros [Open Vulnerability and Assessment Language](https://oval.mitre.org/) (OVAL) fornecidos pelo fornecedor de distro Linux são utilizados pela Update Management para classificação. A categorização é feita para atualizações do Linux como **Segurança** ou **Outras**, com base nos ficheiros OVAis que declaram atualizações que abordam problemas de segurança ou vulnerabilidades. Mas quando o calendário de atualização é executado, executa na máquina Linux usando o gestor de pacotes apropriado como YUM, APT ou ZYPPER para instalá-los. O gestor de pacotes para o distro Linux pode ter um mecanismo diferente para classificar as atualizações, onde os resultados podem diferir dos obtidos a partir dos ficheiros OVAL pela Update Management.

### <a name="resolution"></a>Resolução

Pode verificar manualmente a máquina Linux, as atualizações aplicáveis e a sua classificação pelo gestor de pacotes do distro. Para entender quais as atualizações que são classificadas como **Segurança** pelo seu gestor de pacotes, execute os seguintes comandos.

Para a YUM, o seguinte comando devolve uma lista não zero de atualizações categorizadas como **Security** by Red Hat. Note que no caso do CentOS, devolve sempre uma lista vazia e não ocorre nenhuma classificação de segurança.

```bash
sudo yum -q --security check-update
```

Para a ZYPPER, o seguinte comando devolve uma lista não-zero de atualizações categorizadas como **Security** by SUSE.

```bash
sudo LANG=en_US.UTF8 zypper --non-interactive patch --category security --dry-run
```

Para a APT, o seguinte comando retorna uma lista não zero de atualizações categorizadas como **Security** by Canonical for Ubuntu Linux distros.

```bash
sudo grep security /etc/apt/sources.list > /tmp/oms-update-security.list LANG=en_US.UTF8 sudo apt-get -s dist-upgrade -oDir::Etc::Sourcelist=/tmp/oms-update-security.list
```

A partir desta lista, você então executar o comando `grep ^Inst` para obter todas as atualizações de segurança pendentes.

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a><a name="failed-to-enable-error"></a>Cenário: Recebe o erro "Falhou em ativar a solução 'Atualização'

### <a name="issue"></a>Problema

Quando tenta ativar a Gestão de Atualização na sua conta de Automação, obtém o seguinte erro:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Causa

Este erro pode ocorrer pelas seguintes razões:

* Os requisitos de firewall de rede para o agente Log Analytics podem não estar configurados corretamente. Esta situação pode fazer com que o agente falhe na resolução dos URLs DNS.

* O alvo de Atualização de Gestão está mal configurado e a máquina não está a receber atualizações como esperado.

* Pode também notar que a máquina apresenta um estado de `Non-compliant` **conformidade**. Ao mesmo tempo, o **Agente Desktop Analytics** informa o agente como `Disconnected` .

### <a name="resolution"></a>Resolução

* Execute o resolução de problemas para [Windows](update-agent-issues.md#troubleshoot-offline) ou [Linux,](update-agent-issues-linux.md#troubleshoot-offline)dependendo do SISTEMA.

* Aceda à [configuração da Rede](../automation-hybrid-runbook-worker.md#network-planning) para saber quais endereços e portas devem ser autorizados para que a Gestão de Atualização funcione.  

* Verifique se há problemas de configuração de âmbito. [A configuração](../update-management/scope-configuration.md) do âmbito determina quais as máquinas configuradas para a Gestão de Atualização. Se a sua máquina estiver a aparecer no seu espaço de trabalho, mas não na Gestão de Atualização, tem de definir a configuração de âmbito para direcionar as máquinas. Para saber mais sobre a configuração do âmbito, consulte [Ativar as máquinas no espaço de trabalho.](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace)

* Remova a configuração do trabalhador seguindo os passos no [Remove the Hybrid Runbook Worker de um computador Windows no local](../automation-windows-hrw-install.md#remove-windows-hybrid-runbook-worker) ou [remova o Trabalhador de Runbook Híbrido de um computador Linux no local](../automation-linux-hrw-install.md#remove-linux-hybrid-runbook-worker).

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Cenário: Atualização supersedada indicada como desaparecida na Gestão de Atualização

### <a name="issue"></a>Problema

As atualizações antigas estão a aparecer para uma conta de Automação como desaparecida, apesar de terem sido substituídos. Uma atualização supereduada é uma que não precisa de instalar porque uma atualização posterior que corrige a mesma vulnerabilidade está disponível. A Atualização A Gestão ignora a atualização superediou e torna-a aplicável a favor da atualização de substituição. Para obter informações sobre um problema relacionado, consulte [Update is supersed .](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)

### <a name="cause"></a>Causa

As atualizações superseded não são recusadas nos Serviços de Atualização do Windows Server (WSUS) para que possam ser consideradas não aplicáveis.

### <a name="resolution"></a>Resolução

Quando uma atualização supereduada se tornar 100% não aplicável, deverá alterar o estado de aprovação dessa atualização para `Declined` a WSUS. Para alterar o estado de aprovação de todas as suas atualizações:

1. Na conta Automation, selecione **Update Management** para visualizar o estado da máquina. Consulte [avaliações de atualização de ver.](../update-management/view-update-assessments.md)

2. Verifique a atualização superedificada para se certificar de que não é 100% aplicável.

3. No servidor WSUS as máquinas reportam a [recusar a atualização](/windows-server/administration/windows-server-update-services/manage/updates-operations#declining-updates).

4. Selecione **Computadores** e, na coluna **Compliance,** force um rescan para o cumprimento. Ver [Gerir atualizações para VMs](../update-management/manage-updates-for-vm.md).

5. Repita os passos acima para outras atualizações superadas.

6. Para serviços de atualização do Servidor do Windows (WSUS), limpe todas as atualizações supersed para refrescar a infraestrutura utilizando o Assistente de limpeza do [Servidor](/windows-server/administration/windows-server-update-services/manage/the-server-cleanup-wizard)WSUS .

7. Repita este procedimento regularmente para corrigir o problema do visor e minimizar a quantidade de espaço em disco utilizado para a gestão da atualização.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Cenário: Máquinas não aparecem no portal sob Gestão de Atualização

### <a name="issue"></a>Problema

As suas máquinas têm os seguintes sintomas:

* A sua máquina mostra `Not configured` a partir da vista de Gestão de Atualização de um VM.

* Faltam as suas máquinas na visão de Gestão de Atualização da sua conta Azure Automation.

* Tem máquinas que mostram como `Not assessed` conformidade.  No entanto, você vê dados de batimentos cardíacos em registos do Azure Monitor para o Trabalhador de Runbook Híbrido, mas não para a Gestão de Atualização.

### <a name="cause"></a>Causa

Este problema pode ser causado por problemas de configuração locais ou por configuração de âmbito configurada indevidamente. Possíveis causas específicas são:

* Poderá ter de voltar a registar-se e reinstalar o Trabalhador De Runbook Híbrido.

* Pode ter definido uma quota no seu espaço de trabalho que foi alcançada e isso está a impedir o armazenamento de mais dados.

### <a name="resolution"></a>Resolução

1. Execute o resolução de problemas para [Windows](update-agent-issues.md#troubleshoot-offline) ou [Linux,](update-agent-issues-linux.md#troubleshoot-offline)dependendo do SISTEMA.

2. Certifique-se de que a sua máquina está a reportar para o espaço de trabalho correto. Para obter orientações sobre como verificar este aspeto, consulte [verificar a conectividade do agente com o Azure Monitor](../../azure-monitor/agents/agent-windows.md#verify-agent-connectivity-to-azure-monitor). Certifique-se também de que este espaço de trabalho está ligado à sua conta Azure Automation. Para confirmar, vá à sua conta de Automação e selecione **espaço de trabalho linked** under Related **Resources**.

3. Certifique-se de que as máquinas aparecem no espaço de trabalho Do Log Analytics ligado à sua conta Automation. Executar a seguinte consulta no espaço de trabalho Log Analytics.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

    Se não vir a sua máquina nos resultados da consulta, não fez o check-in recentemente. Há provavelmente um problema de configuração local e deve [reinstalar o agente.](../../azure-monitor/vm/quick-collect-windows-computer.md#install-the-agent-for-windows)

    Se a sua máquina estiver listada nos resultados da consulta, verifique na propriedade **Solutions** que as atualizações estão **listadas.** Isto verifica que está registado na Gestão de Atualização. Se não for, verifique se existem problemas de configuração de âmbito. A [configuração](../update-management/scope-configuration.md) de âmbito determina quais as máquinas configuradas para a Gestão de Atualização. Para configurar a configuração de âmbito para o alvo da máquina, consulte [Ativar as máquinas no espaço de trabalho](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace).

4. No seu espaço de trabalho, faça esta consulta.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

   Se obtém um `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` resultado, a quota definida no seu espaço de trabalho foi alcançada, o que impediu que os dados fossem guardados. No seu espaço de trabalho, vá à gestão do **volume de dados** ao abrigo **do Uso e custos estimados,** e altere ou remova a quota.

5. Se o seu problema ainda não estiver resolvido, siga os passos no [Deploy a Windows Hybrid Runbook Worker](../automation-windows-hrw-install.md) para reinstalar o Trabalhador Híbrido para o Windows. Para o Linux, siga os passos em [Implementar um Trabalhador de Runbook Híbrido Linux.](../automation-linux-hrw-install.md)

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Cenário: Não conseguir registar fornecedor de recursos automation para subscrições

### <a name="issue"></a>Problema

Quando trabalha com implementações de funcionalidades na sua conta Demôm automação, ocorre o seguinte erro:

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>Causa

O fornecedor de recursos Automation não está registado na subscrição.

### <a name="resolution"></a>Resolução

Para registar o fornecedor de recursos Automation, siga estes passos no portal Azure.

1. Na lista de serviços Azure na parte inferior do portal, selecione **Todos os serviços** e, em seguida, selecione **Subscrições** no grupo de serviços Geral.

2. Selecione a sua subscrição.

3. Em **Definições**, selecione **Fornecedores de Recursos**.

4. A partir da lista de fornecedores de recursos, verifique se o fornecedor de recursos Microsoft.Automation está registado.

5. Se não estiver listado, registe o fornecedor Microsoft.Automation seguindo os passos na [Resolve erros para o registo do fornecedor de recursos](../../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="scheduled-update-missed-machines"></a>Cenário: Atualização programada com um calendário dinâmico perdeu algumas máquinas

### <a name="issue"></a>Problema

As máquinas incluídas numa pré-visualização de atualização nem todas aparecem na lista de máquinas corrigidas durante uma execução programada.

### <a name="cause"></a>Causa

Esta questão pode ter uma das seguintes causas:

* As subscrições definidas no âmbito de uma consulta dinâmica não estão configuradas para o fornecedor de recursos de Automação registado.

* As máquinas não estavam disponíveis ou não tinham etiquetas apropriadas quando o horário foi executado.

### <a name="resolution"></a>Resolução

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>Assinaturas não configuradas para fornecedor de recursos de Automação registado

Se a sua subscrição não estiver configurada para o fornecedor de recursos Automation, não pode consultar ou obter informações sobre máquinas nessa subscrição. Utilize as seguintes etapas para verificar o registo da subscrição.

1. No [portal Azure,](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)aceda à lista de serviços Azure.

2. Selecione **Todos os serviços** e, em seguida, selecione **Subscrições** no grupo de serviço Geral.

3. Encontre a subscrição definida no âmbito da sua implementação.

4. Em **Definições**, escolha **Fornecedores de Recursos**.

5. Verifique se o fornecedor de recursos Microsoft.Automation está registado.

6. Se não estiver listado, registe o fornecedor Microsoft.Automation seguindo os passos na [Resolve erros para o registo do fornecedor de recursos](../../azure-resource-manager/templates/error-register-resource-provider.md).

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>Máquinas não disponíveis ou não marcadas corretamente quando o horário executado

Utilize o seguinte procedimento se a sua subscrição estiver configurada para o fornecedor de recursos Automation, mas executar o crono de atualização com os [grupos dinâmicos especificados](../update-management/configure-groups.md) perdeu algumas máquinas.

1. No portal Azure, abra a conta Automation e selecione **Gestão de Atualização.**

2. Verifique o [histórico de Gestão de Atualização](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) para determinar a hora exata em que a implementação da atualização foi executada.

3. Para máquinas que suspeite ter sido perdidas pela Update Management, utilize o Azure Resource Graph (ARG) para [localizar as alterações da máquina](../../governance/resource-graph/how-to/get-resource-changes.md#find-detected-change-events-and-view-change-details).

4. Procure alterações durante um período considerável, como um dia, antes da implementação da atualização ser executada.

5. Verifique os resultados da pesquisa de quaisquer alterações sistémicas, tais como a exclusão ou a atualização de alterações, às máquinas neste período. Estas alterações podem alterar o estado da máquina ou as etiquetas de modo a que as máquinas não sejam selecionadas na lista de máquinas quando as atualizações forem implementadas.

6. Ajuste as configurações das máquinas e dos recursos conforme necessário para corrigir o estado da máquina ou problemas de identificação.

7. Repercuta o calendário de atualização para garantir que a implementação com os grupos dinâmicos especificados inclui todas as máquinas.

## <a name="scenario-expected-machines-dont-appear-in-preview-for-dynamic-group"></a><a name="machines-not-in-preview"></a>Cenário: Máquinas esperadas não aparecem na pré-estreia do grupo dinâmico

### <a name="issue"></a>Problema

Os VMs para os âmbitos selecionados de um grupo dinâmico não aparecem na lista de pré-visualização do portal Azure. Esta lista consiste em todas as máquinas recuperadas por uma consulta ARG para os âmbitos selecionados. Os âmbitos são filtrados para máquinas que tenham Trabalhadores De Runbook Híbridos instalados e para os quais tenha permissões de acesso.

### <a name="cause"></a>Causa

Aqui estão as possíveis causas para esta questão:

* Não tem o acesso correto nos âmbitos selecionados.
* A consulta da ARG não recupera as máquinas esperadas.
* O Trabalhador híbrido runbook não está instalado nas máquinas.

### <a name="resolution"></a>Resolução 

#### <a name="incorrect-access-on-selected-scopes"></a>Acesso incorreto em âmbitos selecionados

O portal Azure apenas exibe máquinas para as quais tenha acesso de escrita num determinado âmbito. Se não tiver o acesso correto para um âmbito, consulte [Tutorial: Conceder a um utilizador acesso aos recursos do Azure utilizando o portal Azure](../../role-based-access-control/quickstart-assign-role-user-portal.md).

#### <a name="arg-query-doesnt-return-expected-machines"></a>Consulta da ARG não devolve máquinas esperadas

Siga os passos abaixo para saber se as suas consultas estão a funcionar corretamente.

1. Executar uma consulta ARG formatada como mostrado abaixo na lâmina do explorador de gráfico de recurso no portal Azure. Esta consulta imita os filtros selecionados quando criou o grupo dinâmico em Gestão de Atualização. Consulte [grupos dinâmicos de utilização com Gestão de Atualização](../update-management/configure-groups.md).

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

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>Trabalhador de runbook híbrido não instalado em máquinas

As máquinas aparecem nos resultados da consulta ARG, mas ainda não aparecem na pré-visualização dinâmica do grupo. Neste caso, as máquinas podem não ser designadas como trabalhadores híbridos e, portanto, não podem executar trabalhos de Automação e Gestão de Atualização da Azure. Para garantir que as máquinas que espera ver são configuras como Trabalhadores Híbridos Runbook:

1. No portal Azure, aceda à conta de Automação por uma máquina que não está a aparecer corretamente.

2. Selecione **grupos de trabalhadores híbridos** em **Automação de Processos.**

3. Selecione o separador **grupos de trabalhadores híbridos system.**

4. Valide que o trabalhador híbrido está presente para esta máquina.

5. Se a máquina não estiver configurada como um trabalhador híbrido, faça ajustes utilizando instruções [no seu datacenter ou nuvem utilizando o Trabalhador de Runbook Híbrido](../automation-hybrid-runbook-worker.md).

6. Junte-se à máquina ao grupo híbrido Runbook Worker.

7. Repita os passos acima para todas as máquinas que não tenham sido exibidas na pré-visualização.

## <a name="scenario-update-management-components-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Cenário: Componentes de Gestão de Atualização ativados, enquanto o VM continua a mostrar como sendo configurado

### <a name="issue"></a>Problema

Continua a ver a seguinte mensagem num VM 15 minutos após o início da implantação:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Causa

Este erro pode ocorrer pelas seguintes razões:

* A comunicação com a conta Automation está a ser bloqueada.

* Há um nome de computador duplicado com diferentes identificações de computador de origem. Este cenário ocorre quando um VM com um nome de computador específico é criado em diferentes grupos de recursos e está reportando para o mesmo espaço de trabalho do Agente Logístico na subscrição.

* A imagem VM que está a ser implantada pode vir de uma máquina clonada que não foi preparada com a Preparação do Sistema (sysprep) com o agente Log Analytics para Windows instalado.

### <a name="resolution"></a>Resolução

Para ajudar a determinar o problema exato com o VM, execute a seguinte consulta no espaço de trabalho Log Analytics que está ligado à sua conta Automation.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Comunicação com a conta Automation bloqueada

Vá à [Rede planejando](../update-management/overview.md#ports) saber quais endereços e portas devem ser autorizados para a Gestão de Atualização funcionar.

#### <a name="duplicate-computer-name"></a>Nome de computador duplicado

Mude o nome dos seus VMs para garantir nomes únicos no seu ambiente.

#### <a name="deployed-image-from-cloned-machine"></a>Imagem implantada a partir de máquina clonada

Se estiver a usar uma imagem clonada, diferentes nomes de computador têm a mesma identificação de computador de origem. Neste caso:

1. No seu espaço de trabalho Log Analytics, remova o VM da procura guardada para a `MicrosoftDefaultScopeConfig-Updates` configuração do âmbito se for mostrado. As pesquisas guardadas podem ser encontradas no **âmbito do General** no seu espaço de trabalho.

2. Executar o seguinte cmdlet.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Corra `Restart-Service HealthService` para reiniciar o serviço de saúde. Esta operação recria a chave e gera um novo UUID.

4. Se esta abordagem não funcionar, executar sysprep na imagem primeiro e, em seguida, instalar o agente Log Analytics para windows.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Cenário: Recebe um erro de subscrição ligado quando cria uma implementação de atualização para máquinas em outro inquilino do Azure

### <a name="issue"></a>Problema

Encontra o seguinte erro quando tenta criar uma atualização para máquinas em outro inquilino Azure:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Causa

Este erro ocorre quando cria uma implementação de atualização que tem VMs Azure em outro inquilino que está incluído numa implementação de atualização.

### <a name="resolution"></a>Resolução

Utilize a seguinte solução para agendar estes itens. Pode utilizar o [cmdlet New-AzAutomationSchedule](/powershell/module/az.automation/new-azautomationschedule) com o `ForUpdateConfiguration` parâmetro para criar um horário. Em seguida, use o [Cmdlet New-AzAutomationSoftwareConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) e passe as máquinas do outro inquilino para o `NonAzureComputer` parâmetro. O exemplo a seguir mostra como fazê-lo:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Cenário: Reboots inexplicáveis

### <a name="issue"></a>Problema

Apesar de ter definido a opção **'Controlo de Reinicialização'** para **Never Reboot,** as máquinas ainda estão a ser reiniciadas após a instalação de atualizações.

### <a name="cause"></a>Causa

O Windows Update pode ser modificado por várias teclas de registo, qualquer uma das quais pode modificar o comportamento do reboot.

### <a name="resolution"></a>Resolução

Reveja as chaves de registo listadas em [Atualizações Automáticas configuradas, editando as](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) chaves de registo e [registo utilizadas para gerir o reinício](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) para se certificar de que as suas máquinas estão configuradas corretamente.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Cenário: Máquina mostra "Falhado no arranque" numa implementação de atualização

### <a name="issue"></a>Problema

Uma máquina mostra um `Failed to start` estado. Ao ver os detalhes específicos da máquina, vê o seguinte erro:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Causa

Este erro pode ocorrer por um dos seguintes motivos:

* A máquina já não existe.
* A máquina está desligada e inacessível.
* A máquina tem um problema de conectividade de rede e, portanto, o trabalhador híbrido na máquina é inacessível.
* Houve uma atualização para o agente Log Analytics que alterou a identificação do computador de origem.
* A sua atualização foi acelerada se atingir o limite de 200 postos de trabalho simultâneos numa conta de Automação. Cada implantação é considerada um trabalho, e cada máquina numa atualização conta como um trabalho. Qualquer outra tarefa de automatização ou atualização de implementação atualmente em execução na sua conta de Automação conta para o limite de trabalho simultâneo.

### <a name="resolution"></a>Resolução

Quando aplicável, utilize [grupos dinâmicos](../update-management/configure-groups.md) para as suas implementações de atualização. Além disso, pode dar os seguintes passos.

1. Verifique se a sua máquina ou servidor satisfaz os [requisitos.](../update-management/overview.md#system-requirements)
2. Verifique a conectividade com o Trabalhador de Runbook Híbrido utilizando o agente híbrido Runbook Worker. Para saber mais sobre o resolução de [problemas, consulte os problemas do agente de atualização troubleshoot](update-agent-issues.md).

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Cenário: As atualizações são instaladas sem implantação

### <a name="issue"></a>Problema

Quando inscreve uma máquina Do Windows na Gestão de Atualizações, vê atualizações instaladas sem implantação.

### <a name="cause"></a>Causa

No Windows, as atualizações são instaladas automaticamente assim que estão disponíveis. Este comportamento pode causar confusão se não tiver agendado uma atualização para a máquina.

### <a name="resolution"></a>Resolução

A  `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` chave de registo não tem padrão para uma definição de 4: `auto download and install` .

Para os clientes de Gestão de Atualização, recomendamos definir esta chave para 3: `auto download but do not auto install` .

Para obter mais informações, consulte [configurar atualizações automáticas](/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Cenário: A máquina já está registada numa conta diferente

### <a name="issue"></a>Problema

Recebe a seguinte mensagem de erro:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Causa

A máquina já foi implantada noutro espaço de trabalho para a Gestão de Atualização.

### <a name="resolution"></a>Resolução

1. Siga os [passos em máquinas não apareça no portal sob Gestão de Atualização](#nologs) para se certificar de que a máquina está a reportar para o espaço de trabalho correto.
2. Limpe os artefactos na máquina [eliminando o grupo híbrido runbook](../automation-windows-hrw-install.md#remove-a-hybrid-worker-group)e tente novamente.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Cenário: A máquina não consegue comunicar com o serviço

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

Um representante, um portal ou uma firewall podem estar a bloquear a comunicação da rede.

### <a name="resolution"></a>Resolução

Reveja a sua rede e certifique-se de que são permitidas portas e endereços apropriados. Consulte [os requisitos](../automation-hybrid-runbook-worker.md#network-planning) de rede para uma lista de portas e endereços que são exigidos pela Update Management e pelos Trabalhadores de Runbook Híbridos.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Cenário: Incapaz de criar certificado auto-assinado

### <a name="issue"></a>Problema

Recebe uma das seguintes mensagens de erro:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Causa

O Trabalhador de Runbook Híbrido não conseguiu gerar um certificado auto-assinado.

### <a name="resolution"></a>Resolução

Verifique se a conta do sistema leu o acesso à pasta **C:\ProgramData\Microsoft\Crypto\RSA** e tente novamente.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Cenário: A atualização programada falhou com um erro de ManutençãoWindowExceeded

### <a name="issue"></a>Problema

A janela de manutenção predefinida para atualizações é de 120 minutos. Pode aumentar a janela de manutenção para um máximo de 6 horas, ou 360 minutos.

### <a name="resolution"></a>Resolução

Para entender por que isto ocorreu durante uma atualização executada após o início com sucesso, [verifique a saída](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) de trabalho da máquina afetada durante a execução. Pode encontrar mensagens de erro específicas das suas máquinas que possa pesquisar e tomar medidas.  

Edite quaisquer implementações de atualização programadas e aumente a janela de manutenção.

Para obter mais informações sobre janelas de manutenção, consulte [as atualizações da Instalação](../update-management/deploy-updates.md#schedule-an-update-deployment).

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Cenário: Máquina mostra como "Não avaliada" e mostra uma exceção hresult

### <a name="issue"></a>Problema

* Você tem máquinas que mostram como `Not assessed` under **Compliance**, e você vê uma mensagem de exceção abaixo deles.
* Vê um código de erro HRESULT no portal.

### <a name="cause"></a>Causa

O Agente de Atualização (Windows Update Agent on Windows; o gestor de pacotes para uma distribuição Linux) não está configurado corretamente. A Atualização Conta com o Agente de Atualização da máquina para fornecer as atualizações necessárias, o estado do patch e os resultados dos patches implantados. Sem esta informação, a Update Management não pode reportar adequadamente os patches que são necessários ou instalados.

### <a name="resolution"></a>Resolução

Tente realizar atualizações localmente na máquina. Se esta operação falhar, normalmente significa que há um erro de configuração do agente de atualização.

Este problema é frequentemente causado por problemas de configuração de rede e firewall. Utilize as seguintes verificações para corrigir o problema.

* Para o Linux, verifique a documentação adequada para se certificar de que pode chegar ao ponto final da rede do seu repositório de embalagens.

* Para o Windows, verifique se a configuração do seu agente como listado em [Atualizações não está a ser descarregada a partir do ponto final intranet (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).

  * Se as máquinas estiverem configuradas para a Atualização do Windows, certifique-se de que pode chegar aos pontos finais descritos em [Questões relacionadas com HTTP/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Se as máquinas estiverem configuradas para serviços de atualização do Servidor do Windows (WSUS), certifique-se de que pode chegar ao servidor WSUS configurado pela chave de [registo WUServer](/windows/deployment/update/waas-wu-settings).

Se vir um HRESULT, clique duas vezes na exceção exibida a vermelho para ver toda a mensagem de exceção. Reveja a tabela seguinte para potenciais resoluções ou ações recomendadas.

|Exceção  |Resolução ou ação  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Procure o código de erro relevante na [lista de códigos de erro de atualização](https://support.microsoft.com/help/938205/windows-update-error-code-list) do Windows para encontrar detalhes adicionais sobre a causa da exceção.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Estes indicam problemas de conectividade de rede. Certifique-se de que a sua máquina tem conectividade de rede para a Gestão de Atualização. Consulte a secção [de planeamento](../update-management/overview.md#ports) da rede para obter uma lista de portas e endereços necessários.        |
|`0x8024001E`| A operação de atualização não foi concluída porque o serviço ou o sistema estavam a desligar-se.|
|`0x8024002E`| O serviço de atualização do Windows está desativado.|
|`0x8024402C`     | Se estiver a utilizar um servidor WSUS, certifique-se de que os valores de registo para `WUServer` e `WUStatusServer` sob a  `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` tecla de registo especificam o servidor WSUS correto.        |
|`0x80072EE2`|Há um problema de conectividade de rede ou um problema em falar com um servidor WSUS configurado. Verifique as definições da WSUS e certifique-se de que o serviço está acessível ao cliente.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Certifique-se de que o serviço de Atualização do Windows (wuauserv) está em funcionamento e não está desativado.        |
|`0x80070005`| Um erro negado de acesso pode ser causado por qualquer um dos seguintes:<br> Computador infetado<br> As definições de Atualização do Windows não configuradas corretamente<br> Erro de permissão de ficheiro com %WinDir%\Pasta de distribuição de software<br> Espaço insuficiente do disco na unidade do sistema (C:).
|Qualquer outra exceção genérica     | Faça uma pesquisa na internet para possíveis resoluções e trabalhe com o seu suporte de TI local.         |

Rever o ficheiro **%Windir%\Windowsupdate.log** também pode ajudá-lo a determinar possíveis causas. Para obter mais informações sobre como ler o registo, consulte [Como ler o ficheiro .log Windowsupdate](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file).

Também pode descarregar e executar o [resolução de problemas](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) do Windows Update para verificar se há problemas com o Windows Update na máquina.

> [!NOTE]
> A documentação [do Windows Update troubleshooter](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) indica que é para uso em clientes Windows, mas também funciona no Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Cenário: Atualização de retornas falha do estado (Linux)

### <a name="issue"></a>Problema

Uma atualização começa mas encontra erros durante a execução.

### <a name="cause"></a>Causa

Causas possíveis:

* O gerente do pacote não é saudável.
* O Agente de Atualização (WUA para Windows, gestor de pacotes específico para o Linux) está mal configurado.
* Pacotes específicos estão a interferir com o patching baseado na nuvem.
* A máquina é inacessível.
* As atualizações tinham dependências que não estavam resolvidas.

### <a name="resolution"></a>Resolução

Se ocorrerem falhas durante uma atualização após o início com sucesso, [verifique a saída](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) de trabalho da máquina afetada durante a execução. Pode encontrar mensagens de erro específicas das suas máquinas que possa pesquisar e tomar medidas. A Atualização De Gestão requer que o gestor de pacotes seja saudável para implementações de atualização bem sucedidas.

Se patches específicos, pacotes ou atualizações forem vistos imediatamente antes do trabalho falhar, pode tentar [excluir](../update-management/deploy-updates.md#schedule-an-update-deployment) estes itens da próxima implementação da atualização. Para recolher informações de registo a partir da Atualização do Windows, consulte [os ficheiros de registo do Windows Update](/windows/deployment/update/windows-update-logs).

Se não conseguir resolver um problema de correção, faça uma cópia do ficheiro **/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log** e preservá-lo para efeitos de resolução de problemas antes do início da próxima atualização.

## <a name="patches-arent-installed"></a>Patches não estão instalados

### <a name="machines-dont-install-updates"></a>As máquinas não instalam atualizações

Experimente executar as atualizações diretamente na máquina. Se a máquina não conseguir aplicar as atualizações, consulte a [lista de potenciais erros no guia de resolução de problemas](#hresult).

Se as atualizações forem executadas localmente, tente remover e reinstalar o agente na máquina seguindo as orientações no [Remove a VM da Update Management](../update-management/remove-vms.md).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Sei que as atualizações estão disponíveis, mas não mostram como estão disponíveis nas minhas máquinas.

Isto acontece frequentemente se as máquinas estiverem configuradas para obter atualizações do WSUS ou do Microsoft Endpoint Configuration Manager, mas o WSUS e o Gestor de Configuração não aprovaram as atualizações.

Pode verificar se as máquinas estão configuradas para WSUS e SCCM, cruzando a chave de `UseWUServer` registo das teclas de registo nas [Atualizações Automáticas Configurantes, editando a secção de registo deste artigo](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).

Se as atualizações não forem aprovadas na WSUS, não estão instaladas. Pode verificar se há atualizações não aprovadas no Log Analytics executando a seguinte consulta.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>As atualizações aparecem como tendo sido instaladas, mas não as encontro na minha máquina

Muitas vezes, as atualizações são substituídas por outras. Para obter mais informações, consulte Update é substituído no guia de resolução de [problemas](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) do Windows Update.

### <a name="installing-updates-by-classification-on-linux"></a>Instalar atualizações por classificação no Linux

A implementação de atualizações no Linux por classificação ("atualizações críticas e de segurança") tem limitações importantes, sobretudo para o CentOS. Estas limitações estão documentadas na [página de visão geral da Gestão](../update-management/overview.md#linux)de Atualização .

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 está constantemente desaparecido

KB2267602 é a [atualização de definições do Windows Defender](https://www.microsoft.com/wdsi/definitions). É atualizado diariamente.

## <a name="next-steps"></a>Passos seguintes

Se não vir o seu problema ou não conseguir resolver o seu problema, experimente um dos seguintes canais para obter apoio adicional.

* Obtenha respostas de especialistas da Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) , a conta oficial do Microsoft Azure para melhorar a experiência do cliente.
* Arquive um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **Apoio**.
