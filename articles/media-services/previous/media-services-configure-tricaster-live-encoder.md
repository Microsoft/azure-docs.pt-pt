---
title: Configure o codificador NewTek TriCaster para enviar um único stream bitrate ao vivo / Microsoft Docs
description: Este tópico mostra como configurar o codificador ao vivo tricaster para enviar um único fluxo bitrate para canais AMS que estão habilitados para codificação ao vivo.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: 8973181a-3059-471a-a6bb-ccda7d3ff297
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 7909fbb958a66d00616d4ed1b844d02bb47d997e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152504"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Use o codificador NewTek TriCaster para enviar um único fluxo ao vivo bitrate  
> [!div class="op_single_selector"]
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

Este artigo mostra como configurar o codificador ao vivo [NewTek TriCaster](https://newtek.com/products/tricaster-40.html) para enviar um único fluxo bitrate para canais AMS que estão habilitados para codificação ao vivo. Para obter mais informações, consulte [Trabalhar com Canais Ativados para Realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial mostra como gerir o Azure Media Services (AMS) com a ferramenta Azure Media Services Explorer (AMSE). Esta ferramenta funciona apenas no Windows PC. Se estiver no Mac ou Linux, utilize o portal Azure para criar [canais](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programas.](media-services-portal-creating-live-encoder-enabled-channel.md)

Ao utilizar o Tricaster para enviar um feed de contribuição para os canais AMS que estão habilitados para codificação ao vivo, pode haver falhas de vídeo/áudio no seu evento ao vivo se utilizar certas funcionalidades do Tricaster, tais como corte rápido entre feeds ou mudança de/para ardósias. A equipa da AMS está a trabalhar na resolução destes problemas, até lá, não é recomendado utilizar estas funcionalidades.

> [!NOTE]
>  Considere mudar-se para TLS 1.2, a versão Pré-Adiada TLS.

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

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Especifique um nome de canal, o campo de descrição é opcional. Em definições do Canal, selecione **Standard** para a opção Live Encoding, com o Protocolo de Entrada definido para **RTMP**. Pode deixar todas as outras definições como está.

    Certifique-se de que **o arranque do novo canal** está agora selecionado.

3. Clique em **Criar Canal**.

   ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> O canal pode demorar até 20 minutos para começar.
>
>

Enquanto o canal está a começar, pode [configurar o codificador](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> A faturação começa assim que o Canal entrar em estado de preparação. Para mais informações, consulte os [estados do Canal.](media-services-manage-live-encoder-enabled-channels.md#states)
>
>

## <a name="configure-the-newtek-tricaster-encoder"></a><a id="configure_tricaster_rtmp"/>Configure o codificador NewTek TriCaster

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

1. Crie um novo projeto **NewTek TriCaster** dependendo da fonte de entrada de vídeo que está a ser usada.
2. Uma vez dentro desse projeto, encontre o botão **Stream** e clique no ícone de engrenagem ao lado para aceder ao menu de configuração do fluxo.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Uma vez aberta a abertura do menu, clique em **Novo** sob a rubrica Ligação. Quando solicitado para o tipo de ligação, selecione **Adobe Flash**.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Clique em **OK**.
5. Um perfil FMLE pode agora ser importado clicando na seta de queda sob perfil de **streaming** e navegando para **Navegar**.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Navegue até onde o perfil de FMLE configurado foi guardado.
7. Selecione-o e prima **OK**.

    Assim que o perfil for carregado, proceda ao próximo passo.
8. Obtenha o URL de entrada do canal para atribuí-lo ao Ponto final do Tricaster **RTMP**.

    Navegue de volta para a ferramenta AMSE e verifique o estado de conclusão do canal. Uma vez que o Estado tenha mudado de **Início** para **Execução,** pode obter o URL de entrada.

    Quando o canal estiver em execução, clique no nome do canal, navegue para baixo para pairar sobre o URL de entrada de cópia para a área de **reposição** e, em seguida, selecione URL de **entrada primária**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Repasse esta informação no campo **De localização** no **Flash Server** dentro do projeto Tricaster. Atribua também um nome de fluxo no campo **de ID** stream.

    Se as informações de transmissão foram adicionadas ao perfil FMLE, também pode ser importada para esta secção clicando em Definições de **Importação,** navegando para o perfil DE FMLE guardado e clicando **OK**. Os campos flash server relevantes devem ser preenchidos com a informação da FMLE.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. Quando terminar, **clique** ok na parte inferior do ecrã. Quando as inserções de vídeo e áudio no Tricaster estiverem prontas, comece a transmitir para AMS clicando no botão **Stream.**

     ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Antes de clicar em **Stream,** **certifique-se** de que o Canal está pronto.
> Além disso, certifique-se de não deixar o Canal em estado de preparação sem um feed de contribuição de entrada por mais de > 15 minutos.
>
>

## <a name="test-playback"></a>Reprodução de teste

Navegue para a ferramenta AMSE e clique no canal para ser testado. A partir do menu, passe por cima da **Reprodução da Pré-visualização** e selecione com O Leitor de **Mídia Azure**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Se o fluxo aparecer no leitor, o codificador foi corretamente configurado para ligar à AMS.

Se for recebido um erro, o canal terá de ser reiniciado e ajustado as definições codificadoras. Consulte o artigo de resolução de [problemas](media-services-troubleshooting-live-streaming.md) para orientação.  

## <a name="create-a-program"></a>Criar um programa

1. Uma vez confirmada a reprodução do canal, crie um programa. Sob o separador **Live** na ferramenta AMSE, clique à direita dentro da área do programa e selecione **Criar Novo Programa**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
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

## <a name="next-step"></a>Passo seguinte

Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
