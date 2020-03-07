---
title: Aprenda terminologia de tecido de serviço Azure
description: Aprenda a terminologia e conceitos de tecido de serviço chave utilizados no resto da documentação.
author: masnider
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: masnider
ms.custom: sfrev
ms.openlocfilehash: a9266c2a8d2ad179cfdb12e367a14f37d1abc9b3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389179"
---
# <a name="service-fabric-terminology-overview"></a>Visão geral da terminologia do tecido de serviço

O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços dimensionáveis e fiáveis.  Pode [hospedar clusters service Fabric em qualquer lugar](service-fabric-deploy-anywhere.md): Azure, num centro de dados no local, ou em qualquer fornecedor de nuvem.  Service Fabric é o orquestrador que alimenta a malha de tecido de [serviço Azure.](/azure/service-fabric-mesh) Pode utilizar qualquer enquadramento para escrever os seus serviços e escolher onde executar a aplicação a partir de múltiplas opções ambientais. Este artigo detalha a terminologia utilizada pelo Service Fabric para compreender os termos utilizados na documentação.

## <a name="infrastructure-concepts"></a>Conceitos de infraestrutura

**Cluster**: Um conjunto de máquinas virtuais ou físicas ligadas à rede nas quais os seus microserviços são implantados e geridos.  Os clusters podem ser dimensionados para milhares de máquinas.

**Nó**: Uma máquina ou VM que faz parte de um aglomerado é chamado de *nó*. Cada nó é atribuído um nome de nó (corda). Os nós têm características, como propriedades de colocação. Cada máquina ou VM tem um serviço Windows de arranque automático, `FabricHost.exe`, que começa a funcionar sobre o arranque e, em seguida, inicia dois executáveis: `Fabric.exe` e `FabricGateway.exe`. Estes dois executáveis compõem o nó. Para cenários de teste, pode hospedar vários nós numa única máquina ou VM executando várias instâncias de `Fabric.exe` e `FabricGateway.exe`.

## <a name="application-and-service-concepts"></a>Conceitos de aplicação e serviço

**Aplicação de malha**de tecido de serviço : As aplicações de malha de tecido de serviço são descritas pelo Modelo de Recursos (ficheiros de recursos YAML e JSON) e podem ser implantadas em qualquer ambiente onde o Tecido de Serviço funciona.

**Aplicação nativa de tecido de serviço**: Aplicações nativas de tecido de serviço são descritas pelo Modelo de Aplicação Nativa (aplicação baseada em XML e manifestos de serviço).  Aplicações nativas de tecido de serviço não podem ser executadas em malha de tecido de serviço.

### <a name="service-fabric-mesh-application-concepts"></a>Conceitos de aplicação de malha de tecido de serviço

**Aplicação**: Uma aplicação é a unidade de implantação, versão e vida útil de uma aplicação Malha. O ciclo de vida de cada instância de aplicação pode ser gerido de forma independente.  As aplicações são compostas por um ou mais pacotes e configurações de código de serviço. Uma aplicação é definida utilizando o esquema do Modelo de Recursos Azure (RM).  Os serviços são descritos como propriedades do recurso de aplicação num modelo RM.  As redes e volumes utilizados pela aplicação são referenciados pela aplicação.  Ao criar uma aplicação, a aplicação, serviço(s), rede e volume(s) são modelados utilizando o Modelo de Recursos de Tecido de Serviço.

**Serviço**: Um serviço numa aplicação representa um microserviço e desempenha uma função completa e autónoma. Cada serviço é composto por um ou mais pacotes de código que descrevem tudo o que é necessário para executar a imagem do recipiente associada ao pacote de código.  O número de serviços numa aplicação pode ser dimensionado para cima e para baixo.

**Rede**: Um recurso de rede cria uma rede privada para as suas aplicações e é independente das aplicações ou serviços que podem referir-se a ela. Vários serviços de diferentes aplicações podem fazer parte da mesma rede. As redes são recursos implantáveis que são referenciados por aplicações.

**Pacote de código**: Os pacotes de código descrevem tudo o que é necessário para executar a imagem do recipiente associada ao pacote de código, incluindo o seguinte:

* Nome, versão e registo do contentor
* CPU e recursos de memória necessários para cada recipiente
* Pontos finais da rede
* Volumes para montar no recipiente, fazendo referência a um recurso de volume separado.

Todos os pacotes de código definidos como parte de um recurso de aplicação são implantados e ativados em conjunto como um grupo.

**Volume**: Os volumes são diretórios que são montados dentro dos casos do seu contentor que pode utilizar para persistir no estado. O controlador de volume Azure Files monta uma partilha de Ficheiros Azure para um recipiente e fornece armazenamento de dados fiável através de qualquer API que suporte o armazenamento de ficheiros. Os volumes são recursos implantáveis que são referenciados por aplicações.

### <a name="service-fabric-native-application-concepts"></a>Conceitos de aplicação nativa de tecido de serviço

**Aplicação**: Uma aplicação é uma coleção de serviços constituintes que desempenham uma determinada função ou funções. O ciclo de vida de cada instância de aplicação pode ser gerido de forma independente.

**Serviço**: Um serviço desempenha uma função completa e autónoma e pode iniciar e funcionar independentemente de outros serviços. Um serviço é composto por código, configuração e dados. Para cada serviço, o código consiste nos binários executáveis, a configuração consiste em definições de serviço que podem ser carregadas no tempo de execução, e os dados consistem em dados estáticos arbitrários a serem consumidos pelo serviço.

Tipo de **aplicação**: O nome/versão atribuído a uma coleção de tipos de serviço. É definido num ficheiro `ApplicationManifest.xml` e incorporado num diretório de pacotes de aplicações. O diretório é então copiado para a loja de imagens do cluster Service Fabric. Em seguida, pode criar uma aplicação nomeada deste tipo de aplicação dentro do cluster.

Leia o artigo modelo [da Aplicação](service-fabric-application-model.md) para mais informações.

**Pacote de aplicação**: Um diretório de disco contendo o ficheiro `ApplicationManifest.xml` do tipo de aplicação. Referencia os pacotes de serviço para cada tipo de serviço que compõe o tipo de aplicação. Os ficheiros do diretório do pacote de aplicações são copiados para a loja de imagens do cluster Service Fabric. Por exemplo, um pacote de aplicação para um tipo de pedido de e-mail pode conter referências a um pacote de serviço de fila, um pacote de serviço frontend e um pacote de serviço de base de dados.

**Aplicação nomeada**: Depois de copiar um pacote de aplicação para a loja de imagens, cria uma instância da aplicação dentro do cluster. Cria uma instância quando especifica o tipo de aplicação do pacote de aplicação, utilizando o seu nome ou versão. Cada instância do tipo de aplicação é atribuída a um nome de identificador de recursos uniforme (URI) que se parece com: `"fabric:/MyNamedApp"`. Dentro de um cluster, pode criar múltiplas aplicações nomeadas a partir de um único tipo de aplicação. Também pode criar aplicações nomeadas de diferentes tipos de aplicações. Cada aplicação nomeada é gerida e versão independentemente.

**Tipo**de serviço : O nome/versão atribuído aos pacotes de código de um serviço, pacotes de dados e pacotes de configuração. O tipo de serviço é definido no ficheiro `ServiceManifest.xml` e incorporado num diretório de pacote de serviço. O diretório do pacote de serviço é então referenciado pelo ficheiro `ApplicationManifest.xml` de um pacote de aplicações. Dentro do cluster, depois de criar uma aplicação nomeada, pode criar um serviço nomeado a partir de um dos tipos de serviço do tipo de aplicação. O ficheiro `ServiceManifest.xml` do tipo de serviço descreve o serviço.

Leia o artigo modelo [da Aplicação](service-fabric-application-model.md) para mais informações.

Existem dois tipos de serviços:

* **Apátrida**: Utilize um serviço apátrida quando o estado persistente do serviço for armazenado num serviço de armazenamento externo, como o Azure Storage, o Azure SQL Database ou o Azure Cosmos DB. Utilize um serviço apátrida quando o serviço não tiver armazenamento persistente. Por exemplo, para um serviço de calculadora onde os valores são passados para o serviço, é realizado um cálculo que utiliza estes valores, e depois um resultado é devolvido.
* **Atenção:** Utilize um serviço imponente quando quiser que o Tecido de Serviço gere o estado do seu serviço através dos seus modelos de programação Reliable Collections ou Reliable Actors. Quando criar um serviço nomeado, especifique quantas divisórias pretende espalhar o seu estado para escalabilidade. Especifique também quantas vezes replicar o seu estado através de nós, para fiabilidade. Cada serviço nomeado tem uma única réplica primária e múltiplas réplicas secundárias. Modifica o estado do seu serviço quando escreve para a réplica primária. Service Fabric replica então este estado a todas as réplicas secundárias para manter o seu estado sincronizado. O Tecido de Serviço deteta automaticamente quando uma réplica primária falha e promove uma réplica secundária existente para uma réplica primária. Service Fabric cria então uma nova réplica secundária.  

**Réplicas ou instâncias** referem-se ao código (e estado para serviços estatais) de um serviço que é implantado e em execução. Ver [Réplicas e instâncias.](service-fabric-concepts-replica-lifecycle.md)

**A reconfiguração** refere-se ao processo de qualquer alteração no conjunto de réplicas de um serviço. Ver [Reconfiguração](service-fabric-concepts-reconfiguration.md).

**Pacote de serviço**: Um diretório de disco contendo o ficheiro `ServiceManifest.xml` do tipo de serviço. Este ficheiro refere o código, os dados estáticos e os pacotes de configuração para o tipo de serviço. Os ficheiros do diretório do pacote de serviço são referenciados pelo ficheiro `ApplicationManifest.xml` do tipo de aplicação. Por exemplo, um pacote de serviço pode referir-se ao código, dados estáticos e pacotes de configuração que compõem um serviço de base de dados.

**Serviço nomeado**: Depois de criar uma aplicação nomeada, pode criar uma instância de um dos seus tipos de serviço dentro do cluster. Especifica o tipo de serviço utilizando o seu nome/versão. Cada instância do tipo de serviço é atribuída a um nome URI com o nome URI da sua aplicação. Por exemplo, se criar um serviço "MyDatabase" nomeado dentro de uma aplicação nomeada "MyNamedApp", o URI parece: `"fabric:/MyNamedApp/MyDatabase"`. Dentro de uma aplicação nomeada, pode criar vários serviços nomeados. Cada serviço nomeado pode ter o seu próprio esquema de partição e contagens de instância ou réplica.

**Pacote de código**: Um diretório de disco contendo os ficheiros executáveis do tipo de serviço, normalmente ficheiros EXE/DLL. Os ficheiros do diretório do pacote de código são referenciados pelo ficheiro `ServiceManifest.xml` do tipo de serviço. Quando cria um serviço nomeado, o pacote de código é copiado para o nó ou nós selecionados para executar o serviço nomeado. Então o código começa a funcionar. Existem dois tipos de executáveis em pacotes de código:

* **Executáveis de hóspedes**: Executáveis que funcionam como está no sistema operativo anfitrião (Windows ou Linux). Estes executíveis não ligam ou referenciam quaisquer ficheiros de execução do Tecido de Serviço e, portanto, não utilizam nenhum modelo de programação do Tecido de Serviço. Estes executáveis são incapazes de usar algumas funcionalidades de Tecido de Serviço, como o serviço de nomeação para descoberta de ponto final. Executáveis de hóspedes não podem reportar métricas de carga específicas para cada instância de serviço.
* **Executáveis executáveis**do anfitrião do serviço : Executáveis que utilizam modelos de programação de tecido de serviço ligando-se a ficheiros de tempo de execução de tecido de serviço, permitindo funcionalidades de Tecido de Serviço. Por exemplo, uma instância de serviço nomeada pode registar pontos finais com o Serviço de Nomeação do Tecido de Serviço e também pode reportar métricas de carga.

