---
title: Ativar os VMs de arranque/paragem de automatização azure durante o horário de folga
description: Este artigo diz como ativar os VMs de início/paragem durante o período de folga para os seus VMs Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: dde2c3e4cf496bb15ca91c72d9a41936af7051c5
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743761"
---
# <a name="enable-startstop-vms-during-off-hours"></a>Ativar VMs de arranque/paragem durante o horário de folga

Execute os passos neste tópico em sequência para ativar os VMs de início/paragem durante o off-hours para VMs usando uma conta de Automação nova ou existente e espaço de trabalho ligado ao Log Analytics. Depois de concluir o processo de configuração, configure as variáveis para personalizar a funcionalidade.

>[!NOTE]
>Para utilizar esta funcionalidade com VMs clássicos, precisa de uma conta Classic Run As, que não é criada por defeito. Ver [Criar uma corrida clássica Como conta](automation-create-standalone-account.md#create-a-classic-run-as-account).
>

## <a name="create-resources-for-the-feature"></a>Criar recursos para a funcionalidade

1. Inscreva-se no [portal](https://portal.azure.com)Azure.
2. Procure e selecione **Contas de Automação**.
3. Na página Contas de Automação, selecione a sua conta De automação na lista.
4. Na conta Automation, selecione **Start/Stop VM** em **Recursos Relacionados**. A partir daqui, pode clicar **em Saber mais sobre e ativar a solução**. Se já tiver a funcionalidade implementada, pode clicar em **Gerir a solução** e encontrá-la na lista.

   ![Ativar a partir da conta de automação](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Também pode criar o recurso a partir de qualquer lugar do portal Azure, clicando em **Criar um recurso.** Na página marketplace, digite uma palavra-chave como **Iniciar** ou **Iniciar/Parar**. À medida que começa a escrever, a lista filtra com base na sua entrada. Em alternativa, pode escrever uma ou mais palavras-chave a partir do nome completo da funcionalidade e, em seguida, prima **Enter**. Selecione **VMs iniciar/parar durante** o horário de folga a partir dos resultados da pesquisa.

5. Nos VMs iniciar/parar durante a página off-hours para a implementação selecionada, reveja as informações sumárias e, em seguida, clique em **Criar**.

   ![Portal do Azure](media/automation-solution-vm-management/azure-portal-01.png)

## <a name="configure-the-feature"></a>Configurar a funcionalidade

Com o recurso criado, aparece a página Add Solution. É-lhe pedido que configure a funcionalidade antes de a importar para a sua subscrição de Automação. Consulte [os VMs de arranque/paragem de configuração durante as horas de folga](automation-solution-vm-management-config.md).

   ![Página de Adição de Soluções de Adição de Gestão VM](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

## <a name="select-a-log-analytics-workspace"></a>Selecione um espaço de trabalho log Analytics

1. Na página Adicionar Solução, selecione **Workspace**. Selecione um espaço de trabalho Log Analytics ligado à subscrição Azure utilizada pela conta Automation. 

2. Se não tiver um espaço de trabalho, selecione **Criar novo espaço**de trabalho . Na página do espaço de trabalho Log Analytics, execute os seguintes passos:

   - Especifique um nome para o novo espaço de trabalho log analytics, como **ContosoLAWorkspace**.
   - Selecione uma **Subscrição** para ligar selecionando a partir da lista de dropdown, se o padrão selecionado não for apropriado.
   - Para **o Grupo de Recursos,** pode criar um novo grupo de recursos ou selecionar um existente.
   - Selecione um **Local**.
   - Selecione um **nível de preços**. Escolha a opção **Per GB (Autónoma).** Os registos do Monitor Azure têm [preços](https://azure.microsoft.com/pricing/details/log-analytics/) atualizados e o nível Per GB é a única opção.

   > [!NOTE]
   > Ao ativar funcionalidades, apenas certas regiões são suportadas para ligar um espaço de trabalho log Analytics e uma conta de Automação. Para obter uma lista dos pares de mapeamento suportados, consulte [O mapeamento da Região para a conta de Automação e o espaço de trabalho log Analytics](how-to/region-mappings.md).

3. Depois de fornecer as informações necessárias na página do espaço de trabalho Log Analytics, clique em **Criar**. Pode acompanhar o seu progresso no âmbito de **Notificações** do menu, que o devolve à página Add Solution quando feito.

## <a name="add-automation-account"></a>Adicionar conta de Automação

Aceda novamente à página Add Solution e **selecione a conta Automation**. Pode selecionar uma conta de Automação existente que ainda não está ligada a um espaço de trabalho do Log Analytics. Se estiver a criar um novo espaço de trabalho log Analytics, pode criar uma nova conta Automation para se associar ao mesmo. Selecione uma conta de Automação existente ou clique **Em Criar uma conta De Automatização**, e na página da conta Add Automation, forneça o nome da conta Automation no campo **Nome.**

Todas as outras opções são automaticamente povoadas, com base no espaço de trabalho do Log Analytics selecionado. Não pode modificar estas opções. Uma execução azure Como conta é o método de autenticação padrão para os livros de execução incluídos com a funcionalidade. 

Depois de clicar em **OK,** as opções de configuração são validadas e a conta Automation é criada. Pode acompanhar o progresso em **Notificações**, no menu.

## <a name="define-feature-parameters"></a>Definir parâmetros de recurso

1. Na página Adicionar Solução, **selecione Configuração**. A página dos Parâmetros aparece.

    ![Página de parâmetros para solução](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

2. Especifique um valor para o campo Nomes do Grupo de **Recursos Alvo.** O campo define nomes de grupo que contêm VMs para a funcionalidade gerir. Pode introduzir mais do que um nome e separar os nomes usando vírgulas (os valores não são sensíveis aos casos). Se quiser segmentar todas as VMs em todos os grupos de recursos da subscrição, a utilização de um caráter universal é suportada. Os valores são armazenados nas `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` e variáveis.

    > [!IMPORTANT]
    > O valor padrão para nomes do **Grupo de Recursos-Alvo** é **&ast;** a . Esta definição visa todos os VMs numa subscrição. Se não quiser que a funcionalidade tenha como alvo todos os VMs da sua subscrição, deve fornecer uma lista de nomes de grupos de recursos antes de selecionar um horário.
  
3. Especifique um valor para o campo **de exclusão vm (string).** Este valor é o nome de uma ou mais máquinas virtuais do grupo de recursos alvo. Pode introduzir mais do que um nome e separar os nomes usando vírgulas (os valores não são sensíveis aos casos). A utilização de um wildcard é suportada. Este valor é armazenado na `External_ExcludeVMNames` variável.
  
4. Utilize o campo **'Agendar'** para selecionar um horário para a gestão vm pela funcionalidade. Selecione uma data e hora de início para a sua agenda, para criar um horário diário recorrente a partir da hora escolhida. A seleção de uma região diferente não está disponível. Para configurar o horário para o seu fuso horário específico depois de configurar a funcionalidade, consulte [Modificar os horários](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules)de arranque e de paragem .

5. Para receber notificações por e-mail de um grupo de [ação,](../azure-monitor/platform/action-groups.md)aceite o valor padrão de **Sim** no campo de notificações por **e-mail** e forneça um endereço de e-mail válido. Se selecionar **Não** mas decidir mais tarde que deseja receber notificações de e-mail, pode atualizar o grupo de ação que é criado com endereços de e-mail válidos separados por vírgulas. 

6. Ativar as seguintes regras de alerta:

   - `AutoStop_VM_Child`
   - `Scheduled_StartStop_Parent`
   - `Sequenced_StartStop_Parent`

## <a name="create-alerts"></a>Criar alertas

Os VMs de arranque/paragem durante o horário de folga não incluem um conjunto predefinido de alertas. Reveja [Criar alertas de log com](../azure-monitor/platform/alerts-log.md) o Monitor Azure para aprender a criar alertas falhados de emprego para suportar os seus DevOps ou processos e procedimentos operacionais.

## <a name="deploy-the-feature"></a>Implementar a funcionalidade

1. Depois de configurar as definições iniciais necessárias para a funcionalidade, clique em **OK** para fechar a página Parâmetros.

2. Clique em **Criar**. Depois de todas as definições serem validadas, a funcionalidade é implantada na sua subscrição. Este processo pode demorar alguns segundos a terminar, e pode acompanhar o seu progresso ao abrigo de **Notificações** do menu.

    > [!NOTE]
    > Se tiver uma subscrição do Azure Cloud Solution Provider (Azure Cloud Solution Provider), após a implementação estar concluída, na sua conta de Automação, vá a **Variáveis** ao abrigo **de Recursos Partilhados** e detetete a [variável External_EnableClassicVMs](automation-solution-vm-management.md#variables) para **Falso**. Isto impede a solução de procurar recursos VM clássicos.

## <a name="next-steps"></a>Passos seguintes

* Para configurar a funcionalidade, consulte Os [VMs de paragem/arranque do Configure durante](automation-solution-vm-management-config.md)as horas de folga .
* Para resolver os erros de funcionalidade, consulte [Os VMs Start/Stop de Troubleshoot durante as questões fora de horas](troubleshoot/start-stop-vm.md).