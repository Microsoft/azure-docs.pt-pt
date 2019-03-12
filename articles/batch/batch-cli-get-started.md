---
title: Introdução à CLI do Azure para Batch | Microsoft Docs
description: Obtenha uma introdução rápida aos comandos do Batch na CLI do Azure para gerir recursos do serviço Azure Batch
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: fcd76587-1827-4bc8-a84d-bba1cd980d85
ms.service: batch
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 07/24/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2bd67ebb977a37c75631f16fbbf4c7dbd6bf250
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57765375"
---
# <a name="manage-batch-resources-with-azure-cli"></a>Gerir os recursos do Batch com a CLI do Azure

A CLI do Azure é a experiência da linha de comandos do Azure para a gestão de recursos do Azure. Pode ser utilizada no macOS, no Linux e no Windows. A CLI do Azure está otimizada para gerir e administrar recursos do Azure a partir da linha de comandos. Pode utilizar o Azure CLI para gerir as suas contas do Azure Batch e os seus recursos, como agrupamentos, trabalhos e tarefas. Com a CLI do Azure, pode criar scripts de muitas das mesmas tarefas que desempenha com as APIs do Batch, o portal do Azure e os cdmlets do Batch PowerShell.

Este artigo disponibiliza uma descrição geral da utilização da [CLI do Azure versão 2.0](https://docs.microsoft.com/cli/azure) com o Batch. Veja [Introdução à CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) para obter uma descrição geral da utilização da CLI com o Azure.

## <a name="set-up-the-azure-cli"></a>Configurar a CLI do Azure

Pode executar a CLI do Azure mais recente no [Azure Cloud Shell](../cloud-shell/overview.md). Para instalar a CLI do Azure localmente, siga os passos descritos em [Install the Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (Instalar a CLI do Azure).

> [!TIP]
> Recomendamos que atualize frequentemente a instalação da CLI do Azure para tirar partido das atualizações e melhoramentos do serviço.
> 
> 

## <a name="command-help"></a>Ajuda do comando

Pode apresentar o texto de ajuda de cada comando na CLI do Azure, acrescentando `-h` aos comandos. Omita as outras opções. Por exemplo:

* Para obter ajuda para o comando `az`, introduza: `az -h`
* Para obter uma lista de todos os comandos do Batch na CLI, utilize: `az batch -h`
* Para obter ajuda sobre a criação de uma conta do Batch, introduza: `az batch account create -h`

Em caso de dúvida, utilize a opção da linha de comandos `-h` para obter ajuda sobre qualquer comando da CLI do Azure.



Além disso, veja a documentação de referência da CLI do Azure para obter detalhes sobre [os comandos da CLI do Azure para o Batch](/cli/azure/batch). 

## <a name="log-in-and-authenticate"></a>Iniciar sessão e autenticar

Para utilizar a CLI do Azure com o Batch, tem de iniciar sessão e autenticar-se. Tem de seguir dois simples passos:

1. **Iniciar sessão no Azure.** Iniciar sessão no Azure dá-lhe acesso a comandos do Azure Resource Manager, incluindo a comandos do [serviço de gestão do Batch](batch-management-dotnet.md).  
2. **Iniciar sessão na conta do Batch.** Iniciar sessão na conta do Batch dá-lhe acesso a comandos do serviço Batch.   

### <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Existem algumas formas diferentes de iniciar sessão no Azure, descritas em detalhe em [Iniciar sessão com a CLI do Azure](/cli/azure/authenticate-azure-cli):

1. [Iniciar sessão interativamente](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). Inicie sessão interativamente se estiver a executar comandos da CLI do Azure manualmente a partir da linha de comandos.
2. [Iniciar sessão com um principal de serviço](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). Inicie sessão com um principal de serviço se estiver a executar comandos da CLI do Azure a partir de um script ou de uma aplicação.

Para os objetivos deste artigo, vamos mostrar como iniciar sessão no Azure interativamente. Escreva [az login](https://docs.microsoft.com/cli/azure/reference-index#az-login) na linha de comandos:

```azurecli
# Log in to Azure and authenticate interactively.
az login
```

O comando `az login` devolve um token que pode utilizar para se autenticar, conforme mostrado aqui. Siga as instruções fornecidas para abrir uma página Web e submeter o token para o Azure:

![Iniciar sessão no Azure](./media/batch-cli-get-started/az-login.png)

Os exemplos apresentados na secção de scripts do shell de exemplo também mostram como iniciar a sessão de CLI do Azure através do registo para o Azure de forma interativa. Quando tiver sessão iniciada, pode chamar comandos para trabalhar com recursos de gestão do Batch, incluindo contas do Batch, chaves, pacotes de aplicações e quotas.  

### <a name="log-in-to-your-batch-account"></a>Iniciar sessão na conta do Batch

Para gerir recursos do Batch, como agrupamentos, trabalhos e tarefas, com a CLI do Azure, tem de iniciar sessão na sua conta do Batch e autenticar-se. Para iniciar sessão no serviço do Batch, utilize o comando [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az-batch-account-login). 

Tem duas opções para se autenticar na conta do Batch:

- **Com a autenticação do Azure Active Directory (Azure AD)** 

    A autenticação com o Azure AD é a opção predefinida quando utiliza a CLI do Azure com o Batch e a recomendada para a maioria dos cenários. 
    
    Ao iniciar sessão no Azure interativamente, conforme descrito na secção anterior, as suas credenciais são colocadas em cache, para que a CLI do Azure possa iniciar sessão por si na conta do Batch com as mesmas. Se iniciar sessão no Azure com um principal de serviço, essas credenciais também são utilizadas para iniciar sessão na sua conta do Batch.

    Uma vantagem do Azure AD é o facto de oferecer o controlo de acesso baseado em funções (RBAC). Com o RBAC, o acesso dos utilizadores depende da função que lhes é atribuída e não no facto de terem ou não as chaves das contas. Em vez de gerir chaves de contas, pode gerir funções do RBAC e deixar que seja o Azure AD a lidar com o acesso e a autenticação.  

     Para iniciar sessão na sua conta do Batch com o Azure AD, chame o comando [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az-batch-account-login): 

    ```azurecli
    az batch account login -g myresource group -n mybatchaccount
    ```

- **Com a autenticação de Chave Partilhada**

    A [autenticação de chave partilhada](/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service#authentication-via-shared-key) utiliza as chaves de acesso da sua conta para autenticar comandos da CLI do Azure para o serviço do Batch.

    Se estiver a criar scripts da CLI do Azure para automatizar a chamada de comandos do Batch, pode utilizar a autenticação de Chave Partilhada ou um principal de serviço do Azure AD. Em alguns cenários, pode ser mais fácil utilizar a autenticação de Chave Partilhada do que um principal de serviço.  

    Para iniciar sessão com a autenticação de Chave Partilhada, inclua a opção `--shared-key-auth` na linha de comandos:

    ```azurecli
    az batch account login -g myresourcegroup -n mybatchaccount --shared-key-auth
    ```

Os exemplos apresentados na secção de scripts do shell de exemplo mostram como iniciar sessão na sua conta do Batch com a CLI do Azure com os do Azure AD e a chave partilhada.

## <a name="use-azure-batch-cli-extension-commands"></a>Utilizar comandos da extensão CLI do Azure Batch

Ao instalar a extensão CLI do Azure Batch, pode utilizar a CLI do Azure para executar trabalhos do Batch ponto a ponto sem escrever código. Os comandos do Batch que a extensão suporta permitem-lhe utilizar modelos JSON para criar conjuntos, trabalhos e tarefas com a CLI do Azure. Também pode utilizar os comandos da CLI da extensão para carregar ficheiros de entrada de trabalhos para a conta do Armazenamento do Azure associada à conta do Batch, bem como transferir ficheiros de saída de trabalhos a partir da mesma. Para obter mais informações, veja [Use Azure Batch CLI templates and file transfer](batch-cli-templates.md) (Utilizar modelos da CLI do Azure Batch e a transferência de ficheiros).

## <a name="script-examples"></a>Exemplos de script

Veja [exemplos de script da CLI](cli-samples.md) para o Batch realizar tarefas comuns. Estes exemplos abrangem muitos dos comandos disponíveis na CLI do Azure para o Batch criar e gerir contas, conjuntos, trabalhos e tarefas. 

## <a name="json-files-for-resource-creation"></a>Ficheiros JSON para a criação de recursos

Ao criar recursos do Batch como conjuntos e tarefas, pode especificar um ficheiro JSON que contenha a configuração do novo recurso em vez de transmitir os respetivos parâmetros como opções de linha de comandos. Por exemplo:

```azurecli
az batch pool create my_batch_pool.json
```

Embora possa criar a maioria dos recursos do Batch apenas com as opções da linha de comandos, algumas funcionalidades exigem que especifique um ficheiro formatado como JSON que contenha os detalhes dos recursos. Por exemplo, tem de utilizar um ficheiro JSON se pretender especificar os ficheiros de recursos de uma tarefa de início.

Para ver a sintaxe do JSON necessário para criar um recurso, veja a documentação [Batch REST API reference][rest_api] (Referência à API REST do Batch). Cada tópico “Adicionar *tipo de recurso*” na referência à API REST contém scripts JSON de exemplo para criar esse mesmo recurso. Pode utilizar estes scripts JSON como modelos para os ficheiros JSON que vão ser utilizados com a CLI do Azure. Por exemplo, para ver a sintaxe de JSON para a criação de conjuntos, veja [Add a pool to an account][rest_add_pool] (Adicionar um conjunto a uma conta).

Para obter um script de exemplo que especifica um ficheiro JSON, veja [Run a job and tasks with Batch](./scripts/batch-cli-sample-run-job.md) (Executar um trabalho e tarefas com o Batch).

> [!NOTE]
> Se especificar um ficheiro JSON ao criar um recurso, todos os outros parâmetros que especificar na linha de comandos desse recurso são ignorados.
> 
> 

## <a name="efficient-queries-for-batch-resources"></a>Consultas eficientes para recursos do Batch

Cada tipo de recurso do Batch suporta um comando `list` que consulta a sua conta do Batch e apresenta uma lista de recursos desse tipo. Por exemplo, pode listar os conjuntos na sua conta e as tarefas num trabalho:

```azurecli
az batch pool list
az batch task list --job-id job001
```

Quando consulta o serviço Batch com uma operação `list`, pode especificar uma cláusula OData para limitar a quantidade de dados devolvidos. Uma vez que todas as filtragens ocorrem do lado do servidor, só são apresentados os dados que pedir. Utilize estas cláusulas para poupar largura de banda (e, por conseguinte, tempo) ao executar operações de lista.

A tabela seguinte descreve as cláusulas OData que o serviço Batch suporta:

| Cláusula | Descrição |
|---|---|
| `--select-clause [select-clause]` | Devolve um subconjunto de propriedades para cada entidade. |
| `--filter-clause [filter-clause]` | Devolve apenas as entidades que correspondem à expressão OData especificada. |
| `--expand-clause [expand-clause]` | Obtém as informações da entidade numa única chamada REST subjacente. Atualmente, a cláusula expand só suporta a propriedade `stats`. |

Para obter um script de exemplo que mostra como utilizar uma cláusula OData, veja [Run a job and tasks with Batch](./scripts/batch-cli-sample-run-job.md) (Executar um trabalho e tarefas com o Batch).

Para obter mais informações sobre como realizar consultas de lista eficazes com cláusulas OData, veja [Query the Azure Batch service efficiently](batch-efficient-list-queries.md) (Consultar o serviço Batch eficazmente).

## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

As sugestões seguintes podem ajudá-lo a resolver problemas da CLI do Azure:

* Utilize `-h` para obter o **texto da ajuda** de qualquer comando da CLI
* Utilize `-v` e `-vv` para apresentar a saída de comandos **detalhadas**. Se o sinalizador `-vv` for incluído, a CLI do Azure apresenta os pedidos e as respostas REST propriamente ditos. Estes parâmetros são úteis para apresentar a saída de erro completa.
* Pode ver a **saída de comandos como JSON** com a opção `--json`. Por exemplo, `az batch pool show pool001 --json` apresenta as propriedades de pool001 no formato JSON. Pode, em seguida, copiar e modificar esta saída para utilizar num `--json-file` (consulte os ficheiros JSON no início deste artigo).
<!---Loc Comment: Please, check link [JSON files] since it's not redirecting to any location.--->

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).
* Para obter mais informações sobre os recursos do Batch, veja [Descrição geral do Azure Batch para programadores](batch-api-basics.md).
* Para obter mais informações sobre como utilizar modelos do Batch para criar conjuntos, trabalhos e tarefas sem ter de escrever código, veja [Use Azure Batch CLI templates and file transfer](batch-cli-templates.md) (Utilizar modelos da CLI do Azure Batch e a transferência de ficheiros).

[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
