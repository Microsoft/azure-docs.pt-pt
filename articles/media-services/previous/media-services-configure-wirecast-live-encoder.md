---
title: Configure o codificar Telestream Wirecast para enviar uma única transmissão em direto bitrate | Microsoft Docs
description: 'Este tópico mostra como configurar o codificador ao vivo da Wirecast para enviar um único fluxo de bitrate para canais AMS que estão habilitados para codificação ao vivo. '
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: a736270919f36cb7d4bbd4e686a98ddc57f402fa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016836"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Use o codificader Wirecast para enviar um único fluxo de bitrate ao vivo

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>

Este artigo mostra como configurar o codificador ao vivo da [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) para enviar um único fluxo de bitrate para canais AMS que estão habilitados para codificação ao vivo. Para obter mais informações, consulte [Trabalhar com Canais Ativados para Realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial mostra como gerir a Azure Media Services (AMS) com a ferramenta Azure Media Services Explorer (AMSE). Esta ferramenta funciona apenas no Windows PC. Se estiver no Mac ou Linux, utilize o portal Azure para criar [canais](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programas.](media-services-portal-creating-live-encoder-enabled-channel.md)

> [!NOTE]
> Os codificadores devem suportar O TLS 1.2 quando utilizarem protocolos RTMPS. Utilize a versão 13.0.2 ou superior devido ao requisito TLS 1.2.

## <a name="prerequisites"></a>Pré-requisitos
* [Criar uma conta Azure Media Services](media-services-portal-create-account.md)
* Certifique-se de que existe um Ponto Final de Streaming em funcionamento. Para obter mais informações, consulte [Gerir os pontos finais de streaming numa conta de serviços de mídia](media-services-portal-manage-streaming-endpoints.md)
* Instale a versão mais recente da ferramenta [AMSE.](https://github.com/Azure/Azure-Media-Services-Explorer)
* Lance a ferramenta e ligue-se à sua conta AMS.

## <a name="tips"></a>Sugestões
* Sempre que possível, utilize uma ligação à Internet por fios.
* Uma boa regra ao determinar os requisitos de largura de banda é duplicar as velocidades de transmissão. Embora este não seja um requisito obrigatório, ajuda a mitigar o impacto do congestionamento da rede.
* Ao utilizar codificadores baseados em software, feche quaisquer programas desnecessários.

## <a name="create-a-channel"></a>Criar um canal
1. Na ferramenta AMSE, navegue no separador **Live** e clique com o botão direito dentro da área do canal. Selecione **Criar canal...** do menu.

    ![Screenshot mostra criar canal selecionado a partir de um menu.](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Especifique um nome de canal, o campo de descrição é opcional. Em Definições de Canal, selecione **Standard** para a opção Live Encoding, com o Protocolo de Entrada definido para **RTMP**. Pode deixar todas as outras definições como está.

    Certifique-se de que **o novo canal** iniciar agora está selecionado.

3. Clique **em Criar Canal.**

   ![A screenshot mostra a caixa de diálogo de canal ao vivo Criar.](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> O canal pode demorar até 20 minutos para começar.
>
>

Enquanto o canal está a começar, pode [configurar o codificante](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> A faturação começa assim que o Canal entra em estado de preparação. Para mais informações, consulte [os estados do Canal.](media-services-manage-live-encoder-enabled-channels.md#states)
>
>

## <a name="configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />Configure o codificar Telestream Wirecast
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
1. Abra a aplicação Telestream Wirecast na máquina que está a ser utilizada e instale-se para o streaming rtmp.
2. Configure a saída navegando no separador **Saída** e selecionando Definições de **Saída...**.

    Certifique-se de que o **Destino de Saída** está definido para o Servidor **RTMP**.
3. Clique em **OK**.
4. Na página de definições, defina o campo **Destino** para ser **a Azure Media Services**.

    O perfil de Codificação é pré-selecionado para **Azure H.264 720p 16:9 (1280x720)**. Para personalizar estas definições, selecione o ícone de engrenagem à direita do drop-down e, em seguida, escolha **Nova Predefinição**.

    ![A screenshot mostra a caixa de diálogo do modelo escolher com o BlobTrigger selecionado.](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Configurar predefinições codificantes.

    Nomeie a predefinição e verifique as seguintes definições recomendadas:

    **Vídeo**

   * Codificação: MainConcept H.264
   * Quadros por segundo: 30
   * Taxa média de bits: 5000 kbits/seg (Pode ser ajustado com base em limitações de rede)
   * Perfil: Principal
   * Quadro chave a cada: 60 quadros

     **Áudio**

   * Taxa de bits-alvo: 192 kbits/seg
   * Taxa de amostra: 44.100 kHz

     ![A screenshot mostra o Predefinição encoder para AzureTest1.](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Prima **Guardar**.

    O campo de Codificação tem agora o perfil recém-criado disponível para seleção.

    Certifique-se de que o novo perfil está selecionado.
7. Obtenha o URL de entrada do canal para atribuí-lo ao Ponto **final RTMP** Wirecast .

    Volte para a ferramenta AMSE e verifique o estado de conclusão do canal. Uma vez que o Estado tenha mudado de **Início** para **Executar,** pode obter o URL de entrada.

    Quando o canal estiver em funcionamento, clique com o nome do canal à direita, navegue para baixo para pairar sobre **o URL de entrada de cópia para a área** de transferência e, em seguida, selecione O URL de entrada **primária**.  

    ![A screenshot mostra a opção copy input U R L para a opção de clipboard para a entrada primária U R L.](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. Na janela **Definições de saída** de corrente, cole estas informações no campo **Address** da secção de saída e atribua um nome de fluxo.

    ![A screenshot mostra definições de saída.](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Selecione **OK**.
2. No ecrã principal da **Wirecast,** confirme que as fontes de entrada para vídeo e áudio estão prontas e, em seguida, atinja **o Stream** no canto superior esquerdo.

    ![A imagem mostra o botão de fluxo de corrente de arame.](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Antes de clicar em **Stream,** **tem de** se certificar de que o Canal está pronto.
> Além disso, certifique-se de que não sai do Canal num estado pronto sem um feed de contribuição por mais de > 15 minutos.
>
>

## <a name="test-playback"></a>Reprodução de teste

Navegue para a ferramenta AMSE e clique com o botão direito no canal a testar. A partir do menu, passe sobre **a Reprodução da Pré-visualização** e selecione **com o Azure Media Player**.  

![Screenshot mostra reversão da pré-visualização com a opção Azure Media Player selecionada.](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Se o fluxo aparecer no leitor, então o codificante foi devidamente configurado para ligar à AMS.

Se for recebido um erro, o canal tem de ser reiniciado e as definições do codificadores ajustadas. Consulte o artigo [de resolução de problemas](media-services-troubleshooting-live-streaming.md) para obter orientação.  

## <a name="create-a-program"></a>Create a program (Criar um programa)
1. Assim que a reprodução do canal for confirmada, crie um programa. No separador **Live** na ferramenta AMSE, clique à direita na área do programa e selecione **Criar Novo Programa**.  

    ![A screenshot mostra a opção de programa Create selecionada.](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
2. Nomeie o programa e, se necessário, ajuste o comprimento da **janela do arquivo** (que predefini em quatro horas). Também pode especificar um local de armazenamento ou sair como padrão.  
3. Verifique a caixa **Iniciar o Programa agora.**
4. Clique **em Criar Programa.**  

   >[!NOTE]
   >A criação de programas leva menos tempo do que a criação de canais.
       
5. Uma vez que o programa esteja em execução, confirme a reprodução clicando à direita no programa e navegando para **reproduzir o programa(s)** e, em seguida, selecione **com o Azure Media Player**.  
6. Uma vez confirmado, clique com o botão direito no programa novamente e selecione **Copiar o URL de saída para a Área de Transferência** (ou recuperar esta informação a partir da opção de **informação e definições** do Programa a partir do menu).

O stream está agora pronto para ser incorporado num jogador, ou distribuído para um público para visualização ao vivo.  

## <a name="troubleshooting"></a>Resolução de problemas
Consulte o artigo [de resolução de problemas](media-services-troubleshooting-live-streaming.md) para obter orientação.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
