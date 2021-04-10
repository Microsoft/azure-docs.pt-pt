---
title: Diferenças entre Azure Managed Instance para Apache Cassandra e Azure Cosmos DB Cassandra API
description: Conheça as diferenças entre Azure Managed Instance para Apache Cassandra e Cassandra API em Azure Cosmos DB. Você também aprende os benefícios de cada um destes serviços e quando escolhê-los.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 3050dda4b3c0e1a05d751a4f5969bba69ad0506d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101749015"
---
# <a name="differences-between-azure-managed-instance-for-apache-cassandra-preview-and-azure-cosmos-db-cassandra-api"></a>Diferenças entre Azure Managed Instance for Apache Cassandra (Preview) e Azure Cosmos DB Cassandra API 

Neste artigo, você vai aprender as diferenças entre Azure Managed Instance para Apache Cassandra e a Cassandra API em Azure Cosmos DB. Este artigo fornece recomendações sobre como escolher entre os dois serviços, ou quando acolher o seu próprio ambiente Apache Cassandra.

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-differences"></a>Diferenças principais

Azure Managed Instance for Apache Cassandra fornece implantação automatizada, escala e operações para manter a saúde do nó para instâncias Apache Cassandra de código aberto em Azure. Também fornece a capacidade de aumentar a capacidade de aglomerados apache cassandra existentes no local ou nuvem auto-hospedada. Ele escala adicionando centros de dados geridos Cassandra ao anel de cluster existente.

A [Cassandra API](../cosmos-db/cassandra-introduction.md) em Azure Cosmos DB é uma camada de compatibilidade sobre o serviço de base de dados nativo da Microsoft, [Azure Cosmos DB.](../cosmos-db/index.yml) A combinação destes serviços em Azure proporciona um contínuo de escolhas para os utilizadores de Apache Cassandra em ambientes complexos de nuvem híbrida.

## <a name="how-to-choose"></a>Como escolher?

O quadro a seguir mostra os cenários comuns, os requisitos de carga de trabalho e as aspirações em que cada uma destas abordagens de implantação se enquadra:

| |Apache Cassandra auto-hospedado no local ou em Azure | Azure Managed Instance for Apache Cassandra | API para Cassandra do Azure Cosmos DB |
|---------|---------|---------|---------|
|**Tipo de implantação**| Você tem uma implementação Apache Cassandra altamente personalizada com patches personalizados ou bufos. | Tem uma implantação apache Cassandra de código aberto padrão sem qualquer código personalizado. | Está satisfeito com uma plataforma que não é Apache Cassandra por baixo, mas está em conformidade com todos os motoristas de clientes de código aberto a um nível [de protocolo de fio.](../cosmos-db/cassandra-support.md) |
| **Sobrecarga operacional**| Tens especialistas em Cassandra que podem implementar, configurar e manter os teus agrupamentos.  | Pretende baixar a sobrecarga operacional para a sua saúde do nó Apache Cassandra, mas ainda assim manter o controlo sobre as configurações do nível da plataforma, tais como replicação e consistência. | Pretende eliminar a sobrecarga operacional utilizando uma base de dados de plataforma como serviço totalmente gerida na nuvem. |
| **Requisitos do sistema operativo**| Tem a obrigação de manter imagens personalizadas ou douradas do sistema operativo Virtual Machine. | Pode usar imagens de baunilha mas quer ter controlo sobre SKUs, memória, discos e IOPS. | Pretende que o fornecimento de capacidade seja simplificado e expresso como uma única métrica normalizada, com uma relação um-para-um com a produção, como [unidades](../cosmos-db/request-units.md) de pedido em Azure Cosmos DB. |
| **Modelo de preços**| Pretende utilizar software de gestão como a ferramenta Datastax e está satisfeito com os custos de licenciamento. | Prefere o licenciamento puro de código aberto e os preços baseados em VM. | Pretende utilizar preços nativos da nuvem, que inclui [ofertas de escala automática](../cosmos-db/manage-scale-cassandra.md#use-autoscale) e [sem servidor.](../cosmos-db/serverless.md) |
| **Análise**| Você quer controlo total sobre o fornecimento de gasodutos analíticos, independentemente das despesas gerais para construí-los e mantê-los. | Você quer usar serviços analíticos baseados em nuvem como Azure Databricks. | Você quer análises transacionais híbridas quase em tempo real incorporadas na plataforma com [Azure Synapse Link para Cosmos DB](../cosmos-db/synapse-link.md). |
| **Padrão de carga de trabalho**| A sua carga de trabalho é bastante estável e não precisa de nómada de escalonamento no aglomerado frequentemente. | A sua carga de trabalho é volátil e precisa de ser capaz de escalar ou escalar os nós num centro de dados ou adicionar/remover facilmente os centros de dados. | A sua carga de trabalho é muitas vezes volátil e precisa de ser capaz de escalar ou escalar rapidamente e a um volume significativo. |
| **SLAs**| Está satisfeito com os seus processos de manutenção de SLAs sobre consistência, produção, disponibilidade e recuperação de desastres. | Você está feliz com os seus processos para manter SLAs em consistência, produção e disponibilidade, mas precisa de ajuda com backups. | Você quer SLAs totalmente abrangentes sobre consistência, produção, disponibilidade e recuperação de desastres. |

## <a name="next-steps"></a>Passos seguintes

Começa com um dos nossos quickstarts:

* [Criar um cluster de instâncias gerido a partir do portal Azure](create-cluster-portal.md)