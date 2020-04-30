---
title: Criar um live stream da Azure Media Services
description: Saiba como criar um stream ao vivo da Azure Media Services utilizando o portal e a Wirecast
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/25/2020
ms.openlocfilehash: 459f11844f873a911b3e5702e8c768b1cd22e504
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80985002"
---
# <a name="create-an-azure-media-services-live-stream"></a>Criar um live stream da Azure Media Services

Este quickstart irá ajudá-lo a criar um stream ao vivo da Azure Media Services utilizando o portal Azure e a Telestream Wirecast. Assume que tem uma subscrição Azure e criou uma conta de Media Services.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Abra o seu navegador web e vá ao [portal Microsoft Azure.](https://portal.azure.com/) Introduza as suas credenciais para iniciar sessão no portal. A vista predefinida é o dashboard de serviço.

Neste início rápido, cobriremos:

- A criação de um codificador no local com um teste gratuito da Telestream Wirecast.
- A criar um fluxo em direto.
- Configurar saídas de transmissão ao vivo.
- Executando um ponto final de streaming predefinido.
- Utilizando o Azure Media Player para visualizar o fluxo ao vivo e a saída a pedido.

Para manter as coisas simples, usaremos um predefinição de codificação para a Azure Media Services em Wirecast, codificação em nuvem pass-through e RTMP.

## <a name="set-up-an-on-premises-encoder-by-using-wirecast"></a>Configurar um codificador no local utilizando a Wirecast

1. Descarregue e instale a Wirecast para o seu sistema operativo no site da [Telestream](https://www.telestream.net).
1. Inicie a aplicação e utilize o seu endereço de e-mail favorito para registar o produto. Mantenha a aplicação aberta.
1. No e-mail que recebe, verifique o seu endereço de e-mail. Em seguida, o pedido iniciará o julgamento gratuito.
1. Recomendado: Assista ao tutorial de vídeo no ecrã de aplicação de abertura.

## <a name="set-up-an-azure-media-services-live-stream"></a>Criar um live stream da Azure Media Services

1. Vá à conta Azure Media Services dentro do portal e, em seguida, selecione **live streaming** a partir da listagem de Serviços de **Media.**

   ![Link de streaming ao vivo](media/live-events-wirecast-quickstart/select-live-streaming.png)
1. Selecione **Adicionar evento ao vivo** para criar um novo evento de streaming ao vivo.

   ![Adicione ícone de evento ao vivo](media/live-events-wirecast-quickstart/add-live-event.png)
1. Insira um nome para o seu novo evento, como *TestLiveEvent,* na caixa de nomes do **evento Live.**

   ![Caixa de nome do evento ao vivo](media/live-events-wirecast-quickstart/live-event-name.png)
1. Introduza uma descrição opcional do evento na caixa **Descrição.**
1. Selecione o **Pass-through – sem** opção de codificação em nuvem.

   ![Opção de codificação de nuvens](media/live-events-wirecast-quickstart/cloud-encoding.png)
1. Selecione a opção **RTMP.**
1. Certifique-se de que a opção **No** é selecionada para **o evento Start live,** para evitar ser faturada para o evento ao vivo antes de estar pronto. (A faturação começará quando o evento ao vivo for iniciado.)

   ![Comece a opção de evento ao vivo](media/live-events-wirecast-quickstart/start-live-event-no.png)
1. Selecione o **botão Rever + criar** para rever as definições.
1. Selecione o botão **Criar** o evento ao vivo. Depois voltas para a lista de eventos ao vivo.
1. Selecione o link para o evento ao vivo que acabou de criar. Reparem que o seu evento está parado.
1. Mantenha esta página aberta no seu navegador. Voltaremos mais tarde.

## <a name="set-up-a-live-stream-by-using-wirecast-studio"></a>Configurar um live stream usando o Estúdio Wirecast

1. Na aplicação Wirecast, selecione **Create Empty Document** a partir do menu principal e, em seguida, selecione **Continuar**.

   ![Tela de início de arame](media/live-events-wirecast-quickstart/open-empty-document.png)
1. Pairar sobre a primeira camada na área das **camadas wirecast.**  Selecione o ícone **Adicionar** que aparece e selecione a entrada de vídeo que pretende transmitir.

   ![Ícone de adicionar arame](media/live-events-wirecast-quickstart/add-icon.png)

   A caixa de diálogo **Master Layer 1** abre.
1. Selecione **Video Capture** a partir do menu e, em seguida, selecione a câmara que pretende utilizar.

   ![Área de pré-visualização para captura de vídeo](media/live-events-wirecast-quickstart/video-shot-selection.png)

   A vista da câmara aparece na área de pré-visualização.
1. Pairar sobre a segunda camada na área das **camadas wirecast.** Selecione o ícone **Adicionar** que aparece e selecione a entrada de áudio que pretende transmitir. A caixa de diálogo **Master Layer 2** abre.
1. Selecione **a captura** de áudio do menu e, em seguida, selecione a entrada de áudio que pretende utilizar.

   ![Inputs para captura de áudio](media/live-events-wirecast-quickstart/audio-shot-select.png)
1. A partir do menu principal, selecione **as definições de saída**. Aparece a caixa de diálogo **Select a Output Destination.**
1. Selecione **Azure Media Services** da lista de lançamentos do **Destino.** A definição de saída para o Azure Media Services povoa automaticamente a *maioria* das definições de saída.

   ![Ecrã de definições de saída de difusão de arame](media/live-events-wirecast-quickstart/azure-media-services.png)


No próximo procedimento, você vai voltar para o Azure Media Services no seu navegador para copiar o URL de entrada para entrar nas definições de saída:

1. Na página azure Media Services do portal, selecione **Iniciar** o evento live stream. (A faturação começa agora.)

   ![Ícone de início](media/live-events-wirecast-quickstart/start.png)
2. Desloque o alternância **de segurança/não fixe** para **não fixar**. Este passo define o protocolo para a RTMP em vez de RTMPS.
3. Na caixa DEURL de **entrada,** copie o URL para a sua área de redação.

   ![URL de entrada](media/live-events-wirecast-quickstart/input-url.png)
4. Mude para a aplicação Wirecast e colá o URL de **entrada** na caixa **de endereços** nas definições de saída.

   ![URL de entrada de difusão de arame](media/live-events-wirecast-quickstart/input-url-wirecast.png)
5. Selecione **OK**.

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

## <a name="start-the-broadcast"></a>Inicie a transmissão

1. Na Wirecast, selecione **Output** > **Start / Stop Broadcasting** > **Start Azure Media Services: Azure Media Services** a partir do menu principal.

   ![Comece a transmitir itens de menu](media/live-events-wirecast-quickstart/start-broadcast.png)

   Quando o stream foi enviado para o evento ao vivo, a janela **ao vivo** em Wirecast aparece no leitor de vídeo na página de eventos ao vivo no Azure Media Services.

1. Selecione o botão **Go** sob a janela de pré-visualização para começar a transmitir o vídeo e o áudio que selecionou para as camadas wirecast.

   ![Botão wirecast Go](media/live-events-wirecast-quickstart/go-button.png)

   > [!TIP]
   > Se houver um erro, tente recarregar o leitor selecionando o link do **leitor de Recarga** acima do leitor.

## <a name="run-the-default-streaming-endpoint"></a>Executar o ponto final de streaming padrão

1. Selecione **pontos finais de Streaming** na listagem de Serviços de Media.

   ![Item de menu de pontos finais de streaming](media/live-events-wirecast-quickstart/streaming-endpoints.png)
1. Se o estado do ponto final de transmissão padrão for interrompido, selecione-o. Este passo leva-o à página para esse ponto final.
1. Selecione **Iniciar**.
   
   ![Botão de arranque para o ponto final de streaming](media/live-events-wirecast-quickstart/start.png)

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>Reproduzir a transmissão de saída utilizando o Azure Media Player

1. Copie o URL de streaming sob o leitor de vídeo **Output.**
1. Num navegador web, abra a [demonstração do Azure Media Player.](https://ampdemo.azureedge.net/azuremediaplayer.html)
1. Colhe o URL de streaming na caixa **DEURL** do Azure Media Player.
1. Selecione o botão **'Atualizar O Leitor'.**
1. Selecione o ícone **Reproduzir** no vídeo para ver o seu live stream.

## <a name="stop-the-broadcast"></a>Parem a transmissão

Quando pensares que já transmitiste conteúdo suficiente, para a transmissão.

1. Na Wirecast, selecione o botão **Broadcast.** Este passo para a transmissão da Wirecast.
1. No portal, selecione **Stop**. Em seguida, recebe uma mensagem de aviso de que o live stream vai parar, mas a saída passará a ser um ativo a pedido.
1. Selecione **Parar** na mensagem de aviso. O Azure Media Player mostra agora um erro, porque o live stream já não está disponível.

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
