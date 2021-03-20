---
title: Alta disponibilidade para recipientes Azure SQL Edge - Azure SQL Edge
description: Saiba mais sobre a alta disponibilidade para recipientes Azure SQL Edge
keywords: SQL Edge, contentores, alta disponibilidade
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 56b9f06547f737bc05d573f98ce1dbac2ba48758
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90937626"
---
# <a name="high-availability-for-azure-sql-edge-containers"></a>Alta disponibilidade para recipientes Azure SQL Edge

Crie e gere os seus casos Azure SQL Edge de forma nativa em Kubernetes. Desdobre a borda Azure SQL para recipientes de estivadores geridos pela [Kubernetes](https://kubernetes.io/). Em Kubernetes, um recipiente com uma instância Azure SQL Edge pode recuperar automaticamente no caso de um nó de cluster falhar. Pode configurar a imagem do recipiente SQL Edge com uma reivindicação de volume persistente de Kubernetes (PVC). Kubernetes monitoriza o processo Azure SQL Edge no recipiente. Se o processo, a vagem, o recipiente ou o nó falharem, a Kubernetes bloqueia automaticamente outra instância e reconecta-se ao armazenamento.

## <a name="azure-sql-edge-containers-on-kubernetes"></a>Recipientes Azure SQL Edge em Kubernetes

Kubernetes 1.6 e mais tarde tem suporte para [*aulas de armazenamento,*](https://kubernetes.io/docs/concepts/storage/storage-classes/) [*reivindicações persistentes de volume*](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims).

Nesta configuração, Kubernetes desempenha o papel de orquestrador de contentores. 

![Diagrama de Azure SQL Edge em um cluster Kubernetes](media/deploy-kubernetes/kubernetes-sql-edge.png)

No diagrama anterior, `azure-sql-edge` encontra-se um recipiente numa [vagem.](https://kubernetes.io/docs/concepts/workloads/pods/pod/) Kubernetes orquestra os recursos no cluster. Um [conjunto de réplica](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) garante que a cápsula é automaticamente recuperada após uma falha no nó. As aplicações ligam-se ao serviço. Neste caso, o serviço representa um equilibrador de carga que acolhe um endereço IP que permanece o mesmo após a falha do `azure-sql-edge` .

No diagrama seguinte, o `azure-sql-edge` recipiente falhou. Como orquestrador, Kubernetes garante a contagem correta de instâncias saudáveis no conjunto de réplicas, e inicia um novo recipiente de acordo com a configuração. O orquestrador inicia uma nova cápsula no mesmo nó, e `azure-sql-edge` reconecta-se com o mesmo armazenamento persistente. O serviço liga-se à `azure-sql-edge` recriação.

![Azure SQL Edge em um cluster Kubernetes após falha do pod](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

No diagrama seguinte, o nó que alberga o `azure-sql-edge` recipiente falhou. O orquestrador inicia a nova cápsula num nó diferente, e `azure-sql-edge` reconecta-se com o mesmo armazenamento persistente. O serviço liga-se à `azure-sql-edge` recriação.

![Azure SQL Edge em um cluster Kubernetes após falha no nó](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png).

Para criar um recipiente em Kubernetes, consulte [implementar um recipiente Azure SQL Edge em Kubernetes](deploy-Kubernetes.md)

## <a name="next-steps"></a>Passos seguintes

Para implantar recipientes Azure SQL Edge no Serviço Azure Kubernetes (AKS), consulte os seguintes artigos:
- [Implementar um recipiente Azure SQL Edge em Kubernetes](deploy-Kubernetes.md)
- [Machine Learning e Inteligência Artificial com ONNX em SQL Edge](onnx-overview.md).
- [Construção de uma solução IoT de ponta a ponta com aresta SQL utilizando IoT Edge](tutorial-deploy-azure-resources.md).
- [Streaming de dados em Azure SQL Edge](stream-data.md)