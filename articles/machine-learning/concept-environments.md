---
title: Sobre os ambientes de aprendizagem automática azure
titleSuffix: Azure Machine Learning
description: Neste artigo, aprenda as vantagens dos ambientes de aprendizagem automática, que permitem definições reprodutíveis, auditáveis e portáteis de dependência de machine learning em várias metas computacionais.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 01/06/2020
ms.openlocfilehash: 036efa27fb8d22c32f2f6bce1efe9dea300a3972
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302780"
---
# <a name="what-are-azure-machine-learning-environments"></a>O que são os ambientes de Aprendizagem automática Azure?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Os ambientes de Aprendizagem automática azure especificam os pacotes Python, variáveis ambientais e configurações de software em torno do seu treino e scripts de pontuação. Também especificam os tempos de execução (Python, Spark ou Docker). Os ambientes são geridos e entidades versonizadas dentro do seu espaço de trabalho machine learning que permitem fluxos de trabalho reprodutíveis, auditáveis e portáteis de aprendizagem automática através de uma variedade de alvos computacionais.

Pode usar um objeto `Environment` na sua computação local para:
* Desenvolva o seu roteiro de treino.
* Reutilizar o mesmo ambiente na Computação de Aprendizagem automática Azure para formação de modelos em escala.
* Implante o seu modelo com o mesmo ambiente.

O diagrama que se segue ilustra como pode utilizar um único objeto `Environment` na configuração do seu funcionamento, para treino, e na sua configuração de inferência e implementação, para implementações de serviços web.

