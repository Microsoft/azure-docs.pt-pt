---
title: Integração de controlo de código fonte da Automatização do Azure
description: Este artigo descreve a integração do controlo de fontes com o GitHub na Automação Azure.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: b0eed8fe9d548ee54698d187e192960bb3b44e44
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368813"
---
# <a name="source-control-integration-in-azure-automation"></a>Integração de controlo de código fonte da Automatização do Azure

 A integração do controlo de fontes na Automatização Azure suporta a sincronização de uma direção única do seu repositório de controlo de fonte. O controlo de fonte supõe-lhe manter os seus livros de execução na sua conta Automation atualizados com scripts no seu repositório de controlo de fonte GitHub ou Azure Repos. Esta funcionalidade facilita a promoção de código que foi testado no seu ambiente de desenvolvimento para a sua conta de Automação de Produção.
 
 Utilizando a integração do controlo de fontes, pode facilmente colaborar com a sua equipa, rastrear alterações e reverter para versões anteriores dos seus livros de execução. Por exemplo, o controlo de fontes permite sincronizar diferentes ramos no controlo de origem com as suas contas de desenvolvimento, teste e produção Automation. 

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](automation-update-azure-modules.md).

## <a name="source-control-types"></a>Tipos de controlo de fontes

A Azure Automation suporta três tipos de controlo de fonte:

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>Pré-requisitos

* Um repositório de controlo de fonte (GitHub ou Azure Repos)
* Uma [conta Run As](manage-runas-account.md)
* Os [mais recentes módulos Azure](automation-update-azure-modules.md) na sua conta Automation, incluindo o módulo `Az.Accounts` (módulo Az equivalente a `AzureRM.Profile`)

> [!NOTE]
> Os trabalhos de sincronização do controlo de fontes são executados sob a conta de Automação do utilizador e são faturados ao mesmo ritmo que outros trabalhos de Automação.

## <a name="configuring-source-control"></a>Configurar o controlo de fonte

Esta secção diz como configurar o controlo de origem para a sua conta Desmótica. Pode utilizar o portal Azure ou o PowerShell.

### <a name="configure-source-control----azure-portal"></a>Configure controlo de fontes -- Portal Azure

Utilize este procedimento para configurar o controlo de origem utilizando o portal Azure.

1. Dentro da sua conta De automação, selecione **Controlo de Origem** e clique + **Adicionar**.

    ![Selecione o controlo de fonte](./media/source-control-integration/select-source-control.png)

2. Escolha **o tipo de Controlo de Origem**e, em seguida, clique em **Autenticar**. 

3. Uma janela do navegador abre-se e pede-lhe para iniciar sessão. Siga as instruções para a autenticação completa.

