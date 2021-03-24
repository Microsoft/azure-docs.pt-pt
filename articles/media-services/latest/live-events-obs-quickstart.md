---
title: Criar um live stream com o OBS Studio
description: Saiba como criar um live stream da Azure Media Services utilizando o portal e o OBS Studio
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/20/2021
ms.openlocfilehash: d52affbdc4dc433c40be687f2e56afae4bcf4c2a
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104949932"
---
# <a name="create-an-azure-media-services-live-stream-with-obs"></a>Criar um live stream Azure Media Services com OBS

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este quickstart irá ajudá-lo a criar um Evento Ao Vivo dos Media Services utilizando o portal Azure e transmitido através do Open Broadcasting Studio (OBS). Assume que tem uma subscrição da Azure e criou uma conta de Serviços de Comunicação Social.

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

   ![Item do menu de pontos finais de streaming.](media/live-events-obs-quickstart/streaming-endpoints.png)
1. Se o estado de ponto final de streaming predefinido for interrompido, selecione-o. Este passo leva-o à página para esse ponto final.
1. Selecione **Iniciar**.

   ![Botão de arranque para o ponto final de streaming.](media/live-events-obs-quickstart/start.png)

## <a name="set-up-an-azure-media-services-live-stream"></a>Criar um live stream da Azure Media Services

1. Aceda à conta Azure Media Services dentro do portal e, em seguida, selecione **streaming ao vivo** a partir da listagem de **Serviços de Media.**

   ![Ligação de streaming ao vivo.](media/live-events-obs-quickstart/select-live-streaming.png)
1. **Selecione Adicionar evento ao vivo** para criar um novo evento de streaming ao vivo.

   ![Adicione o ícone do evento ao vivo.](media/live-events-obs-quickstart/add-live-event.png)
1. Insira um nome para o seu novo evento, como *o TestLiveEvent,* na caixa **de nome do evento Live.**

   ![Caixa de nomes de evento ao vivo.](media/live-events-obs-quickstart/live-event-name.png)
1. Introduza uma descrição opcional do evento na caixa **Descrição.**
1. Selecione o **'Pass-through' – sem opção de codificação de nuvens.**

   ![Opção de codificação em nuvem.](media/live-events-obs-quickstart/cloud-encoding.png)
1. Selecione a opção **RTMP.**
1. Certifique-se de que a opção **No** é selecionada para **o evento Start live**, para evitar ser faturado para o evento ao vivo antes de estar pronto. (A faturação começará quando o evento ao vivo for iniciado.)

   ![Inicie a opção de evento ao vivo.](media/live-events-obs-quickstart/start-live-event-no.png)
1. Selecione o botão **'Rever +'** para rever as definições.
1. Selecione o botão **Criar** para criar o evento ao vivo. Depois voltaste ao anúncio de eventos ao vivo.
1. Selecione o link para o evento ao vivo que criou. Note que o seu evento está parado.
1. Mantenha esta página aberta no seu browser. Voltaremos mais tarde.

## <a name="set-up-a-live-stream-by-using-obs-studio"></a>Configurar um live stream usando o OBS Studio

O OBS começa com uma cena predefinido, mas sem entradas selecionadas.

   ![Ecrã padrão OBS](media/live-events-obs-quickstart/live-event-obs-default-screen.png)

### <a name="add-a-video-source"></a>Adicionar uma fonte de vídeo

1. A partir do painel **'Fontes',** selecione o ícone **de adicionar** para selecionar um novo dispositivo de origem. O menu **'Fontes'** será aberto.

1. Selecione **o dispositivo de captura** de vídeo a partir do menu do dispositivo de origem. O menu **Criar/Selecionar Fonte** abrir-se-á.

   ![Menu de fontes OBS com dispositivo de vídeo selecionado.](media/live-events-obs-quickstart/live-event-obs-video-device-menu.png)

1. Selecione o botão de rádio **Add Existente** e, em seguida, selecione **OK**. O menu **Propriedades para Dispositivo de Vídeo** será aberto.

   ![Novo menu de origem de vídeo OBS com adicionar selecionados existentes.](media/live-events-obs-quickstart/live-event-obs-new-video-source.png)

1. Na lista de retirada do **dispositivo,** selecione a entrada de vídeo que pretende utilizar para a sua transmissão. Deixe o resto das definições em paz por enquanto e selecione **OK**. A fonte de entrada será adicionada ao painel **'Fontes'** e a visualização de entrada de vídeo aparecerá na área **de Pré-visualização.**

   ![Definições de câmaras OBS](media/live-events-obs-quickstart/live-event-surface-camera.png)

### <a name="add-an-audio-source"></a>Adicionar uma fonte de áudio

1. A partir do painel **'Fontes',** selecione o ícone **de adicionar** para selecionar um novo dispositivo de origem. O menu Dispositivo Fonte será aberto.

1. Selecione Captura de entrada de **áudio** a partir do menu do dispositivo de origem. O menu **Criar/Selecionar Fonte** abrir-se-á.

   ![Menu de fontes OBS com dispositivo de áudio selecionado.](media/live-events-obs-quickstart/live-event-obs-audio-device-menu.png)

1. Selecione o botão de rádio **Add Existente** e, em seguida, selecione **OK**. As propriedades para o menu **de captura de entrada de áudio** serão abertas.

   ![Fonte de áudio OBS com adicionar selecionado.](media/live-events-obs-quickstart/live-event-obs-new-audio-source.png)

1. A partir da lista de retirada do **dispositivo,** selecione o dispositivo de captura de áudio que pretende utilizar para a sua transmissão. Deixe o resto das definições em paz por enquanto, e selecione OK. O dispositivo de captura de áudio será adicionado ao painel misturador de áudio.

   ![Lista de retirada de dispositivos de áudio OBS](media/live-events-obs-quickstart/live-event-select-audio-device.png)

### <a name="set-up-streaming-and-advanced-encoding-settings-in-obs"></a>Configurar definições de codificação avançadas e de streaming em OBS

No procedimento seguinte, você irá voltar ao Azure Media Services no seu navegador para copiar o URL de entrada para introduzir nas definições de saída:

1. Na página Azure Media Services do portal, selecione **Começar** a iniciar o evento live stream. (A faturação começa agora.)

   ![Ícone de início.](media/live-events-obs-quickstart/start.png)
1. Desaponte o **toggle RTMP** para **RTMPS**.
1. Na caixa **URL de entrada,** copie o URL para a sua área de transferência.

   ![URL de entrada.](media/live-events-obs-quickstart/input-url.png)

1. Mude para a aplicação OBS.

1. Selecione o **botão Definições** no painel **De Controlos.** As opções de Definições serão abertas.

   ![Painel de controlo do OBS com as definições selecionadas.](media/live-events-obs-quickstart/live-event-obs-settings.png)

1. Selecione **Stream** no menu **Definições.**

1. Na lista de downdown de **Serviço,** selecione Mostrar tudo e, em seguida, selecione **Custom...**.

1. No campo **Server,** cole o URL RTMPS que copiou para a sua área de transferência.

1. Introduza algo no campo **de chave stream.**  Não importa o que é, mas precisa de ter um valor.

    ![Definições de fluxo OBS.](media/live-events-obs-quickstart/live-event-obs-stream-settings.png)

1. Selecione **Saída** do menu **Definições.**

1. Selecione o modo de **saída** no topo da página e escolha **Advanced** para aceder a todas as definições de codificadores disponíveis.

1. Selecione o **separador Streaming** para configurar o codificar.

1. Selecione o codificar certo para o seu sistema.  Se o seu hardware suportar a aceleração da GPU, escolha entre NVIDIA **NVENC** H.264 ou Intel **QuickSync** H.264. Se o seu sistema não tiver uma GPU suportada, selecione a opção codificação de software **X264.**

#### <a name="x264-encoder-settings"></a>Definições de Codificadores X264

1. Se tiver selecionado a opção de codificação **X264,** selecione a caixa **de saída de Rescale.** Selecione 1920x1080 se estiver a utilizar um Evento Premium Live em Media Services ou 1280x720 se estiver a utilizar um Evento Ao Vivo Standard (720P).  Se estiver a utilizar um evento ao vivo, pode escolher qualquer resolução disponível.

