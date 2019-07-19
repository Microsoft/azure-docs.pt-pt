---
title: Configurar o comportamento da sessão-Azure Active Directory B2C | Microsoft Docs
description: Configurar o comportamento da sessão no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b18a7d98654422951773c0a5497f69db93782f51
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849440"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Configurar o comportamento da sessão no Azure Active Directory B2C

Esse recurso oferece um controle refinado, de acordo com o [fluxo por usuário](active-directory-b2c-reference-policies.md), de:

- Tempos de vida das sessões de aplicativo Web gerenciadas pelo Azure AD B2C.
- Comportamento de SSO (logon único) em vários aplicativos e fluxos de usuário em seu locatário Azure AD B2C.

Essas configurações não estão disponíveis para fluxos de usuário de redefinição de senha.

O Azure AD B2C dá suporte ao [protocolo de Autenticação OpenID Connect](active-directory-b2c-reference-oidc.md) para habilitar a entrada segura em aplicativos Web. Você pode usar as seguintes propriedades para gerenciar sessões de aplicativos Web:

## <a name="session-behavior-properties"></a>Propriedades de comportamento da sessão

- **Tempo de vida da sessão do aplicativo Web (minutos)** – o tempo de vida do cookie de sessão B2C do Azure ad armazenado no navegador do usuário após a autenticação bem-sucedida.
    - Padrão = 1440 minutos.
    - Mínimo (inclusivo) = 15 minutos.
    - Máximo (inclusivo) = 1440 minutos.
- **Tempo limite da sessão do aplicativo Web** -se essa opção for definida como **absoluta**, o usuário será forçado a autenticar novamente depois que o período especificado pelo tempo de **vida da sessão do aplicativo Web (minutos)** tiver decorrido. Se essa opção for definida como **sem interrupção** (a configuração padrão), o usuário permanecerá conectado, desde que o usuário esteja continuamente ativo em seu aplicativo Web.
- **Configuração de logon único** Se você tiver vários aplicativos e fluxos de usuário em seu locatário B2C, poderá gerenciar interações de usuário entre eles usando a propriedade de **configuração de logon único** . Você pode definir a propriedade para uma das seguintes configurações:
    - **Locatário** -essa configuração é o padrão. O uso dessa configuração permite que vários aplicativos e fluxos de usuário em seu locatário B2C compartilhem a mesma sessão de usuário. Por exemplo, quando um usuário entra em um aplicativo, o usuário também pode entrar diretamente em outro, contoso farmácia, ao acessá-lo.
    - **Aplicativo** – essa configuração permite que você mantenha uma sessão de usuário exclusivamente para um aplicativo, independentemente de outros aplicativos. Por exemplo, se você quiser que o usuário entre no contoso farmácia (com as mesmas credenciais), mesmo que o usuário já tenha entrado no contoso shopping, outro aplicativo no mesmo locatário B2C.
    - **Política** – essa configuração permite que você mantenha uma sessão de usuário exclusivamente para um fluxo de usuário, independentemente dos aplicativos que o utilizam. Por exemplo, se o usuário já tiver entrado e concluído uma etapa de autenticação multifator (MFA), o usuário poderá receber acesso a partes de segurança mais alta de vários aplicativos, desde que a sessão vinculada ao fluxo do usuário não expire.
    - **Desabilitado** – essa configuração força o usuário a executar todo o fluxo do usuário em cada execução da política.

Os seguintes casos de uso são habilitados usando estas propriedades:

- Atenda aos requisitos de segurança e conformidade do seu setor definindo os tempos de vida de sessão do aplicativo Web apropriados.
- Forçar a autenticação após um período de tempo definido durante a interação de um usuário com uma parte de segurança alta do seu aplicativo Web.

## <a name="configure-the-properties"></a>Configurar as propriedades

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém seu locatário Azure AD B2C clicando no **filtro diretório e assinatura** no menu superior e escolhendo o diretório que contém o locatário Azure ad B2C.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
4. Selecione **fluxos de usuário (políticas)** .
5. Abra o fluxo de usuário que você criou anteriormente.
6. Selecione **propriedades**.
7. Configure a **vida útil da sessão do aplicativo Web (minutos)** , o **tempo limite da sessão do aplicativo Web**, **a configuração de logon único**e **exija o token de ID em solicitações de logout** , conforme necessário.

    ![Configurações de propriedade de comportamento de sessão no portal do Azure](./media/session-behavior/session-behavior.png)

8. Clique em **Guardar**.
