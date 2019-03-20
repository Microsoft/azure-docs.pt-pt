---
title: Autenticação baseada em cabeçalho com o PingAccess para o Proxy de aplicações do Azure AD | Documentos da Microsoft
description: Publica aplicações com o Proxy de aplicações e o PingAccess para suportar a autenticação com base no cabeçalho.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/11/2017
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4df8f329a135683ea68896605a0a1c6f3ee45984
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58084086"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Autenticação baseada em cabeçalho para início de sessão único com o Proxy de aplicações e o PingAccess

O Azure Active Directory para o Proxy de aplicações e o PingAccess estabeleceram uma parceria em conjunto para fornecer aos clientes do Azure Active Directory com acesso ao mesmo mais aplicações. PingAccess expande a [ofertas de Proxy de aplicações atuais](application-proxy.md) para incluir o acesso de início de sessão único para aplicações que utilizam cabeçalhos para autenticação.

## <a name="what-is-pingaccess-for-azure-ad"></a>O que é o PingAccess para Azure AD?

PingAccess para o Azure Active Directory é uma oferta do PingAccess que permite que dê acesso de utilizadores e início de sessão único para aplicações que utilizam cabeçalhos para autenticação. Proxy de aplicações trata destas aplicações, como qualquer outro, utilizar o Azure AD para autenticar o acesso e, em seguida, passar o tráfego através do serviço de conector. PingAccess encontra-se à frente das aplicações e traduz-se o token de acesso do Azure AD num cabeçalho para que o aplicativo recebe a autenticação no formato pode ler.

Os usuários não notará nada diferente quando iniciam sessão para utilizar as suas aplicações empresariais. Pode ainda trabalham de qualquer lugar em qualquer dispositivo. 

Uma vez que os conectores de Proxy de aplicações direcionar o tráfego remoto para todas as aplicações, independentemente de seu tipo de autenticação, eles continuarão balanceamento de carga automaticamente, bem.

## <a name="how-do-i-get-access"></a>Como posso obter acesso?

Uma vez que este cenário é disponibilizado através de uma parceria entre o Azure Active Directory e o PingAccess, precisa de licenças para ambos os serviços. No entanto, as subscrições do Azure Active Directory Premium incluem uma licença de PingAccess básica, que abrange até 20 aplicações. Se precisar de mais de 20 aplicativos baseados no cabeçalho de publicar, pode comprar uma licença adicional do PingAccess. 

