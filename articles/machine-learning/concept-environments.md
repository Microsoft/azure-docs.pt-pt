---
title: Sobre ambientes de aprendizagem automática Azure
titleSuffix: Azure Machine Learning
description: Neste artigo, aprenda as vantagens dos ambientes de aprendizagem automática, que permitem definições de dependência de aprendizagem automática reprodutível, auditáveis e portáteis em diversos alvos de computação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 03/18/2020
ms.openlocfilehash: 50ddbffd00e0cbbd0641089613aaa40d03658c9e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80064196"
---
# <a name="what-are-azure-machine-learning-environments"></a>O que são ambientes de aprendizagem automática Azure?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ambientes de aprendizagem automática Azure especificam os pacotes Python, variáveis ambientais e configurações de software em torno dos seus scripts de treino e pontuação. Também especificam os tempos de execução (Python, Spark ou Docker). Os ambientes são entidades geridas e versadas dentro do seu espaço de trabalho machine learning que permitem fluxos de trabalho de aprendizagem automática reprodutível, auditáveis e portáteis através de uma variedade de alvos computacional.

Pode utilizar um `Environment` objeto no seu cálculo local para:
* Desenvolva o seu roteiro de treino.
* Reutilizar o mesmo ambiente no Azure Machine Learning Compute para a formação de modelos à escala.
* Desdobre o seu modelo com o mesmo ambiente.

O diagrama que se segue ilustra como pode utilizar um único `Environment` objeto tanto na configuração de execução, como na configuração da sua inferência e implementação, para implementações de serviço web.

