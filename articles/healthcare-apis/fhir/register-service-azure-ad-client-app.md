---
title: Registe uma aplicação de serviço em Azure AD - Azure API para FHIR
description: Saiba como registar uma aplicação de cliente de serviço no Azure Ative Directory.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 6f7bf122b292ca144eac406957f19a13c7ba6662
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020066"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Registar um pedido de cliente de serviço no Azure Ative Directory

Neste artigo, você vai aprender a registar uma aplicação de cliente de serviço no Azure Ative Directory. Os registos de pedidos de cliente são representações do Azure Ative Directory de aplicações que podem ser usadas para autenticar e obter fichas. Um cliente de serviço destina-se a ser utilizado por uma aplicação para obter um token de acesso sem autenticação interativa de um utilizador. Terá determinadas permissões de aplicação e utilizará um segredo de aplicação (palavra-passe) ao obter fichas de acesso.

Siga estes passos para criar um novo cliente de serviço.

## <a name="app-registrations-in-azure-portal"></a>Inscrições de aplicativos no portal Azure

1. No [portal Azure,](https://portal.azure.com)navegue até ao **Diretório Ativo Azure**.

2. Selecione **Registos de aplicações**.

    ![Portal Azure. Novo Registo de Aplicações.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Selecione **Novo registo**.

4. Dê ao cliente de serviço um nome de exibição. Normalmente, as aplicações do cliente de serviço não utilizam um URL de resposta.

    :::image type="content" source="media/service-client-app/service-client-registration.png" alt-text="Portal Azure. Novo Registo de Aplicativos do Cliente de Serviço.":::

5. Selecione **Registar**.

## <a name="api-permissions"></a>Permissões de API

Agora que registou a sua aplicação, terá de selecionar quais as permissões da API que esta aplicação deve poder solicitar em nome dos utilizadores:

1. Selecione **permissões API**.
1. **Selecione Adicionar uma permissão**.

    Se estiver a utilizar a API Azure para FHIR, adicionará uma permissão às APIs de Cuidados de Saúde Azure, procurando APIs de **Azure Healthcare** ao abrigo **de APIs que a minha organização utiliza.** 

    Se estiver a fazer referência a uma aplicação de recursos diferente, selecione o [registo de aplicação de recursos da FHIR API](register-resource-azure-ad-client-app.md) que criou anteriormente ao abrigo **das Minhas APIs.**

    :::image type="content" source="media/service-client-app/service-client-org-api.png" alt-text="Cliente confidencial. Os meus APIs org" lightbox="media/service-client-app/service-client-org-api-expanded.png":::

1. Selecione âmbitos (permissões) que a aplicação confidencial deve poder pedir em nome de um utilizador:

    :::image type="content" source="media/service-client-app/service-client-add-permission.png" alt-text="Cliente de serviço. Permissões Delegadas":::

1. Conceder consentimento ao pedido. Se não tiver as permissões necessárias, consulte o administrador do Azure Ative Directory:

    :::image type="content" source="media/service-client-app/service-client-grant-permission.png" alt-text="Cliente de serviço. Consentimento de Subvenção":::

## <a name="application-secret"></a>Segredo da aplicação

O cliente de serviço precisa de um segredo (palavra-passe) para obter um token.

1. Selecione **Certificados & segredos**.
2. Selecione **Novo segredo do cliente**.

    ![Portal Azure. Segredo do Cliente de Serviço](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. Fornecer uma descrição e duração do segredo (1 ano, 2 anos ou nunca).

4. Uma vez gerado o segredo, só será exibido uma vez no portal. Tome nota e guarde-o em segurança.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a registar uma aplicação de cliente de serviço no Azure Ative Directory. Em seguida, teste o acesso ao seu servidor FHIR usando o Carteiro.
 
>[!div class="nextstepaction"]
>[Acesso Azure API para FHIR com Carteiro](access-fhir-postman-tutorial.md)
