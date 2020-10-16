---
title: Ver detalhes do modelo - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: O separador de modelos sob qualquer projeto mostra detalhes de cada modelo, como nome de modelo, estado do modelo, pontuação BLEU, treino, afinação, contagem de frases de teste.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 52e305f9d50ab0f383810778be681ac41be97c72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88509803"
---
# <a name="view-model-details"></a>Ver detalhes do modelo

O separador Modelos em projeto mostra todos os modelos desse projeto. Todos os modelos treinados para este projeto estão listados neste separador.

Para cada modelo do projeto, estes detalhes são apresentados.

1. Nome do modelo: Mostra o nome do modelo de um dado modelo.

2. Estado: Mostra o estado de um determinado modelo. A sua nova formação terá um estatuto de Submissão até que seja aceite. O estado será alterado para o processamento de Dados enquanto o serviço avalia o conteúdo dos seus documentos. Quando a avaliação dos seus documentos estiver concluída, o estado mudará para Running e poderá ver o número de frases que fazem parte do treino, incluindo os conjuntos de afinação e testes que são criados automaticamente para si. Abaixo está uma lista de estado de modelo que descreve o estado dos modelos.

    - Submetido: Especifica que o backend está a processar os documentos para esse modelo.

    - FormaçãoQuesa: Especifica que a formação está a ser consultada ao sistema MT para esse modelo.

    - Em execução: Especifica que o treino está a decorrer no sistema MT para esse modelo.

    - Bem sucedido: Especifica que a formação foi bem sucedida no sistema MT e que está disponível um modelo. Neste estado, é apresentada uma pontuação BLEU para este modelo.

    - Implementado: Especifica que o modelo treinado com sucesso é submetido ao sistema MT para implantação.

    - Desplorar: Especifica que o modelo implantado não é despreocupado.

    - Não desoplorado: Especifica que o processo de desafetação de um modelo foi concluído com sucesso.

    - Treino Falhado: Especifica que o treino falhou. Se ocorrer uma falha de treino, refaça o trabalho de treino. Se o erro persistir, contacte-nos. Não apague o modelo falhado.

    - DataProcessingFailed: Especifica que o processamento de dados falhou por um ou mais documentos pertencentes ao modelo.

    - ImplementaçãoFailed: Especifica que a implementação do modelo falhou.

    - Corrente migratória: Especifica que o modelo está em estado de projeto após migração do Hub para o Tradutor Personalizado.

3. BLEU Score: mostra a pontuação BLEU (Subsudy de Avaliação Bilingue) do modelo, indicando a qualidade do seu sistema de tradução. Esta partitura diz-lhe como as traduções efetuadas pelo sistema de tradução resultantes deste treino correspondem às frases de referência no conjunto de dados de teste. A pontuação BLEU aparece se o treino estiver concluído com sucesso. Se o treino não estiver completo ou falhar, não verá nenhuma pontuação bleu.

4. Contagem de frases de formação: Mostra o número total de frases usadas como conjunto de treino.

5. Contagem de frases de sintonização: Mostra o número total de frases usadas como conjunto de afinação.

6.  Contagem de frases de formação: Mostra o número total de frases utilizadas como conjunto de testes.

7.  Contagem de frases mono: Mostra o número total de frases usadas como mono conjunto.

8.  Acionar o botão de ação: Para um modelo treinado com sucesso, mostra o botão "Implementar" se não for implantado. Se um modelo for implantado, é mostrado um botão "Impdeploy".

9. Excluir: Pode utilizar este botão se pretender eliminar o modelo. Excluir um modelo não apagará nenhum dos documentos utilizados para criar este modelo.

    ![Ver detalhes do modelo](media/how-to/how-to-view-model-details.png)

>[!Note]
>Para comparar treinos consecutivos para os mesmos sistemas, é importante manter o conjunto de afinação e o conjunto de testes constantes.

## <a name="view-model-training-details"></a>Ver detalhes de treino de modelo

Quando o seu treino estiver completo, pode rever detalhes sobre o treino a partir da página de detalhes. Selecione um projeto, localize e selecione o separador modelos e escolha um modelo.

A página do modelo tem dois separadores: Detalhes de treino e Teste.

1.  **Detalhes da formação:** Este separador mostra a lista de documentos utilizados na formação:

    -  Documentos Nome: Este campo mostra o nome do documento

    -  Tipo de documento: Este campo mostra se este documento é paralelo/mono.

    -  Contagem de frases em linguagem de origem: Este campo mostra que o número de frases existe como parte da linguagem de origem.

    -  Contagem de frases em linguagem-alvo: Este campo mostra que o número de frases existe como parte da linguagem-alvo.

    -  Frases alinhadas: Este campo mostra que o número de frases foi alinhado pelo Tradutor Personalizado durante o processo de alinhamento.

    -  Frases Usadas: Este campo mostra que o número de frases foi usado pelo Tradutor Personalizado durante este treino.

    ![Detalhes de formação de modelos](media/how-to/how-to-model-training-details.png)

2.  **Teste:** Este separador mostra os detalhes do teste para um treino bem sucedido.

## <a name="next-steps"></a>Passos seguintes

- Reveja [os resultados dos testes](how-to-view-system-test-results.md) e analise os resultados da formação.
