---
title: Usar segredos em execuções de treinamento
titleSuffix: Azure Machine Learning
description: Passe segredos para que o treinamento seja executado de modo seguro usando o espaço de trabalho Key Vault
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/23/2019
ms.custom: seodec18
ms.openlocfilehash: c0d696e3fc060a2779eba7d7e895397ea3245383
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489270"
---
# <a name="use-secrets-in-training-runs"></a>Usar segredos em execuções de treinamento
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a usar os segredos no treinamento em execução com segurança. Por exemplo, para se conectar a um banco de dados externo a fim de consultar dado de treinamento, você precisará passar o nome de usuário e a senha para o contexto de execução remota. Codificar esses valores em scripts de treinamento em texto não criptografado é inseguro, pois ele exporia o segredo. 

Em vez disso, seu Workspace do Azure Machine Learning tem [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) como recurso associado. Esse Key Vault pode ser usado para passar segredos para execuções remotas com segurança por meio de um conjunto de APIs no SDK do Azure Machine Learning Python

O fluxo básico para usar segredos é:
 1. No computador local, faça logon no Azure e conecte-se ao seu espaço de trabalho
 2. No computador local, defina um segredo no espaço de trabalho Key Vault
 3. Enviar uma execução remota.
 4. Na execução remota, obtenha o segredo do valor da chave e use-o.

## <a name="set-secrets"></a>Definir segredos

Em Azure Machine Learning SDK do Python, a classe [keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) contém métodos para definir segredos. Em sua sessão do Python local, primeiro obtenha uma referência para o espaço de trabalho Key Vault e, em seguida, use o método [set_secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) para definir um segredo por nome e valor.

```python
from azureml.core import Workspace
import os

ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Não coloque o valor secreto no código Python, pois ele é inseguro para armazená-lo no arquivo como texto não criptografado. Em vez disso, obtenha o valor secreto da variável de ambiente, por exemplo, o segredo de compilação do Azure DevOps ou da entrada interativa do usuário.

Você pode listar nomes de segredo usando o método [list_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) . O método __set_secret__ atualizará o valor secreto se o nome já existir.

## <a name="get-secrets"></a>Obter segredos

No código local, você pode usar o [keyvault. Obtenha](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) o método _secret para obter o valor secreto por nome.

Em execuções enviadas usando o [experimento. Submit](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-), use o método [Execute. Get _secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) . Como a execução enviada está ciente de seu espaço de trabalho, esse método faz o atalho da instanciação do espaço de trabalho e retorna o valor secreto diretamente.

```python
# Code in submitted run
from azureml.core import Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Tenha cuidado para não expor o valor secreto escrevendo ou imprimindo-o.

Os métodos set e Get também têm versões de lote [set_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) e [get_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) para acessar vários segredos de uma só vez.

## <a name="next-steps"></a>Passos seguintes

 * [Exibir bloco de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Saiba mais sobre o Enterprise Security com Azure Machine Learning](concept-enterprise-security.md)
