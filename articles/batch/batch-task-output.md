---
title: Manter resultados ou logs de trabalhos concluídos e tarefas em um repositório de dados – lote do Azure | Microsoft Docs
description: Saiba mais sobre opções diferentes para persistir dados de saída de tarefas e trabalhos do lote. Você pode manter os dados no armazenamento do Azure ou em outro armazenamento de dados.
services: batch
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b5fe15b36913106692377c483110cce2c0418a3f
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322852"
---
# <a name="persist-job-and-task-output"></a>Manter saída de trabalhos e tarefas

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Alguns exemplos comuns de saída de tarefa incluem:

- Arquivos criados quando a tarefa processa dados de entrada.
- Arquivos de log associados à execução da tarefa.

Este artigo descreve várias opções para manter a saída da tarefa.

## <a name="options-for-persisting-output"></a>Opções para manter a saída

Dependendo do seu cenário, há algumas abordagens diferentes que você pode tomar para manter a saída da tarefa:

- [Use a API do serviço de lote](batch-task-output-files.md).  
- [Use a biblioteca de convenções de arquivo em lotes para .net](batch-task-output-file-conventions.md).  
- Implemente o padrão de convenções de arquivo em lotes em seu aplicativo.
- Implemente uma solução de movimentação de arquivos personalizada.

As seções a seguir descrevem brevemente cada abordagem, bem como as considerações gerais de design para manter a saída.

### <a name="use-the-batch-service-api"></a>Usar a API do serviço de lote

