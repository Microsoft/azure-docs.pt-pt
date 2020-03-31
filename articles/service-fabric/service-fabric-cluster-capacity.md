---
title: Planeamento da capacidade de cluster de tecido de serviço
description: Considerações de planeamento de capacidade de cluster de tecido de serviço. Níveis de nós, operações, durabilidade e fiabilidade
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: pepogors
ms.openlocfilehash: 6e60fc10dd7e0eec24de4a089d09d914624dcfbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258918"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Considerações de planeamento de capacidade de cluster de tecido de serviço
Para qualquer implementação de produção, o planeamento da capacidade é um passo importante. Aqui estão alguns dos itens que você tem que considerar como parte desse processo.

* O número de tipos de nós que o seu cluster precisa para começar
* As propriedades de cada tipo de nó (tamanho, primário, virado para a internet, número de VMs, etc.)
* As características de fiabilidade e durabilidade do cluster

> [!NOTE]
> Deve rever minimamente todos os valores de política **não permitidos** durante o planeamento. Isto é para garantir que define os valores adequadamente e para atenuar a queima do seu cluster mais tarde devido a configurações de configuração do sistema imutáveis. 
> 

Revejamos brevemente cada um destes itens.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>O número de tipos de nós que o seu cluster precisa para começar
Primeiro, tens de descobrir para que o cluster que estás a criar vai ser usado.  Que tipo de aplicações está a planear implementar neste cluster? Se não é claro sobre o propósito do cluster, provavelmente ainda não está pronto para entrar no processo de planeamento de capacidades.

Estabeleça o número de tipos de nó que o seu cluster precisa para começar.  Cada tipo de nó é mapeado para um conjunto de escala de máquina virtual. Cada tipo de nó pode então ser aumentado ou reduzido verticalmente de forma independente, pode ter conjuntos diferentes de portas abertas e ter métricas de capacidade diferente. Assim, a decisão do número de tipos de nó resume-se essencialmente às seguintes considerações:

* A sua aplicação tem vários serviços, e algum deles precisa de ser público ou virado para a internet? As aplicações típicas contêm um serviço de gateway frontal que recebe entrada de um cliente e um ou mais serviços de back-end que comunicam com os serviços front-end. Então, neste caso, acabas por ter pelo menos dois tipos de nós.
* Os seus serviços (que compõem a sua aplicação) têm diferentes necessidades de infraestrutura, tais como maiores ciclos de RAM ou CPU mais elevados? Por exemplo, vamos assumir que a aplicação que pretende implementar contém um serviço frontal e um serviço de back-end. O serviço frontal pode funcionar em VMs menores (tamanhos VM como D2) que têm portas abertas à internet.  O serviço back-end, no entanto, é intensivo em computação e precisa de funcionar em VMs maiores (com tamanhos VM como D4, D6, D15) que não estão virados para a internet.
  
  Neste exemplo, embora possa decidir colocar todos os serviços num só tipo de nó, recomendamos que os coloque num cluster com dois tipos de nó.  Isto permite que cada tipo de nó tenha propriedades distintas, como conectividade de internet ou tamanho VM. O número de VMs também pode ser dimensionado de forma independente.  
* Porque não pode prever o futuro, vá com factos que conhece, e escolha o número de tipos de nós que as suas aplicações precisam para começar. Pode sempre adicionar ou remover os tipos de nó mais tarde. Um cluster de tecido de serviço deve ter pelo menos um tipo de nó.

## <a name="the-properties-of-each-node-type"></a>As propriedades de cada tipo de nó
O **tipo de nó** pode ser visto como equivalente a papéis em Serviços cloud. Os tipos de nó definem os tamanhos vm, o número de VMs e as suas propriedades. Todos os tipos de nó que são definidos num cluster de tecido de serviço mapeia para um conjunto de escala de [máquina virtual](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).  
Cada tipo de nó é um conjunto de escala distinto e pode ser escalado para cima ou para baixo independentemente, ter diferentes conjuntos de portas abertas, e ter métricas de capacidade diferentes. Para obter mais informações sobre as relações entre tipos de nó e conjuntos de escala de máquinas virtuais, como pDR numa das instâncias, como abrir novas portas, e assim por diante, consulte tipos de nó de [cluster de tecido de serviço.](service-fabric-cluster-nodetypes.md)

Um cluster de tecido de serviço pode consistir em mais do que um tipo de nó. Nesse caso, o cluster é composto por um tipo primário de nó e um ou mais tipos de nó não primário.