Para obter mais informações, consulte [Edições do Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Publicar a sua aplicação no Azure

Este artigo destina-se para as pessoas que estão a publicar uma aplicação com este cenário pela primeira vez. Ele explica como começar com a aplicação e o PingAccess, além dos passos de publicação. Se já tiver configurado a ambos os serviços mas relembrar os passos de publicação, pode ignorar a instalação do conector e continua [adicionar a sua aplicação para o Azure AD com o Proxy de aplicações](#add-your-app-to-Azure-AD-with-Application-Proxy).

>[!NOTE]
>Uma vez que este cenário é uma parceria entre o Azure AD e o PingAccess, algumas das instruções existem no site da Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Instalar um conector de Proxy de aplicações

Se já tiver ativado o Proxy de aplicações e ter um conector instalado, pode ignorar esta secção e continua [adicionar a sua aplicação para o Azure AD com o Proxy de aplicações](#add-your-app-to-azure-ad-with-application-proxy).

O conector de Proxy de aplicações é um serviço do Windows Server que direciona o tráfego dos funcionários às suas aplicações publicadas. Para obter mais instruções de instalação, consulte [ativar o Proxy de aplicações no portal do Azure](application-proxy-add-on-premises-application.md).

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) como administrador global.
2. Selecione **do Azure Active Directory** > **proxy de aplicações**.
3. Selecione **transferir o conector** para iniciar o download do conector de Proxy de aplicações. Siga as instruções de instalação.

   ![Ativar o Proxy de aplicações e transferir o conector](./media/application-proxy-configure-single-sign-on-with-ping-access/install-connector.png)

4. Transferir o conector automaticamente deve ativar Proxy de aplicações para o seu diretório, mas se não é possível selecionar **ativar o Proxy de aplicação**.


### <a name="add-your-app-to-azure-ad-with-application-proxy"></a>Adicionar a sua aplicação para o Azure AD com o Proxy de aplicações

Existem duas ações que necessárias no portal do Azure. Em primeiro lugar, terá de publicar seu aplicativo com o Proxy de aplicações. Em seguida, terá de recolher algumas informações sobre a aplicação que podem ser usados durante os passos do PingAccess.

Siga estes passos para publicar a aplicação. Para um mais detalhadas passo a passo dos passos 1 a 8, consulte [publicar aplicações com o Proxy de aplicações do Azure AD](application-proxy-add-on-premises-application.md).

1. Se não soubéssemos na última seção, inicie sessão para o [portal do Azure](https://portal.azure.com) como um administrador global.
2. Selecione **do Azure Active Directory** > **aplicações empresariais**.
3. Selecione **adicionar** na parte superior do painel.
4. Selecione **aplicação no local**.
5. Preencha os campos obrigatórios com informações sobre a sua nova aplicação. Utilize as seguintes orientações para as definições:
   - **URL interno**: Normalmente fornecer o URL que leva-o para a página de início de sessão da aplicação quando estiver na rede empresarial. Para este cenário o conector tem de tratar o proxy do PingAccess como a página da aplicação. Utilize este formato: `https://<host name of your PA server>:<port>`. A porta é 3000 por predefinição, mas pode configurá-lo no PingAccess.

     > [!WARNING]
     > Para este tipo de SSO, o URL interno tem de utilizar https e não é possível utilizar o http.

   - **Método de pré-autenticação**: Azure Active Directory
   - **Traduzir URL nos cabeçalhos**: Não

   >[!NOTE]
   >Se esta for a sua primeira aplicação, utilize a porta 3000 para iniciar e voltar atrás para atualizar esta definição se alterar a configuração do PingAccess. Se esta for a sua aplicação de segundo ou posterior, isto terá de acordo com o serviço de escuta que configurou no PingAccess. Saiba mais sobre [serviços de escuta no PingAccess](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html).

6. Selecione **adicionar** na parte inferior do painel. Seu aplicativo é adicionado e abre o menu de início rápido.
7. No menu de início rápido, selecione **atribuir um utilizador para fins de teste**, e adicionar pelo menos um utilizador à aplicação. Certifique-se de que esta conta de teste tenha acesso à aplicação no local.
8. Selecione **atribuir** para guardar a atribuição de utilizador de teste.
9. No painel de gestão da aplicação, selecione **início de sessão único**.
10. Escolher **com base no cabeçalho de início de sessão** no menu pendente. Selecione **Guardar**.

    >[!TIP]
    >Se esta for a primeira vez que utilizar com base no cabeçalho de início de sessão único, terá de instalar o PingAccess. Para certificar-se de que a sua subscrição do Azure é associada automaticamente a sua instalação do PingAccess, utilize a ligação nesta página de início de sessão único para transferir o PingAccess. Pode abrir o site de download agora ou voltar a esta página mais tarde. 

    ![Selecione baseado em cabeçalho de início de sessão](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.PNG)

11. Feche o painel de aplicações da empresa ou desloque-se até à esquerda para regressar ao menu do Azure Active Directory.
12. Selecione **Registos das aplicações**.

    ![Selecionar registos das aplicações](./media/application-proxy-configure-single-sign-on-with-ping-access/app-registrations.png)

13. Selecione a aplicação que acabou de adicionar, em seguida, **URLs de resposta**.

    ![Selecione os URLs de resposta](./media/application-proxy-configure-single-sign-on-with-ping-access/reply-urls.png)

14. Verifique se a externo, é URL que atribuiu à sua aplicação no passo 5, na lista de URLs de resposta. Se não estiver, adicione-o agora.
15. No painel de definições da aplicação, selecione **permissões obrigatórias**.

    ![Selecione as permissões necessárias](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

16. Selecione **Adicionar**. Para a API, escolha **Windows Azure Active Directory**, em seguida, **selecione**. Para as permissões, escolha **leitura e escrever todos os aplicativos** e **iniciar sessão e ler o perfil de utilizador**, em seguida, **selecione** e **feito**.  

    ![Selecionar permissões](./media/application-proxy-configure-single-sign-on-with-ping-access/select-permissions.png)

17. Conceder permissões antes de fechar o ecrã de permissões. 
    ![Conceder permissões](./media/application-proxy-configure-single-sign-on-with-ping-access/grantperms.png)

### <a name="collect-information-for-the-pingaccess-steps"></a>Recolher informações para obter os passos do PingAccess

1. No painel de definições da aplicação, selecione **propriedades**. 

   ![Selecionar propriedades](./media/application-proxy-configure-single-sign-on-with-ping-access/properties.png)

2. Guardar a **Id da aplicação** valor. Isto é utilizado para o ID de cliente quando configurar o PingAccess.
3. No painel de definições da aplicação, selecione **chaves**.

   ![Selecione chaves](./media/application-proxy-configure-single-sign-on-with-ping-access/Keys.png)

4. Crie uma chave ao introduzir uma descrição da chave e escolher uma data de expiração no menu pendente.
5. Selecione **Guardar**. É apresentado um GUID no **valor** campo.

   Guarde este valor agora, como não poderá vê-lo novamente depois de fechar esta janela.

   ![Criar uma chave nova](./media/application-proxy-configure-single-sign-on-with-ping-access/create-keys.png)

6. Feche o painel de registos de aplicação ou desloque-se até à esquerda para regressar ao menu do Azure Active Directory.
7. Selecione **propriedades**.
8. Guardar a **ID de diretório** GUID.

### <a name="optional---update-graphapi-to-send-custom-fields"></a>Opcional - Graph de atualização para enviar os campos personalizados

Para obter uma lista de tokens de segurança do Azure AD envia para a autenticação, consulte [referência de token do Azure AD](../develop/v1-id-and-access-tokens.md). Se precisar de uma declaração personalizada que envia os tokens de outros, utilize do Graph ou o manifesto da aplicação no Portal do Azure para definir o campo de aplicação *acceptMappedClaims* ao **verdadeiro**.    

Este exemplo utiliza a API do Graph:

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application> 

{
  "acceptMappedClaims":true
}
```
Este exemplo utiliza a [portal do Azure](https://portal.azure.com) para atualizar a *acceptedMappedClaims* campo:
1. Inicie sessão no [Portal do Azure](https://portal.azure.com) como administrador global.
2. Selecione **do Azure Active Directory** > **registos das aplicações**.
3. Selecione a aplicação > **manifesto**.
4. Selecione **edite**, procure o *acceptedMappedClaims* campo e altere o valor para **verdadeiro**.
![Manifesto da aplicação](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-ping-access-manifest.PNG)
1. Selecione **Guardar**.

>[!NOTE]
>Para utilizar uma declaração personalizada, também tem de ter uma política personalizada definida e atribuída à aplicação.  Esta política deve incluir todos os atributos personalizados necessários.
>
>Definição de política e a atribuição podem ser feitos através do PowerShell, do Azure AD Graph ou MS Graph.  Se estiver fazendo isso no PowerShell, poderá ter de utilizar pela primeira vez `New-AzureADPolicy `e, em seguida, atribua-o para a aplicação com `Set-AzureADServicePrincipalPolicy`.  Para obter mais informações, consulte a [documentação de política do Azure AD](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

### <a name="optional---use-a-custom-claim"></a>Opcional – utilize uma declaração personalizada
Para tornar a sua aplicação utilizar uma declaração personalizada e incluir campos adicionais, não se esqueça de que tem também [criou uma política de mapeamento de afirmações personalizadas e Atribuímos à aplicação](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

## <a name="download-pingaccess-and-configure-your-app"></a>Transfira o PingAccess e configurar a sua aplicação

Agora que concluiu todos os passos de configuração do Azure Active Directory, pode mover-se para configurar o PingAccess. 

Os passos detalhados para a parte do PingAccess deste cenário continuam na documentação do Ping Identity, [configurar o PingAccess para o Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html).

Esses passos guiá-lo pelo processo de obter uma conta do PingAccess se ainda não tiver uma, instalar o servidor do PingAccess e criar uma ligação do fornecedor de OIDC do Azure AD com o ID de diretório que copiou do portal do Azure. Em seguida, utilize os valores de ID da aplicação e a chave para criar uma sessão de Web em PingAccess. Depois disso, pode configurar o mapeamento de identidades e criar um anfitrião virtual, o site e o aplicativo.

### <a name="test-your-app"></a>Testar a aplicação

Quando tiver concluído todas essas etapas, a aplicação deve estar em execução. Para testá-lo, abra um browser e navegue para o URL externo que criou quando publicado a aplicação no Azure. Inicie sessão com a conta de teste que atribuiu à aplicação.

## <a name="next-steps"></a>Passos Seguintes

- [Configurar o PingAccess para o Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [Como o Proxy de aplicações do Azure AD fornece início de sessão único?](application-proxy-single-sign-on.md)
- [Resolver problemas de Proxy de aplicações](application-proxy-troubleshoot.md)
