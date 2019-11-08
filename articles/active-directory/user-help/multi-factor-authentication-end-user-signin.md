---
title: Entrar usando a verificação de dois fatores-Azure AD
description: Saiba como entrar em sua conta corporativa ou de estudante usando os vários métodos de verificação de dois fatores.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/02/2017
ms.author: lizross
ms.reviewer: librown
ms.custom: end-user, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: de080a613c69dd6f48f548c4ba89b2728e8c5e1d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820435"
---
# <a name="sign-in-to-your-work-or-school-account-using-two-factor-verification"></a>Entre em sua conta corporativa ou de estudante usando a verificação de dois fatores

> [!NOTE]
> A finalidade deste artigo é percorrer uma experiência de entrada típica. Para obter ajuda com a entrada ou para solucionar problemas, consulte [problemas com a autenticação multifator do Azure](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>Qual será sua experiência de entrada?
Sua experiência de entrada difere dependendo do que você escolher usar como seu segundo fator: uma chamada telefônica, um aplicativo de autenticação ou textos. Escolha a opção que melhor descreve o que você está fazendo:

| Como você entra? |
| --- |
| [Com uma chamada telefônica para meu telefone celular ou comercial](#signing-in-with-a-phone-call) |
| [Com um texto para meu celular](#signing-in-with-a-text-message)
| [Com notificações do aplicativo Microsoft Authenticator](#to-sign-in-with-a-notification-from-the-microsoft-authenticator-app) |
| Com códigos de verificação do aplicativo Microsoft Authenticator |
| [Com um método alternativo, porque eu não posso usar o meu método preferencial no momento](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Entrando com uma chamada telefônica
As informações a seguir descrevem a experiência de verificação em duas etapas com uma chamada para seu telefone celular ou comercial.

1. Entre em um aplicativo ou serviço como o Office 365 usando seu nome de usuário e senha.  
2. A Microsoft chama você.  
3. Responda ao telefone e pressione a tecla #.  

## <a name="signing-in-with-a-text-message"></a>Entrando com uma mensagem de texto
As informações a seguir descrevem a experiência de verificação em duas etapas com uma mensagem de texto para seu telefone celular:

1. Entre em um aplicativo ou serviço como o Office 365 usando seu nome de usuário e senha.
2. A Microsoft envia uma mensagem de texto que contém um código numérico.
3. Insira o código na caixa fornecida na página de entrada.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Entrando com o aplicativo Microsoft Authenticator
As informações a seguir descrevem a experiência de usar o aplicativo Microsoft Authenticator para verificações em duas etapas. Há duas maneiras diferentes de usar o aplicativo. Você pode receber notificações por push em seu dispositivo ou pode abrir o aplicativo para obter um código de verificação.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Para entrar com uma notificação do aplicativo Microsoft Authenticator
1. Entre em um aplicativo ou serviço como o Office 365 usando seu nome de usuário e senha.
2. A Microsoft envia uma notificação para o aplicativo Microsoft Authenticator em seu dispositivo.

   ![A Microsoft envia notificação](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Abra a notificação em seu telefone e selecione a chave **verificar** . Se sua empresa exigir um PIN, insira-o aqui.
4. Agora você deve estar conectado.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Para entrar usando um código de verificação com o aplicativo Microsoft Authenticator

Se você usar o aplicativo Microsoft Authenticator para obter códigos de verificação, quando abrir o aplicativo, verá um número sob o nome da conta. Esse número é alterado a cada 30 segundos para que você não use o mesmo número duas vezes. Quando for solicitado um código de verificação, abra o aplicativo e use qualquer número exibido no momento.

1. Entre em um aplicativo ou serviço como o Office 365 usando seu nome de usuário e senha.
2. A Microsoft solicita um código de verificação.

   ![Inserir código de verificação](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Abra o aplicativo Microsoft Authenticator em seu telefone e insira o código na caixa em que você está entrando.

## <a name="signing-in-with-an-alternate-method"></a>Entrando com um método alternativo
Às vezes, você não tem o telefone ou dispositivo que você configurou como seu método de verificação preferencial. Essa situação é o motivo pelo qual recomendamos que você configure métodos de backup para sua conta. A seção a seguir mostra como entrar com um método alternativo quando o método primário pode não estar disponível.

1. Entre em um aplicativo ou serviço como o Office 365 usando seu nome de usuário e senha.
2. Selecione **usar uma opção de verificação diferente**. Você vê diferentes opções de verificação com base em quantas foram configuradas.
3. Escolha um método alternativo e entre.

   ![Usar método alternativo](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Passos seguintes
- Se você tiver problemas para entrar com a verificação em duas etapas, obtenha mais informações em como [ter problemas com a autenticação multifator do Azure](multi-factor-authentication-end-user-troubleshoot.md).

- Saiba como [gerenciar suas configurações de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md).

- Descubra como começar [a usar o aplicativo Microsoft Authenticator](user-help-auth-app-download-install.md) para que você possa utilizar notificações para entrar, em vez de textos e chamadas telefônicas.
