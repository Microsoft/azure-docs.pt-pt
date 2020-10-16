---
title: Gerir a sua conta
description: Saiba o que compreende uma conta Azure Batch
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6ef87fab099c6ff7402b351664ee15a198d69a4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83722971"
---
# <a name="manage-your-batch-account"></a>Gerencie a sua conta Batch

Uma conta do Batch é uma entidade identificada exclusivamente no âmbito do serviço Batch. Todo o processamento está associado a uma conta do Batch.

Pode criar uma conta do Azure Batch através do [portal do Azure](batch-account-create-portal.md) ou através de programação, como com a [Biblioteca .NET de Gestão de Batch](batch-management-dotnet.md). Ao criar a conta, pode associar uma conta de armazenamento do Azure para armazenar aplicações ou dados de entrada e saída relacionados com tarefas.

Pode executar várias cargas de trabalho do Batch numa única conta do Batch ou distribuí-las entre contas do Batch que estejam na mesma subscrição, mas em diferentes regiões do Azure.

## <a name="components-of-the-batch-account"></a>Componentes da conta Batch

A conta Batch permite-lhe executar trabalhos de lote paralelos e de alto desempenho em grande escala (HPC) em Azure. Dentro da conta que gere:

- As aplicações que está a executar

- A atribuição de piscinas e nódes dentro de piscinas

- O número e tipos de tarefas 

- A entrada e saída de dados. Não é necessário instalar software adicional para gerir tarefas.

- Quando cria a conta Batch, é-lhe pedido que lhe atribua um nome. Este nome é o seu ID e uma vez atribuído não pode ser alterado.

- Para alterar o nome de uma conta, tem de a eliminar e criar uma nova conta Batch.

- A conta é criada dentro da subscrição que pretende utilizar.

- Utilize a conta para identificar e recuperar chaves de conta primária e secundária de qualquer conta Batch dentro da sua subscrição.

- A conta mantém informações sobre a atribuição de piscinas e quotas fundamentais.  

- A conta contém informações de localização.

- A conta identifica a sua conta de armazenamento.

## <a name="next-steps"></a>Passos seguintes

- Criar uma conta Batch utilizando o [portal Azure.](batch-account-create-portal.md)
- Crie uma conta Batch programáticamente, tal como com a [biblioteca Batch Management .NET](batch-management-dotnet.md).
- [Configure ou desative o acesso remoto aos nós computacional numa piscina Azure Batch](pool-endpoint-configuration.md).
- [Executar tarefas de preparação de emprego e de libertação de emprego nos nosm de computação batch](batch-job-prep-release.md)

