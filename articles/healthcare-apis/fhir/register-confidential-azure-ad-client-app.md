---
title: Registe uma aplicação confidencial de clientes em Azure AD - Azure API para FHIR
description: Registe uma aplicação confidencial de cliente no Azure Ative Directory que autentica em nome de um utilizador e solicita acesso a aplicações de recursos.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 8021fb3fa9f11ef895569f48a2ae21b3f7adcd36
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020136"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Registar um pedido de cliente confidencial no Azure Ative Directory

Neste tutorial, você vai aprender a registar uma aplicação confidencial de cliente no Azure Ative Directory. 

O registo de um pedido de cliente é uma representação do Azure Ative Directory de uma aplicação que pode ser usada para autenticar em nome de um utilizador e solicitar o acesso a aplicações de [recursos.](register-resource-azure-ad-client-app.md) Um pedido de cliente confidencial é uma aplicação que pode ser confiável para guardar um segredo e apresentar esse segredo ao solicitar acesso a fichas. Exemplos de aplicações confidenciais são aplicações do lado do servidor.

Para registar uma nova aplicação confidencial no portal, siga estes passos.

## <a name="register-a-new-application"></a>Registar uma nova aplicação

1. No [portal Azure,](https://portal.azure.com)navegue até ao **Diretório Ativo Azure**.

1. Selecione **Registos de aplicações**.

    ![Portal Azure. Novo Registo de Aplicações.](media/how-to-aad/portal-aad-new-app-registration.png)

1. Selecione **Novo registo**.

1. Dê à aplicação um nome de exibição.

1. Fornecer uma URL de resposta. Estes detalhes podem ser alterados mais tarde, mas se souber qual é o URL de resposta da sua aplicação, insira-o agora.

    ![Novo Registo confidencial de aplicativos para clientes.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)
1. Selecione **Registar**.

## <a name="api-permissions"></a>Permissões de API

Agora que registou a sua aplicação, terá de selecionar quais as permissões da API que esta aplicação deve poder solicitar em nome dos utilizadores:

1. Selecione **permissões API**.

    ![Cliente confidencial. Permissões de API](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

1. **Selecione Adicionar uma permissão**.

    Se estiver a utilizar a API Azure para FHIR, adicionará uma permissão às APIs de Cuidados de Saúde Azure, procurando APIs de **Azure Healthcare** ao abrigo **de APIs que a minha organização utiliza.** Só poderá encontrá-lo se já tiver [implantado a API Azure para fHIR.](fhir-paas-powershell-quickstart.md)

    Se estiver a fazer referência a uma aplicação de recursos diferente, selecione o [registo de aplicação de recursos da FHIR API](register-resource-azure-ad-client-app.md) que criou anteriormente ao abrigo **das Minhas APIs.**


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="Cliente confidencial. Os meus APIs org" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

3. Selecione âmbitos (permissões) que a aplicação confidencial deve poder pedir em nome de um utilizador:

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="Cliente confidencial. Permissões Delegadas":::

## <a name="application-secret"></a>Segredo da aplicação

1. Selecione **Certificados & segredos**.
1. Selecione **Novo segredo do cliente**. 

    ![Cliente confidencial. Segredo de Aplicação](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. Fornecer uma descrição e duração do segredo (1 ano, 2 anos ou nunca).

3. Uma vez gerado, será exibido no portal apenas uma vez. Tome nota e guarde-o de forma segura.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a registar um pedido de cliente confidencial no Azure Ative Directory. Em seguida, pode aceder ao seu servidor FHIR usando o Carteiro
 
>[!div class="nextstepaction"]
>[Acesso Azure API para FHIR com Carteiro](access-fhir-postman-tutorial.md)
