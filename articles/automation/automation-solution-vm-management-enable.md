---
title: Ativar O início/paragem de VMs da automatização Azure durante as horas de folga
description: Este artigo diz como ativar os VMs iniciar/parar durante o período de folga para os seus VMs Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 36f885416c5e9cb656d01a65b9c503f8897d2f9f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593904"
---
# <a name="enable-startstop-vms-during-off-hours"></a>Ativar a solução Iniciar/Parar VMs fora do horário de expediente

Execute os passos neste tópico em sequência para ativar os VMs iniciar/parar durante o período de folga para VMs utilizando uma conta de Automação nova ou existente e espaço de trabalho linked Log Analytics. Depois de concluir o processo de configuração, configuure as variáveis para personalizar a funcionalidade.

>[!NOTE]
>Para utilizar esta funcionalidade com VMs clássicos, precisa de uma conta Classic Run As, que não é criada por padrão. Ver [Criar uma conta Clássica como conta](automation-create-standalone-account.md#create-a-classic-run-as-account).
>

## <a name="create-resources-for-the-feature"></a>Criar recursos para a funcionalidade

1. Inscreva-se no [portal](https://portal.azure.com)Azure .
2. Procure e selecione **Contas de Automação.**
3. Na página 'Contas de Automação', selecione a sua conta Demôm automação na lista.
4. A partir da conta Automation, selecione **Iniciar/Parar VM** em **Recursos Relacionados.** A partir daqui, clique em **Saber mais e ativar a solução.** Se já tiver a funcionalidade implementada, pode clicar em **Gerir a solução** e encontrá-la na lista.

   ![Ativar a partir da conta de automação](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Também pode criar o recurso a partir de qualquer lugar do portal Azure, clicando em **Criar um recurso.** Na página Marketplace, escreva uma palavra-chave como **Iniciar** ou **Iniciar/Parar**. À medida que começa a escrever, a lista filtra com base na sua entrada. Em alternativa, pode escrever uma ou mais palavras-chave a partir do nome completo da função e, em seguida, **premir Enter**. Selecione **VMs de início/paragem durante as horas de folga** a partir dos resultados da pesquisa.

5. Nos VMs iniciar/parar durante a página fora de horas para a implementação selecionada, reveja as informações de resumo e clique em **Criar**.

   ![Portal do Azure](media/automation-solution-vm-management/azure-portal-01.png)

## <a name="configure-the-feature"></a>Configure a funcionalidade

Com o recurso criado, aparece a página Add Solution. É-lhe solicitado que configuure a funcionalidade antes de poder importá-la para a sua subscrição de Automação. Consulte [vMs de arranque/paragem configurar durante as horas de folga](automation-solution-vm-management-config.md).

   ![Página de Solução de adicionar de gestão VM](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

## <a name="select-a-log-analytics-workspace"></a>Selecione um espaço de trabalho Log Analytics

1. Na página 'Adicionar Solução', selecione **Workspace**. Selecione um espaço de trabalho Log Analytics que esteja ligado à subscrição Azure utilizada pela conta Automation. 

2. Se não tiver um espaço de trabalho, selecione **Criar Novo Espaço de Trabalho.** Na página do espaço de trabalho Log Analytics, execute os seguintes passos:

   - Especifique um nome para o novo espaço de trabalho Log Analytics, como **ContosoLAWorkspace**.
   - Selecione uma **Subscrição** para ligar selecionando a partir da lista de dropdown, se o padrão selecionado não for apropriado.
   - Para **o Grupo de Recursos,** pode criar um novo grupo de recursos ou selecionar um existente.
   - Selecione uma **localização**.
   - Selecione um **nível de preços**. Escolha a opção **Per GB (Autónomo).** Os registos do Azure Monitor atualizaram [os preços](https://azure.microsoft.com/pricing/details/log-analytics/) e o nível Per GB é a única opção.

   > [!NOTE]
   > Ao ativar funcionalidades, apenas certas regiões são suportadas para ligar um espaço de trabalho Log Analytics e uma conta Automation. Para obter uma lista dos pares de mapeamento suportados, consulte [o mapeamento da Região para a conta de Automação e o espaço de trabalho log Analytics.](how-to/region-mappings.md)

3. Depois de fornecer as informações necessárias na página do espaço de trabalho Do Log Analytics, clique em **Criar**. Pode acompanhar o seu progresso no menu **de Notificações,** que o retorna à página Add Solution quando feito.

## <a name="add-automation-account"></a>Adicionar conta de Automação

Volte a aceder à página 'Adicionar Solução' e selecione **a conta Dem automação.** Pode selecionar uma conta de Automação existente que ainda não esteja ligada a um espaço de trabalho log Analytics. Se estiver a criar um novo espaço de trabalho do Log Analytics, pode criar uma nova conta Automation para o associar. Selecione uma conta de Automação existente ou clique em **Criar uma conta Demôm automação,** e na página de conta 'Adicionar Automação', fornecer o nome da conta Automação no campo **Nome.**

Todas as outras opções são automaticamente povoadas, com base no espaço de trabalho log Analytics selecionado. Não pode modificar estas opções. Uma conta Azure Run Como é o método de autenticação predefinido para os runbooks incluídos na funcionalidade. 

Depois de clicar **em OK,** as opções de configuração são validadas e a conta Automação é criada. Pode acompanhar o progresso em **Notificações**, no menu.

## <a name="define-feature-parameters"></a>Definir parâmetros de características

1. Na página 'Adicionar Solução', selecione **Configuração**. A página parâmetros aparece.

    ![Página de parâmetros para solução](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

2. Especifique um valor para o campo **Nomes do Grupo de Recursos Alvo.** O campo define nomes de grupo que contêm VMs para a funcionalidade gerir. Pode introduzir mais de um nome e separar os nomes usando vírgulas (os valores não são sensíveis a casos). Se quiser segmentar todas as VMs em todos os grupos de recursos da subscrição, a utilização de um caráter universal é suportada. Os valores são armazenados nas `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` variáveis e variáveis.

    > [!IMPORTANT]
    > O valor predefinido para **nomes do Grupo de Recursos Alvo** é um **&ast;** . Esta definição visa todos os VMs numa subscrição. Se não quiser que a funcionalidade direcione todos os VMs da sua subscrição, tem de fornecer uma lista de nomes de grupos de recursos antes de selecionar um horário.
  
3. Especifique um valor para o campo **VM Exclude List (cadeia).** Este valor é o nome de uma ou mais máquinas virtuais do grupo de recursos-alvo. Pode introduzir mais de um nome e separar os nomes usando vírgulas (os valores não são sensíveis a casos). A utilização de um wildcard é suportada. Este valor é armazenado na `External_ExcludeVMNames` variável.
  
4. Utilize o campo **Agendar** para selecionar um horário de gestão de VM pela funcionalidade. Selecione uma data e hora de início para a sua agenda, para criar uma agenda diária recorrente a partir da hora escolhida. Não está disponível a seleção de uma região diferente. Para configurar o horário para o seu fuso horário específico depois de configurar a funcionalidade, consulte [Modificar os horários de arranque e de paragem](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules).

5. Para receber notificações por e-mail de um grupo de [ação,](../azure-monitor/alerts/action-groups.md)aceite o valor padrão do **Sim** no campo das  **notificações por e-mail** e forneça um endereço de e-mail válido. Se selecionar **No** mas decidir numa data posterior que pretende receber notificações por e-mail, pode atualizar o grupo de ação que é criado com endereços de e-mail válidos separados por vírgulas. 

6. Ativar as seguintes regras de alerta:

   - `AutoStop_VM_Child`
   - `Scheduled_StartStop_Parent`
   - `Sequenced_StartStop_Parent`

## <a name="create-alerts"></a>Criar alertas

Os VMs de início/paragem durante o horário de folga não incluem um conjunto de alertas predefinidos. Reveja Criar alertas de registo com o [Azure Monitor](../azure-monitor/alerts/alerts-log.md) para aprender a criar alertas falhados de trabalho para suportar os seus DevOps ou processos e procedimentos operacionais.

## <a name="deploy-the-feature"></a>Implementar a funcionalidade

1. Depois de configurar as definições iniciais necessárias para a funcionalidade, clique **em OK** para fechar a página Parâmetros.

2. Clique em **Criar**. Depois de todas as definições serem validadas, a funcionalidade implementa-se na sua subscrição. Este processo pode demorar vários segundos a terminar, e pode acompanhar o seu progresso no menu **de Notificações.**

    > [!NOTE]
    > Se tiver uma subscrição do Azure Cloud Solution Provider (Azure CSP), após a implementação estar concluída, na sua conta de Automação, vá a **Variáveis** em Recursos **Partilhados** e dedibra a variável [External_EnableClassicVMs](automation-solution-vm-management.md#variables) a **Falsa**. Isto impede a solução de procurar recursos VM clássicos.

## <a name="next-steps"></a>Passos seguintes

* Para configurar a função, consulte [Configure Stop/Start VMs durante as horas de folga](automation-solution-vm-management-config.md).
* Para resolver erros de funcionalidade, consulte [Os VMs de início/paragem de resolução de problemas durante as horas fora de horas](troubleshoot/start-stop-vm.md).