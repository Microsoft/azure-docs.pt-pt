---
title: Aprenda a terminologia do Azure Service Fabric | Documentos da Microsoft
description: Uma descrição da terminologia do Service Fabric. Aborda os conceitos de terminologia-chave e termos utilizados no restante da documentação.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: chackdan;subramar
ms.assetid: 3a970679-e19e-43b3-9be8-71773f307c57
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/17/2018
ms.author: ryanwi
ms.openlocfilehash: fda6af0f253457aaf3aef1e8444850592255b318
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113678"
---
# <a name="service-fabric-terminology-overview"></a>Descrição geral da terminologia do Service Fabric
O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços dimensionáveis e fiáveis.  Pode [anfitrião do Service Fabric clusters em qualquer lugar](service-fabric-deploy-anywhere.md): Azure, num datacenter no local ou em qualquer fornecedor de cloud.  O Service Fabric é o orquestrador que alimenta [malha do Azure Service Fabric](/azure/service-fabric-mesh). Pode utilizar qualquer estrutura para escrever seus serviços e escolha onde pretende executar a aplicação a partir de várias opções de ambiente. Este artigo fornece detalhes sobre a terminologia usada pelo Service Fabric para compreender os termos usados na documentação.

## <a name="infrastructure-concepts"></a>Conceitos de infraestrutura
**Cluster**: Um conjunto ligado à rede de máquinas virtuais ou físicas, no qual os microsserviços são implementados e geridos.  Os clusters podem ser dimensionados para milhares de máquinas.

**Nó**: Uma máquina ou VM que faça parte de um cluster é chamado um *nó*. É atribuído um nome de nó (uma cadeia) a cada nó. Nós têm características, como as propriedades de colocação. Cada máquina ou a VM tem um serviço do Windows de início automático, `FabricHost.exe`, que começa a ser executado na inicialização e, em seguida, inicia dois executáveis: `Fabric.exe` e `FabricGateway.exe`. Estes dois executáveis compõem o nó. Para cenários de teste, pode alojar vários nós numa única máquina ou VM ao executar várias instâncias `Fabric.exe` e `FabricGateway.exe`.

## <a name="application-and-service-concepts"></a>Aplicação e conceitos do serviço

**Aplicação de Service Fabric malha**: Aplicações de Mesh do Service Fabric são descritas pelo modelo de recursos (arquivos de recursos YAML e JSON) e podem ser implementadas em qualquer ambiente em que executa a Service Fabric.

**Aplicação de Service Fabric nativo**: Aplicativos nativos do Service Fabric são descritos pelo modelo de aplicativo nativo (aplicativo baseado em XML e manifestos de serviço).  Aplicativos nativos do Service Fabric não é possível executar na malha de recursos de infraestrutura do serviço.

### <a name="service-fabric-mesh-application-concepts"></a>Conceitos de aplicação de malha do Service Fabric

**Aplicação**: Um aplicativo é a unidade de implementação, o controle de versão e o tempo de vida de um aplicativo de malha. O ciclo de vida de cada instância da aplicação pode ser gerido de forma independente.  As aplicações são compostas por um ou mais pacotes de código de serviço e as definições. Um aplicativo é definido usando o esquema do modelo de recursos do Azure (RM).  Os serviços são descritos como propriedades do recurso de aplicação num modelo RM.  Redes e volumes utilizados pela aplicação são referenciados pela aplicação.  Ao criar uma aplicação, a aplicação, serviço ou serviços, rede e volumes são modelados com o modelo de recursos do Service Fabric.

**Serviço**: Um serviço num aplicativo representa um microsserviço e executa uma função completa e autónoma. Cada serviço é composto por um ou mais, pacotes de código que descrevem tudo necessários para executar a imagem de contentor associada ao pacote de código.  O número de serviços num aplicativo pode ser dimensionado e reduzir verticalmente.

**Rede**: Um recurso de rede cria uma rede privada para as suas aplicações e é independente das aplicações ou serviços que podem fazer referência a ele. Vários serviços de aplicativos diferentes podem fazer parte da mesma rede. As redes são implementáveis recursos que são referenciados pelos aplicativos.

