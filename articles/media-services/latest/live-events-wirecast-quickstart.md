---
title: Crie um stream azure Media Services live com o portal e wirecast
description: Saiba como criar um stream azure Media Service Live
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/25/2020
ms.openlocfilehash: e5bdd75ca61d53a64f003633d74e3d8f7992a98b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80336440"
---
# <a name="create-a-azure-media-services-live-stream-with-the-portal-and-wirecast"></a>Criar um stream azure Media Services live com o portal e wirecast

Este Guia De Início de Partida assume que tem uma subscrição Azure e criou uma conta Azure Media Services.

Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Abra o browser e navegue para o [portal do Microsoft Azure](https://portal.azure.com/). Introduza as suas credenciais para iniciar sessão no portal. A vista predefinida é o dashboard de serviço.

Neste arranque rápido, cobriremos:

- Criação de um codificador no local com um teste gratuito da Telestream Wirecast
- Criação de um fluxo ao vivo
- Configuração de saídas de fluxo ao vivo
- Executando um ponto final de streaming padrão
- Usando o Azure Media Player para ver o fluxo ao vivo e a saída a pedido

Para manter as coisas simples, usaremos um predefinição de codificação para a Azure Media Services em Wirecast, codificação em nuvem pass-through e RTMP.

## <a name="setting-up-an-on-premises-encoder-with-wirecast"></a>Configuração de um codificador no local com a Wirecast

1. Descarregue e instale a Wirecast para o seu sistema operativo emhttps://www.telestream.net
1. Inicie a aplicação e utilize o seu endereço de e-mail favorito para registar o produto.  Mantenha a aplicação aberta.
1. Receberá um e-mail pedindo-lhe que verifique o seu endereço de e-mail, então a aplicação iniciará o teste gratuito.
1. RECOMENDADO: Assista ao tutorial de vídeo no ecrã de aplicação de abertura.

## <a name="setting-up-an-azure-media-services-live-stream"></a>Criação de um live stream da Azure Media Services

1. Depois de ter navegado para a conta Azure Media Services dentro do portal, selecione **streaming ao vivo** a partir da listagem de Serviços de Media.<br/>
![Selecione link de streaming ao vivo](media/live-events-wirecast-quickstart/select-live-streaming.png)<br/>
1. Clique em **Adicionar evento ao vivo** para criar um novo evento de streaming ao vivo.<br/>
![Adicione ícone de evento ao vivo](media/live-events-wirecast-quickstart/add-live-event.png)<br/>
1. Insira um nome para o seu novo evento, como *TestLiveEvent* no campo **Nome** do evento ao vivo.<br/>
![Campo de texto de nome de evento ao vivo](media/live-events-wirecast-quickstart/live-event-name.png)<br/>
1. Introduza uma descrição opcional do evento no campo **Descrição.**
1. Selecione o **Pass-through – sem** botão de rádio codificador em nuvem.<br/>
![Botão de rádio codificação de nuvem](media/live-events-wirecast-quickstart/cloud-encoding.png)
1. Selecione o botão de rádio **RTMP.**
1. Certifique-se de que o botão de rádio **Não** é selecionado para o evento Start live, para evitar que seja faturado para o evento ao vivo antes de estar pronto.  (A faturação começará assim que o evento ao vivo for iniciado.) ![Comece o botão de rádio do evento ao vivo](media/live-events-wirecast-quickstart/start-live-event-no.png)<br/>
1. Clique no **Rever + criar** botão para rever as definições.
1. Clique no botão **Criar** o evento ao vivo. Em seguida, será devolvido à vista de listagem de eventos ao vivo.
1. Clique no **link para o evento ao vivo** que acabou de criar. Reparem que o seu evento está parado.
1. Mantenha esta página aberta no seu navegador.  Voltaremos mais tarde.

## <a name="setting-up-a-live-stream-with-wirecast-studio"></a>Configuração de um live stream com Wirecast Studio

1. Assumindo que ainda tem a aplicação Wirecast aberta, selecione **Create Empty Document** a partir do menu principal e, em seguida, clique em **Continuar**.
![Tela de início de arame](media/live-events-wirecast-quickstart/open-empty-document.png)
1. Pairar sobre a primeira camada na área das camadas wirecast.  Clique no ícone **Adicionar** que aparece e selecione a entrada de vídeo que pretende transmitir.  A caixa de diálogo Master Layer 1 abrirá.<br/>
![Ícone de adicionar arame](media/live-events-wirecast-quickstart/add-icon.png)
1. Selecione **a captura** de vídeo a partir do menu e, em seguida, selecione a câmara que pretende utilizar. Se selecionar uma câmara, a vista da câmara aparecerá na área de Pré-visualização.
![Tela de seleção de tiro de vídeo wirecast](media/live-events-wirecast-quickstart/video-shot-selection.png)
1. Pairar sobre a segunda camada na área das camadas wirecast. Clique no ícone **Adicionar** que aparece e selecione a entrada de áudio que pretende transmitir.  A caixa de diálogo Master Layer 2 abrirá.
1. Selecione **a captura** de áudio do menu e, em seguida, selecione a entrada de áudio que pretende utilizar.
![Tela de seleção de tiro áudio wirecast](media/live-events-wirecast-quickstart/audio-shot-select.png)
1. A partir do menu principal, selecione **as definições de saída**.  Aparecerá a caixa de diálogo Output.
1. Selecione **Azure Media Services** a partir da queda de saída.  A definição de saída para o Azure Media Services irá povoar automaticamente a *maioria* das definições de saída.<br/>
![Ecrã de definições de saída de difusão de arame](media/live-events-wirecast-quickstart/azure-media-services.png)
1. Na secção seguinte, você vai voltar para o Azure Media Services no seu navegador para copiar o URL de *entrada* para introduzir nas definições de saída.

### <a name="copy-and-paste-the-input-url"></a>Copiar e colar o URL de entrada

1. De volta à página do Azure Media Services do portal, clique **em Iniciar** o evento live stream. (A faturação começa agora.)<br/>
![Ícone de início](media/live-events-wirecast-quickstart/start.png)
2. Clique no alternância **'Secure/Not secure'** para o definir para **Não proteger**.  Isto definirá o protocolo à RTMP em vez de RTMPS.
3. Copie o URL de **entrada** para a sua área de redação.
![URL de entrada](media/live-events-wirecast-quickstart/input-url.png)
4. Mude para a aplicação Wirecast e colá o URL de **entrada** para o campo **'Endereço'** nas definições de saída.<br/>
![URL de entrada de difusão de arame](media/live-events-wirecast-quickstart/input-url-wirecast.png)
5. Clique **bem**.

## <a name="setting-up-outputs"></a>Configuração de saídas

Esta peça irá configurar as suas saídas e permitir-lhe-á guardar uma gravação do seu live stream.  

> [!NOTE]
> Para transmitir esta saída, o ponto final de streaming deve estar em funcionamento.  Consulte Executar a secção de ponto final de streaming padrão abaixo.

1. Clique no link **Criar saídas** abaixo do visualizador de vídeo Outputs.
1. Se quiser, edite o nome da saída no campo **Nome** para algo mais fácil de encontrar mais tarde.
![Campo de nome de saída](media/live-events-wirecast-quickstart/output-name.png)
1. Deixe todo o resto dos campos em paz por enquanto.
1. Clique em **Seguinte** adicionar localizador de streaming.
1. Mude o nome do localizador para algo mais fácil de utilizar, se quiser.
![Campo de nome do localizador](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Deixe tudo o resto neste ecrã sozinho por enquanto.
1. Clique em **Criar**.

## <a name="starting-the-broadcast"></a>Iniciar a transmissão

1. Na Wirecast, selecione **Output > Start / Stop a transmitir > Start Azure Media Services : Azure Media Services** do menu principal.  Quando o stream for enviado para o evento ao vivo, a janela ao vivo em Wirecast será exibida no leitor de vídeo sonoro na página de eventos ao vivo no Azure Media Services.

   ![Comece a transmitir itens de menu](media/live-events-wirecast-quickstart/start-broadcast.png)

1. Clique no botão **Go** sob a janela de pré-visualização para começar a transmitir o vídeo e áudio selecionado para as camadas wirecast.

   ![Botão wirecast Go](media/live-events-wirecast-quickstart/go-button.png)

   > [!TIP]
   > Se houver um erro, tente recarregar o leitor clicando no link do leitor de Recarga acima do leitor.

## <a name="running-the-default-streaming-endpoint"></a>Execução do ponto final de streaming padrão

1. Certifique-se de que o seu ponto final de streaming está a funcionar selecionando **pontos finais de Streaming** na listagem de Serviços de Media. Será levado para a página de pontos finais de streaming.<br/>
![Item de menu final de ponto final de streaming](media/live-events-wirecast-quickstart/streaming-endpoints.png)
1. Se o estado de ponto final de transmissão padrão for interrompido, clique no ponto final de transmissão **predefinido.** Isto vai levá-lo à página para esse ponto final.
1. Clique em **Iniciar**.  Isto vai começar o ponto final de streaming.<br/>
![Item de menu final de ponto final de streaming](media/live-events-wirecast-quickstart/start.png)

## <a name="play-the-output-broadcast-with-azure-media-player"></a>Reproduzir a transmissão da saída com o Azure Media Player

1. Copie o **URL de streaming** sob o leitor de vídeo output.
1. Em um navegador web, abra a demo Azure Media Playerhttps://ampdemo.azureedge.net/azuremediaplayer.html
1. Colhe o URL de **streaming** no campo URL do Azure Media Player.
1. Clique no botão **'Atualizar O Leitor'.**
1. Clique no ícone de **reprodução** no vídeo para ver o seu live stream.

## <a name="stopping-the-broadcast"></a>Parar a transmissão

Quando pensar que já transmitiu conteúdo suficiente, pare a transmissão.

1. Na Wirecast, clique no botão **de transmissão.**  Isto impedirá a transmissão da Wirecast.
1. No portal, clique em **Stop**. Receberá uma mensagem de aviso de que o live stream para, mas a saída passará a ser um ativo a pedido.
1. Clique em **Stop** na mensagem de aviso. O Azure Media Player também irá agora mostrar um erro, uma vez que o live stream já não está disponível.

## <a name="play-the-on-demand-output-with-the-azure-media-player"></a>Jogue a saída a pedido com o Azure Media Player

A saída que criou está agora disponível para streaming a pedido, desde que o seu ponto final de streaming esteja em funcionamento.

1. Navegue para a listagem de Serviços de Media e selecione **Ativos.**
1. Encontre a saída do evento que criou anteriormente e clique no **link para o ativo**. A página de saída do ativo será aberta.
1. Copie o **URL de streaming** sob o leitor de vídeo para o ativo.
1. Volte ao Azure Media Player no navegador e colhe o **URL de streaming** no campo URL do Azure Media Player.
1. Clique em **Atualizar Player**.
1. Clique no ícone de **reprodução** no vídeo para ver o ativo on-demand.

## <a name="clean-up-resources"></a>Limpar recursos

> [!IMPORTANT]
> Parem os serviços! Depois de ter concluído os passos neste arranque rápido, certifique-se de parar o evento ao vivo e o ponto final de streaming ou continuará a ser faturado pelo tempo em que eles permanecem em execução. Para parar o evento ao vivo, consulte a paragem da transmissão, passos 2 e 3 acima.

### <a name="stopping-the-streaming-endpoint"></a>Parar o ponto final de streaming

1. A partir da listagem de Serviços de Media, selecione **pontos finais de Streaming**.
2. Clique no ponto final de streaming **padrão** que começou mais cedo. Isto abrirá a página do ponto final.
3. Clique em **Stop**.  Isto irá parar o ponto final de streaming.

> [!TIP]
> Se não quiser manter os ativos deste evento, certifique-se de eliminá-los de forma a evitar que sejam cobrados para armazenamento.

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Que artigo é o próximo em sequência](./live-events-outputs-concept.md)
