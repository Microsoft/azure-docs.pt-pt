---
title: Criar um serviço de voz personalizada - discurso
titleSuffix: Azure Cognitive Services
description: Quando estiver pronto para fazer o upload dos seus dados, vá ao portal Custom Voice. Crie ou selecione um projeto Custom Voice. O projeto deve partilhar a linguagem/local e as propriedades de género adequadas como os dados que pretende utilizar para a sua formação de voz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: bbe1d651a7d2d2cac1b1aa78b815b2797ad185c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717325"
---
# <a name="create-a-custom-voice"></a>Criar uma voz personalizada

Na Prepare os dados para voz [personalizada,](how-to-custom-voice-prepare-data.md)descrevemos os diferentes tipos de dados que pode utilizar para treinar uma voz personalizada e os diferentes requisitos de formato. Assim que tiver preparado os seus dados, pode começar a carregá-los para o [portal De Voz Personalizada,](https://aka.ms/custom-voice-portal)ou através da API de treino de Voz Personalizada. Aqui descrevemos os passos de treinar uma voz personalizada através do portal.

> [!NOTE]
> Esta página assume que leu [Começar com voz personalizada](how-to-custom-voice.md) e preparar dados para voz [personalizada,](how-to-custom-voice-prepare-data.md)e criou um projeto De Voz Personalizada.

Consulte os idiomas suportados para voz personalizada: [linguagem para personalização](language-support.md#customization).

## <a name="upload-your-datasets"></a>Faça upload dos seus conjuntos de dados

Quando estiver pronto para fazer o upload dos seus dados, vá ao [portal De Voz Personalizada](https://aka.ms/custom-voice-portal). Crie ou selecione um projeto Custom Voice. O projeto deve partilhar a linguagem/local e as propriedades de género adequadas como os dados que pretende utilizar para a sua formação de voz. Por exemplo, `en-GB` selecione se as gravações áudio que tem são feitas em inglês com sotaque do Reino Unido.

Vá ao separador **Dados** e clique em **dados de upload**. No assistente, selecione o tipo de dados correto que corresponda ao que preparou.

Cada conjunto de dados que envia deve satisfazer os requisitos para o tipo de dados que escolher. É importante formatar corretamente os seus dados antes de ser carregado. Isto garante que os dados serão processados com precisão pelo serviço Custom Voice. Vá à [Prepare os dados para Voz Personalizada](how-to-custom-voice-prepare-data.md) e certifique-se de que os seus dados foram devidamente formatados.

> [!NOTE]
> Os utilizadores de subscrição gratuita (F0) podem fazer o upload de dois conjuntos de dados simultaneamente. Os utilizadores de subscrição padrão (S0) podem carregar cinco conjuntos de dados simultaneamente. Se atingir o limite, aguarde pelo menos um dos seus conjuntos de dados que termine de importar. Então tente de novo.

> [!NOTE]
> O número máximo de conjuntos de dados autorizados a serem importados por subscrição é de 10 .ficheiros zip para utilizadores de subscrição gratuita (F0) e 500 para utilizadores de subscrição padrão (S0).

Os conjuntos de dados são automaticamente validados assim que carregar no botão de carregamento. A validação de dados inclui uma série de verificações nos ficheiros áudio para verificar o seu formato de ficheiro, tamanho e taxa de amostragem. Corrija os erros se houver e submeta novamente. Quando o pedido de importação de dados for iniciado com sucesso, deve ver uma entrada na tabela de dados que corresponda ao conjunto de dados que acaba de carregar.

O quadro seguinte mostra os estados de tratamento dos conjuntos de dados importados:

| Estado | Significado |
| ----- | ------- |
| Em processamento | O seu conjunto de dados foi recebido e está a ser tratado. |
| Bem-sucedido | O seu conjunto de dados foi validado e pode agora ser usado para construir um modelo de voz. |
| Falhou | O seu conjunto de dados foi falhado durante o processamento devido a muitas razões, por exemplo erros de ficheiros, problemas de dados ou problemas de rede. |

Após a validação estar completa, pode ver o número total de expressões correspondidas para cada um dos seus conjuntos de dados na coluna **Utterances.** Se o tipo de dados que selecionou requer segmentação de áudio longo, esta coluna apenas reflete as expressões que segmentamos para si, quer com base nas suas transcrições, quer através do serviço de transcrição da fala. Pode ainda descarregar o conjunto de dados validado para ver os resultados pormenorizados das expressões importadas com sucesso e as suas transcrições de mapeamento. Atenção: a segmentação de áudio longo pode demorar mais de uma hora a concluir o processamento de dados.

Para conjuntos de dados en-US e zh-CN, pode ainda fazer o download de um relatório para verificar as pontuações de pronúncia e o nível de ruído para cada uma das suas gravações. A pontuação da pronúncia varia de 0 a 100. Uma pontuação abaixo de 70 normalmente indica um erro de fala ou desajuste do script. Um sotaque pesado pode reduzir a sua pontuação de pronúncia e impactar a voz digital gerada.

Uma relação sinal-ruído mais elevada (SNR) indica ruído mais baixo no seu áudio. Você normalmente pode alcançar um SNR de 50+ gravando em estúdios profissionais. O áudio com um SNR abaixo de 20 pode resultar em ruído óbvio na sua voz gerada.

Considere regravar quaisquer declarações com baixas pontuações de pronúncia ou baixas relações sinal-ruído. Se não conseguir voltar a gravar, pode excluir essas declarações do seu conjunto de dados.

## <a name="build-your-custom-voice-model"></a>Construa o seu modelo de voz personalizado

Depois de validado o seu conjunto de dados, pode usá-lo para construir o seu modelo de voz personalizado.

1.  Navegue para o treino de > de **voz personalizada > texto a palavra.**

2.  Clique no **modelo de comboio**.

3.  Em seguida, insira um **Nome** e **Descrição** para ajudá-lo a identificar este modelo.

    Escolha um nome com cuidado. O nome que introduzir aqui será o nome que utilizar para especificar a voz no seu pedido de síntese de fala como parte da entrada SSML. Apenas letras, números e alguns caracteres de \_pontuação como -, e (', ' ' são permitidos. Use diferentes nomes para diferentes modelos de voz.

    Um uso comum do campo **Descrição** é registar os nomes dos conjuntos de dados que foram usados para criar o modelo.

4.  Na página de dados de **treino Select,** escolha um ou vários conjuntos de dados que gostaria de utilizar para treinar. Verifique o número de expressões antes de as submeter. Pode começar com várias expressões para modelos de voz en-US e zh-CN. Para outros locais, você deve selecionar mais de 2.000 expressões para ser capaz de treinar uma voz.

    > [!NOTE]
    > Os nomes áudio duplicados serão removidos do treino. Certifique-se de que os conjuntos de dados selecionados não contêm os mesmos nomes áudio em vários ficheiros .zip.

    > [!TIP]
    > A utilização dos conjuntos de dados do mesmo altifalante é necessária para obter resultados de qualidade. Quando os conjuntos de dados que submeteu para treino contiverem um número total de menos de 6.000 expressões distintas, irá treinar o seu modelo de voz através da técnica de Síntese Paramétrica Estatística. No caso de os seus dados de treino excederem um número total de 6.000 expressões distintas, iniciará um processo de treino com a técnica de Síntese de Concatenação. Normalmente, a tecnologia de concatenação pode resultar em resultados de voz mais naturais e de maior fidelidade. [Contacte a equipa Custom Voice](https://go.microsoft.com/fwlink/?linkid=2108737) se quiser treinar um modelo com a mais recente tecnologia Neural TTS que pode produzir uma voz digital equivalente às [vozes neurais](language-support.md#neural-voices)publicamente disponíveis.

5.  Clique em **Comboio** para começar a criar o seu modelo de voz.

A tabela formativa apresenta uma nova entrada que corresponde a este modelo recém-criado. A tabela também mostra o estado: Processamento, Sucesso, Falhado.

O estado que é mostrado reflete o processo de conversão do seu conjunto de dados para um modelo de voz, como mostrado aqui.

| Estado | Significado |
| ----- | ------- |
| Em processamento | O teu modelo de voz está a ser criado. |
| Bem-sucedido | O seu modelo de voz foi criado e pode ser implantado. |
| Falhou | O seu modelo de voz foi falhado no treino devido a muitas razões, por exemplo problemas de dados invisíveis ou problemas de rede. |

O tempo de formação varia consoante o volume de dados áudio processados. Os tempos típicos variam de cerca de 30 minutos para centenas de expressões a 40 horas para 20.000 expressões. Assim que o teu treino de modelo for bem sucedido, podes começar a testá-lo.

> [!NOTE]
> Os utilizadores de subscrição gratuita (F0) podem treinar simultaneamente uma fonte de voz. Os utilizadores de subscrição padrão (S0) podem treinar três vozes simultaneamente. Se atingir o limite, espere até que pelo menos uma das suas fontes de voz termine o treino e tente novamente.

> [!NOTE]
> O número máximo de modelos de voz autorizados a serem treinados por subscrição é de 10 modelos para utilizadores de subscrição gratuita (F0) e 100 para utilizadores de subscrição padrão (S0).

Se estiver a utilizar a capacidade de treino de voz neural, pode selecionar para treinar um modelo otimizado para cenários de streaming em tempo real, ou um modelo neural HD otimizado para síntese de [áudio longo](long-audio-api.md)assíncrono.  

## <a name="test-your-voice-model"></a>Teste o seu modelo de voz

Depois da sua fonte de voz ser construída com sucesso, pode testá-la antes de a utilizar.

1.  Navegue para o teste de > de **voz personalizada > texto-a-voz**.

2.  Clique no **teste Adicionar**.

3.  Selecione um ou vários modelos que gostaria de testar.

4.  Forneça o texto que pretende que a voz(s) fale. Se tiver selecionado para testar vários modelos de uma só vez, o mesmo texto será utilizado para os testes para diferentes modelos.

    > [!NOTE]
    > A linguagem do seu texto deve ser a mesma que a linguagem da sua fonte de voz. Só modelos treinados com sucesso podem ser testados. Neste passo, apenas o texto simples é apoiado.

5.  Clique em **Criar**.

Depois de ter apresentado o seu pedido de teste, voltará à página de teste. A tabela inclui agora uma entrada que corresponde ao seu novo pedido e à coluna de estado. Pode levar alguns minutos para sintetizar o discurso. Quando a coluna de estado diz **"Sucesso",** pode reproduzir o áudio ou descarregar a entrada de texto (um ficheiro .txt) e a saída de áudio (um ficheiro .wav), e fazer uma audição posterior para qualidade.

Também pode encontrar os resultados dos testes na página de detalhes de cada modelo selecionado para testes. Vá ao separador **Formação** e clique no nome do modelo para introduzir a página de detalhes do modelo.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Criar e usar um ponto final de voz personalizado

Depois de ter criado e testado com sucesso o seu modelo de voz, implementa-o num ponto final de Texto-a-Fala personalizado. Em seguida, utilize este ponto final no lugar do ponto final habitual ao fazer pedidos de texto para a fala através da API REST. O seu ponto final personalizado só pode ser chamado pela subscrição que utilizou para implementar a fonte.

Para criar um novo ponto final de voz personalizado, vá ao **Text-to-Speech > Custom Voice > Deployment**. **Selecione Adicionar ponto final** e introduza um **nome** e **descrição** para o seu ponto final personalizado. Em seguida, selecione o modelo de voz personalizado que gostaria de associar a este ponto final.

Depois de clicar no botão **Adicionar,** na tabela de pontofinal, verá uma entrada para o seu novo ponto final. Pode levar alguns minutos para instantaneamente um novo ponto final. Quando o estado da implantação for **bem sucedido,** o ponto final está pronto a ser utilizado.

> [!NOTE]
> Os utilizadores de subscrição gratuita (F0) só podem ter um modelo implementado. Os utilizadores de subscrição standard (S0) podem criar até 50 pontos finais, cada um com a sua própria voz personalizada.

> [!NOTE]
> Para utilizar a sua voz personalizada, deve especificar o nome do modelo de voz, utilizar o URI personalizado diretamente num pedido HTTP e utilizar a mesma subscrição para passar através da autenticação do serviço TTS.

Depois de o seu ponto final ser implantado, o nome final aparece como um link. Clique no link para visualizar informações específicas do seu ponto final, tais como a chave de ponto final, URL de ponto final e código de amostra.

Os testes online do ponto final também estão disponíveis através do portal de voz personalizado. Para testar o seu ponto final, escolha **o ponto final** da página de detalhes **endpoint.** A página de teste do ponto final aparece. Introduza o texto a ser falado (em texto simples ou [em formato SSML](speech-synthesis-markup.md) na caixa de texto. Para ouvir o texto falado na sua fonte de voz personalizada, selecione **Reproduzir**. Esta função de teste será carregada contra o uso personalizado da síntese da fala.

O ponto final personalizado é funcionalmente idêntico ao ponto final padrão que é usado para pedidos de texto a fala. Consulte a [REST API](rest-text-to-speech.md) para mais informações.

## <a name="next-steps"></a>Passos seguintes

* [Guia: Grave as amostras de voz](record-custom-voice-samples.md)
* [Referência da API texto-a-fala](rest-text-to-speech.md)
* [API de áudio longo](long-audio-api.md)
