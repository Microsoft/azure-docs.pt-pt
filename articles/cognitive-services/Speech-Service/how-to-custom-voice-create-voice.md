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
ms.openlocfilehash: 0650a173b02e1b8f1f829953be1dd852024e6f65
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524520"
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

Na visão do detalhe de dados, pode verificar as pontuações de pronúncia e o nível de ruído de cada um dos seus conjuntos de dados. A pontuação da pronúncia varia entre 0 e 100. Uma pontuação abaixo de 70 normalmente indica um erro de fala ou uma incompatibilidade do script. Um sotaque pesado pode reduzir a sua pontuação de pronúncia e impactar a voz digital gerada.

Uma relação sinal-ruído mais elevada (SNR) indica um ruído mais baixo no seu áudio. Normalmente, pode chegar a um SNR de 50+ gravando em estúdios profissionais. O áudio com um SNR abaixo de 20 pode resultar num ruído óbvio na sua voz gerada.

Considere voltar a registar quaisquer expressões com pontuações baixas de pronúncia ou más relações sinal-ruído. Se não conseguir voltar a gravar, pode excluir essas expressões do seu conjunto de dados.

> [!NOTE]
> É necessário que, se estiver a utilizar a Voz Neural Personalizada, tenha de registar o seu talento de voz no separador **Voice Talent.** Ao preparar o seu script de gravação, certifique-se de que inclui a frase abaixo para adquirir o reconhecimento de talento de voz de usar os seus dados de voz para criar um modelo de voz TTS e gerar voz sintética. "Eu [declaro o seu primeiro e último nome] estou ciente de que as gravações da minha voz serão usadas [pelo nome da empresa] para criar e usar uma versão sintética da minha voz."
Esta frase será usada para verificar se as gravações nos seus conjuntos de dados de treino são feitas pela mesma pessoa que faz o consentimento. [Leia mais sobre como os seus dados serão processados e como a verificação de talentos de voz é feita aqui.](https://aka.ms/CNV-data-privacy) 

## <a name="build-your-custom-voice-model"></a>Construa o seu modelo de voz personalizado

Depois de o conjunto de dados ter sido validado, pode usá-lo para construir o seu modelo de voz personalizado.

1.  Navegue **para text-to-speech > > de voz personalizada [nome do projeto] > Modelo**.

2.  Clique **no modelo train**.

3.  Em seguida, insira um **Nome** e **Descrição** para ajudá-lo a identificar este modelo.

    Escolha um nome com cuidado. O nome que introduzir aqui será o nome que usa para especificar a voz no seu pedido de síntese de fala como parte da entrada SSML. Apenas letras, números e alguns caracteres de pontuação como -, \_ e (', ') são permitidos. Use nomes diferentes para diferentes modelos de voz.

    Um uso comum do campo **Descrição** é registar os nomes dos conjuntos de dados que foram usados para criar o modelo.

4.  A partir da página **de dados de treino Select,** escolha um ou vários conjuntos de dados que gostaria de usar para treinar. Verifique o número de declarações antes de as submeter. Pode começar com qualquer número de expressões para modelos de voz en-US e zh-CN utilizando o método de treino "Adaptive". Para outros locais, você deve selecionar mais de 2.000 expressões para ser capaz de treinar uma voz usando um nível padrão, incluindo os métodos de treino "Estatística paramétrico" e "Concatenative", e mais de 300 expressões para treinar uma voz neural personalizada. 

    > [!NOTE]
    > Os nomes de áudio duplicados serão removidos do treino. Certifique-se de que os conjuntos de dados selecionados não contêm os mesmos nomes de áudio em vários ficheiros .zip.

    > [!TIP]
    > A utilização dos conjuntos de dados do mesmo altifalante é necessária para obter resultados de qualidade. Diferentes métodos de treino requerem diferentes tamanhos de dados de treino. Para formar um modelo com o método "Estatística paramétrica", são necessárias pelo menos 2.000 expressões distintas. Para o método "Concatenative", são 6.000 expressões, enquanto para "Neural", o requisito mínimo de tamanho de dados é de 300 expressões.

5. Selecione o **método de treino** no passo seguinte. 

    > [!NOTE]
    > Se quiser treinar uma voz neural, deve especificar um perfil de talento de voz com o ficheiro de consentimento áudio fornecido pelo talento de voz, reconhecendo usar os seus dados de fala para treinar um modelo de voz personalizado. A Voz Neural Personalizada está disponível com acesso limitado. Certifique-se de compreender os [requisitos responsáveis da IA](https://aka.ms/gating-overview) e [aplicar o acesso aqui.](https://aka.ms/customneural) 
    
    Nesta página também pode selecionar para carregar o seu script para testes. O script de teste deve ser um ficheiro Txt, inferior a 1Mb. O formato de codificação suportado inclui ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE ou UTF-16-BE. Cada parágrafo da expressão resultará num áudio separado. Se quiser combinar todas as frases num único áudio, faça-as num parágrafo. 

6. Clique **em Train** para começar a criar o seu modelo de voz.

A tabela Training apresenta uma nova entrada que corresponde a este modelo recém-criado. A tabela também apresenta o estado: Processamento, Sucesso, Falhado.

O estado mostrado reflete o processo de conversão do seu conjunto de dados para um modelo de voz, como mostrado aqui.

| Estado | Significado |
| ----- | ------- |
| Em processamento | O teu modelo de voz está a ser criado. |
| Com êxito | O seu modelo de voz foi criado e pode ser implementado. |
| Com falhas | O seu modelo de voz falhou na formação devido a muitas razões, por exemplo, problemas de dados invisíveis ou problemas de rede. |

O tempo de treino varia consoante o volume de dados áudio processados e o método de treino selecionado. Pode variar de 30 minutos a 40 horas. Uma vez que o seu treino de modelo seja bem sucedido, você pode começar a testá-lo. 

> [!NOTE]
> Os utilizadores de subscrição gratuita (F0) podem treinar uma fonte de voz simultaneamente. Os utilizadores de subscrição padrão (S0) podem treinar três vozes simultaneamente. Se atingir o limite, aguarde até que pelo menos uma das suas fontes de voz termine o treino e tente novamente.

> [!NOTE]
> O treino de vozes neurais personalizadas não é gratuito. Verifique o [preço](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) aqui. 

> [!NOTE]
> O número máximo de modelos de voz autorizados a ser treinados por subscrição é de 10 modelos para utilizadores de subscrição gratuita (F0) e 100 para utilizadores de subscrição padrão (S0).

Se estiver a utilizar a capacidade de treino de voz neural, pode selecionar para treinar um modelo otimizado para cenários de streaming em tempo real ou um modelo neural HD otimizado para [síntese de áudio longo assíncronos.](long-audio-api.md)  

## <a name="test-your-voice-model"></a>Teste o seu modelo de voz

Cada treino gerará 100 ficheiros áudio de amostras automaticamente para ajudá-lo a testar o modelo. Depois de o seu modelo de voz ter sido construído com sucesso, pode testá-lo antes de o utilizar.

1.  Navegue **para text-to-speech > > de voz personalizada [nome do projeto] > Modelo**.

2.  Clique no nome do modelo que gostaria de testar.

3.  Na página de detalhes do modelo, pode encontrar os ficheiros de áudio da amostra no **separador Teste.** 

A qualidade da voz depende de uma série de fatores, incluindo o tamanho dos dados de treino, a qualidade da gravação, a precisão do ficheiro de transcrição, quão bem a voz gravada nos dados de treino corresponde à personalidade da voz desenhada para o seu caso de uso pretendido, e muito mais. [Consulte aqui para saber mais sobre as capacidades e limites da nossa tecnologia e as melhores práticas para melhorar a qualidade do seu modelo.](https://aka.ms/CNV-limits) 

## <a name="create-and-use-a-custom-voice-endpoint"></a>Criar e usar um ponto final de voz personalizado

Depois de ter criado e testado com sucesso o seu modelo de voz, implementou-o num ponto final text-to-speech personalizado. Em seguida, utilize este ponto final no lugar do ponto final habitual ao fazer pedidos de texto-a-discurso através da API REST. O seu ponto final personalizado só pode ser chamado pela subscrição que utilizou para implementar o tipo de letra.

Para criar um novo ponto final de voz personalizado, vá ao **Texto-a-Voz > Voz Personalizada > Endpoint**. **Selecione Adicionar ponto final** e introduza um **Nome** e **Descrição** para o seu ponto de finalização personalizado. Em seguida, selecione o modelo de voz personalizado que pretende associar a este ponto final.

Depois de clicar no botão **Adicionar,** na tabela ponto final, verá uma entrada para o seu novo ponto final. Pode levar alguns minutos para instantanear um novo ponto final. Quando o estado da implantação for **bem sucedido,** o ponto final está pronto a ser utilizado.

Pode **suspender** e **retomar** o seu ponto final se não o utilizar sempre. Quando um ponto final é reativado após a suspensão, o URL do ponto final será mantido o mesmo para que não seja necessário alterar o seu código nas suas apps. 

Também pode atualizar o ponto final para um novo modelo. Para alterar o modelo, certifique-se de que o novo modelo tem o mesmo nome que o que pretende atualizar. 

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
