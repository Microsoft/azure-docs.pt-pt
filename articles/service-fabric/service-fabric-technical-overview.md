---
title: Aprenda terminologia do tecido de serviço Azure
description: Aprenda a terminologia e conceitos de tecido de serviço chave utilizados no resto da documentação.
author: masnider
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: masnider
ms.openlocfilehash: 2ac4b81a284ed8c38bc9cefccd08db5afa51d600
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96575946"
---
# <a name="service-fabric-terminology-overview"></a>Visão geral da terminologia do tecido de serviço

O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços dimensionáveis e fiáveis.  Pode [hospedar clusters de Tecido de Serviço](service-fabric-deploy-anywhere.md)em qualquer lugar: Azure, num datacenter no local ou em qualquer fornecedor de nuvem.  Service Fabric é o orquestrador que alimenta [a malha de tecido de serviço Azure.](../service-fabric-mesh/index.yml) Pode utilizar qualquer enquadramento para escrever os seus serviços e escolher onde executar a aplicação a partir de múltiplas opções ambientais. Este artigo detalha a terminologia utilizada pela Service Fabric para compreender os termos utilizados na documentação.

## <a name="infrastructure-concepts"></a>Conceitos de infraestrutura

**Cluster**: Um conjunto de máquinas virtuais ou físicas ligadas à rede em que os seus microserviços são implantados e geridos.  Os clusters podem ser dimensionados para milhares de máquinas.

**Nó**: Uma máquina ou VM que faz parte de um cluster é chamado de *nó*. A cada nó é atribuído um nome de nó (string). Os nós têm características, como propriedades de colocação. Cada máquina ou VM tem um serviço Windows de arranque automático, `FabricHost.exe` que começa a funcionar no arranque e, em seguida, inicia dois executáveis: e `Fabric.exe` `FabricGateway.exe` . Estes dois executáveis compõem o nó. Para os cenários de teste, pode hospedar vários nós numa única máquina ou VM executando várias instâncias de `Fabric.exe` e `FabricGateway.exe` .

## <a name="application-and-service-concepts"></a>Conceitos de aplicação e serviço

**Aplicação de malha de tecido de serviço**: As aplicações de malha de tecido de serviço são descritas pelo Modelo de Recurso (ficheiros de recursos YAML e JSON) e podem ser implantadas em qualquer ambiente onde o Tecido de Serviço funciona.

**Aplicação nativa do tecido de** serviço : Aplicações nativas do tecido de serviço são descritas pelo Modelo de Aplicação Nativa (manifestos de aplicação e serviço baseados em XML).  As aplicações nativas do tecido de serviço não podem funcionar na malha de tecido de serviço.

### <a name="service-fabric-mesh-application-concepts"></a>Conceitos de aplicação de malha de tecido de serviço

**Aplicação**: Uma aplicação é a unidade de implantação, versão e vida útil de uma aplicação de Malha. O ciclo de vida de cada instância de aplicação pode ser gerido de forma independente.  As aplicações são compostas por um ou mais pacotes de código de serviço e configurações. Uma aplicação é definida usando o esquema do Modelo de Recursos Azure (RM).  Os serviços são descritos como propriedades do recurso de aplicação num modelo de RM.  As redes e volumes utilizados pela aplicação são referenciados pela aplicação.  Ao criar uma aplicação, a aplicação, serviços, rede e volume(s) são modelados usando o Modelo de Recursos de Tecido de Serviço.

**Serviço**: Um serviço numa aplicação representa um microserviço e executa uma função completa e autónoma. Cada serviço é composto por um ou mais pacotes de código que descrevem tudo o que é necessário para executar a imagem do recipiente associada ao pacote de código.  O número de serviços numa aplicação pode ser aumentado para cima e para baixo.

**Rede**: Um recurso de rede cria uma rede privada para as suas aplicações e é independente das aplicações ou serviços que podem se referir a ela. Vários serviços de diferentes aplicações podem fazer parte da mesma rede. As redes são recursos implantáveis que são referenciados por aplicações.

**Pacote de código**: Os pacotes de código descrevem tudo o que é necessário para executar a imagem do recipiente associada ao pacote de código, incluindo o seguinte:

