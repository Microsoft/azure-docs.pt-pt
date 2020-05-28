---
title: Ver detalhes do modelo - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Os modelos atabem em qualquer projeto que mostre detalhes de cada modelo, como nome do modelo, estado do modelo, pontuação BLEU, treino, afinação, contagem de frases de teste.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 56d947a6b2d236a04f2b71421a235120a9f02c76
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83994194"
---
# <a name="view-model-details"></a>Ver detalhes do modelo

O separador Models no âmbito do projeto mostra todos os modelos desse projeto. Todos os modelos treinados para esse projeto estão listados neste separador.

Para cada modelo do projeto, estes detalhes são apresentados.

1.  Nome do modelo: Mostra o nome modelo de um determinado modelo.

2.  Estado: Mostra o estado de um determinado modelo. A sua nova formação terá um estatuto de Submetida até ser aceite. O estado irá alterar-se para o processamento de Dados enquanto o serviço avalia o conteúdo dos seus documentos. Quando a avaliação dos seus documentos estiver completa, o estado mudará para Running e poderá ver o número de frases que fazem parte do treino, incluindo os conjuntos de afinação e teste que são criados automaticamente para si. Abaixo está uma lista de modelos que descreve o estado dos modelos.

    -  Submetido: Especifica que o backend está a processar os documentos para esse modelo.

    -  TrainingQueued: Especifica que o treino está a ser feito em fila para o sistema MT para esse modelo.

    -  Execução: Especifica que o treino está a decorrer no sistema MT para este modelo.

    -  Sucesso: Especifica que a formação foi bem sucedida no sistema MT e um modelo está disponível. Neste estado, é apresentada uma pontuação BLEU para este modelo.

    -  Implantado: Especifica que o modelo treinado bem sucedido é submetido ao sistema MT para implementação.

    -  Não ser implantado: Especifica que o modelo implantado não está a ser implantado.

    -  Não implantado: Especifica que o processo de não implantação de um modelo foi concluído com sucesso.

    -  Treino Falhado: Especifica que o treino falhou. Se ocorrer uma falha de treino, tente novamente o trabalho de treino. Se o erro persistir, contacte-nos. Não apague o modelo falhado.

    - Processamento de DadosFailed: Especifica que o tratamento de dados falhou em um ou mais documentos pertencentes ao modelo.

    - ImplementaçãoFalha: Especifica que a implementação do modelo falhou.

    - Migração Draft: Especifica que o modelo está em estado de projeto após migração do Hub para tradutor personalizado.

4.  Bleu Score: mostra bleu (Avaliação Bilingue Substudy) do modelo, indicando a qualidade do seu sistema de tradução. Esta partitura indica-lhe a proximidade das traduções feitas pelo sistema de tradução resultantes deste treino correspondem às frases de referência no conjunto de dados de teste. A pontuação BLEU aparece se o treino estiver concluído com sucesso. Se o treino não estiver completo/ falhado, não verá nenhuma pontuação de BLEU.

5.  Contagem de frases de formação: Mostra o número total de frases usadas como conjunto de treino.

6.  Contagem de frases de afinação: Mostra o número total de frases usadas como conjunto de afinação.

7.  Contagem de frases de formação: Mostra o número total de frases usadas como conjunto de testes.

8.  Contagem de frases mono: Mostra o número total de frases usadas como conjunto mono.

9.  Implementar botão de ação: Para um modelo treinado com sucesso, mostra o botão "Deploy" se não for implantado. Se um modelo for implantado, é mostrado um botão "Undeploy".

10. Eliminar: Pode utilizar este botão se pretender eliminar o modelo. Eliminar um modelo não apaga nenhum dos documentos utilizados para criar esse modelo.

    ![Ver detalhes do modelo](media/how-to/how-to-view-model-details.png)

>[!Note]
>Para comparar treinos consecutivos para os mesmos sistemas, é importante manter o conjunto de afinação e o conjunto de testes constantes.

## <a name="view-model-training-details"></a>Ver detalhes de formação de modelos

Quando o seu treino estiver completo, pode rever detalhes sobre o treino a partir da página de detalhes. Selecione um projeto, localize e selecione o separador de modelos e escolha um modelo.

A página modelo tem dois separadores: detalhes de treino e Teste.

1.  **Detalhes do treino:** Este separador mostra a lista de documentos utilizados na formação:

    -  Nome dos Documentos: Este campo mostra o nome do documento

    -  Tipo de documento: Este campo mostra se este documento é paralelo/mono.

    -  Contagem de frases na linguagem fonte: Este campo mostra que o número de frases está lá como parte da linguagem fonte.

    -  Contagem de frases na linguagem-alvo: Este campo mostra que o número de frases está lá como parte da linguagem-alvo.

    -  Frases alinhadas: Este campo mostra que o número de frases foi alinhado pelo Tradutor Personalizado durante o processo de alinhamento.

    -  Frases usadas: Este campo mostra que o número de frases foi usado pelo Tradutor Personalizado durante este treino.

    ![Detalhes de formação de modelos](media/how-to/how-to-model-training-details.png)

2.  **Teste:** Este separador mostra os detalhes do teste para um treino bem sucedido.

## <a name="next-steps"></a>Próximos passos

- Reveja [os resultados dos testes](how-to-view-system-test-results.md) e analise os resultados do treino.
