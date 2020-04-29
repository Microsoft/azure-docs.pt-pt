---
title: Avaliar a precisão para discurso personalizado - Serviço de discurso
titleSuffix: Azure Cognitive Services
description: Neste documento, você vai aprender a medir quantitativamente a qualidade do nosso modelo de discurso a texto ou o seu modelo personalizado. Os dados de transcrição audio + com etiqueta humana são necessários para testar a precisão, devendo ser fornecidos 30 minutos a 5 horas de áudio representativo.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: f710b8bfdd4dcfd3b7a63aa0b457036ab7037016
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74806101"
---
# <a name="evaluate-custom-speech-accuracy"></a>Avaliar a precisão da Voz Personalizada

Neste documento, você aprenderá a medir quantitativamente a qualidade do modelo de discurso-texto da Microsoft ou do seu modelo personalizado. Os dados de transcrição audio + com etiqueta humana são necessários para testar a precisão, devendo ser fornecidos 30 minutos a 5 horas de áudio representativo.

## <a name="what-is-word-error-rate-wer"></a>O que é taxa de erro de palavra (WER)?

O padrão da indústria para medir a precisão do modelo é a Taxa de *Erro de Palavras* (WER). O WER conta o número de palavras incorretas identificadas durante o reconhecimento e divide-se pelo número total de palavras fornecidas na transcrição com rótulo humano. Finalmente, esse número é multiplicado em 100% para calcular o WER.

![Fórmula WER](./media/custom-speech/custom-speech-wer-formula.png)

As palavras incorretamente identificadas enquadram-se em três categorias:

* Inserção (I): Palavras que são incorretamente adicionadas na transcrição da hipótese
* Eliminação (D): Palavras que não são detetadas na transcrição da hipótese
* Substituição (S): Palavras que foram substituídas entre referência e hipótese

Segue-se um exemplo:

![Exemplo de palavras mal identificadas](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Resolver erros e melhorar o WER

Pode utilizar o WER a partir dos resultados de reconhecimento da máquina para avaliar a qualidade do modelo que está a utilizar com a sua aplicação, ferramenta ou produto. Um WER de 5%-10% é considerado de boa qualidade e está pronto a ser usado. Um WER de 20% é aceitável, no entanto, você deve considerar formação adicional. Um WER de 30% ou mais sinaliza má qualidade e requer personalização e formação.

A forma como os erros são distribuídos é importante. Quando muitos erros de eliminação são encontrados, é geralmente devido à fraca força do sinal de áudio. Para resolver este problema, terá de recolher dados áudio mais próximos da fonte. Erros de inserção significam que o áudio foi gravado num ambiente ruidoso e que o crosstalk pode estar presente, causando problemas de reconhecimento. Os erros de substituição são frequentemente encontrados quando uma amostra insuficiente de termos específicos de domínio foi fornecida como transcrições com rótulo humano ou texto relacionado.

Ao analisar ficheiros individuais, pode determinar que tipo de erros existem e quais os erros que são exclusivos de um ficheiro específico. Compreender problemas ao nível dos ficheiros irá ajudá-lo a direcionar melhorias.

## <a name="create-a-test"></a>Criar um teste

Se quiser testar a qualidade do modelo de linha de base de voz para texto da Microsoft ou um modelo personalizado que treinou, pode comparar dois modelos lado a lado para avaliar a precisão. A comparação inclui o WER e os resultados do reconhecimento. Tipicamente, um modelo personalizado é comparado com o modelo de base da Microsoft.

Para avaliar os modelos lado a lado:

1. Inscreva-se no [portal da Fala Personalizada.](https://speech.microsoft.com/customspeech)
2. Navegue para **o discurso-a-texto > discurso personalizado > teste**.
3. Clique no **Teste adicionar**.
4. **Selecione Avaliar a precisão**. Dê ao teste um nome, descrição e selecione o seu conjunto de dados de transcrição com rótulo humano+ humano.
5. Selecione até dois modelos que gostaria de testar.
6. Clique em **Criar**.

Depois de o seu teste ter sido criado com sucesso, pode comparar os resultados lado a lado.

## <a name="side-by-side-comparison"></a>Comparação lado a lado

Uma vez concluído o teste, indicado pela alteração de estado para *Sucesso,* encontrará um número WER para ambos os modelos incluídos no seu teste. Clique no nome do teste para ver a página de detalhes de teste. Esta página de detalhes lista todas as expressões no seu conjunto de dados, indicando os resultados de reconhecimento dos dois modelos juntamente com a transcrição do conjunto de dados submetido. Para ajudar a inspecionar a comparação lado a lado, pode alternar vários tipos de erros, incluindo inserção, eliminação e substituição. Ao ouvir o áudio e comparar os resultados do reconhecimento em cada coluna, que mostra a transcrição com rótulo humano e os resultados de dois modelos de fala a texto, pode decidir qual o modelo que satisfaz as suas necessidades e onde são necessários treinos e melhorias adicionais.

## <a name="next-steps"></a>Passos seguintes

* [Preparar o seu modelo](how-to-custom-speech-train-model.md)
* [Implante o seu modelo](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Prepare e teste os seus dados](how-to-custom-speech-test-data.md)
* [Inspecione os seus dados](how-to-custom-speech-inspect-data.md)
