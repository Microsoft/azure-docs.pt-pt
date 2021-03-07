---
title: Experiências automatizadas de ML de resolução de problemas
titleSuffix: Azure Machine Learning
description: Aprenda a resolver problemas e a resolver problemas nas suas experiências automatizadas de aprendizagem automática de máquinas.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, automl, references_regions
ms.openlocfilehash: f2060d023e0c05fb368b6362bd137c7fd1afe848
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435678"
---
# <a name="troubleshoot-automated-ml-experiments-in-python"></a>Experiências automatizadas de ML em Python

Neste guia, aprenda a identificar e resolver problemas conhecidos nas suas experiências automatizadas de aprendizagem automática de máquinas com o [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).

## <a name="version-dependencies"></a>Dependências de versão

**`AutoML` dependências para versões de pacote mais recentes quebram compatibilidade**. Depois da versão 1.13.0 da SDK, os modelos não são carregados em SDKs mais antigos devido à incompatibilidade entre as versões mais antigas fixadas em `AutoML` pacotes anteriores, e as versões mais recentes fixadas hoje.

Espere erros como:

* Módulo não encontrou erros como,

  `No module named 'sklearn.decomposition._truncated_svd`

* Erros de importação tais como,

  `ImportError: cannot import name 'RollingOriginValidator'`,
* Atribuir erros tais como,

  `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`

As resoluções dependem da sua `AutoML` versão de formação SDK:

* Se a sua `AutoML` versão de treino SDK for superior a 1.13.0, precisa `pandas == 0.25.1` e `scikit-learn==0.22.1` .

    * Se houver uma incompatibilidade da versão, atualize scikit-learn e/ou pandas para corrigir a versão com o seguinte,

      ```bash
          pip install --upgrade pandas==0.25.1
          pip install --upgrade scikit-learn==0.22.1
      ```

* Se a sua `AutoML` versão de treino SDK for inferior ou igual a 1.12.0, precisa `pandas == 0.23.4` e `sckit-learn==0.20.3` .
  * Se houver uma incompatibilidade da versão, downgrade scikit-learn e/ou pandas para corrigir a versão com o seguinte,
  
    ```bash
      pip install --upgrade pandas==0.23.4
      pip install --upgrade scikit-learn==0.20.3
    ```

## <a name="setup"></a>Configuração

`AutoML` as alterações no pacote desde a versão 1.0.76 exigem que a versão anterior seja desinstalada antes da atualização para a nova versão.

* **`ImportError: cannot import name AutoMLConfig`**

    Se encontrar este erro depois de atualizar uma versão SDK antes de v1.0.76 para v1.0.76 ou posterior, resolva o erro executando: `pip uninstall azureml-train automl` e depois `pip install azureml-train-automl` . O automl_setup.cmd script faz isto automaticamente.

