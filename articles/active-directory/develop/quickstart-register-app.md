---
title: 'Quickstart: Registar uma aplicação na plataforma de identidade da Microsoft Rio Azure'
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
ms.custom: aaddev, identityplatformtop40, contperfq1, contentperfq2
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 860cd24c623cb6db407c82aa81e2c3662da289ed
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96762980"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Quickstart: Registar uma aplicação com a plataforma de identidade microsoft

Neste arranque rápido, regista-se uma aplicação no portal Azure para que a plataforma de identidade da Microsoft possa fornecer serviços de autenticação e autorização para a sua aplicação e para os seus utilizadores.

Cada aplicação deseja que a plataforma de identidade da Microsoft execute a gestão de identidade e acesso (IAM) para que seja registada. Seja uma aplicação de cliente como uma web ou aplicação móvel, ou é uma API web que apoia uma aplicação de cliente, registando-a estabelece uma relação de confiança entre a sua aplicação e o fornecedor de identidade, a plataforma de identidade da Microsoft.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa - [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Conclusão do [Quickstart: Criar um inquilino](quickstart-create-new-tenant.md)

## <a name="register-an-application"></a>Registar uma aplicação

O registo da sua aplicação estabelece uma relação de confiança entre a sua app e a plataforma de identidade da Microsoft. A confiança é unidirecional: a sua aplicação confia na plataforma de identidade da Microsoft, e não o contrário.

Siga estes passos para criar o registo da aplicação:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
1. Procure e selecione **Azure Active Directory**.
1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
1. Insira um **Nome** para a sua inscrição. Os utilizadores da sua aplicação podem ver este nome, e pode alterá-lo mais tarde.
1. Especificar quem pode utilizar a aplicação, por vezes referida como *o público de inscrição*.

    | Tipos de conta suportados | Descrição |
    |-------------------------|-------------|
    | **Contas apenas neste diretório organizacional** | Selecione esta opção se estiver a construir uma aplicação para uso apenas pelos utilizadores (ou hóspedes) no *seu* inquilino.<br><br>Muitas vezes chamado de aplicação *line-of-business* (LOB), esta é uma aplicação **de inquilino único** na plataforma de identidade da Microsoft. |
    | **Contas em qualquer diretório organizacional** | Selecione esta opção se quiser que os utilizadores de *qualquer* inquilino AZure AD possam usar a sua aplicação. Esta opção é apropriada se, por exemplo, estiver a construir uma aplicação de software-as-a-service (SaaS) que pretende fornecer a várias organizações.<br><br>Isto é conhecido como uma aplicação **multi-inquilino** na plataforma de identidade da Microsoft. |
    | **Contas em qualquer diretório organizacional e contas Microsoft pessoais** | Selecione esta opção para visar o maior conjunto de clientes.<br><br>Ao selecionar esta opção, está a registar uma aplicação **multi-inquilino** que também pode suportar utilizadores com **contas pessoais da Microsoft** (MSA). |
    | **Contas pessoais da Microsoft** | Selecione esta opção se estiver a construir uma aplicação para utilização apenas por utilizadores com contas pessoais da Microsoft. As contas pessoais da Microsoft incluem contas Skype, Xbox, Live e Hotmail. |

1. Não introduza nada para **Redirecionar URI (opcional)**, configurará um na secção seguinte.
1. Selecione **Registar-se** para completar o registo inicial da aplicação.

    :::image type="content" source="media/quickstart-register-app/portal-02-app-reg-01.png" alt-text="Screenshot do portal Azure num navegador web mostrando ao Registo um painel de aplicações.":::

Quando o registo termina, o portal Azure exibe o painel **de visão geral** do registo da aplicação, que inclui o seu **ID de Aplicação (cliente).** Também referido como ID apenas *do cliente,* este valor identifica exclusivamente a sua aplicação na plataforma de identidade microsoft.

O código da sua aplicação, ou mais tipicamente uma biblioteca de autenticação utilizada na sua aplicação, também utiliza o ID do cliente como um aspeto na validação dos fichas de segurança que recebe da plataforma de identidade.

:::image type="content" source="media/quickstart-register-app/portal-03-app-reg-02.png" alt-text="Screenshot do portal Azure num navegador web mostrando o painel de visão geral de um registo de aplicações.":::

## <a name="add-a-redirect-uri"></a>Adicione um URI de redirecionamento

Um URI de redirecionamento é o local onde a plataforma de identidade da Microsoft redireciona o cliente de um utilizador e envia fichas de segurança após a autenticação.

Numa aplicação web de produção, por exemplo, o URI de redirecionamento é muitas vezes um ponto final público onde a sua aplicação está a funcionar, como `https://contoso.com/auth-response` . Durante o desenvolvimento, é comum também adicionar o ponto final onde você executou a sua app localmente, como `https://127.0.0.1/auth-response` ou `http://localhost/auth-response` .

Adiciona e modifica URIs de redirecionamento para as suas aplicações registadas configurando as definições da [sua plataforma.](#configure-platform-settings)

### <a name="configure-platform-settings"></a>Configurar configurações de plataforma

As definições para cada tipo de aplicação, incluindo URIs de redirecionamento, são configuradas nas **configurações da Plataforma** no portal Azure. Algumas plataformas, como aplicações **Web** e **Single-page,** exigem que especifique manualmente um URI de redirecionamento. Para outras plataformas, como mobile e desktop, pode selecionar a partir de URIs de redirecionamento gerados para si quando configurar as suas outras definições.

Para configurar as definições de aplicações com base na plataforma ou dispositivo que está a direcionar:

1. Selecione a sua candidatura em **registos de Aplicações** no portal Azure.
1. Em **Gestão**, **selecione Autenticação**.
1. Nas **configurações da Plataforma**, selecione Adicionar uma **plataforma**.
1. Nas **plataformas Configure,** selecione o azulejo para o seu tipo de aplicação (plataforma) para configurar as suas definições.

    :::image type="content" source="media/quickstart-register-app/portal-04-app-reg-03-platform-config.png" alt-text="Screenshot do painel de configuração da Plataforma no portal Azure" border="false":::

    | Plataforma | Definições de configuração |
    | -------- | ---------------------- |
    | **Web** | Introduza um **URI de redirecionamento** para a sua aplicação, o local onde a plataforma de identidade da Microsoft redireciona o cliente de um utilizador e envia fichas de segurança após a autenticação.<br/><br/>Selecione esta plataforma para aplicações web padrão que são executadas num servidor. |
    | **Aplicação de página única** | Introduza um **URI de redirecionamento** para a sua aplicação, o local onde a plataforma de identidade da Microsoft redireciona o cliente de um utilizador e envia fichas de segurança após a autenticação.<br/><br/>Selecione esta plataforma se estiver a construir uma aplicação web do lado do cliente no JavaScript ou com uma estrutura como Angular, Vue.js, React.js ou Blazor WebAssembly. |
    | **iOS / macOS** | Introduza o **ID do pacote de** aplicações, encontrado no XCode em *Info.plist* ou Build Settings.<br/><br/>Um URI de redirecionamento é gerado para si quando especifica um Bundle ID. |
    | **Android** | Introduza o **nome do pacote** de aplicações , que pode encontrar no ficheiro *AndroidManifest.xml,* e gerar e introduzir o **hash Signature**.<br/><br/>Um URI de redirecionamento é gerado para si quando especifica estas definições. |
    | **Aplicações móveis e desktop** | Selecione um dos **URIs de redirecionamento sugerido** ou especifique um **URI de redirecionamento personalizado**.<br/>Para aplicações de desktop, recomendamos:<br/>`https://login.microsoftonline.com/common/oauth2/nativeclient`<br/><br/>Selecione esta plataforma para aplicações móveis que não estejam a usar a mais recente Biblioteca de Autenticação da Microsoft (MSAL) ou que não estejam a utilizar um corretor. Selecione também esta plataforma para aplicações de desktop. |
1. Selecione **Configurar** para completar a configuração da plataforma.

### <a name="redirect-uri-restrictions"></a>Redirecionar as restrições URI

Existem certas restrições no formato dos URIs de redirecionamento que adiciona a um registo de aplicações. Para obter detalhes sobre estas restrições, consulte [as restrições e limitações do Redirecionamento URI (URL de resposta).](reply-url.md)

## <a name="add-credentials"></a>Adicionar credenciais

As credenciais são utilizadas por aplicações confidenciais de clientes que acedem a uma API web. Exemplos de clientes confidenciais são aplicações web, outras APIs web ou aplicações do tipo serviço e daemon. As credenciais permitem que a sua aplicação autença como ele próprio, não requerendo qualquer interação de um utilizador em tempo de execução.

Pode adicionar certificados e segredos de cliente (uma cadeia) como credenciais ao registo confidencial da aplicação do seu cliente.

:::image type="content" source="media/quickstart-register-app/portal-05-app-reg-04-credentials.png" alt-text="Screenshot do portal Azure mostrando o painel de certificados e segredos em um registo de App":::

### <a name="add-a-certificate"></a>Adicione um certificado

Por vezes chamados de *chave pública,* os certificados são o tipo de credencial recomendado, uma vez que fornecem um nível de garantia mais elevado do que o segredo de um cliente.

1. Selecione a sua candidatura em **registos de Aplicações** no portal Azure.
1. Selecione **certificados & segredos**  >  **Enviar certificado de upload**.
1. Selecione o ficheiro que pretende carregar. Tem de ser do tipo .cer, .pem ou .crt.
1. Selecione **Adicionar**.

### <a name="add-a-client-secret"></a>Adicione um segredo de cliente

O segredo do cliente, também conhecido como senha de *aplicação,* é um valor de cadeia que a sua aplicação pode usar em vez de um certificado para a própria identidade. É o mais fácil dos dois tipos credenciais para usar e é frequentemente usado durante o desenvolvimento, mas é considerado menos seguro do que um certificado. Deve utilizar certificados nas suas aplicações em execução em produção.

1. Selecione a sua candidatura em **registos de Aplicações** no portal Azure.
1. Selecione **Certificados & segredos**  >   **Novo segredo do cliente.**
1. Adicione uma descrição do segredo do cliente.
1. Selecione uma duração.
1. Selecione **Adicionar**.
1. **Grave o valor de** utilização do segredo no código de aplicação do seu cliente - *nunca mais* é exibido depois de deixar esta página.

## <a name="next-steps"></a>Passos seguintes

As aplicações do cliente normalmente precisam de aceder a recursos numa API web. Além de proteger a aplicação do seu cliente com a plataforma de identidade da Microsoft, pode utilizar a plataforma para autorizar o acesso baseado em permissões à sua API web.

Passe para o próximo quickstart da série para criar outro registo de aplicações para a sua API web e expor os seus âmbitos.

> [!div class="nextstepaction"]
> [Configurar uma aplicação para expor uma API web](quickstart-configure-app-expose-web-apis.md)
