---
title: Introdução a grupos de contentores
description: Conheça grupos de contentores em Casos de Contentores Azure, uma coleção de casos que partilham um ciclo de vida e recursos como CPUs, armazenamento e rede
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: b5f4f834d44294d846495a59af2fb65b231e4820
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583828"
---
# <a name="container-groups-in-azure-container-instances"></a>Grupos de contentores no Azure Container Instances

O recurso de alto nível em Casos de Contentores Azure é o grupo de *contentores*. Este artigo descreve quais são os grupos de contentores e os tipos de cenários que permitem.

## <a name="what-is-a-container-group"></a>O que é um grupo de contentores?

Um grupo de contentores é uma coleção de contentores que são programados na mesma máquina de acolhimento. Os contentores de um grupo de contentores partilham um ciclo de vida, recursos, rede local e volumes de armazenamento. É semelhante no conceito a um *pod* em [Kubernetes.][kubernetes-pod]

O diagrama seguinte mostra um exemplo de um grupo de contentores que inclui vários recipientes:

![Diagrama de grupos de contentores][container-groups-example]

Este grupo de contentores de exemplo:

* Está programado numa única máquina de acolhimento.
* É atribuída uma etiqueta de nome DNS.
* Expõe um único endereço IP público, com uma porta exposta.
* É composto por dois contentores. Um contentor ouve no porto 80, enquanto o outro ouve no porto 5000.
* Inclui duas partilhas de ficheiros Azure como suportes de volume, e cada recipiente monta uma das ações localmente.

> [!NOTE]
> Atualmente, grupos multi-contentores suportam apenas contentores Linux. Para os recipientes Windows, as instâncias de contentores Azure apenas suportam a implantação de uma única instância de contentores. Enquanto estamos a trabalhar para trazer todas as funcionalidades para os recipientes windows, pode encontrar as diferenças de plataforma atuais na [visão geral](container-instances-overview.md#linux-and-windows-containers)do serviço .

## <a name="deployment"></a>Implementação

Aqui estão duas formas comuns de implantar um grupo multi-contentores: use um modelo de Gestor de [Recursos][resource-manager template] ou um [ficheiro YAML][yaml-file]. Um modelo de Gestor de Recursos é recomendado quando precisa de implementar recursos adicionais de serviço Azure (por exemplo, uma [partilha de Ficheiros Azure)][azure-files]quando implementa as instâncias do contentor. Devido à natureza mais concisa do formato YAML, um ficheiro YAML é recomendado quando a sua implementação inclui apenas instâncias de contentores. Para mais detalhes sobre as propriedades que pode definir, consulte a referência do [modelo do Gestor](/azure/templates/microsoft.containerinstance/containergroups) de Recursos ou documentação de referência [YAML.](container-instances-reference-yaml.md)

Para preservar a configuração de um grupo de contentores, pode exportar a configuração para um ficheiro YAML utilizando a exportação de [contentores Az][az-container-export]de comando Azure CLI . A exportação permite-lhe armazenar as configurações do grupo de contentores no controlo da versão para "configuração como código". Ou, use o ficheiro exportado como ponto de partida para desenvolver uma nova configuração no YAML.



## <a name="resource-allocation"></a>Atribuição de recursos

As Instâncias de Contentores Azure alocam recursos como CPUs, memória e [GPUs][gpus] opcionalmente (pré-visualização) a um grupo multi-contentores, adicionando os pedidos de [recursos][resource-requests] das instâncias do grupo. Tomando os recursos da CPU como exemplo, se criar um grupo de contentores com duas instâncias de contentores, cada um solicitando 1 CPU, então o grupo de contentores é atribuído a 2 CPUs.

### <a name="resource-usage-by-container-instances"></a>Utilização de recursos por instâncias de contentores

Cada instância de contentores de um grupo é atribuída aos recursos especificados no seu pedido de recurso. No entanto, os recursos máximos utilizados por uma instância de contentores num grupo podem ser diferentes se configurar a sua propriedade de limite de [recursos][resource-limits] opcional. O limite de recursos de uma instância de contentores deve ser maior ou igual à propriedade de pedido de [recursos][resource-requests] obrigatório.

* Se não especificar um limite de recursos, o uso máximo de recursos da instância do recipiente é o mesmo que o pedido de recursos.

* Se especificar um limite para uma instância de contentor, a utilização máxima da instância pode ser maior do que o pedido, até ao limite estabelecido. Consequentemente, a utilização de recursos por outras instâncias de contentores no grupo poderia diminuir. O limite máximo de recursos que pode definir para uma instância de contentores são os recursos totais atribuídos ao grupo.
    
Por exemplo, num grupo com dois contentores cada um solicitando 1 CPU, um dos seus contentores pode executar uma carga de trabalho que requer mais CPUs para funcionar do que o outro.

Neste cenário, pode definir um limite de recursos de até 2 CPUs para a instância do contentor. Esta configuração permite que a instância do recipiente utilize até 2 CPUs, se disponível.

> [!NOTE]
> Uma pequena quantidade de recursos de um grupo de contentores é utilizada pela infraestrutura subjacente do serviço. Os seus contentores poderão aceder à maioria, mas não todos os recursos atribuídos ao grupo. Por esta razão, planeie um pequeno tampão de recursos ao solicitar recursos para contentores no grupo.

### <a name="minimum-and-maximum-allocation"></a>Atribuição mínima e máxima

* Aloque um **mínimo** de 1 CPU e 1 GB de memória a um grupo de contentores. As instâncias individuais de contentores dentro de um grupo podem ser aprovisionadas com menos de 1 CPU e 1 GB de memória. 

* Para obter os recursos **máximos** de um grupo de contentores, consulte a [disponibilidade de recursos][region-availability] para as instâncias de contentores Azure na região de implantação.

## <a name="networking"></a>Redes

Os grupos de contentores podem partilhar um endereço IP virado para o exterior, uma ou mais portas nesse endereço IP, e uma etiqueta DNS com um nome de domínio totalmente qualificado (FQDN). Para permitir que os clientes externos cheguem a um contentor dentro do grupo, deve expor a porta no endereço IP e a partir do recipiente. O endereço IP de um grupo de contentores e o FQDN são libertados quando o grupo de contentores é eliminado. 

Dentro de um grupo de contentores, as instâncias de contentores podem chegar entre si através de um local de acolhimento em qualquer porto, mesmo que esses portos não sejam expostos externamente no endereço IP do grupo ou a partir do contentor.

Coloque opcionalmente grupos de contentores numa [rede virtual Azure][virtual-network] para permitir que os contentores comuniquem de forma segura com outros recursos da rede virtual.

## <a name="storage"></a>Armazenamento

Pode especificar volumes externos para montar dentro de um grupo de contentores. Os volumes suportados incluem:
* [Partilha de ficheiros Azure][azure-files]
* [Segredo][secret]
* [Diretório vazio][empty-directory]
* [Repo git clonado][volume-gitrepo]

Pode mapear esses volumes em caminhos específicos dentro dos recipientes individuais de um grupo. 

## <a name="common-scenarios"></a>Cenários comuns

Grupos multi-contentores são úteis nos casos em que se pretende dividir uma única tarefa funcional num pequeno número de imagens de contentores. Estas imagens podem então ser entregues por diferentes equipas e ter requisitos de recursos separados.

A utilização de exemplos pode incluir:

* Um contentor que serve uma aplicação web e um contentor que retira o conteúdo mais recente do controlo de origem.
* Um recipiente de aplicação e um recipiente de exploração madeireira. O recipiente de exploração recolhe os registos e a saída de métricas pela aplicação principal e escreve-os para armazenamento a longo prazo.
* Um recipiente de aplicação e um recipiente de monitorização. O recipiente de monitorização faz periodicamente um pedido à aplicação para garantir que está a funcionar e a responder corretamente, e levanta um alerta se não estiver.
* Um recipiente frontal e um recipiente traseiro. A parte frontal pode servir uma aplicação web, com a parte traseira a executar um serviço para recuperar dados. 

## <a name="next-steps"></a>Passos seguintes

Saiba como implantar um grupo de contentores multi-contentores com um modelo de Gestor de Recursos Azure:

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
[virtual-network]: container-instances-virtual-network-concepts.md
[secret]: container-instances-volume-secret.md
[volume-gitrepo]: container-instances-volume-gitrepo.md
[gpus]: container-instances-gpu.md
[empty-directory]: container-instances-volume-emptydir.md
[az-container-export]: /cli/azure/container#az-container-export
