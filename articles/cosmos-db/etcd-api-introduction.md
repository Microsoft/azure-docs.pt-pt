---
title: Introdução ao Azure Cosmos DB etcd API
description: Este artigo fornece uma visão geral e benefícios fundamentais da API etcd em Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 16aac5c765c36c49919685ee58e8034786ddf1ae
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797367"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Introdução ao Azure Cosmos DB etcd API (pré-visualização)

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft para aplicações críticas para atividades. Oferece distribuição global chave na mão, escala elástico de produção e armazenamento, latências milissegundos de um dígito no percentil 99, e garantia de alta disponibilidade, tudo apoiado por SLA's líderes do setor.

[Etcd](https://github.com/etcd-io/etcd) é uma loja de chaves/valor distribuída. Em [Kubernetes,](https://kubernetes.io/)etcé é usado para armazenar o estado e a configuração dos clusters Kubernetes. Garantir a disponibilidade, fiabilidade e desempenho do etcd é crucial para a saúde geral do cluster, escalabilidade, disponibilidade de elasticidade e desempenho de um cluster Kubernetes.

A API etcd em Azure Cosmos DB permite-lhe usar o Azure Cosmos DB como reserva para Azure Kubernetes. A API etcd em Azure Cosmos DB está atualmente em pré-visualização. A Azure Cosmos DB implementa o protocolo de arame etcd. Com a API etcd em Azure Cosmos DB, os desenvolvedores obterão automaticamente Kubernetes altamente fiáveis, [disponíveis,](high-availability.md) [distribuídos globalmente.](distribute-data-globally.md) Esta API permite que os desenvolvedores dimensionem a gestão estatal de Kubernetes num serviço PaaS nativo totalmente gerido em nuvem. 

> [!NOTE]
> Ao contrário de outras APIs em Azure Cosmos DB, não é possível fornecer uma conta API etcd através do portal Azure, CLI ou SDKs. Pode fornecer uma conta API etcd implantando apenas o modelo de Gestor de Recursos; para etapas detalhadas, consulte [Como fornecer Azure Kubernetes com](bootstrap-kubernetes-cluster.md) artigo da Azure Cosmos DB. A Azure Cosmos DB etcD API está atualmente em pré-visualização limitada. Pode [inscrever-se para a pré-visualização,](https://aka.ms/cosmosetcdapi-signup)preenchendo o formulário de inscrição.

## <a name="wire-level-compatibility"></a>Compatibilidade do nível do fio

O Azure Cosmos DB implementa o protocolo de arame da versão 3 do etcd, e permite que os servidores API [do nó mestre](https://kubernetes.io/docs/concepts/overview/components/) utilizem o Azure Cosmos DB tal como faria num ambiente instalado localmente, etc. A API etcd suporta a autenticação mútua tLS. 

O diagrama seguinte mostra os componentes de um aglomerado kubernetes. No mestre do cluster, o Servidor API utiliza API Azure Cosmos DB etcd, em vez de instalado localmente etc. 

![Azure Cosmos DB implementando o protocolo de arame etcd](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Principais vantagens

### <a name="no-etcd-operations-management"></a>Sem gestão de operações etcd

Como um serviço de nuvem nativa totalmente gerido, o Azure Cosmos DB elimina a necessidade de os desenvolvedores da Kubernetes configurarem e gerirem etc. A API etcd em Azure Cosmos DB é escalável, altamente disponível, tolerante a falhas, e oferece alto desempenho. A sobrecarga de configuração da replicação em vários nós, realização de atualizações rolantes, patches de segurança e monitorização da saúde etcd são manuseadas pela Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Distribuição global & elevada disponibilidade 

Ao utilizar a API, a Azure Cosmos DB garante 99,99% de disponibilidade para leituras e escritos em uma única região, e 99,999% de disponibilidade em várias regiões. 

### <a name="elastic-scalability"></a>Escalabilidade elástica

A Azure Cosmos DB oferece escalabilidade elástica para ler e escrever pedidos em diferentes regiões.
À medida que o cluster Kubernetes cresce, a conta de API etcd em Azure Cosmos DB escala elásticamente sem qualquer tempo de inatividade. Armazenar dados etcd em Azure Cosmos DB, em vez dos nós mestrekubernetes também permite uma escala mais flexível do nó mestre. 

### <a name="security--enterprise-readiness"></a>Segurança & prontidão da empresa

Quando os dados etcd são armazenados no Azure Cosmos DB, os desenvolvedores da Kubernetes obtêm automaticamente a [encriptação incorporada em repouso](database-encryption-at-rest.md), [certificações e conformidade,](compliance.md)e [capacidades](online-backup-and-restore.md) de backup e restauro suportadas pela Azure Cosmos DB. 

## <a name="next-steps"></a>Passos seguintes

* [Como usar O Azure Kubernetes com O Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Principais benefícios do Azure Cosmos DB](introduction.md)
* [Guia quickstart do motor AKS](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)