---
title: Criar uma voz personalizada - serviços de voz
titlesuffix: Azure Cognitive Services
description: Quando estiver pronto para carregar os dados, vá para o portal de voz personalizada. Crie ou selecione um projeto de voz personalizada. O projeto têm de partilhar o idioma/região correto e as propriedades de género como os dados intenção de utilizar para fins de formação de voz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: 6189ea2866d1c16f994179df0179e29353e6c47d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65410730"
---
# <a name="create-a-custom-voice"></a>Criar uma voz personalizada

Na [preparar dados para voz personalizada](how-to-custom-voice-prepare-data.md), descrevemos os tipos de dados diferentes, pode usar para preparar uma voz personalizada e os requisitos de formato diferente. Depois de preparar seus dados, pode começar a carregá-los para o [portal de voz personalizada](https://aka.ms/custom-voice-portal), ou por meio da API de formação de voz personalizada. Neste artigo, descrevemos os passos de uma voz personalizada através do portal de treinamento.

> [!NOTE]
> Essa página assume que leu [introdução de voz personalizada](how-to-custom-voice.md) e [preparar dados para voz personalizada](how-to-custom-voice-prepare-data.md)e criou um projeto de voz personalizada.

Verificar os idiomas suportados para voz personalizada: [linguagem para personalização](language-support.md#customization).

## <a name="upload-your-datasets"></a>Carregar os conjuntos de dados

Quando estiver pronto para carregar os dados, vá para o [portal de voz personalizada](https://aka.ms/custom-voice-portal). Crie ou selecione um projeto de voz personalizada. O projeto têm de partilhar o idioma/região correto e as propriedades de género como os dados intenção de utilizar para fins de formação de voz. Por exemplo, selecione `en-GB` se as gravações de áudio tiver é feito em inglês com um destaque do Reino Unido.

Vá para o **dados** separador e clique em **carregar dados**. No assistente, selecione o tipo de dados correto que corresponda ao que está preparado.

Cada conjunto de dados que carrega têm de cumprir os requisitos para o tipo de dados que escolher. É importante Formatar corretamente os seus dados antes de ser carregado. Isto garante que os dados serão processados com precisão o serviço de voz personalizada. Aceda a [preparar dados para voz personalizada](how-to-custom-voice-prepare-data.md) e certifique-se de seus dados foi formatados corretamente.

> [!NOTE]
> Os utilizadores de subscrição gratuita (F0) podem carregar dois conjuntos de dados em simultâneo. Utilizadores de subscrição Standard (S0) podem carregar cinco conjuntos de dados em simultâneo. Se atingir o limite, aguarde até que seja, pelo menos, um dos seus conjuntos de dados concluída a importação. Em seguida, tente novamente.

> [!NOTE]
> O número máximo de conjuntos de dados podem ser importados por subscrição é. zip 10 ficheiros, gratuitamente, os utilizadores de subscrição (F0) e 500 para utilizadores de subscrição standard (S0).

Conjuntos de dados são validados automaticamente assim que pressionar o botão de carregamento. Validação de dados inclui uma série de verificações nos arquivos de áudio para verificar se seu formato de ficheiro, o tamanho e a frequência de amostragem. Corrija os erros, se houver e submeta novamente. Quando o pedido de importação de dados é iniciado com êxito, deverá ver uma entrada na tabela de dados que corresponde ao conjunto de dados que acabou de carregar.

A tabela seguinte mostra os Estados de processamento de conjuntos de dados importados:

| Estado | Significado |
| ----- | ------- |
| Em processamento | O conjunto de dados foi recebido e está a ser processado. |
| Bem-sucedido | O conjunto de dados foi validado e agora pode ser utilizado para criar um modelo de voz. |
| Com Falhas | O conjunto de dados foi falhou durante o processamento devido a várias razões, por exemplo, erros de ficheiros, problemas de dados ou problemas de rede. |

Depois de concluída a validação, pode ver o número total de expressões correspondentes para cada um dos seus conjuntos de dados do **expressões com** coluna. Se o tipo de dados que selecionou necessita de segmentação de áudio de longa, esta coluna reflete apenas as expressões que podemos ter segmentada para qualquer um com base em suas transcrições ou através do serviço de transcrição de voz. Ainda mais, pode baixar o conjunto de dados validado para ver os resultados de detalhe das expressões que foram importados com êxito e suas transcrições de mapeamento. Sugestão: Segmentação de áudio longa pode demorar mais de uma hora para concluir o processamento de dados.

Para conjuntos de dados de en-US e zh-CN, pode baixar ainda mais um relatório para verificar as pontuações de pronúncia e o nível de ruído para cada um dos seus gravações. A pronúncia classificação vai de 0 a 100. Uma pontuação inferior a 70 normalmente indica um erro de correspondência do erro ou um script de voz. Um destaque pesada pode reduzir sua pontuação pronúncia e afetar a voz digital gerada.

Um rácio de sinal e ruído superior (SNR) indica inferior ruído em sua áudio. Normalmente, pode entrar SNR 50 + por gravação em estúdios profissionais. Áudio com um SNR abaixo de 20 pode resultar em óbvio ruído em sua voz gerado.

Considere novamente gravar qualquer expressões com pontuações de pronúncia baixa ou fraco rácios de sinal e ruído. Se não é possível voltar a registar, poderá excluir essas expressões com seu conjunto de dados.

## <a name="build-your-custom-voice-model"></a>Criar o seu modelo de voz personalizada

Depois do conjunto de dados foi validado, pode usá-lo para criar o seu modelo de voz personalizada.

1.  Navegue para **voz > voz personalizada > treinamento**.

2.  Clique em **preparar modelo**.

3.  Em seguida, introduza um **Name** e **Descrição** para ajudar a identificar este modelo.

    Escolha um nome com cuidado. O nome que introduzir aqui será o nome que utiliza para especificar a voz no seu pedido para síntese de fala como parte da SSML de entrada. Apenas letras, números e carateres de pontuação alguns, como-, \_e (",") são permitidos. Use nomes diferentes para modelos de voz diferentes.

    Um uso comum do **Descrição** campo é registrar os nomes dos conjuntos de dados que foram utilizados para criar o modelo.

4.  Partir do **selecionar dados de treinamento** página, selecione um ou vários conjuntos de dados que pretende utilizar para formação. Verifique o número de expressões com antes de enviá-los. Pode começar com qualquer número de expressões para en-US e zh-CN modelos de voz. Para outras localidades, tem de selecionar expressões com mais de 2.000 ser capaz de preparar uma voz.

    > [!NOTE]
    > Nomes duplicados de áudio serão removidos do treinamento. Certifique-se de que seleciona os conjuntos de dados não contêm os mesmos nomes de áudio em vários arquivos. zip.

    > [!TIP]
    > Utilizar os conjuntos de dados do mesmo falante é necessário para resultados de qualidade. Quando os conjuntos de dados que tiver submetido para a formação de contenham um número total de menos de 6.000 discursos distintos, será de preparar o seu modelo de voz por meio da técnica de síntese Paramétrica estatísticas. No caso em que os dados de treinamento excedem um número total de 6.000 discursos distintos, irá disparar um processo de treinamento com a técnica de síntese de concatenação. Normalmente a tecnologia de concatenação pode resultar em resultados de voz mais natural e de fidelidade mais elevada. [Contacte a equipa de voz personalizada](mailto:speechsupport@microsoft.com) se pretender preparar um modelo com a tecnologia mais recente de Neural TTS que pode produzir uma voz digital equivalente a publicamente disponíveis [vozes neurais](language-support.md#neural-voices).

5.  Clique em **Train** para começar a criar o seu modelo de voz.

A tabela de treinamento apresenta uma nova entrada que corresponde a este criada de novo modelo. A tabela também apresenta o estado: Processamento, com êxito, com falhas.

O estado de que é apresentado reflete o processo de conversão o conjunto de dados para um modelo de voz, como mostrado aqui.

| Estado | Significado |
| ----- | ------- |
| Em processamento | Seu modelo de voz está a ser criado. |
| Bem-sucedido | Seu modelo de voz foi criado e pode ser implementado. |
| Com Falhas | Seu modelo de voz foi falhou no treinamento devido a várias razões, problemas de dados que não foram vistos por exemplo, ou problemas de rede. |

O tempo de treinamento varia dependendo do volume de dados de áudio processados. Vezes típicos variam entre sobre 30 minutos para centenas de expressões e 40 horas para expressões 20.000 com. Assim que o seu treinamento de modelo é concluído com êxito, pode começar a testá-lo.

> [!NOTE]
> Os utilizadores de subscrição gratuita (F0) podem preparar um tipo de voz em simultâneo. Utilizadores de subscrição Standard (S0) podem preparar os três vozes em simultâneo. Se atingir o limite, aguarde até que seja, pelo menos, um dos tipos de voz concluída treinamento e, em seguida, tente novamente.

> [!NOTE]
> O número máximo de modelos de voz permitida para ser treinado por subscrição é de 10 modelos para utilizadores de subscrição gratuita (F0) e 100 para utilizadores de subscrição standard (S0).

## <a name="test-your-voice-model"></a>Testar o seu modelo de voz

Depois de baseia-se com êxito o tipo de voz, pode testá-lo antes de implementar para ser utilizado.

1.  Navegue para **voz > voz personalizada > teste**.

2.  Clique em **adicionar teste**.

3.  Selecione um ou vários modelos que pretende testar.

4.  Forneça o texto que pretende voice(s) falar. Se tiver selecionado para testar vários modelos ao mesmo tempo, o mesmo texto será utilizado para fins de teste para modelos diferentes.

    > [!NOTE]
    > O idioma do seu texto deve ser o mesmo que o idioma do seu tipo de voz. Podem ser testados apenas modelos treinados com êxito. Texto de simples apenas é suportado neste passo.

5.  Clique em **Criar**.

Depois de submeter o pedido de teste, será retornado para a página de teste. A tabela inclui agora uma entrada que corresponde ao seu novo pedido e a coluna de estado. Pode demorar alguns minutos para sintetizar voz. Quando a coluna Estado diz **bem-sucedido**, pode reproduzir o áudio ou transferir a entrada de texto (um ficheiro. txt) e áudio (um arquivo. wav) de saída e ainda mais audition a última opção de qualidade.

Também pode encontrar os resultados de teste na página de detalhes de cada modelos que selecionou para fins de teste. Vá para o **treinamento** separador e clique no nome de modelo para introduzir a página de detalhes do modelo.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Criar e utilizar um ponto de final de voz personalizada

Depois de criado com êxito e testou seu modelo de voz, implementá-la num ponto de final de voz personalizado. Em seguida, utilizar este ponto final em vez do ponto final comum quando são efetuados pedidos de voz através da API REST. O ponto final personalizado pode ser chamado apenas pela subscrição que utilizou para implementar o tipo de letra.

Para criar um novo ponto de final de voz personalizada, aceda a **voz > voz personalizada > implementação**. Selecione **adicionar ponto final** e introduza um **nome** e **Descrição** para seu ponto final personalizado. Em seguida, selecione o modelo de voz personalizada que pretende associar este ponto final.

Depois de clicar o **adicionar** botão, na tabela do ponto final, verá uma entrada para o ponto final de novo. Pode demorar alguns minutos para instanciar um novo ponto final. Quando o estado da implementação estiver **bem-sucedido**, o ponto final está pronto para ser utilizado.

> [!NOTE]
> Os utilizadores de subscrição gratuita (F0) podem ter apenas um modelo implementado. Utilizadores de subscrição Standard (S0) podem criar até 50 pontos de extremidade, cada um com sua própria voz personalizada.

> [!NOTE]
> Para utilizar a sua voz personalizada, tem de especificar o nome do modelo de voz, utilizar o URI personalizado diretamente numa solicitação HTTP e utilizar a mesma subscrição para passar a autenticação do serviço TTS.

Depois do ponto final é implementado, o nome do ponto final é apresentado como um link. Clique na ligação para apresentar informações específicas para o ponto final, como a chave de ponto final, o URL de ponto final e o código de exemplo.

Testes online do ponto de extremidade também estão disponível através do portal de voz personalizada. Para testar o ponto final, escolha **verificar o ponto final** partir a **detalhes do ponto de extremidade** página. O ponto final de teste de página é apresentada. Introduza o texto seja falado (em qualquer texto sem formatação ou [formato SSML](speech-synthesis-markup.md) na caixa de texto. Para ouvir o texto falado em seu tipo de voz personalizada, selecione **reproduzir**. Esta funcionalidade de teste é cobrada em relação a sua utilização de síntese de voz personalizada.

O ponto final personalizado é funcionalmente idêntico para o ponto de extremidade padrão que é utilizado para pedidos de texto para voz. Ver [REST API](rest-text-to-speech.md) para obter mais informações.

## <a name="next-steps"></a>Passos Seguintes

* [Guia de: Gravar seus exemplos de voz](record-custom-voice-samples.md)
* [Referência da API de texto para discurso](rest-text-to-speech.md)
