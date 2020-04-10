---
title: Começar com a Configuração do Estado da Automação Azure
description: Explicação e exemplos das tarefas mais comuns na Configuração do Estado da Automação Azure (DSC)
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2c33fbed6c547897847c79cc1260d2bdc9ec6b17
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010329"
---
# <a name="get-started-with-azure-automation-state-configuration"></a>Começar com a Configuração do Estado da Automação Azure

Este artigo explica como fazer as tarefas mais comuns com a Configuração do Estado da Automação Azure, tais como criar, importar e compilar configurações, máquinas de embarque para gerir e ver relatórios. Para uma visão geral do que é a Configuração do Estado da Automação Azure, consulte a visão geral da configuração do Estado da [Automação Azure](automation-dsc-overview.md). Para obter documentação de configuração de estado desejada (DSC), consulte a [visão geral de configuração do Estado desejada pelo Windows PowerShell](/powershell/scripting/dsc/overview/overview).

Este artigo fornece um guia passo a passo para a utilização da Configuração do Estado da Automação Azure. Se quiser um ambiente de amostra que já esteja configurado sem seguir os passos descritos neste artigo, pode utilizar o seguinte modelo de Gestor de Recursos: Modelo de Nó gerido por [automação Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Este modelo configura um ambiente de configuração do Estado de Automação Azure concluído, incluindo um VM Azure que é gerido pela Configuração do Estado da Automação Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os exemplos deste artigo, são necessários os seguintes exemplos:

- Uma conta de Automatização do Azure. Para obter instruções sobre como criar uma conta Run As de Automatização do Azure, veja [Conta Run As do Azure](automation-sec-configure-azure-runas-account.md).
- Um VM (não Clássico) gestor de recursos Azure executa um [sistema operativo suportado](automation-dsc-overview.md#operating-system-requirements). Para obter instruções sobre como criar uma VM, veja [Criar a sua primeira máquina virtual do Windows no portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>Criar uma configuração DSC

Cria uma [configuração DSC](/powershell/scripting/dsc/configurations/configurations) simples que garante a presença ou ausência da Funcionalidade do Windows do **Servidor Web** (IIS), dependendo da forma como atribui nós.

1. Iniciar [O VSCode](https://code.visualstudio.com/docs) (ou qualquer editor de texto).
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
1. Guarde o ficheiro como `TestConfig.ps1`.

Esta configuração chama um recurso em cada bloco de nó, o [recurso WindowsFeature,](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource)que garante a presença ou ausência da funcionalidade **Web-Server.**

## <a name="importing-a-configuration-into-azure-automation"></a>Importar uma configuração para a Automação Azure

Em seguida, importa-se a configuração para a conta Automation.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, o nome da sua conta Automation.
1. Na página da **conta Automation,** selecione **a configuração do Estado (DSC)** em Gestão de **Configuração**.
1. Na página de **configuração do Estado (DSC),** clique no separador **Configurações** e, em seguida, clique **+ Adicionar**.
1. Na página de **Configuração de Importação,** navegue para o `TestConfig.ps1` ficheiro no seu computador.

   ![Screenshot da lâmina **Import Configuration**](./media/automation-dsc-getting-started/AddConfig.png)

1. Clique em **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Visualizar uma configuração na Automação Azure

Depois de importar uma configuração, pode vê-la no portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, o nome da sua conta Automation.
1. Na página da **conta Automation,** selecione **a configuração do Estado (DSC)** em Gestão de **Configuração**.
1. Na página de **configuração do Estado (DSC),** clique no separador **Configurações** e, em seguida, clique em **TestConfig** (este é o nome da configuração que importou no procedimento anterior).
1. Na página de **Configuração TestConfig,** clique em Ver fonte de **configuração**.

   ![Screenshot da lâmina de configuração TestConfig](./media/automation-dsc-getting-started/ViewConfigSource.png)

   Abre-se uma página **de origem de configuração testConfig,** exibindo o código PowerShell para a configuração.

## <a name="compiling-a-configuration-in-azure-automation"></a>Compilação de uma configuração na Automação Azure

Antes de poder aplicar um estado desejado a um nó, uma configuração DSC que defina esse estado deve ser compilada em uma ou mais configurações de nó (documento MOF), e colocada no Servidor de Pull Automation DSC. Para uma descrição mais detalhada das configurações de compilação na Configuração do Estado da Automação Azure, consulte [configurações de compilação na Configuração do Estado da Automação Azure](automation-dsc-compile.md).
Para obter mais informações sobre a compilação de configurações, consulte [Configurações DSC](/powershell/scripting/dsc/configurations/configurations).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, o nome da sua conta Automation.
1. Na página da **conta Automation,** clique na **configuração do Estado (DSC)** em Gestão de **Configuração**.
1. Na página de **configuração do Estado (DSC),** clique no separador **Configurações** e, em seguida, clique em **TestConfig** (o nome da configuração previamente importada).
1. Na página de **Configuração TestConfig,** clique em **Compile**, e, em seguida, clique **em Sim**. Isto começa um trabalho de compilação.

   ![Screenshot da página de configuração TestConfig realçando o botão de compilação](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Quando compila uma configuração em Automação Azure, ele automaticamente implementa quaisquer MOFs de configuração de nó criado para o servidor de puxar.

## <a name="viewing-a-compilation-job"></a>Visualizar um trabalho de compilação

Depois de iniciar uma compilação, pode vê-la no azulejo **Detrabalhos** de Compilação na página **de Configuração.** O azulejo de **Compilação Jobs** mostra atualmente trabalhos em funcionamento, concluídos e falhados. Ao abrir uma página de trabalho de compilação, mostra informações sobre esse trabalho, incluindo quaisquer erros ou avisos encontrados, parâmetros de entrada utilizados na configuração e registos de compilação.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, o nome da sua conta Automation.
1. Na página da **conta Automation,** clique na **configuração do Estado (DSC)** em Gestão de **Configuração**.
1. Na página de **configuração do Estado (DSC),** clique no separador **Configurações** e, em seguida, clique em **TestConfig** (o nome da configuração previamente importada).
1. No âmbito dos trabalhos de **compilação,** selecione o trabalho de compilação que pretende ver. Uma página de **Trabalho de Compilação** abre com a data em que o trabalho de compilação foi iniciado.

   ![Screenshot da página De Trabalho de Compilação](./media/automation-dsc-getting-started/CompilationJob.png)

1. Clique em qualquer azulejo na página De Trabalho de **Compilação** para ver mais detalhes sobre o trabalho.

## <a name="viewing-node-configurations"></a>Visualização de configurações de nó

A conclusão bem sucedida de um trabalho de compilação cria uma ou mais novas configurações de nó. Uma configuração de nó é um documento MOF que é implantado no servidor de puxar e pronto para ser puxado e aplicado por um ou mais nós. Pode visualizar as configurações do nó na sua conta De automação na página de **configuração do Estado (DSC).** Uma configuração nó tem um nome com o nome de *configuração*do formulário . *Nome do nó*.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, o nome da sua conta Automation.
1. Na lâmina da **conta Automation,** clique na **configuração do Estado (DSC)** em Gestão de **Configuração**.
1. Na página de **configuração do Estado (DSC),** clique no separador de **configurações compiladas.**

   ![Screenshot do separador Configurações Compiladas](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration"></a>Embarcar num Azure VM para gestão com configuração do Estado da Automação Azure

Pode utilizar a Configuração do Estado da Automação Azure para gerir Os VMs Azure (tanto Classic como Resource Manager), VMs no local, máquinas Linux, VMs AWS e máquinas físicas no local. Neste artigo, aprende-se a embarcar apenas em VMs do Gestor de Recursos Azure. Para obter informações sobre o embarque de outros tipos de máquinas, consulte máquinas de [embarque para gestão pela Configuração do Estado da Automação Azure](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-state-configuration"></a>Para embarcar num VM do Gestor de Recursos Azure para gestão pela Configuração do Estado da Automação Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, o nome da sua conta Automation.
1. Na lâmina da **conta Automation,** clique na **configuração do Estado (DSC)** em Gestão de **Configuração**.
1. Na página de **configuração do Estado (DSC),** enquanto no separador **Nós,** clique **+ Adicionar**.

   ![Screenshot da página DSC Nodes destacando o botão Add Azure VM](./media/automation-dsc-getting-started/OnboardVM.png)

1. Na página **Máquinas Virtuais,** selecione o seu VM.
1. Na página de detalhes da **máquina Virtual,** clique em **+ Ligar**.

   > [!IMPORTANT]
   > Este deve ser um VM do Gestor de Recursos Azure que executa um [sistema operativo suportado.](automation-dsc-overview.md#operating-system-requirements)

2. Na página **De registo,** selecione o nome da configuração do nó que pretende aplicar ao VM na caixa de nomes de configuração do **Nó.** Fornecer um nome neste momento é opcional. Pode alterar a configuração do nó atribuído depois de embarcar no nó.
   Verifique **o nó de reiniciar se necessário**e, em seguida, clique **OK**.

   ![Screenshot da lâmina de registo](./media/automation-dsc-getting-started/RegisterVM.png)

   A configuração do nó especificada é aplicada ao VM em intervalos especificados pela Frequência do Modo de **Configuração**, e o VM verifica as atualizações para a configuração do nó em intervalos especificados pela Frequência de **Atualização**. Para obter mais informações sobre como estes valores são utilizados, consulte [Configurar o Gestor](/powershell/scripting/dsc/managing-nodes/metaConfig)de Configuração Local .

Azure inicia o processo de embarque no VM. Quando está completo, o VM aparece na página de configuração de **Nóesda** da **configuração do Estado (DSC)** na conta Automation.

## <a name="viewing-the-list-of-managed-nodes"></a>Visualizando a lista de nódosos geridos

Pode ver a lista de todas as máquinas que foram a bordo para gestão na sua conta de Automação na página de Configuração de **Nóesos** do **Estado (DSC).**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, o nome da sua conta Automation.
1. Na lâmina da **conta Automation,** clique na **configuração do Estado (DSC)** em Gestão de **Configuração**.
1. Na página de **configuração do Estado (DSC),** clique no separador **Nós.**

## <a name="viewing-reports-for-managed-nodes"></a>Relatórios de visualização de nódosos geridos

Cada vez que a Configuração do Estado de Automação Azure executa uma verificação de consistência num nó gerido, o nó envia um relatório de estado de volta para o servidor de puxar. Pode ver estes relatórios na página para o nó.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, o nome da sua conta Automation.
1. Na lâmina da **conta Automation,** clique na **configuração do Estado (DSC)** em Gestão de **Configuração**.
1. Na página de **configuração do Estado (DSC),** clique no separador **Nós.** Aqui, pode ver a visão geral do estado de Configuração e os detalhes para cada nó.

   ![Screenshot da página do nó](./media/automation-dsc-getting-started/NodesTab.png)

1. Enquanto está no separador **Nós,** clique no registo do nó para abrir o relatório. Clique no relatório que pretende ver detalhes adicionais de reporte.

   ![Screenshot da lâmina do relatório](./media/automation-dsc-getting-started/NodeReport.png)

Na lâmina para um relatório individual, pode ver as seguintes informações de estado para a verificação de consistência correspondente:

- O estado do relatório — quer o nó seja "Conforme", a configuração "Falhada", ou o nó é "Incompatível" (quando o nó está no modo **ApplyandMonitor** e a máquina não está no estado desejado).
- A hora de início para a verificação de consistência.
- O tempo total de execução para a verificação de consistência.
- O tipo de verificação de consistência.
- Quaisquer erros, incluindo o código de erro e a mensagem de erro.
- Quaisquer recursos DSC utilizados na configuração, e o estado de cada recurso (se o nó está no estado desejado para esse recurso) — pode clicar em cada recurso para obter informações mais detalhadas para esse recurso.
- O nome, endereço IP e modo de configuração do nó.

Também pode clicar em **Ver relatório bruto** para ver os dados reais que o nó envia para o servidor.
Para obter mais informações sobre a utilização desse si, consulte A utilização de um servidor de [relatório DSC](/powershell/scripting/dsc/pull-server/reportserver).

Pode levar algum tempo depois de um nó estar a bordo antes de o primeiro relatório estar disponível. Talvez precise esperar até 30 minutos pelo primeiro relatório depois de embarcar num nó.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Reatribuir um nó a uma configuração diferente do nó

Pode atribuir um nó para utilizar uma configuração de nó diferente da que inicialmente atribuiu.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, o nome da sua conta Automation.
1. Na lâmina da **conta Automation,** clique na **configuração do Estado (DSC)** em Gestão de **Configuração**.
1. Na página de **configuração do Estado (DSC),** clique no separador **Nós.**
1. No separador **Nós,** clique no nome do nó que pretende reatribuir.
1. Na página para esse nó, clique na **configuração do nó de atribuir**.

    ![Screenshot da página de detalhes do Nó destacando o botão de configuração do nó de atribuir](./media/automation-dsc-getting-started/AssignNode.png)

1. Na página de Configuração do **Nó de Atribuir,** selecione a configuração do nó a que pretende atribuir o nó e, em seguida, clique em **OK**.

    ![Screenshot da página de configuração do nó de atribuição](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Desregistar um nó

Se já não quiser que um nó seja gerido pela Azure Automation DSC, pode desregistrá-lo.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, o nome da sua conta Automation.
1. Na lâmina da **conta Automation,** clique na **configuração do Estado (DSC)** em Gestão de **Configuração**.
1. Na página de **configuração do Estado (DSC),** clique no separador **Nós.**
1. No separador **Nós,** clique no nome do nó que deseja desregistar.
1. Na página para esse nó, clique **em Desregistar**.

    ![Screenshot da página de detalhes do Nó destacando o botão Unregister](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Artigos Relacionados

- [Visão geral da configuração do Estado da Automação Azure](automation-dsc-overview.md)
- [Máquinas de embarque para gestão pela Configuração do Estado da Automação Azure](automation-dsc-onboarding.md)
- [Visão geral de configuração de estado desejada do Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Cmdlets de Configuração do Estado da Automação Azure](/powershell/module/azurerm.automation/#automation)
- [Preços de configuração do Estado da Automação Azure](https://azure.microsoft.com/pricing/details/automation/)
