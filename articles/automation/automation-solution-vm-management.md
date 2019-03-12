---
title: Iniciar/parar VMs durante a solução de horário comercial
description: Esta solução de gestão de VMS inicia e para as suas máquinas de virtuais do Azure Resource Manager com base numa agenda e monitoriza proativamente a partir de registos do Azure Monitor.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 22347ce7296dc55d98f1ee6d4458fa6d7c5a21e6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551294"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Iniciar/parar VMs durante a solução de horário comercial na automatização do Azure

A iniciar/parar VMs fora do horário comercial solução inicia e interrompe as máquinas virtuais do Azure em agendas definidas pelo utilizador, fornece informações através de registos do Azure Monitor e envia e-mails opcionais ao utilizar [grupos de ação](../azure-monitor/platform/action-groups.md). Ele oferece suporte do Azure Resource Manager e as VMs clássicas na maioria dos cenários.

Esta solução fornece uma opção de automatização descentralizada de baixo custo para os utilizadores que queiram otimizar seus custos VM. Com esta solução, pode:

- Agendar VMs para iniciar e parar.
- Agendar VMs para iniciar e parar em ordem ascendente através de etiquetas do Azure (não suportado para VMs clássicas).
- VMs de AUTOSTOP com base na utilização de CPU baixa.

Seguem-se limitações para a solução atual:

- Essa solução gerencia VMs em qualquer região, mas só pode ser utilizada na mesma subscrição como a sua conta de automatização do Azure.
- Esta solução está disponível no Azure e AzureGov para qualquer região que suporte uma área de trabalho do Log Analytics, uma conta de automatização do Azure e alertas. Regiões de AzureGov atualmente não suportam a funcionalidade de correio eletrónico.

