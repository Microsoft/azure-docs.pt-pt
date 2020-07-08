---
title: Registe uma aplicação de serviço em Azure AD - Azure API para FHIR
description: Saiba como registar uma aplicação de cliente de serviço no Azure Ative Directory que pode ser usada para autenticar e obter fichas.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 83514ea13ca51e6980a1b4b60d4555974db5a240
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871849"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Registar um pedido de cliente de serviço no Azure Ative Directory

Neste artigo, você vai aprender a registar uma aplicação de cliente de serviço no Azure Ative Directory. Os registos de pedidos de cliente são representações do Azure Ative Directory de aplicações que podem ser usadas para autenticar e obter fichas. Um cliente de serviço destina-se a ser utilizado por uma aplicação para obter um token de acesso sem autenticação interativa de um utilizador. Terá determinadas permissões de aplicação e utilizará um segredo de aplicação (palavra-passe) ao obter fichas de acesso.

Siga os passos abaixo para criar um novo cliente de serviço.

## <a name="app-registrations-in-azure-portal"></a>Inscrições de aplicativos no portal Azure

1. No [portal do Azure](https://portal.azure.com), no painel de navegação do lado esquerdo, clique em **Azure Active Directory**.

2. No **Azure Ative Directory** blade clique nos **registos da App:**

    ![Portal Azure. Novo Registo de Aplicações.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Clique em **Novo registo**.

## <a name="service-client-application-details"></a>Detalhes da aplicação do cliente de serviço

* O cliente de serviço precisa de um nome de exibição e também pode fornecer um URL de resposta, mas normalmente não será utilizado.

    ![Portal Azure. Novo Registo de Aplicativos do Cliente de Serviço.](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-NAME.png)

## <a name="api-permissions"></a>Permissões de API

Terá de conceder as funções de candidatura do cliente de serviço. 

1. Abra as **permissões da API** e selecione o registo [de pedidos de recursos da FHIR API.](register-resource-azure-ad-client-app.md) Se estiver a utilizar a API Azure para FHIR, adicionará uma permissão às APIs de Cuidados de Saúde Azure, procurando APIs de Azure Healthcare ao abrigo **de APIs que a minha organização utiliza.**

    ![Portal Azure. Permissões API do Cliente de Serviço](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-API-PERMISSIONS.png)

2. Selecione as funções de aplicação que você tem a partir das que são definidas na aplicação de recursos:

    ![Portal Azure. Permissões de Aplicação do Cliente de Serviço](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-APPLICATION-PERMISSIONS.png)

3. Conceder consentimento ao pedido. Se não tiver as permissões necessárias, consulte o administrador do Azure Ative Directory:

    ![Portal Azure. Consentimento de Administração do Cliente de Serviço](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-ADMIN-CONSENT.png)

## <a name="application-secret"></a>Segredo da aplicação

O cliente de serviço precisa de um segredo (palavra-passe), que utilizará na obtenção de fichas.

1. Clique **em segredos de &amp; certificados**

2. Clique em **Novo segredo do cliente**

    ![Portal Azure. Segredo do Cliente de Serviço](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. Providenciar uma duração do segredo.

4. Uma vez gerado, só será exibido uma vez no portal. Tome nota e guarde-o em segurança.

## <a name="next-steps"></a>Próximos passos

Neste artigo, aprendeu a registar uma aplicação de cliente de serviço no Azure Ative Directory. Em seguida, implemente uma FHIR API em Azure.
 
>[!div class="nextstepaction"]
>[Implementar o servidor FHIR de código aberto](fhir-oss-powershell-quickstart.md)