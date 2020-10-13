---
title: Configure políticas de proteção de conteúdos utilizando o portal Azure Microsoft Docs
description: Este artigo demonstra como usar o portal Azure para configurar políticas de proteção de conteúdos. O artigo também mostra como ativar encriptação dinâmica para os seus ativos.
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
ms.openlocfilehash: 061d66f08e79f935c778eb84dd47f71cab1a62d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89266634"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Configure políticas de proteção de conteúdos utilizando o portal Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Para concluir este tutorial, precisa de uma conta do Azure. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).   > Não estão a ser adicionadas novas funcionalidades ou funcionalidades aos Serviços de Comunicação Social v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)
>

 Com a Azure Media Services, pode proteger os seus meios de comunicação a partir do momento em que este deixa o computador através do armazenamento, processamento e entrega. Pode utilizar os Serviços de Comunicação social para fornecer o seu conteúdo encriptado dinamicamente com o Padrão avançado de encriptação (AES) utilizando chaves de encriptação de 128 bits. Também pode usá-lo com encriptação comum (CENC) utilizando a PlayReady e/ou Widevine digital rights management (DRM) e Apple FairPlay. 

A Media Services presta um serviço de entrega de licenças de DRM e chaves claras da AES aos clientes autorizados. Pode utilizar o portal Azure para criar uma política de autorização de chave/licença para todos os tipos de encriptações.

Este artigo demonstra como configurar uma política de proteção de conteúdos utilizando o portal. O artigo também mostra como aplicar encriptação dinâmica aos seus ativos.

## <a name="start-to-configure-content-protection"></a>Comece a configurar a proteção de conteúdos
Para utilizar o portal para configurar a proteção global de conteúdos utilizando a sua conta Deseguis, tome as seguintes medidas:

