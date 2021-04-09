---
title: Controlo de fontes no Estúdio Synapse
description: Saiba como configurar o controlo de origem no Azure Synapse Studio
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 3564609d869bef090f0a3db5e6040ba0f5ad80b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98796962"
---
# <a name="source-control-in-azure-synapse-studio"></a>Controlo de fontes no Azure Synapse Studio

Por padrão, os autores do Azure Synapse Studio contra o serviço Synapse. Se tiver necessidade de colaboração usando Git para controlo de fontes, o Synapse Studio permite-lhe associar o seu espaço de trabalho a um repositório Git, Azure DevOps ou GitHub. 

Este artigo irá delinear como configurar e trabalhar num espaço de trabalho da Sinapse com repositório de git habilitado. E também destacamos algumas boas práticas e um guia de resolução de problemas.

> [!NOTE]
> A integração do Azure Synapse Studio não está disponível na Nuvem do Governo de Azure.

## <a name="configure-git-repository-in-synapse-studio"></a>Configure git repositório no Estúdio Synapse 

Depois de lançar o seu Synapse Studio, pode configurar um repositório de git no seu espaço de trabalho. Um espaço de trabalho do Synapse Studio pode ser associado com apenas um repositório de git de cada vez. 

### <a name="configuration-method-1-global-bar"></a>Método de configuração 1: barra global

No bar global Synapse Studio, selecione o menu de drop-down **Synapse Live** e, em seguida, selecione **Configurar o repositório de códigos**.

![Configurar as definições de repositório de código da autoria](media/configure-repo-1.png)

### <a name="configuration-method-2-manage-hub"></a>Método de configuração 2: Gerir o hub

Vá ao centro de gestão do Estúdio Synapse. Selecione **a configuração de Git** na secção de controlo **'Fonte'.** Se não tiver nenhum repositório ligado, clique em **Configurar**.

![Configurar as definições de repositório de código do centro de gestão](media/configure-repo-2.png)

> [!NOTE]
> Os utilizadores concedidos como contribuintes, proprietários ou funções de nível superior podem configurar, editar Definição e desligar o repositório de git no estúdio Azure Synapse 

Pode ligar o repositório de gits de Azure DevOps ou GitHub git no seu espaço de trabalho.

## <a name="connect-with-azure-devops-git"></a>Conecte-se com Azure DevOps Git 

Você pode associar um espaço de trabalho Synapse com um Repositório Azure DevOps para controlo de origem, colaboração, versão, e assim por diante. Se não tiver um repositório Azure DevOps, siga [estas instruções](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) para criar os seus recursos de repositório.

### <a name="azure-devops-git-repository-settings"></a>Azure DevOps Git configurações de repositório

Ao ligar-se ao seu repositório de git, selecione primeiro o seu tipo de repositório como Azure DevOps git, e, em seguida, selecione um inquilino AZure AD da lista de dropdown, e clique em **Continuar**.

![Configurar as definições de repositório de código](media/connect-with-azuredevops-repo-selected.png)

O painel de configuração mostra as seguintes definições de git Azure DevOps:

| Definições | Descrição | Valor |
|:--- |:--- |:--- |
| **Tipo repositório** | O tipo de repositório de código Azure Repos.<br/> | Azure DevOps Git ou GitHub |
| **Azure Active Directory** | Seu nome de inquilino da AZure AD. | `<your tenant name>` |
| **Conta Azure DevOps** | O nome da organização do Azure Repos. Pode localizar o nome da organização Azure Repos em `https://{organization name}.visualstudio.com` . Pode [iniciar sessão na sua organização Azure Repos](https://www.visualstudio.com/team-services/git/) para aceder ao seu perfil de Estúdio Visual e ver os seus repositórios e projetos. | `<your organization name>` |
| **Nome do Projeto** | O nome do seu projeto Azure Repos. Pode localizar o nome do seu projeto Azure Repos em `https://{organization name}.visualstudio.com/{project name}` . | `<your Azure Repos project name>` |
| **Nome do Repositório** | O seu nome de repositório de código Azure Repos. Os projetos Azure Repos contêm repositórios de Git para gerir o seu código fonte à medida que o seu projeto cresce. Você pode criar um novo repositório ou usar um repositório existente que já está no seu projeto. | `<your Azure Repos code repository name>` |
| **Ramo de colaboração** | O seu ramo de colaboração Azure Repos que é usado para a publicação. Por defeito, a sua `master` . Altere esta definição no caso de pretender publicar recursos a partir de outra sucursal. Pode selecionar os ramos existentes ou criar novos | `<your collaboration branch name>` |
| **Pasta raiz** | A sua pasta de raiz no seu ramo de colaboração Azure Repos. | `<your root folder name>` |
| **Importar os recursos existentes para o repositório** | Especifica se importa os recursos existentes do Estúdio Synapse para um repositório Azure Repos Git. Verifique a caixa para importar os seus recursos de espaço de trabalho (exceto piscinas) no repositório de Git associado no formato JSON. Esta ação exporta cada recurso individualmente. Quando esta caixa não é verificada, os recursos existentes não são importados. | Verificado (predefinido) |
| **Recurso de importação para este ramo** | Selecione para que ramo os recursos (script sql, caderno, definição de trabalho de faísca, conjunto de dados, fluxo de dados, etc.) são importados. 

Também pode usar o link do repositório para apontar rapidamente para o repositório de git com o que pretende ligar. 

### <a name="use-a-different-azure-active-directory-tenant"></a>Use um inquilino azure ative inquilina diferente

O Azure Repos Git repo pode estar em um inquilino azure ative diretório diferente. Para especificar um inquilino do Azure AD diferente, tem de ter permissões de administrador para a subscrição do Azure que está a utilizar. Para mais informações, consulte [o administrador de subscrição de alteração](../../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator)

> [!IMPORTANT]
> Para se ligar a outro Diretório Ativo Azure, o utilizador que fez login deve fazer parte desse diretório ativo. 

### <a name="use-your-personal-microsoft-account"></a>Utilize a sua conta pessoal da Microsoft

Para utilizar uma conta pessoal da Microsoft para a integração do Git, pode ligar o seu Azure Repo pessoal ao Ative Directory da sua organização.

1. Adicione a sua conta pessoal da Microsoft ao Ative Directory da sua organização como hóspede. Para mais informações, consulte [os utilizadores de colaboração do Add Azure Ative Directory B2B no portal Azure](../../active-directory/external-identities/add-users-administrator.md).

2. Faça login no portal Azure com a sua conta pessoal da Microsoft. Em seguida, mude para o Diretório Ativo da sua organização.

3. Vá à secção Azure DevOps, onde agora vê o seu repo pessoal. Selecione o repo e conecte-se com o Ative Directory.

Após estes passos de configuração, o seu repo pessoal está disponível quando configurar a integração git no Synapse Studio.

Para obter mais informações sobre a ligação do Azure Repos ao Ative Directory da sua organização, consulte [Connect your organization to Azure Ative Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="connect-with-github"></a>Conecte-se com GitHub 

 Você pode associar um espaço de trabalho com um repositório GitHub para controlo de origem, colaboração, versão. Se não tiver uma conta GitHub ou repositório, siga [estas instruções](https://github.com/join) para criar os seus recursos.

A integração do GitHub com o Synapse Studio suporta tanto o GitHub público (isto [https://github.com](https://github.com) é,) como a GitHub Enterprise. Você pode usar repositórios públicos e privados gitHub desde que tenha lido e escrito permissão para o repositório em GitHub.

### <a name="github-settings"></a>Configurações do GitHub

Ao ligar-se ao seu repositório de git, primeiro selecione o seu tipo de repositório como GitHub e, em seguida, forneça a sua conta GitHub ou GITHub Enterprise Server URL se utilizar o GitHub Enterprise Server e clique em **Continuar**.

![Configurações do repositório gitHub](media/connect-with-github-repo-1.png)

O painel de configuração mostra as seguintes definições de repositório GitHub:

| **Definição** | **Descrição**  | **Valor**  |
|:--- |:--- |:--- |
| **Tipo repositório** | O tipo de repositório de código Azure Repos. | GitHub |
| **Use GitHub Enterprise** | Caixa de verificação para selecionar GitHub Enterprise | não selecionado (padrão) |
| **URL da Empresa GitHub** | O URL raiz gitHub Enterprise (deve ser HTTPS para o servidor local gitHub Enterprise). Por exemplo: `https://github.mydomain.com`. Só é necessário se **a Utilização da GitHub Enterprise** for selecionada | `<your GitHub enterprise url>` |                                                           
| **Conta GitHub** | O nome da sua conta GitHub. Este nome pode ser encontrado em https: \/ /github.com/{account name}/{repository name}. Navegar nesta página leva-o a introduzir credenciais GitHub OAuth na sua conta GitHub. | `<your GitHub account name>` |
| **Nome do Repositório**  | O teu nome de repositório de código GitHub. As contas do GitHub contêm repositórios de Git para gerir o seu código fonte. Pode criar um novo repositório ou usar um repositório existente que já está na sua conta. | `<your repository name>` |
| **Ramo de colaboração** | O seu ramo de colaboração GitHub que é usado para a publicação. Por defeito, o seu mestre. Altere esta definição no caso de pretender publicar recursos a partir de outra sucursal. | `<your collaboration branch>` |
| **Pasta raiz** | A sua pasta de raiz no seu ramo de colaboração GitHub. |`<your root folder name>` |
| **Importar os recursos existentes para o repositório** | Especifica se importa os recursos existentes do Estúdio Synapse para um repositório de Git. Verifique a caixa para importar os seus recursos de espaço de trabalho (exceto piscinas) no repositório de Git associado no formato JSON. Esta ação exporta cada recurso individualmente. Quando esta caixa não é verificada, os recursos existentes não são importados. | Selecionado (predefinido) |
| **Recurso de importação para este ramo** | Selecione qual o ramo dos recursos (script sql, caderno, definição de trabalho de faísca, conjunto de dados, fluxo de dados, etc.) é importado. 

### <a name="github-organizations"></a>Organizações GitHub

A ligação a uma organização do GitHub requer que a organização conceda permissão ao Synapse Studio. Um utilizador com permissões ADMIN na organização deve realizar os passos abaixo.

#### <a name="connecting-to-github-for-the-first-time"></a>Primeira ligação ao GitHub

Se estiver a ligar-se ao GitHub do Synapse Studio pela primeira vez, siga estes passos para se ligar a uma organização do GitHub.

1. No painel de configuração Git, insira o nome da organização no campo *Conta GitHub.* Aparecerá um pedido para iniciar sessão no GitHub. 

1. Faça login usando as suas credenciais de utilizador.

1. Ser-lhe-á pedido que autorize a Synapse como uma aplicação chamada *Azure Synapse.* Neste ecrã, você verá uma opção para conceder permissão para a Synapse aceder à organização. Se não vir a opção de conceder permissão, peça a um administrador que conceda manualmente a permissão através do GitHub.

Assim que seguir estes passos, o seu espaço de trabalho poderá ligar-se aos repositórios públicos e privados dentro da sua organização. Se não conseguir ligar, tente limpar a cache do navegador e voltar a tentar.

#### <a name="already-connected-to-github-using-a-personal-account"></a>Já ligado ao GitHub usando uma conta pessoal

Se já ligou ao GitHub e só autorizou o acesso a uma conta pessoal, siga os passos abaixo para conceder permissões a uma organização.

1. Vá ao GitHub e abra **as definições**.

    ![Abra as definições do GitHub](media/github-settings.png)

1. Selecione **Aplicações**. No separador **de aplicações OAuth Autorizado,** deverá ver *Azure Synapse*.

    ![Autorizar aplicações OAuth](media/authorize-app.png)

1. Selecione o *Azure Synapse* e conceda o acesso à sua organização.

    ![Concessão de permissão de organização](media/grant-organization-permission.png)

Assim que completar estes passos, o seu espaço de trabalho poderá ligar-se aos repositórios públicos e privados dentro da sua organização.

## <a name="version-control"></a>Controlo de versões

Os sistemas de controlo de versão (também conhecidos como _controlo de fontes)_ permitem que os desenvolvedores colaborem em alterações de código e de rastreio. O controlo de fontes é uma ferramenta essencial para projetos multi-desenvolvedores.

### <a name="creating-feature-branches"></a>Criação de ramos de recursos

Cada repositório de Git que está associado a um Estúdio Synapse tem um ramo de colaboração. (ou `main` é o ramo de `master` colaboração padrão). Os utilizadores também podem criar ramos de funcionalidade clicando **+ Novo Ramo** no dropdown do ramo. Assim que aparecer o novo painel de ramificação, insira o nome do seu ramo de recurso.

![Criar um novo ramo](media/create-new-branch.png)

Quando estiver pronto para fundir as alterações do seu ramo de recurso para o seu ramo de colaboração, clique no dropdown do ramo e selecione **Criar pedido de puxar**. Esta ação leva-o ao fornecedor Git onde pode levantar pedidos de puxar, fazer revisões de código e fundir alterações no seu ramo de colaboração. Só pode publicar no serviço Synapse a partir da sua filial de colaboração. 

![Criar um novo pedido de puxar](media/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Configurar configurações de publicação

Por padrão, o Synapse Studio gera os modelos de espaço de trabalho e guarda-os num ramo chamado `workspace_publish` . Para configurar um ramo de publicação personalizado, adicione um `publish_config.json` ficheiro à pasta raiz no ramo de colaboração. Ao publicar, o Synapse Studio lê este ficheiro, procura o campo `publishBranch` e guarda ficheiros de modelos de espaço de trabalho para o local especificado. Se o ramo não existir, o Synapse Studio irá automaticamente criá-lo. E exemplo do que este ficheiro parece estar abaixo:

```json
{
    "publishBranch": "workspace_publish"
}
```

O Azure Synapse Studio só pode ter uma filial de cada vez. Quando especificar uma nova filial de publicação, a sucursal de publicação anterior não seria eliminada. Se pretender remover o ramo de publicação anterior, elimine-o manualmente.


### <a name="publish-code-changes"></a>Publicar alterações de código

Após a fusão das alterações na sucursal de colaboração, clique em **Publicar** para publicar manualmente as alterações de código na sucursal de colaboração para o serviço Synapse.

![Publicar alterações](media/gitmode-publish.png)

Um painel lateral abrir-se-á onde confirma que a sucursal de publicação e as alterações pendentes estão corretas. Assim que verificar as alterações, clique **em OK** para confirmar a publicação.

![Confirme o ramo de publicação correto](media/publish-change.png)

> [!IMPORTANT]
> O ramo de colaboração não é representativo do que é implantado no serviço. As alterações no ramo de colaboração *devem* ser publicadas manualmente.

## <a name="switch-to-a-different-git-repository"></a>Mude para um repositório de Git diferente

Para mudar para um repositório de Git diferente, aceda à página de configuração git no centro de gestão sob **controlo source**. **Selecione Desligar**. 

![Ícone de Git](media/remove-repository.png)

Introduza o nome do seu espaço de trabalho e clique **em Desligar** para remover o repositório Git associado ao seu espaço de trabalho.

Depois de remover a associação com o repo atual, pode configurar as suas definições de Git para usar um repo diferente e, em seguida, importar recursos existentes para o novo repo.

> [!IMPORTANT]
> Remover a configuração do Git de um espaço de trabalho não apaga nada do repositório. O espaço de trabalho da Sinapse conterá todos os recursos publicados. Pode continuar a editar o espaço de trabalho diretamente contra o serviço.

## <a name="best-practices-for-git-integration"></a>Melhores práticas para integração de Git

-   **Permissões.** Depois de ter um repositório de git ligado ao seu espaço de trabalho, qualquer pessoa que possa aceder ao seu git repo com qualquer papel no seu espaço de trabalho será capaz de atualizar artefactos, como script sql, caderno, definição de trabalho de faísca, conjunto de dados, fluxo de dados e pipeline em modo git. Normalmente, não quer que todos os membros da equipa tenham permissões para atualizar o espaço de trabalho. Apenas conceda permissão de repositório git para autores de artefactos do espaço de trabalho sinapse. 
-   **Colaboração**. Recomenda-se não permitir check-ins diretos no ramo da colaboração. Esta restrição pode ajudar a prevenir bugs, uma vez que cada check-in passará por um processo de revisão de pedidos de pull descrito na [Criação de ramos de funcionalidades.](source-control.md#creating-feature-branches)
-   Modo ao vivo da **sinapse**. Após a publicação no modo git, todas as alterações serão refletidas no modo ao vivo da Synapse. No modo ao vivo da Synapse, a publicação é desativada. E podes ver, executar artefactos em modo ao vivo se te for dada a permissão certa. 
-   **Editar artefactos em Estúdio.** O estúdio Synapse é o único local onde pode ativar o controlo de fontes de espaço de trabalho e alterar automaticamente o git. Qualquer alteração via SDK, PowerShell, não será sincronizada para git. Recomendamos que edite sempre artefacto em Studio quando o git estiver ativado.

## <a name="troubleshooting-git-integration"></a>Integração de git de resolução de problemas

### <a name="access-to-git-mode"></a>Acesso ao modo git 

Se lhe foi concedida a permissão para o repositório gitHub ligado ao seu espaço de trabalho, mas não pode aceder ao modo Git: 

1. Limpe o seu cache e refresque a página. 

1. Inicie sessão na sua conta GitHub.

### <a name="stale-publish-branch"></a>Filial de publicação de Stale

Se a sucursal de publicação estiver dessincronizada com o ramo de colaboração e contiver recursos desatualizados apesar de uma publicação recente, tente seguir estes passos:

1. Remova o seu repositório git atual

1. Reconfigure Git com as mesmas definições, mas certifique-se de que **importar os recursos existentes para repositório** é verificado e escolher o mesmo ramo.  

1. Criar um pedido Pull para unir as alterações no ramo de colaboração 

## <a name="unsupported-features"></a>Funcionalidades não suportadas

- O Synapse Studio não permite a recolha de cerejas de compromissos ou a publicação seletiva de recursos. 
- O Synapse Studio não suporta personalizar a mensagem de compromisso.
- Por design, apagar ação em Studio será comprometido a git diretamente

## <a name="next-steps"></a>Passos seguintes

* Para implementar a integração e implantação contínuas, consulte [integração e entrega contínuas (CI/CD)](continuous-integration-deployment.md).