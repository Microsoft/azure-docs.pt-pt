---
title: Controle do código-fonte no Azure Data Factory | Microsoft Docs
description: Saiba como configurar o controle do código-fonte no Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: 91c0ad260c16ac09fd764246d31f99d481f8f147
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68886835"
---
# <a name="source-control-in-azure-data-factory"></a>Controle do código-fonte no Azure Data Factory

A experiência de interface do usuário do Azure Data Factory (UX) tem duas experiências disponíveis para a criação visual:

- Crie diretamente com o serviço de Data Factory
- Crie com Azure Repos a integração do git ou do GitHub

## <a name="author-directly-with-the-data-factory-service"></a>Crie diretamente com o serviço de Data Factory

Ao criar diretamente com o serviço de Data Factory, a única maneira de salvar as alterações é por meio do botão **publicar tudo** . Depois de clicar, todas as alterações feitas são publicadas diretamente no serviço de Data Factory. 

![Modo de publicação](media/author-visually/data-factory-publish.png)

A criação direta com o serviço de Data Factory tem as seguintes limitações:

- O serviço de Data Factory não inclui um repositório para armazenar as entidades JSON para suas alterações.
- O serviço de Data Factory não é otimizado para colaboração ou controle de versão.

> [!NOTE]
> A criação direta com o serviço de Data Factory está desabilitada no Azure Data Factory UX quando um repositório Git é configurado. As alterações podem ser feitas diretamente no serviço por meio do PowerShell ou de um SDK.

## <a name="author-with-azure-repos-git-integration"></a>Criar com Azure Repos integração com o Git

A criação visual com Azure Repos integração do git dá suporte ao controle do código-fonte e à colaboração para o trabalho em seus pipelines de data factory. Você pode associar um data factory a um repositório de organização git Azure Repos para controle do código-fonte, colaboração, versionamento e assim por diante. Uma única organização git Azure Repos pode ter vários repositórios, mas um repositório git Azure Repos pode ser associado a apenas um data factory. Se você não tiver uma organização ou repositório Azure Repos, siga [estas instruções](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) para criar seus recursos.

> [!NOTE]
> Você pode armazenar scripts e arquivos de dados em um repositório git Azure Repos. No entanto, você precisa carregar os arquivos manualmente no armazenamento do Azure. Um pipeline Data Factory não carrega automaticamente arquivos de script ou de dados armazenados em um repositório git Azure Repos no armazenamento do Azure.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Configurar um repositório git Azure Repos com Azure Data Factory

Você pode configurar um repositório git Azure Repos com um data factory por meio de dois métodos.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Método de configuração 1: Azure Data Factory home page

Na home page Azure Data Factory, selecione **configurar repositório de código**.