Um único tipo de nó não pode escalar de forma fiável para além de 100 nós por escala de máquina virtual definida para aplicações SF; alcançar mais de 100 nós de forma fiável, exigirá que adicione conjuntos adicionais de escala de máquina virtual.

### <a name="primary-node-type"></a>Tipo de nó primário

Os serviços de sistema Service Fabric (por exemplo, o serviço Cluster Manager ou o serviço Image Store) são colocados no tipo de nó primário. 

![Screenshot de um cluster que tem dois tipos de nó][SystemServices]

* O **tamanho mínimo de VMs** para o tipo de nó primário é determinado pelo nível de **durabilidade** que escolher. O nível de durabilidade padrão é Bronze. Consulte [as características de durabilidade do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) para obter mais detalhes.  
* O **número mínimo de VMs** para o tipo de nó primário é determinado pelo nível de **fiabilidade** que escolher. O nível de fiabilidade padrão é Silver. Consulte [as características de fiabilidade do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) para obter mais detalhes.  

A partir do modelo de Gestor de Recursos Azure, `isPrimary` o tipo de nó primário é configurado com o atributo sob a [definição](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object)do tipo nó .

### <a name="non-primary-node-type"></a>Tipo de nó não primário

Em um aglomerado com vários tipos de nó, há um tipo de nó primário e o resto não são primários.

* O **tamanho mínimo de VMs** para tipos de nó não primários é determinado pelo nível de **durabilidade** que escolher. O nível de durabilidade padrão é Bronze. Para obter mais informações, consulte [as características de durabilidade do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).  
* O **número mínimo de VMs** para tipos de nó não primárioé um. No entanto, deve escolher este número com base no número de réplicas da aplicação/serviços que pretende executar neste tipo de nó. O número de VMs num nó pode ser aumentado depois de ter implantado o cluster.

## <a name="the-durability-characteristics-of-the-cluster"></a>Caraterísticas de durabilidade do cluster
O nível de durabilidade é utilizado para indicar ao sistema os privilégios que os seus VMs têm com a infraestrutura Azure subjacente. No tipo de nó primário, este privilégio permite ao Service Fabric interromper qualquer pedido de infraestrutura de nível VM (como um reboot vM, reimagem VM ou migração VM) que impactam os requisitos de quórum para os serviços do sistema e seus serviços estatais. Nos tipos de nó não primário, este privilégio permite ao Service Fabric interromper quaisquer pedidos de infraestrutura de nível VM (como reboot vM, reimagem vm e migração VM) que impactam os requisitos de quórum para os seus serviços de estado.

| Nível de durabilidade  | Número mínimo exigido de VMs | VM SKUs suportado                                                                  | Atualizações que faz para o seu conjunto de escala de máquina virtual                               | Atualizações e manutenção iniciadas pelo Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Gold             | 5                              | SKUs de nó completo dedicado a um único cliente (por exemplo, L32s, GS5, G5, DS15_v2, D15_v2) | Pode ser adiado até ser aprovado pelo cluster Service Fabric | Pode ser pausado por 2 horas por UD para permitir tempo adicional para as réplicas recuperarem de falhas anteriores |
| Silver           | 5                              | VMs de núcleo único ou superior com pelo menos 50 GB de SSD local                      | Pode ser adiado até ser aprovado pelo cluster Service Fabric | Não pode ser adiado por qualquer período significativo de tempo                                                    |
| Bronze           | 1                              | VMs com pelo menos 50 GB de SSD local                                              | Não será adiado pelo cluster Service Fabric           | Não pode ser adiado por qualquer período significativo de tempo                                                    |

> [!WARNING]
> Os tipos de nó que correm com durabilidade de Bronze não obtêm _privilégios._ Isto significa que os trabalhos de infraestrutura que impactam as suas cargas horárias não serão interrompidos ou atrasados, o que poderá afetar as suas cargas de trabalho. Utilize apenas bronze para tipos de nó que executam apenas cargas de trabalho apátridas. Para cargas de trabalho de produção, recomenda-se a execução de Prata ou superior. 
> 
> Independentemente de qualquer nível de durabilidade, a operação de [deatribuição](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/deallocate) no Conjunto de Escala VM destruirá o cluster

**Vantagens da utilização dos níveis de durabilidade de Prata ou Ouro**
 
- Reduz o número de passos necessários numa operação de escala (isto é, a desativação do nó e o Remove-ServiceFabricNodeState são chamados automaticamente).
- Reduz o risco de perda de dados devido a uma operação de mudança de VM SKU iniciada pelo cliente ou às operações de infraestrutura Azure.

