---
title: Escalabilidade dos serviços de Tecido de Serviço
description: Conheça a escala em Tecido de Serviço Azure e as várias técnicas usadas para escalar aplicações.
author: masnider
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: masnider
ms.openlocfilehash: 17827342b67d37d9fbeb56654824e004367823ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282565"
---
# <a name="scaling-in-service-fabric"></a>Escala no tecido de serviço
O Azure Service Fabric facilita a construção de aplicações escaláveis através da gestão dos serviços, divisórias e réplicas nos nós de um cluster. Executar muitas cargas de trabalho no mesmo hardware permite a utilização máxima de recursos, mas também proporciona flexibilidade em termos de como você escolhe escalar as suas cargas de trabalho. Este vídeo do Canal 9 descreve como pode construir aplicações de microserviços escaláveis:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T116/player]

A escala em Tecido de Serviço é realizada de várias maneiras diferentes:

1. Escalando criando ou removendo instâncias de serviço apátridas
2. Escalando criando ou removendo novos serviços nomeados
3. Escalando criando ou removendo novas instâncias de candidatura saqueadas
4. Escalando utilizando serviços divididos
5. Escalando adicionando e removendo nós do cluster 
6. Escalando usando métricas do Cluster Resource Manager

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Escalando criando ou removendo instâncias de serviço apátridas
Uma das formas mais simples de escalar dentro do Service Fabric trabalha com serviços apátridas. Quando se cria um serviço apátrida, `InstanceCount`temos a oportunidade de definir um . `InstanceCount`define quantas cópias em execução do código desse serviço são criadas quando o serviço começa. Digamos, por exemplo, que há 100 nós no aglomerado. Digamos também que um serviço é `InstanceCount` criado com um de 10. Durante o tempo de funcionamento, essas 10 cópias em execução do código podem tornar-se demasiado ocupadas (ou não poderiam estar suficientemente ocupadas). Uma forma de escalar essa carga de trabalho é alterar o número de casos. Por exemplo, uma parte do código de monitorização ou de gestão pode alterar o número de instâncias existente para 50, ou para 5, dependendo se a carga de trabalho precisa de escalar dentro ou fora com base na carga. 

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
### <a name="using-dynamic-instance-count"></a>Usando a contagem de instâncias dinâmicas
Especificamente para serviços apátridas, o Service Fabric oferece uma forma automática de alterar a contagem de casos. Isto permite que o serviço escala dinamicamente com o número de nós disponíveis. A maneira de optar por este comportamento é definir a contagem de exemplo = -1. CasosCount = -1 é uma instrução para o Tecido de Serviço que diz "Executar este serviço apátrida em cada nó." Se o número de nós mudar, o Tecido de Serviço altera automaticamente a contagem de instâncias para corresponder, certificando-se de que o serviço está em funcionamento em todos os nós válidos. 

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

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Escalando criando ou removendo novos serviços nomeados
Uma instância de serviço nomeada é uma instância específica de um tipo de serviço (ver ciclo de vida de [aplicação service Fabric](service-fabric-application-lifecycle.md)) dentro de alguma instância de aplicação nomeada no cluster. 

Novas instâncias de serviço nomeadas podem ser criadas (ou removidas) à medida que os serviços se tornam mais ou menos ocupados. Isto permite que os pedidos sejam distribuídos por mais instâncias de serviço, normalmente permitindo que a carga nos serviços existentes diminua. Ao criar serviços, o Gestor de Recursos de Cluster de Tecidos de Serviço coloca os serviços no cluster de forma distribuída. As decisões exatas regem-se pelas métricas do cluster e [outras](service-fabric-cluster-resource-manager-metrics.md) regras de colocação. Os serviços podem ser criados de várias maneiras [`New-ServiceFabricService`](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)diferentes, mas [`CreateServiceAsync`](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync?view=azure-dotnet)os mais comuns são através de ações administrativas como alguém que chama , ou por chamada de código. `CreateServiceAsync`pode até ser chamado de dentro de outros serviços que executam no cluster.

