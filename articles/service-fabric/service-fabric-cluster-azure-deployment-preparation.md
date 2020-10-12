---
title: Planeie uma implantação de cluster de tecido de serviço Azure
description: Saiba mais sobre o planeamento e preparação para uma implantação de cluster de tecido de serviço de produção para a Azure.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 1064e59491b7144aafade24bd50131478fe025eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87281332"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Planear e preparar uma implantação de clusters

O planeamento e a preparação de um cluster de produção é muito importante.  Há muitos fatores a considerar.  Este artigo acompanha-o através dos passos de preparação do seu cluster.

## <a name="read-the-best-practices-information"></a>Leia as melhores práticas
Para gerir com sucesso as aplicações e clusters da Azure Service Fabric, existem operações que recomendamos vivamente que realize para otimizar a fiabilidade do seu ambiente de produção.  Para mais informações, leia [a aplicação de Service Fabric e as melhores práticas do cluster.](service-fabric-best-practices-overview.md)

## <a name="select-the-os-for-the-cluster"></a>Selecione o SO para o cluster
O Service Fabric permite a criação de clusters de Tecido de Serviço em quaisquer VMs ou computadores que executem o Windows Server ou o Linux.  Antes de implantar o seu cluster, tem de escolher o SISTEMA: Windows ou Linux.  Cada nó (máquina virtual) do cluster executa o mesmo SISTEMA, não é possível misturar VMs Windows e Linux no mesmo cluster.

## <a name="capacity-planning"></a>Planeamento de capacidade
Para qualquer implementação de produção, o planeamento da capacidade é um passo importante. Seguem-se alguns aspetos a considerar como parte do processo.

* O número inicial de tipos de nós para o seu cluster 
* As propriedades de cada um dos tipos de nó (tamanho, número de casos, primário, virado para a internet, número de VMs, etc.)
* As características de fiabilidade e durabilidade do cluster

### <a name="select-the-initial-number-of-node-types"></a>Selecione o número inicial de tipos de nós
Primeiro, tens de descobrir para que é que o aglomerado que estás a criar vai ser usado. Que tipo de aplicações planeias implementar neste cluster? A sua aplicação tem vários serviços, e algum deles precisa de ser público ou com a internet? Os seus serviços (que compõem a sua aplicação) têm diferentes necessidades de infraestrutura, como maiores ciclos de RAM ou CPU mais elevados? Um cluster de tecido de serviço pode consistir em mais de um tipo de nó: um tipo de nó primário e um ou mais tipos de nó não primários. Cada tipo de nó é mapeado para um conjunto de escala de máquina virtual. Cada tipo de nó pode então ser aumentado ou reduzido verticalmente de forma independente, pode ter conjuntos diferentes de portas abertas e ter métricas de capacidade diferente. [As propriedades do nó e os constrangimentos de colocação][placementconstraints] podem ser configurados para restringir serviços específicos a tipos específicos de nós.  Para mais informações, consulte [o planeamento da capacidade do cluster de tecido de serviço.](service-fabric-cluster-capacity.md)

### <a name="select-node-properties-for-each-node-type"></a>Selecione propriedades de nó para cada tipo de nó
Os tipos de nó definem o VM SKU, número e propriedades dos VMs no conjunto de escalas associados.

O tamanho mínimo de VMs para cada tipo de nó é determinado pelo nível de [durabilidade][durability] que escolhe para o tipo de nó.

O número mínimo de VMs para o tipo de nó primário é determinado pelo [nível de fiabilidade][reliability] que escolher.

Consulte as recomendações mínimas para [os tipos de nó primários,](service-fabric-cluster-capacity.md#primary-node-type) [cargas de trabalho declaradas em tipos de nó não primários](service-fabric-cluster-capacity.md#stateful-workloads)e [cargas de trabalho apátridas em tipos de nó de nó primários](service-fabric-cluster-capacity.md#stateless-workloads).

Mais do que o número mínimo de nós deve basear-se no número de réplicas da aplicação/serviços que pretende executar neste tipo de nó.  [O planeamento de capacidade para aplicações de Tecido de Serviço](service-fabric-capacity-planning.md) ajuda-o a estimar os recursos necessários para executar as suas aplicações. Pode sempre escalar o cluster para cima ou para baixo mais tarde para se ajustar para alterar a carga de trabalho da aplicação. 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>Utilize discos de OS efémeros para conjuntos de escala de máquinas virtuais

*Os discos EFÉMER OS* são armazenamento criados na máquina virtual local (VM), e não guardados para armazenamento remoto de Azure. São recomendados para todos os tipos de nó de tecido de serviço (Primário e Secundário), porque em comparação com os discos tradicionais persistentes de OS, discos de OS efémeros:

* Reduzir a latência de leitura/escrita para o disco DE
* Ativar operações de gestão de nó de reset/reimage mais rápidas
* Reduzir os custos globais (os discos são gratuitos e não incorrem em custos adicionais de armazenamento)

Os discos EFÉMER OS não são uma característica específica do Tecido de Serviço, mas sim uma característica dos *conjuntos de escala de máquina virtual* Azure que são mapeados para os tipos de nó de tecido de serviço. A sua utilização com tecido de serviço requer o seguinte no seu modelo de Cluster Azure Resource Manager:

1. Certifique-se de que os seus tipos de nó especificam [tamanhos Azure VM suportados](../virtual-machines/ephemeral-os-disks.md) para discos DESemestálricos e que o tamanho VM tem tamanho de cache suficiente para suportar o tamanho do disco de SO (ver *nota* abaixo).) Por exemplo:

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > Certifique-se de que seleciona um tamanho VM com um tamanho de cache igual ou superior ao tamanho do disco DE do próprio VM, caso contrário a sua implantação Azure pode resultar em erro (mesmo que seja inicialmente aceite).

