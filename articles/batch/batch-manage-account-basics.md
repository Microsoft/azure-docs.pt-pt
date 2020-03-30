---
title: Gerencie a sua conta- Lote Azure [ Lote ] Microsoft Docs
description: Saiba o que inclui uma conta Azure Batch
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a4df2ec0439e659fd8dc1448c6209c9718114791
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479795"
---
# <a name="manage-your-batch-account"></a>Gerir a sua conta Lote

Uma conta do Batch é uma entidade identificada exclusivamente no âmbito do serviço Batch. Todo o processamento está associado a uma conta do Batch.

Pode criar uma conta do Azure Batch através do [portal do Azure](batch-account-create-portal.md) ou através de programação, como com a [Biblioteca .NET de Gestão de Batch](batch-management-dotnet.md). Ao criar a conta, pode associar uma conta de armazenamento do Azure para armazenar aplicações ou dados de entrada e saída relacionados com tarefas.

Pode executar várias cargas de trabalho do Batch numa única conta do Batch ou distribuí-las entre contas do Batch que estejam na mesma subscrição, mas em diferentes regiões do Azure.

## <a name="components-of-the-batch-account"></a>Componentes da conta Lote

A conta Batch permite-lhe executar trabalhos de computação em grande escala paralelas e de alta performance (HPC) de forma eficiente em Azure. Dentro da conta que gere:

- as aplicações que está a executar

- a atribuição de piscinas e nódosos dentro das piscinas

- o número e tipos de tarefas 

- a entrada e saída de dados. Não precisa de instalar software adicional para gerir tarefas.

- Quando criar a conta Batch, é-lhe pedido que lhe atribua um nome. Este nome é o seu ID e uma vez atribuído não pode ser alterado.

- Para alterar o nome de uma conta, é necessário apagá-la e criar uma nova conta De lote.

- A conta é criada dentro da subscrição que pretende utilizar.

- Utilize a conta para identificar e recuperar as chaves da conta primária e secundária de qualquer conta do Lote dentro da sua subscrição.

- A conta mantém informações sobre a atribuição de piscinas e quotas fundamentais.  

- A conta contém informações de localização.

- A conta identifica a sua conta de armazenamento.

## <a name="next-steps"></a>Passos seguintes

- Crie uma conta Batch utilizando o [portal Azure](batch-account-create-portal.md).
- Crie uma conta Batch programáticamente, como na [biblioteca Batch Management .NET](batch-management-dotnet.md).
- [Configure ou desative o acesso remoto aos nódosos de computação numa piscina de Lote Azure](pool-endpoint-configuration.md).
- [Executar tarefas de preparação de emprego e libertação de emprego em nódos de computação batch](batch-job-prep-release.md)

