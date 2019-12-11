---
title: Configurar políticas de proteção de conteúdo usando o portal do Azure | Microsoft Docs
description: Este artigo demonstra como usar o portal do Azure para configurar políticas de proteção de conteúdo. O artigo também mostra como habilitar a criptografia dinâmica para seus ativos.
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
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978207"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Configurar políticas de proteção de conteúdo usando o portal do Azure

> [!NOTE]
> Para concluir este tutorial, precisa de uma conta do Azure. Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).   > Não há novos recursos ou funcionalidades sendo adicionados aos serviços de mídia v2. <br/>Veja a versão mais recente, [Serviços de Multimédia v3](https://docs.microsoft.com/azure/media-services/latest/). Além disso, consulte [diretrizes de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)
>

 Com os serviços de mídia do Azure, você pode proteger sua mídia desde o momento em que ela deixa o computador por meio de armazenamento, processamento e entrega. Você pode usar os serviços de mídia para entregar o conteúdo criptografado dinamicamente com o criptografia AES (AES) usando chaves de criptografia de 128 bits. Você também pode usá-lo com CENC (criptografia comum) usando o PlayReady e/ou o Widevine DRM (gerenciamento de direitos digitais) e o Apple FairPlay. 

Os serviços de mídia fornecem um serviço para fornecer licenças DRM e chaves limpas AES para clientes autorizados. Você pode usar o portal do Azure para criar uma política de autorização de chave/licença para todos os tipos de criptografia.

Este artigo demonstra como configurar uma política de proteção de conteúdo usando o Portal. O artigo também mostra como aplicar a criptografia dinâmica aos seus ativos.

## <a name="start-to-configure-content-protection"></a>Começar a configurar a proteção de conteúdo
Para usar o portal para configurar a proteção de conteúdo global usando sua conta de serviços de mídia, execute as seguintes etapas:

1. No [portal](https://portal.azure.com/), selecione sua conta dos serviços de mídia.

1. Selecione **configurações** > **proteção de conteúdo**.

    ![Proteção de conteúdo](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Política de autorização de chave/licença
Os serviços de mídia dão suporte a várias maneiras de autenticar usuários que fazem solicitações de licença ou chave. Tem de configurar a política de autorização da chave de conteúdo. Em seguida, o cliente deve atender à política antes que a chave/licença possa ser entregue a ela. A política de autorização da chave de conteúdo pode ter uma ou mais restrições de autorização, quer sejam restrições abertas ou de token.

Você pode usar o portal para criar uma política de autorização de chave/licença para todos os tipos de criptografia.

### <a name="open-authorization"></a>Abrir autorização
Restrição aberta significa que o sistema entrega a chave para qualquer pessoa que faça uma solicitação de chave. Essa restrição pode ser útil para fins de teste. 

### <a name="token-authorization"></a>Autorização de token
A política de token restrito tem de ser acompanhada por um token emitido por um serviço de tokens seguro (STS). Os serviços de mídia oferecem suporte a tokens nos formatos Simple Web token (SWT) e token Web JSON (JWT). Os serviços de mídia não fornecem um STS. Você pode criar um STS personalizado ou usar o serviço de controle de acesso do Azure para emitir tokens. O STS deve ser configurado para criar um token assinado com as declarações de chave e o problema especificadas que especificou na configuração de restrição de token. Se o token for válido e as declarações no token corresponderem às configuradas para a chave (ou licença), o serviço de distribuição de chaves dos serviços de mídia retornará a chave (ou licença) solicitada ao cliente.

Ao configurar a política restrita por token, você deve especificar os parâmetros de chave de verificação primária, emissor e público. A chave de verificação primária contém a chave de que o token foi assinado com. O emissor é o serviço de token seguro que emite o token. O público (às vezes chamado de escopo) descreve a intenção do token ou o recurso ao qual o token autoriza o acesso. O serviço de entrega de chave de serviços de multimédia valida que estes valores no token correspondem aos valores no modelo.

![Política de autorização de chave/licença](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>Modelo de licença PlayReady
O modelo de licença do PlayReady define a funcionalidade que está habilitada em sua licença do PlayReady. Para obter mais informações sobre o modelo de licença do PlayReady, consulte a [visão geral do modelo de licença do PlayReady dos serviços de mídia](media-services-playready-license-template-overview.md).

### <a name="nonpersistent"></a>Não persistente
Se você configurar uma licença como não persistente, ela só será mantida na memória enquanto o Player usar a licença.  

![Proteção de conteúdo não persistente](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Persistente
Se você configurar uma licença como persistente, ela será salva no armazenamento persistente no cliente.

![Proteção de conteúdo persistente](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Modelo de licença do Widevine
O modelo de licença do Widevine define a funcionalidade que está habilitada em suas licenças do Widevine.

### <a name="basic"></a>Basic
Quando você seleciona **básico**, o modelo é criado com todos os valores padrão.

### <a name="advanced"></a>Segurança
Para obter mais informações sobre o modelo de direitos Widevine, consulte [visão geral do modelo de licença do Widevine](media-services-widevine-license-template-overview.md).

![Proteção avançada de conteúdo](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Configuração do FairPlay
Para habilitar a criptografia FairPlay, selecione **configuração do Fairplay**. Em seguida, selecione o **certificado** do aplicativo e insira a **chave secreta do aplicativo**. Para obter mais informações sobre a configuração e os requisitos do FairPlay, consulte [proteger o conteúdo do HLS com o Apple Fairplay ou o Microsoft PlayReady](media-services-protect-hls-with-FairPlay.md).

![Configuração do FairPlay](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Aplicar a criptografia dinâmica ao seu ativo
Para aproveitar a criptografia dinâmica, codifique o arquivo de origem em um conjunto de arquivos MP4 de taxa de bits adaptável.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Selecione um ativo que você deseja criptografar
Para ver todos os seus ativos, selecione **configurações** > **ativos**.

![Opção de ativos](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Criptografar com AES ou DRM
Ao selecionar **criptografar** para um ativo, você verá duas opções: **AES** ou **DRM**. 

#### <a name="aes"></a>AES
A criptografia de chave não criptografada AES está habilitada em todos os protocolos de streaming: Smooth Streaming, HLS e MPEG-DASH.

![Configuração de criptografia](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. Depois de selecionar o **DRM**, você verá diferentes políticas de proteção de conteúdo (que devem ser configuradas por esse ponto) e um conjunto de protocolos de streaming:

    a. **PlayReady e Widevine com MPEG-Dash** criptografa dinamicamente seu fluxo MPEG-Dash com PlayReady e Widevine DRMs.

    b. **PlayReady e Widevine com MPEG-Dash + Fairplay com HLS** criptografam dinamicamente seu fluxo MPEG-Dash com PlayReady e Widevine DRMs. Essa opção também criptografa os fluxos de HLS com FairPlay.

    c. O **PlayReady somente com Smooth streaming, HLS e MPEG-Dash** criptografa dinamicamente os fluxos de Smooth streaming, HLS e MPEG-Dash com o DRM do PlayReady.

    d. **Widevine somente com MPEG-Dash** dinamicamente CRIPTOGRAFA seu MPEG-Dash com Widevine DRM.
    
    e. **Fairplay somente com HLS** criptografa dinamicamente seu fluxo de HLS com fairplay.

1. Para habilitar a criptografia FairPlay, na folha **configurações globais do proteção de conteúdo** , selecione **configuração do Fairplay**. Em seguida, selecione o **certificado do aplicativo**e insira a **chave secreta do aplicativo**.

    ![Tipo de criptografia](./media/media-services-portal-content-protection/media-services-content-protection009.png)

1. Depois de fazer a seleção de criptografia, selecione **aplicar**.

>[!NOTE] 
>Se você planeja reproduzir um HLS criptografado por AES no Safari, consulte a postagem do blog [criptografada HLS no Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="additional-notes"></a>Notas adicionais

* O Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

