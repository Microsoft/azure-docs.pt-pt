---
title: Ativar os VMs de arranque/paragem de automatização azure durante a solução de horas de folga
description: Este artigo descreve como ativar a solução Azure Automation Start/Stop VM para as suas máquinas virtuais Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: d47daa29c65f847fdeb33b9e24a892ac1f31b52a
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096967"
---
# <a name="enable-azure-automation-startstop-vms-solution"></a>Ativar a solução de VMs start/stop de automação Azure

Execute os seguintes passos para adicionar os **VMs de início/paragem durante a** solução off-hours a uma nova ou existente conta de Automação e espaço de trabalho ligado ao Log Analytics. Depois de concluir o processo de embarque, configure as variáveis para personalizar a solução.

>[!NOTE]
>Para utilizar esta solução com VMs clássicos, precisa de uma conta Classic Run As, que não é criada por defeito. Para obter instruções sobre a criação de uma conta Classic Run As, consulte [Create a Classic Run As account](automation-create-standalone-account.md#create-a-classic-run-as-account).
>

## <a name="enable-solution"></a>Ativar a solução

1. Inscreva-se no [portal](https://portal.azure.com)Azure.

2. Procure e selecione **Contas de Automação**.

3. Na página Contas de Automação, selecione a sua conta De automação na lista.

4. Na conta Automation, selecione **Start/Stop VM** em **Recursos Relacionados**. A partir daqui, pode clicar **em Saber mais sobre e ativar a solução**. Se já tiver uma solução Start/Stop VM implementada, pode selecioná-la clicando em **Gerir a solução** e encontrando-a na lista.

   ![Ativar a partir da conta de automação](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Também pode criá-lo a partir de qualquer lugar do portal Azure, clicando em **Criar um recurso**. Na página marketplace, digite uma palavra-chave como **Iniciar** ou **Iniciar/Parar**. À medida que começa a escrever, a lista filtra com base na sua entrada. Em alternativa, pode escrever uma ou mais palavras-chave a partir do nome completo da solução e, em seguida, prima Enter. Selecione **VMs iniciar/parar durante** o horário de folga a partir dos resultados da pesquisa.

5. Nos **VMs iniciar/parar durante a página off-hours** para a solução selecionada, reveja as informações sumárias e, em seguida, clique em **Criar**.

   ![Portal do Azure](media/automation-solution-vm-management/azure-portal-01.png)

6. Aparece a página Add Solution. É-lhe pedido que configure a solução antes de a importar para a sua subscrição de Automação.

   ![Página de Soluções de Adição de Gestão VM](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

7. Na página Adicionar Solução, selecione **Workspace**. Selecione um espaço de trabalho log Analytics ligado à mesma subscrição Azure em que a conta Automation está. Se não tiver um espaço de trabalho, selecione **Criar novo espaço**de trabalho . Na página do espaço de trabalho Log Analytics, execute os seguintes passos:

   - Especifique um nome para o novo espaço de trabalho log analytics, como **ContosoLAWorkspace**.
   - Selecione uma **Subscrição** para ligar selecionando a partir da lista de dropdown, se o padrão selecionado não for apropriado.
   - Para **o Grupo de Recursos,** pode criar um novo grupo de recursos ou selecionar um existente.
   - Selecione um **Local**.
   - Selecione um **nível de preços**. Escolha a opção **Per GB (Autónoma).** Os registos do Monitor Azure têm [preços](https://azure.microsoft.com/pricing/details/log-analytics/) atualizados e o nível Per GB é a única opção.

   > [!NOTE]
   > Ao ativar soluções, apenas certas regiões são suportadas para ligar um espaço de trabalho log Analytics e uma conta de Automação.
   >
   > Para obter uma lista dos pares de mapeamento suportados, consulte [O mapeamento da Região para a conta de Automação e o espaço de trabalho log Analytics](how-to/region-mappings.md).

8. Depois de fornecer as informações necessárias na página do espaço de trabalho Log Analytics, clique em **Criar**. Pode acompanhar o seu progresso no âmbito de **Notificações** do menu, que o devolve à página Add Solution quando feito.

9. Na página Add Solution, selecione **conta Automation**. Se estiver a criar um novo espaço de trabalho log Analytics, pode criar uma nova conta Automation para estar associada a ela, ou selecionar uma conta de Automação existente que ainda não esteja ligada a um espaço de trabalho do Log Analytics. Selecione uma conta de Automação existente ou clique **Criar uma conta De Automação,** e na página da conta Add Automation, forneça as seguintes informações:
 
   - No campo **Nome**, introduza o nome da conta de Automatização.

     Todas as outras opções são automaticamente povoadas com base no espaço de trabalho do Log Analytics selecionado. Estas opções não podem ser modificadas. O método de autenticação predefinido para os runbooks incluídos nesta solução é a conta Run As do Azure. Depois de clicar em **OK,** as opções de configuração são validadas e a conta Automation é criada. Pode acompanhar o progresso em **Notificações**, no menu.

10. Finalmente, na página Adicionar Solução, **selecione Configuração**. A página dos Parâmetros aparece.

    ![Página de parâmetros para solução](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Aqui, é solicitado a:
  
   - Especifique os **nomes**do Grupo de Recursos Alvo . Estes valores são nomes de grupos de recursos que contêm VMs para serem geridos por esta solução. Pode introduzir mais do que um nome e separar cada um utilizando uma vírvia (os valores não são sensíveis a casos). Se quiser segmentar todas as VMs em todos os grupos de recursos da subscrição, a utilização de um caráter universal é suportada. Este valor é armazenado nas variáveis **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames.**
  
   - Especifique a Lista de **Exclusão vm (cadeia)**. Este valor é o nome de uma ou mais máquinas virtuais do grupo de recursos alvo. Pode introduzir mais do que um nome e separar cada um utilizando uma vírvia (os valores não são sensíveis a casos). A utilização de um wildcard é suportada. Este valor é armazenado na **variável External_ExcludeVMNames.**
  
   - Selecione um **Horário**. Selecione uma data e hora para o seu horário. Será criado um horário diário recorrente a partir do tempo que selecionou. A seleção de uma região diferente não está disponível. Para configurar o horário para o seu fuso horário específico depois de configurar a solução, consulte modificar o horário de [arranque e encerramento](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules).
  
   - Para receber notificações de **e-mail** de um grupo de ação, aceite o valor padrão de **Sim** e forneça um endereço de e-mail válido. Se selecionar **Não** mas decidir mais tarde que deseja receber notificações de e-mail, pode atualizar o grupo de [ação](../azure-monitor/platform/action-groups.md) que é criado com endereços de e-mail válidos separados por uma vírem. Também precisa de ativar as seguintes regras de alerta:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > O valor padrão para nomes **&ast;** do Grupo de **Recursos-Alvo** é a . Isto visa todos os VMs numa subscrição. Se não quiser que a solução para direcionar todos os VMs da sua subscrição, este valor precisa de ser atualizado para uma lista de nomes de grupos de recursos antes de ativar os horários.

11. Depois de configurar as definições iniciais necessárias para a solução, clique em **OK** para fechar a página Parâmetros e selecione **Criar**. 

Depois de todas as definições serem validadas, a solução é implementada na sua subscrição. Este processo pode demorar alguns segundos a terminar, e pode acompanhar o seu progresso ao abrigo de **Notificações** do menu.

> [!NOTE]
> Se tiver uma subscrição do Azure Cloud Solution Provider (Azure Cloud Solution Provider), após a implementação estar concluída, na sua conta de Automação, vá a **Variáveis** ao abrigo **de Recursos Partilhados** e detetete a [variável External_EnableClassicVMs](automation-solution-vm-management.md#variables) para **Falso**. Isto impede a solução de procurar recursos VM clássicos.

## <a name="next-steps"></a>Passos seguintes

Agora que tem a solução [configure](automation-solution-vm-management-config.md) ativada, pode configurá-la para suportar os seus requisitos de gestão vm.