---
title: Sobre ambientes de aprendizagem automática Azure
titleSuffix: Azure Machine Learning
description: Neste artigo, aprenda as vantagens dos ambientes de aprendizagem automática, que permitem definições de dependência de aprendizagem automática reprodutível, auditáveis e portáteis em diversos alvos de computação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: BlackMist
ms.date: 07/08/2020
ms.openlocfilehash: e1b92563acd6983b1680cacc06a8f2d0789dddf1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91302507"
---
# <a name="what-are-azure-machine-learning-environments"></a>O que são ambientes de aprendizagem automática Azure?


Os ambientes de aprendizagem automática Azure são uma encapsulação do ambiente onde o seu treino de aprendizagem automática acontece. Especificam os pacotes Python, variáveis ambientais e configurações de software em torno dos seus scripts de treino e pontuação. Também especificam os tempos de execução (Python, Spark ou Docker). Os ambientes são entidades geridas e versadas dentro do seu espaço de trabalho machine learning que permitem fluxos de trabalho de aprendizagem automática reprodutível, auditáveis e portáteis através de uma variedade de alvos computacional.

Pode utilizar um `Environment` objeto no seu cálculo local para:
* Desenvolva o seu roteiro de treino.
* Reutilizar o mesmo ambiente no Azure Machine Learning Compute para a formação de modelos à escala.
* Desdobre o seu modelo com o mesmo ambiente.
* Reveja o ambiente em que um modelo existente foi treinado.

O diagrama que se segue ilustra como pode utilizar um único `Environment` objeto tanto na configuração de execução (para treino) como na configuração de inferência e implementação (para implementações de serviço web).

![Diagrama de um ambiente em fluxo de trabalho de aprendizagem automática](./media/concept-environments/ml-environment.png)

O ambiente, o alvo do cálculo e o roteiro de treino em conjunto formam a configuração de execução: a especificação completa de uma corrida de treino.

## <a name="types-of-environments"></a>Tipos de ambientes

Os ambientes podem ser amplamente divididos em três categorias: *curado,* *gerido pelo utilizador*e gerido pelo *sistema.*

Ambientes curados são fornecidos pela Azure Machine Learning e estão disponíveis no seu espaço de trabalho por padrão. Destinados a ser usados como está, eles contêm coleções de pacotes python e configurações para ajudá-lo a começar com várias estruturas de aprendizagem automática. Estes ambientes pré-criados também permitem um tempo de implantação mais rápido. Para obter uma lista completa, consulte o [artigo ambientes curados.](resource-curated-environments.md)

Em ambientes geridos pelo utilizador, é responsável por configurar o seu ambiente e instalar todos os pacotes que o seu script de treino necessita no alvo do cálculo. A Conda não verifica o seu ambiente nem instala nada para si. Se está a definir o seu próprio ambiente, tem de listar `azureml-defaults` com a versão como uma dependência de `>= 1.0.45` pip. Este pacote contém a funcionalidade necessária para hospedar o modelo como um serviço web.

