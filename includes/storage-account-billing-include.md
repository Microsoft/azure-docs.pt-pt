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
ms.openlocfilehash: cdcbe993bd1100b2060a1f8d38eb82ac97121c0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74851638"
---
Você é cobrado para Armazenamento Azure com base no uso da sua conta de armazenamento. Todos os objetos numa conta do Storage são faturados em conjunto como um grupo. 

Os custos de armazenagem são calculados de acordo com os seguintes fatores: 

* **A região** refere-se à região geográfica em que se baseia a sua conta.
* **O tipo** de conta refere-se ao tipo de conta de armazenamento que está a usar. 
* **O nível de acesso** refere-se ao padrão de utilização de dados especificado para a sua conta de armazenamento v2 ou Blob.
* **A Capacidade** de Armazenamento refere-se à quantidade de loteamento da sua conta de armazenamento que está a usar para armazenar dados.
* **A replicação** determina quantas cópias dos seus dados são mantidas de uma só vez, e em que locais.
* **As transações** referem-se a todas as operações de leitura e escrita para o Armazenamento Azure.
* **A saída** de dados refere-se a quaisquer dados transferidos de uma região do Azure. Quando os dados na sua conta de armazenamento são acedidos por uma aplicação que não está a funcionar na mesma região, é-lhe cobrado o seu número de dados. Para obter informações sobre a utilização de grupos de recursos para agrupar os seus dados e serviços na mesma região para limitar as taxas de saída, consulte o que é um grupo de [recursos Azure?](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) 

A página [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) fornece informações detalhadas sobre os preços com base na capacidade de armazenamento, replicação e transações. A página [Detalhes de Preços das Transferências de Dados](https://azure.microsoft.com/pricing/details/data-transfers/) fornece informações detalhadas sobre os preços de saída de dados. Pode utilizar a [Calculadora de Preços do Storage do Azure](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) para o ajudar a avaliar os custos.

