---
title: Adicionar autenticação a APIs personalizadas - Azure Logic Apps | Documentos da Microsoft
description: Configurar a autenticação para chamar APIs personalizadas a partir do Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/22/2017
ms.openlocfilehash: 555083235aff08476e82f0daa81203b66591f3cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66167372"
---
# <a name="secure-calls-to-custom-apis-from-azure-logic-apps"></a>Proteger chamadas a APIs personalizadas no Azure Logic Apps

Para proteger as chamadas para as suas APIs, pode configurar a autenticação do Azure Active Directory (Azure AD) através do portal do Azure para que não tenha de atualizar o seu código. Pode, também, pedir e impor a autenticação através do código da API.

## <a name="authentication-options-for-your-api"></a>Opções de autenticação para a sua API

Proteger as chamadas para a API personalizada nas seguintes formas:

* [Sem alterações de código](#no-code): Proteger a sua API com [do Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) através do portal do Azure, por isso, não precisa atualizar seu código ou voltar a implementar a sua API.

  > [!NOTE]
  > Por predefinição, a autenticação do Azure AD que ativar no portal do Azure não fornece autorização refinada. Por exemplo, esta autenticação bloqueia sua API para apenas um inquilino específico, não para uma aplicação ou utilizador específico. 

* [Atualizar o código da sua API](#update-code): Proteger a sua API através da imposição [autenticação de certificado](#certificate), [autenticação básica](#basic), ou [autenticação do Azure AD](#azure-ad-code) por meio de código.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Autenticar as chamadas para a sua API sem alterar o código

Eis os passos gerais para este método:

1. Criar duas identidades de aplicação do Azure Active Directory (Azure AD): um para a aplicação lógica e uma para a sua aplicação web (ou a aplicação de API).

2. Para autenticar as chamadas para a sua API, utilize as credenciais (ID de cliente e segredo) para o principal de serviço que está associada a identidade da aplicação do Azure AD para a aplicação lógica.

3. Inclui os IDs de aplicação na sua definição da aplicação lógica.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>Parte 1: Criar uma identidade de aplicação do Azure AD para a aplicação lógica

A aplicação lógica utiliza esta identidade de aplicação do Azure AD para autenticar no Azure AD. Apenas terá de configurar esta identidade uma vez para o seu diretório. Por exemplo, pode optar por utilizar a mesma identidade para todas as suas aplicações lógicas, mesmo que pode criar identidades únicas de cada aplicação lógica. Pode configurar estas identidades no portal do Azure ou utilizar [PowerShell](#powershell).

**Criar a identidade da aplicação para a aplicação lógica no portal do Azure**

1. Na [portal do Azure](https://portal.azure.com "https://portal.azure.com"), escolha **do Azure Active Directory**. 

2. Confirme que está no mesmo diretório como a aplicação web ou a aplicação API.

   > [!TIP]
   > Para mudar de diretórios, escolha o seu perfil e selecione outro diretório. Em alternativa, escolha **descrição geral** > **trocar diretório**.

3. No menu do diretório, sob **Manage**, escolha **registos das aplicações** > **novo registo de aplicação**.

   > [!TIP]
   > Por predefinição, a lista de registos de aplicação mostra todos os registos de aplicações no seu diretório. Para ver apenas os registos de aplicações, ao lado da caixa de pesquisa, selecione **as minhas aplicações**. 

   ![Criar novo registo de aplicação](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. Dê um nome de sua identidade da aplicação, deixe **tipo de aplicação** definida como **aplicação Web / API**, fornecer uma única cadeia de caracteres formatada como um domínio para **URL de início de sessão**e selecione  **Criar**.

   ![Forneça o nome e início de sessão no URL para a identidade da aplicação](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   A identidade da aplicação que criou para a aplicação lógica agora aparece na lista de registos de aplicações.

   ![Identidade da aplicação para a aplicação lógica](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. Na lista de registos de aplicações, selecione a sua identidade de aplicação nova. Copie e guarde o **ID da aplicação** para utilizar como o "ID de cliente" para a aplicação de lógica na parte 3.

   ![Copie e guarde o ID da aplicação para a aplicação lógica](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. Se as definições de identidade da aplicação não são visíveis, escolha **configurações** ou **todas as definições**.

7. Sob **acesso à API**, escolha **chaves**. Sob **Descrição**, forneça um nome para a sua chave. Sob **Expires**, selecione uma duração para a sua chave.

   A chave que está a criar atua como a identidade da aplicação "segredo" ou palavra-passe para a aplicação lógica.

   ![Criar a chave para a identidade de aplicação lógica](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. Na barra de ferramentas, escolha **guardar**. Sob **valor**, a chave é agora apresentada. 
**Não se esqueça de copiar e guardar a chave** para utilização posterior porque a chave está oculto quando deixar o **chaves** página.

   Ao configurar a aplicação lógica na parte 3, especifique esta chave como o "segredo" ou a palavra-passe.

   ![Copie e guarde a chave para utilizar mais tarde](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**Criar a identidade da aplicação para a aplicação lógica no PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode executar esta tarefa através do Azure Resource Manager com o PowerShell. No PowerShell, execute estes comandos:

1. `Add-AzAccount`

2. `$SecurePassword = Read-Host -AsSecureString` (Introduza uma palavra-passe e clique em enter)

3. `New-AzADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password $SecurePassword`

4. Certifique-se copiar o **ID do inquilino** (GUID para o seu inquilino do Azure AD), o **ID de aplicação**e a palavra-passe que utilizou.

Para obter mais informações, saiba como [criar um principal de serviço com o PowerShell para aceder aos recursos](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>Parte 2: Criar uma identidade de aplicação do Azure AD para a sua aplicação web ou a aplicação API

Se a aplicação de API ou a aplicação web já está implementada, pode ativar a autenticação e criar a identidade da aplicação no portal do Azure. Caso contrário, pode [ativar a autenticação ao implementar com um modelo Azure Resource Manager](#authen-deploy). 

**Criar a identidade da aplicação e ativar a autenticação no portal do Azure para aplicações implementadas**

1. Na [portal do Azure](https://portal.azure.com "https://portal.azure.com"), localize e selecione a aplicação web ou a aplicação API. 

2. Sob **configurações**, escolha **autenticação/autorização**. Sob **autenticação de serviço de aplicações**, ativar a autenticação **no**. Sob **provedores de autenticação**, escolha **Azure Active Directory**.

   ![Ativar a autenticação](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. Agora crie uma identidade de aplicação para a sua aplicação web ou a aplicação API, como mostrado aqui. Sobre o **definições de diretório do Azure Active Directory** página, defina **modo de gestão** para **Express**. Escolher **criar nova aplicação AD**. Dê um nome de sua identidade da aplicação e escolha **OK**. 

   ![Criar a identidade da aplicação para a sua aplicação web ou a aplicação API](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. Na página **Autenticação/Autorização**, escolha **Guardar**.

Agora tem de encontrar o cliente ID e o ID de inquilino para a identidade da aplicação que está associada à sua aplicação web ou a aplicação de API. Utilize estes IDs de na parte 3. Então, prossiga com estes passos no portal do Azure.

**Encontrar o ID de cliente e o ID de inquilino da identidade da aplicação para a sua aplicação web ou a aplicação API no portal do Azure**

1. Sob **provedores de autenticação**, escolha **Azure Active Directory**. 

   ![Escolher o "Azure Active Directory"](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. Sobre o **definições de diretório do Azure Active Directory** página, defina **modo de gestão** para **avançadas**.

3. Copiar o **ID de cliente**e guarde esse GUID para utilização na parte 3.

   > [!TIP] 
   > Se **ID de cliente** e **Url de emissor** não aparecer, tente atualizar o portal do Azure e repita o passo 1.

4. Sob **Url de emissor**, copie e guarde apenas o GUID para a parte 3. Também pode utilizar este GUID na sua aplicação web ou um modelo de implementação da aplicação API, se necessário.

   Este GUID é o GUID de um inquilino específico ("ID de inquilino") e deve aparecer neste URL: `https://sts.windows.net/{GUID}`

5. Sem guardar as alterações, feche o **definições de diretório do Azure Active Directory** página.

<a name="authen-deploy"></a>

**Ativar a autenticação ao implementar com um modelo Azure Resource Manager**

Ainda deverá criar uma identidade de aplicação do Azure AD para a sua aplicação web ou a aplicação de API diferente desde a identidade da aplicação para a aplicação lógica. Para criar a identidade da aplicação, siga os passos anteriores, na parte 2, no portal do Azure. 

Também pode seguir os passos na parte 1, mas não se esqueça de utilizar a aplicação web ou da aplicação API real `https://{URL}` para **URL de início de sessão** e **App ID URI**. Destes passos, terá de guardar o ID de cliente e o ID de inquilino para utilizar no modelo de implementação da sua aplicação e também para a parte 3.

> [!NOTE]
> Ao criar a identidade da aplicação do Azure AD para a sua aplicação web ou a aplicação API, tem de utilizar o portal do Azure, não do PowerShell. O commandlet do PowerShell não configurar as permissões necessárias para se conectar aos utilizadores a um Web site.

Depois de obter a client ID e o ID de inquilino, inclua estes IDs de como um recurso secundário da sua aplicação web ou a aplicação API no seu modelo de implementação:

``` json
"resources": [ {
    "apiVersion": "2015-08-01",
    "name": "web",
    "type": "config",
    "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
    "properties": {
        "siteAuthEnabled": true,
        "siteAuthSettings": {
            "clientId": "{client-ID}",
            "issuer": "https://sts.windows.net/{tenant-ID}/",
        }
    }
} ]
```

Para implementar automaticamente uma aplicação web em branco e uma aplicação lógica, juntamente com a autenticação do Azure Active Directory, [ver o modelo completo aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json), ou clique em **implementar no Azure** aqui:

[![Implementar no Azure](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Parte 3: Preencha a secção de autorização na sua aplicação lógica

O modelo anterior já tem esta secção de autorização, configurar, mas se estiver a criar diretamente a aplicação lógica, tem de incluir a secção de autorização completa.

Abra sua definição da aplicação lógica na vista de código, vá para o **HTTP** secção de ação, localizar o **autorização** secção e incluir essa linha:

`{"tenant": "{tenant-ID}", "audience": "{client-ID-from-Part-2-web-app-or-API app}", "clientId": "{client-ID-from-Part-1-logic-app}", "secret": "{key-from-Part-1-logic-app}", "type": "ActiveDirectoryOAuth" }`

| Elemento | Necessário | Descrição | 
| ------- | -------- | ----------- | 
| tenant | Sim | O GUID para o inquilino do Azure AD | 
| audience | Sim | O GUID para o recurso de destino que pretende aceder, que é o ID de cliente desde a identidade da aplicação para a sua aplicação web ou a aplicação API | 
| clientId | Sim | O GUID para o cliente pedir o acesso, o que é o ID de cliente desde a identidade da aplicação para a aplicação lógica | 
| secret | Sim | A chave ou palavra-passe desde a identidade da aplicação para o cliente que está a pedir o token de acesso | 
| type | Sim | O tipo de autenticação. Para a autenticação de ActiveDirectoryOAuth, o valor é `ActiveDirectoryOAuth`. | 
|||| 

Por exemplo:

``` json
{
   "actions": {
      "some-action": {
         "conditions": [],
         "inputs": {
            "method": "post",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         },
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>Chamadas de API seguras por meio de código

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Autenticação de certificados

Para validar pedidos recebidos da sua aplicação lógica à sua aplicação web ou a aplicação API, pode utilizar certificados de cliente. Para configurar o seu código, saiba [como configurar a autenticação mútua de TLS](../app-service/app-service-web-configure-tls-mutual-auth.md).

Na **autorização** secção, incluir esta linha: 

`{"type": "clientcertificate", "password": "password", "pfx": "long-pfx-key"}`

| Elemento | Necessário | Descrição | 
| ------- | -------- | ----------- | 
| type | Sim | O tipo de autenticação. Para certificados de cliente SSL, o valor tem de ser `ClientCertificate`. | 
| password | Sim | A palavra-passe para aceder ao certificado de cliente (arquivo PFX) | 
| pfx | Sim | O conteúdo codificado em base64 do certificado de cliente (arquivo PFX) | 
|||| 

<a name="basic"></a>

#### <a name="basic-authentication"></a>Autenticação básica

Para validar pedidos recebidos da sua aplicação lógica à sua aplicação web ou a aplicação API, pode utilizar a autenticação básica, como um nome de utilizador e palavra-passe. A autenticação básica é um padrão comum, e pode usar esta autenticação em qualquer linguagem utilizada para criar a sua aplicação web ou a aplicação de API.

Na **autorização** secção, incluir esta linha:

`{"type": "basic", "username": "username", "password": "password"}`.

| Elemento | Necessário | Descrição | 
| ------- | -------- | ----------- | 
| type | Sim | O tipo de autenticação que pretende utilizar. Para a autenticação básica, o valor tem de ser `Basic`. | 
| username | Sim | O nome de utilizador que pretende utilizar para autenticação | 
| password | Sim | A palavra-passe que pretende utilizar para autenticação | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Autenticação do Active Directory do Azure através de código

Por predefinição, a autenticação do Azure AD que ativar no portal do Azure não fornece autorização refinada. Por exemplo, esta autenticação bloqueia sua API para apenas um inquilino específico, não para uma aplicação ou utilizador específico. 

Para restringir o acesso de API à sua aplicação lógica por meio de código, extraia o cabeçalho que tem o JSON web token (JWT). Verifique a identidade do chamador e rejeitar pedidos que não correspondem.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/overview-authentication-authorization.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>Passos Seguintes

* [Implementar e chamar APIs personalizadas da lógica de fluxos de trabalho de aplicação](../logic-apps/logic-apps-custom-api-host-deploy-call.md)
