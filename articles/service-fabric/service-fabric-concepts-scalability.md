---
title: Escalabilidade dos serviços de tecido de serviço
description: Saiba mais sobre o dimensionamento no Azure Service Fabric e as várias técnicas usadas para escalar aplicações.
author: masnider
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 87ac89edc1c9996afc03e7c2bd6743202fdfcb52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786196"
---
# <a name="scaling-in-service-fabric"></a>Escala no Tecido de Serviço
O Azure Service Fabric facilita a construção de aplicações escaláveis gerindo os serviços, divisórias e réplicas nos nós de um cluster. Executar muitas cargas de trabalho no mesmo hardware permite a máxima utilização de recursos, mas também proporciona flexibilidade em termos de como você escolhe escalar suas cargas de trabalho. Este vídeo do Canal 9 descreve como pode construir aplicações de microserviços escaláveis:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T116/player]

O dimensionamento em Tecido de Serviço é realizado de várias maneiras:

1. Dimensionamento criando ou removendo instâncias de serviço apátridas
2. Dimensionamento através da criação ou remoção de novos serviços nomeados
3. Dimensionamento criando ou removendo novas instâncias de aplicação nomeadas
4. Dimensionamento utilizando serviços divididos
5. Dimensionamento adicionando e removendo os nódes do cluster 
6. Dimensionamento utilizando métricas do Cluster Resource Manager

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Dimensionamento criando ou removendo instâncias de serviço apátridas
Uma das formas mais simples de escalar dentro da Service Fabric funciona com serviços apátridas. Quando se cria um serviço apátrida, tem-se a oportunidade de definir um `InstanceCount` . `InstanceCount` define quantas cópias de execução do código desse serviço são criadas quando o serviço começa. Digamos, por exemplo, que há 100 nós no aglomerado. Digamos também que um serviço é criado com um `InstanceCount` de 10. Durante o tempo de funcionamento, essas 10 cópias do código podem ficar demasiado ocupadas (ou podem não estar ocupadas o suficiente). Uma forma de escalar essa carga de trabalho é alterar o número de casos. Por exemplo, alguma peça de código de monitorização ou gestão pode alterar o número de instâncias existente para 50, ou para 5, dependendo se a carga de trabalho precisa de ser dimensionante para dentro ou para fora com base na carga. 

C#:

```csharp
StatelessServiceUpdateDescription updateDescription = new StatelessServiceUpdateDescription(); 
updateDescription.InstanceCount = 50;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Powershell:

```posh
Update-ServiceFabricService -Stateless -ServiceName $serviceName -InstanceCount 50
```
### <a name="using-dynamic-instance-count"></a>Usando a contagem de exemplos dinâmicos
Especificamente para serviços apátridas, o Service Fabric oferece uma forma automática de alterar a contagem de exemplos. Isto permite que o serviço se dimensione dinamicamente com o número de nós disponíveis. A forma de optar por este comportamento é definir a contagem de exemplos = -1. InstanceCount = -1 é uma instrução para o Service Fabric que diz "Executar este serviço apátrida em cada nó." Se o número de nós mudar, o Tecido de Serviço muda automaticamente a contagem de casos para corresponder, certificando-se de que o serviço está a funcionar em todos os nós válidos. 

C#:

```csharp
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//Set other service properties necessary for creation....
serviceDescription.InstanceCount = -1;
await fc.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless -PartitionSchemeSingleton -InstanceCount "-1"
```

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Dimensionamento através da criação ou remoção de novos serviços nomeados
Uma instância de serviço nomeada é uma instância específica de um tipo de serviço (ver ciclo de [vida da aplicação de tecido de serviço](service-fabric-application-lifecycle.md)) dentro de algumas instâncias de aplicação nomeadas no cluster. 

Novas instâncias de serviço nomeadas podem ser criadas (ou removidas) à medida que os serviços se tornam mais ou menos ocupados. Isto permite que os pedidos sejam distribuídos por mais instâncias de serviço, permitindo geralmente que a carga nos serviços existentes diminua. Ao criar serviços, o Service Fabric Cluster Resource Manager coloca os serviços no cluster de forma distribuída. As decisões exatas são regidas pelas [métricas](service-fabric-cluster-resource-manager-metrics.md) do cluster e outras regras de colocação. Os serviços podem ser criados de várias maneiras, mas os mais comuns são através de ações administrativas como alguém que chama [`New-ServiceFabricService`](/powershell/module/servicefabric/new-servicefabricservice) , ou por chamada de [`CreateServiceAsync`](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) código. `CreateServiceAsync` pode mesmo ser chamado de dentro de outros serviços em execução no cluster.

Criar serviços dinamicamente pode ser usado em todos os tipos de cenários, e é um padrão comum. Por exemplo, considere um serviço estatal que represente um determinado fluxo de trabalho. As chamadas que representam o trabalho vão aparecer neste serviço, e este serviço vai executar os passos para esse fluxo de trabalho e registar o progresso. 

Como faria esta escala de serviço em particular? O serviço poderia ser multi-inquilino de alguma forma, e aceitar chamadas e dar pontapés para muitos casos diferentes do mesmo fluxo de trabalho de uma só vez. No entanto, isso pode tornar o código mais complexo, uma vez que agora tem de se preocupar com muitos casos diferentes do mesmo fluxo de trabalho, todos em diferentes fases e de diferentes clientes. Além disso, lidar com vários fluxos de trabalho ao mesmo tempo não resolve o problema da escala. Isto porque, em algum momento, este serviço consumirá demasiados recursos para caber numa determinada máquina. Muitos serviços não construídos para este padrão em primeiro lugar também experimentam dificuldades devido a algum estrangulamento ou abrandamento inerentes no seu código. Este tipo de problemas fazem com que o serviço não funcione tão bem quando o número de fluxos de trabalho simultâneos que está a rastrear aumenta.  

Uma solução é criar uma instância deste serviço para cada instância diferente do fluxo de trabalho que pretende rastrear. Este é um grande padrão e funciona se o serviço é apátrida ou stateful. Para que este padrão funcione, há geralmente outro serviço que funciona como um "Serviço de Gestão de Carga de Trabalho". O trabalho deste serviço é receber pedidos e encaminhar esses pedidos para outros serviços. O gestor pode criar dinamicamente uma instância do serviço de carga de trabalho quando recebe a mensagem e, em seguida, transmitir pedidos a esses serviços. O serviço de gerente também pode receber chamadas quando um determinado serviço de fluxo de trabalho completar o seu trabalho. Quando o gestor recebe estas chamadas, pode apagar essa instância do serviço de fluxo de trabalho, ou deixá-lo se forem esperadas mais chamadas. 

Versões avançadas deste tipo de gestor podem até criar piscinas dos serviços que gere. A piscina ajuda a garantir que quando um novo pedido chega não tem que esperar que o serviço rode. Em vez disso, o gestor pode apenas escolher um serviço de fluxo de trabalho que não esteja atualmente ocupado a partir da piscina, ou rota aleatoriamente. Manter um conjunto de serviços disponíveis torna o tratamento de novos pedidos mais rápido, uma vez que é menos provável que o pedido tenha de esperar que um novo serviço seja ressarado. A criação de novos serviços é rápida, mas não gratuita ou instantânea. A piscina ajuda a minimizar o tempo que o pedido tem de esperar antes de ser reparado. Você verá frequentemente este gerente e padrão de piscina quando os tempos de resposta são mais importantes. Fazer fila no pedido e criar o serviço em segundo plano e _depois_ transmiti-lo é também um padrão popular de gestor, como está a criar e eliminar serviços com base em alguns rastreios da quantidade de trabalho que o serviço tem atualmente pendente. 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Dimensionamento criando ou removendo novas instâncias de aplicação nomeadas
Criar e eliminar instâncias de aplicação inteiras é semelhante ao padrão de criação e eliminação de serviços. Para este padrão, existe algum serviço de gestão que está a tomar a decisão com base nos pedidos que está a ver e na informação que está a receber dos outros serviços dentro do cluster. 

Quando deve ser utilizada a criação de uma nova instância de aplicação nomeada em vez de criar uma nova instância de serviço nomeada em algumas aplicações já existentes? Há alguns casos:

  * A nova instância de aplicação destina-se a um cliente cujo código precisa de ser executado sob algumas definições específicas de identidade ou segurança.
    * O Service Fabric permite definir diferentes pacotes de código para executar sob identidades específicas. Para lançar o mesmo pacote de códigos sob diferentes identidades, as ativações devem ocorrer em diferentes instâncias de aplicação. Considere um caso em que tenha as cargas de trabalho de um cliente existentes implementadas. Estes podem estar a funcionar com uma determinada identidade para que possa monitorizar e controlar o seu acesso a outros recursos, como bases de dados remotas ou outros sistemas. Neste caso, quando um novo cliente se inscreve, provavelmente não quer ativar o seu código no mesmo contexto (espaço de processo). Enquanto pôde, isto dificulta a atuação do seu código de serviço no contexto de uma determinada identidade. Normalmente deve ter mais segurança, isolamento e código de gestão de identidade. Em vez de utilizar diferentes instâncias de serviço nomeadas dentro da mesma instância de aplicação e, portanto, do mesmo espaço de processo, pode utilizar diferentes instâncias de Aplicação de Tecido de Serviço. Isto facilita a definição de diferentes contextos de identidade.
  * A nova instância de aplicação também serve como um meio de configuração
    * Por predefinição, todas as instâncias de serviço nomeadas de um determinado tipo de serviço dentro de uma instância de aplicação serão executadas no mesmo processo num dado nó. O que isto significa é que, embora possa configurar cada instância de serviço de forma diferente, fazê-lo é complicado. Os serviços devem ter algum símbolo que usam para procurar o seu config dentro de um pacote de configuração. Normalmente este é apenas o nome do serviço. Isto funciona bem, mas acoplia a configuração aos nomes dos casos de serviço nomeados por indivíduo nessa instância de aplicação. Isto pode ser confuso e difícil de gerir, uma vez que a configuração é normalmente um artefacto do tempo de design com valores específicos de instância de aplicação. Criar mais serviços significa sempre mais atualizações de aplicações para alterar a informação dentro dos pacotes config ou para implementar novos para que os novos serviços possam procurar as suas informações específicas. Muitas vezes é mais fácil criar uma nova instância de aplicação. Em seguida, pode utilizar os parâmetros de aplicação para definir qualquer configuração necessária para os serviços. Desta forma, todos os serviços criados dentro dessa instância de aplicação nomeada podem herdar configurações específicas. Por exemplo, em vez de ter um único ficheiro de configuração com as definições e personalizações para cada cliente, como segredos ou por limite de recursos do cliente, em vez disso, teria uma instância de aplicação diferente para cada cliente com estas definições ultrapassadas. 
  * A nova aplicação serve como um limite de upgrade
    * Dentro do Service Fabric, diferentes instâncias de aplicação nomeadas servem como limites para upgrade. Uma atualização de uma instância de aplicação nomeada não terá impacto no código que outra instância de aplicação nomeada está a ser executada. As diferentes aplicações acabarão por executar diferentes versões do mesmo código nos mesmos nós. Isto pode ser um fator quando você precisa tomar uma decisão de escala porque você pode escolher se o novo código deve seguir as mesmas atualizações que outro serviço ou não. Por exemplo, digamos que uma chamada chega ao serviço de gerente que é responsável por escalonar as cargas de trabalho de um determinado cliente, criando e eliminando os serviços de forma dinâmica. Neste caso, no entanto, a chamada é para uma carga de trabalho associada a um _novo_ cliente. A maioria dos clientes gosta de estar isolada umas das outras não só pelas razões de segurança e configuração listadas anteriormente, mas porque proporciona mais flexibilidade em termos de executar versões específicas do software e escolher quando são atualizados. Pode também criar uma nova instância de aplicação e criar o serviço lá apenas para dividir a quantidade dos seus serviços que qualquer atualização tocará. Instâncias de aplicação separadas proporcionam maior granularidade ao fazer atualizações de aplicações, e também permitem testes A/B e implementações Azul/Verde. 
  * A instância de aplicação existente está cheia
    * No Service Fabric, [a capacidade de aplicação](service-fabric-cluster-resource-manager-application-groups.md) é um conceito que pode utilizar para controlar a quantidade de recursos disponíveis para casos específicos de aplicação. Por exemplo, pode decidir que um determinado serviço precisa de ter outro caso criado para escalar. No entanto, esta aplicação está fora de capacidade para uma determinada métrica. Se este cliente ou carga de trabalho em particular ainda deve ser concedido mais recursos, então você pode aumentar a capacidade existente para essa aplicação ou criar uma nova aplicação. 

## <a name="scaling-at-the-partition-level"></a>Escala ao nível da partição
O Tecido de Serviço suporta a partição. A partição divide um serviço em várias secções lógicas e físicas, cada uma das quais opera de forma independente. Isto é útil com serviços estatais, uma vez que nenhum conjunto de réplicas tem que lidar com todas as chamadas e manipular todo o estado de uma só vez. A [visão geral da partição](service-fabric-concepts-partitioning.md) fornece informações sobre os tipos de esquemas de partição que são apoiados. As réplicas de cada partição são espalhadas pelos nós num cluster, distribuindo a carga desse serviço e garantindo que nem o serviço como um todo ou qualquer partição tem um único ponto de falha. 

Considere um serviço que usa um esquema de partição variado com uma chave baixa de 0, uma chave alta de 99, e uma contagem de partição de 4. Num aglomerado de três nós, o serviço pode ser estabelecido com quatro réplicas que partilham os recursos em cada nó, como mostrado aqui:

<center>

![Layout de partição com três nódoas](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Se aumentar o número de nós, o Service Fabric irá mover algumas das réplicas existentes lá. Por exemplo, digamos que o número de nós aumenta para quatro e as réplicas são redistribuídas. Agora o serviço tem agora três réplicas em cada nó, cada uma pertencente a diferentes divisórias. Isto permite uma melhor utilização dos recursos, uma vez que o novo nó não está frio. Normalmente, também melhora o desempenho, uma vez que cada serviço tem mais recursos disponíveis.

<center>

![Layout de partição com quatro nódoas](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>Dimensionamento utilizando o Gestor de Recursos do Cluster de Tecido de Serviço e métricas
[As métricas](service-fabric-cluster-resource-manager-metrics.md) são como os serviços expressam o seu consumo de recursos ao Service Fabric. A utilização de métricas dá ao Cluster Resource Manager a oportunidade de reorganizar e otimizar o layout do cluster. Por exemplo, pode haver muitos recursos no cluster, mas podem não ser atribuídos aos serviços que estão atualmente a trabalhar. A utilização de métricas permite ao Cluster Resource Manager reorganizar o cluster para garantir que os serviços tenham acesso aos recursos disponíveis. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Dimensionamento adicionando e removendo os nódes do cluster 
Outra opção para escalar com o Tecido de Serviço é alterar o tamanho do cluster. Alterar o tamanho do cluster significa adicionar ou remover nós para um ou mais dos tipos de nós no cluster. Por exemplo, considere um caso em que todos os nós do aglomerado estão quentes. Isto significa que os recursos do cluster são quase todos consumidos. Neste caso, adicionar mais nós ao cluster é a melhor maneira de escalar. Uma vez que os novos nós se juntam ao cluster, o Service Fabric Cluster Resource Manager transfere os serviços para eles, resultando numa menor carga total nos nós existentes. Para serviços apátridas com contagem de exemplos = -1, mais instâncias de serviço são criadas automaticamente. Isto permite que algumas chamadas passem dos nós existentes para os novos nós. 

Para obter mais informações, consulte [a escala de clusters.](service-fabric-cluster-scaling.md)

## <a name="choosing-a-platform"></a>Escolher uma plataforma

Devido às diferenças de implementação entre sistemas operativos, optar por utilizar o Tecido de Serviço com Windows ou Linux pode ser uma parte vital do dimensionamento da sua aplicação. Uma barreira potencial é a forma como a exploração madeireira encenada é realizada. O Tecido de Serviço no Windows utiliza um controlador de núcleo para um registo de uma por máquina, partilhado entre réplicas de serviço imponente. Este tronco pesa cerca de 8 GB. O Linux, por outro lado, usa um registo de 256 MB para cada réplica, tornando-o menos ideal para aplicações que pretendem maximizar o número de réplicas de serviço leves que correm num dado nó. Estas diferenças nos requisitos de armazenamento temporário podem potencialmente informar a plataforma desejada para a implantação do cluster de tecido de serviço.

## <a name="putting-it-all-together"></a>Juntar tudo
Vamos pegar em todas as ideias que discutimos aqui e falar através de um exemplo. Considere o seguinte serviço: está a tentar construir um serviço que funciona como uma agenda de endereços, mantendo nomes e informações de contacto. 

Logo à frente tem um monte de perguntas relacionadas com a escala: Quantos utilizadores vai ter? Quantos contactos cada utilizador irá armazenar? Tentar perceber tudo isto quando se está a fazer o seu serviço pela primeira vez é difícil. Digamos que ias fazer um único serviço estático com uma contagem específica de divisórias. As consequências de escolher a contagem errada de divisórias podem fazer com que tenha problemas de escala mais tarde. Da mesma forma, mesmo que escolha a contagem certa, pode não ter toda a informação de que precisa. Por exemplo, também tem de decidir o tamanho do cluster na frente, tanto em termos do número de nós como dos seus tamanhos. Normalmente é difícil prever quantos recursos um serviço vai consumir ao longo da sua vida. Também pode ser difícil saber com antecedência o padrão de tráfego que o serviço realmente vê. Por exemplo, talvez as pessoas adicionem e removam os seus contactos apenas logo pela manhã, ou talvez seja distribuído uniformemente ao longo do dia. Com base nisto, pode precisar de escalar e dinamicamente. Talvez possas aprender a prever quando vais precisar de escalar para fora e para dentro, mas de qualquer maneira provavelmente vais precisar de reagir à mudança do consumo de recursos pelo teu serviço. Isto pode implicar mudar o tamanho do cluster de modo a fornecer mais recursos quando a reorganização da utilização dos recursos existentes não é suficiente. 

Mas por que tentar escolher um único esquema de partição para todos os utilizadores? Porquê limitar-se a um serviço e a um aglomerado estático? A situação real é geralmente mais dinâmica. 

Ao construir para escala, considere o seguinte padrão dinâmico. Poderá ser necessário adaptá-lo à sua situação:

1. Em vez de tentar escolher um esquema de partição para todos na frente, construa um "serviço de gerente".
2. O trabalho do serviço de gerente é olhar para a informação do cliente quando se inscrevem para o seu serviço. Em seguida, dependendo dessa informação, o serviço de gerente cria uma instância do seu _serviço de_ armazenamento de contactos real _apenas para esse cliente_. Se necessitarem de configurações específicas, isolamento ou upgrades, também pode decidir rodar uma instância de Aplicação para este cliente. 

Este padrão de criação dinâmica muitos benefícios:

  - Não está a tentar adivinhar a contagem correta de divisórias para todos os utilizadores à frente ou construir um único serviço que seja infinitamente escalável por si só. 
  - Os diferentes utilizadores não têm de ter a mesma contagem de divisórias, contagem de réplicas, restrições de colocação, métricas, cargas predefinidas, nomes de serviço, configurações de DNS ou qualquer outras propriedades especificadas ao nível do serviço ou aplicação. 
  - Ganha-se uma segmentação adicional de dados. Cada cliente tem a sua própria cópia do serviço
    - Cada atendimento ao cliente pode ser configurado de forma diferente e concedido mais ou menos recursos, com mais ou menos divisórias ou réplicas, conforme necessário, com base na sua escala esperada.
      - Por exemplo, digamos que o cliente pagou pelo nível "Gold" - eles poderiam obter mais réplicas ou maior contagem de divisórias, e potencialmente recursos dedicados aos seus serviços através de métricas e capacidades de aplicação.
      - Ou dizem que forneceram informações que indicam que o número de contactos de que precisavam era "Pequeno" - receberiam apenas algumas divisórias, ou poderiam mesmo ser colocados numa piscina de serviço partilhada com outros clientes.
  - Não estás a executar um monte de casos de serviço ou réplicas enquanto esperas que os clientes apareçam.
  - Se um cliente alguma vez sair, então remover as suas informações do seu serviço é tão simples como ter o gestor a apagar esse serviço ou aplicação que criou.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os conceitos de Tecido de Serviço, consulte os seguintes artigos:

* [Disponibilidade de serviços de tecido de serviço](service-fabric-availability-services.md)
* [Serviços de tecido de partilha](service-fabric-concepts-partitioning.md)
