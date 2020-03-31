---
title: Controlo de código fonte
description: Saiba como configurar o controlo de fontes na Azure Data Factory
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
ms.openlocfilehash: 6645c2672e15c562216b4347f779ef3634a2f124
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130876"
---
# <a name="source-control-in-azure-data-factory"></a>Controlo de fontes na Fábrica de Dados Azure

A experiência de interface de utilizador da Azure Data Factory (UX) tem duas experiências disponíveis para autoria visual:

- Autor diretamente com o serviço Data Factory
- Autor com integração Azure Repos Git ou GitHub

> [!NOTE]
> Apenas a autoria direta mente com o serviço Data Factory é suportada na Nuvem do Governo Azure.

## <a name="author-directly-with-the-data-factory-service"></a>Autor diretamente com o serviço Data Factory

Enquanto é da autoria direta mente com o serviço Data Factory, a única forma de guardar alterações é através do botão **Publicar Todos.** Uma vez clicado, todas as alterações que fez são publicadas diretamente no serviço Data Factory. 

![Publicar modo](media/author-visually/data-factory-publish.png)

A autoria direta mente com o serviço Data Factory tem as seguintes limitações:

- O serviço Data Factory não inclui um repositório para armazenar as entidades JSON para as suas alterações.
- O serviço Data Factory não está otimizado para o controlo de colaboração ou versão.

> [!NOTE]
> A autoria direta mente com o serviço Data Factory é desativada na Fábrica de Dados Azure UX quando um repositório Git é configurado. As alterações podem ser feitas diretamente no serviço através da PowerShell ou de um SDK.

## <a name="author-with-azure-repos-git-integration"></a>Author with Azure Repos Git integration (Criar com a integração em Repositórios do Azure no Git)

A autoria visual com a integração azure Repos Git apoia o controlo de fontes e a colaboração para o trabalho nos seus oleodutos de fábrica de dados. Pode associar uma fábrica de dados a um repositório de organização Azure Repos Git para controlo de fontes, colaboração, versão, e assim por diante. Uma única organização Azure Repos Git pode ter múltiplos repositórios, mas um repositório Azure Repos Git pode ser associado apenas a uma fábrica de dados. Se não tiver uma organização ou repositório Azure Repos, siga [estas instruções](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) para criar os seus recursos.

> [!NOTE]
> Pode armazenar scripts e ficheiros de dados num repositório Azure Repos Git. No entanto, tem de fazer o upload dos ficheiros manualmente para o Armazenamento Azure. Um pipeline data Factory não envia automaticamente ficheiros de script ou dados armazenados num repositório Azure Repos Git para o Armazenamento Azure.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Configure um repositório Azure Repos Git com a Azure Data Factory

Pode configurar um repositório Azure Repos Git com uma fábrica de dados através de dois métodos.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Método de configuração 1: Página inicial da Azure Data Factory

Na página inicial da Azure Data Factory, selecione Configurar o **Repositório**de Código .

