---
title: Introdução a Azure Managed Instance para Apache Cassandra
description: Saiba mais sobre Azure Managed Instance para Apache Cassandra. Este serviço gere a implantação e dimensionamento de casos nativos de código aberto de Apache Cassandra em Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 03/02/2021
ms.openlocfilehash: d99c62e7d88510c351f87d85b4f8c5c271767cb3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101748931"
---
# <a name="what-is-azure-managed-instance-for-apache-cassandra-preview"></a>Qual é a instância gerida por Azure para Apache Cassandra? (Pré-visualização)

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O serviço Azure Managed Instance for Apache Cassandra fornece operações automatizadas de implantação e escala para centros de dados apaches de código aberto geridos. Este serviço ajuda-o a acelerar cenários híbridos e a reduzir a manutenção contínua. Terá capacidades de integração profunda e movimento de dados com [a Azure Cosmos DB Cassandra API](../cosmos-db/cassandra-introduction.md) após a sua libertação geral.

:::image type="content" source="./media/introduction/icon.gif" alt-text="Azure Managed Instance for Apache Cassandra é um serviço gerido para Apache Cassandra." border="false":::

## <a name="key-benefits"></a>Principais vantagens

### <a name="hybrid-deployments"></a>Implementações híbridas

Pode utilizar este serviço para colocar facilmente casos geridos de datacenters Apache Cassandra, que são implantados automaticamente como conjuntos de escala de máquina virtual, numa rede virtual Azure nova ou existente. Estes centros de dados podem ser adicionados ao seu anel Apache Cassandra existente que funciona no local através do [Azure ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute) em Azure, ou a outro ambiente em nuvem.

- **Implantação simplificada:** Após a conectividade híbrida ser estabelecida, a implementação é fácil através do protocolo de fofocas.
- **Métricas hospedadas:** As métricas estão hospedadas em [Prometeu](https://prometheus.io/docs/introduction/overview/) para monitorizar a atividade em todo o seu cluster.

### <a name="simplified-scaling"></a>Escalagem simplificada

No caso gerido, a escalada e a escala dos nós num datacenter são totalmente geridos. Insira o número de nós que precisa, e o orquestrador de escalas cuida de estabelecer a sua operação dentro do anel Cassandra.

### <a name="managed-and-cost-effective"></a>Gerido e rentável

O serviço fornece operações de gestão para as seguintes tarefas comuns da Apache Cassandra:

- Provisionamento de um cluster
- Fornecimento de um datacenter
- Escalar um datacenter
- Eliminar um datacenter
- Inicie uma ação de reparação num espaço-chave
- Alterar configuração de um datacenter
- Backups de configuração

O modelo de preços é flexível, a pedido, baseado em casos, e não tem taxas de licenciamento. Este modelo de preços permite-lhe adaptar-se às suas necessidades específicas de carga de trabalho. Você escolhe quantos núcleos, que VM SKU, que tamanho de memória, e o tamanho do espaço do disco que você precisa.

## <a name="next-steps"></a>Passos seguintes

Começa com um dos nossos quickstarts:

* [Criar um cluster de instâncias gerido a partir do portal Azure](create-cluster-portal.md)
* [Implementar um Cluster de Faíscas Apache Gerido com Tijolos de Dados Azure](deploy-cluster-databricks.md)
* [Gerir a Azure Managed Instance para os recursos da Apache Cassandra utilizando o Azure CLI](manage-resources-cli.md)
