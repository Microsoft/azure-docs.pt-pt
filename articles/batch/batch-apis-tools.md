---
title: APIs e ferramentas para desenvolvedores
description: Saiba mais sobre as APIs e ferramentas disponíveis para o desenvolvimento de soluções com o serviço Azure Batch.
ms.topic: conceptual
ms.date: 05/22/2020
ms.custom: seodec18
ms.openlocfilehash: 502eb08631223215933b75dca882c12c02d17bd9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89146407"
---
# <a name="overview-of-batch-apis-and-tools"></a>Descrição geral dos APIs e ferramentas do Batch

O processamento de cargas de trabalho paralelas com o Azure Batch é geralmente realizado através de programação, utilizando uma das APIs do Batch. A aplicação ou serviço de cliente pode utilizar APIs do Batch para comunicar com o serviço Batch. Com as APIs do Batch, pode criar e gerir conjuntos de nós de computação, máquinas virtuais ou serviços cloud. Pode, então, agendar tarefas para serem executadas nesses nós.

Pode processar de forma eficaz cargas de trabalho em grande escala para a sua organização ou fornecer um front-end de serviço aos seus clientes para que eles possam executar tarefas — a pedido ou com base numa agenda — em um, centenas ou mesmo em milhares de nós. Também pode utilizar o Azure Batch como parte de um fluxo de trabalho maior, gerido por ferramentas como o [Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> Para saber mais sobre as funcionalidades e fluxo de trabalho utilizados no Azure Batch, consulte o [fluxo de trabalho e recursos do serviço Batch.](batch-service-workflow-features.md)

## <a name="azure-accounts-for-batch-development"></a>Contas do Azure para desenvolvimento do Batch

Quando desenvolver soluções do Batch, irá utilizar as seguintes contas na subscrição do Azure:

- **Conta de lote** - Os recursos do Azure Batch, incluindo piscinas, nós de computação, empregos e tarefas, estão associados a uma conta Azure [Batch](accounts.md). Quando a aplicação faz um pedido com o serviço do Batch, autentica o pedido com o nome de conta do Azure Batch, o URL da conta e uma tecla de acesso ou um token do Azure Active Directory. Pode [criar uma conta Batch](batch-account-create-portal.md) no portal Azure ou programáticamente.
- **Conta de armazenamento** - o Batch inclui suporte incorporado para trabalhar com ficheiros no [Armazenamento do Azure](../storage/index.yml). Quase todos os cenários do Batch utilizam o Armazenamento de blobs para testar os programas que as tarefas executam e os dados que processam, e para o armazenamento de dados de saída por elas gerados. Cada conta Batch está geralmente associada a uma conta de armazenamento correspondente.

## <a name="service-level-and-management-level-apis"></a>APIs de nível de serviço e de gestão

O Azure Batch tem dois conjuntos de APIs, um para o nível de serviço e outro para o nível de gestão. O nome é muitas vezes semelhante, mas retornam resultados diferentes.

Apenas as ações das APIs de gestão são rastreadas no registo de atividades. As APIs de nível de serviço contornam a camada de Gestão de Recursos Azure (management.azure.com) e não estão registadas.

Por exemplo, o [serviço de lote API para eliminar um pool](/rest/api/batchservice/pool/delete) é direcionado diretamente para a conta do lote: `DELETE {batchUrl}/pools/{poolId}`

Considerando que a [API de gestão](/rest/api/batchmanagement/pool/delete)  do lote para eliminar uma piscina é direcionada para a camada management.azure.com: `DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Batch/batchAccounts/{accountName}/pools/{poolName}`

## <a name="batch-service-apis"></a>APIs de serviço do Batch

As suas aplicações e serviços podem emitir chamadas diretas de API REST ou utilizar uma ou mais das bibliotecas de cliente seguintes para executar e gerir as cargas de trabalho do Azure Batch.

| API | Referência da API | Download | Tutorial | Exemplos de código | Saiba mais |
| --- | --- | --- | --- | --- | --- |
| **REST do Batch** |[Azure REST API - Docs](/rest/api/batchservice/) |N/D |- |- | [Versões suportadas](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **.NET do Batch** |[Azure SDK para .NET - Docs](/dotnet/api/overview/azure/batch) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Batch/) |[Tutorial](tutorial-parallel-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) | [Notas de versão](https://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[Azure SDK para Python - Docs](/python/api/overview/azure/batch/client) |[PyPI](https://pypi.org/project/azure-batch/) |[Tutorial](tutorial-parallel-python.md)|[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch) | [Leia-me](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/batch/azure-batch/README.md) |
| **Batch Node.js** |[Azure SDK para JavaScript - Docs](/javascript/api/overview/azure/batch/client) |[npm](https://www.npmjs.com/package/azure-batch) |[Tutorial](batch-nodejs-get-started.md) |- | [Leia-me](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[Azure SDK para Java - Docs](/java/api/overview/azure/batch) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Java) | [Leia-me](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>APIs de Gestão de Batch

As APIs do Azure Resource Manager para o Batch concedem acesso programático às contas do Batch. Ao utilizar estas APIs, pode gerir, através de programação, contas, quotas, pacotes de aplicações e outros recursos do Batch através do fornecedor Microsoft.Batch.  

| API | Referência da API | Download | Tutorial | Exemplos de código |
| --- | --- | --- | --- | --- |
| **REST de Gestão de Lotes** |[Azure REST API - Docs](/rest/api/batchmanagement/) |- |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Gestão de Batch .NET** |[Azure SDK para .NET - Docs](/dotnet/api/overview/azure/batch/management) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) | [Tutorial](batch-management-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) |
| **Python de Gestão de Lotes** |[Azure SDK para Python - Docs](/python/api/overview/azure/batch/management) |[PyPI](https://pypi.org/project/azure-mgmt-batch/) |- |- |
| **Node.js de Gestão de Lotes** |[Azure SDK para JavaScript - Docs](/javascript/api/overview/azure/batch/management) |[npm](https://www.npmjs.com/package/azure-arm-batch) |- |- | 
| **Java de Gestão de Lotes** |[Azure SDK para Java - Docs](/java/api/overview/azure/batch/management) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |- |

## <a name="batch-command-line-tools"></a>Ferramentas de linha de comandos do Batch

Estas ferramentas da linha de comandos fornecem a mesma funcionalidade que o serviço de Batch e as APIs de Gestão do Batch: 

- [Cmdlets do Batch PowerShell](/powershell/module/az.batch/): os cmdlets do Azure Batch no módulo [Azure PowerShell](/powershell/azure/) permitem-lhe gerir recursos do Batch com o PowerShell.
- [CLI do Azure](/cli/azure): a CLI do Azure é um conjunto de ferramentas para várias plataformas que fornece comandos shell para interagir com vários serviços do Azure, incluindo o serviço do Batch e o serviço de Gestão do Batch. Consulte [Manage Batch resources with Azure CLI](batch-cli-get-started.md)(Gerir recursos do Batch com a CLI do Azure), para obter mais informações sobre como utilizar a CLI do Azure com o Batch.

## <a name="other-tools-for-application-development"></a>Outras ferramentas para programação da aplicação

Estas ferramentas adicionais podem ser úteis para construir e depurar as suas aplicações e serviços do Batch.

- [Portal do Azure](https://portal.azure.com/): pode criar, monitorizar e eliminar conjuntos, trabalhos e tarefas do Batch no portal do Azure. Você pode ver informações de estado para estes e outros recursos enquanto você gere seus trabalhos, e até mesmo baixar arquivos dos nós de computação nas suas piscinas. Por exemplo, pode transferir o `stderr.txt` de uma tarefa falhada, durante a resolução de problemas. Também pode transferir ficheiros de Ambiente de Trabalho Remoto (RDP) que pode utilizar para iniciar sessão em nós de computação.
- [Azure Batch Explorer](https://azure.github.io/BatchExplorer/): o Batch Explorer (anteriormente denominado BatchLabs) é uma ferramenta de cliente autónoma, gratuita e rica em funcionalidades para ajudar a criar, depurar e monitorizar aplicações do Azure Batch. Transfira um [pacote de instalação](https://azure.github.io/BatchExplorer/) para Mac, Linux ou Windows.
- [Estaleiro Azure Batch](https://github.com/Azure/batch-shipyard): O Estaleiro de Lote é uma ferramenta para ajudar a provisão, execução e monitorização do processamento de lotes à base de contentores e cargas de trabalho HPC em Azure Batch.
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/): Embora não seja estritamente uma ferramenta Azure Batch, o Storage Explorer é outra ferramenta valiosa para ter enquanto está a desenvolver e depurar as suas soluções Batch.

## <a name="additional-resources"></a>Recursos adicionais

- Para obter informações sobre o registo de eventos a partir da sua aplicação Batch, consulte [métricas, alertas e registos do Lote para avaliação e monitorização de diagnósticos.](batch-diagnostics.md)
- Para obter informações de referência sobre os eventos levantados pelo serviço Batch, consulte [Batch Analytics](batch-analytics.md).
- Para obter informações sobre variáveis ambientais para nós computacional, consulte variáveis do [ambiente de execução do Azure Batch](batch-compute-node-environment-variables.md).

## <a name="next-steps"></a>Passos seguintes

- Conheça o fluxo de trabalho do [serviço Batch e os recursos primários,](batch-service-workflow-features.md) tais como piscinas, nós, empregos e tarefas.
- [Introdução à biblioteca do Azure Batch para .NET](tutorial-parallel-dotnet.md) para saber como utilizar C# e a biblioteca .NET do Batch para executar uma carga de trabalho simples utilizando um fluxo de trabalho Batch comum. Uma [versão do Python](tutorial-parallel-python.md) e um [tutorial do Node.js](batch-nodejs-get-started.md) também estão disponíveis.
- Transfira as [amostras de código no GitHub](https://github.com/Azure-Samples/azure-batch-samples) para ver como tanto o C# como o Python pode interagir com o Batch para agendar e processar amostras de cargas de trabalho.
