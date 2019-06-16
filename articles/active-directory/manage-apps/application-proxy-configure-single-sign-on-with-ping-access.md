---
title: Autenticação baseada em cabeçalho com o PingAccess para o Proxy de aplicações do Azure AD | Documentos da Microsoft
description: Publica aplicações com o Proxy de aplicações e o PingAccess para suportar a autenticação com base no cabeçalho.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab08c93662988655154cf300ac4ee3758fbc7872
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66472798"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Autenticação baseada em cabeçalho para início de sessão único com o Proxy de aplicações e o PingAccess

Proxy de aplicações do Azure Active Directory (Azure AD) fez uma parceria com o PingAccess para que os clientes do Azure AD podem aceder a mais de seus aplicativos. PingAccess expande a [ofertas de Proxy de aplicações atuais](application-proxy.md) para incluir o acesso de início de sessão único para aplicações que utilizam cabeçalhos para autenticação.

## <a name="whats-pingaccess-for-azure-ad"></a>O que é o PingAccess para o Azure AD?

Com o PingAccess para o Azure AD, pode conceder acesso de utilizadores e início de sessão único (SSO) para aplicações que utilizam cabeçalhos para autenticação. Proxy de aplicações trata esses aplicativos, como qualquer outro, utilizar o Azure AD para autenticar o acesso e, em seguida, passar o tráfego através do serviço de conector. PingAccess encontra-se à frente dos aplicativos e traduz-se o token de acesso do Azure AD num cabeçalho. Em seguida, o aplicativo recebe a autenticação no formato pode ler.

Os usuários não notará nada diferente quando iniciam sessão para utilizar as suas aplicações empresariais. Pode ainda trabalham de qualquer lugar em qualquer dispositivo. Os conectores de Proxy de aplicações direcionar o tráfego remoto a todas as aplicações, independentemente do seu tipo de autenticação, para que eles irá equilibrar ainda cargas automaticamente.

## <a name="how-do-i-get-access"></a>Como posso obter acesso?

Uma vez que este cenário é proveniente de uma parceria entre o Azure Active Directory e o PingAccess, precisa de licenças para ambos os serviços. No entanto, as subscrições do Azure Active Directory Premium incluem uma licença de PingAccess básica, que abrange até 20 aplicações. Se precisar de mais de 20 aplicativos baseados no cabeçalho de publicar, pode comprar uma licença adicional do PingAccess.

