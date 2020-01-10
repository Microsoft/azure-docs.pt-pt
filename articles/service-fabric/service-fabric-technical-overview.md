---
title: Conheça a terminologia do Azure Service Fabric
description: Uma visão geral da terminologia do Service Fabric. Discute os principais conceitos e termos de terminologia usados no restante da documentação.
author: masnider
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: masnider
ms.openlocfilehash: cf2cfdad10d93bb4f28345d75a86fdcd94587410
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465602"
---
# <a name="service-fabric-terminology-overview"></a>Visão geral da terminologia Service Fabric
O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços dimensionáveis e fiáveis.  Você pode [hospedar clusters Service Fabric em qualquer lugar](service-fabric-deploy-anywhere.md): o Azure, em um datacenter local ou em qualquer provedor de nuvem.  Service Fabric é o orquestrador que capacita a [malha de Service Fabric do Azure](/azure/service-fabric-mesh). Você pode usar qualquer estrutura para escrever seus serviços e escolher onde executar o aplicativo de várias opções de ambiente. Este artigo detalha a terminologia usada pelo Service Fabric para entender os termos usados na documentação.

## <a name="infrastructure-concepts"></a>Conceitos de infraestrutura
**Cluster**: um conjunto conectado à rede de máquinas virtuais ou físicas em que seus microserviços são implantados e gerenciados.  Os clusters podem ser dimensionados para milhares de máquinas.

**Nó**: uma máquina ou VM que faz parte de um cluster é chamada de *nó*. É atribuído um nome de nó (uma cadeia) a cada nó. Os nós têm características, como propriedades de posicionamento. Cada máquina ou VM tem um serviço do Windows de início automático, `FabricHost.exe`, que começa a ser executada na inicialização e, em seguida, inicia dois executáveis: `Fabric.exe` e `FabricGateway.exe`. Esses dois executáveis compõem o nó. Para cenários de teste, você pode hospedar vários nós em um único computador ou VM executando várias instâncias de `Fabric.exe` e `FabricGateway.exe`.

## <a name="application-and-service-concepts"></a>Conceitos de aplicativos e serviços

**Service Fabric aplicativo de malha**: Service Fabric aplicativos de malha são descritos pelo modelo de recursos (YAML e arquivos de recurso JSON) e podem ser implantados em qualquer ambiente onde Service Fabric é executado.

**Service Fabric aplicativo nativo**: Service Fabric aplicativos nativos são descritos pelo modelo de aplicativo nativo (manifestos de aplicativo e serviço baseados em XML).  Service Fabric aplicativos nativos não podem ser executados em Service Fabric malha.

### <a name="service-fabric-mesh-application-concepts"></a>Conceitos de aplicativos de malha Service Fabric

**Aplicativo**: um aplicativo é a unidade de implantação, controle de versão e tempo de vida de um aplicativo de malha. O ciclo de vida de cada instância do aplicativo pode ser gerenciado de forma independente.  Os aplicativos são compostos de um ou mais pacotes e configurações de código de serviço. Um aplicativo é definido usando o esquema do modelo de recursos do Azure (RM).  Os serviços são descritos como propriedades do recurso de aplicativo em um modelo RM.  Redes e volumes usados pelo aplicativo são referenciados pelo aplicativo.  Ao criar um aplicativo, o aplicativo, serviço (s), rede e volume (s) são modelados usando o modelo de recurso Service Fabric.

**Serviço**: um serviço em um aplicativo representa um microserviço e executa uma função completa e autônoma. Cada serviço é composto por um ou mais pacotes de código que descrevem tudo o que é necessário para executar a imagem de contêiner associada ao pacote de códigos.  O número de serviços em um aplicativo pode ser aumentado e reduzido verticalmente.

**Rede**: um recurso de rede cria uma rede privada para seus aplicativos e é independente dos aplicativos ou serviços que podem se referir a ele. Vários serviços de diferentes aplicativos podem fazer parte da mesma rede. As redes são recursos implantáveis que são referenciados por aplicativos.

**Pacote de códigos**: pacotes de código descrevem tudo o que é necessário para executar a imagem de contêiner associada ao pacote de códigos, incluindo o seguinte:

