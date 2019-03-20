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
ms.author: juliako;cenkdin;anilmur
ms.openlocfilehash: d0da69601bfc6fd09c10b30d45195722781d87d6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57890054"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Utilizar o codificador de Wirecast para enviar um fluxo em direto com velocidade de transmissão única 
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

Este artigo mostra como configurar o [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) codificador em direto para enviar um fluxo de velocidade de transmissão única para o AMS canais que estão ativados para live encoding.  Para obter mais informações, consulte [Trabalhar com Canais Ativados para Realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial mostra como gerir o Azure Media Services (AMS) com a ferramenta do Explorador de serviços de multimédia do Azure (AMSE). Essa ferramenta só é executado no PC do Windows. Se estiver no Mac ou Linux, utilize o portal do Azure para criar [canais](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programas](media-services-portal-creating-live-encoder-enabled-channel.md).

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

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Especifique um nome de canal, o campo de descrição é opcional. Em definições do canal, selecione **padrão** para a opção Live Encoding, com o protocolo de entrada definido como **RTMP**. Pode deixar todas as outras definições conforme está.

    Certifique-se de que o **iniciar agora o novo canal** está selecionada.

3. Clique em **criar canal**.

   ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> O canal pode demorar até 20 minutos a iniciar.
>
>

Embora o canal está a iniciar, pode [configurar o codificador](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> A faturação tem início assim que o canal entra num Estado de pronto. Para obter mais informações, consulte [Estados do canal](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfigurewirecastrtmp-configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />Configurar o codificador Telestream Wirecast
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
1. Abra a aplicação de Telestream Wirecast sobre o computador que está a ser utilizado e definida para RTMP de transmissão em fluxo.
2. Configurar a saída ao navegar para o **saída** separador e selecionando **definições de saída...** .

    Certifique-se de que o **destino de saída** está definida como **RTMP servidor**.
3. Clique em **OK**.
4. Na página Definições, defina o **destino** campo esteja **dos serviços de multimédia do Azure**.

    O perfil de codificação é pré-selecionada ao **720 p 16:9 (1280 x 720) do Azure H.264**. Para personalizar estas definições, selecione o ícone de engrenagem à direita da lista pendente e, em seguida, escolha **predefinir novo**.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Configure predefinições de codificador.

    Nomeie a configuração predefinida e verificar as seguintes definições recomendadas:

    **Vídeo**

   * Codificador: MainConcept H.264
   * Quadros por segundo: 30
   * Taxa de bits média: 5000 kbits/seg (pode ser ajustado com base no limitações de rede)
   * Perfil: Principal
   * Quadro-chave cada: 60 quadros

     **Áudio**

   * Taxa de bits de destino: 192 kbits/seg
   * Taxa da amostragem: 44.100 kHz

     ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Prima **Guardar**.

    O campo de codificação tem agora o perfil criado recentemente estão disponível para seleção.

    Certifique-se de que o novo perfil está selecionado.
7. URL de entrada do Get o canal para poder atribuí-lo para o Wirecast **ponto final de RTMP**.

    Navegue de volta para a ferramenta AMSE e verificar o estado de conclusão do canal. Assim que o estado foi alterado de **inicial** ao **em execução**, pode obter o URL de entrada.

    Quando o canal está a ser executado, faça duplo clique o nome do canal, percorra para baixo para a passagem de Mouse **URL de entrada de cópia para área de transferência** e, em seguida, selecione **URL de entrada principal**.  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. Na Wirecast **definições de saída** janela, cole esta informação na **endereço** campo da secção de saída e atribuir um nome de fluxo.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Selecione **OK**.
2. No principal **Wirecast** ecrã, confirme as origens de entrada para o vídeo e áudio, está prontos e, em seguida, prima **Stream** no canto superior esquerdo.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Antes de clicar em **Stream**, **tem** Certifique-se de que o canal está pronto.
> Além disso, certifique-se de que não o canal no estado pronto sem uma contribuição entrada feed durante mais de > 15 minutos.
>
>

## <a name="test-playback"></a>Reprodução de teste

Navegue para a ferramenta AMSE e o canal a ser testado com o botão direito. No menu, coloque o cursor sobre **reprodução a pré-visualização** e selecione **com o Azure Media Player**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Se o fluxo aparece no player de, em seguida, o codificador foi corretamente configurado para ligar para o AMS.

Se for recebido um erro, o canal tem de ser reposto e definições de codificador ajustado. Consulte a [resolução de problemas](media-services-troubleshooting-live-streaming.md) artigo para obter orientações.  

## <a name="create-a-program"></a>Criar um programa
1. Assim que a reprodução de canal é confirmada, crie um programa. Sob o **Live** separador a ferramenta AMSE, com o botão direito dentro da área de programa e selecione **criar novo programa**.  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
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

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
