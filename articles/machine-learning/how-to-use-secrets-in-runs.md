---
title: Segredos de autenticação em formação
titleSuffix: Azure Machine Learning
description: Passe segredos para treino corre de forma segura usando Workspace Key Vault
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 89934470dc3bf86bb2843137a2129bff13323ca0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91302082"
---
# <a name="use-authentication-credential-secrets-in-azure-machine-learning-training-runs"></a>Use segredos credenciais de autenticação em ações de formação de machine learning Azure


Neste artigo, aprende-se a usar segredos em treinos de forma segura. As informações de autenticação, como o nome de utilizador e a palavra-passe, são segredos. Por exemplo, se ligar a uma base de dados externa para consultar dados de formação, terá de passar o seu nome de utilizador e palavra-passe para o contexto de execução remota. Codificar tais valores em scripts de treino em texto claro é inseguro, pois exporia o segredo. 

Em vez disso, o seu espaço de trabalho Azure Machine Learning tem um recurso associado chamado [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Utilize este Cofre-Chave para passar segredos para execuções remotas através de um conjunto de APIs no Azure Machine Learning Python SDK.

O fluxo padrão para usar segredos é:
 1. No computador local, faça login no Azure e ligue-se ao seu espaço de trabalho.
 2. No computador local, estabeleça um segredo no Cofre da Chave do Espaço de Trabalho.
 3. Envie uma execução remota.
 4. Dentro da corrida remota, pegue o segredo do Key Vault e use-o.

## <a name="set-secrets"></a>Definir segredos

Na Aprendizagem automática Azure, a aula [de Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true) contém métodos para definir segredos. Na sua sessão de Python local, obtenha primeiro uma referência ao seu espaço de trabalho Key Vault e, em seguida, use o [`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true#&preserve-view=trueset-secret-name--value-) método para definir um segredo pelo nome e valor. O __método set_secret__ atualiza o valor secreto se o nome já existir.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Não coloque o valor secreto no seu código Python, pois é inseguro guardá-lo em ficheiro como texto claro. Em vez disso, obtenha o valor secreto a partir de uma variável ambiental, por exemplo, Azure DevOps construir segredo, ou a partir da entrada interativa do utilizador.

Pode listar nomes secretos usando o [`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true#&preserve-view=truelist-secrets--) método e há também uma versão de lote,[set_secrets()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true#&preserve-view=trueset-secrets-secrets-batch-) que lhe permite definir vários segredos de cada vez.

## <a name="get-secrets"></a>Obter segredos

No seu código local, pode utilizar o [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-secret-name-) método para obter o valor secreto pelo nome.

Para as execuções [`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py&preserve-view=true#&preserve-view=truesubmit-config--tags-none----kwargs-)  submetidas, utilize o método com a [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-secret-name-) [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true) classe. Como uma execução submetida está ciente do seu espaço de trabalho, este método atalho o espaço de trabalho instantâneo e devolve o valor secreto diretamente.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Tenha cuidado para não expor o valor secreto escrevendo ou imprimindo.

Há também uma versão de lote, [get_secrets()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-secrets-secrets-) para aceder a múltiplos segredos ao mesmo tempo.

## <a name="next-steps"></a>Passos seguintes

 * [Ver caderno de exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Conheça a segurança da empresa com a Azure Machine Learning](concept-enterprise-security.md)
