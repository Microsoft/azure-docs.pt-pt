---
title: Ver detalhes do modelo - Translator personalizado
titleSuffix: Azure Cognitive Services
description: Separador de modelos em qualquer projeto mostra detalhes de cada modelo, como nome do modelo, o estado de modelo, pontuação BLEU, treinamento, otimização, testes de contagem de sentença.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 01d7d197983638e415798b0b73e7ac7168c8cfab
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192705"
---
# <a name="view-model-details"></a>Ver detalhes do modelo

O separador de modelos em projeto mostra todos os modelos desse projeto. Todos os modelos treinados para que o projeto está listado neste separador.

Para cada modelo do projeto, são apresentados os seguintes detalhes de modelo.

1.  Nome do modelo: Mostra o nome do modelo de um determinado modelo.

2.  Estado: Mostra o estado de um determinado modelo. Seu novo treinamento terão um Estado de submetida até que ele é aceite. O estado será alterado para processamento de dados, enquanto o serviço avalia o conteúdo dos seus documentos. Depois de concluída a avaliação dos seus documentos o estado será alterado para em execução e poderá ver o número de frases que fazem parte do treinamento, incluindo o ajuste e testes conjuntos que são criadas automaticamente para. Segue-se uma lista de estado de modelo que descreve o estado dos modelos.

    -  Submetido: Especifica que o back-end está a processar os documentos para esse modelo.

    -  TrainingQueued: Especifica que o treinamento é em fila para o sistema de MT para esse modelo.

    -  A executar: Especifica que o treinamento está em execução no sistema de MT para esse modelo.

    -  Foi efetuada com êxito: Especifica que o treinamento foi concluída com êxito no sistema de MT e um modelo está disponível. Este estado, uma pontuação de BLEU é apresentada para esse modelo.

    -  Implementado: Especifica que o modelo preparado com êxito foi submetido para o sistema de MT para implementação.

    -  Undeploying: Especifica que o modelo implementado é undeploying.

    -  Não implementadas: Especifica que o processo de undeployment de um modelo foi concluído com êxito.

    -  Falha de treinamento: Especifica que o treinamento falhou. Se ocorrer uma falha de treinamento, tente novamente a tarefa de preparação. Se o erro persistir, contacte-nos. Não elimine o modelo com falha.

    - DataProcessingFailed: Especifica que o processamento de dados falhou para um ou mais documentos que pertencem ao modelo.

    - DeploymentFailed: Especifica que a implementação de modelo falhou.

    - MigratedDraft: Especifica que o modelo está no estado de rascunho após a migração de Hub ao Translator personalizado.

4.  Pontuação de BLEU: mostra a pontuação de BLEU (Understudy bilingue avaliação) do modelo, que indica a qualidade do seu sistema de tradução. Esta pontuação indica como as traduções feitas pelo sistema de tradução resultante desta formação correspondem as frases de referência no conjunto de dados de teste. A pontuação de BLEU é apresentada se o treinamento foi concluído com êxito. Se o treinamento não está completa / com falhas, não verá qualquer pontuação BLEU.

5.  Contagem de sentença de treinamento: Mostra o número total de frases utilizado como conjunto de preparação.

6.  Ajuste a contagem de sentença: Mostra o número total de frases utilizado como conjunto de otimização.

7.  Contagem de sentença de treinamento: Mostra o número total de frases utilizado como conjunto de teste.

8.  Contagem de sentença mono: Mostra o número total de frases utilizado como conjunto mono.

9.  Botão de ação de implementar: Um modelo preparado com êxito, ele mostra o botão "Deploy" se não for implementada. Se um modelo é implementado, é apresentado um botão "Undeploy".

10. Elimine: Pode utilizar este botão se de que pretende eliminar o modelo. A eliminar um modelo não elimina qualquer um dos documentos utilizados para criar esse modelo.

    ![Ver detalhes do modelo](media/how-to/how-to-view-model-details.png)

>[!Note]
>Para comparar consecutivos treinamentos para os mesmos sistemas, é importante manter o ajuste definida e teste definido constante.

## <a name="view-model-training-details"></a>Ver detalhes de treinamento de modelo

Quando seu treinamento estiver concluído, pode rever os detalhes sobre o treinamento da página de detalhes. Selecione um projeto, localize e selecione o separador de modelos e escolha um modelo.

A página do modelo tem dois separadores: Detalhes de treinamento e teste.

1.  **Detalhes de treinamento:** Este separador mostra a lista de document(s) usado no treinamento:

    -  Nome de documentos: Este campo mostra o nome do documento

    -  Tipo de documento: Este campo mostra se este documento é paralela / mono.

    -  Contagem de frase no idioma de origem: Este campo mostra o número de frases estão lá como parte da linguagem de código.

    -  Contagem de frase no idioma de destino: Este campo mostra o número de frases estão lá como parte do idioma de destino.

    -  Alinhados frases: Este campo mostra o número de frases tem sido alinhado ao tradutor de personalizado durante a alinhar o processo.

    -  Utilizar frases: Este campo mostra o número de frases tem sido usado por personalizado Translator durante esta formação.

    ![Detalhes de treinamento de modelo](media/how-to/how-to-model-training-details.png)

2.  **Teste:** Este separador mostra os detalhes de teste para um treinamento bem-sucedido.

## <a name="next-steps"></a>Passos Seguintes

- Revisão [resultados de teste](how-to-view-system-test-results.md) e analisar os resultados de treinamento.