> [!NOTE]
> Se estiver a utilizar a solução para as VMs clássicas, em seguida, todas as suas VMs serão processadas sequencialmente por serviço cloud. Máquinas virtuais ainda são processadas em paralelo em serviços cloud diferentes.
>
> Subscrições do fornecedor de soluções Cloud (Azure CSP) do Azure suportam apenas o modelo Azure Resource Manager, serviços de não - Azure Resource Manager não estão disponíveis no programa. Quando executa a solução iniciar/parar poderá receber erros porque esta tem cmdlets para gerir recursos clássicos. Para saber mais sobre o CSP, veja [serviços disponíveis em subscrições de CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments). Se utilizar uma subscrição do CSP, deve modificar a [ **External_EnableClassicVMs** ](#variables) variável à **False** após a implementação.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Os runbooks para esta solução funcionam com um [conta Run As do Azure](automation-create-runas-account.md). A conta Run As é o método de autenticação preferencial, porque utiliza a autenticação de certificado em vez de uma palavra-passe que pode expirar ou ser alteradas frequentemente.

Recomenda-se para utilizar uma conta de automatização separada para a solução iniciar/parar VM. Isso ocorre porque versões do módulo do Azure são atualizadas com frequência e seus parâmetros podem ser alterados. A solução iniciar/parar VM não está atualizada sobre a cadência da mesma, para que ele poderá não funcionar com as versões mais recentes de cmdlets que utiliza. Recomenda-se de testar atualizações do módulo num teste de conta de automatização antes de importá-los na sua conta de automatização de produção.

## <a name="deploy-the-solution"></a>Implementar a solução

Execute os seguintes passos para adicionar a iniciar/parar VMs durante a solução de horário comercial à sua conta de automatização e, em seguida, configure as variáveis para personalizar a solução.

1. A partir de uma conta de automatização, selecione **iniciar/parar VM** sob **recursos relacionados**. A partir daqui, pode clicar **Saiba mais sobre e ative a solução**. Se já tiver uma solução iniciar/parar VM implementada, pode selecioná-lo ao clicar em **gerir a solução** e procurá-la na lista.

   ![Ativar a partir de conta de automatização](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Também pode criá-la em qualquer lugar no portal do Azure, ao clicar em **criar um recurso**. Na página do Marketplace, escreva uma palavra-chave, como **começar** ou **iniciar/parar**. À medida que começa a escrever, a lista filtra com base na sua entrada. Em alternativa, pode digitar um ou mais palavras-chave do nome completo da solução e, em seguida, prima Enter. Selecione **iniciar/parar VMs fora do horário comercial** resultados da pesquisa.
2. Na **iniciar/parar VMs fora do horário comercial** página da solução selecionada, reveja as informações de resumidas e, em seguida, clique em **criar**.

   ![Portal do Azure](media/automation-solution-vm-management/azure-portal-01.png)

3. O **Adicionar solução** é apresentada a página. São-lhe pedido para configurar a solução antes de poder importá-lo na sua subscrição da automatização.

   ![Página Adicionar solução de gestão VM](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

4. Sobre o **Adicionar solução** página, selecione **área de trabalho**. Selecione uma área de trabalho do Log Analytics que está ligada à mesma subscrição do Azure que a conta de automatização faz parte. Se não tiver uma área de trabalho, selecione **criar nova área de trabalho**. Sobre o **área de trabalho do Log Analytics** página, execute os seguintes passos:
   - Especifique um nome para a nova **área de trabalho do Log Analytics**, por exemplo, "ContosoLAWorkspace".
   - Selecione um **subscrição** para ligar ao escolher na lista pendente, se a predefinição selecionada não é apropriada.
   - Para **grupo de recursos**, pode criar um novo grupo de recursos ou selecione um existente.
   - Selecione uma **Localização**. Atualmente, as únicas localizações disponíveis são **Sudeste da Austrália**, **Canadá Central**, **Índia Central**, **E.U.A. Leste**, **Leste do Japão**, **Sudeste asiático**, **sul do Reino Unido**, **Europa Ocidental**, e **E.U.A. oeste 2**.
   - Selecione um **Escalão de preço**. Escolha o **por GB (autónomo)** opção. Registos de Monitor do Azure foi atualizado [preços](https://azure.microsoft.com/pricing/details/log-analytics/) e o escalão por GB é a única opção.

5. Depois de fornecer as informações necessárias sobre o **área de trabalho do Log Analytics** página, clique em **criar**. Pode acompanhar o progresso em **notificações** no menu, que retorna ao **Adicionar solução** página quando tiver terminado.
6. Sobre o **Adicionar solução** página, selecione **conta de automatização**. Se estiver a criar uma nova área de trabalho do Log Analytics, pode criar uma nova conta de automatização a ser associado ele ou selecione uma conta de automatização existente que já não está ligada a uma área de trabalho do Log Analytics. Selecione uma conta de automatização existente ou clique em **criar uma conta de automatização**e, no **adicionar conta de automatização** página, forneça as seguintes informações:
   - No campo **Nome**, introduza o nome da conta de Automatização.

    Todas as outras opções são preenchidas automaticamente com base na área de trabalho da Log Analytics selecionada. Estas opções não podem ser modificadas. O método de autenticação predefinido para os runbooks incluídos nesta solução é a conta Run As do Azure. Depois de clicar em **OK**, as opções de configuração são validadas e a conta de automatização é criada. Pode acompanhar o progresso em **Notificações**, no menu.

7. Por fim, sobre o **Adicionar solução** página, selecione **configuração**. O **parâmetros** é apresentada a página.

   ![Página de parâmetros para a solução](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Aqui, lhe for pedido para:
   - Especifique a **nomes de ResourceGroup de destino**. Estes valores são os nomes de grupo de recursos que contêm as VMs a ser geridas por esta solução. Pode introduzir mais de um nome e separar cada um com uma vírgula (valores não diferenciam maiúsculas de minúsculas). Se quiser segmentar todas as VMs em todos os grupos de recursos da subscrição, a utilização de um caráter universal é suportada. Este valor é armazenado no **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames** variáveis.
   - Especifique a **lista de exclusões de VM (cadeia)**. Este valor é o nome de um ou mais máquinas virtuais do grupo de recursos de destino. Pode introduzir mais de um nome e separar cada um com uma vírgula (valores não diferenciam maiúsculas de minúsculas). A utilização de um caráter universal é suportada. Este valor é armazenado no **External_ExcludeVMNames** variável.
   - Selecione um **agenda**. Este valor é uma data e hora recorrente para iniciar e parar as VMs em grupos de recursos de destino. Por predefinição, a agenda está configurada para 30 minutos a partir de agora. Selecionar uma região diferente não está disponível. Para configurar a agenda para o seu fuso horário específico depois de configurar a solução, consulte [modificar a agenda de arranque e encerramento](#modify-the-startup-and-shutdown-schedules).
   - Para receber **notificações por E-Mail** de um grupo de ação, aceite o valor predefinido **Sim** e fornecer um endereço de e-mail válido. Se selecionou **não** mas decidir posteriormente que pretende receber notificações por e-mail, pode atualizar o [grupo de ação](../azure-monitor/platform/action-groups.md) que é criada com endereços de e-mail válidos separados por vírgulas. Também tem de ativar as seguintes regras de alerta:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > O valor predefinido para **nomes de ResourceGroup de destino** é um **&ast;**. Isto destina-se todas as VMs numa subscrição. Se não pretender que a solução para todas as VMs na sua subscrição, que este valor tem de ser atualizado para uma lista de nomes de grupo de recursos antes de ativar as agendas de destino.

8. Depois de ter configurado as definições iniciais necessárias para a solução, clique em **OK** para fechar a **parâmetros** página e selecione **criar**. Depois de todas as definições são validadas, a solução é implementada na sua subscrição. Este processo pode demorar vários segundos a concluir, e pode acompanhar o progresso em **notificações** no menu.

> [!NOTE]
> Se tiver uma subscrição do fornecedor de soluções de Cloud do Azure (Azure CSP), após a implementação estiver concluída, na sua conta de automatização, aceda a **variáveis** sob **recursos partilhados** e defina o [ **External_EnableClassicVMs** ](#variables) variável à **falso**. Isso deixa a solução da procura recursos de VM clássica.

## <a name="scenarios"></a>Cenários

A solução contém três cenários distintos. Estes cenários são:

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>Cenário 1: Iniciar/parar VMs com base numa agenda

Este cenário é a configuração predefinida quando implantar a solução pela primeira vez. Por exemplo, pode configurar a sua paragem todas as VMs numa subscrição quando deixar de trabalho durante a noite e iniciá-los da manhã, quando estiver no escritório. Ao configurar as agendas **agendada-StartVM** e **agendada StopVM** durante a implementação, iniciar e parar as VMs de destinadas. Configurar esta solução para parar a VMs apenas é suportado, consulte [modificar as agendas de inicialização e desligamento](#modify-the-startup-and-shutdown-schedules) para saber como configurar uma agenda personalizada.

> [!NOTE]
> O fuso horário é seu fuso horário atual quando configurar o parâmetro de tempo da agenda. No entanto, ele é armazenado no formato UTC na automatização do Azure. Não tem de fazer qualquer conversão do fuso horário como isso é manipulado durante a implantação.

Controle que as VMs estão no âmbito ao configurar as seguintes variáveis: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**, e **External_ExcludeVMNames**.

Pode habilitar o direcionamento a ação em relação a uma subscrição e grupo de recursos ou visando uma lista específica de VMs, mas não ambos.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>As ações de início e fim em relação a um grupo de recursos e subscrição de destino

1. Configurar o **External_Stop_ResourceGroupNames** e **External_ExcludeVMNames** variáveis para especificar o destino de VMs.
2. Ativar e atualizar o **agendada-StartVM** e **agendada StopVM** agendas.
3. Executar o **ScheduledStartStop_Parent** runbook com o parâmetro de ação definido como **iniciar** e o parâmetro WHATIF definido como **verdadeiro** para pré-visualizar as alterações.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>A ação de início e fim de destino pela lista VM

1. Executar o **ScheduledStartStop_Parent** runbook com o parâmetro de ação definido como **iniciar**, adicione uma lista separada por vírgulas de VMs no *VMList* parâmetro e, em seguida, defina o Parâmetro WHATIF **True**. Pré-visualize as suas alterações.
1. Configurar o **External_ExcludeVMNames** parâmetro com uma lista separada por vírgulas de VMs (VM1, VM2, VM3).
1. Este cenário não considera os **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupnames** variáveis. Para este cenário, terá de criar seu próprio plano de automatização. Para obter detalhes, consulte [agendar um runbook na automatização do Azure](../automation/automation-schedules.md).

> [!NOTE]
> O valor para **nomes de ResourceGroup de destino** é armazenado como o valor para ambos **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames**. Para obter mais granularidade, pode modificar cada uma dessas variáveis para diferentes grupos de recursos de destino. Para a ação de início, utilize **External_Start_ResourceGroupNames**e para a ação de parar, utilize **External_Stop_ResourceGroupNames**. As VMs são adicionadas automaticamente ao início e interromper agendas.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>Cenário 2: Iniciar/parar VMS em sequência através de etiquetas

Num ambiente que inclua dois ou mais componentes em várias VMs suportar uma carga de trabalho distribuída, que suporta a sequência em que os componentes são iniciados e parados na ordem é importante. Este cenário pode ser feito, efetuando os seguintes passos:

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>As ações de início e fim em relação a um grupo de recursos e subscrição de destino

1. Adicionar uma **sequencestart** e uma **sequencestop** etiqueta com um valor de número inteiro positivo para VMs que são visados **External_Start_ResourceGroupNames** e  **External_Stop_ResourceGroupNames** variáveis. As ações de início e fim são efetuadas por ordem ascendente. Para saber como Etiquetar uma VM, veja [Etiquetar uma Máquina Virtual do Windows no Azure](../virtual-machines/windows/tag.md) e [Etiquetar uma Máquina Virtual Linux no Azure](../virtual-machines/linux/tag.md).
1. Modificar as agendas **Sequenced-StartVM** e **Sequenced StopVM** para a data e hora que cumprem os requisitos e ativar o agendamento.
1. Executar o **SequencedStartStop_Parent** runbook com o parâmetro de ação definido como **iniciar** e o parâmetro WHATIF definido como **verdadeiro** para pré-visualizar as alterações.
1. A ação de pré-visualização e faça as alterações necessárias antes de implementar em VMs de produção. Quando pronto, manualmente executar o runbook com o parâmetro definido como **False**, ou permitir que a agenda da automatização **Sequenced-StartVM** e **Sequenced StopVM** executar automaticamente após a sua agenda prescrita.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>A ação de início e fim de destino pela lista VM

1. Adicionar uma **sequencestart** e uma **sequencestop** etiqueta com um valor de número inteiro positivo para VMs que planeia adicionar para o **VMList** parâmetro.
1. Executar o **SequencedStartStop_Parent** runbook com o parâmetro de ação definido como **iniciar**, adicione uma lista separada por vírgulas de VMs no *VMList* parâmetro e, em seguida, defina o Parâmetro WHATIF **True**. Pré-visualize as suas alterações.
1. Configurar o **External_ExcludeVMNames** parâmetro com uma lista separada por vírgulas de VMs (VM1, VM2, VM3).
1. Este cenário não considera os **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupnames** variáveis. Para este cenário, terá de criar seu próprio plano de automatização. Para obter detalhes, consulte [agendar um runbook na automatização do Azure](../automation/automation-schedules.md).
1. A ação de pré-visualização e faça as alterações necessárias antes de implementar em VMs de produção. Quando pronto, manualmente executar a monitorização-e-diagnóstico/monitorização-ação-groupsrunbook com o parâmetro definido como **False**, ou permitir que a agenda da automatização **Sequenced-StartVM** e **Sequenced StopVM** executar automaticamente após a sua agenda prescrita.

### <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a>Cenário 3: Iniciar/parar automaticamente com base na utilização da CPU

Esta solução pode ajudar a gerenciar os custos de máquinas virtuais em execução na sua subscrição ao avaliar as VMs do Azure que não são utilizadas durante períodos de pico, tal como após horas, e automaticamente encerrá-los se a utilização do processador é less than x %.

Por predefinição, a solução é pré-configurado para avaliar a métrica de CPU de percentagem para ver se a utilização média é 5 por cento ou menos. Este cenário é controlado pelas seguintes variáveis e pode ser modificado se os valores predefinidos não cumprem os requisitos:

- External_AutoStop_MetricName
- External_AutoStop_Threshold
- External_AutoStop_TimeAggregationOperator
- External_AutoStop_TimeWindow

Pode habilitar o direcionamento a ação em relação a uma subscrição e grupo de recursos ou visando uma lista específica de VMs, mas não ambos.

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>A ação de parada em relação a um grupo de recursos e subscrição de destino

1. Configurar o **External_Stop_ResourceGroupNames** e **External_ExcludeVMNames** variáveis para especificar o destino de VMs.
1. Ativar e atualizar o **Schedule_AutoStop_CreateAlert_Parent** agenda.
1. Executar o **AutoStop_CreateAlert_Parent** runbook com o parâmetro de ação definido como **iniciar** e o parâmetro WHATIF definido como **verdadeiro** para pré-visualizar as alterações.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>A ação de início e fim de destino pela lista VM

1. Executar o **AutoStop_CreateAlert_Parent** runbook com o parâmetro de ação definido como **iniciar**, adicione uma lista separada por vírgulas de VMs no *VMList* parâmetro e, em seguida, defina o Parâmetro WHATIF **True**. Pré-visualize as suas alterações.
1. Configurar o **External_ExcludeVMNames** parâmetro com uma lista separada por vírgulas de VMs (VM1, VM2, VM3).
1. Este cenário não considera os **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupnames** variáveis. Para este cenário, terá de criar seu próprio plano de automatização. Para obter detalhes, consulte [agendar um runbook na automatização do Azure](../automation/automation-schedules.md).

Agora que tem uma agenda para a parar VMs com base na utilização da CPU, tem de ativar uma das seguintes agendas iniciá-las.

- Ação de início de destino através da subscrição e grupo de recursos. Veja os passos em [cenário 1](#scenario-1-startstop-vms-on-a-schedule) para teste e ativação **agendada-StartVM** agendas.
- Destino iniciar ação por subscrição, grupo de recursos e etiqueta. Veja os passos em [cenário 2](#scenario-2-startstop-vms-in-sequence-by-using-tags) para teste e ativação **Sequenced-StartVM** agendas.

## <a name="solution-components"></a>Componentes da solução

Esta solução inclui pré-configurada runbooks, agendas e integração com os registos do Azure Monitor, pelo que pode personalizar o arranque e encerramento das suas máquinas virtuais de acordo com as necessidades da sua empresa.

### <a name="runbooks"></a>Runbooks

A tabela seguinte lista os runbooks implementados à sua conta de automatização por esta solução. Não faça alterações ao código do runbook. Em vez disso, escreva seu próprio runbook, a nova funcionalidade.

> [!IMPORTANT]
> Não diretamente execute qualquer runbook com "subordinado" anexado ao respetivo nome.

Incluir todos os runbooks do principal do _WhatIf_ parâmetro. Quando definido como **True**, _WhatIf_ suporta com detalhes sobre o comportamento exato que o runbook toma quando executado sem o _WhatIf_ parâmetro e valida o correto VMs estão a ser direcionada. Um runbook só executa as ações definidas quando o _WhatIf_ parâmetro estiver definido como **falso**.

|Runbook | Parâmetros | Descrição|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Chamada do runbook principal. Este runbook cria alertas numa base por recurso para o cenário de AutoStop.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: VERDADEIRO ou FALSO  | Cria ou atualiza as regras de alerta do Azure nas VMs nos grupos de subscrição ou ao recurso de destino. <br> VMList: Lista separada por vírgulas de VMs. Por exemplo, _vm1, vm2, vm3_.<br> *WhatIf* valida a lógica de runbook sem executar.|
|AutoStop_Disable | nenhum | Desativa AutoStop alertas e a agenda predefinida.|
|AutoStop_StopVM_Child | WebHookData | Chamada do runbook principal. Regras de alerta chamam este runbook para parar a VM.|
|Bootstrap_Main | nenhum | Utilizado uma vez para definir configurações de arranque do sistema, tais como webhookURI, que normalmente não são acessíveis a partir do Azure Resource Manager. Este runbook é removido automaticamente após a implementação com êxito.|
|ScheduledStartStop_Child | VMName <br> Ação: Iniciar ou parar <br> ResourceGroupName | Chamada do runbook principal. Executa uma ação iniciar ou parar para a parada agendada.|
|ScheduledStartStop_Parent | Ação: Iniciar ou parar <br>VMList <br> WhatIf: VERDADEIRO ou FALSO | Esta definição afeta todas as VMs na subscrição. Editar a **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames** ser executado somente sobre esses grupos de recursos de destino. Também pode excluir VMs específicas ao atualizar o **External_ExcludeVMNames** variável.<br> VMList: Lista separada por vírgulas de VMs. Por exemplo, _vm1, vm2, vm3_.<br> _WhatIf_ valida a lógica de runbook sem executar.|
|SequencedStartStop_Parent | Ação: Iniciar ou parar <br> WhatIf: VERDADEIRO ou FALSO<br>VMList| Criar etiquetas com o nome **sequencestart** e **sequencestop** em cada VM para o qual pretende que a atividade de início/paragem de sequência. Esses nomes de marca diferenciam maiúsculas de minúsculas. O valor da etiqueta deve ser um número inteiro positivo (1, 2, 3) que corresponde à ordem na qual pretende iniciar ou parar. <br> VMList: Lista separada por vírgulas de VMs. Por exemplo, _vm1, vm2, vm3_. <br> _WhatIf_ valida a lógica de runbook sem executar. <br> **Nota**: As VMs devem estar em grupos de recursos definidos como External_Start_ResourceGroupNames External_Stop_ResourceGroupNames e External_ExcludeVMNames em variáveis de automatização do Azure. Têm de ter as etiquetas adequadas para ações entrem em vigor.|

### <a name="variables"></a>Variáveis

A tabela seguinte lista as variáveis criadas na sua conta de automatização. Só deve modificar variáveis com prefixo **externo**. Modificar variáveis com prefixo **interno** faz com que os efeitos indesejáveis.

|Variável | Descrição|
|---------|------------|
|External_AutoStop_Condition | O operador condicional necessário para configurar a condição antes de acionar um alerta. Os valores aceitáveis são **GreaterThan**, **GreaterThanOrEqual**, **LessThan**, e **LessThanOrEqual**.|
|External_AutoStop_Description | O alerta parar a VM se a percentagem de CPU é superior ao limiar.|
|External_AutoStop_MetricName | O nome da métrica de desempenho para o qual a regra de alerta do Azure está a ser configurado.|
|External_AutoStop_Threshold | O limiar para a regra de alerta do Azure especificado na variável _External_AutoStop_MetricName_. Valores de percentagem podem variar entre 1 e 100.|
|External_AutoStop_TimeAggregationOperator | O operador de agregação da hora, que é aplicado para o tamanho da janela selecionados para avaliar a condição. Os valores aceitáveis são **médio**, **mínimo**, **máxima**, **Total**, e **última**.|
|External_AutoStop_TimeWindow | O tamanho da janela durante o qual o Azure analisa as métricas selecionadas para acionar um alerta. Este parâmetro aceita entradas no formato de intervalo de tempo. Valores possíveis são de 5 minutos a seis horas.|
|External_EnableClassicVMs| Especifica se as VMs clássicas são visadas pela solução. O valor predefinido é True. Isso deve ser definido como falso para subscrições de CSP.|
|External_ExcludeVMNames | Introduza nomes de VMS a serem excluídos, separando os nomes com uma vírgula, sem espaços. Isso está limitado a 140 VMs. Se adicionar mais de 140 VMs a esta lista separada por vírgulas, as VMs que estão definidas para ser excluído poderão ser iniciadas ou interrompidas inadvertidamente.|
|External_Start_ResourceGroupNames | Especifica um ou mais grupos de recursos, a separação de valores com uma vírgula, direcionada para ações de início.|
|External_Stop_ResourceGroupNames | Especifica um ou mais grupos de recursos, a separação de valores com uma vírgula, direcionada para ações de paragem.|
|Internal_AutomationAccountName | Especifica o nome da conta de automatização.|
|Internal_AutoSnooze_WebhookUri | Especifica o que URI de Webhook chamado para o cenário de AutoStop.|
|Internal_AzureSubscriptionId | Especifica o ID de subscrição do Azure.|
|Internal_ResourceGroupName | Especifica o nome de grupo de recursos da conta de automatização.|

Em todos os cenários, o **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**, e **External_ExcludeVMNames** variáveis forem necessárias para o direcionamento de VMs, com exceção de fornecer uma lista separada por vírgulas de VMs para o **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**, e  **ScheduledStartStop_Parent** runbooks. Ou seja, as suas VMs tem de residir em grupos de recursos de destino para iniciar e parar as ações para ocorrer. A funciona lógica semelhante para o Azure policy, que pode direcionar o subscrição ou grupo de recursos e ações herdada por VMs criadas recentemente. Esta abordagem evita a necessidade de manter um cronograma separado para cada VM e gerir começa e pára no dimensionamento.

### <a name="schedules"></a>Agendas

A tabela seguinte lista cada uma das agendas predefinidas criadas na sua conta de automatização. Pode modificá-las ou criar seu próprio agendamentos personalizados. Por predefinição, todas as agendas estão desativadas, exceto para **Scheduled_StartVM** e **Scheduled_StopVM**.

Não deve ativar todas as agendas, porque isso pode criar ações de agenda sobrepostos. É melhor determinar quais otimizações que pretende executar e modificar de acordo. Ver os cenários de exemplo na secção Descrição geral para obter mais explicações.

|Nome da agenda | Frequência | Descrição|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Cada 8 horas | Executa o runbook AutoStop_CreateAlert_Parent a cada 8 horas, que por sua vez, para os valores baseados em VM em External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames e External_ExcludeVMNames em variáveis de automatização do Azure. Em alternativa, pode especificar uma lista separada por vírgulas de VMs ao utilizar o parâmetro VMList.|
|Scheduled_StopVM | Utilizador definido diariamente | Executa o runbook Scheduled_Parent com um parâmetro de _parar_ todos os dias, o período de tempo especificado. Para automaticamente todas as VMs que cumpram as regras definidas por meio de variáveis de recurso. Ativar o agendamento de relacionados **agendada-StartVM**.|
|Scheduled_StartVM | Utilizador definido diariamente | Executa o runbook Scheduled_Parent com um parâmetro de _iniciar_ todos os dias, o período de tempo especificado. Inicia automaticamente todas as VMs que cumpram as regras definidas pelas variáveis apropriadas. Ativar o agendamento de relacionados **agendada StopVM**.|
|StopVM sequenciada | 1:00 (UTC), toda sexta-feira | Executa o runbook Sequenced_Parent com um parâmetro de _parar_ toda sexta-feira num momento especificado. Sequencialmente (ascendente) interrompe todas as VMs com uma etiqueta de **SequenceStop** definido por variáveis apropriadas. Para obter mais informações sobre os valores de etiqueta e variáveis de recurso, consulte a secção de Runbooks. Ativar o agendamento de relacionados **Sequenced-StartVM**.|
|Sequenced-StartVM | 1:21 horas (UTC), sempre de segunda a | Executa o runbook Sequenced_Parent com um parâmetro de _iniciar_ sempre de segunda num momento especificado. Sequencialmente (descendente) inicia todas as VMs com uma etiqueta de **SequenceStart** definido por variáveis apropriadas. Para obter mais informações sobre os valores de etiqueta e variáveis de recurso, consulte a secção de Runbooks. Ativar o agendamento de relacionados **Sequenced StopVM**.|

## <a name="azure-monitor-logs-records"></a>Registos de registos de Monitor do Azure

A automatização cria dois tipos de registos na área de trabalho do Log Analytics: registos de tarefas e fluxos de trabalho.

### <a name="job-logs"></a>Registos de trabalhos

|Propriedade | Descrição|
|----------|----------|
|Autor da chamada |  Quem iniciou a operação. Os valores possíveis são um endereço de e-mail ou o sistema para trabalhos agendados.|
|Categoria | Classificação do tipo de dados. Para a Automatização, o valor é JobLogs.|
|CorrelationId | GUID que é o ID de correlação do trabalho do runbook.|
|JobId | GUID que é o ID do trabalho do runbook.|
|operationName | Especifica o tipo de operação efetuada no Azure. Para a automatização, o valor é o trabalho.|
|resourceId | Especifica o tipo de recurso no Azure. Para a Automatização, o valor é a conta de Automatização associada ao runbook.|
|ResourceGroup | Especifica o nome do grupo de recursos do trabalho do runbook.|
|ResourceProvider | Especifica o serviço do Azure que fornece os recursos que pode implementar e gerir. Para a Automatização, o valor é Automatização do Azure.|
|ResourceType | Especifica o tipo de recurso no Azure. Para a Automatização, o valor é a conta de Automatização associada ao runbook.|
|resultType | O estado do trabalho do runbook. Os valores possíveis são:<br>- Iniciado<br>- Parado<br>- Suspenso<br>- Falhado<br>- Bem-sucedido|
|resultDescription | Descreve o estado do resultado do trabalho do runbook. Os valores possíveis são:<br>- Trabalho iniciado<br>- Trabalho falhado<br>- Trabalho Concluído|
|RunbookName | Especifica o nome do runbook.|
|SourceSystem | Especifica o sistema de origem dos dados submetidos. Para a automatização, o valor é OpsManager|
|StreamType | Especifica o tipo de evento. Os valores possíveis são:<br>- Verboso<br>- Saída<br>- Erro<br>- Aviso|
|SubscriptionId | Especifica o ID de subscrição do trabalho.
|Hora | Data e hora da execução do trabalho do runbook.|

### <a name="job-streams"></a>Fluxos de trabalho

|Propriedade | Descrição|
|----------|----------|
|Autor da chamada |  Quem iniciou a operação. Os valores possíveis são um endereço de e-mail ou o sistema para trabalhos agendados.|
|Categoria | Classificação do tipo de dados. Para a Automatização, o valor é JobStreams.|
|JobId | GUID que é o ID do trabalho do runbook.|
|operationName | Especifica o tipo de operação efetuada no Azure. Para a automatização, o valor é o trabalho.|
|ResourceGroup | Especifica o nome do grupo de recursos do trabalho do runbook.|
|resourceId | Especifica o ID de recurso no Azure. Para a Automatização, o valor é a conta de Automatização associada ao runbook.|
|ResourceProvider | Especifica o serviço do Azure que fornece os recursos que pode implementar e gerir. Para a Automatização, o valor é Automatização do Azure.|
|ResourceType | Especifica o tipo de recurso no Azure. Para a Automatização, o valor é a conta de Automatização associada ao runbook.|
|resultType | O resultado do trabalho do runbook no momento em que o evento foi gerado. Um valor possível é:<br>- InProgress|
|resultDescription | Inclui o fluxo de saída do runbook.|
|RunbookName | O nome do runbook.|
|SourceSystem | Especifica o sistema de origem dos dados submetidos. Para a automatização, o valor será OpsManager.|
|StreamType | O tipo de fluxo de trabalho. Os valores possíveis são:<br>-Progresso<br>- Saída<br>- Aviso<br>- Erro<br>- Depuração<br>- Verboso|
|Hora | Data e hora da execução do trabalho do runbook.|

Quando efetua uma pesquisa de registos que devolva registos da categoria da **JobLogs** ou **JobStreams**, pode selecionar o **JobLogs** ou **JobStreams**exibição, que apresenta um conjunto de mosaicos que resumem as atualizações devolvidas pela pesquisa.

## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo

A tabela seguinte disponibiliza pesquisas de registos de exemplo para registos de trabalhos que esta solução recolhe.

|Consulta | Descrição|
|----------|----------|
|Localizar trabalhos para o runbook ScheduledStartStop_Parent que tiver concluído com êxito | ```search Category == "JobLogs" | where ( RunbookName_s == "ScheduledStartStop_Parent" ) | where ( ResultType == "Completed" )  | resumir |AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) | Ordenar por TimeGenerated desc ' '|
|Localizar trabalhos para o runbook SequencedStartStop_Parent que tiver concluído com êxito | ```search Category == "JobLogs" | where ( RunbookName_s == "SequencedStartStop_Parent" ) | where ( ResultType == "Completed" ) | resumir |AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) | Ordenar por TimeGenerated desc ' '|

## <a name="viewing-the-solution"></a>Visualizar a solução

Para acessar a solução, navegue até à sua conta de automatização, selecione **área de trabalho** sob **recursos relacionados**. Na página de análise de registo, selecione **soluções** sob **geral**. Sobre o **soluções** , selecione a solução **Start-Stop-VM [workspace]** da lista.

Selecionar a solução apresenta os **Start-Stop-VM [workspace]** página de solução. Aqui pode ver detalhes importantes, como o **StartStopVM** mosaico. Como na sua área de trabalho do Log Analytics, este mosaico apresenta uma contagem e uma representação gráfica dos trabalhos para a solução de runbook que foram iniciados e tiver concluído com êxito.

![Página de solução de gestão de atualizações de automatização](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

A partir daqui, pode efetuar a análise dos registos de tarefa ao clicar no mosaico de anel. O dashboard de solução mostra o histórico de tarefas e predefinidas consultas de pesquisa de registo. Mude para o portal do log analytics avançadas para pesquisar com base em suas consultas de pesquisa.

## <a name="configure-email-notifications"></a>Configurar notificações por e-mail

Para alterar as notificações por e-mail depois da solução é implementada, modifique o grupo de ação que foi criado durante a implementação.  

> [!NOTE]
> As subscrições na Cloud do Azure Government não suportam a funcionalidade de e-mail desta solução.

No portal do Azure, navegue para o Monitor -> grupos de ação. Selecione o grupo de ação intitulado **StartStop_VM_Notication**.

![Página de solução de gestão de atualizações de automatização](media/automation-solution-vm-management/azure-monitor.png)

Sobre o **StartStop_VM_Notification** página, clique em **editar detalhes** sob **detalhes**. Esta ação abre o **E-Mail/SMS/Push/voz** página. Atualizar o endereço de e-mail e clique em **OK** para guardar as alterações.

![Página de solução de gestão de atualizações de automatização](media/automation-solution-vm-management/change-email.png)

Em alternativa pode adicionar ações adicionais para o grupo de ação, para saber mais sobre grupos de ação, consulte [grupos de ação](../azure-monitor/platform/action-groups.md)

Segue-se uma mensagem de e-mail de exemplo que é enviada quando a solução encerra as máquinas virtuais.

![Página de solução de gestão de atualizações de automatização](media/automation-solution-vm-management/email.png)

## <a name="add-exclude-vms"></a>Adicionar/excluir VMs

A solução fornece a capacidade de adicionar VMs a ser visada pela solução ou excluir especificamente máquinas da solução.

### <a name="add-a-vm"></a>Adicionar uma VM

Existem algumas opções que pode utilizar para se certificar de que uma VM está incluída na solução iniciar/parar quando é executada.

* Cada um dos pai [runbooks](#runbooks) da solução têm um **VMList** parâmetro. Pode passar uma lista separada por vírgulas de nomes VM para este parâmetro quando agendar o runbook principal apropriado para sua situação e estas VMs será incluído quando a solução é executada.

* Para selecionar várias VMs, defina o **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames** com os nomes de grupo de recursos que contêm as VMs que pretende iniciar ou parar. Também pode definir este valor `*`, para que a solução executada em todos os grupos de recursos na subscrição.

### <a name="exclude-a-vm"></a>Excluir uma VM

Para excluir uma VM da solução, pode adicioná-lo para o **External_ExcludeVMNames** variável. Esta variável é uma lista separada por vírgulas de VMs específicas para impedir que a solução iniciar/parar. Esta lista está limitada a 140 VMs. Se adicionar mais de 140 VMs a esta lista separada por vírgulas, as VMs que estão definidas para ser excluído poderão ser iniciadas ou interrompidas inadvertidamente.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Modificar as agendas de inicialização e desligamento

Gerir os agendamentos de arranque e encerramento nesta solução segue os mesmos passos conforme descrito na [agendar um runbook na automatização do Azure](automation-schedules.md). Deve haver um cronograma separado para iniciar e parar VMs.

Configuração da solução para simplesmente parar VMs num determinado período de tempo é suportada. Neste cenário que acabou de criar uma **parar** agenda e não correspondente **iniciar** agendada. Para efetuar este procedimento, tem de:

1. Certifique-se de ter adicionado os grupos de recursos para as VMs encerrar dentro de **External_Stop_ResourceGroupNames** variável.
2. Crie seu próprio agendamento para o tempo que pretende encerrar as VMs.
3. Navegue para o **ScheduledStartStop_Parent** runbook e clique em **agenda**. Isto permite-lhe selecionar a agenda que criou no passo anterior.
4. Selecione **parâmetros e definições de execução** e defina o parâmetro de ação para "Stop".
5. Clique em **OK** para guardar as alterações.

## <a name="update-the-solution"></a>Atualizar a solução

Se tiver implementado uma versão anterior desta solução, primeiro elimine-o partir da sua conta antes de implementar uma versão atualizada. Siga os passos para [remover a solução](#remove-the-solution) e, em seguida, siga os passos acima para [implantar a solução](#deploy-the-solution).

## <a name="remove-the-solution"></a>Remover a solução

Se decidir que já não tem de utilizar a solução, pode eliminá-la a partir da conta de automatização. A eliminar a solução, remova apenas os runbooks. Não elimina o agendas ou variáveis que foram criadas quando a solução foi adicionada. Esses ativos, que terá de eliminar manualmente se não estiver a utilizá-los com outros runbooks.

Para eliminar a solução, execute os seguintes passos:

1. A partir da sua conta de automatização, selecione **área de trabalho** da página da esquerda.
1. Sobre o **soluções** , selecione a solução **Start-Stop-VM [Workspace]**. Sobre o **VMManagementSolution [Workspace]** página, no menu, selecione **eliminar**.<br><br> ![Eliminar solução de gerenciamento VM](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. Na **Eliminar solução** janela, confirme que pretende eliminar a solução.
1. Enquanto as informações são confirmadas e a solução é eliminada, pode acompanhar o progresso em **notificações** no menu. É reencaminhado para o **soluções** página depois do processo para remover a solução é iniciado.

A conta de automatização e a área de trabalho do Log Analytics não são eliminados como parte deste processo. Se não pretender reter a área de trabalho do Log Analytics, terá de eliminar manualmente. Pode fazê-lo do portal do Azure:

1. A partir do ecrã principal portal do Azure, selecione **áreas de trabalho do Log Analytics**.
1. Sobre o **áreas de trabalho do Log Analytics** , selecione a área de trabalho.
1. Selecione **eliminar** no menu na página de definições de área de trabalho.

Se não pretender reter os componentes de conta de automatização do Azure, pode eliminar manualmente cada um. Para obter a lista de runbooks, variáveis e agendas criadas pela solução, consulte a [componentes da solução](#solution-components).

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre como construir consultas de pesquisa diferentes e rever os registos da tarefa de automatização com os registos do Azure Monitor, consulte [pesquisas de registos nos registos do Azure Monitor](../log-analytics/log-analytics-log-searches.md).
- Para saber mais sobre a execução de runbooks, como monitorizar tarefas de runbooks e outros detalhes técnicos, veja [Acompanhar uma tarefa de runbook](automation-runbook-execution.md).
- Para saber mais sobre os registos do Azure Monitor e origens de recolha de dados, veja [descrição geral de registos de dados do armazenamento do Azure a recolher no Azure Monitor](../azure-monitor/platform/collect-azure-metrics-logs.md).
