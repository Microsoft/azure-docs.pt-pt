---
title: Adicionar autenticação no Android
description: Saiba como utilizar o Azure App Service para autenticar os utilizadores da sua aplicação Android com fornecedores de identidade como Google, Facebook, Twitter e Microsoft.
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 705ebb5809840155e6bbf3f8eef091eb95f63e63
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461645"
---
# <a name="add-authentication-to-your-android-app"></a>Adicione autenticação à sua aplicação Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Resumo
Neste tutorial, adiciona-se autenticação ao projeto de arranque rápido todo-lista no Android utilizando um fornecedor de identidade suportado. Este tutorial é baseado no Get started com o tutorial de [Começar com aplicativos móveis] que você deve completar primeiro.

## <a name="register"></a>Registe a sua aplicação para autenticação e configure o Serviço de Aplicações Azure
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicione a sua aplicação aos URLs De Redirecionamento Externo Permitidos

A autenticação segura requer que defina um novo esquema de URL para a sua aplicação. Isto permite que o sistema de autenticação redirecione para a sua aplicação uma vez concluído o processo de autenticação. Neste tutorial, usamos o _nome_ de aplicativo do esquema URL em toda a parte. No entanto, pode utilizar qualquer esquema de URL que escolha. Deve ser exclusivo da sua aplicação móvel. Para ativar a reorientação do lado do servidor:

1. No [Portal do Azure]selecione o seu Serviço de Aplicações.

2. Clique na opção de **menu Autenticação/Autorização.**

3. Nos **URLs de Redirecionamento Externo Permitidos,** introduza `appname://easyauth.callback`.  O _nome de aplicação_ nesta cadeia é o URL Scheme para a sua aplicação móvel.  Deve seguir a especificação normal do URL para um protocolo (utilize apenas letras e números, e comece com uma letra).  Deve tomar nota da corda que escolher, pois terá de ajustar o seu código de aplicação móvel com o URL Scheme em vários locais.

4. Clique em **OK**.

5. Clique em **Guardar**.

## <a name="permissions"></a>Restringir permissões a utilizadores autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* No Android Studio, abra o projeto que completou com o tutorial [Começar com aplicativos móveis] A partir do menu **Executar,** clique na **aplicação Executar**, e verifique se uma exceção não tratada com um código de estado de 401 (Não autorizado) é levantada após o início da aplicação.

     Esta exceção acontece porque a aplicação tenta aceder à parte de trás como um utilizador não autenticado, mas a tabela *TodoItem* agora requer autenticação.

Em seguida, atualiza a aplicação para autenticar os utilizadores antes de solicitar recursos das Aplicações Móveis.

## <a name="add-authentication-to-the-app"></a>Adicionar autenticação à app
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>Fichas de autenticação cache no cliente
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Passos seguintes
Agora que completou este tutorial básico de autenticação, considere continuar a um dos seguintes tutoriais:

* [Adicione notificações push à sua aplicação Android](app-service-mobile-android-get-started-push.md).
  Saiba como configurar as suas Aplicações Móveis no final para utilizar centros de notificação Azure para enviar notificações push.
* [Ative a sincronização offline para a sua aplicação Android.](app-service-mobile-android-get-started-offline-data.md)
  Aprenda a adicionar suporte offline à sua aplicação utilizando uma extremidade de apps móveis. Com sincronização offline, os utilizadores podem interagir com uma aplicação móvel&mdash;visualização, adição ou modificação de dados&mdash;mesmo quando não há ligação de rede.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Começar com aplicativos móveis]: app-service-mobile-android-get-started.md
[Portal do Azure]: https://portal.azure.com/
