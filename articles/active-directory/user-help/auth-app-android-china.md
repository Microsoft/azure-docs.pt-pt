---
title: Disponibilidade e limitações do Microsoft Autenticador para Android na China Microsoft Docs
description: Saiba como obter a aplicação Microsoft Authenticator - disponibilidade na China
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 05/20/2020
ms.author: curtand
ms.openlocfilehash: e42c4e5aced9fd567c67b520a98318c965045e07
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715533"
---
# <a name="microsoft-authenticator-for-android-in-the-public-cloud-in-china"></a>Autenticador da Microsoft para Android na nuvem pública na China

A aplicação Microsoft Authenticator para Android está disponível para download na China. A Google Play Store não está disponível na China, pelo que a aplicação deve ser descarregada de outros mercados de aplicações chinesas. A aplicação Microsoft Authenticator para Android está atualmente disponível nas seguintes lojas na China:

- [Baidu](https://shouji.baidu.com/software/26638379.html)
- [Lenovo](https://www.lenovomm.com/appdetail/com.azure.authenticator/20197724)
- [Huawei](https://appgallery.cloud.huawei.com/uowap/index.html#/detailApp/C100262999?source=appshare&subsource=C100262999&shareTo=weixin&locale=zh_CN)
- [Samsung Galaxy Store](http://apps.samsung.com/appquery/appDetail.as?appId=com.azure.authenticator)

A construção mais atual da aplicação está na Google Play Store, mas estamos a atualizar a aplicação em todas as outras lojas de aplicações o mais rápido possível. Como não existe um pacote personalizado de aplicações Android (APK) implantado em qualquer loja de aplicações, a aplicação pode ser atualizada perfeitamente a partir de um dos seguintes locais:

- A loja que foi descarregado de
- A Google Play Store se o utilizador atravessar regiões

## <a name="limitations"></a>Limitações

A aplicação Microsoft Authenticator para Android utiliza o sistema de mensagens Cloud da Base de Fogo da Google e os Serviços Google Play para receber notificações push. Como nenhum dos serviços está disponível na China, existem algumas limitações nas funcionalidades da app:

- O registo da aplicação Autenticador como método de autenticação multifactor (MFA) utilizando notificações push não funciona.

- [O registo telefónico](../authentication/howto-authentication-sms-signin.md) não pode ser configurado. Requer que o utilizador configurar a aplicação como um método MFA utilizando notificações push, que atualmente não funcionam.

Se um utilizador tiver conseguido previamente configurar a autenticação de entrada ou multifactor através da aplicação, pode efetuar uma verificação manual para pedidos de notificações na aplicação e utilizá-la para verificação de identidade.

## <a name="multi-factor-authentication-workaround"></a>Supor de autenticação de vários fatores

Em vez de utilizarem notificações push para autenticação de vários fatores, os utilizadores podem configurar o seu MFA para receber códigos de verificação no seu dispositivo que podem usar para verificar a sua identidade. Estes códigos de verificação são válidos por 30 segundos e para usá-los, os administradores devem permitir que o seu inquilino efetue a verificação utilizando códigos de verificação de uma palavra-passe de uma vez (TOTP) baseados no tempo.

## <a name="availability"></a>Disponibilidade

Funcionalidade do Autenticador microsoft | Disponibilidade na China
------------------------------- | ---------------------
Registo de MFA com notificações push | Não
Conta MFA pré-existente que verifica identidade usando notificações push | Não
Conta MFA pré-existente que executa verificação manual para notificações | Sim
Registo/autenticação do MFA utilizando apenas códigos TOTP/verificação | Sim
Registo de Registo de Registo telefónico | Não
Sinal de telefone existente usando notificações push | Não
Verificação de registo telefónico existente através da verificação manual de pedidos de autenticação | Sim

## <a name="next-steps"></a>Próximos passos

- [Descarregue e instale a aplicação Microsoft Authenticator](user-help-auth-app-download-install.md)
