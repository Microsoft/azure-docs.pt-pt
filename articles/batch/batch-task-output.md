---
title: Persistir na produção de trabalho e tarefa para uma loja de dados
description: Conheça diferentes opções para persistir dados de produção a partir de tarefas e empregos do Lote. Pode persistir dados para o Armazenamento Azure, ou para outra loja de dados.
ms.topic: how-to
ms.date: 11/14/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 492de237acdf2d6d42f27638bac3adc0f6549828
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726303"
---
# <a name="persist-job-and-task-output"></a>Manter saída de trabalhos e tarefas

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Alguns exemplos comuns de saída de tarefas incluem:

- Ficheiros criados quando a tarefa processa dados de entrada.
- Ficheiros de registo associados à execução de tarefas.

Este artigo descreve várias opções para a persistência da saída de tarefas.

## <a name="options-for-persisting-output"></a>Opções para a persistência da produção

Dependendo do seu cenário, existem algumas abordagens diferentes que pode tomar para persistir a saída de tarefas:

- [Utilize o serviço de lote API](batch-task-output-files.md).  
- [Utilize a biblioteca de Convenções de Ficheiros de Lote para .NET](batch-task-output-file-conventions.md).  
- Implemente a norma de Convenções de Ficheiros de Lote na sua aplicação.
- Implementar uma solução de movimento de ficheiro personalizado.

As seguintes secções descrevem brevemente cada abordagem, bem como considerações gerais de conceção para a persistência da produção.

### <a name="use-the-batch-service-api"></a>Utilize o serviço de lote API

