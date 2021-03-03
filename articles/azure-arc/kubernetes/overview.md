---
title: Visão geral de Azure Arc habilitado Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/19/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Este artigo fornece uma visão geral de Azure Arc habilitado Kubernetes.
keywords: Kubernetes, Arc, Azure, contentores
ms.custom: references_regions
ms.openlocfilehash: 42a04bb349b2acbd68c7088bc0348deda1ee09e1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652273"
---
# <a name="what-is-azure-arc-enabled-kubernetes"></a>O que é o Kubernetes ativado pelo Azure Arc?

Com o Azure Arc ativado Kubernetes, pode anexar e configurar aglomerados Kubernetes localizados dentro ou fora de Azure. Quando ligar um cluster Kubernetes ao Arco de Azure, será:
* Apareça no portal Azure com um ID do Azure Resource Manager e uma identidade gerida. 
* São colocados num grupo de subscrição e recursos Azure.
* Receba tags como qualquer outro recurso Azure. 

Para ligar um cluster Kubernetes ao Azure, o administrador do cluster precisa de enviar agentes. Estes agentes:
* Corra no espaço de `azure-arc` nomes Kubernetes como implementações padrão de Kubernetes.
* Lidar com a conectividade com o Azure.
* Colete registos e métricas do Arco azul.
* Atenção aos pedidos de configuração. 

A Azure Arc permitiu que a Kubernetes suporta sSL padrão da indústria para proteger dados em trânsito. Além disso, os dados são armazenados encriptados em repouso numa base de dados DB Azure Cosmos para garantir a confidencialidade dos dados.

## <a name="supported-kubernetes-distributions"></a>Distribuição apoiada de Kubernetes

Azure Arc permitiu que Kubernetes trabalha com qualquer cluster certificado de Kubernetes (Cloud Native Computing Foundation). A equipa da Azure Arc trabalhou com [os principais parceiros da indústria para validar a conformidade](./validation-program.md) das suas distribuições kubernetes com a Azure Arc ativada pela Kubernetes.

## <a name="supported-scenarios"></a>Cenários suportados 

Azure Arc habilitado Kubernetes suporta os seguintes cenários: 

* Ligue kubernetes correndo fora de Azure para inventário, agrupamento e marcação.

* Implemente aplicações e aplique a configuração utilizando a gestão de configuração baseada em GitOps. 

* Veja e monitorize os seus agrupamentos utilizando o Azure Monitor para obter recipientes. 

* Aplicar políticas usando a política Azure para Kubernetes. 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Regiões suportadas 

Azure Arc habilitado Kubernetes é atualmente apoiado nestas regiões: 

* E.U.A. Leste
* Europa Ocidental
* E.U.A. Centro-Oeste
* E.U.A. Centro-Sul
* Sudeste Asiático
* Sul do Reino Unido
* E.U.A. Oeste 2
* Leste da Austrália
* E.U.A. Leste 2
* Europa do Norte

## <a name="next-steps"></a>Passos seguintes

* [Ligue um cluster ao Arco de Azure](./quickstart-connect-cluster.md)