1. No [portal,](https://portal.azure.com/)selecione a sua conta De Serviços de Comunicação Social.

1. Selecione **Definições**  >  **Proteção de conteúdo**.

    ![Proteção de conteúdo](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Política de autorização chave/licença
Os Serviços de Comunicação Social suportam múltiplas formas de autenticar utilizadores que fazem pedidos de chave ou licença. Tem de configurar a política de autorização da chave de conteúdo. O seu cliente deve então cumprir a apólice antes que a chave/licença lhe possa ser entregue. A política de autorização da chave de conteúdo pode ter uma ou mais restrições de autorização, quer sejam restrições abertas ou de token.

Pode utilizar o portal para criar uma política de autorização de chave/licença para todos os tipos de encriptações.

### <a name="open-authorization"></a>Autorização aberta
A restrição aberta significa que o sistema fornece a chave a quem faz um pedido chave. Esta restrição pode ser útil para efeitos de teste. 

### <a name="token-authorization"></a>Autorização simbólica
A política de token restrito tem de ser acompanhada por um token emitido por um serviço de tokens seguro (STS). Os Serviços de Multimédia suportam tokens no formato simple web tokens (SWT) e JSON Web Token (JWT). Os Serviços de Comunicação Social não fornecem uma STS. Pode criar um STS personalizado ou utilizar o Serviço de Controlo de Acesso Azure para emitir fichas. O STS deve ser configurado para criar um símbolo assinado com a chave especificada e emitir alegações que especificou na configuração da restrição simbólica. Se o token for válido e as reclamações no símbolo corresponderem às configuradas para a chave (ou licença), o serviço de entrega de chaves dos Serviços de Comunicação social devolve a chave (ou licença) solicitada ao cliente.

Ao configurar a política restrita de token, deve especificar a chave de verificação primária, o emitente e os parâmetros do público. A chave de verificação primária contém a chave com a que o símbolo foi assinado. O emitente é o serviço de fichas seguras que emite o símbolo. O público (às vezes chamado de âmbito) descreve a intenção do símbolo ou o recurso a que o símbolo autoriza o acesso. O serviço de entrega chave dos Serviços de Comunicação social valida que estes valores no símbolo correspondem aos valores do modelo.

![Política de autorização chave/licença](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>Modelo de licença PlayReady
O modelo de licença PlayReady define a funcionalidade que está ativada na sua licença PlayReady. Para obter mais informações sobre o modelo de licença PlayReady, consulte a visão geral do modelo de licença do [Media Services PlayReady](media-services-playready-license-template-overview.md).

### <a name="nonpersistent"></a>Não-istante
Se configurar uma licença como não-cliente, só é guardada na memória enquanto o jogador usa a licença.  

![Proteção de conteúdos não-agentes](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Persistente
Se configurar uma licença como persistente, é guardada em armazenamento persistente no cliente.

![Proteção persistente de conteúdos](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Modelo de licença do Widevine
O modelo de licença Widevine define a funcionalidade que está ativada nas suas licenças Widevine.

### <a name="basic"></a>Básico
Quando seleciona **Basic,** o modelo é criado com todos os valores predefinidos.

### <a name="advanced"></a>Avançado
Para obter mais informações sobre o modelo de direitos widevine, consulte a visão geral do [modelo de licença widevine](media-services-widevine-license-template-overview.md).

![Proteção avançada de conteúdos](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Configuração FairPlay
Para ativar a encriptação FairPlay, selecione **a configuração FairPlay**. Em seguida, selecione o **certificado de Aplicação** e introduza a **Chave Secreta da Aplicação.** Para obter mais informações sobre a configuração e requisitos do FairPlay, consulte [Protect your HLS content with Apple FairPlay ou Microsoft PlayReady](media-services-protect-hls-with-FairPlay.md).

![Configuração FairPlay](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Aplique encriptação dinâmica no seu ativo
Para tirar partido da encriptação dinâmica, codifique o seu ficheiro de origem num conjunto de ficheiros MP4 adaptivos bitrate.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Selecione um ativo que pretende encriptar
Para ver todos os seus ativos, selecione **Configurações**  >  **Ativos**.

![Opção de ativos](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Criptografe com AES ou DRM
Quando **seleciona Encrypt** para um ativo, vê duas opções: **AES** ou **DRM**. 

#### <a name="aes"></a>AES
A encriptação de chave clara AES está ativada em todos os protocolos de streaming: Smooth Streaming, HLS e MPEG-DASH.

![Configuração de encriptação](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. Depois de selecionar **DRM,** vê diferentes políticas de proteção de conteúdos (que devem ser configuradas por este ponto) e um conjunto de protocolos de streaming:

    a. **PlayReady e Widevine com MPEG-DASH** encriptam dinamicamente o seu stream MPEG-DASH com DrMs PlayReady e Widevine.

    b. **PlayReady e Widevine com MPEG-DASH + FairPlay com HLS** criptografam dinamicamente o seu stream MPEG-DASH com DrMs PlayReady e Widevine. Esta opção também encripta os seus streams HLS com FairPlay.

    c. **PlayReady apenas com streaming liso, HLS e MPEG-DASH** encriptam dinamicamente streams Smooth Streaming, HLS e MPEG-DASH com PlayReady DRM.

    d. **Widevine apenas com MPEG-DASH** encripta dinamicamente o seu MPEG-DASH com DrM Widevine.
    
    e. **O FairPlay apenas com o HLS** encripta dinamicamente o seu fluxo HLS com FairPlay.

1. Para ativar a encriptação FairPlay, na lâmina **De Definições Globais de Proteção de Conteúdos,** selecione **a configuração FairPlay**. Em seguida, selecione o **certificado app**e introduza a **Chave Secreta da Aplicação.**

    ![Tipo de encriptação](./media/media-services-portal-content-protection/media-services-content-protection009.png)

1. Depois de fazer a seleção de encriptação, **selecione Apply**.

>[!NOTE] 
>Se planeia reproduzir um HLS encriptado com AES no Safari, consulte o post de blog [Encrypted HLS no Safari.](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)

## <a name="additional-notes"></a>Notas adicionais

* Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
