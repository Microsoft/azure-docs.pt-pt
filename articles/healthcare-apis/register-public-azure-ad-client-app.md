---
title: Registe uma aplicação de cliente público em Azure AD - Azure API para FHIR
description: Este artigo explica como registar uma aplicação de cliente público no Azure Ative Directory, em preparação para a implantação da FHIR API em Azure.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 5aa9e5a33dbe66e3ebd787decfa3a520454fc6f6
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "84871793"
---
# <a name="register-a-public-client-application-in-azure-active-directory"></a>Registar um pedido de cliente público no Azure Ative Directory

Neste artigo, você vai aprender a registar uma aplicação pública no Azure Ative Diretório.  

Os registos de pedidos de cliente são representações do Azure Ative Directory de aplicações que podem autenticar e pedir permissões de API em nome de um utilizador. Os clientes públicos são aplicações como aplicações móveis e aplicações JavaScript de página única que não conseguem manter segredos confidenciais. O procedimento é semelhante ao [registo de um cliente confidencial,](register-confidential-azure-ad-client-app.md)mas como não se pode confiar nos clientes públicos para guardar um segredo de aplicação, não há necessidade de adicionar um.

## <a name="app-registrations-in-azure-portal"></a>Inscrições de aplicativos no portal Azure

1. No [portal do Azure](https://portal.azure.com), no painel de navegação do lado esquerdo, clique em **Azure Active Directory**.

2. Na lâmina do **Diretório Ativo Azure,** clique nos **registos da App:**

    ![Portal Azure. Novo Registo de Aplicações.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Clique no **Novo Registo.**

## <a name="application-registration-overview"></a>Visão geral do registo de pedidos

1. Dê à aplicação um nome de exibição.

2. Fornecer uma URL de resposta. O URL de resposta é onde os códigos de autenticação serão devolvidos à aplicação do cliente. Pode adicionar mais URLs de resposta e editar os existentes mais tarde.

    ![Portal Azure. Novo Registo de Aplicações públicas.](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-NAME.png)

## <a name="api-permissions"></a>Permissões de API

Da mesma forma com a [aplicação confidencial](register-confidential-azure-ad-client-app.md)do cliente, terá de selecionar quais as permissões da API que esta aplicação deve poder solicitar em nome dos utilizadores:

1. Abra as **permissões da API**.

    Se estiver a utilizar a API Azure para FHIR, adicionará uma permissão às APIs de Cuidados de Saúde Azure, procurando APIs de Azure Healthcare ao abrigo **de APIs que a minha organização utiliza** (imagem abaixo).
    
    Se estiver a fazer referência a uma aplicação de recursos diferente, selecione o [registo de aplicação de recursos da FHIR API](register-resource-azure-ad-client-app.md) que criou anteriormente ao abrigo **das Minhas APIs**:

    ![Portal Azure. Novas permissões públicas da API - Azure API para FHIR Predefinido](media/public-client-app/api-permissions.png)


2. Selecione as permissões que gostaria que a aplicação pudesse solicitar: ![ Portal Azure. Permissões da aplicação](media/public-client-app/app-permissions.png)

## <a name="validate-fhir-server-authority"></a>Validar a autoridade do servidor FHIR
Se a aplicação que registou neste artigo e o seu servidor FHIR estiver no mesmo inquilino AZure AD, é bom que avance para os próximos passos.

Se configurar a sua aplicação de cliente num inquilino AD Azure diferente do seu servidor FHIR, terá de atualizar a **Autoridade**. Na Azure API para FHIR, define a Autoridade em Definições -- > Autenticação. Desemaça a sua Autoridade para **https://login.microsoftonline.com/\<TENANT-ID>** .

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a registar uma aplicação de cliente público no Azure Ative Directory. Em seguida, teste o acesso ao seu servidor FHIR usando o Carteiro.
 
>[!div class="nextstepaction"]
>[Acesso Azure API para FHIR com Carteiro](access-fhir-postman-tutorial.md)
