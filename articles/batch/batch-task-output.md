---
title: Manter resultados ou de registos de tarefas concluídas e tarefas para um arquivo de dados - Azure Batch | Documentos da Microsoft
description: Saiba mais sobre as diferentes opções para manter os dados de saída de trabalhos e tarefas do Batch. Pode manter os dados para o armazenamento do Azure ou para outro armazenamento de dados.
services: batch
author: laurenhughes
manager: jeconnoc
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
ms.openlocfilehash: bc579cd372616563b61e5ba04fe32612f3efb1c7
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57541252"
---
# <a name="persist-job-and-task-output"></a>Saída persistente de função e tarefa

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Alguns exemplos comuns de resultado da tarefa incluem:

- Arquivos criados quando os processos de tarefa de dados de entrada.
- Ficheiros de registo associados com a execução da tarefa.

Este artigo descreve várias opções para manter o resultado da tarefa.

## <a name="options-for-persisting-output"></a>Opções para a persistência de saída

Dependendo do seu cenário, existem algumas abordagens diferentes, que pode tomar para manter a saída da tarefa:

- [Utilizar a API de serviço do Batch](batch-task-output-files.md).  
- [Utilizar a biblioteca convenções de ficheiros do Batch para .NET](batch-task-output-file-conventions.md).  
- Implemente o padrão de convenções de ficheiros do Batch na sua aplicação.
- Implemente uma solução de movimento do ficheiro personalizado.

As secções seguintes descrevem resumidamente cada abordagem, bem como considerações de design geral para a persistência de saída.

### <a name="use-the-batch-service-api"></a>Utilizar a API de serviço do Batch

