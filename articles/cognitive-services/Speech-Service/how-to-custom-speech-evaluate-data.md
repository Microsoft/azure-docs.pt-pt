---
title: Avaliar a precisão para discurso personalizado - serviço de fala
titleSuffix: Azure Cognitive Services
description: Neste documento você aprenderá a medir quantitativamente a qualidade do nosso modelo de discurso-texto ou o seu modelo personalizado. Os dados de transcrição áudio + humanos são necessários para testar a precisão e devem ser fornecidos 30 minutos a 5 horas de áudio representativo.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: cadbe79bbe0af2b5cebacb3d0c7c4e910fc7dbb8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85856833"
---
# <a name="evaluate-custom-speech-accuracy"></a>Avaliar a precisão da Voz Personalizada

Neste documento, aprenderá a medir quantitativamente a qualidade do modelo de fala-a-texto da Microsoft ou o seu modelo personalizado. Os dados de transcrição áudio + humanos são necessários para testar a precisão e devem ser fornecidos 30 minutos a 5 horas de áudio representativo.

## <a name="what-is-word-error-rate-wer"></a>O que é a Taxa de Erro de Palavra (WER)?

A norma da indústria para medir a precisão do modelo é *a Taxa de Erro de Texto* (WER). O WER conta o número de palavras incorretas identificadas durante o reconhecimento e, em seguida, divide-se pelo número total de palavras fornecidas na transcrição com rótulo humano (mostrada abaixo como N). Finalmente, este número é multiplicado em 100% para calcular o WER.

![Fórmula WER](./media/custom-speech/custom-speech-wer-formula.png)

As palavras incorretamente identificadas enquadram-se em três categorias:

* Inserção (I): Palavras que são incorretamente adicionadas na transcrição da hipótese
* Supressão (D): Palavras que não são detetadas na transcrição da hipótese
* Substituição (S): Palavras que foram substituídas entre referência e hipótese

Eis um exemplo:

![Exemplo de palavras mal identificadas](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Resolver erros e melhorar o WER

Pode utilizar o WER a partir dos resultados de reconhecimento da máquina para avaliar a qualidade do modelo que está a utilizar com a sua aplicação, ferramenta ou produto. Um WER de 5%-10% é considerado de boa qualidade e está pronto a ser usado. Um WER de 20% é aceitável, no entanto, você pode considerar a formação adicional. Um WER de 30% ou mais sinaliza má qualidade e requer personalização e formação.

A forma como os erros são distribuídos é importante. Quando muitos erros de eliminação são encontrados, geralmente é devido à fraca força do sinal de áudio. Para resolver este problema, terá de recolher dados áudio mais próximos da fonte. Erros de inserção significam que o áudio foi gravado num ambiente ruidoso e a conversa cruzada pode estar presente, causando problemas de reconhecimento. Os erros de substituição são frequentemente encontrados quando uma amostra insuficiente de termos específicos do domínio foi fornecida como transcrições com rótulo humano ou texto relacionado.

Ao analisar ficheiros individuais, pode determinar que tipo de erros existem e quais os erros exclusivos de um ficheiro específico. Compreender problemas ao nível dos ficheiros irá ajudá-lo a direcionar as melhorias.

## <a name="create-a-test"></a>Criar um teste

Se quiser testar a qualidade do modelo de linha de base fala-a-texto da Microsoft ou um modelo personalizado que treinou, pode comparar dois modelos lado a lado para avaliar a precisão. A comparação inclui wer e resultados de reconhecimento. Normalmente, um modelo personalizado é comparado com o modelo de base da Microsoft.

Para avaliar os modelos lado a lado:

1. Inscreva-se no [portal de Discurso Personalizado](https://speech.microsoft.com/customspeech).
2. Navegue **para discurso a texto > > de discurso personalizado [nome do projeto] > Testes**.
3. Clique **em Adicionar Teste**.
4. Selecione **Avaliar a precisão**. Dê ao teste um nome, descrição e selecione o seu conjunto de dados de transcrição com rótulo humano + áudio + humano.
5. Selecione até dois modelos que gostaria de testar.
6. Clique em **Criar**.

Depois de o seu teste ter sido criado com sucesso, pode comparar os resultados lado a lado.

## <a name="side-by-side-comparison"></a>Comparação lado a lado

Uma vez concluído o teste, indicado pela alteração de estado para *'Sucesso',* encontrará um número WER para ambos os modelos incluídos no seu teste. Clique no nome do teste para ver a página de detalhes do teste. Esta página de detalhe lista todas as expressões no seu conjunto de dados, indicando os resultados de reconhecimento dos dois modelos juntamente com a transcrição do conjunto de dados submetido. Para ajudar a inspecionar a comparação lado a lado, pode alternar vários tipos de erros, incluindo inserção, eliminação e substituição. Ao ouvir o áudio e comparar os resultados de reconhecimento em cada coluna, que mostra a transcrição com rótulo humano e os resultados de dois modelos de discurso a texto, pode decidir qual o modelo que satisfaz as suas necessidades e onde são necessárias formações e melhorias adicionais.

## <a name="next-steps"></a>Passos seguintes

* [Preparar o seu modelo](how-to-custom-speech-train-model.md)
* [Implemente o seu modelo](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Prepare e teste os seus dados](how-to-custom-speech-test-data.md)
* [Fiscalizar os seus dados](how-to-custom-speech-inspect-data.md)
