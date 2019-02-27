---
title: Descrição do Cluster do Resource Manager de cluster | Documentos da Microsoft
description: Descrever um cluster do Service Fabric ao especificar domínios de falha, os domínios de atualização, propriedades de nó e as capacidades de nós para o Gestor de recursos do Cluster.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 1020e18894f4bb307ad14f780e76eab1df1314bb
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56875978"
---
# <a name="describing-a-service-fabric-cluster"></a>Descrever um cluster do service fabric
O Gestor de recursos de Cluster do Service Fabric fornece vários mecanismos para descrever um cluster. Durante o tempo de execução, o Gestor de recursos de Cluster utiliza estas informações para garantir a elevada disponibilidade dos serviços em execução no cluster. Ao impor estas regras importantes, ele também tenta otimizar o consumo de recursos dentro do cluster.

## <a name="key-concepts"></a>Conceitos-chave
O Gestor de recursos de Cluster suporta vários recursos que descrevem um cluster:

* Domínios de falha
* Domínios de atualização
* Propriedades de nó
* Capacidades de nós

## <a name="fault-domains"></a>Domínios de falha
Um domínio de falha é qualquer área de falha de coordenada. Uma única máquina é um domínio de falha (uma vez que ele poderá falhar na própria, por várias razões, de fonte de alimentação falhas para falhas de unidade ao firmware do NIC ruim). As máquinas ligadas ao mesmo comutador Ethernet estão no mesmo domínio de falha, como máquinas numa única fonte de energia ou numa única localização de partilha. Uma vez que é natural para falhas de hardware para se sobrepõem, domínios de falha são inerentemente hierárquica e são representados como URIs no Service Fabric.

É importante que domínios de falha estão configurados corretamente, uma vez que o Service Fabric utiliza estas informações para colocar em segurança a serviços. Service Fabric não quer colocar os serviços, de modo a que a perda de um domínio de falha (causada pela falha de algum componente) faz com que um serviço descer. No Azure o ambiente do Service Fabric utiliza as informações de domínio de falha fornecidas pelo ambiente para configurar corretamente os nós do cluster em seu nome. Autónomo do Service Fabric, os domínios de falha são definidos no momento em que o cluster está configurado 

