---
title: 'Quickstart: A sua primeira consulta python'
description: Neste arranque rápido, siga os passos para ativar a biblioteca de Gráficos de Recursos para Python e faça a sua primeira consulta.
ms.date: 05/26/2020
ms.topic: quickstart
ms.openlocfilehash: 9d247f00bdfc5a5ac1642c853923dc8fc84d6e18
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83876621"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-python"></a>Quickstart: Execute a sua primeira consulta de gráfico de recursos usando Python

O primeiro passo para a utilização do Gráfico de Recursos Azure é verificar se as bibliotecas necessárias para python estão instaladas. Este quickstart acompanha-o através do processo de adição das bibliotecas à sua instalação Python.

No final deste processo, terá adicionado as bibliotecas à sua instalação Python e executará a sua primeira consulta de Resource Graph.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>Adicione a biblioteca de gráficos de recursos

Para permitir que python questione o Gráfico de Recursos Azure, a biblioteca deve ser adicionada. Esta biblioteca funciona onde a Python pode ser usada, incluindo [a batida no Windows 10](/windows/wsl/install-win10) ou instalada localmente.

1. Verifique se o último Python está instalado (pelo menos **3.8**). Se ainda não estiver instalado, descarregue-o em [Python.org](https://www.python.org/downloads/).

1. Verifique se o mais recente Azure CLI está instalado (pelo menos **2.5.1**). Se ainda não estiver instalado, consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli).

   > [!NOTE]
   > O Azure CLI é necessário para permitir que a Python utilize a **autenticação baseada no CLI** nos seguintes exemplos. Para obter informações sobre outras opções, consulte [Authenticate utilizando as bibliotecas de gestão Azure para Python](/azure/developer/python/azure-sdk-authenticate).

1. Autenticar através do Azure CLI.

   ```azurecli
   az login
   ```

1. No seu ambiente de eleição Python, instale as bibliotecas necessárias para o Azure Resource Graph:

   ```bash
   # Add the Resource Graph library for Python
   pip install azure-mgmt-resourcegraph

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Se a Python estiver instalada para todos os utilizadores, este comando deve ser executado a partir de uma consola elevada.

1. Valide que as bibliotecas foram instaladas. `azure-mgmt-resourcegraph`deve ser **2.0.0** ou superior, `azure-mgmt-resource` deve ser **9.0.0** ou superior, e `azure-cli-core` deve ser **2.5.0** ou superior.

   ```bash
   # Check each installed library
   pip show azure-mgmt-resourcegraph
   pip show azure-mgmt-resource
   pip show azure-cli-core
   ```

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta do Resource Graph

Com as bibliotecas Python adicionadas ao seu ambiente de eleição, é hora de experimentar uma simples consulta de Gráfico de Recursos. A consulta devolve os primeiros cinco recursos Azure com o **Nome** e **O Tipo** de Recursos de cada recurso.

1. Execute a sua primeira consulta do Gráfico de Recursos Azure utilizando as bibliotecas instaladas e o `resources` método:

   ```python
   # Import Azure Resource Graph library
   import azure.mgmt.resourcegraph as arg
   
   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import SubscriptionClient
   
   # Wrap all the work in a function
   def getresources( strQuery ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = [sub.as_dict() for sub in subClient.subscriptions.list()]
       subList = []
       for sub in subsRaw:
           subList.append(sub.get('subscription_id'))
       
       # Create Azure Resource Graph client and set options
       argClient = get_client_from_cli_profile(arg.ResourceGraphClient)
       argQueryOptions = arg.models.QueryRequestOptions(result_format="objectArray")
       
       # Create query
       argQuery = arg.models.QueryRequest(subscriptions=subList, query=strQuery, options=argQueryOptions)
       
       # Run query
       argResults = argClient.resources(argQuery)
   
       # Show JSON results
       print(argResults)
   
   getresources("Resources | project name, type | limit 5")
   ```

   > [!NOTE]
   > Como este exemplo de consulta não fornece um modificador de ordenação como `order by`, é provável que executar esta consulta várias vezes produza um conjunto diferente de recursos em cada pedido.

1. Atualize a chamada `getresources` e altere a consulta para `order by` a propriedade **Nome:**

   ```python
   getresources("Resources | project name, type | limit 5 | order by name asc")
   ```

   > [!NOTE]
   > Assim como na primeira consulta, é provável que executar esta consulta várias vezes produza um conjunto diferente de recursos em cada pedido. A ordem dos comandos da consulta é importante. Neste exemplo, `order by` vem depois de `limit`, Esta ordem de comando primeiro limita os resultados da consulta e, em seguida, ordena-os.

1. Atualize a chamada `getresources` e altere a consulta para primeiro `order by` a propriedade **Nome** e, em seguida, para os `limit` cinco melhores resultados:

   ```python
   getresources("Resources | project name, type | order by name asc | limit 5")
   ```

Quando a consulta final é executada várias vezes, assumindo que nada no seu ambiente está a mudar, os resultados devolvidos são consistentes e encomendados pela propriedade **Name,** mas ainda limitados aos cinco melhores resultados.

## <a name="clean-up-resources"></a>Limpar recursos

Se desejar remover as bibliotecas instaladas do seu ambiente Python, pode fazê-lo utilizando o seguinte comando:

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-resourcegraph
pip uninstall azure-mgmt-resource
pip uninstall azure-cli-core
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, adicionou as bibliotecas do Resource Graph ao seu ambiente Python e executou a sua primeira consulta. Para saber mais sobre a linguagem do Graph de Recursos, continue na página de detalhes da linguagem da consulta.

> [!div class="nextstepaction"]
> [Obtenha mais informações sobre a linguagem de consulta](./concepts/query-language.md)