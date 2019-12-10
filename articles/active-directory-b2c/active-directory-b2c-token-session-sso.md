---
title: Configuração de sessão e logon único
titleSuffix: Azure AD B2C
description: Configuração de sessão e logon único (SSO) no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 736dd1f0490c2c9c7c4f526df96dd5ace6a1f819
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950073"
---
# <a name="session-and-single-sign-on-configuration-in-azure-active-directory-b2c"></a>Configuração de sessão e logon único no Azure Active Directory B2C

Esse recurso oferece um controle refinado, de acordo com o [fluxo por usuário](active-directory-b2c-reference-policies.md), de:

- Tempos de vida das sessões de aplicativo Web gerenciadas pelo Azure AD B2C.
- Comportamento de SSO (logon único) em vários aplicativos e fluxos de usuário em seu locatário Azure AD B2C.

## <a name="session-behavior"></a>Comportamento da sessão

O Azure AD B2C dá suporte ao [protocolo de Autenticação OpenID Connect](active-directory-b2c-reference-oidc.md) para habilitar a entrada segura em aplicativos Web. Você pode usar as seguintes propriedades para gerenciar sessões de aplicativos Web:

- **Tempo de vida da sessão do aplicativo Web (minutos)** – o tempo de vida do cookie de sessão B2C do Azure ad armazenado no navegador do usuário após a autenticação bem-sucedida.
    - Padrão = 1440 minutos.
    - Mínimo (inclusivo) = 15 minutos.
    - Máximo (inclusivo) = 1440 minutos.
- **Tempo limite da sessão do aplicativo Web** -se essa opção for definida como **absoluta**, o usuário será forçado a autenticar novamente depois que o período especificado pelo tempo de **vida da sessão do aplicativo Web (minutos)** tiver decorrido. Se essa opção for definida como **sem interrupção** (a configuração padrão), o usuário permanecerá conectado, desde que o usuário esteja continuamente ativo em seu aplicativo Web.

Os seguintes casos de uso são habilitados usando estas propriedades:

- Atenda aos requisitos de segurança e conformidade do seu setor definindo os tempos de vida de sessão do aplicativo Web apropriados.
- Forçar a autenticação após um período de tempo definido durante a interação de um usuário com uma parte de segurança alta do seu aplicativo Web.

Essas configurações não estão disponíveis para fluxos de usuário de redefinição de senha.

## <a name="single-sign-on-sso-configuration"></a>Configuração de SSO (logon único)

Se você tiver vários aplicativos e fluxos de usuário em seu locatário B2C, poderá gerenciar interações de usuário entre eles usando a propriedade de **configuração de logon único** . Você pode definir a propriedade para uma das seguintes configurações:

- **Locatário** -essa configuração é o padrão. O uso dessa configuração permite que vários aplicativos e fluxos de usuário em seu locatário B2C compartilhem a mesma sessão de usuário. Por exemplo, quando um usuário entra em um aplicativo, o usuário também pode entrar diretamente em outro, contoso farmácia, ao acessá-lo.
- **Aplicativo** – essa configuração permite que você mantenha uma sessão de usuário exclusivamente para um aplicativo, independentemente de outros aplicativos. Por exemplo, se você quiser que o usuário entre no contoso farmácia (com as mesmas credenciais), mesmo que o usuário já tenha entrado no contoso shopping, outro aplicativo no mesmo locatário B2C.
- **Política** – essa configuração permite que você mantenha uma sessão de usuário exclusivamente para um fluxo de usuário, independentemente dos aplicativos que o utilizam. Por exemplo, se o usuário já tiver entrado e concluído uma etapa de autenticação multifator (MFA), o usuário poderá receber acesso a partes de segurança mais alta de vários aplicativos, desde que a sessão vinculada ao fluxo do usuário não expire.
- **Desabilitado** – essa configuração força o usuário a executar todo o fluxo do usuário em cada execução da política.

Essas configurações não estão disponíveis para fluxos de usuário de redefinição de senha.

