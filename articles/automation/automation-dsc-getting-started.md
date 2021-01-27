---
title: Começar com a configuração do Estado da Automação Azure
description: Este artigo diz como fazer as tarefas mais comuns na Configuração do Estado da Automação Azure.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 25f5e92aa8aa211782bc238781c59b9ca0511173
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897246"
---
# <a name="get-started-with-azure-automation-state-configuration"></a>Começar com a configuração do Estado da Automação Azure

Este artigo fornece um guia passo a passo para fazer as tarefas mais comuns com a Configuração do Estado da Automação Azure, tais como criar, importar e compilar configurações, permitir que as máquinas consigam gerir e visualizar relatórios. Para uma visão geral da configuração do estado, consulte a [visão geral da configuração do estado](automation-dsc-overview.md). Para a documentação de configuração estatal desejada (DSC), consulte a [visão geral da configuração desejada do Windows PowerShell](/powershell/scripting/dsc/overview/overview).

Se quiser um ambiente de amostra que já esteja configurado sem seguir os passos descritos neste artigo, pode utilizar o modelo de nó gerido pela [automação Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Este modelo configura um ambiente completo de configuração do estado (DSC), incluindo um VM Azure que é gerido por Configuração do Estado (DSC).

## <a name="prerequisites"></a>Pré-requisitos

Para completar os exemplos deste artigo, são necessários os seguintes:

- Uma conta de Automatização do Azure. Para obter instruções sobre como criar uma conta Run As de Automatização do Azure, veja [Conta Run As do Azure](./manage-runas-account.md).
- Um VM (não Clássico) gestor de recursos Azure com um [sistema operativo suportado.](automation-dsc-overview.md#operating-system-requirements) Para obter instruções sobre como criar uma VM, veja [Criar a sua primeira máquina virtual do Windows no portal do Azure](../virtual-machines/windows/quick-create-portal.md)

## <a name="create-a-dsc-configuration"></a>Criar uma configuração DSC

Cria uma [configuração DSC](/powershell/scripting/dsc/configurations/configurations) simples que garante a presença ou ausência da Funcionalidade do Windows **do Servidor Web** (IIS), dependendo da forma como atribui nós.

1. Iniciar [o VSCode](https://code.visualstudio.com/docs) (ou qualquer editor de texto).
1. Digite o seguinte texto:

    ```powershell
    configuration TestConfig
    {
        Node IsWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
            }
        }
    }
    ```
1. Guarde o ficheiro como **TestConfig.ps1**.

Esta configuração chama um recurso em cada bloco de nó, o [recurso WindowsFeature](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource). Este recurso garante a presença ou ausência da função **Web-Server.**

## <a name="import-a-configuration-into-azure-automation"></a>Importe uma configuração para a Azure Automation

Em seguida, importa a configuração para a conta Automation.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, o nome da sua conta Automation.
1. Na página da conta Automation, selecione **a configuração do Estado (DSC)** em **Gestão de Configuração**.
1. Na página de configuração do Estado (DSC), clique no **separador Configurações** e, em seguida, clique em **Adicionar**.
1. No painel de configuração de importação, consulte o `TestConfig.ps1` ficheiro no seu computador.

   ![Screenshot da lâmina **Import Configuration**](./media/automation-dsc-getting-started/AddConfig.png)

1. Clique em **OK**.

## <a name="view-a-configuration-in-azure-automation"></a>Ver uma configuração em Azure Automation

Depois de importar uma configuração, pode vê-la no portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, o nome da sua conta Automation.
1. Na página da conta Automation, selecione  **a configuração do Estado (DSC)** em **Gestão de Configuração**.
1. Na página de configuração do Estado (DSC), clique no **separador Configurações** e, em seguida, clique em **TestConfig**. Este é o nome da configuração que importou no procedimento anterior.
1. No painel de configuração TestConfig, clique **na fonte de configuração de Ver**.

   ![Screenshot da lâmina de configuração TestConfig](./media/automation-dsc-getting-started/ViewConfigSource.png)

   Abre-se um painel de origem de configuração TestConfig, apresentando o código PowerShell para a configuração.

## <a name="compile-a-configuration-in-azure-automation"></a>Compilar uma configuração em Azure Automation

Antes de poder aplicar um estado desejado a um nó, uma configuração DSC que define esse estado deve ser compilada em uma ou mais configurações de nó (documento MOF) e colocada no Servidor de Puxar DSC da Automação. Para obter uma descrição mais detalhada das configurações de compilação na Configuração do Estado (DSC), consulte [as configurações de Compile na Configuração do Estado da Automação Azure](automation-dsc-compile.md).
Para obter mais informações sobre a compilação de configurações, consulte [as Configurações DSC](/powershell/scripting/dsc/configurations/configurations).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, o nome da sua conta Automation.
1. Na página da conta Demômes, clique na **configuração do Estado (DSC)** em **Gestão de Configuração**.
1. Na página de configuração do Estado (DSC), clique no **separador Configurações** e, em seguida, clique em **TestConfig**. Este é o nome da configuração anteriormente importada.
1. No painel de configuração TestConfig, clique em **Compile** e, em seguida, clique em **Sim**. Isto começa um trabalho de compilação.

   ![Screenshot da página de configuração TestConfig realçando o botão de compilação](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Quando compila uma configuração no Azure Automation, implementa automaticamente quaisquer ficheiros MOF de configuração de nó criados para o servidor pull.

## <a name="view-a-compilation-job"></a>Ver um trabalho de compilação

Depois de iniciar uma compilação, pode vê-la no azulejo **de Trabalhos de Compilação** na página **Configuração.** O azulejo **da Compilação Jobs** mostra atualmente em execução, concluído e empregos falhados. Quando abre um painel de trabalho de compilação, mostra informações sobre esse trabalho, incluindo quaisquer erros ou avisos encontrados, parâmetros de entrada utilizados na configuração e registos de compilação.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, o nome da sua conta Automation.
1. Na página da conta Demômes, clique na **configuração do Estado (DSC)** em **Gestão de Configuração**.
1. Na página de configuração do Estado (DSC), clique no **separador Configurações** e, em seguida, clique em **TestConfig**. Este é o nome da configuração anteriormente importada.
1. Em **trabalhos de compilação,** selecione o trabalho de compilação para visualizar. Abre-se um painel de trabalho de compilação, rotulado com a data em que o trabalho de compilação foi iniciado.

   ![Screenshot da página de Trabalho de Compilação](./media/automation-dsc-getting-started/CompilationJob.png)

1. Clique em qualquer azulejo no painel de trabalho de compilação para ver mais detalhes sobre o trabalho.

## <a name="view-node-configurations"></a>Ver configurações de nó

A conclusão bem sucedida de um trabalho de compilação cria uma ou mais configurações de nó novos. Uma configuração de nó é um documento MOF que é implantado no servidor pull e pronto para ser puxado e aplicado por um ou mais nós. Pode ver as configurações do nó na sua conta Demômes na página de configuração do Estado (DSC). Uma configuração de nó tem um nome com o formulário `ConfigurationName.NodeName` .

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, o nome da sua conta Automation.
1. Na página da conta Demômes, clique na **configuração do Estado (DSC)** em **Gestão de Configuração**.
1. Na página de configuração do Estado (DSC), clique no separador **configurações compiladas.**

   ![Screenshot do separador Configurações Compiladas](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="enable-an-azure-resource-manager-vm-for-management-with-state-configuration"></a>Ativar um VM gestor de recursos Azure para gestão com Configuração do Estado

Pode utilizar a Configuração estatal para gerir VMs Azure (clássico e Gestor de Recursos), VMs no local, máquinas Linux, VMs AWS e máquinas físicas no local. Neste artigo, aprende-se a ativar apenas VMs do Azure Resource Manager. Para obter informações sobre a ativação de outros tipos de máquinas, consulte [Ativar as máquinas para gestão através da Azure Automation State Configuration](automation-dsc-onboarding.md).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, o nome da sua conta Automation.
1. Na página da conta Demômes, clique na **configuração do Estado (DSC)** em **Gestão de Configuração**.
1. Na página de configuração do Estado (DSC), selecione o **separador Nodes** e, em seguida, clique **em + Adicionar**.

   ![Screenshot da página DSC Nodes destacando o botão Add Azure VM](./media/automation-dsc-getting-started/OnboardVM.png)

1. No painel de Máquinas Virtuais, selecione o seu VM.
1. No painel de detalhes da máquina Virtual, clique **+ Conecte-se**.

   > [!IMPORTANT]
   > O VM deve ser um VM gestor de recursos Azure que executa um [sistema operativo suportado](automation-dsc-overview.md#operating-system-requirements).

2. Na página 'Registar', selecione o nome da configuração do nó para aplicar ao VM no campo nome de configuração do **nó.** Fornecer um nome neste momento é opcional. Pode alterar a configuração do nó atribuído depois de ativar o nó.

3. Verifique **o nó de reinicialização se necessário,** em seguida, clique em **OK**.

   ![Screenshot da lâmina de registo](./media/automation-dsc-getting-started/RegisterVM.png)

   A configuração do nó especificado é aplicada ao VM em intervalos especificados pelo valor previsto para **a Frequência do Modo de Configuração**. O VM verifica as atualizações da configuração do nó em intervalos especificados pelo valor de **Freqüência de Atualização.** Para obter mais informações sobre a forma como estes valores são utilizados, consulte [configurar o Gestor de Configuração Local.](/powershell/scripting/dsc/managing-nodes/metaConfig)

A azure inicia o processo de ativação do VM. Quando estiver concluído, o VM aparece no **separador Nodes** da página de configuração do Estado (DSC) na conta Automation.

## <a name="view-the-list-of-managed-nodes"></a>Ver a lista de nómada gerida

Pode ver a lista de todas as máquinas que foram ativadas para gestão na sua conta Automation na página **Nodes** da configuração do Estado (DSC).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, o nome da sua conta Automation.
1. Na página da conta Demômes, clique na **configuração do Estado (DSC)** em **Gestão de Configuração**.
1. Na página de configuração do Estado (DSC), clique no **separador Nodes.**

## <a name="view-reports-for-managed-nodes"></a>Ver relatórios para os nosdes geridos

Cada vez que a Configuração do Estado executa uma verificação de consistência num nó gerido, o nó envia um relatório de estado de volta para o servidor de puxar. Pode ver estes relatórios na página para esse nó.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, o nome da sua conta Automation.
1. Na página da conta Demômes, clique na **configuração do Estado (DSC)** em **Gestão de Configuração**.
1. Na página de configuração do Estado (DSC), clique no **separador Nodes.** Aqui, pode ver a visão geral do estado de Configuração e os detalhes de cada nó.

   ![Screenshot da página nól do nó](./media/automation-dsc-getting-started/NodesTab.png)

1. Enquanto está no separador **Nodes,** clique no registo do nó para abrir o relatório. Clique no relatório que pretende ver detalhes adicionais de reporte.

   ![Screenshot da lâmina do relatório](./media/automation-dsc-getting-started/NodeReport.png)

Na lâmina para um relatório individual, pode ver as seguintes informações de estado para a verificação de consistência correspondente:

- O estado do relatório. Os valores possíveis são:
    * Em conformidade - o nó está em conformidade com a verificação.
   * Falhou - a configuração falhou na verificação.
   * Não conforme - o nó está em `ApplyandMonitor` modo e a máquina não está no estado desejado.
- A hora de início para a verificação de consistência.
- O tempo total de execução para a verificação de consistência.
- O tipo de verificação de consistência.
- Quaisquer erros, incluindo o código de erro e a mensagem de erro.
- Quaisquer recursos DSC utilizados na configuração, e o estado de cada recurso (se o nó está no estado desejado para esse recurso). Pode clicar em cada recurso para obter informações mais detalhadas para esse recurso.
- O nome, endereço IP e modo de configuração do nó.

Também pode clicar em **Ver relatório bruto** para ver os dados reais que o nó envia para o servidor.
Para obter mais informações sobre a utilização desses dados, consulte [utilizar um servidor de relatório DSC](/powershell/scripting/dsc/pull-server/reportserver).

Pode levar algum tempo depois de um nó estar ativado antes de o primeiro relatório estar disponível. Pode ter de esperar até 30 minutos para o primeiro relatório depois de ativar um nó.

## <a name="reassign-a-node-to-a-different-node-configuration"></a>Reatribua um nó para uma configuração diferente do nó

Pode atribuir um nó para utilizar uma configuração de nó diferente da que inicialmente atribuiu.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, o nome da sua conta Automation.
1. Na página da conta Demômes, clique na **configuração do Estado (DSC)** em **Gestão de Configuração**.
1. Na página de configuração do Estado (DSC), clique no **separador Nodes.**
1. No **separador Nodes,** clique no nome do nó que pretende reatribuir.
1. Na página para esse nó, clique na **configuração do nó de atribuição**.

    ![Screenshot da página de detalhes do nó realçando o botão de configuração do nó de atribuição](./media/automation-dsc-getting-started/AssignNode.png)

1. Na página 'Configuração do nó' 'Atribuir', selecione a configuração do nó à qual pretende atribuir o nó e, em seguida, clique em **OK**.

    ![Screenshot da página de configuração do nó de atribuição](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregister-a-node"></a>Não registar um nó

Se já não pretender que um nó seja gerido pela Configuração do Estado, pode desregistrá-lo.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, o nome da sua conta Automation.
1. Na página da conta Demômes, clique na **configuração do Estado (DSC)** em **Gestão de Configuração**.
1. Na página de configuração do Estado (DSC), clique no **separador Nodes.**
1. No **separador Nodes,** clique no nome do nó que pretende não registar.
1. No painel para o nó, clique em **Unregister**.

    ![Screenshot da página de detalhes do nó realçando o botão Unregister](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="next-steps"></a>Próximos passos

- Para obter uma visão geral, consulte [a visão geral da configuração do Estado da Automação Azure](automation-dsc-overview.md).
- Para ativar a funcionalidade de VMs no seu ambiente, consulte [ativar a configuração do Estado da Automação Azure](automation-dsc-onboarding.md).
- Para compreender o PowerShell DSC, consulte a visão geral da [configuração do estado desejada do Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- Para obter informações sobre preços, consulte [os preços de configuração do Estado da Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](/powershell/module/az.automation).
