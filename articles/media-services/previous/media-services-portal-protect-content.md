---
title: Configurar políticas de proteção de conteúdo com o portal do Azure | Documentos da Microsoft
description: Este artigo demonstra como utilizar o portal do Azure para configurar políticas de proteção de conteúdo. O artigo também mostra como ativar a encriptação dinâmica para os seus ativos.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: c0571e202448951de0994d34f68e1649eabd5519
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259079"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Configurar políticas de proteção de conteúdo com o portal do Azure
 Com os serviços de multimédia do Azure, pode proteger os seus suportes de dados a partir do momento em que deixa seu computador por meio de armazenamento, processamento e entrega. Pode utilizar os serviços de multimédia para distribuir os seus conteúdos encriptados dinamicamente com a encriptação AES (Advanced Standard) utilizando as chaves de encriptação de 128 bits. Também pode utilizá-lo com a encriptação comum (CENC) com o PlayReady e/ou Widevine gestão de direitos digitais (DRM) e o Apple FairPlay. 

Os Media Services fornecem um serviço para entregar licenças DRM e chaves para os clientes autorizados de não encriptada AES. Pode utilizar o portal do Azure para criar uma política de autorização de chaves/licenças para todos os tipos de encriptações.

Este artigo demonstra como configurar uma política de proteção de conteúdo com o portal. O artigo também mostra como aplicar encriptação dinâmica para os seus ativos.

## <a name="start-to-configure-content-protection"></a>Começar a configurar a proteção de conteúdo
Para utilizar o portal para configurar a proteção de conteúdo global com a conta dos Media Services, siga os passos seguintes:

1. Na [portal](https://portal.azure.com/), selecione a sua conta de Media Services.

1. Selecione **configurações** > **proteção de conteúdo**.

    ![Proteção de conteúdo](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Política de autorização de chave/licença
Os Media Services suportam várias formas de autenticar os utilizadores que efetuam pedidos de chave ou de licença. Tem de configurar a política de autorização da chave de conteúdo. Seu cliente, em seguida, tem de cumprir a política antes de chaves/licenças podem ser entregues ao mesmo. A política de autorização da chave de conteúdo pode ter uma ou mais restrições de autorização, quer sejam restrições abertas ou de token.

Pode utilizar o portal para criar uma política de autorização de chaves/licenças para todos os tipos de encriptações.

### <a name="open-authorization"></a>Autorização aberta
Restrição aberta significa que o sistema fornece a chave para qualquer pessoa que faz uma solicitação de chave. Esta restrição poderá ser útil para fins de teste. 

### <a name="token-authorization"></a>Autorização de token
A política de token restrito tem de ser acompanhada por um token emitido por um serviço de tokens seguro (STS). Serviços de multimédia suportam tokens no web simples token (SWT) e os formatos JSON Web Token (JWT). Serviços de multimédia não fornece um STS. Pode criar um STS personalizado ou utilizar o serviço de controle de acesso do Azure para emitir tokens. O STS deve ser configurado para criar um token assinado com as declarações de chave e o problema especificadas que especificou na configuração de restrição de token. Se o token é válido e as afirmações no token corresponderem às configuradas para a chave (ou uma licença), o serviço de entrega de chave de serviços de multimédia devolve a chave de pedido (ou licença) para o cliente.

Quando configurar a política de token restrito, tem de especificar a chave de verificação primária, emissor e parâmetros de público-alvo. A chave de verificação primária contém a chave de que o token foi assinado com. O emissor é o serviço de token seguro que emite o token. O público-alvo (por vezes denominado âmbito) descreve a intenção do token ou o recurso o token de acesso a autoriza. O serviço de entrega de chave de serviços de multimédia valida que estes valores no token correspondem aos valores no modelo.

![Política de autorização de chave/licença](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>Modelo de licença PlayReady
O modelo de licença PlayReady define a funcionalidade que está ativada na sua licença de PlayReady. Para obter mais informações sobre o modelo de licença do PlayReady, consulte a [descrição geral do modelo de licença PlayReady de serviços de multimédia](media-services-playready-license-template-overview.md).

### <a name="nonpersistent"></a>Nonpersistent
Se configurar uma licença como nonpersistent, é mantida na memória apenas enquanto o player utiliza a licença.  

![Proteção de conteúdo nonpersistent](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Persistente
Se configurar uma licença como persistente, é guardado no armazenamento persistente no cliente.

![Proteção de conteúdo persistente](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Modelo de licença do Widevine
O modelo de licença do Widevine define a funcionalidade que está ativada em suas licenças do Widevine.

### <a name="basic"></a>Básica
Quando seleciona **básica**, o modelo é criado com todos os valores predefinidos.

### <a name="advanced"></a>Avançado
Para obter mais informações sobre o modelo de direitos do Widevine, consulte a [descrição geral do modelo de licença Widevine](media-services-widevine-license-template-overview.md).

![Proteção de conteúdo avançada](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Configuração do FairPlay
Para ativar a encriptação FairPlay, selecione **configuração do FairPlay**. Em seguida, selecione o **certificado de aplicação** e introduza o **chave secreta da aplicação**. Para obter mais informações sobre a configuração do FairPlay e requisitos, consulte [proteger o seu conteúdo com o Apple FairPlay ou o Microsoft PlayReady HLS](media-services-protect-hls-with-FairPlay.md).

![Configuração do FairPlay](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Aplicar encriptação dinâmica ao seu elemento
Para tirar partido da encriptação dinâmica, codificar o ficheiro de origem para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Selecione um elemento que pretende encriptar
Para ver todos os seus ativos, selecione **configurações** > **ativos**.

![Opção de ativos](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Encriptar com AES ou DRM
Quando seleciona **Encrypt** para um recurso, verá duas opções: **AES** ou **DRM**. 

#### <a name="aes"></a>AES
Não encriptada AES encriptação de chave está ativada em todos os protocolos de transmissão em fluxo: Transmissão em fluxo uniforme, HLS e MPEG-DASH.

![Configuração da encriptação](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. Depois de selecionar **DRM**, consulte as políticas de proteção de conteúdo diferentes (que tem de ser configuradas a essa altura) e um conjunto de protocolos de transmissão em fluxo:

    a. **PlayReady e Widevine com MPEG-DASH** dinamicamente encripta a sua transmissão em fluxo MPEG-DASH com PlayReady e Widevine DRMs.

    b. **PlayReady e Widevine com MPEG-DASH + FairPlay com HLS** encriptar dinamicamente a sua transmissão em fluxo MPEG-DASH com PlayReady e Widevine DRMs. Esta opção também encripta transmissões em fluxo HLS com FairPlay.

    c. **PlayReady apenas com Smooth Streaming, HLS e MPEG-DASH** dinamicamente encripta os fluxos de transmissão em fluxo uniforme, HLS e MPEG-DASH com o PlayReady DRM.

    d. **Widevine apenas com MPEG-DASH** dinamicamente criptografa seus MPEG-DASH com Widevine DRM.
    
    e. **FairPlay apenas com HLS** dinamicamente encripta a sua transmissão em fluxo HLS com FairPlay.

1. Para ativar a encriptação FairPlay, o **definições globais de proteção de conteúdo** painel, selecione **configuração do FairPlay**. Em seguida, selecione o **certificado de aplicação**e introduza o **chave secreta da aplicação**.

    ![Tipo de encriptação](./media/media-services-portal-content-protection/media-services-content-protection009.png)

1. Depois de efetuar a seleção de encriptação, selecione **aplicar**.

>[!NOTE] 
>Se pretender reproduzir um HLS encriptado por AES no Safari, consulte a mensagem de blogue [hls encriptado por no Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Passos Seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

