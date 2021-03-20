---
title: Persistir de trabalho e saída de tarefa para uma loja de dados
description: Saiba mais sobre diferentes opções para persistir dados de saída a partir de tarefas e empregos do Batch. Pode persistir dados para o Azure Storage ou para outra loja de dados.
ms.topic: how-to
ms.date: 11/14/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4ebe0b6d57225eff9f3f1251d5e491c95e9b7ffc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85965115"
---
# <a name="persist-job-and-task-output"></a>Manter saída de trabalhos e tarefas

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Alguns exemplos comuns de saída de tarefas incluem:

- Ficheiros criados quando a tarefa processa os dados de entrada.
- Registar ficheiros associados à execução de tarefas.

Este artigo descreve várias opções para a saída de tarefas persistente.

## <a name="options-for-persisting-output"></a>Opções para a produção persistente

Dependendo do seu cenário, existem algumas abordagens diferentes que pode tomar para persistir na saída da tarefa:

- [Utilize o serviço de lote API](batch-task-output-files.md).  
- [Utilize a biblioteca de convenções de ficheiros de lote para .NET](batch-task-output-file-conventions.md).  
- Implemente a norma de Convenções de Ficheiros de Lote na sua aplicação.
- Implementar uma solução de movimento de ficheiros personalizado.

As secções seguintes descrevem brevemente cada abordagem, bem como considerações gerais de conceção para a produção persistente.

### <a name="use-the-batch-service-api"></a>Utilize o serviço de lote API

