---
title: Configurar o codificador Haivision KB para enviar um fluxo em direto com velocidade de transmissão única para o Azure | Documentos da Microsoft
description: Este tópico mostra como configurar o codificador em direto de Haivision KB para enviar um fluxo de velocidade de transmissão única para canais de AMS ativados para live encoding.
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
ms.author: juliako;dbgeorge
ms.openlocfilehash: 058a1f964eb14d89628c92cbadd80511b7a27bae
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58014945"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Utilizar o codificador em direto Haivision KB para enviar um fluxo em direto com velocidade de transmissão única  
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

Este tópico mostra como configurar o [codificador em direto de Havision KB](https://www.haivision.com/products/kb-series/) codificador para enviar um fluxo de velocidade de transmissão única para o AMS canais que estão ativados para live encoding. Para obter mais informações, consulte [Trabalhar com Canais Ativados para Realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial mostra como gerir o Azure Media Services (AMS) com a ferramenta do Explorador de serviços de multimédia do Azure (AMSE). Essa ferramenta só é executado no PC do Windows. Se estiver no Mac ou Linux, utilize o portal do Azure para criar [canais](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programas](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Pré-requisitos
*   Acesso a um codificador Haivision KB, executar SW v5.01, ou superior.
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
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Especifique um nome de canal, o campo de descrição é opcional. Em definições do canal, selecione **padrão** para a opção Live Encoding, com o protocolo de entrada definido como **RTMP**. Pode deixar todas as outras definições conforme está. Certifique-se de que o **iniciar agora o novo canal** está selecionada.
3. Clique em **criar canal**.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> O canal pode demorar até 20 minutos a iniciar.

## <a name="configure-the-haivision-kb-encoder"></a>Configurar o codificador Haivision KB
Neste tutorial, são utilizadas as seguintes definições de saída. O resto desta secção descreve os passos de configuração mais detalhadamente.

Vídeo:
-   Codec: H.264
-   Perfil: Alto (nível 4.0)
-   Velocidade de transmissão: 5000 kbps
-   Quadro-chave: 2 segundos (60 quadros)
-   Taxa de quadros: 30

Áudio:
-   Codec: AAC (LC)
-   Velocidade de transmissão: 192 kbps
-   Taxa da amostragem: 44.1 kHz

## <a name="configuration-steps"></a>Passos de configuração
1.  Inicie sessão para a interface do usuário Haivision KB.
2.  Clique nas **botão de Menu** no Centro de controlo de canal e selecione **adicionar canal**  
    ![Captura de ecrã 2017-08-14 em 9.15.09 AM](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Tipo de **nome do canal** no nome do campo e clique em seguinte.  
    ![Captura de ecrã 2017-08-14 em 9.19.07 AM](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Selecione o **origem de entrada de canal** partir a **origem de entrada** pendente e clique em seguinte.
    ![Captura de ecrã 2017-08-14 em 9.20.44 AM](./media/media-services-configure-kb-live-encoder/step4.png)
5.  Do **modelo de codificador** escolha pendente **H264-720-AAC-192** e clique em seguinte.
    ![Captura de ecrã 2017-08-14 em 9.23.15 AM](./media/media-services-configure-kb-live-encoder/step5.png)
6.  Do **selecione nova saída** escolha pendente **RTMP** e clique em seguinte.  
    ![Captura de ecrã 2017-08-14 em 9.27.51 AM](./media/media-services-configure-kb-live-encoder/step6.png)
7.  Do **saída de canal** janela, preencher as informações de fluxo do Azure. Colar o **RTMP** ligação da configuração do canal inicial no **servidor** área. Na **nome de saída** área escreva. o nome do canal. Na área de modelo de nome do Stream, utilize o modelo RTMPStreamName_ % video_bitrate % para nomear o fluxo.
    ![Captura de ecrã 2017-08-14 em 9.33.17 AM](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Clique em next e, em seguida, clique em concluído.
9.  Clique nas **botão Reproduzir** para iniciar o canal de codificador.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Reprodução de teste
Navegue para a ferramenta AMSE e o canal a ser testado com o botão direito. No menu, coloque o cursor sobre a reprodução a pré-visualização e selecione com o leitor de multimédia do Azure.

Se o fluxo aparece no player de, em seguida, o codificador foi corretamente configurado para ligar para o AMS.

Se for recebido um erro, o canal tem de ser reposto e definições de codificador ajustado. Consulte o artigo de resolução de problemas para obter orientações.

## <a name="create-a-program"></a>Criar um programa
1.  Assim que a reprodução de canal é confirmada, crie um programa. Na guia em direto na ferramenta AMSE, faça duplo clique dentro da área de programa e selecione Criar novo programa.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Nomeie o programa e, se necessário, ajuste o tamanho de janela de arquivo (que assume a quatro horas). Também pode especificar uma localização de armazenamento ou deixe como a predefinição.
2.  Caixa de verificação no início da programa agora.
3.  Clique em criar programa.
4.  Quando o programa estiver em execução, confirme a reprodução clicando com o programa e navegar para reproduzir os programas e, em seguida, selecione com o leitor de multimédia do Azure.
5.  Depois de confirmar, faça duplo clique o programa novamente e selecione copiar o URL de saída para a área de transferência (ou recuperar essas informações da opção de definições do menu e informações sobre o programa).

O fluxo está agora pronto para ser incorporados num leitor ou distribuído para um público-alvo para visualização em direto.

> [!NOTE]
> Criação de programa demora menos tempo do que a criação de canal.