O serviço Batch suporta a especificação de saída de arquivos no armazenamento do Azure para dados de tarefas quando [adicionar uma tarefa a uma tarefa](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) ou [adicionar uma coleção de tarefas a um trabalho](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

Para obter mais informações sobre o resultado da tarefa persistente com a API de serviço do Batch, consulte [API do serviço de dados de tarefa de persistência para o armazenamento do Azure com o Batch](batch-task-output-files.md).

### <a name="use-the-batch-file-conventions-library-for-net"></a>Utilizar a biblioteca convenções de ficheiros do Batch para .NET

Batch define um conjunto opcional de convenções de nomenclatura dos ficheiros de saída da tarefa no armazenamento do Azure. O [padrão de convenções de ficheiros do Batch](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) descreve essas convenções. O padrão de convenções de ficheiros determina os nomes do contentor e blob caminho de destino no armazenamento do Azure para um ficheiro de saída determinado com base nos nomes dos trabalhos e tarefas.

Cabe a opte por utilizar o padrão de convenções de ficheiros para atribuição de nomes dos arquivos de dados de saída. Também pode nomear o contentor de destino e no entanto gostaria de Blobs. Se usar o padrão de convenções de ficheiros à nomenclatura dos ficheiros de saída, em seguida, os ficheiros de saída estão disponíveis para visualização na [portal do Azure][portal].

Os desenvolvedores que criam soluções do Batch com c# e .NET podem utilizar o [biblioteca de convenções de ficheiros para o .NET] [ nuget_package] para manter os dados de tarefa ao armazenamento do Azure da conta, according para o [arquivo em lotes As convenções padrão](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). A biblioteca convenções de ficheiros lida com a movimentação de saída de arquivos para armazenamento do Azure e o destino de contentores e blobs de nomenclatura de forma bem conhecida.

Para obter mais informações sobre o resultado da tarefa persistente com a biblioteca convenções de ficheiros para .NET, consulte [manter os dados de trabalhos e tarefas para o armazenamento do Azure com a biblioteca convenções de ficheiros do Batch para .NET](batch-task-output-file-conventions.md).

### <a name="implement-the-batch-file-conventions-standard"></a>Implementar o padrão de convenções de ficheiros do Batch

Se estiver a utilizar um idioma diferente do .NET, pode implementar o [padrão de convenções de ficheiros do Batch](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) em seu próprio aplicativo.

Pode querer implementar o padrão de nomenclatura de convenções de ficheiros por conta própria quando pretender que um esquema de nomeação comprovado, ou quando desejar exibir o resultado da tarefa no portal do Azure.

### <a name="implement-a-custom-file-movement-solution"></a>Implementar uma solução de movimento do ficheiro personalizado

Também pode implementar a sua própria solução de movimento do ficheiro completo. Utilize esta abordagem quando:

- Pretende manter os dados de tarefas para um arquivo de dados que não seja o armazenamento do Azure. Para carregar ficheiros para um arquivo de dados, como o SQL do Azure ou Azure DataLake, pode criar um script personalizado ou o executável para carregar a essa localização. Em seguida, pode chamá-lo na linha de comando depois de executar o executável principal. Por exemplo, num nó Windows, poderá chamar estes dois comandos: `doMyWork.exe && uploadMyFilesToSql.exe`
- Pretende efetuar o carregamento de verificação ou antecipado dos resultados iniciais.
- Deseja manter um controle granular sobre tratamento de erros. Por exemplo, pode querer implementar sua própria solução se de que pretende utilizar ações de dependência de tarefas que Tomemos determinadas ações de carregamento com base nos códigos de saída de tarefa específica. Para obter mais informações sobre ações de dependência de tarefas, consulte [criar dependências de tarefas para executar tarefas que dependem de outras tarefas](batch-task-dependencies.md).

## <a name="design-considerations-for-persisting-output"></a>Considerações de design para a persistência de saída

Ao criar a sua solução do Batch, considere os seguintes fatores relacionados com resultados de trabalhos e tarefas.

- **Duração do nó de computação**: Nós de computação, muitas vezes, são transitórios, especialmente em conjuntos de dimensionamento automático ativado. Saída de uma tarefa que é executada num nó está disponível somente enquanto o nó existe e apenas dentro do período de retenção de ficheiros que definiu para a tarefa. Se uma tarefa de produzir a saída que pode ser necessárias quando a tarefa estiver concluída, em seguida, a tarefa tem de carregar os ficheiros de saída para um armazenamento durável como o armazenamento do Azure.

- **Armazenamento de saída**: O armazenamento do Azure é recomendado como um arquivo de dados para o resultado da tarefa, mas pode utilizar qualquer armazenamento durável. Escrever o resultado da tarefa no armazenamento do Azure é integrado a API de serviço do Batch. Se utilizar outra forma de armazenamento durável, terá de escrever a lógica do aplicativo para manter as tarefas de saída por conta própria.

- **Obtenção de saída**: Pode obter o resultado da tarefa diretamente a partir de nós de computação do conjunto ou no armazenamento do Azure ou outro armazenamento de dados se o ter mantido o resultado da tarefa. Para obter o resultado de uma tarefa diretamente a partir de um nó de computação, tem o nome de ficheiro e a respetiva localização de saída no nó. Se manter a saída de tarefa ao armazenamento do Azure, em seguida, terá do caminho completo para o ficheiro no armazenamento do Azure para transferir os ficheiros de saída com o SDK de armazenamento do Azure.

- **Exibindo saída**: Quando navegar para uma tarefa do Batch no portal do Azure e selecione **ficheiros no nó**, verá todos os ficheiros associados a tarefa, não apenas os ficheiros de saída que está interessado. Novamente, os ficheiros em nós de computação estão disponíveis apenas enquanto o nó existe e apenas dentro do tempo de retenção de ficheiros que definiu para a tarefa. Para ver o resultado da tarefa que tenha persistidos no armazenamento do Azure, pode utilizar o portal do Azure ou uma aplicação de cliente de armazenamento do Azure, tais como o [Explorador de armazenamento do Azure][storage_explorer]. Para ver dados de saída no armazenamento do Azure com o portal ou outra ferramenta, tem de saber a localização do ficheiro e navegar até ele diretamente.

## <a name="next-steps"></a>Passos Seguintes

- Explore com os novos recursos na API de serviço do Batch para manter os dados de tarefas no [API do serviço de dados de tarefa de persistência para o armazenamento do Azure com o Batch](batch-task-output-files.md).
- Saiba como utilizar a biblioteca convenções de ficheiros do Batch para .NET no [manter os dados de trabalhos e tarefas para o armazenamento do Azure com a biblioteca convenções de ficheiros do Batch para .NET](batch-task-output-file-conventions.md).
- Consulte a [PersistOutputs] [ github_persistoutputs] projeto de exemplo no GitHub, que demonstra como utilizar tanto a biblioteca de cliente do Batch para .NET e a biblioteca convenções de ficheiros para .NET para manter a saída de tarefas para o armazenamento durável .

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