**Pacote do código**: Pacotes do código descrevem tudo necessários para executar a imagem de contentor associada ao pacote de código, incluindo o seguinte:

* Nome do contentor, versão e do Registro
* Recursos de CPU e memória necessários para cada contentor
* Pontos finais de rede
* Volumes para montar no contentor, fazendo referência um recurso de volume separado.

Todos os pacotes de código definidos como parte de um recurso de aplicação são implementados e ativados em conjunto como um grupo.

**Volume**: Volumes são diretórios que ser montados dentro as instâncias de contentor que pode utilizar para persistir o estado. O driver do volume de ficheiros do Azure monta uma partilha de ficheiros do Azure para um contentor e fornece armazenamento de dados confiável por meio de qualquer API que suporta o armazenamento de ficheiros. Volumes são implementáveis recursos que são referenciados pelos aplicativos.

### <a name="service-fabric-native-application-concepts"></a>Conceitos de aplicação nativa do Service Fabric

**Aplicação**: Um aplicativo é uma coleção de serviços constituintes que realizam uma determinada função ou funções. O ciclo de vida de cada instância da aplicação pode ser gerido de forma independente.

**Serviço**: Um serviço executa uma função completa e autónoma e pode iniciar e executar independentemente dos outros serviços. Um serviço é composto por código, configuração e dados. Para cada serviço, os binários executáveis consiste em código, configuração é composta por definições de serviço que podem ser carregadas no tempo de execução e dados consiste em dados estáticos arbitrários seja consumido pelo serviço.

**Tipo de aplicação**: O nome/versão atribuído a uma coleção de tipos de serviço. Ele é definido num `ApplicationManifest.xml` de ficheiros e incorporados num diretório do pacote de aplicação. O diretório, em seguida, é copiado para armazenamento de imagens do cluster do Service Fabric. Em seguida, pode criar uma aplicação com o nome deste tipo de aplicativo dentro do cluster.

Leitura a [modelo de aplicativo](service-fabric-application-model.md) artigo para obter mais informações.

**Pacote de aplicação**: Um diretório de disco que contém o tipo de aplicação `ApplicationManifest.xml` ficheiro. Referencia os pacotes de serviço para cada tipo de serviço que compõe o tipo de aplicação. Os ficheiros no diretório do pacote de aplicação são copiados para o arquivo de imagens do cluster do Service Fabric. Por exemplo, um pacote de aplicação de um tipo de aplicação de e-mail pode conter referências a um pacote de serviço de fila, um pacote de serviço de front-end e um pacote de serviço de base de dados.

**Com o nome application**: Depois de copiar um pacote de aplicação para o armazenamento de imagens, cria uma instância da aplicação dentro do cluster. Criar uma instância quando especifica o tipo de aplicação do pacote de aplicações, utilizando o respetivo nome ou versão. Cada instância de tipo de aplicação é atribuída um nome de identifier (URI) de recurso uniforme parecido com: `"fabric:/MyNamedApp"`. Dentro de um cluster, pode criar várias aplicações com o nome de um tipo de aplicação único. Também pode criar aplicações com o nome de diferentes tipos de aplicativos. Cada aplicativo nomeado é com a versão e não geridos independentemente.

**Tipo de serviço**: O nome/versão atribuído a um serviço código pacotes, pacotes de dados e pacotes de configuração. O tipo de serviço é definido no `ServiceManifest.xml` de ficheiros e incorporados num diretório do pacote de serviço. Diretório do pacote de serviço, em seguida, é referenciado por um pacote de aplicação `ApplicationManifest.xml` ficheiro. Dentro do cluster, depois de criar um aplicativo nomeado, pode criar um serviço com nome de um dos tipos de serviço do tipo de aplicação. O tipo de serviço `ServiceManifest.xml` ficheiro descreve o serviço.

Leitura a [modelo de aplicativo](service-fabric-application-model.md) artigo para obter mais informações.

Existem dois tipos de serviços:

* **Sem monitoração de estado**: Utilize um serviço sem estado, quando o estado do serviço persistente é armazenado num serviço de armazenamento externo, como o armazenamento do Azure, base de dados do Azure SQL ou do Azure Cosmos DB. Quando o serviço não tem nenhum armazenamento persistente, utilize um serviço sem estado. Por exemplo, para um serviço da Calculadora em que os valores são transmitidos para o serviço, uma computação é realizada que usa esses valores e, em seguida, é apresentado um resultado.
* **Com monitorização de estado**: Utilize um serviço com estado quando pretender que o Service Fabric para gerir o estado do seu serviço através de suas coleções fiáveis ou Reliable Actors modelos de programação. Quando cria um serviço com nome, especifique quantas partições que pretende distribuir seu estado através de escalabilidade. Também pode Especifica quantas vezes para replicar o estado entre os nós de confiabilidade. Cada serviço com nome tem uma única réplica primária e várias réplicas secundárias. Modificar o estado do serviço com nome ao escrever para a réplica primária. Service Fabric, em seguida, replica este estado para todas as réplicas secundárias para manter o seu estado em sincronia. Recursos de infraestrutura do serviço Deteta automaticamente quando uma réplica primária falha e promove uma réplica secundária existente para uma réplica primária. Service Fabric, em seguida, cria uma nova réplica secundária.  

**Réplicas ou instâncias** Consulte código (e no estado de serviços com estado) de um serviço que é implementado e em execução. Ver [réplicas e instâncias](service-fabric-concepts-replica-lifecycle.md).

**Reconfiguração** é o processo de qualquer alteração no conjunto de réplicas de um serviço. Ver [reconfiguração](service-fabric-concepts-reconfiguration.md).

**Pacote de serviço**: Um diretório de disco que contém o tipo de serviço `ServiceManifest.xml` ficheiro. Este ficheiro faz referência a código, dados estáticos e pacotes de configuração para o tipo de serviço. Os ficheiros no diretório do pacote de serviço são referenciados pelo tipo de aplicação `ApplicationManifest.xml` ficheiro. Por exemplo, um pacote de serviço pode se referir ao código, dados estáticos e pacotes de configuração que compõem um serviço de base de dados.

**Com o nome serviço**: Depois de criar uma aplicação com nome, pode criar uma instância de um dos respetivos tipos de serviço dentro do cluster. Especifique o tipo de serviço com o seu nome/versão. Cada instância de tipo de serviço é atribuída um nome URI de âmbito no URI de seu aplicativo com nome. Por exemplo, se criar um serviço dentro de uma aplicação com o nome "MyNamedApp" com o nome "MyDatabase", o URI é semelhante a: `"fabric:/MyNamedApp/MyDatabase"`. Dentro de um aplicativo nomeado, pode criar vários serviços com nome. Cada serviço com nome pode ter seu próprio esquema de partição e a instância ou contagens de réplica.

**Pacote do código**: Um diretório de disco que contém os ficheiros executáveis do tipo de serviço, normalmente, ficheiros. EXE/DLL. Os ficheiros no diretório do pacote de código são referenciados pelo tipo de serviço `ServiceManifest.xml` ficheiro. Quando cria um serviço com nome, o pacote do código é copiado para o nó ou nós selecionado para executar o serviço com nome. Em seguida, o código começa a ser executada. Existem dois tipos de executáveis de pacote de código:

* **Executáveis convidados**: Executáveis que são executados como-se encontra num sistema de operativo anfitrião (Windows ou Linux). Desses executáveis não ligar a ou fazer referência a quaisquer ficheiros de runtime do Service Fabric e, portanto, não utilizar quaisquer modelos de programação do Service Fabric. Desses executáveis são não é possível utilizar alguns recursos de Service Fabric, como o serviço de nomenclatura para a deteção de ponto final. Executáveis de convidado não é possível reportar a métricas de carga que são específicas para cada instância de serviço.
* **Executáveis de anfitrião do serviço**: Ficheiros executáveis que utilizam o Service Fabric modelos de programação ao ligar aos ficheiros de runtime do Service Fabric, a ativação de funcionalidades do Service Fabric. Por exemplo, uma instância de serviço com nome pode registar os pontos finais com o Service Fabric Naming Service e também pode comunicar as métricas de carregamento.

