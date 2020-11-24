---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/19/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: fcfe05db6a9be1049ca5da06985f31135ac79f3b
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95560294"
---
Está cobrado para o Azure Storage com base no uso da sua conta de armazenamento. Todos os objetos numa conta do Storage são faturados em conjunto como um grupo. 

Os custos de armazenagem são calculados de acordo com os seguintes fatores: 

* **Região** refere-se à região geográfica em que a sua conta está sediada.
* **O tipo de conta** refere-se ao tipo de conta de armazenamento que está a utilizar. 
* **O nível de acesso** refere-se ao padrão de utilização de dados especificado para a sua conta de armazenamento v2 ou Blob para fins gerais.
* **Capacidade** de armazenamento refere-se à quantidade de loteamento da sua conta de armazenamento que está a usar para armazenar dados.
* **A replicação** determina quantas cópias dos seus dados são mantidas ao mesmo tempo e em que locais.
* **As transações** referem-se a todas as operações de leitura e escrita para o Azure Storage.
* **A saída de dados** refere-se a quaisquer dados transferidos de uma região de Azure. Quando os dados na sua conta de armazenamento são acedidos por uma aplicação que não está a funcionar na mesma região, é cobrado por saída de dados. Para obter informações sobre a utilização de grupos de recursos para agrupar os seus dados e serviços na mesma região para limitar as taxas de saída, veja [o que é um grupo de recursos Azure?](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) 

A página [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) fornece informações detalhadas sobre os preços com base na capacidade de armazenamento, replicação e transações. A página [Detalhes de Preços das Transferências de Dados](https://azure.microsoft.com/pricing/details/data-transfers/) fornece informações detalhadas sobre os preços de saída de dados. Pode utilizar a [Calculadora de Preços do Storage do Azure](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) para o ajudar a avaliar os custos.