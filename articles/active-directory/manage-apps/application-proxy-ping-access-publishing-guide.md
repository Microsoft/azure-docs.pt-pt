---
title: Autenticação baseada em cabeçalho com PingAccess para Proxy de Aplicação AD Azure
description: Publique aplicações com PingAccess e App Proxy para suportar a autenticação baseada em cabeçalho.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: e09bb0b07112a962b709c380c48f2a656c16097b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663727"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Autenticação baseada em cabeçalho para início de sessão único com o Proxy de Aplicações e PingAccess

Azure Ative Directory (Azure AD) Application Proxy estabeleceu uma parceria com o PingAccess para que os seus clientes AD Azure possam aceder a mais das suas aplicações. O PingAccess oferece outra opção para além do único sinal integrado [baseado no cabeçalho](application-proxy-configure-single-sign-on-with-headers.md).

## <a name="whats-pingaccess-for-azure-ad"></a>O que é PingAccess para Azure AD?

Com o PingAccess para Azure AD, pode dar aos utilizadores acesso e uma única sinteção (SSO) a aplicações que utilizam cabeçalhos para autenticação. A Application Proxy trata estas aplicações como qualquer outra, utilizando o Azure AD para autenticar o acesso e, em seguida, passar o tráfego através do serviço de conector. O PingAccess senta-se em frente às aplicações e traduz o token de acesso do Azure AD num cabeçalho. A aplicação recebe então a autenticação no formato que pode ler.

Os seus utilizadores não notarão nada de diferente quando iniciarem saturação para utilizarem as suas aplicações corporativas. Ainda podem trabalhar em qualquer lugar em qualquer dispositivo. Os conectores Proxy da Aplicação direcionam o tráfego remoto para todas as aplicações sem considerar o seu tipo de autenticação, pelo que continuarão a equilibrar as cargas automaticamente.

## <a name="how-do-i-get-access"></a>Como posso ter acesso?

Uma vez que este cenário provém de uma parceria entre o Azure Ative Directory e o PingAccess, precisa de licenças para ambos os serviços. No entanto, as subscrições Azure Ative Directory Premium incluem uma licença básica de PingAccess que cobre até 20 aplicações. Se precisar de publicar mais de 20 aplicações baseadas em cabeçalhos, pode adquirir uma licença adicional ao PingAccess.

Para mais informações, consulte [as edições do Azure Ative Directory.](../fundamentals/active-directory-whatis.md)

## <a name="publish-your-application-in-azure"></a>Publique a sua candidatura no Azure

