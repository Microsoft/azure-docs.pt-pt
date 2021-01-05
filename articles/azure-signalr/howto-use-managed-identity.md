---
title: Identidades geridas no Serviço Azure SignalR
description: Saiba como as identidades geridas funcionam no Serviço Azure SignalR e como usar uma identidade gerida em cenários sem servidor.
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: 4f70cbacf686210c1188cb0a87e6116af8ed4b01
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763192"
---
# <a name="managed-identities-for-azure-signalr-service"></a>Identidades geridas para o Serviço Azure SignalR

Este artigo mostra-lhe como criar uma identidade gerida para o Serviço Azure SignalR e como usá-lo em cenários sem servidor.

> [!Important] 
> O Serviço Azure SignalR pode suportar apenas uma identidade gerida. Isto significa que pode adicionar uma identidade atribuída ao sistema ou uma identidade atribuída ao utilizador. 

## <a name="add-a-system-assigned-identity"></a>Adicionar uma identidade atribuída ao sistema

Para configurar uma identidade gerida no portal Azure, primeiro criará uma instância do Serviço Azure SignalR e, em seguida, ativará a funcionalidade.

1. Crie uma instância de Serviço Azure SignalR no portal como normalmente faria. Procure-o no portal.

2. Selecione **identidade**.

4. No separador **System atribuído,** **altere o Estado** para **ligar**. Selecione **Guardar**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="Adicionar uma identidade atribuída ao sistema no portal":::

## <a name="add-a-user-assigned-identity"></a>Adicionar uma identidade atribuída ao utilizador

A criação de uma instância do Serviço Azure SignalR com uma identidade atribuída ao utilizador requer que crie a identidade e adicione o seu identificador de recursos ao seu serviço.

1. Criar um recurso de identidade gerido atribuído pelo utilizador de acordo com [estas instruções](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Crie uma instância de Serviço Azure SignalR no portal como normalmente faria. Procure-o no portal.

3. Selecione **identidade**.

4. No **separador Utilizador atribuído,** selecione **Adicionar**.

5. Procure a identidade que criou anteriormente e selecione-a. Selecione **Add** (Adicionar).

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="Adicionar uma identidade atribuída ao utilizador no portal":::

## <a name="use-a-managed-identity-in-serverless-scenarios"></a>Use uma identidade gerida em cenários sem servidor

O Serviço Azure SignalR é um serviço totalmente gerido, pelo que não pode utilizar uma identidade gerida para obter fichas manualmente. Em vez disso, o Serviço Azure SignalR utiliza a identidade gerida que definiu para obter um token de acesso. Em seguida, o serviço define o token de acesso num `Authorization` cabeçalho num pedido a montante em cenários sem servidor.

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>Ativar a autenticação de identidade gerida em configurações a montante

1. Adicione uma identidade atribuída ao sistema ou identidade atribuída ao utilizador.

2. Adicione uma Configuração a montante e clique em qualquer asterisco para entrar numa página detalhada, como mostrado abaixo.
    :::image type="content" source="media/signalr-howto-use-managed-identity/pre-msi-settings.png" alt-text="pré-msi-definição":::
    
    :::image type="content" source="media/signalr-howto-use-managed-identity/msi-settings.png" alt-text="msi-definição":::

3. Nas definições de autenticação de identidade gerida, para **Recurso,** pode especificar o recurso-alvo. O recurso tornar-se-á uma `aud` reivindicação no token de acesso obtido, que pode ser usado como parte da validação nos seus pontos finais a montante. O recurso pode ser um dos seguintes:
    - Vazio
    - ID de aplicação (cliente) do principal serviço
    - ID URI de aplicação do diretor de serviço
    - [ID de recursos de um serviço Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > Se validar um token de acesso por si mesmo no seu serviço, pode escolher qualquer um dos formatos de recursos. Certifique-se apenas de que o valor **do recurso** nas definições **de Auth** e a validação são consistentes. Se utilizar o controlo de acesso baseado em funções (Azure RBAC) para um plano de dados, deve utilizar o recurso que o prestador de serviços solicita.

### <a name="validate-access-tokens"></a>Validar fichas de acesso

O símbolo no `Authorization` cabeçalho é um token de acesso à plataforma de identidade da [Microsoft.](../active-directory/develop/access-tokens.md#validating-tokens)

Para validar os tokens de acesso, a sua aplicação também deve validar o público e os tokens de assinatura. Estes devem ser validados contra os valores do documento de descoberta OpenID. Por exemplo, consulte a [versão independente do inquilina do documento.](https://login.microsoftonline.com/common/.well-known/openid-configuration)

O middleware Azure Ative Directory (Azure AD) tem capacidades incorporadas para validar fichas de acesso. Pode navegar pelas nossas [amostras](../active-directory/develop/sample-v2-code.md) para encontrar uma na linguagem à sua escolha.

Fornecemos bibliotecas e amostras de código que mostram como lidar com a validação de fichas. Existem também várias bibliotecas parceiras de código aberto disponíveis para validação JSON Web Token (JWT). Há pelo menos uma opção para quase todas as plataformas e idiomas lá fora. Para obter mais informações sobre bibliotecas de autenticação AD AZure e amostras de código, consulte [as bibliotecas de autenticação da plataforma de identidade da Microsoft.](../active-directory/develop/reference-v2-libraries.md)

#### <a name="authentication-in-function-app"></a>Autenticação na App de Função

Definir a validação de símbolos de acesso na App de Função é fácil e eficiente sem trabalhos de código.

1. Na página **autenticação/autorização,** altere a **autenticação do Serviço de Aplicação** para **On**.

2. Selecione **Iniciar sessão com o Azure Ative Directory** em **Ação a tomar quando o pedido não for autenticado**.

3. No Fornecedor de Autenticação, clique no **Diretório Ativo Azure**

4. Na nova página. Selecione **Express** e **crie nova app de AD** e, em seguida, clique em **OK** :::image type="content" source="media/signalr-howto-use-managed-identity/function-aad.png" alt-text="Function Aad":::

5. Navegue para o Serviço SignalR e siga [as etapas](howto-use-managed-identity.md#add-a-system-assigned-identity) para adicionar uma identidade ou identidade atribuída ao utilizador.

6. Entre nas **definições upstream** no Serviço SignalR e escolha **Use Identidade Gerida** e **Selecione das aplicações existentes.** Selecione a aplicação que criou anteriormente.

Após estas definições, a App de Função rejeitará pedidos sem um token de acesso no cabeçalho.

## <a name="use-a-managed-identity-for-key-vault-reference"></a>Use uma identidade gerida para referência do Cofre de Chaves

O Serviço SignalR pode aceder ao Key Vault para obter segredo usando a identidade gerida.

1. Adicione uma identidade atribuída ao sistema ou identidade atribuída ao utilizador para o Serviço Azure SignalR.

2. Grant Secret leia permissão para a identidade gerida nas políticas de acesso no Cofre de Chaves. Consulte [atribuir uma política de acesso ao Cofre de Chaves utilizando o portal Azure](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal)

Atualmente, esta funcionalidade pode ser utilizada nos seguintes cenários:

- [Segredo de referência no padrão de URL a montante](./concept-upstream.md#key-vault-secret-reference-in-url-template-settings)


## <a name="next-steps"></a>Passos seguintes

- [Desenvolvimento das Funções do Azure e configuração com o Azure SignalR Service](signalr-concept-serverless-development-config.md)