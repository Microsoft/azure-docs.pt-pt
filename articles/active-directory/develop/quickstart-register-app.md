---
title: 'Quickstart: Registar uma aplicação na plataforma de identidade microsoft | Rio Azure'
description: Neste arranque rápido, aprende-se a registar uma aplicação com a plataforma de identidade da Microsoft.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, contperf-fy21q1, contperf-fy21q2
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: c72ae2a2e6dbd2278bdd78f26c145386be22764e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175435"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Quickstart: Registar uma aplicação com a plataforma de identidade microsoft

Neste arranque rápido, regista-se uma aplicação no portal Azure para que a plataforma de identidade da Microsoft possa fornecer serviços de autenticação e autorização para a sua aplicação e para os seus utilizadores.

A plataforma de identidade da Microsoft realiza gestão de identidade e acesso (IAM) apenas para aplicações registadas. Seja uma aplicação de cliente como uma web ou aplicação móvel, ou é uma API web que apoia uma aplicação de cliente, registando-a estabelece uma relação de confiança entre a sua aplicação e o fornecedor de identidade, a plataforma de identidade da Microsoft.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure que tem uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Conclusão da [Criação de um inquilina](quickstart-create-new-tenant.md) rápido.

## <a name="register-an-application"></a>Registar uma aplicação

O registo da sua aplicação estabelece uma relação de confiança entre a sua app e a plataforma de identidade da Microsoft. A confiança é unidirecional: a sua aplicação confia na plataforma de identidade da Microsoft, e não o contrário.

Siga estes passos para criar o registo da aplicação:

1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se tiver acesso a vários inquilinos, no menu superior, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: para selecionar o inquilino no qual pretende registar uma candidatura.
1. Procure e selecione **Azure Active Directory**.
1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
1. Introduza um **nome** de exibição para a sua aplicação. Os utilizadores da sua aplicação podem ver o nome do ecrã quando utilizarem a aplicação, por exemplo durante a sposição.
    Pode alterar o nome do visor a qualquer momento e vários registos de aplicações podem partilhar o mesmo nome. O ID de Aplicação (cliente) gerado automaticamente pelo registo da aplicação, não o seu nome de exibição, identifica exclusivamente a sua aplicação dentro da plataforma de identidade.
1. Especificar quem pode usar a aplicação, por vezes chamada *de público de inscrição*.

    | Tipos de conta suportados | Descrição |
    |-------------------------|-------------|
    | **Contas apenas neste diretório organizacional** | Selecione esta opção se estiver a construir uma aplicação para uso apenas pelos utilizadores (ou hóspedes) no *seu* inquilino.<br><br>Muitas vezes chamada de aplicação *line-of-business* (LOB), esta aplicação é uma aplicação *de inquilino único* na plataforma de identidade da Microsoft. |
    | **Contas em qualquer diretório organizacional** | Selecione esta opção se quiser que os utilizadores em *qualquer* inquilino do Azure Ative (Azure AD) possam utilizar a sua aplicação. Esta opção é apropriada se, por exemplo, estiver a construir uma aplicação de software-as-a-service (SaaS) que pretende fornecer a várias organizações.<br><br>Este tipo de aplicação é conhecido como uma aplicação *multitenante* na plataforma de identidade da Microsoft. |
    | **Contas em qualquer diretório organizacional e contas Microsoft pessoais** | Selecione esta opção para visar o maior conjunto de clientes.<br><br>Ao selecionar esta opção, está a registar uma aplicação *multitenante* que também pode suportar utilizadores que tenham *contas pessoais da Microsoft.* |
    | **Contas pessoais da Microsoft** | Selecione esta opção se estiver a construir uma aplicação apenas para utilizadores que tenham contas pessoais da Microsoft. As contas pessoais da Microsoft incluem contas Skype, Xbox, Live e Hotmail. |

1. Não introduza nada para **redirecionar URI (opcional)**. Vai configurar um URI redirecionado na próxima secção.
1. Selecione **Registar-se** para completar o registo inicial da aplicação.

    :::image type="content" source="media/quickstart-register-app/portal-02-app-reg-01.png" alt-text="Screenshot do portal Azure num navegador web, mostrando ao Registo um painel de aplicações.":::

