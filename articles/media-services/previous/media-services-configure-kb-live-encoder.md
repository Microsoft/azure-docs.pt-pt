---
title: Configurar o codificador HaiVision KB para enviar uma transmissão ao vivo de taxa de bits única para o Azure | Microsoft Docs
description: Este tópico mostra como configurar o codificador ativo do HaiVision KB para enviar um fluxo de taxa de bits única para os canais do AMS que estão habilitados para codificação ativa.
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
ms.openlocfilehash: 7bb3db4861842e145689682035adc3c691538adf
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297805"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Usar o codificador ativo do HaiVision KB para enviar uma transmissão ao vivo de taxa de bits única  
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [HaiVision](media-services-configure-kb-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

Este tópico mostra como configurar o codificador de [codificador dinâmico do HAVISION KB](https://www.haivision.com/products/kb-series/) para enviar um fluxo de taxa de bits única para os canais do AMS que estão habilitados para codificação ativa. Para obter mais informações, consulte [Trabalhar com Canais Ativados para Realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial mostra como gerir o Azure Media Services (AMS) com a ferramenta do Explorador de serviços de multimédia do Azure (AMSE). Essa ferramenta só é executado no PC do Windows. Se estiver no Mac ou Linux, utilize o portal do Azure para criar [canais](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programas](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Pré-requisitos
*   Acesso a um codificador do HaiVision KB, executando o SW v 5.01 ou superior.
* [Criar uma conta de Media Services do Azure](media-services-portal-create-account.md)
* Certifique-se de que existe um ponto de final de transmissão em fluxo em execução. Para obter mais informações, consulte [gerir transmissão em fluxo pontos finais na conta de Media Services](media-services-portal-manage-streaming-endpoints.md)
* Instale a versão mais recente dos [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) ferramenta.
* Inicie a ferramenta e ligue à sua conta de AMS.

## <a name="tips"></a>Sugestões
* Sempre que possível, utilize uma ligação de internet conectada.
* Uma boa regra prática ao determinar os requisitos de largura de banda é duas vezes as velocidades de transmissão de transmissão em fluxo. Embora não seja um requisito obrigatório, ele ajuda a mitigar o impacto de congestionamento de rede.
* Quando utilizar codificadores baseada em software, feche todos os programas desnecessários.

## <a name="create-a-channel"></a>Criar um canal
1. Na ferramenta AMSE, navegue para o **Live** separador e, com o botão direito dentro da área de canal. Selecione **criar canal...** no menu.
[HaiVision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Especifique um nome de canal, o campo de descrição é opcional. Em definições do canal, selecione **padrão** para a opção Live Encoding, com o protocolo de entrada definido como **RTMP**. Pode deixar todas as outras definições conforme está. Certifique-se de que o **iniciar agora o novo canal** está selecionada.
3. Clique em **criar canal**.
[HaiVision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> O canal pode demorar até 20 minutos a iniciar.

## <a name="configure-the-haivision-kb-encoder"></a>Configurar o codificador do HaiVision KB
Neste tutorial, são utilizadas as seguintes definições de saída. O resto desta secção descreve os passos de configuração mais detalhadamente.

Vídeo:
-   Codec H. 264
-   Criar Alta (nível 4,0)
-   720p 5000 kbps
-   Keyframe 2 segundos (60 quadros)
-   Taxa de quadros: 30

Sonoro
-   Codec AAC (LC)
-   720p 192 kbps
-   Taxa de amostra: 44,1 kHz

## <a name="configuration-steps"></a>Passos de configuração
1.  Faça logon na interface do usuário do HaiVision KB.
2.  Clique no **botão de menu** no centro de controle do canal e selecione **Adicionar canal**  
    ![Captura de tela 2017-08-14 às 9.15.09](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Digite o **nome do canal** no campo nome e clique em Avançar.  
    ![Captura de tela 2017-08-14 às 9.19.07](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Selecione a **fonte de entrada do canal** na lista suspensa **fonte de entrada** e clique em Avançar.
    ![Captura de tela 2017-08-14 às 9.20.44](./media/media-services-configure-kb-live-encoder/step4.png)
5.  Na lista suspensa **modelo** do codificador, escolha **H264-720-AAC-192** e clique em Avançar.
    ![Captura de tela 2017-08-14 às 9.23.15](./media/media-services-configure-kb-live-encoder/step5.png)
6.  Na lista suspensa **selecionar nova saída** , escolha **RTMP** e clique em Avançar.  
    ![Captura de tela 2017-08-14 às 9.27.51](./media/media-services-configure-kb-live-encoder/step6.png)
7.  Na janela **saída do canal** , preencha as informações de fluxo do Azure. Cole o link **RTMP** da configuração do canal inicial na área do **servidor** . Na área **nome de saída** , digite o nome do canal. Na área modelo de nome de fluxo, use o modelo RTMPStreamName_% video_bitrate% para nomear o fluxo.
    ![Captura de tela 2017-08-14 às 9.33.17](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Clique em avançar e em concluído.
9.  Clique no **botão reproduzir** para iniciar o canal do codificador.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Reprodução de teste
Navegue para a ferramenta AMSE e o canal a ser testado com o botão direito. No menu, passe o mouse sobre reproduzir a visualização e selecione com Player de Mídia do Azure.

Se o fluxo aparece no player de, em seguida, o codificador foi corretamente configurado para ligar para o AMS.

Se for recebido um erro, o canal tem de ser reposto e definições de codificador ajustado. Consulte o artigo de solução de problemas para obter diretrizes.

## <a name="create-a-program"></a>Criar um programa
1.  Assim que a reprodução de canal é confirmada, crie um programa. Na guia Live da ferramenta AMSE, clique com o botão direito do mouse na área do programa e selecione criar novo programa.
[HaiVision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Nomeie o programa e, se necessário, ajuste o tamanho da janela de arquivo (cujo padrão é quatro horas). Também pode especificar uma localização de armazenamento ou deixe como a predefinição.
2.  Marque a caixa Iniciar o programa agora.
3.  Clique em criar programa.
4.  Depois que o programa estiver em execução, confirme a reprodução clicando com o botão direito do mouse no programa e navegando para reproduzir os programas e, em seguida, selecionando com Player de Mídia do Azure.
5.  Depois de confirmar, clique com o botão direito do mouse no programa novamente e selecione copiar a URL de saída para a área de transferência (ou recuperar essas informações na opção informações e configurações do programa do menu).

O fluxo está agora pronto para ser incorporados num leitor ou distribuído para um público-alvo para visualização em direto.

> [!NOTE]
> Criação de programa demora menos tempo do que a criação de canal.
