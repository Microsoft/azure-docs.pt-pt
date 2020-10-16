---
title: Utilizar a integração do Controlo de Origem na Azure Automation - Legacy
description: Este artigo diz como usar a integração do controlo de origem.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2019
ms.topic: conceptual
ms.openlocfilehash: f1e4e288b5b95f355221188a45f1e6c764fde77c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86187341"
---
# <a name="use-source-control-integration-in-azure-automation---legacy"></a>Utilize a integração de controlo de fonte na Azure Automation - Legacy

> [!NOTE]
> Há uma nova experiência para o controlo de fontes. Para saber mais sobre a nova experiência, consulte [Source Control (Preview)](source-control-integration.md).

A integração do Controlo de Origem permite-lhe associar os runbooks na sua conta Automation a um repositório de controlo de fontes GitHub. O controlo de origem permite-lhe colaborar facilmente com a sua equipa, rastrear alterações e voltar para versões anteriores dos seus runbooks. Por exemplo, o controlo de fontes permite sincronizar diferentes ramos no controlo de fontes às suas contas de desenvolvimento, teste ou produção Demômida, facilitando a promoção de código que foi testado no seu ambiente de desenvolvimento para a sua conta de automação de produção.

O Controlo de Origem permite-lhe empurrar o código da Azure Automation para obter controlo ou retirar os seus livros de origem do controlo de origem para a Azure Automation. Este artigo descreve como configurar o controlo de origem no seu ambiente Azure Automation. Começamos por configurar a Azure Automation para aceder ao seu repositório GitHub e percorrer diferentes operações que podem ser feitas usando a integração do controlo de fontes. 

