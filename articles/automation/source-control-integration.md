---
title: Utilize a integração de controlo de fonte na Azure Automation
description: Este artigo diz como sincronizar o controlo de fonte da Azure Automation com outros repositórios.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: eea4de106fe566b55ae30330d4c9d101f7126bbf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86229623"
---
# <a name="use-source-control-integration"></a>Utilizar a integração do controlo de código fonte

 A integração de controlo de fonte na Azure Automation suporta a sincronização de uma direção única a partir do seu repositório de controlo de origem. O controlo de origem permite-lhe manter os seus livros na sua conta Automation até à data com scripts no seu repositório de controlo de fontes GitHub ou Azure Repos. Esta funcionalidade facilita a promoção de códigos que foram testados no seu ambiente de desenvolvimento na sua conta de Automação de produção.
 
 A integração do controlo de origem permite-lhe colaborar facilmente com a sua equipa, rastrear alterações e voltar para versões anteriores dos seus runbooks. Por exemplo, o controlo de origem permite sincronizar diferentes ramos no controlo de fontes com as suas contas de desenvolvimento, teste e produção De automação. 

## <a name="source-control-types"></a>Tipos de controlo de fontes

A Azure Automation suporta três tipos de controlo de origem:

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>Pré-requisitos

* Um repositório de controlo de fontes (GitHub ou Azure Repos)
* Uma [conta Run As](manage-runas-account.md)
* Os [mais recentes módulos Azure](automation-update-azure-modules.md) na sua conta Automation, incluindo o `Az.Accounts` módulo (módulo Az equivalente `AzureRM.Profile` a)

> [!NOTE]
> Os trabalhos de sincronização de controlo de fonte são executados sob a conta de Automação do utilizador e são faturados ao mesmo ritmo que outros trabalhos de Automação.

## <a name="configure-source-control"></a>Configure o controlo da fonte

Esta secção diz como configurar o controlo de origem para a sua conta Demôm automação. Pode utilizar o portal Azure ou o PowerShell.

### <a name="configure-source-control-in-azure-portal"></a>Configurar controlo de fontes no portal Azure

Utilize este procedimento para configurar o controlo de origem utilizando o portal Azure.

1. Na sua conta Dem automação, selecione **Controlo de Origem** e clique em **Adicionar**.

    ![Selecione controlo de origem](./media/source-control-integration/select-source-control.png)

2. Escolha o **tipo de Controlo de Origem**e, em seguida, clique em **Autenticar**. 

3. Uma janela do navegador abre-se e pede-lhe para iniciar sinte. Siga as instruções para completar a autenticação.

