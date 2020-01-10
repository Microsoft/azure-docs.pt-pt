---
title: Guia de introdução para desenvolvedores no Azure | Microsoft Docs
description: Este artigo fornece informações essenciais para os desenvolvedores que buscam começar a usar a plataforma de Microsoft Azure para suas necessidades de desenvolvimento.
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
ms.openlocfilehash: 0df55f4ce790d6ea38f44ce0ca6dab43085c3455
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770922"
---
# <a name="get-started-guide-for-azure-developers"></a>Guia de introdução para programadores do Azure

## <a name="what-is-azure"></a>O que é o Azure?

O Azure é uma plataforma de nuvem completa que pode hospedar seus aplicativos existentes e simplificar o desenvolvimento de novos aplicativos. O Azure pode até mesmo aprimorar os aplicativos locais. O Azure integra os serviços de nuvem que você precisa para desenvolver, testar, implantar e gerenciar seus aplicativos, tudo isso ao mesmo tempo em que aproveita as eficiências da computação em nuvem.

Ao hospedar seus aplicativos no Azure, você pode começar pequena e dimensionar facilmente seu aplicativo à medida que a demanda do cliente cresce. O Azure também oferece a confiabilidade necessária para aplicativos de alta disponibilidade, mesmo incluindo o failover entre regiões diferentes. O [portal do Azure](https://portal.azure.com) permite que você gerencie facilmente todos os seus serviços do Azure. Você também pode gerenciar seus serviços programaticamente usando APIs e modelos específicos do serviço.

Este guia é uma introdução à plataforma Azure para desenvolvedores de aplicativos. Ele fornece orientação e direção de que você precisa para começar a criar novos aplicativos no Azure ou migrar aplicativos existentes para o Azure.

## <a name="where-do-i-start"></a>Por onde devo começar?

Com todos os serviços que o Azure oferece, pode ser uma tarefa intimidante descobrir quais serviços você precisa para dar suporte à sua arquitetura de solução. Esta seção destaca os serviços do Azure que os desenvolvedores normalmente usam. Para obter uma lista de todos os serviços do Azure, consulte a [documentação do Azure](../../index.md).

Primeiro, você deve decidir como hospedar seu aplicativo no Azure. Você precisa gerenciar toda a sua infraestrutura como uma VM (máquina virtual). Você pode usar os recursos de gerenciamento de plataforma que o Azure fornece? Talvez você precise de uma estrutura sem servidor para hospedar somente a execução de código?

Seu aplicativo precisa de armazenamento em nuvem, para o qual o Azure fornece várias opções. Você pode aproveitar a autenticação corporativa do Azure. Também há ferramentas para desenvolvimento e monitoramento baseados em nuvem, e a maioria dos serviços de hospedagem oferece integração DevOps.

Agora, vamos examinar alguns dos serviços específicos que recomendamos investigar para seus aplicativos.

### <a name="application-hosting"></a>Hospedagem de aplicativos

O Azure fornece várias ofertas de computação baseadas em nuvem para executar seu aplicativo para que você não precise se preocupar com os detalhes da infraestrutura. Você pode facilmente escalar ou escalar verticalmente seus recursos conforme o uso do aplicativo cresce.

O Azure oferece serviços que dão suporte ao desenvolvimento de aplicativos e às necessidades de hospedagem. O Azure fornece IaaS (infraestrutura como serviço) para oferecer a você controle total sobre a hospedagem do aplicativo. As ofertas de PaaS (plataforma como serviço) do Azure fornecem os serviços totalmente gerenciados necessários para capacitar seus aplicativos. Há até mesmo uma verdadeira hospedagem sem servidor no Azure, onde tudo o que você precisa fazer é escrever seu código.

![Opções de Hospedagem de aplicativo do Azure](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>App Service do Azure 

Quando você quiser o caminho mais rápido para publicar seus projetos baseados na Web, considere Azure App serviço. O serviço de aplicativo facilita estender seus aplicativos Web para dar suporte a seus clientes móveis e publicar facilmente as APIs REST consumidas. Essa plataforma fornece autenticação usando provedores sociais, dimensionamento automático baseado em tráfego, teste em produção e implantações baseadas em contêiner e contínuas.

Você pode criar aplicativos Web, back-ends de aplicativo móvel e aplicativos de API.

Como todos os três tipos de aplicativos compartilham o tempo de execução do serviço de aplicativo, você pode hospedar um site, dar suporte a clientes móveis e expor suas APIs no Azure, tudo do mesmo projeto ou solução. Para saber mais sobre o serviço de aplicativo, confira [o que são os aplicativos Web do Azure](../../app-service/overview.md).

O serviço de aplicativo foi projetado com DevOps em mente. Ele dá suporte a várias ferramentas para publicação e implantações de integração contínua. Essas ferramentas incluem WebHooks do GitHub, Jenkins, Azure DevOps, TeamCity e outros.

Você pode migrar seus aplicativos existentes para o serviço de aplicativo usando a [ferramenta de migração online](https://www.migratetoazure.net/).

> **Quando usar**: Use o serviço de aplicativo quando estiver migrando aplicativos Web existentes para o Azure e quando precisar de uma plataforma de hospedagem totalmente gerenciada para seus aplicativos Web. Você também pode usar o serviço de aplicativo quando precisar dar suporte a clientes móveis ou expor APIs REST com seu aplicativo.
> 
> **Introdução**: o serviço de aplicativo torna fácil criar e implantar seu primeiro aplicativo [Web](../../app-service/app-service-web-get-started-dotnet.md), [aplicativo móvel](../../app-service-mobile/app-service-mobile-ios-get-started.md)ou aplicativo de [API](../../app-service/app-service-web-tutorial-rest-api.md).
> 
> **Experimente agora**: o serviço de aplicativo permite que você provisione um aplicativo de curta duração para experimentar a plataforma sem precisar se inscrever em uma conta do Azure. Experimente a plataforma e [crie seu aplicativo de serviço de Azure app](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Máquinas Virtuais do Azure

Como um provedor de IaaS (infraestrutura como serviço), o Azure permite implantar ou migrar seu aplicativo para VMs Windows ou Linux. Junto com a rede virtual do Azure, as máquinas virtuais do Azure dão suporte à implantação de VMs do Windows ou Linux no Azure. Com as VMs, você tem controle total sobre a configuração do computador. Ao usar VMs, você é responsável por toda a instalação de software do servidor, configuração, manutenção e patches do sistema operacional.

Devido ao nível de controle que você tem com VMs, você pode executar uma ampla gama de cargas de trabalho de servidor no Azure que não se ajustam a um modelo de PaaS. Essas cargas de trabalho incluem servidores de banco de dados, Windows Server Active Directory e Microsoft SharePoint. Para obter mais informações, consulte a documentação das máquinas virtuais para [Linux](/azure/virtual-machines/linux/) ou [Windows](/azure/virtual-machines/windows/).

> **Quando usar**: use máquinas virtuais quando desejar ter controle total sobre a infraestrutura do aplicativo ou para migrar cargas de trabalho do aplicativo local para o Azure sem precisar fazer alterações.
> 
> **Introdução**: criar uma VM do [Linux](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) ou [VM do Windows](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) a partir da portal do Azure.

#### <a name="azure-functions-serverless"></a>Azure Functions (sem servidor)

Em vez de se preocupar em criar e gerenciar um aplicativo inteiro ou a infraestrutura para executar seu código, e se você pudesse apenas escrever seu código e executá-lo em resposta a eventos ou agendar?  [Azure Functions](../../azure-functions/functions-overview.md) é uma oferta de estilo "sem servidor" que permite escrever apenas o código de que você precisa. Com o functions, você pode disparar a execução de código com solicitações HTTP, WebHooks, eventos de serviço de nuvem ou em um agendamento. Você pode codificar na linguagem de desenvolvimento de sua escolha, como C\#, F\#, Node. js, Python ou PHP. Com a cobrança baseada em consumo, você paga apenas pelo tempo que seu código é executado e o Azure é dimensionado conforme necessário.

> **Quando usar**: Use Azure Functions quando tiver código disparado por outros serviços do Azure, por eventos baseados na Web ou em um agendamento. Você também pode usar funções quando não precisar da sobrecarga de um projeto hospedado completo ou quando quiser apenas pagar pelo tempo em que o código é executado. Para saber mais, confira [Azure Functions visão geral](../../azure-functions/functions-overview.md).
> 
> **Introdução**: Siga o tutorial de início rápido das funções para [criar sua primeira função](../../azure-functions/functions-create-first-azure-function.md) no Portal.
> 
> **Experimente agora**: Azure Functions permite que você execute seu código sem precisar se inscrever para uma conta do Azure. Experimente agora em e [Crie sua primeira função do Azure](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

O Azure Service Fabric é uma plataforma de sistemas distribuídos. Essa plataforma facilita a criação, o empacotamento, a implantação e o gerenciamento de microserviços escalonáveis e confiáveis. Ele também fornece recursos abrangentes de gerenciamento de aplicativos, como: 

* Aprovisionamento
* Deploying 
* Monitorização
* Atualização/aplicação de patch
* A eliminar 

Os aplicativos, que são executados em um pool compartilhado de computadores, podem começar pequeno e dimensionar para centenas ou milhares de máquinas, conforme necessário.

O Service Fabric dá suporte a WebAPI com OWIN (Open Web interface for .NET) e ASP.NET Core. Ele fornece SDKs para a criação de serviços no Linux no .NET Core e no Java. Para saber mais sobre Service Fabric, consulte a [documentação do Service Fabric](https://docs.microsoft.com/azure/service-fabric/).

> **Quando usar:** Service Fabric é uma boa opção quando você está criando um aplicativo ou reescrevendo um aplicativo existente para usar uma arquitetura de microserviço. Use Service Fabric quando precisar de mais controle sobre, ou acesso direto à infraestrutura subjacente.
> 
> **Introdução:** [crie seu primeiro aplicativo de Service Fabric do Azure](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Aprimore seus aplicativos com os serviços do Azure

Junto com a hospedagem de aplicativos, o Azure fornece ofertas de serviço que podem aprimorar a funcionalidade. O Azure também pode melhorar o desenvolvimento e a manutenção de seus aplicativos, tanto na nuvem quanto no local.

#### <a name="hosted-storage-and-data-access"></a>Armazenamento hospedado e acesso a dados

A maioria dos aplicativos deve armazenar dados, portanto, você decide hospedar seu aplicativo no Azure, considere um ou mais dos seguintes serviços de armazenamento e dados.

- **Azure Cosmos DB**: um serviço de banco de dados multimodelo distribuído globalmente. Esse banco de dados permite dimensionar de forma elástica a taxa de transferência e o armazenamento em qualquer número de regiões geográficas com um SLA abrangente. 
  
  > **Quando usar:** Quando seu aplicativo precisa de documentos, tabelas ou bancos de dados de grafo, incluindo bancos de dados do MongoDB, com vários modelos de consistência bem definidos. 
  > 
  > **Introdução**: [criar um aplicativo Web Azure Cosmos DB](../../cosmos-db/create-sql-api-dotnet.md). Se você for um desenvolvedor do MongoDB, consulte [compilar um aplicativo Web do MongoDB com Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

- **Armazenamento do Azure**: oferece armazenamento durável, altamente disponível para BLOBs, filas, arquivos e outros tipos de dados não relacionais. O armazenamento fornece a base de armazenamento para VMs.

  > **Quando usar**: quando seu aplicativo armazena dados não relacionais, como pares de chave-valor (tabelas), BLOBs, compartilhamentos de arquivos ou mensagens (filas).
  > 
  > **Introdução**: escolha um desses tipos de armazenamento: [BLOBs](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [tabelas](../../cosmos-db/table-storage-how-to-use-dotnet.md), [filas](../../storage/queues/storage-dotnet-how-to-use-queues.md)ou [arquivos](../../storage/files/storage-dotnet-how-to-use-files.md).

- **Banco**de dados SQL do Azure: uma versão baseada no Azure do mecanismo de Microsoft SQL Server para o armazenamento de data de tabela relacional na nuvem. O banco de dados SQL fornece um desempenho previsível, escalabilidade sem tempo de inatividade, continuidade dos negócios e proteção do dado.

  > **Quando usar**: quando seu aplicativo requer armazenamento de dados com integridade referencial, suporte transacional e suporte para consultas TSQL.
  > 
  > **Introdução**: [crie um banco de dados SQL em minutos usando o portal do Azure](../../sql-database/sql-database-get-started.md).


Você pode usar [Azure data Factory](../../data-factory/introduction.md) para mover dados locais existentes para o Azure. Se você não estiver pronto para mover dados para a nuvem, [conexões híbridas](../../app-service/app-service-hybrid-connections.md) no serviço Azure App permite que você conecte seu aplicativo hospedado do serviço de aplicativo a recursos locais. Você também pode se conectar aos serviços de armazenamento e dados do Azure de seus aplicativos locais.

#### <a name="docker-support"></a>Suporte do Docker

OS contêineres do Docker, uma forma de virtualização do sistema operacional, permitem implantar aplicativos de maneira mais eficiente e previsível. Um aplicativo em contêineres funciona na produção da mesma maneira que em seus sistemas de desenvolvimento e teste. Você pode gerenciar contêineres usando ferramentas padrão do Docker. Você pode usar suas habilidades existentes e ferramentas de código aberto populares para implantar e gerenciar aplicativos baseados em contêiner no Azure.

O Azure fornece várias maneiras de usar contêineres em seus aplicativos.

- **Extensão de VM do Docker do Azure**: permite que você configure sua VM com ferramentas do Docker para atuar como um host do Docker.

  > **Quando usar**: quando você quiser gerar implantações de contêiner consistentes para seus aplicativos em uma VM ou quando quiser usar [Docker Compose](https://docs.docker.com/compose/overview/).
  > 
  > **Introdução**: [criar um ambiente do Docker no Azure usando a extensão de VM do Docker](../../virtual-machines/virtual-machines-linux-dockerextension.md).

- **Serviço kubernetes do Azure**: permite criar, configurar e gerenciar um cluster de máquinas virtuais que são pré-configuradas para executar aplicativos em contêineres. Para saber mais sobre o serviço kubernetes do Azure, consulte [introdução ao serviço kubernetes do Azure](../../aks/intro-kubernetes.md).

  > **Quando usar**: quando você precisar criar ambientes escalonáveis e prontos para produção que fornecem ferramentas de gerenciamento e agendamento adicionais, ou quando você estiver implantando um cluster do Docker Swarm.
  > 
  > **Introdução**: [implantar um cluster do serviço kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md).

- **Máquina do Docker**: permite que você instale e gerencie um mecanismo do Docker em hosts virtuais usando comandos de máquina Docker.

  >**Quando usar**: quando você precisar criar um protótipo rápido de um aplicativo Criando um único host do Docker.

- **Imagem personalizada do Docker para o serviço de aplicativo**: permite que você use contêineres do Docker de um registro de contêiner ou de um contêiner de cliente ao implantar um aplicativo Web no Linux.

  > **Quando usar**: ao implantar um aplicativo Web no Linux em uma imagem do Docker.
  > 
  > **Introdução**: [usar uma imagem personalizada do Docker para o serviço de aplicativo no Linux](../../app-service/containers/quickstart-docker-go.md).

### <a name="authentication"></a>Autenticação

É crucial não apenas saber quem está usando seus aplicativos, mas também impedir o acesso não autorizado aos seus recursos. O Azure fornece várias maneiras de autenticar seus clientes de aplicativo.

- **Azure Active Directory (AD do Azure)** : o serviço de gerenciamento de acesso e identidade baseado em nuvem multilocatário da Microsoft. Você pode adicionar SSO (logon único) a seus aplicativos integrando-se ao Azure AD. Você pode acessar as propriedades do diretório usando o API do Graph do Azure AD diretamente ou a API Microsoft Graph. Você pode integrar com o suporte do Azure AD para a estrutura de autorização do OAuth 2.0 e o Open ID Connect usando pontos de extremidade HTTP/REST nativos e as bibliotecas de autenticação multiplataforma do Azure AD.

  > **Quando usar**: quando você quiser fornecer uma experiência de SSO, trabalhar com dados baseados em grafo ou autenticar usuários baseados em domínio.
  > 
  > **Introdução**: para saber mais, confira o [Guia do desenvolvedor do Azure Active Directory](../../active-directory/develop/v1-overview.md).

- **Autenticação do serviço de aplicativo**: quando você escolhe o serviço de aplicativo para hospedar seu aplicativo, você também obtém suporte interno de autenticação para o Azure AD, juntamente com provedores de identidade social — incluindo Facebook, Google, Microsoft e Twitter.

  > **Quando usar**: quando você deseja habilitar a autenticação em um aplicativo do serviço de aplicativo usando o Azure AD, provedores de identidade social ou ambos.
  > 
  > **Introdução**: para saber mais sobre a autenticação no serviço de aplicativo, consulte [autenticação e autorização no serviço de Azure app](../../app-service/overview-authentication-authorization.md).

Para saber mais sobre as práticas recomendadas de segurança no Azure, consulte [padrões e práticas recomendadas de segurança do Azure](../../security/fundamentals/best-practices-and-patterns.md).

### <a name="monitoring"></a>Monitorização

Com seu aplicativo em funcionamento no Azure, você precisa monitorar o desempenho, observar problemas e ver como os clientes estão usando seu aplicativo. O Azure fornece várias opções de monitoramento.

-   **Application insights**: um serviço de análise extensível hospedado no Azure que se integra ao Visual Studio para monitorar seus aplicativos Web em tempo real. Ele fornece os dados de que você precisa para melhorar o desempenho e a usabilidade de seus aplicativos continuamente. Essa melhoria ocorrerá se você hospedar seus aplicativos no Azure ou não.

    >**Introdução**: Siga o [tutorial de Application insights](../../azure-monitor/app/app-insights-overview.md).

-   **Azure monitor**: um serviço que ajuda você a Visualizar, consultar, rotear, arquivar e agir sobre as métricas e os logs que você gera com a infraestrutura e os recursos do Azure. O monitor é uma fonte única para monitorar recursos do Azure e fornece as exibições de dados que você vê no portal do Azure.
 
    >**Introdução**: introdução [ao Azure monitor](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>Integração DevOps

Seja Provisionando VMs ou publicando seus aplicativos Web com integração contínua, o Azure integra-se com a maioria das populares ferramentas de DevOps. Você pode trabalhar com as ferramentas que já tem e maximizar sua experiência existente com o suporte para ferramentas como: 

* Jenkins 
* GitHub 
* Puppet
* Chef
* TeamCity
* Ansible
* Azure DevOps

> **Introdução**: para ver as opções de DevOps para um aplicativo do serviço de aplicativo, consulte [implantação contínua no serviço Azure app](../../app-service/deploy-continuous-deployment.md).
> 
> **Experimente agora:** [experimente várias das integrações do DevOps](https://azure.microsoft.com/try/devops/).


## <a name="azure-regions"></a>Regiões do Azure

O Azure é uma plataforma de nuvem global que geralmente está disponível em muitas regiões em todo o mundo. Ao provisionar um serviço, aplicativo ou VM no Azure, você será solicitado a selecionar uma região. Essa região representa um datacenter específico onde seu aplicativo é executado ou onde os dados são armazenados. Essas regiões correspondem a locais específicos, que são publicados na página de [regiões do Azure](https://azure.microsoft.com/regions/) .

### <a name="choose-the-best-region-for-your-application-and-data"></a>Escolha a melhor região para seu aplicativo e dados

Um dos benefícios de usar o Azure é que você pode implantar seus aplicativos em vários data centers em todo o mundo. A região que você escolher pode afetar o desempenho do seu aplicativo. Por exemplo, é melhor escolher uma região que esteja mais próxima da maioria dos seus clientes para reduzir a latência em solicitações de rede. Talvez você também queira selecionar sua região para atender aos requisitos legais para distribuir seu aplicativo em determinados países/regiões. É sempre uma prática recomendada armazenar dados de aplicativos no mesmo datacenter ou em um datacenter o mais próximo possível do datacenter que está hospedando seu aplicativo.

### <a name="multi-region-apps"></a>Aplicativos de várias regiões

Embora improvável, não é impossível que um datacenter inteiro fique offline devido a um evento, como um desastre natural ou uma falha na Internet. É uma prática recomendada hospedar aplicativos de negócios vitais em mais de um datacenter para fornecer disponibilidade máxima. O uso de várias regiões também pode reduzir a latência para usuários globais e fornecer oportunidades adicionais de flexibilidade ao atualizar aplicativos.

Alguns serviços, como máquinas virtuais e serviços de aplicativos, usam o [Gerenciador de tráfego do Azure](../../traffic-manager/traffic-manager-overview.md) para habilitar o suporte a várias regiões com failover entre regiões para dar suporte a aplicativos empresariais de alta disponibilidade. Para obter um exemplo, consulte [arquitetura de referência do Azure: executar um aplicativo Web em várias regiões](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

>**Quando usar**: quando você tem aplicativos corporativos e de alta disponibilidade que se beneficiam do failover e da replicação.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Como fazer gerenciar meus aplicativos e projetos?

O Azure fornece um conjunto avançado de experiências para você criar e gerenciar seus recursos, aplicativos e projetos do Azure — de forma programática e na [portal do Azure](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Interfaces de linha de comando e PowerShell

O Azure fornece duas maneiras de gerenciar seus aplicativos e serviços na linha de comando. Você pode usar ferramentas como bash, terminal, prompt de comando ou sua ferramenta de linha de comando de sua escolha. Normalmente, você pode fazer as mesmas tarefas na linha de comando como no portal do Azure — como criar e configurar máquinas virtuais, redes virtuais, aplicativos Web e outros serviços.

-   [CLI (interface de linha de comando) do Azure](../../xplat-cli-install.md): permite que você se conecte a uma assinatura do Azure e programe várias tarefas em relação aos recursos do Azure na linha de comando.

-   [Azure PowerShell](../../powershell-install-configure.md): fornece um conjunto de módulos com cmdlets que permitem que você gerencie recursos do Azure usando o Windows PowerShell.

### <a name="azure-portal"></a>Portal do Azure

O [portal do Azure](https://portal.azure.com) é um aplicativo baseado na Web. Você pode usar o portal do Azure para criar, gerenciar e remover recursos e serviços do Azure. Inclui:

* Um painel configurável
* Ferramentas de gerenciamento de recursos do Azure
* Acesso a configurações de assinatura e informações de cobrança. Para obter mais informações, consulte a [visão geral de portal do Azure](../../azure-portal-overview.md).

### <a name="rest-apis"></a>APIs REST

O Azure é criado em um conjunto de APIs REST que dão suporte à interface do usuário do portal do Azure. A maioria dessas APIs REST também tem suporte para permitir que você provisione e gerencie programaticamente seus recursos e aplicativos do Azure de qualquer dispositivo habilitado para Internet. Para obter o conjunto completo da documentação da API REST, consulte a [referência do SDK REST do Azure](https://docs.microsoft.com/rest/api/).

### <a name="apis"></a>APIs

Juntamente com as APIs REST, muitos serviços do Azure também permitem que você gerencie programaticamente recursos de seus aplicativos usando SDKs do Azure específicos da plataforma, incluindo SDKs para as seguintes plataformas de desenvolvimento:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/azure/javascript/)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](/azure/python/)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Go](https://docs.microsoft.com/azure/go)

Serviços como [aplicativos móveis](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) e [serviços de mídia do Azure](../../media-services/previous/media-services-dotnet-how-to-use.md) fornecem SDKs do lado do cliente para permitir que você acesse serviços de aplicativos de cliente Web e móveis.

### <a name="azure-resource-manager"></a>Gestor de Recursos do Azure 
    
A execução do aplicativo no Azure provavelmente envolve o trabalho com vários serviços do Azure. Esses serviços seguem o mesmo ciclo de vida e podem ser considerados como uma unidade lógica. Por exemplo, um aplicativo Web pode usar aplicativos Web, banco de dados SQL, armazenamento, cache do Azure para Redis e serviços de rede de distribuição de conteúdo do Azure. [Azure Resource Manager](../../azure-resource-manager/management/overview.md) permite trabalhar com os recursos em seu aplicativo como um grupo. Você pode implantar, atualizar ou excluir todos os recursos em uma única operação coordenada.

Juntamente com o agrupamento lógico e o gerenciamento de recursos relacionados, o Azure Resource Manager inclui recursos de implantação que permitem personalizar a implantação e a configuração de recursos relacionados. Por exemplo, você pode usar a implantação do Resource Manager e configurar um aplicativo. Esse aplicativo pode consistir em várias máquinas virtuais, um balanceador de carga e um banco de dados SQL do Azure como uma única unidade.

Você desenvolve essas implantações usando um modelo de Azure Resource Manager, que é um documento formatado em JSON. Os modelos permitem que você defina uma implantação e gerencie seus aplicativos usando modelos declarativos, em vez de scripts. Seus modelos podem funcionar para ambientes diferentes, como teste, preparo e produção. Por exemplo, você pode usar modelos para adicionar um botão a um repositório GitHub que implanta o código no repositório para um conjunto de serviços do Azure com um único clique.

> **Quando usar**: use modelos do Resource Manager quando desejar uma implantação baseada em modelo para seu aplicativo que você possa gerenciar programaticamente usando APIs REST, o CLI do Azure e Azure PowerShell.
> 
> **Introdução**: para começar a usar modelos, consulte [criação de modelos de Azure Resource Manager](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Noções básicas sobre contas, assinaturas e cobrança

Como desenvolvedores, gostaríamos de mergulhar diretamente no código e tentar começar o mais rápido possível com a execução de nossos aplicativos. Certamente queremos incentivar você a começar a trabalhar no Azure o mais facilmente possível. Para ajudar a torná-la fácil, o Azure oferece uma [avaliação gratuita](https://azure.microsoft.com/free/). Alguns serviços até mesmo têm uma funcionalidade "experimente gratuitamente", como [Azure app serviço](https://tryappservice.azure.com/), o que não exige que você mesmo crie uma conta. Tão divertido quanto é se aprofundar em codificar e implantar seu aplicativo no Azure, também é importante levar algum tempo para entender como o Azure funciona. Especificamente, você deve entender como ele funciona a partir de um ponto de vista de contas de usuário, assinaturas e cobrança.

### <a name="what-is-an-azure-account"></a>O que é uma conta do Azure?

Para criar ou trabalhar com uma assinatura do Azure, você deve ter uma conta do Azure. Uma conta do Azure é simplesmente uma identidade no Azure AD ou em um diretório, como uma organização corporativa ou de estudante, que confia no Azure AD. Se você não pertencer a tal organização, sempre poderá criar uma assinatura usando sua conta da Microsoft, que é confiável pelo Azure AD. Para saber mais sobre como integrar o Windows Server Active Directory local ao Azure AD, consulte [integrando suas identidades locais com Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md).

Cada subscrição do Azure tem uma relação de confiança com uma instância do Azure AD. Tal significa que confia nesse diretório para autenticar utilizadores, serviços e dispositivos. Várias subscrições podem confiar no mesmo diretório, mas uma subscrição apenas pode confiar num diretório. Para saber mais, confira [como as assinaturas do Azure estão associadas a Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Além de definir identidades de conta individuais do Azure, também chamadas de *usuários*, você pode definir *grupos* no Azure AD. A criação de grupos de usuários é uma boa maneira de gerenciar o acesso a recursos em uma assinatura usando o controle de acesso baseado em função (RBAC). Para saber como criar grupos, consulte [criar um grupo na Azure Active Directory visualização](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Você também pode criar e gerenciar grupos [usando o PowerShell](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Gerir as suas subscrições

Uma assinatura é um agrupamento lógico de serviços do Azure que está vinculado a uma conta do Azure. Uma única conta do Azure pode conter várias assinaturas. A cobrança dos serviços do Azure é feita por assinatura. Para obter uma lista das ofertas de assinatura disponíveis por tipo, consulte [detalhes da oferta de Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/). As assinaturas do Azure têm um administrador de conta que tem controle total sobre a assinatura. Eles também têm um administrador de serviços que tem controle sobre todos os serviços na assinatura. Para obter informações sobre administradores de assinatura clássicos, consulte [Adicionar ou alterar administradores de assinatura do Azure](../../billing/billing-add-change-azure-subscription-administrator.md). Contas individuais podem receber controle detalhado dos recursos do Azure usando o [RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md).

#### <a name="resource-groups"></a>Grupos de recursos

Ao provisionar novos serviços do Azure, você faz isso em uma determinada assinatura. Os serviços individuais do Azure, que também são chamados de recursos, são criados no contexto de um grupo de recursos. Os grupos de recursos facilitam a implantação e o gerenciamento dos recursos do aplicativo. Um grupo de recursos deve conter todos os recursos para o aplicativo com o qual você deseja trabalhar como uma unidade. Você pode mover recursos entre grupos de recursos e até mesmo para assinaturas diferentes. Para saber mais sobre como mover recursos, confira [mover recursos para um novo grupo de recursos ou assinatura](../../resource-group-move-resources.md).

A Azure Resource Explorer é uma excelente ferramenta para visualizar os recursos que você já criou em sua assinatura. Para saber mais, confira [usar Azure Resource Explorer para exibir e modificar recursos](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Conceder acesso aos recursos

Quando você permite o acesso aos recursos do Azure, é sempre uma prática recomendada fornecer aos usuários o privilégio mínimo necessário para realizar uma determinada tarefa.

- **RBAC (controle de acesso baseado em função)** : no Azure, você pode conceder acesso a contas de usuário (entidades de segurança) em um escopo especificado: assinatura, grupo de recursos ou recursos individuais. O RBAC permite que você implante recursos em um grupo de recursos e conceda permissões a um usuário ou grupo específico. Ele também permite que você limite o acesso apenas aos recursos que pertencem ao grupo de recursos de destino. Você também pode conceder acesso a um único recurso, como uma máquina virtual ou uma rede virtual. Para conceder acesso, atribua uma função para o usuário, grupo ou entidade de serviço. Há muitas funções predefinidas, e você também pode definir suas próprias funções personalizadas. Para saber mais, confira [o que é o RBAC (controle de acesso baseado em função)?](../../role-based-access-control/overview.md).

  > **Quando usar**: quando você precisar de gerenciamento de acesso refinado para usuários e grupos ou quando precisar tornar um usuário um proprietário de uma assinatura.
  > 
  > **Introdução**: para saber mais, consulte [gerenciar o acesso usando o RBAC e o portal do Azure](../../role-based-access-control/role-assignments-portal.md).

- **Objetos de entidade de serviço**: junto com o fornecimento de acesso a entidades de usuário e grupos, você pode conceder o mesmo acesso a uma entidade de serviço.

  > **Quando usar**: quando você estiver gerenciando programaticamente recursos do Azure ou concedendo acesso para aplicativos. Para obter mais informações, consulte [criar Active Directory aplicativo e entidade de serviço](../../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="tags"></a>Etiquetas

Azure Resource Manager permite atribuir marcas personalizadas a recursos individuais. Marcas, que são pares chave-valor, podem ser úteis quando você precisa organizar recursos para cobrança ou monitoramento. As marcas fornecem uma maneira de controlar recursos em vários grupos de recursos. Você pode atribuir marcas das seguintes maneiras:

* No portal 
* No modelo de Azure Resource Manager 
* Utilizar a API REST
* Com a CLI do Azure
* Utilizar o PowerShell 

Você pode atribuir várias marcas a cada recurso. Para saber mais, consulte [usando marcas para organizar os recursos do Azure](../../resource-group-using-tags.md).

### <a name="billing"></a>Faturação

Na migração do computador local para os serviços hospedados na nuvem, o rastreamento e a estimativa do uso do serviço e dos custos relacionados são preocupações significativas. É importante estimar o custo dos novos recursos a serem executados mensalmente. Você também pode projetar como a cobrança procura um determinado mês com base nos gastos atuais.

#### <a name="get-resource-usage-data"></a>Obter dados de uso de recursos

O Azure fornece um conjunto de APIs REST de cobrança que fornecem acesso ao consumo de recursos e informações de metadados para assinaturas do Azure. Esses APIs de Cobrança oferecem a capacidade de prever e gerenciar melhor os custos do Azure. Você pode acompanhar e analisar os gastos em incrementos por hora e criar alertas de gastos. Você também pode prever a cobrança futura com base nas tendências de uso atuais.

>**Introdução**: para saber mais sobre como usar o APIs de cobrança, confira [visão geral de APIs de uso e RateCard de cobrança do Azure](../../billing-usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Prever custos futuros

Embora seja desafiador estimar os custos antecipadamente, o Azure tem ferramentas que podem ajudar. Ele tem uma [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para ajudar a estimar o custo dos recursos implantados. Você também pode usar os recursos de cobrança no portal e as APIs REST de cobrança para estimar custos futuros com base no consumo atual.

>**Introdução**: consulte [visão geral de APIs de uso e RateCard de cobrança do Azure](../../billing-usage-rate-card-overview.md).