![Configure um repositório de código Azure Repos](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Método de configuração 2: Tela de autoria UX
Na tela de autoria da Azure Data Factory UX, selecione o menu de entrega da Fábrica de **Dados** e, em seguida, selecione Configurar o **Repositório**de Código .

![Configure as definições de repositório de código para a autoria ux](media/author-visually/configure-repo-2.png)

Ambos os métodos abrem o painel de configuração de configurações do repositório.

![Configure as definições de repositório de código](media/author-visually/repo-settings.png)

O painel de configuração mostra as seguintes definições de repositório de código Azure Repos:

| Definição | Descrição | Valor |
|:--- |:--- |:--- |
| **Tipo repositório** | O tipo de repositório do código Azure Repos.<br/> | Azure DevOps Git ou GitHub |
| **Azure Active Directory** | O seu nome de inquilino da AD Azure. | `<your tenant name>` |
| **Organização Azure Repos** | O nome da organização Azure Repos. Pode localizar o nome da organização `https://{organization name}.visualstudio.com`Azure Repos em . Pode [inscrever-se na sua organização Azure Repos](https://www.visualstudio.com/team-services/git/) para aceder ao seu perfil de Estúdio Visual e ver os seus repositórios e projetos. | `<your organization name>` |
| **Nome do Projeto** | O nome do seu projeto Azure Repos. Pode localizar o nome do seu `https://{organization name}.visualstudio.com/{project name}`projeto Azure Repos em . | `<your Azure Repos project name>` |
| **RepositórioNome** | O seu nome de repositório de código Azure Repos. Os projetos Azure Repos contêm repositórios Git para gerir o seu código fonte à medida que o seu projeto cresce. Pode criar um novo repositório ou usar um repositório existente que já está no seu projeto. | `<your Azure Repos code repository name>` |
| **Ramo de colaboração** | O seu ramo de colaboração Azure Repos que é usado para publicação. Por defeito, `master`é. Mude este cenário no caso de querer publicar recursos de outra sucursal. | `<your collaboration branch name>` |
| **Pasta raiz** | A sua pasta-raiz no seu ramo de colaboração Azure Repos. | `<your root folder name>` |
| **Importar recursos da fábrica de dados existentes para repositório** | Especifica se importa os recursos de fábrica de dados existentes da tela de **autoria** UX para um repositório Azure Repos Git. Selecione a caixa para importar os seus recursos de fábrica de dados para o repositório Git associado em formato JSON. Esta ação exporta cada recurso individualmente (isto é, os serviços e conjuntos de dados ligados são exportados para JSONs separados). Quando esta caixa não é selecionada, os recursos existentes não são importados. | Selecionado (predefinido) |
| **Sucursal para importar recursos em** | Especifica-se em que ramo os recursos da fábrica de dados (oleodutos, conjuntos de dados, serviços ligados, etc.) são importados. Pode importar recursos num dos seguintes ramos: a. Colaboração b. Criar novo C. Utilizar existente |  |

> [!NOTE]
> Se estiver a utilizar o Microsoft Edge e não vir quaisquer valores na sua conta Azure DevOps dropdown, adicione https://*.visualstudio.com à lista de sites fidedignos.

### <a name="use-a-different-azure-active-directory-tenant"></a>Use um inquilino azure ative diretório diferente

Pode criar um repositório Git do Azure Repos num inquilino diferente do Azure Active Directory. Para especificar um inquilino do Azure AD diferente, tem de ter permissões de administrador para a subscrição do Azure que está a utilizar.

### <a name="use-your-personal-microsoft-account"></a>Use a sua conta pessoal da Microsoft

Para utilizar uma conta pessoal da Microsoft para a integração git, pode ligar o seu Azure Repo pessoal ao Diretório Ativo da sua organização.

1. Adicione a sua conta pessoal da Microsoft ao Diretório Ativo da sua organização como convidado. Para mais informações, consulte [add Azure Ative Directory B2B de colaboração no portal Azure](../active-directory/b2b/add-users-administrator.md).

2. Inicie sessão no portal Azure com a sua conta pessoal da Microsoft. Em seguida, mude para o Diretório Ativo da sua organização.

3. Vá à secção Azure DevOps, onde agora vê o seu repo pessoal. Selecione o repo e ligue-se ao Ative Directy.

Após estes passos de configuração, o seu repo pessoal está disponível quando configura a integração git na UI da Fábrica de Dados.

Para obter mais informações sobre a ligação do Azure Repos ao Diretório Ativo da sua organização, consulte [Connect your Azure DevOps organization to Azure Ative Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="author-with-github-integration"></a>Author with GitHub integration (Criar com a integração no GitHub)

A autoria visual com a integração do GitHub suporta o controlo de fontes e a colaboração para o trabalho nos seus oleodutos de fábrica de dados. Pode associar uma fábrica de dados a um repositório de conta GitHub para controlo de fontes, colaboração, versão. Uma única conta GitHub pode ter múltiplos repositórios, mas um repositório GitHub pode ser associado apenas a uma fábrica de dados. Se não tiver uma conta GitHub ou repositório, siga [estas instruções](https://github.com/join) para criar os seus recursos.

A integração gitHub com data factory suporta tanto [https://github.com](https://github.com)o GitHub público (isto é, ) como a GitHub Enterprise. Você pode usar repositórios gitHub públicos e privados com Data Factory desde que você tenha lido e escrito permissão para o repositório em GitHub.

Para configurar um repo GitHub, deve ter permissões de administrador para a subscrição Azure que está a usar.

Para uma introdução de nove minutos e demonstração desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>Configure um repositório GitHub com a Azure Data Factory

Pode configurar um repositório GitHub com uma fábrica de dados através de dois métodos.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Método de configuração 1: Página inicial da Azure Data Factory

Na página inicial da Azure Data Factory, selecione Configurar o **Repositório**de Código .

![Configure um repositório de código Azure Repos](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Método de configuração 2: Tela de autoria UX

Na tela de autoria da Azure Data Factory UX, selecione o menu de entrega da Fábrica de **Dados** e, em seguida, selecione Configurar o **Repositório**de Código .

![Configure as definições de repositório de código para a autoria ux](media/author-visually/configure-repo-2.png)

Ambos os métodos abrem o painel de configuração de configurações do repositório.

![Definições de repositório GitHub](media/author-visually/github-integration-image2.png)

O painel de configuração mostra as seguintes definições de repositório GitHub:

| **Definição** | **Descrição**  | **Valor**  |
|:--- |:--- |:--- |
| **Tipo repositório** | O tipo de repositório do código Azure Repos. | GitHub |
| **Use GitHub Enterprise** | Caixa de verificação para selecionar GitHub Enterprise | não selecionado (por defeito) |
| **URL da Empresa GitHub** | O URL de raiz gitHub Enterprise (deve ser HTTPS para o servidor local GitHub Enterprise). Por exemplo: https://github.mydomain.com. Só é necessário se **a Utilização GitHub Enterprise** for selecionada | `<your GitHub enterprise url>` |                                                           
| **Conta do GitHub** | O nome da sua conta GitHub. Este nome pode ser\/encontrado a partir de https: /github.com/{name da conta}/{repositório de nome}. Navegar para esta página leva-o a introduzir credenciais GitHub OAuth na sua conta GitHub. | `<your GitHub account name>` |
| **Nome do repositório**  | O seu nome de repositório de código GitHub. As contas do GitHub contêm repositórios Git para gerir o seu código fonte. Pode criar um novo repositório ou usar um repositório existente que já está na sua conta. | `<your repository name>` |
| **Ramo de colaboração** | O seu ramo de colaboração GitHub que é usado para publicação. Por defeito, é mestre. Mude este cenário no caso de querer publicar recursos de outra sucursal. | `<your collaboration branch>` |
| **Pasta raiz** | A sua pasta-raiz no seu ramo de colaboração GitHub. |`<your root folder name>` |
| **Importar recursos da fábrica de dados existentes para repositório** | Especifica se importa os recursos de fábrica de dados existentes da tela de autoria UX para um repositório GitHub. Selecione a caixa para importar os seus recursos de fábrica de dados para o repositório Git associado em formato JSON. Esta ação exporta cada recurso individualmente (isto é, os serviços e conjuntos de dados ligados são exportados para JSONs separados). Quando esta caixa não é selecionada, os recursos existentes não são importados. | Selecionado (predefinido) |
| **Sucursal para importar recursos em** | Especifica-se em que ramo os recursos da fábrica de dados (oleodutos, conjuntos de dados, serviços ligados, etc.) são importados. Pode importar recursos num dos seguintes ramos: a. Colaboração b. Criar novo C. Utilizar existente |  |

### <a name="known-github-limitations"></a>Limitações conhecidas do GitHub

- Pode armazenar scripts e ficheiros de dados num repositório GitHub. No entanto, tem de fazer o upload dos ficheiros manualmente para o Armazenamento Azure. Um pipeline data Factory não faz o upload automaticamente de scripts ou ficheiros de dados armazenados num repositório GitHub para o Armazenamento Azure.

- GitHub Enterprise com uma versão com uma versão superior a 2.14.0 não funciona no navegador Microsoft Edge.

- A integração do GitHub com as ferramentas de autoria visual da Data Factory só funciona na versão geralmente disponível da Data Factory.

- Um máximo de 1.000 entidades por tipo de recurso (como oleodutos e conjuntos de dados) podem ser recolhidos a partir de uma única filial do GitHub. Se este limite for atingido, é sugerido que divida os seus recursos em fábricas separadas. A Azure DevOps Git não tem esta limitação.

## <a name="switch-to-a-different-git-repo"></a>Mude para um repo git diferente

Para mudar para um repo Git diferente, clique no ícone **Git Repo Definições** no canto superior direito da página de visão geral da Fábrica de Dados. Se não consegue ver o ícone, limpe a cache do navegador local. Selecione o ícone para remover a associação com o repo atual.

![Ícone git](media/author-visually/remove-repo.png)

Assim que aparecer o painel de definições de repositório, **selecione Remover Git**. Introduza o nome da sua fábrica de dados e clique **em confirmar** para remover o repositório Git associado à sua fábrica de dados.

![Remova a associação com o repo git atual](media/author-visually/remove-repo2.png)

Depois de remover a associação com o repo atual, pode configurar as suas definições git para usar um repo diferente e, em seguida, importar os recursos existentes da Data Factory para o novo repo. 

## <a name="version-control"></a>Controlo de versões

Os sistemas de controlo de versão (também conhecidos como controlo de _fonte)_ permitem que os desenvolvedores colaborem em alterações de código e de rastreio que são feitas na base de código. O controlo de origem é uma ferramenta essencial para projetos multi-desenvolvedores.

### <a name="creating-feature-branches"></a>Criação de ramos de recurso

Cada repositório Azure Repos Git que está associado a uma fábrica de dados tem um ramo de colaboração. (é`master` o ramo de colaboração padrão). Os utilizadores também podem criar ramificações de funcionalidades clicando **+ Nova Filial** no dropdown do ramo. Assim que aparecer o novo painel de ramificação, introduza o nome do seu ramo de recurso.

![Criar um novo ramo](media/author-visually/new-branch.png)

Quando estiver pronto para fundir as alterações do seu ramo de funcionalidade para o seu ramo de colaboração, clique no dropdown do ramo e selecione **Create pull request**. Esta ação leva-o ao Azure Repos Git onde pode levantar pedidos de pull, fazer revisões de códigos e fundir alterações no seu ramo de colaboração. (é`master` o padrão). Só está autorizado a publicar no serviço Data Factory a partir do seu ramo de colaboração. 

![Criar um novo pedido de puxar](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Configurar as definições de publicação

Para configurar o ramo de publicação - isto é, o `publish_config.json` ramo onde os modelos do Gestor de Recursos são guardados - adicione um ficheiro à pasta raiz no ramo de colaboração. Data Factory lê este ficheiro, `publishBranch`procura o campo, e cria um novo ramo (se já não existe) com o valor fornecido. Em seguida, guarda todos os modelos do Gestor de Recursos para o local especificado. Por exemplo:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Quando especifica uma nova filial, a Data Factory não apaga a filial anterior. Se pretender remover a filial anterior, elimine-a manualmente.

> [!NOTE]
> A Fábrica de `publish_config.json` Dados só lê o ficheiro quando carrega a fábrica. Se já tem a fábrica carregada no portal, arete o navegador para que as suas alterações façam efeito.

### <a name="publish-code-changes"></a>Publicar alterações de código

Depois de ter fundido alterações`master` no ramo de colaboração (é o padrão), clique em **Publicar** para publicar manualmente as alterações de código no ramo principal do serviço Data Factory.

![Publicar alterações ao serviço Data Factory](media/author-visually/publish-changes.png)

Abrir-se-á um painel lateral onde confirma que a filial e as alterações pendentes estão corretas. Assim que verificar as suas alterações, clique em **OK** para confirmar a publicação.

![Confirme a filial correta](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> O ramo principal não é representativo do que está implantado no serviço data factory. O ramo principal *deve* ser publicado manualmente no serviço Data Factory.

## <a name="advantages-of-git-integration"></a>Vantagens da integração no Git

-   **Controlo de Origem**. À medida que as cargas de trabalho da sua fábrica de dados se tornam cruciais, gostaria de integrar a sua fábrica com a Git para alavancar vários benefícios de controlo de fontes como os seguintes:
    -   Capacidade de rastrear/auditar alterações.
    -   Capacidade de reverter alterações que introduziram bugs.
-   **Salvamentos Parciais**. À medida que faz muitas alterações na sua fábrica, vai perceber que no modo LIVE regular, não pode guardar as suas alterações como rascunho, porque não está pronto, ou não quer perder as suas alterações caso o computador se despenhe. Com a integração git, pode continuar a guardar as suas mudanças de forma incremental e publicar na fábrica apenas quando estiver pronto. Git funciona como um local de preparação para o seu trabalho, até que tenha testado as suas alterações à sua satisfação.
-   **Colaboração e Controlo.** Se tiver vários membros da equipa que participam na mesma fábrica, pode querer deixar que os seus companheiros de equipa colaborem uns com os outros através de um processo de revisão de códigos. Também pode instalar a sua fábrica de modo a que nem todos os colaboradores da fábrica têm permissão para se deslocarem para a fábrica. Os membros da equipa podem apenas ser autorizados a fazer alterações via Git, mas apenas algumas pessoas na equipa estão autorizadas a "publicar" as alterações na fábrica.
-   **Mostrando difusões.** No modo Git, você pode ver uma boa difusão da carga útil que está prestes a ser publicada na fábrica. Esta difusão mostra-lhe todos os recursos/entidades que foram modificados/adicionados/eliminados desde a última vez que publicou na sua fábrica. Com base nesta difusão, pode continuar com a publicação, ou voltar e verificar as suas alterações, e depois voltar mais tarde.
-   **Melhor CI/CD**. Se estiver a utilizar o modo Git, pode configurar o seu gasoduto de libertação para disparar automaticamente assim que houver alterações na fábrica de dev. Você também pode personalizar as propriedades da sua fábrica que estão disponíveis como parâmetros no modelo De Gestor de Recursos. Pode ser útil manter apenas o conjunto de propriedades necessáriocomo parâmetros, e ter todo o resto codificado.
-   **Melhor Desempenho.** Uma fábrica média carrega dez vezes mais rápido no modo Git do que no modo LIVE regular, porque os recursos são descarregados via Git.

## <a name="best-practices-for-git-integration"></a>Boas práticas para integração git

### <a name="permissions"></a>Permissões

Normalmente, não queres que todos os membros da equipa tenham permissões para atualizar a fábrica. Recomenda-se as seguintes definições de permissões:

*   Todos os membros da equipa deviam ter lido permissões para a fábrica de dados.
*   Apenas um conjunto selecionado de pessoas deve ser autorizado a publicar na fábrica. Para isso, devem ter o papel de **colaborador da Fábrica** de Dados na fábrica. Para obter mais informações sobre permissões, consulte [Papéis e permissões para a Azure Data Factory](concepts-roles-permissions.md).
   
Recomenda-se não permitir check-ins diretos no ramo de colaboração. Esta restrição pode ajudar a prevenir bugs, uma vez que cada check-in passará por um processo de revisão de pedidos de retirada descrito na [Criação](source-control.md#creating-feature-branches)de ramos de recurso .

### <a name="using-passwords-from-azure-key-vault"></a>Usando senhas do Cofre de Chaves Azure

Recomenda-se utilizar o Azure Key Vault para armazenar quaisquer cordas de ligação ou senhas para serviços ligados à fábrica de dados. Por razões de segurança, não armazenamos nenhuma informação secreta em Git, pelo que quaisquer alterações aos Serviços Linked são publicadas imediatamente no serviço Azure Data Factory.

A utilização do Key Vault também facilita a integração e implementação contínua, uma vez que não terá de fornecer estes segredos durante a implementação do modelo do Gestor de Recursos.

## <a name="troubleshooting-git-integration"></a>Resolução de problemas da integração de Git

### <a name="stale-publish-branch"></a>Sucursal de publicação Stale

Se a sucursal de publicação estiver dessincronizada com o ramo principal e contiver recursos desatualizados apesar de uma publicação recente, tente seguir estes passos:

1. Remova o seu repositório Git atual
1. Reconfigure git com as mesmas configurações, mas certifique-se de que importar recursos da Fábrica de **Dados existentes para repositório** é selecionado e escolha **Nova sucursal**
1. Criar um pedido de pull para fundir as alterações ao ramo de colaboração 

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
