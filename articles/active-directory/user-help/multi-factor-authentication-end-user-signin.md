---
title: Azure MFA início de sessão com a verificação de dois passos | Documentos da Microsoft
description: Esta página irá fornecer orientações sobre onde ir para ver os vários início de sessão métodos disponíveis com o MFA do Azure.
keywords: autenticação de utilizador, início de sessão experiência, inicie sessão com o número de telemóvel, inicie sessão com o telefone do escritório
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/02/2017
ms.author: lizross
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: 2bba40d48b855b2cfa5c607e63a49e2bb79b6b23
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059453"
---
# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>A experiência de início de sessão com o Azure multi-factor Authentication
> [!NOTE]
> O objetivo deste artigo é percorrer uma experiência de início de sessão normal. Para obter ajuda com a iniciar sessão ou para solucionar problemas, consulte [a ter problemas com o Azure multi-factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>Qual será sua experiência de início de sessão?
Sua experiência de início de sessão é diferente consoante aquilo que escolher utilizar como seu segundo fator: uma chamada telefónica, uma aplicação de autenticação ou textos. Escolha a opção que melhor descreve o que está a fazer:

| Como iniciar sessão? |
| --- |
| [Com uma chamada telefónica para meu telefone mobile ou o office](#signing-in-with-a-phone-call) |
| [Com uma mensagem de texto para o meu telemóvel](#signing-in-with-a-text-message)
| [Com as notificações a partir da aplicação Microsoft Authenticator](#signing-in-with-the-microsoft-authenticator-app-using-notification) |
| [Com os códigos de verificação a partir da aplicação Microsoft Authenticator](#signing-in-with-the-microsoft-authenticator-app-using-verification-code) |
| [Com um método alternativo, porque eu não é possível utilizar o meu método preferido de neste momento](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Iniciar sessão com uma chamada telefónica
As seguintes informações descrevem a experiência de verificação de dois passos com uma chamada para o seu telefone mobile ou o office.

1. Inicie sessão para uma aplicação ou serviço como o Office 365 com o seu nome de utilizador e palavra-passe.  
2. A Microsoft chama de.  
3. Atenda o telefone e prima a tecla #.  

## <a name="signing-in-with-a-text-message"></a>Iniciar sessão com uma mensagem de texto
As informações seguintes descrevem a experiência de verificação de dois passos com uma mensagem de texto para o telemóvel:

1. Inicie sessão para uma aplicação ou serviço como o Office 365 com o seu nome de utilizador e palavra-passe.
2. A Microsoft envia uma mensagem de texto que contém um código de número.
3. Introduza o código na caixa fornecida na página de início de sessão.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Iniciar sessão com a aplicação Microsoft Authenticator
As informações seguintes descrevem a experiência de utilização da aplicação Microsoft Authenticator para verificações de dois passos. Existem duas formas diferentes de utilizar a aplicação. Pode receber notificações push no seu dispositivo, ou pode abrir a aplicação para obter um código de verificação.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Para iniciar sessão com uma notificação a partir da aplicação Microsoft Authenticator
1. Inicie sessão para uma aplicação ou serviço como o Office 365 com o seu nome de utilizador e palavra-passe.
2. A Microsoft envia uma notificação para a aplicação Microsoft Authenticator no seu dispositivo.

  ![A Microsoft envia a notificação](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Abra a notificação no seu telemóvel e selecione o **Verifique se** chave. Se a sua empresa exigir um PIN, introduza-o aqui.
4. Deve agora ser conectado.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Para iniciar sessão com um código de verificação com a aplicação Microsoft Authenticator

Se utilizar a aplicação Microsoft Authenticator para obter códigos de verificação, em seguida, quando abrir a aplicação verá um número em nome da sua conta. Este número altera a cada 30 segundos para que não usa o mesmo número duas vezes. Quando lhe for pedido para um código de verificação, abra a aplicação e utilizar qualquer número é atualmente apresentado.

1. Inicie sessão para uma aplicação ou serviço como o Office 365 com o seu nome de utilizador e palavra-passe.
2. Microsoft pede-lhe um código de verificação.

  ![Introduzir código de verificação](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Abra a aplicação Microsoft Authenticator no seu telemóvel e introduza o código na caixa de onde estiver a iniciar sessão.

## <a name="signing-in-with-an-alternate-method"></a>Iniciar sessão com um método alternativo
Por vezes, não tem o telefone ou o dispositivo que configurou como o seu método preferido de verificação. Esta situação é por isso que recomendamos que configure métodos de cópia de segurança para a sua conta. A seção a seguir mostra como iniciar sessão com um método alternativo para o quando o método principal pode não estar disponível.

1. Inicie sessão para uma aplicação ou serviço como o Office 365 com o seu nome de utilizador e palavra-passe.
2. Selecione **utilizar outra opção de verificação**. Ver opções de verificação diferente com base no número tiver configurado.
3. Escolha um método alternativo e iniciar sessão.

  ![Utilize o método alternativo](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Passos Seguintes

Se tiver problemas ao iniciar sessão com a verificação de dois passos, obter mais informações em [a ter problemas com o Azure multi-factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

Saiba como [gerir as definições da verificação de dois passos](multi-factor-authentication-end-user-manage-settings.md).

Descubra como [começar com a aplicação Microsoft Authenticator](microsoft-authenticator-app-how-to.md) para que possa utilizar notificações para iniciar sessão, em vez de textos e chamadas telefónicas.
