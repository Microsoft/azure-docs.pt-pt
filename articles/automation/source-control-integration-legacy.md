---
title: Integração de controle do código-fonte na automação do Azure-herdado
description: Este artigo descreve a integração do controle do código-fonte com o GitHub na automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2019
ms.topic: conceptual
ms.openlocfilehash: 651b97dabfd3cce858ea1f905a39c10bd7d81c41
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75417445"
---
# <a name="source-control-integration-in-azure-automation---legacy"></a>Integração de controle do código-fonte na automação do Azure-herdado

> [!NOTE]
> Há uma nova experiência para o controle do código-fonte. Para saber mais sobre a nova experiência, consulte [controle do código-fonte (versão prévia)](source-control-integration.md).

A integração de controle do código-fonte permite associar runbooks em sua conta de automação a um repositório de controle do código-fonte do GitHub. O controle do código-fonte permite que você colabore facilmente com sua equipe, controle alterações e reverta para versões anteriores de seus runbooks. Por exemplo, o controle do código-fonte permite que você sincronize diferentes branches no controle do código-fonte para suas contas de desenvolvimento, teste ou automação de produção, facilitando a promoção de código que foi testado em seu ambiente de desenvolvimento para a automação de produção considerar.

O controle de origem permite enviar código por push da automação do Azure para o controle do código-fonte ou extrair seus runbooks do controle do código-fonte para a automação do Azure. Este artigo descreve como configurar o controle do código-fonte em seu ambiente de automação do Azure. Começamos Configurando a automação do Azure para acessar seu repositório GitHub e percorrer operações diferentes que podem ser feitas usando a integração de controle do código-fonte. 