Quando a inscrição termina, o portal Azure exibe o painel **de visão geral** do registo da aplicação. Vê o **ID da Aplicação (cliente).** Também chamado de ID do *cliente,* este valor identifica exclusivamente a sua aplicação na plataforma de identidade microsoft.

O código da sua aplicação, ou mais tipicamente uma biblioteca de autenticação utilizada na sua aplicação, também utiliza o ID do cliente. O ID é usado como parte da validação dos fichas de segurança que recebe da plataforma de identidade.

:::image type="content" source="media/quickstart-register-app/portal-03-app-reg-02.png" alt-text="Screenshot do portal Azure num navegador web, mostrando o painel de visão geral de um registo de aplicações.":::

## <a name="add-a-redirect-uri"></a>Adicione um URI de redirecionamento

Um *URI de redirecionamento* é o local onde a plataforma de identidade da Microsoft redireciona o cliente de um utilizador e envia fichas de segurança após a autenticação.

Numa aplicação web de produção, por exemplo, o URI de redirecionamento é muitas vezes um ponto final público onde a sua aplicação está a funcionar, como `https://contoso.com/auth-response` . Durante o desenvolvimento, é comum também adicionar o ponto final onde você executou a sua app localmente, como `https://127.0.0.1/auth-response` ou `http://localhost/auth-response` .

