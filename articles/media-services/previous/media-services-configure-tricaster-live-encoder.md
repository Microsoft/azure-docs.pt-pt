---
title: Configurar o codificador codificador Newtek tricastr para enviar uma transmissão ao vivo de taxa de bits única | Microsoft Docs
description: Este tópico mostra como configurar o codificador dinâmico do TriCaster para enviar um fluxo de taxa de bits única para os canais do AMS que estão habilitados para codificação ativa.
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
ms.openlocfilehash: 0e793a5aa7d619b0bb7a1d3efcdf665ea400c555
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "69016745"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Usar o codificador codificador Newtek TriCaster para enviar uma transmissão ao vivo de taxa de bits única  
> [!div class="op_single_selector"]
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Dinâmico do elemento](media-services-configure-elemental-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

Este artigo mostra como configurar o codificador dinâmico [codificador Newtek](https://newtek.com/products/tricaster-40.html) TriCaster para enviar um fluxo de taxa de bits única para os canais do AMS que estão habilitados para codificação ativa. Para obter mais informações, consulte [Trabalhar com Canais Ativados para Realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial mostra como gerir o Azure Media Services (AMS) com a ferramenta do Explorador de serviços de multimédia do Azure (AMSE). Essa ferramenta só é executado no PC do Windows. Se estiver no Mac ou Linux, utilize o portal do Azure para criar [canais](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programas](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> Ao usar o tricastr para enviar em um feed de contribuição para os canais do AMS que estão habilitados para codificação ativa, pode haver falhas de vídeo/áudio em seu evento ao vivo se você usar determinados recursos do TriCaster, como a rápida recorte entre feeds ou troca de/para slates. A equipe do AMS está trabalhando para corrigir esses problemas, até lá, não é recomendável usar esses recursos.
>
>

## <a name="prerequisites"></a>Pré-requisitos

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

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Especifique um nome de canal, o campo de descrição é opcional. Em definições do canal, selecione **padrão** para a opção Live Encoding, com o protocolo de entrada definido como **RTMP**. Pode deixar todas as outras definições conforme está.

    Certifique-se de que o **iniciar agora o novo canal** está selecionada.

3. Clique em **criar canal**.

   ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> O canal pode demorar até 20 minutos a iniciar.
>
>

Embora o canal está a iniciar, pode [configurar o codificador](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> A faturação tem início assim que o canal entra num Estado de pronto. Para obter mais informações, consulte [Estados do canal](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfigure_tricaster_rtmpconfigure-the-newtek-tricaster-encoder"></a><a id="configure_tricaster_rtmp"/>Configurar o codificador codificador Newtek tricastr

Neste tutorial, são utilizadas as seguintes definições de saída. O resto desta secção descreve os passos de configuração mais detalhadamente.

**Vídeo**:

* Codec H. 264
* Criar Alta (nível 4,0)
* 720p 5000 kbps
* Keyframe 2 segundos (60 segundos)
* Taxa de quadros: 30

**Áudio**:

* Codec AAC (LC)
* 720p 192 kbps
* Taxa de amostra: 44,1 kHz

### <a name="configuration-steps"></a>Passos de configuração

1. Crie um novo projeto do **codificador Newtek** TriCaster dependendo de qual fonte de entrada de vídeo está sendo usada.
2. Uma vez dentro desse projeto, localize o botão de **fluxo** e clique no ícone de engrenagem ao lado dele para acessar o menu de configuração de fluxo.

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Depois que o menu for aberto, clique em **novo** no cabeçalho conexão. Quando for solicitado o tipo de conexão, selecione **Adobe Flash**.

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Clique em **OK**.
5. Um perfil FMLE agora pode ser importado clicando na seta suspensa no **perfil de streaming** e navegando até **procurar**.

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Navegue até onde o perfil FMLE configurado foi salvo.
7. Selecione-o e pressione **OK**.

    Depois que o perfil for carregado, vá para a próxima etapa.
8. Obtenha a URL de entrada do canal para atribuí-la ao **ponto de extremidade RTMP**do TriCaster.

    Navegue de volta para a ferramenta AMSE e verificar o estado de conclusão do canal. Assim que o estado foi alterado de **inicial** ao **em execução**, pode obter o URL de entrada.

    Quando o canal estiver em execução, clique com o botão direito do mouse no nome do canal, navegue para baixo até o cursor **copiar a URL de entrada para a área de transferência** e selecione **URL de entrada primária**.  

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Cole essas informações no campo **local** , em **servidor flash** , dentro do projeto de tricasts. Atribua também um nome de fluxo no campo **ID de fluxo** .

    Se as informações de fluxo tiverem sido adicionadas ao perfil FMLE, elas também poderão ser importadas para esta seção clicando em **Importar configurações**, navegando até o perfil FMLE salvo e clicando em **OK**. Os campos de servidor flash relevantes devem preencher com as informações de FMLE.

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. Quando terminar, clique em **OK** na parte inferior da tela. Quando as entradas de vídeo e áudio no TriCaster estiverem prontas, comece a transmitir para o AMS clicando no botão **fluxo** .

     ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Antes de clicar em **Stream**, **tem** Certifique-se de que o canal está pronto.
> Além disso, certifique-se de que não o canal no estado pronto sem uma contribuição entrada feed durante mais de > 15 minutos.
>
>

## <a name="test-playback"></a>Reprodução de teste

Navegue para a ferramenta AMSE e o canal a ser testado com o botão direito. No menu, coloque o cursor sobre **reprodução a pré-visualização** e selecione **com o Azure Media Player**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Se o fluxo aparece no player de, em seguida, o codificador foi corretamente configurado para ligar para o AMS.

Se um erro for recebido, o canal precisará ser redefinido e as configurações do codificador serão ajustadas. Consulte a [resolução de problemas](media-services-troubleshooting-live-streaming.md) artigo para obter orientações.  

## <a name="create-a-program"></a>Criar um programa

1. Assim que a reprodução de canal é confirmada, crie um programa. Sob o **Live** separador a ferramenta AMSE, com o botão direito dentro da área de programa e selecione **criar novo programa**.  

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
2. Nome do programa e, se necessário, ajuste o **duração da janela de arquivo** (que está predefinida para quatro horas). Também pode especificar uma localização de armazenamento ou deixe como a predefinição.  
3. Verifique os **iniciar o programa agora** caixa.
4. Clique em **criar programa**.  

    >[!NOTE]
    >Criação de programa demora menos tempo do que a criação de canal.
        
5. Quando o programa estiver em execução, confirmar reprodução o botão direito e navegar até **reprodução os programas** e, em seguida, selecionando **com o Azure Media Player**.  
6. Depois de confirmar, com o programa novamente o botão direito e selecione **copie o URL de saída para a área de transferência** (ou recuperar essas informações da **informações e definições do programa** opção do menu).

O fluxo está agora pronto para ser incorporados num leitor ou distribuído para um público-alvo para visualização em direto.  

## <a name="troubleshooting"></a>Resolução de problemas

Consulte a [resolução de problemas](media-services-troubleshooting-live-streaming.md) artigo para obter orientações.

## <a name="next-step"></a>Passo seguinte

Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
