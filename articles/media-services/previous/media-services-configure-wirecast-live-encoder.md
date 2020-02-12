---
title: Configurar o codificador Telestream Wirecast para enviar um fluxo em direto com velocidade de transmissão única | Documentos da Microsoft
description: 'Este tópico mostra como configurar o codificador de Wirecast em direto para enviar um fluxo de velocidade de transmissão única para canais de AMS ativados para live encoding. '
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
ms.openlocfilehash: 1d9d63aa6b3da1b8d8389722bd5af0eeed585d03
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134965"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Utilizar o codificador de Wirecast para enviar um fluxo em direto com velocidade de transmissão única 
> [!div class="op_single_selector"]
> * [Arame](media-services-configure-wirecast-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
>
>

Este artigo mostra como configurar o codificador ao vivo [telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) para enviar um único fluxo bitrate para canais AMS que estão habilitados para codificação ao vivo. Para obter mais informações, consulte [Trabalhar com Canais Ativados para Realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial mostra como gerir o Azure Media Services (AMS) com a ferramenta do Explorador de serviços de multimédia do Azure (AMSE). Essa ferramenta só é executado no PC do Windows. Se estiver no Mac ou Linux, utilize o portal Azure para criar [canais](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programas.](media-services-portal-creating-live-encoder-enabled-channel.md)

> [!NOTE]
> Os codificadores devem suportar o TLS 1.2 ao utilizarem protocolos RTMPS. Utilize a versão Wirecast 13.0.2 ou superior devido ao requisito TLS 1.2.

## <a name="prerequisites"></a>Pré-requisitos
* [Criar uma conta Azure Media Services](media-services-portal-create-account.md)
* Certifique-se de que existe um ponto de final de transmissão em fluxo em execução. Para mais informações, consulte [Gerir pontos finais de streaming numa conta de Serviços de Media](media-services-portal-manage-streaming-endpoints.md)
* Instale a versão mais recente da ferramenta [AMSE.](https://github.com/Azure/Azure-Media-Services-Explorer)
* Inicie a ferramenta e ligue à sua conta de AMS.

## <a name="tips"></a>Sugestões
* Sempre que possível, utilize uma ligação de internet conectada.
* Uma boa regra prática ao determinar os requisitos de largura de banda é duas vezes as velocidades de transmissão de transmissão em fluxo. Embora não seja um requisito obrigatório, ele ajuda a mitigar o impacto de congestionamento de rede.
* Quando utilizar codificadores baseada em software, feche todos os programas desnecessários.

## <a name="create-a-channel"></a>Criar um canal
1. Na ferramenta AMSE, navegue para o separador **Live** e clique à direita na área do canal. Selecione **Criar canal...** no menu.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Especifique um nome de canal, o campo de descrição é opcional. Em definições do Canal, selecione **Standard** para a opção Live Encoding, com o Protocolo de Entrada definido para **RTMP**. Pode deixar todas as outras definições conforme está.

    Certifique-se de que **o arranque do novo canal** está agora selecionado.

3. Clique em **Criar Canal**.

   ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> O canal pode demorar até 20 minutos a iniciar.
>
>

Enquanto o canal está a começar, pode [configurar o codificador](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> A faturação tem início assim que o canal entra num Estado de pronto. Para mais informações, consulte os [estados do Canal.](media-services-manage-live-encoder-enabled-channels.md#states)
>
>

## <a name="a-idconfigure_wirecast_rtmp-configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />Configurar o codificador telestream wirecast
Neste tutorial, são utilizadas as seguintes definições de saída. O resto desta secção descreve os passos de configuração mais detalhadamente.

**Vídeo**:

* Codec: H.264
* Perfil: Alta (nível 4.0)
* Velocidade de transmissão: kbps de 5000
* Quadro-chave: 2 segundos (60 segundos)
* Taxa de fotograma: 30

**Áudio:**

* Codec: AAC (LC)
* Velocidade de transmissão: kbps de 192
* Taxa da amostragem: kHz 44.1

### <a name="configuration-steps"></a>Passos de configuração
1. Abra a aplicação de Telestream Wirecast sobre o computador que está a ser utilizado e definida para RTMP de transmissão em fluxo.
2. Configure a saída navegando para o separador **Saída** e selecione Definições de **saída...** .

    Certifique-se de que o **Destino de Saída** está definido para o Servidor **RTMP**.
3. Clique em **OK**.
4. Na página de definições, delineie o campo **Destino** para ser **o Azure Media Services**.

    O perfil de codificação é pré-selecionado para **Azure H.264 720p 16:9 (1280x720)** . Para personalizar estas definições, selecione o ícone de engrenagem à direita da queda e, em seguida, escolha **new preset**.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Configure predefinições de codificador.

    Nomeie a configuração predefinida e verificar as seguintes definições recomendadas:

    **Vídeo**

   * Codificador: MainConcept H.264
   * Quadros por segundo: 30
   * Taxa de bits média: 5000 kbits/seg (pode ser ajustado com base no limitações de rede)
   * Perfil: principal
   * Quadro-chave cada: 60 quadros

     **Áudio**

   * Taxa de bits de destino: 192 kbits/seg
   * Taxa da amostragem: kHz 44.100

     ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Prima **Guardar**.

    O campo de codificação tem agora o perfil criado recentemente estão disponível para seleção.

    Certifique-se de que o novo perfil está selecionado.
7. Obtenha o URL de entrada do canal para atribuí-lo ao ponto final da **REDI**.

    Navegue de volta para a ferramenta AMSE e verificar o estado de conclusão do canal. Uma vez que o Estado tenha mudado de **Início** para **Execução,** pode obter o URL de entrada.

    Quando o canal estiver em execução, clique no nome do canal, navegue para baixo para pairar sobre o URL de entrada de cópia para a área de **reposição** e, em seguida, selecione URL de **entrada primária**.  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. Na janela **definições** de saída de escutas, cola esta informação no campo **'Endereço'** da secção de saída e atribua um nome de fluxo.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Selecione **OK**.
2. No ecrã principal da **Wirecast,** confirme que as fontes de entrada para vídeo e áudio estão prontas e depois acerta em **Stream** no canto superior esquerdo.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Antes de clicar em **Stream,** **certifique-se** de que o Canal está pronto.
> Além disso, certifique-se de que não o canal no estado pronto sem uma contribuição entrada feed durante mais de > 15 minutos.
>
>

## <a name="test-playback"></a>Reprodução de teste

Navegue para a ferramenta AMSE e o canal a ser testado com o botão direito. A partir do menu, passe por cima da **Reprodução da Pré-visualização** e selecione com O Leitor de **Mídia Azure**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Se o fluxo aparece no player de, em seguida, o codificador foi corretamente configurado para ligar para o AMS.

Se for recebido um erro, o canal tem de ser reposto e definições de codificador ajustado. Consulte o artigo de resolução de [problemas](media-services-troubleshooting-live-streaming.md) para orientação.  

## <a name="create-a-program"></a>Criar um programa
1. Assim que a reprodução de canal é confirmada, crie um programa. Sob o separador **Live** na ferramenta AMSE, clique à direita dentro da área do programa e selecione **Criar Novo Programa**.  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
2. Nomeie o programa e, se necessário, ajuste o Comprimento da Janela de **Arquivo** (que se incorre para quatro horas). Também pode especificar uma localização de armazenamento ou deixe como a predefinição.  
3. Verifique a caixa **iniciar o programa agora.**
4. Clique em **Criar Programa**.  

   >[!NOTE]
   >Criação de programa demora menos tempo do que a criação de canal.
       
5. Uma vez que o programa esteja em execução, confirme a reprodução clicando no programa e navegando para **reproduzir o programa ou** o programa e, em seguida, selecione com **Azure Media Player**.  
6. Uma vez confirmado, clique novamente no programa e selecione Copiar o URL de **Saída para Clipboard** (ou recuperar esta informação a partir da **informação do Programa e** a opção de definições do menu).

O fluxo está agora pronto para ser incorporados num leitor ou distribuído para um público-alvo para visualização em direto.  

## <a name="troubleshooting"></a>Resolução de problemas
Consulte o artigo de resolução de [problemas](media-services-troubleshooting-live-streaming.md) para orientação.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