1. Coloque o **Bitrate** em qualquer lugar entre 1500 Kbps e 4000 Kbps. Recomendamos 2500 Kbps se estiver a utilizar um Standard codificando o Live Event a 720P. Se estiver a utilizar um Evento Premium Live de 1080P, recomenda-se 4000 Kbps. Pode desejar ajustar o bitrate com base nas capacidades de CPU disponíveis e largura de banda na sua rede para alcançar a definição de qualidade desejada.

1. Introduza *2* no campo de intervalo do **quadro-chave.** O valor define o intervalo do quadro da chave para 2 segundos, que controla o tamanho final dos fragmentos entregues sobre HLS ou DASH dos Serviços de Comunicação Social. Nunca estale o intervalo de quadro da chave superior a 4 segundos.  Se estiver a ver uma alta latência ao transmitir, deve sempre verificar ou informar os utilizadores da sua aplicação para definir sempre este valor em 2 segundos. Ao tentar obter uma entrega ao vivo de latência mais baixa, pode optar por definir este valor até 1 segundo.

1. OPCIONAL: Defina a Predefinição de Utilização do CPU muito **rapidamente** e faça algumas experiências para ver se o cpu local consegue lidar com a combinação de bitrate e predefinido com sobrecarga suficiente. Tente evitar configurações que resultariam num CPU médio superior a 80% para evitar problemas durante o streaming ao vivo. Para melhorar a qualidade, pode testar com configurações predefinidas **mais rápidas** e **rápidas** até atingir as limitações do CPU.

   ![Definições de codificadores OBS X264](media/live-events-obs-quickstart/live-event-obs-x264-settings.png)

1. Deixe as restantes definições inalteradas e selecione **OK**.

#### <a name="nvidia-nvenc-encoder-settings"></a>Definições de codificadores Nvidia NVENC

1. Se selecionou a opção de codificação **GPU NVENC,** verifique a caixa **de saída de reescalctura** e selecione 1920x1080 se estiver a utilizar um Evento Premium Live em Serviços de Media ou 1280x720 se estiver a utilizar um Evento Ao Vivo Standard (720P). Se estiver a utilizar um evento ao vivo, pode escolher qualquer resolução disponível.

1. Desagrere o **Controlo de Tarifas** para CBR para controlo da taxa de bitrate constante.

1. Coloque o **Bitrate** entre 1500 Kbps e 4000 Kbps. Recomendamos 2500 Kbps se estiver a utilizar um Standard codificando o Live Event a 720P. Se estiver a utilizar um Evento Premium Live de 1080P, recomenda-se 4000 Kbps. Pode optar por ajustar isto com base nas capacidades de CPU disponíveis e largura de banda na sua rede para alcançar a definição de qualidade desejada.

1. Desa estale o **intervalo do quadro-chave** para 2 segundos, como indicado acima nas opções X264. Não exceda 4 segundos, pois isto pode afetar significativamente a latência da sua transmissão em direto.

1. Defina o **Predefinição** para Baixa Latência, Low-Latency Performance ou Low-Latency Qualidade dependendo da velocidade do CPU na sua máquina local. Experimente estas definições para alcançar o melhor equilíbrio entre qualidade e utilização de CPU no seu próprio hardware.

1. Desa estade o **Perfil** para "principal" ou "alto" se estiver a utilizar uma configuração de hardware mais potente.

1. Deixe o **Look-ahead** sem controlo. Se tiver uma máquina muito poderosa, pode verificar isto.

1. Deixe a **sintonização visual psicosa** descontrolada. Se tiver uma máquina muito poderosa, pode verificar isto.

1. Desloque a **GPU** a 0 para decidir automaticamente quais as GPUs a atribuir. Se desejar, pode restringir a utilização da GPU.

1. Definir os **quadros Max B** para 2

   ![Obs NVidia NVidia NVidia NVIDIA GPU configurações de codificadores.](media/live-events-obs-quickstart/live-event-obs-nvidia-settings.png)

#### <a name="intel-quicksync-encoder-settings"></a>Configurações de Codificação Intel QuickSync

