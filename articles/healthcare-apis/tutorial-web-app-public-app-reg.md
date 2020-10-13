---
title: Tutorial de Aplicativo Web - Configuração de aplicação de clientes
description: Este tutorial percorre os passos de registo de uma aplicação pública para se preparar para implementar uma aplicação web
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: c4f6124227f5dd5c7735d300f71d5ae7d0c06b3a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975778"
---
# <a name="client-application-registration"></a>Registo de inscrição de cliente
No tutorial anterior, implementou e montou a sua API Azure para fHIR. Agora que tem a sua AZure API para a configuração do FHIR, vamos registar uma aplicação de cliente público. Pode ler através do registo completo [de uma aplicação de cliente público](register-public-azure-ad-client-app.md) como orientar para mais detalhes ou resolução de problemas, mas nós chamamos os principais passos para este tutorial abaixo.

1. Navegue para o Diretório Ativo Azure
1. Selecione **registo de**  -->  **aplicações novo registo**
1. Nomeie a sua candidatura e se
1. Selecione **cliente público/nativo (mobile & desktop)** e desacione o URI de redirecionamento para https://www.getpostman.com/oauth2/callback .

   :::image type="content" source="media/tutorial-web-app/register-public-app.png" alt-text="Screenshot do Painel de Aplicação Registro e um nome de aplicação de exemplo e URL de redirecionamento.":::

## <a name="client-application-settings"></a>Definições de aplicação de cliente

Assim que o seu pedido de cliente estiver registado, copie o ID da Aplicação (cliente) e o ID do Inquilino na Página geral. Vai precisar destes dois valores mais tarde ao aceder ao cliente.

:::image type="content" source="media/tutorial-web-app/client-id-tenant-id.png" alt-text="Screenshot do Painel de Aplicação Registro e um nome de aplicação de exemplo e URL de redirecionamento.":::

### <a name="connect-with-web-app"></a>Conecte-se com a aplicação web

Se escreveu [a sua aplicação web](tutorial-web-app-write-web-app.md) para se conectar com a API Azure para FHIR, também precisa definir as opções de autenticação corretas. 

1. No menu esquerdo, em **Manage,** **selecione Autenticação.** 

1. Para adicionar uma nova configuração da plataforma, selecione **Web**.

1. Configurar o URI de redirecionamento em preparação para quando criar a sua aplicação web na quarta parte deste tutorial. Para isso, adicione `https://\<WEB-APP-NAME>.azurewebsites.net` à lista URI de redirecionamento. Se escolher um nome diferente durante o passo em que escreve a [sua aplicação web,](tutorial-web-app-write-web-app.md)terá de voltar e atualizá-lo.

1. Selecione as caixas de verificação **de token de acesso** e **id.**

   :::image type="content" source="media/tutorial-web-app/web-app-authentication.png" alt-text="Screenshot do Painel de Aplicação Registro e um nome de aplicação de exemplo e URL de redirecionamento.":::

## <a name="add-api-permissions"></a>Adicionar permissões de APIs

Agora que configurar a autenticação correta, desa um conjunto das permissões da API:

1. Selecione **permissões API** e clique **em Adicionar uma permissão.**
1. Segundo **as APIs, a minha organização usa,** procure apis de cuidados de saúde Azure.
1. Selecione **user_impersonation** e clique **em adicionar permissões**.

:::image type="content" source="media/tutorial-web-app/api-permissions.png" alt-text="Screenshot do Painel de Aplicação Registro e um nome de aplicação de exemplo e URL de redirecionamento.":::

## <a name="next-steps"></a>Passos Seguintes
Agora tem uma aplicação de cliente público. No próximo tutorial, vamos percorrer testes e ter acesso a esta aplicação através do Carteiro.

>[!div class="nextstepaction"]
>[Teste de aplicação do cliente no Carteiro](tutorial-web-app-test-postman.md)
