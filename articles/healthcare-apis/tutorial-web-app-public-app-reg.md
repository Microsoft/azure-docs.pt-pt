---
title: Tutorial de Aplicativo Web - Configuração de aplicação de clientes
description: Este tutorial percorre os passos de registo de uma aplicação pública para se preparar para implementar uma aplicação web
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: mihansen
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 58e21e46edfe1d03d42bf2202bcf1f22282631a9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "84872642"
---
# <a name="client-application-registration"></a>Registo de inscrição de cliente
No tutorial anterior, implementou e montou a sua API Azure para fHIR. Agora que tem a sua AZure API para a configuração do FHIR, vamos registar uma aplicação de cliente público. Pode ler através do registo completo [de uma aplicação de cliente público](register-public-azure-ad-client-app.md) como orientar para mais detalhes ou resolução de problemas, mas nós chamamos os principais passos para este tutorial abaixo.

1. Navegue para o Diretório Ativo Azure
1. Selecione **registo de**  -->  **aplicações novo registo**
1. Nomeie a sua aplicação e crie o URI de redirecionamento parahttps://www.getpostman.com/oauth2/callback


![Inscrição de pedido de cliente](media/tutorial-web-app/reg-public-app.png)

## <a name="client-application-settings"></a>Definições de aplicação de cliente
Assim que o seu pedido de cliente estiver registado, copie o ID da Aplicação (cliente) da Página geral. Vai precisar deste valor mais tarde ao aceder ao cliente.

![ID de aplicativo de cópia](media/tutorial-web-app/app-id.png)

Em seguida, desa estale as opções de autenticação corretas. Selecione **autenticação** do lado esquerdo. Verifique as caixas **de token de acesso** e **identificação.** Também pode configurar o URI de redirecionamento em preparação para quando criar a sua aplicação web na quarta parte deste tutorial. Para isso, adicione https:// \< WEB-APP-NAME>.azurewebsites.net à lista URI de redirecionamento. Se escolher um nome diferente durante o passo em que escreve a [sua aplicação web,](tutorial-web-app-write-web-app.md)terá de voltar e atualizá-lo.

![Definições de autenticação de aplicativos](media/tutorial-web-app/app-authentication.png)

Agora que configurar a autenticação correta, desa estale as permissões API. 
1. Selecione **permissões API** e clique **Em Adicionar uma permissão**
1. Sob **APIs a minha organização usa,** pesquisa por APIs de Cuidados de Saúde Azure
1. Selecione **user_impersonation** e clique **em adicionar permissões**

## <a name="next-steps"></a>Passos Seguintes
Agora tem uma aplicação de cliente público. No próximo tutorial, vamos percorrer testes e ter acesso a esta aplicação através do Carteiro.

>[!div class="nextstepaction"]
>[Teste de aplicação do cliente no Carteiro](tutorial-web-app-test-postman.md)
