---
title: Inscrição utilizando a autenticação com uma conta de trabalho ou escola - Azure AD
description: Saiba como iniciar scontabilidade no seu trabalho ou conta escolar utilizando os vários métodos de verificação de dois fatores.
services: active-directory
author: curtand
manager: daveba
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/02/2017
ms.author: curtand
ms.reviewer: librown
ms.custom: end-user, seo-update-azuread-jan
ms.openlocfilehash: 51cfe2c448fde8c50f8b846979a4b35fa4be5ec1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90056033"
---
# <a name="sign-in-to-your-work-or-school-account-using-your-two-factor-verification-method"></a>Inscreva-se na sua conta de trabalho ou escola usando o seu método de verificação de dois fatores

> [!NOTE]
> O objetivo deste artigo é passar por uma experiência típica de inscrição. Para obter ajuda para iniciar sessão ou para resolver problemas, consulte [ter problemas com a autenticação multi-factor Azure](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>Qual será a sua experiência de inscrição?
A sua experiência de início de súdin difere consoante o que escolhe utilizar como segundo fator: uma chamada telefónica, uma aplicação de autenticação ou textos. Escolha a opção que melhor descreve o que está a fazer:

| Como se inscreve? |
| --- |
| [Com uma ligação para o meu telemóvel ou telefone de escritório](#signing-in-with-a-phone-call) |
| [Com uma mensagem para o meu telemóvel](#signing-in-with-a-text-message)
| [Com notificações da aplicação Microsoft Authenticator](#to-sign-in-with-a-notification-from-the-microsoft-authenticator-app) |
| Com códigos de verificação da aplicação Microsoft Authenticator |
| [Com um método alternativo, porque não posso usar o meu método preferido agora](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Entrando com um telefonema
As seguintes informações descrevem a experiência de verificação em duas etapas com uma chamada para o seu telemóvel ou telefone de escritório.

1. Inscreva-se numa aplicação ou serviço como o Microsoft 365 utilizando o seu nome de utilizador e senha.  
2. A Microsoft chama-te.  
3. Atende o telefone e acerte a chave #.  

## <a name="signing-in-with-a-text-message"></a>Iniciar sessão com uma mensagem de texto
As seguintes informações descrevem a experiência de verificação em duas etapas com uma mensagem de texto para o seu telemóvel:

1. Inscreva-se numa aplicação ou serviço como o Microsoft 365 utilizando o seu nome de utilizador e senha.
2. A Microsoft envia-lhe uma mensagem de texto que contém um código numer de números.
3. Introduza o código na caixa fornecida na página de entrada.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Iniciar sessão com a aplicação Microsoft Authenticator
As seguintes informações descrevem a experiência de utilização da aplicação Microsoft Authenticator para verificações em duas etapas. Existem duas maneiras diferentes de usar a app. Pode receber notificações push no seu dispositivo, ou pode abrir a aplicação para obter um código de verificação.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Para iniciar sôment com uma notificação da aplicação Microsoft Authenticator
1. Inscreva-se numa aplicação ou serviço como o Microsoft 365 utilizando o seu nome de utilizador e senha.
2. A Microsoft envia uma notificação para a aplicação Microsoft Authenticator no seu dispositivo.

   ![Microsoft envia notificação](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Abra a notificação no seu telefone e selecione a tecla **Verificar.** Se a sua empresa necessitar de um PIN, insira-o aqui.
4. Deve ser contratado.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Para iniciar sôms usando um código de verificação com a aplicação Microsoft Authenticator

Se utilizar a aplicação Microsoft Authenticator para obter códigos de verificação, quando abre a aplicação vê um número no nome da sua conta. Este número muda a cada 30 segundos para que não utilize o mesmo número duas vezes. Quando lhe for pedido um código de verificação, abra a aplicação e use qualquer número que esteja atualmente a ser apresentado.

1. Inscreva-se numa aplicação ou serviço como o Microsoft 365 utilizando o seu nome de utilizador e senha.
2. A Microsoft pede-lhe um código de verificação.

   ![Insira o código de verificação](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Abra a aplicação Microsoft Authenticator no seu telemóvel e introduza o código na caixa onde está a iniciar sessão.

## <a name="signing-in-with-an-alternate-method"></a>Iniciar sessão com um método alternativo
Às vezes não tem o telefone ou dispositivo que configura como o seu método de verificação preferido. Esta situação é a razão pela qual recomendamos que crie métodos de backup para a sua conta. A seguinte secção mostra como iniciar sôs com um método alternativo quando o seu método primário pode não estar disponível.

1. Inscreva-se numa aplicação ou serviço como o Microsoft 365 utilizando o seu nome de utilizador e senha.
2. Selecione **Utilize uma opção de verificação diferente.** Vê diferentes opções de verificação com base em quantas configurações.
3. Escolha um método alternativo e inscreva-se.

   ![Use método alternativo](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Passos seguintes
- Se tiver problemas em iniciar sessão com verificação em duas etapas, obtenha mais informações em [Ter problemas com a autenticação multi-factor Azure](multi-factor-authentication-end-user-troubleshoot.md).

- Saiba como [gerir as definições de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md).

- Descubra como [começar com a aplicação Microsoft Authenticator](user-help-auth-app-download-install.md) para que possa utilizar notificações para iniciar sôm, em vez de textos e chamadas telefónicas.
