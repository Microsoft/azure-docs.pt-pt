---
title: Integração de Controlo de Fontes na Automação Azure - Legado
description: Este artigo descreve a integração do controlo de fontes com o GitHub na Automação Azure.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2019
ms.topic: conceptual
ms.openlocfilehash: dcadfcb4c2f8e6bc371b0a70b917c8c1e218fba9
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679518"
---
# <a name="source-control-integration-in-azure-automation---legacy"></a>Integração de controlo de fontes na Automação Azure - Legado

> [!NOTE]
> Há uma nova experiência para o controlo de fontes. Para saber mais sobre a nova experiência, consulte [Source Control (Preview)](source-control-integration.md).

A integração do Controlo de Fontes permite-lhe associar os livros de execução na sua conta Automation a um repositório de controlo de fontes GitHub. O controlo de fontes permite-lhe colaborar facilmente com a sua equipa, rastrear alterações e reverter para versões anteriores dos seus livros de execução. Por exemplo, o controlo de fontes permite sincronizar diferentes ramos no controlo de origem das suas contas de desenvolvimento, teste ou produção Automação, facilitando a promoção de código que foi testado no seu ambiente de desenvolvimento à sua conta de automação de produção.

O Controlo de Origem permite-lhe premir o código da Azure Automation para controlar a fonte ou retirar os seus livros de controlo de origem para a Automação Azure. Este artigo descreve como configurar o controlo de origem no seu ambiente de Automação Azure. Começamos por configurar a Automatização Azure para aceder ao seu repositório GitHub e caminhar por diferentes operações que podem ser feitas usando a integração do controlo de fontes. 

