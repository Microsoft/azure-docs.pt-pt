---
title: Disponibilidade e limitações do Microsoft Authenticator para Android na China Microsoft Docs
description: Saiba como obter a app Microsoft Authenticator - disponibilidade na China
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 05/20/2020
ms.author: curtand
ms.openlocfilehash: 795c68fc063d98bdee6ccf59dba6ee718dc92d03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84323030"
---
# <a name="microsoft-authenticator-for-android-in-the-public-cloud-in-china"></a>Microsoft Authenticator para Android na nuvem pública na China

O aplicativo Microsoft Authenticator para Android está disponível para download na China. A Google Play Store não se encontra disponível na China, pelo que a aplicação deve ser descarregada a partir de outros mercados de aplicações chinesas. A aplicação Microsoft Authenticator para Android está atualmente disponível nas seguintes lojas na China:

- [Baidu](https://shouji.baidu.com/software/26638379.html)
- [Lenovo](https://www.lenovomm.com/appdetail/com.azure.authenticator/20197724)
- [Huawei](https://appgallery.cloud.huawei.com/uowap/index.html#/detailApp/C100262999?source=appshare&subsource=C100262999&shareTo=weixin&locale=zh_CN)
- [Samsung Galaxy Store](http://apps.samsung.com/appquery/appDetail.as?appId=com.azure.authenticator)

A construção mais atual da aplicação encontra-se na Google Play Store, mas estamos a atualizar a aplicação em todas as outras lojas de aplicações o mais rapidamente possível. Como não existe um pacote personalizado de aplicações Android (APK) implantado em qualquer loja de aplicações, a aplicação pode ser atualizada sem problemas a partir de um dos seguintes locais:

- A loja foi descarregada de
- A Google Play Store se o utilizador cruzar regiões

## <a name="limitations"></a>Limitações

A aplicação Microsoft Authenticator para Android utiliza o sistema de mensagens Cloud Firebase da Google e os Serviços google Play para receber notificações push. Como nenhum dos serviços está disponível na China, existem algumas limitações nas funcionalidades da aplicação:

- O registo da aplicação Authenticator como método de autenticação de vários fatores (MFA) usando notificações push não funciona.

- [O registo telefónico](../authentication/howto-authentication-sms-signin.md) não pode ser configurado. Requer que o utilizador crie a aplicação como um método MFA usando notificações push, que atualmente não funcionam.

Se um utilizador já ter conseguido configurar a autenticação de entrada de telefone ou de vários fatores através da aplicação, pode efetuar uma verificação manual dos pedidos de notificações na aplicação e usá-la para verificação de identidade.

## <a name="multi-factor-authentication-workaround"></a>Solução de autenticação multi-factor

Em vez de utilizarem notificações push para autenticação multi-factor, os utilizadores podem [configurar a sua aplicação Authenticator para receber códigos de verificação](multi-factor-authentication-setup-auth-app.md#set-up-the-microsoft-authenticator-app-to-use-verification-codes) no seu dispositivo que podem usar para o MFA para verificar a sua identidade. Estes códigos de verificação são válidos por 30 segundos e para usá-los, os administradores devem permitir ao seu inquilino efetuar a verificação usando códigos de verificação de passwords One-Time (TOTP) baseados no tempo.

## <a name="availability"></a>Disponibilidade

Funcionalidade autenticadora da Microsoft | Disponibilidade na China
------------------------------- | ---------------------
Registo de MFA utilizando notificações push | Não
Conta MFA pré-existente que verifica a identidade usando notificações push | Não
Conta MFA pré-existente que realiza verificação manual de notificações | Sim
Registo/autenticação de MFA apenas através de códigos TOTP/verificação | Sim
Registo de inscrição no telefone | Não
Inscrição de telefone existente usando notificações push | Não
Verificação de entrada de telefone existente através da verificação manual dos pedidos de autenticação | Sim

## <a name="next-steps"></a>Próximos passos

- [Descarregue e instale a aplicação Microsoft Authenticator](user-help-auth-app-download-install.md)