A criação de serviços dinamicamente pode ser usada em todos os tipos de cenários, e é um padrão comum. Por exemplo, considere um serviço estatal que represente um fluxo de trabalho particular. As chamadas representativas do trabalho vão aparecer neste serviço, e este serviço vai executar os passos para esse fluxo de trabalho e registar progressos. 

Como faria esta escala de serviço em particular? O serviço poderia ser multi-inquilino de alguma forma, e aceitar chamadas e dar início a passos para muitos casos diferentes do mesmo fluxo de trabalho de uma só vez. No entanto, isso pode tornar o código mais complexo, uma vez que agora tem de se preocupar com muitos casos diferentes do mesmo fluxo de trabalho, todos em diferentes fases e de diferentes clientes. Além disso, lidar com múltiplos fluxos de trabalho ao mesmo tempo não resolve o problema de escala. Isto porque, a dada altura, este serviço consumirá demasiados recursos para caber numa determinada máquina. Muitos serviços não construídos para este padrão em primeiro lugar também experimentam dificuldades devido a algum estrangulamento ou desaceleração inerente supor o seu código. Este tipo de problemas fazem com que o serviço não funcione tão bem quando o número de fluxos de trabalho simultâneos que está a acompanhar aumenta.  

Uma solução é criar uma instância deste serviço para cada instância diferente do fluxo de trabalho que pretende acompanhar. Este é um grande padrão e funciona se o serviço é apátrida ou apátrida. Para que este padrão funcione, há geralmente outro serviço que funciona como um "Serviço de Gestor de Carga de Trabalho". O trabalho deste serviço é receber pedidos e encaminhar esses pedidos para outros serviços. O gestor pode criar dinamicamente uma instância do serviço de carga de trabalho quando recebe a mensagem e, em seguida, transmitir pedidos para esses serviços. O serviço de gerente também poderia receber chamadas quando um determinado serviço de fluxo de trabalho completa o seu trabalho. Quando o gestor recebe estas chamadas, pode eliminar essa instância do serviço de fluxo de trabalho, ou deixá-la se forem esperadas mais chamadas. 

Versões avançadas deste tipo de gestor podem até criar piscinas dos serviços que gere. A piscina ajuda a garantir que quando um novo pedido chega, não tem que esperar que o serviço se alege. Em vez disso, o gestor pode apenas escolher um serviço de fluxo de trabalho que não esteja atualmente ocupado a partir da piscina, ou rota aleatoriamente. Manter um conjunto de serviços disponíveis torna o tratamento de novos pedidos mais rapidamente, uma vez que é menos provável que o pedido tenha de esperar que um novo serviço seja prestado. A criação de novos serviços é rápida, mas não gratuita ou instantânea. A piscina ajuda a minimizar o tempo que o pedido tem de esperar antes de ser reparado. Você verá frequentemente este gerente e padrão de piscina quando os tempos de resposta são os mais importantes. Fazer fila do pedido e criar o serviço em segundo plano e _depois_ transmiti-lo é também um padrão de gestor popular, assim como a criação e apagamento de serviços com base em algum acompanhamento da quantidade de trabalho que o serviço tem atualmente pendente. 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Escalando criando ou removendo novas instâncias de candidatura saqueadas
Criar e eliminar instâncias inteiras de aplicações é semelhante ao padrão de criação e apagamento de serviços. Para este padrão, há algum serviço de gestor que está a tomar a decisão com base nos pedidos que está a ver e na informação que está a receber dos outros serviços dentro do cluster. 

