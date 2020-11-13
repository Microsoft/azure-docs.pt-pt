---
title: Treine e implemente um modelo de discurso personalizado - serviço de fala
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprende a treinar e implementar modelos de Discurso Personalizado. A formação de um modelo de voz para texto pode melhorar a precisão de reconhecimento para o modelo de base da Microsoft ou um modelo personalizado. Um modelo é treinado usando transcrições com rótulo humano e texto relacionado.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 34c0703ee7c335ca904a21bcce6ed44abc6dc13f
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555792"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>Treine e implemente um modelo de discurso personalizado

Neste artigo, você aprende a treinar e implementar modelos de Discurso Personalizado. A formação de um modelo de voz para texto pode melhorar a precisão de reconhecimento para o modelo de base da Microsoft. Um modelo é treinado usando transcrições com rótulo humano e texto relacionado. Estes conjuntos de dados, juntamente com dados áudio previamente carregados, são usados para refinar e treinar o modelo de fala-a-texto.

## <a name="use-training-to-resolve-accuracy-issues"></a>Use a formação para resolver problemas de precisão

Se estiver a encontrar problemas de reconhecimento com um modelo base, usar transcrições com rótulo humano e dados relacionados para treinar um modelo personalizado pode ajudar a melhorar a precisão. Utilize esta tabela para determinar que conjunto de dados utilizar para resolver os seus problemas):

| Caso de utilização | Tipo de dados |
| -------- | --------- |
| Melhorar a precisão do reconhecimento no vocabulário e gramática específicos da indústria, como terminologia médica ou jargão de TI. | Texto relacionado (frases/expressões) |
| Defina a forma fonética e exibida de uma palavra ou termo que tenha pronúncia não padrão, como nomes de produtos ou siglas. | Texto relacionado (pronúncia) |
| Melhore a precisão do reconhecimento em estilos de fala, acentos ou ruídos de fundo específicos. | Transcrições com rótulo humano + áudio + |

## <a name="train-and-evaluate-a-model"></a>Treine e avalie um modelo

O primeiro passo para treinar um modelo é carregar dados de treino. Utilize [prepare e teste os seus dados](how-to-custom-speech-test-data.md) para obter instruções passo a passo para preparar transcrições com rótulo humano e texto relacionado (expressões e pronúncias). Depois de ter carregado os dados de treino, siga estas instruções para começar a treinar o seu modelo:

1. Inscreva-se no [portal de Discurso Personalizado](https://speech.microsoft.com/customspeech).
2. Navegue **para discurso-a-texto > discurso personalizado > [nome do projeto] > Training**.
3. Clique **no modelo train**.
4. Em seguida, dê ao seu treino um **nome** e **descrição.**
5. A partir do menu de drop-down **do modelo Scenario e Baseline,** selecione o cenário que melhor se adequa ao seu domínio. Se não tem a certeza de qual cenário escolher, selecione **General**. O modelo de base é o ponto de partida para o treino. O modelo mais recente é geralmente a melhor escolha.
6. A partir da página **de dados de treino Select,** escolha um ou vários conjuntos de dados de transcrição com rótulo humano que gostaria de usar para treinar.
7. Uma vez concluído o treino, pode optar por realizar testes de precisão no modelo recém-treinado. Este passo é opcional.
8. Selecione **Criar** para construir o seu modelo personalizado.

A tabela Training apresenta uma nova entrada que corresponde a este modelo recém-criado. A tabela também apresenta o estado: Processamento, Sucesso, Falhado.

Consulte o [como avaliar](how-to-custom-speech-evaluate-data.md) e melhorar a precisão do modelo de discurso personalizado. Se optar por testar a precisão, é importante selecionar um conjunto de dados acústico que seja diferente daquele que usou com o seu modelo para obter uma noção realista do desempenho do modelo.

## <a name="deploy-a-custom-model"></a>Implementar um modelo personalizado

Depois de ter carregado e inspecionado dados, avaliado a precisão e treinado um modelo personalizado, pode implementar um ponto final personalizado para usar com as suas apps, ferramentas e produtos. 

Para criar um novo ponto final personalizado, inscreva-se no [portal Discurso Personalizado](https://speech.microsoft.com/customspeech) e selecione **Implementação** a partir do menu Desemesto de Discurso Personalizado no topo da página. Se esta for a sua primeira corrida, vai notar que não há pontos finais listados na tabela. Depois de criar um ponto final, use esta página para rastrear cada ponto final implantado.

Em seguida, **selecione Adicionar ponto final** e introduza um **Nome** e **Descrição** para o seu ponto de final personalizado. Em seguida, selecione o modelo personalizado que gostaria de associar a este ponto final. A partir desta página, também pode ativar o registo. A sessão de registo permite-lhe monitorizar o tráfego do ponto final. Se for desativado, o tráfego não será armazenado.

![Como implementar um modelo](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Não se esqueça de aceitar os termos de uso e detalhes de preços.

Em seguida, **selecione Criar**. Esta ação devolve-o à página **de Implementação.** A tabela agora inclui uma entrada que corresponde ao seu ponto final personalizado. O estado do ponto final mostra o seu estado atual. Pode levar até 30 minutos para instantaneaizar um novo ponto final usando os seus modelos personalizados. Quando o estado da implantação muda para **Concluir,** o ponto final está pronto a ser utilizado.

Depois de o seu ponto final ser implantado, o nome do ponto final aparece como um link. Clique no link para apresentar informações específicas do seu ponto final, como a chave de ponto final, URL de ponto final e código de amostra.

## <a name="view-logging-data"></a>Ver dados de registo

Os dados de registo estão disponíveis para download em **Detalhes > Endpoint**.
> [!NOTE]
>Os dados de registo estão disponíveis durante 30 dias no armazenamento da Microsoft e serão removidos posteriormente. Caso uma conta de armazenamento detida por um cliente esteja ligada à subscrição de serviços cognitivos, os dados de registo não serão automaticamente eliminados.

## <a name="next-steps"></a>Passos seguintes

* Saiba [como utilizar o seu modelo personalizado.](how-to-specify-source-language.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Prepare e teste os seus dados](how-to-custom-speech-test-data.md)
- [Fiscalizar os seus dados](how-to-custom-speech-inspect-data.md)
- [Avaliar os seus dados](how-to-custom-speech-evaluate-data.md)