> [!WARNING]
> É importante que as informações de domínio de falha fornecidas para o Service Fabric estão correta. Por exemplo, digamos que nós de cluster do Service Fabric estão em execução dentro de 10 máquinas virtuais em execução em cinco anfitriões físicos. Neste caso, muito embora haja 10 máquinas virtuais, existem apenas 5 diferentes (nível superior) domínios de falha. Partilharem o mesmo anfitrião físico, faz com que as VMs partilhar o mesmo domínio de falha de raiz, uma vez que as VMs ocorrer falha coordenada, se falhar o respetivo anfitrião físico.  
>
> Service Fabric espera que o domínio de falhas de nó não para alterar. Outros mecanismos de garantir a elevada disponibilidade das VMs, como [HA-VMs](https://technet.microsoft.com/library/cc967323.aspx) pode causar conflitos com o Service Fabric, que usam uma migração transparente de VMs a partir de um anfitrião para outro. Estes mecanismos não reconfigurar ou notificar o código em execução dentro da VM. Como tal, estão **nepodporuje** como ambientes para executar o Service Fabric clusters. Recursos de infraestrutura do serviço devem ser a tecnologia de apenas de elevada disponibilidade empregada. Mecanismos, como migração em direto da VM, SANs, ou outras pessoas não são necessárias. Se utilizado em conjunto com o Service Fabric, esses mecanismos _reduzir_ de disponibilidade de aplicações e a confiabilidade porque introduzem complexidade adicional, adicionar fontes centralizadas de falha e utilizar a confiabilidade e estratégias de disponibilidade que entram em conflito com as pessoas de Service Fabric. 
>
>

No gráfico abaixo, todas as entidades que contribuem para domínios de falha e listam todos os diferentes domínios de falha que resultam de cores. Neste exemplo, temos de centros de dados ("DC"), racks ("R") e os painéis ("B"). Em termos conceituais, se cada painel contém mais do que uma máquina virtual, pode haver outra camada na hierarquia de domínio de falha.

<center>
![Nós organizados por meio de domínios de falha][Image1]
</center>

Durante o tempo de execução, o Gestor de recursos de Cluster do Service Fabric considera os domínios de falha no cluster e planos de layouts. As réplicas com monitoração de estado ou instâncias sem monitoração de estado para um determinado serviço são distribuídas para que eles estão em domínios de falha separados. Distribui o serviço em domínios de falha, garante que a disponibilidade do serviço não sejam comprometida quando a falha de um domínio de falha em qualquer nível da hierarquia.

Gestor de recursos de Cluster do Service Fabric não importa quantas camadas existem na hierarquia de domínio de falha. No entanto, ele tenta Certifique-se de que a perda de qualquer um parte da hierarquia não tem impacto na serviços em execução no mesmo. 

É melhor se existir o mesmo número de nós em cada nível de profundidade na hierarquia de domínio de falha. Se o "árvore" de domínios de falha está em falta no seu cluster, é mais difícil para o Gestor de recursos do Cluster para descobrir a melhor alocação dos serviços. Layouts de domínios de falha desequilibrados significam que a perda de algum impacto de domínios a disponibilidade dos serviços mais do que outros domínios. Como resultado, o Gestor de recursos do Cluster é interrompido entre dois objetivos: Ele deseja usar as máquinas no domínio "pesado", ao disponibilizar serviços nos mesmos, e quer colocar os serviços em outros domínios para que a perda de um domínio não causa problemas. 

O que o aspeto de domínios desequilibrados? O diagrama abaixo, vamos mostrar duas layouts de cluster diferente. No primeiro exemplo, os nós são distribuídos uniformemente por domínios de falha. No segundo exemplo, um domínio de falha tem muitos mais nós que os outros domínios de falha. 

<center>
![Dois layouts de cluster diferente][Image2]
</center>

No Azure, a escolha de que o domínio de falha contém um nó é gerida por si. No entanto, dependendo do número de nós que Aprovisiona ainda pode acabar com domínios de falha com mais nós nos mesmos do que outras pessoas. Por exemplo, digamos que tem cinco domínios de falha no cluster, mas Aprovisiona sete nós para um determinado NodeType. Neste caso, os primeiros dois domínios de falha acabar com mais nós. Se continuar a implementar mais NodeTypes com apenas algumas instâncias, o problema fica ainda pior. Por esse motivo, que recomenda-se que o número de nós em cada nó de tipo é um múltiplo do número de domínios de falha.

## <a name="upgrade-domains"></a>Domínios de atualização
Domínios de atualização são outro recurso que ajuda o Gestor de recursos do Cluster do Service Fabric entender o layout do cluster. Domínios de atualização definem conjuntos de nós que são atualizadas ao mesmo tempo. Domínios de atualização ajudam o Gestor de recursos de Cluster, compreender e orquestrar operações de gestão, como as atualizações.

Domínios de atualização são muito como domínios de falha, mas com algumas diferenças fundamentais. Em primeiro lugar, áreas de falhas de hardware coordenada definem domínios de falha. Domínios de atualização, por outro lado, são definidos pela política. Tem de decidir quantos desejar, em vez de-lo a ser ditado pelo ambiente. Poderia ter tantos domínios de atualização de nós. Outra diferença entre domínios de falha e domínios de atualização é que os domínios de atualização não são hierárquicos. Em vez disso, eles são mais parecido com uma marca simple. 

O diagrama seguinte mostra que três domínios de atualização estão repartidos em três domínios de falha. Ela também mostra uma possível colocação para três réplicas diferentes de um serviço com estado, onde cada acaba em diferentes domínios de atualização e de falha. Esse posicionamento permite que a perda de um domínio de falha no meio de uma atualização de serviço e ainda terá uma cópia do código e dados.  

<center>
![Colocação com falhas e domínios de atualização][Image3]
</center>

Há prós e contras em ter um grande número de domínios de atualização. Os domínios de atualização mais significa que cada passo da atualização é mais granular e, por conseguinte, afeta um número menor de nós ou serviços. Como resultado, menos os serviços têm de mover cada vez, apresentando o menor volume de alterações no sistema. Isso tende a melhorar a fiabilidade, uma vez que o menor do serviço é afetado por qualquer problema que introduziu durante a atualização. Os domínios de atualização mais também significa que terá de memória intermédia menos disponível nos outros nós para lidar com o impacto da atualização. Por exemplo, se tiver cinco domínios de atualização, os nós em cada estiver lidando com aproximadamente 20% do tráfego. Se precisar de desativar o domínio de atualização para uma atualização, essa carga em geral precisa ir a alguma lugar. Uma vez que tem quatro restantes os domínios de atualização, cada um tem de ter espaço para cerca de 5% do tráfego total. Os domínios de atualização mais significa que terá menos memória intermédia em nós do cluster. Por exemplo, considere se tiver 10 os domínios de atualização em vez disso. Nesse caso, cada UD apenas manipula cerca de 10% do tráfego total. Quando uma atualização passos através do cluster, cada domínio apenas precisaria ter espaço para cerca de 1.1% do tráfego total. Os domínios de atualização mais geralmente permitem-lhe executar os nós numa utilização mais elevada, uma vez que precisar de capacidade menos reservada. O mesmo é verdadeiro para domínios de falha.  

A desvantagem de ter vários domínios de atualização é que as atualizações tendem a demorar mais tempo. Service Fabric aguarda um curto período de tempo depois de um domínio de atualização concluído e realiza verificações antes de iniciar a atualização seguinte. Esses atrasos ativar detectando problemas introduzidos pela atualização antes de continua a atualização. O compromisso é aceitável, pois evita que alterações ruins afetem muito do serviço de cada vez.

Muito poucos os domínios de atualização tem os efeitos negativos de lado muitos – apesar de cada domínio de atualização individual está inativo e a ser atualizado uma grande parte da sua capacidade geral não está disponível. Por exemplo, se tiver apenas três domínios de atualização que está a efetuar para baixo de cerca de 1/3 do seu serviço geral ou a capacidade do cluster ao mesmo tempo. Ter grande parte do seu serviço para baixo ao mesmo tempo não é desejável, já que precisa ter capacidade suficiente no resto do seu cluster para processar a carga de trabalho. Manutenção de buffer significa que durante o funcionamento normal esses nós são menos carregado do que seria, caso contrário. Isso aumenta o custo de executar seu serviço.

Não existe nenhum limite real para o número total de falhas ou domínios de atualização num ambiente ou restrições sobre como que se sobreponham. Dito isso, há vários padrões comuns:

- Domínios de falha e domínios de atualização mapeado 1:1
- Um domínio de atualização de mensagens em fila por nó (instância de sistema operacional física ou virtual)
- Um modelo de "repartido" ou "matrix" em que os domínios de falha e domínios de atualização formam uma matriz com máquinas normalmente em execução para baixo as diagonals

<center>
![Layouts de domínio de atualização e de falha][Image4]
</center>

Não existe que nenhum melhor responder que esquema escolha, cada um tem alguns prós e contras. Por exemplo, o modelo de 1FD:1UD é fácil de configurar. O 1 o domínio de atualização por modelo de nó é mais como o que as pessoas são utilizadas para. Durante as atualizações de cada nó é atualizada de forma independente. Isso é semelhante a como pequenos conjuntos de máquinas foram atualizados manualmente no passado. 

O modelo mais comuns é a matriz de FD/UD, onde o FDs e UDs formam uma tabela e nós são colocados iniciar ao longo do diagonal. Esse é o modelo utilizado por predefinição em clusters do Service Fabric no Azure. Para clusters com vários nós, tudo o que acaba se parecendo com o padrão de matriz densa acima.

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Restrições de domínio de atualização e de falha e o comportamento resultante
### <a name="default-approach"></a>*Abordagem de predefinição*
Por predefinição, o Gestor de recursos do Cluster mantém Balanceados entre domínios de atualização e de falha de serviços. Isso é modelado como um [restrição](service-fabric-cluster-resource-manager-management-integration.md). Os Estados de restrição de falhas e de domínio de atualização: "Para uma partição de determinado serviço há nunca deve ser uma diferença maior que um no número de objetos do serviço (instâncias de serviço sem estado ou réplicas de serviço com estado) entre quaisquer dois domínios no mesmo nível da hierarquia de". Digamos que esta restrição fornece uma garantia de "máximo diferença". A restrição de domínio de atualização e de falha impede que certos movimentos ou esquemas que violam a regra indicada acima. 

Vamos examinar um exemplo. Vamos supor que temos um cluster connosco seis, configurada com cinco domínios de falha e cinco domínios de atualização.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

*Configuração 1*

Agora vamos supor que a criação de um serviço com um TargetReplicaSetSize (ou, para um serviço sem estado um InstanceCount) de cinco. N1 N5 direcionado para as réplicas. Na verdade, N6 nunca é usada, independentemente de quantos serviços assim que cria. Mas por quê? Vamos ver a diferença entre o layout atual e o que aconteceria se N6 for escolhida.

Este é o número total de réplicas por domínio de atualização e de falha e o esquema, temos:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Esquema de 1*


Este esquema é balanceada em termos de nós por domínio de falha e o domínio de atualização. Ele também é balanceado em termos de número de réplicas por domínio de atualização e de falha. Cada domínio tem o mesmo número de nós e o mesmo número de réplicas.

Agora, vejamos o que aconteceria se tivéssemos usado N6 em vez de N2. Como seriam as réplicas distribuídas, em seguida?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

*Esquema de 2*


Este esquema viola a nossa definição da garantia de "máximo diferença" para a restrição de domínio de falha. FD0 tem duas réplicas, enquanto FD1 tem zero, fazer a diferença entre FD0 e FD1 um total de dois, que é maior do que a diferença de máxima de um. Uma vez que a restrição seja violada, o Gestor de recursos de Cluster não permite esse esquema. Da mesma forma se escolhemos N2 e N6 (em vez de N1 e N2), teríamos:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Esquema de 3*


Este esquema é balanceada em termos de domínios de falha. No entanto, agora ele está violação a restrição de domínio de atualização porque UD0 tem zero réplicas enquanto UD1 tem dois. Por conseguinte, este esquema também é inválida e não ser escolhida pelo Gestor de recursos de Cluster.

Essa abordagem para a distribuição de réplicas com monitoração de estado ou instâncias sem monitoração de estado fornece a tolerância de falhas possível melhor. Numa situação quando um domínio fica inativo, o número mínimo de réplicas/instâncias é perdido. 

Por outro lado, essa abordagem pode ser demasiado restritivo e permite ao cluster utilizar todos os recursos. Para determinadas configurações de cluster, não não possível utilizar alguns nós. Isso pode levar a Service Fabric não colocar os seus serviços, resultando em mensagens de aviso. No exemplo anterior alguns de nós do cluster não podem ser utilizado (N6 no determinado exemplo). Mesmo que adicionaria nós para esse cluster (N7 – N10), instâncias/réplicas só são colocadas na N1 – N5 devido a restrições de domínio de atualização e de falha. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |

*Configuração 2*


### <a name="alternative-approach"></a>*Abordagem alternativa*

O Gestor de recursos de Cluster suporta a outra versão da restrição e atualizar o domínio de falha que permite o posicionamento ao mesmo tempo garantindo ainda um nível mínimo de segurança. A restrição de domínio de atualização e de falha alternativa pode ser declarada da seguinte maneira: "Para uma partição de determinado serviço, distribuição de réplica em vários domínios deve garantir que a partição não sofrem uma perda de quórum". Digamos que esta restrição fornece uma garantia de "quórum seguro". 

> [!NOTE]
>Para um serviço com estado, definimos *perda de quórum* numa situação quando a maioria das réplicas de partição estão indisponíveis, ao mesmo tempo. Por exemplo, se TargetReplicaSetSize é cinco, um conjunto de qualquer três réplicas representa quórum. Da mesma forma, se TargetReplicaSetSize 6, quatro réplicas são necessárias para o quórum. Em ambos os casos mais do que duas réplicas podem ficar indisponíveis ao mesmo tempo se a partição que deseja continuar a funcionar normalmente. Para um serviço sem estado, não há nenhum algo como *perda de quórum* como serviços sem estado continuam a funcionar, normalmente, mesmo que a maioria das instâncias ficam inativos, ao mesmo tempo. Portanto, nosso foco será nos serviços com estado no resto do texto.
>

Vamos voltar para o exemplo anterior. Com a versão de "quórum seguro" da restrição, todos os três layouts de determinado seria válidos. Isto acontece porque, mesmo que houvesse falha de FD0 na segunda layout ou UD1 no layout da terceira, a partição ainda teria quórum (a maioria das suas réplicas ainda seria cópia de segurança). Com esta versão da restrição N6 quase sempre pode ser utilizado.

A abordagem de "quórum seguro" fornece mais flexibilidade do que a abordagem de "máximo diferença", pois é mais fácil encontrar distribuições de réplica que são válidas em quase qualquer topologia do cluster. No entanto, essa abordagem não pode garantir a falha melhores características de tolerância porque algumas falhas são pior do que outras pessoas. Na pior das hipóteses, a maioria das réplicas pode ser perdida com a falha de um domínio e uma réplica adicional. Por exemplo, em vez de 3 falhas necessárias perder o quórum com 5 réplicas ou instâncias, pode agora perder uma maioria com apenas duas falhas. 

### <a name="adaptive-approach"></a>*Abordagem adaptável*
Uma vez que ambas as abordagens têm vantagens e desvantagens, introduzimos uma abordagem adaptável, que combina essas duas estratégias.

> [!NOTE]
>Este será o comportamento padrão a partir do Service Fabric versão 6.2. 
>
A abordagem adaptável usa a lógica de "máximo diferença" por predefinição e muda para a lógica de "quórum seguro" apenas quando necessário. O Gestor de recursos de Cluster detecta automaticamente qual estratégia é necessária examinar como é que o cluster e os serviços são configurados. Para um determinado serviço: *Se o TargetReplicaSetSize for uniformemente divisível pelo número de domínios de falha e o número de domínios de atualização **e** o número de nós é menor ou igual a (número de domínios de falhas) * (de acordo com o número de domínios de atualização), o Cluster Gestor de recursos deve utilizar a lógica de "quórum com base" para esse serviço.* Lembre-se de que o Gestor de recursos do Cluster irá utilizar esta abordagem para serviços com e sem monitoração de estado, apesar de não ser relevante para serviços sem estado de perda de quórum.

Voltemos ao exemplo anterior e partem do princípio de que um cluster tem agora 8 nós (o cluster ainda está configurado com cinco domínios de falha e cinco domínios de atualização e TargetReplicaSetSize de um serviço hospedado em que permanece de cluster de cinco). 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

*Configuração 3*

Uma vez que todas as condições necessárias são cumpridas, Gestor de recursos de Cluster utilizará a lógica de "o quórum com base em" o serviço de distribuição. Isto permite a utilização de N6 – N8. Uma distribuição de serviço possíveis em vez disso pode ser parecida com:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

*Esquema de 4*

Se TargetReplicaSetSize do seu serviço é reduzida para quatro (por exemplo), o Gestor de recursos de Cluster observe que essa alteração e retomar usando a lógica de "máximo diferença", porque TargetReplicaSetSize já não está dividable pelo número de domínios de falha e domínios de atualização. Como resultado, determinados movimentos de réplica ocorrerá para distribuir restantes quatro réplicas em nós N1 N5, para que a versão de "máximo diferença" a lógica de domínio do domínio de falha e a atualização não é violada. 

Voltando para o layout do quarto e o TargetReplicaSetSize de cinco. Se N1 é removida do cluster, o número de domínios de atualização torna-se igual para quatro. Novamente, o Gestor de recursos do Cluster é iniciado com a lógica de "máximo diferença" como o número de domínios de atualização não divide de maneira uniforme TargetReplicaSetSize o serviço mais. Como resultado, réplica R1, quando criada novamente, tem de parar em N4 para que a restrição de domínio de atualização e de falha não é violada.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |N/A |N/D |N/D |N/D |N/D |N/A |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Esquema de 5*

## <a name="configuring-fault-and-upgrade-domains"></a>Configurar domínios de atualização e de falha
Definir domínios de falha e domínios de atualização é feita automaticamente em implementações do Service Fabric alojados no Azure. Service Fabric seleciona e utiliza as informações do ambiente do Azure.

Se estiver a criar seu próprio cluster (ou pretende executar uma topologia específica no desenvolvimento), pode fornecer as informações de domínio de falha e o domínio de atualização. Neste exemplo, definimos um cluster de desenvolvimento local de nove nós que abrange três "centros de dados" (cada um com três racks). Este cluster tem também três domínios de atualização repartidos por essas três datacenters. É um exemplo da configuração abaixo: 

ClusterManifest.xml

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one-box /one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```

por meio de ClusterConfig.json das implementações autónomas

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> Ao definir clusters através do Azure Resource Manager, os domínios de falha e domínios de atualização são atribuídos pelo Azure. Por conseguinte, a definição dos seus tipos de nós e conjuntos de dimensionamento de máquinas virtuais no modelo do Azure Resource Manager não inclui informações de domínio de atualização ou de domínio de falha.
>

## <a name="node-properties-and-placement-constraints"></a>Propriedades de nó e restrições de posicionamento
Às vezes (na verdade, na maioria das vezes) vai querer garantir que determinadas cargas de trabalho são executados apenas em determinados tipos de nós do cluster. Por exemplo, pode exigir alguma carga de trabalho GPUs ou SSDs, enquanto outros podem não ter. Um ótimo exemplo de filtragem de hardware para cargas de trabalho específicas é quase todos os arquitetura de n camadas por aí. Determinados computadores servem como o front-end ou API que serve o lado do aplicativo e são expostos a clientes ou à internet. Computadores diferentes, muitas vezes, com recursos de hardware diferente, lidar com o trabalho de camadas de computação ou de armazenamento. Normalmente, são _não_ expostos diretamente a clientes ou à internet. Service Fabric espera que existem casos em que determinadas cargas de trabalho necessário para executar em configurações de hardware específico. Por exemplo:

* uma aplicação de n camadas existente foi "elevadas e desviado" num ambiente do Service Fabric
* uma carga de trabalho quer ser executado em hardware específico de desempenho, dimensionamento ou motivos de isolamento de segurança
* Uma carga de trabalho deve ser isolada de outras cargas de trabalho por motivos de consumo de política ou de recursos

Para oferecer suporte a esses tipos de configurações, o Service Fabric tem uma noção de primeira classe de etiquetas que podem ser aplicados a nós. Essas marcas são chamadas **propriedades de nó**. **Restrições de posicionamento** são as declarações anexadas aos serviços individuais que selecionar para uma ou mais propriedades de nó. Restrições de posicionamento definem em que os serviços devem ser executados. O conjunto de restrições é extensível - qualquer par de chave/valor pode trabalhar. 

<center>
![Layout diferentes cargas de trabalho de cluster][Image5]
</center>

### <a name="built-in-node-properties"></a>Criado nas propriedades de nó
Service Fabric define algumas propriedades de nó predefinidos que podem ser utilizadas automaticamente sem que o utilizador ter de defini-los. As propriedades predefinidas definidas em cada nó são os **NodeType** e o **NodeName**. Por exemplo, pode escrever uma restrição de posicionamento como `"(NodeType == NodeType03)"`. Em geral, achamos NodeType para ser uma das mais comumente usados propriedades. É útil, uma vez que ele corresponde 1:1 com um tipo de uma máquina. Cada tipo de máquina corresponde a um tipo de carga de trabalho num aplicativo de n camadas tradicional.

<center>
![Restrições de posicionamento e propriedades de nó][Image6]
</center>

## <a name="placement-constraint-and-node-property-syntax"></a>Restrição de posicionamento e a sintaxe de propriedade de nó 
O valor especificado na propriedade de nó pode ser uma cadeia, booleano, ou assinado há muito tempo. A instrução no serviço é chamada um posicionamento *restrição* , uma vez que ela restringe onde o serviço pode ser executado no cluster. A restrição pode ser qualquer instrução booleana que opera nas propriedades de nó diferente no cluster. Os seletores válidos nestas instruções booleanos são:

1) verificações condicionais para a criação de instruções específicas

| Declaração | Sintaxe |
| --- |:---:|
| "igual a" | "==" |
| "não igual a" | "!=" |
| "maior que" | ">" |
| "maior que ou igual a" | ">=" |
| "menor que" | "<" |
| "menor que ou igual a" | "<=" |

2) instruções booleanas para operações de agrupamentos e lógicas

| Declaração | Sintaxe |
| --- |:---:|
| "e" | "&&" |
| "ou" | "&#124;&#124;" |
| "não" | "!" |
| "grupo como a única instrução" | "()" |

Aqui estão alguns exemplos de declarações de restrição básica.

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Apenas nós em que a instrução de restrição de posicionamento global avalia como "True" podem ter o serviço colocado na mesma. Nós que não têm uma propriedade definida não coincidem qualquer restrição de posicionamento que contém essa propriedade.

Digamos que as seguintes propriedades de nó foram definidas para um tipo de nó específico:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

por meio de ClusterConfig.json das implementações autónomas ou Template para o Azure alojado clusters. 

> [!NOTE]
> No modelo do Azure Resource Manager o tipo de nó é normalmente parametrizado. Pareceria com "[parameters('vmNodeType1Name')]" em vez de "NodeType01".
>

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

É possível criar o posicionamento do serviço *restrições* para um serviço como da seguinte forma:

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Se todos os nós do NodeType01 são válidos, também pode selecionar esse tipo de nó com a restrição "(NodeType == NodeType01)".

Uma das coisas legais sobre restrições de posicionamento de um serviço é que pode ser atualizados dinamicamente durante o tempo de execução. Então, se for necessário, pode mover um serviço no cluster, adicionar e remover os requisitos, etc. Service Fabric encarrega-se de garantir que o serviço permaneça ativos e disponíveis, mesmo quando esses tipos de alterações são feitos.

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

PowerShell:

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Restrições de posicionamento são especificadas para cada instância de serviço com nome diferente. Atualizações sempre tomar o lugar de (substituir) o que foi anteriormente especificado.

A definição do cluster define as propriedades num nó. Alterar as propriedades de um nó requer uma atualização de configuração de cluster. Atualizar propriedades de um nó requer que cada nó afetado para reiniciar para comunicar as respetivas propriedades de novo. Estas atualizações sem interrupção são geridas pelo Service Fabric.

## <a name="describing-and-managing-cluster-resources"></a>Descrever e gerir recursos de Cluster
Uma das tarefas mais importantes de qualquer orchestrator é ajudar a gerir o consumo de recursos do cluster. Gerir recursos de cluster, pode significar duas coisas diferentes. Em primeiro lugar, aí é garantir que as máquinas não estão sobrecarregadas. Isso significa certificar-se de que as máquinas não estão em execução mais serviços que eles possam tratar. Em segundo lugar, há balanceamento e a otimização de que é crucial para executar os serviços de forma eficiente. Ofertas de serviço confidencial em vigor ou desempenho custos não podem permitir alguns nós ser frequente, enquanto outras estão frios. Nós frequente levam à contenção de recursos e um desempenho fraco e nós frios representam desperdício de recursos e os custos de aumento. 

O Service Fabric representa a recursos como `Metrics`. As métricas são qualquer recurso lógico ou físico que pretende descrever para o Service Fabric. Exemplos de métricas são coisas como "WorkQueueDepth" ou "MemoryInMb". Para obter informações sobre os recursos físicos que podem reger o Service Fabric em nós, consulte [governação de recursos](service-fabric-resource-governance.md). Para obter informações sobre como configurar métricas personalizadas e respetivas utilizações, consulte [neste artigo](service-fabric-cluster-resource-manager-metrics.md)

As métricas são diferentes das restrições de posicionamentos e propriedades de nó. Propriedades de nó são estáticos descritores dos próprios nós. Métricas descrevem os recursos que têm de nós e que serviços consumam quando estas são executadas num nó. Uma propriedade de nó pode ser "HasSSD" e pode ser definida como verdadeira ou falsa. A quantidade de espaço disponível nesse SSD e o quanto é consumida pelos serviços seria uma métrica como "DriveSpaceInMb". 

É importante observar que tal como para restrições de posicionamento e propriedades de nó, o Gestor de recursos de Cluster do Service Fabric não entende o que significa que os nomes das métricas. Nomes de métricas são apenas cadeias de caracteres. É uma boa prática para declarar unidades como parte dos nomes de métrica que criou quando que poderia ser ambígua.

## <a name="capacity"></a>Capacidade
Se desativado todos os recursos *balanceamento*, Gestor de recursos de Cluster do Service Fabric ainda a garantir que nenhum nó acaba sendo acima da sua capacidade. É possível gerir estouros de capacidade, a menos que o cluster é demasiado completo ou a carga de trabalho é maior do que qualquer nó. Capacidade é outra *restrição* que o Gestor de recursos de Cluster utiliza para compreender qual a quantidade de um recurso de um nó tem. Capacidade restante também é controlado para o cluster como um todo. A capacidade e o consumo no nível de serviço são expressos em termos de métricas. Por exemplo, a métrica pode ser "ClientConnections" e um determinado nó pode ter uma capacidade para "ClientConnections" de 32768. Outros nós podem ter outros limites algum serviço em execução em que pode nó Digamos que ele está atualmente a consumir 32256 da métrica "ClientConnections".

Durante o tempo de execução, o Gestor de recursos de Cluster controla a capacidade restante no cluster e em nós. Para poder controlar a capacidade do Gestor de recursos de Cluster subtrai utilização de cada serviço da capacidade do nó onde o serviço é executado. Com essas informações, o Gestor de recursos de Cluster do Service Fabric pode descobrir onde colocar ou mover réplicas para que nós não passam pela capacidade.

<center>
![Nós de cluster e a capacidade][Image7]
</center>

C#:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "ClientConnections";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 0;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

Pode ver as capacidades definidas no manifesto do cluster:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

por meio de ClusterConfig.json das implementações autónomas ou Template para o Azure alojado clusters. 

```json
"nodeTypes": [
    {
        "name": "NodeType03",
        "capacities": {
            "ClientConnections": "65536",
        }
    }
],
```

Normalmente um serviço carregar dinamicamente as alterações. Dizer que a carga de uma réplica de "ClientConnections" mudou de 1024 para 2048, mas o nó que ele foi executado em, em seguida, só tendo de 512 capacidade restante para essa métrica. Agora essa réplica ou de posicionamento da instância é inválido, uma vez que não existe espaço suficiente nesse nó. O Gestor de recursos do Cluster tem de participar e que o nó volta abaixo da capacidade. Ele reduz a carga no nó que está acima da capacidade ao mover um ou mais das réplicas ou instâncias de nesse nó para outros nós. Ao mover as réplicas, o Gestor de recursos de Cluster tenta minimizar o custo desses movimentos. O custo de movimento é discutido [este artigo](service-fabric-cluster-resource-manager-movement-cost.md) e muito mais sobre o Gestor de recursos do Cluster do reequilibrar estratégias e está descrito com regras [aqui](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Capacidade do cluster
Então, como é que o Gestor de recursos de Cluster do Service Fabric manter o cluster geral do que está a ser demasiado completo? Bem, com carga dinâmico, não há muito pode fazer. Serviços podem ter o pico de carga independentemente das ações realizadas pelo Gestor de recursos de Cluster. Como resultado, o cluster com bastante espaço hoje pode ser inferior ao que fique famoso por isso amanhã. Dito isso, há alguns controles que são suportadas para evitar problemas. A primeira coisa que podemos fazer é impedir que a criação de novas cargas de trabalho que faria com que o cluster para ficar cheio.

Digamos que criar um serviço sem estado e ele tem alguns carga associada a ele. Digamos que o serviço se preocupa a métrica "DiskSpaceInMb". Vamos também supor que ele vai para consumir cinco unidades de "DiskSpaceInMb" para cada instância do serviço. Pretende criar três instâncias do serviço. Ótimo! Então, isso significa que precisamos 15 unidades de "DiskSpaceInMb" estar presente no cluster para que nós nem mesmo ser capaz de criar estas instâncias de serviço. O Gestor de recursos de Cluster continuamente calcula a capacidade e o consumo de cada métrica para que ele possa determinar a capacidade restante do cluster. Se não existir espaço suficiente, o Gestor de recursos de Cluster rejeita a chamada de serviço de criar.

Como o requisito é apenas o que há 15 unidades de estar disponível, este espaço poderem ser alocado muitas formas diferentes. Por exemplo, pode haver uma unidade restante da capacidade em 15 nós diferentes, ou três unidades restantes da capacidade em cinco nós diferentes. Se o Gestor de recursos do Cluster pode reorganizar as coisas, portanto, há cinco unidades disponíveis em três nós, ele coloca o serviço. Reorganizar o cluster é possível que, normalmente, a menos que o cluster está quase cheia ou os serviços existentes não podem ser consolidados por algum motivo.

## <a name="buffered-capacity"></a>Capacidade em buffer
A capacidade em buffer é outro recurso do Gestor de recursos do Cluster. Ele permite que a reserva de uma parte da capacidade global do nó. Esse buffer de capacidade só é utilizado para colocar serviços durante as atualizações e falhas de nó. Capacidade em buffer é especificada globalmente por métrica para todos os nós. O valor que escolher para a capacidade de reserva é uma função do número de falhas e domínios de atualização de mensagens em fila ter no cluster. Mais falhas e domínios de atualização significa que pode escolher um número mais baixo para a sua capacidade em buffer. Se tiver mais de domínios, pode esperar quantidades menores do seu cluster estar indisponível durante as atualizações. Especificar a capacidade de colocação em memória intermédia só faz sentido se também tiver especificado a capacidade de nós para uma métrica.

Eis um exemplo de como especificar a capacidade em buffer:

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

por meio de ClusterConfig.json das implementações autónomas ou Template para o Azure alojados clusters:

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      },
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      }
    ]
  }
]
```

A criação de novos serviços falha quando o cluster sem capacidade em buffer para uma métrica. Impedir a criação de novos serviços para preservar a memória intermédia garante que as atualizações e falhas não causam nós fiquem acima da capacidade. Capacidade em buffer é opcional, mas recomenda-se em qualquer cluster que define uma capacidade para uma métrica.

O Gestor de recursos de Cluster mostra essas informações de carga. Para cada uma, estas informações incluem: 
  - as definições de capacidade em buffer
  - a capacidade total
  - o consumo atual
  - Se cada uma é considerada com balanceamento de ou não
  - estatísticas sobre o desvio padrão
  - os nós que têm o maior e menor carga  
  
Abaixo, vamos ver um exemplo dessa saída:

```posh
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2016 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2016 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## <a name="next-steps"></a>Passos Seguintes
* Para obter informações sobre o fluxo de arquitetura e as informações no Gestor de recursos do Cluster, verifique [neste artigo](service-fabric-cluster-resource-manager-architecture.md)
* A definição de métricas de desfragmentação é uma forma de consolidar a carga em nós em vez de dispersão-lo. Para saber como configurar a desfragmentação, consulte [neste artigo](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* Começar do início e [obtenha uma introdução para o Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
* Para obter informações sobre como o Gestor de recursos de Cluster gere e faz o balanceamento de carga no cluster, consulte o artigo no [balanceamento de carga](service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
