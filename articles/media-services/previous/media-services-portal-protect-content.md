---
title: Configure as políticas de proteção de conteúdos utilizando o portal Azure [ Microsoft Docs
description: Este artigo demonstra como usar o portal Azure para configurar políticas de proteção de conteúdos. O artigo também mostra como ativar a encriptação dinâmica para os seus ativos.
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
ms.openlocfilehash: 0c2a9612fab6c685cbc690aa9bbc12d1c7b7b746
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74978207"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Configure as políticas de proteção de conteúdos utilizando o portal Azure

> [!NOTE]
> Para concluir este tutorial, precisa de uma conta do Azure. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).   > Não estão a ser adicionadas novas funcionalidades ou funcionalidades aos Serviços de Media v2. <br/>Confira a versão mais recente, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [a orientação de migração da v2 para a v3](../latest/migrate-from-v2-to-v3.md)
>

 Com a Azure Media Services, pode proteger os seus meios a partir do momento em que deixa o seu computador através do armazenamento, processamento e entrega. Pode utilizar os Serviços de Media para fornecer o seu conteúdo encriptado de forma dinâmica com o Advanced Encryption Standard (AES) utilizando chaves de encriptação de 128 bits. Também pode usá-lo com encriptação comum (CENC) utilizando a Gestão de direitos digitais PlayReady e/ou Widevine (DRM) e Apple FairPlay. 

A Media Services presta um serviço para a entrega de licenças de DRM e chaves claras a clientes autorizados. Pode utilizar o portal Azure para criar uma política de autorização de chave/licença para todos os tipos de encriptações.

Este artigo demonstra como configurar uma política de proteção de conteúdos utilizando o portal. O artigo também mostra como aplicar encriptação dinâmica aos seus ativos.

## <a name="start-to-configure-content-protection"></a>Comece a configurar a proteção de conteúdos
Para utilizar o portal para configurar a proteção global de conteúdos utilizando a sua conta Media Services, tome as seguintes medidas:

1. No [portal,](https://portal.azure.com/)selecione a sua conta Media Services.

1. Selecione **definições** > **proteção de conteúdo**.

    ![Proteção de conteúdo](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Política de autorização de chave/licença
A Media Services suporta várias formas de autenticar utilizadores que fazem pedidos chave ou licença. Tem de configurar a política de autorização da chave de conteúdo. O seu cliente deve então cumprir a apólice antes que a chave/licença possa ser entregue. A política de autorização da chave de conteúdo pode ter uma ou mais restrições de autorização, quer sejam restrições abertas ou de token.

Pode utilizar o portal para criar uma política de autorização de chave/licença para todos os tipos de encriptações.

### <a name="open-authorization"></a>Autorização aberta
A restrição aberta significa que o sistema fornece a chave a quem fizer um pedido chave. Esta restrição pode ser útil para efeitos de teste. 

### <a name="token-authorization"></a>Autorização simbólica
A política de token restrito tem de ser acompanhada por um token emitido por um serviço de tokens seguro (STS). Os Serviços de Multimédia suportam tokens no formato simple web tokens (SWT) e JSON Web Token (JWT). A Media Services não fornece uma STS. Pode criar um STS personalizado ou utilizar o Serviço de Controlo de Acesso Azure para emitir fichas. O STS deve ser configurado para criar um símbolo assinado com a chave especificada e emitir alegações que especificaste na configuração de restrição simbólica. Se o símbolo for válido e os créditos no token corresponderem aos configurados para a chave (ou licença), o serviço de entrega da chave media services devolve a chave (ou licença) solicitada ao cliente.

Quando configurar a política restrita de fichas, deve especificar a chave de verificação primária, o emitente e os parâmetros do público. A chave de verificação primária contém a chave com a que o símbolo foi assinado. O emitente é o serviço de fichas seguro que emite o símbolo. O público (às vezes chamado de âmbito) descreve a intenção do símbolo ou o recurso a que o símbolo autoriza o acesso. O serviço de entrega de chaves media services valida que estes valores no token correspondem aos valores do modelo.

![Política de autorização de chave/licença](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>Modelo de licença PlayReady
O modelo de licença PlayReady define a funcionalidade que está ativada na sua licença PlayReady. Para obter mais informações sobre o modelo de licença PlayReady, consulte a visão geral do modelo de licença PlayReady dos [Serviços de Media](media-services-playready-license-template-overview.md)Services .

### <a name="nonpersistent"></a>Não persistente
Se configurar uma licença como não persistente, é mantida na memória apenas enquanto o leitor utiliza a licença.  

![Proteção de conteúdo não persistente](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Persistente
Se configurar uma licença como persistente, é guardada em armazenamento persistente no cliente.

![Proteção persistente de conteúdos](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Modelo de licença do Widevine
O modelo de licença Widevine define a funcionalidade que está ativada nas suas licenças Widevine.

### <a name="basic"></a>Básico
Quando seleciona **Basic,** o modelo é criado com todos os valores predefinidos.

### <a name="advanced"></a>Avançado
Para obter mais informações sobre o modelo de direitos da Widevine, consulte a visão geral do modelo de [licença Widevine](media-services-widevine-license-template-overview.md).

![Proteção avançada de conteúdos](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Configuração FairPlay
Para ativar a encriptação FairPlay, selecione **a configuração FairPlay**. Em seguida, selecione o **certificado de aplicação** e introduza a chave secreta da **aplicação**. Para obter mais informações sobre a configuração e os requisitos do FairPlay, consulte [Protect your HLS content with Apple FairPlay ou Microsoft PlayReady](media-services-protect-hls-with-FairPlay.md).

![Configuração FairPlay](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Aplique encriptação dinâmica ao seu ativo
Para tirar partido da encriptação dinâmica, codifique o seu ficheiro fonte num conjunto de ficheiros MP4 de bitrate adaptativo.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Selecione um ativo que pretende encriptar
Para ver todos os seus ativos, selecione Ativos**de** **Definições** > .

![Opção de ativos](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Criptografe com AES ou DRM
Quando selecionar **Encriptar** para um ativo, vê duas opções: **AES** ou **DRM**. 

#### <a name="aes"></a>AES
A encriptação clara da chave AES está ativada em todos os protocolos de streaming: Smooth Streaming, HLS e MPEG-DASH.

![Configuração de encriptação](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. Depois de selecionar **o DRM,** vê diferentes políticas de proteção de conteúdos (que devem ser configuradas por este ponto) e um conjunto de protocolos de streaming:

    a. **PlayReady e Widevine com MPEG-DASH** encriptam dinamicamente o seu fluxo MPEG-DASH com DRMs PlayReady e Widevine.

    b. **PlayReady e Widevine com MPEG-DASH + FairPlay com HLS** encriptam dinamicamente o seu fluxo MPEG-DASH com DRMs PlayReady e Widevine. Esta opção também encripta os seus streams hls com FairPlay.

    c. **PlayReady apenas com streaming liso, HLS e MPEG-DASH** encripta dinâmicamente streams Smooth Streaming, HLS e MPEG-DASH com ReDRM PlayReady.

    d. **A widevine apenas com MPEG-DASH** encripta dinamicamente o seu MPEG-DASH com Drm Widevine.
    
    e. **FairPlay apenas com HLS** encripta dINAmicamente o seu fluxo HLS com FairPlay.

1. Para ativar a encriptação FairPlay, na lâmina de **definições globais** de proteção de conteúdo, selecione a **configuração FairPlay**. Em seguida, selecione o **certificado de aplicação**, e introduza a Chave Secreta da **Aplicação**.

    ![Tipo de encriptação](./media/media-services-portal-content-protection/media-services-content-protection009.png)

1. Depois de fazer a seleção de encriptação, selecione **Aplicar**.

>[!NOTE] 
>Se planeia jogar um HLS encriptado a AES no Safari, consulte o blog post [Encrypted HLS no Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="additional-notes"></a>Notas adicionais

* A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

