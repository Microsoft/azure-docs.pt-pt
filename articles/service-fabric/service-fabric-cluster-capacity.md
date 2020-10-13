---
title: Considerações de planeamento da capacidade do cluster de tecidos de serviço
description: Tipos de nó, durabilidade, fiabilidade e outras coisas a ter em conta ao planear o seu cluster de Tecido de Serviço.
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: pepogors
ms.custom: sfrev
ms.openlocfilehash: d2b303c22eea9fb46a68bb3c8e36991d47d61554
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91817745"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Considerações de planeamento da capacidade do cluster de tecidos de serviço

O planeamento da capacidade do cluster é importante para cada ambiente de produção de Tecidos de Serviço. As principais considerações incluem:

* **Número inicial e propriedades dos tipos de *nó de* cluster**

* ** *Nível* de durabilidade de cada tipo de nó,** que determina os privilégios de tecido de serviço VM dentro da infraestrutura Azure

* ** *Nível* de fiabilidade do cluster,** que determina a estabilidade dos serviços de sistema de tecido de serviço e a função global de cluster

Este artigo irá acompanhá-lo através dos pontos de decisão significativos para cada uma destas áreas.

## <a name="initial-number-and-properties-of-cluster-node-types"></a>Número inicial e propriedades dos tipos de nó de cluster

Um *tipo de nó* define o tamanho, número e propriedades para um conjunto de nós (máquinas virtuais) no cluster. Cada tipo de nó que é definido num conjunto de clusters de tecido de serviço para um [conjunto de escala de máquina virtual](../virtual-machine-scale-sets/overview.md).

Como cada tipo de nó é um conjunto de escala distinta, pode ser dimensionado para cima ou para baixo independentemente, ter diferentes conjuntos de portas abertas, e ter métricas de capacidade diferentes. Para obter mais informações sobre a relação entre os tipos de nós e os conjuntos de balanças de máquinas virtuais, consulte [os tipos de nó de aglomerado de tecido de serviço](service-fabric-cluster-nodetypes.md).

Cada cluster requer um **tipo de nó primário,** que executa serviços críticos de sistema que fornecem capacidades de plataforma de Tecido de Serviço. Embora seja possível também utilizar os tipos de nó primários para executar as suas aplicações, é recomendado dedicar-lhes apenas a executar serviços do sistema.

**Os tipos de nó não primários** podem ser usados para definir funções de aplicação (como serviços *frontais* e *back-end)* e para isolar fisicamente os serviços dentro de um cluster. Os aglomerados de tecido de serviço podem ter zero ou mais tipos de nó não primários.

O tipo de nó primário é configurado utilizando o `isPrimary` atributo sob a definição do tipo nó no modelo de implementação do Gestor de Recursos Azure. Consulte o [objeto NodeTypeDescription](/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object) para obter a lista completa das propriedades do tipo nó. Por exemplo, use, abra qualquer *AzureDeploy.jsem* ficheiros em [amostras de cluster de Tecido de Serviço](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/) e encontre na *página* a procura do `nodeTypes` objeto.

### <a name="node-type-planning-considerations"></a>Considerações de planeamento do tipo nó

O número de tipos de nós iniciais depende da finalidade do seu agrupamento e das aplicações e serviços que o executam. Considere as perguntas seguintes:

* ***A sua aplicação tem vários serviços, e algum deles precisa de ser público ou com a internet?***

    As aplicações típicas contêm um serviço de gateway frontal que recebe entrada de um cliente, e um ou mais serviços de back-end que comunicam com os serviços front-end, com ligação em rede separada entre os serviços front-end e back-end. Estes casos normalmente requerem três tipos de nó: um tipo de nó primário e dois tipos de nó não primário (um para o serviço frontal e traseiro).

