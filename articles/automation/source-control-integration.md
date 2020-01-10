---
title: Integração de controle do código-fonte na automação do Azure
description: Este artigo descreve a integração do controle do código-fonte com o GitHub na automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 837ebd71886e9435a44080b06c079623c3936c69
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75417057"
---
# <a name="source-control-integration-in-azure-automation"></a>Integração de controlo de código fonte da Automatização do Azure

O controle do código-fonte permite que você mantenha seus runbooks em sua conta de automação atualizados com seus scripts em seu GitHub ou Azure Repos repositório de controle do código-fonte. O controle do código-fonte permite que você colabore facilmente com sua equipe, controle alterações e reverta para versões anteriores de seus runbooks. Por exemplo, o controle do código-fonte permite que você sincronize diferentes branches no controle do código-fonte para suas contas de desenvolvimento, teste ou automação de produção. Isso facilita a promoção de código que foi testado em seu ambiente de desenvolvimento para sua conta de automação de produção. A integração de controle do código-fonte com automação dá suporte à sincronização de direção única no repositório do controle do código-fonte.

A automação do Azure dá suporte a três tipos de controle do código-fonte:

* GitHub
* Azure Repos (git)
* Azure Repos (TFVC)

## <a name="pre-requisites"></a>Pré-requisitos

* Um repositório de controle do código-fonte (GitHub ou Azure Repos)
* Uma [conta Executar como](manage-runas-account.md)
* Verifique se você tem os [módulos do Azure mais recentes](automation-update-azure-modules.md) em sua conta de automação, incluindo o módulo **AzureRM. Profile** . 

> [!NOTE]
> Os trabalhos de sincronização de controle do código-fonte são executados na conta de automação dos usuários e são cobrados com a mesma taxa de outros trabalhos de automação.

## <a name="configure-source-control---azure-portal"></a>Configurar o controle do código-fonte-portal do Azure

Em sua conta de automação, selecione **controle do código-fonte** e clique em **+ Adicionar**

![Selecionar controle do código-fonte](./media/source-control-integration/select-source-control.png)

Escolha **tipo de controle do código-fonte**, clique em **autenticar**. Uma janela do navegador é aberta e solicita que você entre, siga os prompts para concluir a autenticação.

Na página **Resumo do controle do código-fonte** , preencha as informações e clique em **salvar**. A tabela a seguir mostra uma descrição dos campos disponíveis.

|Propriedade  |Descrição  |
|---------|---------|
|Nome do controle do código-fonte     | Um nome amigável para o controle do código-fonte. *Esse nome deve conter apenas letras e números.*        |
|Tipo de controle do código-fonte     | O tipo de fonte de controle do código-fonte. As opções disponíveis são:</br> GitHub</br>Azure Repos (git)</br> Azure Repos (TFVC)        |
|Repositório     | O nome do repositório ou do projeto. Os primeiros 200 repositórios são retornados. Para pesquisar um repositório, digite o nome no campo e clique em **Pesquisar no GitHub**.|
|Branch     | A ramificação da qual extrair os arquivos de origem. O direcionamento de Branch não está disponível para o tipo de controle do código-fonte TFVC.          |
|Caminho da pasta     | A pasta que contém os runbooks a serem sincronizados. Exemplo:/Runbooks </br>*Somente runbooks na pasta especificada são sincronizados. Não há suporte para recursão.*        |
|Sincronização automática<sup>1</sup>     | Ativa ou desativa a sincronização automática quando uma confirmação é feita no repositório de controle do código-fonte         |
|Publicar runbook     | Se definido como **on**, após os runbooks serem sincronizados do controle do código-fonte, eles serão publicados automaticamente.         |
|Descrição     | Um campo de texto para fornecer detalhes adicionais        |

<sup>1</sup> para habilitar a sincronização automática ao configurar a integração do controle do código-fonte com o Azure Repos, você deve ser um administrador do projeto.

