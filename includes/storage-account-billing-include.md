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
ms.openlocfilehash: ed7bfca6095dbb03042efd14456f34556f74a843
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66115658"
---
A faturação do armazenamento do Azure com base na sua utilização de conta de armazenamento. Todos os objetos numa conta do Storage são faturados em conjunto como um grupo. 

Os custos de armazenamento são calculados de acordo com os seguintes fatores: região/localização, tipo de conta, camada de acesso, a capacidade de armazenamento, esquema de replicação, transações de armazenamento e saída de dados.

* **Região** refere-se para a região geográfica na qual se baseia a sua conta.
* **Tipo de conta** refere-se para o tipo de conta de armazenamento que está a utilizar. 
* **Camada de acesso** refere-se para o padrão de utilização de dados que especificou para o seu para fins gerais v2 ou a conta de armazenamento de Blobs.
* Armazenamento **capacidade** refere-se à quantidade de sua alocação de conta de armazenamento estão a utilizar para armazenar dados.
* **Replicação** determina o número de cópias dos seus dados é mantido em simultâneo e em que localizações.
* **Transações** Consulte todos de leitura e escrita para armazenamento do Azure.
* **A saída de dados** refere-se a todos os dados transferidos para fora de uma região do Azure. Quando os dados na sua conta de armazenamento são acedidos por uma aplicação que não está em execução na mesma região, é-lhe cobrada a saída dos dados. Para obter informações sobre como utilizar grupos de recursos para agrupar os seus dados e serviços na mesma região para limitar os custos de saída, consulte [o que é um grupo de recursos do Azure?](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group). 

A página [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) fornece informações detalhadas sobre os preços com base na capacidade de armazenamento, replicação e transações. A página [Detalhes de Preços das Transferências de Dados](https://azure.microsoft.com/pricing/details/data-transfers/) fornece informações detalhadas sobre os preços de saída de dados. Pode utilizar a [Calculadora de Preços do Storage do Azure](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) para o ajudar a avaliar os custos.

