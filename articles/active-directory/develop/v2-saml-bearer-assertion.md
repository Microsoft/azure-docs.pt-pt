---
title: A plataforma de identidade da Microsoft & fluxo de afirmação do portador saml / Azure
description: Aprenda a recolher dados do Microsoft Graph sem pedir ao utilizador credenciais utilizando o fluxo de afirmação do portador SAML.
services: active-directory
author: umeshbarapatre
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 1cd79b1f9e4cd3afadee250da0c184c0c5b8ac07
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886182"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Plataforma de identidade da Microsoft e fluxo de afirmação do portador OAuth 2.0 SAML
O fluxo de afirmação do portador OAuth 2.0 SAML permite-lhe solicitar um sinal de acesso OAuth usando uma afirmação SAML quando um cliente precisa de usar uma relação de confiança existente. A assinatura aplicada à afirmação saml fornece a autenticação da aplicação autorizada. Uma afirmação SAML é um símbolo de segurança XML emitido por um fornecedor de identidade e consumido por um prestador de serviços. O prestador de serviços baseia-se no seu conteúdo para identificar o sujeito da afirmação para fins relacionados com a segurança.

A afirmação saml é publicada no ponto final do símbolo OAuth.  O ponto final processa a afirmação e emite um token de acesso com base na aprovação prévia da app. O cliente não é obrigado a ter ou armazenar um token de atualização, nem o segredo do cliente é necessário para ser passado para o ponto final simbólico.

O fluxo de afirmação do portador do SAML é útil ao recolher dados de APIs do Microsoft Graph (que apenas suportam permissões delegadas) sem solicitar credenciais ao utilizador. Neste cenário, a concessão de credenciais de cliente, que é preferida para processos de fundo, não funciona.

Para aplicações que fazem sessão interativa baseada no navegador para obter uma afirmação SAML e, em seguida, querer adicionar acesso a uma API protegida OAuth (como o Microsoft Graph), você pode fazer um pedido de OAuth para obter um sinal de acesso para a API. Quando o navegador for redirecionado para a AD Azure para autenticar o utilizador, o navegador irá recolher a sessão a partir do sessão saml e o utilizador não precisa de introduzir as suas credenciais.

O fluxo de afirmação do portador Da OAuth SAML também é suportado para utilizadores autenticando com fornecedores de identidade, tais como Serviços da Federação de Diretório Ativo (ADFS) federados para o Diretório Ativo Azure.  A afirmação SAML obtida a partir da ADFS pode ser utilizada num fluxo OAuth para autenticar o utilizador.

![Fluxo oAuth](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>Gráfico de chamada usando a afirmação do portador SAML
Agora, vamos compreender como podemos realmente obter a afirmação da SAML programaticamente. Esta abordagem é testada com ADFS. No entanto, isto funciona com qualquer fornecedor de identidade que apoie a devolução da afirmação da SAML programaticamente. O processo básico é: obtenha uma afirmação SAML, obtenha um sinal de acesso e aceda ao Microsoft Graph.

### <a name="prerequisites"></a>Pré-requisitos

Estabelecer uma relação de confiança entre o servidor/ambiente de autorização (Microsoft 365) e o fornecedor de identidade, ou emitente da afirmação do portador SAML 2.0 (ADFS). Para configurar a ADFS para um único sign-on e como fornecedor de identidade, pode consultar [este artigo](https://blogs.technet.microsoft.com/canitpro/2015/09/11/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365/).

Registe a aplicação no [portal:](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
1. Inscreva-se na [lâmina de registo da aplicação do portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) (por favor, note que estamos a utilizar os pontos finais v2.0 para a API graph e, portanto, precisamos de registar a aplicação neste portal. Caso contrário, poderíamos ter usado as inscrições no diretório ativo azure). 
1. Selecione **Novo registo**.
1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação: 
    1. **Nome** - Introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação.
    1. **Tipos de conta suportados** - Selecione as contas que quer que a sua aplicação suporte.
    1. **Redirecione o URI (opcional)** - Selecione o tipo de aplicação que está a construir, Web ou cliente público (ambiente de trabalho mobile &) e, em seguida, introduza o URI redirecionamento (ou URL de resposta) para a sua aplicação.
    1. Quando terminar, selecione **Registar**.
1. Tome nota da identificação da aplicação (cliente).
1. No painel esquerdo, selecione **Certificados & segredos.** Clique em **novo segredo** de cliente na secção de segredos do **Cliente.** Copie o novo segredo do cliente, não poderá recuperar quando deixar a lâmina.
1. No painel esquerdo, selecione **permissões API** e, em seguida, **adicione uma permissão**. Selecione **Microsoft Graph**, em seguida, **permissões delegadas**, e, em seguida, selecione **Tasks.read** já que pretendemos usar o Outlook Graph API. 

Instale o [Carteiro,](https://www.getpostman.com/)uma ferramenta necessária para testar os pedidos da amostra.  Mais tarde, pode converter os pedidos em código.

### <a name="get-the-saml-assertion-from-adfs"></a>Obtenha a afirmação SAML da ADFS
Crie um pedido post para o ponto final da ADFS usando o envelope SOAP para obter a afirmação SAML:

![Obtenha afirmação saml](./media/v2-saml-bearer-assertion/2.png)

Valores de cabeçalho:

![Valores cabeçalho](./media/v2-saml-bearer-assertion/3.png)

Organismo de pedido da ADFS:

![Corpo de pedido da ADFS](./media/v2-saml-bearer-assertion/4.png)

Uma vez publicado este pedido com sucesso, deverá receber uma afirmação SAML da ADFS. Apenas são necessários os dados da etiqueta **SAML:Afirmação,** convertê-lo em codificação base64 para utilizar em outros pedidos.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>Obtenha o símbolo OAuth2 usando a afirmação SAML 
Neste passo, pegue um token OAuth2 usando a resposta de afirmação ADFS.

1. Crie um pedido POST como mostrado abaixo com os valores cabeçalho:

    ![Pedido de correio](./media/v2-saml-bearer-assertion/5.png)
1. No organismo do pedido, substitua **client_id**, **client_secret**, e **afirmação** (a afirmação SAML codificada base64 obteve o passo anterior):

    ![Corpo do pedido](./media/v2-saml-bearer-assertion/6.png)
1. Após pedido bem sucedido, receberá um sinal de acesso do diretório ativo azure.

### <a name="get-the-data-with-the-oauth-token"></a>Obtenha os dados com o símbolo oauth

Depois de receber o sinal de acesso, ligue para as APIs do Gráfico (tarefas outlook neste exemplo). 

1. Crie um pedido GET com o token de acesso recolhido no passo anterior:

    ![Pedido get](./media/v2-saml-bearer-assertion/7.png)

1. Após pedido bem sucedido, receberá uma resposta JSON.

## <a name="next-steps"></a>Passos seguintes

Conheça os diferentes fluxos de [autenticação e cenários de aplicação.](authentication-flows-app-scenarios.md)