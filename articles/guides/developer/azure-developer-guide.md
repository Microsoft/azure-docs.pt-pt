---
title: Inicie guia para programadores no Azure Microsoft Docs
description: Este artigo fornece informações essenciais para os desenvolvedores que procuram começar a usar a plataforma Microsoft Azure para as suas necessidades de desenvolvimento.
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
ms.date: 11/18/2019
ms.author: glenga
ms.openlocfilehash: d74fd2e3f6b5cc090c6313aba67a1e139c713b85
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79245008"
---
# <a name="get-started-guide-for-azure-developers"></a>Guia de introdução para programadores do Azure

## <a name="what-is-azure"></a>O que é o Azure?

O Azure é uma plataforma cloud completa que pode acolher as suas aplicações existentes e dinamizar o desenvolvimento de novas aplicações. O Azure pode até melhorar as aplicações no local. O Azure integra os serviços na nuvem que precisa para desenvolver, testar, implementar e gerir as suas aplicações, aproveitando todas as eficiências da computação em nuvem.

Ao hospedar as suas aplicações no Azure, pode iniciar pequenas e facilmente escalar a sua aplicação à medida que a procura do seu cliente cresce. O Azure também oferece a fiabilidade necessária para aplicações de alta disponibilidade, incluindo mesmo falhas entre diferentes regiões. O [portal Azure](https://portal.azure.com) permite-lhe gerir facilmente todos os seus serviços Azure. Também pode gerir os seus serviços programáticamente utilizando APIs e modelos específicos de serviço.

Este guia é uma introdução à plataforma Azure para desenvolvedores de aplicações. Fornece orientação e direção que precisa para começar a construir novas aplicações em Azure ou migrar aplicações existentes para o Azure.

## <a name="where-do-i-start"></a>Por onde devo começar?

Com todos os serviços que o Azure oferece, pode ser uma tarefa intimidante descobrir quais os serviços que precisa para apoiar a sua arquitetura de solução. Esta secção destaca os serviços Azure que os desenvolvedores geralmente utilizam. Para obter uma lista de todos os serviços azure, consulte a [documentação azure](../../index.yml).

Primeiro, deve decidir como hospedar a sua candidatura em Azure. Precisa de gerir toda a sua infraestrutura como uma máquina virtual (VM). Pode utilizar as instalações de gestão da plataforma que o Azure fornece? Talvez precise de um quadro sem servidores para acolher apenas a execução de código?

A sua aplicação necessita de armazenamento em nuvem, para a qual o Azure oferece várias opções. Pode aproveitar a autenticação empresarial do Azure. Existem também ferramentas para desenvolvimento e monitorização baseados na nuvem, e a maioria dos serviços de hospedagem oferecem integração de DevOps.

Agora, vamos ver alguns dos serviços específicos que recomendamos investigar para as suas aplicações.

### <a name="application-hosting"></a>Hospedagem de candidaturas

O Azure oferece várias ofertas de cálculo baseadas em nuvem para executar a sua aplicação para que não tenha que se preocupar com os detalhes da infraestrutura. Pode facilmente aumentar ou aumentar os seus recursos à medida que o uso da sua aplicação cresce.

O Azure oferece serviços que suportam o desenvolvimento da sua aplicação e as necessidades de hospedagem. O Azure fornece infraestrutura como serviço (IaaS) para lhe dar total controlo sobre o alojamento da sua aplicação. As ofertas da Plataforma Azure como Serviço (PaaS) fornecem os serviços totalmente geridos necessários para alimentar as suas apps. Há até um verdadeiro alojamento sem servidores em Azure onde tudo o que precisas de fazer é escrever o teu código.

![Opções de hospedagem de aplicações Azure](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Serviço de Aplicações do Azure

Quando quiser o caminho mais rápido para publicar os seus projetos baseados na web, considere o Azure App Service. O Serviço de Aplicações facilita a extensão das suas aplicações web para apoiar os seus clientes móveis e publicar APIs REST facilmente consumidos. Esta plataforma fornece a autenticação utilizando fornecedores sociais, autoscalcificação baseada no tráfego, testes em produção e implementações contínuas e baseadas em contentores.

Pode criar aplicações web, back back app móvel e aplicações API.

Como os três tipos de aplicações partilham o tempo de execução do App Service, pode hospedar um website, apoiar clientes móveis e expor as suas APIs em Azure, tudo a partir do mesmo projeto ou solução. Para saber mais sobre o Serviço de Aplicações, consulte o que é web [apps azure](../../app-service/overview.md).

O Serviço de Aplicações foi concebido com devOps em mente. Suporta várias ferramentas para a publicação e implantação contínua de integração. Estas ferramentas incluem webhooks GitHub, Jenkins, Azure DevOps, TeamCity, entre outros.

Pode migrar as suas aplicações existentes para o App Service utilizando a ferramenta de [migração online.](https://appmigration.microsoft.com/)

> **Quando utilizar**: Utilize o Serviço de Aplicações quando estiver a migrar as aplicações web existentes para o Azure e quando precisar de uma plataforma de hospedagem totalmente gerida para as suas aplicações web. Também pode utilizar o Serviço de Aplicações quando necessitar de apoiar clientes móveis ou expor APIs REST com a sua aplicação.
>
> **Começar**: O Serviço de Aplicações facilita a criação e implementação da sua primeira [aplicação web,](../../app-service/app-service-web-get-started-dotnet.md) [aplicação móvel](../../app-service-mobile/app-service-mobile-ios-get-started.md)ou [aplicação API](../../app-service/app-service-web-tutorial-rest-api.md).
>
> **Experimente agora**: O App Service permite-lhe fornecer uma aplicação de curta duração para experimentar a plataforma sem ter de se inscrever numa conta Azure. Experimente a plataforma e crie a [sua app Azure App Service.](https://tryappservice.azure.com/)

#### <a name="azure-virtual-machines"></a>Máquinas Virtuais do Azure

Como fornecedor de Infraestruturas como Serviço (IaaS), o Azure permite-lhe implementar ou migrar a sua aplicação para VMs Windows ou Linux. Juntamente com a Rede Virtual Azure, a Azure Virtual Machines suporta a implementação de VMs Windows ou Linux para o Azure. Com VMs, você tem total controlo sobre a configuração da máquina. Ao utilizar VMs, é responsável por todas as correções de instalação, configuração, manutenção e sistema operativo do servidor.

Devido ao nível de controlo que tem com VMs, pode executar uma vasta gama de cargas de trabalho de servidor estivado no Azure que não se encaixam num modelo PaaS. Estas cargas de trabalho incluem servidores de base de dados, Diretório Ativo do Servidor do Windows e Microsoft SharePoint. Para mais informações, consulte a documentação das Máquinas Virtuais para [linux](/azure/virtual-machines/linux/) ou [Windows](/azure/virtual-machines/windows/).

> **Quando utilizar:** Utilize máquinas virtuais quando pretender o controlo total da sua infraestrutura de aplicação ou para migrar as cargas de trabalho de aplicação no local para o Azure sem ter de fazer alterações.
>
> **Início**: Crie um [VM Linux](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) ou [Windows VM](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) a partir do portal Azure.

#### <a name="azure-functions-serverless"></a>Funções Azure (servidor)

Em vez de se preocupar em construir e gerir toda uma aplicação ou a infraestrutura para executar o seu código, e se pudesse simplesmente escrever o seu código e executá-lo em resposta a eventos ou a um horário?  [O Azure Functions](../../azure-functions/functions-overview.md) é uma oferta de estilo "servidor" que permite escrever apenas o código de que precisa. Com funções, pode desencadear a execução de código com pedidos HTTP, webhooks, eventos de serviço na nuvem ou em horário. Você pode codificar na sua linguagem\#de\#desenvolvimento de eleição, tais como C, F, Node.js, Python ou PHP. Com a faturação baseada no consumo, paga-se apenas pelo tempo que o seu código executa, e as escalas Azure, se necessário.

> **Quando utilizar**: Utilize funções Azure quando tiver um código que é acionado por outros serviços Azure, por eventos baseados na web ou numa programação. Também pode utilizar Funções quando não precisa da sobrecarga de um projeto completo hospedado ou quando apenas quer pagar pelo tempo em que o seu código funciona. Para saber mais, consulte a visão geral das [Funções Azure.](../../azure-functions/functions-overview.md)
>
> **Comece**a : Siga o tutorial de arranque rápido das funções para criar a [sua primeira função](../../azure-functions/functions-create-first-azure-function.md) a partir do portal.
>
> **Experimente agora**: As Funções Azure permitem executar o seu código sem ter de se inscrever para uma conta Azure. Experimente agora e [crie a sua primeira Função Azure.](https://tryappservice.azure.com/)

#### <a name="azure-service-fabric"></a>Azure Service Fabric

O Azure Service Fabric é uma plataforma de sistemas distribuídos. Esta plataforma facilita a construção, embalagem, implantação e gestão de microserviços escaláveis e fiáveis. Também fornece capacidades abrangentes de gestão de aplicações, tais como:

* Aprovisionamento
* Implantação
* Monitorização
* Upgrade/remendação
* Apagando

As aplicações, que funcionam numa piscina partilhada de máquinas, podem iniciar pequenas e dimensionadas para centenas ou milhares de máquinas, se necessário.

O Tecido de Serviço suporta webAPI com interface web aberta para .NET (OWIN) e ASP.NET Core. Fornece SDKs para serviços de construção em Linux tanto em .NET Core como java. Para saber mais sobre o Tecido de Serviço, consulte a [documentação do Tecido de Serviço.](https://docs.microsoft.com/azure/service-fabric/)

> **Quando utilizar:** O Service Fabric é uma boa escolha quando se está a criar uma aplicação ou a reescrever uma aplicação existente para utilizar uma arquitetura de microserviços. Use o Tecido de Serviço quando necessitar de mais controlo sobre ou acesso direto à infraestrutura subjacente.
>
> **Começar:** [Crie a sua primeira aplicação Azure Service Fabric](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Melhore as suas aplicações com serviços Azure

Juntamente com o alojamento de aplicações, o Azure oferece ofertas de serviço que podem melhorar a funcionalidade. O Azure também pode melhorar o desenvolvimento e manutenção das suas aplicações, tanto na nuvem como no local.

#### <a name="hosted-storage-and-data-access"></a>Armazenamento hospedado e acesso a dados

A maioria das aplicações deve armazenar dados, por isso, seja qual for a decisão de hospedar a sua aplicação no Azure, considere um ou mais dos seguintes serviços de armazenamento e dados.

- **Azure Cosmos DB**: Um serviço de base de dados multimodelo distribuído globalmente. Esta base de dados permite-lhe escalar elástico sucintamente a entrada e armazenamento em várias regiões geográficas com um SLA abrangente.

  > **Quando utilizar:** Quando a sua aplicação precisa de documentos, tabelas ou bases de dados de gráficos, incluindo bases de dados mongoDB, com múltiplos modelos de consistência bem definidos.
  >
  > **Start start**: [Build a Azure Cosmos DB web app](../../cosmos-db/create-sql-api-dotnet.md). Se você é um desenvolvedor de MongoDB, consulte [Construir uma aplicação web MongoDB com Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

- **Armazenamento Azure**: Oferece armazenamento durável e altamente disponível para bolhas, filas, ficheiros e outros tipos de dados não-relacionais. O armazenamento fornece a base de armazenamento para VMs.

  > **Quando utilizar**: Quando a sua aplicação armazena dados não relacionais, tais como pares de valor-chave (tabelas), blobs, partilhas de ficheiros ou mensagens (filas).
  >
  > **Início**: Escolha entre um destes tipos de armazenamento: [bolhas, mesas,](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) [filas,](../../storage/queues/storage-dotnet-how-to-use-queues.md)ou [ficheiros](../../storage/files/storage-dotnet-how-to-use-files.md). [tables](../../cosmos-db/table-storage-how-to-use-dotnet.md)

- **Base de Dados Azure SQL**: Uma versão baseada em Azure do motor Microsoft SQL Server para armazenar dados tabulares relacionais na nuvem. A Base de Dados SQL proporciona um desempenho previsível, escalabilidade sem tempo de inatividade, continuidade do negócio e proteção de dados.

  > **Quando utilizar**: Quando a sua aplicação necessita de armazenamento de dados com integridade referencial, suporte transacional e suporte para consultas TSQL.
  >
  > **Início**: [Crie uma base de dados SQL em minutos utilizando o portal Azure](../../sql-database/sql-database-get-started.md).


Pode utilizar a [Azure Data Factory](../../data-factory/introduction.md) para mover os dados existentes no local para o Azure. Se não estiver pronto para mover dados para a nuvem, as [Ligações Híbridas](../../app-service/app-service-hybrid-connections.md) no Serviço de Aplicações Azure permitem ligar a sua app Service hospedada aos recursos no local. Também pode ligar-se aos serviços de dados e armazenamento do Azure a partir das suas aplicações no local.

#### <a name="docker-support"></a>Apoio ao Estivador

Os contentores Docker, uma forma de virtualização de Os, permitem-lhe implementar aplicações de uma forma mais eficiente e previsível. Uma aplicação contentorizada funciona em produção da mesma forma que nos seus sistemas de desenvolvimento e teste. Pode gerir os recipientes utilizando ferramentas padrão do Docker. Você pode usar suas habilidades existentes e ferramentas populares de código aberto para implementar e gerir aplicações baseadas em recipientes no Azure.

A Azure fornece várias formas de utilizar contentores nas suas aplicações.

- **Extensão VM Azure Docker**: Permite configurar o seu VM com ferramentas Docker para agir como hospedeiro docker.

  > **Quando utilizar**: Quando pretender gerar implementações consistentes de contentores para as suas aplicações num VM, ou quando pretender utilizar [o Docker Compose](https://docs.docker.com/compose/overview/).
  >
  > **Início**: [Criar um ambiente Docker em Azure utilizando a extensão Docker VM](../../virtual-machines/virtual-machines-linux-dockerextension.md).

- **Serviço Azure Kubernetes**: Permite-lhe criar, configurar e gerir um conjunto de máquinas virtuais que são reconfiguradas para executar aplicações contentorizadas. Para saber mais sobre o Serviço Azure Kubernetes, consulte a introdução do [Serviço Azure Kubernetes.](../../aks/intro-kubernetes.md)

  > **Quando utilizar**: Quando necessitar de construir ambientes prontos para a produção e escaláveis que forneçam ferramentas adicionais de agendamento e gestão, ou quando estiver a implantar um cluster Docker Swarm.
  >
  > **Início**: Implementar um cluster de [serviço Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md).

- **Docker Machine**: Permite-lhe instalar e gerir um Motor Docker em anfitriões virtuais utilizando comandos de máquina de estiva.

  >**Quando utilizar**: Quando precisa de protótipo rapidamente de uma aplicação criando um único anfitrião Docker.

- **Imagem personalizada do Docker para o Serviço**de Aplicações : Permite-lhe utilizar recipientes Docker a partir de um registo de contentores ou de um contentor de cliente quando implementa uma aplicação web no Linux.

  > **Quando utilizar**: Ao implementar uma aplicação web no Linux para uma imagem do Docker.
  >
  > **Início**: Use uma imagem personalizada do Docker para o Serviço de [Aplicações no Linux](../../app-service/containers/quickstart-docker-go.md).

### <a name="authentication"></a>Autenticação

É crucial não só saber quem está a usar as suas aplicações, mas também impedir o acesso não autorizado aos seus recursos. A Azure fornece várias formas de autenticar os seus clientes de aplicações.

- **Azure Ative Directory (Azure AD)**: O multiinquilino da Microsoft, serviço de gestão de identidade e acesso baseado na nuvem. Pode adicionar um único sinal nas suas aplicações, integrando-se com o Azure AD. Pode aceder diretamente às propriedades do diretório utilizando a API do Gráfico AD Azure ou a API do Microsoft Graph. Pode integrar-se com o suporte da AD Azure para o quadro de autorização OAuth2.0 e Open ID Connect utilizando pontos finais http/REST nativos e as bibliotecas de autenticação adautenticação multiplataforma Azure AD.

  > **Quando utilizar**: Quando pretender fornecer uma experiência SSO, trabalhe com dados baseados em Gráficos ou autenticar utilizadores baseados em domínios.
  >
  > **Início**: Para saber mais, consulte o [guia do programador azure Ative Directory.](../../active-directory/develop/v2-overview.md)

- **Autenticação do Serviço**de Aplicações : Quando escolhe o App Service para hospedar a sua aplicação, também obtém suporte de autenticação incorporado para o Azure AD, juntamente com fornecedores de identidade social — incluindo Facebook, Google, Microsoft e Twitter.

  > **Quando utilizar**: Quando pretender ativar a autenticação numa aplicação do App Service utilizando o Azure AD, fornecedores de identidade social ou ambos.
  >
  > **Início**: Para saber mais sobre a autenticação no Serviço de Aplicações, consulte autenticação e autorização no Serviço de [Aplicações Azure](../../app-service/overview-authentication-authorization.md).

Para saber mais sobre as melhores práticas de segurança em Azure, consulte as [melhores práticas e padrões de segurança azure.](../../security/fundamentals/best-practices-and-patterns.md)

### <a name="monitoring"></a>Monitorização

Com a sua aplicação em funcionamento no Azure, é necessário monitorizar o desempenho, ter cuidado com os problemas e ver como os clientes estão a usar a sua aplicação. O Azure oferece várias opções de monitorização.

-   **Application Insights**: Um serviço de análise extensível hospedado em Azure que se integra com o Visual Studio para monitorizar as suas aplicações web ao vivo. Dá-lhe os dados de que necessita para melhorar o desempenho e a usabilidade das suas apps continuamente. Esta melhoria ocorre se acolhe ou não as suas aplicações no Azure.

    >**Começar**: Siga o tutorial de Insights de [Aplicação](../../azure-monitor/app/app-insights-overview.md).

-   **Azure Monitor**: Um serviço que o ajuda a visualizar, consultar, fazer percurso, arquivar e atuar nas métricas e registos que gera com a sua infraestrutura e recursos Azure. O Monitor é uma única fonte para monitorizar os recursos do Azure e fornece as visualizações de dados que vê no portal Azure.

    >**Início**: [Começar com o Monitor Azure](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>Integração de DevOps

Quer esteja a fornecer VMs ou a publicar as suas aplicações web com integração contínua, o Azure integra-se com a maioria das ferramentas populares da DevOps. Pode trabalhar com as ferramentas que já tem e maximizar a sua experiência existente com suporte a ferramentas como:

* Jenkins
* GitHub
* Puppet
* Chef
* TeamCity
* Ansible
* DevOps do Azure

> **Começar**: Para ver as opções de DevOps para uma aplicação de Serviço de Aplicações, consulte a Implantação Contínua para o Serviço de [Aplicações Azure](../../app-service/deploy-continuous-deployment.md).
>
> **Experimente agora:** [Experimente várias das integrações de DevOps.](https://azure.microsoft.com/try/devops/)


## <a name="azure-regions"></a>Regiões do Azure

O Azure é uma plataforma global de nuvem que está geralmente disponível em muitas regiões do mundo. Ao fornecer um serviço, aplicação ou VM em Azure, é-lhe pedido que selecione uma região. Esta região representa um datacenter específico onde a sua aplicação é executado ou onde os seus dados são armazenados. Estas regiões correspondem a locais específicos, que são publicados na página das regiões do [Azure.](https://azure.microsoft.com/regions/)

### <a name="choose-the-best-region-for-your-application-and-data"></a>Escolha a melhor região para a sua aplicação e dados

Um dos benefícios da utilização do Azure é que pode implementar as suas aplicações em vários centros de dados em todo o mundo. A região que escolher pode afetar o desempenho da sua candidatura. Por exemplo, é melhor escolher uma região mais próxima da maioria dos seus clientes para reduzir a latência nos pedidos de rede. Também pode querer selecionar a sua região para cumprir os requisitos legais para distribuir a sua app em determinados países/regiões. É sempre uma boa prática armazenar dados de aplicações no mesmo datacenter ou num datacenter o mais próximo possível do datacenter que está hospedando a sua aplicação.

### <a name="multi-region-apps"></a>Aplicativos multi-regiões

Embora improvável, não é impossível que um centro de dados inteiro fique offline por causa de um evento como um desastre natural ou falha na Internet. É uma boa prática acolher aplicações empresariais vitais em mais de um datacenter para fornecer a máxima disponibilidade. A utilização de várias regiões também pode reduzir a latência para os utilizadores globais e proporcionar oportunidades adicionais de flexibilidade na atualização das aplicações.

Alguns serviços, como a Virtual Machine e os App Services, utilizam o Gestor de [Tráfego Azure](../../traffic-manager/traffic-manager-overview.md) para permitir o apoio de várias regiões com falhas entre regiões para apoiar aplicações empresariais de alta disponibilidade. Por exemplo, consulte a arquitetura de [referência Azure: Executar uma aplicação web em várias regiões](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

>**Quando utilizar**: Quando tem aplicações empresariais e de alta disponibilidade que beneficiam de falhas e replicação.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Como posso gerir as minhas aplicações e projetos?

O Azure oferece um conjunto rico de experiências para que possa criar e gerir os seus recursos, aplicações e projetos Azure — tanto programáticamente como no [portal Azure.](https://portal.azure.com/)

### <a name="command-line-interfaces-and-powershell"></a>Interfaces de linha de comando e PowerShell

A Azure fornece duas formas de gerir as suas aplicações e serviços a partir da linha de comando. Pode utilizar ferramentas como Bash, Terminal, o pedido de comando ou a sua ferramenta de escolha da linha de comando. Normalmente, pode fazer as mesmas tarefas a partir da linha de comando que no portal Azure — como criar e configurar máquinas virtuais, redes virtuais, aplicações web e outros serviços.

-   Interface de [Linha de Comando Azure (CLI)](../../xplat-cli-install.md): Permite ligar-se a uma subscrição Azure e programar várias tarefas contra recursos Azure a partir da linha de comando.

-   [Azure PowerShell](../../powershell-install-configure.md): Fornece um conjunto de módulos com cmdlets que lhe permitem gerir os recursos do Azure utilizando o Windows PowerShell.

### <a name="azure-portal"></a>Portal do Azure

O [portal Azure](https://portal.azure.com) é uma aplicação baseada na web. Pode utilizar o portal Azure para criar, gerir e remover recursos e serviços Azure. Inclui:

* Um dashboard configurável
* Ferramentas de gestão de recursos Azure
* Acesso a definições de subscrição e informação de faturação. Para mais informações, consulte a visão geral do [portal Azure.](../../azure-portal-overview.md)

### <a name="rest-apis"></a>APIs REST

O Azure é construído sobre um conjunto de APIs REST que suportam o portal Azure UI. A maioria destas APIs REST também são suportadas para permitir que você disponibilize programáticamente e gere os seus recursos e aplicações Azure a partir de qualquer dispositivo via Internet. Para obter o conjunto completo de documentação REST API, consulte a [referência Azure REST SDK](https://docs.microsoft.com/rest/api/).

### <a name="apis"></a>APIs

Juntamente com as APIs REST, muitos serviços Azure também permitem gerir programáticamente os recursos a partir das suas aplicações, utilizando SDKs Azure específicos da plataforma, incluindo SDKs para as seguintes plataformas de desenvolvimento:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/azure/javascript/)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](/azure/python/)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Ir](https://docs.microsoft.com/azure/go)

Serviços como [Mobile Apps](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) e [Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) fornecem SDKs do lado do cliente para permitir o acesso a serviços de aplicações de clientes web e móveis.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Executar a sua aplicação no Azure provavelmente envolve trabalhar com vários serviços Azure. Estes serviços seguem o mesmo ciclo de vida e podem ser considerados como uma unidade lógica. Por exemplo, uma aplicação web pode usar Web Apps, SQL Database, Storage, Azure Cache para Redis e serviços da Rede de Entrega de Conteúdos Azure. [O Azure Resource Manager](../../azure-resource-manager/management/overview.md) permite-lhe trabalhar com os recursos da sua aplicação em grupo. Pode implantar, atualizar ou eliminar todos os recursos numa única operação coordenada.

Juntamente com o agrupamento lógico e a gestão de recursos relacionados, o Azure Resource Manager inclui capacidades de implementação que lhe permitem personalizar a implementação e configuração de recursos relacionados. Por exemplo, pode utilizar o Gestor de Recursos implementar e configurar uma aplicação. Esta aplicação pode ser constituída por múltiplas máquinas virtuais, um equilibrante de carga e uma base de dados Azure SQL como uma única unidade.

Desenvolve estas implementações utilizando um modelo de Gestor de Recursos Azure, que é um documento formado pela JSON. Os modelos permitem definir uma implementação e gerir as suas aplicações utilizando modelos declarativos, em vez de scripts. Os seus modelos podem funcionar para diferentes ambientes, tais como testes, encenação e produção. Por exemplo, pode usar modelos para adicionar um botão a um repo GitHub que implementa o código no repo a um conjunto de serviços Azure com um único clique.

> **Quando utilizar**: Utilize os modelos do Gestor de Recursos quando pretender uma implementação baseada no modelo para a sua aplicação que possa gerir programáticamente utilizando APIs REST, o Azure CLI e o Azure PowerShell.
>
> **Começar**: Para começar a usar modelos, consulte [os modelos de Gestor de Recursos Do Azure.](../../resource-group-authoring-templates.md)

## <a name="understanding-accounts-subscriptions-and-billing"></a>Compreender as contas, subscrições e faturação

Como desenvolvedores, gostamos de mergulhar no código e tentar começar o mais rápido possível com a execução das nossas aplicações. Queremos certamente encorajá-lo a começar a trabalhar em Azure o mais facilmente possível. Para ajudar a facilitar a vida, o Azure oferece um [teste gratuito.](https://azure.microsoft.com/free/) Alguns serviços têm até uma funcionalidade "Experimente gratuitamente", como o [Azure App Service](https://tryappservice.azure.com/), que não requer sequer criar uma conta. Por mais divertido que seja mergulhar na codificação e implantação da sua aplicação para o Azure, também é importante ter algum tempo para perceber como funciona o Azure. Especificamente, deve entender como funciona do ponto de vista das contas de utilizador, subscrições e faturação.

### <a name="what-is-an-azure-account"></a>O que é uma conta do Azure?

Para criar ou trabalhar com uma subscrição Azure, você deve ter uma conta Azure. Uma conta Azure é simplesmente uma identidade em Azure AD ou num diretório, como um trabalho ou organização escolar, que a Azure AD confia. Se não pertencer a tal organização, pode sempre criar uma subscrição utilizando a sua Conta Microsoft, que é de confiança do Azure AD. Para saber mais sobre a integração no local do Windows Server Ative Directory com o Azure AD, consulte a integração das suas identidades no local com o [Diretório Ativo azure](../../active-directory/hybrid/whatis-hybrid-identity.md).

Cada subscrição do Azure tem uma relação de confiança com uma instância do Azure AD. Tal significa que confia nesse diretório para autenticar utilizadores, serviços e dispositivos. Várias subscrições podem confiar no mesmo diretório, mas uma subscrição apenas pode confiar num diretório. Para saber mais, veja como as [subscrições do Azure estão associadas ao Diretório Ativo azure.](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

Além de definir identidades individuais da conta Azure, também chamadas *de utilizadores,* pode definir *grupos* em Azure AD. A criação de grupos de utilizadores é uma boa forma de gerir o acesso aos recursos numa subscrição utilizando o controlo de acesso baseado em papéis (RBAC). Para aprender a criar grupos, consulte [Criar um grupo na pré-visualização do Diretório Ativo Azure](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Também pode criar e gerir grupos [utilizando o PowerShell](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Gerir as subscrições

Uma subscrição é um agrupamento lógico de serviços Azure que está ligado a uma conta Azure. Uma única conta Azure pode conter várias subscrições. A faturação dos serviços Azure é feita por subscrição. Para obter uma lista das ofertas de subscrição disponíveis por tipo, consulte o [Microsoft Azure Offer Details](https://azure.microsoft.com/support/legal/offer-details/). As subscrições do Azure têm um Administrador de Conta que tem total controlo sobre a subscrição. Eles também têm um Administrador de Serviço que tem controlo sobre todos os serviços na subscrição. Para obter informações sobre administradores de subscrição clássicos, consulte Adicionar ou alterar administradores de [subscrição do Azure.](../../cost-management-billing/manage/add-change-subscription-administrator.md) As contas individuais podem ser concedidas com um controlo detalhado dos recursos do Azure utilizando [o controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/overview.md).

#### <a name="resource-groups"></a>Grupos de recursos

Quando presta novos serviços Azure, fá-lo numa determinada subscrição. Os serviços Individuais Azure, que também são chamados de recursos, são criados no contexto de um grupo de recursos. Os grupos de recursos facilitam a implantação e gestão dos recursos da sua aplicação. Um grupo de recursos deve conter todos os recursos para a sua aplicação com que pretende trabalhar como unidade. Pode mover recursos entre grupos de recursos e até para diferentes subscrições. Para aprender sobre a movimentação de recursos, consulte [o Move recursos para um novo grupo de recursos ou subscrição.](../../azure-resource-manager/management/move-resource-group-and-subscription.md)

O Azure Resource Explorer é uma excelente ferramenta para visualizar os recursos que já criou na sua subscrição. Para saber mais, consulte [Use Azure Resource Explorer para visualizar e modificar recursos.](../../resource-manager-resource-explorer.md)

#### <a name="grant-access-to-resources"></a>Conceder acesso aos recursos

Quando permite o acesso aos recursos do Azure, é sempre uma boa prática proporcionar aos utilizadores o menor privilégio que é necessário para fazer uma determinada tarefa.

- **Controlo de acesso baseado em funções (RBAC)**: No Azure, pode conceder acesso às contas de utilizador (principais) num âmbito especificado: subscrição, grupo de recursos ou recursos individuais. O RBAC permite-lhe implantar recursos num grupo de recursos e conceder permissões a um utilizador ou grupo específico. Permite também limitar o acesso apenas aos recursos que pertencem ao grupo de recursos-alvo. Também pode conceder acesso a um único recurso, como uma máquina virtual ou rede virtual. Para conceder acesso, atribui uma função ao utilizador, grupo ou diretor de serviço. Existem muitos papéis predefinidos, e também pode definir os seus próprios papéis personalizados. Para saber mais, veja [O que é o controlo de acesso baseado em papéis (RBAC)?](../../role-based-access-control/overview.md)

  > **Quando utilizar**: Quando necessitar de uma gestão de acesso de grãos finos para utilizadores e grupos ou quando necessita de fazer de um utilizador um utilizador de uma subscrição.
  >
  > **Comece**: Para saber mais, consulte [Gerir o acesso utilizando o RBAC e o portal Azure](../../role-based-access-control/role-assignments-portal.md).

- **Objetos principais**de serviço : Juntamente com o acesso aos principais e grupos do utilizador, pode conceder o mesmo acesso a um diretor de serviço.

  > **Quando utilizar**: Quando estiver a gerir programaticamente os recursos do Azure ou a conceder acesso a candidaturas. Para mais informações, consulte [Create Ative Directy e diretor de serviço.](../../active-directory/develop/howto-create-service-principal-portal.md)

#### <a name="tags"></a>Etiquetas

O Azure Resource Manager permite-lhe atribuir etiquetas personalizadas a recursos individuais. As etiquetas, que são pares de valor-chave, podem ser úteis quando você precisa organizar recursos para faturação ou monitorização. As etiquetas fornecem-lhe uma forma de rastrear recursos em vários grupos de recursos. Pode atribuir etiquetas das seguintes formas:

* No portal
* No modelo de Gestor de Recursos Azure
* Utilizar a API REST
* Com a CLI do Azure
* Com o PowerShell

Pode atribuir várias etiquetas a cada recurso. Para saber mais, consulte [Usar etiquetas para organizar os seus recursos Azure.](../../resource-group-using-tags.md)

### <a name="billing"></a>Faturação

Na passagem da computação no local para serviços hospedados na nuvem, o acompanhamento e a estimativa da utilização do serviço e os custos conexos são preocupações significativas. É importante estimar que novos recursos custam a ser executados mensalmente. Também pode projetar como a faturação parece para um determinado mês com base nas despesas correntes.

#### <a name="get-resource-usage-data"></a>Obtenha dados de utilização de recursos

O Azure fornece um conjunto de APIs de Billing REST que dão acesso ao consumo de recursos e informações de metadados para assinaturas Azure. Estas APIs de faturação dão-lhe a capacidade de prever melhor e gerir os custos do Azure. Pode rastrear e analisar os gastos em incrementos de hora e criar alertas de gastos. Também pode prever futurafaturação com base nas tendências de utilização atuais.

>**Início**: Para saber mais sobre a utilização das APIs de faturação, consulte a visão geral da [Faturação de Azure e da RateCard APIs](../../cost-management-billing/manage/usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Prever custos futuros

Embora seja um desafio estimar os custos com antecedência, o Azure tem ferramentas que podem ajudar. Tem uma [calculadora](https://azure.microsoft.com/pricing/calculator/) de preços para ajudar a estimar o custo dos recursos implantados. Também pode utilizar os recursos de Faturação no portal e as APIs de Billing REST para estimar os custos futuros, com base no consumo atual.

>**Início**: Consulte a visão geral da [Faturação Azure e da RateCard APIs](../../cost-management-billing/manage/usage-rate-card-overview.md).
