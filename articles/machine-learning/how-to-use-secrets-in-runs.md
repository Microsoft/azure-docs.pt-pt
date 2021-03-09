---
title: Segredos de autenticação em formação
titleSuffix: Azure Machine Learning
description: Aprenda a passar segredos para treinos de forma segura usando o Azure Key Vault para o seu espaço de trabalho.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: b93da1e252357830578783c8f3ab5ca02f5a3e5b
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520749"
---
# <a name="use-authentication-credential-secrets-in-azure-machine-learning-training-runs"></a>Use segredos credenciais de autenticação em ações de formação de machine learning Azure


Neste artigo, aprende-se a usar segredos em treinos de forma segura. As informações de autenticação, como o nome de utilizador e a palavra-passe, são segredos. Por exemplo, se ligar a uma base de dados externa para consultar dados de formação, terá de passar o seu nome de utilizador e palavra-passe para o contexto de execução remota. Codificar tais valores em scripts de treino em texto claro é inseguro, pois exporia o segredo. 

Em vez disso, o seu espaço de trabalho Azure Machine Learning tem um recurso associado chamado [Azure Key Vault](../key-vault/general/overview.md). Utilize este Cofre-Chave para passar segredos para execuções remotas através de um conjunto de APIs no Azure Machine Learning Python SDK.

O fluxo padrão para usar segredos é:
 1. No computador local, faça login no Azure e ligue-se ao seu espaço de trabalho.
 2. No computador local, estabeleça um segredo no Cofre da Chave do Espaço de Trabalho.
 3. Envie uma execução remota.
 4. Dentro da corrida remota, pegue o segredo do Key Vault e use-o.

## <a name="set-secrets"></a>Definir segredos

Na Aprendizagem automática Azure, a aula [de Keyvault](/python/api/azureml-core/azureml.core.keyvault.keyvault) contém métodos para definir segredos. Na sua sessão de Python local, obtenha primeiro uma referência ao seu espaço de trabalho Key Vault e, em seguida, use o [`set_secret()`](/python/api/azureml-core/azureml.core.keyvault.keyvault#set-secret-name--value-) método para definir um segredo pelo nome e valor. O __método set_secret__ atualiza o valor secreto se o nome já existir.

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

Pode listar nomes secretos usando o [`list_secrets()`](/python/api/azureml-core/azureml.core.keyvault.keyvault#list-secrets--) método e há também uma versão de lote,[set_secrets()](/python/api/azureml-core/azureml.core.keyvault.keyvault#set-secrets-secrets-batch-) que lhe permite definir vários segredos de cada vez.

## <a name="get-secrets"></a>Obter segredos

No seu código local, pode utilizar o [`get_secret()`](/python/api/azureml-core/azureml.core.keyvault.keyvault#get-secret-name-) método para obter o valor secreto pelo nome.

Para as execuções [`Experiment.submit`](/python/api/azureml-core/azureml.core.experiment.experiment#submit-config--tags-none----kwargs-)  submetidas, utilize o método com a [`get_secret()`](/python/api/azureml-core/azureml.core.run.run#get-secret-name-) [`Run`](/python/api/azureml-core/azureml.core.run%28class%29) classe. Como uma execução submetida está ciente do seu espaço de trabalho, este método atalho o espaço de trabalho instantâneo e devolve o valor secreto diretamente.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Tenha cuidado para não expor o valor secreto escrevendo ou imprimindo.

Há também uma versão de lote, [get_secrets()](/python/api/azureml-core/azureml.core.run.run#get-secrets-secrets-) para aceder a múltiplos segredos ao mesmo tempo.

## <a name="next-steps"></a>Passos seguintes

 * [Ver caderno de exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Conheça a segurança da empresa com a Azure Machine Learning](concept-enterprise-security.md)