![Diagrama de um ambiente no fluxo de trabalho de aprendizagem automática](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Tipos de ambientes

Os ambientes podem, em geral, ser divididos em três categorias: *curadoria,* *gestão do utilizador*e *gerido pelo sistema.*

Os ambientes com curadoria são fornecidos pela Azure Machine Learning e estão disponíveis no seu espaço de trabalho por padrão. Eles contêm coleções de pacotes python e configurações para ajudá-lo a começar com vários quadros de aprendizagem automática. 

Em ambientes geridos pelo utilizador, é responsável por configurar o seu ambiente e instalar todos os pacotes que o seu script de treino necessita no alvo da computação. A Conda não verifica o seu ambiente nem instala nada para si. Se está a definir o seu próprio ambiente, deve enumerar `azureml-defaults` com versão `>= 1.0.45` como uma dependência do pip. Este pacote contém a funcionalidade necessária para hospedar o modelo como um serviço web.

Você usa ambientes geridos pelo sistema quando você quer que [a Conda](https://conda.io/docs/) gere o ambiente Python e as dependências do script para você. O serviço assume este tipo de ambiente por defeito, devido à sua utilidade em alvos de computação remota que não são manualmente configuráveis.

## <a name="create-and-manage-environments"></a>Criar e gerir ambientes

Pode criar ambientes através de:

* Definindo novos objetos `Environment`, seja usando um ambiente curado ou definindo as suas próprias dependências.
* Utilizando objetos `Environment` existentes do seu espaço de trabalho. Esta abordagem permite consistência e reprodutibilidade com as suas dependências.
* Importando de uma definição ambiental existente da Anaconda.
* Utilização do CLI de aprendizagem automática azure

Para amostras de código específicas, consulte a secção "Criar um ambiente" dos [ambientes de reutilização para formação e implantação](how-to-use-environments.md#create-an-environment). Os ambientes também são facilmente geridos através do seu espaço de trabalho. Incluem a seguinte funcionalidade:

* Os ambientes estão automaticamente registados no seu espaço de trabalho quando submete uma experiência. Também podem ser registados manualmente.
* Você pode buscar ambientes do seu espaço de trabalho para usar para treinar ou implantação, ou para fazer edificações para a definição de ambiente.
* Com a versão, pode ver alterações nos seus ambientes ao longo do tempo, o que garante a reprodutibilidade.
* Pode construir imagens do Docker automaticamente a partir dos seus ambientes.

Para amostras de código, consulte a secção "Gerir ambientes" dos [ambientes de reutilização para treino e implantação](how-to-use-environments.md#manage-environments).

## <a name="environment-building-caching-and-reuse"></a>Construção ambiental, cache e reutilização

O serviço azure machine learning constrói definições ambientais em imagens e ambientes de conda do Docker. Também caches os ambientes para que possam ser reutilizados em treinos subsequentes e implementações de pontos finais de serviço.

### <a name="building-environments-as-docker-images"></a>Construindo ambientes como imagens do Docker

Normalmente, quando se submete pela primeira vez a uma corrida utilizando um ambiente, o serviço de Aprendizagem automática Azure invoca uma Tarefa de [Construção ACR](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview) no Registo de Contentores Azure (ACR) associado ao Espaço de Trabalho. A imagem de Docker construída é então emcache no Workspace ACR. No início da execução, a imagem é recuperada pelo alvo da computação.

A construção da imagem consiste em dois passos:

 1. Descarregar uma imagem base e executar quaisquer passos do Docker
 2. Construção de um ambiente de condomínio de acordo com dependências de conda especificadas na definição ambiental.

O segundo passo é omitido se especificar [as dependências geridas pelo utilizador](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py). Neste caso, é responsável pela instalação de quaisquer pacotes Python, incluindo-os na sua imagem base, ou especificando passos personalizados do Docker dentro do primeiro passo. Também é responsável por especificar a localização correta para o Python executável.

### <a name="image-caching-and-reuse"></a>Cache de imagem e reutilização

Se utilizar a mesma definição de ambiente para outra execução, o serviço de Aprendizagem automática Azure reutiliza a imagem em cache do Workspace ACR. 

Para ver os detalhes de uma imagem em cache, utilize o método [Ambiente.get_image_details.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-)

Para determinar se reutiliza uma imagem em cache ou construir uma nova, o serviço calcula um valor de [haxixe](https://en.wikipedia.org/wiki/Hash_table) da definição ambiental e compara-o com as hashes dos ambientes existentes. O hash baseia-se em:
 
 * Valor de propriedade de imagem base
 * Docker personalizado pisa o valor da propriedade
 * Lista de pacotes Python na definição de Conda
 * Lista de pacotes na definição de Spark 

O hash não depende do nome ou versão do ambiente. As alterações na definição do ambiente, tais como adicionar ou remover um pacote Python ou alterar a versão do pacote, faz com que o valor do hash mude e desencadeie uma reconstrução da imagem. No entanto, se simplesmente mudar o nome do seu ambiente ou criar um novo ambiente com as propriedades e pacotes exatos de um existente, então o valor do hash permanece o mesmo e a imagem em cache é usada.

Consulte o diagrama que mostra três definições de ambiente. Dois deles têm nome e versão diferentes, mas imagem base idêntica e pacotes Python. Têm o mesmo hash e, portanto, correspondem à mesma imagem em cache. O terceiro ambiente tem diferentes pacotes e versões Python, e, portanto, corresponde a uma imagem em cache diferente.

![Diagrama de ambiente caching como imagens docker](./media/concept-environments/environment-caching.png)

Se criar um ambiente com dependência de pacotes desafixado, por exemplo, ```numpy```, esse ambiente continuará a utilizar a versão do pacote instalada no momento da criação ambiental. Além disso, qualquer ambiente futuro com definição correspondente continuará a usar a versão antiga. Para atualizar o pacote, especifique um número de versão para forçar a reconstrução da imagem, por exemplo, ```numpy==1.18.1```. Note que novas dependências, incluindo as aninhadas serão instaladas que podem quebrar um cenário de trabalho anterior

> [!WARNING]
>  O método [Ambiente.build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace-) irá reconstruir a imagem em cache, com possível efeito colateral de atualizar pacotes não ligados e quebrar a reprodutibilidade para todas as definições ambientais correspondentes a essa imagem em cache.

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [criar e utilizar ambientes](how-to-use-environments.md) em Azure Machine Learning.
* Consulte a documentação de referência Python SDK para a [classe ambiental](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Consulte a documentação de referência R SDK para [ambientes](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).