* Nome do recipiente, versão e registo
* CPU e recursos de memória necessários para cada contentor
* Pontos finais da rede
* Volumes a montar no recipiente, referindo um recurso de volume separado.

Todos os pacotes de código definidos como parte de um recurso de aplicação são implantados e ativados em conjunto como um grupo.

**Volume**: Os volumes são diretórios que são montados dentro dos casos do seu contentor que pode utilizar para persistir. O controlador de volume Azure Files monta uma partilha de Ficheiros Azure num recipiente e fornece um armazenamento fiável de dados através de qualquer API que suporte o armazenamento de ficheiros. Os volumes são recursos implantáveis que são referenciados por aplicações.

### <a name="service-fabric-native-application-concepts"></a>Conceitos de aplicação nativa de tecido de serviço

**Aplicação**: Uma aplicação é uma coleção de serviços constitutivas que desempenham uma determinada função ou funções. O ciclo de vida de cada instância de aplicação pode ser gerido de forma independente.

**Serviço**: Um serviço executa uma função completa e autónoma e pode iniciar e funcionar independentemente de outros serviços. Um serviço é composto por código, configuração e dados. Para cada serviço, o código consiste nos binários executáveis, a configuração consiste em configurações de serviço que podem ser carregadas no tempo de execução, e os dados consistem em dados estáticos arbitrários a serem consumidos pelo serviço.

**Tipo de aplicação**: O nome/versão atribuído a uma coleção de tipos de serviço. É definido num `ApplicationManifest.xml` ficheiro e incorporado num diretório de pacotes de candidatura. O diretório é então copiado para a loja de imagens do cluster Service Fabric. Em seguida, pode criar uma aplicação nomeada a partir deste tipo de aplicação dentro do cluster.

Leia o artigo [do modelo de aplicação](service-fabric-application-model.md) para mais informações.

**Pacote de aplicação**: Um diretório de discos contendo o ficheiro do tipo de aplicação. `ApplicationManifest.xml` Refere os pacotes de serviço para cada tipo de serviço que compõe o tipo de aplicação. Os ficheiros do diretório de pacotes de aplicações são copiados para a loja de imagens do cluster Service Fabric. Por exemplo, um pacote de aplicação para um tipo de aplicação de e-mail pode conter referências a um pacote de serviço de fila, um pacote de serviço de frontend e um pacote de serviço de base de dados.

**Aplicação com o nome**: Depois de copiar um pacote de aplicações para a loja de imagens, cria-se uma instância da aplicação dentro do cluster. Cria um caso quando especifica o tipo de aplicação do pacote de aplicações, utilizando o seu nome ou versão. Cada instância do tipo de aplicação é atribuída a um nome de identificador de recursos uniforme (URI) que se parece com: `"fabric:/MyNamedApp"` . Dentro de um cluster, pode criar várias aplicações nomeadas a partir de um único tipo de aplicação. Também pode criar aplicações nomeadas de diferentes tipos de aplicações. Cada aplicação nomeada é gerida e versão independente.

**Tipo de serviço**: O nome/versão atribuído a pacotes de código de um serviço, pacotes de dados e pacotes de configuração. O tipo de serviço é definido no `ServiceManifest.xml` ficheiro e incorporado num diretório de pacotes de serviço. O diretório de pacotes de serviço é então referenciado por um ficheiro de um pacote de `ApplicationManifest.xml` aplicações. Dentro do cluster, depois de criar uma aplicação nomeada, pode criar um serviço nomeado a partir de um dos tipos de serviço do tipo de aplicação. O ficheiro do tipo de serviço `ServiceManifest.xml` descreve o serviço.

Leia o artigo [do modelo de aplicação](service-fabric-application-model.md) para mais informações.

Existem dois tipos de serviços:

* **Apátrida**: Utilize um serviço apátrida quando o estado persistente do serviço estiver armazenado num serviço de armazenamento externo, como o Azure Storage, Azure SQL Database ou Azure Cosmos DB. Utilize um serviço apátrida quando o serviço não tiver armazenamento persistente. Por exemplo, para um serviço de calculadora onde os valores são passados para o serviço, é realizado um cálculo que utiliza estes valores, e depois um resultado é devolvido.
* **Stateful**: Utilize um serviço estatal quando pretender que o Service Fabric gere o estado do seu serviço através dos seus modelos de programação Reliable Collections ou Reliable Actors. Quando criar um serviço nomeado, especifique quantas divisórias pretende espalhar o seu estado para escalabilidade. Especificar também quantas vezes replicar o seu estado através dos nós, para fiabilidade. Cada serviço nomeado tem uma única réplica primária e múltiplas réplicas secundárias. Modifica o estado do seu serviço de nome quando escreve para a réplica primária. O Tecido de Serviço replica este estado a todas as réplicas secundárias para manter o seu estado sincronizado. O Tecido de Serviço deteta automaticamente quando uma réplica primária falha e promove uma réplica secundária existente a uma réplica primária. O Tecido de Serviço cria então uma nova réplica secundária.  

**Réplicas ou instâncias** referem-se ao código (e estado para serviços estatais) de um serviço que é implantado e em execução. Ver [réplicas e instâncias.](service-fabric-concepts-replica-lifecycle.md)

**Reconfiguração** refere-se ao processo de qualquer alteração no conjunto de réplica de um serviço. Ver [Reconfiguração](service-fabric-concepts-reconfiguration.md).

**Pacote de** serviço : Um diretório de discos contendo o ficheiro do tipo de `ServiceManifest.xml` serviço. Este ficheiro faz referência ao código, dados estáticos e pacotes de configuração para o tipo de serviço. Os ficheiros do diretório de pacotes de serviço são referenciados pelo ficheiro do tipo de `ApplicationManifest.xml` aplicação. Por exemplo, um pacote de serviço pode referir-se ao código, dados estáticos e pacotes de configuração que compõem um serviço de base de dados.

**Serviço com o nome**: Depois de criar uma aplicação nomeada, pode criar uma instância de um dos seus tipos de serviço dentro do cluster. Especifica o tipo de serviço utilizando o seu nome/versão. Cada instância do tipo de serviço é atribuída a um nome URI analisado no âmbito do URI da sua aplicação. Por exemplo, se criar um serviço com o nome "MyDatabase" dentro de uma aplicação com o nome "MyNamedApp", o URI parece: `"fabric:/MyNamedApp/MyDatabase"` . Dentro de uma aplicação nomeada, pode criar vários serviços nomeados. Cada serviço nomeado pode ter o seu próprio esquema de partição e caso ou contagem de réplicas.

**Pacote de código**: Um diretório de discos contendo os ficheiros executáveis do tipo de serviço, normalmente ficheiros EXE/DLL. Os ficheiros do diretório de pacotes de código são referenciados pelo ficheiro do tipo de `ServiceManifest.xml` serviço. Quando cria um serviço nomeado, o pacote de código é copiado para os nós ou nós selecionados para executar o serviço nomeado. Então o código começa a funcionar. Existem dois tipos de códigos executáveis:

* **Executáveis de hóspedes**: Executáveis que funcionam como está no sistema operativo anfitrião (Windows ou Linux). Estes executáveis não ligam ou referenciam quaisquer ficheiros de tempo de execução do Tecido de Serviço e, portanto, não utilizam nenhum modelo de programação do Tecido de Serviço. Estes executáveis não podem utilizar algumas funcionalidades do Service Fabric, como o serviço de nomeação para a descoberta do ponto final. Os executáveis dos hóspedes não podem reportar métricas de carga específicas de cada instância de serviço.
* **Executáveis de hospedeiros de serviço**: Executáveis que utilizam modelos de programação do Tecido de Serviço ligando-os a ficheiros de tempo de execução do Tecido de Serviço, permitindo funcionalidades de Tecido de Serviço. Por exemplo, uma instância de serviço nomeada pode registar pontos finais com o Serviço de Nomeação do Service Fabric e também pode reportar métricas de carga.