**Desvantagens da utilização dos níveis de durabilidade de Prata ou Ouro**
 
- As implementações para o seu conjunto de máquinas virtuais e outros recursos azure relacionados podem ser adiadas, podem esgotar-se ou podem ser totalmente bloqueadas por problemas no seu cluster ou ao nível da infraestrutura. 
- Aumenta o número de [eventos de ciclo](service-fabric-reliable-services-lifecycle.md) de vida de réplica (por exemplo, swaps primários) devido a desativações automáticas do nó durante as operações de infraestrutura azure.
- Retira os nódosos de serviço por períodos de tempo, enquanto as atualizações de software da plataforma Azure ou as atividades de manutenção de hardware estão a ocorrer. Pode ver nós com estado incapacitante/desativado durante estas atividades. Isto reduz temporariamente a capacidade do seu cluster, mas não deve afetar a disponibilidade do seu cluster ou aplicações.

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>Recomendações para quando utilizar níveis de durabilidade de Prata ou Ouro

Use durabilidade prateada ou dourada para todos os tipos de nós que acolhem serviços estatais que espera escalar (reduzir a contagem de casos vm) frequentemente, e você prefere que as operações de implantação sejam atrasadas e a capacidade de ser reduzida em favor da simplificação desta escala operações. Os cenários de escala para fora (adicionando casos de VMs) não jogam na sua escolha do nível de durabilidade, só a escala-in faz.

### <a name="changing-durability-levels"></a>Alteração dos níveis de durabilidade
- Tipos de nó com níveis de durabilidade de Prata ou Ouro não podem ser desclassificados para Bronze.
- A atualização de Bronze para Prata ou Ouro pode demorar algumas horas.
- Ao alterar o nível de durabilidade, certifique-se de atualizá-lo tanto na configuração de extensão de tecido de serviço no seu recurso de conjunto de escala de máquina virtual, como na definição do tipo de nó no seu recurso de cluster De Serviço. Estes valores devem coincidir.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Recomendações operacionais para o tipo de nó que definiu para o nível de durabilidade prata ou ouro.