* **automl_setup falha**

  * No Windows, executar automl_setup a partir de um Pedido de Anaconda. [Instale o Miniconda](https://docs.conda.io/en/latest/miniconda.html).

  * Certifique-se de que a versão 4.4.10 ou posterior da conda 64 bits é instalada. Pode verificar a parte com o `conda info` comando. O `platform` deve ser para Windows ou `win-64` `osx-64` mac. Para verificar a versão, utilize o comando `conda -V` . Se tiver uma versão anterior instalada, pode atualizá-la utilizando o comando: `conda update conda` . Para verificar 32 bits correndo 

  * Certifique-se de que a conda está instalada. 

  * Linux - `gcc: error trying to exec 'cc1plus'`

    1. Se o `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` erro for encontrado, instale as ferramentas de construção GCC para a sua distribuição Linux. Por exemplo, em Ubuntu, utilize o comando `sudo apt-get install build-essential` .

    1. Passe um novo nome como o primeiro parâmetro para automl_setup para criar um novo ambiente conda. Ver ambientes conda existentes utilizando `conda env list` e remova-os com `conda env remove -n <environmentname>` .

* **automl_setup_linux.sh falha:** Se automl_setup_linus.sh falhar em Ubuntu Linux com o erro: `unable to execute 'gcc': No such file or directory`

  1. Certifique-se de que as portas de saída 53 e 80 estão ativadas. Numa máquina virtual Azure, pode fazê-lo a partir do portal Azure selecionando o VM e clicando em **Networking**.
  1. Executar o comando: `sudo apt-get update`
  1. Executar o comando: `sudo apt-get install build-essential --fix-missing`
  1. Corra `automl_setup_linux.sh` de novo

* **configuration.ipynb falha:**

  * Para a conda local, primeiro certifique-se de que `automl_setup` tem funcionada com sucesso.
  * Certifique-se de que o subscription_id está correto. Encontre o subscription_id no portal Azure selecionando Todos os Serviços e, em seguida, Subscrições. Os caracteres "<" e ">" não devem ser incluídos no valor subscription_id. Por exemplo, `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` tem o formato válido.
  * Garantir ao Contribuinte ou ao Proprietário o acesso à subscrição.
  * Verifique se a região é uma das regiões apoiadas: `eastus2` , , , , , , `eastus` `westcentralus` `southeastasia` `westeurope` `australiaeast` `westus2` . `southcentralus`
  * Garantir o acesso à região através do portal Azure.
  
* **workspace.from_config falha:**

  Se a chamada `ws = Workspace.from_config()` falhar:

  1. Certifique-se de que o portátil configuração.ipynb foi executado com sucesso.
  1. Se o portátil estiver a ser executado a partir de uma pasta que não esteja por baixo da pasta onde `configuration.ipynb` foi executada, copie a pasta aml_config e o ficheiro config.jsque contém para a nova pasta. Workspace.from_config lê o config.jsligado para a pasta do portátil ou para a pasta dos pais.
  1. Se estiver a ser utilizada uma nova subscrição, grupo de recursos, espaço de trabalho ou região, certifique-se de que volta a executar o `configuration.ipynb` caderno. A alteração config.jsem direto só funcionará se o espaço de trabalho já existir no grupo de recursos especificado ao abrigo da subscrição especificada.
  1. Se quiser alterar a região, altere o espaço de trabalho, o grupo de recursos ou a subscrição. `Workspace.create` não criará nem atualizará um espaço de trabalho se já existir, mesmo que a região especificada seja diferente.

## <a name="tensorflow"></a>TensorFlow

A partir da versão 1.5.0 do SDK, a aprendizagem automática de máquinas não instala por predefinição os modelos TensorFlow. Para instalar o TensorFlow e usá-lo com as suas experiências automáticas de ML, instale `tensorflow==1.12.0` via `CondaDependencies` .

```python
  from azureml.core.runconfig import RunConfiguration
  from azureml.core.conda_dependencies import CondaDependencies
  run_config = RunConfiguration()
  run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
```

## <a name="numpy-failures"></a>Falhas numpiáticas

* falha no Windows : **`import numpy` Alguns** ambientes windows vêem um erro de carregamento numpy com a versão python mais recente 3.6.8. Se vir esta edição, experimente com a versão Python 3.6.7.

* **`import numpy` falha**: Verifique a versão TensorFlow no ambiente automático ml conda. As versões suportadas são < 1.13. Desinstalar o TensorFlow do ambiente se a versão for >= 1.13.

Pode verificar a versão do TensorFlow e desinstalar da seguinte forma:

  1. Inicie uma concha de comando, ative o ambiente conda onde são instalados pacotes ml automatizados.
  1. `pip freeze`Insira e `tensorflow` procure, se encontrado, a versão listada deve ser < 1.13
  1. Se a versão listada não for uma versão suportada, `pip uninstall tensorflow` na concha de comando e insira y para confirmação.

## `jwt.exceptions.DecodeError`

Mensagem de erro exata: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` .

Para as versões SDK <= 1.17.0, a instalação pode resultar numa versão não suportada do PyJWT. Verifique se a versão PyJWT no ambiente automatizado ml conda é uma versão suportada. Esta é a versão PyJWT < 2.0.0.

Pode verificar a versão do PyJWT da seguinte forma:

1. Inicie uma concha de comando e ative o ambiente conda onde são instalados pacotes ML automatizados.

1. `pip freeze`Insira e `PyJWT` procure, se encontrado, a versão listada deve ser < 2.0.0

Se a versão listada não for uma versão suportada:

1. Considere o upgrade para a versão mais recente do AutoML SDK: `pip install -U azureml-sdk[automl]`

1. Se isso não for viável, desinstale o PyJWT do ambiente e instale a versão certa da seguinte forma:

    1. `pip uninstall PyJWT` na concha de comando e entre `y` para confirmação.
    1. Instale-o utilizando `pip install 'PyJWT<2.0.0'` .
  
## <a name="databricks"></a>Databricks
Ver [Como configurar uma experiência automatizada de ML com databricks](how-to-configure-databricks-automl-environment.md#troubleshooting).

## <a name="forecasting-r2-score-is-always-zero"></a>A previsão da pontuação R2 é sempre zero

 Esta questão surge se os dados de formação fornecidos tão bem como séries temporís imposições que contenham o mesmo valor para os `n_cv_splits`  +  `forecasting_horizon` últimos pontos de dados.

Se este padrão for esperado na sua série de tempo, pode mudar a sua métrica primária para **erro quadrado de raiz normalizado**.

## <a name="failed-deployment"></a>Implantação falhada

 Para versões <= 1.18.0 do SDK, a imagem base criada para implantação pode falhar com o seguinte erro: `ImportError: cannot import name cached_property from werkzeug` .

  Os seguintes passos podem contornar a questão:

  1. Descarregue o pacote de modelos
  1. Desaperte o pacote
  1. Implementar utilizando os ativos não aziçados

## <a name="sample-notebook-failures"></a>Falhas no caderno de amostras

 Se um caderno de amostras falhar com um erro que a propriedade, o método ou a biblioteca não existem:

* Certifique-se de que o núcleo correto foi selecionado no Caderno Jupyter. O núcleo é apresentado no lado superior direito da página do caderno. O padrão é *azure_automl*. O núcleo é salvo como parte do caderno. Se mudar para um novo ambiente conda, tem de selecionar o novo núcleo no caderno.

  * Para os Cadernos Azure, deve ser Python 3.6.
  * Para ambientes conda locais, deve ser o nome ambiente conda que especificou em automl_setup.

* Para garantir que o caderno é para a versão SDK que está a usar,
  * Verifique a versão SDK executando `azureml.core.VERSION` numa célula de Caderno Jupyter.
  * Pode baixar a versão anterior dos cadernos de amostras do GitHub com estes passos:
    1. Selecione o `Branch` botão
    1. Navegue até ao `Tags` separador
    1. Selecione a versão

## <a name="next-steps"></a>Passos seguintes

+ Saiba mais sobre [como treinar um modelo de regressão com machine learning automatizado](tutorial-auto-train-models.md) ou como treinar utilizando [aprendizagem automática de máquinas num recurso remoto.](how-to-auto-train-remote.md)

+ Saiba mais sobre [como e onde implementar um modelo.](how-to-deploy-and-where.md)