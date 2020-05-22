---
title: Fluxo de trabalho de serviço de lote e recursos
description: Conheça as características do serviço Batch e o seu fluxo de trabalho de alto nível do ponto de vista do desenvolvimento.
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 8112f6059a14bb18bf1fbf4011ceb074d71b5837
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83791135"
---
# <a name="batch-service-workflow-and-resources"></a>Fluxo de trabalho de serviço de lote e recursos

Nesta visão geral dos componentes centrais do serviço Azure Batch, discutimos o fluxo de trabalho de alto nível que os desenvolvedores de lotes podem usar para construir soluções de computação paralela em larga escala, juntamente com os recursos de serviço primário que são usados.

Quer esteja a desenvolver uma aplicação computacional distribuída ou um serviço que emita chamadas diretas de [API](https://docs.microsoft.com/rest/api/batchservice/) rest ou se está a usar outro dos [SDKs](batch-apis-tools.md#batch-service-apis)de Lote, irá utilizar muitos dos recursos e funcionalidades discutidos aqui.

> [!TIP]
> Para obter uma introdução mais detalhada do serviço Batch, veja [Noções básicas do Azure Batch](batch-technical-overview.md). Veja também a versão mais recente das [Atualizações de serviço do Batch](https://azure.microsoft.com/updates/?product=batch).

## <a name="basic-workflow"></a>Fluxo de trabalho básico

O fluxo de trabalho detalhado que se segue é típico de quase todos os serviços e aplicações que utilizam o serviço Batch para processar cargas de trabalho paralelas:

1. Carregue os **ficheiros de dados** que quer processar para uma conta do [Armazenamento do Azure](../storage/index.yml). O Batch inclui suporte incorporado para aceder ao Armazenamento de Blobs do Azure e as suas tarefas podem transferir estes ficheiros para [nós de computação](nodes-and-pools.md#nodes) quando são executadas.
2. Carregue os **ficheiros de aplicação** que as aplicações vão executar. Estes ficheiros podem ser binários ou scripts e as respetivas dependências e são executados pelas tarefas dos seus trabalhos. As tarefas podem transferir estes ficheiros a partir da sua conta de Armazenamento ou pode utilizar a funcionalidade de [pacotes de aplicações](nodes-and-pools.md#application-packages) do Batch na gestão e implementação de aplicações.
3. Crie uma [piscina](nodes-and-pools.md#pools) de nóscomputa. Quando cria um conjunto, especifica o número de nós de computação do mesmo e o tamanho e o sistema operativo desses nós. Quando cada tarefa dos seus trabalhos é executada, é atribuída a um dos nós do conjunto.
4. Criar um [emprego.](jobs-and-tasks.md#jobs) Os trabalhos gerem uma coleção de tarefas. Cada trabalho é associado a um conjunto específico no qual as tarefas desses trabalhos vão ser executadas.
5. Adicione [tarefas](jobs-and-tasks.md#tasks) ao trabalho. Cada tarefa executa a aplicação ou script que carregou para processar os ficheiros de dados que transfere a partir da sua conta de Armazenamento. À medida que cada tarefa é concluída, pode carregar o respetivo resultado para o Armazenamento do Azure.
6. Monitorize o progresso do trabalho e obtenha o resultado da tarefa no Armazenamento do Azure.

> [!NOTE]
> Precisa de uma [conta do Batch](accounts.md) para utilizar o serviço. Quase todas as soluções do Batch utilizam também uma conta de [Armazenamento do Azure](../storage/index.yml) associada para armazenamento e obtenção de ficheiros.

## <a name="batch-service-resources"></a>Recursos do serviço Batch

Os seguintes tópicos discutem os recursos do Lote que permitem os seus cenários computacionais distribuídos.

- [Contas de lote e contas de armazenamento](accounts.md)
- [Nódosos e piscinas](nodes-and-pools.md)
- [Empregos e tarefas](jobs-and-tasks.md)
- [Ficheiros e diretórios](files-and-directories.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [Ferramentas e APIs do Batch](batch-apis-tools.md) disponíveis para criação de soluções para o Batch.
- Aprenda os conceitos básicos de programação de uma aplicação compatível com o Batch ao utilizar a [biblioteca de cliente .NET do Batch](quick-run-dotnet.md) ou [Python](quick-run-python.md). Estes inícios rápidos orientam-no numa aplicação de exemplo que utiliza o serviço do Batch para executar uma carga de trabalho em vário nós de computação e inclui a utilização do Armazenamento do Azure para o teste e obtenção do ficheiro de carga de trabalho.
- Transfira e instale o [Batch Explorer](https://azure.github.io/BatchExplorer/) para utilizar enquanto desenvolve as soluções do Batch. Utilize o Batch Explorer para ajudar a criar, depurar e monitorizar aplicações do Azure Batch.
- Consulte os recursos comunitários, incluindo [stack overflow,](https://stackoverflow.com/questions/tagged/azure-batch)o [repo comunitário de lote,](https://github.com/Azure/Batch)e o [fórum Do Lote Azure.](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch)