O serviço Batch suporta especificar ficheiros de saída no Azure Storage para dados de tarefas quando [adiciona uma tarefa a um trabalho](/rest/api/batchservice/add-a-task-to-a-job) ou adiciona uma recolha de [tarefas a um trabalho](/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

Para obter mais informações sobre a saída de tarefas persistente com a API do serviço Batch, consulte [os dados de tarefa persiste para O Armazenamento Azure com o serviço Batch API](batch-task-output-files.md).

### <a name="use-the-batch-file-conventions-library-for-net"></a>Utilize a biblioteca de convenções de ficheiros de lote para .NET

O lote define um conjunto opcional de convenções para nomear ficheiros de saída de tarefas no Azure Storage. A [norma de Convenções de Ficheiros de Lote](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) descreve estas convenções. A norma de Convenções de Ficheiros determina os nomes do contentor de destino e do caminho blob no Azure Storage para um dado ficheiro de saída com base nos nomes do trabalho e da tarefa.

Cabe-lhe a si decidir utilizar o padrão de Convenções de Ficheiros para nomear os seus ficheiros de dados de saída. Também pode nomear o recipiente de destino e o blob como desejar. Se utilizar a norma "Convenções de Ficheiros" para nomear ficheiros de saída, os seus ficheiros de saída estão disponíveis para visualização no [portal Azure][portal].

Os desenvolvedores que couem soluções batch com C# e .NET podem utilizar a [biblioteca de Convenções de Ficheiros para .NET][nuget_package] para persistir dados de tarefas numa conta de Armazenamento Azure, de acordo com a [norma de Convenções de Ficheiros de Lote](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions). A biblioteca de Convenções de Ficheiros trata da movimentação de ficheiros de saída para o Azure Storage e nomear contentores e bolhas de destino de uma forma bem conhecida.

Para obter mais informações sobre a saída de tarefas persistentes com a biblioteca de Convenções de Ficheiros para .NET, consulte [os dados de trabalho e de tarefa de Persistência para O Armazenamento do Azure com a biblioteca de Convenções de Ficheiros de Lote para .NET](batch-task-output-file-conventions.md).

### <a name="implement-the-batch-file-conventions-standard"></a>Implementar a norma de convenções de ficheiros de lote

Se estiver a utilizar um idioma diferente de .NET, pode implementar a [norma De Ficheiros de Lote](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) na sua própria aplicação.

Pode querer implementar as Convenções de Ficheiros que nomeiam o padrão quando pretender um esquema de nomeação comprovado, ou quando pretender visualizar a saída da tarefa no portal Azure.

### <a name="implement-a-custom-file-movement-solution"></a>Implementar uma solução de movimento de ficheiros personalizados

Também pode implementar a sua própria solução completa de movimento de ficheiros. Utilize esta abordagem quando:

- Pretende persistir dados de tarefas para uma loja de dados que não seja o Azure Storage. Para fazer o upload de ficheiros para uma loja de dados como Azure SQL ou Azure DataLake, pode criar um script personalizado ou executável para fazer o upload para esse local. Em seguida, pode chamá-lo na linha de comando depois de executar o seu executável primário. Por exemplo, num nó do Windows, pode chamar estes dois comandos: `doMyWork.exe && uploadMyFilesToSql.exe`
- Pretende efetuar o check-pointing ou o upload antecipado dos resultados iniciais.
- Pretende manter o controlo granular sobre o manuseamento de erros. Por exemplo, pode querer implementar a sua própria solução se quiser utilizar ações de dependência de tarefas para realizar determinadas ações de upload com base em códigos específicos de saída de tarefas. Para obter mais informações sobre as ações de dependência de [tarefas, consulte Criar dependências de tarefas para executar tarefas que dependam de outras tarefas](batch-task-dependencies.md).

## <a name="design-considerations-for-persisting-output"></a>Considerações de design para a produção persistente

Ao conceber a sua solução Batch, considere os seguintes fatores relacionados com as saídas de trabalho e tarefas.

- **Tempo de vida útil do nó computacional**: Os nós computacional são frequentemente transitórios, especialmente em piscinas com uma escala automática. A saída de uma tarefa que funciona num nó só está disponível enquanto o nó existir, e apenas dentro do período de retenção de ficheiros que definiu para a tarefa. Se uma tarefa produzir uma saída que possa ser necessária após a conclusão da tarefa, então a tarefa deve enviar os seus ficheiros de saída para uma loja durável, como o Azure Storage.

- **Armazenamento de saída**: O Azure Storage é recomendado como uma loja de dados para a saída da tarefa, mas pode utilizar qualquer armazenamento durável. A saída de tarefa de escrita para o Azure Storage está integrada na API do serviço batch. Se utilizar outra forma de armazenamento durável, terá de escrever a lógica da aplicação para persistir na saída da tarefa.

- **Recuperação de saídas**: Pode recuperar a saída da tarefa diretamente dos nós de cálculo na sua piscina, ou do Azure Storage ou de outra loja de dados se tiver uma saída de tarefas persistente. Para recuperar a saída de uma tarefa diretamente de um nó de computação, precisa do nome do ficheiro e da sua localização de saída no nó. Se persistir na saída da tarefa para o Azure Storage, então precisa de todo o caminho para o ficheiro no Azure Storage para descarregar os ficheiros de saída com o Azure Storage SDK.

- **Visualização :** Quando navega para uma tarefa de Lote no portal Azure e seleciona **Ficheiros no nó,** é-lhe apresentado todos os ficheiros associados à tarefa e não apenas os ficheiros de saída em que está interessado. Mais uma vez, os ficheiros sobre os nós computativos só estão disponíveis enquanto o nó existir e apenas dentro do tempo de retenção de ficheiros que definiu para a tarefa. Para visualizar a saída de tarefa que insistiu no Azure Storage, pode utilizar o portal Azure ou uma aplicação de cliente de Armazenamento Azure, como o [Azure Storage Explorer][storage_explorer]. Para visualizar os dados de saída no Azure Storage com o portal ou outra ferramenta, deve saber a localização do ficheiro e navegar diretamente para o mesmo.

## <a name="next-steps"></a>Passos seguintes

- Explore utilizando as novas funcionalidades no serviço Batch API para persistir dados de tarefas em persistência em [dados de tarefas persistindo para Azure Storage com o serviço Batch API](batch-task-output-files.md).
- Saiba mais sobre a utilização da biblioteca de convenções de ficheiros de lote para .NET em [persistência e dados de tarefa para Azure Storage com a biblioteca de convenções de ficheiros de lote para .NET](batch-task-output-file-conventions.md).
- Consulte o projeto de amostra [persistOutputs][github_persistoutputs] no GitHub, que demonstra como utilizar tanto a biblioteca de clientes Batch para .NET como a biblioteca de Convenções de Ficheiros para .NET persistir na saída da tarefa para armazenamento durável.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