**Pacote de dados**: Um diretório de disco que contém os ficheiros de dados estáticos e apenas de leitura do tipo de serviço, tipicamente ficheiros de fotografia, som e vídeo. Os ficheiros no diretório do pacote de dados são referenciados pelo ficheiro `ServiceManifest.xml` do tipo de serviço. Quando cria um serviço nomeado, o pacote de dados é copiado para o nó ou nós selecionados para executar o serviço nomeado. O código começa a funcionar e agora pode aceder aos ficheiros de dados.

**Pacote de configuração**: Um diretório de disco que contém os ficheiros estáticos e de configuração do tipo de serviço, normalmente ficheiros de texto. Os ficheiros do diretório do pacote de configuração são referenciados pelo ficheiro `ServiceManifest.xml` do tipo de serviço. Quando cria um serviço nomeado, os ficheiros do pacote de configuração são copiados para um ou mais nós selecionados para executar o serviço nomeado. Em seguida, o código começa a ser executado e pode agora aceder aos ficheiros de configuração.

**Contentores**: Por padrão, o Tecido de Serviço implanta e ativa os serviços como processos. O Serviço Fabric também pode implantar serviços em imagens de contentores. Os contentores são uma tecnologia de virtualização que abstrata o sistema operativo subjacente a partir de aplicações. Uma aplicação e o seu tempo de funcionamento, dependências e bibliotecas de sistemas funcionam dentro de um contentor. O contentor tem acesso total e privado à vista isolada do próprio contentor das construções do sistema operativo. O Serviço Fabric suporta recipientes windows server e recipientes Docker no Linux. Para mais informações, leia [O Tecido de Serviço e os recipientes.](service-fabric-containers-overview.md)

Esquema de **partição**: Quando cria um serviço nomeado, especifica um esquema de partição. Os serviços com quantidades substanciais de Estado dividem os dados entre divisórias, o que espalha o Estado pelos nós do cluster. Dividindo os dados entre divisórias, o estado do seu serviço nomeado pode escalar. Dentro de uma partição, os serviços nomeados apátridas têm casos, enquanto os serviços nomeados pelo Estado têm réplicas. Normalmente, os serviços apátridas têm apenas uma divisão, porque não têm estado interno. Os casos de partição fornecem disponibilidade. Se uma instância falhar, outras instâncias continuam a funcionar normalmente, e então o Service Fabric cria uma nova instância. Os serviços de nome sinuoso mantêm o seu estado dentro das réplicas e cada partição tem a sua própria réplica definida para que o Estado seja mantido em sincronização. Se uma réplica falhar, o Service Fabric constrói uma nova réplica das réplicas existentes.

Leia o artigo de serviços fiáveis do [Serviço de Partição](service-fabric-concepts-partitioning.md) Para obter mais informações.

## <a name="system-services"></a>Serviços de sistema

Existem serviços de sistema que são criados em todos os clusters que fornecem as capacidades da plataforma do Service Fabric.

**Serviço de Nomeação**: Cada cluster de tecido de serviço tem um Serviço de Nomeação, que resolve nomes de serviço para um local no cluster. Gere os nomes e propriedades do serviço, como um Sistema de Nome de Domínio de Internet (DNS) para o cluster. Os clientes comunicam com segurança com qualquer nó no cluster utilizando o Serviço de Nomeação para resolver um nome de serviço e a sua localização. As aplicações movem-se dentro do cluster. Por exemplo, isto pode ser devido a falhas, equilíbrio de recursos ou redimensionamento do cluster. Pode desenvolver serviços e clientes que resolvam a localização atual da rede. Os clientes obtêm o endereço IP da máquina real e a porta onde está atualmente em funcionamento.

Leia [Comunicar com serviços](service-fabric-connect-and-communicate-with-services.md) para obter mais informações sobre as APIs de comunicação do cliente e serviço que trabalham com o Serviço de Nomeação.

**Serviço image Store**: Cada cluster de tecido de serviço tem um serviço image store onde são mantidos pacotes de aplicações implantados e versais. Copie um pacote de candidatura para a Loja de Imagens e, em seguida, registe o tipo de aplicação contido nesse pacote de aplicações. Após o fornecimento do tipo de candidatura, cria-se uma aplicação nomeada a partir do mesmo. Pode desregistar um tipo de pedido no serviço Image Store depois de todas as suas aplicações nomeadas terem sido eliminadas.

[Leia Compreender a definição ImageStoreConnectionString](service-fabric-image-store-connection-string.md) para obter mais informações sobre o serviço Image Store.

Leia o artigo de [aplicação](service-fabric-deploy-remove-applications.md) para obter mais informações sobre a implementação de aplicações no serviço Image Store.

**Serviço Failover Manager**: Cada cluster de tecido de serviço tem um serviço Failover Manager responsável pelas seguintes ações:

 - Desempenha funções relacionadas com elevada disponibilidade e consistência dos serviços.
 - Orquestra a aplicação e upgrades de cluster.
 - Interage com outros componentes do sistema.

**Serviço de Gestor de Reparação**: Este é um serviço de sistema opcional que permite que as ações de reparação sejam realizadas num cluster de uma forma segura, autómável e transparente. O gestor de reparação é utilizado em:

   - Efetuar reparações de manutenção Azure em clusters de tecido de serviço azure de [prata e ouro.](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)
   - Realização de ações de reparação para aplicação de [orquestração de patch](service-fabric-patch-orchestration-application.md)

## <a name="deployment-and-application-models"></a>Modelos de implementação e aplicação

Para implementar os seus serviços, precisa descrever como devem funcionar. O Service Fabric suporta três modelos de implantação diferentes:

### <a name="resource-model-preview"></a>Modelo de recurso (pré-visualização)

Recursos de Tecido de Serviço são tudo o que pode ser implantado individualmente para o Tecido de Serviço; incluindo aplicações, serviços, redes e volumes. Os recursos são definidos usando um ficheiro JSON, que pode ser implantado para um ponto final de cluster.  Para malha de tecido de serviço, é utilizado o esquema do Modelo de Recurso Azure. Um esquema de ficheiro YAML também pode ser usado para ficheiros de definição de autor mais facilmente. Os recursos podem ser implantados em qualquer lugar que o Tecido de Serviço saisse. O modelo de recurso é a forma mais simples de descrever as suas aplicações de Tecido de Serviço. O seu foco principal é a simples implantação e gestão de serviços contentorizados. Para saber mais, leia Introdução ao Modelo de Recursos de [Tecido de Serviço](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Modelo nativo

O modelo de aplicação nativa fornece às suas aplicações acesso completo de baixo nível ao Tecido de Serviço. As aplicações e serviços são definidos como tipos registados em ficheiros manifestos XML.

O modelo nativo suporta os quadros Reliable Services and Reliable Actors, que proporciona acesso às C# APIs de tempo de execução do Tecido de Serviço e apis de gestão de clusters em Java. O modelo nativo também suporta recipientes arbitrários e executáveis. O modelo nativo não é suportado no ambiente de malha de [tecido de serviço.](/azure/service-fabric-mesh/service-fabric-mesh-overview)

**Serviços fiáveis**: Uma API para a construção de serviços apátridas e apátridas. Os serviços estatais armazenam o seu estado em Coleções Fiáveis, como um dicionário ou uma fila. Também pode ligar várias pilhas de comunicação, tais como Web API e Windows Communication Foundation (WCF).

**Atores fiáveis**: Uma API para construir objetos apátridas e apátridas através do modelo de programação virtual do Ator. Este modelo é útil quando se tem muitas unidades independentes de computação ou estado. Este modelo usa um modelo de threading baseado em turnos, por isso é melhor evitar código que chama para outros atores ou serviços porque um ator individual não pode processar outros pedidos de entrada até que todos os seus pedidos de saída estejam terminados.

Também pode executar as suas aplicações existentes no Service Fabric:

**Recipientes**: O tecido de serviço suporta a implementação de recipientes Docker em recipientes Linux e Windows Server no Windows Server 2016, juntamente com suporte para o modo de isolamento Hyper-V. No modelo de [aplicação](service-fabric-application-model.md)Service Fabric, um recipiente representa um hospedeiro de aplicação no qual são colocadas várias réplicas de serviço. O Service Fabric pode executar qualquer contentor, e o cenário é semelhante ao cenário executável do hóspede, onde você embala uma aplicação existente dentro de um recipiente. Além disso, também pode executar serviços de Tecido de Serviço dentro de [contentores.](service-fabric-services-inside-containers.md)

**Executáveis de hóspedes:** Pode executar qualquer tipo de código, como Node.js, Python, Java ou C++ em Azure Service Fabric como serviço. Service Fabric refere-se a este tipo de serviços como executáveis de hóspedes, que são tratados como serviços apátridas. As vantagens de executar um hóspede executável num cluster de Tecido de Serviço incluem alta disponibilidade, monitorização da saúde, gestão do ciclo de vida da aplicação, alta densidade e descoberta.

Leia o [modelo de programação Escolha um modelo](service-fabric-choose-framework.md) de programação para o seu artigo de serviço para mais informações.

### <a name="docker-compose"></a>Docker Compor 

[Docker Compose](https://docs.docker.com/compose/) faz parte do projeto Docker. O Service Fabric fornece suporte limitado para a implementação de [aplicações utilizando o modelo Docker Compose](service-fabric-docker-compose.md).

## <a name="environments"></a>Ambientes

O Service Fabric é uma tecnologia de plataforma de código aberto em que vários serviços e produtos diferentes se baseiam. A Microsoft fornece as seguintes opções:

 - Malha de tecido de **serviço Azure**: Um serviço totalmente gerido para executar aplicações de tecido de serviço no Microsoft Azure.
 - **Azure Service Fabric**: O Azure acolheu a oferta de cluster de tecido de serviço. Proporciona integração entre o Tecido de Serviço e a infraestrutura Azure, juntamente com a gestão de upgrade e configuração dos clusters service Fabric.
 - **Serviço Tecido autónomo**: Um conjunto de ferramentas de instalação e configuração para [implantar clusters de tecido](/azure/service-fabric/service-fabric-deploy-anywhere) de serviço em qualquer lugar (no local ou em qualquer fornecedor de nuvem). Não gerido por Azure.
 - **Cluster**de desenvolvimento de tecido de serviço : Proporciona uma experiência de desenvolvimento local em Windows, Linux ou Mac para o desenvolvimento de aplicações de Tecido de Serviço.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Matriz de suporte ao modelo de ambiente, enquadramento e implementação

Ambientes diferentes têm diferentes níveis de apoio a quadros e modelos de implantação. A tabela seguinte descreve as combinações de modelos de estrutura e de implantação suportadas.

| Tipo de Aplicação | Descrito por | Malha de tecido de serviço azure | Clusters de tecido de serviço Azure (qualquer Os)| Aglomerado local | Cluster autónomo |
|---|---|---|---|---|---|
| Aplicações de malha de tecido de serviço | Modelo de Recursos (YAML e JSON) | Suportado |Não suportado | Suportado pelo Windows, Linux e Mac não suportados | Windows- não suportado |
|Aplicações nativas de tecido de serviço | Modelo de aplicação nativa (XML) | Não Suportado| Suportado|Suportado|Suportado pelo Windows|

A tabela seguinte descreve os diferentes modelos de aplicação e a ferramenta que existe para eles contra o Tecido de Serviço.

| Tipo de Aplicação | Descrito por | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Aplicações de malha de tecido de serviço | Modelo de Recursos (YAML e JSON) | VS 2017 |Não suportado |Não suportado | Suportado - Apenas ambiente de malha | Não Suportado|
|Aplicações nativas de tecido de serviço | Modelo de aplicação nativa (XML) | VS 2017 e VS 2015| Suportado|Suportado|Suportado|Suportado|

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Tecido de Serviço:

* [Visão geral do tecido de serviço](service-fabric-overview.md)
* [Why a microservices approach to building applications?](service-fabric-overview-microservices.md) (Porquê seguir a abordagem de microsserviços para criar aplicações?)
* [Cenários de aplicações](service-fabric-application-scenarios.md)

Para saber mais sobre malha de tecido de serviço:

* [Visão geral da malha de tecido de serviço](/azure/service-fabric-mesh/service-fabric-mesh-overview)
