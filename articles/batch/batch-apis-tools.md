---
title: APIs e ferramentas para desenvolvedores
description: Saiba mais sobre as APIs e ferramentas disponíveis para o desenvolvimento de soluções com o serviço Azure Batch.
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 56c268675a39dfd3c70b3d8d8e457a2a200d7db6
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82114926"
---
# <a name="overview-of-batch-apis-and-tools"></a>Descrição geral dos APIs e ferramentas do Batch

O processamento de cargas de trabalho paralelas com o Azure Batch é geralmente realizado através de programação, utilizando uma das APIs do Batch. A aplicação ou serviço de cliente pode utilizar APIs do Batch para comunicar com o serviço Batch. Com as APIs do Batch, pode criar e gerir conjuntos de nós de computação, máquinas virtuais ou serviços cloud. Pode, então, agendar tarefas para serem executadas nesses nós. 

Pode processar de forma eficaz cargas de trabalho em grande escala para a sua organização ou fornecer um front-end de serviço aos seus clientes para que eles possam executar tarefas — a pedido ou com base numa agenda — em um, centenas ou mesmo em milhares de nós. Também pode utilizar o Azure Batch como parte de um fluxo de trabalho maior, gerido por ferramentas como o [Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> Quando estiver pronto para aprofundar a API do Batch para uma maior compreensão das funcionalidades que fornece, veja [Descrição geral da funcionalidade Batch para desenvolvedores](batch-api-basics.md).
> 
> 

## <a name="azure-accounts-for-batch-development"></a>Contas do Azure para desenvolvimento do Batch
Quando desenvolver soluções do Batch, irá utilizar as seguintes contas na subscrição do Azure:

* **Conta** de lote - Os recursos do Lote Azure, incluindo piscinas, nós de computação, empregos e tarefas, estão associados a uma conta de [Lote](batch-api-basics.md#account)Azure . Quando a aplicação faz um pedido com o serviço do Batch, autentica o pedido com o nome de conta do Azure Batch, o URL da conta e uma tecla de acesso ou um token do Azure Active Directory. Pode [criar uma conta do Batch](batch-account-create-portal.md) no portal do Azure ou através de programação.
* **Conta de armazenamento** - o Batch inclui suporte incorporado para trabalhar com ficheiros no [Armazenamento do Azure][azure_storage]. Quase todos os cenários do Batch utilizam o Armazenamento de blobs para testar os programas que as tarefas executam e os dados que processam, e para o armazenamento de dados de saída por elas gerados. Para obter opções de armazenamento no Batch, consulte a [descrição geral da funcionalidade do Batch](batch-api-basics.md#azure-storage-account).

## <a name="batch-service-apis"></a>APIs de serviço do Batch

As suas aplicações e serviços podem emitir chamadas diretas de API REST ou utilizar uma ou mais das bibliotecas de cliente seguintes para executar e gerir as cargas de trabalho do Azure Batch.

| API | Referência da API | Transferência | Tutorial | Exemplos de código | Mais Informações |
| --- | --- | --- | --- | --- | --- |
| **REST do Batch** |[docs.microsoft.com][batch_rest] |N/D |- |- | [Versões Suportadas](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **.NET do Batch** |[docs.microsoft.com][api_net] |[Nuget][api_net_nuget] |[Tutorial](tutorial-parallel-dotnet.md) |[GitHub][api_sample_net] | [Notas de lançamento](https://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[docs.microsoft.com][api_python] |[PyPI][api_python_pypi] |[Tutorial](tutorial-parallel-python.md)|[GitHub][api_sample_python] | [Leia-me](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **Batch Node.js** |[docs.microsoft.com][api_nodejs] |[npm][api_nodejs_npm] |[Tutorial](batch-nodejs-get-started.md) |- | [Leia-me](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[docs.microsoft.com][api_java] |[Maven][api_java_jar] |- |[Leia-me][api_sample_java] | [Leia-me](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>APIs de Gestão de Batch

As APIs do Azure Resource Manager para o Batch concedem acesso programático às contas do Batch. Ao utilizar estas APIs, pode gerir, através de programação, contas, quotas, pacotes de aplicações e outros recursos do Batch através do fornecedor Microsoft.Batch.  

| API | Referência da API | Transferência | Tutorial | Exemplos de código |
| --- | --- | --- | --- | --- |
| **REST de Gestão de Lotes** |[docs.microsoft.com][api_rest_mgmt] |N/D |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Gestão de Batch .NET** |[docs.microsoft.com][api_net_mgmt] |[Nuget][api_net_mgmt_nuget] | [Tutorial](batch-management-dotnet.md) |[GitHub][api_sample_net] |
| **Python de Gestão de Lotes** |[docs.microsoft.com][api_python_mgmt] |[PyPI][api_python_mgmt_pypi] |- |- |
| **Node.js de Gestão de Lotes** |[docs.microsoft.com][api_nodejs_mgmt] |[npm][api_nodejs_mgmt_npm] |- |- | 
| **Java de Gestão de Lotes** |- |[Maven][api_java_mgmt_jar] |- |- |
## <a name="batch-command-line-tools"></a>Ferramentas de linha de comandos do Batch

Estas ferramentas da linha de comandos fornecem a mesma funcionalidade que o serviço de Batch e as APIs de Gestão do Batch: 

* [Cmdlets do Batch PowerShell][batch_ps]: os cmdlets do Azure Batch no módulo [Azure PowerShell](/powershell/azure/overview) permitem-lhe gerir recursos do Batch com o PowerShell.
* [CLI do Azure](/cli/azure): a CLI do Azure é um conjunto de ferramentas para várias plataformas que fornece comandos shell para interagir com vários serviços do Azure, incluindo o serviço do Batch e o serviço de Gestão do Batch. Consulte [Manage Batch resources with Azure CLI](batch-cli-get-started.md)(Gerir recursos do Batch com a CLI do Azure), para obter mais informações sobre como utilizar a CLI do Azure com o Batch.

## <a name="other-tools-for-application-development"></a>Outras ferramentas para programação da aplicação

Aqui estão algumas ferramentas adicionais que podem ser úteis para criar e depurar as suas aplicações e serviços do Batch:

* [Portal do Azure][portal]: pode criar, monitorizar e eliminar conjuntos, trabalhos e tarefas do Batch no portal do Azure. Pode ver as informações de estado para estes e outros recursos ao executar os trabalhos, e até mesmo transferir os ficheiros de nós de computação nos seus conjuntos. Por exemplo, pode transferir o `stderr.txt` de uma tarefa falhada, durante a resolução de problemas. Também pode transferir ficheiros de Ambiente de Trabalho Remoto (RDP) que pode utilizar para iniciar sessão em nós de computação.
* [Azure Batch Explorer][batch_labs]: o Batch Explorer (anteriormente denominado BatchLabs) é uma ferramenta de cliente autónoma, gratuita e rica em funcionalidades para ajudar a criar, depurar e monitorizar aplicações do Azure Batch. Transfira um [pacote de instalação](https://azure.github.io/BatchExplorer/) para Mac, Linux ou Windows.
* [Estaleiro de Lote Azure](https://github.com/Azure/batch-shipyard): O Estaleiro de Lote é uma ferramenta para ajudar a fornecer, executar e monitorizar o processamento de lotes baseado em contentores e as cargas de trabalho do HPC no Lote Azure.
* [Explorador de Armazenamento Azure][storage_explorer]: Embora não seja estritamente uma ferramenta de Lote Azure, o Explorador de Armazenamento é outra ferramenta valiosa para ter enquanto está a desenvolver e a depurar as suas soluções de Lote.

## <a name="additional-resources"></a>Recursos adicionais

- Para saber mais sobre o registo de eventos da aplicação do Batch, consulte [Log events for diagnostic evaluation and monitoring of Batch solutions](batch-diagnostics.md)(Registar eventos para avaliação de diagnóstico e monitorização de soluções do Batch). Para uma referência de eventos gerados pelo serviço do Batch, consulte [Batch Analytics](batch-analytics.md).
- Para obter informações sobre as variáveis de ambiente para nós de computação, consulte [Azure Batch compute node environment variables](batch-compute-node-environment-variables.md)(Variáveis de ambiente de nó de computação do Azure Batch).

## <a name="next-steps"></a>Passos seguintes

* Leia a [Descrição geral da funcionalidade Batch para programadores](batch-api-basics.md), com informações essenciais para todos os utilizadores que se preparam para utilizar o Batch. O artigo contém informações mais detalhadas sobre recursos do serviço Batch, como conjuntos, nós, trabalhos e tarefas, e as várias funcionalidades de API que pode utilizar ao criar a sua aplicação Batch.
* [Introdução à biblioteca do Azure Batch para .NET](tutorial-parallel-dotnet.md) para saber como utilizar C# e a biblioteca .NET do Batch para executar uma carga de trabalho simples utilizando um fluxo de trabalho Batch comum. Uma [versão do Python](tutorial-parallel-python.md) e um [tutorial do Node.js](batch-nodejs-get-started.md) também estão disponíveis.
* Transfira as [amostras de código no GitHub][github_samples] para ver como tanto o C# como o Python pode interagir com o Batch para agendar e processar amostras de cargas de trabalho.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: /java/api/overview/azure/batch
[api_java_mgmt]: /java/api/overview/azure/batch/managementapi
[api_java_jar]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_java_mgmt_jar]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: /dotnet/api/overview/azure/batch/
[api_net_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[api_rest_mgmt]: /rest/api/batchmanagement/
[api_net_mgmt]: /dotnet/api/overview/azure/batch/management
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: /javascript/api/overview/azure/batch/client
[api_nodejs_mgmt]: /javascript/api/overview/azure/batch/management
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_nodejs_mgmt_npm]: https://www.npmjs.com/package/azure-arm-batch
[api_python]: /python/api/overview/azure/batch/client
[api_python_mgmt]: /python/api/overview/azure/batch/management
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_python_mgmt_pypi]: https://pypi.python.org/pypi/azure-mgmt-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/module/az.batch/
[batch_rest]: /rest/api/batchservice/
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_labs]: https://azure.github.io/BatchExplorer/
[storage_explorer]: https://storageexplorer.com/
[portal]: https://portal.azure.com
