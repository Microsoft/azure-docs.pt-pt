---
title: Introdução à configuração de estado da automação do Azure
description: Explicação e exemplos das tarefas mais comuns na configuração de estado da automação do Azure (DSC)
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 55950892bec71fdff50cdd0e0b1aae107d845739
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169739"
---
# <a name="getting-started-with-azure-automation-state-configuration"></a>Introdução à configuração de estado da automação do Azure

Este artigo explica como realizar as tarefas mais comuns com a configuração de estado da automação do Azure, como criar, importar e compilar configurações, máquinas de integração para gerenciar e exibir relatórios. Para obter uma visão geral da configuração de estado da automação do Azure, consulte [visão geral da configuração do estado de automação do Azure](automation-dsc-overview.md). Para obter a documentação da configuração de estado desejado (DSC), consulte [visão geral da configuração de estado desejado do Windows PowerShell](/powershell/dsc/overview).

Este artigo fornece um guia passo a passo para usar a configuração de estado da automação do Azure. Se você quiser um ambiente de exemplo que já esteja configurado sem seguir as etapas descritas neste artigo, você pode usar o seguinte modelo do Resource Manager: [Modelo de nó gerenciado da automação do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Este modelo configura um ambiente de configuração de estado de automação do Azure concluído, incluindo uma VM do Azure que é gerenciada pela configuração de estado da automação do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os exemplos neste artigo, são necessários os seguintes:

- Uma conta de Automatização do Azure. Para obter instruções sobre como criar uma conta Run As de Automatização do Azure, veja [Conta Run As do Azure](automation-sec-configure-azure-runas-account.md).
- Uma VM Azure Resource Manager (não clássica) executando um [sistema operacional com suporte](automation-dsc-overview.md#operating-system-requirements). Para obter instruções sobre como criar uma VM, veja [Criar a sua primeira máquina virtual do Windows no portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>Criando uma configuração DSC

Você cria uma [configuração DSC](/powershell/dsc/configurations) simples que garante a presença ou a ausência do recurso do Windows do **servidor Web** (IIS), dependendo de como você atribui nós.

1. Inicie o [VSCode](https://code.visualstudio.com/docs) (ou qualquer editor de texto).
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

Essa configuração chama um recurso em cada bloco de nó, o [recurso WindowsFeature](/powershell/dsc/windowsfeatureresource), que garante a presença ou a ausência do recurso de **servidor Web** .

## <a name="importing-a-configuration-into-azure-automation"></a>Importando uma configuração para a automação do Azure

Em seguida, você importará a configuração para a conta de automação.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **todos os recursos** e, em seguida, no nome da sua conta de automação.
1. Na página **conta de automação** , selecione **configuração de estado (DSC)** em **Gerenciamento de configuração**.
1. Na página **configuração de estado (DSC)** , clique na guia **configurações** e, em seguida, clique em **+ Adicionar**.
1. Na página **importar configuração** , navegue até o arquivo `TestConfig.ps1` no computador.

   ![Captura de tela da folha * * importar configuração * *](./media/automation-dsc-getting-started/AddConfig.png)

1. Clique em **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Exibindo uma configuração na automação do Azure

Depois de importar uma configuração, você poderá exibi-la no portal do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **todos os recursos** e, em seguida, no nome da sua conta de automação.
1. Na página **conta de automação** , selecione **configuração de estado (DSC)** em **Gerenciamento de configuração**.
1. Na página **configuração de estado (DSC)** , clique na guia **configurações** e, em seguida, clique em **TestConfig** (esse é o nome da configuração que você importou no procedimento anterior).
1. Na página **configuração do TestConfig** , clique em **Exibir fonte de configuração**.

   ![Captura de tela da folha de configuração do TestConfig](./media/automation-dsc-getting-started/ViewConfigSource.png)

   Uma página de **origem de configuração do TestConfig** é aberta, exibindo o código do PowerShell para a configuração.

## <a name="compiling-a-configuration-in-azure-automation"></a>Compilando uma configuração na automação do Azure

Antes que você possa aplicar um estado desejado a um nó, uma configuração DSC que define esse Estado deve ser compilada em uma ou mais configurações de nó (documento MOF) e colocada no servidor de pull DSC de Automação. Para obter uma descrição mais detalhada da compilação de configurações na configuração de estado da automação do Azure, consulte [compilando configurações na configuração de estado da automação do Azure](automation-dsc-compile.md).
Para obter mais informações sobre a compilação de configurações, consulte [configurações de DSC](/powershell/dsc/configurations).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **todos os recursos** e, em seguida, no nome da sua conta de automação.
1. Na página **conta de automação** , clique em **configuração de estado (DSC)** em **Gerenciamento de configuração**.
1. Na página **configuração de estado (DSC)** , clique na guia **configurações** e, em seguida, clique em **TestConfig** (o nome da configuração importada anteriormente).
1. Na página **configuração do TestConfig** , clique em **Compilar**e, em seguida, clique em **Sim**. Isso inicia um trabalho de compilação.

   ![Captura de tela da página de configuração do TestConfig realçando o botão de compilação](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Quando você compila uma configuração na automação do Azure, ela implanta automaticamente qualquer MOFs de configuração de nó criada no servidor de pull.

## <a name="viewing-a-compilation-job"></a>Exibindo um trabalho de compilação

Depois de iniciar uma compilação, você poderá exibi-la no bloco **trabalhos de compilação** na página **configuração** . O bloco **trabalhos de compilação** mostra os trabalhos atualmente em execução, concluídos e com falha. Quando você abre uma página de trabalho de compilação, ela mostra informações sobre esse trabalho, incluindo erros ou avisos encontrados, parâmetros de entrada usados na configuração e logs de compilação.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **todos os recursos** e, em seguida, no nome da sua conta de automação.
1. Na página **conta de automação** , clique em **configuração de estado (DSC)** em **Gerenciamento de configuração**.
1. Na página **configuração de estado (DSC)** , clique na guia **configurações** e, em seguida, clique em **TestConfig** (o nome da configuração importada anteriormente).
1. Em **trabalhos de compilação**, selecione o trabalho de compilação que você deseja exibir. Uma página de **trabalho de compilação** é aberta rotulada com a data em que o trabalho de compilação foi iniciado.

   ![Captura de tela da página de trabalho de compilação](./media/automation-dsc-getting-started/CompilationJob.png)

1. Clique em qualquer bloco na página **trabalho de compilação** para ver mais detalhes sobre o trabalho.

## <a name="viewing-node-configurations"></a>Exibindo configurações de nó

A conclusão bem-sucedida de um trabalho de compilação cria uma ou mais novas configurações de nó. Uma configuração de nó é um documento MOF que é implantado no servidor de pull e pronto para ser puxado e aplicado por um ou mais nós. Você pode exibir as configurações de nó em sua conta de automação na página de **configuração de estado (DSC)** . Uma configuração de nó tem um nome com o formulário *ConfigurationName*. *NodeName*.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **todos os recursos** e, em seguida, no nome da sua conta de automação.
1. Na folha **conta de automação** , clique em **configuração de estado (DSC)** em **Gerenciamento de configuração**.
1. Na página **configuração de estado (DSC)** , clique na guia **configurações compiladas** .

   ![Captura de tela da guia Configurações compiladas](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration"></a>Integração de uma VM do Azure para gerenciamento com a configuração de estado da automação do Azure

Você pode usar a configuração de estado da automação do Azure para gerenciar VMs do Azure (clássicas e Resource Manager), VMs locais, máquinas Linux, VMs AWS e máquinas físicas locais. Neste artigo, você aprende a carregar apenas Azure Resource Manager VMs. Para obter informações sobre a integração de outros tipos de computadores, consulte [máquinas de integração para gerenciamento pela configuração de estado da automação do Azure](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-state-configuration"></a>Para carregar uma VM Azure Resource Manager para gerenciamento pela configuração de estado da automação do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **todos os recursos** e, em seguida, no nome da sua conta de automação.
1. Na folha **conta de automação** , clique em **configuração de estado (DSC)** em **Gerenciamento de configuração**.
1. Na página **configuração de estado (DSC)** , na guia **nós** , clique em **+ Adicionar**.

   ![Captura de tela da página nós DSC destacando o botão Adicionar VM do Azure](./media/automation-dsc-getting-started/OnboardVM.png)

1. Na página **máquinas virtuais** , selecione sua VM.
1. Na página de detalhes da **máquina virtual** , clique em **+ conectar**.

   > [!IMPORTANT]
   > Isso deve ser uma VM Azure Resource Manager que esteja executando um [sistema operacional com suporte](automation-dsc-overview.md#operating-system-requirements).

2. Na página **registro** , selecione o nome da configuração do nó que você deseja aplicar à VM na caixa nome da **configuração do nó** . Fornecer um nome neste ponto é opcional. Você pode alterar a configuração de nó atribuída após a integração do nó.
   Marque a **reinicialização do nó, se necessário**, e clique em **OK**.

   ![Captura de tela da folha de registro](./media/automation-dsc-getting-started/RegisterVM.png)

   A configuração de nó especificada é aplicada à VM em intervalos especificados pela frequência do **modo de configuração**e a VM verifica se há atualizações para a configuração de nó em intervalos especificados pela **frequência de atualização**. Para obter mais informações sobre como esses valores são usados, consulte [Configurando o Configuration Manager local](/powershell/scripting/dsc/managing-nodes/metaConfig).

O Azure inicia o processo de integração da VM. Quando ele for concluído, a VM aparecerá na guia **nós** da página de **configuração de estado (DSC)** na conta de automação.

## <a name="viewing-the-list-of-managed-nodes"></a>Exibindo a lista de nós gerenciados

Você pode exibir a lista de todos os computadores que foram integrados para gerenciamento em sua conta de automação na guia **nós** da página de **configuração de estado (DSC)** .

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **todos os recursos** e, em seguida, no nome da sua conta de automação.
1. Na folha **conta de automação** , clique em **configuração de estado (DSC)** em **Gerenciamento de configuração**.
1. Na página **configuração de estado (DSC)** , clique na guia **nós** .

## <a name="viewing-reports-for-managed-nodes"></a>Exibindo relatórios para nós gerenciados

Cada vez que a configuração de estado da automação do Azure executa uma verificação de consistência em um nó gerenciado, o nó envia um relatório de status de volta para o servidor de pull. Você pode exibir esses relatórios na página desse nó.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **todos os recursos** e, em seguida, no nome da sua conta de automação.
1. Na folha **conta de automação** , clique em **configuração de estado (DSC)** em **Gerenciamento de configuração**.
1. Na página **configuração de estado (DSC)** , clique na guia **nós** . Aqui, você pode ver a visão geral do estado de configuração e os detalhes de cada nó.

   ![Captura de tela da página do nó](./media/automation-dsc-getting-started/NodesTab.png)

1. Na guia **nós** , clique no registro de nó para abrir o relatório. Clique no relatório que você deseja exibir detalhes adicionais do relatório.

   ![Captura de tela da folha do relatório](./media/automation-dsc-getting-started/NodeReport.png)

Na folha de um relatório individual, você pode ver as seguintes informações de status para a verificação de consistência correspondente:

- O status do relatório — se o nó é "compatível", a configuração "falha" ou o nó é "não compatível" (quando o nó está no modo **ApplyandMonitor** e o computador não está no estado desejado).
- A hora de início para a verificação de consistência.
- O tempo de execução total para a verificação de consistência.
- O tipo de verificação de consistência.
- Quaisquer erros, incluindo o código de erro e a mensagem de erro.
- Todos os recursos de DSC usados na configuração e o estado de cada recurso (se o nó estiver no estado desejado para esse recurso) — você pode clicar em cada recurso para obter informações mais detalhadas para esse recurso.
- O nome, o endereço IP e o modo de configuração do nó.

Você também pode clicar em **Exibir relatório bruto** para ver os dados reais que o nó envia para o servidor.
Para obter mais informações sobre como usar esses dados, consulte [usando um servidor de relatório de DSC](/powershell/dsc/reportserver).

Pode levar algum tempo depois que um nó é integrado antes de o primeiro relatório estar disponível. Talvez seja necessário aguardar até 30 minutos para o primeiro relatório depois que você carregar um nó.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Reatribuindo um nó a uma configuração de nó diferente

Você pode atribuir um nó para usar uma configuração de nó diferente daquela que você atribuiu inicialmente.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **todos os recursos** e, em seguida, no nome da sua conta de automação.
1. Na folha **conta de automação** , clique em **configuração de estado (DSC)** em **Gerenciamento de configuração**.
1. Na página **configuração de estado (DSC)** , clique na guia **nós** .
1. Na guia **nós** , clique no nome do nó que você deseja reatribuir.
1. Na página desse nó, clique em **atribuir configuração de nó**.

    ![Captura de tela da página detalhes do nó realçando o botão atribuir configuração de nó](./media/automation-dsc-getting-started/AssignNode.png)

1. Na página **atribuir configuração de nó** , selecione a configuração de nó à qual você deseja atribuir o nó e clique em **OK**.

    ![Captura de tela da página atribuir configuração de nó](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Cancelando o registro de um nó

Se você não quiser mais que um nó seja gerenciado pelo Azure DSC de Automação, poderá cancelar seu registro.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **todos os recursos** e, em seguida, no nome da sua conta de automação.
1. Na folha **conta de automação** , clique em **configuração de estado (DSC)** em **Gerenciamento de configuração**.
1. Na página **configuração de estado (DSC)** , clique na guia **nós** .
1. Na guia **nós** , clique no nome do nó no qual você deseja cancelar o registro.
1. Na página desse nó, clique em **Cancelar registro**.

    ![Captura de tela da página detalhes do nó destacando o botão Cancelar registro](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Artigos relacionados

- [Visão geral da configuração do estado de automação do Azure](automation-dsc-overview.md)
- [Máquinas de integração para gerenciamento pela configuração de estado da automação do Azure](automation-dsc-onboarding.md)
- [Visão geral da configuração de estado desejado do Windows PowerShell](/powershell/dsc/overview)
- [Cmdlets de configuração do estado de automação do Azure](/powershell/module/azurerm.automation/#automation)
- [Preço da configuração do estado de automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
