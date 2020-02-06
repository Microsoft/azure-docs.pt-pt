---
title: Controlo de código fonte
description: Saiba como configurar o controle do código-fonte no Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: ''
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/09/2019
ms.openlocfilehash: 1cc5932eca520b0bbc0c592b54d36ea8b5942b08
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031634"
---
# <a name="source-control-in-azure-data-factory"></a>Controle do código-fonte no Azure Data Factory

A experiência de interface do usuário do Azure Data Factory (UX) tem duas experiências disponíveis para a criação visual:

- Crie diretamente com o serviço de Data Factory
- Crie com Azure Repos a integração do git ou do GitHub

> [!NOTE]
> Somente a criação direta com o serviço de Data Factory tem suporte na nuvem do Azure governamental.

## <a name="author-directly-with-the-data-factory-service"></a>Crie diretamente com o serviço de Data Factory

Enquanto é da autoria direta mente com o serviço Data Factory, a única forma de guardar alterações é através do botão **Publicar Todos.** Depois de clicar, todas as alterações feitas são publicadas diretamente no serviço de Data Factory. 

![Modo de publicação](media/author-visually/data-factory-publish.png)

A criação direta com o serviço de Data Factory tem as seguintes limitações:

- O serviço de Data Factory não inclui um repositório para armazenar as entidades JSON para suas alterações.
- O serviço de Data Factory não é otimizado para colaboração ou controle de versão.

> [!NOTE]
> A criação direta com o serviço de Data Factory está desabilitada no Azure Data Factory UX quando um repositório Git é configurado. As alterações podem ser feitas diretamente no serviço por meio do PowerShell ou de um SDK.

## <a name="author-with-azure-repos-git-integration"></a>Criar com Azure Repos integração com o Git

A criação visual com Azure Repos integração do git dá suporte ao controle do código-fonte e à colaboração para o trabalho em seus pipelines de data factory. Você pode associar um data factory a um repositório de organização git Azure Repos para controle do código-fonte, colaboração, versionamento e assim por diante. Uma única organização git Azure Repos pode ter vários repositórios, mas um repositório git Azure Repos pode ser associado a apenas um data factory. Se não tiver uma organização ou repositório Azure Repos, siga [estas instruções](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) para criar os seus recursos.

> [!NOTE]
> Você pode armazenar scripts e arquivos de dados em um repositório git Azure Repos. No entanto, você precisa carregar os arquivos manualmente no armazenamento do Azure. Um pipeline Data Factory não carrega automaticamente arquivos de script ou de dados armazenados em um repositório git Azure Repos no armazenamento do Azure.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Configurar um repositório git Azure Repos com Azure Data Factory

Você pode configurar um repositório git Azure Repos com um data factory por meio de dois métodos.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Método de configuração 1: Azure Data Factory home page

Na página inicial da Azure Data Factory, selecione Configurar o **Repositório**de Código .

![Configurar um repositório de código de Azure Repos](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Método de configuração 2: tela de criação de UX
Na tela de autoria da Azure Data Factory UX, selecione o menu de entrega da Fábrica de **Dados** e, em seguida, selecione Configurar o **Repositório**de Código .

![Definir as configurações do repositório de código para a criação de UX](media/author-visually/configure-repo-2.png)

Ambos os métodos abrem o painel de configuração configurações do repositório.

![Definir as configurações do repositório de código](media/author-visually/repo-settings.png)

O painel de configuração mostra as seguintes configurações de Azure Repos repositório de código:

| Definição | Descrição | Valor |
|:--- |:--- |:--- |
| **Tipo repositório** | O tipo do repositório de código de Azure Repos.<br/> | Azure DevOps git ou GitHub |
| **Azure Active Directory** | O nome do locatário do Azure AD. | `<your tenant name>` |
| **Organização Azure Repos** | O nome da organização Azure Repos. Pode localizar o nome da sua organização Azure Repos em `https://{organization name}.visualstudio.com`. Pode [inscrever-se na sua organização Azure Repos](https://www.visualstudio.com/team-services/git/) para aceder ao seu perfil de Estúdio Visual e ver os seus repositórios e projetos. | `<your organization name>` |
| **Nome do Projeto** | O nome do projeto de Azure Repos. Pode localizar o nome do seu projeto Azure Repos em `https://{organization name}.visualstudio.com/{project name}`. | `<your Azure Repos project name>` |
| **RepositórioNome** | O nome do repositório de código de Azure Repos. Azure Repos projetos contêm repositórios git para gerenciar seu código-fonte à medida que seu projeto cresce. Você pode criar um novo repositório ou usar um repositório existente que já esteja em seu projeto. | `<your Azure Repos code repository name>` |
| **Ramo de colaboração** | Sua Azure Repos Branch de colaboração que é usada para publicação. Por defeito, é `master`. Altere essa configuração caso você queira publicar recursos de outra ramificação. | `<your collaboration branch name>` |
| **Pasta raiz** | Sua pasta raiz na sua Azure Repos Branch de colaboração. | `<your root folder name>` |
| **Importar recursos da fábrica de dados existentes para repositório** | Especifica se importa os recursos de fábrica de dados existentes da tela de **autoria** UX para um repositório Azure Repos Git. Selecione a caixa para importar seus recursos de data factory para o repositório git associado no formato JSON. Essa ação exporta cada recurso individualmente (ou seja, os serviços vinculados e os conjuntos de valores são exportados para JSONs separados). Quando essa caixa não está selecionada, os recursos existentes não são importados. | Selecionado (padrão) |
| **Sucursal para importar recursos em** | Especifica em qual ramificação os recursos de data factory (pipelines, conjuntos de valores, serviços vinculados etc.) são importados. Você pode importar recursos para um dos seguintes branches: a. Colaboração b. Criar novo c. Usar existente |  |

> [!NOTE]
> Se você estiver usando o Microsoft Edge e não vir nenhum valor em seu menu suspenso da conta do Azure DevOps, adicione https://*. VisualStudio. com à lista de sites confiáveis.

### <a name="use-a-different-azure-active-directory-tenant"></a>Usar um locatário de Azure Active Directory diferente

Pode criar um repositório Git do Azure Repos num inquilino diferente do Azure Active Directory. Para especificar um locatário diferente do Azure AD, você precisa ter permissões de administrador para a assinatura do Azure que você está usando.

### <a name="use-your-personal-microsoft-account"></a>Usar seu conta Microsoft pessoal

Para usar um conta Microsoft pessoal para integração com o Git, você pode vincular seu repositório pessoal do Azure ao Active Directory da sua organização.

1. Adicione seus conta Microsoft pessoais ao Active Directory da sua organização como um convidado. Para mais informações, consulte [add Azure Ative Directory B2B de colaboração no portal Azure](../active-directory/b2b/add-users-administrator.md).

2. Faça logon no portal do Azure com sua conta Microsoft pessoal. Em seguida, alterne para o Active Directory da sua organização.

3. Vá para a seção DevOps do Azure, em que agora você vê seu repositório pessoal. Selecione o repositório e conecte-se com Active Directory.

Após essas etapas de configuração, seu repositório pessoal estará disponível quando você configurar a integração do git na interface do usuário do Data Factory.

Para obter mais informações sobre a ligação do Azure Repos ao Diretório Ativo da sua organização, consulte [Connect your Azure DevOps organization to Azure Ative Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="author-with-github-integration"></a>Criar com integração do GitHub

A criação visual com a integração do GitHub dá suporte ao controle do código-fonte e à colaboração para o trabalho em seus pipelines de data factory. Você pode associar um data factory a um repositório de conta do GitHub para controle do código-fonte, colaboração, versionamento. Uma única conta do GitHub pode ter vários repositórios, mas um repositório GitHub pode ser associado a apenas um data factory. Se não tiver uma conta GitHub ou repositório, siga [estas instruções](https://github.com/join) para criar os seus recursos.

A integração gitHub com data factory suporta tanto o GitHub público (isto é, [https://github.com) ](https://github.com)como a GitHub Enterprise. Você pode usar repositórios GitHub públicos e privados com Data Factory, desde que tenha permissão de leitura e gravação para o repositório no GitHub.

Para configurar um repositório GitHub, você deve ter permissões de administrador para a assinatura do Azure que você está usando.

Para uma introdução e uma demonstração desse recurso de nove minutos, Assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>Configurar um repositório GitHub com Azure Data Factory

Você pode configurar um repositório GitHub com um data factory por meio de dois métodos.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Método de configuração 1: Azure Data Factory home page

Na página inicial da Azure Data Factory, selecione Configurar o **Repositório**de Código .

![Configurar um repositório de código de Azure Repos](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Método de configuração 2: tela de criação de UX

Na tela de autoria da Azure Data Factory UX, selecione o menu de entrega da Fábrica de **Dados** e, em seguida, selecione Configurar o **Repositório**de Código .

![Definir as configurações do repositório de código para a criação de UX](media/author-visually/configure-repo-2.png)

Ambos os métodos abrem o painel de configuração configurações do repositório.

![Configurações do repositório GitHub](media/author-visually/github-integration-image2.png)

O painel de configuração mostra as seguintes configurações do repositório do GitHub:

| **Definição** | **Descrição**  | **Valor**  |
|:--- |:--- |:--- |
| **Tipo repositório** | O tipo do repositório de código de Azure Repos. | GitHub |
| **Use GitHub Enterprise** | Caixa de seleção para selecionar o GitHub empresarial | não selecionado (padrão) |
| **URL da Empresa GitHub** | A URL raiz da empresa do GitHub. Por exemplo: https://github.mydomain.com. Só é necessário se **a Utilização GitHub Enterprise** for selecionada | `<your GitHub enterprise url>` |                                                           
| **Conta GitHub** | O nome da sua conta do GitHub. Este nome pode ser encontrado a partir de https:\//github.com/{name da conta}/{repositório de nome}. Navegar até esta página solicita que você insira as credenciais do GitHub OAuth para sua conta do GitHub. | `<your GitHub account name>` |
| **Nome do repositório**  | O nome do repositório de código do GitHub. As contas do GitHub contêm repositórios git para gerenciar seu código-fonte. Você pode criar um novo repositório ou usar um repositório existente que já esteja em sua conta. | `<your repository name>` |
| **Ramo de colaboração** | Sua ramificação de colaboração do GitHub que é usada para publicação. Por padrão, é o mestre. Altere essa configuração caso você queira publicar recursos de outra ramificação. | `<your collaboration branch>` |
| **Pasta raiz** | Sua pasta raiz na sua ramificação de colaboração do GitHub. |`<your root folder name>` |
| **Importar recursos da fábrica de dados existentes para repositório** | Especifica se os recursos existentes do data factory devem ser importados da tela de criação do UX em um repositório do GitHub. Selecione a caixa para importar seus recursos de data factory para o repositório git associado no formato JSON. Essa ação exporta cada recurso individualmente (ou seja, os serviços vinculados e os conjuntos de valores são exportados para JSONs separados). Quando essa caixa não está selecionada, os recursos existentes não são importados. | Selecionado (padrão) |
| **Sucursal para importar recursos em** | Especifica em qual ramificação os recursos de data factory (pipelines, conjuntos de valores, serviços vinculados etc.) são importados. Você pode importar recursos para um dos seguintes branches: a. Colaboração b. Criar novo c. Usar existente |  |

### <a name="known-github-limitations"></a>Limitações conhecidas do GitHub

- Você pode armazenar scripts e arquivos de dados em um repositório GitHub. No entanto, você precisa carregar os arquivos manualmente no armazenamento do Azure. Um pipeline Data Factory não carrega automaticamente arquivos de script ou de dados armazenados em um repositório GitHub para o armazenamento do Azure.

- O GitHub Enterprise com uma versão anterior a 2.14.0 não funciona no navegador Microsoft Edge.

- A integração do GitHub com o Data Factory ferramentas de criação visual funciona apenas na versão disponível do Data Factory.

- Um máximo de 1.000 entidades por tipo de recurso (como pipelines e conjuntos de valores) pode ser buscado em uma única ramificação do GitHub. Se esse limite for atingido, o será sugerido para dividir seus recursos em fábricas separadas. A Azure DevOps Git não tem esta limitação.

## <a name="switch-to-a-different-git-repo"></a>Alternar para um repositório git diferente

Para mudar para um repo Git diferente, clique no ícone **Git Repo Definições** no canto superior direito da página de visão geral da Fábrica de Dados. Se você não conseguir ver o ícone, limpe o cache do navegador local. Selecione o ícone para remover a associação com o repositório atual.

![Ícone do git](media/author-visually/remove-repo.png)

Assim que aparecer o painel de definições de repositório, **selecione Remover Git**. Introduza o nome da sua fábrica de dados e clique **em confirmar** para remover o repositório Git associado à sua fábrica de dados.

![Remover a associação com o repositório git atual](media/author-visually/remove-repo2.png)

Depois de remover a associação com o repositório atual, você pode definir as configurações do git para usar um repositório diferente e, em seguida, importar recursos existentes do Data Factory para o novo repositório. 

## <a name="version-control"></a>Controlo de versões

Os sistemas de controlo de versão (também conhecidos como controlo de _fonte)_ permitem que os desenvolvedores colaborem em alterações de código e de rastreio que são feitas na base de código. O controle do código-fonte é uma ferramenta essencial para projetos de vários desenvolvedores.

### <a name="creating-feature-branches"></a>Criando ramificações de recurso

Cada repositório git Azure Repos que está associado a um data factory tem uma ramificação de colaboração. (`master` é o ramo de colaboração padrão). Os utilizadores também podem criar ramificações de funcionalidades clicando **+ Nova Filial** no dropdown do ramo. Depois que o painel novo Branch for exibido, insira o nome do Branch de recursos.

![Criar uma nova ramificação](media/author-visually/new-branch.png)

Quando estiver pronto para fundir as alterações do seu ramo de funcionalidade para o seu ramo de colaboração, clique no dropdown do ramo e selecione **Create pull request**. Essa ação leva você para Azure Repos git, em que você pode gerar solicitações de pull, fazer revisões de código e mesclar alterações em sua ramificação de colaboração. (`master` é o padrão). Você só tem permissão para publicar no serviço de Data Factory da sua ramificação de colaboração. 

![Criar uma nova solicitação pull](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Definir configurações de publicação

Para configurar o ramo de publicação - isto é, o ramo onde os modelos do Gestor de Recursos são guardados - adicione um ficheiro `publish_config.json` à pasta raiz no ramo de colaboração. Data Factory lê este ficheiro, procura o campo `publishBranch`, e cria um novo ramo (se já não existe) com o valor fornecido. Em seguida, ele salva todos os modelos do Resource Manager no local especificado. Por exemplo:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Quando você especifica uma nova ramificação de publicação, Data Factory não exclui o Branch de publicação anterior. Se você quiser remover o Branch de publicação anterior, exclua-o manualmente.

> [!NOTE]
> A Data Factory só lê o ficheiro `publish_config.json` quando carrega a fábrica. Se você já tiver a fábrica carregada no portal, atualize o navegador para fazer suas alterações entrarem em vigor.

### <a name="publish-code-changes"></a>Publicar alterações de código

Depois de ter fundido alterações no ramo de colaboração (`master` é o padrão), clique em **Publicar** manualmente as alterações de código no ramo principal do serviço Data Factory.

![Publicar alterações no serviço de Data Factory](media/author-visually/publish-changes.png)

Um painel lateral será aberto onde você confirmar que a ramificação de publicação e as alterações pendentes estão corretas. Assim que verificar as suas alterações, clique em **OK** para confirmar a publicação.

![Confirmar o Branch de publicação correto](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> O Branch mestre não é representativo do que é implantado no serviço de Data Factory. O ramo principal *deve* ser publicado manualmente no serviço Data Factory.

## <a name="advantages-of-git-integration"></a>Vantagens da integração do git

-   **Controlo de Origem**. À medida que suas cargas de trabalho de data factory se tornarem cruciais, você desejaria integrar sua fábrica com o Git para aproveitar vários benefícios de controle do código-fonte, como o seguinte:
    -   Capacidade de controlar/auditar alterações.
    -   Capacidade de reverter alterações que introduziram bugs.
-   **Salvamentos Parciais**. Ao fazer muitas alterações em sua fábrica, você perceberá que, no modo dinâmico normal, não poderá salvar suas alterações como rascunho, porque você não está pronto ou não deseja perder as alterações caso o computador falhe. Com a integração do git, você pode continuar salvando as alterações incrementalmente e publicar na fábrica somente quando estiver pronto. O Git age como um local de preparo para seu trabalho, até que você tenha testado suas alterações para sua satisfação.
-   **Colaboração e Controlo.** Se você tiver vários membros da equipe participando da mesma fábrica, talvez queira permitir que seus colegas colaborem entre si por meio de um processo de revisão de código. Você também pode configurar sua fábrica de forma que nem todos os colaboradores da fábrica tenham permissão para implantar na fábrica. Os integrantes da equipe podem ter apenas permissão para fazer alterações via git, mas apenas determinadas pessoas na equipe têm permissão para "publicar" as alterações na fábrica.
-   **Mostrando difusões.** No modo git, você consegue ver uma boa diferença da carga que está prestes a ser publicada na fábrica. Essa comparação mostra todos os recursos/entidades que foram modificados/adicionados/excluídos desde a última vez que você publicou em sua fábrica. Com base nessa diferença, você pode continuar com a publicação ou voltar e verificar as alterações e, em seguida, voltar mais tarde.
-   **Melhor CI/CD**. Se você estiver usando o modo git, poderá configurar seu pipeline de liberação para disparar automaticamente assim que houver qualquer alteração feita na fábrica de desenvolvimento. Você também tem de personalizar as propriedades em sua fábrica que estão disponíveis como parâmetros no modelo do Resource Manager. Pode ser útil manter apenas o conjunto necessário de propriedades como parâmetros e ter tudo o que está embutido em código.
-   **Melhor Desempenho.** Uma fábrica média carrega dez vezes mais rápido no modo git do que no modo dinâmico normal, pois os recursos são baixados por meio do git.

## <a name="best-practices-for-git-integration"></a>Práticas recomendadas para a integração com o Git

### <a name="permissions"></a>Permissões

Normalmente, você não quer que todos os membros da equipe tenham permissões para atualizar a fábrica. As seguintes configurações de permissões são recomendadas:

*   Todos os membros da equipe devem ter permissões de leitura para o data factory.
*   Somente um conjunto selecionado de pessoas deve ter permissão para publicar na fábrica. Para isso, devem ter o papel de **colaborador da Fábrica** de Dados na fábrica. Para obter mais informações sobre permissões, consulte [Papéis e permissões para a Azure Data Factory](concepts-roles-permissions.md).
   
É recomendável não permitir check-ins diretos para a ramificação de colaboração. Esta restrição pode ajudar a prevenir bugs, uma vez que cada check-in passará por um processo de revisão de pedidos de retirada descrito na [Criação](source-control.md#creating-feature-branches)de ramos de recurso .

### <a name="using-passwords-from-azure-key-vault"></a>Usando senhas de Azure Key Vault

É recomendável usar Azure Key Vault para armazenar quaisquer cadeias de conexão ou senhas para Data Factory serviços vinculados. Por motivos de segurança, não armazenamos nenhuma informação secreta no git, portanto, quaisquer alterações nos serviços vinculados são publicadas imediatamente no serviço de Azure Data Factory.

Usar Key Vault também facilita a integração e a implantação contínua, pois você não precisará fornecer esses segredos durante a implantação do modelo do Resource Manager.

## <a name="troubleshooting-git-integration"></a>Solução de problemas de integração do git

### <a name="stale-publish-branch"></a>Branch de publicação obsoleto

Se a ramificação de publicação estiver fora de sincronia com a ramificação mestre e contiver recursos desatualizados, independentemente de uma publicação recente, tente seguir estas etapas:

1. Remover seu repositório git atual
1. Reconfigure git com as mesmas configurações, mas certifique-se de que importar recursos da Fábrica de **Dados existentes para repositório** é selecionado e escolha **Nova sucursal**
1. Criar uma solicitação de pull para mesclar as alterações para a ramificação de colaboração 

Abaixo estão alguns exemplos de situações que podem causar uma sucursal de publicação estagnada:
- Um utilizador tem vários ramos. Numa sucursal de recurso, eliminaram um serviço ligado que não está associado ao AKV (os serviços não ligados ao AKV são publicados imediatamente independentemente de estarem em Git ou não) e nunca fundiram a função na brnach de colaboração.
- Um utilizador modificou a fábrica de dados utilizando o SDK ou powerShell
- Um utilizador transferiu todos os recursos para uma nova sucursal e tentou publicar pela primeira vez. Os serviços ligados devem ser criados manualmente na importação de recursos.
- Um utilizador envia manualmente um serviço não LIGADO AKV ou um JSON de Funcionamento de Integração. Referem esse recurso a partir de outro recurso, como um conjunto de dados, um serviço ligado ou um pipeline. Um serviço não AKV ligado criado através do UX é publicado imediatamente porque as credenciais precisam de ser encriptadas. Se fizer o upload de um conjunto de dados referindo esse serviço ligado e tentar publicar, o UX permitirá porque existe no ambiente git. Será rejeitado no momento da publicação, uma vez que não existe no serviço de fábrica de dados.

## <a name="provide-feedback"></a>Enviar comentários
Selecione **Feedback** para comentar sobre funcionalidades ou para notificar a Microsoft sobre problemas com a ferramenta:

![Comentários](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre monitorização e gestão de gasodutos, consulte [monitor e gerenciar os gasodutos programáticamente](monitor-programmatically.md).
* Para implementar a integração e implantação contínuas, consulte a [integração e entrega contínuas (CI/CD) na Fábrica de Dados Azure.](continuous-integration-deployment.md)
