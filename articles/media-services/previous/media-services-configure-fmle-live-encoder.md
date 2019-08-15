---
title: Configurar o codificador FMLE para enviar uma transmissão ao vivo de taxa de bits única | Microsoft Docs
description: Este tópico mostra como configurar o codificador do Flash Media Live encoder (FMLE) para enviar um fluxo de taxa de bits única para os canais do AMS que estão habilitados para codificação ativa.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 3113f333-517a-47a1-a1b3-57e200c6b2a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: 09d9bdffefe9204e9f58b8f07af5b21228269f6c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "69016761"
---
# <a name="use-the-fmle-encoder-to-send-a-single-bitrate-live-stream"></a>Usar o codificador FMLE para enviar uma transmissão ao vivo de taxa de bits única 
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

Este artigo mostra como configurar o codificador FMLE ( [Flash Media Live Encoder](https://www.adobe.com/products/flash-media-encoder.html) ) para enviar um fluxo de taxa de bits única para os canais do AMS que estão habilitados para codificação ativa. Para obter mais informações, consulte [Trabalhar com Canais Ativados para Realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial mostra como gerir o Azure Media Services (AMS) com a ferramenta do Explorador de serviços de multimédia do Azure (AMSE). Essa ferramenta só é executado no PC do Windows. Se estiver no Mac ou Linux, utilize o portal do Azure para criar [canais](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programas](media-services-portal-creating-live-encoder-enabled-channel.md).

Este tutorial descreve como usar o AAC. No entanto, o FMLE não dá suporte a AAC por padrão. Você precisaria comprar um plug-in para a codificação AAC, como de MainConcept: [Plug-in AAC](https://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)

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

    ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle1.png)

2. Especifique um nome de canal, o campo de descrição é opcional. Em definições do canal, selecione **padrão** para a opção Live Encoding, com o protocolo de entrada definido como **RTMP**. Pode deixar todas as outras definições conforme está.

    Certifique-se de que o **iniciar agora o novo canal** está selecionada.

3. Clique em **criar canal**.

   ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle2.png)

> [!NOTE]
> O canal pode demorar até 20 minutos a iniciar.
>
>

Embora o canal está a iniciar, pode [configurar o codificador](media-services-configure-fmle-live-encoder.md).

> [!IMPORTANT]
> Observe que a cobrança começa assim que o canal entra em um estado pronto. Para obter mais informações, consulte [Estados do canal](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_fmle_rtmp></a>Configurar o codificador FMLE
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
1. Navegue até a interface do Flash Media Live encoder (FMLE) no computador que está sendo usado.

    A interface é uma página principal de configurações. Anote as seguintes configurações recomendadas para começar a usar o streaming usando o FMLE.

   * Formato: Taxa de quadros H. 264: 30, 0
   * Tamanho da entrada: 1280 x 720
   * Taxa de bits: 5000 kbps (pode ser ajustado com base nas limitações de rede)  

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

     Ao usar fontes entrelaçadas, faça uma marca de seleção na opção "desentrelaçar"
2. Selecione o ícone de chave inglesa ao lado do formato. essas configurações adicionais devem ser:

   * Criar Principal
   * Nível: 4.0
   * Frequência de quadro-chave: 2 segundos

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle4.png)
3. Defina a seguinte configuração de áudio importante:

   * Formato: AAC
   * Taxa de amostra: 44100 Hz
   * 720p 192 kbps

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle5.png)
4. Obtenha a URL de entrada do canal para atribuí-la ao **ponto de extremidade RTMP**do FMLE.

    Navegue de volta para a ferramenta AMSE e verificar o estado de conclusão do canal. Assim que o estado foi alterado de **inicial** ao **em execução**, pode obter o URL de entrada.

    Quando o canal está a ser executado, faça duplo clique o nome do canal, percorra para baixo para a passagem de Mouse **URL de entrada de cópia para área de transferência** e, em seguida, selecione **URL de entrada principal**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)
5. Cole essas informações no campo **URL do FMS** da seção de saída e atribua um nome de fluxo.

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

    Para redundância extra, repita essas etapas com a URL de entrada secundária.
6. Selecione **Ligar**.

> [!IMPORTANT]
> Antes de clicar em **conectar**, você **deve** garantir que o canal esteja pronto.
> Além disso, certifique-se de que não o canal no estado pronto sem uma contribuição entrada feed durante mais de > 15 minutos.
>
>

## <a name="test-playback"></a>Reprodução de teste

Navegue para a ferramenta AMSE e o canal a ser testado com o botão direito. No menu, coloque o cursor sobre **reprodução a pré-visualização** e selecione **com o Azure Media Player**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle8.png)

Se o fluxo aparece no player de, em seguida, o codificador foi corretamente configurado para ligar para o AMS.

Se for recebido um erro, o canal tem de ser reposto e definições de codificador ajustado. Consulte a [resolução de problemas](media-services-troubleshooting-live-streaming.md) artigo para obter orientações.  

## <a name="create-a-program"></a>Criar um programa
1. Assim que a reprodução de canal é confirmada, crie um programa. Sob o **Live** separador a ferramenta AMSE, com o botão direito dentro da área de programa e selecione **criar novo programa**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle9.png)
2. Nomeie o programa e, se necessário, ajuste o **tamanho da janela de arquivo** (que usa como padrão 4 horas). Também pode especificar uma localização de armazenamento ou deixe como a predefinição.  
3. Verifique os **iniciar o programa agora** caixa.
4. Clique em **criar programa**.  

    >[!NOTE]
    >Criação de programa demora menos tempo do que a criação de canal.
        
5. Quando o programa estiver em execução, confirmar reprodução o botão direito e navegar até **reprodução os programas** e, em seguida, selecionando **com o Azure Media Player**.  
6. Depois de confirmar, com o programa novamente o botão direito e selecione **copie o URL de saída para a área de transferência** (ou recuperar essas informações da **informações e definições do programa** opção do menu).

O fluxo está agora pronto para ser incorporados num leitor ou distribuído para um público-alvo para visualização em direto.  

## <a name="troubleshooting"></a>Resolução de problemas
Consulte a [resolução de problemas](media-services-troubleshooting-live-streaming.md) artigo para obter orientações.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
