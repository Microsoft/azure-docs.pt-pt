---
title: Criação da fábrica de dados do Azure | Documentos da Microsoft
description: Saiba como utilizar a criação da fábrica de dados do Azure
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: shlo
ms.openlocfilehash: c4e71f3c355f00bda3df6d43bc81743b391ea0dd
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56871966"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Criação no Azure Data Factory
O Azure Data Factory utilizador interface experiência (UX) permite criar e implementar recursos da sua fábrica de dados sem ter de escrever qualquer código visualmente. Pode arrastar atividades para uma tela de pipeline, realizar execuções de testes, depurar iterativamente e implementar e monitorizar as execuções de pipeline. Existem duas abordagens para utilizar a experiência do Usuário para executar a criação visual:

- Autor diretamente com o serviço Data Factory.
- Autor com a integração de Git de repositórios do Azure para colaboração, controlo de origem e controle de versão.

## <a name="author-directly-with-the-data-factory-service"></a>Autor diretamente com o serviço Data Factory
Criação de Visual com o serviço Data Factory é diferente da criação de visual com integração de Git de duas formas:

- O serviço Data Factory não inclui um repositório para armazenar as entidades JSON para as suas alterações.
- O serviço Data Factory não está otimizado para colaboração ou controle de versão.

![Configurar o serviço Data Factory](media/author-visually/configure-data-factory.png)

Ao utilizar a experiência do Usuário **tela de criação** para criar diretamente com o serviço Data Factory, apenas o **publicar tudo** modo está disponível. Quaisquer alterações que fizer são publicadas diretamente para o serviço Data Factory.

![Modo de publicação](media/author-visually/data-factory-publish.png)

## <a name="author-with-azure-repos-git-integration"></a>Autor com a integração de Git de repositórios do Azure
Criação visual com a integração de Git de repositórios do Azure suporta o controlo de código fonte e colaboração for work nos seus pipelines da fábrica de dados. Pode associar uma fábrica de dados um repositório da organização do Azure repositórios Git para controlo de origem, colaboração, controlo de versões e assim por diante. Uma única organização de Git de repositórios do Azure pode ter vários repositórios, mas um repositório de Git de repositórios do Azure pode ser associado com apenas uma fábrica de dados. Se não tiver uma organização de repositórios do Azure ou o repositório, siga [estas instruções](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) para criar os seus recursos.

> [!NOTE]
> Pode armazenar ficheiros de dados e de script num repositório de Git de repositórios do Azure. No entanto, terá de carregar os ficheiros manualmente para o armazenamento do Azure. Um pipeline do Data Factory não automaticamente carregar arquivos de script ou de dados armazenados num repositório Git de repositórios do Azure para o armazenamento do Azure.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Configurar um repositório de Git de repositórios do Azure com o Azure Data Factory
Pode configurar um repositório de Git de repositórios do Azure com uma fábrica de dados por meio de dois métodos.

#### <a name="method1"></a> Método de configuração 1 (repositório de Git de repositórios do Azure): Página Vamos começar

No Azure Data Factory, vá para o **Vamos começar** página. Selecione **configurar o repositório de código**:

![Configurar um repositório de código de repositórios do Azure](media/author-visually/configure-repo.png)

O **definições do repositório** é apresentado o painel de configuração:

![Configurar as definições de repositório de código](media/author-visually/repo-settings.png)

O painel mostra o seguinte código de repositórios do Azure, as definições de repositório:

| Definição | Descrição | Value |
|:--- |:--- |:--- |
| **Tipo de repositório** | O tipo de repositório de código de repositórios do Azure.<br/>**Nota**: GitHub não é atualmente suportado. | Azure repositórios Git |
| **Azure Active Directory** | Nome do seu inquilino do Azure AD. | <your tenant name> |
| **Organização de repositórios do Azure** | O nome de organização de repositórios do Azure. Pode localizar o nome de organização de repositórios do Azure em `https://{organization name}.visualstudio.com`. Pode [iniciar sessão na sua organização de repositórios de Azure](https://www.visualstudio.com/team-services/git/) para aceder ao seu perfil do Visual Studio e verá os repositórios e os projetos. | <your organization name> |
| **ProjectName** | O nome do projeto de Repos do Azure. Pode localizar o nome do projeto de Repos de Azure no `https://{organization name}.visualstudio.com/{project name}`. | <your Azure Repos project name> |
| **RepositoryName** | O nome de repositório de código de repositórios do Azure. Os projetos de repositórios do Azure contêm os repositórios de Git para gerir o seu código-fonte à medida que aumenta a seu projeto. Pode criar um novo repositório ou utilizar um repositório existente que já se encontra no seu projeto. | <your Azure Repos code repository name> |
| **Ramo de colaboração** | O ramo de colaboração de repositórios do Azure que serve para publicação. Por predefinição, é `master`. Altere esta definição caso queira publicar recursos a partir de outro ramo. | <your collaboration branch name> |
| **Pasta raiz** | A pasta de raiz no seu ramo de colaboração de repositórios do Azure. | <your root folder name> |
| **Importar recursos do Data Factory existentes para o repositório** | Especifica se pretende importar recursos de fábrica de dados existentes de UX **tela de criação** para um repositório de Git de repositórios do Azure. Selecione a caixa para importar os seus recursos de fábrica de dados para o repositório de Git associado no formato JSON. Esta ação exporta cada recurso individualmente (ou seja, os serviços ligados e conjuntos de dados são exportados para o JSONs separados). Quando esta caixa não está selecionada, os recursos existentes não são importados. | Selecionado (predefinição) |

#### <a name="configuration-method-2-azure-repos-git-repo-ux-authoring-canvas"></a>Método de configuração 2 (repositório de Git de repositórios do Azure): Experiência do Usuário baseada em telas de criação
Na UX de fábrica de dados do Azure **tela de criação**, localize a fábrica de dados. Selecione o **Data Factory** menu pendente e, em seguida, selecione **configurar repositório de código**.

É apresentado um painel de configuração. Para obter detalhes sobre as definições de configuração, consulte as descrições <a href="#method1">método de configuração 1</a>.

![Configurar as definições de repositório de código para a criação da experiência do Usuário](media/author-visually/configure-repo-2.png)

### <a name="use-a-different-azure-active-directory-tenant"></a>Utilizar um inquilino diferente do Azure Active Directory

Pode criar um repositório de Git de repositórios do Azure no inquilino do Azure Active Directory diferente. Para especificar um inquilino do Azure AD, tem de ter permissões de administrador para a subscrição do Azure que está a utilizar.

### <a name="use-your-personal-microsoft-account"></a>Utilizar a sua conta Microsoft pessoal

Para utilizar uma conta Microsoft pessoal para integração de Git, pode ligar o seu repositório Azure pessoais ao Active Directory da sua organização.

1. Adicione a sua conta Microsoft pessoal ao Active Directory da sua organização como convidado. Para mais informações, veja [utilizadores de colaboração de adicionar Azure Active Directory B2B no portal do Azure](../active-directory/b2b/add-users-administrator.md).

2. Inicie sessão no portal do Azure com a sua conta Microsoft pessoal. Em seguida, mude para o Active Directory da sua organização.

3. Vá para a secção de DevOps do Azure, onde agora ver seu repositório de pessoal. Selecione o repositório e ligue-se com o Active Directory.

Após estes passos de configuração, seu repositório pessoal está disponível quando configurar a integração de Git na IU do Data Factory.

Para obter mais informações sobre a ligação de repositórios do Azure para o Active Directory da sua organização, consulte [ligar a sua organização de DevOps do Azure ao Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

### <a name="switch-to-a-different-git-repo"></a>Mude para um repositório de Git diferente

Para mudar para um repositório de Git diferente, localize o ícone no canto superior direito da página de descrição geral do Data Factory, conforme mostrado na seguinte captura de ecrã. Se não pode ver o ícone, limpe a cache do browser de local. Selecione o ícone para remover a associação com o repositório atual.

Depois de remover a associação com o repositório atual, pode configurar as definições de Git para utilizar um repositório diferente. Em seguida, pode importar recursos do Data Factory existentes para o novo repositório.

![Remova a associação com o repositório de Git atual](media/author-visually/remove-repo.png)

### <a name="use-version-control"></a>Usar o controle de versão
Sistemas de controle de versão (também conhecido como _controle de fonte_) permitem que os desenvolvedores colaborar em código e acompanhar alterações feitas no código bases. Controlo de origem é uma ferramenta essencial para projetos com vários desenvolvedores.

Cada repositório de Git de repositórios do Azure que está associada uma fábrica de dados tem um ramo de colaboração. (`master` é o ramo de colaboração predefinido). Os utilizadores também podem criar ramificações recursos clicando **+ nova ramificação** e desenvolvimento nas ramificações recursos.

![Alterar o código, a sincronização ou de publicação](media/author-visually/sync-publish.png)

Quando estiver preparado com o desenvolvimento de recursos no seu ramo de funcionalidade, pode clicar em **criar pedido pull**. Esta ação lhe Azure repositórios Git em que pode fazer pedidos pull, revisões de código e intercalar as alterações ao ramo de colaboração. (`master` é a predefinição). Só são permitidas para publicar para o serviço Data Factory do ramo de colaboração. 

![Criar um novo pedido pull](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Configurar definições de publicação

Para configurar o ramo de publicação - ou seja, o ramo onde são guardados os modelos do Resource Manager - adicionar um `publish_config.json` ficheiro para a pasta de raiz no ramo de colaboração. Fábrica de dados lê esse arquivo, procura o campo `publishBranch`e cria um novo ramo (se ainda não exista) com o valor fornecido. Em seguida, salva todos os modelos do Resource Manager para a localização especificada. Por exemplo:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Quando publicar a partir do modo de Git, pode confirmar que o Data Factory está usando o ramo de publicação que esperava, conforme mostrado na captura de ecrã seguinte:

![Confirmar o ramo de publicação correto](media/author-visually/configure-publish-branch.png)

Quando especificar um novo ramo de publicação, o Data Factory não elimina o ramo de publicação anterior. Se quiser remoto o ramo de publicação anterior, elimine-o manualmente.

Fábrica de dados só lê o `publish_config.json` quando ele carregar a fábrica de ficheiros. Se já tiver o factory carregado no portal, atualize o browser para tornar as alterações entrem em vigor.

### <a name="publish-code-changes"></a>Publicar alterações de código
Depois de foram mescladas alterações para o ramo de colaboração (`master` é o padrão), selecione **publicar** para publicar manualmente as alterações de código no ramo principal para o serviço Data Factory.

![Publicar as alterações no serviço Data Factory](media/author-visually/publish-changes.png)

> [!IMPORTANT]
> O ramo principal não é representa o que é implementado no serviço Data Factory. O ramo principal *tem* ser publicados manualmente o serviço Data Factory.

### <a name="advantages-of-git-integration"></a>Vantagens da integração de Git

-   **Controle de fonte**. À medida que as cargas de trabalho de fábrica de dados forem cruciais, iria querer integrar sua fábrica com o Git para tirar partido de vários benefícios de controle de origem com o seguinte:
    -   Capacidade de controlar/auditoria alterações.
    -   Capacidade de reverter as alterações introduzidas bugs.
-   **Poupa parcial**. À medida que faz muitas alterações na sua fábrica, percebe que no modo em DIRETO de regular, não é possível guardar as alterações como rascunho, porque não está preparado, ou não deseja perder as alterações no caso de falha de seu computador. Com a integração de Git, pode continuar a guardar as alterações de forma incremental e publicar à fábrica de apenas quando estiver pronto. Git funciona como um local de teste para o seu trabalho, até ter testado as suas alterações para a sua satisfação.
-   **Colaboração e controlo**. Se tiver vários membros da Equipe a participar no mesmo Factory, pode querer permitir que seus colegas de equipa colaborar entre si através de um processo de revisão de código. Também pode configurar a fábrica de forma que nem todas as contribuinte para a fábrica tem permissão para implementar a fábrica. Os membros da Equipe apenas poderão ter permissão para efetuar alterações através de Git, mas apenas determinadas pessoas da Equipe estão autorizadas a "Publicar" as alterações para a fábrica.
-   **Mostrar diferenças**. No modo Git, obtém a um bom diff da carga que diz respeito a publicar para a fábrica. Este diff mostra todos os recursos/entidades que foi modificado/adicionada/eliminada desde a última vez publicados para a fábrica. Com base nesta diferença, pode prosseguir mais com publicação, ou voltar atrás e verificar as alterações e, em seguida, voltar mais tarde.
-   **Better CI/CD**. Se estiver a utilizar o modo de Git, pode configurar o seu pipeline de lançamento para acionar automaticamente como em breve, porque existem todas as alterações feitas na fábrica de desenvolvimento. Também obtém personalizar as propriedades na sua fábrica que estão disponíveis como parâmetros no modelo do Resource Manager. Pode ser útil manter apenas o conjunto de propriedades como parâmetros necessário, e tudo mais difícil codificado.
-   **Melhor desempenho**. Uma fábrica de média carrega 10 x vezes mais rapidamente no modo do Git que no modo em DIRETO regular, porque os recursos são transferidos através de Git.

### <a name="best-practices-for-git-integration"></a>Melhores práticas para a integração de Git

-   **Permissões**. Normalmente, não quer todos os membros de equipe para estar a ter permissões para atualizar a fábrica.
    -   Todos os membros da Equipe devem ter permissões de leitura à fábrica de dados.
    -   Deve ser permitido apenas um conjunto selecionado de pessoas para publicar para a fábrica e, para que têm de ser parte da função de "Contribuinte do Data Factory" na fábrica de.
    -   Uma das boas práticas de controlo de origem é também por não permitir o check-ins diretos para o ramo de colaboração. Esse requisito impede bugs, como cada check-in executa um processo de pedido de solicitação.
-   **Alternar entre modos**.
    -    Assim que estiver no modo Git, não recomendamos-lhe alternar para o modo em DIRETO, principalmente porque todas as alterações efetuadas no modo em DIRETO, não será visível quando voltar a mudar para Git. Tente fazer as alterações no modo de Git em si e, em seguida, publicá-los por meio da interface do Usuário.
    -   Da mesma forma, não utilize qualquer cmdlets do powershell do Data factory, que eles obtenham o mesmo efeito ao diretamente aplicar as alterações fornecidas para a fábrica em direto.
-   **Utilizar palavras-passe do Azure Key Vault**.
    -   Recomendamos vivamente utilizar AzureKeyVault para armazenar quaisquer cadeias de ligação ou palavras-passe para os serviços ligados de DataFactory.
    -   Não armazenamos todas essas informações secretas no Git (por motivos de segurança), quaisquer alterações aos serviços ligados são publicadas imediatamente à fábrica de em direto. Esta publicação imediata, às vezes, não for o pretendido, como as alterações podem não ter de se tornar testadas, que anule a finalidade de Git.
    -   Como resultado, todos os tais segredos devem ser obtidos do serviços ligados que utilizem o Azure Key Vault com base.
    -   Alguns dos outros benefícios da utilização do Cofre de chaves, é que ela facilita o CI/CD, fazendo não fornecer estes segredos Gestor de recursos durante a implementação do modelo.

## <a name="author-with-github-integration"></a>Autor com integração do GitHub

Criação de Visual com integração do GitHub suporta o controlo de código fonte e colaboração for work nos seus pipelines da fábrica de dados. Pode associar uma fábrica de dados um repositório de conta do GitHub para o controlo de origem, colaboração, controlo de versões. Uma única conta GitHub pode ter vários repositórios, mas um repositório do GitHub pode ser associado a fábrica de dados apenas uma. Se não tiver uma conta do GitHub ou o repositório, siga [estas instruções](https://github.com/join) para criar os seus recursos.

A integração do GitHub com o Data Factory suporta dois GitHub público (ou seja, [ https://github.com ](https://github.com)) e o GitHub Enterprise. Pode usar a repositórios de GitHub públicos e privados com o Data Factory, como há muito tempo leu e permissão de escrita para o repositório no GitHub.

Para configurar um repositório do GitHub, terá de ter permissões de administrador para a subscrição do Azure que está a utilizar.

Para obter uma introdução de nove minutos e demonstração desta funcionalidade, veja o vídeo seguinte:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="limitations"></a>Limitações

- Pode armazenar ficheiros de dados e de script num repositório do GitHub. No entanto, terá de carregar os ficheiros manualmente para o armazenamento do Azure. Um pipeline do Data Factory não automaticamente carregar arquivos de script ou de dados armazenados num repositório do GitHub para o armazenamento do Azure.

- GitHub Enterprise com uma versão mais antiga do que 2.14.0 não funciona no browser Microsoft Edge.

- Integração do GitHub com as ferramentas de criação visual de fator de dados funciona apenas na versão em disponibilidade geral do Data Factory.

### <a name="configure-a-public-github-repository-with-azure-data-factory"></a>Configurar um repositório do GitHub público com o Azure Data Factory

Pode configurar um repositório do GitHub com uma fábrica de dados por meio de dois métodos.

**Método de configuração 1 (repositório público): Página Vamos começar**

No Azure Data Factory, vá para o **Vamos começar** página. Selecione **configurar o repositório de código**:

![Página de introdução à fábrica de dados](media/author-visually/github-integration-image1.png)

O **definições de repositório** é apresentado o painel de configuração:

![Definições de repositório do GitHub](media/author-visually/github-integration-image2.png)

O painel mostra o seguinte código de repositórios do Azure, as definições de repositório:

| **Definição**                                              | **Descrição**                                                                                                                                                                                                                                                                                                                                                                                                                   | **Valor**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **Tipo de repositório**                                      | O tipo de repositório de código de repositórios do Azure.                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **Conta do GitHub**                                       | Nome da sua conta GitHub. Este nome pode ser encontrado partir https://github.com/{account nome} / {nome do repositório}. Navegar para esta página pede-lhe para introduzir as credenciais de OAuth do GitHub para a sua conta do GitHub.                                                                                                                                                                                                                                               |                    |
| **RepositoryName**                                       | O nome de repositório de código do GitHub. Contas GitHub contenham repositórios de Git para gerir o seu código-fonte. Pode criar um novo repositório ou utilizar um repositório existente que já está na sua conta.                                                                                                                                                                                                                              |                    |
| **Ramo de colaboração**                                 | O ramo de colaboração do GitHub que serve para publicação. Por padrão, ele é a Mestra. Altere esta definição caso queira publicar recursos a partir de outro ramo.                                                                                                                                                                                                                                                               |                    |
| **Pasta raiz**                                          | A pasta de raiz no seu ramo de colaboração do GitHub.                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **Importar recursos do Data Factory existentes para o repositório** | Especifica se pretende importar recursos de fábrica de dados existentes de UX **tela de criação** para um repositório do GitHub. Selecione a caixa para importar os seus recursos de fábrica de dados para o repositório de Git associado no formato JSON. Esta ação exporta cada recurso individualmente (ou seja, os serviços ligados e conjuntos de dados são exportados para o JSONs separados). Quando esta caixa não está selecionada, os recursos existentes não são importados. | Selecionado (predefinição) |
| **Ramo para importar recursos para**                       | Especifica para o ramo os recursos de fábrica de dados (pipelines, conjuntos de dados, serviços ligados, etc.) são importados. Pode importar recursos para um dos seguintes ramificações: um. B de colaboração. Crie nova c. Utilizar Existente                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-public-repo-ux-authoring-canvas"></a>Método de configuração 2 (repositório público): Experiência do Usuário baseada em telas de criação

Na UX de fábrica de dados do Azure **tela de criação**, localize a fábrica de dados. Selecione o **Data Factory** menu pendente e, em seguida, selecione **configurar repositório de código**.

É apresentado um painel de configuração. Para obter detalhes sobre as definições de configuração, consulte as descrições *método de configuração 1* acima.

### <a name="configure-a-github-enterprise-repository-with-azure-data-factory"></a>Configurar um repositório de GitHub Enterprise com o Azure Data Factory

Pode configurar um repositório de GitHub Enterprise com uma fábrica de dados por meio de dois métodos.

#### <a name="configuration-method-1-enterprise-repo-lets-get-started-page"></a>Método de configuração 1 (repositório de Enterprise): Página Vamos começar

No Azure Data Factory, vá para o **Vamos começar** página. Selecione **configurar o repositório de código**:

![Página de introdução à fábrica de dados](media/author-visually/github-integration-image1.png)

O **definições de repositório** é apresentado o painel de configuração:

![Definições de repositório do GitHub](media/author-visually/github-integration-image3.png)

O painel mostra o seguinte código de repositórios do Azure, as definições de repositório:

| **Definição**                                              | **Descrição**                                                                                                                                                                                                                                                                                                                                                                                                                   | **Valor**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **Tipo de repositório**                                      | O tipo de repositório de código de repositórios do Azure.                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **Utilizar o GitHub Enterprise**                                | Caixa de verificação para selecionar o GitHub Enterprise                                                                                                                                                                                                                                                                                                                                                                                              |                    |
| **URL do GitHub Enterprise**                                | O URL de raiz de GitHub Enterprise. Por exemplo: https://github.mydomain.com                                                                                                                                                                                                                                                                                                                                                          |                    |
| **Conta do GitHub**                                       | Nome da sua conta GitHub. Este nome pode ser encontrado partir https://github.com/{account nome} / {nome do repositório}. Navegar para esta página pede-lhe para introduzir as credenciais de OAuth do GitHub para a sua conta do GitHub.                                                                                                                                                                                                                                               |                    |
| **RepositoryName**                                       | O nome de repositório de código do GitHub. Contas GitHub contenham repositórios de Git para gerir o seu código-fonte. Pode criar um novo repositório ou utilizar um repositório existente que já está na sua conta.                                                                                                                                                                                                                              |                    |
| **Ramo de colaboração**                                 | O ramo de colaboração do GitHub que serve para publicação. Por padrão, ele é a Mestra. Altere esta definição caso queira publicar recursos a partir de outro ramo.                                                                                                                                                                                                                                                               |                    |
| **Pasta raiz**                                          | A pasta de raiz no seu ramo de colaboração do GitHub.                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **Importar recursos do Data Factory existentes para o repositório** | Especifica se pretende importar recursos de fábrica de dados existentes de UX **tela de criação** para um repositório do GitHub. Selecione a caixa para importar os seus recursos de fábrica de dados para o repositório de Git associado no formato JSON. Esta ação exporta cada recurso individualmente (ou seja, os serviços ligados e conjuntos de dados são exportados para o JSONs separados). Quando esta caixa não está selecionada, os recursos existentes não são importados. | Selecionado (predefinição) |
| **Ramo para importar recursos para**                       | Especifica para o ramo os recursos de fábrica de dados (pipelines, conjuntos de dados, serviços ligados, etc.) são importados. Pode importar recursos para um dos seguintes ramificações: um. B de colaboração. Crie nova c. Utilizar Existente                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-enterprise-repo-ux-authoring-canvas"></a>Método de configuração 2 (repositório de Enterprise): Experiência do Usuário baseada em telas de criação

Na UX de fábrica de dados do Azure **tela de criação**, localize a fábrica de dados. Selecione o **Data Factory** menu pendente e, em seguida, selecione **configurar repositório de código**.

É apresentado um painel de configuração. Para obter detalhes sobre as definições de configuração, consulte as descrições *método de configuração 1* acima.

## <a name="use-the-expression-language"></a>Utilize a linguagem de expressão
Pode especificar expressões para valores de propriedade usando a linguagem de expressão que é suportada pelo Azure Data Factory.

Especificar expressões para valores de propriedade selecionando **adicionar conteúdo dinâmico**:

![Utilize a linguagem de expressão](media/author-visually/dynamic-content-1.png)

## <a name="use-functions-and-parameters"></a>Utilize as funções e parâmetros

Pode utilizar as funções ou especificar parâmetros para conjuntos de dados e pipelines na fábrica de dados **construtor de expressões**:

Para obter informações sobre as expressões suportadas, consulte [expressões e funções no Azure Data Factory](control-flow-expression-language-functions.md).

![Adicionar conteúdo dinâmico](media/author-visually/dynamic-content-2.png)

## <a name="provide-feedback"></a>Enviar comentários
Selecione **comentários** para comentar sobre as funcionalidades ou para notificar a Microsoft sobre problemas com a ferramenta:

![Comentários](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre monitorização e gestão de pipelines, consulte [monitorizar e gerir pipelines de forma programática](monitor-programmatically.md).