Quando deve ser utilizada a criação de uma nova instância de candidatura nomeada em vez de criar um novo serviço nomeado em alguma seleção já existente? Há alguns casos:

  * A nova instância de aplicação é para um cliente cujo código precisa de ser executado sob algumas definições de identidade ou segurança específicas.
    * O Service Fabric permite definir diferentes pacotes de código para executar sob determinadas identidades. Para lançar o mesmo pacote de código sob diferentes identidades, as ativações devem ocorrer em diferentes instâncias de aplicação. Considere um caso em que tem as cargas de trabalho de um cliente existentes. Estes podem estar a funcionar sob uma determinada identidade para que possa monitorizar e controlar o seu acesso a outros recursos, tais como bases de dados remotas ou outros sistemas. Neste caso, quando um novo cliente se inscreve, provavelmente não quer ativar o seu código no mesmo contexto (espaço de processo). Embora pudesse, isto dificulta que o seu código de serviço aja no contexto de uma determinada identidade. Normalmente deve ter mais segurança, isolamento e código de gestão de identidade. Em vez de utilizar diferentes instâncias de serviço nomeadas dentro da mesma instância de aplicação e, portanto, no mesmo espaço de processo, pode utilizar diferentes casos de Aplicação de Tecido de Serviço. Isto facilita a definição de diferentes contextos de identidade.
  * A nova instância de aplicação também serve como um meio de configuração
    * Por padrão, todas as instâncias de serviço nomeadas de um determinado tipo de serviço dentro de uma instância de aplicação serão executadas no mesmo processo num determinado nó. O que isto significa é que, embora possa configurar cada instância de serviço de forma diferente, fazê-lo é complicado. Os serviços devem ter algum sinal que usam para procurar o seu config dentro de um pacote de configuração. Normalmente este é apenas o nome do serviço. Isto funciona bem, mas acopla a configuração aos nomes dos indivíduos chamados instâncias de serviço dentro dessa instância de aplicação. Isto pode ser confuso e difícil de gerir, uma vez que a configuração é normalmente um artefacto de tempo de design com valores específicos da instância de aplicação. Criar mais serviços significa sempre mais atualizações de aplicações para alterar a informação dentro dos pacotes de config ou para implementar novos para que os novos serviços possam procurar as suas informações específicas. Muitas vezes é mais fácil criar uma nova instância de candidatura. Em seguida, pode utilizar os parâmetros de aplicação para definir qualquer configuração necessária para os serviços. Desta forma, todos os serviços que são criados dentro dessa instância de aplicação nomeada podem herdar configurações de configuração particulares. Por exemplo, em vez de ter um único ficheiro de configuração com as definições e personalizações para cada cliente, tais como segredos ou por limites de recursos do cliente, teria uma instância de aplicação diferente para cada cliente com estas definições overridden. 
  * A nova aplicação serve como um limite de upgrade
    * Dentro do Tecido de Serviço, diferentes casos de aplicação nomeados servem como limites para a atualização. Uma atualização de uma instância de candidatura nomeada não afetará o código que outra instância de aplicação nomeada está a executar. As diferentes aplicações acabarão por executar diferentes versões do mesmo código nos mesmos nódosos. Isto pode ser um fator quando precisa de tomar uma decisão de escala, porque pode escolher se o novo código deve seguir as mesmas atualizações que outro serviço ou não. Por exemplo, digamos que uma chamada chega ao serviço de gerente que é responsável pela escala de cargas de trabalho de um determinado cliente, criando e apagando serviços de forma dinâmica. No entanto, neste caso, a chamada é para uma carga de trabalho associada a um _novo_ cliente. A maioria dos clientes gosta de estar isolada umas das outras não só pelas razões de segurança e configuração listadas anteriormente, mas porque proporciona mais flexibilidade em termos de executar versões específicas do software e escolher quando são atualizados. Também pode criar uma nova instância de aplicação e criar o serviço lá simplesmente para dividir ainda mais a quantidade dos seus serviços que qualquer upgrade irá tocar. As instâncias de aplicação separadas proporcionam uma maior granularidade ao fazer upgrades de aplicações, e também permitem testes a/B e implementações Blue/Green. 
  * A instância de candidatura existente está cheia
    * No Tecido de Serviço, a capacidade de [aplicação](service-fabric-cluster-resource-manager-application-groups.md) é um conceito que pode utilizar para controlar a quantidade de recursos disponíveis para determinadas instâncias de aplicação. Por exemplo, pode decidir que um determinado serviço precisa de ter outra instância criada para escalar. No entanto, esta instância de aplicação está fora de capacidade para uma determinada métrica. Se este cliente em particular ou carga de trabalho ainda tiver mais recursos, então pode aumentar a capacidade existente para essa aplicação ou criar uma nova aplicação. 

