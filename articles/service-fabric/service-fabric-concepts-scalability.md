---
title: Escalabilidade de serviços do Service Fabric | Documentos da Microsoft
description: Descreve como dimensionar os serviços do Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: ed324f23-242f-47b7-af1a-e55c839e7d5d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 79db12be04df396cf79d55ff4ec15ad728d4f251
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58094614"
---
# <a name="scaling-in-service-fabric"></a>Dimensionamento no Service Fabric
O Azure Service Fabric simplifica a criação de aplicações escaláveis ao gerir os serviços, partições e réplicas em nós de um cluster. Executar várias cargas de trabalho no mesmo hardware permite a utilização de recursos máximo, mas também fornece flexibilidade em termos de como optar por dimensionar as cargas de trabalho. Este vídeo do Channel 9 descreve como pode criar aplicações de microsserviços dimensionáveis:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T116/player]

Dimensionamento no Service Fabric é conseguido várias formas diferentes:

1. Dimensionamento ao criar ou removendo instâncias de serviço sem estado
2. Dimensionamento ao criar ou remover novos com o nome dos serviços
3. Dimensionamento ao criar ou remover novos com o nome instâncias da aplicação
4. Dimensionar com os serviços particionados
5. Dimensionamento ao adicionar e remover nós do cluster 
6. Dimensionamento ao utilizar o Gestor de recursos de Cluster métricas

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Dimensionamento ao criar ou removendo instâncias de serviço sem estado
Uma das formas mais simples para dimensionar no Service Fabric funciona com os serviços sem estado. Quando cria um serviço sem estado, tenha a oportunidade para definir um `InstanceCount`. `InstanceCount` Define o número de cópias em execução de código desses serviços é criado quando o serviço é iniciado. Digamos, por exemplo, existem 100 nós no cluster. Vamos supor também que é criado um serviço com um `InstanceCount` de 10. Durante o tempo de execução, esses 10 cópias em execução do código foi tudo tornar-se demasiado ocupadas (ou podem não ser suficientemente ocupadas). Uma forma de dimensionar essa carga de trabalho é alterar o número de instâncias. Por exemplo, uma parte do código de monitorização ou de gestão pode alterar o número de instâncias existente 50 ou 5, dependendo se a carga de trabalho precisa aumentar e reduzir com base na carga. 

C#:

```csharp
StatelessServiceUpdateDescription updateDescription = new StatelessServiceUpdateDescription(); 
updateDescription.InstanceCount = 50;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

PowerShell:

```posh
Update-ServiceFabricService -Stateless -ServiceName $serviceName -InstanceCount 50
```
### <a name="using-dynamic-instance-count"></a>Usando a contagem de instância dinâmica
Especificamente para serviços sem estado, o Service Fabric oferece uma forma automática para alterar a contagem de instâncias. Isso permite que o serviço de dimensionar dinamicamente, com o número de nós que estão disponíveis. A forma de optar ativamente por participar nesse comportamento é definir a contagem de instâncias = -1. InstanceCount = -1 é uma instrução para o Service Fabric, que diz "Run este serviço sem estado em cada nó." Se o número de nós for alterado, o Service Fabric altera automaticamente o número de instâncias para fazer corresponder, garantindo que o serviço está em execução em todos os nós válido. 

C#:

```csharp
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//Set other service properties necessary for creation....
serviceDescription.InstanceCount = -1;
await fc.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless -PartitionSchemeSingleton -InstanceCount "-1"
```

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Dimensionamento ao criar ou remover novos com o nome dos serviços
Uma instância de serviço com nome é uma instância específica de um tipo de serviço (consulte [ciclo de vida de aplicativos do Service Fabric](service-fabric-application-lifecycle.md)) dentro de alguns instância da aplicação com o nome do cluster. 

Novas instâncias de serviço com nome podem ser criadas (ou removidas) como serviços tornam-se mais ou menos ocupados. Isso permite que os pedidos sejam distribuídas em mais instâncias de serviço, normalmente, permitindo a carga nos serviços existentes para diminuir. Ao criar serviços, o Gestor de recursos de Cluster do Service Fabric coloca os serviços do cluster de uma maneira distribuída. As decisões exatas são regidas pelos [métricas](service-fabric-cluster-resource-manager-metrics.md) no cluster e de outras regras de colocação. Os serviços podem ser criados várias formas diferentes, mas são mais comuns através de ações administrativas, tal como alguém chamar [ `New-ServiceFabricService` ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps), ou ao chamar código [ `CreateServiceAsync` ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync?view=azure-dotnet). `CreateServiceAsync` Pode ainda ser chamado de dentro de outros serviços em execução no cluster.

Criar serviços dinamicamente podem ser usados em todos os tipos de cenários e é um padrão comum. Por exemplo, considere um serviço com estado que representa um fluxo de trabalho específico. Chamadas que represente o trabalho vai ser apresentado para este serviço, e este serviço vai executar os passos para esse curso de fluxo de trabalho e o registo. 

Como tornaria desta escala de determinado serviço? O serviço poderia ser multi-inquilino de alguma forma e aceitar chamadas e iniciar os passos para muitos diferentes instâncias do mesmo fluxo de trabalho ao mesmo tempo. No entanto, que pode tornar o código mais complexo, desde agora tem de se preocupar em muitas instâncias diferentes do mesmo fluxo de trabalho, tudo em diferentes fases e de diferentes clientes. Além disso, a manipulação de vários fluxos de trabalho ao mesmo tempo não resolve o problema de dimensionamento. Isso é porque em algum momento, este serviço irá consumir demasiados recursos para caber num determinado computador. Em primeiro lugar a muitos serviços não criados para este padrão também experiência dificuldade devido a algumas afunilamento inerente ou lentidão em seu código. Esses tipos de problemas de fazer com que o serviço não funcionam muito bem quando o número de fluxos de trabalho simultâneos está acompanhando obtém maior.  

Uma solução é criar uma instância deste serviço para cada instância diferente do fluxo de trabalho que pretende controlar. Este é um excelente padrão e funciona se o serviço está com ou sem estado. Para este padrão trabalhar, há geralmente outro serviço que age como um "serviço de Gestor da carga de trabalho". A tarefa deste serviço é para receber pedidos de e para encaminhar os pedidos para outros serviços. O gestor pode criar dinamicamente uma instância do serviço de carga de trabalho ao receber a mensagem e, em seguida, passar em pedidos a esses serviços. O service manager também pode receber retornos de chamada quando um serviço de fluxo de trabalho conclui seu trabalho. Quando o Gestor de recebe esses retornos de chamada ela pode eliminar essa instância do serviço de fluxo de trabalho ou deixá-lo se são esperadas mais chamadas. 

Versões avançadas desse tipo de gestor podem até mesmo criar conjuntos dos serviços que gerencia. O conjunto ajuda Certifique-se de que quando chega um novo pedido não tem de aguardar que o serviço de girar. Em vez disso, o gestor pode simplesmente escolher um serviço de fluxo de trabalho que não está atualmente ocupado do agrupamento de ou encaminhar aleatoriamente. Manter um conjunto de serviços disponíveis torna mais rápido, novos pedidos de processamento, uma vez que é menos provável que o pedido tem de aguardar por um novo serviço ser rotacionado. Criação de novos serviços rapidamente, mas não é gratuito ou instantânea. O conjunto ajuda a minimizar a quantidade de tempo que do pedido tem de aguardar antes em manutenção. Frequentemente verá este manager e o padrão de conjunto quando tempos de resposta são mais importam. O pedido de colocação em fila e a criação do serviço em segundo plano e _, em seguida,_ passando para ele também é um padrão de Gestor populares, como está a criar e eliminar serviços com base em algum serviço de controle da quantidade de trabalho que atualmente tem pendente . 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Dimensionamento ao criar ou remover novos com o nome instâncias da aplicação
Criar e excluir instâncias de toda a aplicação são semelhante ao padrão de criação e a eliminação de serviços. Para esse padrão, existe algum serviço de gestor que está fazendo a decisão com base nos pedidos que ele vê e as informações que está a receber de outros serviços dentro do cluster. 

Quando deve criar uma nova instância de aplicação com o nome utilizado em vez de criar novas instâncias de serviço com nome em algum aplicativo já existente? Há alguns casos:

  * A nova instância de aplicação é de um cliente cujo código precisa ser executado em alguns identidade específico ou definições de segurança.
    * Service Fabric permite definir os pacotes de código diferente para ser executado sob identidades específicas. Para iniciar o mesmo pacote de código sob diferentes identidades, as ativações tem de ocorrer em diferentes instâncias da aplicação. Considere o caso em que tem cargas de trabalho de um cliente existente implementadas. Estes poderão estar em execução sob uma identidade específica para que possa monitorizar e controlar o acesso a outros recursos, como bancos de dados remotos ou outros sistemas. Neste caso, quando um novo cliente se inscreve, provavelmente não deseja ativar o seu código no mesmo contexto (espaço de processo). Enquanto poderia, isso torna mais difícil para o seu código de serviço atuar no contexto de uma identidade específica. Normalmente, tem de ter mais segurança, isolamento e código de gerenciamento de identidade. Em vez de utilizar diferentes instâncias nomeadas de serviço dentro da mesma instância da aplicação e, por conseguinte, o mesmo processo espaço, pode utilizar diferentes instâncias nomeadas de aplicação do Service Fabric. Isto torna mais fácil definir os contextos de identidade diferente.
  * A nova instância da aplicação também serve como um meio de configuração
    * Por predefinição, todas as instâncias de serviço com nome de um tipo de serviço específico dentro de uma instância de aplicação serão executado no mesmo processo num determinado nó. Isso significa que embora possa configurar cada instância de serviço diferente, fazer isso pode ser complicado. Serviços tem de ter um token que utilizam para procurar a respetiva configuração dentro de um pacote de configuração. Normalmente, isso é apenas o nome do serviço. Isso funciona bem, mas ela vincula a configuração para os nomes das instâncias de serviço com nome individuais dentro dessa instância de aplicação. Isso pode ser confuso e difícil de gerenciar, uma vez que a configuração é normalmente um artefato de tempo de design com valores específicos de instância de aplicação. A criação de serviços mais sempre significa mais as atualizações de aplicações para alterar as informações dentro os pacotes de configuração ou implementar novos para que os novos serviços podem pesquisar a suas informações específicas. Geralmente é mais fácil de criar uma instância com nome de aplicação totalmente novo. Em seguida, pode utilizar os parâmetros da aplicação para definir qualquer configuração é necessária para os serviços. Desta forma, todos os serviços que são criados dentro dessa instância da aplicação de chamada pode herdar as definições de configuração específico. Por exemplo, em vez de ter um único ficheiro de configuração com as definições e personalizações para todos os clientes, tais como segredos ou por limites de recursos de cliente, em vez disso, terá de uma instância de aplicação diferente para cada cliente com estas definições substituído. 
  * A nova aplicação serve como um limite de atualização
    * Dentro do Service Fabric, instâncias da aplicação com nome diferentes servem como limites para atualização. Uma atualização de uma instância de aplicação com o nome não irá afetar o código que está em execução outra instância de aplicação com o nome. Os diferentes aplicativos acabará com versões diferentes do mesmo código em nós do mesmo. Isso pode ser um fator quando precisa para tomar uma decisão de dimensionamento, uma vez que pode escolher se o novo código deve seguir o mesmo é atualizado como outro serviço ou não. Por exemplo, digamos que uma chamada chega no serviço manager que é responsável por cargas de trabalho de um cliente específico de dimensionamento ao criar e eliminar serviços dinamicamente. Neste caso, no entanto, a chamada é para uma carga de trabalho associada com um _novo_ dos clientes. A maioria dos clientes, como estando isoladas umas das outras, não apenas para as razões de segurança e configuração listadas anteriormente, mas porque fornece mais flexibilidade em termos que executem versões específicas do software e escolher quando eles atualizado. Também pode criar uma nova instância de aplicação e criar o serviço simplesmente para a partição ainda mais a quantidade de serviços que qualquer um atualização terá contato. Instâncias de um aplicativo separado fornecem maior granularidade ao fazer as atualizações de aplicações e também ativa A / implementações de testes e azul/verde B. 
  * A instância existente do aplicativo está cheio
    * No Service Fabric, [capacidade da aplicação](service-fabric-cluster-resource-manager-application-groups.md) é um conceito que pode utilizar para controlar a quantidade de recursos disponíveis para as instâncias do aplicativo em particular. Por exemplo, pode decidir que um determinado serviço tem de ter criada para dimensionar de outra instância. No entanto, esta instância da aplicação é sem capacidade para uma determinada métrica. Se este cliente específico ou a carga de trabalho ainda deve ser concedida mais recursos, em seguida, poderia ou aumentar a capacidade existente para essa aplicação ou criar uma nova aplicação. 

## <a name="scaling-at-the-partition-level"></a>Dimensionamento no nível da partição
O Service Fabric suporta a criação de partições. Criação de partições divide um serviço em várias seções de lógicas e físicas, sendo que cada um funciona de forma independente. Isto é útil com serviços com estado, uma vez que nenhum conjunto de réplicas tem de lidar com todas as chamadas e manipular todo o estado de uma só vez. O [descrição geral da criação de partições](service-fabric-concepts-partitioning.md) fornece informações sobre os tipos de esquemas de particionamento que são suportadas. As réplicas de cada partição são distribuídas em todos os nós num cluster, distribuir carga desses serviços e garantir que nenhum do serviço como um todo ou qualquer partição tem um ponto único de falha. 

Considere um serviço que utiliza um esquema de particionamento ranged com uma chave de baixa de 0, uma chave superior de 99 e uma contagem de partições de 4. Num cluster de três nós, o serviço poderá ser disposto com quatro réplicas que partilham os recursos em cada nó, como mostrado aqui:

<center>

![Esquema de partição com três nós](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Se aumentar o número de nós, o Service Fabric irá mover algumas das réplicas existentes lá. Por exemplo, vamos dizer o número de nós aumenta para quatro e as réplicas obter redistribuído. Agora o serviço agora tem três réplicas em execução em cada nó, cada um que pertencem a diferentes partições. Isto permite uma melhor utilização de recursos, uma vez que o novo nó não está frio. Normalmente, ele também melhora o desempenho, cada serviço tem mais recursos disponíveis para o mesmo.

<center>

![Esquema de partição com quatro nós](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>Dimensionamento com o Gestor de recursos de Cluster do Service Fabric e métricas
[Métricas](service-fabric-cluster-resource-manager-metrics.md) são como serviços express seu consumo de recursos para o Service Fabric. Utilizar métricas dá-Gestor de recursos do Cluster uma oportunidade de reorganizar e otimizar o esquema do cluster. Por exemplo, pode haver muitos recursos do cluster, mas não podem ser alocados para os serviços que estamos atualmente a trabalhar. Uso de métricas, permite que o Gestor de recursos de Cluster para reorganize o cluster para garantir que os serviços têm acesso aos recursos disponíveis. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Dimensionamento ao adicionar e remover nós do cluster 
Outra opção de dimensionamento com o Service Fabric é para alterar o tamanho do cluster. Alterar o tamanho do cluster significa adicionar ou remover nós de um ou mais dos tipos de nó do cluster. Por exemplo, considere o caso em que todos os nós no cluster são frequente. Isso significa que os recursos do cluster são quase tudo consumidos. Neste caso, a adição de mais nós ao cluster é a melhor forma de dimensionar. Depois dos novos nós aderirem ao cluster o Gestor de recursos de Cluster do Service Fabric move serviços aos mesmos, resultando em menos total carga em nós existentes. Para serviços sem estado com contagem de instâncias = -1, serviço de mais instâncias são criadas automaticamente. Isso permite que algumas chamadas mover de nós existentes para os novos nós. 

Para obter mais informações, consulte [dimensionamento de clusters](service-fabric-cluster-scaling.md).

## <a name="putting-it-all-together"></a>Juntar tudo
Vamos dar todas as ideias que discuti aqui e falar por meio de um exemplo. Considere o seguinte serviço: está a tentar criar um serviço que age como um catálogo de endereços, manter os nomes e informações de contato. 

À direita com antecedência tem um monte de perguntas relacionadas ao dimensionamento: O número de utilizadores vai ter? O número de contatos armazenará cada utilizador? Quando estiver trabalhando em pé seu serviço pela primeira vez é difícil tentar imaginar isso todos. Digamos que fosse vão, com um único serviço estático com uma contagem de partição específica. As conseqüências de escolher o número de partições errada podem fazer com que tenha problemas de dimensionamento mais tarde. Da mesma forma, mesmo se escolher a contagem de certa que poderá não ter todas as informações precisa. Por exemplo, também precisa decidir o tamanho do cluster com antecedência, tanto em termos de número de nós e seus tamanhos. É, normalmente, é difícil prever o número de recursos um serviço irá consumir durante sua vida útil. Também pode ser difícil saber antecipadamente o padrão de tráfego que o serviço, na verdade, vê. Por exemplo, pessoas talvez adicionar e remover seus contatos apenas primeira coisa que da manhã, ou talvez ele é distribuído uniformemente ao longo do dia. Com base nisso que poderá ter de aumentar e reduzir horizontalmente de forma dinâmica. Talvez aprender a prever quando vai precisar de aumentar horizontalmente, mas de qualquer forma provavelmente vai precisar de reagir às mudanças do consumo de recursos pelo seu serviço. Isso pode envolver a alterar o tamanho do cluster para oferecer mais recursos quando a utilização dos recursos existentes de reorganização não é suficiente. 

Mas por que até mesmo tentar encontrar um esquema de partição única para todos os utilizadores? Por que limite a um serviço e um cluster estático? A situação real é normalmente mais dinâmica. 

Ao criar para dimensionamento, considere o seguinte padrão dinâmico. Poderá ter de adaptá-lo à sua situação:

1. Em vez de tentar escolher um esquema de particionamento para todos os utilizadores com antecedência, crie um serviço"manager".
2. A tarefa do manager service é examinar informações de clientes quando se inscrevem no seu serviço. Em seguida, dependendo dessas informações, o serviço Gestor de criar uma instância da sua _real_ serviço de armazenamento de contacto _apenas para esse cliente_. Se exigirem configuração específica, isolamento ou atualização, também pode decidir acelerar uma instância de aplicação para este cliente. 

Essa criação dinâmica padrão muitos benefícios:

  - Não está a tentar adivinhar a contagem de partição correta para todos os utilizadores com antecedência ou criar um serviço único que é infinitamente escalável independente. 
  - Diferentes utilizadores não precisam ter o mesmo número de partições, contagem de réplicas, restrições de posicionamento, métricas, cargas de predefinição, os nomes de serviço, as definições de dns ou qualquer uma das outras propriedades especificadas no nível de serviço ou de aplicativo. 
  - Ganha a segmentação de dados adicionais. Cada cliente tem sua própria cópia do serviço
    - Cada serviço de cliente pode ser configurado de forma diferente e podem ser concedido mais ou menos recursos, com mais ou menos partições ou réplicas conforme necessário com base na respetiva escala esperada.
      - Por exemplo, digamos que o cliente pago para o escalão "Gold" - poderem mais réplicas ou maior número de partições e, potencialmente, recursos dedicados aos respetivos serviços através de capacidades de métricas e de aplicação.
      - Ou Digamos que forneceram informações indicando o número de contatos que necessários foram "Pequeno" - obteria apenas algumas partições ou até mesmo pode ser inseridas um conjunto de serviços compartilhados com outros clientes.
  - Não está a executar uma série de instâncias de serviço ou réplicas enquanto está aguardando para os clientes sejam apresentados
  - Se alguma vez perder um cliente, em seguida, remover as informações do seu serviço é tão simples como tendo o Gestor de eliminar esse serviço ou aplicação que ele criou.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre conceitos do Service Fabric, consulte os artigos seguintes:

* [Disponibilidade dos serviços do Service Fabric](service-fabric-availability-services.md)
* [A criação de partições de serviços do Service Fabric](service-fabric-concepts-partitioning.md)
