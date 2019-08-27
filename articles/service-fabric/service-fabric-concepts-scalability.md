---
title: Escalabilidade dos serviços de Service Fabric | Microsoft Docs
description: Descreve como dimensionar os serviços de Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: ed324f23-242f-47b7-af1a-e55c839e7d5d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/26/2019
ms.author: masnider
ms.openlocfilehash: f44a44c0923374b2f6024903213305f1defb3b94
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035926"
---
# <a name="scaling-in-service-fabric"></a>Dimensionamento em Service Fabric
O Azure Service Fabric facilita a criação de aplicativos escalonáveis gerenciando os serviços, as partições e as réplicas nos nós de um cluster. A execução de muitas cargas de trabalho no mesmo hardware permite a utilização máxima de recursos, mas também fornece flexibilidade em termos de como você escolhe dimensionar suas cargas de trabalho. Este vídeo do Channel 9 descreve como você pode criar aplicativos de microserviços escalonáveis:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T116/player]

O dimensionamento em Service Fabric é feito de várias maneiras diferentes:

1. Dimensionamento criando ou removendo instâncias de serviço sem estado
2. Dimensionamento criando ou removendo novos serviços nomeados
3. Dimensionamento criando ou removendo novas instâncias de aplicativo nomeado
4. Dimensionamento usando serviços particionados
5. Dimensionamento adicionando e removendo nós do cluster 
6. Dimensionamento usando métricas do Gerenciador de recursos de cluster

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Dimensionamento criando ou removendo instâncias de serviço sem estado
Uma das maneiras mais simples de dimensionar em Service Fabric funciona com serviços sem estado. Ao criar um serviço sem estado, você tem a chance de definir um `InstanceCount`. `InstanceCount`define quantas cópias em execução do código do serviço são criadas quando o serviço é iniciado. Digamos, por exemplo, que há 100 nós no cluster. Digamos também que um serviço seja criado com um `InstanceCount` de 10. Durante o tempo de execução, essas 10 cópias em execução do código podem se tornar muito ocupadas (ou podem não estar ocupadas o suficiente). Uma maneira de dimensionar essa carga de trabalho é alterar o número de instâncias. Por exemplo, parte do código de monitoramento ou de gerenciamento pode alterar o número existente de instâncias para 50, ou para 5, dependendo se a carga de trabalho precisa ser expandida ou reduzida com base na carga. 

C#:

```csharp
StatelessServiceUpdateDescription updateDescription = new StatelessServiceUpdateDescription(); 
updateDescription.InstanceCount = 50;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

PowerShell

```posh
Update-ServiceFabricService -Stateless -ServiceName $serviceName -InstanceCount 50
```
### <a name="using-dynamic-instance-count"></a>Usando a contagem de instâncias dinâmicas
Especificamente para serviços sem estado, o Service Fabric oferece uma maneira automática de alterar a contagem de instâncias. Isso permite que o serviço seja dimensionado dinamicamente com o número de nós disponíveis. A maneira de aceitar esse comportamento é definir a contagem de instâncias =-1. InstanceCount =-1 é uma instrução para Service Fabric que diz "executar este serviço sem estado em todos os nós". Se o número de nós for alterado, Service Fabric alterar automaticamente a contagem de instâncias para corresponder, garantindo que o serviço esteja em execução em todos os nós válidos. 

C#:

```csharp
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//Set other service properties necessary for creation....
serviceDescription.InstanceCount = -1;
await fc.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless -PartitionSchemeSingleton -InstanceCount "-1"
```

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Dimensionamento criando ou removendo novos serviços nomeados
Uma instância de serviço nomeada é uma instância específica de um tipo de serviço (consulte [Service Fabric ciclo de vida do aplicativo](service-fabric-application-lifecycle.md)) em alguma instância de aplicativo nomeada no cluster. 

Novas instâncias de serviço nomeado podem ser criadas (ou removidas) à medida que os serviços se tornam mais ou menos ocupados. Isso permite que as solicitações sejam distribuídas por mais instâncias de serviço, geralmente permitindo que a carga de serviços existentes diminua. Ao criar serviços, o Gerenciador de recursos de Cluster Service Fabric coloca os serviços no cluster de maneira distribuída. As decisões exatas são governadas pelas [métricas](service-fabric-cluster-resource-manager-metrics.md) no cluster e outras regras de posicionamento. Os serviços podem ser criados de várias maneiras diferentes, mas o mais comum é por meio de ações administrativas [`New-ServiceFabricService`](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps), como alguém que chama [`CreateServiceAsync`](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync?view=azure-dotnet), ou por chamada de código. `CreateServiceAsync`pode até ser chamado de dentro de outros serviços em execução no cluster.

Criar serviços dinamicamente pode ser usado em todos os tipos de cenários e é um padrão comum. Por exemplo, considere um serviço com estado que representa um fluxo de trabalho específico. As chamadas que representam o trabalho serão exibidas para esse serviço, e esse serviço irá executar as etapas para esse fluxo e registrar o progresso. 

Como você tornaria essa escala de serviço específica? O serviço pode ser multilocatário em algum formato e aceitar chamadas e disparar etapas para várias instâncias diferentes do mesmo fluxo de trabalho de uma só vez. No entanto, isso pode tornar o código mais complexo, já que agora ele precisa se preocupar com muitas instâncias diferentes do mesmo fluxo de trabalho, tudo em diferentes estágios e de clientes diferentes. Além disso, lidar com vários fluxos de trabalho ao mesmo tempo não resolve o problema de escala. Isso ocorre porque, em algum momento, esse serviço consumirá muitos recursos para caber em um determinado computador. Muitos serviços não criados para esse padrão, em primeiro lugar, também têm dificuldade devido a algum afunilamento ou lentidão inerente em seu código. Esses tipos de problemas fazem com que o serviço não funcione também quando o número de fluxos de trabalho simultâneos que ele está acompanhando ficar maior.  

Uma solução é criar uma instância desse serviço para cada instância diferente do fluxo de trabalho que você deseja acompanhar. Esse é um ótimo padrão e funciona se o serviço é com ou sem estado. Para que esse padrão funcione, geralmente há outro serviço que atua como um "serviço do Gerenciador de carga de trabalho". O trabalho desse serviço é receber solicitações e rotear essas solicitações para outros serviços. O gerente pode criar dinamicamente uma instância do serviço de carga de trabalho quando ele recebe a mensagem e, em seguida, passar solicitações para esses serviços. O serviço de Gerenciador também pode receber retornos de chamada quando um determinado serviço de fluxo de trabalho concluir seu Job. Quando o gerente recebe esses retornos de chamada, ele pode excluir essa instância do serviço de fluxo de trabalho ou deixá-lo se mais chamadas forem esperadas. 

Versões avançadas desse tipo de Gerenciador podem até mesmo criar pools dos serviços que ele gerencia. O pool ajuda a garantir que quando uma nova solicitação chega, ela não precisa esperar que o serviço seja girado. Em vez disso, o gerente pode simplesmente escolher um serviço de fluxo de trabalho que não esteja ocupado no pool ou ser roteado aleatoriamente. Manter um pool de serviços disponíveis torna o tratamento de novas solicitações mais rápido, pois é menos provável que a solicitação tenha de esperar que um novo serviço seja girado. A criação de novos serviços é rápida, mas não é gratuita ou instantânea. O pool ajuda a minimizar a quantidade de tempo que a solicitação deve aguardar antes de ser atendida. Você geralmente verá esse gerente e padrão de pool quando a resposta for mais relevante. Enfileirar a solicitação e criar o serviço em segundo plano e _, em seguida,_ passá-lo também é um padrão popular de gerente, assim como criar e excluir serviços com base em algum controle da quantidade de trabalho que o serviço tem atualmente pendente. 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Dimensionamento criando ou removendo novas instâncias de aplicativo nomeado
Criar e excluir instâncias inteiras do aplicativo é semelhante ao padrão de criação e exclusão de serviços. Para esse padrão, há algum serviço de Gerenciador que está tomando a decisão com base nas solicitações que está vendo e nas informações que ele está recebendo dos outros serviços dentro do cluster. 

Quando a criação de uma nova instância de aplicativo nomeado deve ser usada em vez de criar novas instâncias de serviço nomeado em algum aplicativo já existente? Há alguns casos:

  * A nova instância do aplicativo é para um cliente cujo código precisa ser executado em determinadas configurações de segurança ou identidade.
    * Service Fabric permite que a definição de diferentes pacotes de código seja executada sob identidades específicas. Para iniciar o mesmo pacote de códigos sob identidades diferentes, as ativações precisam ocorrer em diferentes instâncias do aplicativo. Considere um caso em que você tenha as cargas de trabalho de um cliente existentes implantadas. Eles podem ser executados sob uma identidade específica para que você possa monitorar e controlar o acesso a outros recursos, como bancos de dados remotos ou outros sistemas. Nesse caso, quando um novo cliente se inscreve, você provavelmente não deseja ativar seu código no mesmo contexto (espaço de processo). Embora você possa, isso dificulta o seu código de serviço a atuar dentro do contexto de uma identidade específica. Normalmente, você deve ter mais código de segurança, isolamento e gerenciamento de identidade. Em vez de usar diferentes instâncias de serviço nomeadas dentro da mesma instância do aplicativo e, portanto, o mesmo espaço de processo, você pode usar diferentes instâncias de aplicativo nomeadas Service Fabric. Isso facilita a definição de contextos de identidade diferentes.
  * A nova instância do aplicativo também serve como um meio de configuração
    * Por padrão, todas as instâncias de serviço nomeadas de um determinado tipo de serviço dentro de uma instância do aplicativo serão executadas no mesmo processo em um determinado nó. Isso significa que, embora você possa configurar cada instância de serviço de forma diferente, fazer isso é complicado. Os serviços devem ter algum token que eles usam para pesquisar sua configuração em um pacote de configuração. Normalmente, esse é apenas o nome do serviço. Isso funciona bem, mas associa a configuração aos nomes das instâncias de serviço nomeadas individuais dentro dessa instância do aplicativo. Isso pode ser confuso e difícil de gerenciar, pois a configuração normalmente é um artefato de tempo de design com valores específicos da instância do aplicativo. A criação de mais serviços sempre significa mais atualizações de aplicativo para alterar as informações nos pacotes de configuração ou para implantar novas, para que os novos serviços possam pesquisar suas informações específicas. Geralmente, é mais fácil criar uma instância de aplicativo chamada totalmente nova. Em seguida, você pode usar os parâmetros do aplicativo para definir qualquer configuração necessária para os serviços. Dessa forma, todos os serviços criados dentro dessa instância de aplicativo nomeado podem herdar configurações específicas. Por exemplo, em vez de ter um único arquivo de configuração com as configurações e personalizações para cada cliente, como segredos ou limites de recursos do cliente, você teria uma instância de aplicativo diferente para cada cliente com essas configurações substituí. 
  * O novo aplicativo serve como um limite de atualização
    * Em Service Fabric, diferentes instâncias de aplicativo nomeado servem como limites para atualização. Uma atualização de uma instância de aplicativo nomeada não afetará o código que outra instância de aplicativo nomeada está executando. Os diferentes aplicativos acabarão executando versões diferentes do mesmo código nos mesmos nós. Isso pode ser um fator quando você precisa tomar uma decisão de dimensionamento porque pode escolher se o novo código deve seguir as mesmas atualizações de outro serviço ou não. Por exemplo, digamos que uma chamada chegue ao serviço do Gerenciador responsável por dimensionar as cargas de trabalho de um cliente específico criando e excluindo serviços dinamicamente. Nesse caso, no entanto, a chamada é para uma carga de trabalho associada a um _novo_ cliente. A maioria dos clientes gostam de ser isolados uns dos outros não apenas pelos motivos de segurança e configuração listados anteriormente, mas porque ele fornece mais flexibilidade em termos de execução de versões específicas do software e escolha quando eles são atualizados. Você também pode criar uma nova instância do aplicativo e criar o serviço lá simplesmente para particionar ainda mais a quantidade de seus serviços que qualquer atualização irá tocar. Instâncias de aplicativo separadas fornecem maior granularidade ao fazer atualizações de aplicativo e também permitem testes A/B e implantações azuis/verdes. 
  * A instância do aplicativo existente está cheia
    * Em Service Fabric, a [capacidade do aplicativo](service-fabric-cluster-resource-manager-application-groups.md) é um conceito que você pode usar para controlar a quantidade de recursos disponíveis para instâncias de aplicativo específicas. Por exemplo, você pode decidir que um determinado serviço precisa ter outra instância criada para ser dimensionado. No entanto, essa instância do aplicativo está sem capacidade para uma determinada métrica. Se esse cliente ou carga de trabalho específico ainda precisar receber mais recursos, você poderá aumentar a capacidade existente para esse aplicativo ou criar um novo aplicativo. 

## <a name="scaling-at-the-partition-level"></a>Dimensionamento no nível da partição
O Service Fabric dá suporte ao particionamento. O particionamento divide um serviço em várias seções lógicas e físicas, cada uma delas operando de forma independente. Isso é útil com serviços com estado, já que nenhum conjunto de réplicas tem que lidar com todas as chamadas e manipular todo o estado de uma só vez. A [visão geral do particionamento](service-fabric-concepts-partitioning.md) fornece informações sobre os tipos de esquemas de particionamento com suporte. As réplicas de cada partição são distribuídas entre os nós em um cluster, distribuindo a carga desse serviço e garantindo que nem o serviço como um todo ou qualquer partição tenha um único ponto de falha. 

Considere um serviço que usa um esquema de particionamento de intervalo com uma chave baixa de 0, uma chave alta de 99 e uma contagem de partições de 4. Em um cluster de três nós, o serviço pode ser disposto com quatro réplicas que compartilham os recursos em cada nó, como mostrado aqui:

<center>

![Layout de partição com três nós](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Se você aumentar o número de nós, Service Fabric moverá algumas das réplicas existentes ali. Por exemplo, digamos que o número de nós aumente para quatro e as réplicas sejam redistribuídas. Agora, o serviço agora tem três réplicas em execução em cada nó, cada uma pertencente a partições diferentes. Isso permite uma melhor utilização de recursos, pois o novo nó não está frio. Normalmente, ele também melhora o desempenho, pois cada serviço tem mais recursos disponíveis para ele.

<center>

![Layout de partição com quatro nós](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>Dimensionamento usando o Gerenciador de recursos de Cluster Service Fabric e métricas
[](service-fabric-cluster-resource-manager-metrics.md) As métricas são como os serviços expressam seu consumo de recursos para Service Fabric. O uso de métricas dá ao Gerenciador de recursos de cluster uma oportunidade de reorganizar e otimizar o layout do cluster. Por exemplo, pode haver muitos recursos no cluster, mas eles podem não ser alocados para os serviços que estão trabalhando atualmente. O uso de métricas permite ao Gerenciador de recursos de cluster reorganizar o cluster para garantir que os serviços tenham acesso aos recursos disponíveis. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Dimensionamento adicionando e removendo nós do cluster 
Outra opção para dimensionar com Service Fabric é alterar o tamanho do cluster. Alterar o tamanho do cluster significa adicionar ou remover nós de um ou mais dos tipos de nó no cluster. Por exemplo, considere um caso em que todos os nós no cluster estejam ativos. Isso significa que os recursos do cluster são quase todos consumidos. Nesse caso, adicionar mais nós ao cluster é a melhor maneira de dimensionar. Depois que os novos nós ingressam no cluster, o Gerenciador de recursos de Cluster Service Fabric move os serviços para eles, resultando em menos carga total nos nós existentes. Para serviços sem estado com contagem de instâncias =-1, mais instâncias de serviço são criadas automaticamente. Isso permite que algumas chamadas sejam movidas dos nós existentes para os novos nós. 

Para obter mais informações, consulte dimensionamento de [cluster](service-fabric-cluster-scaling.md).

## <a name="choosing-a-platform"></a>Escolhendo uma plataforma

Devido a diferenças de implementação entre sistemas operacionais, optar por usar Service Fabric com Windows ou Linux pode ser uma parte vital do dimensionamento de seu aplicativo. Uma barreira potencial é o modo como o log de preparação é executado. Service Fabric no Windows usa um driver de kernel para um log um por computador, compartilhado entre réplicas de serviço com estado. Esse log pesa em aproximadamente 8 GB. O Linux, por outro lado, usa um log de preparo de 256 MB para cada réplica, tornando-o menos ideal para aplicativos que desejam maximizar o número de réplicas de serviço leves em execução em um determinado nó. Essas diferenças em requisitos de armazenamento temporários podem informar a plataforma desejada para Service Fabric implantação de cluster.

## <a name="putting-it-all-together"></a>Juntar tudo
Vamos pegar todas as ideias que discutimos aqui e falar por meio de um exemplo. Considere o seguinte serviço: você está tentando criar um serviço que atua como um catálogo de endereços, mantendo os nomes e as informações de contato. 

À frente você tem várias perguntas relacionadas à escala: Quantos usuários você vai ter? Quantos contatos cada usuário armazenará? Tentar descobrir tudo isso quando você estiver posicionando seu serviço pela primeira vez é difícil. Digamos que você vá com um único serviço estático com uma contagem de partição específica. As consequências de escolher a contagem de partições errada podem causar problemas de escala posteriormente. Da mesma forma, mesmo que você escolha a contagem correta, talvez não tenha todas as informações necessárias. Por exemplo, você também precisa decidir o tamanho do cluster na frente, tanto em termos do número de nós quanto de seus tamanhos. Geralmente, é difícil prever quantos recursos um serviço irá consumir durante seu tempo de vida. Também pode ser difícil saber antecipadamente o padrão de tráfego que o serviço realmente vê. Por exemplo, talvez as pessoas adicionem e removam seus contatos apenas na manhã, ou talvez sejam distribuídas uniformemente no decorrer do dia. Com base nesse caso, talvez seja necessário escalar horizontalmente e de forma dinâmica. Talvez você possa aprender a prever quando precisará escalar e reduzir horizontalmente, mas de qualquer forma, provavelmente será necessário reagir à alteração do consumo de recursos pelo seu serviço. Isso pode envolver a alteração do tamanho do cluster para fornecer mais recursos ao reorganizar o uso de recursos existentes não é suficiente. 

Mas por que mesmo tentar escolher um único esquema de partição para todos os usuários? Por que limitar-se a um serviço e a um cluster estático? Normalmente, a situação real é mais dinâmica. 

Ao compilar para escala, considere o padrão dinâmico a seguir. Talvez seja necessário adaptá-lo à sua situação:

1. Em vez de tentar escolher um esquema de particionamento para todos com antecedência, crie um "serviço de Gerenciador".
2. O trabalho do serviço de Gerenciador é examinar as informações do cliente ao se inscrever no serviço. Em seguida, dependendo dessas informações, o serviço do Gerenciador criará uma instância do seu serviço de armazenamento de contato _real_ _apenas para esse cliente_. Se eles exigirem configuração, isolamento ou atualizações específicas, você também poderá optar por criar uma instância do aplicativo para esse cliente. 

Esse padrão de criação dinâmica tem muitos benefícios:

  - Você não está tentando adivinhar a contagem correta de partições para todos os usuários na frente ou criar um único serviço que é infinitamente escalonável por conta própria. 
  - Diferentes usuários não precisam ter a mesma contagem de partições, contagem de réplicas, restrições de posicionamento, métricas, cargas padrão, nomes de serviço, configurações de DNS ou qualquer uma das outras propriedades especificadas no nível de serviço ou aplicativo. 
  - Você obterá segmentação de dados adicional. Cada cliente tem sua própria cópia do serviço
    - Cada serviço de cliente pode ser configurado de forma diferente e recebe mais ou menos recursos, com mais ou menos partições ou réplicas, conforme necessário, com base em sua escala esperada.
      - Por exemplo, digamos que o cliente pagou pela camada "ouro" – eles poderiam obter mais réplicas ou maior contagem de partições, e potencialmente recursos dedicados a seus serviços por meio de métricas e capacidades do aplicativo.
      - Ou digamos que eles forneceram informações indicando que o número de contatos que precisavam era "pequeno" – eles receberão apenas algumas partições ou poderiam até mesmo serem colocados em um pool de serviços compartilhados com outros clientes.
  - Você não está executando uma série de instâncias de serviço ou réplicas enquanto está aguardando que os clientes apareçam
  - Se um cliente sair, remover suas informações do serviço é tão simples quanto fazer com que o Gerenciador exclua esse serviço ou aplicativo que ele criou.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre conceitos de Service Fabric, consulte os seguintes artigos:

* [Disponibilidade de serviços de Service Fabric](service-fabric-availability-services.md)
* [Particionando serviços de Service Fabric](service-fabric-concepts-partitioning.md)
