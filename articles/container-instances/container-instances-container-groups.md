---
title: Grupos de contentores do Azure Container Instances
description: Compreender a grupos com vários contentores, como profissional no Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/06/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 094dc796541aaccbdfa1efc93e833bc6c5a7d1f1
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57729558"
---
# <a name="container-groups-in-azure-container-instances"></a>Grupos de contentores no Azure Container Instances

O recurso de nível superior no Azure Container Instances é a *grupo de contentores*. Este artigo descreve o que são grupos de contentores e os tipos de cenários permitem.

## <a name="how-a-container-group-works"></a>Como funciona um grupo de contentores

Um grupo de contentores é uma coleção de contentores que obter agendadas no mesmo computador anfitrião. Os contentores no grupo de contentores partilham um ciclo de vida, recursos, rede local e volumes de armazenamento. Ele é semelhante em conceito para um *pod* na [Kubernetes][kubernetes-pod].

O diagrama seguinte mostra um exemplo de um grupo de contentor que inclui vários contentores:

![Diagrama de grupos de contentor][container-groups-example]

Este grupo de contentores de exemplo:

* Está agendado num único computador anfitrião.
* É atribuído uma etiqueta de nome DNS.
* Expõe um único endereço IP público, com uma porta exposto.
* Consiste em dois contentores. Um contentor escuta na porta 80, enquanto as outras escuta na porta 5000.
* Inclui duas partilhas de ficheiros do Azure como volume monta e cada contentor monta um dos localmente as partilhas.

> [!NOTE]
> Grupos com vários contentores atualmente suportam apenas os contentores de Linux. Para contentores do Windows, o Azure Container Instances só suporta a implementação de uma única instância. Enquanto estamos a trabalhar para colocar todas as funcionalidades para os contentores do Windows, pode encontrar as diferenças da plataforma atual no serviço [descrição geral](container-instances-overview.md#linux-and-windows-containers).

## <a name="deployment"></a>Implementação

Eis duas formas comuns de implementar um grupo de vários contentor: utilizar um [modelo do Resource Manager] [ resource-manager template] ou uma [ficheiro YAML][yaml-file]. Utilizar um modelo do Resource Manager, quando precisa para implementar recursos adicionais de serviço do Azure (por exemplo, um [partilha de ficheiros do Azure][azure-files]) no momento implementa as instâncias de contentores. Devido à natureza de mais concisa do formato YAML, um ficheiro YAML é recomendado quando a implementação inclui apenas as instâncias de contentor.

## <a name="resource-allocation"></a>Alocação de recursos

O Azure Container Instances aloca recursos, tais como CPUs, memória e opcionalmente [GPUs] [ gpus] (pré-visualização) para um grupo de contentores ao adicionar o [solicitações de recursos] [ resource-requests] das instâncias do grupo. Colocar recursos de CPU como exemplo, se criar um grupo de contentores com duas instâncias, cada 1 solicitante da CPU, em seguida, o grupo de contentores é alocado 2 CPUs.

Os recursos máximos disponíveis para um grupo de contentores dependem da [região do Azure] [ region-availability] utilizada para a implementação.

### <a name="container-resource-requests-and-limits"></a>Limites e pedidos de recursos de contentor

* Por predefinição, instâncias de contentor num grupo partilham os recursos de pedido do grupo. Num grupo com duas instâncias de cada 1 solicitante CPU, o grupo como todo tem acesso a 2 CPUs. Cada instância poderá utilizar até as 2 CPUs e as instâncias podem competir por recursos de CPU, enquanto estão em execução.

* Para limitar a utilização de recursos por uma instância de um grupo, se desejar, defina uma [limite de recursos] [ resource-limits] para a instância. Um grupo com duas instâncias de 1 a pedir CPU, um dos seus contentores pode exigir mais CPUs ser executada que a outra.

  Neste cenário, poderia definir um limite de recursos de 0,5 CPU para uma instância e um limite de 2 CPUs para a segunda. Esta configuração limita a utilização de recursos o primeiro contentor como 0,5 CPU, permitindo que o segundo contentor a utilizar até às CPUs completa 2, se disponível.

Para obter mais informações, consulte a [ResourceRequirements] [ resource-requirements] REST API de grupos de propriedade no contentor.

### <a name="minimum-and-maximum-allocation"></a>Mínimo e máxima alocação

* Alocar um **mínima** 1 CPU e 1 GB de memória para um grupo de contentores. Instâncias de contentor individuais dentro de um grupo podem ser aprovisionadas com menos de 1 CPU e 1 GB de memória. 

* Para o **máximo** recursos num grupo de contentor, veja [disponibilidade de recursos] [aci-região-disponibilidade] para o Azure Container Instances na região de implementação.

## <a name="networking"></a>Redes

Grupos de contentores partilham um endereço IP e um espaço de nomes de porta nesse endereço IP. Para permitir que os clientes externos acedam um contentor no grupo, deve expor a porta no endereço IP e do contêiner. Uma vez que os contentores dentro do grupo partilham um espaço de nomes de porta, o mapeamento de portas não é suportado. Contentores dentro de um grupo podem chegar entre si através de localhost nas portas que eles tenham exposto, mesmo que essas portas não são expostas externamente no endereço IP do grupo.

Opcionalmente, implementar grupos de contentores para um [rede virtual do Azure] [ virtual-network] (pré-visualização) para permitir que os contentores para comunicar de forma segura com outros recursos na rede virtual.

## <a name="storage"></a>Armazenamento

Pode especificar volumes externos para montar dentro de um grupo de contentor. Pode mapear esses volumes em caminhos específicos dentro de contentores individuais num grupo.

## <a name="common-scenarios"></a>Cenários comuns

Os grupos de vários contentores são úteis nos casos em que pretende dividir uma única tarefa funcional num pequeno número de imagens de contentor. Estas imagens, em seguida, podem ser fornecidas por equipes diferentes e tiverem requisitos de recursos separado.

Exemplo de utilização pode incluir:

* Um contentor que serve uma aplicação web e um contentor de extrair o conteúdo mais recente no controle da fonte.
* Um contêiner de aplicativo e um contentor de registo. O contentor de registo recolhe a saída de registos e métricas por aplicativo principal e escreve-as para o armazenamento de longo prazo.
* Um contêiner de aplicativo e um contentor de monitorização. O contentor de monitorização faz periodicamente um pedido para a aplicação para se certificar de que está em execução e a responder corretamente e gera um alerta se não for.
* Um contentor de front-end e um contentor de back-end. O front-end pode atender a um aplicativo web, com o back-end que executa um serviço para recuperar dados. 

## <a name="next-steps"></a>Passos Seguintes

Saiba como implementar um grupo de contentores de vários contentores com um modelo Azure Resource Manager:

> [!div class="nextstepaction"]
> [Implementar um grupo de contentores][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-vnet.md
[gpus]: container-instances-gpu.md