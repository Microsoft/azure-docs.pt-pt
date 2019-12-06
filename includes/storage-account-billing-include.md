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
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851638"
---
Você será cobrado pelo armazenamento do Azure com base no uso da sua conta de armazenamento. Todos os objetos numa conta do Storage são faturados em conjunto como um grupo. 

Os custos de armazenamento são calculados de acordo com os seguintes fatores: 

* **Região** refere-se à região geográfica na qual sua conta se baseia.
* **Tipo de conta** refere-se ao tipo de conta de armazenamento que você está usando. 
* **Camada de acesso** refere-se ao padrão de uso de dados que você especificou para sua conta de armazenamento de BLOBs v2 ou de uso geral.
* A **capacidade** de armazenamento refere-se à quantidade de alocação de sua conta de armazenamento que você está usando para armazenar dados.
* A **replicação** determina quantas cópias de seus dados são mantidas ao mesmo tempo e em quais locais.
* As **Transações** referem-se a todas as operações de leitura e gravação no armazenamento do Azure.
* **Saída de dados** refere-se a todos os dados transferidos de uma região do Azure. Quando os dados em sua conta de armazenamento são acessados por um aplicativo que não está sendo executado na mesma região, você é cobrado pela saída de dados. Para obter informações sobre como usar grupos de recursos para agrupar seus dados e serviços na mesma região para limitar os encargos de egresso, consulte [o que é um grupo de recursos do Azure?](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group). 

A página [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) fornece informações detalhadas sobre os preços com base na capacidade de armazenamento, replicação e transações. A página [Detalhes de Preços das Transferências de Dados](https://azure.microsoft.com/pricing/details/data-transfers/) fornece informações detalhadas sobre os preços de saída de dados. Pode utilizar a [Calculadora de Preços do Storage do Azure](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) para o ajudar a avaliar os custos.

