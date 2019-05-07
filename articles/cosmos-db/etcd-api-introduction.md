---
title: Introdução ao etcd Azure Cosmos DB API
description: Este artigo fornece uma descrição geral e chave os benefícios de etcd API no Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 30d18e1e23767ce3a179221714e001574f8e9778
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075494"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Introdução ao etcd Azure Cosmos DB API (pré-visualização)

O Azure Cosmos DB é um serviço da base de dados de vários modelos distribuída globalmente da Microsoft para aplicativos de missão crítica. Ele oferece distribuição global chave na mão, dimensionamento de débito e armazenamento, latências de milissegundo de dígito no percentil 99, elástica e elevada disponibilidade garantida, tudo isto suportado por SLAS líderes do setor.

[Etcd](https://github.com/etcd-io/etcd) é um arquivo de chave/valor distribuída. Na [Kubernetes](https://kubernetes.io/), etcd é utilizada para armazenar o estado e a configuração de clusters de Kubernetes. Garantir a disponibilidade, confiabilidade e desempenho de etcd é crucial para o estado de funcionamento geral do cluster, a escalabilidade, a disponibilidade de elasticidade e o desempenho de um cluster de Kubernetes. 

O etcd API no Azure Cosmos DB permite-lhe utilizar o Azure Cosmos DB como o armazenamento de back-end para [Kubernetes do Azure](../aks/index.yml). etcd API no Azure Cosmos DB está atualmente em pré-visualização. O Azure Cosmos DB implementa o protocolo de etcd. Com etcd API no Azure Cosmos DB, os desenvolvedores tirarão automaticamente altamente confiáveis [disponíveis](high-availability.md), [distribuídas globalmente](distribute-data-globally.md) Kubernetes. Esta API permite aos desenvolvedores dimensionar a gestão de estado de Kubernetes num serviço PaaS nativo totalmente gerido na cloud. 

> [!NOTE]
> Ao contrário de outras APIs no Azure Cosmos DB, não pode aprovisionar uma conta da API etcd através do portal do Azure, CLI ou SDKs. Pode aprovisionar uma conta da API etcd ao implementar o modelo de Gestor de recursos apenas; Para obter passos detalhados, consulte [como aprovisionar Kubernetes do Azure com o Azure Cosmos DB](bootstrap-kubernetes-cluster.md) artigo.  

## <a name="wire-level-compatibility"></a>Compatibilidade de nível durante a transmissão

O Azure Cosmos DB implementa o protocolo de transmissão de etcd versão 3 e permite que o [do nó principal](https://kubernetes.io/docs/concepts/overview/components/) servidores de API a utilizar do Azure Cosmos DB, tal como faria num ambiente de etcd instalado localmente. O etcd API suporta a autenticação mútua de TLS. 

O diagrama seguinte mostra os componentes de um cluster de Kubernetes. No ramo principal do cluster, o servidor de API utiliza API do Azure Cosmos DB etcd, em vez de etcd instalado localmente. 

![O Azure Cosmos DB, implementando o protocolo de transmissão de etcd](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Principais vantagens

### <a name="no-etcd-operations-management"></a>Sem gestão de operações etcd

Como um serviço cloud totalmente gerido de nativo, o Azure Cosmos DB remove a necessidade dos desenvolvedores do Kubernetes configurar e gerir etcd. O etcd API no Azure Cosmos DB é dimensionável e com elevada disponibilidade, tolerante a falhas e oferece um desempenho elevado. A sobrecarga de configuração de replicação em vários nós, efetuar sem interrupção de atualizações, patches de segurança, e monitorizar o estado de funcionamento etcd são manipuladas pelo Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Distribuição global e de elevada disponibilidade 

Ao utilizar a API de etcd, o Azure Cosmos DB garante a disponibilidade de 99,99% de leituras de dados e escreve numa região única e 99,999% de disponibilidade em várias regiões. 

### <a name="elastic-scalability"></a>Escalabilidade elástica

O Azure Cosmos DB oferece escalabilidade elástica para leitura e pedidos de escrita em diferentes regiões.
À medida que aumenta o cluster de Kubernetes, a conta de etcd API no Azure Cosmos DB dimensiona elasticamente sem qualquer período de inatividade. Armazenar dados de etcd no Azure Cosmos DB, em vez dos nós principais do Kubernetes também lhe permite dimensionar mais flexível do nó principal. 

### <a name="security--enterprise-readiness"></a>Preparação de segurança e enterprise

Quando etcd dados são armazenados no Azure Cosmos DB, os desenvolvedores de Kubernetes obtém automaticamente a [internos de criptografia em repouso](database-encryption-at-rest.md), [certificações e conformidade](compliance.md), e [backup e restauração capacidades](online-backup-and-restore.md) suportado pelo Azure Cosmos DB. 

## <a name="next-steps"></a>Passos Seguintes

* [Como utilizar o Kubernetes do Azure com o Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Principais benefícios do Azure Cosmos DB](introduction.md)
* [Guia de início rápido do motor AKS](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)