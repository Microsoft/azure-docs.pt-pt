---
title: Introdução aos grupos de contentores
description: Conheça os grupos de contentores em Instâncias de Contentores Azure, uma coleção de casos que partilham um ciclo de vida e recursos como CPUs, armazenamento e rede
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: 72ebe6186da179bc5a1effddcc14327455eb7557
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89612411"
---
# <a name="container-groups-in-azure-container-instances"></a>Grupos de contentores no Azure Container Instances

O recurso de alto nível em Instâncias de Contentores Azure é o *grupo de contentores.* Este artigo descreve quais são os grupos de contentores e os tipos de cenários que permitem.

## <a name="what-is-a-container-group"></a>O que é um grupo de contentores?

Um grupo de contentores é uma coleção de contentores que são programados na mesma máquina hospedeira. Os contentores de um grupo de contentores partilham um ciclo de vida, recursos, rede local e volumes de armazenamento. É semelhante em conceito a um *casulo* em [Kubernetes.][kubernetes-pod]

O diagrama a seguir mostra um exemplo de um grupo de contentores que inclui vários contentores:

![Diagrama de grupos de contentores][container-groups-example]

Este grupo de contentores exemplo:

* Está programado numa única máquina hospedeira.
* É atribuída uma etiqueta de nome DNS.
* Expõe um único endereço IP público, com uma porta exposta.
* É composto por dois contentores. Um contentor ouve no porto 80, enquanto o outro ouve no porto 5000.
* Inclui duas ações de ficheiroS Azure como suportes de volume, e cada recipiente monta uma das ações localmente.

> [!NOTE]
> Atualmente, os grupos multi-contentores suportam apenas contentores Linux. Para os recipientes do Windows, as instâncias do contentor Azure suportam apenas a colocação de uma única instância de contentor. Enquanto estamos a trabalhar para trazer todas as funcionalidades para os contentores do Windows, pode encontrar as diferenças atuais da plataforma na [Visão Geral](container-instances-overview.md#linux-and-windows-containers)do serviço.

## <a name="deployment"></a>Implementação

Aqui estão duas formas comuns de implantar um grupo multi-contentor: use um [modelo de Gestor de Recursos][resource-manager template] ou um ficheiro [YAML][yaml-file]. Recomenda-se um modelo de Gestor de Recursos quando é necessário implementar recursos adicionais de serviço Azure (por exemplo, uma [partilha de Ficheiros Azure][azure-files]) quando implementar as instâncias do contentor. Devido à natureza mais concisa do formato YAML, recomenda-se um ficheiro YAML quando a sua implantação inclui apenas casos de contentores. Para obter detalhes sobre as propriedades que pode definir, consulte a referência do [modelo do Gestor de Recursos](/azure/templates/microsoft.containerinstance/containergroups) ou a documentação de referência [YAML.](container-instances-reference-yaml.md)

Para preservar a configuração de um grupo de contentores, pode exportar a configuração para um ficheiro YAML utilizando a exportação de [contentores][az-container-export]Azure CLI . A exportação permite-lhe armazenar as configurações do grupo de contentores no controlo de versão para "configuração como código". Ou, utilize o ficheiro exportado como ponto de partida ao desenvolver uma nova configuração em YAML.



## <a name="resource-allocation"></a>Alocação de recursos

A Azure Container Instances atribui recursos como CPUs, memória e [GPUs][gpus] opcionalmente (pré-visualização) a um grupo multi-contentores, adicionando os [pedidos][resource-requests] de recursos das instâncias no grupo. Tomando os recursos da CPU como exemplo, se criarmos um grupo de contentores com duas instâncias de contentores, cada um solicitando 1 CPU, então o grupo de contentores é atribuído 2 CPUs.

### <a name="resource-usage-by-container-instances"></a>Utilização de recursos por instâncias de contentores

Cada instância de contentor num grupo é atribuída aos recursos especificados no seu pedido de recursos. No entanto, os recursos máximos utilizados por uma instância de contentor num grupo podem ser diferentes se configurar a sua propriedade [de limite de recursos][resource-limits] opcional. O limite de recursos de uma instância de contentor deve ser superior ou igual ao imóvel obrigatório [de pedido de recursos.][resource-requests]

* Se não especificar um limite de recursos, o uso máximo de recursos da instância do recipiente é o mesmo que o seu pedido de recursos.

* Se especificar um limite para uma instância de contentor, a utilização máxima da instância pode ser maior do que o pedido, até ao limite definido. Consequentemente, a utilização de recursos por outras instâncias de contentores no grupo pode diminuir. O limite máximo de recursos que pode definir para uma instância de contentor é o total de recursos atribuídos ao grupo.
    
Por exemplo, num grupo com duas instâncias de contentores cada um solicitando 1 CPU, um dos seus recipientes pode executar uma carga de trabalho que requer mais CPUs para executar do que o outro.

Neste cenário, pode estabelecer um limite de recursos de até 2 CPUs para a instância do contentor. Esta configuração permite que a instância do recipiente utilize até 2 CPUs se disponível.

> [!NOTE]
> Uma pequena quantidade de recursos de um grupo de contentores é utilizada pela infraestrutura subjacente do serviço. Os seus contentores poderão aceder à maioria, mas não a todos os recursos atribuídos ao grupo. Por esta razão, planeie um pequeno tampão de recursos ao solicitar recursos para contentores no grupo.

### <a name="minimum-and-maximum-allocation"></a>Atribuição mínima e máxima

* Aloque um **mínimo** de 1 CPU e 1 GB de memória a um grupo de contentores. As instâncias individuais de contentores dentro de um grupo podem ser a provisionadas com menos de 1 CPU e 1 GB de memória. 

* Para obter os recursos **máximos** de um grupo de contentores, consulte a [disponibilidade de recursos][region-availability] para instâncias de contentores Azure na região de implantação.

## <a name="networking"></a>Redes

Os grupos de contentores podem partilhar um endereço IP virado para o exterior, uma ou mais portas nesse endereço IP e uma etiqueta DNS com um nome de domínio totalmente qualificado (FQDN). Para permitir que clientes externos cheguem a um contentor dentro do grupo, deve expor a porta no endereço IP e a partir do contentor. O endereço IP de um grupo de contentores e fQDN são libertados quando o grupo de contentores é eliminado. 

Dentro de um grupo de contentores, as instâncias de contentores podem chegar entre si através de uma localidade em qualquer porto, mesmo que essas portas não estejam expostas externamente no endereço IP do grupo ou a partir do contentor.

Opcionalmente, coloque grupos de contentores numa [rede virtual Azure][virtual-network] para permitir que os contentores se comuniquem de forma segura com outros recursos na rede virtual.

## <a name="storage"></a>Armazenamento

Pode especificar volumes externos para montar dentro de um grupo de contentores. Os volumes suportados incluem:
* [Azure partilha de ficheiros][azure-files]
* [Segredo][secret]
* [Diretório vazio][empty-directory]
* [Repo de git clonado][volume-gitrepo]

Pode mapear esses volumes em caminhos específicos dentro dos contentores individuais de um grupo. 

## <a name="common-scenarios"></a>Cenários comuns

Os grupos multi-contentores são úteis nos casos em que se pretende dividir uma única tarefa funcional num pequeno número de imagens de contentores. Estas imagens podem então ser entregues por diferentes equipas e têm requisitos de recursos separados.

O uso de exemplos pode incluir:

* Um recipiente que serve uma aplicação web e um recipiente que retira o conteúdo mais recente do controlo de origem.
* Um recipiente de aplicação e um contentor de madeira. O contentor de madeira recolhe os registos e métricas de saída pela aplicação principal e escreve-os para armazenamento a longo prazo.
* Um recipiente de aplicação e um recipiente de monitorização. O recipiente de monitorização faz periodicamente um pedido à aplicação para garantir que está a funcionar e a responder corretamente, e levanta um alerta se não estiver.
* Um recipiente frontal e um recipiente de back-end. A parte frontal pode servir uma aplicação web, com a parte de trás a executar um serviço para recuperar dados. 

## <a name="next-steps"></a>Passos seguintes

Saiba como implantar um grupo de contentores multi-contentores com um modelo de Gestor de Recursos Azure:

> [!div class="nextstepaction"]
> [Implantar um grupo de contentores][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/

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