* Nome do contêiner, versão e registro
* Recursos de CPU e memória necessários para cada contêiner
* Pontos de extremidade de rede
* Volumes a serem montados no contêiner, fazendo referência a um recurso de volume separado.

Todos os pacotes de código definidos como parte de um recurso de aplicativo são implantados e ativados juntos como um grupo.

**Volume**: volumes são diretórios que são montados dentro de suas instâncias de contêiner que você pode usar para manter o estado. O driver de volume de arquivos do Azure monta um compartilhamento de arquivos do Azure em um contêiner e fornece armazenamento de dados confiável por meio de qualquer API que ofereça suporte ao armazenamento de arquivos. Os volumes são recursos implantáveis que são referenciados por aplicativos.

### <a name="service-fabric-native-application-concepts"></a>Service Fabric conceitos de aplicativos nativos

**Aplicativo**: um aplicativo é uma coleção de serviços constituintes que executam uma determinada função ou funções. O ciclo de vida de cada instância do aplicativo pode ser gerenciado de forma independente.

**Serviço**: um serviço executa uma função completa e autônoma e pode ser iniciado e executado independentemente de outros serviços. Um serviço é composto por código, configuração e dados. Para cada serviço, o código consiste em binários executáveis, a configuração consiste em configurações de serviço que podem ser carregadas em tempo de execução, e os dados consistem em dados estáticos arbitrários a serem consumidos pelo serviço.

**Tipo de aplicativo**: o nome/versão atribuído a uma coleção de tipos de serviço. Ele é definido em um arquivo de `ApplicationManifest.xml` e inserido em um diretório de pacote de aplicativos. O diretório é então copiado para o repositório de imagens do Service Fabric cluster. Em seguida, você pode criar um aplicativo nomeado desse tipo de aplicativo dentro do cluster.

Leia o artigo [modelo de aplicativo](service-fabric-application-model.md) para obter mais informações.

**Pacote de aplicativos**: um diretório de disco que contém o arquivo de `ApplicationManifest.xml` do tipo de aplicativo. Faz referência aos pacotes de serviço para cada tipo de serviço que compõe o tipo de aplicativo. Os arquivos no diretório do pacote de aplicativos são copiados para Service Fabric repositório de imagens do cluster. Por exemplo, um pacote de aplicativos para um tipo de aplicativo de email pode conter referências a um pacote de serviço de fila, um pacote de serviço de front-end e um pacote de serviço de banco de dados.

**Aplicativo nomeado**: depois de copiar um pacote de aplicativos para o repositório de imagens, você cria uma instância do aplicativo dentro do cluster. Você cria uma instância ao especificar o tipo de aplicativo do pacote de aplicativos, usando seu nome ou versão. Cada instância de tipo de aplicativo recebe um nome de URI (Uniform Resource Identifier) semelhante a: `"fabric:/MyNamedApp"`. Em um cluster, você pode criar vários aplicativos nomeados a partir de um único tipo de aplicativo. Você também pode criar aplicativos nomeados de diferentes tipos de aplicativos. Cada aplicativo nomeado é gerenciado e versionado de forma independente.

**Tipo de serviço**: o nome/versão atribuído aos pacotes de código, pacotes de dados e pacotes de configuração de um serviço. O tipo de serviço é definido no arquivo de `ServiceManifest.xml` e inserido em um diretório de pacote de serviço. O diretório do pacote de serviço é referenciado pelo arquivo de `ApplicationManifest.xml` de um pacote de aplicativos. No cluster, depois de criar um aplicativo nomeado, você pode criar um serviço nomeado de um dos tipos de serviço do tipo de aplicativo. O arquivo de `ServiceManifest.xml` do tipo de serviço descreve o serviço.

Leia o artigo [modelo de aplicativo](service-fabric-application-model.md) para obter mais informações.

Há dois tipos de serviços:

* **Sem estado**: Use um serviço sem estado quando o estado persistente do serviço for armazenado em um serviço de armazenamento externo, como armazenamento do Azure, banco de dados SQL do azure ou Azure Cosmos DB. Use um serviço sem estado quando o serviço não tiver armazenamento persistente. Por exemplo, para um serviço de calculadora em que os valores são passados para o serviço, é realizada uma computação que usa esses valores e, em seguida, um resultado é retornado.
* Com **estado**: Use um serviço com estado quando desejar que o Service Fabric gerencie o estado do serviço por meio de suas coleções confiáveis ou modelos de programação de Reliable Actors. Quando você cria um serviço nomeado, especifique quantas partições deseja distribuir o seu estado para escalabilidade. Especifique também quantas vezes replicar seu estado entre os nós, para fins de confiabilidade. Cada serviço nomeado tem uma única réplica primária e várias réplicas secundárias. Você modifica o estado do serviço nomeado ao gravar na réplica primária. Em seguida, Service Fabric Replica esse estado para todas as réplicas secundárias para manter seu estado sincronizado. Service Fabric detecta automaticamente quando uma réplica primária falha e promove uma réplica secundária existente para uma réplica primária. Service Fabric, em seguida, cria uma nova réplica secundária.  

**Réplicas ou instâncias** referem-se ao código (e estado para serviços com estado) de um serviço implantado e em execução. Consulte [réplicas e instâncias](service-fabric-concepts-replica-lifecycle.md).

**Reconfiguração** refere-se ao processo de qualquer alteração no conjunto de réplicas de um serviço. Consulte [reconfiguração](service-fabric-concepts-reconfiguration.md).

**Pacote de serviço**: um diretório de disco que contém o arquivo de `ServiceManifest.xml` do tipo de serviço. Esse arquivo faz referência ao código, aos dados estáticos e aos pacotes de configuração para o tipo de serviço. Os arquivos no diretório do pacote de serviço são referenciados pelo arquivo de `ApplicationManifest.xml` do tipo de aplicativo. Por exemplo, um pacote de serviço pode se referir ao código, aos dados estáticos e aos pacotes de configuração que compõem um serviço de banco de dado.

**Serviço nomeado**: depois de criar um aplicativo nomeado, você pode criar uma instância de um de seus tipos de serviço dentro do cluster. Especifique o tipo de serviço usando seu nome/versão. Cada instância de tipo de serviço recebe um nome de URI com escopo no URI do aplicativo nomeado. Por exemplo, se você criar um serviço chamado "MyDatabase" em um aplicativo chamado "MyNamedApp", o URI será semelhante a: `"fabric:/MyNamedApp/MyDatabase"`. Em um aplicativo nomeado, você pode criar vários serviços nomeados. Cada serviço nomeado pode ter seu próprio esquema de partição e contagens de instância ou réplica.

**Pacote de códigos**: um diretório de disco que contém os arquivos executáveis do tipo de serviço, normalmente arquivos exe/dll. Os arquivos no diretório do pacote de códigos são referenciados pelo arquivo de `ServiceManifest.xml` do tipo de serviço. Quando você cria um serviço nomeado, o pacote de códigos é copiado para o nó ou nós selecionados para executar o serviço nomeado. Em seguida, o código começa a ser executado. Há dois tipos de executáveis de pacote de código:

* **Executáveis convidados**: executáveis que são executados no estado em que se encontram no sistema operacional do host (Windows ou Linux). Esses executáveis não vinculam ou fazem referência a nenhum arquivo Service Fabric Runtime e, portanto, não usam nenhum modelo de programação de Service Fabric. Esses executáveis não podem usar alguns recursos Service Fabric, como o serviço de nomenclatura para a descoberta de ponto de extremidade. Os executáveis convidados não podem relatar métricas de carga específicas para cada instância de serviço.
* **Executáveis do host de serviço**: executáveis que usam Service Fabric modelos de programação vinculando a Service Fabric arquivos de tempo de execução, habilitando recursos de Service Fabric. Por exemplo, uma instância de serviço nomeada pode registrar pontos de extremidade com Serviço de Nomenclatura de Service Fabric e também pode relatar métricas de carga.