## <a name="scaling-at-the-partition-level"></a>Escala ao nível da partição
Serviço Tecido suporta partição. A partilha divide um serviço em várias secções lógicas e físicas, cada uma das quais opera de forma independente. Isto é útil com serviços estatais, uma vez que nenhum conjunto de réplicas tem que lidar com todas as chamadas e manipular todo o estado de uma só vez. A [visão geral](service-fabric-concepts-partitioning.md) da partilha fornece informações sobre os tipos de esquemas de partilha que são apoiados. As réplicas de cada partição estão espalhadas pelos nós de um cluster, distribuindo a carga desse serviço e garantindo que nem o serviço como um todo ou qualquer partição tem um único ponto de falha. 

Considere um serviço que usa um esquema de partição variado com uma tecla baixa de 0, uma chave alta de 99, e uma contagem de partição de 4. Num aglomerado de três nós, o serviço pode ser estabelecido com quatro réplicas que partilham os recursos em cada nó, como mostrado aqui:

<center>

![Layout de partição com três nósos](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Se aumentar o número de nós, o Tecido de Serviço irá mover algumas das réplicas existentes lá. Por exemplo, digamos que o número de nós aumenta para quatro e as réplicas são redistribuídas. Agora o serviço tem três réplicas em cada nó, cada uma pertencente a divisórias diferentes. Isto permite uma melhor utilização de recursos, uma vez que o novo nó não é frio. Tipicamente, também melhora o desempenho, uma vez que cada serviço tem mais recursos disponíveis para ele.

<center>

![Layout de partição com quatro nósos](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>Escalando usando o Gestor de Recursos de Cluster de Tecido sacana de serviço e métricas
[As métricas](service-fabric-cluster-resource-manager-metrics.md) são como os serviços expressam o seu consumo de recursos ao Tecido de Serviço. A utilização de métricas dá ao Cluster Resource Manager a oportunidade de reorganizar e otimizar o layout do cluster. Por exemplo, pode haver muitos recursos no cluster, mas podem não ser afetados aos serviços que estão atualmente a fazer trabalho. A utilização de métricas permite ao Cluster Resource Manager reorganizar o cluster para garantir que os serviços têm acesso aos recursos disponíveis. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Escalando adicionando e removendo nós do cluster 
Outra opção para escalar com o Tecido de Serviço é alterar o tamanho do cluster. Alterar o tamanho do cluster significa adicionar ou remover nós para um ou mais tipos de nós no cluster. Por exemplo, considere um caso em que todos os nós do aglomerado são quentes. Isto significa que os recursos do cluster são quase todos consumidos. Neste caso, adicionar mais nós ao cluster é a melhor maneira de escalar. Uma vez que os novos nós se juntam ao cluster, o Gestor de Recursos de Cluster de Tecidos de Serviço transfere os serviços para eles, resultando numa carga menor nos nós existentes. Para serviços apátridas com contagem de exemplos = -1, mais instâncias de serviço são automaticamente criadas. Isto permite que algumas chamadas se desloquem dos nós existentes para os novos nós. 

Para mais informações, consulte a [escala de cluster](service-fabric-cluster-scaling.md).

## <a name="choosing-a-platform"></a>Escolher uma plataforma

Devido às diferenças de implementação entre sistemas operativos, optar por utilizar o Tecido de Serviço com Windows ou Linux pode ser uma parte vital da escala da sua aplicação. Uma barreira potencial é a forma como a exploração madeireira é realizada. Serviço Tecido no Windows utiliza um controlador de kernel para um registo de uma por máquina, partilhado entre réplicas de serviço sinuosos. Este tronco pesa cerca de 8 GB. O Linux, por outro lado, usa um registo de 256 MB para cada réplica, tornando-o menos ideal para aplicações que querem maximizar o número de réplicas de serviço leve que funcionam num dado nó. Estas diferenças nos requisitos de armazenamento temporário poderiam potencialmente informar a plataforma desejada para a implantação do cluster Service Fabric.

## <a name="putting-it-all-together"></a>Juntar tudo
Vamos pegar em todas as ideias que discutimos aqui e falar através de um exemplo. Considere o seguinte serviço: está a tentar construir um serviço que funciona como um livro de endereços, agarrando-se a nomes e informações de contacto. 

Na frente tem um monte de questões relacionadas com a escala: Quantos utilizadores vai ter? Quantos contactos cada utilizador irá armazenar? Tentar perceber tudo isto quando está a defender o serviço pela primeira vez é difícil. Digamos que ias optar por um único serviço estático com uma contagem específica de partição. As consequências de escolher a contagem errada de partição podem fazer com que tenha problemas de escala mais tarde. Da mesma forma, mesmo que escolha a contagem certa, pode não ter toda a informação de que precisa. Por exemplo, você também tem que decidir o tamanho do cluster na frente, tanto em termos do número de nós e seus tamanhos. Normalmente é difícil prever quantos recursos um serviço vai consumir ao longo da sua vida. Também pode ser difícil saber com antecedência o padrão de tráfego que o serviço realmente vê. Por exemplo, talvez as pessoas acrescentem e removam os seus contactos apenas logo pela manhã, ou talvez seja distribuído uniformemente ao longo do dia. Com base nisto, talvez precises de escalar para fora e em dinamicamente. Talvez possas aprender a prever quando vais precisar de escalar para fora e para dentro, mas de qualquer maneira provavelmente vais precisar de reagir à mudança do consumo de recursos pelo teu serviço. Isto pode implicar a alteração do tamanho do cluster de forma a fornecer mais recursos ao reorganizar o uso dos recursos existentes não é suficiente. 

Mas por que tentar escolher um único esquema de partição para todos os utilizadores? Por que se limitar a um serviço e um aglomerado estático? A situação real é geralmente mais dinâmica. 

Ao construir para a escala, considere o seguinte padrão dinâmico. Pode ter de o adaptar à sua situação:

1. Em vez de tentar escolher um esquema de partição para todos, construa um "serviço de gerente".
2. O trabalho do serviço de gerente é olhar para a informação do cliente quando se inscrevem para o seu serviço. Em seguida, dependendo dessa informação, o serviço de gerente criar uma instância do seu serviço _real_ de armazenamento de contato _apenas para esse cliente_. Se necessitarem de configuração, isolamento ou upgrades específicos, também pode decidir girar uma instância de Aplicação para este cliente. 

Este padrão dinâmico de criação muitos benefícios:

  - Não está a tentar adivinhar a contagem correta de partições para todos os utilizadores na frente ou construir um único serviço que é infinitamente escalável por si só. 
  - Os diferentes utilizadores não têm de ter a mesma contagem de divisórias, contagem de réplicas, restrições de colocação, métricas, cargas predefinidas, nomes de serviço, configurações de DNS ou qualquer uma das outras propriedades especificadas ao nível de serviço ou aplicação. 
  - Ganha-se uma segmentação adicional de dados. Cada cliente tem a sua própria cópia do serviço
    - Cada serviço ao cliente pode ser configurado de forma diferente e concedido mais ou menos recursos, com mais ou menos divisórias ou réplicas, conforme necessário, com base na sua escala esperada.
      - Por exemplo, digamos que o cliente pagou pelo nível "Gold" - eles poderiam obter mais réplicas ou maior contagem de partições, e recursos potencialmente dedicados aos seus serviços através de métricas e capacidades de aplicação.
      - Ou dizer que forneceram informações que indicassem que o número de contactos de que precisavam era "Pequeno" - eles receberiam apenas algumas divisórias, ou poderiam mesmo ser colocados numa piscina de serviço partilhada com outros clientes.
  - Não estás a executar um monte de casos de serviço ou réplicas enquanto esperas que os clientes apareçam.
  - Se um cliente alguma vez sair, então retirar as suas informações do seu serviço é tão simples como fazer com que o gestor apague esse serviço ou aplicação que criou.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os conceitos de Tecido de Serviço, consulte os seguintes artigos:

* [Disponibilidade de serviços de Tecido de Serviço](service-fabric-availability-services.md)
* [Serviço de Partição Serviço De Tecido](service-fabric-concepts-partitioning.md)
