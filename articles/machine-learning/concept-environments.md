---
title: O que são ambientes de ML
titleSuffix: Azure Machine Learning
description: Neste artigo, Aprenda as vantagens dos ambientes de aprendizado de máquina, que permitem definições de dependência de aprendizado de máquina reproduzíveis, auditáveis e portáteis em diferentes destinos de computação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 01/06/2020
ms.openlocfilehash: 3216248943ccc0dba788816cdba38732f9e43e14
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930752"
---
# <a name="what-are-azure-machine-learning-environments"></a>O que são ambientes Azure Machine Learning?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ambientes especificam os pacotes python, as variáveis de ambiente e as configurações de software em relação aos seus scripts de treinamento e pontuação, e tempos de execução (Python, Spark ou Docker). Eles são entidades gerenciadas e com controle de versão em seu espaço de trabalho de Azure Machine Learning que permitem fluxos de trabalho de aprendizado de máquina reproduzíveis, auditáveis e portáteis entre diferentes destinos de computação.

Você pode usar um objeto de ambiente em sua computação local para desenvolver seu script de treinamento, reutilizar o mesmo ambiente em Azure Machine Learning computação para treinamento de modelo em escala e até mesmo implantar seu modelo com o mesmo ambiente.

O seguinte ilustra que o mesmo objeto de ambiente pode ser usado em sua configuração de execução para treinamento e em sua inferência e configuração de implantação para implantações de serviço Web.

![Diagrama de ambiente no fluxo de trabalho do Machine Learning](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Tipos de ambientes

Os ambientes podem ser amplamente divididos em três categorias: **organizadas**, **gerenciadas pelo usuário** e **gerenciadas pelo sistema**.

Os ambientes organizados são fornecidos por Azure Machine Learning e estão disponíveis em seu espaço de trabalho por padrão. Eles contêm coleções de pacotes e configurações do Python para ajudá-lo a começar estruturas de aprendizado de máquina diferentes. 

Para um ambiente gerenciado pelo usuário, você é responsável por configurar seu ambiente e instalar todos os pacotes de que seu script de treinamento precisa no destino de computação. O Conda não verificará seu ambiente nem instalará nada para você. Observe que, se você estiver definindo seu próprio ambiente, deverá listar `azureml-defaults` com a versão `>= 1.0.45` como uma dependência Pip. Esse pacote contém a funcionalidade necessária para hospedar o modelo como um serviço Web.

Ambientes gerenciados pelo sistema são usados quando você deseja que o [Conda](https://conda.io/docs/) gerencie o ambiente do Python e as dependências de script para você. O serviço assume esse tipo de ambiente por padrão, devido à sua utilidade em destinos de computação remota que não são configuráveis manualmente.

## <a name="creating-and-managing-environments"></a>Criando e gerenciando ambientes

Os ambientes podem ser criados por:

* Definindo novos objetos de `Environment`, usando um ambiente organizado ou definindo suas próprias dependências
* Usando objetos `Environment` existentes do seu espaço de trabalho. Isso permite a consistência e a reprodução com suas dependências
* Importando de uma definição de ambiente Anaconda existente.
* Usando a CLI do Azure Machine Learning

Consulte o [instruções](how-to-use-environments.md#create-an-environment) para obter exemplos de código específico. Os ambientes também são facilmente gerenciados por meio de seu espaço de trabalho e incluem a seguinte funcionalidade:

* Os ambientes são automaticamente registrados em seu espaço de trabalho quando você envia um experimento. Eles também podem ser registrados manualmente
* Busque ambientes do seu espaço de trabalho e use-os para treinamento, implantação ou faça edições na definição de ambiente
* O controle de versão permite que você veja as alterações em seus ambientes ao longo do tempo e garanta reprodução
* Crie imagens do Docker automaticamente de seus ambientes

Consulte a seção [gerenciar ambientes](how-to-use-environments.md#manage-environments) do instruções para obter exemplos de código.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [criar e usar ambientes](how-to-use-environments.md) no Azure Machine Learning
* Consulte os documentos de referência do SDK do Python para a [classe de ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Consulte os documentos de referência do SDK do R para [ambientes](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).