Adiciona e modifica URIs de redirecionamento para as suas aplicações registadas configurando as definições da [sua plataforma.](#configure-platform-settings)

### <a name="configure-platform-settings"></a>Configurar configurações de plataforma

As definições para cada tipo de aplicação, incluindo URIs de redirecionamento, são configuradas nas **configurações da Plataforma** no portal Azure. Algumas plataformas, como aplicações **Web** e **Single-page,** exigem que especifique manualmente um URI de redirecionamento. Para outras plataformas, como mobile e desktop, pode selecionar a partir de URIs de redirecionamento gerados para si quando configurar as suas outras definições.

Para configurar as definições de aplicações com base na plataforma ou dispositivo que está a direcionar:

1. No portal Azure, nas **inscrições da App,** selecione a sua candidatura.
1. Em **Gestão**, **selecione Autenticação**.
1. Nas **configurações da Plataforma**, selecione Adicionar uma **plataforma**.
1. Nas **plataformas Configure,** selecione o azulejo para o seu tipo de aplicação (plataforma) para configurar as suas definições.

    :::image type="content" source="media/quickstart-register-app/portal-04-app-reg-03-platform-config.png" alt-text="Screenshot do painel de configuração da plataforma no portal Azure." border="false":::

    | Plataforma | Definições de configuração |
    | -------- | ---------------------- |
    | **Web** | Introduza um **URI de redirecionamento** para a sua aplicação. Este URI é o local onde a plataforma de identidade da Microsoft redireciona o cliente de um utilizador e envia fichas de segurança após a autenticação.<br/><br/>Selecione esta plataforma para aplicações web padrão que são executadas num servidor. |
    | **Aplicação de página única** | Introduza um **URI de redirecionamento** para a sua aplicação. Este URI é o local onde a plataforma de identidade da Microsoft redireciona o cliente de um utilizador e envia fichas de segurança após a autenticação.<br/><br/>Selecione esta plataforma se estiver a construir uma aplicação web do lado do cliente utilizando o JavaScript ou uma estrutura como Angular, Vue.js, React.js ou Blazor WebAssembly. |
    | **iOS / macOS** | Introduza o **ID do pacote de** aplicações. Encontre-o em **Definições de Construção** ou em Xcode em *Info.plist*.<br/><br/>Um URI de redirecionamento é gerado para si quando especifica um **Bundle ID**. |
    | **Android** | Introduza o **nome do pacote de** aplicações . Encontre-o no ficheiro *AndroidManifest.xml.* Também gerar e inserir o **hash Signature**.<br/><br/>Um URI de redirecionamento é gerado para si quando especifica estas definições. |
    | **Aplicações móveis e desktop** | Selecione um dos **URIs de redirecionamento sugerido.** Ou especifique um **URI de redirecionamento personalizado.**<br/><br/>Para aplicações de desktop, recomendamos<br/>`https://login.microsoftonline.com/common/oauth2/nativeclient`<br/><br/>Selecione esta plataforma para aplicações móveis que não estejam a usar a mais recente Biblioteca de Autenticação da Microsoft (MSAL) ou que não estejam a utilizar um corretor. Selecione também esta plataforma para aplicações de desktop. |
1. Selecione **Configurar** para completar a configuração da plataforma.

### <a name="redirect-uri-restrictions"></a>Redirecionar as restrições URI

Existem algumas restrições no formato dos URIs redirecionados que adiciona a um registo de aplicações. Para obter detalhes sobre estas restrições, consulte [as restrições e limitações do Redirecionamento URI (URL de resposta).](reply-url.md)

## <a name="add-credentials"></a>Adicionar credenciais

As credenciais são utilizadas por [aplicações confidenciais de clientes](msal-client-applications.md) que acedem a uma API web. Exemplos de clientes confidenciais são [aplicações web,](scenario-web-app-call-api-overview.md)outras [APIs web,](scenario-protected-web-api-overview.md)ou [aplicações do tipo serviço e do tipo daemon.](scenario-daemon-overview.md) As credenciais permitem que a sua aplicação autença como ele próprio, não requerendo qualquer interação de um utilizador em tempo de execução. 

Pode adicionar certificados e segredos de cliente (uma cadeia) como credenciais ao registo confidencial da aplicação do seu cliente.

:::image type="content" source="media/quickstart-register-app/portal-05-app-reg-04-credentials.png" alt-text="Screenshot do portal Azure, mostrando o painel de Certificados e Segredos num registo de aplicações.":::

### <a name="add-a-certificate"></a>Adicione um certificado

Às vezes chamado de *chave pública,* um certificado é o tipo de credencial recomendado. Dá mais garantias do que um segredo de cliente. Para obter mais informações sobre a utilização de um certificado como método de autenticação na sua aplicação, consulte [as credenciais de certificado de autenticação de aplicação da plataforma de identidade da Microsoft](active-directory-certificate-credentials.md).

1. No portal Azure, nas **inscrições da App,** selecione a sua candidatura.
1. Selecione **certificados & segredos**  >  **Enviar certificado de upload**.
1. Selecione o ficheiro que pretende carregar. Deve ser um dos seguintes tipos de ficheiros: *.cer*, *.pem*, *.crt*.
1. Selecione **Adicionar**.

### <a name="add-a-client-secret"></a>Adicione um segredo de cliente

O segredo do cliente também é conhecido como *uma senha de aplicação.* É um valor de cadeia que a sua aplicação pode usar no lugar de um certificado para a própria identidade. O segredo do cliente é o mais fácil dos dois tipos credenciais de usar. É frequentemente usado durante o desenvolvimento, mas é considerado menos seguro que um certificado. Utilize certificados nas suas aplicações que estão em execução em produção. 

Para obter mais informações sobre recomendações de segurança de aplicações, consulte as [melhores práticas e recomendações da plataforma de identidade da Microsoft.](identity-platform-integration-checklist.md#security)

1. No portal Azure, nas **inscrições da App,** selecione a sua candidatura.
1. Selecione **Certificados & segredos**  >   **Novo segredo do cliente.**
1. Adicione uma descrição do segredo do cliente.
1. Selecione uma duração.
1. Selecione **Adicionar**.
1. *Grave o valor do segredo* para uso no código de aplicação do seu cliente. Este valor secreto *nunca mais* é exibido depois de deixar esta página.


## <a name="next-steps"></a>Passos seguintes

As aplicações do cliente normalmente precisam de aceder a recursos numa API web. Pode proteger a sua aplicação de cliente utilizando a plataforma de identidade microsoft. Também pode utilizar a plataforma para autorizar o acesso baseado em permissões à sua API web.

Vá ao próximo quickstart da série para criar outro registo de aplicações para a sua API web e expor os seus âmbitos.

> [!div class="nextstepaction"]
> [Configurar uma aplicação para expor uma API web](quickstart-configure-app-expose-web-apis.md)
