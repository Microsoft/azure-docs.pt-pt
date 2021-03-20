---
title: Criar um live stream da Azure Media Services
description: Saiba como criar um live stream da Azure Media Services utilizando o portal e o Wirecast
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 08/31/2020
ms.openlocfilehash: 76bbb980b6430f7cffc23ec078e2c932128dc637
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89265274"
---
# <a name="create-an-azure-media-services-live-stream"></a>Criar um live stream da Azure Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este quickstart irá ajudá-lo a criar um live stream da Azure Media Services utilizando o portal Azure e o Telestream Wirecast. Assume que tem uma subscrição da Azure e criou uma conta de Serviços de Comunicação Social.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Abra o seu navegador web e vá ao [portal Microsoft Azure](https://portal.azure.com/). Introduza as suas credenciais para iniciar sessão no portal. A vista predefinida é o dashboard de serviço.

Neste arranque rápido, cobriremos:

- Criação de um codificadora no local com um teste gratuito da Telestream Wirecast.
- A montar um fluxo ao vivo.
- Criando saídas de transmissão ao vivo.
- Executando um ponto final de streaming predefinido.
- Utilizar o Azure Media Player para ver o live stream e a saída on demand.

Para manter as coisas simples, usaremos uma predefinição codificadora para a Azure Media Services em Wirecast, codificação de nuvem de passagem e RTMP.

## <a name="set-up-an-on-premises-encoder-by-using-wirecast"></a>Configurar um codificadora no local utilizando o Wirecast

1. Descarregue e instale o Wirecast para o seu sistema operativo no website da [Telestream](https://www.telestream.net).
1. Inicie a aplicação e use o seu endereço de e-mail favorito para registar o produto. Mantenha o pedido aberto.
1. No e-mail que recebe, verifique o seu endereço de e-mail. Então o pedido começará o julgamento gratuito.
1. Recomendado: Assista ao tutorial de vídeo no ecrã de abertura da aplicação.

## <a name="set-up-an-azure-media-services-live-stream"></a>Criar um live stream da Azure Media Services

1. Aceda à conta Azure Media Services dentro do portal e, em seguida, selecione **streaming ao vivo** a partir da listagem de **Serviços de Media.**

   ![Link de streaming ao vivo](media/live-events-wirecast-quickstart/select-live-streaming.png)
1. **Selecione Adicionar evento ao vivo** para criar um novo evento de streaming ao vivo.

   ![Adicionar ícone de evento ao vivo](media/live-events-wirecast-quickstart/add-live-event.png)
1. Insira um nome para o seu novo evento, como *o TestLiveEvent,* na caixa **de nome do evento Live.**

   ![Caixa de nome de evento ao vivo](media/live-events-wirecast-quickstart/live-event-name.png)
1. Introduza uma descrição opcional do evento na caixa **Descrição.**
1. Selecione o **'Pass-through' – sem opção de codificação de nuvens.**

   ![Opção de codificação em nuvem](media/live-events-wirecast-quickstart/cloud-encoding.png)
1. Selecione a opção **RTMP.**
1. Certifique-se de que a opção **No** é selecionada para **o evento Start live**, para evitar ser faturado para o evento ao vivo antes de estar pronto. (A faturação começará quando o evento ao vivo for iniciado.)

   ![Inicie a opção de evento ao vivo](media/live-events-wirecast-quickstart/start-live-event-no.png)
1. Selecione o botão **'Rever +'** para rever as definições.
1. Selecione o botão **Criar** para criar o evento ao vivo. Depois voltaste ao anúncio de eventos ao vivo.
1. Selecione o link para o evento ao vivo que acabou de criar. Note que o seu evento está parado.
1. Mantenha esta página aberta no seu browser. Voltaremos mais tarde.

## <a name="set-up-a-live-stream-by-using-wirecast-studio"></a>Configurar um live stream usando o Wirecast Studio

1. Na aplicação Wirecast, selecione **Criar Documento Vazio** a partir do menu principal e, em seguida, selecione **Continue**.

   ![Ecrã de início de wirecast](media/live-events-wirecast-quickstart/open-empty-document.png)
1. Paire sobre a primeira camada na área das **camadas de wirecast.**  Selecione o ícone **Adicionar** que aparece e selecione a entrada de vídeo que pretende transmitir.

   ![Ícone de adicionar wirecast](media/live-events-wirecast-quickstart/add-icon.png)

   Abre-se a caixa de diálogo **Master Layer 1.**
1. Selecione **Video Capture** no menu e, em seguida, selecione a câmara que pretende utilizar.

   ![Área de pré-visualização para captura de vídeo](media/live-events-wirecast-quickstart/video-shot-selection.png)

   A vista da câmara aparece na área de pré-visualização.
1. Pairar sobre a segunda camada na área das **camadas de wirecast.** Selecione o ícone **Adicionar** que aparece e selecione a entrada de áudio que pretende transmitir. Abre-se a caixa de diálogo **Master Layer 2.**
1. Selecione **a captura** de áudio no menu e, em seguida, selecione a entrada de áudio que pretende utilizar.

   ![Entradas para captura de áudio](media/live-events-wirecast-quickstart/audio-shot-select.png)
1. A partir do menu principal, selecione **definições de saída**. Aparece a caixa de diálogo **Select a Output Destination.**
1. Selecione **Azure Media Services** **da** lista de drop-down destination. A definição de saída para a Azure Media Services povoa automaticamente a *maioria* das definições de saída.

   ![Ecrã de definições de saída de wirecast](media/live-events-wirecast-quickstart/azure-media-services.png)


No procedimento seguinte, você irá voltar ao Azure Media Services no seu navegador para copiar o URL de entrada para introduzir nas definições de saída:

1. Na página Azure Media Services do portal, selecione **Começar** a iniciar o evento live stream. (A faturação começa agora.)

   ![Ícone de início](media/live-events-wirecast-quickstart/start.png)
2. Coloque o **alternador Secure/Not secure** to Secure to **Not secure**. Este passo define o protocolo para RTMP em vez de RTMPS.
3. Na caixa **URL de entrada,** copie o URL para a sua área de transferência.

   ![URL de entrada](media/live-events-wirecast-quickstart/input-url.png)
4. Mude para a aplicação Wirecast e cole o **URL de entrada** na caixa de **endereços** nas definições de saída.

   ![URL de entrada de wirecast](media/live-events-wirecast-quickstart/input-url-wirecast.png)
5. Selecione **OK**.

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

## <a name="start-the-broadcast"></a>Inicie a transmissão

1. No Wirecast, selecione **Output**  >  **Start / Stop Broadcasting** Start  >  **Azure Media Services: Azure Media Services** a partir do menu principal.

   ![Inicie os itens de menu de transmissão](media/live-events-wirecast-quickstart/start-broadcast.png)

   Quando o stream foi enviado para o evento ao vivo, a janela **ao vivo** em Wirecast aparece no leitor de vídeo na página de evento ao vivo nos Serviços Azure Media.

1. Selecione o botão **Go** por baixo da janela de pré-visualização para começar a transmitir o vídeo e o áudio que selecionou para as camadas Wirecast.

   ![Botão Wirecast Go](media/live-events-wirecast-quickstart/go-button.png)

   > [!TIP]
   > Se houver um erro, tente recarregar o leitor selecionando a ligação do **leitor Reload** acima do leitor.

## <a name="run-the-default-streaming-endpoint"></a>Executar o ponto final de streaming padrão

1. Selecione **pontos finais de streaming** na listagem de Serviços de Mídia.

   ![Item do menu de pontos finais de streaming](media/live-events-wirecast-quickstart/streaming-endpoints.png)
1. Se o estado de ponto final de streaming predefinido for interrompido, selecione-o. Este passo leva-o à página para esse ponto final.
1. Selecione **Iniciar**.
   
   ![Botão de arranque para o ponto final de streaming](media/live-events-wirecast-quickstart/start.png)

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>Jogue a transmissão de saída usando o Azure Media Player

1. Copie o URL de streaming sob o leitor de vídeo **Output.**
1. Num navegador web, abra a demonstração do [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html).
1. Cole o URL de streaming na caixa **de URL** do Azure Media Player.
1. Selecione o botão **'Actualizar' Player.**
1. Selecione o ícone **Reproduzir** no vídeo para ver o seu live stream.

## <a name="stop-the-broadcast"></a>Pare a transmissão

Quando pensa que já transmitiu conteúdo suficiente, pare a transmissão.

1. No Wirecast, selecione o **botão Broadcast.** Este passo impede a transmissão da Wirecast.
1. No portal, **selecione Stop**. Em seguida, recebe uma mensagem de aviso de que o live stream irá parar, mas a saída passará a ser um ativo a pedido.
1. **Selecione Parar** na mensagem de aviso. O Azure Media Player mostra agora um erro, porque o live stream já não está disponível.

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