O serviço de lote dá suporte à especificação de arquivos de saída no armazenamento do Azure para dados de tarefa quando você [adiciona uma tarefa a um trabalho](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) ou [adiciona uma coleção de tarefas a um trabalho](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

Para obter mais informações sobre como manter a saída da tarefa com a API do serviço de lote, consulte [manter dados da tarefa no armazenamento do Azure com a API do serviço de lote](batch-task-output-files.md).

### <a name="use-the-batch-file-conventions-library-for-net"></a>Usar a biblioteca de convenções de arquivo em lotes para .NET

O lote define um conjunto opcional de convenções para nomear arquivos de saída de tarefa no armazenamento do Azure. O [padrão de convenções de arquivo em lotes](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) descreve essas convenções. O padrão de convenções de arquivo determina os nomes do contêiner de destino e do caminho do blob no armazenamento do Azure para um determinado arquivo de saída com base nos nomes do trabalho e da tarefa.

Cabe a você optar por usar o padrão de convenções de arquivo para nomear seus arquivos de dados de saída. Você também pode nomear o contêiner de destino e o blob, mas deseja. Se você usar o padrão de convenções de arquivo para nomear arquivos de saída, os arquivos de saída estarão disponíveis para exibição no [portal do Azure][portal].

Os desenvolvedores que criam soluções C# de lote com o e o .net podem usar a [biblioteca de convenções de arquivo para .net][nuget_package] para manter os dados da tarefa em uma conta de armazenamento do Azure, de acordo com o [padrão de convenções de arquivo em lotes](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). A biblioteca de convenções de arquivo lida com a movimentação de arquivos de saída para o armazenamento do Azure e para os contêineres de destino de nomeação e blobs de forma bem conhecida.

Para obter mais informações sobre como manter a saída da tarefa com a biblioteca de convenções de arquivo para .NET, consulte [manter dados de trabalho e tarefa no armazenamento do Azure com a biblioteca de convenções de arquivo em lotes para .net](batch-task-output-file-conventions.md).

### <a name="implement-the-batch-file-conventions-standard"></a>Implementar o padrão de convenções de arquivo em lotes

Se você estiver usando uma linguagem diferente do .NET, poderá implementar o [padrão de convenções de arquivo em lotes](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) em seu próprio aplicativo.

Talvez você queira implementar o padrão de nomeação de convenções de arquivo por conta própria quando desejar um esquema de nomenclatura comprovado ou quando desejar exibir a saída da tarefa no portal do Azure.

### <a name="implement-a-custom-file-movement-solution"></a>Implementar uma solução de movimentação de arquivos personalizada

Você também pode implementar sua própria solução completa de movimentação de arquivos. Use essa abordagem quando:

- Você deseja manter os dados da tarefa em um armazenamento de dados diferente do armazenamento do Azure. Para carregar arquivos em um armazenamento de dados como o SQL Azure ou Azure datalake, você pode criar um script personalizado ou executável para carregar nesse local. Em seguida, você pode chamá-lo na linha de comando depois de executar o executável primário. Por exemplo, em um nó do Windows, você pode chamar estes dois comandos:`doMyWork.exe && uploadMyFilesToSql.exe`
- Você deseja executar o ponto de verificação ou carregamento antecipado de resultados iniciais.
- Você deseja manter o controle granular sobre o tratamento de erros. Por exemplo, talvez você queira implementar sua própria solução se quiser usar ações de dependência de tarefas para realizar determinadas ações de upload com base em códigos de saída de tarefa específicos. Para obter mais informações sobre ações de dependência de tarefas, consulte [criar dependências de tarefa para executar tarefas que dependem de outras tarefas](batch-task-dependencies.md).

## <a name="design-considerations-for-persisting-output"></a>Considerações de design para manter a saída

Ao projetar sua solução de lote, considere os seguintes fatores relacionados às saídas de trabalho e tarefa.

- **Tempo de vida do nó de computação**: Nós de computação geralmente são transitórios, especialmente em pools habilitados para autoescala. A saída de uma tarefa que é executada em um nó está disponível somente enquanto o nó existe e somente dentro do período de retenção de arquivo que você definiu para a tarefa. Se uma tarefa produzir uma saída que pode ser necessária depois que a tarefa for concluída, a tarefa deverá carregar seus arquivos de saída para um repositório durável, como o armazenamento do Azure.

- **Armazenamento de saída**: O armazenamento do Azure é recomendado como um armazenamento de dados para a saída da tarefa, mas você pode usar qualquer armazenamento durável. Gravar a saída da tarefa no armazenamento do Azure é integrado à API do serviço de lote. Se você usar outra forma de armazenamento durável, precisará escrever a lógica do aplicativo para manter a saída da tarefa por conta própria.

- **Recuperação de saída**: Você pode recuperar a saída da tarefa diretamente dos nós de computação no pool ou do armazenamento do Azure ou de outro armazenamento de dados se tiver a saída de tarefa persistente. Para recuperar a saída de uma tarefa diretamente de um nó de computação, você precisa do nome do arquivo e do local de saída no nó. Se você mantiver a saída da tarefa no armazenamento do Azure, precisará do caminho completo para o arquivo no armazenamento do Azure para baixar os arquivos de saída com o SDK do armazenamento do Azure.

- **Exibindo saída**: Quando você navega para uma tarefa em lote no portal do Azure e seleciona **arquivos no nó**, você recebe todos os arquivos associados à tarefa, não apenas os arquivos de saída nos quais está interessado. Novamente, os arquivos em nós de computação estão disponíveis somente enquanto o nó existe e somente dentro do tempo de retenção de arquivo que você definiu para a tarefa. Para exibir a saída da tarefa que você persistiu no armazenamento do Azure, você pode usar o portal do Azure ou um aplicativo cliente de armazenamento do Azure, como o [Gerenciador de armazenamento do Azure][storage_explorer]. Para exibir os dados de saída no armazenamento do Azure com o portal ou outra ferramenta, você deve saber o local do arquivo e navegar diretamente para ele.

## <a name="next-steps"></a>Passos seguintes

- Explore usando os novos recursos da API do serviço de lote para manter os dados da tarefa em [manter dados de tarefas no armazenamento do Azure com a API de serviço do lote](batch-task-output-files.md).
- Saiba como usar a biblioteca de convenções de arquivo em lotes para .NET em [manter dados de trabalho e tarefa no armazenamento do Azure com a biblioteca de convenções de arquivo em lotes para .net](batch-task-output-file-conventions.md).
- Consulte o projeto de exemplo [PersistOutputs][github_persistoutputs] no GitHub, que demonstra como usar a biblioteca de cliente do lote para .net e a biblioteca de convenções de arquivo para .net para manter a saída da tarefa para o armazenamento durável.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
