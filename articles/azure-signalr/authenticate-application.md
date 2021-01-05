---
title: Autenticar uma aplicação para aceder ao Serviço Azure SignalR
description: Este artigo fornece informações sobre a autenticação de uma aplicação com o Azure Ative Directory para aceder ao Serviço Azure SignalR
author: terencefan
ms.author: tefa
ms.service: signalr
ms.topic: conceptual
ms.date: 08/03/2020
ms.openlocfilehash: 97386b18360e22b457dbcdda53c4f81e7d4ed272
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797591"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-azure-signalr-service"></a>Autenticar uma aplicação com diretório ativo Azure para aceder ao Serviço Azure SignalR
O Microsoft Azure fornece uma gestão integrada do controlo de acessos para recursos e aplicações com base no Azure Ative Directory (Azure AD). Uma vantagem fundamental da utilização do Azure AD com o Serviço Azure SignalR é que já não precisa de armazenar as suas credenciais no código. Em vez disso, pode solicitar um token de acesso OAuth 2.0 a partir da plataforma Microsoft Identity. O nome do recurso para solicitar um token é `https://signalr.azure.com/` . A Azure AD autentica o principal de segurança (uma aplicação, grupo de recursos ou principal de serviço) que executa a aplicação. Se a autenticação for bem sucedida, a Azure AD devolve um sinal de acesso à aplicação, podendo então utilizar o token de acesso para autorizar o pedido aos recursos do Serviço Azure SignalR.

Quando um papel é atribuído a um diretor de segurança da AZure, a Azure concede acesso a esses recursos para esse diretor de segurança. O acesso pode ser analisado ao nível de subscrição, ao grupo de recursos ou ao recurso Azure SignalR. Uma segurança Azure AD pode atribuir funções a um utilizador, a um grupo, a um diretor de serviço de aplicações ou a uma [identidade gerida para os recursos da Azure.](../active-directory/managed-identities-azure-resources/overview.md) 