**Pacote de dados**: Um diretório de disco que contém os ficheiros de dados estáticos, apenas de leitura do tipo de serviço, ficheiros de fotografia, som e vídeo. Os ficheiros no diretório de pacotes de dados são referenciados pelo ficheiro do tipo de `ServiceManifest.xml` serviço. Quando cria um serviço nomeado, o pacote de dados é copiado para os nós ou nós selecionados para executar o serviço nomeado. O código começa a ser em execução e pode agora aceder aos ficheiros de dados.

**Pacote de configuração**: Um diretório de discos que contém os ficheiros de configuração estática e apenas de leitura do tipo de serviço, normalmente ficheiros de texto. Os ficheiros do diretório de pacotes de configuração são referenciados pelo ficheiro do tipo de `ServiceManifest.xml` serviço. Quando cria um serviço nomeado, os ficheiros do pacote de configuração são copiados para um ou mais nós selecionados para executar o serviço nomeado. Em seguida, o código começa a ser executado e pode agora aceder aos ficheiros de configuração.

**Contentores**: Por predefinição, o Tecido de Serviço implementa e ativa os serviços como processos. O Tecido de Serviço também pode implementar serviços em imagens de contentores. Os contentores são uma tecnologia de virtualização que abstrata o sistema operativo subjacente a partir de aplicações. Uma aplicação e o seu tempo de funcionamento, dependências e bibliotecas do sistema funcionam dentro de um contentor. O contentor tem acesso completo e privado à própria visão isolada do contentor das construções do sistema operativo. O Tecido de Serviço suporta recipientes Windows Server e estivadores em Linux. Para mais informações, leia [o Tecido de Serviço e os recipientes.](service-fabric-containers-overview.md)

**Esquema de partilha**: Quando cria um serviço nomeado, especifica um esquema de partição. Serviços com quantidades substanciais de estado dividem os dados através de divisórias, o que espalha o estado por todos os nós do cluster. Ao dividir os dados através de divisórias, o estado do seu serviço pode escalar. Dentro de uma partição, os serviços apátridas têm casos, enquanto os serviços com nomes estatais têm réplicas. Normalmente, os serviços apátridas têm apenas uma divisão, porque não têm estado interno. As instâncias de partição fornecem disponibilidade. Se uma instância falhar, outras instâncias continuam a funcionar normalmente, e então o Tecido de Serviço cria uma nova instância. Os serviços com nomes estatais mantêm o seu estado dentro de réplicas e cada divisória tem o seu próprio conjunto de réplicas para que o estado seja mantido em sincronização. Em caso de falha de uma réplica, o Service Fabric constrói uma nova réplica a partir das réplicas existentes.

Leia o artigo de serviço de [partilha para](service-fabric-concepts-partitioning.md) obter mais informações.

## <a name="system-services"></a>Serviços de sistema

Existem serviços de sistema que são criados em todos os clusters que fornecem as capacidades da plataforma do Service Fabric.

**Serviço de Nomeação**: Cada cluster de tecido de serviço tem um Serviço de Nomeação, que resolve nomes de serviço para uma localização no cluster. Gere os nomes e propriedades do serviço, como um Sistema de Nome de Domínio de Internet (DNS) para o cluster. Os clientes comunicam de forma segura com qualquer nó no cluster, utilizando o Serviço de Nomeação para resolver um nome de serviço e a sua localização. As aplicações movem-se dentro do cluster. Por exemplo, isto pode ser devido a falhas, equilíbrio de recursos ou redimensionamento do cluster. Pode desenvolver serviços e clientes que resolvam a localização atual da rede. Os clientes obtêm o endereço IP da máquina real e a porta onde está atualmente em funcionamento.

Leia [Comunicar com os serviços](service-fabric-connect-and-communicate-with-services.md) para obter mais informações sobre as APIs de comunicação de clientes e serviços que trabalham com o Serviço de Nomeação.

**Serviço Image Store**: Cada cluster de Tecido de Serviço tem um serviço image Store onde são mantidos pacotes de aplicações em versão. Copie um pacote de aplicação para a Loja de Imagens e, em seguida, registe o tipo de aplicação contido nesse pacote de aplicações. Depois de ser apresentado o tipo de aplicação, cria-se um pedido nomeado a partir do mesmo. Pode desregissar um tipo de aplicação do serviço Image Store depois de todas as suas aplicações nomeadas terem sido eliminadas.

