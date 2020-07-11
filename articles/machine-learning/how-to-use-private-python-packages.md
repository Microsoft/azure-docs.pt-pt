---
title: Use pacotes privados python
titleSuffix: Azure Machine Learning
description: Aceda a pacotes privados Python de forma segura a partir de Ambientes de Aprendizagem de Máquinas Azure.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: laobri
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: 580525b2e8e408949ce1d8f2d1b8241c431fc755
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209816"
---
# <a name="use-private-python-packages-with-azure-machine-learning"></a>Use pacotes privados python com aprendizagem automática Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprenda a usar pacotes privados python de forma segura dentro do Azure Machine Learning. As caixas de utilização para pacotes python privados incluem:

 * Desenvolveu um pacote privado que não quer partilhar publicamente.
 * Você quer usar um repositório curado de pacotes armazenados dentro de uma firewall da empresa.

A abordagem recomendada depende se você tem poucos pacotes para um único espaço de trabalho Azure Machine Learning, ou um repositório inteiro de pacotes para todos os espaços de trabalho dentro de uma organização.

Os pacotes privados são usados através da classe [Ambiente.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment) Dentro de um ambiente, você declara quais pacotes Python para usar, incluindo os privados. Para conhecer o ambiente em Azure Machine Learning em geral, consulte [Como usar ambientes.](how-to-use-environments.md) 

## <a name="prerequisites"></a>Pré-requisitos

 * [O Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
 * [Um espaço de trabalho de aprendizagem de máquinas Azure](how-to-manage-workspace.md)

### <a name="use-small-number-of-packages-for-development-and-testing"></a>Utilizar um pequeno número de pacotes para desenvolvimento e testes

Para um pequeno número de pacotes privados para um único espaço de trabalho, utilize o [`Environment.add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) método estático. Esta abordagem permite-lhe adicionar rapidamente um pacote privado ao espaço de trabalho, e é bem adequado para fins de desenvolvimento e teste.

Aponte o argumento do caminho do ficheiro para um ficheiro de roda local e execute o ```add_private_pip_wheel``` comando. O comando devolve um URL usado para rastrear a localização do pacote dentro do seu espaço de trabalho. Capture o URL de armazenamento e passe-o o `add_pip_package()` método.

```python
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

Internamente, o serviço Azure Machine Learning substitui o URL por URL SAS seguro, para que o seu ficheiro de roda seja mantido privado e seguro.

### <a name="consume-a-repository-of-packages-from-azure-devops-feed"></a>Consumir um repositório de pacotes da Azure DevOps

Se estiver a desenvolver ativamente pacotes Python para a sua aplicação de machine learning, pode acolhê-los num repositório Azure DevOps como artefactos e publicá-los como um feed. Esta abordagem permite-lhe integrar o fluxo de trabalho da DevOps para a construção de pacotes com o seu espaço de trabalho de aprendizagem de máquinas Azure. Para aprender a configurar feeds Python usando Azure DevOps, leia [Get Started with Python Packages in Azure Artifacts](https://docs.microsoft.com/azure/devops/artifacts/quickstarts/python-packages?view=azure-devops)

Esta abordagem utiliza o Personal Access Token para autenticar contra o repositório. A mesma abordagem aplica-se a outros repositórios com autenticação baseada em símbolos, como repositórios privados do GitHub. 

 1. [Crie um Token de Acesso Pessoal (PAT)](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=preview-page#create-a-pat) para a sua instância Azure DevOps. Definir o âmbito do token para __embalagem > Ler__. 

 2. Adicione o URL de Azure DevOps e PAT como propriedades de espaço de trabalho, utilizando o método [Workspace.set_connection.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#set-connection-name--category--target--authtype--value-)

     ```python
    from azureml.core import Workspace
    
    pat_token = input("Enter secret token")
    ws = Workspace.from_config()
    ws.set_connection(name="connection-1", 
        category = "PythonFeed",
        target = "https://<my-org>.pkgs.visualstudio.com", 
        authType = "PAT", 
        value = pat_token) 
     ```

 3. Crie um ambiente de aprendizagem automática Azure e adicione pacotes Python a partir do feed.
    
    ```python
    from azureml.core import Environment
    from azureml.core.conda_dependencies import CondaDependencies
    
    env = Environment(name="my-env")
    cd = CondaDependencies()
    cd.add_pip_package("<my-package>")
    cd.set_pip_option("--extra-index-url https://<my-org>.pkgs.visualstudio.com/<my-project>/_packaging/<my-feed>/pypi/simple")
    env.python.conda_dependencies=cd
    ```

O ambiente está agora pronto para ser usado em execuções de treino ou implementações de pontos finais de serviço web. Ao construir o ambiente, o serviço Azure Machine Learning utiliza o PAT para autenticar contra o feed com o URL de base correspondente.

### <a name="consume-a-repository-of-packages-from-private-storage"></a>Consumir um repositório de pacotes de armazenamento privado

Você pode consumir pacotes de uma conta de armazenamento Azure dentro da firewall da sua organização. Tal conta de armazenamento pode conter um conjunto curado de pacotes para uso empresarial ou um espelho interno de pacotes publicamente disponíveis.

Para instalar tal armazenamento privado:

 1. [Coloque o Espaço de Trabalho dentro de uma rede virtual (VNET)](how-to-enable-virtual-network.md).
 2. Crie uma conta de armazenamento e [não o acesso ao público.](https://docs.microsoft.com/azure/storage/common/storage-network-security)
 2. Coloque as embalagens Python que pretende utilizar num recipiente dentro da conta de armazenamento 
 3. [Permitir o acesso da conta de armazenamento a partir do Workspace VNET](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network) 

Em seguida, pode fazer referência aos pacotes na definição do ambiente Azure Machine Learning através do url completo no armazenamento de bolhas Azure.

## <a name="next-steps"></a>Passos seguintes

 * Saiba mais sobre [segurança empresarial em Azure Machine Learning](concept-enterprise-security.md)