> [!NOTE]
> Uma definição de função é uma coleção de permissões. O controlo de acesso baseado em funções (RBAC) controla a forma como estas permissões são aplicadas através da atribuição de funções. Uma atribuição de função é composta por três elementos: principal de segurança, definição de função e âmbito. Para obter mais informações, consulte [compreender as diferentes funções.](../role-based-access-control/overview.md)

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registe o seu pedido junto de um inquilino da AD Azure
O primeiro passo para a utilização da Azure AD para autorizar os recursos do Serviço Azure SignalR é registar a sua candidatura junto de um inquilino AZure AD do [portal Azure.](https://portal.azure.com/) Quando regista a sua candidatura, fornece informações sobre o pedido à AD. A Azure AD fornece então um ID do cliente (também chamado de ID de aplicação) que pode usar para associar a sua aplicação ao tempo de execução da Azure AD. Para saber mais sobre o ID do cliente, consulte [os objetos principais de aplicação e serviço no Azure Ative Directory.](../active-directory/develop/app-objects-and-service-principals.md) 

As seguintes imagens mostram passos para registar uma aplicação web:

![Registar uma aplicação](./media/authenticate/app-registrations-register.png)

> [!Note]
> Se registar a sua candidatura como uma aplicação nativa, pode especificar qualquer URI válido para o URI de redirecionamento. Para aplicações nativas, este valor não tem de ser um URL real. Para aplicações web, o URI de redirecionamento deve ser um URI válido, pois especifica o URL para o qual são fornecidos tokens.

Depois de ter registado a sua candidatura, verá o **ID de Aplicação (cliente)** em **Definições:**

![ID de aplicação do pedido registado](./media/authenticate/application-id.png)

Para obter mais informações sobre o registo de uma candidatura com a Azure AD, consulte [integrar aplicações com o Azure Ative Directory](../active-directory/develop/quickstart-register-app.md).


### <a name="create-a-client-secret"></a>Criar um segredo de cliente   
O pedido precisa de um segredo de cliente para provar a sua identidade ao pedir um token. Para adicionar o segredo do cliente, siga estes passos.

1. Navegue para o registo da sua aplicação no portal Azure.
1. Selecione a definição **de segredos & certificados.**
1. Sob **os segredos do Cliente,** selecione **Novo segredo de cliente** para criar um novo segredo.
1. Forneça uma descrição para o segredo e escolha o intervalo de validade desejado.
1. Copie imediatamente o valor do novo segredo para um local seguro. O valor de preenchimento é apresentado apenas uma vez.

![Criar um segredo de cliente](./media/authenticate/client-secret.png)

### <a name="upload-a-certificate"></a>Faça upload de um certificado

Também pode fazer upload de uma certificação em vez de criar um segredo de cliente.

![Faça upload de uma certificação](./media/authenticate/certification.png)

## <a name="add-rbac-roles-using-the-azure-portal"></a>Adicione funções RBAC usando o portal Azure  
Para saber mais sobre a gestão do acesso aos recursos do Azure utilizando o RBAC e o portal Azure, consulte [este artigo.](..//role-based-access-control/role-assignments-portal.md) 

Depois de ter determinado a possibilidade adequada para uma atribuição de funções, navegue para esse recurso no portal Azure. Mostrar as definições de controlo de acesso (IAM) para o recurso e seguir estas instruções para gerir as atribuições de funções:

1. No [portal Azure,](https://portal.azure.com/)navegue para o seu recurso SignalR.
1. Selecione **Access Control (IAM)** para exibir as definições de controlo de acesso para o Azure SignalR. 
1. Selecione o **separador funções** para ver a lista de atribuições de funções. Selecione o botão **Adicionar** na barra de ferramentas e, em seguida, selecione **Adicionar a atribuição de função**. 

    ![Adicione botão na barra de ferramentas](./media/authenticate/role-assignments-add-button.png)

1. Na página **de atribuição de funções Adicionar,** faça os seguintes passos:
    1. Selecione a **função Azure SignalR** que pretende atribuir. 
    1. Procure localizar o **principal de segurança** (utilizador, grupo, principal serviço) ao qual pretende atribuir a função.
    1. **Selecione Guardar** para guardar a atribuição de funções. 

        ![Atribuir papel a uma aplicação](./media/authenticate/assign-role-to-application.png)

    1. A identidade a quem atribuiu o papel aparece listada nessa função. Por exemplo, a imagem a seguir mostra essa aplicação `signalr-dev` e `signalr-service` está na função SignalR App Server. 
        
        ![Lista de atribuição de funções](./media/authenticate/role-assignment-list.png)

Pode seguir passos semelhantes para atribuir uma função a um grupo de recursos ou subscrição. Uma vez definido o papel e o seu âmbito, pode testar este comportamento com amostras [neste local do GitHub.](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)

## <a name="sample-codes-while-configuring-your-app-server"></a>Códigos de amostra enquanto configura o servidor de aplicações.

Adicione as seguintes opções `AddAzureSignalR` quando:

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>";
});
```

Ou simplesmente configurar o `ConnectionString` seu `appsettings.json` arquivo.

```json
{
"Azure": {
    "SignalR": {
      "Enabled": true,
      "ConnectionString": "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>"
    }
  },
}
```

Ao `certificate` utilizar, mude `clientSecret` o para `clientCert` o seguinte:

```C#
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientCert=<clientCertFilepath>;tenantId=<tenantId>";
```

## <a name="next-steps"></a>Passos seguintes
- Para saber mais sobre o RBAC, veja [o que é o controlo de acesso baseado em funções do Azure (Azure RBAC)](../role-based-access-control/overview.md)?
- Para aprender a atribuir e gerir atribuições de funções da Azure PowerShell, Azure CLI ou da REST API, consulte estes artigos:
    - [Gerir o controlo de acesso baseado em funções (RBAC) com a Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Gerir o controlo de acesso baseado em funções (RBAC) com o Azure CLI](../role-based-access-control/role-assignments-cli.md)
    - [Gerir o controlo de acesso baseado em funções (RBAC) com a API REST](../role-based-access-control/role-assignments-rest.md)
    - [Gerir o controlo de acesso baseado em funções (RBAC) com modelos de gestor de recursos Azure](../role-based-access-control/role-assignments-template.md)

Consulte os seguintes artigos relacionados:
- [Autenticar uma identidade gerida com o Azure Ative Directory para aceder ao Serviço Azure SignalR](authenticate-managed-identity.md)
- [Autorizar o acesso ao Serviço Azure SignalR utilizando o Diretório Ativo Azure](authorize-access-azure-active-directory.md)