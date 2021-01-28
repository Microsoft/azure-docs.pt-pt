---
title: Criar um live stream com o OBS Studio
description: Saiba como criar um live stream da Azure Media Services utilizando o portal e o OBS Studio
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 08/31/2020
ms.openlocfilehash: 83f072b67f5aa162137a55d2b311dccf0daf7f53
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956077"
---
# <a name="create-an-azure-media-services-live-stream-with-obs"></a>Criar um live stream Azure Media Services com OBS

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este quickstart irá ajudá-lo a criar um live stream da Azure Media Services utilizando o portal Azure e o Open Broadcasting Studio (OBS). Assume que tem uma subscrição da Azure e criou uma conta de Serviços de Comunicação Social.

Neste arranque rápido, cobriremos:

- Criação de um codificadora no local com OBS.
- A montar um fluxo ao vivo.
- Criando saídas de transmissão ao vivo.
- Executando um ponto final de streaming predefinido.
- Utilizar o Azure Media Player para ver o live stream e a saída on demand.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Abra o seu navegador web e vá ao [portal Microsoft Azure](https://portal.azure.com/). Introduza as suas credenciais para iniciar sessão no portal. A vista predefinida é o dashboard de serviço.

## <a name="set-up-an-on-premises-encoder-by-using-obs"></a>Configurar um codificadora no local utilizando o OBS

1. Descarregue e instale o OBS para o seu sistema operativo no site do [Open Broadcaster Software](https://obsproject.com/).
1. Inicie a aplicação e mantenha-a aberta.

## <a name="run-the-default-streaming-endpoint"></a>Executar o ponto final de streaming padrão

1. Selecione **pontos finais de streaming** na listagem de Serviços de Mídia.

   ![Item do menu de pontos finais de streaming](media/live-events-obs-quickstart/streaming-endpoints.png)
1. Se o estado de ponto final de streaming predefinido for interrompido, selecione-o. Este passo leva-o à página para esse ponto final.
1. Selecione **Iniciar**.

   ![Botão de arranque para o ponto final de streaming](media/live-events-obs-quickstart/start.png)

## <a name="set-up-an-azure-media-services-live-stream"></a>Criar um live stream da Azure Media Services

1. Aceda à conta Azure Media Services dentro do portal e, em seguida, selecione **streaming ao vivo** a partir da listagem de **Serviços de Media.**

   ![Link de streaming ao vivo](media/live-events-obs-quickstart/select-live-streaming.png)
1. **Selecione Adicionar evento ao vivo** para criar um novo evento de streaming ao vivo.

   ![Adicionar ícone de evento ao vivo](media/live-events-obs-quickstart/add-live-event.png)
1. Insira um nome para o seu novo evento, como *o TestLiveEvent,* na caixa **de nome do evento Live.**

   ![Caixa de nome de evento ao vivo](media/live-events-obs-quickstart/live-event-name.png)
1. Introduza uma descrição opcional do evento na caixa **Descrição.**
1. Selecione o **'Pass-through' – sem opção de codificação de nuvens.**

   ![Opção de codificação em nuvem](media/live-events-obs-quickstart/cloud-encoding.png)
1. Selecione a opção **RTMP.**
1. Certifique-se de que a opção **No** é selecionada para **o evento Start live**, para evitar ser faturado para o evento ao vivo antes de estar pronto. (A faturação começará quando o evento ao vivo for iniciado.)

   ![Inicie a opção de evento ao vivo](media/live-events-obs-quickstart/start-live-event-no.png)
1. Selecione o botão **'Rever +'** para rever as definições.
1. Selecione o botão **Criar** para criar o evento ao vivo. Depois voltaste ao anúncio de eventos ao vivo.
1. Selecione o link para o evento ao vivo que acabou de criar. Note que o seu evento está parado.
1. Mantenha esta página aberta no seu browser. Voltaremos mais tarde.

## <a name="set-up-a-live-stream-by-using-obs-studio"></a>Configurar um live stream usando o OBS Studio

O OBS começa com uma cena predefinido, mas sem entradas selecionadas.

   ![Ecrã padrão OBS](media/live-events-obs-quickstart/live-event-obs-default-screen.png)

### <a name="add-a-video-source"></a>Adicionar uma fonte de vídeo

1. A partir do painel **Fontes,** clique no ícone **adicionar** para selecionar um novo dispositivo de origem. O menu **'Fontes'** será aberto.

1. Selecione **o dispositivo de captura** de vídeo a partir do menu do dispositivo de origem. O menu **Criar/Selecionar Fonte** abrir-se-á.

   ![Menu de fontes OBS com dispositivo de vídeo selecionado](media/live-events-obs-quickstart/live-event-obs-video-device-menu.png)

1. Selecione o botão de rádio **Add Existente** e, em seguida, clique **em OK**. O menu **Propriedades para Dispositivo de Vídeo** será aberto.

   ![Novo menu de origem de vídeo OBS com adicionar selecionados](media/live-events-obs-quickstart/live-event-obs-new-video-source.png)

1. Na lista de retirada do **dispositivo,** selecione a entrada de vídeo que pretende utilizar para a sua transmissão. Deixe o resto das definições em paz por enquanto, e clique **em OK**. A fonte de entrada será adicionada ao painel **'Fontes'** e a visualização de entrada de vídeo aparecerá na área **de Pré-visualização.**

   ![Definições de câmaras OBS](media/live-events-obs-quickstart/live-event-surface-camera.png)

### <a name="add-an-audio-source"></a>Adicionar uma fonte de áudio

1. A partir do painel **Fontes,** clique no ícone **adicionar** para selecionar um novo dispositivo de origem. O menu Dispositivo Fonte será aberto.

1. Selecione Captura de entrada de **áudio** a partir do menu do dispositivo de origem. O menu **Criar/Selecionar Fonte** abrir-se-á.

   ![Menu de fontes OBS com dispositivo de áudio selecionado](media/live-events-obs-quickstart/live-event-obs-audio-device-menu.png)

1. Selecione o botão de rádio **Add Existente** e, em seguida, clique **em OK**. As propriedades para o menu **de captura de entrada de áudio** serão abertas.

   ![Fonte de áudio OBS com adicionar selecionado existente ](media/live-events-obs-quickstart/live-event-obs-new-audio-source.png)

1. A partir da lista de retirada do **dispositivo,** selecione o dispositivo de captura de áudio que pretende utilizar para a sua transmissão. Deixe o resto das definições em paz por enquanto, e clique em OK. O dispositivo de captura de áudio será adicionado ao painel misturador de áudio.

   ![Lista de retirada de dispositivos de áudio OBS](media/live-events-obs-quickstart/live-event-select-audio-device.png)

### <a name="set-up-streaming-in-obs"></a>Configurar streaming em OBS

No procedimento seguinte, você irá voltar ao Azure Media Services no seu navegador para copiar o URL de entrada para introduzir nas definições de saída:

1. Na página Azure Media Services do portal, selecione **Começar** a iniciar o evento live stream. (A faturação começa agora.)

   ![Ícone de início](media/live-events-obs-quickstart/start.png)
1. Desaponte o **toggle RTMP** para **RTMPS**.
1. Na caixa **URL de entrada,** copie o URL para a sua área de transferência.

   ![URL de entrada](media/live-events-obs-quickstart/input-url.png)

1. Mude para a aplicação OBS.

1. Clique no **botão Definições** no painel **Controlos.** As opções de Definições serão abertas.

   ![Painel de controlos OBS com definições selecionadas](media/live-events-obs-quickstart/live-event-obs-settings.png)

1. Selecione **Stream** no menu **Definições.**

1. Na lista de downdown de **Serviço,** selecione Mostrar tudo e, em seguida, selecione **Custom...**.

1. No campo **Server,** cole o URL RTMPS que copiou para a sua área de transferência.

1. Introduza algo no campo **de chave stream.**  Não importa o que é, mas precisa de ter um valor.

    ![Definições de fluxo OBS](media/live-events-obs-quickstart/live-event-obs-stream-settings.png)

1. Selecione **Saída** do menu **Definições.**

1. Introduza *2* no campo de intervalo do **quadro-chave.** Isto define o comprimento do fragmento em 2 segundos. Para uma entrega ao vivo de latência mais baixa, utilize o valor de 1 segundo.

1. OPCIONAL: Coloque a **predefinição de utilização** do CPU *muito rapidamente* se estiver a utilizar um computador com pouca potência de processamento. Opcionalmente, pode definir os kbps para algo mais baixo se houver condições de rede indesejáveis.

   ![Definições de saída do OBS](media/live-events-obs-quickstart/live-event-obs-advanced-output-settings.png)

1. Deixe as restantes definições inalteradas e clique **em OK**.

### <a name="start-streaming"></a>Começar a transmitir

1. No painel **Controlos,** clique **em Iniciar Streaming**.

    ![OBS iniciar botão de streaming](media/live-events-obs-quickstart/live-event-obs-start-streaming.png)

2. Mude para o ecrã de evento Azure Media Services Live no seu navegador e clique no link **Reload Player.** Deve agora ver o seu stream no leitor de pré-visualização.

## <a name="set-up-outputs"></a>Configurar saídas

Esta parte irá configurar as suas saídas e permitir-lhe guardar uma gravação do seu live stream.  

> [!NOTE]
> Para transmitir esta saída, o ponto final de streaming deve estar em execução. Ver mais tarde [Executar a secção de ponto final de streaming predefinido.](#run-the-default-streaming-endpoint)

1. Selecione o link **de saídas Create** abaixo do visualizador de vídeo **Outputs.**
1. Se quiser, edite o nome da saída na caixa **Nome** para algo mais fácil de utilizar para que seja fácil de encontrar mais tarde.

   ![Caixa de nome de saída](media/live-events-wirecast-quickstart/output-name.png)
1. Deixe todo o resto das caixas em paz por enquanto.
1. Selecione **Seguinte** para adicionar um localizador de streaming.
1. Mude o nome do localizador para algo mais fácil de utilizar, se quiser.

   ![Caixa de nome localizador](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Deixe tudo o resto neste ecrã sozinho por enquanto.
1. Selecione **Criar**.

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>Jogue a transmissão de saída usando o Azure Media Player

1. Copie o URL de streaming sob o leitor de vídeo **Output.**
1. Num navegador web, abra a demonstração do [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html).
1. Cole o URL de streaming na caixa **de URL** do Azure Media Player.
1. Selecione o botão **'Actualizar' Player.**
1. Selecione o ícone **Reproduzir** no vídeo para ver o seu live stream.

## <a name="stop-the-broadcast"></a>Pare a transmissão

Quando pensa que já transmitiu conteúdo suficiente, pare a transmissão.

1. No portal, **selecione Stop**.

1. Em OBS, selecione o botão **Stop Streaming** no painel **Controlos.** Este passo impede a transmissão da OBS.

## <a name="play-the-on-demand-output-by-using-azure-media-player"></a>Jogue a saída a pedido utilizando o Azure Media Player

A saída que criou está agora disponível para streaming a pedido, desde que o seu ponto final de streaming esteja em execução.

1. Aceda à listagem de Serviços de Comunicação e selecione **Ativos.**
1. Encontre a saída do evento que criou anteriormente e selecione o link para o ativo. A página de saída do ativo abre.
1. Copie o URL de streaming sob o leitor de vídeo para o ativo.
1. Volte ao Azure Media Player no navegador e cole o URL de streaming na caixa URL.
1. Selecione **Update Player**.
1. Selecione o ícone **Reproduzir** no vídeo para ver o ativo a pedido.

## <a name="clean-up-resources"></a>Limpar os recursos

> [!IMPORTANT]
> Parem os serviços! Depois de completar os passos neste quickstart, não se esqueça de parar o evento ao vivo e o ponto final de streaming, ou será cobrado pelo tempo que continuarem a correr. Para parar o evento ao vivo, consulte o procedimento [stop the broadcast,](#stop-the-broadcast) passos 2 e 3.

Para parar o ponto final de streaming:

1. A partir da listagem de Serviços de Mídia, selecione **Streaming endpoints**.
2. Selecione o ponto final de streaming predefinido que começou anteriormente. Este passo abre a página do ponto final.
3. **Selecione Stop**.

> [!TIP]
> Se não quiser manter os ativos deste evento, certifique-se de eliminá-los para que não seja cobrado para armazenamento.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Eventos ao vivo e saídas ao vivo nos Serviços de Media](./live-events-outputs-concept.md)