**Pacote de dados**: um diretório de disco que contém os arquivos de dados estáticos somente leitura do tipo de serviço, normalmente arquivos de foto, som e vídeo. Os arquivos no diretório do pacote de dados são referenciados pelo arquivo de `ServiceManifest.xml` do tipo de serviço. Quando você cria um serviço nomeado, o pacote de dados é copiado para o nó ou nós selecionados para executar o serviço nomeado. O código começa a ser executado e agora pode acessar os arquivos de dados.

**Pacote de configuração**: um diretório de disco que contém os arquivos de configuração estáticos e somente leitura do tipo de serviço, normalmente arquivos de texto. Os arquivos no diretório do pacote de configuração são referenciados pelo arquivo de `ServiceManifest.xml` do tipo de serviço. Quando você cria um serviço nomeado, os arquivos no pacote de configuração são copiados um ou mais nós selecionados para executar o serviço nomeado. Em seguida, o código começa a ser executado e agora pode acessar os arquivos de configuração.

**Contêineres**: por padrão, Service Fabric implanta e ativa serviços como processos. Service Fabric também pode implantar serviços em imagens de contêiner. Contêineres são uma tecnologia de virtualização que virtualiza o sistema operacional subjacente de aplicativos. Um aplicativo e seu tempo de execução, suas dependências e suas bibliotecas de sistema são executados dentro de um contêiner. O contêiner tem acesso privado e completo à exibição isolada própria do contêiner das construções do sistema operacional. O Service Fabric dá suporte a contêineres do Docker em contêineres do Linux e do Windows Server. Para obter mais informações, leia [Service Fabric e contêineres](service-fabric-containers-overview.md).

**Esquema de partição**: ao criar um serviço nomeado, você especifica um esquema de partição. Os serviços com quantidades substanciais de estado dividem os dados entre partições, que espalham o estado nos nós do cluster. Dividindo os dados entre partições, o estado do serviço nomeado pode ser dimensionado. Em uma partição, os serviços nomeados sem estado têm instâncias, enquanto os serviços nomeados com estado têm réplicas. Normalmente, os serviços nomeados sem monitoração de Estado têm apenas uma partição, pois não têm estado interno. As instâncias de partição fornecem disponibilidade. Se uma instância falhar, outras instâncias continuarão a operar normalmente e, em seguida, Service Fabric criará uma nova instância. Os serviços nomeados com estado mantêm seu estado dentro das réplicas e cada partição tem seu próprio conjunto de réplicas para que o estado seja mantido em sincronia. Se uma réplica falhar, Service Fabric criará uma nova réplica a partir das réplicas existentes.

Leia o artigo [partição Service Fabric Reliable Services](service-fabric-concepts-partitioning.md) para obter mais informações.

## <a name="system-services"></a>Serviços do sistema
Há serviços do sistema que são criados em todos os clusters que fornecem os recursos de plataforma do Service Fabric.

**Serviço de nomenclatura**: cada cluster Service Fabric tem um serviço de nomenclatura, que resolve nomes de serviço para um local no cluster. Você gerencia os nomes de serviço e as propriedades, como um DNS (sistema de nomes de domínio) da Internet para o cluster. Os clientes se comunicam com segurança com qualquer nó no cluster usando o Serviço de Nomenclatura para resolver um nome de serviço e sua localização. Os aplicativos se movem dentro do cluster. Por exemplo, isso pode ser devido a falhas, balanceamento de recursos ou o redimensionamento do cluster. Você pode desenvolver serviços e clientes que resolvem o local de rede atual. Os clientes obtêm o endereço IP do computador real e a porta onde ele está em execução.

Leia [comunicar-se com serviços](service-fabric-connect-and-communicate-with-services.md) para obter mais informações sobre as APIs de comunicação do cliente e do serviço que funcionam com o serviço de nomenclatura.

**Serviço de repositório de imagens**: cada cluster Service Fabric tem um serviço de repositório de imagens em que os pacotes de aplicativos com versão implantados são mantidos. Copie um pacote de aplicativos para o Repositório de Imagens e registre o tipo de aplicativo contido nesse pacote de aplicativo. Depois que o tipo de aplicativo é provisionado, você cria um aplicativo nomeado a partir dele. Você pode cancelar o registro de um tipo de aplicativo do serviço de Repositório de Imagens depois que todos os seus aplicativos nomeados tiverem sido excluídos.