O serviço Batch suporta especificar ficheiros de saída no Armazenamento Azure para obter dados de tarefas quando [adiciona uma tarefa a um trabalho](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) ou adiciona uma [coleção de tarefas a um trabalho](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

Para obter mais informações sobre a saída de tarefas persistente com a API do serviço de lote, consulte os dados de [tarefa sinuosos para o Armazenamento Azure com o serviço de lote API](batch-task-output-files.md).

### <a name="use-the-batch-file-conventions-library-for-net"></a>Utilize a biblioteca de Convenções de Ficheiros de Lote para .NET

O lote define um conjunto opcional de convenções para nomear ficheiros de saída de tarefas no Armazenamento Azure. A [norma Deconvenções](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) de Ficheiros de Lote descreve estas convenções. A norma Determina os nomes do contentor de destino e do caminho blob no Armazenamento Azure para um determinado ficheiro de saída com base nos nomes do trabalho e da tarefa.

Cabe-lhe a si decidir utilizar o padrão de Convenções de Ficheiros para nomear os seus ficheiros de dados de saída. Também pode nomear o recipiente de destino e a bolha como desejar. Se utilizar a norma De Convenções de Ficheiros para nomear ficheiros de saída, os seus ficheiros de saída estão disponíveis para visualização no [portal Azure][portal].

Os desenvolvedores que construem soluções de lote com C# e .NET podem usar a biblioteca de Convenções de [Ficheiros para .NET][nuget_package] para persistir os dados de tarefas numa conta de Armazenamento Azure, de acordo com a norma de Convenções de [Ficheiros](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions)de Lote . A biblioteca de File Conventions lida com ficheiros de saída móveis para o Armazenamento Azure e nomeando contentores e bolhas de destino de uma forma bem conhecida.

Para obter mais informações sobre a saída de tarefas persistente sinuosa mente com a biblioteca de Convenções de Ficheiros para .NET, consulte persist e dados de [tarefas para o Armazenamento De Azure com a biblioteca](batch-task-output-file-conventions.md)de Convenções de Ficheiros de Lote para .NET .

### <a name="implement-the-batch-file-conventions-standard"></a>Implementar a norma de convenções de ficheiros de lote

Se estiver a utilizar uma linguagem diferente de .NET, pode implementar a norma de Convenções de [Ficheiros](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) de Lote na sua própria aplicação.

Você pode querer implementar as Convenções de Arquivo nomeando o padrão quando você quer um esquema de nomeação comprovado, ou quando você quer ver a saída de tarefas no portal Azure.

### <a name="implement-a-custom-file-movement-solution"></a>Implementar uma solução de movimento de ficheiro personalizado

Também pode implementar a sua própria solução completa de movimento de ficheiros. Utilize esta abordagem quando:

- Pretende persistir os dados de tarefas numa loja de dados que não seja o Armazenamento Azure. Para fazer o upload de ficheiros para uma loja de dados como o Azure SQL ou o Azure DataLake, pode criar um script personalizado ou executável para fazer o upload para esse local. Pode chamá-lo na linha de comando depois de executar a sua execução primária. Por exemplo, num nó windows, pode chamar estes dois comandos:`doMyWork.exe && uploadMyFilesToSql.exe`
- Deseja efetuar o check-pointing ou o upload antecipado dos resultados iniciais.
- Pretende manter o controlo granular sobre o manuseamento de erros. Por exemplo, pode querer implementar a sua própria solução se quiser usar ações de dependência de tarefas para tomar certas ações de upload com base em códigos específicos de saída de tarefas. Para obter mais informações sobre as ações de dependência de tarefas, consulte Criar dependências de [tarefas para executar tarefas que dependem de outras tarefas](batch-task-dependencies.md).

## <a name="design-considerations-for-persisting-output"></a>Considerações de conceção para a produção persistente

Ao conceber a sua solução Batch, considere os seguintes fatores relacionados com as saídas de trabalho e tarefas.

- **Vida útil do nó computacional**: Os nós de computação são muitas vezes transitórios, especialmente em piscinas ativadas por escala automática. A saída de uma tarefa que funciona num nó só está disponível enquanto o nó existe, e apenas dentro do período de retenção de ficheiros que definiu para a tarefa. Se uma tarefa produzir uma saída que possa ser necessária após a conclusão da tarefa, então a tarefa deve enviar os seus ficheiros de saída para uma loja durável como o Armazenamento Azure.

- **Armazenamento de saída**: O Armazenamento Azure é recomendado como um armazenamento de dados para a saída de tarefas, mas você pode usar qualquer armazenamento durável. A saída de tarefas de escrita para o Armazenamento Azure está integrada na API do serviço de lote. Se utilizar outra forma de armazenamento durável, terá de escrever a lógica da aplicação para persistir a saída de tarefas.

- **Recuperação**de saída: Pode recuperar a saída de tarefa diretamente dos nós de computação na sua piscina, ou do Armazenamento Azure ou de outra loja de dados se tiver persistido na saída de tarefas. Para recuperar a saída de uma tarefa diretamente de um nó de cálculo, precisa do nome do ficheiro e da sua localização de saída no nó. Se persistir a saída de tarefas para o Armazenamento Azure, precisa de todo o caminho para o ficheiro no Armazenamento Azure para descarregar os ficheiros de saída com o Azure Storage SDK.

- **Visualização da saída**: Quando navega para uma tarefa de Lote no portal Azure e seleciona **Ficheiros no nó,** é apresentado com todos os ficheiros associados à tarefa, e não apenas os ficheiros de saída em que está interessado. Mais uma vez, os ficheiros em nós computacionais só estão disponíveis enquanto o nó existe e apenas dentro do tempo de retenção de ficheiros que definiu para a tarefa. Para ver a saída de tarefas que persistiu no Armazenamento Azure, pode utilizar o portal Azure ou uma aplicação de cliente de Armazenamento Azure, como o [Azure Storage Explorer][storage_explorer]. Para ver os dados de saída no Armazenamento Azure com o portal ou outra ferramenta, deve saber a localização do ficheiro e navegar diretamente para ele.

## <a name="next-steps"></a>Próximos passos

- Explore utilizando as novas funcionalidades do serviço de lote API para persistir os dados de tarefas em [Dados de tarefas Persist para o Armazenamento Azure com o serviço de lote API](batch-task-output-files.md).
- Saiba utilizar a biblioteca de Convenções de Ficheiros de Lote para .NET em persist e dados de [tarefas para o Armazenamento De Lote com a biblioteca](batch-task-output-file-conventions.md)de Convenções de Ficheiros de Lote para .NET .
- Consulte o projeto de amostra [persistOutputs][github_persistoutputs] no GitHub, que demonstra como utilizar tanto a biblioteca de clientes do Batch para .NET como a biblioteca de Convenções de Ficheiros para .NET persistir a saída de tarefas para armazenamento duradouro.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