Este artigo é para que as pessoas publiquem uma aplicação com este cenário pela primeira vez. Além de detalhar os passos de publicação, guia-o a começar com o Application Proxy e o PingAccess. Se já configurar ambos os serviços mas quiser um atualização nas escadas de publicação, salte para a Azure AD com a secção [De Procuração de Aplicações.](#add-your-application-to-azure-ad-with-application-proxy)

> [!NOTE]
> Uma vez que este cenário é uma parceria entre a Azure AD e o PingAccess, algumas das instruções existem no site Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Instale um conector Proxy de aplicação

Se já ativou o Proxy da Aplicação ativado e instalado um conector, pode saltar esta secção e ir para [Adicionar a sua aplicação ao Azure AD com Aplicação Proxy](#add-your-application-to-azure-ad-with-application-proxy).

O conector Application Proxy é um serviço windows Server que direciona o tráfego dos seus funcionários remotos para as suas aplicações publicadas. Para obter instruções de instalação mais detalhadas, consulte [Tutorial: Adicione um pedido de acesso remoto no local através do Application Proxy in Azure Ative Directory](application-proxy-add-on-premises-application.md).

1. Inscreva-se no [portal Azure Ative Directory](https://aad.portal.azure.com/) como administrador de aplicação. Aparece a página **do centro de administração Azure Ative Directory.**
1. Selecione **Azure Ative Directory**  >  **Application proxy**  >  **Serviço de conector de descarregamento**. Aparece a página **de Download do Conector de Aplicação Proxy.**

   ![Download do conector de procuração de aplicação](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Siga as instruções de instalação.

O download do conector deve ativar automaticamente o Application Proxy para o seu diretório, mas se não o fizer, pode selecionar **Enable Application Proxy**.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Adicione a sua aplicação ao Azure AD com Proxy de aplicação

Há duas ações que precisa de tomar no portal Azure. Em primeiro lugar, tem de publicar a sua candidatura com o Application Proxy. Em seguida, precisa recolher algumas informações sobre a aplicação que pode usar durante os passos do PingAccess.

#### <a name="publish-your-application"></a>Publicar a aplicação

Primeiro terá de publicar a sua candidatura. Esta ação envolve:

- Adicionar a sua aplicação no local à Azure AD
- Atribuir um utilizador para testar a aplicação e escolher SSO baseado em cabeçalho
- Configuração do URL de redirecionamento da aplicação
- Concessão de permissões para utilizadores e outras aplicações para utilizar a sua aplicação no local

Para publicar a sua própria aplicação no local:

1. Se não o fez na última secção, inscreva-se no [portal Azure Ative Directory](https://aad.portal.azure.com/) como administrador de candidatura.
1. Selecione **aplicações da Empresa**  >  **Nova aplicação**  >  **Adicione uma aplicação no local**. Aparece a sua própria página **de aplicação no local.**

   ![Adicione a sua própria aplicação no local](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Preencha os campos necessários com informações sobre a sua nova aplicação. Utilize as orientações abaixo para as definições.

   > [!NOTE]
   > Para uma passagem mais detalhada deste passo, consulte [adicionar uma aplicação no local ao Azure AD.](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)

   1. **URL interno**: Normalmente fornece o URL que o leva à página de sindução da aplicação quando está na rede corporativa. Para este cenário, o conector precisa de tratar o proxy pingAccess como a primeira página da aplicação. Utilize este formato: `https://<host name of your PingAccess server>:<port>` . A porta é 3000 por defeito, mas pode configugá-la em PingAccess.

      > [!WARNING]
      > Para este tipo de inscrição única, o URL interno deve ser utilizado `https` e não pode ser utilizado `http` . Além disso, existe um constrangimento ao configurar uma aplicação que não deve ter duas aplicações que esta permite que o App Proxy mantenha a distinção entre aplicações.

   1. **Método de pré-autenticação**: Escolha **o Diretório Ativo Azure**.
   1. **Traduzir URL em cabeçalhos**: Escolha **não**.

   > [!NOTE]
   > Se esta for a sua primeira aplicação, use a porta 3000 para iniciar e volte a atualizar esta definição se alterar a configuração do PingAccess. Para aplicações posteriores, a porta terá de corresponder ao Ouvinte que configuraste no PingAccess. Saiba mais sobre [os ouvintes no PingAccess.](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html)

1. Selecione **Adicionar**. A página geral da nova aplicação aparece.

Agora atribua um utilizador para testes de aplicação e escolha um único sinal baseado no cabeçalho:

1. A partir da barra lateral da aplicação, selecione **Utilizadores e grupos**  >  **Adicione**  >  **utilizadores e grupos \<Number> (Selecionados)**. Uma lista de utilizadores e grupos aparece para você escolher.

   ![Mostra a lista de utilizadores e grupos](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Selecione um utilizador para testes de aplicação e **selecione Select**. Certifique-se de que esta conta de teste tem acesso à aplicação no local.
1. Selecione **Atribuir**.
1. A partir da barra lateral da aplicação, selecione **Único**  >  **cabeçalho baseado em sinalização**.

   > [!TIP]
   > Se esta for a sua primeira vez usando um único sinal de cabeçalho, tem de instalar o PingAccess. Para se certificar de que a subscrição do Azure está automaticamente associada à sua instalação PingAccess, utilize o link nesta página de inscrição única para descarregar PingAccess. Pode abrir o site de descarregamento agora, ou voltar a esta página mais tarde.

   ![Mostra o ecrã de sinalização baseado no cabeçalho e o PingAccess](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Selecione **Guardar**.

Em seguida, certifique-se de que o url de redirecionamento está definido para o seu URL externo:

1. A partir da barra lateral do centro do **diretório Azure Ative,** selecione as inscrições da **App Azure Ative Directory**  >  **App registrations**. Aparece uma lista de candidaturas.
1. Selecione a sua candidatura.
1. Selecione o link ao lado **de Redirecionar URIs,** mostrando o número de URIs de redirecionamento configurado para clientes web e público. A página **\<application name> - Autenticação** aparece.
1. Verifique se o URL externo que atribuiu à sua aplicação anteriormente está na lista **de URIs de redirecionamento.** Se não for, adicione agora o URL externo, utilizando um tipo URI de redirecionamento de **Web,** e selecione **Save**.

Para além do URL externo, deve ser adicionado um ponto final autorizado do Azure Ative Directory no URL externo à lista de URIs de redirecionamento.

`https://*.msappproxy.net/pa/oidc/cb`
`https://*.msappproxy.net/`

Por fim, crie a sua aplicação no local para que os utilizadores tenham lido o acesso e outras aplicações tenham acesso/leitura/ escrita:

1. A partir da barra lateral de **registos** da App para a sua aplicação, selecione **permissões API**  >  **Adicione uma permissão**  >  **Microsoft APIs** Microsoft  >  **Graph**. Aparece a página **de permissões API do Pedido** para o Microsoft **Graph,** que contém as APIs para o Windows Azure Ative Directory.

   ![Mostra a página de permissões da API do Pedido](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Selecione **permissões delegadas**  >  **User**  >  **Utilizador.Ler**.
1. **Selecione Aplicação de permissões** de  >  **aplicação.ReadWrite.All**  >  **Application.ReadWrite.All**.
1. **Selecione Permissões de adicionar**.
1. Na página de permissões da **API,** selecione **o consentimento de administração grant para \<your directory name>**.

#### <a name="collect-information-for-the-pingaccess-steps"></a>Recolher informações para os passos do PingAccess

Você precisa recolher estas três peças de informação (todos GUIDs) para configurar a sua aplicação com PingAccess:

| Nome do campo AD AZure | Nome do campo PingAccess | Formato de dados |
| --- | --- | --- |
| **ID da Aplicação (cliente)** | **ID do cliente** | GUID |
| **ID do Diretório (inquilino)** | **Emitente** | GUID |
| `PingAccess key` | **Segredo do Cliente** | Corda aleatória |

Para recolher esta informação:

1. A partir da barra lateral do centro do **diretório Azure Ative,** selecione as inscrições da **App Azure Ative Directory**  >  **App registrations**. Aparece uma lista de candidaturas.
1. Selecione a sua candidatura. Aparece a página **de registos da App** para a sua aplicação.

   ![Visão geral do registo de um pedido](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Ao lado do valor **de ID da Aplicação (cliente),** selecione a Cópia para ícone **de área de transferência** e, em seguida, copie e guarde-a. Especifica este valor mais tarde como ID do cliente do PingAccess.
1. Em seguida, o valor **de ID do Diretório (inquilino),** selecione **copy to clipboard**, em seguida, copie e guarde-o. Especifica este valor mais tarde como emitente do PingAccess.
1. A partir da barra lateral das **inscrições** da App para a sua aplicação, selecione **Certificados e segredos**  >  **Novo segredo do cliente.** Aparece a página **secreta do Add a Client.**

   ![Mostra a página secreta do Add a Client](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. Em **Descrição,** tipo `PingAccess key` .
1. Em **Expira,** escolha como definir a chave PingAccess: **Em 1 ano,** **em 2 anos,** ou **Nunca**.
1. Selecione **Adicionar**. A chave PingAccess aparece na tabela de segredos dos clientes, com uma cadeia aleatória que enche automaticamente no campo **VALUE.**
1. Ao lado do campo **VALUE** da tecla PingAccess, selecione a Cópia para o ícone **da área de transferência** e, em seguida, copie e guarde-a. Especifica este valor mais tarde como segredo de cliente do PingAccess.

**Atualizar o `acceptMappedClaims` campo:**

1. Inscreva-se no [portal Azure Ative Directory](https://aad.portal.azure.com/) como administrador de aplicação.
1. Selecione as inscrições da **App Azure Ative**  >  **Directory**. Aparece uma lista de candidaturas.
1. Selecione a sua candidatura.
1. A partir da barra lateral da página de **registos** da App para a sua aplicação, selecione **Manifesto**. Aparece o código JSON manifesto para o registo do seu pedido.
1. Procure o `acceptMappedClaims` campo e altere o valor para `True` .
1. Selecione **Guardar**.

### <a name="use-of-optional-claims-optional"></a>Utilização de reclamações opcionais (opcional)

Reclamações opcionais permitem-lhe adicionar alegações padrão, mas não incluídas por padrão, que cada utilizador e inquilino tem. Pode configurar reclamações opcionais para a sua aplicação modificando o manifesto de aplicação. Para mais informações, consulte o [Artigo manifesto de aplicação AD Azure](../develop/reference-app-manifest.md)

Exemplo para incluir o endereço de e-mail no access_token que o PingAccess irá consumir:
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

### <a name="use-of-claims-mapping-policy-optional"></a>Utilização da política de mapeamento de sinistros (opcional)

[Política de Mapeamento de Reclamações (pré-visualização)](../develop/active-directory-claims-mapping.md#claims-mapping-policy-properties) para atributos que não existem no AzureAD. O mapeamento de reclamações permite migrar aplicações antigas on-prem para a nuvem adicionando alegações personalizadas adicionais que são apoiadas pelo seu ADFS ou objetos de utilizador

Para fazer com que a sua aplicação utilize uma reclamação personalizada e inclua campos adicionais, certifique-se de que também [criou uma política de mapeamento de reclamações personalizada e a atribuiu à aplicação.](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)

> [!NOTE]
> Para utilizar uma reclamação personalizada, também deve ter uma política personalizada definida e atribuída à aplicação. Esta política deve incluir todos os atributos personalizados necessários.
>
> Pode fazer definição de política e atribuição através do PowerShell ou do Microsoft Graph. Se estiver a fazê-las no PowerShell, poderá ter de ser usada primeiro `New-AzureADPolicy` e depois atribuí-la à aplicação com `Add-AzureADServicePrincipalPolicy` . Para obter mais informações, consulte [a atribuição de política de mapeamento de Sinistros](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

Exemplo:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Permitir que o PingAccess utilize reclamações personalizadas

Permitir que o PingAccess utilize reclamações personalizadas é opcional, mas é necessário se espera que a aplicação consuma reclamações adicionais.

Quando configurar o PingAccess no passo seguinte, a Sessão Web que irá criar (Definições >Sessões Web de acesso >) deve ter **o Perfil** de Pedido desescolhido e atualizar **os atributos do utilizador** definidos para **Não**

## <a name="download-pingaccess-and-configure-your-application"></a>Baixe o PingAccess e configuure a sua aplicação

Agora que completou todos os passos de configuração do Azure Ative Directory, pode passar à configuração do PingAccess.

Os passos detalhados para a parte do PingAccess deste cenário continuam na documentação da Identidade ping. Siga as instruções em [Configure PingAccess para Azure AD para proteger as aplicações publicadas usando o Microsoft Azure AD Application Proxy](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) no site ping Identity e descarregue a [versão mais recente do PingAccess](https://www.pingidentity.com/en/lp/azure-download.html?).

Esses passos ajudam-no a instalar o PingAccess e a criar uma conta PingAccess (se ainda não tiver uma). Em seguida, para criar uma ligação Azure AD OpenID Connect (OIDC), criou um fornecedor simbólico com o valor de ID do **Diretório (inquilino)** que copiou do portal AD Azure. Em seguida, para criar uma sessão web no PingAccess, você usa o **ID de aplicação (cliente)** e `PingAccess key` valores. Depois disso, pode configurar o mapeamento de identidade e criar um anfitrião virtual, site e aplicação.

### <a name="test-your-application"></a>Teste a sua aplicação

Quando completar todos estes passos, a sua candidatura deve estar a funcionar. Para testá-lo, abra um navegador e navegue para o URL externo que criou quando publicou a aplicação no Azure. Inscreva-se na conta de teste que atribuiu à aplicação.

## <a name="next-steps"></a>Próximos passos

- [Configure PingAccess para Azure AD para proteger aplicações publicadas usando o Microsoft Azure AD Application Proxy](https://docs.pingidentity.com/bundle/pingaccess-60/page/jep1564006742933.html)
- [Inscrição única para aplicações no Azure Ative Directory](what-is-single-sign-on.md)
- [Resolver problemas do Proxy de Aplicações e as mensagens de erro](application-proxy-troubleshoot.md)