- Mantenha o seu cluster e aplicações saudáveis a todo o momento, e certifique-se de que as aplicações respondem a todos os [eventos](service-fabric-reliable-services-lifecycle.md) de ciclo de vida de réplica de Serviço (como réplica em construção está preso) em tempo oportuno.
- Adote formas mais seguras de fazer uma alteração VM SKU (Escala para cima/para baixo): Alterar o VM SKU de um conjunto de escala de máquina virtual requer uma série de passos e considerações. Aqui está o processo que pode seguir para evitar questões comuns.
    - Para tipos de nó não **primários:** Recomenda-se que crie um novo conjunto de escala de máquina virtual, modifique a restrição de colocação de serviço para incluir o novo tipo de conjunto/nó de escala de máquina virtual e, em seguida, reduza a contagem de tempo de escala de máquina virtual antiga para zero, um nó de cada vez (isto é para garantir que a remoção dos nós não impacte a fiabilidade do cluster).
    - **Para o tipo de nó primário:** Se o VM SKU que selecionou estiver na capacidade e quiser mudar para um VM SKU maior, siga a nossa orientação sobre [escalavertical para um tipo de nó primário](https://docs.microsoft.com/azure/service-fabric/service-fabric-scale-up-node-type). 

- Mantenha uma contagem mínima de cinco nódosos para qualquer conjunto de escala de máquina virtual que tenha um nível de durabilidade de Ouro ou Prata ativado.
- Cada escala de máquina virtual definida com nível de durabilidade Prata ou Ouro deve mapear para o seu próprio tipo de nó no cluster De Tecido de Serviço. Mapear vários conjuntos de escala de máquinavirtual para um único nó impedirá a coordenação entre o cluster de tecido de serviço e a infraestrutura Azure de funcionar corretamente.
- Não elimine instâncias vm aleatórias, utilize sempre a função de escala de escala de máquina virtual. A eliminação de casos de VM aleatórios tem o potencial de criar desequilíbrios na instância VM espalhada pela UD e FD. Este desequilíbrio pode afetar negativamente a capacidade dos sistemas de carregar corretamente o equilíbrio entre as instâncias de serviço/réplicas do serviço.
- Se utilizar a Escala Automática, então estabeleça as regras que escalam (remoção de instâncias VM) são feitas apenas um nó de cada vez. Reduzir mais do que um caso de cada vez não é seguro.
- Se apagar ou vender VMs no tipo de nó primário, nunca deve reduzir a contagem de VMs atribuídos abaixo do que o nível de fiabilidade requer. Estas operações serão bloqueadas indefinidamente numa escala definida com um nível de durabilidade de Prata ou Ouro.

## <a name="the-reliability-characteristics-of-the-cluster"></a>As características de fiabilidade do cluster
O nível de fiabilidade é utilizado para definir o número de réplicas dos serviços do sistema que pretende executar neste cluster no tipo de nó primário. Quanto mais o número de réplicas, mais fiáveis estão os serviços do sistema no seu cluster.  

O nível de fiabilidade pode assumir os seguintes valores:

* Platinum - Executar os serviços do Sistema com uma contagem de nove conjuntos de réplicas de nove
* Gold - Executar os serviços do Sistema com uma contagem de sete conjuntos de réplicas alvo
* Silver - Executar os serviços do Sistema com uma contagem de cinco conjuntos de réplicas alvo 
* Bronze - Executar os serviços do Sistema com uma contagem de três conjuntos de réplicas alvo

> [!NOTE]
> O nível de fiabilidade que escolher determina o número mínimo de nós que o seu tipo de nó primário deve ter. 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>Recomendações para o nível de fiabilidade

Quando aumenta ou diminui o tamanho do seu cluster (a soma das instâncias VM em todos os tipos de nós), tem de atualizar a fiabilidade do seu cluster de um nível para outro. Ao fazê-lo, os upgrades de cluster necessários para alterar a contagem de conjuntos de réplicas de serviços do sistema. Aguarde que a atualização em curso esteja concluída antes de fazer quaisquer outras alterações no cluster, como adicionar nós.  Pode monitorizar o progresso da atualização no Service Fabric Explorer ou executando [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

Aqui está a recomendação sobre a escolha do nível de fiabilidade.  O número de nós de sementes também está definido para o número mínimo de nós para um nível de fiabilidade.  Por exemplo, para um cluster com fiabilidade gold existem 7 nós de sementes.

| **Número de nós de cluster** | **Nível de Fiabilidade** |
| --- | --- |
| 1 |Não especifique o parâmetro do Nível de Fiabilidade, o sistema calcula-o |
| 3 |Bronze |
| 5 ou 6|Silver |
| 7 ou 8 |Gold |
| 9 e para cima |Platina |

## <a name="primary-node-type---capacity-guidance"></a>Tipo de nó primário - orientação de capacidade

Aqui está a orientação para o planeamento da capacidade do tipo nó primário:

- **Número de casos vm para executar qualquer carga de trabalho de produção em Azure:** Deve especificar um tamanho mínimo do nó primário de 5 e um nível de fiabilidade de prata.  
- **Número de casos vm para executar cargas** de trabalho de teste em Azure Pode especificar um tamanho mínimo do nó primário de 1 ou 3. O cluster de um nó, corre com uma configuração especial e assim, a escala para fora desse cluster não é suportada. O cluster de um nó, não tem fiabilidade e, por isso, no seu modelo de Gestor de Recursos, tem de remover/não especificar essa configuração (não configurar o valor de configuração não é suficiente). Se configurar o cluster de um nó configurado via portal, então a configuração é automaticamente cuidada. Um e três aglomerados de nós não são suportados para a execução de cargas de trabalho de produção. 
- **VM SKU:** O tipo principal de nó é onde os serviços do sistema funcionam, por isso o VM SKU que escolher para ele, deve ter em conta a carga máxima total que planeia colocar no cluster. Aqui está uma analogia para ilustrar o que quero dizer aqui - Pense no tipo de nó primário como os seus "Pulmões", é o que fornece oxigénio ao seu cérebro, e assim se o cérebro não tiver oxigénio suficiente, o seu corpo sofre. 

Uma vez que as necessidades de capacidade de um cluster são determinadas pela carga de trabalho que planeia executar no cluster, não podemos fornecer-lhe orientação qualitativa para a sua carga de trabalho específica, no entanto aqui está a orientação ampla para ajudá-lo a começar

Para cargas de trabalho de produção: 

- Recomenda-se dedicar os seus clusters ao nó primário aos serviços do sistema e utilizar restrições de colocação para implementar a sua aplicação em Nótipos secundários.
- O VM SKU recomendado é Standard D2_V2 ou equivalente com um mínimo de 50 GB de SSD local.
- A utilização mínima suportada VM SKU é Standard_D2_V3 ou Standard D1_V2 ou equivalente com um mínimo de 50 GB de SSD local. 
- A nossa recomendação é de um mínimo de 50 GB. Para as suas cargas de trabalho, especialmente quando se executam recipientes Windows, são necessários discos maiores. 
- Os VM SKUs de núcleo parcial como o Standard A0 não são suportados para cargas de trabalho de produção.
- Uma série VM SKUs não é suportada para cargas de trabalho de produção por razões de desempenho.
- Os VM sde baixa prioridade não são apoiados.

> [!WARNING]
> Alterar o tamanho vm sKU do nó primário em um cluster de funcionamento, é uma operação de escala, e documentado em [escala de escala de máquina virtual para fora](virtual-machine-scale-set-scale-node-type-scale-out.md) documentação.

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Tipo de nó não primário - orientação de capacidade para cargas de trabalho audais

Esta orientação destina-se a cargas de trabalho audais utilizando [coleções fiáveis](service-fabric-choose-framework.md) de tecido de serviço ou atores fiáveis que você está executando no tipo de nó não primário.

**Número de casos vm:** Para cargas horárias de produção que são imponentes, recomenda-se que as execute com uma contagem mínima e de réplica de 5. Isto significa que em estado constante acaba-se com uma réplica (de um conjunto de réplicas) em cada domínio de avaria e domínio de atualização. Todo o conceito de nível de fiabilidade para o tipo de nó primário é uma forma de especificar esta definição para os serviços do sistema. Assim, a mesma consideração aplica-se também aos seus serviços estatais.

Assim, para as cargas de trabalho de produção, o tamanho mínimo recomendado não primário do nó é 5, se estiver a executar cargas de trabalho audais nele.

**VM SKU:** Este é o tipo de nó onde os seus serviços de aplicação estão em execução, por isso o VM SKU que escolher para ele, deve ter em conta a carga máxima que planeia colocar em cada Nó. As necessidades de capacidade do tipo de nó, é determinada pela carga de trabalho que planeia executar no cluster, por isso não podemos fornecer-lhe orientação qualitativa para a sua carga de trabalho específica, no entanto aqui está a orientação ampla para ajudá-lo a começar

Para cargas de trabalho de produção 

- O VM SKU recomendado é Standard D2_V2 ou equivalente com um mínimo de 50 GB de SSD local.
- A utilização mínima suportada VM SKU é Standard_D2_V3 ou Standard D1_V2 ou equivalente com um mínimo de 50 GB de SSD local. 
- Os VM SKUs de núcleo parcial como o Standard A0 não são suportados para cargas de trabalho de produção.
- Uma série VM SKUs não é suportada para cargas de trabalho de produção por razões de desempenho.

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Tipo de nó não primário - orientação de capacidade para cargas de trabalho apátridas

Esta orientação de cargas de trabalho apátridas que você está executando no tipo de nó não primário.

**Número de casos vm:** Para cargas de trabalho de produção apátridas, o tamanho mínimo do nó não primário suportado é 2. Isto permite-lhe executar duas instâncias apátridas da sua aplicação e permitir que o seu serviço sobreviva à perda de uma instância VM. 

**VM SKU:** Este é o tipo de nó onde os seus serviços de aplicação estão em execução, por isso o VM SKU que escolher para ele, deve ter em conta a carga máxima que planeia colocar em cada Nó. As necessidades de capacidade do tipo de nó são determinadas pela carga de trabalho que planeia executar no cluster. Não podemos fornecer-lhe orientação qualitativa para a sua carga de trabalho específica.  No entanto, aqui está a orientação ampla para ajudá-lo a começar.

Para cargas de trabalho de produção 

- O VM SKU recomendado é Standard D2_V2 ou equivalente. 
- A utilização mínima suportada VM SKU é Standard D1 ou Standard D1_V2 ou equivalente. 
- Os VM SKUs de núcleo parcial como o Standard A0 não são suportados para cargas de trabalho de produção.
- Uma série VM SKUs não é suportada para cargas de trabalho de produção por razões de desempenho.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Passos seguintes
Assim que terminar o planeamento da sua capacidade e configurar um cluster, leia o seguinte:

* [Segurança do cluster de tecido de serviço](service-fabric-cluster-security.md)
* [Escala de cluster de tecido de serviço](service-fabric-cluster-scaling.md)
* [Planeamento de recuperação de desastres](service-fabric-disaster-recovery.md)
* [Relação dos Nótipos com o conjunto de escala de máquina virtual](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
