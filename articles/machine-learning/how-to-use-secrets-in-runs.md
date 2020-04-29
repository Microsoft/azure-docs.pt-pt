---
title: Use segredos em corridas de treino
titleSuffix: Azure Machine Learning
description: Passe segredos para treino saca de forma segura usando workspace Key Vault
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: d877794abf12b8b412cd1ecf4efd72fd1179d768
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78942274"
---
# <a name="use-secrets-in-training-runs"></a>Use segredos em corridas de treino
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a usar segredos no treino corre de forma segura. Informações de autenticação como o nome de utilizador e a palavra-passe são segredos. Por exemplo, se se ligar a uma base de dados externa para consultar dados de treino, terá de passar o seu nome de utilizador e palavra-passe para o contexto de execução remota. Codificar tais valores em scripts de treino em texto claro é inseguro, pois exporia o segredo. 

Em vez disso, o seu espaço de trabalho Azure Machine Learning tem um recurso associado chamado [Cofre chave Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Use este Cofre chave para passar segredos para corridas remotas de forma segura através de um conjunto de APIs no Azure Machine Learning Python SDK.

O fluxo básico para o uso de segredos é:
 1. No computador local, inicie sessão no Azure e ligue-se ao seu espaço de trabalho.
 2. No computador local, estabeleça um segredo no Workspace Key Vault.
 3. Submeta uma corrida remota.
 4. Dentro da corrida remota, pegue o segredo do Cofre chave e use-o.

## <a name="set-secrets"></a>Definir segredos

No Azure Machine Learning, a classe [Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) contém métodos para definir segredos. Na sua sessão local de Python, obtenha primeiro uma referência [`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) ao seu cofre chave do espaço de trabalho e, em seguida, use o método para definir um segredo pelo nome e valor. O método __set_secret__ atualiza o valor secreto se o nome já existir.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Não coloque o valor secreto no seu código Python, uma vez que é inseguro armazená-lo em ficheiro como texto claro. Em vez disso, obtenha o valor secreto de uma variável ambiental, por exemplo, os DevOps Azure constroem segredo, ou a partir da entrada interativa do utilizador.

Pode listar nomes [`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#list-secrets--) secretos usando o método e existe também uma versão de lote,[set_secrets()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) que lhe permite definir vários segredos de cada vez.

## <a name="get-secrets"></a>Obter segredos

No seu código local,[`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) pode usar o método para obter o valor secreto pelo nome.

Para as corridas submetidas, [`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-) utilize o [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) método com a [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) classe. Como uma execução submetida está ciente do seu espaço de trabalho, este método atalho a instantânea do Espaço de Trabalho e devolve o valor secreto diretamente.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Tenha cuidado para não expor o valor secreto escrevendo ou imprimindo-o.

Há também uma versão de lote, [get_secrets()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) para aceder a múltiplos segredos ao mesmo tempo.

## <a name="next-steps"></a>Passos seguintes

 * [Ver caderno de exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Conheça a segurança da empresa com a Azure Machine Learning](concept-enterprise-security.md)
