---
title: Guia de introdução para desenvolvedores no Azure | Documentos da Microsoft
description: Este tópico fornece informações essenciais para os desenvolvedores que desejam para começar a utilizar a plataforma Microsoft Azure para as suas necessidades de desenvolvimento.
services: ''
cloud: ''
documentationcenter: ''
author: ggailey777
manager: erikre
ms.assetid: ''
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: glenga
ms.openlocfilehash: d78998c3eab79c3c9d5511335c5d27be5803cc3e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762508"
---
# <a name="get-started-guide-for-azure-developers"></a>Guia de introdução para programadores do Azure

## <a name="what-is-azure"></a>O que é o Azure?

O Azure é uma plataforma na cloud completa que pode alojar as suas aplicações existentes, simplificar o desenvolvimento de novos aplicativos e até mesmo melhorar a aplicações no local. Azure integra-se os serviços cloud que precisa para desenvolver, testar, implementar e gerir as suas aplicações — ao tirar partido das eficiências da cloud de informática.

Ao alojar as suas aplicações no Azure, pode começar com pouco e dimensionar facilmente a sua aplicação à medida que aumenta a sua procura dos clientes. O Azure também oferece a fiabilidade de que precisamos para aplicações de elevada disponibilidade, incluindo até mesmo a ativação pós-falha entre regiões diferentes. O [portal do Azure](https://portal.azure.com) permite-lhe gerir facilmente todos os seus serviços do Azure. Também pode gerir os serviços por meio de programação através de APIs e modelos específicos do serviço.

**Quem deve ler este**: Este guia é uma introdução à plataforma Azure para desenvolvedores de aplicativos. Ele fornece orientação e direção que precisa para começar a criar novas aplicações no Azure ou a migrar aplicações existentes no Azure.

## <a name="where-do-i-start"></a>Por onde devo começar?

Com todos os serviços que o Azure oferece, ele pode ser uma tarefa árdua para descobrir quais serviços de que precisa para suportar a arquitetura da solução. Esta secção destaca os serviços do Azure que os desenvolvedores normalmente usam. Para obter uma lista de todos os serviços do Azure, consulte a [documentação do Azure](../../index.md).

Em primeiro lugar, deve decidir como quer alojar a sua aplicação no Azure. Precisa de gerir toda a infraestrutura como uma máquina virtual (VM). Pode usar os recursos de gerenciamento de plataforma fornecidos pelo Azure? Talvez precise de uma arquitetura sem servidor para execução de código de host só?

Seu aplicativo precisa de armazenamento na cloud, o que o Azure fornece várias opções para. Pode tirar partido da autenticação do Azure. Também existem ferramentas para desenvolvimento com base na cloud e a monitorização e a maioria dos serviços de alojamento oferece integração de DevOps.

Agora, vamos examinar alguns dos serviços específicos que recomendamos para as suas aplicações a investigar.

### <a name="application-hosting"></a>Hospedagem de aplicativos

O Azure fornece que vários baseado na nuvem ofertas para executar a sua aplicação para que não precisa se preocupar sobre os detalhes de infraestrutura de computação. Pode facilmente aumentar verticalmente ou ampliar seus recursos à medida que aumenta a sua utilização da aplicação.

O Azure oferece serviços que suportam o desenvolvimento de aplicativos e necessidades de hospedagem. O Azure fornece a infraestrutura como serviço (IaaS) para lhe dar controle total sobre a hospedagem de aplicativos. Plataforma do Azure como um ofertas de serviço (PaaS) fornecer os serviços totalmente geridos necessários para criar as suas aplicações. Há até mesmo verdadeiro o alojamento sem servidor no Azure em que tudo o que precisa fazer é escrever seu código.

![Opções de alojamento de aplicações do Azure](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Serviço de Aplicações do Azure 

Quando pretender que o caminho mais rápido para publicar os seus projetos baseada na web, considere o serviço de aplicações do Azure. Serviço de aplicações torna mais fácil expandir as suas aplicações web para oferecer suporte a seus clientes móveis e publicar APIs de REST facilmente consumidos. Esta plataforma oferece autenticação utilizando fornecedores de redes sociais, dimensionamento automático com base no tráfego, teste em produção e implementações baseadas em contentores e contínuas.

Pode criar aplicações web, back-ends de aplicações móveis e aplicações API.

Uma vez que todos os três tipos de aplicações partilham o tempo de execução do serviço de aplicações, pode alojar um Web site, oferecer suporte a clientes móveis e expor as suas APIs no Azure, tudo a partir do mesmo projeto ou solução. Para saber mais sobre o serviço de aplicações, veja [o que é o Azure Web Apps](../../app-service/overview.md).

Serviço de aplicações foi desenvolvido com DevOps em mente. Ele oferece suporte a várias ferramentas para implementações de integração contínua e publicação, incluindo GitHub webhooks, Jenkins, Azure DevOps, TeamCity e outras pessoas.

Pode migrar seus aplicativos existentes no serviço de aplicações ao utilizar o [ferramenta de migração online](https://www.migratetoazure.net/).

>**Quando utilizar**: Utilize o serviço de aplicações quando estiver a migrar aplicativos web existentes para o Azure e quando precisar de uma plataforma de hospedagem totalmente gerida para as suas aplicações web. Também pode utilizar o serviço de aplicações quando tiver de suportar clientes móveis ou expor REST APIs com a sua aplicação.

>**Introdução ao**: Serviço de aplicações torna fácil criar e implementar a sua primeira [aplicação web](../../app-service/app-service-web-get-started-dotnet.md), [aplicação móvel](../../app-service-mobile/app-service-mobile-ios-get-started.md), ou [aplicação API](../../app-service/app-service-web-tutorial-rest-api.md).

>**Experimente agora**: Serviço de aplicações permite-lhe aprovisionar uma aplicação de curta duração para experimentar a plataforma sem ter de inscrever-se numa conta do Azure. Experimentar a plataforma e [criar a sua aplicação de serviço de aplicações do Azure](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Máquinas Virtuais do Azure

Uma infraestrutura como um provedor de serviço (IaaS), o Azure permite-lhe implementar ou migrar a sua aplicação para Windows ou VMs do Linux. Em conjunto com a rede Virtual do Azure, máquinas virtuais do Azure suporta a implementação do Windows ou VMs do Linux no Azure. Com as VMs, tem controle total sobre a configuração da máquina. Ao utilizar VMs, é responsável por todos os servidor instalação, configuração, manutenção e sistema operativo patches de software.

Por causa de nível de controle que tem com as VMs, pode executar uma grande variedade de cargas de trabalho do servidor no Azure que não se encaixam num modelo de PaaS. Estas cargas de trabalho incluem servidores de base de dados, o Windows Server Active Directory e o Microsoft SharePoint. Para obter mais informações, consulte a documentação de máquinas virtuais para qualquer um [Linux](/azure/virtual-machines/linux/) ou [Windows](/azure/virtual-machines/windows/).

>**Quando utilizar**: Utilize máquinas virtuais quando desejar controle total sobre sua infra-estrutura de aplicativo ou a migrar cargas de trabalho de aplicação de local para o Azure sem ter de fazer alterações.

>**Introdução ao**: Criar uma [VM do Linux](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) ou [Windows VM](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) do portal do Azure.

#### <a name="azure-functions-serverless"></a>Funções do Azure (sem servidor)

Em vez de se preocupar sobre a criação e gestão de toda a aplicação ou a infraestrutura para executar o código. E se pode simplesmente escrever seu código e que ele seja executado em resposta a eventos ou com base numa agenda?  [As funções do Azure](../../azure-functions/functions-overview.md) é um "sem servidor" – oferta de estilo que lhe permita escrever apenas o código que necessita. Com as funções, a execução de código é acionada por pedidos de HTTP, webhooks, eventos do serviço cloud ou numa agenda. Pode programar em sua linguagem de desenvolvimento de escolha, tal como C\#, F\#, node. js, Python ou PHP. Com a faturação com base no consumo, paga apenas o tempo que seu código é executado, e o Azure pode ser dimensionada conforme necessário.

>**Quando utilizar**: Utilize as funções do Azure quando tiver o código que é acionado por outros serviços do Azure, por eventos baseados na web, ou com base numa agenda. Também pode utilizar as funções quando não precisar da sobrecarga de um projeto hospedado concluído ou quando quer apenas paga pelo tempo que o código é executado. Para obter mais informações, consulte [descrição geral das funções do Azure](../../azure-functions/functions-overview.md).

>**Introdução ao**: Siga o tutorial de início rápido das funções para [criar a primeira função](../../azure-functions/functions-create-first-azure-function.md) do portal.

>**Experimente agora**: As funções do Azure permite-lhe executar seu código sem ter de inscrever-se numa conta do Azure. Experimente agora e [criar a sua primeira função do Azure](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita a criar, empacotar, implementar e gerir micro-serviços dimensionáveis e fiáveis. Também fornece recursos de gerenciamento de aplicativo abrangente para o aprovisionamento, implementação, monitorização, aplicação de patches e a atualizar e eliminar aplicações implementadas. Aplicações que executam num conjunto partilhado de máquinas, podem começar com pouco e aumente para centenas ou milhares de máquinas, conforme necessário.

O Service Fabric suporta WebAPI com Open Web Interface para .NET (OWIN) e ASP.NET Core. Ele disponibiliza SDKs para criar serviços no Linux em .NET Core e Java. Para saber mais sobre o Service Fabric, veja a [documentação do Service Fabric](https://docs.microsoft.com/azure/service-fabric/).

>**Quando utilizar:** Service Fabric é uma boa opção quando está criando um aplicativo ou reescrever uma aplicação existente para utilizar uma arquitetura de microsserviços. Utilize o Service Fabric quando precisar de mais controle sobre acesso direto ou para a infraestrutura subjacente.

>**Introdução:** [Criar a sua primeira aplicação do Azure Service Fabric](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Melhore as suas aplicações com serviços do Azure

Além de hospedagem de aplicativos, o Azure fornece ofertas de serviço que podem melhorar a funcionalidade, desenvolvimento e manutenção de seus aplicativos, tanto na cloud e no local.

#### <a name="hosted-storage-and-data-access"></a>Acesso de dados e de armazenamento alojado

A maioria dos aplicativos tem de armazenar dados, então, independentemente de como optar alojar a sua aplicação no Azure, considere um ou mais dos seguintes serviços de armazenamento e dados.

-   **Azure Cosmos DB**: Um serviço de base de dados com múltiplos modelos distribuída globalmente, que permite-lhe dimensionar de forma elástica o débito e armazenamento em qualquer número de regiões geográficas com um SLA com suporte abrangente. 
    >**Quando utilizar:** Quando seu aplicativo precisa de documentos, tabela ou bases de dados de gráfico, incluindo bases de dados do MongoDB, com vários modelos de consistência bem definidos. 

    >**Introdução ao**: [Criar uma aplicação web do Azure Cosmos DB](../../cosmos-db/create-sql-api-dotnet.md). Se for um desenvolvedor de MongoDB, veja [criar uma aplicação web do MongoDB com o Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

-   **O armazenamento do Azure**: Oferece armazenamento durável e altamente disponível para blobs, filas, ficheiros e outros tipos de dados não relacionais. Armazenamento fornece a base de armazenamento para as VMs.

    >**Quando utilizar**: Quando a aplicação armazena dados não relacionais, como pares chave-valor (tabelas), blobs, partilhas de ficheiros ou mensagens (filas).

    >**Introdução ao**: Escolher um dos armazenamento destes tipos: [blobs](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [tabelas](../../cosmos-db/table-storage-how-to-use-dotnet.md), [filas](../../storage/queues/storage-dotnet-how-to-use-queues.md), ou [ficheiros](../../storage/files/storage-dotnet-how-to-use-files.md).

-   **Base de dados SQL do Azure**: Uma versão do motor do Microsoft SQL Server para armazenar dados em tabela relacionais na cloud baseado no Azure. Base de dados SQL proporciona um desempenho previsível, escalabilidade sem períodos de indisponibilidade, continuidade do negócio e proteção de dados.

    >**Quando utilizar**: Quando seu aplicativo precisa de armazenamento de dados com a integridade referencial, transacional, suporte e suporte para consultas TSQL.

    >**Introdução ao**: [Criar uma base de dados SQL em minutos com o portal do Azure](../../sql-database/sql-database-get-started.md).


Pode usar [do Azure Data Factory](../../data-factory/introduction.md) mover existente dados no local para o Azure. Se não estiver pronto para mover dados para a cloud, [ligações híbridas](../../biztalk-services/integration-hybrid-connection-overview.md) no permite que os serviços do BizTalk que liga seu serviço de aplicações alojadas aplicação aos recursos no local. Também pode ligar ao armazenamento de dados do Azure e serviços das suas aplicações no local.

#### <a name="docker-support"></a>Suporte do docker

Contentores do docker, uma forma de Virtualização de SO, permitem-lhe implementar aplicações de forma mais eficiente e previsível. Uma aplicação contentorizada funciona na produção da mesma forma como em seus sistemas de desenvolvimento e teste. Pode gerir contentores utilizando as ferramentas padrão do Docker. Pode usar suas habilidades existentes e ferramentas de código aberto populares para implementar e gerir aplicações baseadas em contentores no Azure.

O Azure fornece várias formas de utilizar contentores em seus aplicativos.

-   **A extensão Docker VM do Azure**: Permite-lhe configurar a VM com ferramentas do Docker para atuar como um anfitrião do Docker.

    >**Quando utilizar**: Quando deseja gerar implementações de contentor consistente para as suas aplicações numa VM, ou quando desejar usar [Docker Compose](https://docs.docker.com/compose/overview/).

    >**Introdução ao**: [Criar um ambiente do Docker no Azure com a extensão Docker VM](../../virtual-machines/virtual-machines-linux-dockerextension.md).

-   **Serviço de contentor do Azure**: Permite-lhe criar, configurar e gerir um cluster de máquinas virtuais que estão pré-configuradas para executar aplicações em contentores. Para saber mais sobre o Container Service, veja [introdução ao Azure Container Service](../../container-service/container-service-intro.md).

    >**Quando utilizar**: Quando precisa para ambientes de prontos para produção e dimensionáveis de compilação que fornecem o agendamento adicionais e ferramentas de gestão ou quando estiver a implementar um cluster Docker Swarm.

    >**Introdução ao**: [Implementar um cluster do Container Service](../../container-service/dcos-swarm/container-service-deployment.md).

-   **Máquina docker**: Permite-lhe instalar e gerir um motor do Docker nos hosts virtuais usando comandos de uma máquina docker.

    >**Quando utilizar**: Quando tiver de protótipo rapidamente uma aplicação através da criação de um único host do Docker.

-   **Imagem personalizada do Docker para o serviço de aplicações**: Permite-lhe utilizar contentores do Docker a partir de um registo de contentor ou um contentor de cliente, ao implementar uma aplicação web no Linux.

    >**Quando utilizar**: Ao implementar uma aplicação web no Linux para uma imagem do Docker.

    >**Introdução ao**: [Utilizar uma imagem personalizada do Docker para o serviço de aplicações no Linux](../../app-service/containers/quickstart-docker-go.md).

### <a name="authentication"></a>Authentication

É fundamental, não apenas saber quem está a utilizar os seus aplicativos, mas também para impedir acesso não autorizado aos seus recursos. O Azure fornece várias formas de autenticar os clientes de aplicação.

-   **Azure Active Directory (Azure AD)**: Com base na cloud de multi-inquilino, identidades e acessos serviço Gestão da Microsoft. Pode adicionar o início de sessão único (SSO) para seus aplicativos ao integrar com o Azure AD. Pode acessar as propriedades do diretório com o Azure AD Graph API diretamente ou a Graph API do Microsoft. Pode integrar com o suporte do Azure AD para o framework de autorização de OAuth2.0 e, em seguida, abrir ID Connect com pontos finais HTTP/REST nativos e as bibliotecas de autenticação Multiplataforma do Azure AD.

    >**Quando utilizar**: Quando deseja fornecer uma experiência SSO, trabalhar com dados baseado em gráfico ou autenticar utilizadores com base em domínio.

    >**Introdução ao**: Para obter mais informações, consulte a [Guia do programador do Azure Active Directory](../../active-directory/develop/v1-overview.md).

-   **Autenticação do serviço de aplicações**: Ao escolher o serviço de aplicações para alojar a sua aplicação, obtém também suporte interno a autenticação para o Azure AD, juntamente com os fornecedores de identidade social — incluindo o Facebook, Google, Microsoft e Twitter.

    >**Quando utilizar**: Quando pretender ativar a autenticação num aplicativo de serviço de aplicações ao utilizar o Azure AD, fornecedores de identidade social, ou ambos.

    >**Introdução ao**: Para saber mais sobre a autenticação no serviço de aplicações, veja [autenticação e autorização no serviço de aplicações do Azure](../../app-service/overview-authentication-authorization.md).

Para saber mais sobre as melhores práticas de segurança no Azure, veja [padrões e práticas recomendadas de segurança do Azure](../../security/security-best-practices-and-patterns.md).

### <a name="monitoring"></a>Monitorização

Com a sua aplicação em execução no Azure, precisa de ser capaz de monitorizar o desempenho, assista para problemas e veja como os clientes estão a utilizar a aplicação. O Azure fornece várias opções de monitorização.

-   **Visual Studio Application Insights**: Um serviço de análise extensível alojado no Azure que se integra com o Visual Studio para monitorizar as aplicações web em direto. Ele fornece os dados que precisam para melhorar continuamente o desempenho e a facilidade de utilização das suas aplicações, se estiver alojadas no Azure ou não.

    >**Introdução ao**: Siga os [tutorial do Application Insights](../../azure-monitor/app/app-insights-overview.md).

-   **Azure Monitor**: Um serviço que ajuda-o a visualizar, consultar, encaminhar, arquivar e agir relativamente a métricas e registos que são gerados pela sua infraestrutura do Azure e recursos. Monitor fornece as vistas de dados que vê no portal do Azure e é uma origem única para monitorizar os recursos do Azure.
 
    >**Introdução ao**: [Introdução ao Azure Monitor](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>Integração DevOps

Se está aprovisionamento de VMs ou publicar as suas aplicações web com a integração contínua, o Azure integra-se com a maioria das ferramentas de DevOps populares. Com suporte para ferramentas como o Jenkins, GitHub, a Puppet, Chef, TeamCity, Ansible, Azure DevOps e outras pessoas, pode trabalhar com as ferramentas que já tem e maximizar a sua experiência existente.

>**Experimente agora:** [Experimente a várias das integrações de DevOps](https://azure.microsoft.com/try/devops/).

>**Introdução ao**: Para ver opções de DevOps para uma aplicação de serviço de aplicações, consulte [implementação contínua no App Service do Azure](../../app-service/deploy-continuous-deployment.md).


## <a name="azure-regions"></a>Regiões do Azure

O Azure é uma plataforma de nuvem global, que está disponível em geral em muitas regiões em todo o mundo. Quando aprovisiona um serviço, uma aplicação ou uma VM no Azure, é-lhe perguntado para selecionar uma região, que representa um datacenter específico em que a aplicação é executada ou onde os dados estão armazenados. Estas regiões correspondem a localizações específicas, que são publicadas com o [regiões do Azure](https://azure.microsoft.com/regions/) página.

### <a name="choose-the-best-region-for-your-application-and-data"></a>Escolha a região melhor para a sua aplicação e dados

Uma das vantagens de utilizar o Azure é que pode implementar as aplicações para vários centros de dados em todo o mundo. A região que escolher pode afetar o desempenho da sua aplicação. Por exemplo, é melhor escolher uma região mais próxima da maioria dos seus clientes para reduzir a latência nos pedidos de rede. Também poderá selecionar a sua região para cumprir os requisitos legais para distribuir a sua aplicação em determinados países. É sempre uma prática recomendada de armazenar dados da aplicação no mesmo datacenter ou num Data Center como perto quanto possível para o datacenter que aloja a aplicação.

### <a name="multi-region-apps"></a>Aplicações de várias regiões

Embora isso seja improvável, não é impossível para todo o datacenter para ficar offline devido a um evento como um desastre natural ou falha de Internet. É melhor prática para alojar as aplicações empresariais vitais em mais de um datacenter para fornecer a máxima disponibilidade. Utilizar várias regiões pode também reduzir a latência de usuários globais e fornecer outras oportunidades de flexibilidade quando a atualização de aplicativos.

Alguns serviços, como máquinas virtuais e serviços de aplicações, utilizam [Gestor de tráfego do Azure](../../traffic-manager/traffic-manager-overview.md) para ativar o suporte de várias regiões com ativação pós-falha entre regiões para suportar aplicações empresariais de elevada disponibilidade. Por exemplo, veja [arquitetura de referência do Azure: Executar uma aplicação web em várias regiões](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

>**Quando utilizar**: Quando tiver de empresas e aplicações de elevada disponibilidade que beneficiam de replicação e ativação pós-falha.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Como posso gerir as minhas aplicações e projetos?

O Azure fornece um conjunto avançado de experiências para criar e gerir os seus recursos do Azure, aplicações e projetos — tanto por meio de programação e, no [portal do Azure](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Interfaces de linha de comandos e o PowerShell

O Azure fornece duas formas de gerenciar seus aplicativos e serviços da linha de comando com o Bash, Terminal, da linha de comandos ou a ferramenta de linha de comando à escolha. Normalmente, pode efetuar as mesmas tarefas a partir da linha de comandos como no portal do Azure — como criar e configurar máquinas virtuais, redes virtuais, aplicações web e outros serviços.

-   [Interface de linha de comandos (CLI) do Azure](../../xplat-cli-install.md): Permite-lhe ligar a uma subscrição do Azure e várias tarefas relativamente aos recursos do Azure na linha de comando do programa.

-   [Azure PowerShell](../../powershell-install-configure.md): Fornece um conjunto de módulos com cmdlets que permitem-lhe gerir recursos do Azure com o Windows PowerShell.

### <a name="azure-portal"></a>Portal do Azure

O portal do Azure é uma aplicação baseada na web que pode utilizar para criar, gerir e remover recursos do Azure e serviços. O portal do Azure está localizado em <https://portal.azure.com>. Ele inclui um dashboard personalizável, ferramentas para gerenciar recursos do Azure e aceder às definições de subscrição e informações de faturação. Para obter mais informações, consulte a [descrição geral do portal do Azure](../../azure-portal-overview.md).

### <a name="rest-apis"></a>APIs REST

Azure baseia-se um conjunto de APIs REST que suportam o portal do Azure da interface do Usuário. A maioria dessas APIs REST também é suportada para permitem-lhe aprovisionar e gerir o seus recursos do Azure e aplicações a partir de qualquer dispositivo habilitado para Internet através de programação. Para o conjunto completo de documentação da REST API, consulte a [referência do SDK de REST do Azure](https://docs.microsoft.com/rest/api/).

### <a name="apis"></a>APIs

Para além das REST APIs, muitos serviços do Azure também permitem-lhe gerir programaticamente os recursos das suas aplicações através de SDKs do Azure específica da plataforma, incluindo SDKs para as seguintes plataformas de desenvolvimento:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/javascript/azure)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [python](https://docs.microsoft.com/python/azure)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Go](https://docs.microsoft.com/go/azure)

Serviços, como [Mobile Apps](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) e [dos serviços de multimédia do Azure](../../media-services/previous/media-services-dotnet-how-to-use.md) fornecer SDKs de cliente para permitir que aceder aos serviços da web e aplicações de cliente móvel.

### <a name="azure-resource-manager"></a>Azure Resource Manager 
    
Executar a sua aplicação no Azure provavelmente envolve o trabalho com vários serviços do Azure, que siga o mesmo ciclo de vida e pode ser considerada como uma unidade lógica. Por exemplo, uma aplicação web poderá utilizar aplicações Web, base de dados SQL, armazenamento, Cache do Azure para Redis e os serviços de rede de entrega de conteúdos do Azure. [O Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) permite que trabalhe com os recursos em seu aplicativo como um grupo. Pode implementar, atualizar ou eliminar todos os recursos numa operação única e coordenada.

Além de logicamente de agrupamento e gerir os recursos relacionados, o Azure Resource Manager inclui capacidades de implementação que lhe permite personalizar a implementação e configuração de recursos relacionados. Por exemplo, utilizando o Gestor de recursos, pode implementar e configurar uma aplicação que consiste em várias máquinas virtuais, um balanceador de carga e uma base de dados SQL do Azure como uma única unidade.

Desenvolva estas implementações com um modelo Azure Resource Manager, que é um documento em formato JSON. Modelos permitem-lhe definir uma implementação e gerir as suas aplicações ao utilizar modelos declarativos, em vez de scripts. Os modelos podem funcionar para ambientes diferentes, como de teste, preparação e produção. Por exemplo, utilizando os modelos, pode adicionar um botão para um repositório do GitHub que implementa o código no repositório de um conjunto de serviços do Azure com um único clique.

>**Quando utilizar**: Utilize modelos do Resource Manager, quando desejar uma implementação baseada em modelo para a sua aplicação que pode gerir programaticamente ao utilizar REST APIs, a CLI do Azure e o Azure PowerShell.

>**Introdução ao**: Para começar a utilizar modelos, veja [modelos Authoring Azure Resource Manager](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Compreender as contas, subscrições e faturação

Como desenvolvedores, gostamos de mergulhar no código e tente começar a utilizar mais rapidamente possível com a criação de nossos aplicativos são executados. Certamente, queremos encorajá-lo a começar a trabalhar no Azure, tão facilmente quanto possível. Para ajudar a torná-lo fácil, o Azure oferece um [avaliação gratuita](https://azure.microsoft.com/free/). Alguns serviços até tem uma funcionalidade "Experimentar gratuitamente", como [App Service do Azure](https://tryappservice.azure.com/), que não exige que até mesmo criar uma conta. Como diversão, pois é a discutir de codificação e a implementação da aplicação para o Azure, também é importante levar algum tempo para compreender como o Azure funciona do ponto de vista de contas de utilizador, subscrições e faturação.

### <a name="what-is-an-azure-account"></a>O que é uma conta do Azure?

Para poder criar ou trabalhar com uma subscrição do Azure, tem de ter uma conta do Azure. Uma conta do Azure é simplesmente uma identidade no Azure AD ou num diretório, como uma organização escolar ou profissional, que é considerado fidedigno pelo Azure AD. Se não pertence a uma organização desse tipo, pode sempre criar uma subscrição com o seu Microsoft Account, que é considerado fidedigno pelo Azure AD. Para saber mais sobre a integração no local Windows Server Active Directory com o Azure AD, veja [integrar as identidades no local com o Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md).

Cada subscrição do Azure tem uma relação de confiança com uma instância do Azure AD. Tal significa que confia nesse diretório para autenticar utilizadores, serviços e dispositivos. Várias subscrições podem confiar no mesmo diretório, mas uma subscrição apenas pode confiar num diretório. Para obter mais informações, consulte [subscrições do Azure como estão associadas com o Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Além de definir individual Azure da conta identidades, também denominadas *usuários*, também pode definir *grupos* no Azure AD. Criar grupos de utilizadores é uma boa forma de gerir o acesso aos recursos numa subscrição utilizando o controlo de acesso baseado em funções (RBAC). Para saber como criar grupos, veja [criar um grupo na pré-visualização do Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Também pode criar e gerir grupos por [com o PowerShell](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Gerir as suas subscrições

Uma subscrição é um agrupamento lógico de serviços do Azure que está ligado a uma conta do Azure. Uma única conta do Azure pode conter várias subscrições. A faturação de serviços do Azure é feita numa base por subscrição. Para obter uma lista de ofertas de subscrição disponíveis por tipo, consulte [detalhes de oferta do Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/). As subscrições do Azure têm um administrador de conta, que tem controlo total sobre a subscrição, e um administrador de serviço, que tem controlo sobre todos os serviços na subscrição. Para obter informações sobre os administradores de subscrição clássica, consulte [adicionar ou alterar os administradores de subscrição do Azure](../../billing/billing-add-change-azure-subscription-administrator.md). Além dos administradores, contas individuais podem ser concedidas detalhadas de controlo de recursos do Azure, utilizando [controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/overview.md).

#### <a name="resource-groups"></a>Grupos de recursos

Quando aprovisionar novos serviços do Azure, fazer isso numa determinada subscrição. Serviços do Azure individuais, também designados por recursos, são criados no contexto de um grupo de recursos. Grupos de recursos tornam mais fácil de implementar e gerir recursos de seu aplicativo. Um grupo de recursos deve conter todos os recursos para a sua aplicação que pretende trabalhar com o como uma unidade. Pode mover recursos entre grupos de recursos e até mesmo a subscrições diferentes. Para saber mais sobre mover os recursos, veja [mover recursos para um novo grupo de recursos ou subscrição](../../resource-group-move-resources.md).

O Explorador de recursos do Azure é uma ótima ferramenta para visualizar os recursos que já tenha criado na sua subscrição. Para obter mais informações, consulte [Use Azure Resource Explorer para ver e modificar recursos](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Conceder acesso a recursos

Ao permitir o acesso aos recursos do Azure, é sempre melhor prática para fornecer aos utilizadores com o menor privilégio necessário para executar uma determinada tarefa.

-   **Controlo de acesso baseado em funções (RBAC)**: No Azure, pode conceder acesso a contas de utilizador (principais) com um âmbito especificado: subscrição, grupo de recursos ou recursos individuais. RBAC permite-lhe implementar um conjunto de recursos num grupo de recursos e conceder permissões a um grupo ou utilizador específico. Ele também permitem-lhe limitar o acesso a apenas os recursos que pertencem ao grupo de recursos de destino. Também pode conceder acesso a um único recurso, como uma máquina virtual ou a rede virtual. Para conceder acesso, atribuir uma função para o utilizador, grupo ou principal de serviço. Existem muitas funções predefinidas, e também pode definir suas próprias funções personalizadas. Para obter mais informações, consulte [o que é o controlo de acesso baseado em funções (RBAC)?](../../role-based-access-control/overview.md).

    >**Quando utilizar**: Quando precisa de gestão de acessos detalhada para utilizadores e grupos ou quando precisa fazer um proprietário de uma subscrição de um utilizador.

    >**Introdução ao**: Para obter mais informações, consulte [gerir o acesso com RBAC e o portal do Azure](../../role-based-access-control/role-assignments-portal.md).

-   **Objetos de principal de serviço**: Além de fornecer acesso a entidades de utilizador e grupos, pode conceder o mesmo acesso a um principal de serviço.

    > **Quando utilizar**: Quando estiver programaticamente gerir recursos do Azure ou conceder acesso para aplicações. Para obter mais informações, consulte [principal de serviço e de aplicação do Active Directory criar](../../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="tags"></a>Etiquetas

O Azure Resource Manager permite-lhe atribuir etiquetas personalizadas para recursos individuais. As etiquetas que são pares chave-valor, podem ser útil quando precisa organizar os recursos de faturação ou monitorização. Etiquetas fornecem uma maneira de controlar recursos em vários grupos de recursos. Pode atribuir as etiquetas no portal, no modelo do Azure Resource Manager ou através de programação, utilizando a API REST, a CLI do Azure ou o PowerShell. Pode atribuir várias etiquetas para cada recurso. Para obter mais informações, consulte [utilizar etiquetas para organizar os recursos do Azure](../../resource-group-using-tags.md).

### <a name="billing"></a>Faturação

Na mudança do local para serviços alojados na cloud de informática, controlar e prever a utilização do serviço e os custos relacionados são preocupações significativas. É importante ser capaz de estimar o que novos recursos de custos para executar mensalmente. Também terá de conseguir projeto aspeto a faturação num determinado mês, com base nos gastos atuais.

#### <a name="get-resource-usage-data"></a>Obter dados de utilização de recursos

O Azure fornece um conjunto de APIs de REST de faturação conceder acesso ao consumo de recursos e informações de metadados para as subscrições do Azure. Estas APIs de faturação dão-lhe a capacidade de prever e gerir os custos do Azure melhor. Pode controlar e analisam os gastos em incrementos de hora a hora, criar alertas de gastos e prever futuros faturação com base em tendências de utilização atual.

>**Introdução ao**: Para saber mais sobre como utilizar as APIs de faturação, veja [descrição geral da utilização de faturação do Azure e RateCard APIs](../../billing-usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Prever futuros custos

Embora é difícil de estimar os custos antes do tempo, o Azure tem um [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/) que pode utilizar ao calcular o custo dos recursos implementados. Também pode utilizar o painel de faturação no portal e as APIs de REST de faturação de estimar os custos futuros, com base no consumo atual.

>**Introdução ao**: Ver [descrição geral da utilização de faturação do Azure e RateCard APIs](../../billing-usage-rate-card-overview.md).
