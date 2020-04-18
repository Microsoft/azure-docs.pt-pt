---
title: Configure o codificador telestream wirecast para enviar um único fluxo ao vivo bitrate [ Microsoft Docs
description: 'Este tópico mostra como configurar o codificador ao vivo da Wirecast para enviar um único fluxo bitrate para canais AMS que estão habilitados para codificação ao vivo. '
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: 8e3705aaecb0760513f0605aece89b7ffc0044a8
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641650"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Utilize o codificador de arame para enviar um único fluxo ao vivo bitrate 
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>

Este artigo mostra como configurar o codificador ao vivo [telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) para enviar um único fluxo bitrate para canais AMS que estão habilitados para codificação ao vivo. Para obter mais informações, consulte [Trabalhar com Canais Ativados para Realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial mostra como gerir o Azure Media Services (AMS) com a ferramenta Azure Media Services Explorer (AMSE). Esta ferramenta funciona apenas no Windows PC. Se estiver no Mac ou Linux, utilize o portal Azure para criar [canais](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programas.](media-services-portal-creating-live-encoder-enabled-channel.md)

> [!NOTE]
> Os codificadores devem suportar o TLS 1.2 ao utilizarem protocolos RTMPS. Utilize a versão Wirecast 13.0.2 ou superior devido ao requisito TLS 1.2.

## <a name="prerequisites"></a>Pré-requisitos
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

    ![aramecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Especifique um nome de canal, o campo de descrição é opcional. Em definições do Canal, selecione **Standard** para a opção Live Encoding, com o Protocolo de Entrada definido para **RTMP**. Pode deixar todas as outras definições como está.

    Certifique-se de que **o arranque do novo canal** está agora selecionado.

3. Clique em **Criar Canal**.

   ![aramecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> O canal pode demorar até 20 minutos para começar.
>
>

Enquanto o canal está a começar, pode [configurar o codificador](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> A faturação começa assim que o Canal entrar em estado de preparação. Para mais informações, consulte os [estados do Canal.](media-services-manage-live-encoder-enabled-channels.md#states)
>
>

## <a name="configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />Configure o codificador telestream wirecast
Neste tutorial, são utilizadas as seguintes definições de saída. O resto desta secção descreve os passos de configuração com mais detalhes.

**Vídeo**:

* Codec: H.264
* Perfil: alto (Nível 4.0)
* Bitrate: 5000 kbps
* Quadro-chave: 2 segundos (60 segundos)
* Taxa de fotogramas: 30

**Áudio:**

* Codec: AAC (LC)
* Velocidade de transmissão: 192 kbps
* Taxa de amostra: 44,1 kHz

### <a name="configuration-steps"></a>Passos de configuração
1. Abra a aplicação Telestream Wirecast na máquina em uso e configurar para o streaming RTMP.
2. Configure a saída navegando para o separador **Saída** e selecione Definições de **saída...**.

    Certifique-se de que o **Destino de Saída** está definido para o Servidor **RTMP**.
3. Clique em **OK**.
4. Na página de definições, delineie o campo **Destino** para ser **o Azure Media Services**.

    O perfil de codificação é pré-selecionado para **Azure H.264 720p 16:9 (1280x720)**. Para personalizar estas definições, selecione o ícone de engrenagem à direita da queda e, em seguida, escolha **new preset**.

    ![aramecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Configurar predefinições de codificador.

    Nomeie o predefinido e verifique se estão as seguintes definições recomendadas:

    **Vídeo**

   * Codificador: MainConcept H.264
   * Quadros por Segundo: 30
   * Taxa média de bit: 5000 kbits/seg (Pode ser ajustada com base nas limitações da rede)
   * Perfil: Principal
   * Quadro-chave cada: 60 quadros

     **Áudio**

   * Taxa de bit-alvo: 192 kbits/seg
   * Taxa de amostra: 44.100 kHz

     ![aramecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Prima **Guardar**.

    O campo Decodificação tem agora o perfil recém-criado disponível para seleção.

    Certifique-se de que o novo perfil está selecionado.
7. Obtenha o URL de entrada do canal para atribuí-lo ao ponto final da **REDI**.

    Navegue de volta para a ferramenta AMSE e verifique o estado de conclusão do canal. Uma vez que o Estado tenha mudado de **Início** para **Execução,** pode obter o URL de entrada.

    Quando o canal estiver em execução, clique no nome do canal, navegue para baixo para pairar sobre o URL de entrada de cópia para a área de **reposição** e, em seguida, selecione URL de **entrada primária**.  

    ![aramecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. Na janela **definições** de saída de escutas, cola esta informação no campo **'Endereço'** da secção de saída e atribua um nome de fluxo.

    ![aramecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Selecione **OK**.
2. No ecrã principal da **Wirecast,** confirme que as fontes de entrada para vídeo e áudio estão prontas e depois acerta em **Stream** no canto superior esquerdo.

    ![aramecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Antes de clicar em **Stream,** **certifique-se** de que o Canal está pronto.
> Além disso, certifique-se de não deixar o Canal em estado de preparação sem um feed de contribuição de entrada por mais de > 15 minutos.
>
>

## <a name="test-playback"></a>Reprodução de teste

Navegue para a ferramenta AMSE e clique no canal para ser testado. A partir do menu, passe por cima da **Reprodução da Pré-visualização** e selecione com O Leitor de **Mídia Azure**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Se o fluxo aparecer no leitor, o codificador foi corretamente configurado para ligar à AMS.

Se for recebido um erro, o canal tem de ser reiniciado e ajustar as definições do codificador. Consulte o artigo de resolução de [problemas](media-services-troubleshooting-live-streaming.md) para orientação.  

## <a name="create-a-program"></a>Criar um programa
1. Uma vez confirmada a reprodução do canal, crie um programa. Sob o separador **Live** na ferramenta AMSE, clique à direita dentro da área do programa e selecione **Criar Novo Programa**.  

    ![aramecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
2. Nomeie o programa e, se necessário, ajuste o Comprimento da Janela de **Arquivo** (que se incorre para quatro horas). Também pode especificar um local de armazenamento ou sair como padrão.  
3. Verifique a caixa **iniciar o programa agora.**
4. Clique em **Criar Programa**.  

   >[!NOTE]
   >A criação de programas leva menos tempo do que a criação de canais.
       
5. Uma vez que o programa esteja em execução, confirme a reprodução clicando no programa e navegando para **reproduzir o programa ou** o programa e, em seguida, selecione com **Azure Media Player**.  
6. Uma vez confirmado, clique novamente no programa e selecione Copiar o URL de **Saída para Clipboard** (ou recuperar esta informação a partir da **informação do Programa e** a opção de definições do menu).

O stream está agora pronto para ser incorporado num jogador, ou distribuído para um público para visualização ao vivo.  

## <a name="troubleshooting"></a>Resolução de problemas
Consulte o artigo de resolução de [problemas](media-services-troubleshooting-live-streaming.md) para orientação.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