Para obter mais informações, consulte [Edições do Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Publicar a sua aplicação no Azure

Este artigo é para as pessoas publicar uma aplicação com este cenário pela primeira vez. Além de detalhar os passos de publicação, ele orienta-o na introdução ao Proxy de aplicações e o PingAccess. Se já tiver configurado a ambos os serviços mas relembrar os passos de publicação, avance para o [adicionar a sua aplicação para o Azure AD com o Proxy de aplicações](#add-your-application-to-azure-ad-with-application-proxy) secção.

> [!NOTE]
> Uma vez que este cenário é uma parceria entre o Azure AD e o PingAccess, algumas das instruções existem no site da Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Instalar um conector de Proxy de aplicações

Se ativou o Proxy de aplicações ativada e já instaladas um conector, pode ignorar esta secção e aceda a [adicionar a sua aplicação para o Azure AD com o Proxy de aplicações](#add-your-application-to-azure-ad-with-application-proxy).

O conector do Proxy de aplicações é um serviço do Windows Server que direciona o tráfego dos funcionários para as aplicações publicadas. Para obter mais instruções de instalação, consulte [Tutorial: Adicionar uma aplicação no local para acesso remoto através do Proxy de aplicações no Azure Active Directory](application-proxy-add-on-premises-application.md).

1. Inicie sessão para o [portal do Azure Active Directory](https://aad.portal.azure.com/) como um administrador da aplicação. O **Centro de administração do Azure Active Directory** é apresentada a página.
2. Selecione **do Azure Active Directory** > **proxy de aplicações** > **transferir o conector serviço**. O **transferir do conector de Proxy da aplicação** é apresentada a página.

   ![Download do conector de proxy de aplicação](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)
3. Siga as instruções de instalação.

Transferir o conector automaticamente deve ativar Proxy de aplicações para o seu diretório, mas se não, pode selecionar **ativar o Proxy de aplicação**.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Adicionar a sua aplicação para o Azure AD com o Proxy de aplicações

Existem duas ações que necessárias no portal do Azure. Em primeiro lugar, terá de publicar seu aplicativo com o Proxy de aplicações. Em seguida, terá de recolher algumas informações sobre a aplicação que podem ser usados durante os passos do PingAccess.

#### <a name="publish-your-application"></a>Publicar a aplicação

Primeiro terá de publicar a sua aplicação. Esta ação envolve:

- A adicionar a sua aplicação no local para o Azure AD
- Atribuir um utilizador para o aplicativo de teste e escolhendo o SSO baseado em cabeçalho
- Configurar o URL de redirecionamento da aplicação
- Conceder permissões para utilizadores e outras aplicações para utilizar a aplicação no local

Para publicar a sua aplicação no local:

1. Se não soubéssemos na última seção, inicie sessão para o [portal do Azure Active Directory](https://aad.portal.azure.com/) como um administrador da aplicação.
2. Selecione **aplicações empresariais** > **novo aplicativo** > **aplicação no local**. O **adicionar sua própria aplicação no local** é apresentada a página.

   ![Adicionar a sua própria aplicação no local](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
3. Preencha os campos obrigatórios com informações sobre a sua nova aplicação. Utilize as orientações abaixo para as definições.

   > [!NOTE]
   > Para obter instruções mais detalhadas deste passo, consulte [adicionar uma aplicação no local ao Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).

   1. **URL interno**: Normalmente fornecer o URL que leva-o para a página de início de sessão da aplicação quando estiver na rede empresarial. Para este cenário, o conector tem de tratar o proxy do PingAccess como página frontal do aplicativo. Utilize este formato: `https://<host name of your PingAccess server>:<port>`. A porta é 3000 por predefinição, mas pode configurá-lo no PingAccess.

      > [!WARNING]
      > Para este tipo de início de sessão único, o URL interno tem de utilizar `https` e não é possível utilizar `http`.

   2. **Método de pré-autenticação**: Escolher **do Azure Active Directory**.
   3. **Traduzir URL nos cabeçalhos**: Escolher **não**.

   > [!NOTE]
   > Se esta for a sua primeira aplicação, utilize a porta 3000 para iniciar e voltar atrás para atualizar esta definição se alterar a configuração do PingAccess. Para aplicativos subsequentes, a porta terá de acordo com o serviço de escuta que configurou no PingAccess. Saiba mais sobre [serviços de escuta no PingAccess](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html).
4. Selecione **Adicionar**. É apresentada a página de descrição geral para a nova aplicação.

Agora, atribuir um utilizador de teste de aplicativos e escolher com base no cabeçalho de início de sessão único:

1. Na barra lateral do aplicativo, selecione **utilizadores e grupos** > **adicionar utilizador** > **utilizadores e grupos (\<número > selecionados)** . É apresentada uma lista de utilizadores e grupos para a sua escolha.

   ![Utilizadores e grupos](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)
2. Selecione um utilizador para teste de aplicativos e selecione **selecione**. Certifique-se de que esta conta de teste tenha acesso à aplicação no local.
3. Selecione **Atribuir**.
4. Na barra lateral do aplicativo, selecione **início de sessão único** > **baseada em cabeçalho**.

   > [!TIP]
   > Se esta for a primeira vez que utilizar com base no cabeçalho de início de sessão único, terá de instalar o PingAccess. Para certificar-se de que a sua subscrição do Azure é associada automaticamente a sua instalação do PingAccess, utilize a ligação nesta página de início de sessão único para transferir o PingAccess. Pode abrir o site de download agora ou voltar a esta página mais tarde.

   ![Com base no cabeçalho de início de sessão](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)
5. Selecione **Guardar**.

Em seguida, certifique-se de seu redirecionamento de que URL está definida como o URL externo:

1. Partir do **Centro de administração do Azure Active Directory** barra lateral, selecione **Azure Active Directory** > **registos das aplicações**. É apresentada uma lista de aplicativos.

   ![Registos de aplicações](./media/application-proxy-configure-single-sign-on-with-ping-access/app-registrations.png)
2. Selecione a aplicação.
3. Selecione a ligação junto a **URIs de redirecionamento**, que mostra o número de URIs definida para públicos clientes web e de redirecionamento. O  **\<nome da aplicação >-autenticação** é apresentada a página.
4. Verifique se o URL externo que atribuiu à anteriormente a sua aplicação está a **URIs de redirecionamento** lista. Se não estiver, adicione o URL externo agora, usando um tipo URI de redirecionamento de **Web**e selecione **guardar**.

Por fim, defina a sua aplicação no local, para que os utilizadores têm acesso de leitura e outras aplicações têm acesso de leitura/gravação:

1. Do **registos de aplicações** barra lateral para a sua aplicação, selecione **permissões de API** > **adicionar uma permissão**  >   **APIs da Microsoft** > **Microsoft Graph**. O **permissões de pedido de API** página **Microsoft Graph** for apresentada, que contém as APIs para o Windows Azure Active Directory.

   ![Permissões de pedido de API](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)
2. Selecione **permissões delegadas** > **utilizador** > **User.Read**.
3. Selecione **permissões de aplicação** > **aplicativo** > **Application.ReadWrite.All**.
4. Selecione **adicionar permissões**.
5. Na **permissões de API** página, selecione **conceder autorização de administrador para \<seu nome de diretório >** .

#### <a name="collect-information-for-the-pingaccess-steps"></a>Recolher informações para obter os passos do PingAccess

Tem de recolher estas três partes de informações (todas as GUIDs) para configurar a sua aplicação com PingAccess:

| Nome do campo do Azure AD | Nome do campo do PingAccess | Formato de dados |
| --- | --- | --- |
| **ID da aplicação (cliente)** | **ID de cliente** | GUID |
| **ID de diretório (inquilino)** | **Emissor** | GUID |
| `PingAccess key` | **Segredo do cliente** | Cadeia de caracteres aleatória |

Para recolher estas informações:

1. Partir do **Centro de administração do Azure Active Directory** barra lateral, selecione **Azure Active Directory** > **registos das aplicações**. É apresentada uma lista de aplicativos.
2. Selecione a aplicação. O **registos das aplicações** a página é apresentada a sua aplicação.

   ![Descrição geral do registo para uma aplicação](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)
3. Junto a **ID da aplicação (cliente)** valor, selecione a **copiar para área de transferência** ícone, em seguida, copie e guarde-o. Especificar este valor mais tarde como ID de cliente. do PingAccess
4. Próximo a **ID de diretório (inquilino)** valor, selecione também **copiar para área de transferência**, em seguida, copie e guarde-o. Especificar este valor mais tarde como o emissor do PingAccess.
5. Na barra lateral do **registos de aplicações** para a sua aplicação, selecione **certificados e segredos** > **novo segredo do cliente**. O **adicionar um segredo de cliente** é apresentada a página.

   ![Adicionar um segredo de cliente](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)
6. Na **Descrição**, tipo `PingAccess key`.
7. Sob **Expires**, selecione como definir a chave do PingAccess: **Dentro de 1 ano**, **dentro de 2 anos**, ou **Never**.
8. Selecione **Adicionar**. Os PingAccess chave é apresentado na tabela de segredos do cliente, com um aleatório de cadeias de caracteres que autofills no **valor** campo.
9. Ao lado da chave PingAccess **valor** campo, selecione a **copiar para área de transferência** ícone, em seguida, copie e guarde-o. Especificar este valor mais tarde como segredo do cliente do PingAccess.

### <a name="update-graphapi-to-send-custom-fields-optional"></a>Atualizar Graph para enviar os campos personalizados (opcional)

Se precisar de uma declaração personalizada que envia outros tokens dentro do access_token consumidos por PingAccess, defina o `acceptMappedClaims` campo de aplicativo para `True`. Pode utilizar o Explorador do gráfico ou manifesto da aplicação do portal do Azure AD para fazer esta alteração.

**Este exemplo utiliza a API do Graph:**

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application>

{
  "acceptMappedClaims":true
}
```

**Este exemplo utiliza a [portal do Azure Active Directory](https://aad.portal.azure.com/) para atualizar o `acceptMappedClaims` campo:**

1. Inicie sessão para o [portal do Azure Active Directory](https://aad.portal.azure.com/) como um administrador da aplicação.
2. Selecione **do Azure Active Directory** > **registos das aplicações**. É apresentada uma lista de aplicativos.
3. Selecione a aplicação.
4. Na barra lateral do **registos de aplicações** página para a sua aplicação, selecione **manifesto**. O código JSON manifesto para o registo da sua aplicação é apresentada.
5. Procure o `acceptMappedClaims` campo e altere o valor para `True`.
6. Selecione **Guardar**.


### <a name="use-of-optional-claims-optional"></a>Utilização de afirmações opcionais (opcionais)
Afirmações opcionais permite-lhe adicionar declarações standard-but-not-included-by-default que cada usuário e o inquilino tem. Pode configurar afirmações opcionais para a sua aplicação ao modificar o manifesto do aplicativo. Para obter mais informações, consulte o [Noções básicas sobre o artigo de manifesto de aplicação do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/)

Exemplo para incluir o endereço de e-mail para o access_token que PingAccess irá consumir:
```
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>Utilização de declarações de mapeamento de política (opcional)
[Mapeamento de política (pré-visualização) de afirmações](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties/) para atributos que não existem no AzureAD. Mapeamento de afirmações permite-lhe migrar aplicações antigas do local para a cloud ao adicionar declarações personalizadas adicionais que são apoiadas por seus objetos de AD FS ou o utilizador

Para tornar a sua aplicação utilizar uma declaração personalizada e incluir campos adicionais, não se esqueça de que também [criou uma política de mapeamento de afirmações personalizadas e Atribuímos à aplicação](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

> [!NOTE]
> Para utilizar uma declaração personalizada, também tem de ter uma política personalizada definida e atribuída à aplicação. Esta política deve incluir todos os atributos personalizados necessários.
>
> Pode fazer a definição de política e a atribuição através do PowerShell, o Azure AD Graph Explorer ou o Microsoft Graph. Se estiver fazendo-os no PowerShell, poderá ter de utilizar pela primeira vez `New-AzureADPolicy` e, em seguida, atribua-o para a aplicação com `Add-AzureADServicePrincipalPolicy`. Para obter mais informações, consulte [atribuição de política de mapeamento de afirmações](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

Exemplo:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id 
```

### <a name="enable-pingaccess-to-use-custom-claims-optional-but-required-if-you-expect-the-application-to-consume-additional-claims"></a>Ativar o PingAccess utilizar afirmações personalizadas (opcional mas obrigatório se esperar que o aplicativo consuma afirmações adicionais)
Quando irá configurar o PingAccess no passo seguinte, a sessão de Web, irá criar (definições -> acesso -> Web sessões) tem de ter **perfil pedido** desmarcada e **atualizar atributos de utilizador** definido como **não**

## <a name="download-pingaccess-and-configure-your-application"></a>Transfira o PingAccess e configurar a sua aplicação

Agora que concluiu todos os passos de configuração do Azure Active Directory, pode mover-se para configurar o PingAccess.

Os passos detalhados para a parte do PingAccess deste cenário continuam na documentação do Ping Identity. Siga as instruções em [configurar o PingAccess para o Azure AD para proteger as aplicações publicadas através do Proxy de aplicações do Microsoft Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html) no web site da Ping Identity.

Esses passos ajudam a instalar o PingAccess e configurar uma conta de PingAccess (se ainda não tiver uma). Em seguida, para criar uma ligação do Azure AD OpenID Connect (OIDC), configurar um fornecedor do token com o **ID de diretório (inquilino)** valor que copiou do portal do Azure AD. Em seguida, para criar uma sessão de web em PingAccess, utilize o **ID da aplicação (cliente)** e `PingAccess key` valores. Depois disso, pode configurar o mapeamento de identidades e criar um anfitrião virtual, o site e o aplicativo.

### <a name="test-your-application"></a>Testar a sua aplicação

Quando tiver concluído todas essas etapas, seu aplicativo deve estar em execução. Para testá-lo, abra um browser e navegue para o URL externo que criou quando publicado a aplicação no Azure. Inicie sessão com a conta de teste que atribuiu à aplicação.

## <a name="next-steps"></a>Passos Seguintes

- [Configurar o PingAccess para o Azure AD proteger as aplicações publicadas através do Proxy de aplicações do Microsoft Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [Início de sessão único para aplicações no Azure Active Directory](what-is-single-sign-on.md)
- [Resolver problemas do Proxy de aplicações e mensagens de erro](application-proxy-troubleshoot.md)