4. Na página Resumo do Controlo de Origem, utilize os campos para preencher as propriedades de controlo de origem definidas abaixo. Clique em **Guardar** quando terminar. 

    |Propriedade  |Descrição  |
    |---------|---------|
    |Nome de controlo de fonte     | Um nome amigável para o controlo de fontes. Este nome deve conter apenas letras e números.        |
    |Tipo de controlo de origem     | Tipo de mecanismo de controlo de fonte. As opções disponíveis são:</br> * GitHub</br>* Azure Repos (Git)</br> * Azure Repos (TFVC)        |
    |Repositório     | Nome do repositório ou projeto. Os primeiros 200 repositórios são recuperados. Para procurar um repositório, digite o nome no campo e clique **em Procurar no GitHub**.|
    |Ramo     | Ramo a partir do qual retirar os ficheiros de origem. O alvo da sucursal não está disponível para o tipo de controlo de fonte TFVC.          |
    |Caminho da pasta     | Pasta que contém os livros de execução para sincronizar, por exemplo, **/Livros de execução**. Apenas os livros de execução na pasta especificada são sincronizados. A recursição não é apoiada.        |
    |Auto Sync<sup>1</sup>     | Regulação que liga ou desliga a sincronização automática quando um compromisso é feito no repositório de controlo de fonte.        |
    |Publicar Livro de Execução     | Definição de On se os livros de execução forem automaticamente publicados após a sincronização do controlo de origem, e desligado de outra forma.           |
    |Descrição     | Texto especificando detalhes adicionais sobre o controlo de origem.        |

    <sup>1</sup> Para ativar o Auto Sync ao configurar a integração do controlo de fonte com o Azure Repos, deve ser administrador de projeto.

   ![Resumo do controlo de fontes](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> O login do seu repositório de controlo de fonte pode ser diferente do seu login para o portal Azure. Certifique-se de que está a iniciar sessão com a conta correta para o seu repositório de controlo de origem ao configurar o controlo de origem. Em caso de dúvida, abra um novo separador no seu navegador, faça login a partir de **visualstudio.com** ou **github.com**, e tente ligar-se novamente ao controlo de origem.

### <a name="configure-source-control----powershell"></a>Configure o controlo de fonte -- PowerShell

Também pode utilizar o PowerShell para configurar o controlo de fonte sinuoso na Automação Azure. Para utilizar os cmdlets PowerShell para esta operação, precisa de um sinal de acesso pessoal (PAT). Utilize o cmdlet [New-AzAutomationSourceControl](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
) para criar a ligação de controlo de origem. Este cmdlet requer uma corda segura para o PAT. Para aprender a criar uma cadeia segura, consulte [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

As seguintes subsecções ilustram a criação da PowerShell da ligação de controlo de fontes para gitHub, Azure Repos (Git) e Azure Repos (TFVC).

#### <a name="create-source-control-connection-for-github"></a>Criar ligação de controlo de fonte sacar o GitHub

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Criar ligação de controlo de fontes para O Pinos (Git)

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Criar ligação de controlo de fontes para o Azure Repos (TFVC)

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Permissões de fichade acesso pessoal (PAT)

O controlo de fonte requer algumas permissões mínimas para os PATs. As seguintes subsecções contêm as permissões mínimas necessárias para o GitHub e o Azure Repos.

##### <a name="minimum-pat-permissions-for-github"></a>Permissões mínimas de PAT para gitHub

A tabela que se segue define as permissões PAT mínimas necessárias para o GitHub. Para obter mais informações sobre a criação de um PAT no GitHub, consulte Criar um sinal de [acesso pessoal para a linha de comando](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Âmbito  |Descrição  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | Estado de compromisso de acesso         |
|`repo_deployment`      | Estado de implantação de acesso         |
|`public_repo`     | Aceder aos repositórios públicos         |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | Escreva ganchos de repositório         |
|`read:repo_hook`|Ler ganchos de repositório|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Permissões mínimas de PAT para Azure Repos

A lista que se segue define as permissões mínimas de PAT necessárias para o Azure Repos. Para obter mais informações sobre a criação de um PAT em Azure Repos, consulte [O acesso authenticado com fichas de acesso pessoal.](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)

| Âmbito  |  Tipo de acesso  |
|---------| ----------|
| Código      | Leitura  |
| Projeto e equipa | Leitura |
| Identidade | Leitura     |
| Perfil de utilizador | Leitura     |
| Artigos de trabalho | Leitura    |
| Ligações de serviço | Ler, consultar, gerir<sup>1</sup>    |

<sup>1</sup> A permissão de ligações de serviço só é necessária se tiver ativado a sincronização automática.

## <a name="synchronizing"></a>Sincronização

Siga estes passos para sincronizar com o controlo de origem. 

1. Selecione a fonte da tabela na página de controlo Fonte. 

2. Clique em **Iniciar Sincronização** para iniciar o processo de sincronização. 

3. Veja o estado do trabalho de sincronização atual ou dos anteriores clicando no separador **de empregos Sync.** 

4. No menu de dropdown do **Controlo de Origem,** selecione um mecanismo de controlo de origem.

    ![Estado de sincronização](./media/source-control-integration/sync-status.png)

5. Clicar num trabalho permite-lhe visualizar a saída do trabalho. O exemplo seguinte é a saída de um trabalho de sincronização de controlo de fonte.

    ```output
    ============================================================================

    Azure Automation Source Control.
    Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

    Setting AzureRmEnvironment.

    Getting AzureRunAsConnection.

    Logging in to Azure...

    Source control information for syncing:

    [Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

    Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

    Connecting to VSTS...

    Source Control Sync Summary:

    2 files synced:
     - ExampleRunbook1.ps1
     - ExampleRunbook2.ps1

     =========================================================================

    ```

6. O registo adicional está disponível selecionando **Todos os Registos** na página de Resumo do Sumário de Sincronização de Controlo de Origem. Estas entradas de registo adicionais podem ajudá-lo a resolver problemas que podem surgir ao utilizar o controlo de origem.

## <a name="disconnecting-source-control"></a>Desligar o controlo de fontes

Para desligar de um repositório de controlo de fonte:

1. Controlo **de Código** aberto em **Definições de Conta** na sua conta Desmótica.

2. Selecione o mecanismo de controlo de origem para remover. 

3. Na página Resumo do Controlo de Fontes, clique em **Eliminar**.

## <a name="handling-encoding-issues"></a>Lidar com questões de codificação

Se várias pessoas estiverem a editar livros de execução no seu repositório de controlo de fontes utilizando diferentes editores, podem ocorrer questões de codificação. Para saber mais sobre esta situação, consulte [as causas comuns das questões de codificação.](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="updating-the-pat"></a>Atualizar o PAT

Atualmente, não existe forma de utilizar o portal Azure para atualizar o PAT no controlo de fontes. Depois de o PAT ter expirado ou sido revogado, pode atualizar o controlo de fonte com um novo sinal de acesso de uma destas formas:

* Utilize a [API REST](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Utilize o cmdlet [Update-AzAutomationSourceControl.](/powershell/module/az.automation/update-azautomationsourcecontrol)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os tipos de livro de corridas e as suas vantagens e limitações, consulte os tipos de livro de [execução da Automação Azure](automation-runbook-types.md).