2. Especificar uma versão virtual de conjunto de escala de máquina `vmssApiVersion` () de `2018-06-01` ou mais tarde:

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. Na secção de conjunto de escala de máquina virtual do seu modelo de implementação, especifique `Local` a opção `diffDiskSettings` para:

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

> [!NOTE]
> As aplicações do utilizador não devem ter qualquer dependência/ficheiro/artefacto no disco os, uma vez que o disco de so seria perdido no caso de uma atualização do SISTEMA.
> Por isso, não é recomendado utilizar [patchOrchestrationApplicationApplication](https://github.com/microsoft/Service-Fabric-POA) com discos efémeros.
>

> [!NOTE]
> Os VMSS não efémeros existentes não podem ser atualizados no local para utilizar discos efémeros.
> Para migrar, os utilizadores terão de [adicionar](./virtual-machine-scale-set-scale-node-type-scale-out.md) um novo nóType com discos efémeros, mover as cargas de trabalho para o novo nodeType & [remover](./service-fabric-how-to-remove-node-type.md) o nóType existente.
>

Para obter mais informações e mais opções de configuração, consulte [discos Efemérides DES para VMs Azure](../virtual-machines/ephemeral-os-disks.md) 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Selecione os níveis de durabilidade e fiabilidade para o cluster
O nível de durabilidade é utilizado para indicar ao sistema os privilégios que os seus VMs têm com a infraestrutura Azure subjacente. No tipo de nó primário, este privilégio permite que o Service Fabric faça uma pausa em qualquer pedido de infraestrutura de nível VM (como um reboot VM, reimagem VM ou migração VM) que impactem os requisitos do quórum para os serviços do sistema e os seus serviços estatais. Nos tipos de nó não primários, este privilégio permite que o Service Fabric faça uma pausa em quaisquer pedidos de infraestrutura de nível VM (como reboot VM, reimagem VM e migração VM) que impactem os requisitos do quórum para os seus serviços estatais.  Para obter vantagens dos diferentes níveis e recomendações sobre em que nível utilizar e quando, ver [As características de durabilidade do cluster.][durability]

O nível de fiabilidade é utilizado para definir o número de réplicas dos serviços de sistema que pretende executar neste cluster no tipo de nó primário. Quanto mais o número de réplicas, mais confiáveis são os serviços do sistema no seu cluster.  Para obter vantagens dos diferentes níveis e recomendações sobre em que nível utilizar e quando, consulte [as características de fiabilidade do cluster.][reliability] 

## <a name="enable-reverse-proxy-andor-dns"></a>Ativar procuração inversa e/ou DNS
Os serviços que se conectam entre si dentro de um cluster geralmente podem aceder diretamente aos pontos finais de outros serviços porque os nós de um cluster estão na mesma rede local. Para facilitar a ligação entre serviços, a Service Fabric fornece serviços adicionais: Um [serviço DNS](service-fabric-dnsservice.md) e um [serviço de procuração inversa.](service-fabric-reverseproxy.md)  Ambos os serviços podem ser ativados ao implantar um cluster.

Uma vez que muitos serviços, especialmente serviços contentorizados, podem ter um nome URL existente, ser capaz de resolvê-los usando o protocolo padrão DNS (em vez do protocolo de Serviço de Nomeação) é conveniente, especialmente em cenários de "levantar e mudar" de aplicação. Isto é exatamente o que o serviço DNS faz. Permite mapear nomes DNS para um nome de serviço e, portanto, resolver endereços IP de ponto final.

O proxy invertido endereça serviços no cluster que expõem pontos finais HTTP (incluindo HTTPS). O proxy reverso simplifica muito a chamada de outros serviços, fornecendo um formato URI específico.  O representante inverso também lida com os passos de determinação, ligação e retíria necessários para que um serviço se comunique com outro.

## <a name="prepare-for-disaster-recovery"></a>Preparar para a recuperação após desastre
Uma parte crítica da prestação de alta disponibilidade é garantir que os serviços possam sobreviver a todos os tipos de falhas. Isto é especialmente importante para falhas que não são planeadas e fora do seu controlo. [Prepare-se para a recuperação de desastres](service-fabric-disaster-recovery.md) descreve alguns modos de falha comuns que podem ser desastres se não forem modelados e geridos corretamente. Também discute mitigações e ações a tomar se um desastre acontecer de qualquer forma.

## <a name="production-readiness-checklist"></a>Lista de verificação da preparação de produção
A sua aplicação e cluster estão prontos para levar o tráfego de produção? Antes de colocar o seu cluster na produção, passe pela [lista de verificação de prontidão de produção](service-fabric-production-readiness-checklist.md). Mantenha a sua aplicação e cluster a funcionar sem problemas, utilizando os itens desta lista de verificação. Recomendamos vivamente que todos estes itens sejam verificados antes de entrarem em produção.

## <a name="next-steps"></a>Passos seguintes
* [Criar um cluster de tecido de serviço executando janelas](service-fabric-best-practices-overview.md)
* [Criar um cluster de tecido de serviço executando Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster