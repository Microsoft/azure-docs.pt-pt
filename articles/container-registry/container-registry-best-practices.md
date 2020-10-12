---
title: Registar melhores práticas
description: Saiba como utilizar o registo de contentor do Azure de forma eficiente, ao seguir estas melhores práticas.
ms.topic: article
ms.date: 09/27/2018
ms.openlocfilehash: fc84fb8cb98f58e28570095370d55a7358ce3a99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83682677"
---
# <a name="best-practices-for-azure-container-registry"></a>Melhores práticas do Azure Container Registry

Ao seguir estas melhores práticas, pode ajudar a maximizar o desempenho e a utilização rentável do seu registo privado do Docker no Azure.

Consulte também [recomendações para a marcação e versão de imagens de contentores](container-registry-image-tag-version.md) para estratégias para etiquetar e versagens no seu registo. 

## <a name="network-close-deployment"></a>Implementação sem rede

Crie o registo de contentor na mesma região do Azure em que implementa contentores. Colocar o seu registo numa região sem rede para os anfitriões do seu contentor pode ajudar a reduzir a latência e o custo.

A implementação sem rede é uma das principais razões para utilizar um registo de contentor privado. As imagens do Docker têm uma [construção em camadas](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) eficiente que permite implementações incrementais. No entanto, os novos nós têm de solicitar todas as camadas necessárias para uma determinada imagem. Este `docker pull` inicial pode adicionar rapidamente até vários gigabytes. Ter um registo privado próximo à sua implementação minimiza a latência de rede.
Além disso, todas as clouds públicas, o Azure incluído, implementam as taxas de saída da rede. Extrair imagens de um datacenter para outro, adiciona taxas de saída da rede além da latência.

## <a name="geo-replicate-multi-region-deployments"></a>Georreplicar implementações em várias regiões

Utilize a funcionalidade de [georreplicação](container-registry-geo-replication.md) do Azure Container Registry, se estiver a implementar contentores em várias regiões. Se estiver a servir clientes globais de datacenters locais ou a sua equipa de desenvolvimento estiver em diferentes localizações, pode simplificar a gestão dos registos e minimizar a latência através da georreplicação do seu registo. A georreplicação está disponível apenas nos registos [Premium](container-registry-skus.md).

Para saber como utilizar a georreplicação, veja o tutorial de três partes [Geo-replication in Azure Container Registry (Georreplicação no Azure Container Registry)](container-registry-tutorial-prepare-registry.md).

## <a name="repository-namespaces"></a>Espaços de nomes do repositório

Ao tirar partido dos espaços de nomes do repositório, pode permitir a partilha de um único registo em vários grupos na sua organização. Os registos podem ser partilhados em implementações e equipas. O Azure Container Registry suporta espaços de nomes aninhados, ao ativar o isolamento de grupo.

Por exemplo, considere as seguintes etiquetas da imagem de contentor. As imagens que são usadas em toda `aspnetcore` a empresa, como, são colocadas no espaço de nomes de raiz, enquanto as imagens de contentores pertencentes aos grupos de Produtos e Marketing usam cada uma as suas próprias espaços com nome.

- *contoso.azurecr.io/aspnetcore:2.0*
- *contoso.azurecr.io/products/widget/web:1*
- *contoso.azurecr.io/products/bettermousetrap/refundapi:12.3*
- *contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42*

## <a name="dedicated-resource-group"></a>Grupo de recursos dedicado

Como os registos de contentores são recursos que são utilizados em vários hospedeiros de contentores, um registo deve residir no seu próprio grupo de recursos.

Embora possa experimentar um tipo de anfitrião específico, como o Azure Container Instances, irá provavelmente eliminar a instância do contentor quando tiver terminado. No entanto, também pode manter a coleção de imagens enviadas para o Azure Container Registry. Ao colocar o seu registo no seu próprio grupo de recursos, está a minimizar o risco de eliminar acidentalmente a coleção de imagens no registo, ao eliminar o grupo de recursos de instância do contentor.

## <a name="authentication"></a>Autenticação

Ao autenticar com um registo de contentor do Azure, existem dois cenários principais: autenticação individual e autenticação de serviço (ou "sem interface"). A tabela seguinte apresenta uma breve descrição geral destes cenários e o método de autenticação recomendado para cada um.

| Tipo | Cenário de exemplo | Método recomendado |
|---|---|---|
| Identidade individual | Um programador a extrair imagens ou enviar imagens a partir da respetiva máquina de desenvolvimento. | [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) |
| Identidade de serviço/sem interface | Compile e implemente pipelines onde o utilizador não esteja diretamente envolvido. | [Diretor de serviços](container-registry-authentication.md#service-principal) |

Para obter informações aprofundadas sobre a autenticação do Azure Container Registry, veja [Authenticate with an Azure container registry (Autenticar com um registo de contentor do Azure)](container-registry-authentication.md).

## <a name="manage-registry-size"></a>Gerir o tamanho do registo

Os constrangimentos de armazenamento de cada nível de serviço de registo de [contentores][container-registry-skus] destinam-se a alinhar-se com um cenário típico: **Básico** para começar, **Standard** para a maioria das aplicações de produção, e **Premium** para desempenho em hiper-escala e [geo-replicação.][container-registry-geo-replication] Ao longo da vida do registo, deve gerir o tamanho eliminando periodicamente o conteúdo não utilizado.

Utilize o comando Azure CLI [az acr show-use][az-acr-show-usage] para mostrar o tamanho atual do seu registo:

```azurecli
az acr show-usage --resource-group myResourceGroup --name myregistry --output table
```

```output
NAME      LIMIT         CURRENT VALUE    UNIT
--------  ------------  ---------------  ------
Size      536870912000  185444288        Bytes
Webhooks  100                            Count
```

Também pode encontrar o armazenamento atual utilizado na **visão geral** do seu registo no portal Azure:

![Informações de utilização do registo no portal do Azure][registry-overview-quotas]

### <a name="delete-image-data"></a>Eliminar dados de imagem

O Registo do Contentor Azure suporta vários métodos para eliminar os dados de imagem do registo do seu contentor. Pode eliminar imagens por marcação ou manifesta digestão ou apagar um repositório inteiro.

Para obter detalhes sobre a eliminação de dados de imagem do seu registo, incluindo imagens não gravadas (por vezes chamadas de "penduradas" ou "órfãs"), consulte [apagar imagens de contentores no Registo do Contentor de Azure](container-registry-delete.md).

## <a name="next-steps"></a>Passos seguintes

O Registo de Contentores Azure está disponível em vários níveis (também chamados SKUs) que cada um fornece diferentes capacidades. Para mais informações sobre os níveis de serviço disponíveis, consulte os [níveis de serviço do Registo de Contentores Azure](container-registry-skus.md).

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-show-usage]: /cli/azure/acr#az-acr-show-usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md
