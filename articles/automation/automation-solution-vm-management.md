---
title: Solução Iniciar/Parar VMs fora do horário comercial
description: Essa solução de gerenciamento de VM é iniciada e interrompe sua Azure Resource Manager máquinas virtuais em um agendamento e monitora proativamente de logs de Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2019
ms.topic: conceptual
ms.openlocfilehash: 37fee7f96a27942a1295cb8c2315fedffc5bdefe
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030170"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Iniciar/Parar VMs fora do horário comercial solução na automação do Azure

A solução Iniciar/Parar VMs fora do horário comercial iniciar e interromper suas máquinas virtuais do Azure em agendas definidas pelo usuário, fornece informações sobre logs de Azure Monitor e envia emails opcionais usando [grupos de ações](../azure-monitor/platform/action-groups.md). Ele dá suporte a VMs Azure Resource Manager e clássicas para a maioria dos cenários. Para usar essa solução com VMs clássicas, você precisa de uma conta RunAs clássica, que não é criada por padrão. Para obter instruções sobre como criar uma conta RunAs clássica, consulte [contas Executar como clássicas](automation-create-standalone-account.md#classic-run-as-accounts).

> [!NOTE]
> A solução Iniciar/Parar VMs fora do horário comercial foi testada com os módulos do Azure que são importados para sua conta de automação quando você implanta a solução. Atualmente, a solução não funciona com versões mais recentes do módulo do Azure. Isso afeta apenas a conta de automação que você usa para executar a solução de Iniciar/Parar VMs fora do horário comercial. Você ainda pode usar versões mais recentes do módulo do Azure em suas outras contas de automação, conforme descrito em [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md)

Essa solução fornece uma opção de automação descentralizada de baixo custo para usuários que desejam otimizar seus custos de VM. Com essa solução, você pode:

- Agende as VMs para iniciar e parar.
- Agende as VMs para iniciar e parar em ordem crescente usando marcas do Azure (sem suporte para VMs clássicas).
- Autostop VMs com base no baixo uso da CPU.

A seguir estão as limitações da solução atual:

- Essa solução gerencia VMs em qualquer região, mas só pode ser usada na mesma assinatura que a sua conta de automação do Azure.
- Essa solução está disponível no Azure e AzureGov para qualquer região que dê suporte a um espaço de trabalho Log Analytics, uma conta de automação do Azure e alertas. Atualmente, as regiões AzureGov não dão suporte à funcionalidade de email.

> [!NOTE]
> Se você estiver usando a solução para VMs clássicas, todas as suas VMs serão processadas sequencialmente por serviço de nuvem. As máquinas virtuais ainda são processadas em paralelo em diferentes serviços de nuvem. Se você tiver mais de 20 VMs por serviço de nuvem, é recomendável criar várias agendas com o runbook pai **ScheduledStartStop_Parent** e especificar 20 VMs por agenda. Nas propriedades da agenda, especifique como uma lista separada por vírgulas, nomes de VM no parâmetro **VMList** . Caso contrário, se o trabalho de automação para esta solução for executado mais de três horas, ele será temporariamente descarregado ou interrompido de acordo com o limite de [compartilhamento justo](automation-runbook-execution.md#fair-share) .
>
> As assinaturas do provedor de soluções na nuvem do Azure (CSP do Azure) dão suporte apenas ao modelo de Azure Resource Manager, os serviços não Azure Resource Manager não estão disponíveis no programa. Quando a solução iniciar/parar é executada, você pode receber erros, pois tem cmdlets para gerenciar recursos clássicos. Para saber mais sobre o CSP, confira [serviços disponíveis em assinaturas do CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services). Se você usar uma assinatura CSP, deverá modificar a variável [**External_EnableClassicVMs**](#variables) para **false** após a implantação.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Os runbooks para esta solução funcionam com uma [conta Executar como do Azure](automation-create-runas-account.md). A conta Executar como é o método de autenticação preferencial, pois ela usa a autenticação de certificado em vez de uma senha que pode expirar ou ser alterada com frequência.

Recomendamos que você use uma conta de automação separada para a solução iniciar/parar VM. Isso ocorre porque as versões de módulo do Azure são atualizadas com frequência e seus parâmetros podem ser alterados. A solução iniciar/parar VM não é atualizada na mesma cadência para que ela possa não funcionar com versões mais recentes dos cmdlets que ele usa. Também recomendamos que você teste as atualizações de módulo em uma conta de automação de teste antes de importá-las em suas contas de automação de produção.

### <a name="permissions-needed-to-deploy"></a>Permissões necessárias para implantar

Há certas permissões que um usuário deve ter para implantar a solução iniciar/parar VMs fora do horário de expediente. Essas permissões são diferentes se usar uma conta de automação criada previamente e Log Analytics espaço de trabalho ou criar novas durante a implantação. Se você for um colaborador na assinatura e um administrador global em seu locatário Azure Active Directory, não será necessário configurar as permissões a seguir. Se você não tiver esses direitos ou precisar configurar uma função personalizada, consulte as permissões necessárias abaixo.

#### <a name="pre-existing-automation-account-and-log-analytics-workspace"></a>Conta de automação pré-existente e espaço de trabalho de Log Analytics

Para implantar a solução iniciar/parar VMs fora do horário comercial em uma conta de automação existente e Log Analytics espaço de trabalho, o usuário que está implantando a solução requer as seguintes permissões no **grupo de recursos**. Para saber mais sobre as funções, confira [funções personalizadas para recursos do Azure](../role-based-access-control/custom-roles.md).

| Permissão | Âmbito|
| --- | --- |
| Microsoft.Automation/automationAccounts/read | Grupo de Recursos |
| Microsoft.Automation/automationAccounts/variables/write | Grupo de Recursos |
| Microsoft.Automation/automationAccounts/schedules/write | Grupo de Recursos |
| Microsoft.Automation/automationAccounts/runbooks/write | Grupo de Recursos |
| Microsoft.Automation/automationAccounts/connections/write | Grupo de Recursos |
| Microsoft.Automation/automationAccounts/certificates/write | Grupo de Recursos |
| Microsoft.Automation/automationAccounts/modules/write | Grupo de Recursos |
| Microsoft.Automation/automationAccounts/modules/read | Grupo de Recursos |
| Microsoft.automation/automationAccounts/jobSchedules/write | Grupo de Recursos |
| Microsoft.Automation/automationAccounts/jobs/write | Grupo de Recursos |
| Microsoft.Automation/automationAccounts/jobs/read | Grupo de Recursos |
| Microsoft.OperationsManagement/solutions/write | Grupo de Recursos |
| Microsoft.OperationalInsights/workspaces/* | Grupo de Recursos |
| Microsoft.Insights/diagnosticSettings/write | Grupo de Recursos |
| Microsoft.Insights/ActionGroups/Write | Grupo de Recursos |
| Microsoft.Insights/ActionGroups/read | Grupo de Recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupo de Recursos |
| Microsoft.Resources/deployments/* | Grupo de Recursos |

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>Nova conta de automação e um novo espaço de trabalho Log Analytics

Para implantar a solução iniciar/parar VMs fora do horário comercial em uma nova conta de automação e Log Analytics espaço de trabalho, o usuário que está implantando a solução precisa das permissões definidas na seção anterior, bem como as seguintes permissões:

- Coadministrador na assinatura – é necessário apenas criar a conta Executar como clássica se você pretende gerenciar VMs clássicas. [As contas runas clássicas](automation-create-standalone-account.md#classic-run-as-accounts) não são mais criadas por padrão.
- Um membro da função de **desenvolvedor de aplicativo** [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) . Para obter mais informações sobre como configurar contas Executar como, consulte [permissões para configurar contas Executar como](manage-runas-account.md#permissions).
- Colaborador na assinatura ou nas permissões a seguir.

| Permissão |Âmbito|
| --- | --- |
| Microsoft.Authorization/Operations/read | Subscrição|
| Microsoft.Authorization/permissions/read |Subscrição|
| Microsoft.Authorization/roleAssignments/read | Subscrição |
| Microsoft.Authorization/roleAssignments/write | Subscrição |
| Microsoft.Authorization/roleAssignments/delete | Subscrição |
| Microsoft.Automation/automationAccounts/connections/read | Grupo de Recursos |
| Microsoft.Automation/automationAccounts/certificates/read | Grupo de Recursos |
| Microsoft.Automation/automationAccounts/write | Grupo de Recursos |
| Microsoft.OperationalInsights/workspaces/write | Grupo de Recursos |

## <a name="deploy-the-solution"></a>Implementar a solução

Execute as etapas a seguir para adicionar a solução de Iniciar/Parar VMs fora do horário comercial à sua conta de automação e, em seguida, configure as variáveis para personalizar a solução.

1. Em uma conta de automação, selecione **iniciar/parar VM** em **recursos relacionados**. A partir daqui, você pode clicar em **saiba mais sobre e habilitar a solução**. Se você já tiver uma solução iniciar/parar VM implantada, poderá selecioná-la clicando em **gerenciar a solução** e localizando-a na lista.

   ![Habilitar da conta de automação](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Você também pode criá-lo de qualquer lugar na portal do Azure, clicando em **criar um recurso**. Na página do Marketplace, digite uma palavra-chave como **Iniciar** ou **iniciar/parar**. À medida que começa a escrever, a lista filtra com base na sua entrada. Como alternativa, você pode digitar uma ou mais palavras-chave do nome completo da solução e, em seguida, pressionar Enter. Selecione **iniciar/parar VMs fora do horário comercial** nos resultados da pesquisa.

2. Na página **iniciar/parar VMs fora do horário comercial** da solução selecionada, examine as informações de resumo e clique em **criar**.

   ![Portal do Azure](media/automation-solution-vm-management/azure-portal-01.png)

3. A página **Adicionar solução** é exibida. Você será solicitado a configurar a solução antes de poder importá-la para sua assinatura de automação.

   ![Página Adicionar solução de gerenciamento de VM](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

4. Na página **Adicionar solução** , selecione **espaço de trabalho**. Selecione um espaço de trabalho Log Analytics vinculado à mesma assinatura do Azure em que a conta de automação está. Se você não tiver um espaço de trabalho, selecione **criar novo espaço de trabalho**. Na página **log Analytics espaço de trabalho** , execute as seguintes etapas:
   - Especifique um nome para o novo **espaço de trabalho log Analytics**, como "ContosoLAWorkspace".
   - Selecione uma **assinatura** à qual vincular selecionando na lista suspensa, se o padrão selecionado não for apropriado.
   - Para o **grupo de recursos**, você pode criar um novo grupo de recursos ou selecionar um existente.
   - Selecione uma **Localização**.
   - Selecione um **Escalão de preço**. Escolha a opção **por GB (autônomo)** . Os logs de Azure Monitor têm [preços](https://azure.microsoft.com/pricing/details/log-analytics/) atualizados e a camada por GB é a única opção.

   > [!NOTE]
   > Quando ativar soluções, apenas são suportadas determinadas regiões para associar uma área de trabalho do Log Analytics e uma Conta de Automatização.
   >
   > Para obter uma lista dos pares de mapeamento com suporte, confira [mapeamento de região para conta de automação e espaço de trabalho de log Analytics](how-to/region-mappings.md).

5. Depois de fornecer as informações necessárias na página **log Analytics espaço de trabalho** , clique em **criar**. Você pode acompanhar seu progresso em **notificações** no menu, que retorna à página **Adicionar solução** quando terminar.
6. Na página **Adicionar solução** , selecione **conta de automação**. Se você estiver criando um novo espaço de trabalho Log Analytics, poderá criar uma nova conta de automação a ser associada a ela ou selecionar uma conta de automação existente que ainda não esteja vinculada a um espaço de trabalho Log Analytics. Selecione uma conta de automação existente ou clique em **criar uma conta de automação**e, na página **adicionar conta de automação** , forneça as seguintes informações:
   - No campo **Nome**, introduza o nome da conta de Automatização.

     Todas as outras opções são preenchidas automaticamente com base no espaço de trabalho Log Analytics selecionado. Essas opções não podem ser modificadas. O método de autenticação predefinido para os runbooks incluídos nesta solução é a conta Run As do Azure. Depois de clicar em **OK**, as opções de configuração são validadas e a conta de automação é criada. Pode acompanhar o progresso em **Notificações**, no menu.

7. Por fim, na página **Adicionar solução** , selecione **configuração**. A página **parâmetros** é exibida.

   ![Página de parâmetros para a solução](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Aqui, você será solicitado a:
   - Especifique os **nomes do resourcegroup de destino**. Esses valores são nomes de grupos de recursos que contêm VMs a serem gerenciadas por essa solução. Você pode inserir mais de um nome e separá-los usando uma vírgula (os valores não diferenciam maiúsculas de minúsculas). Se quiser segmentar todas as VMs em todos os grupos de recursos da subscrição, a utilização de um caráter universal é suportada. Esse valor é armazenado nas variáveis **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames** .
   - Especifique a **lista de exclusão da VM (cadeia de caracteres)** . Esse valor é o nome de uma ou mais máquinas virtuais do grupo de recursos de destino. Você pode inserir mais de um nome e separá-los usando uma vírgula (os valores não diferenciam maiúsculas de minúsculas). Há suporte para o uso de um curinga. Esse valor é armazenado na variável **External_ExcludeVMNames** .
   - Selecione uma **agenda**. Selecione uma data e hora para sua agenda. Uma agenda recorrente diária será criada a partir da hora que você selecionou. A seleção de uma região diferente não está disponível. Para configurar o agendamento para seu fuso horário específico depois de configurar a solução, consulte [modificando o agendamento de inicialização e desligamento](#modify-the-startup-and-shutdown-schedules).
   - Para receber **notificações por email** de um grupo de ações, aceite o valor padrão de **Sim** e forneça um endereço de email válido. Se você selecionar **não** , mas decidir em uma data posterior que deseja receber notificações por email, poderá atualizar o [grupo de ação](../azure-monitor/platform/action-groups.md) que é criado com endereços de email válidos separados por uma vírgula. Você também precisa habilitar as seguintes regras de alerta:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > O valor padrão para **nomes de resourcegroup de destino** é um **&ast;** . Isso se destina a todas as VMs em uma assinatura. Se você não quiser que a solução Direcione para todas as VMs em sua assinatura, esse valor precisará ser atualizado para uma lista de nomes de grupos de recursos antes de habilitar os agendamentos.

8. Depois de definir as configurações iniciais necessárias para a solução, clique em **OK** para fechar a página **parâmetros** e selecione **criar**. Depois que todas as configurações forem validadas, a solução será implantada em sua assinatura. Esse processo pode levar vários segundos para ser concluído e você pode acompanhar seu progresso em **notificações** no menu.

> [!NOTE]
> Se você tiver uma assinatura do provedor de soluções na nuvem do Azure (CSP do Azure), após a conclusão da implantação, em sua conta de automação, vá para **variáveis** em **recursos compartilhados** e defina a variável [**External_EnableClassicVMs**](#variables) como **false**. Isso interrompe a solução de procurar por recursos de VM clássicos.

## <a name="scenarios"></a>Cenários

A solução contém três cenários distintos. Esses cenários são:

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>Cenário 1: iniciar/parar VMs em um agendamento

Esse cenário é a configuração padrão quando você implanta a solução pela primeira vez. Por exemplo, você pode configurá-lo para interromper todas as VMs em uma assinatura quando sair do trabalho à noite e iniciá-las na manhã quando você estiver de volta ao escritório. Quando você configura as agendas **Scheduled-StartVM** e **Scheduled-StopVM** durante a implantação, elas iniciam e param VMs direcionadas. A configuração dessa solução para apenas parar as VMs tem suporte, consulte [modificar as agendas de inicialização e desligamento](#modify-the-startup-and-shutdown-schedules) para saber como configurar uma agenda personalizada.

> [!NOTE]
> O fuso horário é o fuso horário atual quando você configura o parâmetro tempo de agendamento. No entanto, ele é armazenado no formato UTC na automação do Azure. Você não precisa fazer nenhuma conversão de fuso horário, pois isso é tratado durante a implantação.

Você controla quais VMs estão no escopo configurando as seguintes variáveis: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**e **External_ExcludeVMNames**.

Você pode habilitar o direcionamento da ação em relação a uma assinatura e a um grupo de recursos ou direcionar uma lista específica de VMs, mas não ambos.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Direcionar as ações de início e parada em relação a uma assinatura e a um grupo de recursos

1. Configure as variáveis de **External_Stop_ResourceGroupNames** e **External_ExcludeVMNames** para especificar as VMs de destino.
2. Habilite e atualize as agendas **Scheduled-StartVM** e **Scheduled-StopVM** .
3. Execute o runbook **ScheduledStartStop_Parent** com o parâmetro action definido como **Start** e o parâmetro WHATIF definido como **true** para visualizar as alterações.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Direcionar a ação de início e parada por lista de VMs

1. Execute o runbook **ScheduledStartStop_Parent** com o parâmetro action definido como **Iniciar**, adicione uma lista de VMs separadas por vírgula no parâmetro *VMList* e, em seguida, defina o parâmetro WHATIF como **true**. Visualize suas alterações.
1. Configure o parâmetro **External_ExcludeVMNames** com uma lista separada por vírgulas de VMs (VM1, VM2, VM3).
1. Esse cenário não respeita as variáveis **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupnames** . Para esse cenário, você precisa criar seu próprio agendamento de automação. Para obter detalhes, consulte [agendando um runbook na automação do Azure](../automation/automation-schedules.md).

> [!NOTE]
> O valor dos **nomes de resourcegroup de destino** é armazenado como o valor para **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames**. Para maior granularidade, você pode modificar cada uma dessas variáveis para os diferentes grupos de recursos de destino. Para iniciar ação, use **External_Start_ResourceGroupNames**e, para ação de parada, use **External_Stop_ResourceGroupNames**. As VMs são adicionadas automaticamente às agendas de início e parada.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>Cenário 2: iniciar/parar VMS em sequência usando marcas

Em um ambiente que inclui dois ou mais componentes em várias VMs que dão suporte a uma carga de trabalho distribuída, o suporte à sequência na qual os componentes são iniciados e interrompidos em ordem é importante. Você pode realizar esse cenário executando as seguintes etapas:

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Direcionar as ações de início e parada em relação a uma assinatura e a um grupo de recursos

1. Adicione um **sequencestart** e uma marca **sequencestop** com um valor inteiro positivo para as VMs que são destinadas às variáveis **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames** . As ações iniciar e parar são executadas em ordem crescente. Para saber como marcar uma VM, consulte [marcar uma máquina virtual do Windows no Azure](../virtual-machines/windows/tag.md) e [marcar uma máquina virtual do Linux no Azure](../virtual-machines/linux/tag.md).
1. Modifique as agendas **Sequenced-StartVM** e **Sequenced-StopVM** para a data e hora que atendem às suas necessidades e habilite a agenda.
1. Execute o runbook **SequencedStartStop_Parent** com o parâmetro action definido como **Start** e o parâmetro WHATIF definido como **true** para visualizar as alterações.
1. Visualize a ação e faça as alterações necessárias antes de implementar em VMs de produção. Quando estiver pronto, execute manualmente o runbook com o parâmetro definido como **false**ou permita que o agendamento de automação **Sequenced-StartVM** e **Sequenced-StopVM** seja executado automaticamente após o agendamento prescrito.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Direcionar a ação de início e parada por lista de VMs

1. Adicione um **sequencestart** e uma marca **sequencestop** com um valor inteiro positivo às VMs que você planeja adicionar ao parâmetro **VMList** .
1. Execute o runbook **SequencedStartStop_Parent** com o parâmetro action definido como **Iniciar**, adicione uma lista de VMs separadas por vírgula no parâmetro *VMList* e, em seguida, defina o parâmetro WHATIF como **true**. Visualize suas alterações.
1. Configure o parâmetro **External_ExcludeVMNames** com uma lista separada por vírgulas de VMs (VM1, VM2, VM3).
1. Esse cenário não respeita as variáveis **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupnames** . Para esse cenário, você precisa criar seu próprio agendamento de automação. Para obter detalhes, consulte [agendando um runbook na automação do Azure](../automation/automation-schedules.md).
1. Visualize a ação e faça as alterações necessárias antes de implementar em VMs de produção. Quando estiver pronto, execute manualmente o Monitoring-and-Diagnostics/Monitoring-Action-groupsrunbook com o parâmetro definido como **false**ou permita que o agendamento de automação **Sequenced-StartVM** e **Sequenced-StopVM** sejam executados automaticamente após sua programação prescrita.

### <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a>Cenário 3: iniciar/parar automaticamente com base na utilização da CPU

Essa solução pode ajudar a gerenciar o custo da execução de máquinas virtuais em sua assinatura avaliando VMs do Azure que não são usadas fora de períodos de pico, como após horas, e desligando-as automaticamente se a utilização do processador for menor que x%.

Por padrão, a solução é pré-configurada para avaliar a métrica percentual de CPU para ver se a utilização média é de 5% ou menos. Esse cenário é controlado pelas seguintes variáveis e pode ser modificado se os valores padrão não atenderem às suas necessidades:

- External_AutoStop_MetricName
- External_AutoStop_Threshold
- External_AutoStop_TimeAggregationOperator
- External_AutoStop_TimeWindow

Você pode habilitar o direcionamento da ação em relação a uma assinatura e a um grupo de recursos ou direcionar uma lista específica de VMs, mas não ambos.

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>Direcionar a ação de parada para uma assinatura e um grupo de recursos

1. Configure as variáveis de **External_Stop_ResourceGroupNames** e **External_ExcludeVMNames** para especificar as VMs de destino.
1. Habilite e atualize o agendamento de **Schedule_AutoStop_CreateAlert_Parent** .
1. Execute o runbook **AutoStop_CreateAlert_Parent** com o parâmetro action definido como **Start** e o parâmetro WHATIF definido como **true** para visualizar as alterações.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Direcionar a ação de início e parada por lista de VMs

1. Execute o runbook **AutoStop_CreateAlert_Parent** com o parâmetro action definido como **Iniciar**, adicione uma lista de VMs separadas por vírgula no parâmetro *VMList* e, em seguida, defina o parâmetro WHATIF como **true**. Visualize suas alterações.
1. Configure o parâmetro **External_ExcludeVMNames** com uma lista separada por vírgulas de VMs (VM1, VM2, VM3).
1. Esse cenário não respeita as variáveis **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupnames** . Para esse cenário, você precisa criar seu próprio agendamento de automação. Para obter detalhes, consulte [agendando um runbook na automação do Azure](../automation/automation-schedules.md).

Agora que você tem um agendamento para parar as VMs com base na utilização da CPU, você precisa habilitar uma das seguintes agendas para iniciá-las.

- Ação de início de destino por assinatura e grupo de recursos. Consulte as etapas no [cenário 1](#scenario-1-startstop-vms-on-a-schedule) para testar e habilitar agendas **-StartVM agendadas** .
- Ação de início de destino por assinatura, grupo de recursos e marca. Consulte as etapas no [cenário 2](#scenario-2-startstop-vms-in-sequence-by-using-tags) para testar e habilitar os agendamentos **Sequenced-StartVM** .

## <a name="solution-components"></a>Componentes da solução

Essa solução inclui runbooks pré-configurados, agendas e integração com logs de Azure Monitor para que você possa personalizar a inicialização e o desligamento de suas máquinas virtuais para atender às suas necessidades de negócios.

### <a name="runbooks"></a>Runbooks

A tabela a seguir lista os runbooks implantados na sua conta de automação por essa solução. Não faça alterações no código do runbook. Em vez disso, escreva seu próprio runbook para novas funcionalidades.

> [!IMPORTANT]
> Não execute diretamente nenhum runbook com "Child" anexado ao seu nome.

Todos os runbooks pai incluem o parâmetro _WhatIf_ . Quando definido como **true**, o _WhatIf_ dá suporte ao detalhamento do comportamento exato que o runbook usa quando executado sem o parâmetro _WhatIf_ e valida as VMs corretas que estão sendo direcionadas. Um runbook executa apenas suas ações definidas quando o parâmetro _WhatIf_ é definido como **false**.

|Runbook | Parâmetros | Descrição|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Chamado a partir do runbook pai. Esse runbook cria alertas por recurso para o cenário autostop.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: true ou false  | Cria ou atualiza as regras de alerta do Azure em VMs na assinatura ou nos grupos de recursos de destino. <br> VMList: lista de VMs separadas por vírgula. Por exemplo, _VM1, VM2, VM3_.<br> *WhatIf* valida a lógica do runbook sem execução.|
|AutoStop_Disable | nenhuma | Desabilita os alertas autostop e o agendamento padrão.|
|AutoStop_StopVM_Child | WebHookData | Chamado a partir do runbook pai. As regras de alerta chamam esse runbook para interromper a VM.|
|Bootstrap_Main | nenhuma | Usado uma vez para configurar as configurações de inicialização, como webhookURI, que normalmente não são acessíveis a partir de Azure Resource Manager. Esse runbook é removido automaticamente após a implantação bem-sucedida.|
|ScheduledStartStop_Child | VMName <br> Ação: iniciar ou parar <br> ResourceGroupName | Chamado a partir do runbook pai. Executa uma ação de início ou parada para a interrupção agendada.|
|ScheduledStartStop_Parent | Ação: iniciar ou parar <br>VMList <br> WhatIf: true ou false | Essa configuração afeta todas as VMs na assinatura. Edite o **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames** para executar somente nesses grupos de recursos de destino. Você também pode excluir VMs específicas atualizando a variável **External_ExcludeVMNames** .<br> VMList: lista de VMs separadas por vírgula. Por exemplo, _VM1, VM2, VM3_.<br> _WhatIf_ valida a lógica do runbook sem execução.|
|SequencedStartStop_Parent | Ação: iniciar ou parar <br> WhatIf: true ou false<br>VMList| Crie marcas chamadas **sequencestart** e **SEQUENCESTOP** em cada VM para a qual você deseja sequenciar a atividade iniciar/parar. Esses nomes de marca diferenciam maiúsculas de minúsculas. O valor da marca deve ser um inteiro positivo (1, 2, 3) que corresponde à ordem na qual você deseja iniciar ou parar. <br> VMList: lista de VMs separadas por vírgula. Por exemplo, _VM1, VM2, VM3_. <br> _WhatIf_ valida a lógica do runbook sem execução. <br> **Observação**: as VMs devem estar dentro dos grupos de recursos definidos como External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames e External_ExcludeVMNames nas variáveis de automação do Azure. Eles devem ter as marcas apropriadas para que as ações entrem em vigor.|

### <a name="variables"></a>Variáveis

A tabela a seguir lista as variáveis criadas em sua conta de automação. Modifique apenas variáveis prefixadas com **external**. Modificar variáveis com prefixo **interno** causa efeitos indesejáveis.

|Variável | Descrição|
|---------|------------|
|External_AutoStop_Condition | O operador condicional necessário para configurar a condição antes de disparar um alerta. Os valores aceitáveis são **GreaterThan**, **GreaterThanOrEqual**, **LessThan**e **LessThanOrEqual**.|
|External_AutoStop_Description | O alerta para interromper a VM se a porcentagem de CPU exceder o limite.|
|External_AutoStop_MetricName | O nome da métrica de desempenho para a qual a regra de alerta do Azure deve ser configurada.|
|External_AutoStop_Threshold | O limite para a regra de alerta do Azure especificado na variável _External_AutoStop_MetricName_. Os valores percentuais podem variar de 1 a 100.|
|External_AutoStop_TimeAggregationOperator | O operador de agregação de tempo, que é aplicado ao tamanho de janela selecionado para avaliar a condição. Os valores aceitáveis são **média**, **mínimo**, **máximo**, **total**e **último**.|
|External_AutoStop_TimeWindow | O tamanho da janela durante o qual o Azure analisa as métricas selecionadas para disparar um alerta. Esse parâmetro aceita entrada no formato TimeSpan. Os valores possíveis são de 5 minutos a 6 horas.|
|External_EnableClassicVMs| Especifica se as VMs clássicas são destinadas à solução. O valor predefinido é Verdadeiro. Isso deve ser definido como false para assinaturas do CSP. As VMs clássicas exigem uma [conta Executar como clássica](automation-create-standalone-account.md#classic-run-as-accounts).|
|External_ExcludeVMNames | Insira os nomes de VM a serem excluídos, separando os nomes usando uma vírgula sem espaços. Isso é limitado a 140 VMs. Se você adicionar mais de 140 VMs a essa lista separada por vírgulas, as VMs definidas como excluídas poderão ser iniciadas ou interrompidas inadvertidamente.|
|External_Start_ResourceGroupNames | Especifica um ou mais grupos de recursos, separando valores usando uma vírgula, direcionada para ações de início.|
|External_Stop_ResourceGroupNames | Especifica um ou mais grupos de recursos, separando valores usando uma vírgula, destinada a ações de parada.|
|Internal_AutomationAccountName | Especifica o nome da conta de automação.|
|Internal_AutoSnooze_WebhookUri | Especifica o URI do webhook chamado para o cenário autostop.|
|Internal_AzureSubscriptionId | Especifica a ID da assinatura do Azure.|
|Internal_ResourceGroupName | Especifica o nome do grupo de recursos da conta de automação.|

Em todos os cenários, as variáveis **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**e **External_ExcludeVMNames** são necessárias para direcionar as VMs, com exceção de fornecer uma lista separada por vírgulas de vms para os runbooks **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**e **ScheduledStartStop_Parent** . Ou seja, suas VMs devem residir em grupos de recursos de destino para que as ações iniciar e parar ocorram. A lógica funciona semelhante à política do Azure, na qual você pode direcionar a assinatura ou o grupo de recursos e ter ações herdadas por VMs recém-criadas. Essa abordagem evita ter que manter um agendamento separado para cada VM e gerenciar inícios e paradas em escala.

### <a name="schedules"></a>Agendas

A tabela a seguir lista cada uma das agendas padrão criadas em sua conta de automação. Você pode modificá-los ou criar suas próprias agendas personalizadas. Por padrão, todas as agendas são desabilitadas, exceto **Scheduled_StartVM** e **Scheduled_StopVM**.

Você não deve habilitar todos os agendamentos, pois isso pode criar ações de agendamento sobrepostas. É melhor determinar quais otimizações você deseja executar e modificar de acordo. Consulte os cenários de exemplo na seção visão geral para obter mais explicações.

|Nome da agenda | Frequência | Descrição|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | A cada 8 horas | Executa o runbook AutoStop_CreateAlert_Parent a cada 8 horas, o que, por sua vez, interrompe os valores baseados em VM em External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames e External_ExcludeVMNames nas variáveis de automação do Azure. Como alternativa, você pode especificar uma lista separada por vírgulas de VMs usando o parâmetro VMList.|
|Scheduled_StopVM | Definido pelo usuário, diariamente | Executa o runbook Scheduled_Parent com um parâmetro de _parar_ todos os dias no horário especificado. Interrompe automaticamente todas as VMs que atendem às regras definidas pelas variáveis de ativo. Habilite o agendamento relacionado, Scheduled **-StartVM**.|
|Scheduled_StartVM | Definido pelo usuário, diariamente | Executa o runbook Scheduled_Parent com um parâmetro de _Iniciar_ todos os dias no horário especificado. Inicia automaticamente todas as VMs que atendem às regras definidas pelas variáveis apropriadas. Habilite o agendamento relacionado, Scheduled **-StopVM**.|
|Sequenced-StopVM | 1:00 AM (UTC), toda sexta-feira | Executa o Sequenced_Parent runbook com um parâmetro de _parar_ toda sexta-feira no horário especificado. Sequencialmente (crescente) interrompe todas as VMs com uma marca de **SequenceStop** definida pelas variáveis apropriadas. Para obter mais informações sobre valores de marca e variáveis de ativo, consulte a seção Runbooks. Habilite o agendamento relacionado, **Sequenced-StartVM**.|
|Sequenced-StartVM | 1:00 PM (UTC), todas as segundas-feiras | Executa o Sequenced_Parent runbook com um parâmetro de _Iniciar_ todas as segundas-feiras no horário especificado. Sequencialmente (decrescente) inicia todas as VMs com uma marca de **SequenceStart** definida pelas variáveis apropriadas. Para obter mais informações sobre valores de marca e variáveis de ativo, consulte a seção Runbooks. Habilite o agendamento relacionado, **Sequenced-StopVM**.|

## <a name="azure-monitor-logs-records"></a>Registros de logs de Azure Monitor

A automação cria dois tipos de registros no espaço de trabalho Log Analytics: logs de trabalho e fluxos de trabalho.

### <a name="job-logs"></a>Registos de trabalhos

|Propriedade | Descrição|
|----------|----------|
|Autor da chamada |  Quem iniciou a operação. Os valores possíveis são um endereço de e-mail ou o sistema para trabalhos agendados.|
|Categoria | Classificação do tipo de dados. Para a Automatização, o valor é JobLogs.|
|CorrelationId | O GUID que é a ID de correlação do trabalho de runbook.|
|JobId | GUID que é a ID do trabalho de runbook.|
|operationName | Especifica o tipo de operação efetuada no Azure. Para a automação, o valor é trabalho.|
|resourceId | Especifica o tipo de recurso no Azure. Para a Automatização, o valor é a conta de Automatização associada ao runbook.|
|ResourceGroup | Especifica o nome do grupo de recursos do trabalho do runbook.|
|ResourceProvider | Especifica o serviço do Azure que fornece os recursos que pode implementar e gerir. Para a Automatização, o valor é Automatização do Azure.|
|ResourceType | Especifica o tipo de recurso no Azure. Para a Automatização, o valor é a conta de Automatização associada ao runbook.|
|resultType | O estado do trabalho do runbook. Os valores possíveis são:<br>- Iniciado<br>- Parado<br>- Suspenso<br>- Falhado<br>- Bem-sucedido|
|resultDescription | Descreve o estado do resultado do trabalho do runbook. Os valores possíveis são:<br>- Trabalho iniciado<br>- Trabalho falhado<br>- Trabalho Concluído|
|RunbookName | Especifica o nome do runbook.|
|SourceSystem | Especifica o sistema de origem dos dados submetidos. Para a automação, o valor é OpsManager|
|StreamType | Especifica o tipo de evento. Os valores possíveis são:<br>- Verboso<br>- Saída<br>- Erro<br>- Aviso|
|SubscriptionId | Especifica o ID de subscrição do trabalho.
|Tempo | Data e hora da execução do trabalho do runbook.|

### <a name="job-streams"></a>Fluxos de trabalho

|Propriedade | Descrição|
|----------|----------|
|Autor da chamada |  Quem iniciou a operação. Os valores possíveis são um endereço de e-mail ou o sistema para trabalhos agendados.|
|Categoria | Classificação do tipo de dados. Para a Automatização, o valor é JobStreams.|
|JobId | GUID que é a ID do trabalho de runbook.|
|operationName | Especifica o tipo de operação efetuada no Azure. Para a automação, o valor é trabalho.|
|ResourceGroup | Especifica o nome do grupo de recursos do trabalho do runbook.|
|resourceId | Especifica a ID do recurso no Azure. Para a Automatização, o valor é a conta de Automatização associada ao runbook.|
|ResourceProvider | Especifica o serviço do Azure que fornece os recursos que pode implementar e gerir. Para a Automatização, o valor é Automatização do Azure.|
|ResourceType | Especifica o tipo de recurso no Azure. Para a Automatização, o valor é a conta de Automatização associada ao runbook.|
|resultType | O resultado do trabalho do runbook no momento em que o evento foi gerado. Um valor possível é:<br>- InProgress|
|resultDescription | Inclui o fluxo de saída do runbook.|
|RunbookName | O nome do runbook.|
|SourceSystem | Especifica o sistema de origem dos dados submetidos. Para a automação, o valor é OpsManager.|
|StreamType | O tipo de fluxo de trabalho. Os valores possíveis são:<br>-Progresso<br>- Saída<br>- Aviso<br>- Erro<br>- Depuração<br>- Verboso|
|Tempo | Data e hora da execução do trabalho do runbook.|

Ao executar qualquer pesquisa de log que retorne registros de categoria de **JobLogs** ou **JobStreams**, você pode selecionar a exibição **JobLogs** ou **JobStreams** , que exibe um conjunto de blocos Resumindo as atualizações retornadas pela pesquisa.

## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo

A tabela seguinte disponibiliza pesquisas de registos de exemplo para registos de trabalhos que esta solução recolhe.

|Consulta | Descrição|
|----------|----------|
|Localizar trabalhos para ScheduledStartStop_Parent de runbook que foram concluídos com êxito | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Localizar trabalhos para SequencedStartStop_Parent de runbook que foram concluídos com êxito | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="viewing-the-solution"></a>Exibindo a solução

Para acessar a solução, navegue até sua conta de automação, selecione **espaço de trabalho** em **recursos relacionados**. Na página log Analytics, selecione **soluções** em **geral**. Na página **soluções** , selecione a solução **Start-Stop-VM [Workspace]** na lista.

Selecionar a solução exibe a página de solução **Start-Stop-VM [Workspace]** . Aqui você pode examinar detalhes importantes, como o bloco **StartStopVM** . Como no espaço de trabalho Log Analytics, esse bloco exibe uma contagem e uma representação gráfica dos trabalhos de runbook para a solução que foi iniciada e concluída com êxito.

![Página de solução de Gerenciamento de Atualizações de automação](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

A partir daqui, você pode executar uma análise adicional dos registros de trabalho clicando no bloco de rosca. O painel da solução mostra o histórico de trabalhos e as consultas de pesquisa de log predefinidas. Alterne para o portal avançado do log Analytics para pesquisar com base em suas consultas de pesquisa.

## <a name="configure-email-notifications"></a>Configurar as notificações por e-mail

Para alterar as notificações por email depois que a solução for implantada, modifique o grupo de ações que foi criado durante a implantação.  

> [!NOTE]
> As assinaturas na nuvem do Azure governamental não dão suporte à funcionalidade de email dessa solução.

No portal do Azure, navegue até os grupos de ação monitor->. Selecione o grupo de ações intitulado **StartStop_VM_Notication**.

![Página de solução de Gerenciamento de Atualizações de automação](media/automation-solution-vm-management/azure-monitor.png)

Na página **StartStop_VM_Notification** , clique em **Editar detalhes** em **detalhes**. Isso abre a página **email/SMS/Push/voz** . Atualize o endereço de email e clique em **OK** para salvar suas alterações.

![Página de solução de Gerenciamento de Atualizações de automação](media/automation-solution-vm-management/change-email.png)

Como alternativa, você pode adicionar outras ações ao grupo de ações para saber mais sobre grupos de ações, consulte [grupos de ações](../azure-monitor/platform/action-groups.md)

Este é um email de exemplo que é enviado quando a solução desliga as máquinas virtuais.

![Página de solução de Gerenciamento de Atualizações de automação](media/automation-solution-vm-management/email.png)

## <a name="add-exclude-vms"></a>Adicionar/excluir VMs

A solução fornece a capacidade de adicionar VMs para serem direcionadas pela solução ou especificamente excluir computadores da solução.

### <a name="add-a-vm"></a>Adicionar uma VM

Há algumas opções que você pode usar para garantir que uma VM seja incluída na solução iniciar/parar quando for executada.

* Cada um dos [runbooks](#runbooks) pai da solução tem um parâmetro **VMList** . Você pode passar uma lista separada por vírgulas de nomes de VM para esse parâmetro ao agendar o runbook pai apropriado para sua situação e essas VMs serão incluídas quando a solução for executada.

* Para selecionar várias VMs, defina o **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames** com os nomes do grupo de recursos que contêm as VMs que você deseja iniciar ou parar. Você também pode definir esse valor como `*`para que a solução seja executada em todos os grupos de recursos na assinatura.

### <a name="exclude-a-vm"></a>Excluir uma VM

Para excluir uma VM da solução, você pode adicioná-la à variável **External_ExcludeVMNames** . Essa variável é uma lista separada por vírgulas de VMs específicas a serem excluídas da solução iniciar/parar. Essa lista é limitada a 140 VMs. Se você adicionar mais de 140 VMs a essa lista separada por vírgulas, as VMs definidas como excluídas poderão ser iniciadas ou interrompidas inadvertidamente.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Modificar as agendas de inicialização e desligamento

O gerenciamento de agendas de inicialização e desligamento nesta solução segue as mesmas etapas descritas em [agendar um runbook na automação do Azure](automation-schedules.md). Precisa haver uma agenda separada para iniciar e parar as VMs.

Há suporte para a configuração da solução para interromper apenas as VMs em um determinado momento. Nesse cenário, você apenas cria um agendamento de **interrupção** e nenhum **início** agendado correspondente. Para efetuar este procedimento, tem de:

1. Verifique se você adicionou os grupos de recursos para que as VMs sejam desligadas na variável **External_Stop_ResourceGroupNames** .
2. Crie sua própria agenda para o momento em que deseja desligar as VMs.
3. Navegue até o **ScheduledStartStop_Parent** runbook e clique em **agendar**. Isso permite que você selecione a agenda criada na etapa anterior.
4. Selecione **parâmetros e configurações de execução** e defina o parâmetro ação como "parar".
5. Clique em **OK** para guardar as alterações.

## <a name="update-the-solution"></a>Atualizar a solução

Se você tiver implantado uma versão anterior dessa solução, deverá primeiro excluí-la da sua conta antes de implantar uma versão atualizada. Siga as etapas para [remover a solução](#remove-the-solution) e, em seguida, siga as etapas acima para [implantar a solução](#deploy-the-solution).

## <a name="remove-the-solution"></a>Remover a solução

Se você decidir que não precisa mais usar a solução, poderá excluí-la da conta de automação. A exclusão da solução remove apenas os runbooks. Ele não exclui as agendas ou variáveis que foram criadas quando a solução foi adicionada. Esses ativos que você precisa excluir manualmente se não estiverem usando-os com outros runbooks.

Para excluir a solução, execute as seguintes etapas:

1. Em sua conta de automação, em **recursos relacionados**, selecione **espaço de trabalho vinculado**.
1. Selecione **ir para o espaço de trabalho**.
1. Em **geral**, selecione **soluções**. 
1. Na página **soluções** , selecione a solução **Start-Stop-VM [Workspace]** . Na página **VMManagementSolution [Workspace]** , no menu, selecione **excluir**.<br><br> ![excluir solução de gerenciamento de VM](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. Na janela **excluir solução** , confirme que você deseja excluir a solução.
1. Embora as informações sejam verificadas e a solução seja excluída, você pode acompanhar seu progresso em **notificações** no menu. Você voltará para a página **soluções** depois que o processo para remover a solução for iniciado.

A conta de automação e o espaço de trabalho Log Analytics não são excluídos como parte desse processo. Se não quiser manter o espaço de trabalho Log Analytics, você precisará excluí-lo manualmente. Isso pode ser feito no portal do Azure:

1. Em portal do Azure, procure e selecione **espaços de trabalho do log Analytics**.
1. Na página **log Analytics espaços de trabalho** , selecione o espaço de trabalho.
1. Selecione **excluir** no menu da página Configurações do espaço de trabalho.

Se não quiser manter os componentes da conta de automação do Azure, você poderá excluir cada um manualmente. Para obter a lista de runbooks, variáveis e agendas criadas pela solução, consulte os [componentes da solução](#solution-components).

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre como construir diferentes consultas de pesquisa e examinar os logs de trabalho de automação com logs de Azure Monitor, consulte [pesquisas de log em logs de Azure monitor](../log-analytics/log-analytics-log-searches.md).
- Para saber mais sobre a execução de runbooks, como monitorizar tarefas de runbooks e outros detalhes técnicos, veja [Acompanhar uma tarefa de runbook](automation-runbook-execution.md).
- Para saber mais sobre os logs de Azure Monitor e fontes de coleta de dados, consulte [visão geral sobre coleta de dados do armazenamento do Azure em logs de Azure monitor](../azure-monitor/platform/collect-azure-metrics-logs.md).