1. Se selecionou a opção de codificação gpu da Intel **QuickSync,** verifique a caixa **de saída de reescalcário** e selecione ou 1920x1080 se estiver a utilizar um Evento Premium Live em Serviços de Media ou 1280x720 se estiver a utilizar um Evento Ao Vivo Standard (720P). Se estiver a utilizar um evento ao vivo, pode escolher qualquer resolução disponível.

1. Desajuste a **Utilização do Alvo** para "equilibrado" ou ajuste conforme necessário com base na carga combinada do CPU e da GPU. Ajuste conforme necessário e experimente para obter uma utilização de CPU de 80% máxima, em média, com a qualidade que o seu hardware é capaz de produzir. Se estiver com hardware mais restrito, teste com "rápido" ou desça para "muito rápido" se estiver com problemas de desempenho.

1. Desa estade o **Perfil** para "principal" ou "alto" se estiver a utilizar uma configuração de hardware mais potente.

1. Desa estale o **intervalo do quadro-chave** para 2 segundos, como indicado acima nas opções X264. Não exceda 4 segundos, pois isto pode afetar significativamente a latência da sua transmissão em direto.

1. Desagrere o **Controlo de Tarifas** para CBR para controlo da taxa de bitrate constante.

1. Coloque o **Bitrate** entre 1500 e 4000 Kbps.  Recomendamos 2500 Kbps se estiver a utilizar um Standard codificando o Live Event a 720P. Se estiver a utilizar um Evento Premium Live de 1080P, recomenda-se 4000 Kbps. Pode optar por ajustar isto com base nas capacidades de CPU disponíveis e largura de banda na sua rede para alcançar a definição de qualidade desejada.

1. Coloque a **Latência** em "baixo".

1. Desagram os **quadros B** para 2.

1. Deixe as **melhorias de vídeo subjetivas** descontroladas.

   ![Obs Intel QuickSync GPU configurações de codificadores.](media/live-events-obs-quickstart/live-event-obs-intel-settings.png)

### <a name="set-audio-settings"></a>Definir definições de áudio

No procedimento seguinte, ajustará as definições de codificação de áudio.

1. Selecione o separador '>'s Desemcoso em Definições.

1. Ajuste a placa 1 **Bitrate de áudio** para 128 Kbps.

   ![Definições de Bitrate de áudio OBS.](media/live-events-obs-quickstart/live-event-obs-audio-output-panel.png)

1. Selecione o separador Áudio em Definições.

1. Desloque a **taxa de amostra** para 44,1 kHz.

   ![Definições de taxa de amostra de áudio OBS.](media/live-events-obs-quickstart/live-event-obs-audio-sample-rate-settings.png)

### <a name="start-streaming"></a>Começar a transmitir

1. No painel **Controlos,** clique **em Iniciar Streaming**.

    ![OBS começa a transmitir botão.](media/live-events-obs-quickstart/live-event-obs-start-streaming.png)

2. Mude para o ecrã de evento Azure Media Services Live no seu navegador e clique no link **Reload Player.** Deve agora ver o seu stream no leitor de pré-visualização.

## <a name="set-up-outputs"></a>Configurar saídas

Esta parte irá configurar as suas saídas e permitir-lhe guardar uma gravação do seu live stream.  

> [!NOTE]
> Para transmitir esta saída, o ponto final de streaming deve estar em execução. Ver mais tarde [Executar a secção de ponto final de streaming predefinido.](#run-the-default-streaming-endpoint)

1. Selecione o link **de saídas Create** abaixo do visualizador de vídeo **Outputs.**
1. Se quiser, edite o nome da saída na caixa **Nome** para algo mais fácil de utilizar para que seja fácil de encontrar mais tarde.

   ![Caixa de nome de saída.](media/live-events-wirecast-quickstart/output-name.png)
1. Deixe todo o resto das caixas em paz por enquanto.
1. Selecione **Seguinte** para adicionar um localizador de streaming.
1. Mude o nome do localizador para algo mais fácil de utilizar, se quiser.

   ![Caixa de nome localizador.](media/live-events-wirecast-quickstart/live-event-locator.png)
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
