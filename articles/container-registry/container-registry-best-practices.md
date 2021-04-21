---
title: Registar melhores práticas
description: Saiba como utilizar o registo de contentor do Azure de forma eficiente, ao seguir estas melhores práticas.
ms.topic: article
ms.date: 01/07/2021
ms.openlocfilehash: 0811cc4a5bffc21ffba19e64a3887eab6bc36fbb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784142"
---
# <a name="best-practices-for-azure-container-registry"></a>Melhores práticas do Azure Container Registry

Ao seguir estas boas práticas, pode ajudar a maximizar o desempenho e a utilização rentável do seu registo privado em Azure para armazenar e implantar imagens de contentores e outros artefactos.

Para obter antecedentes sobre conceitos de registo, consulte [sobre registos, repositórios e imagens.](container-registry-concepts.md) Consulte também [recomendações para a marcação e versão de imagens de contentores](container-registry-image-tag-version.md) para estratégias para etiquetar e versagens no seu registo. 

## <a name="network-close-deployment"></a>Implementação sem rede

Crie o registo de contentor na mesma região do Azure em que implementa contentores. Colocar o seu registo numa região sem rede para os anfitriões do seu contentor pode ajudar a reduzir a latência e o custo.

A implementação sem rede é uma das principais razões para utilizar um registo de contentor privado. As imagens do Docker têm uma [construção em camadas](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) eficiente que permite implementações incrementais. No entanto, os novos nós têm de solicitar todas as camadas necessárias para uma determinada imagem. Este `docker pull` inicial pode adicionar rapidamente até vários gigabytes. Ter um registo privado próximo à sua implementação minimiza a latência de rede.
Além disso, todas as clouds públicas, o Azure incluído, implementam as taxas de saída da rede. Extrair imagens de um datacenter para outro, adiciona taxas de saída da rede além da latência.

## <a name="geo-replicate-multi-region-deployments"></a>Georreplicar implementações em várias regiões

Utilize a funcionalidade de [georreplicação](container-registry-geo-replication.md) do Azure Container Registry, se estiver a implementar contentores em várias regiões. Se estiver a servir clientes globais de datacenters locais ou a sua equipa de desenvolvimento estiver em diferentes localizações, pode simplificar a gestão dos registos e minimizar a latência através da georreplicação do seu registo. Também pode configurar [webhooks](container-registry-webhook.md) regionais para notificá-lo de eventos em réplicas específicas, como quando as imagens são empurradas.

A geo-replicação está disponível com registos [Premium.](container-registry-skus.md) Para saber como utilizar a georreplicação, veja o tutorial de três partes [Geo-replication in Azure Container Registry (Georreplicação no Azure Container Registry)](container-registry-tutorial-prepare-registry.md).

## <a name="maximize-pull-performance"></a>Maximizar o desempenho da puxar

Além de colocar imagens perto das suas implementações, as características das suas próprias imagens podem ter impacto no desempenho da atração.

