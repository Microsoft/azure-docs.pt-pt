---
title: Controlo de código fonte
description: Saiba como configurar o controlo de origem na Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: dcstwh
ms.author: weetok
manager: anandsub
ms.reviewer: ''
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/02/2020
ms.openlocfilehash: 27f14834e45dd32bdf3a26d22d38ab250aa1bcbe
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127741"
---
# <a name="source-control-in-azure-data-factory"></a>Controlo de fontes na Azure Data Factory
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Por padrão, a experiência de interface de utilizador da Azure Data Factory (UX) é diretamente contra o serviço de fábrica de dados. Esta experiência tem as seguintes limitações:

- O serviço Data Factory não inclui um repositório para armazenar as entidades JSON para as suas alterações. A única forma de guardar alterações é através do botão **Publicar Tudo** e todas as alterações são publicadas diretamente no serviço de fábrica de dados.
- O serviço Data Factory não está otimizado para o controlo de colaboração e versão.
- O modelo do Gestor de Recursos Azure necessário para implementar a própria Data Factory não está incluído.

Para proporcionar uma melhor experiência de autoria, a Azure Data Factory permite-lhe configurar um repositório git com a Azure Repos ou GitHub. Git é um sistema de controlo de versão que permite um rastreio e colaboração de mudanças mais fáceis. Este artigo irá delinear como configurar e trabalhar num repositório de git, juntamente com realçar as melhores práticas e um guia de resolução de problemas.

> [!NOTE]
> A integração do gitHub enterprise na Azure Data Factory só está disponível para a GitHub Enterprise na Azure Government Cloud.

Para saber mais sobre como a Azure Data Factory se integra com o Git, veja o vídeo tutorial de 15 minutos abaixo:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4GNKv]

## <a name="advantages-of-git-integration"></a>Vantagens da integração no Git

Abaixo está uma lista de algumas das vantagens que a integração git proporciona à experiência de autoria:

-   **Controlo de origem:** À medida que as cargas de trabalho da sua fábrica de dados se tornam cruciais, gostaria de integrar a sua fábrica com a Git para aproveitar vários benefícios de controlo de origem, como os seguintes:
    -   Capacidade de rastrear/auditar alterações.
    -   Capacidade de reverter alterações que introduziram bugs.
-   **Salvamentos parciais:** Ao autorizar o serviço de fábrica de dados, não é possível guardar alterações como rascunho e todas as publicações devem passar a validação da fábrica de dados. Se os seus oleodutos não estão terminados ou simplesmente não quer perder alterações se o seu computador falhar, a integração de git permite mudanças incrementais dos recursos da fábrica de dados, independentemente do estado em que se encontram. Configurar um repositório de git permite-lhe guardar alterações, permitindo-lhe apenas publicar quando tiver testado as suas alterações a seu contento.
-   **Colaboração e controlo:** Se tiver vários membros da equipa a contribuir para a mesma fábrica, talvez queira deixar que os seus colegas colaborem entre si através de um processo de revisão de código. Também pode configurar a sua fábrica de modo a que nem todos os colaboradores têm permissões iguais. Alguns membros da equipa só podem ser autorizados a fazer alterações via Git e apenas algumas pessoas na equipa estão autorizadas a publicar as alterações na fábrica.
-   **Melhor CI/CD:**  Se estiver a implementar em vários ambientes com um [processo de entrega contínua,](continuous-integration-deployment.md)a integração de git facilita certas ações. Algumas destas ações incluem:
    -   Configure o seu oleoduto de desbloqueio para ativar automaticamente assim que houver alterações na sua fábrica de 'dev'.
    -   Personalize as propriedades na sua fábrica que estão disponíveis como parâmetros no modelo de Gestor de Recursos. Pode ser útil manter apenas o conjunto de propriedades necessário como parâmetros, e ter tudo o resto codificado.
-   **Melhor desempenho:** Uma fábrica média com integração de git carrega 10 vezes mais rápido do que uma autoria contra o serviço de fábrica de dados. Esta melhoria de desempenho é porque os recursos são descarregados via Git.

> [!NOTE]
> A autoria direta com o serviço Data Factory é desativada no Azure Data Factory UX quando um repositório de Git é configurado. As alterações efetuadas através do PowerShell ou de um SDK são publicadas diretamente no serviço Data Factory e não são introduzidas no Git.

## <a name="connect-to-a-git-repository"></a>Conecte-se a um repositório de Git

Existem quatro maneiras diferentes de ligar um repositório git à sua fábrica de dados tanto para a Azure Repos como para o GitHub. Depois de ligar a um repositório Git, pode visualizar e gerir a sua configuração no [centro de gestão](author-management-hub.md) sob **a configuração git** na secção de controlo **Source**

### <a name="configuration-method-1-home-page"></a>Método de configuração 1: Página inicial

Na página inicial da Fábrica de Dados Azure, selecione **Configurar o Repositório de Código**.

![Configurar um repositório de código a partir da página inicial](media/author-visually/configure-repo.png)

### <a name="configuration-method-2-authoring-canvas"></a>Método de configuração 2: Tela de autoria

Na tela de autoria Azure Data Factory UX, selecione o menu de entrega da **Data Factory** e, em seguida, selecione **Configurar o Repositório de Código**.

![Configurar as definições de repositório de código da autoria](media/author-visually/configure-repo-2.png)

### <a name="configuration-method-3-management-hub"></a>Método de configuração 3: Centro de gestão

Vá ao centro de gestão no ADF UX. Selecione **a configuração de Git** na secção de controlo **'Fonte'.** Se não tiver nenhum repositório ligado, clique em **Configurar o repositório de código**.

![Configurar as definições de repositório de código do centro de gestão](media/author-visually/configure-repo-3.png)

### <a name="configuration-method-4-during-factory-creation"></a>Método de configuração 4: Durante a criação de fábrica

Ao criar uma nova fábrica de dados no portal Azure, pode configurar informações de repositório de Git no separador **de configuração Git.**

> [!NOTE]
> Ao configurar o git no Portal Azure, as configurações como o nome do projeto e o nome do repo têm de ser inseridas manualmente em vez de fazerem parte de um dropdown.

![Configurar as definições de repositório de código do Portal Azure](media/author-visually/configure-repo-4.png)

## <a name="author-with-azure-repos-git-integration"></a>Criar com a integração em Repositórios do Azure

A autoria visual com a integração Azure Repos Git suporta o controlo de fontes e a colaboração para o trabalho nos seus oleodutos de fábrica de dados. Você pode associar uma fábrica de dados a um repositório de organização Azure Repos Git para controlo de origem, colaboração, versão, e assim por diante. Uma única organização Azure Repos Git pode ter vários repositórios, mas um repositório Azure Repos Git pode ser associado a apenas uma fábrica de dados. Se não tiver uma organização ou repositório do Azure Repos, siga [estas instruções](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) para criar os seus recursos.

> [!NOTE]
> Pode armazenar scripts e ficheiros de dados num repositório Azure Repos Git. No entanto, tem de fazer o upload manual dos ficheiros para o Azure Storage. Um oleoduto de fábrica de dados não carrega automaticamente scripts ou ficheiros de dados armazenados num repositório Azure Repos Git para o Azure Storage.

### <a name="azure-repos-settings"></a>Configurações de Azure Repos

![Configurar as definições de repositório de código](media/author-visually/repo-settings.png)

O painel de configuração mostra as seguintes definições de repositório de código Azure Repos:

| Definições | Descrição | Valor |
|:--- |:--- |:--- |
| **Tipo repositório** | O tipo de repositório de código Azure Repos.<br/> | Azure DevOps Git ou GitHub |
| **Azure Active Directory** | Seu nome de inquilino da AZure AD. | `<your tenant name>` |
| **Organização Azure Repos** | O nome da organização do Azure Repos. Pode localizar o nome da organização Azure Repos em `https://{organization name}.visualstudio.com` . Pode [iniciar sessão na sua organização Azure Repos](https://www.visualstudio.com/team-services/git/) para aceder ao seu perfil de Estúdio Visual e ver os seus repositórios e projetos. | `<your organization name>` |
| **Nome do Projeto** | O nome do seu projeto Azure Repos. Pode localizar o nome do seu projeto Azure Repos em `https://{organization name}.visualstudio.com/{project name}` . | `<your Azure Repos project name>` |
| **Nome do Repositório** | O seu nome de repositório de código Azure Repos. Os projetos Azure Repos contêm repositórios de Git para gerir o seu código fonte à medida que o seu projeto cresce. Você pode criar um novo repositório ou usar um repositório existente que já está no seu projeto. | `<your Azure Repos code repository name>` |
| **Ramo de colaboração** | O seu ramo de colaboração Azure Repos que é usado para a publicação. Por defeito, `main` é. Altere esta definição no caso de pretender publicar recursos a partir de outra sucursal. | `<your collaboration branch name>` |
| **Pasta raiz** | A sua pasta de raiz no seu ramo de colaboração Azure Repos. | `<your root folder name>` |
| **Importar os recursos existentes da Data Factory para repositório** | Especifica se importa os recursos existentes da fábrica de dados da tela de **autoria** UX para um repositório Azure Repos Git. Selecione a caixa para importar os seus recursos de fábrica de dados para o repositório de Git associado no formato JSON. Esta ação exporta cada recurso individualmente (isto é, os serviços e conjuntos de dados ligados são exportados para JSONs separados). Quando esta caixa não é selecionada, os recursos existentes não são importados. | Selecionado (predefinido) |
| **Sucursal para importar recurso em** | Especifica em que ramo são importados os recursos da fábrica de dados (oleodutos, conjuntos de dados, serviços ligados, etc.). Pode importar recursos para um dos seguintes ramos: a. Colaboração b. Criar novo c. Utilizar a existência |  |

> [!NOTE]
> Se estiver a utilizar o Microsoft Edge e não vir quaisquer valores na sua conta Azure DevOps, adicione https://*.visualstudio.com à lista de sites fidedignos.

### <a name="use-a-different-azure-active-directory-tenant"></a>Use um inquilino azure ative inquilina diferente

O Azure Repos Git repo pode estar em um inquilino azure ative diretório diferente. Para especificar um inquilino do Azure AD diferente, tem de ter permissões de administrador para a subscrição do Azure que está a utilizar. Para mais informações, consulte [o administrador de subscrição de alteração](../cost-management-billing/manage/add-change-subscription-administrator.md#to-assign-a-user-as-an-administrator)

> [!IMPORTANT]
> Para se ligar a outro Diretório Ativo Azure, o utilizador que fez login deve fazer parte desse diretório ativo. 

### <a name="use-your-personal-microsoft-account"></a>Utilize a sua conta pessoal da Microsoft

Para utilizar uma conta pessoal da Microsoft para a integração do Git, pode ligar o seu Azure Repo pessoal ao Ative Directory da sua organização.

1. Adicione a sua conta pessoal da Microsoft ao Ative Directory da sua organização como hóspede. Para mais informações, consulte [os utilizadores de colaboração do Add Azure Ative Directory B2B no portal Azure](../active-directory/external-identities/add-users-administrator.md).

2. Faça login no portal Azure com a sua conta pessoal da Microsoft. Em seguida, mude para o Diretório Ativo da sua organização.

3. Vá à secção Azure DevOps, onde agora vê o seu repo pessoal. Selecione o repo e conecte-se com o Ative Directory.

Após estes passos de configuração, o seu repo pessoal está disponível quando configurar a integração git na UI da Fábrica de Dados.

Para obter mais informações sobre a ligação do Azure Repos ao Ative Directory da sua organização, consulte [a sua organização Azure DevOps ao Azure Ative Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="author-with-github-integration"></a>Criar com a integração no GitHub

A autoria visual com a integração do GitHub suporta o controlo de fontes e a colaboração para o trabalho nos seus oleodutos de fábrica de dados. Pode associar uma fábrica de dados a um repositório de conta GitHub para controlo de origem, colaboração, versão. Uma única conta GitHub pode ter vários repositórios, mas um repositório GitHub pode ser associado a apenas uma fábrica de dados. Se não tiver uma conta GitHub ou repositório, siga [estas instruções](https://github.com/join) para criar os seus recursos.

A integração do GitHub com a Data Factory suporta tanto o GitHub público (isto [https://github.com](https://github.com) é,) como a GitHub Enterprise. Você pode usar repositórios públicos e privados gitHub com data factory desde que tenha lido e escrito permissão para o repositório em GitHub.

Para configurar um repo GitHub, tem de ter permissões de administrador para a subscrição do Azure que está a utilizar.

### <a name="github-settings"></a>Configurações do GitHub

![Configurações do repositório gitHub](media/author-visually/github-integration-image2.png)

O painel de configuração mostra as seguintes definições de repositório GitHub:

| **Definição** | **Descrição**  | **Valor**  |
|:--- |:--- |:--- |
| **Tipo repositório** | O tipo de repositório de código Azure Repos. | GitHub |
| **Use GitHub Enterprise** | Caixa de verificação para selecionar GitHub Enterprise | não selecionado (padrão) |
| **URL da Empresa GitHub** | O URL raiz gitHub Enterprise (deve ser HTTPS para o servidor local gitHub Enterprise). Por exemplo: `https://github.mydomain.com`. Só é necessário se **a Utilização da GitHub Enterprise** for selecionada | `<your GitHub enterprise url>` |                                                           
| **Conta GitHub** | O nome da sua conta GitHub. Este nome pode ser encontrado em https: \/ /github.com/{account name}/{repositório}. Navegar nesta página leva-o a introduzir credenciais GitHub OAuth na sua conta GitHub. | `<your GitHub account name>` |
| **Nome do Repositório**  | O teu nome de repositório de código GitHub. As contas do GitHub contêm repositórios de Git para gerir o seu código fonte. Pode criar um novo repositório ou usar um repositório existente que já está na sua conta. | `<your repository name>` |
| **Ramo de colaboração** | O seu ramo de colaboração GitHub que é usado para a publicação. Por defeito, é principal. Altere esta definição no caso de pretender publicar recursos a partir de outra sucursal. | `<your collaboration branch>` |
| **Pasta raiz** | A sua pasta de raiz no seu ramo de colaboração GitHub. |`<your root folder name>` |
| **Importar os recursos existentes da Data Factory para repositório** | Especifica se importa os recursos existentes da fábrica de dados da tela de autoria UX para um repositório gitHub. Selecione a caixa para importar os seus recursos de fábrica de dados para o repositório de Git associado no formato JSON. Esta ação exporta cada recurso individualmente (isto é, os serviços e conjuntos de dados ligados são exportados para JSONs separados). Quando esta caixa não é selecionada, os recursos existentes não são importados. | Selecionado (predefinido) |
| **Sucursal para importar recurso em** | Especifica em que ramo são importados os recursos da fábrica de dados (oleodutos, conjuntos de dados, serviços ligados, etc.). Pode importar recursos para um dos seguintes ramos: a. Colaboração b. Criar novo c. Utilizar a existência |  |

### <a name="github-organizations"></a>Organizações GitHub

A ligação a uma organização do GitHub requer que a organização conceda permissão à Azure Data Factory. Um utilizador com permissões ADMIN na organização deve executar os passos abaixo para permitir a ligação da fábrica de dados.

#### <a name="connecting-to-github-for-the-first-time-in-azure-data-factory"></a>Ligação ao GitHub pela primeira vez na Azure Data Factory

Se estiver a ligar-se ao GitHub da Azure Data Factory pela primeira vez, siga estes passos para se ligar a uma organização do GitHub.

1. No painel de configuração Git, insira o nome da organização no campo *Conta GitHub.* Aparecerá um pedido para iniciar sessão no GitHub. 
1. Faça login usando as suas credenciais de utilizador.
1. Será solicitado que autorize a Azure Data Factory como uma aplicação chamada *AzureDataFactory*. Neste ecrã, você verá uma opção para conceder permissão para a ADF aceder à organização. Se não vir a opção de conceder permissão, peça a um administrador que conceda manualmente a permissão através do GitHub.

Assim que seguir estes passos, a sua fábrica poderá ligar-se a repositórios públicos e privados dentro da sua organização. Se não conseguir ligar, tente limpar a cache do navegador e voltar a tentar.

#### <a name="already-connected-to-github-using-a-personal-account"></a>Já ligado ao GitHub usando uma conta pessoal

Se já ligou ao GitHub e só autorizou o acesso a uma conta pessoal, siga os passos abaixo para conceder permissões a uma organização. 

1. Vá ao GitHub e abra **as definições**.

    ![Abra as definições do GitHub](media/author-visually/github-settings.png)

1. Selecione **Aplicações**. No separador **de aplicações OAuth Autorizado,** deverá ver *a AzureDataFactory*.

    ![Selecione aplicativos OAuth](media/author-visually/github-organization-select-application.png)

1. Selecione a candidatura e conceda o acesso à sua organização.

    ![Conceder acesso](media/author-visually/github-organization-grant.png)

Assim que seguir estes passos, a sua fábrica poderá ligar-se a repositórios públicos e privados dentro da sua organização. 

### <a name="known-github-limitations"></a>Limitações conhecidas do GitHub

- Pode armazenar scripts e ficheiros de dados num repositório GitHub. No entanto, tem de fazer o upload manual dos ficheiros para o Azure Storage. Um gasoduto Data Factory não faz o upload automaticamente de scripts ou ficheiros de dados armazenados num repositório GitHub para o Azure Storage.

- GitHub Enterprise com uma versão com mais de 2.14.0 não funciona no navegador Microsoft Edge.

- A integração do GitHub com as ferramentas de autoria visual da Data Factory só funciona na versão geralmente disponível da Data Factory.


- Um máximo de 1.000 entidades por tipo de recurso (como oleodutos e conjuntos de dados) pode ser recolhido a partir de uma única filial do GitHub. Se este limite for atingido, sugere-se que divida os seus recursos em fábricas separadas. O Git do Azure DevOps não tem essa limitação.

## <a name="version-control"></a>Controlo de versões

Os sistemas de controlo de versão (também conhecidos como _controlo de fontes)_ permitem que os desenvolvedores colaborem em códigos e alterações de rastreio que são feitas na base de código. O controlo de fontes é uma ferramenta essencial para projetos multi-desenvolvedores.

### <a name="creating-feature-branches"></a>Criação de ramos de recursos

Cada repositório Azure Repos Git que está associado a uma fábrica de dados tem um ramo de colaboração. () `main` é o ramo de colaboração padrão). Os utilizadores também podem criar ramos de funcionalidade clicando **+ Novo Ramo** no dropdown do ramo. Assim que aparecer o novo painel de ramificação, insira o nome do seu ramo de recurso.

![Criar um novo ramo](media/author-visually/new-branch.png)

Quando estiver pronto para fundir as alterações do seu ramo de recurso para o seu ramo de colaboração, clique no dropdown do ramo e selecione **Criar pedido de puxar**. Esta ação leva-o ao Azure Repos Git onde pode levantar pedidos de puxar, fazer revisões de código e fundir alterações no seu ramo de colaboração. (é `main` o padrão). Só pode publicar no serviço data factory a partir da sua filial de colaboração. 

![Criar um novo pedido de puxar](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Configurar configurações de publicação

Por predefinição, a fábrica de dados gera os modelos de Gestor de Recursos da fábrica publicada e guarda-os numa sucursal chamada `adf_publish` . Para configurar um ramo de publicação personalizado, adicione um `publish_config.json` ficheiro à pasta raiz no ramo de colaboração. Ao publicar, a ADF lê este ficheiro, procura o campo `publishBranch` e guarda todos os modelos do Gestor de Recursos para o local especificado. Se o ramo não existir, a fábrica de dados irá automaticamente criá-lo. E exemplo do que este ficheiro parece estar abaixo:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

A Azure Data Factory só pode ter uma filial de cada vez. Quando especifica uma nova agência de publicação, a Data Factory não apaga a sucursal de publicação anterior. Se pretender remover o ramo de publicação anterior, elimine-o manualmente.

> [!NOTE]
> A Data Factory só lê o `publish_config.json` ficheiro quando carrega a fábrica. Se já tem a fábrica carregada no portal, refresque o navegador para que as suas alterações façam efeito.

### <a name="publish-code-changes"></a>Publicar alterações de código

Depois de ter fundido alterações no ramo de colaboração `main` (é o padrão), clique em **Publicar** para publicar manualmente as alterações de código no ramo principal para o serviço Data Factory.

![Publicar alterações no serviço Data Factory](media/author-visually/publish-changes.png)

Um painel lateral abrir-se-á onde confirma que a sucursal de publicação e as alterações pendentes estão corretas. Assim que verificar as alterações, clique **em OK** para confirmar a publicação.

![Confirme o ramo de publicação correto](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> O ramo principal não é representativo do que está implantado no serviço data factory. O ramo principal *deve* ser publicado manualmente ao serviço data factory.

## <a name="best-practices-for-git-integration"></a>Melhores práticas para integração de Git

### <a name="permissions"></a>Permissões

Normalmente, não quer que todos os membros da equipa tenham permissões para atualizar a Data Factory. Recomenda-se as seguintes permissões:

*   Todos os membros da equipa deviam ter lido permissões para a Fábrica de Dados.
*   Apenas um conjunto selecionado de pessoas deve ser autorizado a publicar na Fábrica de Dados. Para tal, devem ter o **papel de contribuinte** da Data Factory no Grupo de **Recursos** que contém a Fábrica de Dados. Para obter mais informações sobre permissões, consulte [Funções e permissões para Azure Data Factory](concepts-roles-permissions.md).

Recomenda-se não permitir check-ins diretos no ramo da colaboração. Esta restrição pode ajudar a prevenir bugs, uma vez que cada check-in passará por um processo de revisão de pedidos de pull descrito na [Criação de ramos de funcionalidades.](source-control.md#creating-feature-branches)

### <a name="using-passwords-from-azure-key-vault"></a>Usando senhas do Cofre de Chaves Azure

Recomenda-se a utilização do Cofre de Chaves Azure para armazenar quaisquer fios de ligação ou palavras-passe ou autenticação de identidade gerida para serviços ligados à data factory. Por razões de segurança, a fábrica de dados não armazena segredos em Git. Quaisquer alterações aos Serviços Ligados que contenham segredos, como palavras-passe, são imediatamente publicadas no serviço Azure Data Factory.

A utilização da autenticação Key Vault ou MSI também facilita a integração e implementação contínuas, uma vez que não terá de fornecer estes segredos durante a implementação do modelo do Gestor de Recursos.

## <a name="troubleshooting-git-integration"></a>Resolver problemas de integração no Git

### <a name="stale-publish-branch"></a>Filial de publicação de Stale

Se a sucursal de publicação estiver dessincronizada com o ramo principal e contiver recursos desatualizados apesar de uma publicação recente, tente seguir estes passos:

1. Remova o seu repositório git atual
1. Reconfigure Git com as mesmas definições, mas certifique-se de que **importa os recursos existentes da Data Factory para repositório** é selecionado e escolha **Novo ramo**
1. Criar um pedido Pull para unir as alterações no ramo de colaboração 

Abaixo estão alguns exemplos de situações que podem causar uma sucursal de publicação velha:
- Um utilizador tem vários ramos. Numa sucursal de recurso, eliminaram um serviço ligado que não está associado à AKV (os serviços ligados não-AKV são publicados imediatamente independentemente de estarem em Git ou não) e nunca fundiram o ramo de recurso no ramo de colaboração.
- Um utilizador modificou a fábrica de dados utilizando o SDK ou o PowerShell
- Um utilizador mudou todos os recursos para uma nova sucursal e tentou publicar pela primeira vez. Os serviços ligados devem ser criados manualmente na importação de recursos.
- Um utilizador envia manualmente um serviço não ligado a AKV ou um JSON de execução de integração. Referem esse recurso a partir de outro recurso, como um conjunto de dados, um serviço ligado ou um pipeline. Um serviço não ligado a AKV criado através do UX é publicado imediatamente porque as credenciais precisam de ser encriptadas. Se carregar um conjunto de dados referindo esse serviço ligado e tentar publicar, o UX permitirá porque existe no ambiente de git. Será rejeitado no momento da publicação, uma vez que não existe no serviço de fábrica de dados.

## <a name="switch-to-a-different-git-repository"></a>Mude para um repositório de Git diferente

Para mudar para um repositório de Git diferente, aceda à página de configuração git no centro de gestão sob **controlo source**. **Selecione Desligar**. 

![Ícone de Git](media/author-visually/remove-repository.png)

Insira o nome da sua fábrica de dados e clique em **confirmar** para remover o repositório Git associado à sua fábrica de dados.

![Remova a associação com o atual git repo](media/author-visually/remove-repository-2.png)

Depois de remover a associação com o repo atual, pode configurar as suas definições de Git para usar um repo diferente e, em seguida, importar os recursos existentes da Data Factory para o novo repo.

> [!IMPORTANT]
> Remover a configuração do Git de uma fábrica de dados não apaga nada do repositório. A fábrica conterá todos os recursos publicados. Pode continuar a editar a fábrica diretamente contra o serviço.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre monitorização e gestão de gasodutos, consulte [o Monitor e gere os gasodutos programáticamente.](monitor-programmatically.md)
* Para implementar a integração e implantação contínuas, consulte [integração e entrega contínua (CI/CD) na Azure Data Factory](continuous-integration-deployment.md).
