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
ms.date: 03/30/2020
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ecae47f6aa0ab3f179632467b7da7805f06162d6
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397270"
---
# <a name="manage-your-batch-account"></a>Gerir a sua conta Lote

Uma conta do Batch é uma entidade identificada exclusivamente no âmbito do serviço Batch. Todo o processamento está associado a uma conta do Batch.

Pode criar uma conta do Azure Batch através do [portal do Azure](batch-account-create-portal.md) ou através de programação, como com a [Biblioteca .NET de Gestão de Batch](batch-management-dotnet.md). Ao criar a conta, pode associar uma conta de armazenamento do Azure para armazenar aplicações ou dados de entrada e saída relacionados com tarefas.

Pode executar várias cargas de trabalho do Batch numa única conta do Batch ou distribuí-las entre contas do Batch que estejam na mesma subscrição, mas em diferentes regiões do Azure.

## <a name="components-of-the-batch-account"></a>Componentes da conta Lote

A conta Batch permite-lhe executar trabalhos de computação em grande escala paralelas e de alta performance (HPC) de forma eficiente em Azure. Dentro da conta que gere:

- As aplicações que está a executar

- A atribuição de piscinas e nósos dentro de piscinas

- O número e os tipos de tarefas 

- A entrada e a saída de dados. Não precisa de instalar software adicional para gerir tarefas.

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