> [!NOTE]
> O Source Control suporta puxar e empurrar os livros de [fluxo de trabalho powerShell,](automation-runbook-types.md#powershell-workflow-runbooks) bem como [os livros de execução PowerShell](automation-runbook-types.md#powershell-runbooks). [Os livros gráficos](automation-runbook-types.md#graphical-runbooks) ainda não estão suportados.

## <a name="configure-source-control"></a>Configure o controlo da fonte

Existem dois passos simples necessários para configurar o controlo de origem para a sua conta Automation, e apenas um se já tiver uma conta GitHub. 

### <a name="create-a-github-repository"></a>Criar um repositório GitHub

Se já tem uma conta GitHub e um repositório que pretende ligar à Azure Automation, então assine na sua conta existente e comece a partir do passo 2 abaixo. Caso contrário, navegue para [o GitHub,](https://github.com/)inscreva-se para uma nova conta e [crie um novo repositório.](https://help.github.com/articles/create-a-repo/)

### <a name="set-up-source-control"></a>Configurar controlo de origem

1. A partir da página 'Conta de Automação' no portal Azure, em **Definições de Conta,** clique em **Controlo de Origem.**

2. A página Controlo de Origem abre, onde pode configurar os detalhes da sua conta GitHub. Segue-se a lista de parâmetros a configurar:  

   | **Parâmetro** | **Descrição** |
   |:--- |:--- |
   | Escolha Fonte |Selecione a fonte. Atualmente, apenas **o GitHub** é apoiado. |
   | Autorização |Clique no botão **Authorize** para conceder acesso à Azure Automation ao seu repositório GitHub. Se já tiver iniciado sessão na sua conta GitHub numa janela diferente, então as credenciais dessa conta são utilizadas. Uma vez que a autorização seja bem sucedida, a página mostra o seu nome de utilizador GitHub sob **Propriedade de Autorização**. |
   | Escolha o repositório |Selecione um repositório GitHub da lista de repositórios disponíveis. |
   | Escolha o ramo |Selecione um ramo da lista de ramos disponíveis. Só o **ramo principal** é mostrado se não criou nenhum ramo. |
   | Caminho da pasta do runbook |O caminho da pasta do livro de execução especifica o caminho no repositório GitHub a partir do qual pretende empurrar ou puxar o seu código. Deve ser introduzido no formato **/nome de pasta/sub-nome**. Apenas os livros de execução no caminho da pasta do livro serão sincronizados na sua conta Dem automação. Os livros de bordo nas sub-dobradeiras do percurso da pasta do livro **não** serão sincronizados. Utilize **/** para sincronizar todos os livros sob o repositório. |
3. Por exemplo, se tiver um repositório chamado **PowerShellScripts** que contenha uma pasta chamada **RootFolder**, que contém uma pasta chamada **SubFolder**. Pode utilizar as seguintes cordas para sincronizar cada nível de pasta:

   1. Para sincronizar os livros a partir do **repositório,** o caminho da pasta do livro é **/** .
   2. Para sincronizar os runbooks do **RootFolder,** o caminho da pasta do livro de bordo é **/RootFolder**.
   3. Para sincronizar os runbooks do **SubFolder,** o caminho da pasta do livro de bordo é **/RootFolder/SubFolder**.
4. Depois de configurar os parâmetros, são apresentados na página 'set Up Source Control'.  

    ![A página de controlo 'Fonte' mostrando as definições](media/source-control-integration-legacy/automation-SourceControlConfigure.png)
5. Uma vez que clique **em OK,** a integração do controlo de origem está agora configurada para a sua conta Dem automação e deve ser atualizada com as informações do GitHub. Agora pode clicar nesta parte para ver todo o histórico de trabalho sincronizado de controlo de origem.  

    ![Valores para a configuração de controlo de origem configurada atual](media/source-control-integration-legacy/automation-RepoValues.png)
6. Depois de configurar o controlo de origem, são criados dois [ativos variáveis](./shared-resources/variables.md) na sua conta Demôm automação. Adicionalmente, um pedido autorizado é adicionado à sua conta GitHub.

   * A variável **Microsoft.Azure.Automation.SourceControl.Connection** contém os valores da cadeia de ligação, como mostrado abaixo.  

     | **Parâmetro** | **Valor** |
     |:--- |:--- |
     | `Name`  |Microsoft.Azure.Automation.SourceControl.Connection |
     | `Type`  |Cadeia |
     | `Value` |{"Branch": \<*Your branch name*> "RunbookFolderPath": \<*Runbook folder path*> "ProviderType": \<*has a value 1 for GitHub*> "Repositório": \<*Name of your repository*> "Nome de utilizador": \<*Your GitHub user name*> } |

   * A variável **Microsoft.Azure.Automation.SourceControl.OAuthToken**, contém o valor encriptado seguro do seu OAuthToken.  

     |**Parâmetro**            |**Valor** |
     |:---|:---|
     | `Name`  | `Microsoft.Azure.Automation.SourceControl.OAuthToken` |
     | `Type`  | `Unknown(Encrypted)` |
     | `Value` | <`Encrypted OAuthToken`> |  

     ![Uma janela mostrando variáveis de controlo de origem](media/source-control-integration-legacy/automation-Variables.png)  

   * **O Controlo de Fontes de Automação** é adicionado como uma aplicação autorizada à sua conta GitHub. Para ver a aplicação, a partir da sua página inicial do GitHub, navegue para **perfil**  >  **Settings**  >  **Configurações Aplicações**. Esta aplicação permite à Azure Automation sincronizar o seu repositório GitHub numa conta Automation.  

     ![Definições de aplicações no GitHub](media/source-control-integration-legacy/automation-GitApplication.png)

## <a name="use-source-control-in-automation"></a>Utilize o controlo de fontes na Automação

O check-in do runbook permite-lhe empurrar as alterações que fez a um livro de recortes na Azure Automation para o seu repositório de controlo de origem. Abaixo estão os passos para verificar num livro de bordo:

1. A partir da sua conta Automation, [crie um novo livro textual,](./learn/automation-tutorial-runbook-textual.md)ou edite um livro de texto [existente.](automation-edit-textual-runbook.md) Este livro pode ser um fluxo de trabalho PowerShell ou um script de powerShell.  
2. Depois de editar o seu runbook, guarde-o e clique **em Check-in** na página de edição.  

    ![Uma janela mostrando o checkin para o botão GitHub](media/source-control-integration-legacy/automation-CheckinButton.png)

     > [!NOTE] 
     > O check-in da Azure Automation substitui o código que existe atualmente no seu controlo de origem. A instrução equivalente de linha de comando git para check-in é **git add + git commit + git push**  

3. Quando clicar **no check-in,** é solicitado com uma mensagem de confirmação, clique em **Sim** para continuar.  

    ![Uma caixa de diálogo confirmando o check-in para o controlo de origem](media/source-control-integration-legacy/automation-CheckinMessage.png)
4. O check-in inicia o manual de controlo de origem: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Este runbook liga-se ao GitHub e empurra as mudanças da Azure Automation para o seu repositório. Para ver o histórico de trabalho, volte ao separador **Integração de Controlo de Origem** e clique para abrir a página de Sincronização do Repositório. Esta página mostra todos os seus trabalhos de controlo de origem. Selecione o trabalho que deseja ver e clique para ver os detalhes.  

    ![Uma janela mostrando os resultados de um trabalho de sincronização](media/source-control-integration-legacy/automation-CheckinRunbook.png)

   > [!NOTE]
   > Os runbooks de controlo de origem são livros de automação especiais que não pode ver ou editar. Embora não apareçam na sua lista de runbook, vê trabalhos de sincronização na sua lista de empregos.

5. O nome do livro de execução modificado é enviado como parâmetro de entrada para o runbook verificado. Pode [ver os detalhes do trabalho](automation-runbook-execution.md#job-statuses) expandindo o runbook na página de Sincronização do Repositório.  

    ![Uma janela mostrando a entrada para um trabalho de sincronização](media/source-control-integration-legacy/automation-CheckinInput.png)
6. Refresque o seu repositório GitHub assim que o trabalho estiver concluído para ver as alterações.  Deve haver um compromisso no seu repositório com uma mensagem de compromisso: ** *Nome do runbook* atualizado na Azure Automation.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Sync runbooks do controlo de origem para a Azure Automation

O botão de sincronização na página de Sincronização repositória permite-lhe puxar todos os livros de execução no caminho da pasta do seu repositório para a sua conta Dem automação. O mesmo repositório pode ser sincronizado com mais de uma conta de Automação. Abaixo estão os passos para sincronizar um livro de bordo:

1. A partir da conta Automation onde configura o controlo de fontes, abra a página de Sincronização integração/sincronização do controlo de origem e clique em **Sync**.  É solicitado com uma mensagem de confirmação, clique em **Sim** para continuar.  

    ![Sync botão com mensagem confirmando todos os runbooks serão sincronizados](media/source-control-integration-legacy/automation-SyncButtonwithMessage.png)

2. O Sync inicia o  **livro de aplicação Sync-MicrosoftAzureAutomationAccountFromGitHubV1,** que se conecta ao GitHub e retira as alterações do seu repositório para a Azure Automation. Devias ver um novo emprego na página de sincronização do Repositório para esta ação. Para ver detalhes sobre o trabalho de sincronização, clique para abrir a página de detalhes do trabalho.  

    ![Uma janela mostrando os resultados sincronizados de um trabalho de sincronização num repositório do GitHub](media/source-control-integration-legacy/automation-SyncRunbook.png)

    > [!NOTE]
    > Uma sincronização do controlo de origem substitui a versão de projeto dos runbooks que existem atualmente na sua conta de Automação para **todos os** runbooks que estão atualmente sob controlo de origem. A instrução equivalente da linha de comando git para sincronizar é **puxar git**

![Uma janela mostrando todos os registos de um trabalho de sincronização de controlo de fonte suspensa](media/source-control-integration-legacy/automation-AllLogs.png)

## <a name="disconnect-source-control"></a>Desligar o controlo da fonte

Para desligar a sua conta GitHub, abra a página de sincronização do Repositório e clique em **Desligar**. Uma vez desligado o controlo de origem, os livros que foram sincronizados anteriormente ainda permanecem na sua conta de Automação, mas a página de Sincronização repositória não será ativada.  

  ![Uma janela mostrando o botão Desligar para desligar o controlo de origem](media/source-control-integration-legacy/automation-Disconnect.png)

## <a name="next-steps"></a>Passos seguintes

* Para integrar o controlo de origem na Azure Automation, consulte [a Azure Automation: Source Control Integration in Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/).  
* Para integrar o controlo de origem do runbook com o Visual Studio Online, consulte [a Azure Automation: Integrando o Runbook Source Control utilizando o Visual Studio Online](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/).  
