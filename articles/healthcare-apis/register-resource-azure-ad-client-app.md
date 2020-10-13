---
title: Registe uma aplicação de recursos em Azure AD - Azure API para FHIR
description: Registe uma aplicação de recurso (ou API) no Azure Ative Directory, para que as aplicações do cliente possam solicitar o acesso ao recurso ao autenticar.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: e92fab392dc73d8de0b7b2547e38b3f345562930
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975863"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Registar uma aplicação de recursos no Azure Ative Directory

Neste artigo, você vai aprender a registar uma aplicação de recurso (ou API) no Azure Ative Directory. Uma aplicação de recurso é uma representação do Azure Ative Directory do próprio servidor FHIR API e as aplicações do cliente podem solicitar o acesso ao recurso quando autenticam. A aplicação de recursos também é conhecida como *o público* em OAuth parlance.

## <a name="azure-api-for-fhir"></a>API do Azure para FHIR

Se estiver a utilizar a API Azure para FHIR, uma aplicação de recurso é criada automaticamente quando implementa o serviço. Enquanto estiver a utilizar a AZure API para FHIR no mesmo inquilino do Azure Ative Directory que está a implementar a sua aplicação, pode ignorar este como orientar e, em vez disso, implementar a sua API Azure para a FHIR começar.

Se estiver a utilizar um inquilino diferente do Azure Ative Directory (não associado à sua subscrição), pode importar a API AZure para aplicação de recursos FHIR para o seu inquilino com a PowerShell:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

ou pode utilizar o Azure CLI:

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>Servidor FHIR para Azure

Se estiver a utilizar o FHIR Server de código aberto para o Azure, siga os passos abaixo para registar uma aplicação de recurso.

### <a name="app-registrations-in-azure-portal"></a>Inscrições de aplicativos no portal Azure

1. No [portal do Azure](https://portal.azure.com), no painel de navegação do lado esquerdo, clique em **Azure Active Directory**.

2. No **Azure Ative Directory** blade clique nos **registos da App:**

    ![Portal Azure. Novo Registo de Aplicações.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Clique no **Novo Registo.**

### <a name="add-a-new-application-registration"></a>Adicionar um novo registo de candidatura

Preencha os detalhes da nova aplicação. Não existem requisitos específicos para o nome do visor, mas defini-lo no URI do servidor FHIR facilita a sua posição:

![Novo registo de aplicação](media/how-to-aad/portal-aad-register-new-app-registration-NAME.png)

### <a name="set-identifier-uri-and-define-scopes"></a>Definir identificador URI e definir âmbitos

Uma aplicação de recurso tem um identificador URI (Application ID URI), que os clientes podem usar ao solicitar o acesso ao recurso. Este valor irá povoar a `aud` reivindicação do token de acesso. Recomenda-se que este URI seja o URI do seu servidor FHIR. Para smart em aplicações FHIR, presume-se que o *público* é o URI do servidor FHIR.

1. Clique **em Expor uma API**

2. Clique **em Conjunto** ao lado do *ID URI da aplicação.*

3. Introduza o identificador URI e clique em **Guardar**. Um bom identificador URI seria o URI do seu servidor FHIR.

4. Clique **em Adicionar um âmbito** e adicionar quaisquer âmbitos que gostaria de definir para a sua API. É-lhe exigido que adicione pelo menos um âmbito para conceder permissões à sua aplicação de recursos no futuro. Se não tiver quaisquer âmbitos específicos que queira adicionar, pode adicionar user_impersonation como âmbito.

![Público e âmbito](media/how-to-aad/portal-aad-register-new-app-registration-AUD-SCOPE.png)

### <a name="define-application-roles"></a>Definir funções de aplicação

A Azure API para fHIR e o SERVIDOR OSS FHIR para Azure utilizam [funções de aplicação do Azure Ative Directory](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) para controlo de acesso baseado em funções. Para definir quais as funções que devem estar disponíveis para o seu FHIR Server API, abra o [manifesto](https://docs.microsoft.com/azure/active-directory/active-directory-application-manifest/)da aplicação de recursos:

1. Clique **em Manifesto:**

    ![Funções de Candidatura](media/how-to-aad/portal-aad-register-new-app-registration-APP-ROLES.png)

2. Na `appRoles` propriedade, adicione as funções que gostaria que os utilizadores ou aplicações tivessem:

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User",
          "Application"
        ],
        "description": "FHIR Server Administrators",
        "displayName": "admin",
        "id": "1b4f816e-5eaf-48b9-8613-7923830595ad",
        "isEnabled": true,
        "value": "admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "description": "Users who can read",
        "displayName": "reader",
        "id": "c20e145e-5459-4a6c-a074-b942bbd4cfe1",
        "isEnabled": true,
        "value": "reader"
      }
    ],
    ```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a registar uma aplicação de recursos no Azure Ative Directory. Em seguida, registe a sua aplicação confidencial de cliente.
 
>[!div class="nextstepaction"]
>[Registar Aplicação confidencial do cliente](register-confidential-azure-ad-client-app.md)