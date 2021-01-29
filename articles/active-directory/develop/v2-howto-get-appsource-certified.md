---
title: Obtenha a certificação appSource para Azure Ative Directory| Microsoft Docs
description: Saiba mais detalhes sobre como obter a sua aplicação AppSource certificada para O Azure Ative Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/23/2020
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 3dfeca64804ceb522046e5c0dc10702575f60aad
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054590"
---
# <a name="get-appsource-certified-for-azure-active-directory"></a>Obtenha a certificação appSource para diretório ativo Azure

[O Microsoft AppSource](https://appsource.microsoft.com/) é um destino para os utilizadores empresariais descobrirem, tentarem e gerirem aplicações SaaS de linha de negócio (SaaS autónomo e add-on aos produtos Microsoft SaaS existentes).

Para listar uma aplicação SaaS autónoma no AppSource, a sua aplicação deve aceitar um único s-on a partir de contas de trabalho de qualquer empresa ou organização que tenha diretório Azure Ative (Azure AD). O processo de início de sposição deve utilizar os protocolos [OpenID Connect](v2-protocols-oidc.md) ou [OAuth 2.0.](v2-oauth2-auth-code-flow.md) A integração DA SAML não é aceite para a certificação AppSource.

## <a name="multi-tenant-applications"></a>Aplicações multi-inquilino

Uma *aplicação multi-inquilino* é uma aplicação que aceita inscrições de utilizadores de qualquer empresa ou organização que tenha Azure AD sem exigir uma instância, configuração ou implementação separadas. O AppSource recomenda que as aplicações implementem o multi-arrendamento para permitir a experiência de teste gratuito *de clique* único.

Para ativar o multi-arrendamento na sua aplicação, siga estes passos:
1. Desatado `Multi-Tenanted` a propriedade `Yes` para informação do seu registo de inscrição no [portal Azure.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) Por predefinição, as aplicações criadas no portal Azure são configuradas como *[inquilino único.](#single-tenant-applications)*
1. Atualize o seu código para enviar pedidos para o `common` ponto final. Para isso, atualize o ponto final `https://login.microsoftonline.com/{yourtenant}` de `https://login.microsoftonline.com/common*` .
1. Para algumas plataformas, como ASP.NET, também precisa de atualizar o seu código para aceitar vários emitentes.

Para obter mais informações sobre o multi-arrendamento, consulte [Como assinar em qualquer utilizador do Azure Ative Directory (Azure AD) utilizando o padrão de aplicação multi-inquilinos](howto-convert-app-to-be-multi-tenant.md).

### <a name="single-tenant-applications"></a>Pedidos de inquilino único

Uma *aplicação de inquilino único* é uma aplicação que apenas aceita inscrições de utilizadores de uma instância AD definida. Os utilizadores externos (incluindo contas de trabalho ou escola de outras organizações, ou contas pessoais) podem inscrever-se numa aplicação de um único inquilino depois de adicionar cada utilizador como conta de hóspedes à instância AD do Azure que a aplicação está registada. 

Pode adicionar os utilizadores como contas de hóspedes ao Azure AD através da [colaboração Azure AD B2B](../external-identities/what-is-b2b.md) e pode fazê-lo [programáticamente.](../../active-directory-b2c/code-samples.md) Ao utilizar o B2B, os utilizadores podem criar um portal de self-service que não requer um convite para iniciar sessão. Para mais informações, consulte [o portal self-service para inscrição de colaboração Azure AD B2B](../external-identities/self-service-portal.md).

As aplicações de um único inquilino podem ativar a experiência *Do Contacto Me,* mas se quiser ativar a experiência de teste de um clique/teste gratuito que o AppSource recomenda, em vez disso, ativar o multi-arrendamento na sua aplicação.

## <a name="appsource-trial-experiences"></a>Experiências de teste appSource

### <a name="free-trial-customer-led-trial-experience"></a>Ensaio gratuito (experiência de ensaio conduzida pelo cliente)

O teste liderado pelo cliente é a experiência que o AppSource recomenda, uma vez que oferece um acesso de um clique à sua aplicação. O exemplo a seguir mostra como é esta experiência:

1.  Um utilizador encontra a sua aplicação no site appSource e, em seguida, seleciona a opção **de teste Gratuita.**

    ![Mostra teste gratuito para experiência de teste liderada pelo cliente](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png)

2.  O AppSource redireciona o utilizador para um URL no seu site. O seu web site inicia automaticamente o processo *de inscrição única* (na carga da página).

    ![Mostra como o utilizador é redirecionado para um URL no seu site](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png)

3.  O utilizador é redirecionado para a página de sindes de sção da Microsoft e o utilizador fornece credenciais para iniciar seduca.

    ![Mostra a página de sindução da Microsoft](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png)

4. O utilizador dá o seu consentimento para a sua aplicação.

    ![Exemplo: Página de consentimento para uma aplicação](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png)

5.  O sismo completa e o utilizador é redirecionado de volta para o seu site.  O utilizador inicia o teste gratuito.

    ![Mostra a experiência que o utilizador vê quando redirecionado de volta para o seu site](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="contact-me-partner-led-trial-experience"></a>Contacte-me (experiência de teste liderada por parceiros)

Pode utilizar a experiência experimental do parceiro quando uma operação manual ou de longo prazo precisa de acontecer para o fornecimento do utilizador/empresa -- por exemplo, a sua aplicação precisa de providenciar máquinas virtuais, casos de base de dados ou operações que levem muito tempo a concluir. Neste caso, depois de o utilizador selecionar o botão **'Teste de Pedido'** e preencher um formulário, o AppSource envia-lhe as informações de contacto do utilizador. Quando recebe esta informação, fornece o ambiente e envia as instruções ao utilizador sobre como aceder à experiência experimental:<br/><br/>

1. Um utilizador encontra a sua aplicação no site appSource e, em seguida, seleciona **Contacte-me.**

    ![Mostra contacte-me para experiência de teste liderada por parceiros](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png)

2. O utilizador preenche um formulário com informações de contacto.

    ![Mostra um exemplo com informações de contacto](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png)

3. Recebe as informações do utilizador, configura uma instância de teste e envia a hiperligação para aceder à sua aplicação ao utilizador.

    ![Mostra espaço reservado para informações do utilizador](./media/active-directory-devhowto-appsource-certified/usercontact.png)

4. O utilizador acede à sua aplicação e completa o processo de inscrição única.

    ![Mostra o ecrã de inscrição da aplicação](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png)

5. O utilizador dá o seu consentimento para a sua aplicação.

    ![Mostra uma página de consentimento de exemplo para uma aplicação](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png)

6. O sismo completa e o utilizador é redirecionado de volta para o seu site. O utilizador inicia o teste gratuito.

    ![Mostra a experiência que o utilizador vê quando redirecionado de volta para o seu site](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="more-information"></a>Mais informações

Para obter mais informações sobre a experiência de teste appSource, consulte [este vídeo](https://aka.ms/trialexperienceforwebapps). 

## <a name="get-support"></a>Obter suporte

Para a integração do AD Azure, usamos [o Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-active-directory.html) com a comunidade para fornecer suporte.

Recomendamos vivamente que faça as suas perguntas sobre o Microsoft Q&A primeiro e navegue nos problemas existentes para ver se alguém já fez a sua pergunta antes. Certifique-se de que as suas perguntas ou comentários estão marcados com [`[azure-active-directory]`](https://docs.microsoft.com/answers/topics/azure-active-directory.html) .

Utilize a secção de comentários a seguir para fornecer feedback e ajude-nos a aperfeiçoar e moldar o nosso conteúdo.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre aplicações de construção que suportem as entradas AD do Azure, consulte cenários de [autenticação para Azure AD](authentication-flows-app-scenarios.md).
- Para obter informações sobre como listar a sua aplicação SaaS no AppSource, vá ver [Informações de Parceiros appSource](https://appsource.microsoft.com/partners)