* **Tamanho da imagem** - Minimize os tamanhos das suas imagens removendo [camadas desnecessárias](container-registry-concepts.md#manifest) ou reduzindo o tamanho das camadas. Uma maneira de reduzir o tamanho da imagem é usar a abordagem [de construção de Docker em várias fases](https://docs.docker.com/develop/develop-images/multistage-build/) para incluir apenas os componentes de tempo de execução necessários. 

  Verifique também se a sua imagem pode incluir uma imagem de SO base mais leve. E se utilizar um ambiente de implantação como o Azure Container Instances que cache certas imagens base, verifique se pode trocar uma camada de imagem por uma das imagens em cache. 
* **Número de camadas** - Equilibre o número de camadas utilizadas. Se tiver muito poucos, não se beneficia da reutilização da camada e do caching no hospedeiro. Demasiados, e o seu ambiente de implantação passa mais tempo puxando e descomprimindo. Cinco a 10 camadas é o ideal.

Escolha também um [nível](container-registry-skus.md) de serviço do Registo de Contentores Azure que satisfaça as suas necessidades de desempenho. O nível Premium fornece a maior largura de banda e a maior taxa de operações de leitura e escrita simultâneas quando se tem implementações de grande volume.

## <a name="repository-namespaces"></a>Espaços de nomes do repositório

Ao utilizar espaços de nomes de repositório, pode permitir a partilha de um único registo em vários grupos dentro da sua organização. Os registos podem ser partilhados em implementações e equipas. O Azure Container Registry suporta espaços de nomes aninhados, ao ativar o isolamento de grupo. No entanto, o registo gere todos os repositórios de forma independente, não como uma hierarquia.

Por exemplo, considere as seguintes etiquetas da imagem de contentor. As imagens que são usadas em toda `aspnetcore` a empresa, como, são colocadas no espaço de nomes de raiz, enquanto as imagens de contentores pertencentes aos grupos de Produtos e Marketing usam cada uma as suas próprias espaços com nome.

- *contoso.azurecr.io/aspnetcore:2.0*
- *contoso.azurecr.io/products/widget/web:1*
- *contoso.azurecr.io/products/bettermousetrap/refundapi:12.3*
- *contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42*

## <a name="dedicated-resource-group"></a>Grupo de recursos dedicado

Como os registos de contentores são recursos que são utilizados em vários hospedeiros de contentores, um registo deve residir no seu próprio grupo de recursos.

Embora possa experimentar um tipo específico de hospedeiro, como [instâncias de contentores Azure,](../container-instances/container-instances-overview.md)é provável que queira apagar a instância do recipiente quando terminar. No entanto, também pode manter a coleção de imagens enviadas para o Azure Container Registry. Ao colocar o seu registo no seu próprio grupo de recursos, está a minimizar o risco de eliminar acidentalmente a coleção de imagens no registo, ao eliminar o grupo de recursos de instância do contentor.

## <a name="authentication-and-authorization"></a>Autenticação e autorização

Ao autenticar com um registo de contentor do Azure, existem dois cenários principais: autenticação individual e autenticação de serviço (ou "sem interface"). A tabela seguinte apresenta uma breve descrição geral destes cenários e o método de autenticação recomendado para cada um.

| Tipo | Cenário de exemplo | Método recomendado |
|---|---|---|
| Identidade individual | Um programador a extrair imagens ou enviar imagens a partir da respetiva máquina de desenvolvimento. | [az acr login](/cli/azure/acr#az_acr_login) |
| Identidade de serviço/sem interface | Compile e implemente pipelines onde o utilizador não esteja diretamente envolvido. | [Service principal (Principal de serviço)](container-registry-authentication.md#service-principal) |

Para obter informações aprofundadas sobre estes e outros cenários de autenticação do Registo do Contentor Azure, consulte [Authenticate com um registo de contentores Azure](container-registry-authentication.md).

O Registo de Contentores Azure suporta práticas de segurança na sua organização para distribuir deveres e privilégios a diferentes identidades. Utilizando [o controlo de acesso baseado em funções,](container-registry-roles.md)atribua permissões apropriadas a diferentes utilizadores, principais de serviços ou outras identidades que realizem diferentes operações de registo. Por exemplo, atribua permissões de pressão a um principiante de serviço utilizado num oleoduto de construção e atribua permissões de puxar para uma identidade diferente usada para implantação. Criar [fichas](container-registry-repository-scoped-permissions.md) para acesso fino e limitado a repositórios específicos.

## <a name="manage-registry-size"></a>Gerir o tamanho do registo      

Os constrangimentos de armazenamento de cada [nível][container-registry-skus] de serviço de registo de contentores destinam-se a alinhar-se com um cenário típico: **Básico** para começar, **Standard** para a maioria das aplicações de produção, e **Premium** para desempenho e [geo-replicação][container-registry-geo-replication]de hiper-escala. Ao longo da vida do registo, deve gerir o tamanho eliminando periodicamente o conteúdo não utilizado.

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

O Registo de Contentores Azure está disponível em vários níveis (também chamados SKUs) que fornecem diferentes capacidades. Para mais informações sobre os níveis de serviço disponíveis, consulte os [níveis de serviço do Registo de Contentores Azure](container-registry-skus.md).

Para obter recomendações para melhorar a postura de segurança dos seus registos de contentores, consulte [a Linha de Base de Segurança Azure para o Registo do Contentor de Azure](security-baseline.md).

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-acr-show-usage]: /cli/azure/acr#az_acr_show_usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md