Leia [entender a configuração de ImageStoreConnectionString](service-fabric-image-store-connection-string.md) para obter mais informações sobre o serviço de repositório de imagens.

Leia o artigo [implantar um aplicativo](service-fabric-deploy-remove-applications.md) para obter mais informações sobre como implantar aplicativos no serviço de repositório de imagens.

**Serviço de Gerenciador de failover**: cada cluster Service Fabric tem um serviço de Gerenciador de failover que é responsável pelas seguintes ações:
   - Executa funções relacionadas à alta disponibilidade e à consistência dos serviços.
   - Orquestra atualizações de aplicativo e cluster.
   - Interage com outros componentes do sistema.

**Serviço de Gerenciador de reparos**: esse é um serviço de sistema opcional que permite que ações de reparo sejam executadas em um cluster de forma segura, automatizada e transparente. O Gerenciador de reparo é usado em:
   - Executar os reparos de manutenção do Azure em clusters de [durabilidade prata e ouro](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) do Azure Service Fabric.
   - Realizando ações de reparo para o [aplicativo de orquestração de patch](service-fabric-patch-orchestration-application.md)

## <a name="deployment-and-application-models"></a>Implantação e modelos de aplicativo 

Para implantar seus serviços, você precisa descrever como eles devem ser executados. O Service Fabric dá suporte a três modelos de implantação diferentes:

### <a name="resource-model-preview"></a>Modelo de recurso (visualização)
Service Fabric recursos são tudo o que pode ser implantado individualmente para Service Fabric; incluindo aplicativos, serviços, redes e volumes. Os recursos são definidos usando um arquivo JSON, que pode ser implantado em um ponto de extremidade do cluster.  Para Service Fabric malha, o esquema de modelo de recurso do Azure é usado. Um esquema de arquivo YAML também pode ser usado para criar arquivos de definição com mais facilidade. Os recursos podem ser implantados em qualquer lugar Service Fabric executado. O modelo de recurso é a maneira mais simples de descrever seus Service Fabric aplicativos. Seu foco principal é a implantação simples e o gerenciamento de serviços em contêineres. Para saber mais, leia [introdução ao modelo de recurso Service Fabric](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Modelo nativo
O modelo de aplicativo nativo fornece aos seus aplicativos acesso completo de baixo nível a Service Fabric. Os aplicativos e serviços são definidos como tipos registrados em arquivos de manifesto XML.

O modelo nativo dá suporte às estruturas Reliable Services e Reliable Actors, que fornece acesso às APIs de tempo de execução do Service Fabric e APIs C# de gerenciamento de cluster no e Java. O modelo nativo também dá suporte a contêineres e executáveis arbitrários. Não há suporte para o modelo nativo no [ambiente de malha Service Fabric](/azure/service-fabric-mesh/service-fabric-mesh-overview).

**Reliable Services**: uma API para compilar serviços com e sem estado. Os serviços com estado armazenam seu estado em coleções confiáveis, como um dicionário ou uma fila. Você também pode conectar várias pilhas de comunicação, como a API Web e o Windows Communication Foundation (WCF).

**Reliable Actors**: uma API para compilar objetos com e sem estado por meio do modelo de programação de ator virtual. Esse modelo é útil quando você tem muitas unidades independentes de computação ou estado. Esse modelo usa um modelo de Threading baseado em desligamento, portanto, é melhor evitar o código que chama outros atores ou serviços porque um ator individual não pode processar outras solicitações de entrada até que todas as suas solicitações de saída sejam concluídas.

Você também pode executar seus aplicativos existentes no Service Fabric:

**Contêineres**: Service Fabric dá suporte à implantação de contêineres do Docker em contêineres do Linux e do Windows Server no windows Server 2016, juntamente com o suporte para o modo de isolamento do Hyper-V. No modelo de [aplicativo](service-fabric-application-model.md)Service Fabric, um contêiner representa um host de aplicativo no qual várias réplicas de serviço são colocadas. Service Fabric pode executar qualquer contêiner e o cenário é semelhante ao cenário de executável convidado, no qual você empacota um aplicativo existente dentro de um contêiner. Além disso, você também pode [executar Service Fabric serviços dentro de contêineres](service-fabric-services-inside-containers.md) .

**Executáveis convidados**: você pode executar qualquer tipo de código, como node. js, Python, Java ou C++ no Azure Service Fabric como um serviço. Service Fabric se refere a esses tipos de serviços como executáveis convidados, que são tratados como serviços sem estado. As vantagens de executar um executável convidado em um Cluster Service Fabric incluem alta disponibilidade, monitoramento de integridade, gerenciamento do ciclo de vida do aplicativo, alta densidade e descoberta.

Leia o artigo [escolher um modelo de programação para o seu serviço](service-fabric-choose-framework.md) para obter mais informações.

### <a name="docker-compose"></a>Docker Compose 
[Docker Compose](https://docs.docker.com/compose/) faz parte do projeto do Docker. Service Fabric fornece suporte limitado para [implantar aplicativos usando o modelo de Docker Compose](service-fabric-docker-compose.md).

## <a name="environments"></a>Ambientes

Service Fabric é uma tecnologia de plataforma de software livre na qual vários serviços e produtos são baseados. A Microsoft fornece as seguintes opções:

 - **Malha de Service Fabric do Azure**: um serviço totalmente gerenciado para executar Service Fabric aplicativos no Microsoft Azure.
 - **Service Fabric do Azure**: a oferta de cluster de Service Fabric hospedado do Azure. Ele fornece integração entre Service Fabric e a infraestrutura do Azure, juntamente com o gerenciamento de atualização e configuração de clusters de Service Fabric.
 - **Service Fabric autônomo**: um conjunto de ferramentas de instalação e configuração para [implantar Service Fabric clusters em qualquer lugar](/azure/service-fabric/service-fabric-deploy-anywhere) (local ou em qualquer provedor de nuvem). Não gerenciado pelo Azure.
 - **Service Fabric cluster de desenvolvimento**: fornece uma experiência de desenvolvimento local no Windows, Linux ou Mac para o desenvolvimento de aplicativos Service Fabric.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Matriz de suporte de ambiente, estrutura e modelo de implantação
Ambientes diferentes têm um nível diferente de suporte para estruturas e modelos de implantação. A tabela a seguir descreve as combinações de estrutura e modelo de implantação com suporte.

| Tipo de aplicativo | Descrito por | Malha de Service Fabric do Azure | Clusters Service Fabric do Azure (qualquer sistema operacional)| Cluster local | Cluster autónomo |
|---|---|---|---|---|---|
| Service Fabric aplicativos de malha | Modelo de recurso (YAML & JSON) | Suportadas |Não suportado | Windows-com suporte, Linux e Mac-sem suporte | Windows-sem suporte |
|Service Fabric aplicativos nativos | Modelo de aplicativo nativo (XML) | Não suportado| Suportadas|Suportadas|Windows-com suporte|

A tabela a seguir descreve os diferentes modelos de aplicativo e as ferramentas que existem para eles em relação ao Service Fabric.

| Tipo de aplicativo | Descrito por | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Service Fabric aplicativos de malha | Modelo de recurso (YAML & JSON) | VS 2017 |Não suportado |Não suportado | Com suporte-somente ambiente de malha | Não suportado|
|Service Fabric aplicativos nativos | Modelo de aplicativo nativo (XML) | VS 2017 e VS 2015| Suportadas|Suportadas|Suportadas|Suportadas|

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre Service Fabric:

* [Visão geral do Service Fabric](service-fabric-overview.md)
* [Why a microservices approach to building applications?](service-fabric-overview-microservices.md) (Porquê seguir a abordagem de microsserviços para criar aplicações?)
* [Cenários de aplicações](service-fabric-application-scenarios.md)

Para saber mais sobre a malha de Service Fabric:

* [Visão geral da malha de Service Fabric](/azure/service-fabric-mesh/service-fabric-mesh-overview)
