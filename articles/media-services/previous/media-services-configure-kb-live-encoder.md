---
title: Configure o codificador Haivision KB para enviar um único stream bitrate ao vivo para Azure [ Microsoft Docs
description: Este tópico mostra como configurar o codificador ao vivo Haivision KB para enviar um único fluxo bitrate para canais AMS que estão habilitados para codificação ao vivo.
services: media-services
documentationcenter: ''
author: dbgeorge
manager: vsood
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: babb5a10818c8108bae34402962fd2503dbecbdc
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641688"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Use o codificador ao vivo Haivision KB para enviar um único fluxo ao vivo bitrate  
> [!div class="op_single_selector"]
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

Este tópico mostra como configurar o [codificador ao vivo Havision KB](https://www.haivision.com/products/kb-series/) para enviar um único fluxo bitrate para canais AMS que estão habilitados para codificação ao vivo. Para obter mais informações, consulte [Trabalhar com Canais Ativados para Realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial mostra como gerir o Azure Media Services (AMS) com a ferramenta Azure Media Services Explorer (AMSE). Esta ferramenta funciona apenas no Windows PC. Se estiver no Mac ou Linux, utilize o portal Azure para criar [canais](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programas.](media-services-portal-creating-live-encoder-enabled-channel.md)

## <a name="prerequisites"></a>Pré-requisitos
*   Acesso a um codificador Haivision KB, executando SW v5.01, ou maior.
* [Criar uma conta Azure Media Services](media-services-portal-create-account.md)
* Certifique-se de que existe um ponto final de streaming a funcionar. Para mais informações, consulte [Gerir pontos finais de streaming numa conta de Serviços de Media](media-services-portal-manage-streaming-endpoints.md)
* Instale a versão mais recente da ferramenta [AMSE.](https://github.com/Azure/Azure-Media-Services-Explorer)
* Lance a ferramenta e ligue-se à sua conta AMS.

## <a name="tips"></a>Sugestões
* Sempre que possível, utilize uma ligação à Internet por fios.
* Uma boa regra ao determinar os requisitos de largura de banda é duplicar as velocidades de transmissão. Embora este não seja um requisito obrigatório, ajuda a mitigar o impacto do congestionamento da rede.
* Ao utilizar codificadores baseados em software, encerre quaisquer programas desnecessários.

## <a name="create-a-channel"></a>Criar um canal
1. Na ferramenta AMSE, navegue para o separador **Live** e clique à direita na área do canal. Selecione **Criar canal...** do menu.
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Especifique um nome de canal, o campo de descrição é opcional. Em definições do Canal, selecione **Standard** para a opção Live Encoding, com o Protocolo de Entrada definido para **RTMP**. Pode deixar todas as outras definições como está. Certifique-se de que **o arranque do novo canal** está agora selecionado.
3. Clique em **Criar Canal**.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> O canal pode demorar até 20 minutos para começar.

## <a name="configure-the-haivision-kb-encoder"></a>Configure o codificador Haivision KB
Neste tutorial, são utilizadas as seguintes definições de saída. O resto desta secção descreve os passos de configuração com mais detalhes.

Vídeo:
-   Codec: H.264
-   Perfil: alto (Nível 4.0)
-   Bitrate: 5000 kbps
-   Quadro-chave: 2 segundos (60 fotogramas)
-   Taxa de fotogramas: 30

Áudio:
-   Codec: AAC (LC)
-   Velocidade de transmissão: 192 kbps
-   Taxa de amostra: 44,1 kHz

## <a name="configuration-steps"></a>Passos de configuração
1.  Inicie sessão na interface de utilizador Haivision KB.
2.  Clique no **botão** menu no centro de controlo do canal e selecione **Adicionar Canal**  
    ![Screenshot 2017-08-14 às 9.15.09](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Digite o Nome do **Canal** no campo Nome e clique em seguida.  
    ![Screenshot 2017-08-14 às 9.19.07](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Selecione a Fonte de Entrada do **Canal** a partir da gota de origem de **entrada** e clique em seguida.
    ![Screenshot 2017-08-14 às 9.20.44](./media/media-services-configure-kb-live-encoder/step4.png)
5.  A partir do **modelo de codificador,** escolha **H264-720-AAC-192** e clique em seguida.
    ![Screenshot 2017-08-14 às 9.23.15](./media/media-services-configure-kb-live-encoder/step5.png)
6.  A partir do **drop-down Select New Output** escolha **RTMP** e clique em seguida.  
    ![Screenshot 2017-08-14 às 9.27.51](./media/media-services-configure-kb-live-encoder/step6.png)
7.  A partir da janela **channel output,** povoe a informação de fluxo Azure. Colar a ligação **RTMP** a partir da configuração inicial do canal na área **do Servidor.** No tipo de nome de **saída** em nome do canal. Na área do modelo de nome stream, use o modelo RTMPStreamName_%video_bitrate% para nomear o fluxo.
    ![Screenshot 2017-08-14 às 9.33.17](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Clique em seguida e clique em Done.
9.  Clique no **botão reproduzir** para iniciar o canal codificador.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Reprodução de teste
Navegue para a ferramenta AMSE e clique no canal para ser testado. A partir do menu, passe por Cima da Reprodução a Pré-Visualização e selecione com O Leitor de Mídia Azure.

Se o fluxo aparecer no leitor, o codificador foi corretamente configurado para ligar à AMS.

Se for recebido um erro, o canal tem de ser reiniciado e ajustar as definições do codificador. Consulte o artigo de resolução de problemas para orientação.

## <a name="create-a-program"></a>Criar um programa
1.  Uma vez confirmada a reprodução do canal, crie um programa. Sob o separador Live na ferramenta AMSE, clique à direita dentro da área do programa e selecione Criar Novo Programa.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Nomeie o programa e, se necessário, ajuste o Comprimento da Janela de Arquivo (que se incorre para quatro horas). Também pode especificar um local de armazenamento ou sair como padrão.
2.  Verifique a caixa iniciar o programa agora.
3.  Clique no Programa Criar.
4.  Uma vez que o programa esteja em execução, confirme a reprodução clicando no programa e navegando para reproduzir o programa ou o programa e, em seguida, selecione com O Leitor de Mídia Azure.
5.  Uma vez confirmado, clique novamente no programa e selecione Copiar o URL de Saída para Clipboard (ou recuperar esta informação a partir da informação do Programa e a opção de definições do menu).

O stream está agora pronto para ser incorporado num jogador, ou distribuído para um público para visualização ao vivo.

> [!NOTE]
> A criação de programas leva menos tempo do que a criação de canais.
