---
title: Integração de controlo na automatização do Azure - herdado de origem
description: Este artigo descreve a integração do controlo de origem com o GitHub na automatização do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: da9b82b1e17a62aa9b3d606b0b16295acf04eb85
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418759"
---
# <a name="source-control-integration-in-azure-automation---legacy"></a>Integração do controlo de origem na automatização do Azure - legado

> [!NOTE]
> Há uma nova experiência para controlo de origem. Para saber mais sobre a nova experiência, veja [controlo de origem (pré-visualização)](source-control-integration.md).

Integração do controlo de origem permite-lhe associar os runbooks na sua conta de automatização para um repositório de controle de origem do GitHub. Controlo de origem permite-lhe facilmente colaborar com sua equipe, controlar as alterações e reverta para versões anteriores dos seus runbooks. Por exemplo, o controle de origem permite-lhe sincronizar ramificações diferentes no controle de origem para os desenvolvimento, teste ou produção as contas de automatização, que facilita a promover o código que foi testado no seu ambiente de desenvolvimento para a produção de automatização conta.

Controlo de origem permite-lhe emitir o código da automatização do Azure para o controlo de origem ou extrair os runbooks no controle da fonte à automatização do Azure. Este artigo descreve como configurar o controlo de origem no seu ambiente de automatização do Azure. Vamos começar por configurar a automatização do Azure para aceder ao seu repositório do GitHub e percorrer diferentes operações que podem ser feitas usando a integração de controlo de origem. 

