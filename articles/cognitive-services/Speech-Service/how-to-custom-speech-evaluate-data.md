---
title: Avaliar exatidão para conversão de voz personalizada - serviços de voz
titlesuffix: Azure Cognitive Services
description: Neste documento, aprenderá como qualitativa medir a qualidade do modelo de voz em texto da Microsoft ou o seu modelo personalizado. Dados de transcrição de áudio + humanos etiqueta é necessária para testar a precisão e 30 minutos a 5 horas de áudio representativo deve ser fornecidos.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 2e9818fad9a0b5d04cc50a293b16d838c319dd86
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606578"
---
# <a name="evaluate-custom-speech-accuracy"></a>Avaliar a precisão de conversão de voz personalizada

Neste documento, aprenderá como qualitativa medir a qualidade do modelo de voz em texto da Microsoft ou o seu modelo personalizado. Dados de transcrição de áudio + humanos etiqueta é necessária para testar a precisão e 30 minutos a 5 horas de áudio representativo deve ser fornecidos.

## <a name="what-is-word-error-rate-wer"></a>O que é a taxa de erro do Word (WER)?

É a norma para medir a precisão do modelo da indústria *taxa de erros do Word* (WER). WER conta o número de palavras incorretas identificadas durante o reconhecimento, em seguida, divide pelo número total de palavras fornecida na transcrição rotulado como humanos. Por fim, esse número é multiplicado por 100% para calcular o WER.

![Fórmula WER](./media/custom-speech/custom-speech-wer-formula.png)

Incorretamente identificado Outono de palavras em três categorias:

* Inserção de (I): Palavras que foram incorretamente adicionadas na transcrição de hipótese
* Eliminação (D): Palavras que são detectadas na transcrição hipótese
* Substituição do (S): Palavras que foram substituídas entre hipótese e de referência

Segue-se um exemplo:

![Exemplo de palavras incorretamente identificados](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Resolva os erros e melhorar o WER

Pode usar o WER dos resultados de reconhecimento de máquina para avaliar a qualidade do modelo que está a utilizar com a aplicação, a ferramenta ou o produto. Um WER de 5% - 10% é considerado como estando boa qualidade e está pronto a utilizar. Um WER de 20% é aceitável, no entanto, deve considerar o treinamento adicional. Um WER de 30% ou mais sinaliza a qualidade é fraca e requer personalização e treinamento.

Como os erros são distribuídos é importante. Quando são encontrados erros de eliminação de muitos, é normalmente devido a intensidade do sinal de áudio fraco. Para resolver este problema, terá de recolher mais perto dos dados de áudio para a origem. Erros de inserção significam que o áudio foi gravado num ambiente de ruído e conversa cruzada poderá estar presente, a causar problemas de reconhecimento. Muitas vezes são encontrados erros de substituição quando um exemplo insuficiente dos termos de específicas de domínio foi fornecido como qualquer uma das transcrições com etiqueta humanos ou relacionados com o texto.

Ao analisar ficheiros individuais, pode determinar que tipo de erros de existir, e os erros são exclusivos para um ficheiro específico. Entendendo problemas ao nível do ficheiro irão ajudá-lo a aprimoramentos de destino.

## <a name="create-a-test"></a>Criar um teste

Se quiser testar a qualidade do modelo de voz em texto da linha de base da Microsoft ou um modelo personalizado que Treinou, pode comparar dois modelos lado a lado para avaliar a precisão. A comparação inclui resultados WER e reconhecimento. Normalmente, um modelo personalizado é comparado com o modelo de linha de base da Microsoft.

Avaliar modelos lado a lado:

1. Navegue para **voz em texto > voz personalizada > teste**.
2. Clique em **adicionar teste**.
3. Selecione **avaliar exatidão**. Dê um nome, descrição, ao teste e selecione o conjunto de dados de transcrição de áudio + rotulado como humanos.
4. Selecione até dois modelos que pretende testar.
5. Clique em **Criar**.

Depois do teste tiver sido criado com êxito, pode comparar os resultados lado a lado.

## <a name="side-by-side-comparison"></a>Comparação lado a lado

Assim que o teste estiver concluído, indicado pela mudança de estado para *bem-sucedido*, encontrará um número WER para ambos os modelos incluídos no seu teste. Clique no nome do teste para ver a página de detalhes de teste. Esta página de detalhes apresenta uma lista de todas as expressões no conjunto de dados, que indica os resultados de reconhecimento de dois modelos, juntamente com a transcrição do conjunto de dados submetido. Para ajudar a inspecionar a comparação lado a lado, pode alternar vários tipos de erro incluindo inserção, exclusão e de substituição. Ao ouvir o áudio e comparar resultados de reconhecimento em cada coluna, que mostra a transcrição de etiqueta humanos e nos resultados de dois modelos de voz em texto, pode decidir qual modelo atenda às suas necessidades e onde estão os aprimoramentos e treinamento adicional é necessário.

## <a name="next-steps"></a>Passos Seguintes

* [Preparar o seu modelo](how-to-custom-speech-train-model.md)
* [Implementar o seu modelo](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Preparar e testar seus dados](how-to-custom-speech-test-data.md)
* [Inspecione os seus dados](how-to-custom-speech-inspect-data.md)
