---
title: Sobre ambientes de Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Neste artigo, Aprenda as vantagens dos ambientes de aprendizado de máquina, que permitem definições de dependência de aprendizado de máquina reproduzíveis, auditáveis e portáteis em uma variedade de destinos de computação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 01/06/2020
ms.openlocfilehash: 8906299cc9e2c000dab2ac9d2a345d9aaf238260
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045854"
---
# <a name="what-are-azure-machine-learning-environments"></a>O que são ambientes Azure Machine Learning?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Os ambientes de Azure Machine Learning especificam os pacotes python, as variáveis de ambiente e as configurações de software em relação aos scripts de treinamento e pontuação. Eles também especificam tempos de execução (Python, Spark ou Docker). Eles são entidades gerenciadas e com controle de versão em seu espaço de trabalho de Machine Learning que permitem fluxos de trabalho de aprendizado de máquina reproduzíveis, auditáveis e portáteis em uma variedade de destinos de computação.

Você pode usar um objeto `Environment` em sua computação local para:
* Desenvolva seu script de treinamento.
* Reutilize o mesmo ambiente em Azure Machine Learning computação para treinamento de modelo em escala.
* Implante seu modelo com o mesmo ambiente.

O diagrama a seguir ilustra como você pode usar um único objeto `Environment` em sua configuração de execução, para treinamento e sua configuração de inferência e implantação, para implantações de serviço Web.

![Diagrama de um ambiente no fluxo de trabalho do Machine Learning](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Tipos de ambientes

Os ambientes podem ser amplamente divididos em três categorias: *organizadas*, *gerenciadas pelo usuário*e *gerenciadas pelo sistema*.

Os ambientes organizados são fornecidos por Azure Machine Learning e estão disponíveis em seu espaço de trabalho por padrão. Eles contêm coleções de pacotes e configurações do Python para ajudá-lo a começar com várias estruturas do Machine Learning. 

Em ambientes gerenciados pelo usuário, você é responsável por configurar seu ambiente e instalar cada pacote de que seu script de treinamento precisa no destino de computação. O Conda não verifica seu ambiente nem instala nada para você. Se você estiver definindo seu próprio ambiente, deverá listar `azureml-defaults` com a versão `>= 1.0.45` como uma dependência Pip. Esse pacote contém a funcionalidade necessária para hospedar o modelo como um serviço Web.

Você usa ambientes gerenciados pelo sistema quando deseja que o [Conda](https://conda.io/docs/) gerencie o ambiente do Python e as dependências de script para você. O serviço assume esse tipo de ambiente por padrão, devido à sua utilidade em destinos de computação remota que não são configuráveis manualmente.

## <a name="create-and-manage-environments"></a>Criar e gerenciar ambientes

Você pode criar ambientes:

* Definir novos objetos de `Environment`, seja usando um ambiente organizado ou definindo suas próprias dependências.
* Usando objetos `Environment` existentes do seu espaço de trabalho. Essa abordagem permite a consistência e a reprodução com suas dependências.
* Importando de uma definição de ambiente Anaconda existente.
* Usando a CLI do Azure Machine Learning

Para obter exemplos de código específicos, consulte a seção "criar um ambiente" de [reutilizar ambientes para treinamento e implantação](how-to-use-environments.md#create-an-environment). Os ambientes também são facilmente gerenciados por meio de seu espaço de trabalho. Eles incluem a seguinte funcionalidade:

* Os ambientes são automaticamente registrados em seu espaço de trabalho quando você envia um experimento. Eles também podem ser registrados manualmente.
* Você pode buscar ambientes do seu espaço de trabalho para usar para treinamento ou implantação ou para fazer edições na definição do ambiente.
* Com o controle de versão, você pode ver as alterações em seus ambientes ao longo do tempo, o que garante reprodução.
* Você pode criar imagens do Docker automaticamente de seus ambientes.

Para obter exemplos de código, consulte a seção "gerenciar ambientes" de [reutilizar ambientes para treinamento e implantação](how-to-use-environments.md#manage-environments).

## <a name="next-steps"></a>Passos seguintes

* Saiba como [criar e usar ambientes](how-to-use-environments.md) no Azure Machine Learning.
* Consulte a documentação de referência do SDK do Python para a [classe de ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Consulte a documentação de referência do SDK do R para [ambientes](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).
