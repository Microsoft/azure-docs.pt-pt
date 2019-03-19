---
title: Configurar o codificador de NewTek tricaster para enviar um fluxo em direto com velocidade de transmissão única | Documentos da Microsoft
description: Este tópico mostra como configurar o codificador em direto de Tricaster para enviar um fluxo de velocidade de transmissão única para canais de AMS ativados para live encoding.
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
ms.author: juliako;cenkd;anilmur
ms.openlocfilehash: adacce5c8307f3be972920bd3aef6d8ea912eb30
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57991991"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Utilizar o codificador de NewTek tricaster para enviar um fluxo em direto com velocidade de transmissão única  
> [!div class="op_single_selector"]
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

Este artigo mostra como configurar o [codificador de NewTek TriCaster](http://newtek.com/products/tricaster-40.html) codificador em direto para enviar um fluxo de velocidade de transmissão única para o AMS canais que estão ativados para live encoding. Para obter mais informações, consulte [Trabalhar com Canais Ativados para Realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial mostra como gerir o Azure Media Services (AMS) com a ferramenta do Explorador de serviços de multimédia do Azure (AMSE). Essa ferramenta só é executado no PC do Windows. Se estiver no Mac ou Linux, utilize o portal do Azure para criar [canais](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programas](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> Quando utilizar Tricaster para o envio de uma contribuição feed para canais de AMS ativados para live encoding, pode haver falhas de vídeo/áudio em seu evento em direto se usar determinados recursos do Tricaster, como o rápido cortando entre feeds ou mudar de/para slates. A equipe de AMS é trabalhar para corrigir esses problemas, até lá, ele não é recomendado usar esses recursos.
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

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Especifique um nome de canal, o campo de descrição é opcional. Em definições do canal, selecione **padrão** para a opção Live Encoding, com o protocolo de entrada definido como **RTMP**. Pode deixar todas as outras definições conforme está.

    Certifique-se de que o **iniciar agora o novo canal** está selecionada.

3. Clique em **criar canal**.

   ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> O canal pode demorar até 20 minutos a iniciar.
>
>

Embora o canal está a iniciar, pode [configurar o codificador](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> A faturação tem início assim que o canal entra num Estado de pronto. Para obter mais informações, consulte [Estados do canal](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfiguretricasterrtmpconfigure-the-newtek-tricaster-encoder"></a><a id="configure_tricaster_rtmp"/>Configurar o codificador de NewTek tricaster

Neste tutorial, são utilizadas as seguintes definições de saída. O resto desta secção descreve os passos de configuração mais detalhadamente.

**Vídeo**:

* Codec: H.264
* Perfil: Alto (nível 4.0)
* Velocidade de transmissão: 5000 kbps
* Quadro-chave: 2 segundos (60 segundos)
* Taxa de quadros: 30

**Áudio**:

* Codec: AAC (LC)
* Velocidade de transmissão: 192 kbps
* Taxa da amostragem: 44.1 kHz

### <a name="configuration-steps"></a>Passos de configuração

1. Criar uma nova **codificador de NewTek TriCaster** projeto dependendo de que origem de entrada de vídeo está a ser utilizada.
2. Uma vez dentro desse projeto, encontre o **Stream** botão e clique no ícone de engrenagem junto ao mesmo para aceder ao menu de configuração do fluxo.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Assim que tiver aberto o menu, clique em **New** sob o cabeçalho de ligação. Quando lhe for pedido para o tipo de ligação, selecione **Adobe Flash**.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Clique em **OK**.
5. Um perfil FMLE agora pode ser importado clicando na pendente na seta sob **perfil de transmissão em fluxo** e navegando até **procurar**.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Navegue para onde foi guardado o perfil FMLE configurado.
7. Selecione-o e prima **OK**.

    Depois do perfil é carregado, avance para o passo seguinte.
8. URL de entrada do Get o canal para poder atribuí-lo para o Tricaster **ponto final de RTMP**.

    Navegue de volta para a ferramenta AMSE e verificar o estado de conclusão do canal. Assim que o estado foi alterado de **inicial** ao **em execução**, pode obter o URL de entrada.

    Quando o canal está em execução, clique com o botão direito do rato em nome do canal, percorra para baixo para a passagem de Mouse **URL de entrada de cópia para área de transferência** e, em seguida, selecione **URL de entrada principal**.  

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Cole esta informação na **localização** em **Flash servidor** dentro do projeto Tricaster. Também atribuir um nome de fluxo a **ID de Stream** campo.

    Se as informações de fluxo foi adicionadas ao perfil de FMLE, ele também é possível importar a esta secção clicando **importar as definições**, navegar para o perfil FMLE guardado e clicar em **OK**. Os campos relevantes do Flash Server devem preencher com as informações do FMLE.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. Quando terminar, clique em **OK** na parte inferior do ecrã. Quando as entradas de áudio e vídeos para o Tricaster estiverem prontas, começar a transmissão em fluxo para o AMS clicando a **Stream** botão.

     ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Antes de clicar em **Stream**, **tem** Certifique-se de que o canal está pronto.
> Além disso, certifique-se de que não o canal no estado pronto sem uma contribuição entrada feed durante mais de > 15 minutos.
>
>

## <a name="test-playback"></a>Reprodução de teste

Navegue para a ferramenta AMSE e o canal a ser testado com o botão direito. No menu, coloque o cursor sobre **reprodução a pré-visualização** e selecione **com o Azure Media Player**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Se o fluxo aparece no player de, em seguida, o codificador foi corretamente configurado para ligar para o AMS.

Se for recebido um erro, será necessário repor o canal e definições de codificador ajustado. Consulte a [resolução de problemas](media-services-troubleshooting-live-streaming.md) artigo para obter orientações.  

## <a name="create-a-program"></a>Criar um programa

1. Assim que a reprodução de canal é confirmada, crie um programa. Sob o **Live** separador a ferramenta AMSE, com o botão direito dentro da área de programa e selecione **criar novo programa**.  

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
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