> [!NOTE]
> O Controlo de Origem suporta puxar e empurrar os livros de [execução powerShell Workflow,](automation-runbook-types.md#powershell-workflow-runbooks) bem como os livros de [execução PowerShell](automation-runbook-types.md#powershell-runbooks). [Os livros gráficos](automation-runbook-types.md#graphical-runbooks) ainda não são suportados.

## <a name="configuring-source-control"></a>Configurar o controlo de fonte

Existem dois passos simples necessários para configurar o controlo de fonte para a sua conta Automation, e apenas um se já tiver uma conta GitHub. 

### <a name="create-a-github-repository"></a>Criar um repositório GitHub

Se já tem uma conta GitHub e um repositório que pretende ligar à Automação Azure, em seguida, assine na sua conta existente e comece a partir do passo 2 abaixo. Caso contrário, navegue para o [GitHub,](https://github.com/)inscreva-se para uma nova conta e [crie um novo repositório](https://help.github.com/articles/create-a-repo/).

### <a name="set-up-source-control"></a>Configurar o controlo de fontes

1. A partir da página da Conta de Automação no portal Azure, em **Definições de Conta,** clique no **Controlo de Origem.**

2. A página Source Control abre, onde pode configurar os detalhes da sua conta GitHub. Segue-se a lista de parâmetros a configurar:  

   | **Parâmetro** | **Descrição** |
   |:--- |:--- |
   | Escolha Fonte |Selecione a fonte. Atualmente, apenas o **GitHub** é suportado. |
   | Autorização |Clique no botão **Autorizar** para conceder acesso à Automação Azure ao seu repositório GitHub. Se já está registado na sua conta GitHub numa janela diferente, então as credenciais dessa conta são usadas. Uma vez que a autorização é bem sucedida, a página mostra o seu nome de utilizador GitHub em **Propriedade de Autorização**. |
   | Escolha o repositório |Selecione um repositório GitHub da lista de repositórios disponíveis. |
   | Escolha ramo |Selecione um ramo da lista de balcões disponíveis. Só o ramo **principal** é mostrado se não criou nenhum ramo. |
   | Caminho da pasta runbook |O percurso da pasta do livro de execução especifica o caminho no repositório GitHub a partir do qual pretende empurrar ou puxar o seu código. Deve ser introduzido no formato /nome da **pasta/subpasta**. Apenas os livros de execução no caminho da pasta do livro de execução serão sincronizados na sua conta Automation. Os livros de execução nas subpastas do percurso das pastas do livro de execução **NÃO** serão sincronizados. Utilize **/** para sincronizar todos os livros sob o repositório. |
3. Por exemplo, se tiver um repositório chamado **PowerShellScripts** que contenha uma pasta chamada **RootFolder**, que contém uma pasta chamada **SubPasta**. Pode utilizar as seguintes cordas para sincronizar cada nível de pasta:

   1. Para sincronizar os livros de reprodução do **/** **repositório,** o caminho das pastas do livro de corridas é .
   2. Para sincronizar os livros de execução da **RootFolder,** o caminho da pasta do livro de execução é **/RootFolder**.
   3. Para sincronizar os livros de execução da **SubPasta,** o caminho da pasta do livro de execução é **/RootFolder/SubFolder**.
4. Depois de configurar os parâmetros, são apresentados na página de Controlo de Origem Configurar.  

    ![A página de controlo fonte mostrando as definições](media/source-control-integration-legacy/automation-SourceControlConfigure.png)
5. Uma vez clicado em **OK,** a integração do controlo de fontes está agora configurada para a sua conta Deautomação e deve ser atualizada com as informações do GitHub. Agora pode clicar nesta parte para ver todo o histórico de trabalho sincronizado de controlo de fontes.  

    ![Valores para a configuração de controlo de fonte configurada atual](media/source-control-integration-legacy/automation-RepoValues.png)
6. Depois de configurar o controlo de fonte, dois [ativos variáveis](automation-variables.md) são criados na sua conta De automação. Adicionalmente, um pedido autorizado é adicionado à sua conta GitHub.

   * A variável **Microsoft.Azure.Automation.SourceControl.Connection** contém os valores da cadeia de ligação, como mostrado abaixo.  

     | **Parâmetro** | **Valor** |
     |:--- |:--- |
     | `Name`  |Microsoft.Azure.Automation.SourceControl.Connection |
     | `Type`  |String |
     | `Value` |{"Branch":\<*O seu nome de filial* \<>"RunbookFolderPath": Rota\<da pasta de*execução*>"ProviderType":*tem um valor 1 para o GitHub*>"Repositório":\<*Nome do seu repositório*>"Nome de utilizador":\<O nome de utilizador do*GitHub*>} |

   * A variável **Microsoft.Azure.Automation.SourceControl.OAuthToken**contém o valor encriptado seguro do seu OAuthToken.  

     |**Parâmetro**            |**Valor** |
     |:---|:---|
     | `Name`  | `Microsoft.Azure.Automation.SourceControl.OAuthToken` |
     | `Type`  | `Unknown(Encrypted)` |
     | `Value` | <`Encrypted OAuthToken`> |  

     ![Uma janela mostrando variáveis de controlo de origem](media/source-control-integration-legacy/automation-Variables.png)  

   * O Controlo de Fontes de **Automação** é adicionado como uma aplicação autorizada à sua conta GitHub. Para ver a aplicação, a partir da sua página inicial do GitHub, navegue para **perfilar** > **Aplicações**de**Definições** > . Esta aplicação permite à Azure Automation sincronizar o seu repositório GitHub a uma conta De automação.  

     ![Definições de aplicação no GitHub](media/source-control-integration-legacy/automation-GitApplication.png)

## <a name="using-source-control-in-automation"></a>Utilização do controlo de fontes na Automação

O check-in do livro de reparações permite-lhe empurrar as alterações que etrôs para um livro de execução em Azure Automation para o seu repositório de controlo de fonte. Abaixo estão os passos para verificar em um livro de execução:

1. A partir da sua conta De automação, [crie um novo livro textual](automation-first-runbook-textual.md), ou [edite um livro de execução textual existente.](automation-edit-textual-runbook.md) Este livro de execução pode ser um PowerShell Workflow ou um livro de execução de scriptpowerShell.  
2. Depois de editar o seu livro de execução, guarde-o e clique no **Check-in** na página de edição.  

    ![Uma janela que mostra o checkin para o botão GitHub](media/source-control-integration-legacy/automation-CheckinButton.png)

     > [!NOTE] 
     > O check-in da Azure Automation substitui o código que existe atualmente no seu controlo de origem. A instrução equivalente da linha de comando Git para fazer o check-in é **git add + git commit + git push**  

3. Quando clicar no **check-in,** é solicitado com uma mensagem de confirmação, clique **em Sim** para continuar.  

    ![Uma caixa de diálogo confirmando o checkin para o controlo de fonte](media/source-control-integration-legacy/automation-CheckinMessage.png)
4. O check-in inicia o livro de controlo de fontes: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Este livro de execução liga-se ao GitHub e empurra as alterações da Automação Azure para o seu repositório. Para ver o histórico de trabalho verificado, volte ao separador **Source Control Integration** e clique para abrir a página de Sincronização do Repositório. Esta página mostra todos os seus trabalhos de controlo de fontes. Selecione o trabalho que pretende ver e clique para ver os detalhes.  

    ![Uma janela mostrando os resultados de um trabalho de sincronização](media/source-control-integration-legacy/automation-CheckinRunbook.png)

   > [!NOTE]
   > Os livros de controlo de fontes são livros de execução especiais da Automation que não pode ver ou editar. Embora não apareçam na sua lista de livros, vê-se trabalhos sincronizados na sua lista de empregos.

5. O nome do livro de execução modificado é enviado como parâmetro de entrada para o livro de execução verificado. Pode [ver os detalhes do trabalho](automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) expandindo o livro de execução na página de Sincronização repositória.  

    ![Uma janela mostrando a entrada para um trabalho de sincronização](media/source-control-integration-legacy/automation-CheckinInput.png)
6. Refresque o seu repositório GitHub assim que o trabalho terminar para ver as alterações.  Deve haver um compromisso no seu repositório com uma mensagem de compromisso: **Nome de *livro* atualizado em Automação Azure.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Livros de sincronização do controlo de fonte saca do Azure Automation

O botão de sincronização na página de Sincronização do Repositório permite-lhe puxar todos os livros de execução no percurso da pasta do livro de execução do seu repositório para a sua conta Deautomação. O mesmo repositório pode ser sincronizado com mais de uma conta de Automação. Abaixo estão os passos para sincronizar um livro de corridas:

1. A partir da conta de Automação onde configura o controlo de origem, abra a página de Sincronização/Sincronização de Controlo de Origem e clique em **Sync**.  É solicitado com uma mensagem de confirmação, clique **em Sim** para continuar.  

    ![Botão sync com mensagem confirmando que todos os livros de execução serão sincronizados](media/source-control-integration-legacy/automation-SyncButtonwithMessage.png)

2. O Sync inicia o livro de execução **Sync-MicrosoftAzureAutomationAccountFromGitHubV1,** que se liga ao GitHub e retira as alterações do seu repositório à Automação Azure. Você deve ver um novo trabalho na página de Sincronização Repositório para esta ação. Para ver detalhes sobre o trabalho de sincronização, clique para abrir a página de detalhes de trabalho.  

    ![Uma janela que mostra os resultados da sincronização de um trabalho de sincronização num repositório GitHub](media/source-control-integration-legacy/automation-SyncRunbook.png)

    > [!NOTE]
    > Uma sincronização do controlo de fonte supõe o projeto de versão dos livros de execução que existem atualmente na sua conta De Automação para **TODOS os** livros de execução que estão atualmente no controlo de origem. A instrução equivalente da linha de comando Git para sincronizar é **git pull**

![Uma janela mostrando todos os registos de um trabalho de sincronização de controlo de fonte suspensa](media/source-control-integration-legacy/automation-AllLogs.png)

## <a name="disconnecting-source-control"></a>Desligar o controlo de fontes

Para desligar da sua conta GitHub, abra a página de Sincronização do Repositório e clique em **Desligar**. Uma vez desligado o controlo de fonte, os livros de execução que foram sincronizados mais cedo ainda permanecem na sua conta Desmitar, mas a página de Sincronização do Repositório não será ativada.  

  ![Uma janela que mostra o botão Desligar para desligar o controlo de origem](media/source-control-integration-legacy/automation-Disconnect.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a integração do controlo de fontes, consulte os seguintes recursos:  

* [Automação Azure: Integração de Controlo de Fontes na Automação Azure](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [Automação Azure: Integração do Controlo de Fontes do Livro de Execução utilizando O Azure DevOps](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  
