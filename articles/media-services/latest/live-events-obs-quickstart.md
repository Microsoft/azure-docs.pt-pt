---
title: Criar um live stream da Azure Media Services com o OBS Studio
description: Saiba como criar um live stream da Azure Media Services utilizando o portal e o Estúdio OBS
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 04/16/2020
ms.openlocfilehash: 74aa17fb17d682449bd817945c3b8bbf3f95363e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726614"
---
# <a name="create-an-azure-media-services-live-stream-with-obs"></a>Criar um live stream da Azure Media Services com o OBS

Este quickstart irá ajudá-lo a criar um stream ao vivo do Azure Media Services utilizando o portal Azure e o Open Broadcasting Studio (OBS). Assume que tem uma subscrição Azure e criou uma conta de Media Services.

Neste início rápido, cobriremos:

- Configurar um codificador no local com OBS.
- A criar um fluxo em direto.
- Configurar saídas de transmissão ao vivo.
- Executando um ponto final de streaming predefinido.
- Utilizando o Azure Media Player para visualizar o fluxo ao vivo e a saída a pedido.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Abra o seu navegador web e vá ao [portal Microsoft Azure.](https://portal.azure.com/) Introduza as suas credenciais para iniciar sessão no portal. A vista predefinida é o dashboard de serviço.

## <a name="set-up-an-on-premises-encoder-by-using-obs"></a>Configurar um codificador no local utilizando o OBS

1. Descarregue e instale OBS para o seu sistema operativo no [website open broadcaster Software](https://obsproject.com/).
1. Inicie a aplicação e mantenha-a aberta.

## <a name="run-the-default-streaming-endpoint"></a>Executar o ponto final de streaming padrão

1. Selecione **pontos finais de Streaming** na listagem de Serviços de Media.

   ![Item de menu de pontos finais de streaming](media/live-events-obs-quickstart/streaming-endpoints.png)
1. Se o estado do ponto final de transmissão padrão for interrompido, selecione-o. Este passo leva-o à página para esse ponto final.
1. Selecione **Iniciar**.

   ![Botão de arranque para o ponto final de streaming](media/live-events-obs-quickstart/start.png)

## <a name="set-up-an-azure-media-services-live-stream"></a>Criar um live stream da Azure Media Services

1. Vá à conta Azure Media Services dentro do portal e, em seguida, selecione **live streaming** a partir da listagem de Serviços de **Media.**

   ![Link de streaming ao vivo](media/live-events-obs-quickstart/select-live-streaming.png)
1. Selecione **Adicionar evento ao vivo** para criar um novo evento de streaming ao vivo.

   ![Adicione ícone de evento ao vivo](media/live-events-obs-quickstart/add-live-event.png)
1. Insira um nome para o seu novo evento, como *TestLiveEvent,* na caixa de nomes do **evento Live.**

   ![Caixa de nome do evento ao vivo](media/live-events-obs-quickstart/live-event-name.png)
1. Introduza uma descrição opcional do evento na caixa **Descrição.**
1. Selecione o **Pass-through – sem** opção de codificação em nuvem.

   ![Opção de codificação de nuvens](media/live-events-obs-quickstart/cloud-encoding.png)
1. Selecione a opção **RTMP.**
1. Certifique-se de que a opção **No** é selecionada para **o evento Start live,** para evitar ser faturada para o evento ao vivo antes de estar pronto. (A faturação começará quando o evento ao vivo for iniciado.)

   ![Comece a opção de evento ao vivo](media/live-events-obs-quickstart/start-live-event-no.png)
1. Selecione o **botão Rever + criar** para rever as definições.
1. Selecione o botão **Criar** o evento ao vivo. Depois voltas para a lista de eventos ao vivo.
1. Selecione o link para o evento ao vivo que acabou de criar. Reparem que o seu evento está parado.
1. Mantenha esta página aberta no seu navegador. Voltaremos mais tarde.

## <a name="set-up-a-live-stream-by-using-obs-studio"></a>Configurar um live stream usando o OBS Studio

O OBS começa com uma cena padrão, mas sem as inputs selecionadas.

   ![Ecrã padrão OBS](media/live-events-obs-quickstart/live-event-obs-default-screen.png)

### <a name="add-a-video-source"></a>Adicione uma fonte de vídeo

1. A partir do painel **Fontes,** clique no ícone **adicionar** para selecionar um novo dispositivo de origem. O menu **Fontes** será aberto.

1. Selecione dispositivo de captura de **vídeo** a partir do menu do dispositivo de origem. O menu **Criar/Selecionar Origem** será aberto.

   ![Menu de fontes OBS com dispositivo de vídeo selecionado](media/live-events-obs-quickstart/live-event-obs-video-device-menu.png)

1. Selecione o botão de rádio **Adicionar Existente** e, em seguida, clique em **OK**. O menu **Properties for Video Device** será aberto.

   ![Novo menu de fonte de vídeo da OBS com adição existente selecionado](media/live-events-obs-quickstart/live-event-obs-new-video-source.png)

1. A partir da lista de desistência do **Dispositivo,** selecione a entrada de vídeo que pretende utilizar para a sua transmissão. Deixe o resto das definições em paz por enquanto, e clique **OK**. A fonte de entrada será adicionada ao painel **Fontes,** e a vista de entrada de vídeo aparecerá na área de **Pré-visualização.**

   ![Definições da câmara OBS](media/live-events-obs-quickstart/live-event-surface-camera.png)

### <a name="add-an-audio-source"></a>Adicione uma fonte de áudio

1. A partir do painel **Fontes,** clique no ícone **adicionar** para selecionar um novo dispositivo de origem. O menu Source Device será aberto.

1. Selecione **A captura** de entrada de áudio do menu do dispositivo de origem. O menu **Criar/Selecionar Origem** será aberto.

   ![Menu de fontes OBS com dispositivo áudio selecionado](media/live-events-obs-quickstart/live-event-obs-audio-device-menu.png)

1. Selecione o botão de rádio **Adicionar Existente** e, em seguida, clique em **OK**. O menu **Properties for Audio Input Capture** será aberto.

   ![Fonte de áudio OBS com adição existente selecionado ](media/live-events-obs-quickstart/live-event-obs-new-audio-source.png)

1. A partir da lista de desistência do **Dispositivo,** selecione o dispositivo de captura de áudio que pretende utilizar para a sua transmissão. Deixe o resto das definições em paz por enquanto, e clique OK. O dispositivo de captura de áudio será adicionado ao painel de mistura de áudio.

   ![Lista de desselecção de dispositivos de áudio OBS](media/live-events-obs-quickstart/live-event-select-audio-device.png)

### <a name="set-up-streaming-in-obs"></a>Configurar o streaming em OBS

No próximo procedimento, você vai voltar para o Azure Media Services no seu navegador para copiar o URL de entrada para entrar nas definições de saída:

1. Na página azure Media Services do portal, selecione **Iniciar** o evento live stream. (A faturação começa agora.)

   ![Ícone de início](media/live-events-obs-quickstart/start.png)
1. Desloque o toggle **RTMP** para **RTMPS**.
1. Na caixa DEURL de **entrada,** copie o URL para a sua área de redação.

   ![URL de entrada](media/live-events-obs-quickstart/input-url.png)

1. Mude para a aplicação OBS.

1. Clique no botão **Definições** no painel **Controlos.** As opções definições serão abertas.

   ![Painel de controlos OBS com definições selecionadas](media/live-events-obs-quickstart/live-event-obs-settings.png)

1. Selecione **Stream** do menu **Definições.**

1. A partir da lista de dropdown do **Serviço,** selecione Mostrar tudo e, em seguida, **selecione Custom...**.

1. No campo **Server,** colhe o URL RTMPS que copiou para a sua área de reparação.

1. Introduza algo no **campo-chave stream.**  Não importa o que seja, mas precisa de ter um valor.

    ![Definições de fluxo OBS](media/live-events-obs-quickstart/live-event-obs-stream-settings.png)

1. Selecione **Saída** do menu **Definições.**

1. Introduza *2* no campo de **intervalo do Quadro-Chave.** Isto define o comprimento do fragmento para 2 segundos. Para uma entrega ao vivo de latência mais baixa, utilize o valor de 1 segundo.

1. OPCIONAL: Delineie o Preset de **Utilização do CPU** para *muito rápido* se estiver a utilizar um computador com pouca potência de processamento. Opcionalmente, pode definir os kbps para algo mais baixo se houver condições de rede indesejáveis.

   ![Definições de saída OBS](media/live-events-obs-quickstart/live-event-obs-advanced-output-settings.png)

1. Deixe o resto das definições sem hipóteses e clique **OK**.

### <a name="start-streaming"></a>Começar a transmitir

1. No painel **Controlos,** clique em **Iniciar streaming**.

    ![OBS começa a transmitir botão](media/live-events-obs-quickstart/live-event-obs-start-streaming.png)

2. Mude para o ecrã de eventos Do Azure Media Services Live no seu navegador e clique no link **Reload Player.** Agora deve ver o seu fluxo no leitor de pré-visualização.

## <a name="set-up-outputs"></a>Configurar saídas

Esta peça irá configurar as suas saídas e permitir-lhe-á guardar uma gravação do seu live stream.  

> [!NOTE]
> Para que transmita esta saída, o ponto final de streaming deve estar em funcionamento. Ver mais tarde Executar a secção de ponto final de [streaming predefinido.](#run-the-default-streaming-endpoint)

1. Selecione o link **de saídas Criar** abaixo do visualizador de vídeo **Outputs.**
1. Se quiser, edite o nome da saída na caixa **nome** para algo mais fácil de encontrar mais tarde.

   ![Caixa de nome de saída](media/live-events-wirecast-quickstart/output-name.png)
1. Deixe o resto das caixas em paz por enquanto.
1. Selecione **Next** para adicionar um localizador de streaming.
1. Mude o nome do localizador para algo mais fácil de utilizar, se quiser.

   ![Caixa de nome localizador](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Deixe tudo o resto neste ecrã sozinho por enquanto.
1. Selecione **Criar**.

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>Reproduzir a transmissão de saída utilizando o Azure Media Player

1. Copie o URL de streaming sob o leitor de vídeo **Output.**
1. Num navegador web, abra a [demonstração do Azure Media Player.](https://ampdemo.azureedge.net/azuremediaplayer.html)
1. Colhe o URL de streaming na caixa **DEURL** do Azure Media Player.
1. Selecione o botão **'Atualizar O Leitor'.**
1. Selecione o ícone **Reproduzir** no vídeo para ver o seu live stream.

## <a name="stop-the-broadcast"></a>Parem a transmissão

Quando pensares que já transmitiste conteúdo suficiente, para a transmissão.

1. No portal, selecione **Stop**.

1. No OBS, selecione o botão **Stop Streaming** no painel **Controlos.** Este passo impede a transmissão da OBS.

## <a name="play-the-on-demand-output-by-using-azure-media-player"></a>Jogue a saída a pedido utilizando o Azure Media Player

A saída que criou está agora disponível para streaming a pedido, desde que o seu ponto final de streaming esteja em funcionamento.

1. Vá à listagem de Serviços de Media e selecione **Ativos.**
1. Encontre a saída do evento que criou anteriormente e selecione o link para o ativo. A página de saída de ativos abre.
1. Copie o URL de streaming sob o leitor de vídeo para o ativo.
1. Volte ao Azure Media Player no navegador e colhe o URL de streaming na caixa URL.
1. Selecione **Update Player**.
1. Selecione o ícone **Reproduzir** no vídeo para visualizar o ativo on-demand.

## <a name="clean-up-resources"></a>Limpar recursos

> [!IMPORTANT]
> Parem os serviços! Depois de completar os passos neste arranque rápido, certifique-se de parar o evento ao vivo e o ponto final de streaming, ou será cobrado pelo tempo que eles permanecem em execução. Para parar o evento ao vivo, consulte o procedimento [de transmissão Stop,](#stop-the-broadcast) passos 2 e 3.

Para parar o ponto final de streaming:

1. A partir da listagem de Serviços de Media, selecione **pontos finais de Streaming**.
2. Selecione o ponto final de transmissão predefinido que começou anteriormente. Este passo abre a página do ponto final.
3. Selecione **Stop**.

> [!TIP]
> Se não quiser manter os ativos deste evento, certifique-se de eliminá-los para que não seja cobrado para armazenamento.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Eventos ao vivo e saídas ao vivo nos Serviços de Media](./live-events-outputs-concept.md)
