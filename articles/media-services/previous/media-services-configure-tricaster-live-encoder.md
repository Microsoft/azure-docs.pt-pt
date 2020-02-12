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
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152504"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Use o codificador NewTek TriCaster para enviar um único fluxo ao vivo bitrate  
> [!div class="op_single_selector"]
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Arame](media-services-configure-wirecast-live-encoder.md)
>
>

Este artigo mostra como configurar o codificador ao vivo [NewTek TriCaster](https://newtek.com/products/tricaster-40.html) para enviar um único fluxo bitrate para canais AMS que estão habilitados para codificação ao vivo. Para obter mais informações, consulte [Trabalhar com Canais Ativados para Realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial mostra como gerir o Azure Media Services (AMS) com a ferramenta do Explorador de serviços de multimédia do Azure (AMSE). Essa ferramenta só é executado no PC do Windows. Se estiver no Mac ou Linux, utilize o portal Azure para criar [canais](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programas.](media-services-portal-creating-live-encoder-enabled-channel.md)

Ao utilizar o Tricaster para enviar um feed de contribuição para os canais AMS que estão habilitados para codificação ao vivo, pode haver falhas de vídeo/áudio no seu evento ao vivo se utilizar certas funcionalidades do Tricaster, tais como corte rápido entre feeds ou mudança de/para ardósias. A equipa da AMS está a trabalhar na resolução destes problemas, até lá, não é recomendado utilizar estas funcionalidades.

> [!NOTE]
>  Considere mudar-se para TLS 1.2, a versão Pré-Adiada TLS.

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

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Especifique um nome de canal, o campo de descrição é opcional. Em definições do Canal, selecione **Standard** para a opção Live Encoding, com o Protocolo de Entrada definido para **RTMP**. Pode deixar todas as outras definições conforme está.

    Certifique-se de que **o arranque do novo canal** está agora selecionado.

3. Clique em **Criar Canal**.

   ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> O canal pode demorar até 20 minutos a iniciar.
>
>

Enquanto o canal está a começar, pode [configurar o codificador](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> A faturação tem início assim que o canal entra num Estado de pronto. Para mais informações, consulte os [estados do Canal.](media-services-manage-live-encoder-enabled-channels.md#states)
>
>

## <a name="a-idconfigure_tricaster_rtmpconfigure-the-newtek-tricaster-encoder"></a><a id="configure_tricaster_rtmp"/>Configurar o codificador NewTek TriCaster

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

    Navegue de volta para a ferramenta AMSE e verificar o estado de conclusão do canal. Uma vez que o Estado tenha mudado de **Início** para **Execução,** pode obter o URL de entrada.

    Quando o canal estiver em execução, clique no nome do canal, navegue para baixo para pairar sobre o URL de entrada de cópia para a área de **reposição** e, em seguida, selecione URL de **entrada primária**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Repasse esta informação no campo **De localização** no **Flash Server** dentro do projeto Tricaster. Atribua também um nome de fluxo no campo **de ID** stream.

    Se as informações de transmissão foram adicionadas ao perfil FMLE, também pode ser importada para esta secção clicando em Definições de **Importação,** navegando para o perfil DE FMLE guardado e clicando **OK**. Os campos flash server relevantes devem ser preenchidos com a informação da FMLE.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. Quando terminar, **clique** ok na parte inferior do ecrã. Quando as inserções de vídeo e áudio no Tricaster estiverem prontas, comece a transmitir para AMS clicando no botão **Stream.**

     ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Antes de clicar em **Stream,** **certifique-se** de que o Canal está pronto.
> Além disso, certifique-se de que não o canal no estado pronto sem uma contribuição entrada feed durante mais de > 15 minutos.
>
>

## <a name="test-playback"></a>Reprodução de teste

Navegue para a ferramenta AMSE e o canal a ser testado com o botão direito. A partir do menu, passe por cima da **Reprodução da Pré-visualização** e selecione com O Leitor de **Mídia Azure**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Se o fluxo aparece no player de, em seguida, o codificador foi corretamente configurado para ligar para o AMS.

Se for recebido um erro, o canal terá de ser reiniciado e ajustado as definições codificadoras. Consulte o artigo de resolução de [problemas](media-services-troubleshooting-live-streaming.md) para orientação.  

## <a name="create-a-program"></a>Criar um programa

1. Assim que a reprodução de canal é confirmada, crie um programa. Sob o separador **Live** na ferramenta AMSE, clique à direita dentro da área do programa e selecione **Criar Novo Programa**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
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

## <a name="next-step"></a>Passo seguinte

Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
