---
title: Adicionar autenticação para proteger chamadas para APIs personalizadas
description: Como configurar a autenticação para proteger chamadas para APIs personalizadas de aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/22/2017
ms.openlocfilehash: 2f8b1cc002fe3f340ff6d5329329507316577885
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666901"
---
# <a name="secure-calls-to-custom-apis-from-azure-logic-apps"></a>Proteger chamadas para APIs personalizadas de aplicativos lógicos do Azure

Para proteger as chamadas para suas APIs, você pode configurar a autenticação do Azure Active Directory (AD do Azure) por meio do portal do Azure para que não precise atualizar seu código. Pode, também, pedir e impor a autenticação através do código da API.

## <a name="authentication-options-for-your-api"></a>Opções de autenticação para sua API

Você pode proteger chamadas para sua API personalizada das seguintes maneiras:

* [Nenhuma alteração de código](#no-code): Proteja sua API com o [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) por meio do portal do Azure, para que você não precise atualizar seu código ou reimplantar sua API.

  > [!NOTE]
  > Por padrão, a autenticação do Azure AD que você ativa no portal do Azure não fornece autorização refinada. Por exemplo, essa autenticação bloqueia sua API para apenas um locatário específico, não para um usuário ou aplicativo específico. 

* [Atualize o código da API](#update-code): Proteja sua API impondo a autenticação de [certificado](#certificate), a [autenticação básica](#basic)ou a [autenticação do Azure ad](#azure-ad-code) por meio de código.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Autenticar chamadas para sua API sem alterar o código

Aqui estão as etapas gerais para este método:

1. Crie duas identidades de aplicativo do Azure Active Directory (AD do Azure): uma para seu aplicativo lógico e uma para seu aplicativo Web (ou aplicativo de API).

2. Para autenticar chamadas para sua API, use as credenciais (ID do cliente e segredo) para a entidade de serviço associada à identidade do aplicativo do Azure AD para seu aplicativo lógico.

3. Inclua as IDs de aplicativo em sua definição de aplicativo lógico.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>Parte 1: criar uma identidade de aplicativo do Azure AD para seu aplicativo lógico

Seu aplicativo lógico usa essa identidade de aplicativo do Azure AD para se autenticar no Azure AD. Você só precisa configurar essa identidade uma vez para seu diretório. Por exemplo, você pode optar por usar a mesma identidade para todos os seus aplicativos lógicos, embora possa criar identidades exclusivas para cada aplicativo lógico. Você pode configurar essas identidades no portal do Azure ou usar o [PowerShell](#powershell).

**Crie a identidade do aplicativo para seu aplicativo lógico no portal do Azure**

1. Na [portal do Azure](https://portal.azure.com "https://portal.azure.com"), escolha **Azure Active Directory**. 

2. Confirme que você está no mesmo diretório que seu aplicativo Web ou aplicativo de API.

   > [!TIP]
   > Para alternar os diretórios, escolha seu perfil e selecione outro diretório. Ou escolha **visão geral** > **diretório de comutador**.

3. No menu diretório, em **gerenciar**, escolha **registros de aplicativo** > **novo registro de aplicativo**.

   > [!TIP]
   > Por padrão, a lista de registros de aplicativo mostra todos os registros de aplicativo em seu diretório. Para exibir apenas os registros do aplicativo, ao lado da caixa de pesquisa, selecione **meus aplicativos**. 

   ![Criar novo registro de aplicativo](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. Dê um nome à identidade do aplicativo, deixe o **tipo de aplicativo** definido como **aplicativo Web/API**, forneça uma cadeia de caracteres formatada como um domínio para a **URL de logon**e escolha **criar**.

   ![Forneça o nome e a URL de logon para a identidade do aplicativo](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   A identidade do aplicativo que você criou para seu aplicativo lógico agora aparece na lista de registros do aplicativo.

   ![Identidade do aplicativo para seu aplicativo lógico](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. Na lista registros do aplicativo, selecione a nova identidade do aplicativo. Copie e salve a **ID do aplicativo** para usar como "ID do cliente" para seu aplicativo lógico na parte 3.

   ![Copiar e salvar a ID do aplicativo para o aplicativo lógico](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. Se as configurações de identidade do aplicativo não estiverem visíveis, escolha **configurações** ou **todas as configurações**.

7. Em **acesso à API**, escolha **chaves**. Em **Descrição**, forneça um nome para a chave. Em **expirar**, selecione uma duração para sua chave.

   A chave que você está criando atua como "segredo" ou senha da identidade do aplicativo para seu aplicativo lógico.

   ![Criar chave para a identidade do aplicativo lógico](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. Na barra de ferramentas, escolha **salvar**. Em **valor**, sua chave agora é exibida. 
**Certifique-se de copiar e salvar sua chave** para uso posterior porque a chave é ocultada quando você sai da página **chaves** .

   Ao configurar seu aplicativo lógico na parte 3, você especifica essa chave como "segredo" ou senha.

   ![Copiar e salvar a chave para mais tarde](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**Criar a identidade do aplicativo para seu aplicativo lógico no PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você pode executar essa tarefa por meio do Azure Resource Manager com o PowerShell. No PowerShell, execute estes comandos:

1. `Add-AzAccount`

1. `$SecurePassword = Read-Host -AsSecureString`

1. Insira uma senha e pressione Enter.

1. `New-AzADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password $SecurePassword`

1. Certifique-se de copiar a **ID do locatário** (GUID para seu locatário do Azure AD), a ID do **aplicativo**e a senha que você usou.

Para obter mais informações, saiba como [criar uma entidade de serviço com o PowerShell para acessar recursos](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>Parte 2: criar uma identidade de aplicativo do Azure AD para seu aplicativo Web ou aplicativo de API

Se seu aplicativo Web ou aplicativo de API já estiver implantado, você poderá ativar a autenticação e criar a identidade do aplicativo no portal do Azure. Caso contrário, você pode [ativar a autenticação ao implantar com um modelo de Azure Resource Manager](#authen-deploy). 

**Criar a identidade do aplicativo e ativar a autenticação no portal do Azure para aplicativos implantados**

1. Na [portal do Azure](https://portal.azure.com "https://portal.azure.com"), localize e selecione seu aplicativo Web ou aplicativo de API. 

2. Em **configurações**, escolha **autenticação/autorização**. Em **autenticação do serviço de aplicativo**,ative a autenticação. Em **provedores de autenticação**, escolha **Azure Active Directory**.

   ![Ativar a autenticação](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. Agora, crie uma identidade de aplicativo para seu aplicativo Web ou aplicativo de API, conforme mostrado aqui. Na página **configurações de Azure Active Directory** , defina **modo de gerenciamento** como **Express**. Escolha **criar novo aplicativo do AD**. Dê um nome à identidade do aplicativo e escolha **OK**. 

   ![Criar identidade de aplicativo para seu aplicativo Web ou aplicativo de API](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. Na página **Autenticação/Autorização**, escolha **Guardar**.

Agora você deve encontrar a ID do cliente e a ID do locatário para a identidade do aplicativo que está associada ao aplicativo Web ou ao aplicativo de API. Você usa essas IDs na parte 3. Portanto, continue com estas etapas para o portal do Azure.

**Localize a ID do cliente e a ID do locatário da identidade do aplicativo para seu aplicativo Web ou aplicativo de API no portal do Azure**

1. Em **provedores de autenticação**, escolha **Azure Active Directory**. 

   ![Escolher o "Azure Active Directory"](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. Na página **configurações de Azure Active Directory** , defina **modo de gerenciamento** como **avançado**.

3. Copie a **ID do cliente**e salve esse GUID para uso na parte 3.

   > [!TIP] 
   > Se a **ID do cliente** e a URL do **emissor** não aparecerem, tente atualizar o portal do Azure e repita a etapa 1.

4. Em **URL do emissor**, copie e salve apenas o GUID da parte 3. Você também pode usar esse GUID no modelo de implantação do aplicativo Web ou do aplicativo de API, se necessário.

   Esse GUID é o GUID do locatário específico ("ID do locatário") e deve aparecer nesta URL: `https://sts.windows.net/{GUID}`

5. Sem salvar as alterações, feche a página **configurações de Azure Active Directory** .

<a name="authen-deploy"></a>

**Ativar a autenticação ao implantar com um modelo de Azure Resource Manager**

Você ainda precisa criar uma identidade de aplicativo do Azure AD para seu aplicativo Web ou aplicativo de API que difere da identidade do aplicativo para seu aplicativo lógico. Para criar a identidade do aplicativo, siga as etapas anteriores na parte 2 da portal do Azure. 

Você também pode seguir as etapas na parte 1, mas certifique-se de usar seu aplicativo Web ou o `https://{URL}` real do aplicativo de API para **URL de logon** e URI de **ID do aplicativo**. Nessas etapas, você precisa salvar a ID do cliente e a ID do locatário para uso no modelo de implantação do aplicativo e também para a parte 3.

> [!NOTE]
> Ao criar a identidade do aplicativo do Azure AD para seu aplicativo Web ou aplicativo de API, você deve usar o portal do Azure, não o PowerShell. O commandlet do PowerShell não configura as permissões necessárias para conectar usuários em um site.

Depois de obter a ID do cliente e a ID do locatário, inclua essas IDs como um subrecurso de seu aplicativo Web ou aplicativo de API em seu modelo de implantação:

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

Para implantar automaticamente um aplicativo Web em branco e um aplicativo lógico junto com Azure Active Directory autenticação, [exiba o modelo completo aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json)ou clique em **implantar no Azure** aqui:

[![Implementar no Azure](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Parte 3: popular a seção de autorização em seu aplicativo lógico

O modelo anterior já tem essa seção de autorização configurada, mas se você estiver criando diretamente o aplicativo lógico, deverá incluir a seção de autorização completa.

Abra a definição do aplicativo lógico na exibição de código, vá para a definição da ação **http** , localize a seção **autorização** e inclua estas propriedades:

```json
{
   "tenant": "<tenant-ID>",
   "audience": "<client-ID-from-Part-2-web-app-or-API app>", 
   "clientId": "<client-ID-from-Part-1-logic-app>",
   "secret": "<key-from-Part-1-logic-app>", 
   "type": "ActiveDirectoryOAuth"
}
```

| Propriedade | Obrigatório | Descrição | 
| -------- | -------- | ----------- | 
| tenant | Sim | O GUID para o locatário do Azure AD | 
| audience | Sim | O GUID para o recurso de destino que você deseja acessar, que é a ID do cliente da identidade do aplicativo para seu aplicativo Web ou aplicativo de API | 
| clientId | Sim | O GUID para o cliente que solicita acesso, que é a ID do cliente da identidade do aplicativo para seu aplicativo lógico | 
| segredo | Sim | A chave ou a senha da identidade do aplicativo para o cliente que está solicitando o token de acesso | 
| tipo | Sim | O tipo de autenticação. Para a autenticação ActiveDirectoryOAuth, o valor é `ActiveDirectoryOAuth`. | 
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

### <a name="secure-api-calls-through-code"></a>Proteger chamadas de API por meio de código

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Autenticação de certificados

Para validar as solicitações de entrada de seu aplicativo lógico para seu aplicativo Web ou aplicativo de API, você pode usar certificados de cliente. Para configurar seu código, saiba [como configurar a autenticação mútua TLS](../app-service/app-service-web-configure-tls-mutual-auth.md).

Na seção **autorização** , inclua estas propriedades:

```json
{
   "type": "ClientCertificate",
   "password": "<password>",
   "pfx": "<long-pfx-key>"
} 
```

| Propriedade | Obrigatório | Descrição |
| -------- | -------- | ----------- |
| `type` | Sim | O tipo de autenticação. Para certificados de cliente SSL, o valor deve ser `ClientCertificate`. |
| `password` | Não | A senha para acessar o certificado do cliente (arquivo PFX) |
| `pfx` | Sim | O conteúdo codificado na base64 do certificado do cliente (arquivo PFX) |
||||

<a name="basic"></a>

#### <a name="basic-authentication"></a>Autenticação básica

Para validar as solicitações de entrada de seu aplicativo lógico para seu aplicativo Web ou aplicativo de API, você pode usar a autenticação básica, como um nome de usuário e senha. A autenticação básica é um padrão comum, e você pode usar essa autenticação em qualquer linguagem usada para criar seu aplicativo Web ou aplicativo de API.

Na seção **autorização** , inclua estas propriedades:

```json
{
   "type": "Basic",
   "username": "<username>",
   "password": "<password>"
}
```

| Propriedade | Obrigatório | Descrição | 
| -------- | -------- | ----------- | 
| tipo | Sim | O tipo de autenticação que você deseja usar. Para a autenticação básica, o valor deve ser `Basic`. | 
| o nome de utilizador | Sim | O nome de usuário que você deseja usar para autenticação | 
| palavra-passe | Sim | A senha que você deseja usar para autenticação | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Autenticação Azure Active Directory por meio de código

Por padrão, a autenticação do Azure AD que você ativa no portal do Azure não fornece autorização refinada. Por exemplo, essa autenticação bloqueia sua API para apenas um locatário específico, não para um usuário ou aplicativo específico. 

Para restringir o acesso de API ao seu aplicativo lógico por meio de código, extraia o cabeçalho que tem o JWT (token Web JSON). Verifique a identidade do chamador e rejeite as solicitações que não correspondem.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/overview-authentication-authorization.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>Passos seguintes

* [Implantar e chamar APIs personalizadas de fluxos de trabalho do aplicativo lógico](../logic-apps/logic-apps-custom-api-host-deploy-call.md)