![Diagrama de um ambiente em fluxo de trabalho de aprendizagem automática](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Tipos de ambientes

Os ambientes podem ser amplamente divididos em três categorias: *curado,* *gerido pelo utilizador*e gerido pelo *sistema.*

Ambientes curados são fornecidos pela Azure Machine Learning e estão disponíveis no seu espaço de trabalho por padrão. Eles contêm coleções de pacotes e configurações Python para ajudá-lo a começar com várias estruturas de aprendizagem automática. 

Em ambientes geridos pelo utilizador, é responsável por configurar o seu ambiente e instalar todos os pacotes que o seu script de treino necessita no alvo do cálculo. A Conda não verifica o seu ambiente nem instala nada para si. Se está a definir o seu próprio ambiente, tem de listar `azureml-defaults` com a versão como uma dependência de `>= 1.0.45` pip. Este pacote contém a funcionalidade necessária para hospedar o modelo como um serviço web.

Usa ambientes geridos pelo sistema quando quer que a [Conda](https://conda.io/docs/) gere o ambiente Python e as dependências do script para si. O serviço assume este tipo de ambiente por padrão, devido à sua utilidade em alvos de computação remota que não são manualmente configuráveis.

## <a name="create-and-manage-environments"></a>Criar e gerir ambientes

Pode criar ambientes através de:

* Definição de novos `Environment` objetos, quer utilizando um ambiente curado, quer definindo as suas próprias dependências.
* Utilizando `Environment` objetos existentes do seu espaço de trabalho. Esta abordagem permite consistência e reprodutibilidade com as suas dependências.
* Importando de uma definição ambiental de Anaconda existente.
* Utilização do CLI de Aprendizagem da Máquina azul

Para obter amostras de código específicas, consulte a secção "Criar um ambiente" de ambientes de [reutilização para treino e implantação](how-to-use-environments.md#create-an-environment). Os ambientes também são facilmente geridos através do seu espaço de trabalho. Incluem a seguinte funcionalidade:

* Os ambientes são automaticamente registados no seu espaço de trabalho quando submete uma experiência. Também podem ser registados manualmente.
* Você pode buscar ambientes do seu espaço de trabalho para usar para treino ou implantação, ou para fazer edições para a definição de ambiente.
* Com a versão, pode ver alterações nos seus ambientes ao longo do tempo, o que garante a reprodutibilidade.
* Podes construir imagens Docker automaticamente a partir dos teus ambientes.

Para obter amostras de código, consulte a secção "Gerir ambientes" de ambientes de [reutilização para treino e implantação](how-to-use-environments.md#manage-environments).

## <a name="environment-building-caching-and-reuse"></a>Construção do ambiente, caching e reutilização

O serviço Azure Machine Learning constrói definições ambientais em imagens e ambientes conda e estivadores. Também caches os ambientes para que possam ser reutilizados em treinos subsequentes e implementações de pontos finais de serviço.

### <a name="building-environments-as-docker-images"></a>Construindo ambientes como imagens docker

Normalmente, quando se submete uma corrida através de um ambiente, o serviço de Aprendizagem automática Azure invoca uma [Tarefa de Construção ACR](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview) no Registo de Contentores Azure (ACR) associado ao espaço de trabalho. A imagem de Docker construída é então em cache no Espaço de Trabalho ACR. No início da execução, a imagem é recuperada pelo alvo do cálculo.

A construção de imagens consiste em dois passos:

 1. Descarregar uma imagem base e executar quaisquer passos do Docker
 2. Construção de um ambiente conda de acordo com as dependências da conda especificadas na definição ambiental.

O segundo passo é omitido se especificar [as dependências geridas pelo utilizador](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py). Neste caso, você é responsável por instalar quaisquer pacotes Python, incluindo-os na sua imagem base, ou especificando passos personalizados do Docker dentro do primeiro passo. Também é responsável por especificar a localização correta para o Python executável.

### <a name="image-caching-and-reuse"></a>Caching e reutilização de imagens

Se utilizar a mesma definição de ambiente para outra execução, o serviço Azure Machine Learning reutiliza a imagem em cache a partir do Workspace ACR. 

Para ver os detalhes de uma imagem em cache, utilize o método [Ambiente.get_image_details.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-)

Para determinar se reutilizar uma imagem em cache ou construir uma nova, o serviço calcula [um valor de haxixe](https://en.wikipedia.org/wiki/Hash_table) da definição de ambiente e compara-a com as hashes dos ambientes existentes. O haxixe baseia-se em:
 
 * Valor da propriedade de imagem base
 * Estivador personalizado pisa o valor da propriedade
 * Lista de pacotes Python na definição de Conda
 * Lista de pacotes na definição de Faísca 

O haxixe não depende do nome ou versão do ambiente. As alterações na definição do ambiente, tais como adicionar ou remover um pacote Python ou alterar a versão do pacote, fazem com que o valor do haxixe mude e desencadeie uma reconstrução de imagem. No entanto, se simplesmente mudar o nome do seu ambiente ou criar um novo ambiente com as propriedades e pacotes exatos de um existente, então o valor do haxixe permanece o mesmo e a imagem em cache é usada.

Consulte o diagrama que mostra três definições ambientais. Dois deles têm nome e versão diferentes, mas imagem base idêntica e pacotes Python. Têm o mesmo haxixe e, portanto, correspondem à mesma imagem em cache. O terceiro ambiente tem diferentes pacotes e versões Python, e, portanto, corresponde a uma imagem em cache diferente.

![Diagrama de ambiente caching como imagens Docker](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> Se criar um ambiente com uma dependência de pacotes não pinados, por ```numpy``` exemplo, esse ambiente continuará a utilizar a versão pacote instalada _no momento da criação do ambiente_. Além disso, qualquer ambiente futuro com definição correspondente continuará a usar a versão antiga. 

Para atualizar o pacote, especifique um número de versão para forçar a reconstrução da imagem, por exemplo ```numpy==1.18.1``` . Note que novas dependências, incluindo as aninhadas serão instaladas que podem quebrar um cenário de trabalho anterior.

> [!WARNING]
>  O método [Ambiente.build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace--image-build-compute-none-) irá reconstruir a imagem em cache, com possível efeito colateral de atualizar pacotes não pinned e quebrar a reprodutibilidade para todas as definições ambientais correspondentes a essa imagem em cache.

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [criar e a utilizar ambientes](how-to-use-environments.md) em Azure Machine Learning.
* Consulte a documentação de referência python SDK para a [classe ambiente.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)
* Consulte a documentação de referência R SDK para [ambientes.](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments)
