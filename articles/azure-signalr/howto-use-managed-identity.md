---
title: Identidades geridas no Serviço Azure SignalR
description: Saiba como as identidades geridas funcionam no Serviço Azure SignalR, como configurar para usar a identidade gerida em cenários sem servidor.
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: 8e122be74d623c7f1ea5576a5fe527dfa538d1d1
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988660"
---
# <a name="how-to-use-managed-identities-for-azure-signalr-service"></a>Como utilizar identidades geridas para o Serviço Azure SignalR

Este tópico mostra-lhe como criar uma identidade gerida para o Serviço Azure SignalR e como usá-lo em cenários sem servidor.

> [!Important] 
> O Serviço Azure SignalR só pode suportar uma identidade gerida. Isto significa que pode adicionar uma identidade atribuída ao sistema ou uma identidade atribuída ao utilizador. 

## <a name="add-a-system-assigned-identity"></a>Adicionar uma identidade atribuída ao sistema

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Para configurar uma identidade gerida no portal, irá primeiro criar um Serviço SignalR normalmente e depois ativar a funcionalidade.

1. Crie um Serviço SignalR no portal como normalmente faria. Navegue até ele no portal.

2. Selecione **identidade**.

4. Dentro do separador **Designado sistema,** **altere o Estado** para **ligar**. Clique em **Guardar**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="Adicionar identidade atribuída ao sistema no Portal":::

## <a name="add-a-user-assigned-identity"></a>Adicionar uma identidade atribuída ao utilizador

A criação de um Serviço SignalR com uma identidade atribuída ao utilizador requer que crie a identidade e adicione o seu identificador de recursos ao seu serviço.

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Primeiro, terá de criar um recurso de identidade atribuído ao utilizador.

1. Criar um recurso de identidade gerido atribuído pelo utilizador de acordo com [estas instruções](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Crie um Serviço SignalR no portal como normalmente faria. Navegue até ele no portal.

3. Selecione **identidade**.

4. Dentro do **separador Utilizador atribuído,** clique em **Adicionar**.

5. Procure a identidade que criou anteriormente e selecione-a. Clique em **Adicionar**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="Adicionar identidade assigened ao utilizador no Portal":::

## <a name="use-managed-identity-in-serverless-scenarios"></a>Use identidade gerida em cenários sem servidor

Como o Serviço SignalR é um serviço totalmente gerido, não é possível utilizar a identidade gerida para obter fichas manualmente. Em vez disso, o Serviço SignalR utiliza a identidade gerida que definiu para obter o token de acesso e configurar para `Authorization` o cabeçalho num pedido a montante em cenários sem servidor.

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>Ativar a autenticação de identidade gerida nas definições a montante

1. Adicionar uma identidade atribuída ao sistema ou identidade atribuída ao utilizador

2. Configure as definições a montante e utilize *a ManagedIdentity* como as definições *Auth.* Encontre como criar configurações a montante com autenticação nas [definições upstream](concept-upstream.md#create-upstream-settings).

3. Nas definições de auth de identidade gerida, pode especificar o *recurso-alvo*. O *Recurso* tornar-se-á `aud` reivindicação no token de acesso obtido, que pode ser usado como parte da validação nos seus pontos finais a montante. O *Recurso* pode ser um dos seguintes
    - Vazio
    - ID de aplicação (cliente) do principal serviço
    - ID URI de aplicação do diretor de serviço
    - IDs de recursos dos serviços Azure, ver [ID de Recursos dos Serviços Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > Se validar o acesso por si mesmo no seu serviço, pode escolher qualquer um do formato *Recursos.* Desde que certifique-se de que o *recurso* nas definições *de Auth* e a validação são consistentes. Se utilizar o Data Plane RBAC, deve utilizar o *Recurso* que o prestador de serviços solicita.

### <a name="validate-access-token"></a>Validar o token de acesso

O token no `Authorization` cabeçalho é um [token de acesso à plataforma de identidade da Microsoft.](https://docs.microsoft.com/azure/active-directory/develop/access-tokens#validating-tokens)

Para validar os tokens de acesso, a sua aplicação também deve validar o público e os tokens de assinatura. Estes devem ser validados contra os valores do documento de descoberta OpenID. Por exemplo, a versão independente do inquilina do documento situa-se em [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration) .

O middleware AD AD Azure tem capacidades incorporadas para validar tokens de acesso, e você pode navegar através das nossas [amostras](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code) para encontrar um no idioma à sua escolha.

Fornecemos bibliotecas e amostras de código que mostram como lidar com a validação de fichas. A informação abaixo é fornecida para aqueles que desejam compreender o processo subjacente. Existem também várias bibliotecas de código aberto de terceiros disponíveis para validação JWT - há pelo menos uma opção para quase todas as plataformas e idiomas lá fora. Para obter mais informações sobre bibliotecas de autenticação AD Azure e amostras de código, consulte [as bibliotecas de autenticação V2.0](https://docs.microsoft.com/azure/active-directory/develop/reference-v2-libraries).

## <a name="next-steps"></a>Passos seguintes

- [Desenvolvimento das Funções do Azure e configuração com o Azure SignalR Service](signalr-concept-serverless-development-config.md)
