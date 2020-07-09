---
title: Registe uma aplicação confidencial de clientes em Azure AD - Azure API para FHIR
description: Registe uma aplicação confidencial de cliente no Azure Ative Directory que autentica em nome de um utilizador e solicita acesso a aplicações de recursos.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 6642adf5f51be1f4f54c538624829fe9004d3907
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871877"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Registar um pedido de cliente confidencial no Azure Ative Directory

Neste tutorial, você vai aprender a registar uma aplicação confidencial de cliente no Azure Ative Directory. 

O registo de um pedido de cliente é uma representação do Azure Ative Directory de uma aplicação que pode ser usada para autenticar em nome de um utilizador e solicitar o acesso a aplicações de [recursos.](register-resource-azure-ad-client-app.md) Um pedido de cliente confidencial é uma aplicação que pode ser confiável para guardar um segredo e apresentar esse segredo ao solicitar acesso a fichas. Exemplos de aplicações confidenciais são aplicações do lado do servidor.

Para registar uma nova aplicação confidencial no portal, siga os passos abaixo.

## <a name="app-registrations-in-azure-portal"></a>Inscrições de aplicativos no portal Azure

1. No [portal do Azure](https://portal.azure.com), no painel de navegação do lado esquerdo, clique em **Azure Active Directory**.

2. No **Azure Ative Directory** blade clique nos **registos da App:**

    ![Portal Azure. Novo Registo de Aplicações.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Clique no **Novo Registo.**

## <a name="register-a-new-application"></a>Registar uma nova aplicação

1. Dê à aplicação um nome de exibição.

2. Fornecer uma URL de resposta. Estes detalhes podem ser alterados mais tarde, mas se souber qual é o URL de resposta da sua aplicação, insira-o agora.

    ![Novo Registo confidencial de aplicativos para clientes.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)

## <a name="api-permissions"></a>Permissões de API

Em seguida, adicione permissões API:

1. Abra as **permissões da API:**

    ![Cliente confidencial. Permissões da API](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

2. Clique **Em Adicionar uma permissão**

3. Selecione a API de recurso apropriado:

    Para a Azure API para FHIR (serviço gerido), clique **em APIs que a minha organização utiliza** e procure por "Azure Healthcare APIs". Para o servidor FHIR de código aberto para Azure, selecione o seu Registo de [Aplicação de Recursos API FHIR](register-resource-azure-ad-client-app.md):

    ![Cliente confidencial. As minhas APIs](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-MyApis.png)

4. Selecione âmbitos (permissões) que a aplicação confidencial deve poder pedir em nome de um utilizador:

    ![Cliente confidencial. Permissões Delegadas](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-DelegatedPermissions.png)

## <a name="application-secret"></a>Segredo da aplicação

1. Criar um segredo de aplicação (segredo de cliente):

    ![Cliente confidencial. Segredo de Aplicação](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. Forneça uma descrição e duração do segredo.

3. Uma vez gerado, será exibido no portal apenas uma vez. Tome nota e guarde-o de forma segura.

## <a name="next-steps"></a>Próximos passos

Neste artigo, aprendeu a registar um pedido de cliente confidencial no Azure Ative Directory. Está agora pronto para implantar a [API Azure para fHIR](fhir-paas-powershell-quickstart.md).

Uma vez implantado a API Azure para FHIR, pode rever as definições disponíveis adicionais.
 
>[!div class="nextstepaction"]
>[Implementar o Azure API for FHIR](fhir-paas-powershell-quickstart.md)