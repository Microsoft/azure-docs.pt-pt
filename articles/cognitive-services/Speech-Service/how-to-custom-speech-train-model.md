---
title: Treine e implemente um modelo de discurso personalizado - serviço de fala
titleSuffix: Azure Cognitive Services
description: Aprenda a treinar e implementar modelos de Discurso Personalizado. A formação de um modelo de voz para texto pode melhorar a precisão de reconhecimento para o modelo de base da Microsoft ou um modelo personalizado.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.openlocfilehash: 166fb94f5a3aea505c0d20df861b8bc4de9ad8aa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491117"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>Preparar e implementar um modelo de Voz Personalizada

Neste artigo, você vai aprender a treinar e implementar modelos de Discurso Personalizado. A formação de um modelo de voz para texto pode melhorar a precisão de reconhecimento para o modelo de base da Microsoft. Usa transcrições com rótulo humano e texto relacionado para treinar um modelo. Estes conjuntos de dados, juntamente com dados áudio previamente carregados, são usados para refinar e treinar o modelo de discurso-texto.

## <a name="use-training-to-resolve-accuracy-problems"></a>Use a formação para resolver problemas de precisão

Se estiver a encontrar problemas de reconhecimento com um modelo base, pode usar transcrições com rótulo humano e dados relacionados para treinar um modelo personalizado e ajudar a melhorar a precisão. Utilize esta tabela para determinar que conjunto de dados utilizar para resolver os seus problemas:

| Caso de utilização | Tipo de dados |
| -------- | --------- |
| Melhorar a precisão do reconhecimento no vocabulário e gramática específicos da indústria, como terminologia médica ou jargão de TI | Texto relacionado (frases/expressões) |
| Defina a forma fonética e exibida de uma palavra ou termo que tenha pronúncia não padrão, como nomes de produtos ou siglas | Texto relacionado (pronúncia) |
| Melhorar a precisão do reconhecimento em estilos de fala, acentos ou ruídos de fundo específicos | Transcrições com rótulo humano + áudio + |

## <a name="train-and-evaluate-a-model"></a>Treine e avalie um modelo

O primeiro passo para treinar um modelo é carregar dados de treino. Consulte [Preparar e testar os seus dados](./how-to-custom-speech-test-and-train.md) para obter instruções passo a passo para preparar transcrições com rótulo humano e textos relacionados (expressões e pronúncias). Depois de fazer o upload dos dados de treino, siga estas instruções para começar a treinar o seu modelo:

1. Inscreva-se no [portal de Discurso Personalizado](https://speech.microsoft.com/customspeech). Se planeia formar um modelo com conjuntos de dados de transcrição com rótulo áudio + humano, escolha uma subscrição de Discurso numa [região com hardware dedicado](custom-speech-overview.md#set-up-your-azure-account) para treino.
2. Vá à **Fala-a-texto**  >  **Discurso Personalizado**  >  **[nome do projeto]**  >  **Formação**.
3. Selecione **modelo de comboio**.
4. Dê ao seu treino um **nome** e **descrição.**
5. Na lista de **modelos Scenario e Baseline,** selecione o cenário que melhor se adequa ao seu domínio. Se não tem certeza de qual o cenário a escolher, selecione **General**. O modelo de base é o ponto de partida para o treino. O modelo mais recente é geralmente a melhor escolha.
6. Na página **de dados de formação Select,** escolha um ou mais conjuntos de dados de texto relacionados ou conjuntos de dados de transcrição com rótulo humano que pretende utilizar para a formação.

> [!NOTE]
> Quando treina um novo modelo, comece com texto relacionado; formação com transcrição áudio + etiqueta humana pode demorar muito mais tempo **(até [vários dias).](how-to-custom-speech-evaluate-data.md#add-audio-with-human-labeled-transcripts)**

> [!NOTE]
> Nem todos os modelos base suportam o treino com áudio. Se um modelo base não o suportar, o serviço Desemaguiso utilizará apenas o texto das transcrições e ignorará o áudio. Consulte [o suporte linguístico](language-support.md#speech-to-text) para uma lista de modelos base que suportam a formação com dados áudio.

> [!NOTE]
> Nos casos em que altera o modelo base utilizado para o treino, e tem áudio no conjunto de dados de treino, verifique *sempre* se o novo modelo base selecionado [suporta a formação com dados áudio](language-support.md#speech-to-text). Se o modelo base anteriormente utilizado não suportasse a formação com dados áudio, e o conjunto de dados de formação contiver áudio, o tempo de treino com o novo modelo base aumentará **drasticamente,** podendo facilmente passar de várias horas para vários dias e mais. Isto é especialmente verdade se a subscrição do seu serviço Desem declarações **não** estiver numa [região com o hardware dedicado](custom-speech-overview.md#set-up-your-azure-account) para a formação.
>
> Se encarar a questão descrita no parágrafo acima, pode diminuir rapidamente o tempo de treino reduzindo a quantidade de áudio no conjunto de dados ou removendo-o completamente e deixando apenas o texto. Esta última opção é altamente recomendada se a subscrição do serviço Speech **não** estiver numa [região com o hardware dedicado](custom-speech-overview.md#set-up-your-azure-account) para a formação.

7. Após o treino estar completo, pode fazer testes de precisão no modelo recém-treinado. Este passo é opcional.
8. Selecione **Criar** para construir o seu modelo personalizado.

A tabela **Training** apresenta uma nova entrada que corresponde ao novo modelo. A tabela também apresenta o estado: **Processamento,** **Sucesso** ou **Falhado**.

Consulte o [como avaliar](how-to-custom-speech-evaluate-data.md) e melhorar a precisão do modelo de discurso personalizado. Se optar por testar a precisão, é importante selecionar um conjunto de dados acústico que seja diferente daquele que usou com o seu modelo para obter uma noção realista do desempenho do modelo.

> [!NOTE]
> Tanto os modelos base como os modelos personalizados só podem ser utilizados até uma determinada data (ver [ciclo de vida Modelo e Endpoint).](./how-to-custom-speech-model-and-endpoint-lifecycle.md) O Speech Studio mostra esta data na coluna **Expiração** para cada modelo e ponto final. Após essa data, o pedido para um ponto final ou para a transcrição do lote pode falhar ou voltar ao modelo base.
>
> Retreine o seu modelo utilizando o modelo base mais recente para beneficiar de melhorias de precisão e evitar que o seu modelo expire.

## <a name="deploy-a-custom-model"></a>Implementar um modelo personalizado

Depois de fazer o upload e inspecionar dados, avaliar a precisão e treinar um modelo personalizado, pode implementar um ponto final personalizado para utilizar com as suas apps, ferramentas e produtos. 

Para criar um ponto final personalizado, inscreva-se no [portal De Discurso Personalizado](https://speech.microsoft.com/customspeech). Selecione **Implantação** no menu **Discurso Personalizado** no topo da página. Se esta for a sua primeira corrida, vai notar que não há pontos finais listados na tabela. Depois de criar um ponto final, utilize esta página para rastrear cada ponto final implantado.

Em seguida, **selecione Adicionar ponto final** e introduza um **Nome** e **Descrição** para o seu ponto de final personalizado. Em seguida, selecione o modelo personalizado que pretende associar ao ponto final.  Também pode ativar o registo a partir desta página. A sessão de registo permite-lhe monitorizar o tráfego do ponto final. Se o registo madeireira for desativado, o tráfego não será armazenado.

![Screenshot que mostra a página de novo ponto final.](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Não se esqueça de aceitar os termos de uso e detalhes de preços.

Em seguida, **selecione Criar**. Esta ação devolve-o à página **de Implementação.** A tabela agora inclui uma entrada que corresponde ao seu ponto final personalizado. O estado do ponto final mostra o seu estado atual. Pode levar até 30 minutos para instantaneaizar um novo ponto final usando os seus modelos personalizados. Quando o estado da implantação muda para **Concluir,** o ponto final está pronto a ser utilizado.

Depois de o seu ponto final ser implantado, o nome do ponto final aparece como um link. Selecione o link para ver informações específicas do seu ponto final, como a chave de ponto final, URL de ponto final e código de amostra. Tome nota da data de validade e atualize o modelo do ponto final antes dessa data para garantir um serviço ininterrupto.

## <a name="view-logging-data"></a>Ver dados de registo

Os dados de registo estão disponíveis para exportação se for à página do ponto final em **Implementações**.
> [!NOTE]
>Os dados de registo estão disponíveis durante 30 dias no armazenamento da Microsoft. Será removido depois. Se uma conta de armazenamento do cliente estiver ligada à subscrição dos Serviços Cognitivos, os dados de registo não serão automaticamente eliminados.

## <a name="next-steps"></a>Passos seguintes

* [Saiba como usar o seu modelo personalizado](how-to-specify-source-language.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Prepare e teste os seus dados](./how-to-custom-speech-test-and-train.md)
- [Fiscalizar os seus dados](how-to-custom-speech-inspect-data.md)
- [Avaliar os seus dados](how-to-custom-speech-evaluate-data.md)
