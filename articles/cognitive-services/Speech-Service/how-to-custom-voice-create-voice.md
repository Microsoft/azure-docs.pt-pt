---
title: Criar um serviço de voz personalizada - serviço de fala
titleSuffix: Azure Cognitive Services
description: Quando estiver pronto para fazer o upload dos seus dados, vá ao portal Voz Personalizada. Criar ou selecionar um projeto De Voz Personalizada. O projeto deve partilhar a língua/localidade certa e as propriedades de género como os dados que pretende utilizar para a sua formação de voz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 3747033fcaf65e0c6da07e9f1bb625771958bb4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319066"
---
# <a name="create-a-custom-voice"></a>Criar uma voz personalizada

Na [Preparação de dados para Voz Personalizada,](how-to-custom-voice-prepare-data.md)descrevemos os diferentes tipos de dados que pode utilizar para treinar uma voz personalizada e os diferentes requisitos de formato. Uma vez preparados os seus dados, pode começar a enviá-los para o [portal Voz Personalizada](https://aka.ms/custom-voice-portal), ou através da API de formação de voz personalizada. Aqui descrevemos os passos de treinar uma voz personalizada através do portal.

> [!NOTE]
> Esta página assume que leu [Começar com a Voz Personalizada](how-to-custom-voice.md) e Preparar [dados para Voz Personalizada,](how-to-custom-voice-prepare-data.md)e criou um projeto de Voz Personalizada.

Consulte os idiomas suportados para obter voz personalizada: [linguagem para personalização](language-support.md#customization).

## <a name="upload-your-datasets"></a>Faça upload dos seus conjuntos de dados

Quando estiver pronto para fazer o upload dos seus dados, aceda ao [portal Voz Personalizada](https://aka.ms/custom-voice-portal). Criar ou selecionar um projeto De Voz Personalizada. O projeto deve partilhar a língua/localidade certa e as propriedades de género como os dados que pretende utilizar para a sua formação de voz. Por exemplo, selecione `en-GB` se as gravações áudio que tiver são feitas em inglês com sotaque britânico.

Vá ao separador **Dados** e clique em **Enviar dados.** No assistente, selecione o tipo de dados correto que corresponda ao que preparou.

Cada conjunto de dados que fizer o upload deve satisfazer os requisitos para o tipo de dados que escolher. É importante formatar corretamente os seus dados antes de serem carregados. Isto garante que os dados serão tratados com precisão pelo serviço De Voz Personalizada. Vá para [preparar os dados para a Voz Personalizada](how-to-custom-voice-prepare-data.md) e certifique-se de que os seus dados foram formatados com razão.

> [!NOTE]
> Os utilizadores de subscrição gratuita (F0) podem carregar dois conjuntos de dados simultaneamente. Os utilizadores de subscrição padrão (S0) podem carregar cinco conjuntos de dados simultaneamente. Se atingir o limite, aguarde até que pelo menos um dos seus conjuntos de dados termine de importar. Então tente de novo.

> [!NOTE]
> O número máximo de conjuntos de dados autorizados a serem importados por subscrição é de 10 ficheiros .zip para utilizadores de subscrição gratuita (F0) e 500 para utilizadores de subscrição padrão (S0).

Os conjuntos de dados são automaticamente validados assim que premir o botão de upload. A validação de dados inclui uma série de verificações nos ficheiros áudio para verificar o seu formato, tamanho e taxa de amostragem. Corrija os erros se houver e submeta novamente. Quando o pedido de importação de dados for iniciado com sucesso, deverá ver uma entrada na tabela de dados que corresponda ao conjunto de dados que acaba de enviar.

O quadro que se segue mostra os estados de tratamento dos conjuntos de dados importados:

| Estado | Significado |
| ----- | ------- |
| Em processamento | O seu conjunto de dados foi recebido e está a ser processado. |
| Com êxito | O seu conjunto de dados foi validado e pode agora ser utilizado para construir um modelo de voz. |
| Com falhas | O seu conjunto de dados foi falhado durante o processamento devido a muitas razões, por exemplo, erros de ficheiros, problemas de dados ou problemas de rede. |

Após a validação estar completa, pode ver o número total de expressões correspondidas para cada um dos seus conjuntos de dados na coluna **Utterances.** Se o tipo de dados selecionado requer segmentação de áudio longo, esta coluna apenas reflete as expressões que segmentamos para si, quer com base nas suas transcrições, quer através do serviço de transcrição de fala. Pode ainda descarregar o conjunto de dados validado para ver os resultados dos detalhes das expressões importadas com sucesso e as suas transcrições de mapeamento. Atenção: a segmentação de áudio longo pode demorar mais de uma hora a completar o processamento de dados.

Para conjuntos de dados en-US e zh-CN, pode ainda fazer o download de um relatório para verificar as pontuações de pronúncia e o nível de ruído de cada uma das suas gravações. A pontuação da pronúncia varia entre 0 e 100. Uma pontuação abaixo de 70 normalmente indica um erro de fala ou uma incompatibilidade do script. Um sotaque pesado pode reduzir a sua pontuação de pronúncia e impactar a voz digital gerada.

Uma relação sinal-ruído mais elevada (SNR) indica um ruído mais baixo no seu áudio. Normalmente, pode chegar a um SNR de 50+ gravando em estúdios profissionais. O áudio com um SNR abaixo de 20 pode resultar num ruído óbvio na sua voz gerada.

Considere voltar a registar quaisquer expressões com pontuações baixas de pronúncia ou más relações sinal-ruído. Se não conseguir voltar a gravar, pode excluir essas expressões do seu conjunto de dados.

## <a name="build-your-custom-voice-model"></a>Construa o seu modelo de voz personalizado

Depois de o conjunto de dados ter sido validado, pode usá-lo para construir o seu modelo de voz personalizado.

1.  Navegue **para text-to-speech > > de voz personalizada [nome do projeto] > Training**.

2.  Clique **no modelo train**.

3.  Em seguida, insira um **Nome** e **Descrição** para ajudá-lo a identificar este modelo.

    Escolha um nome com cuidado. O nome que introduzir aqui será o nome que usa para especificar a voz no seu pedido de síntese de fala como parte da entrada SSML. Apenas letras, números e alguns caracteres de pontuação como -, \_ e (', ') são permitidos. Use nomes diferentes para diferentes modelos de voz.

    Um uso comum do campo **Descrição** é registar os nomes dos conjuntos de dados que foram usados para criar o modelo.

4.  A partir da página **de dados de treino Select,** escolha um ou vários conjuntos de dados que gostaria de usar para treinar. Verifique o número de declarações antes de as submeter. Você pode começar com qualquer número de expressões para modelos de voz en-US e zh-CN. Para outros locais, você deve selecionar mais de 2.000 expressões para ser capaz de treinar uma voz.

    > [!NOTE]
    > Os nomes de áudio duplicados serão removidos do treino. Certifique-se de que os conjuntos de dados selecionados não contêm os mesmos nomes de áudio em vários ficheiros .zip.

    > [!TIP]
    > A utilização dos conjuntos de dados do mesmo altifalante é necessária para obter resultados de qualidade. Quando os conjuntos de dados que submeteu para treino contiverem um número total de menos de 6.000 expressões distintas, irá treinar o seu modelo de voz através da técnica de síntese paramétrica estatística. No caso de os seus dados de treino excederem um número total de 6.000 expressões distintas, iniciará um processo de treino com a técnica de síntese de Concatenation. Normalmente, a tecnologia de concatenação pode resultar em resultados de voz mais naturais e de maior fidelidade. [Contacte a equipa de Voz Personalizada](https://go.microsoft.com/fwlink/?linkid=2108737) se quiser formar um modelo com a mais recente tecnologia Neural TTS que pode produzir uma voz digital equivalente às [vozes neurais](language-support.md#neural-voices)publicamente disponíveis.

5.  Clique **em Train** para começar a criar o seu modelo de voz.

A tabela Training apresenta uma nova entrada que corresponde a este modelo recém-criado. A tabela também apresenta o estado: Processamento, Sucesso, Falhado.

O estado mostrado reflete o processo de conversão do seu conjunto de dados para um modelo de voz, como mostrado aqui.

| Estado | Significado |
| ----- | ------- |
| Em processamento | O teu modelo de voz está a ser criado. |
| Com êxito | O seu modelo de voz foi criado e pode ser implementado. |
| Com falhas | O seu modelo de voz falhou na formação devido a muitas razões, por exemplo, problemas de dados invisíveis ou problemas de rede. |

O tempo de formação varia consoante o volume de dados áudio processados. Os tempos típicos variam de cerca de 30 minutos para centenas de proclamações a 40 horas para 20.000 proclamações. Uma vez que o seu treino de modelo seja bem sucedido, você pode começar a testá-lo.

> [!NOTE]
> Os utilizadores de subscrição gratuita (F0) podem treinar uma fonte de voz simultaneamente. Os utilizadores de subscrição padrão (S0) podem treinar três vozes simultaneamente. Se atingir o limite, aguarde até que pelo menos uma das suas fontes de voz termine o treino e tente novamente.

> [!NOTE]
> O número máximo de modelos de voz autorizados a ser treinados por subscrição é de 10 modelos para utilizadores de subscrição gratuita (F0) e 100 para utilizadores de subscrição padrão (S0).

Se estiver a utilizar a capacidade de treino de voz neural, pode selecionar para treinar um modelo otimizado para cenários de streaming em tempo real ou um modelo neural HD otimizado para [síntese de áudio longo assíncronos.](long-audio-api.md)  

## <a name="test-your-voice-model"></a>Teste o seu modelo de voz

Depois de a sua fonte de voz ter sido construída com sucesso, pode testá-la antes de a utilizar.

1.  Navegue **para text-to-speech > > de voz personalizada [nome do projeto] > Testes**.

2.  Clique **em Adicionar teste**.

3.  Selecione um ou vários modelos que gostaria de testar.

4.  Forneça o texto que pretende que a voz(s) fale. Se tiver selecionado testar vários modelos de uma só vez, o mesmo texto será utilizado para os testes para diferentes modelos.

    > [!NOTE]
    > A linguagem do seu texto deve ser a mesma que a língua do seu tipo de letra de voz. Só podem ser testados modelos treinados com sucesso. Apenas texto simples é apoiado neste passo.

5.  Clique em **Criar**.

Uma vez submetido o seu pedido de teste, voltará à página de teste. A tabela inclui agora uma entrada que corresponde ao seu novo pedido e à coluna de estado. Pode levar alguns minutos para sintetizar o discurso. Quando a coluna de estado diz **"Bem sucedido",** pode reproduzir o áudio ou transferir a entrada de texto (um ficheiro .txt) e a saída de áudio (um ficheiro .wav) e fazer uma audição adicional para obter qualidade.

Também pode encontrar os resultados dos testes na página de detalhes de cada modelo que selecionou para testes. Vá ao **separador Treino** e clique no nome do modelo para introduzir a página de detalhes do modelo.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Criar e usar um ponto final de voz personalizado

Depois de ter criado e testado com sucesso o seu modelo de voz, implementou-o num ponto final text-to-speech personalizado. Em seguida, utilize este ponto final no lugar do ponto final habitual ao fazer pedidos de texto-a-discurso através da API REST. O seu ponto final personalizado só pode ser chamado pela subscrição que utilizou para implementar o tipo de letra.

Para criar um novo ponto final de voz personalizado, vá ao **Text-to-Speech > a > De voz personalizada**. **Selecione Adicionar ponto final** e introduza um **Nome** e **Descrição** para o seu ponto de finalização personalizado. Em seguida, selecione o modelo de voz personalizado que pretende associar a este ponto final.

Depois de clicar no botão **Adicionar,** na tabela ponto final, verá uma entrada para o seu novo ponto final. Pode levar alguns minutos para instantanear um novo ponto final. Quando o estado da implantação for **bem sucedido,** o ponto final está pronto a ser utilizado.

> [!NOTE]
> Os utilizadores de subscrição gratuita (F0) podem ter apenas um modelo implantado. Os utilizadores de subscrição padrão (S0) podem criar até 50 pontos finais, cada um com a sua própria voz personalizada.

> [!NOTE]
> Para utilizar a sua voz personalizada, tem de especificar o nome do modelo de voz, utilizar o URI personalizado diretamente num pedido HTTP e utilizar a mesma subscrição para passar pela autenticação do serviço TTS.

Depois de o seu ponto final ser implantado, o nome do ponto final aparece como um link. Clique no link para apresentar informações específicas do seu ponto final, como a chave de ponto final, URL de ponto final e código de amostra.

Os testes online do ponto final também estão disponíveis através do portal de voz personalizado. Para testar o seu ponto final, escolha **o ponto final** da página de detalhes do Ponto **Final.** A página de teste do ponto final aparece. Introduza o texto a ser falado (em texto simples ou [em formato SSML](speech-synthesis-markup.md) na caixa de texto. Para ouvir o texto falado na sua fonte de voz personalizada, selecione **Reproduzir**. Esta funcionalidade de teste será carregada contra a utilização da síntese de fala personalizada.

O ponto final personalizado é funcionalmente idêntico ao ponto final padrão que é usado para pedidos de texto-a-fala. Consulte [a API REST](rest-text-to-speech.md) para obter mais informações.

## <a name="next-steps"></a>Passos seguintes

* [Guia: Grave as suas amostras de voz](record-custom-voice-samples.md)
* [Referência de API text-to-speech](rest-text-to-speech.md)
* [API de áudio longo](long-audio-api.md)
