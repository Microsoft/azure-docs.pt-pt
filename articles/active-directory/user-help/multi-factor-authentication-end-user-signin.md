---
title: Início de sessão com autenticação com conta de trabalho ou escola - Azure AD
description: Aprenda a inscrever-se no seu trabalho ou na conta escolar utilizando os vários métodos de verificação de dois fatores.
services: active-directory
author: curtand
manager: daveba
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/02/2017
ms.author: curtand
ms.reviewer: librown
ms.custom: end-user, seo-update-azuread-jan
ms.openlocfilehash: 33cf9e284d2206ea497af7a5da7c3cf4a890cc87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064092"
---
# <a name="sign-in-to-your-work-or-school-account-using-your-two-factor-verification-method"></a>Inscreva-se no seu trabalho ou conta escolar utilizando o seu método de verificação de dois fatores

> [!NOTE]
> O objetivo deste artigo é passar por uma experiência típica de inscrição. Para ajuda na sessão, ou para resolver problemas, consulte Ter problemas com a [autenticação de multi-factores Azure](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>Qual será a sua experiência de inscrição?
A sua experiência de início de sessão difere consoante o que opte por usar como segundo fator: uma chamada telefónica, uma aplicação de autenticação ou textos. Escolha a opção que melhor descreve o que está a fazer:

| Como se inscreveu? |
| --- |
| [Com um telefonema para o meu telemóvel ou telefone de escritório](#signing-in-with-a-phone-call) |
| [Com uma mensagem para o meu telemóvel](#signing-in-with-a-text-message)
| [Com notificações da aplicação Microsoft Authenticator](#to-sign-in-with-a-notification-from-the-microsoft-authenticator-app) |
| Com códigos de verificação da aplicação Microsoft Authenticator |
| [Com um método alternativo, porque não posso usar o meu método preferido agora.](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Sessão com um telefonema
As seguintes informações descrevem a experiência de verificação em duas etapas com uma chamada para o seu telemóvel ou telefone de escritório.

1. Inscreva-se numa aplicação ou serviço, como o Office 365, utilizando o seu nome de utilizador e senha.  
2. A Microsoft liga-te.  
3. Atenda o telefone e acerte a tecla #.  

## <a name="signing-in-with-a-text-message"></a>Sessão com uma mensagem de texto
As seguintes informações descrevem a experiência de verificação em duas etapas com uma mensagem de texto para o seu telemóvel:

1. Inscreva-se numa aplicação ou serviço, como o Office 365, utilizando o seu nome de utilizador e senha.
2. A Microsoft envia-lhe uma mensagem de texto que contém um código de número.
3. Introduza o código na caixa fornecida na página de entrada.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Sessão com a aplicação Microsoft Authenticator
As seguintes informações descrevem a experiência de utilização da aplicação Microsoft Authenticator para verificações em duas etapas. Existem duas formas diferentes de usar a app. Pode receber notificações push no seu dispositivo, ou pode abrir a aplicação para obter um código de verificação.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Para iniciar sessão com uma notificação da aplicação Microsoft Authenticator
1. Inscreva-se numa aplicação ou serviço, como o Office 365, utilizando o seu nome de utilizador e senha.
2. A Microsoft envia uma notificação para a aplicação Autenticadora Microsoft no seu dispositivo.

   ![Microsoft envia notificação](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Abra a notificação no seu telefone e selecione a tecla **Verificar.** Se a sua empresa necessitar de um PIN, insira-o aqui.
4. Agora devia sintetrá-lo.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Para iniciar sessão utilizando um código de verificação com a aplicação Microsoft Authenticator

Se utilizar a aplicação Microsoft Authenticator para obter códigos de verificação, quando abre a aplicação, vê um número no nome da sua conta. Este número muda a cada 30 segundos para que não use o mesmo número duas vezes. Quando lhe for pedido um código de verificação, abra a aplicação e use qualquer número que esteja atualmente apresentado.

1. Inscreva-se numa aplicação ou serviço, como o Office 365, utilizando o seu nome de utilizador e senha.
2. A Microsoft pede-lhe um código de verificação.

   ![Introduzir código de verificação](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Abra a aplicação Microsoft Authenticator no seu telefone e introduza o código na caixa onde está a iniciar sessão.

## <a name="signing-in-with-an-alternate-method"></a>Iniciar sessão com um método alternativo
Às vezes não tem o telefone ou dispositivo que configura como o seu método de verificação preferido. Esta situação é por isso que recomendamos que você configurar métodos de backup para a sua conta. A secção seguinte mostra-lhe como iniciar sessão com um método alternativo quando o seu método primário pode não estar disponível.

1. Inscreva-se numa aplicação ou serviço, como o Office 365, utilizando o seu nome de utilizador e senha.
2. Selecione **Utilize uma opção**de verificação diferente . Você vê diferentes opções de verificação com base em quantas você tem configurado.
3. Escolha um método alternativo e inscreva-se.

   ![Utilizar método alternativo](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Passos seguintes
- Se tiver problemas em iniciar sessão com verificação em duas etapas, obtenha mais informações sobre Ter problemas com a [autenticação multi-factor Azure](multi-factor-authentication-end-user-troubleshoot.md).

- Saiba como [gerir as definições de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md).

- Saiba como [começar com a aplicação Microsoft Authenticator](user-help-auth-app-download-install.md) para que possa usar notificações para iniciar o seu início, em vez de textos e chamadas telefónicas.
