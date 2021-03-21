---
title: Construa a página de aterragem para a sua oferta de SaaS transacionável no mercado comercial
description: Saiba como construir uma página de aterragem para a sua oferta de SaaS transatable.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 4bfc29472373a53bcebb2ba59134d1f3702d4793
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549877"
---
# <a name="build-the-landing-page-for-your-transactable-saas-offer-in-the-commercial-marketplace"></a>Construa a página de aterragem para a sua oferta de SaaS transacionável no mercado comercial

Este artigo guia-o através do processo de construção de uma página de aterragem para uma aplicação saaS transacionável que será vendida no mercado comercial da Microsoft.

## <a name="overview"></a>Descrição geral

Pode pensar na página de aterragem como o "lobby" do seu software como uma oferta de serviço (SaaS). Após o comprador subscrever uma oferta, o mercado comercial direciona-os para a página de aterragem para ativar e configurar a sua subscrição para a sua aplicação SaaS. Pense nisto como um passo de confirmação de encomenda que permite ao comprador ver o que comprou e confirmar os seus dados da conta. Utilizando o Azure Ative Directory (Azure AD) e o Microsoft Graph, irá ativar um único sign-on (SSO) para o comprador e obter detalhes importantes sobre o comprador que pode usar para confirmar e ativar a sua subscrição, incluindo o seu nome, endereço de e-mail e organização.

Uma vez que as informações necessárias para ativar a subscrição são limitadas e fornecidas pelo Azure AD e pelo Microsoft Graph, não deverá haver necessidade de solicitar informações que exijam mais do que o consentimento básico. Se necessitar de detalhes do utilizador que exijam consentimento adicional para a sua aplicação, deverá solicitar estas informações após a ativação da subscrição estar concluída. Isto permite a ativação de subscrição sem atrito para o comprador e diminui o risco de abandono.

A página de aterragem normalmente inclui o seguinte:

- Apresente o nome da oferta e do plano adquirido, bem como os termos de faturação.
- Apresente os detalhes da conta do comprador, incluindo o primeiro e último nome, organização e e-mail.
- Insto o comprador a confirmar ou substituir diferentes detalhes da conta.
- Guie o comprador nos próximos passos após a ativação. Por exemplo, receber um e-mail de boas-vindas, gerir a subscrição, obter suporte ou ler documentação.

> [!NOTE]
> O comprador também será direcionado para a página de aterragem ao gerir a sua subscrição após a ativação. Depois de ativada a subscrição do comprador, tem de utilizar o SSO para permitir que o utilizador faça o seu s-in. Recomenda-se direcionar o utilizador para um perfil de conta ou página de configuração.

As seguintes secções irão guiá-lo através do processo de construção de uma página de aterragem:

1. [Crie um registo de aplicativo AD AZure](#create-an-azure-ad-app-registration) para a página de aterragem.
1. [Utilize uma amostra de código como ponto de partida](#use-a-code-sample-as-a-starting-point) para a sua aplicação.
1. [Utilize duas aplicações AD AZure para melhorar a segurança na produção.](#use-two-azure-ad-apps-to-improve-security-in-production)
1. [Resolva o símbolo de identificação de compra de mercado](#resolve-the-marketplace-purchase-identification-token) adicionado ao URL pelo mercado comercial.
1. [Leia as informações de reclamações codificadas no token de identificação,](#read-information-from-claims-encoded-in-the-id-token)que foi recebido da Azure AD após o sinal, que foi enviado com o pedido.
1. [Utilize a API do Gráfico microsoft](#use-the-microsoft-graph-api) para recolher informações adicionais, conforme necessário.

## <a name="create-an-azure-ad-app-registration"></a>Criar um registo de aplicativo AD Azure

O mercado comercial está totalmente integrado com a Azure AD. Os compradores chegam ao mercado autenticados com uma [conta AD Azure ou conta Microsoft (MSA)](../active-directory/fundamentals/active-directory-whatis.md#terminology). Após a compra, o comprador vai do mercado comercial para o URL da sua página de aterragem para ativar e gerir a sua subscrição da sua aplicação SaaS. Deve deixar o comprador iniciar súpido na sua aplicação com a Azure AD SSO. (O URL da página de aterragem é especificado na página de [configuração técnica](plan-saas-offer.md#technical-information) da oferta.

O primeiro passo para usar a identidade é certificar-se de que a sua página de aterragem está registada como uma aplicação AD AZure. O registo da aplicação permite-lhe utilizar a Azure AD para autenticar os utilizadores e solicitar o acesso aos recursos dos utilizadores. Pode ser considerada a definição da aplicação, que permite ao serviço saber como emitir fichas para a app com base nas definições da app.

### <a name="register-a-new-application-using-the-azure-portal"></a>Registar uma nova aplicação através do portal do Azure

Para começar, siga as instruções para [registar uma nova aplicação](../active-directory/develop/quickstart-register-app.md). Para permitir que utilizadores de outras empresas visitem a app, deve escolher uma das opções multitenantes quando lhe perguntarem quem pode usar a aplicação.

Se pretender consultar a Microsoft Graph API, [configuure a sua nova aplicação para aceder a APIs web](../active-directory/develop/quickstart-configure-app-access-web-apis.md). Quando seleciona as permissões API para esta aplicação, o padrão do **Utilizador.Read** é suficiente para recolher informações básicas sobre o comprador para tornar o processo de embarque suave e automático. Não solicite quaisquer permissões API etiquetadas **que necessitem de consentimento administrativo,** pois isso impedirá todos os utilizadores não administradores de visitarem a sua página de aterragem.

Se necessitar de permissões elevadas como parte do seu processo de embarque ou provisionamento, considere usar a funcionalidade [de consentimento incremental](../active-directory/azuread-dev/azure-ad-endpoint-comparison.md) da Azure AD para que todos os compradores enviados do mercado possam interagir inicialmente com a página de aterragem.

## <a name="use-a-code-sample-as-a-starting-point"></a>Use uma amostra de código como ponto de partida

Fornecemos várias aplicações de amostra que implementam um site simples com login AD Azure ativado. Depois da sua aplicação estar registada no Azure AD, a lâmina **Quickstart** oferece uma lista de tipos de aplicações comuns e pilhas de desenvolvimento como visto na Figura 1. Escolha aquele que corresponda ao seu ambiente e siga as instruções para o download e configuração.

***Figura 1: Lâmina de arranque rápido no portal Azure***

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="Ilustra a lâmina de arranque rápido no portal Azure.":::

Depois de descarregar o código e configurar o seu ambiente de desenvolvimento, altere as definições de configuração na aplicação para refletir o ID da aplicação, o ID do inquilino e o segredo do cliente que gravou no procedimento anterior. Note que os passos exatos diferirão dependendo da amostra que estiver a utilizar.

## <a name="use-two-azure-ad-apps-to-improve-security-in-production"></a>Use duas aplicações AD AZure para melhorar a segurança na produção

Este artigo apresenta uma versão simplificada da arquitetura para implementar uma página de desembarque para a sua oferta de marketplace comercial SaaS. Ao executar a página em produção, recomendamos que melhore a segurança comunicando com as APIs de cumprimento do SaaS apenas através de uma aplicação diferente e segura. Isto requer a criação de duas novas aplicações:

- Em primeiro lugar, a aplicação de página de aterragem multitenante descrita até este ponto, exceto sem a funcionalidade de contactar as APIs de cumprimento do SaaS. Esta funcionalidade será descarregada para outra aplicação, como descrito abaixo.
- Em segundo lugar, uma aplicação para possuir as comunicações com as APIs de cumprimento do SaaS. Esta aplicação deve ser um único inquilino, apenas para ser usado pela sua organização, e pode ser estabelecida uma lista de controlo de acesso para limitar o acesso às APIs apenas a partir desta app.

Isto permite que a solução funcione em cenários que respeitem o princípio [da separação de preocupações.](/dotnet/architecture/modern-web-apps-azure/architectural-principles#separation-of-concerns) Por exemplo, a página de aterragem utiliza a primeira aplicação AZure AD registada para assinar no utilizador. Após o acesso do utilizador, a página de aterragem utiliza o segundo AD Azure para solicitar um token de acesso para ligar para a API de cumprimento saas e ligar para a operação de resolução.

## <a name="resolve-the-marketplace-purchase-identification-token"></a>Resolver o token de identificação de compra de mercado

Quando o comprador é enviado para a sua página de aterragem, um sinal é adicionado ao parâmetro URL. Este token é diferente tanto do token emitido pela Azure AD como do token de acesso utilizado para a autenticação de serviço-a-serviço, e é usado como uma entrada para as [APIs de cumprimento saaS](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) resolver chamada para obter os detalhes da subscrição. Tal como acontece com todas as chamadas para as APIs de cumprimento do SaaS, o seu pedido de serviço-a-serviço será autenticado com um token de acesso que se baseia no ID da aplicação Azure para autenticação de serviço-a-serviço.

> [!NOTE]
> Na maioria dos casos, é preferível fazer esta ligação a partir de um segundo pedido de inquilino único. Consulte [duas aplicações AD AZure para melhorar a segurança na produção](#use-two-azure-ad-apps-to-improve-security-in-production) no início deste artigo.

### <a name="request-an-access-token"></a>Pedir um token de acesso

Para autenticar a sua aplicação com as APIs de cumprimento do SaaS, precisa de um token de acesso, que pode ser gerado através do ponto final Azure AD OAuth. Veja [como obter o token de autorização da editora.](./partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token)

### <a name="call-the-resolve-endpoint"></a>Chame o ponto final de resolução

As APIs de cumprimento do SaaS implementam o ponto final de [resolução](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) que pode ser chamado para confirmar a validade do token do mercado e para devolver informações sobre a subscrição.

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>Leia informações de reclamações codificadas no token de ID

Como parte do fluxo [OpenID Connect,](../active-directory/develop/v2-protocols-oidc.md) a Azure AD adiciona um sinal de [ID ao](../active-directory/develop/id-tokens.md) pedido quando o comprador é enviado para a página de aterragem. Este token contém várias peças de informação básica que podem ser úteis no processo de ativação, incluindo as informações vistas nesta tabela.

| Valor | Descrição |
| ------------ | ------------- |
| aud | Público pretendido para este símbolo. Neste caso, deve coincidir com o seu ID de aplicação e ser validado. |
| preferred_username | Nome de utilizador primário do utilizador visitante. Este pode ser um endereço de e-mail, número de telefone ou outro identificador. |
| e-mail | Endereço de e-mail do utilizador. Note que este campo pode estar vazio. |
| name | Valor legível pelo homem que identifica o sujeito do símbolo. Neste caso, será o nome do comprador. |
| oid | Identificador no sistema de identidade da Microsoft que identifica exclusivamente o utilizador em todas as aplicações. O Microsoft Graph devolverá este valor como propriedade de ID para uma determinada conta de utilizador. |
| tid | Identificador que representa o inquilino da AD Azure de onde o comprador é. No caso de uma identidade MSA, esta será ``9188040d-6c67-4c5b-b112-36a304b66dad`` sempre. Para obter mais informações, consulte a nota na secção seguinte: Utilize a API do Gráfico microsoft. |
| sub | Identificador que identifica exclusivamente o utilizador nesta aplicação específica. |
|||

## <a name="use-the-microsoft-graph-api"></a>Utilizar a Microsoft Graph API

O token de ID contém informações básicas para identificar o comprador, mas o seu processo de ativação pode requerer detalhes adicionais - como a empresa do comprador - para completar o processo de embarque. Utilize a API do [Microsoft Graph](/graph/use-the-api) para solicitar estas informações para evitar forçar o utilizador a introduzir novamente estes detalhes. As permissões padrão **User.Read** incluem as seguintes informações, por padrão.

| Valor | Descrição |
| ------------ | ------------- |
| displayName | Nome apresentado na agenda do utilizador. |
| nomeDado | Primeiro nome do utilizador. |
| jobTitle | O cargo de utilizador. |
| correio | Endereço SMTP para o utilizador. |
| telemóvel | Número de telefone celular primário para o utilizador. |
| preferiuLanguage | Código ISO 639-1 para o idioma preferido do utilizador. |
| surname | Sobrenome do utilizador. |
|||

Propriedades adicionais , como o nome da empresa do utilizador ou a localização do utilizador (país)- podem ser selecionadas para inclusão no pedido. Consulte [as propriedades para o tipo de recurso do utilizador](/graph/api/resources/user#properties) para obter mais detalhes.

A maioria das aplicações registadas com Azure AD concedem permissões delegadas para ler a informação do utilizador do inquilino AZure AD da sua empresa. Qualquer pedido ao Microsoft Graph para essa informação deve ser acompanhado por um sinal de acesso para autenticação. Passos específicos para gerar o token de acesso dependerão da pilha de tecnologia que está a usar, mas o código de amostra conterá um exemplo. Para mais informações, consulte [Obter acesso em nome de um utilizador](/graph/auth-v2-user).

> [!NOTE]
> As contas do inquilino da MSA (com identificação do ``9188040d-6c67-4c5b-b112-36a304b66dad`` inquilino) não devolverão mais informações do que já foram recolhidas com o token de identificação. Para que possa ignorar esta chamada para a API do Gráfico para estas contas.

## <a name="next-steps"></a>Passos seguintes

- [Como criar uma oferta SaaS no mercado comercial](create-new-saas-offer.md)