4. Na página Resumo do Controlo de Origem, utilize os campos para preencher as propriedades de controlo de origem definidas abaixo. Clique **em Guardar** quando terminar. 

    |Propriedade  |Descrição  |
    |---------|---------|
    |Nome de controlo de origem     | Um nome amigável para o controlo de origem. Este nome deve conter apenas letras e números.        |
    |Tipo de controlo de origem     | Tipo de mecanismo de controlo de fonte. As opções disponíveis são:</br> * GitHub</br>* Azure Repos (Git)</br> * Azure Repos (TFVC)        |
    |Repositório     | Nome do repositório ou projeto. Os primeiros 200 repositórios são recuperados. Para procurar um repositório, digite o nome no campo e clique **em Pesquisar no GitHub.**|
    |Ramo     | Filial a partir do qual puxar os ficheiros de origem. O alvo da sucursal não está disponível para o tipo de controlo de origem TFVC.          |
    |Folder path     | Pasta que contém os livros para sincronizar, por exemplo, **/Runbooks**. Apenas os livros de execução na pasta especificada são sincronizados. A recursão não é apoiada.        |
    |Auto Sincronização<sup>1</sup>     | Regulação que liga ou desliga a sincronização automática quando um compromisso é feito no repositório de controlo de origem.        |
    |Publicar Runbook     | Definição de On se os runbooks forem automaticamente publicados após a sincronização do controlo de origem, e desligado de outra forma.           |
    |Descrição     | Texto especificando detalhes adicionais sobre o controlo de origem.        |

    <sup>1</sup> Para ativar o Auto Sync ao configurar a integração do controlo de fonte com o Azure Repos, tem de ser administrador de projeto.

   ![Resumo do controlo de origem](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> O login do seu repositório de controlo de origem pode ser diferente do seu login para o portal Azure. Certifique-se de que está a iniciar sessão com a conta correta para o seu repositório de controlo de origem ao configurar o controlo de origem. Em caso de dúvida, abra um novo separador no seu browser, faça login a partir de **dev.azure.com**, **visualstudio.com**ou **github.com**, e tente reconectar-se ao controlo de origem.

### <a name="configure-source-control-in-powershell"></a>Configure o controlo de fontes na PowerShell

Também pode utilizar o PowerShell para configurar o controlo de origem na Azure Automation. Para utilizar os cmdlets PowerShell para esta operação, necessita de um token de acesso pessoal (PAT). Utilize o cmdlet [New-AzAutomationSourceControl](/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0) para criar a ligação de controlo de fonte. Este cmdlet requer uma corda segura para o PAT. Para aprender a criar uma cadeia segura, consulte [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

As subsecções seguintes ilustram a criação da PowerShell da ligação de controlo de origem para GitHub, Azure Repos (Git) e Azure Repos (TFVC). 

#### <a name="create-source-control-connection-for-github"></a>Criar ligação de controlo de fontes para GitHub

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Criar ligação de controlo de origem para Azure Repos (Git)

> [!NOTE]
> Azure Repos (Git) usa um URL que acede **dev.azure.com** em vez de **visualstudio.com**, usado em formatos anteriores. O formato URL mais antigo `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>` é precotado, mas ainda suportado. O novo formato é preferido.


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Criar ligação de controlo de fontes para Azure Repos (TFVC)

> [!NOTE]
> Azure Repos (TFVC) utiliza um URL que acede **dev.azure.com** em vez de **visualstudio.com**, usado em formatos anteriores. O formato URL mais antigo `https://<accountname>.visualstudio.com/<projectname>/_versionControl` é precotado, mas ainda suportado. O novo formato é preferido.

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Permissões de acesso pessoal (PAT)

O controlo de origem requer algumas permissões mínimas para pats. As seguintes subsecções contêm as permissões mínimas necessárias para gitHub e Azure Repos.

##### <a name="minimum-pat-permissions-for-github"></a>Permissões mínimas de PAT para GitHub

A tabela a seguir define as permissões mínimas de PAT necessárias para o GitHub. Para obter mais informações sobre a criação de um PAT no GitHub, consulte [criar um símbolo de acesso pessoal para a linha de comando.](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)

|Âmbito  |Descrição  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | Acesso estado de compromisso         |
|`repo_deployment`      | Estado de implantação de acesso         |
|`public_repo`     | Acesso a repositórios públicos         |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | Escreva ganchos de repositório         |
|`read:repo_hook`|Leia ganchos de repositório|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Permissões mínimas de PAT para Azure Repos

A lista a seguir define as permissões mínimas de PAT necessárias para a Azure Repos. Para obter mais informações sobre a criação de um PAT em Azure Repos, consulte [o Acesso Authenticate com fichas de acesso pessoal.](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=preview-page)

| Âmbito  |  Tipo de Acesso  |
|---------| ----------|
| `Code`      | Leitura  |
| `Project and team` | Leitura |
| `Identity` | Leitura     |
| `User profile` | Leitura     |
| `Work items` | Leitura    |
| `Service connections` | Ler, consultar, gerir<sup>1</sup>    |

<sup>1</sup> A `Service connections` permissão só é necessária se tiver ativado o autosínco.

## <a name="synchronize-with-source-control"></a>Sincronizar com controlo de origem

Siga estes passos para sincronizar com o controlo de origem. 

1. Selecione a fonte da tabela na página de controlo 'Fonte'. 

2. Clique **em Iniciar Sincronização** para iniciar o processo de sincronização. 

3. Ver o estado do trabalho de sincronização atual ou dos anteriores clicando no **separador sync jobs.** 

4. No menu de entrega **do Controlo de Origem,** selecione um mecanismo de controlo de origem.

    ![Estado de sincronização](./media/source-control-integration/sync-status.png)

5. Clicar num trabalho permite-lhe ver a saída do trabalho. Segue-se o exemplo da saída de um trabalho de sincronização de controlo de fonte.

    ```output
    ===================================================================

    Azure Automation Source Control.
    Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

    Setting AzEnvironment.

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

    ==================================================================

    ```

6. A sessão de registos adicionais está disponível selecionando **todos os Registos** na página 'Resumo do Trabalho do Sync Sync'. Estas entradas adicionais de registo podem ajudá-lo a resolver problemas que podem surgir ao utilizar o controlo de origem.

## <a name="disconnect-source-control"></a>Desligar o controlo da fonte

Para desligar de um repositório de controlo de origem:

1. Controlo Open **Source** em **Definições de Conta** na sua conta Demôm automação.

2. Selecione o mecanismo de controlo de origem para remover. 

3. Na página 'Resumo do Controlo de Origem', clique em **Eliminar**.

## <a name="handle-encoding-issues"></a>Lidar com problemas de codificação

Se várias pessoas estiverem a editar livros no seu repositório de controlo de origem usando diferentes editores, podem ocorrer problemas de codificação. Para saber mais sobre esta situação, consulte [as causas comuns das questões de codificação.](/powershell/scripting/components/vscode/understanding-file-encoding?view=powershell-7#common-causes-of-encoding-issues)

## <a name="update-the-pat"></a>Atualizar o PAT

Atualmente, não é possível utilizar o portal Azure para atualizar o PAT no controlo de origem. Quando o seu PAT expirar ou ser revogado, pode atualizar o controlo de origem com um novo token de acesso de uma destas formas:

* Utilize a [API REST](/rest/api/automation/sourcecontrol/update).
* Utilize o [cmdlet Update-AzAutomationSourceControl.](/powershell/module/az.automation/update-azautomationsourcecontrol)

## <a name="next-steps"></a>Passos seguintes

* Para integrar o controlo de origem na Azure Automation, consulte [a Azure Automation: Source Control Integration in Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/).  
* Para integrar o controlo de origem do runbook com o Visual Studio Online, consulte [a Azure Automation: Integrando o Runbook Source Control utilizando o Visual Studio Online](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/).
