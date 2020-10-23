---
title: Começar guia para programadores no Azure Microsoft Docs
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
ms.openlocfilehash: 8694c403b14234a70b0a67f9f4defb7817ba3ae3
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92372213"
---
# <a name="get-started-guide-for-azure-developers"></a>Guia de introdução para programadores do Azure

## <a name="what-is-azure"></a>O que é o Azure?

O Azure é uma plataforma em nuvem completa que pode acolher as suas aplicações existentes e dinamizar o desenvolvimento de novas aplicações. O Azure pode até melhorar as aplicações no local. O Azure integra os serviços em nuvem que precisa para desenvolver, testar, implementar e gerir as suas aplicações, aproveitando ao mesmo tempo as eficiências da computação em nuvem.

Ao hospedar as suas aplicações em Azure, pode iniciar uma pequena e fácil escala da sua aplicação à medida que a procura do seu cliente aumenta. O Azure também oferece a fiabilidade necessária para aplicações de alta disponibilidade, incluindo o failover entre diferentes regiões. O [portal Azure](https://portal.azure.com) permite-lhe gerir facilmente todos os seus serviços Azure. Também pode gerir os seus serviços programáticamente usando APIs e modelos específicos de serviço.

Este guia é uma introdução à plataforma Azure para desenvolvedores de aplicações. Fornece orientação e direção que você precisa começar a construir novas aplicações em Azure ou migrar aplicações existentes para Azure.

## <a name="where-do-i-start"></a>Por onde devo começar?

Com todos os serviços que a Azure oferece, pode ser uma tarefa intimidante descobrir que serviços precisa para apoiar a sua arquitetura de solução. Esta secção destaca os serviços Azure que os desenvolvedores usam comumente. Para obter uma lista de todos os serviços da Azure, consulte a documentação do [Azure.](../../index.yml)

Primeiro, tem de decidir como hospedar a sua candidatura no Azure. Precisa de gerir toda a sua infraestrutura como uma máquina virtual (VM)? Pode utilizar as instalações de gestão de plataformas que o Azure fornece? Talvez precises de uma estrutura sem servidor para hospedar apenas a execução de código?

A sua aplicação necessita de armazenamento em nuvem, para a qual o Azure oferece várias opções. Pode aproveitar a autenticação da empresa da Azure. Existem também ferramentas para o desenvolvimento e monitorização baseados na nuvem, e a maioria dos serviços de hospedagem oferecem integração de DevOps.

Agora, vamos olhar para alguns dos serviços específicos que recomendamos investigar para as suas aplicações.

### <a name="application-hosting"></a>Hospedagem de candidaturas

O Azure fornece várias ofertas de computação baseadas em nuvem para executar a sua aplicação para que não tenha de se preocupar com os detalhes da infraestrutura. Pode aumentar facilmente ou aumentar os seus recursos à medida que o uso da sua aplicação aumenta.

A Azure oferece serviços que suportam o desenvolvimento da sua aplicação e necessidades de hospedagem. O Azure fornece Infraestruturas como Serviço (IaaS) para lhe dar total controlo sobre o alojamento da sua aplicação. As ofertas da Azure's Platform as a Service (PaaS) fornecem os serviços totalmente geridos necessários para alimentar as suas apps. Há até um verdadeiro alojamento sem servidor em Azure onde tudo o que precisa fazer é escrever o seu código.

![Opções de hospedagem de aplicações Azure](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Serviço de Aplicações do Azure

Quando quiser o caminho mais rápido para publicar os seus projetos baseados na Web, considere o Azure App Service. O Serviço de Aplicações facilita a extensão das suas aplicações web para apoiar os seus clientes móveis e publicar APIs REST facilmente consumidas. Esta plataforma fornece a autenticação utilizando fornecedores sociais, autoscalagem baseadas no tráfego, testes em produção e implementações contínuas e baseadas em contentores.

Pode criar aplicativos web, aplicativos móveis e aplicativos API.

Como os três tipos de aplicações partilham o tempo de funcionamento do Serviço de Aplicações, pode hospedar um website, apoiar clientes móveis e expor as suas APIs em Azure, tudo a partir do mesmo projeto ou solução. Para saber mais sobre o Serviço de Aplicações, consulte [o que é a Azure Web Apps.](../../app-service/overview.md)

O Serviço de Aplicações foi concebido tendo em mente os DevOps. Suporta várias ferramentas para implantações de publicação e integração contínua. Estas ferramentas incluem webhooks GitHub, Jenkins, Azure DevOps, TeamCity, entre outros.

Pode migrar as suas aplicações existentes para o Serviço de Aplicações utilizando a [ferramenta de migração online.](https://appmigration.microsoft.com/)

> **Quando utilizar**: Utilize o Serviço de Aplicações quando estiver a migrar aplicações web existentes para o Azure, e quando necessitar de uma plataforma de hospedagem totalmente gerida para as suas aplicações web. Também pode utilizar o Serviço de Aplicações quando precisar de apoiar clientes móveis ou expor as APIs rest com a sua aplicação.
>
> **Começar**: O Serviço de Aplicações facilita a criação e implementação da sua primeira [aplicação web,](../../app-service/quickstart-dotnetcore.md) [aplicação móvel](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started)ou [app API.](../../app-service/app-service-web-tutorial-rest-api.md)
>
> **Experimente agora:** O Serviço de Aplicações permite-lhe aprovisionar uma app de curta duração para experimentar a plataforma sem ter de se inscrever para uma conta Azure. Experimente a plataforma e [crie a sua aplicação Azure App Service](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Máquinas Virtuais do Microsoft Azure

Como fornecedor de Infraestruturas como Prestador de Serviços (IaaS), o Azure permite-lhe implementar ou migrar a sua aplicação para Windows ou Linux VMs. Juntamente com a Rede Virtual Azure, a Azure Virtual Machines suporta a implementação de Windows ou Linux VMs para Azure. Com os VMs, tem controlo total sobre a configuração da máquina. Ao utilizar VMs, é responsável por todas as instalações, configurações, manutenção e patches do sistema operativo.

Devido ao nível de controlo que tens com os VMs, podes executar uma vasta gama de cargas de trabalho do servidor no Azure que não se encaixam num modelo PaaS. Estas cargas de trabalho incluem servidores de bases de dados, Diretório Ativo do Windows Server e Microsoft SharePoint. Para obter mais informações, consulte a documentação das Máquinas Virtuais para [linux](../../virtual-machines/linux/index.yml) ou [Windows](../../virtual-machines/windows/index.yml).

> **Quando utilizar**: Utilize máquinas virtuais quando pretender o controlo total sobre a sua infraestrutura de aplicação ou migrar cargas de trabalho de aplicação no local para Azure sem ter de fazer alterações.
>
> **Começar:** Crie um [VM Linux](../../virtual-machines/linux/quick-create-portal.md) ou [Windows VM](../../virtual-machines/windows/quick-create-portal.md) a partir do portal Azure.

#### <a name="azure-functions-serverless"></a>Funções Azure (sem servidor)

Em vez de se preocupar em construir e gerir toda uma aplicação ou a infraestrutura para executar o seu código, e se pudesse escrever o seu código e fazê-lo funcionar em resposta a eventos ou num horário?  [Azure Functions](../../azure-functions/functions-overview.md) é uma oferta de estilo "sem servidor", que permite escrever apenas o código de que necessita. Com as Funções, pode desencadear a execução de código com pedidos HTTP, webhooks, eventos de serviço na nuvem ou num horário. Pode codificar na sua linguagem de desenvolvimento de eleição, como \# C, \# F, Node.js, Python ou PHP. Com a faturação baseada no consumo, paga-se apenas pelo tempo que o seu código executa, e a Azure escala as escalas conforme necessário.

> **Quando utilizar**: Utilize funções Azure quando tiver um código que é acionado por outros serviços Azure, por eventos baseados na web ou num horário. Também pode utilizar Funções quando não necessitar da sobrecarga de um projeto completo ou quando apenas pretender pagar o tempo que o seu código executa. Para saber mais, consulte [a Visão geral das Funções Azure](../../azure-functions/functions-overview.md).
>
> **Começar**: Siga o tutorial de arranque rápido das funções para criar a [sua primeira função](../../azure-functions/functions-create-first-azure-function.md) a partir do portal.
>
> **Experimente agora:** As Funções Azure permitem executar o seu código sem ter de se inscrever para uma conta Azure. Experimente agora e [crie a sua primeira Função Azure](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

A Azure Service Fabric é uma plataforma de sistemas distribuídos. Esta plataforma facilita a construção, embalagem, implantação e gestão de microserviços escaláveis e fiáveis. Também fornece capacidades abrangentes de gestão de aplicações, tais como:

* Aprovisionamento
* Implantação
* Monitorização
* Upgrade/remendação
* Eliminar

As aplicações, que funcionam num conjunto partilhado de máquinas, podem começar pequenas e dimensionar para centenas ou milhares de máquinas, conforme necessário.

O Service Fabric suporta o WebAPI com interface web aberta para .NET (OWIN) e ASP.NET Core. Fornece SDKs para serviços de construção em Linux tanto em .NET Core como em Java. Para saber mais sobre o Tecido de Serviço, consulte a documentação do [Service Fabric.](../../service-fabric/index.yml)

> **Quando usar:** O Service Fabric é uma boa escolha quando se está a criar uma aplicação ou a reescrever uma aplicação existente para usar uma arquitetura de microserviços. Utilize o Tecido de Serviço quando necessitar de mais controlo sobre, ou acesso direto à infraestrutura subjacente.
>
> **Começar:** [Crie a sua primeira aplicação Azure Service Fabric](../../service-fabric/service-fabric-tutorial-create-dotnet-app.md).

### <a name="enhance-your-applications-with-azure-services"></a>Melhore as suas aplicações com serviços Azure

Juntamente com o alojamento de aplicações, o Azure fornece ofertas de serviços que podem melhorar a funcionalidade. O Azure também pode melhorar o desenvolvimento e manutenção das suas aplicações, tanto na nuvem como no local.

#### <a name="hosted-storage-and-data-access"></a>Armazenamento hospedado e acesso a dados

A maioria das aplicações deve armazenar dados, por isso, seja como for que decida hospedar a sua aplicação no Azure, considere um ou mais dos seguintes serviços de armazenamento e dados.

- **Azure Cosmos DB**: Um serviço de base de dados multi-modelo distribuído globalmente. Esta base de dados permite-lhe escalar elasticamente a produção e armazenamento em qualquer número de regiões geográficas com um SLA abrangente.

  > **Quando usar:** Quando a sua aplicação precisa de bases de dados de documentos, tabelas ou gráficos, incluindo bases de dados MongoDB, com vários modelos de consistência bem definidos.
  >
  > **Começar :** [Construa uma aplicação web Azure Cosmos DB](../../cosmos-db/create-sql-api-dotnet.md). Se você é um desenvolvedor de MongoDB, consulte [build a MongoDB web app with Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

- **Armazenamento Azure**: Oferece armazenamento durável e altamente disponível para bolhas, filas, ficheiros e outros tipos de dados não-culturais. O armazenamento fornece a base de armazenamento para VMs.

  > **Quando utilizar**: Quando a sua aplicação armazena dados não-sexuais, tais como pares de valores-chave (tabelas), bolhas, partilhas de ficheiros ou mensagens (filas).
  >
  > **Começar**: Escolha entre um destes tipos de armazenamento: [bolhas, mesas,](../../storage/blobs/storage-quickstart-blobs-dotnet.md)filas ou [tables](../../cosmos-db/tutorial-develop-table-dotnet.md) [ficheiros](../../storage/queues/storage-dotnet-how-to-use-queues.md). [files](../../storage/files/storage-dotnet-how-to-use-files.md)

- **Base de Dados Azure SQL**: Uma versão baseada em Azure do motor do Microsoft SQL Server para armazenar dados tabulares relacionais na nuvem. A SQL Database proporciona um desempenho previsível, escalabilidade sem tempo de inatividade, continuidade do negócio e proteção de dados.

  > **Quando utilizar:** Quando a sua aplicação requer armazenamento de dados com integridade referencial, suporte transacional e suporte para consultas TSQL.
  >
  > **Começar:** [Criar uma base de dados na Base de Dados Azure SQL em minutos utilizando o portal Azure](../../azure-sql/database/single-database-create-quickstart.md).


Pode utilizar [a Azure Data Factory](../../data-factory/introduction.md) para transferir os dados existentes no local para a Azure. Se não estiver pronto para mover dados para a nuvem, as [Conexões Híbridas](../../app-service/app-service-hybrid-connections.md) no Azure App Service permitem-lhe ligar a sua aplicação do Serviço de Aplicações ao local. Também pode ligar-se aos serviços de dados e armazenamento da Azure a partir das suas aplicações no local.

#### <a name="docker-support"></a>Apoio ao estivador

Os contentores docker, uma forma de virtualização de SO, permitem implementar aplicações de uma forma mais eficiente e previsível. Uma aplicação contentorizada funciona na produção da mesma forma que nos seus sistemas de desenvolvimento e teste. Pode gerir os recipientes utilizando ferramentas padrão do Docker. Pode utilizar as suas habilidades existentes e ferramentas de código aberto populares para implementar e gerir aplicações baseadas em contentores no Azure.

O Azure fornece várias formas de utilizar recipientes nas suas aplicações.


- **Serviço Azure Kubernetes**: Permite criar, configurar e gerir um conjunto de máquinas virtuais pré-configuradas para executar aplicações contentorizadas. Para saber mais sobre o Serviço Azure Kubernetes, consulte [a introdução do Serviço Azure Kubernetes.](../../aks/intro-kubernetes.md)

  > **Quando utilizar:** Quando precisar de construir ambientes prontos para a produção, que fornecem ferramentas de agendamento e gestão adicionais, ou quando estiver a implantar um cluster Docker Swarm.
  >
  > **Começar**: [Implementar um cluster de serviço Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md).

- **Máquina Docker**: Permite instalar e gerir um Motor Docker em anfitriões virtuais utilizando comandos de máquinas de estivador.

  >**Quando utilizar:** Quando precisar de protótipo rapidamente de uma aplicação, criando um único anfitrião Docker.

- **Imagem personalizada do Docker para o Serviço de Aplicações**: Permite-lhe utilizar recipientes Docker a partir de um registo de contentores ou de um contentor de clientes quando implementar uma aplicação web no Linux.

  > **Quando utilizar:** Ao implementar uma aplicação web no Linux para uma imagem do Docker.
  >
  > **Começar**: [Use uma imagem personalizada do Docker para o Serviço de Aplicações em Linux](../../app-service/quickstart-custom-container.md?pivots=platform-linux%253fpivots%253dplatform-linux).

### <a name="authentication"></a>Autenticação

É crucial não só saber quem está a usar as suas aplicações, mas também impedir o acesso não autorizado aos seus recursos. O Azure oferece várias formas de autenticar os seus clientes de aplicações.

- **Azure Ative Directory (Azure AD)**: O serviço multitenante da Microsoft, baseado na identidade e na gestão de acessos. Pode adicionar um único sinal nas suas aplicações integrando-se com Azure AD. Pode aceder diretamente às propriedades do diretório Azure AD ou à API do Gráfico Microsoft. Pode integrar-se com o suporte Azure AD para o quadro de autorização OAuth2.0 e Open ID Connect utilizando pontos finais http/REST nativos e as bibliotecas de autenticação Azure AD multiplataforma.

  > **Quando pretender**fornecer uma experiência SSO, trabalhe com dados baseados em gráficos ou autente utilizadores baseados em domínios.
  >
  > **Começar**: Para saber mais, consulte o [guia do programador do Azure Ative Directory](../../active-directory/develop/v2-overview.md).

- **Autenticação do Serviço de Aplicações**: Quando escolhe o Serviço de Aplicações para hospedar a sua aplicação, também obtém suporte de autenticação incorporada para Azure AD, juntamente com fornecedores de identidade social — incluindo Facebook, Google, Microsoft e Twitter.

  > **Quando utilizar**: Quando pretender ativar a autenticação numa aplicação do Serviço de Aplicações, utilizando a Azure AD, fornecedores de identidade social ou ambos.
  >
  > **Começar**: Para saber mais sobre autenticação no Serviço de Aplicações, consulte [autenticação e autorização no Azure App Service](../../app-service/overview-authentication-authorization.md).

Para saber mais sobre as melhores práticas de segurança em Azure, consulte as [melhores práticas e padrões](../../security/fundamentals/best-practices-and-patterns.md)de segurança da Azure.

### <a name="monitoring"></a>Monitorização

Com a sua aplicação em funcionamento no Azure, precisa de monitorizar o desempenho, observar os problemas e ver como os clientes estão a usar a sua app. O Azure oferece várias opções de monitorização.

-   **Application Insights**: Um serviço de análise extensível hospedado no Azure que se integra com o Visual Studio para monitorizar as suas aplicações web ao vivo. Dá-lhe os dados de que necessita para melhorar o desempenho e a usabilidade das suas apps de forma contínua. Esta melhoria ocorre quer acolhia as suas aplicações no Azure ou não.

    >**Começar**: Siga o [tutorial de Insights de Aplicação](../../azure-monitor/app/app-insights-overview.md).

-   **Azure Monitor**: Um serviço que o ajuda a visualizar, consultar, encaminhar, arquivar e agir nas métricas e registos que gera com a sua infraestrutura e recursos Azure. O Monitor é uma única fonte de monitorização dos recursos do Azure e fornece as vistas de dados que vê no portal Azure.

    >**Começar :** [Começa com o Azure Monitor](../../azure-monitor/overview.md).

### <a name="devops-integration"></a>Integração de DevOps

Quer esteja a atear VMs ou a publicar as suas aplicações web com integração contínua, o Azure integra-se com a maioria das ferramentas populares de DevOps. Pode trabalhar com as ferramentas que já tem e maximizar a sua experiência existente com suporte para ferramentas como:

* Jenkins
* GitHub
* Puppet
* Chef
* TeamCity
* Ansible
* Azure DevOps

> **Começar**: Para ver as opções de DevOps para uma aplicação de Serviço de Aplicações, consulte [a Implementação Contínua para o Serviço de Aplicações Azure.](../../app-service/deploy-continuous-deployment.md)
>
> **Experimente agora:** [Experimente várias das integrações de DevOps](https://azure.microsoft.com/try/devops/).


## <a name="azure-regions"></a>Regiões do Azure

O Azure é uma plataforma global de nuvem que está geralmente disponível em muitas regiões do mundo. Ao providenciar um serviço, aplicação ou VM em Azure, é-lhe pedido que selecione uma região. Esta região representa um datacenter específico onde a sua aplicação é executado ou onde os seus dados são armazenados. Estas regiões correspondem a localizações específicas, publicadas na página das [regiões de Azure.](https://azure.microsoft.com/regions/)

### <a name="choose-the-best-region-for-your-application-and-data"></a>Escolha a melhor região para a sua aplicação e dados

Um dos benefícios de usar o Azure é que pode implementar as suas aplicações em vários centros de dados em todo o mundo. A região que escolher pode afetar o desempenho da sua aplicação. Por exemplo, é melhor escolher uma região mais próxima da maioria dos seus clientes para reduzir a latência nos pedidos de rede. Também pode querer selecionar a sua região para cumprir os requisitos legais para a distribuição da sua app em determinados países/regiões. É sempre uma melhor prática armazenar dados de aplicações no mesmo datacenter ou num datacenter o mais próximo possível do datacenter que está hospedado na sua aplicação.

### <a name="multi-region-apps"></a>Aplicativos multi-regiões

Embora improvável, não é impossível para um centro de dados inteiro ficar offline por causa de um evento como um desastre natural ou falha na Internet. É uma boa prática acolher aplicações de negócio vitais em mais de um datacenter para fornecer a máxima disponibilidade. A utilização de várias regiões também pode reduzir a latência dos utilizadores globais e proporcionar oportunidades adicionais de flexibilidade na atualização das aplicações.

Alguns serviços, como o Virtual Machine e os App Services, utilizam [o Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) para permitir o apoio de várias regiões com falhas entre regiões para apoiar aplicações empresariais de alta disponibilidade. Por exemplo, consulte [a arquitetura de referência Azure: Executar uma aplicação web em várias regiões](/azure/architecture/reference-architectures/app-service-web-app/multi-region).

>**Quando utilizar:** Quando tiver aplicações empresariais e de alta disponibilidade que beneficiam de falha e replicação.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Como posso gerir as minhas aplicações e projetos?

O Azure oferece um rico conjunto de experiências para criar e gerir os seus recursos, aplicações e projetos Azure - tanto programáticamente como no [portal Azure.](https://portal.azure.com/)

### <a name="command-line-interfaces-and-powershell"></a>Interfaces de linha de comando e PowerShell

O Azure fornece duas formas de gerir as suas aplicações e serviços a partir da linha de comando. Pode utilizar ferramentas como Bash, Terminal, o pedido de comando ou a sua ferramenta de linha de comando de eleição. Normalmente, pode fazer as mesmas tarefas a partir da linha de comando que no portal Azure — como criar e configurar máquinas virtuais, redes virtuais, aplicações web e outros serviços.

-   [Azure Command-Line Interface (CLI)](/cli/azure/install-azure-cli): Permite ligar-se a uma subscrição do Azure e programar várias tarefas contra os recursos do Azure a partir da linha de comando.

-   [Azure PowerShell](/powershell/azure/): Fornece um conjunto de módulos com cmdlets que lhe permitem gerir os recursos do Azure utilizando o Windows PowerShell.

### <a name="azure-portal"></a>Portal do Azure

O [portal Azure](https://portal.azure.com) é uma aplicação baseada na web. Pode utilizar o portal Azure para criar, gerir e remover recursos e serviços da Azure. Inclui:

* Um painel configurável
* Ferramentas de gestão de recursos Azure
* Acesso às definições de subscrição e informações de faturação. Para mais informações, consulte a [visão geral do portal Azure](https://azure.microsoft.com/features/azure-portal/).

### <a name="rest-apis"></a>APIs REST

O Azure é construído sobre um conjunto de APIs REST que suportam o portal Azure UI. A maioria destes APIs REST também são suportados para permitir que você provisa programáticamente e gere os seus recursos e aplicações Azure a partir de qualquer dispositivo ativado pela Internet. Para obter o conjunto completo de documentação rest API, consulte a [referência Azure REST SDK](/rest/api/).

### <a name="apis"></a>APIs

Juntamente com as APIs rest, muitos serviços Azure também permitem gerir programáticamente recursos a partir das suas aplicações, utilizando SDKs Azure específicos da plataforma, incluindo SDKs para as seguintes plataformas de desenvolvimento:

-   [.NET](/dotnet/api/)
-   [Node.js](/azure/developer/javascript/)
-   [Java](/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](/azure/python/)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Ir](/azure/go)

Serviços como [Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library) e [Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) fornecem SDKs do lado do cliente para permitir o acesso a serviços a partir de aplicações web e clientes móveis.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Executar a sua aplicação no Azure provavelmente envolve trabalhar com vários serviços Azure. Estes serviços seguem o mesmo ciclo de vida e podem ser considerados como uma unidade lógica. Por exemplo, uma aplicação web pode usar aplicativos Web, SQL Database, Storage, Azure Cache para redis e serviços da Rede de Entrega de Conteúdos Azure. [O Azure Resource Manager](../../azure-resource-manager/management/overview.md) permite-lhe trabalhar com os recursos da sua aplicação como grupo. Pode implementar, atualizar ou eliminar todos os recursos numa única operação coordenada.

Juntamente com o agrupamento e gestão logicamente de recursos relacionados, o Azure Resource Manager inclui capacidades de implementação que permitem personalizar a implementação e configuração de recursos relacionados. Por exemplo, pode utilizar o Gestor de Recursos e configurar uma aplicação. Esta aplicação pode consistir em várias máquinas virtuais, um equilibrador de carga e uma base de dados na Base de Dados Azure SQL como uma única unidade.

Desenvolve estas implementações utilizando um modelo de Gestor de Recursos Azure, que é um documento com formato JSON. Os modelos permitem definir uma implementação e gerir as suas aplicações usando modelos declarativos, em vez de scripts. Os seus modelos podem funcionar para diferentes ambientes, tais como testes, encenação e produção. Por exemplo, pode utilizar modelos para adicionar um botão a um repo GitHub que implementa o código no repo para um conjunto de serviços Azure com um único clique.

> **Quando utilizar**: Utilize os modelos de Gestor de Recursos quando pretender uma implementação baseada em modelos para a sua aplicação que possa gerir programáticamente utilizando APIs DE REST, O CLI Azure e Azure PowerShell.
>
> **Começar**: Para começar a usar modelos, consulte [os modelos do Gestor de Recursos Azure.](../../azure-resource-manager/templates/template-syntax.md)

## <a name="understanding-accounts-subscriptions-and-billing"></a>Compreender as contas, subscrições e faturação

Como desenvolvedores, gostamos de mergulhar no código e tentar começar o mais rápido possível com a execução das nossas aplicações. Queremos encorajá-lo a começar a trabalhar em Azure o mais facilmente possível. Para ajudar a facilitar as coisas, a Azure oferece um [teste gratuito.](https://azure.microsoft.com/free/) Alguns serviços têm até uma funcionalidade "Experimente gratuitamente", como o [Azure App Service](https://tryappservice.azure.com/), que não obriga sequer a criar uma conta. Por mais divertido que seja mergulhar na codificação e implantar a sua aplicação para o Azure, também é importante levar algum tempo para entender como funciona o Azure. Especificamente, deve entender como funciona do ponto de vista das contas dos utilizadores, subscrições e faturação.

### <a name="what-is-an-azure-account"></a>O que é uma conta do Azure?

Para criar ou trabalhar com uma subscrição do Azure, tem de ter uma conta Azure. Uma conta Azure é simplesmente uma identidade em Azure AD ou em um diretório, como uma organização de trabalho ou escola, que a Azure AD confia. Se não pertencer a tal organização, pode sempre criar uma subscrição utilizando a sua Conta Microsoft, que é fidedigna pela Azure AD. Para saber mais sobre a integração no Windows Server Ative Directory com a Ad AZure, consulte [integrar as suas identidades no local com o Azure Ative Directory](../../active-directory/hybrid/whatis-hybrid-identity.md).

Cada subscrição do Azure tem uma relação de confiança com uma instância do Azure AD. Tal significa que confia nesse diretório para autenticar utilizadores, serviços e dispositivos. Várias subscrições podem confiar no mesmo diretório, mas uma subscrição apenas pode confiar num diretório. Para saber mais, consulte [como as assinaturas Azure estão associadas ao Azure Ative Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Além de definir identidades individuais da conta Azure, também *chamados utilizadores,* pode definir *grupos* em Azure AD. A criação de grupos de utilizadores é uma boa forma de gerir o acesso aos recursos numa subscrição utilizando o controlo de acesso baseado em funções (RBAC). Para aprender a criar grupos, consulte [Criar um grupo na pré-visualização do Azure Ative Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Também pode criar e gerir grupos [utilizando o PowerShell](../../active-directory/enterprise-users/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Gerir as subscrições

Uma subscrição é um agrupamento lógico de serviços Azure que está ligado a uma conta Azure. Uma única conta Azure pode conter várias subscrições. A faturação dos serviços Azure é feita por subscrição. Para obter uma lista das ofertas de subscrição disponíveis por tipo, consulte [os Detalhes da Oferta do Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/). As subscrições do Azure têm um Administrador de Conta que tem controlo total sobre a subscrição. Dispõem ainda de um Administrador de Serviço que tem controlo sobre todos os serviços da subscrição. Para obter informações sobre administradores de subscrição clássicos, consulte [adicionar ou alterar administradores de subscrição do Azure](../../cost-management-billing/manage/add-change-subscription-administrator.md). As contas individuais podem ser concedidas a um controlo pormenorizado dos recursos Azure utilizando [o controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/overview.md).

#### <a name="resource-groups"></a>Grupos de recursos

Quando presta novos serviços Azure, fá-lo numa determinada subscrição. Os serviços individuais Azure, que também são chamados de recursos, são criados no contexto de um grupo de recursos. Os grupos de recursos facilitam a implementação e gestão dos recursos da sua aplicação. Um grupo de recursos deve conter todos os recursos para a sua aplicação com os quais pretende trabalhar como unidade. Pode mover recursos entre grupos de recursos e até mesmo para diferentes subscrições. Para aprender sobre a movimentação de recursos, consulte [mover recursos para novo grupo de recursos ou subscrição.](../../azure-resource-manager/management/move-resource-group-and-subscription.md)

O Azure Resource Explorer é uma ótima ferramenta para visualizar os recursos que já criou na sua subscrição. Para saber mais, consulte [o Use Azure Resource Explorer para visualizar e modificar recursos.](/rest/api/)

#### <a name="grant-access-to-resources"></a>Conceder acesso aos recursos

Quando permite o acesso aos recursos do Azure, é sempre uma boa prática proporcionar aos utilizadores o menor privilégio que é necessário para fazer uma determinada tarefa.

- **Controlo de acesso baseado em funções (Azure RBAC)**: Em Azure, pode conceder acesso a contas de utilizador (principais) num âmbito especificado: subscrição, grupo de recursos ou recursos individuais. O Azure RBAC permite-lhe implantar recursos num grupo de recursos e conceder permissões a um utilizador ou grupo específico. Também permite limitar o acesso apenas aos recursos que pertencem ao grupo de recursos-alvo. Também pode conceder acesso a um único recurso, como uma máquina virtual ou uma rede virtual. Para conceder acesso, atribui uma função ao utilizador, grupo ou principal de serviço. Existem muitos papéis predefinidos, e também pode definir os seus próprios papéis personalizados. Para saber mais, veja [o que é o controlo de acesso baseado em funções do Azure (Azure RBAC)?](../../role-based-access-control/overview.md)

  > **Quando utilizar:** Quando necessitar de uma gestão de acesso fino para utilizadores e grupos ou quando necessitar de fazer de um utilizador um proprietário de uma subscrição.
  >
  > **Começar**: Para saber mais, consulte [Adicionar ou remover atribuições de funções Azure utilizando o portal Azure](../../role-based-access-control/role-assignments-portal.md).

- **Objetos principais do**serviço : Além de fornecer acesso a principais utilizadores e grupos, pode conceder o mesmo acesso a um diretor de serviço.

  > **Quando utilizar:** Quando estiver a gerir programáticamente os recursos da Azure ou a conceder acesso a aplicações. Para mais informações, consulte [a aplicação de Diretório Ativo e o principal do serviço.](../../active-directory/develop/howto-create-service-principal-portal.md)

#### <a name="tags"></a>Etiquetas

O Azure Resource Manager permite-lhe atribuir etiquetas personalizadas a recursos individuais. As etiquetas, que são pares de valor-chave, podem ser úteis quando é necessário organizar recursos para faturação ou monitorização. As tags fornecem-lhe uma forma de rastrear recursos em vários grupos de recursos. Pode atribuir etiquetas das seguintes formas:

* No portal
* No modelo do Gestor de Recursos Azure
* Utilizar a API REST
* Com a CLI do Azure
* Utilizar o PowerShell

Pode atribuir várias tags a cada recurso. Para saber mais, consulte [Usando etiquetas para organizar os seus recursos Azure.](../../azure-resource-manager/management/tag-resources.md)

### <a name="billing"></a>Faturação

Na mudança da computação no local para serviços hospedados na nuvem, o acompanhamento e estimativa do uso do serviço e os custos conexos são preocupações significativas. É importante estimar o custo dos novos recursos para executar mensalmente. Também pode projetar como a faturação parece para um dado mês com base na despesa corrente.

#### <a name="get-resource-usage-data"></a>Obtenha dados de utilização de recursos

O Azure fornece um conjunto de APIs de Billing REST que dão acesso ao consumo de recursos e informações de metadados para subscrições Azure. Estas APIs de Faturação dão-lhe a capacidade de prever e gerir melhor os custos do Azure. Você pode rastrear e analisar gastos em incrementos de hora e criar alertas de gastos. Também pode prever faturação futura com base nas tendências de utilização atuais.

>**Começar**: Para saber mais sobre a utilização das APIs de faturação, consulte [a visão geral do Azure Billing Use e rateCard APIs](../../cost-management-billing/manage/usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Prever custos futuros

Embora seja um desafio estimar os custos com antecedência, a Azure tem ferramentas que podem ajudar. Tem uma [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para ajudar a estimar o custo dos recursos mobilizados. Você também pode usar os recursos de Faturação no portal e as APIs de Billing REST para estimar custos futuros, com base no consumo atual.

>**Começar**: Ver [Utilização de Faturação Azure e Visão geral do RateCard APIs](../../cost-management-billing/manage/usage-rate-card-overview.md).