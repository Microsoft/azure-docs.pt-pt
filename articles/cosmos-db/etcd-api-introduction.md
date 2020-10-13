---
title: Introdução ao Azure Cosmos DB etcd API
description: Este artigo fornece uma visão geral e benefícios chave da API etcd em Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 208f0d73b4c8bb11adc9c73796ebc6a622f5ed50
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85118173"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Introdução ao Azure Cosmos DB etcd API (pré-visualização)

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft para aplicações críticas para atividades. Oferece distribuição global chave na mão, dimensionamento elástico de produção e armazenamento, latências milissegundos de um dígito no percentil 99, e alta disponibilidade garantida, tudo apoiado por SLA líder na indústria.

[Etcd](https://github.com/etcd-io/etcd) é uma loja de chaves/valor distribuídos. Em [Kubernetes,](https://kubernetes.io/)etcd é usado para armazenar o estado e a configuração dos clusters Kubernetes. Garantir disponibilidade, fiabilidade e desempenho do etcd é crucial para a saúde geral do cluster, escalabilidade, disponibilidade de elasticidade e desempenho de um cluster Kubernetes.

A API etcd em Azure Cosmos DB permite-lhe usar a Azure Cosmos DB como loja de backend para Azure Kubernetes. etcD API em Azure Cosmos DB está atualmente em pré-visualização. A Azure Cosmos DB implementa o protocolo de fio etc. Com a API etcd em Azure Cosmos DB, os desenvolvedores obterão automaticamente kubernetes altamente [fiáveis, disponíveis,](high-availability.md) [distribuídos globalmente.](distribute-data-globally.md) Esta API permite que os desenvolvedores escalam a gestão do estado de Kubernetes num serviço PaaS nativo em nuvem totalmente gerido. 

> [!NOTE]
> Ao contrário de outras APIs em Azure Cosmos DB, não é possível providenciar uma conta API etc através do portal Azure, CLI ou SDKs. Pode providenciar uma conta API etcd através da implementação apenas do modelo de Gestor de Recursos; para passos detalhados, ver [Como providenciar Azure Kubernetes com artigo DB Azure Cosmos.](bootstrap-kubernetes-cluster.md) A Azure Cosmos DB etcd API está atualmente em pré-visualização limitada. Pode [inscrever-se para a pré-visualização,](https://aka.ms/cosmosetcdapi-signup)preenchendo o formulário de inscrição.

## <a name="wire-level-compatibility"></a>Compatibilidade do nível do fio

A Azure Cosmos DB implementa o protocolo de fio da versão 3 etcd, e permite que os servidores API [do nó principal](https://kubernetes.io/docs/concepts/overview/components/) utilizem a Azure Cosmos DB tal como faria num ambiente etc instalado localmente. A API etcd suporta a autenticação mútua TLS. 

O diagrama seguinte mostra os componentes de um cluster Kubernetes. No mestre do cluster, o Servidor API utiliza API DB AZure Cosmos, em vez de etc instalado localmente. 

:::image type="content" source="./media/etcd-api-introduction/etcd-api-wire-protocol.png" alt-text="Azure Cosmos DB implementando o protocolo de fio etcd" border="false":::

## <a name="key-benefits"></a>Principais vantagens

### <a name="no-etcd-operations-management"></a>Sem gestão de operações etcd

Como um serviço nativo de nuvem totalmente gerido, a Azure Cosmos DB remove a necessidade de os desenvolvedores de Kubernetes se instalarem e gerirem etcd. A API etcd em Azure Cosmos DB é escalável, altamente disponível, tolerante a falhas, e oferece alto desempenho. A sobrecarga da configuração da replicação através de múltiplos nós, a realização de atualizações rolantes, patches de segurança e monitorização da saúde etcd são manuseadas pela Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Distribuição global & alta disponibilidade 

Ao utilizar a API etcd, a Azure Cosmos DB garante 99,99% de disponibilidade para leituras e escritos de dados numa única região, e 99,999% de disponibilidade em várias regiões. 

### <a name="elastic-scalability"></a>Escalabilidade elástica

A Azure Cosmos DB oferece escalabilidade elástica para ler e escrever pedidos em diferentes regiões.
À medida que o cluster Kubernetes cresce, a conta de API etcd em Azure Cosmos DB escala elasticamente sem qualquer tempo de inatividade. Armazenar dados etcd em Azure Cosmos DB, em vez dos nós mestres Kubernetes também permite uma escala mais flexível do nó principal. 

### <a name="security--enterprise-readiness"></a>Segurança & prontidão da empresa

Quando os dados etcd são armazenados em Azure Cosmos DB, os desenvolvedores da Kubernetes obtêm automaticamente a [encriptação incorporada em repouso,](database-encryption-at-rest.md)  [certificações e conformidade,](compliance.md)e [capacidades de backup e restauro](online-backup-and-restore.md) suportadas pela Azure Cosmos DB. 

## <a name="next-steps"></a>Passos seguintes

* [Como usar Azure Kubernetes com Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Principais benefícios da Azure Cosmos DB](introduction.md)
* [Guia quickstart do motor AKS](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)