Leia [A definição imageStoreConnectionString](service-fabric-image-store-connection-string.md) para obter mais informações sobre o serviço Image Store.

Leia o [Artigo de aplicação](service-fabric-deploy-remove-applications.md) implementar mais informações sobre a implementação de aplicações no serviço Image Store.

**Serviço Failover Manager**: Cada cluster de Tecido de Serviço tem um serviço Failover Manager responsável pelas seguintes ações:

 - Desempenha funções relacionadas com elevada disponibilidade e consistência dos serviços.
 - Orquestra aplicações e upgrades de cluster.
 - Interage com outros componentes do sistema.

**Serviço de Gestor de Reparação**: Trata-se de um serviço de sistema opcional que permite ações de reparação num cluster de forma segura, automável e transparente. O gestor de reparação é utilizado em:

   - Realização de reparações de manutenção Azure em clusters de tecido de serviço de [durabilidade prateada e dourada.](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)
   - Execução de ações de reparação para [aplicação de orquestração de remendos](service-fabric-patch-orchestration-application.md)

## <a name="deployment-and-application-models"></a>Modelos de implementação e aplicação

Para implementar os seus serviços, é necessário descrever como devem funcionar. O Service Fabric suporta três modelos de implementação diferentes:

### <a name="resource-model-preview"></a>Modelo de recursos (pré-visualização)

Recursos de Tecido de Serviço são tudo o que pode ser implementado individualmente para o Service Fabric; incluindo aplicações, serviços, redes e volumes. Os recursos são definidos usando um ficheiro JSON, que pode ser implantado num ponto final de cluster.  Para a malha de tecido de serviço, é utilizado o esquema do Modelo de Recurso Azure. Um esquema de ficheiro YAML também pode ser usado para mais facilmente os ficheiros de definição de autor. Os recursos podem ser implantados em qualquer lugar que o Service Fabric executa. O modelo de recursos é a forma mais simples de descrever as suas aplicações de Tecido de Serviço. O seu foco principal é a simples implantação e gestão de serviços contentorizados. Para saber mais, leia [Introdução ao Modelo de Recursos de Tecido de Serviço.](../service-fabric-mesh/service-fabric-mesh-service-fabric-resources.md)

### <a name="native-model"></a>Modelo nativo

O modelo de aplicação nativa fornece às suas aplicações acesso total de baixo nível ao Service Fabric. As aplicações e serviços são definidos como tipos registados em ficheiros manifestos XML.

O modelo nativo suporta os quadros de Reliable Services e Reliable Actors, que fornece acesso às APIs de execução do Tecido de Serviço e APIs de gestão de clusters em C# e Java. O modelo nativo também suporta recipientes arbitrários e executáveis. O modelo nativo não é suportado no [ambiente de malha de tecido de serviço.](../service-fabric-mesh/service-fabric-mesh-overview.md)

**Serviços Fiáveis**: Uma API para construir serviços apátridas e estatais. Os serviços estatais armazenam o seu estado em Coleções Fiáveis, como um dicionário ou uma fila. Também pode ligar várias pilhas de comunicações, tais como API Web e Windows Communication Foundation (WCF).

**Reliable Actors**: Uma API para construir objetos apátridas e imponentes através do modelo de programação do Ator Virtual. Este modelo é útil quando se tem muitas unidades independentes de computação ou estado. Este modelo usa um modelo de rosca baseado em turnos, por isso é melhor evitar código que chama a outros atores ou serviços porque um ator individual não pode processar outros pedidos de entrada até que todos os seus pedidos de saída estejam terminados.

Também pode executar as suas aplicações existentes no Service Fabric:

**Contentores**: O Tecido de Serviço suporta a colocação de contentores Docker em contentores Linux e Windows Server no Windows Server 2016, juntamente com suporte para o modo de isolamento Hyper-V. No modelo de [aplicação](service-fabric-application-model.md)Do Tecido de Serviço, um recipiente representa um hospedeiro de aplicação no qual são colocadas várias réplicas de serviço. O Tecido de Serviço pode executar quaisquer contentores, e o cenário é semelhante ao cenário executável do hóspede, onde você embala uma aplicação existente dentro de um recipiente. Além disso, também pode [executar serviços de Tecido de Serviço dentro de contentores.](service-fabric-services-inside-containers.md)

**Executáveis para hóspedes**: Pode executar qualquer tipo de código, como Node.js, Python, Java ou C++ em Azure Service Fabric como um serviço. O Service Fabric refere-se a este tipo de serviços como executáveis de hóspedes, que são tratados como serviços apátridas. As vantagens de executar um hóspede executável num cluster de Tecido de Serviço incluem alta disponibilidade, monitorização de saúde, gestão do ciclo de vida da aplicação, alta densidade e descoberta.

Leia o [modelo de programação Escolha o seu](service-fabric-choose-framework.md) artigo de serviço para obter mais informações.

### <a name="docker-compose"></a>Docker Compor 

[Docker Compose](https://docs.docker.com/compose/) faz parte do projeto Docker. A Service Fabric oferece suporte limitado para [a implementação de aplicações utilizando o modelo Docker Compose](service-fabric-docker-compose.md).

## <a name="environments"></a>Ambientes

A Service Fabric é uma tecnologia de plataforma de código aberto em que vários serviços e produtos diferentes são baseados. A Microsoft fornece as seguintes opções:

 - **Malha de tecido de serviço Azure**: um serviço totalmente gerido para executar aplicações de tecido de serviço no Microsoft Azure.
 - **Azure Service Fabric**: O Azure acolheu a oferta de cluster de tecido de serviço. Proporciona integração entre o Service Fabric e a infraestrutura Azure, juntamente com a gestão de upgrade e configuração dos clusters de Tecidos de Serviço.
 - **Suporte autónomo do tecido**: Um conjunto de ferramentas de instalação e configuração para [implantar clusters de tecido de serviço em qualquer lugar](./service-fabric-deploy-anywhere.md) (no local ou em qualquer fornecedor de nuvem). Não gerido por Azure.
 - **Cluster de desenvolvimento de tecidos de serviço**: Proporciona uma experiência de desenvolvimento local em Windows, Linux ou Mac para o desenvolvimento de aplicações de Tecido de Serviço.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Matriz de suporte de modelos de ambiente, enquadramento e implantação

Diferentes ambientes têm diferentes níveis de apoio para quadros e modelos de implantação. O quadro seguinte descreve as combinações de modelos de estrutura e implantação suportadas.

| Tipo de Aplicação | Descrito por | Malha de tecido de serviço Azure | Clusters de tecido de serviço Azure (qualquer SISTEMA)| Aglomerado local | Cluster autónomo |
|---|---|---|---|---|---|
| Aplicações de malha de tecido de serviço | Modelo de recursos (YAML & JSON) | Suportado |Não suportado | Windows- suportado, Linux e Mac- não suportados | Windows- não suportado |
|Aplicações nativas do tecido de serviço | Modelo de aplicação nativa (XML) | Não suportado| Suportado|Suportado|Windows- suportado|

A tabela a seguir descreve os diferentes modelos de aplicação e a ferramenta que existe para os mesmos contra o Tecido de Serviço.

| Tipo de Aplicação | Descrito por | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Aplicações de malha de tecido de serviço | Modelo de recursos (YAML & JSON) | VS 2017 |Não suportado |Não suportado | Suportado - Ambiente de malha apenas | Não suportado|
|Aplicações nativas do tecido de serviço | Modelo de aplicação nativa (XML) | VS 2017 e VS 2015| Suportado|Suportado|Suportado|Suportado|

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Tecido de Serviço:

* [Descrição Geral do Service Fabric](service-fabric-overview.md)
* [Why a microservices approach to building applications?](service-fabric-overview-microservices.md) (Porquê seguir a abordagem de microsserviços para criar aplicações?)
* [Cenários de aplicações](service-fabric-application-scenarios.md)

Para saber mais sobre a Malha de Tecido de Serviço:

* [Visão geral da malha de tecido de serviço](../service-fabric-mesh/service-fabric-mesh-overview.md)
