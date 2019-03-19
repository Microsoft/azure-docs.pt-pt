---
title: 'Tutorial: Criar um modelo acústico com o serviço de voz'
titlesuffix: Azure Cognitive Services
description: Saiba como criar um modelo acústico com serviços de voz no Azure.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: panosper
ms.openlocfilehash: f2a111558fa3f515b797745dc51e32f625bbd91f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57844029"
---
# <a name="tutorial-create-a-custom-acoustic-model"></a>Tutorial: Criar um modelo acústico personalizado

A criação de um modelo acústico personalizado é útil se a sua aplicação for concebida para ser utilizada num ambiente específico, como um automóvel, com condições ou dispositivos de gravação específicos, bem como por uma população de utilizadores em particular. Os exemplos envolvem voz acentuada, ruídos de fundo específicos ou utilizar um microfone específico para gravação.

Neste artigo, vai aprender a:
> [!div class="checklist"]
> * Preparar os dados
> * Importar o conjunto de dados acústico
> * Criar o modelo acústico personalizado

Se não tiver uma conta dos Serviços Cognitivos do Azure, crie uma [conta gratuita](https://azure.microsoft.com/try/cognitive-services) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que a conta dos Serviços Cognitivos está associada a uma subscrição, abrindo a página [Subscrições de Serviços Cognitivos](https://cris.ai/Subscriptions).

Pode ligar a uma subscrição de serviços de voz que foi criada no portal do Azure, selecionando **ligar a subscrição existente**.

Para obter informações sobre como criar uma subscrição de serviços de voz no portal do Azure, consulte [experimentar gratuitamente os serviços de voz](get-started.md).

## <a name="prepare-the-data"></a>Preparar os dados

Para personalizar um modelo acústico para um determinado domínio, é necessária uma coleção de dados de voz. Essa coleção pode variar entre duas expressões e várias centenas de horas de voz. A coleção é composta por um conjunto de ficheiros de áudio de dados de voz e um ficheiro de texto de transcrições de cada ficheiro de áudio. Os dados de áudio devem ser representativos do cenário em que pretende utilizar o reconhecedor.

Por exemplo:

* Se quiser reconhecer melhor a voz num ambiente de fábrica ruidoso, os ficheiros de áudio devem ser constituídos por pessoas a falar numa fábrica ruidosa.
* Se estiver interessado na otimização do desempenho para um único orador &mdash; por exemplo, se quiser transcrever todas as conversas informais de FDR &mdash;, os ficheiros de áudio devem ter muitos exemplos apenas desse orador.

Um conjunto de dados acústico para personalizar o modelo acústico consiste em duas partes: (1) um conjunto de arquivos de áudio que contém os dados de voz e (2) um ficheiro que contenha as transcrições de todos os arquivos de áudio.

### <a name="audio-data-recommendations"></a>Recomendações de dados de áudio

* Todos os ficheiros de áudio no conjunto de dados devem ser armazenados no formato de áudio WAV (RIFF).
* O áudio tem de ter uma taxa de amostragem de 8 kilohertz (kHz) ou 16 kHz e os valores de exemplo devem ser armazenados como números inteiros com sinal de 16 bits PCM descomprimidos e modulação por código de pulso.
* Apenas são suportados ficheiros de áudio de canal único (mono).
* Os ficheiros de áudio podem ter entre 100 microssegundos e 1 minuto de comprimento, embora o ideal seja terem cerca de 10 a 12 segundos. Idealmente, cada ficheiro de áudio deve começar e terminar com, pelo menos, 100 microssegundos de silêncio e é comum um valor entre 500 microssegundos e 1 segundo.
* Se os seus dados tiverem ruído de fundo, recomendamos que também tenha alguns exemplos com segmentos de silêncio mais longos nos dados &mdash; por exemplo, alguns segundos &mdash; antes e/ou após o conteúdo de voz.
* Cada ficheiro de áudio deve consistir numa única expressão &mdash; por exemplo, uma frase para ditado, uma consulta ou um elemento de um sistema de diálogo.
* Cada ficheiro de áudio no conjunto de dados deve ter um nome de ficheiro exclusivo e a extensão "wav".
* O conjunto de ficheiros de áudio deve ser colocado numa única pasta sem subdiretórios e empacotado como um arquivo individual de ficheiros .zip.

> [!NOTE]
> As importações de dados através do portal Web estão atualmente limitadas a 2 GB, pelo que este é o tamanho máximo dos conjuntos de dados acústicos. Este tamanho corresponde a cerca de 17 horas de áudio gravadas a 16 kHz ou a 34 horas de áudio gravadas a 8 kHz. Os requisitos principais para os dados de áudio estão resumidos na tabela seguinte:
>

| Propriedade | Valor |
|---------- |----------|
| Formato do Ficheiro | RIFF (WAV) |
| Taxa de Amostragem | 8000 Hertz (Hz) ou 16 000 Hz |
| Canais | 1 (mono) |
| Formato de Exemplo | PCM, números inteiros de 16 bits |
| Duração do Ficheiro | 0,1 segundos < duração < 12 segundos |
| Colar de Silêncio | > 0,1 segundos |
| Formato do Arquivo | .zip |
| Tamanho Máximo do Arquivo | 2 GB |

> [!NOTE]
> Os nomes de ficheiros só devem utilizar carateres latinos e têm de seguir o formato “nomedeficheiro.extensão”.

## <a name="language-support"></a>Suporte de idiomas

Para obter uma lista completa dos idiomas suportados nos modelos de idioma personalizados de **Conversão de Voz em Texto**, veja [Supported languages for the Speech Service](language-support.md#speech-to-text) (Idiomas suportados no Serviço de Voz).

### <a name="transcriptions-for-the-audio-dataset"></a>Transcrições do conjunto de dados de áudio

As transcrições para todos os ficheiros WAV devem estar contidas num único ficheiro de texto simples. Cada linha do ficheiro de transcrição deve ter o nome de um dos ficheiros de áudio, seguido da transcrição correspondente. O nome de ficheiro e a transcrição devem estar separados por uma tabulação (\t).

  Por exemplo:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> A transcrição deve ser codificada como UTF-8 byte order mark (BOM).

As transcrições são normalizadas para texto, de modo a que o sistema as possa processar. No entanto, existem algumas normalizações importantes que devem ser feitas pelo utilizador _antes_ de carregar os dados para o Serviço de Voz Personalizada. Para determinar o idioma adequado a utilizar quando preparar as transcrições, veja [Transcription guidelines for using the Speech Service](prepare-transcription.md) (Diretrizes de transcrição para utilizar o Serviço de Voz).

Execute os passos nas secções seguintes, utilizando o [portal de serviços de voz](https://cris.ai).

## <a name="import-the-acoustic-dataset"></a>Importar o conjunto de dados acústico

Depois de preparar os ficheiros de áudio e as transcrições, estes estão prontos para serem importados para o portal Web do serviço.

A importação dos mesmos, primeiro certifique-se de que tem sessão iniciada para o [portal de serviços de voz](https://cris.ai). Em seguida, na lista pendente **Custom Speech** (Voz Personalizada) no friso, selecione **Adaptation Data** (Dados de Adaptação). Se for a primeira vez que está a carregar dados para o Serviço de Voz Personalizada, é apresentada uma tabela vazia chamada **Datasets** (Conjunto de dados).

Na linha **Acoustic Datasets** (Conjuntos de Dados Acústicos), selecione o botão **Import** (Importar) e o site apresenta uma página para carregar um novo conjunto de dados.

![Página Importar Dados Acústicos](media/stt/speech-acoustic-datasets-import.png)

Nas caixas **Name** (Nome) e **Description** (Descrição), introduza as informações apropriadas. As descrições amigáveis são úteis para manter o controlo dos vários conjuntos de dados que carregar.

Nas caixas **Transcriptions file (.txt)** (Ficheiros de transcrições [.txt]) e **Audio files (.zip)** (Ficheiros de áudio [.zip]) , selecione **Browse** (Procurar) e selecione o ficheiro de transcrição em texto simples e o arquivo zip dos ficheiros WAV. Quando a preparação estiver concluída, selecione **Import** (Importar) para carregar os dados. Os dados serão carregados. Relativamente a conjuntos de dados maiores, o processo de importação poderá demorar vários minutos.

Após a conclusão do carregamento, regresse à tabela **Acoustic Datasets** (Conjuntos de Dados Acústicos). É apresentada uma entrada que corresponde ao seu conjunto de dados acústico. Repare que lhe foi atribuído um ID exclusivo (GUID). Os dados são apresentados o respetivo estado atual: *NotStarted* enquanto está em fila para processamento, *em execução* enquanto ele está em validação, e *Complete* quando os dados estão prontos para uso.

A validação de dados inclui uma série de verificações nos ficheiros de áudio para verificar o formato de ficheiro, a duração e a taxa de amostragem, e nos ficheiros de transcrição para verificar o formato de ficheiro e efetuar a normalização de texto.

Quando o estado for *Succeeded* (Com Êxito), pode selecionar **Details** (Detalhes) para ver o relatório de verificação dos dados acústicos. O número de expressões que passaram e falharam na verificação é apresentado, juntamente com os detalhes sobre as expressões que falharam. No exemplo na imagem seguinte, a verificação de dois ficheiros WAV falhou devido a um formato de áudio incorreto. Neste conjunto de dados, um ficheiro tem uma taxa de amostragem incorreta e o outro tem o formato de ficheiro errado.

![Página Detalhes da Adaptação dos Dados](media/stt/speech-acoustic-datasets-report.png)

Se quiser alterar o nome ou a descrição do conjunto de dados, pode selecionar a ligação **Edit** (Editar) e mudar essas entradas. Não pode modificar as entradas dos ficheiros de áudio ou de transcrições.

## <a name="create-a-custom-acoustic-model"></a>Criar um modelo acústico personalizado

Quando o estado do conjunto de dados acústicos for *Complete* (Concluído), pode utilizá-lo para criar um modelo acústico personalizado. Para tal, selecione **Acoustic Models** (Modelos Acústicos), na lista pendente **Custom Speech** (Voz Personalizada). Os seus modelos acústicos personalizados são apresentados numa tabela denominada **Your models** (Os seus modelos). A tabela estará vazia se for a sua primeira utilização. O nome da tabela mostra a região atual. Atualmente, só pode criar modelos acústicos em inglês dos EUA.

Para criar um novo modelo, selecione **Create New** (Criar Novo), abaixo do nome da tabela. Tal como anteriormente, introduza um nome e uma descrição para ajudar a identificar este modelo. Por exemplo, pode utilizar o campo **Description** (Descrição) para registar o modelo inicial e o conjunto de dados acústicos que utilizou para criar o modelo.

Depois, na lista pendente **Base Acoustic Model** (Modelo Acústico de Base), selecione um modelo base. O modelo base é o ponto de partida para a sua personalização. Pode escolher de entre dois modelos acústicos base:
* O modelo **Microsoft Search and Dictation AM** é adequado para voz direcionada para uma aplicação, como comandos, consultas de pesquisa ou ditado.
* O modelo **Microsoft Conversational** é adequado para reconhecer voz falada num estilo de conversação. Este tipo de voz é, normalmente, direcionado para outra pessoa e ocorre em centros de atendimento telefónico ou reuniões.

A latência de resultados parciais em modelos de Conversação é maior do que em modelos de Pesquisa e Ditado.

> [!NOTE]
> Estamos atualmente a implementar o nosso novo modelo **Universal**, que se destina a todos os cenários. Os modelos mencionados anteriormente também permanecerão disponíveis publicamente.

Em seguida, na lista pendente **Acoustic Data** (Dados Acústicos), selecione os dados acústicos que quer utilizar para realizar a personalização.

![Página Criar Modelo Acústico](media/stt/speech-acoustic-models-create2.png)

Após a conclusão do processamento, pode optar por realizar testes de precisão do modelo novo. Este teste executa uma avaliação de conversão de voz em texto num conjunto de dados acústicos especificado mediante a utilização do modelo acústico personalizado e, depois, reporta os resultados. ara executar este teste, selecione a caixa de verificação **Accuracy Testing** (Testes de Precisão). Em seguida, selecione um modelo de idioma na lista pendente. Se não tiver criado nenhum modelo de idioma personalizado, apenas os modelos de idioma base serão apresentados na lista pendente. Para selecionar o modelo de idioma mais adequado, consulte [Tutorial: Criar um modelo de idioma personalizado](how-to-customize-language-model.md).

Por fim, selecione o conjunto de dados acústicos que quer utilizar para avaliar o modelo personalizado. Se realizar testes de precisão, é importante selecionar um conjunto de dados acústicos diferente do utilizado na criação do modelo, de modo a obter uma noção realista do desempenho do modelo. Testar a precisão nos dados de preparação não permite avaliar o desempenho do modelo adaptado em condições reais. O resultado será demasiado otimista. Tenha também em atenção que os testes de precisão estão limitados a mil expressões. Se o conjunto de dados acústicos para teste for maior, só são avaliadas as primeiras mil expressões.

Quando estiver pronto para começar a executar o processo de personalização, selecione **Create** (Criar).

A tabela de modelos acústicos apresenta uma entrada nova que corresponde a esse modelo novo. A tabela também apresenta o estado do processo: *A aguardar*, *processamento*, ou *completa*.

![Página Modelos Acústicos](media/stt/speech-acoustic-models-creating.png)

## <a name="next-steps"></a>Passos Seguintes

- [Obter a subscrição de avaliação dos Serviços de Voz](https://azure.microsoft.com/try/cognitive-services/)
- [Reconhecer voz em C#](quickstart-csharp-dotnet-windows.md)
- [Dados de Exemplo do Git](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
