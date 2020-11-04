---
title: Construa a página de aterragem para a sua oferta saas gratuita ou experimental no mercado comercial
description: Saiba como construir uma página de aterragem para a sua oferta saas gratuita ou experimental.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/04/2020
ms.openlocfilehash: 699271316eccec1244db886ed2296f87c52f91ae
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348360"
---
# <a name="build-the-landing-page-for-your-free-or-trial-saas-offer-in-the-commercial-marketplace"></a>Construa a página de aterragem para a sua oferta saas gratuita ou experimental no mercado comercial

Este artigo guia-o através do processo de construção de uma página de aterragem para uma aplicação SaaS gratuita ou experimental que será vendida no mercado comercial da Microsoft.

## <a name="overview"></a>Descrição geral

Pode pensar na página de aterragem como o "lobby" do seu software como uma oferta de serviço (SaaS). Depois de o cliente optar por obter a sua app, o mercado comercial direciona-os para a página de aterragem para ativar e configurar a sua subscrição para a sua aplicação SaaS. Quando cria um software como oferta de serviço (SaaS), no Partner Center, pode escolher se deve ou não [vender através da Microsoft.](plan-saas-offer.md#listing-options) Se quiser apenas listar a sua oferta no mercado comercial da Microsoft e não vender através da Microsoft, pode especificar como os potenciais clientes podem interagir com a oferta. Quando ativa a opção de listagem **de testes Get-lo agora (Grátis)** ou **Grátis,** tem de especificar um URL da página de aterragem para o qual o utilizador pode aceder à subscrição ou teste gratuito.

O objetivo da página de aterragem é simplesmente receber o utilizador para que possa ativar o teste gratuito ou a subscrição gratuita. Utilizando o Azure Ative Directory (AZure AD) e o Microsoft Graph, irá ativar um único sign-on (SSO) para o utilizador e obter detalhes importantes sobre o utilizador que pode utilizar para ativar o seu teste gratuito ou subscrição gratuita, incluindo o seu nome, endereço de e-mail e organização.

Uma vez que as informações necessárias para ativar a subscrição são limitadas e fornecidas pelo Azure AD e pelo Microsoft Graph, não deverá haver necessidade de solicitar informações que exijam mais do que o consentimento básico. Se necessitar de detalhes do utilizador que exijam consentimento adicional para a sua aplicação, deverá solicitar estas informações após a ativação da subscrição estar concluída. Isto permite a ativação de subscrição sem atrito para o utilizador e diminui o risco de abandono.

A página de aterragem normalmente inclui as seguintes opções de informação e listagem:

- Apresente o nome e detalhes do teste gratuito ou subscrição gratuita. Por exemplo, especifique os limites de utilização ou a duração de um ensaio.
- Apresente os detalhes da conta do utilizador, incluindo o primeiro e o último nome, organização e e-mail.
- Insto o utilizador a confirmar ou substituir diferentes dados da conta.
- Guie o utilizador nos próximos passos após a ativação. Por exemplo, receber um e-mail de boas-vindas, gerir a subscrição, obter suporte ou ler documentação.

As seguintes secções deste artigo irão guiá-lo através do processo de construção de uma página de desembarque:

1. [Crie um registo de aplicativo AD AZure](#create-an-azure-ad-app-registration) para a página de aterragem.
2. [Utilize uma amostra de código como ponto de partida](#use-a-code-sample-as-a-starting-point) para a sua aplicação.
3. [Leia as informações de reclamações codificadas no token de identificação,](#read-information-from-claims-encoded-in-the-id-token)recebidas da Azure AD após o sinal, que foram enviadas com o pedido.
4. [Utilize a API do Gráfico microsoft](#use-the-microsoft-graph-api) para recolher informações adicionais, conforme necessário.

## <a name="create-an-azure-ad-app-registration"></a>Criar um registo de aplicativo AD Azure

O mercado comercial está totalmente integrado com a Azure AD. Os utilizadores chegam ao mercado autenticados com uma [conta AD Azure ou com a conta Microsoft (MSA)](../active-directory/fundamentals/active-directory-whatis.md#terminology). Depois de adquirir uma subscrição de teste gratuita ou gratuita através da sua oferta apenas de lista, o utilizador vai do mercado comercial para o URL da sua página de aterragem para ativar e gerir a sua subscrição à sua aplicação SaaS. Deve deixar o utilizador iniciar súpido na sua aplicação com Azure AD SSO. (O URL da página de aterragem é especificado na página de [configuração técnica](plan-saas-offer.md#technical-information) da oferta.

O primeiro passo para usar a identidade é certificar-se de que a sua página de aterragem está registada como uma aplicação AD AZure. O registo da aplicação permite-lhe utilizar a Azure AD para autenticar os utilizadores e solicitar o acesso aos recursos dos utilizadores. Pode ser considerada a definição da aplicação, que permite ao serviço saber como emitir fichas para a app com base nas definições da app.

### <a name="register-a-new-application-using-the-azure-portal"></a>Registar uma nova aplicação através do portal do Azure

Para começar, siga as instruções para [registar uma nova aplicação](../active-directory/develop/quickstart-register-app.md). Para permitir que utilizadores de outras empresas visitem a app, tem de escolher **contas em qualquer diretório organizacional (qualquer diretório AD AD - multitenant) e contas pessoais da Microsoft (como o Skype ou Xbox)** quando lhe perguntarem quem pode usar a aplicação.

Se pretender consultar a Microsoft Graph API, [configuure a sua nova aplicação para aceder a APIs web](../active-directory/develop/quickstart-configure-app-access-web-apis.md). Quando seleciona as permissões API para esta aplicação, o padrão do **Utilizador.Read** é suficiente para recolher informações básicas sobre o utilizador para tornar o processo de embarque suave e automático. Não solicite quaisquer permissões API etiquetadas **que necessitem de consentimento administrativo,** pois isso impedirá todos os utilizadores não administradores de visitarem a sua página de aterragem.

Se necessitar de permissões elevadas como parte do seu processo de embarque ou provisionamento, considere utilizar a funcionalidade [de consentimento incremental](../active-directory/azuread-dev/azure-ad-endpoint-comparison.md) da Azure AD para que todos os utilizadores enviados do mercado possam interagir inicialmente com a página de aterragem.

## <a name="use-a-code-sample-as-a-starting-point"></a>Use uma amostra de código como ponto de partida

A Microsoft forneceu várias aplicações de amostras que implementam um site simples com login AD Azure ativado. Depois da sua aplicação estar registada no Azure AD, a lâmina **Quickstart** oferece uma lista de tipos de aplicações comuns e pilhas de desenvolvimento (Figura 1). Escolha aquele que corresponda ao seu ambiente e siga as instruções para o download e configuração.

**_Figura 1: Lâmina de arranque rápido no portal Azure_* _

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="Ilustra a lâmina Quickstart no portal Azure.":::

Depois de descarregar o código e configurar o seu ambiente de desenvolvimento, altere as definições de configuração na aplicação para refletir o ID da aplicação, o ID do inquilino e o segredo do cliente que gravou no procedimento anterior. Note que os passos exatos diferirão dependendo da amostra que estiver a utilizar.

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>Leia informações de reclamações codificadas no token de ID

Como parte do fluxo [OpenID Connect,](../active-directory/develop/v2-protocols-oidc.md) o Azure AD adiciona um sinal de [ID ao](../active-directory/develop/id-tokens.md) pedido quando o utilizador é enviado para a página de aterragem. Este token contém várias peças de informação básica que podem ser úteis no processo de ativação, incluindo as informações vistas nesta tabela.

| Valor | Descrição |
| ------------ | ------------- |
| aud | Público pretendido para este símbolo. Neste caso, deve coincidir com o seu ID de aplicação e ser validado. |
| preferred_username | Nome de utilizador primário do utilizador visitante. Este pode ser um endereço de e-mail, número de telefone ou outro identificador. |
| e-mail | Endereço de e-mail do utilizador. Note que este campo pode estar vazio. |
| name | Valor legível pelo homem que identifica o sujeito do símbolo. Neste caso, será o nome do utilizador. |
| oid | Identificador no sistema de identidade da Microsoft que identifica exclusivamente o utilizador em todas as aplicações. O Microsoft Graph devolverá este valor como propriedade de ID para uma determinada conta de utilizador. |
| tid | Identificador que representa o inquilino Azure AD de que o utilizador é. No caso de uma identidade MSA, esta será `9188040d-6c67-4c5b-b112-36a304b66dad` sempre. Para obter mais informações, consulte a nota na secção seguinte: Utilize a API do Gráfico microsoft. |
| sub | Identificador que identifica exclusivamente o utilizador nesta aplicação específica. |
|||

## <a name="use-the-microsoft-graph-api"></a>Utilizar a Microsoft Graph API

O token ID contém informações básicas para identificar o utilizador, mas o seu processo de ativação pode requerer detalhes adicionais , como a empresa do utilizador, para completar o processo de embarque. Utilize a API do [Microsoft Graph](/graph/use-the-api) para solicitar estas informações para evitar forçar o utilizador a introduzir novamente estes detalhes. As permissões standard _ *User.Read* * incluem as seguintes informações, por padrão:

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

Propriedades adicionais , como o nome da empresa do utilizador ou a localização do utilizador (país)- podem ser selecionadas para inclusão no pedido. Para mais detalhes, consulte [propriedades para o tipo de recurso do utilizador.](/graph/api/resources/user?view=graph-rest-1.0&preserve-view=true#properties)

A maioria das aplicações registadas com Azure AD concedem permissões delegadas para ler a informação do utilizador do inquilino AZure AD da sua empresa. Qualquer pedido ao Microsoft Graph para essa informação deve ser acompanhado por um token de acesso como autenticação. Passos específicos para gerar o token de acesso dependerão da pilha de tecnologia que está a usar, mas o código de amostra conterá um exemplo. Para mais informações, consulte [Obter acesso em nome de um utilizador](/graph/auth-v2-user).

> [!NOTE]
> As contas do inquilino da MSA (com identificação do `9188040d-6c67-4c5b-b112-36a304b66dad` inquilino) não devolverão mais informações do que já foram recolhidas com o token de identificação. Para que possa ignorar esta chamada para a API do Gráfico para estas contas.

## <a name="next-steps"></a>Próximos passos
- [Como criar uma oferta SaaS no mercado comercial](create-new-saas-offer.md)