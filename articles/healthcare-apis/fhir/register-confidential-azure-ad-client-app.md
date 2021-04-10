---
title: Registe uma aplicação confidencial de clientes em Azure AD - Azure API para FHIR
description: Registe uma aplicação confidencial de cliente no Azure Ative Directory que autentica em nome de um utilizador e solicita acesso a aplicações de recursos.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: matjazl
ms.openlocfilehash: fbc683c98eb0124e8d879eada276593ca9bf7042
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934760"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Registar um pedido de cliente confidencial no Azure Ative Directory

Neste tutorial, você vai aprender a registar uma aplicação confidencial de cliente no Azure Ative Directory (Azure AD).  

O registo de um pedido de cliente é uma representação AD da AZure de uma aplicação que pode ser usada para autenticar em nome de um utilizador e solicitar o acesso a [aplicações de recursos.](register-resource-azure-ad-client-app.md) Um pedido de cliente confidencial é uma aplicação que pode ser confiável para guardar um segredo e apresentar esse segredo ao solicitar acesso a fichas. Exemplos de aplicações confidenciais são aplicações do lado do servidor. 

Para registar uma nova aplicação confidencial do cliente, consulte os passos abaixo. 

## <a name="register-a-new-application"></a>Registar uma nova aplicação

1. No [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory**.

1. Selecione **Registos de aplicações**. 

    ![Portal Azure. Novo Registo de Aplicações.](media/how-to-aad/portal-aad-new-app-registration.png)

1. Selecione **Novo registo**.

1. Dê à aplicação um nome de exibição virado para o utilizador.

1. Para **tipos de conta suportada,** selecione quem pode utilizar a aplicação ou aceder à API.

1. (Opcional) Fornecer um **URI de redirecionamento**. Estes detalhes podem ser alterados mais tarde, mas se souber qual é o URL de resposta da sua aplicação, insira-o agora.

    ![Novo Registo confidencial de aplicativos para clientes.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)

1. Selecione **Registar**.

## <a name="api-permissions"></a>Permissões de API

Agora que registou a sua aplicação, tem de selecionar quais as permissões da API que esta aplicação deve solicitar em nome dos utilizadores.

1. Selecione **permissões API**.

    ![Cliente confidencial. Permissões de API](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

1. **Selecione Adicionar uma permissão**.

    Se estiver a utilizar a API Azure para FHIR, irá adicionar uma permissão às APIs de Azure Healthcare, procurando a **API de Azure Healthcare** ao abrigo das APIs que **a minha organização utiliza.** O resultado da pesquisa da Azure Healthcare API só regressará se já tiver [implantado a API Azure para fHIR](fhir-paas-powershell-quickstart.md).

    Se estiver a referir uma aplicação de recursos diferente, selecione o registo [de aplicação de recursos da FHIR API](register-resource-azure-ad-client-app.md) que criou anteriormente nos **meus APIs**.


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="Cliente confidencial. Os meus APIs org" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

1. Selecione âmbitos (permissões) que a aplicação confidencial do cliente irá solicitar em nome de um utilizador. Selecione **user_impersonation** e, em seguida, **selecione Adicionar permissões**.

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="Cliente confidencial. Permissões Delegadas":::


## <a name="application-secret"></a>Segredo da aplicação

1. Selecione **Certificados & segredos** e, em seguida, selecione **Novo segredo de cliente**. 

    ![Cliente confidencial. Segredo de Aplicação](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

1. Insira uma **Descrição** para o segredo do cliente. Selecione os Expira (Em 1 ano, em 2 anos ou nunca) e, em seguida, clique em **Adicionar**.

   ![Adicione um segredo de cliente](media/how-to-aad/add-a-client-secret.png)

1. Após a criação da cadeia secreta do cliente, copie o seu **Valor** e **ID,** e guarde-os num local seguro à sua escolha.

   :::image type="content" source="media/how-to-aad/client-secret-string-password.png" alt-text="Corda secreta do cliente."::: 

> [!NOTE]
>A corda secreta do cliente só é visível uma vez no portal Azure. Quando navega para longe da página web de Certificados & segredos e, em seguida, voltar a ela, a cadeia Value torna-se mascarada. É importante fazer uma cópia da cadeia secreta do seu cliente imediatamente após a sua geração. Se não tem uma cópia de reserva do segredo do seu cliente, deve repetir os passos acima para regenerar.
 
## <a name="next-steps"></a>Passos seguintes

Neste artigo, foi guiado através dos passos de como registar uma aplicação confidencial de cliente no AZure AD. Você também foi guiado através dos passos de como adicionar permissões API à AZure Healthcare API. Por último, foi-lhe mostrado como criar um segredo de aplicação. Além disso, pode aprender a aceder ao seu servidor FHIR usando o Carteiro.
 
>[!div class="nextstepaction"]
>[Acesso Azure API para FHIR com Carteiro](access-fhir-postman-tutorial.md)