![Resumo do controle do código-fonte](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Seu logon para o repositório do controle do código-fonte pode ser diferente do seu logon para o portal do Azure. Verifique se você está conectado com a conta correta para seu repositório de controle do código-fonte ao configurar o controle do código-fonte. Se houver dúvida, abra uma nova guia no seu navegador e faça logoff de visualstudio.com ou github.com e tente conectar o controle do código-fonte novamente.

## <a name="configure-source-control---powershell"></a>Configurar controle do código-fonte-PowerShell

Você também pode usar o PowerShell para configurar o controle do código-fonte na automação do Azure. Para configurar o controle do código-fonte com os cmdlets do PowerShell, é necessário um PAT (token de acesso pessoal). Use o [New-AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl) para criar a conexão de controle do código-fonte. O cmdlet requer uma cadeia de caracteres segura do token de acesso pessoal, para saber como criar uma cadeia de caracteres segura, consulte [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

### <a name="azure-repos-git"></a>Azure Repos (git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Azure Repos (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>Permissões de token de acesso pessoal

O controle do código-fonte requer algumas permissões mínimas para tokens de acesso pessoal. As tabelas a seguir contêm as permissões mínimas necessárias para o GitHub e o Azure Repos.

#### <a name="github"></a>GitHub

Para obter mais informações sobre como criar um token de acesso pessoal no GitHub, visite [criando um token de acesso pessoal para a linha de comando](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Âmbito  |Descrição  |
|---------|---------|
|**repo** (repositório)     |         |
|repositório: status     | Status de confirmação de acesso         |
|repo_deployment      | Status de implantação do Access         |
|public_repo     | Acessar repositórios públicos         |
|**admin:repo_hook**     |         |
|write:repo_hook     | Gravar ganchos de repositório         |
|read:repo_hook|Ler ganchos de repositório|

#### <a name="azure-repos"></a>Repositórios do Azure

Para obter mais informações sobre como criar um token de acesso pessoal no Azure Repos, visite [autenticar o acesso com tokens de acesso pessoal](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

|Âmbito  |
|---------|
|Código (leitura)     |
|Projeto e equipe (leitura)|
|Identidade (leitura)      |
|Perfil do usuário (leitura)     |
|Itens de trabalho (leitura)    |
|Conexões de serviço (ler, consultar e gerenciar)<sup>1</sup>    |

<sup>1</sup> a permissão de conexões de serviço só será necessária se você tiver habilitado o AutoSync.

## <a name="syncing"></a>Sincronizar

Selecione a origem da tabela na página **controle do código-fonte** . Clique em **Iniciar sincronização** para iniciar o processo de sincronização.

Você pode exibir o status do trabalho de sincronização atual ou os anteriores clicando na guia **trabalhos de sincronização** . Na lista suspensa **controle do código-fonte** , selecione um controle do código-fonte.

![Status da sincronização](./media/source-control-integration/sync-status.png)

Clicar em um trabalho permite que você exiba a saída do trabalho. O exemplo a seguir é a saída de um trabalho de sincronização de controle do código-fonte.

```output
========================================================================================================

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



========================================================================================================
```

O registro em log adicional está disponível selecionando **todos os logs** na página Resumo do trabalho de sincronização de **controle do código-fonte** . Essas entradas de log adicionais podem ajudá-lo a solucionar problemas que podem surgir ao usar o controle do código-fonte.

## <a name="disconnecting-source-control"></a>Desconectando controle do código-fonte

Para se desconectar de um repositório de controle do código-fonte, **controle do código-fonte** aberto em **configurações de conta** em sua conta de automação.

Selecione o controle do código-fonte que você deseja remover. Na página **Resumo do controle do código-fonte** , clique em **excluir**.

## <a name="encoding"></a>Encoding

Se várias pessoas estiverem editando runbooks em seu repositório de controle do código-fonte com editores diferentes, haverá a chance de executar problemas de codificação. Essa situação pode levar a caracteres incorretos em seu runbook. Para saber mais sobre isso, confira [causas comuns de problemas de codificação](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="updating-the-access-token"></a>Atualizando o token de acesso

Atualmente, não há nenhuma maneira de atualizar o token de acesso no controle do código-fonte no Portal. Depois que o token de acesso pessoal expirar ou for revogado, você poderá atualizar o controle do código-fonte com um novo token de acesso das seguintes maneiras:

* Por meio da [API REST](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Usando o cmdlet [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) .

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os tipos de runbook, as vantagens e limitações, consulte o artigo [Tipos de runbook da Automatização do Azure](automation-runbook-types.md)