* ***Os serviços que compõem a sua aplicação têm diferentes necessidades de infraestrutura, como maiores ciclos de RAM ou CPU mais elevados?***

    Muitas vezes, o serviço frontal pode funcionar em VMs menores (tamanhos VM como D2) que têm portas abertas para a internet.  Os serviços de back-end computacionalmente intensivos podem ter de funcionar em VMs maiores (com tamanhos VM como D4, D6, D15) que não são virados para a Internet. A definição de diferentes tipos de nós para estes serviços permite-lhe fazer uma utilização mais eficiente e segura dos VMs de tecido de serviço subjacentes, e permite-lhes dimensioná-los de forma independente. Para obter mais informações sobre a quantidade de recursos que precisará, consulte [o planeamento da capacidade para aplicações de Tecidos de Serviço](service-fabric-capacity-planning.md)

* ***Algum dos seus serviços de candidatura terá de ultrapassar os 100 nós?***

    Um único tipo de nó não pode escalar de forma fiável para além de 100 nós por escala de máquina virtual definida para aplicações de Tecido de Serviço. A execução de mais de 100 nós requer conjuntos de escala de máquinas virtuais adicionais (e, portanto, tipos adicionais de nós).

* ***O seu cluster vai percorrer as Zonas de Disponibilidade?***

    O Service Fabric suporta clusters que se estendem por [zonas de disponibilidade,](../availability-zones/az-overview.md) implantando tipos de nós que estão presos a zonas específicas, garantindo uma elevada disponibilidade das suas aplicações. As Zonas de Disponibilidade requerem planeamento adicional do tipo de nó e requisitos mínimos. Para mais detalhes, consulte [topologia recomendada para o tipo de nó primário de conjuntos de tecido de serviço que se estendem por zonas de disponibilidade.](service-fabric-cross-availability-zones.md#recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones) 

Ao determinar o número e as propriedades dos tipos de nós para a criação inicial do seu cluster, tenha em mente que pode sempre adicionar, modificar ou remover tipos de nó (não primários) uma vez que o seu cluster é implantado. [Os tipos de nó primários também podem ser modificados](service-fabric-scale-up-primary-node-type.md) em clusters de execução (embora tais operações exijam muito planeamento e cautela em ambientes de produção).

Uma outra consideração para as propriedades do tipo nó é o nível de durabilidade, que determina privilégios que os VMs de um tipo de nó têm dentro da infraestrutura Azure. Utilize o tamanho dos VMs que escolhe para o seu cluster e a contagem de exemplos que atribui para os tipos individuais de nó para ajudar a determinar o nível de durabilidade adequado para cada um dos seus tipos de nós, conforme descrito a seguir.

## <a name="durability-characteristics-of-the-cluster"></a>Características de durabilidade do cluster

O *nível de durabilidade* designa os privilégios que os seus VMs de tecido de serviço têm com a infraestrutura Azure subjacente. Este privilégio permite que o Service Fabric faça uma pausa em qualquer pedido de infraestrutura de nível VM (como reiniciar, reimagem ou migração) que impacte os requisitos do quórum para os serviços de sistema service fabric e seus serviços estatais.

> [!IMPORTANT]
> O nível de durabilidade é definido por tipo de nó. Se não houver nenhuma especificada, o nível *de bronze* será usado, no entanto não fornece upgrades automáticos de SO. *A* durabilidade prateada ou *dourada* é recomendada para cargas de trabalho de produção.

A tabela abaixo lista os níveis de durabilidade do Tecido de Serviço, os seus requisitos e acessibilidades.

| Nível de durabilidade  | Número mínimo exigido de VMs | Tamanhos VM suportados                                                                  | Atualizações que faz para o seu conjunto de escala de máquina virtual                               | Atualizações e manutenção iniciadas pela Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Gold             | 5                              | Tamanhos de nó completo dedicados a um único cliente (por exemplo, L32s, GS5, G5, DS15_v2, D15_v2) | Pode ser adiado até ser aprovado pelo cluster de Tecido de Serviço | Pode ser pausado por 2 horas por domínio de upgrade para permitir que as réplicas recuperem de falhas anteriores |
| Silver           | 5                              | VMs de núcleo único ou superior com pelo menos 50 GB de SSD local                      | Pode ser adiado até ser aprovado pelo cluster de Tecido de Serviço | Não pode ser adiado por um período significativo de tempo                                                    |
| Bronze          | 1                              | VMs com pelo menos 50 GB de SSD local                                              | Não será adiado pelo cluster de tecido de serviço           | Não pode ser adiado por um período significativo de tempo                                                    |

> [!WARNING]
> Com a durabilidade do Bronze, a atualização automática de imagem de SO não está disponível. Embora [a Aplicação de Orquestração patch](service-fabric-patch-orchestration-application.md) (destinada apenas a clusters hospedados não-Azure) não seja *recomendada* para níveis de prata ou maior durabilidade, é a sua única opção para automatizar atualizações do Windows no que diz respeito aos domínios de upgrade do Service Fabric.

> [!IMPORTANT]
> Independentemente do nível de durabilidade, executar uma operação [Deallocation](/rest/api/compute/virtualmachinescalesets/deallocate) num conjunto de escala de máquina virtual destruirá o cluster.

### <a name="bronze"></a>Bronze

Os tipos de nó que correm com durabilidade de Bronze não obtêm privilégios. Isto significa que os trabalhos de infraestrutura que impactam as suas cargas de trabalho não serão interrompidos ou atrasados. Utilize durabilidade de Bronze para tipos de nó que só funcionam cargas apátridas. Para cargas de trabalho de produção, recomenda-se a execução de Prata ou superior.

### <a name="silver-and-gold"></a>Prata e Ouro

Use a durabilidade prateada ou dourada para todos os tipos de nó que acolhem serviços estatais que espera escalar frequentemente, e onde deseja que as operações de implantação sejam atrasadas e a capacidade seja reduzida em favor da simplificação do processo. Os cenários de escala não devem afetar a sua escolha do nível de durabilidade.

#### <a name="advantages"></a>Vantagens

* Reduz o número de passos necessários para operações de dimensionamento (desativação do nó e Remove-ServiceFabricNodeState são chamados automaticamente).
* Reduz o risco de perda de dados devido às operações de alteração do tamanho de VM no local e às operações de infraestrutura Azure.

#### <a name="disadvantages"></a>Desvantagens

* As implementações em conjuntos de escala de máquinas virtuais e outros recursos Azure relacionados podem ser retardados, ser retardados ou ser bloqueados inteiramente por problemas no seu cluster ou ao nível da infraestrutura.
* Aumenta o número de [eventos de ciclo de vida réplica](service-fabric-reliable-services-lifecycle.md) (por exemplo, swaps primários) devido a desativações automatizadas de nó durante as operações de infraestrutura do Azure.
* Retira os nós de serviço por períodos de tempo enquanto ocorrem atualizações de software da plataforma Azure ou atividades de manutenção de hardware. Pode ver nós com estatuto Desativado/Deficiente durante estas atividades. Isto reduz temporariamente a capacidade do seu cluster, mas não deve afetar a disponibilidade do seu cluster ou aplicações.

#### <a name="best-practices-for-silver-and-gold-durability-node-types"></a>Melhores práticas para os tipos de nó de durabilidade prata e ouro

Siga estas recomendações para a gestão de tipos de nó com durabilidade prateada ou dourada:

* Mantenha sempre o seu cluster e aplicações saudáveis, e certifique-se de que as aplicações respondem a todos os [eventos de ciclo de vida de réplica de serviço](service-fabric-reliable-services-lifecycle.md) (como réplica em construção está preso) em tempo útil.
* Adotar formas mais seguras de alterar o tamanho de um VM (escala para cima/para baixo). Mudar o tamanho VM de um conjunto de escala de máquina virtual requer um planeamento cuidadoso e cuidado. Para mais detalhes, consulte [Scale up a Service Fabric nódoa](service-fabric-scale-up-primary-node-type.md)
* Mantenha uma contagem mínima de cinco nós para qualquer conjunto de balança de máquina virtual que tenha um nível de durabilidade de Ouro ou Prata ativado. O seu cluster entrará em estado de erro se escalar abaixo deste limiar, e terá de limpar manualmente o estado `Remove-ServiceFabricNodeState` () para os nós removidos.
* Cada escala de máquina virtual definida com nível de durabilidade Prata ou Ouro deve mapear para o seu próprio tipo de nó no cluster de Tecido de Serviço. O mapeamento de vários conjuntos de escala de máquina virtual para um único tipo de nó impedirá que a coordenação entre o cluster de Tecido de Serviço e a infraestrutura Azure funcione corretamente.
* Não elimine instâncias VM aleatórias, utilize sempre a escala de escala de máquina virtual em características. A supressão de instâncias VM aleatórias tem o potencial de criar desequilíbrios no caso VM espalhados por [domínios de atualização](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) e [domínios de avarias](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains). Este desequilíbrio pode afetar negativamente a capacidade dos sistemas de carregar corretamente o equilíbrio entre as instâncias de serviço/réplicas de serviço.
* Se utilizar a Autoscale, estabeleça as regras de modo a que as operações (remoção de instâncias VM) sejam feitas apenas um nó de cada vez. Reduzir mais de um caso de cada vez não é seguro.
* Se eliminar ou translocar VMs no tipo de nó primário, nunca reduza a contagem de VMs atribuídos abaixo do que o nível de fiabilidade requer. Estas operações serão bloqueadas indefinidamente numa escala definida com um nível de durabilidade de Prata ou Ouro.

### <a name="changing-durability-levels"></a>Alteração dos níveis de durabilidade

Dentro de determinadas restrições, o nível de durabilidade do tipo nó pode ser ajustado:

* Os tipos de nó com níveis de durabilidade de Prata ou Ouro não podem ser desclassificados para Bronze.
* A modernização de Bronze a Prata ou Ouro pode demorar algumas horas.
* Ao alterar o nível de durabilidade, certifique-se de atualizá-lo tanto na configuração de extensão do Tecido de Serviço no seu recurso de definição de escala de máquina virtual como na definição do tipo nó no seu recurso de cluster de Tecido de Serviço. Estes valores devem coincidir.

Outra consideração quando o planeamento de capacidade é o nível de fiabilidade para o seu cluster, que determina a estabilidade dos serviços do sistema e o seu cluster global, como descrito na secção seguinte.

## <a name="reliability-characteristics-of-the-cluster"></a>Características de fiabilidade do cluster

O *nível de fiabilidade* do cluster determina o número de réplicas de serviços de sistema que estão a funcionar no tipo de nó primário do cluster. Quanto mais réplicas, mais fiáveis são os serviços de sistema (e, portanto, o cluster como um todo).

> [!IMPORTANT]
> O nível de fiabilidade é definido ao nível do cluster e determina o número mínimo de nós do tipo de nó primário. As cargas de trabalho de produção requerem um nível de fiabilidade de Prata (maior ou igual a cinco nós) ou superior.  

O nível de fiabilidade pode assumir os seguintes valores:

* **Platinum** - Serviços de sistema executados com réplica de meta contagem de nove
* **Ouro** - Serviços de sistema executados com réplica de sete alvos
* **Prata** - Serviços de sistema executados com réplica de cinco alvos
* **Bronze** - Serviços de sistema executados com réplica de réplica de três

Aqui está a recomendação sobre a escolha do nível de fiabilidade. O número de nós de sementes é também definido para o número mínimo de nós para um nível de fiabilidade.


| **Número de nós** | **Nível de fiabilidade** |
| --- | --- |
| 1 | *Não especifique o `reliabilityLevel` parâmetro: o sistema calcula-o.* |
| 3 | Bronze |
| 5 ou 6| Silver |
| 7 ou 8 | Gold |
| 9 e mais | Platina |

Quando aumentar ou diminuir o tamanho do seu cluster (a soma de instâncias VM em todos os tipos de nó), considere atualizar a fiabilidade do seu cluster de um nível para outro. Ao fazê-lo, desencadeia-se as atualizações de cluster necessárias para alterar a contagem de conjuntos de réplicas de serviços do sistema. Aguarde que a atualização em curso esteja concluída antes de escla lor o cluster, como adicionar nós.  Pode monitorizar o progresso da atualização no Service Fabric Explorer ou executando [o Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="capacity-planning-for-reliability"></a>Planeamento de capacidade para a fiabilidade

As necessidades de capacidade do seu cluster serão determinadas pelos seus requisitos específicos de carga de trabalho e fiabilidade. Esta secção fornece orientações gerais para ajudá-lo a começar com o planeamento de capacidades.

#### <a name="virtual-machine-sizing"></a>Tamanho de máquina virtual

**Para cargas de trabalho de produção, o tamanho VM recomendado (SKU) é [Standard D2_V2](../virtual-machines/dv2-dsv2-series.md) (ou equivalente) com um mínimo de 50 GB de SSD local, 2 núcleos e 4 GiB de memória.** Recomenda-se um mínimo de 50 GB de SSD local, no entanto algumas cargas de trabalho (como as que executam contentores windows) exigirão discos maiores. Ao escolher [outros tamanhos de VM](../virtual-machines/sizes-general.md) para cargas de trabalho de produção, tenha em mente os seguintes constrangimentos:

- Os tamanhos de VM do núcleo parcial como o Standard A0 não são suportados.
- *Série A* Os tamanhos VM não são suportados por razões de desempenho.
- Os VM de baixa prioridade não são suportados.

#### <a name="primary-node-type"></a>Tipo de nó primário

**As cargas de trabalho** de produção em Azure requerem um mínimo de cinco nós primários (instâncias VM) e um nível de fiabilidade de Prata. É recomendado dedicar o tipo de nó primário de cluster aos serviços do sistema, e usar restrições de colocação para implementar a sua aplicação em tipos de nó secundário.

**As cargas de trabalho** de teste em Azure podem executar um mínimo de um ou três nós primários. Para configurar um cluster de um nó, certifique-se de que a `reliabilityLevel` definição é completamente omitida no seu modelo de Gestor de Recursos (especificar o valor da corda vazia `reliabilityLevel` não é suficiente). Se configurar o cluster de um nó configurado com o portal Azure, esta configuração é feita automaticamente.

> [!WARNING]
> Os clusters de um nó funcionam com uma configuração especial sem fiabilidade e onde a escala não é suportada.

#### <a name="non-primary-node-types"></a>Tipos de nó não primários

O número mínimo de nós para um tipo de nó não primário depende do nível de [durabilidade](#durability-characteristics-of-the-cluster) específico do tipo nó. Deve planear o número de nós (e nível de durabilidade) com base no número de réplicas de aplicações ou serviços que pretende executar para o tipo de nó, e dependendo se a carga de trabalho é declarada ou apátrida. Tenha em mente que pode aumentar ou diminuir o número de VMs num nó a qualquer momento depois de ter implantado o cluster.

##### <a name="stateful-workloads"></a>Cargas de trabalho imponentes

Para cargas de trabalho de produção imponentes utilizando coleções fiáveis do Service Fabric [ou atores fiáveis,](service-fabric-choose-framework.md)recomenda-se uma contagem mínima e de réplica de cinco. Com isto, em estado estável, acaba-se com uma réplica (a partir de um conjunto de réplicas) em cada domínio de avaria e domínio de upgrade. Em geral, utilize o nível de fiabilidade que definiu para os serviços do sistema como um guia para a contagem de réplicas que utiliza para os seus serviços estatais.

##### <a name="stateless-workloads"></a>Cargas de trabalho apátridas

Para as cargas de trabalho de produção apátridas, o tamanho mínimo suportado do nó não primário é três para manter o quórum, no entanto é recomendado um tamanho do tipo nó de cinco.

## <a name="next-steps"></a>Passos seguintes

Antes de configurar o seu cluster, reveja as `Not Allowed` [políticas de atualização](service-fabric-cluster-fabric-settings.md) do cluster para atenuar ter de recriar o seu cluster mais tarde devido a definições de configuração do sistema imutáveis.

Para obter mais informações sobre o planeamento de clusters, consulte:

* [Planeamento e dimensionamento de computação](service-fabric-best-practices-capacity-scaling.md)
* [Planeamento de capacidade para aplicações de Tecido de Serviço](service-fabric-capacity-planning.md)
* [Planeamento de recuperação de desastres](service-fabric-disaster-recovery.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
