---
title: Criptografe conteúdo com o portal Azure
description: Este quickstart mostra-lhe como configurar encriptação para o seu conteúdo usando a Azure Media Services no portal Azure.
ms.topic: quickstart
ms.date: 08/31/2020
ms.openlocfilehash: 5ce942447f6d143116350940bda0e01a8b532d24
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277688"
---
# <a name="quickstart-use-portal-to-encrypt-content"></a>Quickstart: Use o portal para encriptar conteúdo

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Utilize a Azure Media Services para ajudar a proteger os seus meios de comunicação a partir do momento em que deixa o computador durante todo o processo de armazenamento, processamento e entrega. Com os Media Services, pode fornecer o seu conteúdo ao vivo e a pedido encriptado dinamicamente com o Advanced Encryption Standard (AES-128) ou qualquer um dos três principais sistemas de gestão de direitos digitais (DRM): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os Media Services também fornecem um serviço para a entrega de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) a clientes autorizados. 
 
Para especificar opções de encriptação (se houver) no seu stream, utilize uma **política de streaming** e associe-a ao seu localizador de streaming. Cria a **política de chave de conteúdo** para configurar como a chave de conteúdo (que fornece acesso seguro aos seus **ativos)** é entregue aos clientes finais. É necessário definir os requisitos (restrições) sobre a política de chave de conteúdo que deve ser cumprida para que as chaves com a configuração especificada sejam entregues aos clientes. 

> [!NOTE]
> A política-chave de conteúdo não é necessária para um streaming claro ou para o download.

Quando um stream é solicitado por um jogador, os Media Services utilizam a chave especificada para encriptar dinamicamente o seu conteúdo utilizando a chave clara AES ou encriptação DRM. Para desencriptar o fluxo, o leitor solicita a chave do serviço de entrega de chaves media ou do serviço de entrega chave especificado. Para decidir se o utilizador está autorizado a obter a chave, o serviço avalia a  **política de chave de conteúdo** que especificou para a chave.

Este quickstart mostra-lhe como criar uma política de chave de conteúdo onde especifica qual a encriptação que deve ser aplicada ao seu ativo quando é transmitida. O quickstart também mostra como definir a encriptação configurada no seu ativo.

### <a name="suggested-pre-reading"></a>Pré-leitura sugerida

* [Encriptação dinâmica e entrega de chaves](drm-content-protection-concept.md)
* [Localizadores de transmissão em fluxo](stream-streaming-locators-concept.md)
* [Políticas de transmissão em fluxo](stream-streaming-policy-concept.md)
* [Políticas de chave de conteúdo](drm-content-key-policy-concept.md)

## <a name="prerequisites"></a>Pré-requisitos

Faça upload e processo do seu conteúdo como descrito na [gestão de ativos no portal Azure](asset-create-asset-upload-portal-quickstart.md)

## <a name="create-a-content-key-policy"></a>Criar uma política chave de conteúdo

Crie a **política de chave de conteúdo** para configurar como a chave de conteúdo (que fornece acesso seguro aos seus **ativos)** é entregue aos clientes finais.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Localize e clique na sua conta de Serviços de Mídia.
1. Selecione **políticas-chave de conteúdo (novas)**.
1. Prima + Adicione a política da **tecla de conteúdo** na parte superior da janela. 

Aparece a janela **de política de chave de conteúdo Create a.** Nesta janela, você escolhe opções de encriptação. Pode optar por proteger os seus meios de comunicação, escolhendo a gestão de direitos digitais (DRM), o padrão avançado de encriptação (AES), ou ambos.  

![Criar uma política chave de conteúdo](./media/drm-encrypt-content-how-to/create-content-key-policy.png)

Quer escolha uma das opções de DRM ou uma opção chave clara AES-128, recomenda-se que especifique como pretende configurar restrições. Pode optar por ter uma restrição aberta ou simbólica. Para obter uma explicação detalhada, consulte [controlar o acesso ao conteúdo.](drm-content-protection-concept.md#controlling-content-access)

### <a name="add-a-drm-content-key"></a>Adicione uma chave de conteúdo DRM

Pode optar por proteger o seu conteúdo com o Microsoft PlayReady e/ou o Google Widevine ou o Apple FairPlay. Cada tipo de entrega de licença verificará as chaves de conteúdo com base nas suas credenciais num formato encriptado.

#### <a name="license-templates"></a>Modelos de licença

Para mais detalhes sobre os modelos de licença, consulte:

* [Modelo de licença do Google Widevine](drm-widevine-license-template-concept.md)

    > [!NOTE]
    > Pode criar um modelo de licença vazio sem valores, apenas {} " " Em seguida, um modelo de licença é criado com predefinições. O padrão funciona para a maioria dos casos.
* [Requisitos de licença e configuração do Apple FairPlay](drm-fairplay-license-overview.md)
* [Modelo de licença PlayReady](drm-playready-license-template-concept.md)

### <a name="add-aes-clear-key"></a>Adicione chave clara AES

Também pode adicionar uma encriptação de chave clara AES-128 ao seu conteúdo. A chave de conteúdo é transmitida ao cliente num formato não encriptado.

![Chave não encriptada AES](./media/drm-encrypt-content-how-to/aes-clear-key-policy.png)

## <a name="create-a-streaming-locator-for-your-asset"></a>Crie um localizador de streaming para o seu ativo

1. Localize e clique na sua conta de Serviços de Mídia.
1. Selecione **Ativos (novos)**.
1. Na lista de ativos, selecione o que pretende encriptar.  
1. Na secção **de localizador streaming** para o ativo selecionado, prima **+ Adicione um localizador de streaming**. 
1. Selecione uma **política de streaming** adequada para a política de chave de **conteúdo** que configura.

    O tópico [das políticas de streaming](stream-streaming-policy-concept.md) dá detalhes sobre qual a política de streaming que corresponde à política chave de conteúdo.
1. Assim que selecionar a política de streaming adequada, pode selecionar a política de chave de conteúdo da lista de drop-down.
1. Pressione **Adicione** para adicionar o localizador de streaming ao seu ativo.

    Isto publica o ativo e gera os URLs de streaming.

![Um localizador de streaming](./media/drm-encrypt-content-how-to/multi-drm.png)

## <a name="cleanup-resources"></a>Recursos de limpeza

Se pretende experimentar os outros quickstarts, deve manter os recursos criados. Caso contrário, vá ao portal Azure, navegue pelos seus grupos de recursos, selecione o grupo de recursos sob o qual executou este arranque rápido e elimine todos os recursos.

## <a name="next-steps"></a>Passos seguintes

[Gerir recursos](asset-create-asset-upload-portal-quickstart.md)