**Pacote de dados**: Um diretório de disco que contém ficheiros de dados estáticos, só de leitura do tipo de serviço, normalmente, fotografias, arquivos de som e vídeo. Os ficheiros no diretório do pacote de dados são referenciados pelo tipo de serviço `ServiceManifest.xml` ficheiro. Quando cria um serviço com nome, o pacote de dados é copiado para o nó ou nós selecionado para executar o serviço com nome. O código começa a ser executado e agora pode acessar os arquivos de dados.

**Pacote de configuração**: Um diretório de disco que contém ficheiros de configuração estática, só de leitura do tipo de serviço, normalmente, os ficheiros de texto. Os ficheiros no diretório do pacote de configuração são referenciados pelo tipo de serviço `ServiceManifest.xml` ficheiro. Quando cria um serviço com nome, os ficheiros do pacote de configuração são copiados de um ou mais nós selecionado para executar o serviço com nome. Em seguida, o código começa a executar e pode agora acesso os ficheiros de configuração.

**Contentores**: Por predefinição, o Service Fabric implementa e ativa a serviços como processos. Service Fabric também pode implementar serviços nas imagens de contentor. Os contentores são uma tecnologia de Virtualização que Virtualiza o sistema operacional subjacente de aplicativos. Um aplicativo e o tempo de execução, dependências e bibliotecas do sistema, execute dentro de um contentor. O contentor completa, tem acesso privado para a vista de contentor próprio isolado das construções de sistema operativo. O Service Fabric suporta contentores do Docker em contentores de Linux e Windows Server. Para obter mais informações, leia [Service Fabric e contentores](service-fabric-containers-overview.md).

**Esquema de partição**: Quando cria um serviço com nome, especifique um esquema de partição. Serviços com uma quantidade substancial de estado dividir os dados em partições, o que se propaga o estado em todos os nós do cluster. Ao dividir os dados entre partições, pode dimensionar o estado do serviço com nome. Dentro de uma partição, a serviços com nome sem monitoração de estado tem instâncias, enquanto com monitoração de estado com o nome dos serviços tem réplicas. Normalmente, com o nome de serviços sem estado tem apenas uma partição, porque não têm nenhum Estado interno. Fornecem as instâncias de partição de disponibilidade. Se uma instância falhar, outras instâncias continuam a funcionar normalmente e, em seguida, o Service Fabric cria uma nova instância. Com monitorização de estado com o nome dos serviços de manter o estado dentro de réplicas e cada partição tem seu próprio réplica definir, para que o estado é mantido em sincronização. Se uma réplica falhar, o Service Fabric cria uma nova réplica a partir das réplicas existentes.

Leitura a [serviços fiáveis do Service Fabric da partição](service-fabric-concepts-partitioning.md) artigo para obter mais informações.

## <a name="system-services"></a>Serviços do sistema
Existem serviços do sistema que são criados em cada cluster que fornecem as capacidades da plataforma do Service Fabric.

**Serviço de nomenclatura**: Cada cluster do Service Fabric tem um serviço de nomenclatura, que resolve nomes de serviço para uma localização do cluster. Gerir os nomes de serviço e propriedades, como um internet sistema de nome de domínio (DNS) para o cluster. Os clientes comunicam de forma segura com qualquer nó do cluster com o serviço de nomenclatura para resolver um nome de serviço e a respetiva localização. Mover aplicações dentro do cluster. Por exemplo, isto pode ser devido a falhas, balanceamento de recurso ou o redimensionamento do cluster. Pode desenvolver serviços e clientes que resolver a localização de rede atual. Os clientes obter o endereço IP real do computador e a porta em que está em execução.

Leia [comunicar com serviços](service-fabric-connect-and-communicate-with-services.md) para obter mais informações sobre a comunicação de cliente e o serviço de APIs que funcionam com o serviço de nomenclatura.

**Serviço de imagem Store**: Cada cluster do Service Fabric tem um serviço de imagem Store, onde os pacotes de aplicações implementada, com a versão são mantidos. Copiar um pacote de aplicação para o Store de imagem e, em seguida, registar o tipo de aplicação contido dentro desse pacote de aplicação. Depois do tipo de aplicação é aprovisionado, cria uma aplicação com o nome do mesmo. Pode anular o registo de um tipo de aplicação do serviço de imagem Store depois de todos os seus aplicativos com nome tem sido eliminados.

Leia [compreender a definição ImageStoreConnectionString](service-fabric-image-store-connection-string.md) para obter mais informações sobre o serviço de imagem Store.

Leitura a [implementar uma aplicação](service-fabric-deploy-remove-applications.md) artigo para obter mais informações sobre a implantação de aplicativos para o serviço de imagem Store.

**Serviço do Gestor de ativação pós-falha**: Cada cluster do Service Fabric tem um serviço de Gestor de ativação pós-falha que é responsável pelas seguintes ações:
   - Executa funções relacionadas com a elevada disponibilidade e consistência dos serviços.
   - Orquestra as atualizações de aplicação e do cluster.
   - Interage com outros componentes do sistema.

**Serviço de Gestor de reparação**: Este é um serviço de sistema opcional que permite as ações de reparação ser efetuada num cluster de uma forma que é seguro, automatizável e transparente. Gestor de reparação é utilizado em:
   - Efetuar a manutenção do Azure repara na [durabilidade Silver e Gold](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) clusters do Azure Service Fabric.
   - Realização de ações de reparação de [Patch Orchestration Application](service-fabric-patch-orchestration-application.md)

## <a name="deployment-and-application-models"></a>Modelos de implementação e aplicação 

Para implementar os seus serviços, terá de descrever como deve ser executado. O Service Fabric suporta três modelos de implementação diferentes:

### <a name="resource-model-preview"></a>Modelo de recursos (pré-visualização)
Recursos de infraestrutura do serviço são tudo o que podem ser implementados individualmente para o Service Fabric; incluindo aplicações, serviços, redes e volumes. Recursos são definidos através de um ficheiro JSON, que pode ser implementado para um ponto de extremidade do cluster.  Para a malha de recursos de infraestrutura de serviço, é utilizado o esquema do modelo de recursos do Azure. Um esquema de ficheiro YAML também pode ser usado para arquivos de definição para criar mais facilmente. Podem ser implementados recursos em qualquer lugar que executa a Service Fabric. O modelo de recursos é a forma mais simples para descrever as suas aplicações do Service Fabric. Seu foco principal é sobre a implantação simple e gestão de serviços em contentores. Para saber mais, leia [introdução para o modelo de recursos do Service Fabric](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Modelo nativo
O modelo de aplicação nativa fornece seus aplicativos com acesso completo de nível baixo para o Service Fabric. Aplicações e serviços são definidos como tipos registados nos ficheiros de manifestos XML.

O modelo nativo suporta o Reliable Services e estruturas de Reliable Actors, que fornece acesso a APIs de runtime do Service Fabric e gestão de clusters de APIs em c# e Java. O modelo nativo também suporta arbitrários contentores e executáveis. O modelo nativo não é suportado a [malha de recursos de infraestrutura do serviço ambiente](/azure/service-fabric-mesh/service-fabric-mesh-overview).

**Serviços fiáveis**: Uma API para criar serviços com e sem estado. Serviços com estado armazenam seus Estados no Reliable Collections, como um dicionário ou uma fila. Também é possível conectar várias pilhas de comunicação, como a Web API e o Windows Communication Foundation (WCF).

**Reliable Actors**: Uma API para criar objetos com e sem estado através do modelo de programação de Ator virtual. Esse modelo é útil quando tem muitas unidades independentes de computação ou de estado. Este modelo utiliza um modelo thread turnos, então é melhor evitar o código que chama outros atores ou serviços porque um ator individual não é possível processar outros pedidos recebidos até que todos os seus pedidos de saída são concluídos.

Também pode executar seus aplicativos existentes no Service Fabric:

**Contentores**:  Service Fabric suporta a implementação de contentores do Docker em contentores de Linux e Windows Server no Windows Server 2016, juntamente com suporte para o modo de isolamento de Hyper-V. Nos recursos de infraestrutura do serviço [modelo de aplicativo](service-fabric-application-model.md), um contentor representa um host de aplicativo no qual serviço várias réplicas são colocadas. Service Fabric, pode executar quaisquer contentores e o cenário é semelhante do cenário do executável de convidado, onde empacotar uma aplicação existente num contentor. Além disso, pode [executar serviços do Service Fabric no interior de contentores](service-fabric-services-inside-containers.md) também.

**Executáveis convidados**: Pode executar qualquer tipo de código, como o C++, Java ou node. js no Azure Service Fabric como um serviço. Service Fabric se refere a esses tipos de serviços como executáveis convidados, o que são tratadas como serviços sem estado. As vantagens da execução de um convidado executável num cluster do Service Fabric incluem elevada disponibilidade, monitorização de estado de funcionamento, gerenciamento de ciclo de vida de aplicativos, alta densidade e capacidade de deteção.

Leitura a [escolher um modelo de programação para o seu serviço](service-fabric-choose-framework.md) artigo para obter mais informações.

### <a name="docker-compose"></a>Docker Compose 
[Docker Compose](https://docs.docker.com/compose/) faz parte do projeto Docker. O Service Fabric fornece suporte limitado para [implantando aplicativos usando o modelo de Docker Compose](service-fabric-docker-compose.md).

## <a name="environments"></a>Ambientes

Service Fabric é uma tecnologia de plataforma de código aberto baseados em vários produtos e serviços diferentes. A Microsoft fornece as seguintes opções:

 - **O Azure Service Fabric malha**: Um serviço totalmente gerido para executar aplicações do Service Fabric no Microsoft Azure.
 - **Azure Service Fabric**: O Azure alojado oferta de cluster do Service Fabric. Ele fornece integração entre o Service Fabric e a infraestrutura do Azure, juntamente com a gestão de atualização e configuração de clusters do Service Fabric.
 - **Autónomo do Service Fabric**: Um conjunto de ferramentas de instalação e configuração para [implementar clusters do Service Fabric em qualquer lugar](/azure/service-fabric/service-fabric-deploy-anywhere) (no local ou em qualquer fornecedor de cloud). Não é gerido pelo Azure.
 - **Cluster de desenvolvimento do Service Fabric**: Fornece uma experiência de desenvolvimento local no Windows, Linux ou Mac para desenvolvimento de aplicações do Service Fabric.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Matriz de suporte de ambiente, o framework e o modelo de implementação
Ambientes diferentes tem vários níveis de suporte de estruturas e modelos de implementação. A tabela seguinte descreve a estrutura de suporte e combinações de modelo de implementação.

| Tipo de aplicação | Descrito por | Malha de recursos de infraestrutura do serviço do Azure | Clusters de recursos de infraestrutura do serviço do Azure (qualquer sistema operacional)| Local cluster | Cluster autónomo |
|---|---|---|---|---|---|
| Aplicações de malha do Service Fabric | Modelo de recursos (YAML & JSON) | Suportadas |Não suportado | Windows – suportado, Linux e Mac não suportado | Windows e não suportados |
|Aplicativos nativos do Service Fabric | Modelo de aplicativo nativo (XML) | Não suportado| Suportadas|Suportadas|Windows – suportado|

A tabela seguinte descreve os modelos de aplicação diferente e as ferramentas existentes para os mesmos em relação a Service Fabric.

| Tipo de aplicação | Descrito por | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Aplicações de malha do Service Fabric | Modelo de recursos (YAML & JSON) | VS 2017 |Não suportado |Não suportado | Suportado - apenas ambiente de malha | Não suportado|
|Aplicativos nativos do Service Fabric | Modelo de aplicativo nativo (XML) | VS 2017 e VS 2015| Suportadas|Suportadas|Suportadas|Suportadas|

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o Service Fabric:

* [Descrição geral do Service Fabric](service-fabric-overview.md)
* [Why a microservices approach to building applications?](service-fabric-overview-microservices.md) (Porquê seguir a abordagem de microsserviços para criar aplicações?)
* [Cenários de aplicações](service-fabric-application-scenarios.md)

Para saber mais sobre a malha de recursos de infraestrutura do serviço:

* [Descrição geral da malha de recursos de infraestrutura do serviço](/azure/service-fabric-mesh/service-fabric-mesh-overview)