Usa ambientes geridos pelo sistema quando quer que a [Conda](https://conda.io/docs/) gere o ambiente Python e as dependências do script para si. Um novo ambiente conda é construído com base no objeto das dependências conda. O serviço Azure Machine Learning assume este tipo de ambiente por padrão, devido à sua utilidade em alvos de computação remota que não são manualmente configuráveis.

## <a name="create-and-manage-environments"></a>Criar e gerir ambientes

Pode criar ambientes através de:

* Definição de novos `Environment` objetos, quer utilizando um ambiente curado, quer definindo as suas próprias dependências.
* Utilizando `Environment` objetos existentes do seu espaço de trabalho. Esta abordagem permite consistência e reprodutibilidade com as suas dependências.
* Importando de uma definição ambiental de Anaconda existente.
* Utilização do CLI de Aprendizagem da Máquina azul
* [Utilização da extensão do Código VS](how-to-manage-resources-vscode.md#create-environment)

Para obter amostras de código específicas, consulte a secção "Criar um ambiente" de [como utilizar ambientes](how-to-use-environments.md#create-an-environment). Os ambientes também são facilmente geridos através do seu espaço de trabalho. Incluem a seguinte funcionalidade:

* Os ambientes são automaticamente registados no seu espaço de trabalho quando submete uma experiência. Também podem ser registados manualmente.
* Você pode buscar ambientes do seu espaço de trabalho para usar para treino ou implantação, ou para fazer edições para a definição de ambiente.
* Com a versão, pode ver alterações nos seus ambientes ao longo do tempo, o que garante a reprodutibilidade.
* Podes construir imagens Docker automaticamente a partir dos teus ambientes.

Para obter amostras de código, consulte a secção "Gerir ambientes" de [como utilizar ambientes](how-to-use-environments.md#manage-environments).

## <a name="environment-building-caching-and-reuse"></a>Construção do ambiente, caching e reutilização

O serviço Azure Machine Learning constrói definições ambientais em imagens e ambientes conda e estivadores. Também caches os ambientes para que possam ser reutilizados em treinos subsequentes e implementações de pontos finais de serviço. Executar um script de treino remotamente requer a criação de uma imagem de Docker, enquanto que, uma corrida local pode usar um ambiente Conda diretamente. 

### <a name="submitting-a-run-using-an-environment"></a>Submeter uma corrida usando um ambiente

Quando submete pela primeira vez uma execução remota utilizando um ambiente, o serviço de Aprendizagem automática Azure invoca uma [Tarefa de Construção de ACR](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview) no Registo de Contentores Azure (ACR) associado ao espaço de trabalho. A imagem de Docker construída é então em cache no Espaço de Trabalho ACR. Ambientes curados são apoiados por imagens de Docker que estão em cache no ACR Global. No início da execução, a imagem é recuperada pelo alvo do cálculo a partir do ACR relevante.

Para as corridas locais, um ambiente Docker ou Conda é criado com base na definição ambiental. Os scripts são então executados no cálculo alvo - um ambiente local de tempo de execução ou motor local Docker.

### <a name="building-environments-as-docker-images"></a>Construindo ambientes como imagens docker

Se a definição de ambiente já não existir no espaço de trabalho ACR, uma nova imagem será construída. A construção de imagens consiste em dois passos:

 1. Descarregar uma imagem base e executar quaisquer passos do Docker
 2. Construção de um ambiente conda de acordo com as dependências da conda especificadas na definição ambiental.

O segundo passo é omitido se especificar [as dependências geridas pelo utilizador](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py&preserve-view=true). Neste caso, você é responsável por instalar quaisquer pacotes Python, incluindo-os na sua imagem base, ou especificando passos personalizados do Docker dentro do primeiro passo. Também é responsável por especificar a localização correta para o Python executável. Também é possível usar uma [imagem base personalizada do Docker.](how-to-deploy-custom-docker-image.md)

### <a name="image-caching-and-reuse"></a>Caching e reutilização de imagens

Se utilizar a mesma definição de ambiente para outra execução, o serviço Azure Machine Learning reutiliza a imagem em cache a partir do Workspace ACR. 

Para ver os detalhes de uma imagem em cache, utilize [Environment.get_image_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-image-details-workspace-) método.

Para determinar se reutilizar uma imagem em cache ou construir uma nova, o serviço calcula [um valor de haxixe](https://en.wikipedia.org/wiki/Hash_table) da definição de ambiente e compara-a com as hashes dos ambientes existentes. O haxixe baseia-se em:
 
 * Valor da propriedade de imagem base
 * Estivador personalizado pisa o valor da propriedade
 * Lista de pacotes Python na definição de Conda
 * Lista de pacotes na definição de Faísca 

O haxixe não depende do nome ou versão do ambiente - se mudar o nome do seu ambiente ou criar um novo ambiente com as propriedades exatas e pacotes de um existente, então o valor do haxixe permanece o mesmo. No entanto, as alterações na definição do ambiente, tais como adicionar ou remover um pacote Python ou alterar a versão do pacote, fazem com que o valor do haxixe mude. Mudar a ordem das dependências ou canais num ambiente resultará num novo ambiente e, assim, exigirá uma nova construção de imagem. É importante notar que qualquer alteração a um ambiente curado invalidará o haxixe e resultará num novo ambiente "não curado".

O valor do haxixe calculado é comparado com o do Espaço de Trabalho e do ACR Global (ou no alvo de computação para corridas locais). Se houver uma correspondência, então a imagem em cache é puxada, caso contrário, uma construção de imagem é desencadeada. A duração para puxar uma imagem em cache inclui o tempo de download, enquanto a duração para puxar uma imagem recém-construída inclui tanto o tempo de construção como o tempo de descarregamento. 

O diagrama a seguir mostra três definições ambientais. Dois deles têm nomes e versões diferentes, mas imagem base idêntica e pacotes Python. Mas têm o mesmo haxixe e, portanto, correspondem à mesma imagem em cache. O terceiro ambiente tem diferentes pacotes e versões Python, e, portanto, corresponde a uma imagem em cache diferente.

![Diagrama de ambiente caching como imagens Docker](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> Se criar um ambiente com uma dependência de pacotes não pinados, por ```numpy``` exemplo, esse ambiente continuará a utilizar a versão pacote instalada _no momento da criação do ambiente_. Além disso, qualquer ambiente futuro com definição correspondente continuará a usar a versão antiga. 

Para atualizar o pacote, especifique um número de versão para forçar a reconstrução da imagem, por exemplo ```numpy==1.18.1``` . Serão instaladas novas dependências, incluindo as aninhadas, que poderão quebrar um cenário de trabalho anterior. 

> [!WARNING]
>  O método [Ambiente.build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#&preserve-view=truebuild-workspace--image-build-compute-none-) irá reconstruir a imagem em cache, com possível efeito colateral de atualizar pacotes não pinned e quebrar a reprodutibilidade para todas as definições ambientais correspondentes a essa imagem em cache.

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [criar e a utilizar ambientes](how-to-use-environments.md) em Azure Machine Learning.
* Consulte a documentação de referência python SDK para a [classe ambiente.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py&preserve-view=true)
* Consulte a documentação de referência R SDK para [ambientes.](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments)
