---
title: Como criar um tipo de voz personalizada
titlesuffix: Azure Cognitive Services
description: Este artigo é uma descrição geral da personalização de voz de texto em voz, que lhe permite criar uma voz de marca reconhecível, um do-única.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: panosper
ms.openlocfilehash: 24b98ce8cd2c587f0d39390954eb8a64747ca2ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60654084"
---
# <a name="creating-custom-voice-fonts"></a>Criar tipos de voz personalizada

Personalização de voz de voz (TTS) permite-lhe criar uma voz reconhecível, um do-única para sua marca: um *tipo de voz.*

Para criar o seu tipo de voz, fazer uma gravação de studio e carregar os scripts associados como os dados de treinamento. O serviço, em seguida, cria um modelo de voz única ajustado para a gravação. Pode utilizar este tipo de voz para sintetizar voz.

Pode começar a utilizar com uma pequena quantidade de dados para uma prova de conceito. Mas os dados mais fornecer, quanto mais natural e professional pode parecer a sua voz.

## <a name="prerequisites"></a>Pré-requisitos

Precisa de uma conta do Azure e uma subscrição para o serviço de voz. [Criar um](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started) se ainda não o fez. Ligue a sua subscrição para o portal de voz personalizada, conforme mostrado aqui.

1. Inicie sessão para o [portal de voz personalizada](https://customvoice.ai) com a mesma conta Microsoft que utilizou para aplicar para acesso.

2. Em nome da sua conta no canto superior direito, aceda a **subscrições**.

    ![Subscriptions](media/custom-voice/subscriptions.png)

3. Na página de subscrições, escolha **ligar a subscrição existente**. Tenha em atenção que os serviços de voz oferece suporte a diferentes regiões. Verifique a região onde foi criada a chave de subscrição e certifique-se de que se ligar a chave para o portal de secundárias correto.  

4. Cole a chave de subscrição na tabela, conforme mostrado no exemplo a seguir. Cada subscrição tem duas chaves, e pode usar qualquer um deles.

     ![Adicionar subscrição](media/custom-voice/add-subscription.png)

Está pronto para começar!

## <a name="prepare-recordings-and-transcripts"></a>Preparar gravações e transcrições

Um conjunto de dados de formação de voz consiste num conjunto de arquivos de áudio, junto com um arquivo de texto que contém as transcrições de arquivos de áudio.

Pode preparar esses arquivos de duas formas. A escrever um script e fazê-lo a lidos por talentos de voz ou usar áudio disponível ao público e transcrever para texto. Se fizer a última opção, edite disfluencies dos arquivos de áudio, como "um" e outros sons de preenchimento, stutters, mumbled palavras ou mispronunciations.

Para produzir um tipo de voz boa, torne as gravações num ambiente silencioso com um microfone de alta qualidade. Volume consistente, falando taxa, fala pitch e expressivas mannerisms de voz são essenciais para criar uma voz digital excelente.

Para criar uma voz para uso em produção, recomendamos que usar o talento de studio e de voz de gravação profissional. Para obter mais informações, consulte [exemplos de como grave voz para uma voz personalizada](record-custom-voice-samples.md).

### <a name="audio-files"></a>Arquivos de áudio

Cada arquivo de áudio deve conter uma única expressão (por exemplo, uma sentença única ou uma única mão de um sistema de caixa de diálogo). Todos os ficheiros têm de estar no mesmo idioma. (Multilíngües vozes personalizadas não são suportadas.) Os arquivos de áudio devem também ter, cada um nome de ficheiro numérico exclusivo com a extensão de nome de ficheiro `.wav`.

Arquivos de áudio devem estar preparados da seguinte forma. Outros formatos não são suportados e serão rejeitados.

| **Propriedade** | **Valor** |
| ------------ | --------- |
| Formato de ficheiro  | RIFF (.wav)|
| Frequência de amostragem| pelo menos de 16 000 Hz |
| Formato de exemplo| PCM, 16 bits |
| Nome de ficheiro    | Numérico, com `.wav` extensão |
| Formato de arquivo| .zip      |
| Tamanho máximo de arquivo|200 MB|

> [!NOTE]
> arquivos. wav com uma menor do que 16.000 Hertz de taxa de amostragem serão rejeitados. Se um ficheiro. zip contém ondas com taxas de amostragem diferentes, apenas esses igual ou maior do que 16.000 Hz serão importados.
> O portal atualmente. zip de importações arquiva até 200 MB. No entanto, podem ser carregados vários arquivos. O número máximo de conjuntos de dados permitidos é. zip 10 ficheiros, gratuitamente, os utilizadores de subscrição e 50 para utilizadores de subscrição standard.

### <a name="transcripts"></a>Transcrições

O ficheiro de transcrição é um ficheiro de texto sem formatação (ANSI, UTF-8, UTF-8-BOM, UTF-16-LE ou UTF-16-BE). Cada linha do arquivo de transcrição tem de ter o nome de um arquivo de áudio, seguido de um caractere do separador (ponto de código 9) e, finalmente, a respetiva transcrição. Não existem linhas em branco são permitidas.

Por exemplo:

```
0000000001  This is the waistline, and it's falling.
0000000002  We have trouble scoring.
0000000003  It was Janet Maslin.
```

O sistema de voz personalizada normaliza transcrições convertendo o texto em minúsculas e removendo pontuação estranhas. É importante que as transcrições são 100% transcrições precisos de gravações de áudio correspondentes.

> [!TIP]
> Quando criar produção voz vozes, selecionadas expressões de com (ou escrever scripts) que fazer em conta a cobertura fonética e eficiência. A ter problemas ao obter os resultados, deseja? [Contacte a equipa de voz personalizada](mailto:speechsupport@microsoft.com) para descobrir mais sobre-na ter consulte.

## <a name="upload-your-datasets"></a>Carregar os conjuntos de dados

Depois de preparar o arquivo do arquivo de áudio e transcrições, carregue-as através do [portal do serviço de voz personalizada](https://customvoice.ai).

> [!NOTE]
> Não não possível editar os conjuntos de dados após eles foram carregados. Caso se esqueça de incluir transcrições de alguns dos arquivos de áudio, por exemplo, ou acidentalmente, escolha o sexo errado, tem de carregar todo o conjunto de dados novamente. Verificar seu conjunto de dados e definições cuidadosamente antes de iniciar o carregamento.

1. Inicie sessão no portal.

2. Na página principal, sob **voz personalizada**, selecione **dados**.   

    O **voz My** tabela é apresentada. Está vazio, se ainda não tiver carregado quaisquer conjuntos de dados de voz.

3. Para abrir a página para carregar um novo conjunto de dados, selecione **importar dados**.

    ![Importar dados de voz](media/custom-voice/import-voice-data.png)

4. Introduza um nome e descrição nos campos que são fornecidos.

5. Selecione a região para seus tipos de voz. Certifique-se de que as informações de localidade correspondente ao idioma dos dados de gravação e os scripts.

6. Selecione o género do falante cujo voz estiver a utilizar.

7. Selecione o script e arquivos de áudio para carregar.

8. Selecione **importação** para carregar os dados. Para conjuntos de dados maiores, a importação pode demorar vários minutos.

> [!NOTE]
> Os utilizadores de subscrição gratuita podem carregar dois conjuntos de dados cada vez. Utilizadores de subscrição Standard podem carregar cinco conjuntos de dados em simultâneo. Se atingir o limite, aguarde até que seja, pelo menos, um dos seus conjuntos de dados concluída a importação. Em seguida, tente novamente.

Quando o carregamento estiver concluído, o **meus dados de voz** tabela é apresentada novamente. Deverá ver uma entrada que corresponde ao conjunto de dados que acabou de carregar.

Conjuntos de dados são validados automaticamente após o carregamento. Validação de dados inclui uma série de verificações nos arquivos de áudio para verificar se seu formato de ficheiro, o tamanho e a frequência de amostragem. Verificações nos arquivos de transcrição Verifique se o formato de arquivo e fazer algumas normalização do texto. As expressões são transcrito com reconhecimento de fala. Em seguida, o texto resultante é comparado com a transcrição que forneceu.

![Meus dados de voz](media/custom-voice/my-voice-data.png)

A tabela seguinte mostra os Estados de processamento de conjuntos de dados importados:

| Estado | Significado
| ----- | -------
| `NotStarted` | O conjunto de dados foi recebido e é colocada na fila para processamento.
| `Running` | O conjunto de dados está sendo validado.
| `Succeeded` | O conjunto de dados foi validado e agora pode ser utilizado para criar um tipo de voz.

Depois de concluída a validação, pode ver o número total de expressões correspondentes para cada um dos seus conjuntos de dados do **expressão** coluna.

Pode transferir um relatório para verificar as pontuações de pronúncia e o nível de ruído para cada um dos seus gravações. A pronúncia classificação vai de 0 a 100. Uma pontuação inferior a 70 normalmente indica um erro de correspondência do erro ou um script de voz. Um destaque pesada pode reduzir sua pontuação pronúncia e afetar a voz digital gerada.

Um rácio de sinal e ruído superior (SNR) indica inferior ruído em sua áudio. Normalmente, pode entrar SNR 50 + por gravação em estúdios profissionais. Áudio com um SNR abaixo de 20 pode resultar em óbvio ruído em sua voz gerado.

Considere novamente gravar qualquer expressões com pontuações de pronúncia baixa ou fraco rácios de sinal e ruído. Se não é possível voltar a registar, poderá excluir essas expressões com seu conjunto de dados.

## <a name="build-your-voice-font"></a>Criar o seu tipo de voz

Depois do conjunto de dados foi validado, pode usá-lo para criar o seu tipo de voz personalizada.

1.  Na **voz personalizada** menu pendente, escolha **modelos**.

    O **meus tipos de voz** tabela é apresentada, listando quaisquer tipos de voz personalizada que já criou.

1. Sob o título da tabela, selecione **criar vozes**.

    É apresentada a página para a criação de um tipo de voz. A Localidade atual é mostrada na primeira linha da tabela. Altere a Localidade para criar uma voz em outro idioma. A localidade tem de ser o mesmo assim como para os conjuntos de dados que estão a ser utilizados para criar a voz.

1. Como fez ao carregar o conjunto de dados, introduza um nome e descrição para ajudar a identificar este modelo.

    Escolha um nome com cuidado. O nome que introduzir aqui será o nome que utiliza para especificar a voz no seu pedido para síntese de fala como parte da SSML de entrada. Apenas letras, números e carateres de pontuação alguns, como `-`, `_`, e `(', ')` são permitidos.

    Um uso comum do **Descrição** campo é registrar os nomes dos conjuntos de dados que foram utilizados para criar o modelo.

1. Escolha o género do seu tipo de voz. Tem de corresponder o género do conjunto de dados.

1. Selecione os conjuntos de dados que pretende utilizar para o tipo de voz de formação. Todos os conjuntos de dados que utilizar tem de ser do mesmo falante.

1. Clique em **criar** para começar a criar o seu tipo de voz.

    ![Criar modelo](media/custom-voice/create-model.png)

Seu novo modelo é apresentado na **meus tipos de voz** tabela.

![Meus tipos de voz](media/custom-voice/my-voice-fonts.png)

O estado de que é apresentado reflete o processo de conversão o conjunto de dados para um tipo de voz, como mostrado aqui.

| Estado | Significado
| ----- | -------
| `NotStarted` | O seu pedido de criação de tipo de letra de voz é colocada na fila para processamento.
| `Running` | O tipo de voz está a ser criado.
| `Succeeded` | O tipo de voz foi criado e pode ser implementado.

O tempo de treinamento varia dependendo do volume de dados de áudio processados. Vezes típicos variam entre sobre 30 minutos para centenas de expressões e 40 horas para expressões 20.000 com.

> [!NOTE]
> Os utilizadores de subscrição gratuita podem preparar um tipo de voz ao mesmo tempo. Utilizadores de subscrição Standard podem preparar os três vozes em simultâneo. Se atingir o limite, aguarde até que seja, pelo menos, um dos tipos de voz concluída treinamento e, em seguida, tente novamente.

## <a name="test-your-voice-font"></a>Testar o seu tipo de voz

Depois de baseia-se com êxito o tipo de voz, pode testá-lo antes de implementar para ser utilizado. Na **Operations** coluna, selecione **teste**. É apresentada a página de teste para o tipo de voz selecionado. A tabela estiver vazia se ainda não tiver submetido quaisquer pedidos de teste para a voz.

Para apresentar um menu pop-up de envio de solicitações de texto, selecione o **teste com texto** botão sob o título da tabela. Pode submeter o pedido de teste no texto sem formatação ou SSML. O tamanho máximo de entrada é 1024 carateres, incluindo todas as etiquetas para o pedido SSML. O idioma do seu texto deve ser o mesmo que o idioma do seu tipo de voz.

Depois de preencher a caixa de texto e confirmar o modo de entrada, selecione **Sim** para submeter o pedido de teste e voltar para a página de teste. A tabela inclui agora uma entrada que corresponde ao seu novo pedido e a coluna de estado. Pode demorar alguns minutos para sintetizar voz. Quando a coluna Estado diz **com êxito**, pode transferir a entrada de texto (um `.txt` ficheiro) e saída de áudio (um `.wav` ficheiro) e audition a última opção de qualidade.

## <a name="create-and-use-a-custom-endpoint"></a>Criar e utilizar um ponto final personalizado

Depois de criado com êxito e testou seu modelo de voz, implementá-la num ponto de final de voz personalizado. Em seguida, utilizar este ponto final em vez do ponto final comum quando são efetuados pedidos de voz através da API REST. O ponto final personalizado pode ser chamado apenas pela subscrição que utilizou para implementar o tipo de letra.

Para criar um novo ponto final personalizado, escolha **pontos de extremidade** partir a **voz personalizada** menu na parte superior da página. O **My vozes implementado** é apresentada a página, com sua tabela de pontos finais de voz personalizada atual, se aplicável. A Localidade atual será refletida na primeira linha da tabela. Para criar uma implementação para um idioma diferente, altere a localidade apresentada. (Tem de corresponder a voz que estiver a implementar).

Para criar um novo ponto final, selecione o **implementar vozes** botão. Introduza o nome e descrição do seu ponto final personalizado.

Partir do **subscrição** menu, escolha a subscrição que pretende utilizar. Os utilizadores de subscrição gratuita podem ter apenas um modelo implementado por vez. Utilizadores de subscrição Standard podem criar até 20 pontos de extremidade, cada um com sua própria voz personalizada.

![Criar ponto final](media/custom-voice/create-endpoint.png)

Depois de selecionar o modelo de implementação, selecione **criar**. O **My vozes implementado** página reaparece, agora com uma entrada para o ponto final de novo. Pode demorar alguns minutos para instanciar um novo ponto final. Quando o estado da implementação estiver **bem-sucedido**, o ponto final está pronto para ser utilizado.

![Meu vozes implementados](media/custom-voice/my-deployed-voices.png)

Quando o estado de implementação está **com êxito**, o ponto final do seu tipo de voz implementado é apresentado na **My vozes implementado** tabela. Pode utilizar este URI diretamente numa solicitação HTTP.

Testes online do ponto de extremidade também estão disponível através do portal de voz personalizada. Para testar o ponto final, escolha **pontos finais de testes** partir a **voz personalizada** menu pendente. O ponto final de teste de página é apresentada. Escolha uma voz personalizada implementada e introduza o texto seja falado (no texto sem formatação ou formato SSML) na caixa de texto.

> [!NOTE]
> Quando utilizar SSML, o `<voice>` etiqueta tem de especificar o nome que deu a sua voz personalizada quando o criou. Se enviar texto sem formatação, a voz personalizada é sempre usada.

Para ouvir o texto falado em seu tipo de voz personalizada, selecione **reproduzir**.

![Teste de ponto final](media/custom-voice/endpoint-testing.png)

O ponto final personalizado é funcionalmente idêntico para o ponto de extremidade padrão que é utilizado para pedidos de texto para voz. Ver [REST API](rest-apis.md) para obter mais informações.

## <a name="language-support"></a>Suporte de idiomas

Personalização de voz está disponível nestes idiomas:

| Idioma | Região |
|----------|--------|
| Chinês (continental) | zh-CN |
| English (US) | en-US |
| Francês | FR-FR |
| Alemão | de-DE |
| Italiano | it-IT |

> [!NOTE]
> Italiano, alemão e francês formação de voz começa com um conjunto de dados de expressões com mais de 2000. Modelos de bilingue chinês-inglês também são suportados com um conjunto de dados de expressões com mais de 2000.

## <a name="next-steps"></a>Passos Seguintes

- [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
- [Gravar seus exemplos de voz](record-custom-voice-samples.md)