![Configurar um repositório de código de Azure Repos](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Método de configuração 2: Tela de criação de UX
Na tela de criação do Azure Data Factory UX, selecione o menu suspenso **Data Factory** e, em seguida, selecione **configurar repositório de código**.

![Definir as configurações do repositório de código para a criação de UX](media/author-visually/configure-repo-2.png)

Ambos os métodos abrem o painel de configuração configurações do repositório.

![Definir as configurações do repositório de código](media/author-visually/repo-settings.png)

O painel de configuração mostra as seguintes configurações de Azure Repos repositório de código:

| Definição | Descrição | Valor |
|:--- |:--- |:--- |
| **Tipo de repositório** | O tipo do repositório de código de Azure Repos.<br/> | Azure DevOps git ou GitHub |
| **Azure Active Directory** | O nome do locatário do Azure AD. | `<your tenant name>` |
| **Azure Repos organização** | O nome da organização Azure Repos. Você pode localizar o nome da organização Azure Repos `https://{organization name}.visualstudio.com`em. Você pode [entrar em sua organização do Azure Repos](https://www.visualstudio.com/team-services/git/) para acessar seu perfil do Visual Studio e ver seus repositórios e projetos. | `<your organization name>` |
| **ProjectName** | O nome do projeto de Azure Repos. Você pode localizar o nome do projeto Azure Repos `https://{organization name}.visualstudio.com/{project name}`em. | `<your Azure Repos project name>` |
| **RepositoryName** | O nome do repositório de código de Azure Repos. Azure Repos projetos contêm repositórios git para gerenciar seu código-fonte à medida que seu projeto cresce. Você pode criar um novo repositório ou usar um repositório existente que já esteja em seu projeto. | `<your Azure Repos code repository name>` |
| **Branch de colaboração** | Sua Azure Repos Branch de colaboração que é usada para publicação. Por padrão, seu `master`. Altere essa configuração caso você queira publicar recursos de outra ramificação. | `<your collaboration branch name>` |
| **Pasta raiz** | Sua pasta raiz na sua Azure Repos Branch de colaboração. | `<your root folder name>` |
| **Importar recursos existentes do Data Factory para o repositório** | Especifica se os recursos existentes do data factory devem ser importados da **tela de criação** UX em um repositório git Azure repos. Selecione a caixa para importar seus recursos de data factory para o repositório git associado no formato JSON. Essa ação exporta cada recurso individualmente (ou seja, os serviços vinculados e os conjuntos de valores são exportados para JSONs separados). Quando essa caixa não está selecionada, os recursos existentes não são importados. | Selecionado (padrão) |
| **Branch para importar recurso para** | Especifica em qual ramificação os recursos de data factory (pipelines, conjuntos de valores, serviços vinculados etc.) são importados. Você pode importar recursos para um dos seguintes branches: a. Colaboração b. Criar novo c. Utilizar Existente |  |

> [!NOTE]
> Se você estiver usando o Microsoft Edge e não vir nenhum valor em seu menu suspenso da conta do Azure DevOps, adicione https://*. VisualStudio. com à lista de sites confiáveis.

### <a name="use-a-different-azure-active-directory-tenant"></a>Usar um locatário de Azure Active Directory diferente

Pode criar um repositório Git do Azure Repos num inquilino diferente do Azure Active Directory. Para especificar um locatário diferente do Azure AD, você precisa ter permissões de administrador para a assinatura do Azure que você está usando.

### <a name="use-your-personal-microsoft-account"></a>Usar seu conta Microsoft pessoal

Para usar um conta Microsoft pessoal para integração com o Git, você pode vincular seu repositório pessoal do Azure ao Active Directory da sua organização.

1. Adicione seus conta Microsoft pessoais ao Active Directory da sua organização como um convidado. Para obter mais informações, consulte [adicionar Azure Active Directory usuários de colaboração B2B no portal do Azure](../active-directory/b2b/add-users-administrator.md).

2. Faça logon no portal do Azure com sua conta Microsoft pessoal. Em seguida, alterne para o Active Directory da sua organização.

3. Vá para a seção DevOps do Azure, em que agora você vê seu repositório pessoal. Selecione o repositório e conecte-se com Active Directory.

Após essas etapas de configuração, seu repositório pessoal estará disponível quando você configurar a integração do git na interface do usuário do Data Factory.

Para obter mais informações sobre como conectar Azure Repos à Active Directory da sua organização, consulte [conectar sua organização do Azure DevOps ao Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="author-with-github-integration"></a>Criar com integração do GitHub

A criação visual com a integração do GitHub dá suporte ao controle do código-fonte e à colaboração para o trabalho em seus pipelines de data factory. Você pode associar um data factory a um repositório de conta do GitHub para controle do código-fonte, colaboração, versionamento. Uma única conta do GitHub pode ter vários repositórios, mas um repositório GitHub pode ser associado a apenas um data factory. Se você não tiver uma conta ou repositório do GitHub, siga [estas instruções](https://github.com/join) para criar seus recursos.

A integração do GitHub com o data Factory dá suporte ao GitHub público ( [https://github.com](https://github.com)ou seja,) e ao GitHub Enterprise. Você pode usar repositórios GitHub públicos e privados com Data Factory, desde que tenha permissão de leitura e gravação para o repositório no GitHub.

Para configurar um repositório GitHub, você deve ter permissões de administrador para a assinatura do Azure que você está usando.

Para uma introdução e uma demonstração desse recurso de nove minutos, Assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>Configurar um repositório GitHub com Azure Data Factory

Você pode configurar um repositório GitHub com um data factory por meio de dois métodos.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Método de configuração 1: Azure Data Factory home page

Na home page Azure Data Factory, selecione **configurar repositório de código**.

![Configurar um repositório de código de Azure Repos](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Método de configuração 2: Tela de criação de UX

Na tela de criação do Azure Data Factory UX, selecione o menu suspenso **Data Factory** e, em seguida, selecione **configurar repositório de código**.

![Definir as configurações do repositório de código para a criação de UX](media/author-visually/configure-repo-2.png)

Ambos os métodos abrem o painel de configuração configurações do repositório.

![Configurações do repositório GitHub](media/author-visually/github-integration-image2.png)

O painel de configuração mostra as seguintes configurações do repositório do GitHub:

| **Definição** | **Descrição**  | **Valor**  |
|:--- |:--- |:--- |
| **Tipo de repositório** | O tipo do repositório de código de Azure Repos. | GitHub |
| **Usar o GitHub Enterprise** | Caixa de seleção para selecionar o GitHub empresarial | não selecionado (padrão) |
| **URL do GitHub Enterprise** | A URL raiz da empresa do GitHub. Por exemplo: https://github.mydomain.com. Necessário somente se **usar o GitHub Enterprise** estiver selecionado | `<your GitHub enterprise url>` |                                                           
| **Conta do GitHub** | O nome da sua conta do GitHub. Esse nome pode ser encontrado em https:\//github.com/{Account Name}/{Repository Name}. Navegar até esta página solicita que você insira as credenciais do GitHub OAuth para sua conta do GitHub. | `<your GitHub account name>` |
| **Nome do repositório**  | O nome do repositório de código do GitHub. As contas do GitHub contêm repositórios git para gerenciar seu código-fonte. Você pode criar um novo repositório ou usar um repositório existente que já esteja em sua conta. | `<your repository name>` |
| **Branch de colaboração** | Sua ramificação de colaboração do GitHub que é usada para publicação. Por padrão, seu mestre. Altere essa configuração caso você queira publicar recursos de outra ramificação. | `<your collaboration branch>` |
| **Pasta raiz** | Sua pasta raiz na sua ramificação de colaboração do GitHub. |`<your root folder name>` |
| **Importar recursos existentes do Data Factory para o repositório** | Especifica se os recursos existentes do data factory devem ser importados da tela de criação do UX em um repositório do GitHub. Selecione a caixa para importar seus recursos de data factory para o repositório git associado no formato JSON. Essa ação exporta cada recurso individualmente (ou seja, os serviços vinculados e os conjuntos de valores são exportados para JSONs separados). Quando essa caixa não está selecionada, os recursos existentes não são importados. | Selecionado (padrão) |
| **Branch para importar recurso para** | Especifica em qual ramificação os recursos de data factory (pipelines, conjuntos de valores, serviços vinculados etc.) são importados. Você pode importar recursos para um dos seguintes branches: a. Colaboração b. Criar novo c. Utilizar Existente |  |

### <a name="known-github-limitations"></a>Limitações conhecidas do GitHub

- Você pode armazenar scripts e arquivos de dados em um repositório GitHub. No entanto, você precisa carregar os arquivos manualmente no armazenamento do Azure. Um pipeline Data Factory não carrega automaticamente arquivos de script ou de dados armazenados em um repositório GitHub para o armazenamento do Azure.

- O GitHub Enterprise com uma versão anterior a 2.14.0 não funciona no navegador Microsoft Edge.

- A integração do GitHub com o Data Factory ferramentas de criação visual funciona apenas na versão disponível do Data Factory.

## <a name="switch-to-a-different-git-repo"></a>Alternar para um repositório git diferente

Para alternar para um repositório git diferente, clique no ícone de **configurações do repositório git** no canto superior direito da página Visão geral do data Factory. Se você não conseguir ver o ícone, limpe o cache do navegador local. Selecione o ícone para remover a associação com o repositório atual.

![Ícone do git](media/author-visually/remove-repo.png)

Depois que o painel configurações do repositório for exibido, selecione **remover git**. Insira seu nome de data factory e clique em **confirmar** para remover o repositório git associado ao seu data Factory.

![Remover a associação com o repositório git atual](media/author-visually/remove-repo2.png)

Depois de remover a associação com o repositório atual, você pode definir as configurações do git para usar um repositório diferente e, em seguida, importar recursos existentes do Data Factory para o novo repositório. 

## <a name="version-control"></a>Controle de versão

Os sistemas de controle de versão (também conhecidos como _controle do código-fonte_) permitem que os desenvolvedores colaborem no código e controlem as alterações feitas na base de código. O controle do código-fonte é uma ferramenta essencial para projetos de vários desenvolvedores.

### <a name="creating-feature-branches"></a>Criando ramificações de recurso

Cada repositório git Azure Repos que está associado a um data factory tem uma ramificação de colaboração. (`master` é a ramificação de colaboração padrão). Os usuários também podem criar ramificações de recursos clicando em **+ nova ramificação** na lista suspensa Branch. Depois que o painel novo Branch for exibido, insira o nome do Branch de recursos.

![Criar uma nova ramificação](media/author-visually/new-branch.png)

Quando você estiver pronto para mesclar as alterações do Branch de recursos para sua ramificação de colaboração, clique na lista suspensa Branch e selecione **criar solicitação pull**. Essa ação leva você para Azure Repos git, em que você pode gerar solicitações de pull, fazer revisões de código e mesclar alterações em sua ramificação de colaboração. (`master` é o padrão). Você só tem permissão para publicar no serviço de Data Factory da sua ramificação de colaboração. 

![Criar uma nova solicitação pull](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Definir configurações de publicação

Para configurar o Branch de publicação – ou seja, a ramificação em que os modelos do Resource Manager `publish_config.json` são salvos-adicione um arquivo à pasta raiz no Branch de colaboração. Data Factory lê esse arquivo, procura o campo `publishBranch`e cria uma nova ramificação (se ela ainda não existir) com o valor fornecido. Em seguida, ele salva todos os modelos do Resource Manager no local especificado. Por exemplo:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Quando você especifica uma nova ramificação de publicação, Data Factory não exclui o Branch de publicação anterior. Se você quiser que o Branch de publicação anterior seja remoto, exclua-o manualmente.

> [!NOTE]
> Data Factory lê o `publish_config.json` arquivo somente quando ele carrega a fábrica. Se você já tiver a fábrica carregada no portal, atualize o navegador para fazer suas alterações entrarem em vigor.

### <a name="publish-code-changes"></a>Publicar alterações de código

Depois de mesclar as alterações para a ramificação de`master` colaboração (é o padrão), clique em **publicar** para publicar manualmente as alterações de código no Branch mestre para o serviço de data Factory.

![Publicar alterações no serviço de Data Factory](media/author-visually/publish-changes.png)

Um painel lateral será aberto onde você confirmar que a ramificação de publicação e as alterações pendentes estão corretas. Depois de verificar as alterações, clique em **OK** para confirmar a publicação.

![Confirmar o Branch de publicação correto](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> O Branch mestre não é representativo do que é implantado no serviço de Data Factory. O Branch mestre *deve* ser publicado manualmente para o serviço de data Factory.

## <a name="advantages-of-git-integration"></a>Vantagens da integração do git

-   **Controle do código-fonte**. À medida que suas cargas de trabalho de data factory se tornarem cruciais, você desejaria integrar sua fábrica com o Git para aproveitar vários benefícios de controle do código-fonte, como o seguinte:
    -   Capacidade de controlar/auditar alterações.
    -   Capacidade de reverter alterações que introduziram bugs.
-   **Gravações parciais**. Ao fazer muitas alterações em sua fábrica, você perceberá que, no modo dinâmico normal, não poderá salvar suas alterações como rascunho, porque você não está pronto ou não deseja perder as alterações caso o computador falhe. Com a integração do git, você pode continuar salvando as alterações incrementalmente e publicar na fábrica somente quando estiver pronto. O Git age como um local de preparo para seu trabalho, até que você tenha testado suas alterações para sua satisfação.
-   **Colaboração e controle**. Se você tiver vários membros da equipe participando da mesma fábrica, talvez queira permitir que seus colegas colaborem entre si por meio de um processo de revisão de código. Você também pode configurar sua fábrica de forma que nem todos os colaboradores da fábrica tenham permissão para implantar na fábrica. Os integrantes da equipe podem ter apenas permissão para fazer alterações via git, mas apenas determinadas pessoas na equipe têm permissão para "publicar" as alterações na fábrica.
-   **Mostrando diffs**. No modo git, você consegue ver uma boa diferença da carga que está prestes a ser publicada na fábrica. Essa comparação mostra todos os recursos/entidades que foram modificados/adicionados/excluídos desde a última vez que você publicou em sua fábrica. Com base nessa diferença, você pode continuar com a publicação ou voltar e verificar as alterações e, em seguida, voltar mais tarde.
-   **CI/CD melhor**. Se você estiver usando o modo git, poderá configurar seu pipeline de liberação para disparar automaticamente assim que houver qualquer alteração feita na fábrica de desenvolvimento. Você também tem de personalizar as propriedades em sua fábrica que estão disponíveis como parâmetros no modelo do Resource Manager. Pode ser útil manter apenas o conjunto necessário de propriedades como parâmetros e ter tudo o que está embutido em código.
-   **Melhor desempenho**. Uma fábrica média carrega dez vezes mais rápido no modo git do que no modo dinâmico normal, pois os recursos são baixados por meio do git.

## <a name="best-practices-for-git-integration"></a>Práticas recomendadas para a integração com o Git

### <a name="permissions"></a>Permissões

Normalmente, você não quer que todos os membros da equipe tenham permissões para atualizar a fábrica. As seguintes configurações de permissões são recomendadas:

*   Todos os membros da equipe devem ter permissões de leitura para o data factory.
*   Somente um conjunto selecionado de pessoas deve ter permissão para publicar na fábrica. Para fazer isso, eles devem ter a função de **colaborador de data Factory** na fábrica. Para obter mais informações sobre permissões, consulte [funções e permissões para Azure data Factory](concepts-roles-permissions.md).
   
é recomendável não permitir check-ins diretos na ramificação de colaboração. Essa restrição pode ajudar a evitar bugs, uma vez que cada check-in passará por um processo de solicitação de pull.

### <a name="using-passwords-from-azure-key-vault"></a>Usando senhas de Azure Key Vault

é recomendável usar Azure Key Vault para armazenar quaisquer cadeias de conexão ou senhas para Data Factory serviços vinculados. Por motivos de segurança, não armazenamos nenhuma informação secreta no git, portanto, quaisquer alterações nos serviços vinculados são publicadas imediatamente no serviço de Azure Data Factory.

Usar Key Vault também facilita a integração e a implantação contínua, pois você não precisará fornecer esses segredos durante a implantação do modelo do Resource Manager.

## <a name="troubleshooting-git-integration"></a>Solução de problemas de integração do git

### <a name="stale-publish-branch"></a>Branch de publicação obsoleto

Se a ramificação de publicação estiver fora de sincronia com a ramificação mestre e contiver recursos desatualizados, independentemente de uma publicação recente, tente seguir estas etapas:

1. Remova seu repositório git atual/
1. Reconfigure o Git com as mesmas configurações, mas certifique-se de que **importar recursos existentes do data Factory para o repositório** esteja selecionado e escolha **novo Branch**
1. Excluir todos os recursos de sua ramificação de colaboração
1. Criar uma solicitação de pull para mesclar as alterações para a ramificação de colaboração 

## <a name="provide-feedback"></a>Enviar comentários
Selecione **comentários** para comentar sobre recursos ou notificar a Microsoft sobre problemas com a ferramenta:

![Comentários](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre como monitorar e gerenciar pipelines, confira [monitorar e gerenciar pipelines](monitor-programmatically.md)programaticamente.
* Para implementar a integração e a implantação contínuas, consulte [integração e entrega contínuas (CI/CD) em Azure data Factory](continuous-integration-deployment.md).
