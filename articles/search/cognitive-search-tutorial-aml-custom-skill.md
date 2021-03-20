---
title: 'Exemplo: Criar e implementar uma habilidade personalizada com a Azure Machine Learning'
titleSuffix: Azure Cognitive Search
description: Este exemplo demonstra como usar a Azure Machine Learning para construir e implementar uma habilidade personalizada para o oleoduto de enriquecimento de IA da Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 98d8395236bf955eed88f36c03c96981fa0e4b6b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98745638"
---
# <a name="example-build-and-deploy-a-custom-skill-with-azure-machine-learning"></a>Exemplo: Construir e implementar uma habilidade personalizada com a Azure Machine Learning 

Neste exemplo, você usará o [conjunto de dados de avaliações](https://www.kaggle.com/datafiniti/hotel-reviews) de hotéis (distribuídos sob a licença Creative Commons [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/legalcode.txt)) para criar uma [habilidade personalizada](./cognitive-search-aml-skill.md) usando Azure Machine Learning para extrair sentimento baseado em aspetos das avaliações. Isto permite que a atribuição de sentimentos positivos e negativos no âmbito da mesma revisão seja corretamente atribuída a entidades identificadas como pessoal, sala, lobby ou piscina.

Para treinar o modelo de sentimento baseado em aspetos em Azure Machine Learning, você estará usando o [repositório de receitas nlp](https://github.com/microsoft/nlp-recipes/tree/master/examples/sentiment_analysis/absa). O modelo será então implantado como ponto final num cluster Azure Kubernetes. Uma vez implantado, o ponto final é adicionado ao gasoduto de enriquecimento como uma habilidade AML para utilização pelo serviço de Pesquisa Cognitiva.

Há dois conjuntos de dados fornecidos. Se desejar treinar o modelo por si mesmo, o ficheiro hotel_reviews_1000.csv é necessário. Prefere saltar o passo do treino? Descarregue o hotel_reviews_100.csv.

> [!div class="checklist"]
> * Criar um exemplo de Pesquisa Cognitiva Azure
> * Criar um espaço de trabalho Azure Machine Learning (o serviço de pesquisa e espaço de trabalho devem estar na mesma subscrição)
> * Treine e implemente um modelo para um cluster Azure Kubernetes
> * Ligue um gasoduto de enriquecimento de IA ao modelo implantado
> * Ingerir saída do modelo implementado como uma habilidade personalizada

> [!IMPORTANT] 
> Esta habilidade está atualmente em visualização pública. A funcionalidade de pré-visualização é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Atualmente não existe suporte .NET SDK.

## <a name="prerequisites"></a>Pré-requisitos

* Azure subscrição - obtenha uma [subscrição gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Serviço de Pesquisa Cognitiva](./search-get-started-arm.md)
* [Recurso de Serviços Cognitivos](../cognitive-services/cognitive-services-apis-create-account.md?tabs=multiservice%2cwindows)
* [Conta de armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Área de trabalho do Azure Machine Learning](../machine-learning/how-to-manage-workspace.md)

## <a name="setup"></a>Configuração

* Clone ou descarregue o conteúdo [do repositório](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)de amostras .
* Extrair conteúdo se o download for um ficheiro zip. Certifique-se de que os ficheiros são escritos.
* Ao configurar as contas e serviços Azure, copie os nomes e chaves de um ficheiro de texto facilmente acedido. Os nomes e chaves serão adicionados à primeira célula do caderno onde são definidas variáveis para aceder aos serviços Azure.
* Se não está familiarizado com a Azure Machine Learning e os seus requisitos, irá querer rever estes documentos antes de começar:
 * [Configure um ambiente de desenvolvimento para a aprendizagem automática Azure](../machine-learning/how-to-configure-environment.md)
 * [Criar e gerir espaços de trabalho de aprendizagem automática Azure no portal Azure](../machine-learning/how-to-manage-workspace.md)
 * Ao configurar o ambiente de desenvolvimento para a Azure Machine Learning, considere usar a [instância de computação baseada](../machine-learning/how-to-configure-environment.md#compute-instance) na nuvem para velocidade e facilidade no início.
* Faça o upload do ficheiro do conjunto de dados para um recipiente na conta de armazenamento. O ficheiro maior é necessário se desejar realizar o passo de treino no caderno. Se preferir saltar o passo de treino, recomenda-se o ficheiro mais pequeno.

## <a name="open-notebook-and-connect-to-azure-services"></a>Abra o caderno e ligue-se aos serviços da Azure

1. Coloque todas as informações necessárias para as variáveis que permitirão o acesso aos serviços Azure dentro da primeira célula e executar a célula.
1. A execução da segunda célula confirmará que ligou ao serviço de pesquisa da sua subscrição.
1. As secções 1.1 - 1.5 criarão a datatore de serviços de pesquisa, skillset, índice e indexante.

Neste ponto pode optar por saltar os passos para criar o conjunto de dados de formação e experimentar em Azure Machine Learning e saltar diretamente para registar os dois modelos que são fornecidos na pasta de modelos do repo GitHub. Se saltar estes passos, no caderno passará então para a secção 3.5, escreva o script de pontuação. Isto poupará tempo; os dados de descarregamento e upload de etapas podem demorar até 30 minutos para ser concluído.

## <a name="creating-and-training-the-models"></a>Criar e treinar os modelos

A secção 2 tem seis células que descarregam o ficheiro de incorporação de luvas do repositório de receitas nlp. Após o download, o ficheiro é depois enviado para a loja de dados Azure Machine Learning. O ficheiro .zip é de cerca de 2G e levará algum tempo para executar estas tarefas. Uma vez carregados, os dados de treino são então extraídos e agora está pronto para passar para a secção 3.

## <a name="train-the-aspect-based-sentiment-model-and-deploy-your-endpoint"></a>Treine o modelo de sentimento baseado em aspetos e implemente o seu ponto final

A secção 3 do caderno irá formar os modelos que foram criados na secção 2, registar esses modelos e implantá-los como ponto final num cluster Azure Kubernetes. Se não está familiarizado com Azure Kubernetes, é altamente recomendável que reveja os seguintes artigos antes de tentar criar um cluster de inferências:

* [Visão geral do serviço Azure Kubernetes](../aks/intro-kubernetes.md)
* [Kubernetes conceitos fundamentais para O Serviço Azure Kubernetes (AKS)](../aks/concepts-clusters-workloads.md)
* [Quotas, restrições de tamanho de máquina virtual e disponibilidade da região no Serviço Azure Kubernetes (AKS)](../aks/quotas-skus-regions.md)

A criação e implantação do cluster de inferência pode demorar até 30 minutos. É recomendado testar o serviço web antes de passar aos passos finais, atualizar o seu skillset e executar o indexante.

## <a name="update-the-skillset"></a>Atualizar o skillset

A secção 4 do caderno tem quatro células que atualizam o skillset e o indexante. Em alternativa, pode utilizar o portal para selecionar e aplicar a nova habilidade no skillset e, em seguida, executar o indexante para atualizar o serviço de pesquisa.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Active-Learning-with-Azure-Cognitive-Search/player#time=19m35s:paused/03/player]

No portal, vá ao Skillset e selecione o link Skillset Definition (JSON). O portal irá exibir o JSON do seu skillset que foi criado nas primeiras células do caderno. À direita do visor existe um menu suspenso onde pode selecionar o modelo de definição de habilidade. Selecione o modelo Azure Machine Learning (AML). fornecer o nome do espaço de trabalho Azure ML e o ponto final para o modelo implantado no cluster de inferências. O modelo será atualizado com o ponto final uri e a chave.

> :::image type="content" source="media/cognitive-search-aml-skill/portal-aml-skillset-definition.png" alt-text="Modelo de definição de skillset":::

Copie o modelo skillset da janela e cole-o na definição de skillset à esquerda. Editar o modelo para fornecer os valores em falta para:

* Nome
* Descrição
* Contexto
* Nome e fonte de 'entradas'
* Nome e nome de destino 'outputs'

Salve a habilidade.

Depois de guardar o skillset, vá ao indexante e selecione a ligação De definição de Indexer (JSON). O portal apresentará o JSON do indexante que foi criado nas primeiras células do caderno. Os mapeamentos do campo de saída terão de ser atualizados com mapeamentos de campo adicionais para garantir que o indexante pode manusear e passá-los corretamente. Guarde as alterações e, em seguida, selecione Executar. 

## <a name="clean-up-resources"></a>Limpar os recursos

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **de todos os recursos** ou **grupos** de recursos no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para ficar abaixo do limite.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Reveja a habilidade personalizada web api](./cognitive-search-custom-skill-web-api.md) 
>  [Saiba mais sobre a adição de competências personalizadas ao oleoduto de enriquecimento](./cognitive-search-custom-skill-interface.md)