> [!NOTE]
> O controle do código-fonte dá suporte à obtenção [e ao envio](automation-runbook-types.md#powershell-runbooks)de Runbooks de fluxo de trabalho do [PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) , bem como Ainda não há suporte para [Runbooks gráficos](automation-runbook-types.md#graphical-runbooks) .

Há duas etapas simples necessárias para configurar o controle do código-fonte para sua conta de automação e apenas uma se você já tiver uma conta do GitHub. São:

## <a name="step-1--create-a-github-repository"></a>Etapa 1 – criar um repositório GitHub

Se você já tiver uma conta do GitHub e um repositório que deseja vincular à automação do Azure, entre em sua conta existente e inicie na etapa 2 abaixo. Caso contrário, navegue até o [GitHub](https://github.com/), Inscreva-se para uma nova conta e [crie um novo repositório](https://help.github.com/articles/create-a-repo/).

## <a name="step-2--set-up-source-control-in-azure-automation"></a>Etapa 2 – configurar o controle do código-fonte na automação do Azure

1. Na página conta de automação no portal do Azure, em **configurações da conta**, clique em **controle do código-fonte.**

2. A página **controle do código-fonte** é aberta, na qual você pode configurar os detalhes da conta do github. Segue-se a lista de parâmetros a configurar:  

   | **Parâmetro** | **Descrição** |
   |:--- |:--- |
   | Escolher Origem |Selecione a origem. Atualmente, há suporte apenas para o **GitHub** . |
   | Autorização |Clique no botão **autorizar** para conceder acesso de automação do Azure ao repositório github. Se você já estiver conectado à sua conta do GitHub em uma janela diferente, as credenciais dessa conta serão usadas. Depois que a autorização for bem-sucedida, a página mostrará o nome de usuário do GitHub em **propriedade de autorização**. |
   | Escolher repositório |Selecione um repositório GitHub na lista de repositórios disponíveis. |
   | Escolher ramificação |Selecione uma ramificação na lista de branches disponíveis. Somente a ramificação **mestre** será mostrada se você não tiver criado ramificações. |
   | Caminho da pasta do runbook |O caminho da pasta do runbook especifica o caminho no repositório GitHub do qual você deseja enviar por Push ou extrair seu código. Ele deve ser inserido no formato **/foldername/subfoldername**. Somente runbooks no caminho da pasta do runbook serão sincronizados com sua conta de automação. Os Runbooks nas subpastas do caminho da pasta do runbook **não** serão sincronizados. Use **/** para sincronizar todos os runbooks no repositório. |
3. Por exemplo, se você tiver um repositório chamado **PowerShellScripts** que contém uma pasta chamada **RootFolder**, que contém uma pasta chamada **subpasta**. Você pode usar as seguintes cadeias de caracteres para sincronizar cada nível de pasta:

   1. Para sincronizar runbooks do **repositório**, o caminho da pasta do runbook é */*
   2. Para sincronizar runbooks de **RootFolder**, o caminho da pasta do runbook é */RootFolder*
   3. Para sincronizar runbooks da **subpasta**, o caminho da pasta do runbook é */RootFolder/SubFolder*.
4. Depois de configurar os parâmetros, eles são exibidos na página **configurar controle do código-fonte** .  

    ![A página de controle do código-fonte mostrando as configurações](media/source-control-integration-legacy/automation-SourceControlConfigure.png)
5. Depois de clicar em **OK**, a integração de controle do código-fonte agora será configurada para sua conta de automação e deverá ser atualizada com as informações do github. Agora você pode clicar nesta parte para exibir todo o seu histórico de trabalho de sincronização de controle do código-fonte.  

    ![Valores para a configuração de controle do código-fonte configurada atual](media/source-control-integration-legacy/automation-RepoValues.png)
6. Depois de configurar o controle do código-fonte, dois [ativos variáveis](automation-variables.md) são criados na sua conta de automação. Além disso, um aplicativo autorizado é adicionado à sua conta do GitHub.

   * A variável **Microsoft. Azure. Automation. SourceControl. Connection** contém os valores da cadeia de conexão, conforme mostrado abaixo.  

     | **Parâmetro** | **Valor** |
     |:--- |:--- |
     | `Name`  |Microsoft.Azure.Automation.SourceControl.Connection |
     | `Type`  |String |
     | `Value` |{"Branch":\<*seu nome de Branch*>, "RunbookFolderPath":\<*caminho da pasta do runbook*>, "ProviderType":\<*tem um valor 1 para o GitHub*>, "Repository":\<*nome do seu repositório*>, "username":\<*seu nome de usuário do GitHub*>} |

   * A variável **Microsoft. Azure. Automation. SourceControl. OAuthToken**, contém o valor criptografado seguro de seu OAuthToken.  

     |**Parâmetro**            |**Valor** |
     |:---|:---|
     | `Name`  | Microsoft.Azure.Automation.SourceControl.OAuthToken |
     | `Type`  | Desconhecido (criptografado) |
     | `Value` | <*OAuthToken criptografados*> |  

     ![Uma janela mostrando variáveis de controle do código-fonte](media/source-control-integration-legacy/automation-Variables.png)  

   * O **controle do código-fonte da automação** é adicionado como um aplicativo autorizado à sua conta do github. Para exibir o aplicativo: em seu home page do GitHub, navegue até o **perfil** > **configurações** > **aplicativos**. Esse aplicativo permite que a automação do Azure Sincronize seu repositório GitHub para uma conta de automação.  

     ![Configurações do aplicativo no GitHub](media/source-control-integration-legacy/automation-GitApplication.png)

## <a name="using-source-control-in-automation"></a>Usando o controle do código-fonte na automação

### <a name="check-in-a-runbook-from-azure-automation-to-source-control"></a>Fazer check-in de um runbook da automação do Azure para o controle do código-fonte

O check-in de runbook permite enviar por push as alterações feitas a um runbook na automação do Azure no repositório do controle do código-fonte. Abaixo estão as etapas para fazer check-in de um runbook:

1. Na sua conta de automação, [crie um novo runbook textual](automation-first-runbook-textual.md)ou [edite um runbook textual existente](automation-edit-textual-runbook.md). Esse runbook pode ser um fluxo de trabalho do PowerShell ou um runbook de script do PowerShell.  
2. Depois de editar o runbook, salve-o e clique em **check-in** na página **Editar** .  

    ![Uma janela que mostra o check-in para o botão do GitHub](media/source-control-integration-legacy/automation-CheckinButton.png)

     > [!NOTE] 
     > O check-in da automação do Azure substitui o código que existe atualmente no controle do código-fonte. A instrução de linha de comando equivalente do git para fazer check-in é **git adicionar + git Commit + git Push**  

3. Ao clicar em **check-in**, será exibida uma mensagem de confirmação, clique em **Sim** para continuar.  

    ![Uma caixa de diálogo confirmando o check-in no controle do código-fonte](media/source-control-integration-legacy/automation-CheckinMessage.png)
4. O check-in inicia o runbook de controle do código-fonte: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Este runbook se conecta ao GitHub e envia por push as alterações da automação do Azure para seu repositório. Para exibir o histórico de trabalho verificado, volte para a guia **integração de controle do código-fonte** e clique para abrir a página sincronização do repositório. Esta página mostra todos os seus trabalhos de controle do código-fonte.  Selecione o trabalho que você deseja exibir e clique para exibir os detalhes.  

    ![Uma janela mostrando os resultados de um trabalho de sincronização](media/source-control-integration-legacy/automation-CheckinRunbook.png)

   > [!NOTE]
   > Runbooks de controle do código-fonte são runbooks de automação especiais que você não pode exibir ou editar. Embora eles não apareçam na lista de runbooks, você verá trabalhos de sincronização em sua lista de trabalhos.

5. O nome do runbook modificado é enviado como um parâmetro de entrada para o runbook selecionado. Você pode [exibir os detalhes do trabalho](automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) expandindo o runbook na página **sincronização do repositório** .  

    ![Uma janela mostrando a entrada para um trabalho de sincronização](media/source-control-integration-legacy/automation-CheckinInput.png)
6. Atualize seu repositório GitHub depois que o trabalho for concluído para exibir as alterações.  Deve haver uma confirmação em seu repositório com uma mensagem de confirmação:  ***nome do runbook* atualizado na automação do Azure.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Sincronizar runbooks do controle do código-fonte para a automação do Azure

O botão Sincronizar na página de sincronização do repositório permite que você Extraia todos os runbooks no caminho da pasta do runbook do repositório para sua conta de automação. O mesmo repositório pode ser sincronizado com mais de uma conta de automação. Abaixo estão as etapas para sincronizar um runbook:

1. Na conta de automação em que você configurou o controle do código-fonte, abra a página de **sincronização de controle do código-fonte/repositório** e clique em **sincronizar**.  Você receberá uma mensagem de confirmação, clique em **Sim** para continuar.  

    ![O botão Sincronizar com a mensagem confirmando todos os runbooks será sincronizado](media/source-control-integration-legacy/automation-SyncButtonwithMessage.png)

2. A sincronização inicia o runbook: **Sync-MicrosoftAzureAutomationAccountFromGitHubV1**. Esse runbook se conecta ao GitHub e efetua pull das alterações de seu repositório para a automação do Azure. Você deverá ver um novo trabalho na página de **sincronização do repositório** para esta ação. Para exibir detalhes sobre o trabalho de sincronização, clique para abrir a página de detalhes do trabalho.  

    ![Uma janela mostrando os resultados da sincronização de um trabalho de sincronização em um repositório GitHub](media/source-control-integration-legacy/automation-SyncRunbook.png)

    > [!NOTE]
    > Uma sincronização do controle do código-fonte substitui a versão de rascunho dos runbooks que existem atualmente em sua conta de automação para **todos os** runbooks que estão atualmente no controle do código-fonte. A instrução de linha de comando equivalente do git a ser sincronizada é o **pull do git**

![Uma janela mostrando todos os logs de um trabalho de sincronização de controle do código-fonte suspenso](media/source-control-integration-legacy/automation-AllLogs.png)

## <a name="disconnecting-source-control"></a>Desconectando controle do código-fonte

Para se desconectar da sua conta do GitHub, abra a página de sincronização do repositório e clique em **Desconectar**. Depois de desconectar o controle do código-fonte, os runbooks que foram sincronizados anteriormente ainda permanecem em sua conta de automação, mas a página de sincronização do repositório não será habilitada.  

  ![Uma janela que mostra o botão desconectar para desconectar o controle do código-fonte](media/source-control-integration-legacy/automation-Disconnect.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a integração de controle do código-fonte, consulte os seguintes recursos:  

* [Automação do Azure: integração de controle do código-fonte na automação do Azure](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [Automação do Azure: integrando o controle do código-fonte do runbook usando o Azure DevOps](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  
