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
ms.openlocfilehash: 8d70a7b44893ba9c9a0cc2d1d01c65e8e1584e0f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024487"
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

Se estiver a utilizar o FHIR Server de código aberto para o Azure, siga os passos do [repo GitHub](https://github.com/microsoft/fhir-server/blob/master/docs/Register-Resource-Application.md) para registar uma aplicação de recursos. 

## <a name="next-steps"></a>Próximos passos

Neste artigo, aprendeu a registar uma aplicação de recursos no Azure Ative Directory. Em seguida, registe a sua aplicação confidencial de cliente.
 
>[!div class="nextstepaction"]
>[Registar Aplicação confidencial do cliente](register-confidential-azure-ad-client-app.md)