---
title: Integração de controlo na automatização do Azure de origem
description: Este artigo descreve a integração do controlo de origem com o GitHub na automatização do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5b8ec726c81dfab710d30c37d6fb1aac97c12265
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58293980"
---
# <a name="source-control-integration-in-azure-automation"></a>Integração de controlo de código fonte da Automatização do Azure

Controlo de origem permite-lhe manter os runbooks na sua automação de conta são atualizados com seus scripts no seu repositório de controle de origem GitHub ou de repositórios do Azure. Controlo de origem permite-lhe facilmente colaborar com sua equipe, controlar as alterações e reverta para versões anteriores dos seus runbooks. Por exemplo, o controlo de origem permite-lhe sincronizar ramificações diferentes no controle de origem para as contas de automatização de desenvolvimento, teste ou produção. Isto torna mais fácil promover o código que foi testado em seu ambiente de desenvolvimento para a sua conta de automatização de produção. Integração do controlo de origem com a automatização suporta única direção a sincronizar a partir do seu repositório de controle de origem.

A automatização do Azure suporta 3 tipos de controle de origem:

* GitHub
* Repositórios do Azure (Git)
* Azure Repos (TFVC)

## <a name="pre-requisites"></a>Pré-requisitos

* Um repositório de controle de origem (GitHub ou repositórios do Azure)
* A [conta executar como](manage-runas-account.md)

> [!NOTE]
> Tarefas de sincronização de controlo de origem ser executado sob os conta de automatização de utilizadores e são cobradas à mesma tarifa como outras tarefas de automatização.

## <a name="configure-source-control---azure-portal"></a>Configurar o controlo de origem - portal do Azure

Na sua conta de automatização, selecione **controlo de origem** e clique em **+ adicionar**

![Selecione o controlo de origem](./media/source-control-integration/select-source-control.png)

Escolher **tipo de controlo de origem**, clique em **Authenticate**. Uma janela do browser abre-se e pede-lhe iniciar sessão, siga as instruções para a autenticação completa.

Sobre o **resumo de controlo de origem** página, preencha as informações e clique em **guardar**. A tabela seguinte mostra uma descrição dos campos disponíveis.

|Propriedade  |Descrição  |
|---------|---------|
|Nome do controlo de origem     | Um nome amigável para o controle de origem        |
|Tipo de controlo de origem     | O tipo de origem do controle de origem. As opções disponíveis são:</br> GitHub</br>Repositórios do Azure (Git)</br> Azure Repos (TFVC)        |
|Repositório     | O nome do repositório ou projeto. Os primeiras 200 repositórios são devolvidos. Para procurar um repositório, escreva o nome no campo e clique em **pesquisa no GitHub**.|
|Branch     | O ramo para extrair os ficheiros de origem do. Direcionamento de ramo não está disponível para o tipo de controlo de origem TFVC.          |
|Caminho da pasta     | A pasta que contém os runbooks para sincronizar. Exemplo: /Runbooks </br>*Apenas runbooks na pasta especificada são sincronizados. Não é suportada a recursão.*        |
|Sincronização automática     | Folheio ou desativar a sincronização automática quando uma consolidação é efetuada no repositório de controle de origem         |
|Publicar o Runbook     | Se definido como **no**, após os runbooks são sincronizados a partir do controlo de origem que serão publicados automaticamente.         |
|Descrição     | Um campo de texto para fornecer detalhes adicionais        |

![Resumo de controlo de origem](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Certifique-se de que iniciou sessão com a conta correta quando configurar o controlo de origem. Se houver dúvida, abra um novo separador no seu browser e terminar sessão da visualstudio.com ou github.com e tente novamente a ligação de controlo de origem.

## <a name="configure-source-control---powershell"></a>Configurar o controlo de origem - PowerShell

Também pode utilizar o PowerShell para configurar o controlo de origem na automatização do Azure. Para configurar o controlo de origem com os cmdlets do PowerShell, uma [token de acesso pessoal (PAT)](#personal-access-token) é necessária. Utilizar o [New-AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl) para criar a ligação de controlo de origem. O cmdlet requer uma cadeia segura do Token de acesso pessoal, para saber como criar uma cadeia segura, consulte [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

### <a name="azure-repos-git"></a>Repositórios do Azure (Git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<account>.visualstudio.com/DefaultCollection/<project>/_git/<repository> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Azure Repos (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<account>.visualstudio.com/<projectName>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<account>/<repoName>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>Permissões de token de acesso pessoal

Controlo de origem requer algumas permissões mínimas para tokens de acesso pessoal. As tabelas seguintes incluem as permissões mínimas necessárias para o GitHub e transparente de repositórios do Azure.

#### <a name="github"></a>GitHub

Para obter mais informações sobre como criar um token de acesso pessoal no GitHub, visite [criar um token de acesso pessoal para a linha de comandos](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Âmbito  |Descrição  |
|---------|---------|
|**repo** (repositório)     |         |
|repo:status     | Estado de consolidação de acesso         |
|repo_deployment      | Estado de implementação de acesso         |
|public_repo     | Repositórios públicos de acesso         |
|**admin:repo_hook**     |         |
|write:repo_hook     | Escrever ganchos de repositório         |
|read:repo_hook|Leia os ganchos de repositório|

#### <a name="azure-repos"></a>Repositórios do Azure

Para obter mais informações sobre como criar um token de acesso pessoal nos repositórios do Azure, visite [autenticar o acesso com tokens de acesso pessoal](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

|Âmbito  |
|---------|
|Código (ler)     |
|Projeto e a equipa (ler)|
|Identidade (ler)      |
|Perfil de utilizador (leitura)     |
|Itens de trabalho (ler)    |
|Ligações de serviço (leitura, consultar e gerir)<sup>1</sup>    |

<sup>1</sup>permissão a ligações de serviço só é necessário se tiver ativado o autosync.

## <a name="syncing"></a>A sincronizar

Selecione a origem da tabela no **controle de fonte** página. Clique em **Iniciar sincronização** para iniciar o processo de sincronização.

Pode ver o estado da tarefa de sincronização atual ou aqueles anteriores ao clicar o **tarefas de sincronização** separador. Sobre o **controlo de origem** pendente, selecione um controlo de origem.

![Estado da sincronização](./media/source-control-integration/sync-status.png)

Clicar numa tarefa permite-lhe ver o resultado da tarefa. O exemplo seguinte é a saída de uma tarefa de sincronização de controlo de origem.

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

Registo adicional está disponível, selecionando **todos os registos** no **controlo de origem de sincronização de resumo da tarefa** página. Estas entradas de registo adicionais podem ajudar a resolver problemas que podem surgir ao utilizar o controlo de origem.

## <a name="disconnecting-source-control"></a>A desligar o controlo de origem

Para desligar a partir de um repositório de controle de origem, abra **controle de fonte** sob **definições de conta** na conta de automatização.

Selecione o controlo de origem que pretende remover. Sobre o **resumo de controlo de origem** página, clique em **eliminar**.

## <a name="encoding"></a>Codificação

Se várias pessoas estão a editar runbooks no seu repositório de controle de origem com diferentes editores há uma oportunidade de se depare com problemas de codificação. Isso pode inserir caracteres incorretos no runbook. Para obter mais informações, consulte o artigo [faz com que o comum de problemas de codificação](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre os tipos de runbook, as vantagens e limitações, consulte o artigo [Tipos de runbook da Automatização do Azure](automation-runbook-types.md)