> [!NOTE]
> O controlo de origem suporta extrair e enviar [runbooks de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) , bem como [runbooks do PowerShell](automation-runbook-types.md#powershell-runbooks). [Os runbooks gráficos](automation-runbook-types.md#graphical-runbooks) ainda não são suportadas.<br><br>
> 
> 

Existem duas simples etapas necessárias para configurar o controlo de origem para a sua conta de automatização e apenas uma se já tiver uma conta do GitHub. São:

## <a name="step-1--create-a-github-repository"></a>Passo 1 – criar um repositório do GitHub
Se já tiver uma conta do GitHub e um repositório de que pretende associar a automatização do Azure, em seguida, iniciar sessão na sua conta existente e iniciar a partir do passo 2 abaixo. Caso contrário, navegue até [GitHub](https://github.com/), inscreva-se numa conta nova e [criar um novo repositório](https://help.github.com/articles/create-a-repo/).

## <a name="step-2--set-up-source-control-in-azure-automation"></a>Passo 2 – configurar o controlo de origem na automatização do Azure
1. A conta de automatização página no portal do Azure, em **as definições da conta**, clique em **controlo de origem.** 
   
1. O **controlo de origem** é aberta, onde pode configurar os detalhes da sua conta GitHub a página. Segue-se a lista de parâmetros a configurar:  
   
   | **Parâmetro** | **Descrição** |
   |:--- |:--- |
   | Escolher Origem |Selecione a origem. Atualmente, apenas **GitHub** é suportada. |
   | Autorização |Clique nas **autorizar** botão para conceder acesso de automatização do Azure para o seu repositório do GitHub. Se tiver sessão iniciada sua conta do GitHub numa janela diferente, em seguida, são utilizadas as credenciais dessa conta. Assim que a autorização for bem sucedida, a página mostrará o seu nome de utilizador do GitHub sob **propriedade de autorização**. |
   | Escolher repositório |Selecione um repositório do GitHub a partir da lista de repositórios disponíveis. |
   | Escolher ramo |Selecione um ramo a partir da lista de ramos disponíveis. Apenas os **mestre** ramo é apresentado se ainda não criou quaisquer ramos. |
   | Caminho da pasta de Runbook |O caminho da pasta de runbook Especifica o caminho no repositório do GitHub a partir do qual pretende enviar por push ou pull seu código. Têm de ser introduzido no formato **/nomedapasta/nomedasubpasta**. Apenas os runbooks no caminho da pasta de runbook serão sincronizados com a sua conta de automatização. Runbooks em subpastas do caminho da pasta de runbook irão **não** sincronizado. Uso **/** para sincronizar todos os runbooks em que o repositório. |
3. Por exemplo, se tiver um repositório com o nome **PowerShellScripts** que contém uma pasta denominada **RootFolder**, que contém uma pasta denominada **subpasta**. Pode utilizar as seguintes cadeias de caracteres para cada nível de pasta de sincronização:
   
   1. Para sincronizar runbooks a partir **repositório**, é o caminho da pasta de runbook */*
   2. Para sincronizar runbooks a partir **RootFolder**, o caminho de pasta do runbook é */RootFolder*
   3. Para sincronizar runbooks a partir **subpasta**, é o caminho da pasta de runbook */RootFolder/subpasta*.
4. Depois de configurar os parâmetros, estes são apresentados os **no controlo de origem** página.  
   
    ![Configurar a página de controle de origem](media/automation-source-control-integration-legacy/automation_02_SourceControlConfigure.png)
5. Assim que clicar em **OK**, integração de controlo de origem está agora configurada para a sua conta de automatização e devem ser atualizada com as suas informações do GitHub. Agora pode clicar nesta parte para ver todos os seu histórico de tarefas de sincronização de controlo de origem.  
   
    ![Valores de repositório](media/automation-source-control-integration-legacy/automation_03_RepoValues.png)
6. Depois de configurar o controlo de origem, serão criados os seguintes recursos de automatização na conta de automatização:  
   Dois [recursos de variável](automation-variables.md) são criados.  
   
   * A variável **Microsoft.Azure.Automation.SourceControl.Connection** contém os valores de cadeia de ligação, conforme mostrado abaixo.  
     
     | **Parâmetro** | **Valor** |
     |:--- |:--- |
     | `Name`  |Microsoft.Azure.Automation.SourceControl.Connection |
     | `Type`  |String |
     | `Value` |{"Ramo":\<*o nome do ramo*>, "RunbookFolderPath":\<*o caminho de pasta do Runbook*>, "ProviderType":\<*tem um valor de 1 para GitHub*>, "Repositório":\<*nome do seu repositório*>, "Nomedeutilizador":\<*nome de utilizador do Your GitHub*>} |

     * A variável **Microsoft.Azure.Automation.SourceControl.OAuthToken**, contém o valor encriptado seguro de sua OAuthToken.  

     |**Parâmetro**            |**Valor** |
     |:---|:---|
     | `Name`  | Microsoft.Azure.Automation.SourceControl.OAuthToken |
     | `Type`  | UNKNOWN(Encrypted) |
     | `Value` | <*OAuthToken encriptado*> |  

     ![Variáveis](media/automation-source-control-integration-legacy/automation_04_Variables.png)  

     * **Controlo de código fonte da automatização** é adicionado como uma aplicação autorizada para a sua conta do GitHub. Para ver a aplicação: A partir da sua home page do GitHub, navegue para o **perfil** > **definições** > **aplicativos**. Esta aplicação permite que a automatização do Azure sincronizar o seu repositório do GitHub para uma conta de automatização.  

     ![Aplicação de Git](media/automation-source-control-integration-legacy/automation_05_GitApplication.png)


## <a name="using-source-control-in-automation"></a>Usando o controle de origem na automatização
### <a name="check-in-a-runbook-from-azure-automation-to-source-control"></a>Entrada de um runbook da automatização do Azure ao controlo de origem
Verificação do Runbook no permite-lhe envie as alterações efetuadas a um runbook na automatização do Azure no seu repositório de controle de origem. Seguem-se os passos para registar um runbook:

1. Da sua conta de automatização [criar um novo runbook textual](automation-first-runbook-textual.md), ou [editar um runbook existente, textual](automation-edit-textual-runbook.md). Este runbook pode ser um fluxo de trabalho do PowerShell ou um runbook de script do PowerShell.  
2. Depois de editar o runbook, guarde-o e clique em **check-in** partir do **editar** página.  
   
    ![Botão de dar entrada](media/automation-source-control-integration-legacy/automation_06_CheckinButton.png)

     > [!NOTE] 
     > Entrada da automatização do Azure substitui o código que existe atualmente no seu controle de origem. O Git de instrução de linha de comandos equivalente a entrada é **git adicionar + consolidação de git + git push**  

1. Quando clica em **check-in**, obterá uma mensagem de confirmação, clique em **Sim** para continuar.  
   
    ![Mensagem de Entrada](media/automation-source-control-integration-legacy/automation_07_CheckinMessage.png)
2. Entrada inicia o runbook de controle de origem: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Este runbook liga-se ao GitHub e envia as alterações de automatização do Azure para o seu repositório. Para ver a verificação no histórico da tarefa, volte para o **integração do controlo de origem** separador e clique para abrir a página de sincronização do repositório. Esta página mostra todas as tarefas de controle de origem.  Selecione a tarefa que pretende ver e clique para ver os detalhes.  
   
    ![Runbook de dar entrada](media/automation-source-control-integration-legacy/automation_08_CheckinRunbook.png)
   
   > [!NOTE]
   > Runbooks de controle de origem são runbooks de automatização especiais que não é possível ver ou editar. Embora eles não aparecem na sua lista de runbook, verá que mostra na sua lista de tarefas de trabalhos de sincronização.
   > 
   > 
3. O nome do runbook modificado é enviado como um parâmetro de entrada para o runbook selecionado na. Pode [ver os detalhes da tarefa](automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) expandindo o runbook na **sincronização de repositório** página.  
   
    ![Entrada de dar entrada](media/automation-source-control-integration-legacy/automation_09_CheckinInput.png)
4. Atualize seu repositório do GitHub, uma vez concluída a tarefa para ver as alterações.  Deve haver uma consolidação no repositório com uma mensagem de consolidação: **Atualizado *nome do Runbook* na automatização do Azure.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Runbooks de sincronização no controle da fonte para a automatização do Azure
O botão Sincronizar na página de sincronização de repositório permite-lhe extrair todos os runbooks no caminho da pasta do runbook do repositório para sua conta de automatização. O mesmo repositório pode ser sincronizado com mais de uma conta de automatização. Seguem-se os passos para sincronizar um runbook:

1. A partir da conta de automatização onde configura o controle de origem, abra a **sincronização de integração/repositório de controle de origem** página e clique em **sincronização**.  Obterá uma mensagem de confirmação, clique em **Sim** para continuar.  
   
    ![Botão Sincronizar](media/automation-source-control-integration-legacy/automation_10_SyncButtonwithMessage.png)
2. O runbook inicia a sincronização: **Sync-MicrosoftAzureAutomationAccountFromGitHubV1**. Este runbook liga-se ao GitHub e obtém as alterações do seu repositório para automatização do Azure. Deverá ver uma nova tarefa no **sincronização de repositório** página para esta ação. Para ver detalhes sobre a tarefa de sincronização, clique para abrir a página de detalhes da tarefa.  
   
    ![Runbook de sincronização](media/automation-source-control-integration-legacy/automation_11_SyncRunbook.png)

    > [!NOTE] 
    > Uma sincronização a partir do controlo de origem substitui a versão de rascunho os runbooks que existem atualmente na sua conta de automatização para **todos os** runbooks que estão atualmente nos controle de origem. O Git de instrução de linha de comandos equivalente a sincronização é **pull de git**

![Imagem de AllLogs](media/automation-source-control-integration-legacy/automation_13_AllLogs.png)

## <a name="disconnecting-source-control"></a>A desligar o controlo de origem
Ao desligar da sua conta do GitHub, abra a página de sincronização do repositório e clique em **desligar**. Depois de desligar o controlo de origem, os runbooks que foram sincronizados anteriormente ainda permanecem na conta de automatização, mas a página de sincronização de repositório não será ativada.  

  ![Botão de desligar](media/automation-source-control-integration-legacy/automation_12_Disconnect.png)

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre a integração de controlo de origem, consulte os seguintes recursos:  

* [Automatização do Azure: Integração do controlo de origem na automatização do Azure](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [Voto para o sistema de controle de origem favorito](https://www.surveymonkey.com/r/?sm=2dVjdcrCPFdT0dFFI8nUdQ%3d%3d)  
* [Automatização do Azure: Integração de controlo de origem do Runbook através do Azure DevOps](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  


