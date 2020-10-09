---
title: Adicionar autenticação para garantir chamadas a APIs personalizados
description: Como configurar a autenticação para melhorar a segurança das chamadas para APIs personalizadas a partir de Apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/22/2017
ms.openlocfilehash: 5e0dcd478c6eb6696a0e07d35d4dccddac68ac1c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80656240"
---
# <a name="increase-security-for-calls-to-custom-apis-from-azure-logic-apps"></a>Aumentar a segurança das chamadas para APIs personalizadas das Azure Logic Apps

Para melhorar a segurança das chamadas para as suas APIs, pode configurar a autenticação do Azure Ative Directory (Azure AD) através do portal Azure para que não tenha de atualizar o seu código. Pode, também, pedir e impor a autenticação através do código da API.

## <a name="authentication-options-for-your-api"></a>Opções de autenticação para a sua API

Pode melhorar a segurança das chamadas para a sua API personalizada desta forma:

* [Nenhuma alteração de código](#no-code): Proteja a sua API com [o Azure Ative Directory (AD)](../active-directory/fundamentals/active-directory-whatis.md) através do portal Azure, para que não tenha de atualizar o seu código ou recolocar a sua API.

  > [!NOTE]
  > Por predefinição, a autenticação AZURE AD que liga no portal Azure não fornece uma autorização de grãos finos. Por exemplo, esta autenticação bloqueia a sua API apenas a um inquilino específico, e não a um utilizador ou app específico. 

* [Atualizar o código da API](#update-code): Proteja a sua API através da aplicação da [autenticação](#certificate)de certificados, [autenticação básica](#basic)ou [autenticação AZure AD](#azure-ad-code) através de código.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Autenticar chamadas para a sua API sem alterar código

Aqui estão os passos gerais para este método:

1. Crie duas identidades de aplicação Azure Ative (Azure AD): uma para a sua aplicação lógica e outra para a sua aplicação web (ou app API).

2. Para autenticar chamadas para a sua API, utilize as credenciais (ID do cliente e segredo) para o responsável pelo serviço que está associado à identidade da aplicação AZure AD para a sua aplicação lógica.

3. Inclua os IDs de aplicação na definição de aplicação lógica.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>Parte 1: Criar uma identidade de aplicação AD Azure para a sua aplicação lógica

A sua aplicação lógica utiliza esta identidade de aplicação AD Azure para autenticar contra a Azure AD. Só tens de configurar esta identidade uma vez para o teu diretório. Por exemplo, pode optar por usar a mesma identidade para todas as suas aplicações lógicas, mesmo que possa criar identidades únicas para cada aplicação lógica. Pode configurar estas identidades no portal Azure ou utilizar [o PowerShell](#powershell).

**Crie a identidade da aplicação para a sua aplicação lógica no portal Azure**

1. No [portal Azure,](https://portal.azure.com "https://portal.azure.com")escolha **Azure Ative Directory**. 

2. Confirme que está no mesmo diretório que a sua aplicação web ou app API.

   > [!TIP]
   > Para mudar de diretório, escolha o seu perfil e selecione outro diretório. Ou, escolha **o**  >  **diretório da Switch de**visão geral .

3. No menu do diretório, em **Manage,** escolha **registos app**  >  **Novo registo de candidatura.**

   > [!TIP]
   > Por padrão, a lista de registos de aplicações mostra todos os registos de aplicações no seu diretório. Para ver apenas as suas inscrições de aplicações, junto à caixa de pesquisa, selecione **As minhas aplicações.** 

   ![Criar registo de aplicação novo](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. Dê um nome à sua identidade de **aplicação,** deixe o tipo de aplicação definido para **aplicação Web /API,** forneça uma cadeia única formatada como um domínio para **URL de inscrição,** e escolha **Criar**.

   ![Fornecer nome e URL de inscrição para identidade de aplicação](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   A identidade da aplicação que criou para a sua aplicação lógica aparece agora na lista de registos de aplicações.

   ![Identidade de aplicação para a sua aplicação lógica](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. Na lista de registos de aplicações, selecione a sua nova identidade de aplicação. Copie e guarde o **ID da aplicação** para usar como "ID do cliente" para a sua aplicação lógica na Parte 3.

   ![Copiar e guardar iD de aplicação para aplicação lógica](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. Se as definições de identidade da sua aplicação não estiverem visíveis, escolha **Definições** ou **todas as definições**.

7. No **Acesso API,** escolha **Chaves.** Em **Descrição,** forneça um nome para a sua chave. Em **Expira,** selecione uma duração para a sua chave.

   A chave que está a criar funciona como o "segredo" ou senha da identidade da aplicação para a sua aplicação lógica.

   ![Criar chave para a identidade de aplicativo lógica](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. Na barra de ferramentas, escolha **Guardar**. Em **Valor,** a sua chave aparece agora. 
**Certifique-se de copiar e guardar a chave** para utilização posterior porque a chave está escondida quando sai da página **Chaves.**

   Ao configurar a sua aplicação lógica na Parte 3, especifica esta chave como "segredo" ou palavra-passe.

   ![Copiar e guardar a chave para mais tarde](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**Crie a identidade da aplicação para a sua aplicação lógica no PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode executar esta tarefa através do Azure Resource Manager com o PowerShell. Em PowerShell, executar estes comandos:

1. `Add-AzAccount`

1. `$SecurePassword = Read-Host -AsSecureString`

1. Introduza uma palavra-passe e prima Enter.

1. `New-AzADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password $SecurePassword`

1. Certifique-se de copiar o **ID** do Inquilino (GUID para o seu inquilino AZure AD), o **ID de aplicação,** e a senha que usou.

Para mais informações, saiba como [criar um principal de serviço com o PowerShell para aceder aos recursos.](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>Parte 2: Criar uma identidade de aplicação AD Azure para a sua aplicação web ou app API

Se a sua aplicação web ou app API já estiver implementada, pode ligar a autenticação e criar a identidade da aplicação no portal Azure. Caso contrário, pode [ligar a autenticação quando implementar com um modelo de Gestor de Recursos Azure](#authen-deploy). 

**Crie a identidade da aplicação e ligue a autenticação no portal Azure para aplicações implementadas**

1. No [portal Azure,](https://portal.azure.com "https://portal.azure.com")encontre e selecione a sua aplicação web ou app API. 

2. Em **Definições,** escolha **autenticação/autorização.** No âmbito **da Autenticação do Serviço de Aplicações,** ligue a **autenticação.** Em **Fornecedores de Autenticação,** escolha **diretório ativo Azure**.

   ![Ligue a autenticação](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. Agora crie uma identidade de aplicação para a sua aplicação web ou app API como mostrado aqui. Na página **Azure Ative Directory Settings,** defina o **modo de Gestão** para **Expressar**. Escolha **criar nova aplicação de anúncios.** Dê um nome à sua identidade de candidatura e escolha **OK**. 

   ![Crie identidade de aplicação para a sua aplicação web ou app API](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. Na página **Autenticação/Autorização**, escolha **Guardar**.

Agora você deve encontrar o ID do cliente e iD do inquilino para a identidade da aplicação que está associada com a sua app web ou app API. Usas estas identificações na Parte 3. Por isso, continue com estes passos para o portal Azure.

**Encontre iD de cliente de identidade de aplicação e identificação de inquilino para a sua aplicação web ou app API no portal Azure**

1. Em **Fornecedores de Autenticação,** escolha **diretório ativo Azure**. 

   ![Escolher o "Azure Active Directory"](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. Na página **Azure Ative Directory Settings,** defina o **modo de Gestão** para **Avançado**.

3. Copie o **ID**do Cliente e guarde o GUID para utilização na Parte 3.

   > [!TIP] 
   > Se **o ID** do Cliente e **o Url emitente** não aparecerem, tente refrescar o portal Azure e repita o Passo 1.

4. Em **Url Emitente,** copie e guarde apenas o GUID para a Parte 3. Também pode utilizar este GUID na sua aplicação web ou no modelo de implementação da app API, se necessário.

   Este GUID é o GUID do seu inquilino específico ("ID do inquilino") e deve aparecer neste URL: `https://sts.windows.net/{GUID}`

5. Sem guardar as alterações, feche a página **Azure Ative Directory Settings.**

<a name="authen-deploy"></a>

**Ligue a autenticação quando implementar com um modelo de Gestor de Recursos Azure**

Ainda precisa de criar uma identidade de aplicação AD Azure para a sua aplicação web ou app API que difere da identidade da aplicação para a sua aplicação lógica. Para criar a identidade da aplicação, siga os passos anteriores na Parte 2 para o portal Azure. 

Também pode seguir os passos na Parte 1, mas certifique-se de que utiliza a sua aplicação web ou a aplicação API real `https://{URL}` para **URL de sign-on** e **App ID URI.** A partir destes passos, você tem que guardar tanto o ID do cliente como o ID do inquilino para uso no modelo de implementação da sua aplicação e também para a Parte 3.

> [!NOTE]
> Quando criar a identidade da aplicação AD Azure para a sua aplicação web ou app API, deve utilizar o portal Azure, não o PowerShell. O comando PowerShell não configura as permissões necessárias para inscrever os utilizadores num website.

Depois de obter o ID do cliente e o ID do inquilino, inclua estes IDs como subresource da sua aplicação web ou app API no seu modelo de implementação:

``` json
"resources": [ 
   {
      "apiVersion": "2015-08-01",
      "name": "web",
      "type": "config",
      "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
      "properties": {
         "siteAuthEnabled": true,
         "siteAuthSettings": {
            "clientId": "<client-ID>",
            "issuer": "https://sts.windows.net/<tenant-ID>/"
         }
      }
   } 
]
```

Para implementar automaticamente uma aplicação web em branco e uma aplicação lógica juntamente com a autenticação do Azure Ative Directory, [consulte o modelo completo aqui,](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json)ou clique em Implementar para **Azure** aqui:

[![Implementar no Azure](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Parte 3: Povoar a secção de Autorização na sua aplicação lógica

O modelo anterior já tem esta secção de autorização configurada, mas se estiver a ser diretamente a autora da aplicação lógica, deve incluir a secção de autorização completa.

Abra a definição da sua aplicação lógica na vista de código, vá à definição de ação **HTTP,** encontre a secção **de Autorização** e inclua estas propriedades:

```json
{
   "tenant": "<tenant-ID>",
   "audience": "<client-ID-from-Part-2-web-app-or-API app>", 
   "clientId": "<client-ID-from-Part-1-logic-app>",
   "secret": "<key-from-Part-1-logic-app>", 
   "type": "ActiveDirectoryOAuth"
}
```

| Propriedade | Necessário | Descrição | 
| -------- | -------- | ----------- | 
| inquilino | Sim | O GUID para o inquilino AD AZure | 
| público | Sim | O GUID para o recurso-alvo a que pretende aceder, que é o ID do cliente a partir da identidade da aplicação para a sua aplicação web ou app API | 
| clientId | Sim | O GUID para o cliente que solicita acesso, que é o ID do cliente a partir da identidade da aplicação para a sua aplicação lógica | 
| segredo | Sim | A chave ou senha da identidade da aplicação para o cliente que está solicitando o token de acesso | 
| tipo | Sim | O tipo de autenticação. Para a autenticação ActiveDirectoryOAuth, o valor é `ActiveDirectoryOAuth` . | 
|||| 

Por exemplo:

``` json
{
   "actions": {
      "HTTP": {
         "inputs": {
            "method": "POST",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         }
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>Chamadas de API segura através do código

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Autenticação de certificados

Para validar os pedidos de entrada da sua aplicação lógica para a sua aplicação web ou app API, pode utilizar certificados de cliente. Para configurar o seu código, [aprenda a configurar a autenticação mútua TLS](../app-service/app-service-web-configure-tls-mutual-auth.md).

Na secção **de Autorização,** inclua estas propriedades:

```json
{
   "type": "ClientCertificate",
   "password": "<password>",
   "pfx": "<long-pfx-key>"
} 
```

| Propriedade | Necessário | Descrição |
| -------- | -------- | ----------- |
| `type` | Sim | O tipo de autenticação. Para os certificados de cliente TLS/SSL, o valor deve ser `ClientCertificate` . |
| `password` | Não | A senha de acesso ao certificado de cliente (ficheiro PFX) |
| `pfx` | Sim | O conteúdo codificado de base64 do certificado de cliente (ficheiro PFX) |
||||

<a name="basic"></a>

#### <a name="basic-authentication"></a>Autenticação básica

Para validar pedidos de entrada da sua aplicação lógica para a sua aplicação web ou app API, pode utilizar a autenticação básica, como um nome de utilizador e senha. A autenticação básica é um padrão comum, e pode usar esta autenticação em qualquer idioma utilizado para construir a sua aplicação web ou app API.

Na secção **de Autorização,** inclua estas propriedades:

```json
{
   "type": "Basic",
   "username": "<username>",
   "password": "<password>"
}
```

| Propriedade | Necessário | Descrição | 
| -------- | -------- | ----------- | 
| tipo | Sim | O tipo de autenticação que pretende utilizar. Para a autenticação básica, o valor deve `Basic` ser. | 
| nome de utilizador | Sim | O nome de utilizador que pretende utilizar para autenticação | 
| palavra-passe | Sim | A palavra-passe que pretende usar para autenticação | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Autenticação do Diretório Ativo Azure através de código

Por predefinição, a autenticação AZURE AD que liga no portal Azure não fornece uma autorização de grãos finos. Por exemplo, esta autenticação bloqueia a sua API apenas a um inquilino específico, e não a um utilizador ou app específico. 

Para restringir o acesso da API à sua aplicação lógica através de código, extraia o cabeçalho que tem o token web JSON (JWT). Verifique a identidade do chamador e rejeite pedidos que não correspondam.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/overview-authentication-authorization.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>Passos seguintes

* [Implementar e chamar APIs personalizados a partir de fluxos de trabalho de aplicações lógicas](../logic-apps/logic-apps-custom-api-host-deploy-call.md)
