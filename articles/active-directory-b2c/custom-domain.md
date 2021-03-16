---
title: Ativar domínios personalizados AD B2C Azure
titleSuffix: Azure AD B2C
description: Saiba como ativar domínios personalizados nos urls de redirecionamento para O Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 869bd7b02186873f490d324cec863c7f26ee8469
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555449"
---
# <a name="enable-custom-domains-for-azure-active-directory-b2c"></a>Ativar domínios personalizados para o Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Este artigo descreve como ativar domínios personalizados nos seus URLs de redirecionamento para Azure Ative Directory B2C (Azure AD B2C). A utilização de um domínio personalizado com a sua aplicação proporciona uma experiência de utilizador mais perfeita. Do ponto de vista do utilizador, permanecem no seu domínio durante o processo de inscrição em vez de redirecionar para o domínio padrão Azure AD B2C *<nome do inquilino>.b2clogin.com*.

![Experiência personalizada do utilizador do domínio](./media/custom-domain/custom-domain-user-experience.png)

## <a name="custom-domain-overview"></a>Visão geral do domínio personalizado

Pode ativar domínios personalizados para Azure AD B2C utilizando [a porta frontal Azure](https://azure.microsoft.com/services/frontdoor/). Azure Front Door é um ponto de entrada global e escalável que utiliza a rede de arestas globais da Microsoft para criar aplicações web rápidas, seguras e amplamente escaláveis. Pode renderizar o conteúdo Azure AD B2C atrás da Porta Frontal Azure e, em seguida, configurar uma opção na Porta Frontal Azure para entregar o conteúdo através de um domínio personalizado no URL da sua aplicação.

O seguinte diagrama ilustra a integração da Porta frontal Azure:

1. A partir de uma aplicação, um utilizador clica no botão de iniciar sção, que os leva para a página de sinstrução AD B2C do Azure. Esta página especifica um nome de domínio personalizado.
1. O navegador web resolve o nome de domínio personalizado para o endereço IP da porta frontal Azure. Durante a resolução DO DNS, um registo de nome canónico (CNAME) com um nome de domínio personalizado aponta para o anfitrião frontal padrão da porta frontal (por exemplo, `contoso.azurefd.net` ). 
1. O tráfego endereçado ao domínio personalizado (por exemplo, `login.contoso.com` ) é encaminhado para o anfitrião frontal padrão especificado `contoso.azurefd.net` ().
1. A porta frontal Azure invoca o conteúdo Azure AD B2C utilizando o domínio padrão Azure AD `<tenant-name>.b2clogin.com` B2C. O pedido para o ponto final Azure AD B2C inclui um cabeçalho HTTP personalizado que contém o nome de domínio personalizado original.
1. AZure AD B2C responde ao pedido exibindo o conteúdo relevante e o domínio personalizado original.

![Diagrama de rede de domínio personalizado](./media/custom-domain/custom-domain-network-flow.png)

> [!IMPORTANT]
> A ligação do navegador à Porta Frontal Azure deve sempre utilizar o IPv4 em vez do IPv6.

Ao utilizar domínios personalizados, considere o seguinte:

- Pode configurar vários domínios personalizados. Para o número máximo de domínios personalizados suportados, consulte [os limites de serviço AZure AD e as restrições](../active-directory/enterprise-users/directory-service-limits-restrictions.md) para a assinatura AD B2C e [Azure e limites de serviço, quotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits) para a Porta Frontal Azure.
- A Azure Front Door é um serviço Azure separado, por isso serão incorridos custos adicionais. Para mais informações, consulte [os preços da Porta Frontal.](https://azure.microsoft.com/pricing/details/frontdoor)
- Atualmente, a funcionalidade Azure Front Door [Web Application Firewall](../web-application-firewall/afds/afds-overview.md) não é suportada.
- Depois de configurar domínios personalizados, os utilizadores ainda poderão aceder ao nome de domínio padrão AZure AD B2C *<nome de inquilino>.b2clogin.com* (a menos que esteja a usar uma política personalizada e [bloqueie](#block-access-to-the-default-domain-name)o acesso .
- Se tiver várias aplicações, migrar todas para o domínio personalizado porque o navegador armazena a sessão Azure AD B2C sob o nome de domínio que está a ser utilizado.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="add-a-custom-domain-name-to-your-tenant"></a>Adicione um nome de domínio personalizado ao seu inquilino

Siga as orientações para como [adicionar e validar o seu domínio personalizado em Azure AD](../active-directory/fundamentals/add-custom-domain.md). Depois de verificado o domínio, elimine o registo DNS TXT que criou.

> [!IMPORTANT]
> Para estes passos, certifique-se de que se inscreve no seu inquilino **Azure AD B2C** e selecione o serviço **de Diretório Ativo Azure.**

Verifique cada subdomínio que pretende utilizar. Verificar apenas o domínio de nível superior não é suficiente. Por exemplo, para poder iniciar sação com *login.contoso.com* e *account.contoso.com,* é necessário verificar tanto os subdomínios como não apenas o domínio de nível superior *contoso.com*.  

## <a name="create-a-new-azure-front-door-instance"></a>Criar uma nova instância da Porta frontal Azure

Siga os passos para [criar uma Porta frontal para a sua aplicação](../frontdoor/quickstart-create-front-door.md#create-a-front-door-for-your-application) utilizando as definições predefinidos para as regras de anfitrião frontal e encaminhamento. 

> [!IMPORTANT]
> Para estes passos, depois de iniciar seduca no portal Azure no passo 1, selecione **Diretório + subscrição** e escolha o diretório que contém a subscrição Azure que gostaria de usar para Azure Front Door. Este *não* deve ser o diretório contendo o seu inquilino Azure AD B2C. 

No passo **Adicione um backend,** utilize as seguintes definições:

* Para **o tipo de anfitrião Backend**, selecione **Anfitrião Personalizado**.  
* Para **o nome do anfitrião Backend**, selecione o nome de anfitrião para o seu ponto final Azure AD B2C, <nome de inquilino>.b2clogin.com. Por exemplo, contoso.b2clogin.com. 
* Para **o cabeçalho do anfitrião Backend**, selecione o mesmo valor que selecionou para **o nome de anfitrião backend**.

![Adicione um backend](./media/custom-domain/add-a-backend.png)

Depois de adicionar o **backend** à **piscina de backend,** desative as **sondas Health**.

![Adicionar um conjunto de back-end](./media/custom-domain/add-a-backend-pool.png)

## <a name="set-up-your-custom-domain-on-azure-front-door"></a>Confiúnio do seu domínio personalizado na Porta frontal Azure

Siga os passos para [adicionar um domínio personalizado à sua Porta frontal](../frontdoor/front-door-custom-domain.md). Ao criar o registo para o `CNAME` seu domínio personalizado, utilize o nome de domínio personalizado que verificou anteriormente no Adicionar um nome de domínio personalizado ao seu passo [AD Azure.](#add-a-custom-domain-name-to-your-tenant) 

Depois de verificado o nome de domínio personalizado, selecione **o nome de domínio personalizado HTTPS**. Em seguida, sob o **tipo de gestão de certificados,** selecione [gestão da porta frontal,](../frontdoor/front-door-custom-domain-https.md#option-1-default-use-a-certificate-managed-by-front-door)ou [use o meu próprio certificado.](../frontdoor/front-door-custom-domain-https.md#option-2-use-your-own-certificate) 

A imagem que se segue mostra como adicionar um domínio personalizado e ativar HTTPS utilizando um certificado Azure Front Door.

![Configurar o domínio personalizado da Porta frontal Azure](./media/custom-domain/azure-front-door-add-custom-domain.png) 

## <a name="configure-cors"></a>Configurar o CORS

Se [personalizar a interface de utilizador Azure AD B2C](customize-ui-with-html.md) com um modelo HTML, precisa de [configurar o CORS](customize-ui-with-html.md?pivots=b2c-user-flow.md#3-configure-cors) com o seu domínio personalizado.

Configure o armazenamento da Blob Azure para partilha de recursos de origem cruzada com os seguintes passos:

1. No [portal do Azure](https://portal.azure.com), navegue para a sua conta de armazenamento.
1. No menu, selecione **CORS**.
1. Para **origens permitidas,** insira `https://your-domain-name` . `your-domain-name`Substitua-o pelo seu nome de domínio. Por exemplo, `https://login.contoso.com`. Use todas as letras minúsculas ao inserir o nome do seu inquilino.
1. Para **Métodos Permitidos**, selecione ambos `GET` e `OPTIONS` .
1. Para **cabeçalhos permitidos,** introduza um asterisco (*).
1. Para **cabeçalhos expostos,** introduza um asterisco (*).
1. Para **a idade máxima,** insira 200.
1. Selecione **Guardar**.

## <a name="configure-your-identity-provider"></a>Configure o seu fornecedor de identidade

Quando um utilizador opta por fazer o seu contrato com um fornecedor de identidade social, o Azure AD B2C inicia um pedido de autorização e leva o utilizador ao fornecedor de identidade selecionado para completar o processo de inscrição. O pedido de autorização especifica o `redirect_uri` nome de domínio padrão Azure AD B2C: 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>/oauth2/authresp
```

Se configurar a sua política para permitir a sua inscrição com um fornecedor de identidade externo, atualize os URIs redirecionados de OAuth com o domínio personalizado. A maioria dos fornecedores de identidade permite-lhe registar URIs de redirecionamento múltiplo. Recomendamos adicionar URIs de redirecionamento em vez de substituí-los para que possa testar a sua política personalizada sem afetar aplicações que usam o nome de domínio padrão Azure AD B2C. 

No seguinte redireccionamento URI:

```http
https://<custom-domain-name>/<tenant-name>/oauth2/authresp
``` 

- Substitua **<>de nome de domínio personalizado** pelo seu nome de domínio personalizado.
- Substitua **<>pelo** nome do seu inquilino, ou pela identificação do seu inquilino.

O exemplo a seguir mostra um URI de redirecionamento válido da OAuth:

```http
https://login.contoso.com/contoso.onmicrosoft.com/oauth2/authresp
```

Se optar por utilizar o ID do [inquilino,](#optional-use-tenant-id)um URI de redirecionamento de OAuth válido seria o seguinte:

```http
https://login.contoso.com/11111111-1111-1111-1111-111111111111/oauth2/authresp
```

Os [metadados dos fornecedores de identidade SAML](saml-identity-provider-technical-profile.md) seriam os seguintes:

```http
https://<custom-domain-name>.b2clogin.com/<tenant-name>/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

## <a name="test-your-custom-domain"></a>Teste o seu domínio personalizado

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Em **Políticas**, selecione **fluxos de utilizador (políticas)**.
1. Selecione um fluxo de utilizador e, em seguida, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *webapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Clique **em Copiar para a área de transferência.**

    ![Copiar o pedido de autorização URI](./media/custom-domain/user-flow-run-now.png)

1. No URL do ponto final do **fluxo do utilizador Run,** substitua o domínio Azure AD B2C (<nome de inquilino>.b2clogin.com) pelo seu domínio personalizado.  
    Por exemplo, em vez de:

    ```http
    https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login
    ```

    Utilização:

    ```http
    https://login.contoso.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login    
    ```
1. Selecione **Executar o fluxo do utilizador**. A sua política Azure AD B2C deve ser carregada.
1. Inscreva-se com contas locais e sociais.
1. Repita o teste com o resto das suas políticas.

## <a name="configure-your-application"></a>Configurar a aplicação 

Depois de configurar e testar o domínio personalizado, pode atualizar as suas aplicações para carregar o URL que especifica o seu domínio personalizado como nome de anfitrião em vez do domínio Azure AD B2C. 

A integração de domínio personalizado aplica-se aos pontos finais de autenticação que utilizam as políticas AZURE AD B2C (fluxos de utilizador ou políticas personalizadas) para autenticar os utilizadores. Estes pontos finais podem parecer os seguintes:

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/v2.0/.well-known/openid-configuration</code>

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://\<custom-domain\>/<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

Substituir:
- **domínio personalizado** com o seu domínio personalizado
- **nome inquilino** com seu nome de inquilino ou iD inquilino
- **nome da política** com o seu nome de política. [Saiba mais sobre as políticas Azure AD B2C](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 


Os metadados [do prestador de serviços SAML](connect-with-saml-service-providers.md) podem parecer os seguintes: 

```html
https://custom-domain-name/tenant-name/policy-name/Samlp/metadata
```

### <a name="optional-use-tenant-id"></a>(Opcional) Use iD do inquilino

Pode substituir o nome do seu inquilino B2C no URL pelo seu inquilino ID GUID de modo a remover todas as referências a "b2c" no URL. Pode encontrar o seu inquilino ID GUID na página de visão geral do B2C no portal Azure.
Por exemplo, altere `https://account.contosobank.co.uk/contosobank.onmicrosoft.com/` para `https://account.contosobank.co.uk/<tenant ID GUID>/`

Se optar por utilizar o ID do inquilino em vez do nome do inquilino, certifique-se de atualizar o fornecedor de identidade **OAuth redirecionar URIs** em conformidade. Para mais informações, consulte [configurar o seu fornecedor de identidade.](#configure-your-identity-provider)

### <a name="token-issuance"></a>Emissão simbólica

O nome do emitente simbólico (iss) altera alterações com base no domínio personalizado que está a ser utilizado. Por exemplo:

```http
https://<domain-name>/11111111-1111-1111-1111-111111111111/v2.0/
```

::: zone pivot="b2c-custom-policy"

## <a name="block-access-to-the-default-domain-name"></a>Bloquear o acesso ao nome de domínio predefinido

Depois de adicionar o domínio personalizado e configurar a sua aplicação, os utilizadores ainda poderão aceder ao domínio <nome de inquilino>.b2clogin.com. Para impedir o acesso, pode configurar a política para verificar o pedido de autorização "nome de anfitrião" contra uma lista permitida de domínios. O nome do anfitrião é o nome de domínio que aparece no URL. O nome do anfitrião está disponível através `{Context:HostName}` [de reclamações.](claim-resolver-overview.md) Em seguida, pode apresentar uma mensagem de erro personalizada. 

1. Obtenha o exemplo de uma política de acesso condicional que verifica o nome de anfitrião do [GitHub](https://github.com/azure-ad-b2c/samples/blob/master/policies/check-host-name).
1. Em cada ficheiro, substitua a cadeia `yourtenant` pelo nome do seu inquilino Azure AD B2C. Por exemplo, se o nome do seu inquilino B2C for *contosob2c,* todos os casos `yourtenant.onmicrosoft.com` de se tornar `contosob2c.onmicrosoft.com` .
1. Faça o upload dos ficheiros de política na seguinte ordem: `B2C_1A_TrustFrameworkExtensions_HostName.xml` e, em `B2C_1A_signup_signin_HostName.xml` seguida, .

::: zone-end

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="azure-ad-b2c-returns-a-page-not-found-error"></a>Azure AD B2C devolve uma página não encontrada erro

- **Sintoma** - Depois de configurar um domínio personalizado, quando tentar iniciar sação com o domínio personalizado, obtém uma mensagem de erro HTTP 404.
- **Possíveis causas** - Este problema pode estar relacionado com a configuração DNS ou com a configuração de backend Azure Front Door. 
- **Resolução:**  
    1. Certifique-se de que o domínio personalizado está [registado e verificado com sucesso](#add-a-custom-domain-name-to-your-tenant) no seu inquilino Azure AD B2C.
    1. Certifique-se de que o [domínio personalizado](../frontdoor/front-door-custom-domain.md) está configurado corretamente. O `CNAME` registo do seu domínio personalizado deve indicar para o anfitrião frontal da porta dianteira Azure (por exemplo, contoso.azurefd.net).
    1. Certifique-se de que a configuração da [piscina de backend Azure Front Door](#set-up-your-custom-domain-on-azure-front-door) aponta para o inquilino onde configura o nome de domínio personalizado e onde o fluxo do utilizador ou as políticas personalizadas estão armazenadas.

### <a name="identify-provider-returns-an-error"></a>O fornecedor de identificação devolve um erro

- **Sintoma** - Depois de configurar um domínio personalizado, pode iniciar sôss com contas locais. Mas quando se inscreve com credenciais de [fornecedores externos de identidade social ou empresarial,](add-identity-provider.md)os fornecedores de identidade apresentam uma mensagem de erro.
- **Possíveis causas** - Quando o Azure AD B2C leva o utilizador a fazer seduca com um fornecedor de identidade federado, especifica o URI de redirecionamento. O redirect URI é o ponto final para onde o fornecedor de identidade devolve o token. O URI de redirecionamento é o mesmo domínio que a sua aplicação utiliza com o pedido de autorização. Se o URI de redirecionamento ainda não estiver registado no fornecedor de identidade, pode não confiar no novo URI de redirecionamento, o que resulta numa mensagem de erro. 
- **Resolução** - Siga os passos em [Configurar o seu fornecedor de identidade](#configure-your-identity-provider) para adicionar o novo URI de redirecionamento. 


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="can-i-use-azure-front-door-advanced-configuration-such-as-web-application-firewall-rules"></a>Posso utilizar a configuração avançada da Porta frontal Azure, como *regras de firewall de aplicações web?* 
  
Embora as definições avançadas da Porta Frontal do Azure não sejam oficialmente suportadas, pode usá-las por sua conta e risco. 

### <a name="when-i-use-run-now-to-try-to-run-my-policy-why-i-cant-see-the-custom-domain"></a>Quando uso o Run Now para tentar executar a minha política, porque não consigo ver o domínio personalizado?

Copie o URL, altere manualmente o nome de domínio e, em seguida, cole-o de volta para o seu navegador.

### <a name="which-ip-address-is-presented-to-azure-ad-b2c-the-users-ip-address-or-the-azure-front-door-ip-address"></a>Qual endereço IP é apresentado ao Azure AD B2C? O endereço IP do utilizador ou o endereço IP da Porta Frontal Azure?

A porta frontal Azure passa o endereço IP original do utilizador. Este é o endereço IP que você verá no relatório de auditoria ou na sua política personalizada.

### <a name="can-i-use-a-third-party-wab-application-firewall-waf-with-b2c"></a>Posso usar uma firewall de aplicação de wab de terceiros (WAF) com B2C?

Atualmente, o Azure AD B2C suporta um domínio personalizado através da utilização apenas da Porta Frontal Azure. Não adicione outro WAF na frente da Porta frontal Azure.


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [os pedidos de autorização da OAuth](protocols-overview.md).

