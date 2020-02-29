---
title: Planeie uma implantação de cluster de tecido de serviço Azure
description: Saiba mais sobre planeamento e preparação para uma implantação de cluster de serviço de produção para o Azure.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 1762a6975448301957579b3437a8af5c89b3accd
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78193481"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Planeie e prepare-se para uma implantação de cluster

O planeamento e preparação para uma implantação de clusters de produção é muito importante.  Há muitos fatores a considerar.  Este artigo acompanha-o através dos passos de preparação da sua implantação do cluster.

## <a name="read-the-best-practices-information"></a>Leia as informações sobre as melhores práticas
Para gerir com sucesso aplicações e clusters azure Service Fabric, existem operações que recomendamos vivamente que realize para otimizar a fiabilidade do seu ambiente de produção.  Para mais informações, leia a [aplicação Service Fabric e cluster as melhores práticas.](service-fabric-best-practices-overview.md)

## <a name="select-the-os-for-the-cluster"></a>Selecione o S para o cluster
O Service Fabric permite a criação de clusters de Tecido de Serviço em quaisquer VMs ou computadores que executem o Windows Server ou o Linux.  Antes de implementar o seu cluster, deve escolher o SISTEMA: Windows ou Linux.  Cada nó (máquina virtual) do cluster executa o mesmo SISTEMA, não pode misturar Windows e VMs Linux no mesmo cluster.

## <a name="capacity-planning"></a>Planeamento de capacidade
Para qualquer implantação de produção, o planeamento da capacidade é um passo importante. Seguem-se alguns aspetos a considerar como parte do processo.

* O número inicial de tipos de nó para o seu cluster 
* As propriedades de cada tipo de nó (tamanho, número de instâncias, primárias, viradas para a Internet, número de VMs, etc.)
* As características de fiabilidade e durabilidade do cluster

### <a name="select-the-initial-number-of-node-types"></a>Selecione o número inicial de tipos de nó
Primeiro, tens de descobrir para que o cluster que estás a criar vai ser usado. Que tipo de aplicações está a planear implementar neste cluster? A sua aplicação tem vários serviços, e algum deles precisa de ser público ou virado para a internet? Os seus serviços (que compõem a sua aplicação) têm diferentes necessidades de infraestrutura, tais como maiores ciclos de RAM ou CPU mais elevados? Um cluster de tecido de serviço pode consistir em mais do que um tipo de nó: um tipo de nó primário e um ou mais tipos de nó não primário. Cada tipo de nó é mapeado para um conjunto de escala de máquina virtual. Cada tipo de nó pode então ser aumentado ou reduzido verticalmente de forma independente, pode ter conjuntos diferentes de portas abertas e ter métricas de capacidade diferente. [As propriedades do nó e os constrangimentos][placementconstraints] de colocação podem ser criados para restringir serviços específicos a tipos específicos de nó.  Para mais informações, leia [O número de tipos de nós que o seu cluster precisa de começar](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with).

### <a name="select-node-properties-for-each-node-type"></a>Selecione propriedades do nó para cada tipo de nó
Os tipos de nó definem o VM SKU, o número e as propriedades dos VMs no conjunto de escala associado.

O tamanho mínimo de VMs para cada tipo de nó é determinado pelo nível de [durabilidade][durability] que escolher para o tipo de nó.

O número mínimo de VMs para o tipo de nó primário é determinado pelo nível de [fiabilidade][reliability] que escolher.

Consulte as recomendações mínimas para os tipos de [nó primários,](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance) [cargas de trabalho atemdáveis em tipos de nó não primários](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads)e cargas de [trabalho apátridas em tipos de nó não primários](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads).

Mais do que o número mínimo de nós deve basear-se no número de réplicas da aplicação/serviços que pretende executar neste tipo de nó.  [O planeamento de capacidade para aplicações de Tecido](service-fabric-capacity-planning.md) de Serviço ajuda-o a estimar os recursos necessários para executar as suas aplicações. Pode sempre escalar o cluster para cima ou para baixo mais tarde para se ajustar para alterar a carga de trabalho da aplicação. 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>Utilize discos efémeros de OS para conjuntos de escala de máquinas virtuais

*Os discos Ephemeral OS* são armazenamento criado na máquina virtual local (VM), e não guardados para armazenamento azure remoto. São recomendados para todos os tipos de nó de tecido de serviço (primário e secundário), porque em comparação com os discos operativos persistentes tradicionais, discos efémeros osso:

* Reduzir a latência de leitura/escrita para o disco OS
* Permitir operações de gestão de nó de reset/reimagem mais rápidas
* Reduzir os custos globais (os discos são gratuitos e não incorrem em custos adicionais de armazenamento)

Os discos Ephemeral OS não são uma característica específica do Tecido de Serviço, mas sim uma característica dos conjuntos de *escala de máquinas virtuais* Azure que são mapeados para tipos de nó de tecido de serviço. A sua utilização com tecido de serviço requer o seguinte no modelo do gestor de recursos azure do seu cluster:

1. Certifique-se de que os tipos do nó especificam tamanhos de [VM Azure suportados](../virtual-machines/windows/ephemeral-os-disks.md) para discos Efémeros OS e que o tamanho vm tem tamanho de cache suficiente para suportar o seu tamanho de disco OS (ver *Nota* abaixo.) Por exemplo:

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > Certifique-se de selecionar um tamanho VM com um tamanho de cache igual ou superior ao tamanho do disco OS do próprio VM, caso contrário a sua implantação Azure pode resultar em erro (mesmo que seja inicialmente aceite).

2. Especifique uma versão virtual de conjunto de escala de máquina (`vmssApiVersion`) de `2018-06-01` ou posteriormente:

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. Na secção de conjunto de escala de máquina virtual do seu modelo de implementação, especifique `Local` opção para `diffDiskSettings`:

    ```xml
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
        "virtualMachineProfile": {
            "storageProfile": {
                "osDisk": {
                        "caching": "ReadOnly",
                        "createOption": "FromImage",
                        "diffDiskSettings": {
                            "option": "Local"
                        },
                }
            }
        }
    ```

Para mais informações e mais opções de configuração, consulte [discos Ephemeral OS para VMs Azure](../virtual-machines/windows/ephemeral-os-disks.md) 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Selecione os níveis de durabilidade e fiabilidade do cluster
O nível de durabilidade é utilizado para indicar ao sistema os privilégios que os seus VMs têm com a infraestrutura Azure subjacente. No tipo de nó primário, este privilégio permite ao Service Fabric interromper qualquer pedido de infraestrutura de nível VM (como um reboot vM, reimagem VM ou migração VM) que impactam os requisitos de quórum para os serviços do sistema e seus serviços estatais. Nos tipos de nó não primário, este privilégio permite ao Service Fabric interromper quaisquer pedidos de infraestrutura de nível VM (como reboot vM, reimagem vm e migração VM) que impactam os requisitos de quórum para os seus serviços de estado.  Para obter vantagens dos diferentes níveis e recomendações em que nível utilizar e quando, ver [as características de durabilidade do cluster][durability].

O nível de fiabilidade é utilizado para definir o número de réplicas dos serviços do sistema que pretende executar neste cluster no tipo de nó primário. Quanto mais o número de réplicas, mais fiáveis estão os serviços do sistema no seu cluster.  Para obter vantagens dos diferentes níveis e recomendações em que nível utilizar e quando, ver [as características de fiabilidade do cluster][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Ativar procuração inversa e/ou DNS
Os serviços que se ligam entre si dentro de um cluster geralmente podem aceder diretamente aos pontos finais de outros serviços porque os nós de um cluster estão na mesma rede local. Para facilitar a ligação entre serviços, o Service Fabric presta serviços adicionais: um [serviço DNS](service-fabric-dnsservice.md) e um serviço de [procuração inversa.](service-fabric-reverseproxy.md)  Ambos os serviços podem ser ativados ao implantar um cluster.

Uma vez que muitos serviços, especialmente serviços contentorizados, podem ter um nome URL existente, poder resolvê-los usando o protocolo Padrão DNS (em vez do protocolo de Serviço de Nomeação) é conveniente, especialmente em cenários de aplicação "levantar e mudar". Isto é exatamente o que o serviço DNS faz. Permite-lhe mapear os nomes dNS para um nome de serviço e, portanto, resolver endereços IP do ponto final.

O proxy inverso aborda os serviços no cluster que expõe mistos DEHTTP (incluindo HTTPS). O representante inverso simplifica consideravelmente a chamada de outros serviços, fornecendo um formato URI específico.  O proxy invertido também lida com as etapas de determinação, ligação e retry necessárias para que um serviço se comunique com outro.

## <a name="prepare-for-disaster-recovery"></a>Preparar para a recuperação após desastre
Uma parte crítica da prestação de alta disponibilidade é garantir que os serviços possam sobreviver a todos os tipos de falhas. Isto é especialmente importante para falhas que não são planeadas e fora do seu controlo. [Prepare-se para a recuperação](service-fabric-disaster-recovery.md) de desastres descreve alguns modos comuns de falha que podem ser desastres se não forem modelados e geridos corretamente. Também discute atenuações e ações a tomar se um desastre aconteceu de qualquer forma.

## <a name="production-readiness-checklist"></a>Lista de verificação da preparação de produção
A sua aplicação e cluster estão prontos para tomar tráfego de produção? Antes de implantar o seu cluster para produção, passe pela lista de verificação de [prontidão](service-fabric-production-readiness-checklist.md)de produção . Mantenha a sua aplicação e cluster funcionando sem problemas, trabalhando através dos itens nesta lista de verificação. Recomendamos vivamente que todos estes itens sejam verificados antes de entrarem em produção.

## <a name="next-steps"></a>Passos seguintes
* [Criar um cluster de tecido de serviço que executa janelas](service-fabric-best-practices-overview.md)
* [Criar um cluster de tecido de serviço executando Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster