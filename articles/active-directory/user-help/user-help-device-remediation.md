---
title: Correções para o erro "você não pode chegar lá daqui" – Azure AD
description: Encontre possíveis correções por que você está recebendo a mensagem de erro "você não pode chegar lá daqui".
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lizross
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d6385343ed26e5043e1491529fd2454caff4d04
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74028565"
---
# <a name="possible-fixes-for-the-you-cant-get-there-from-here-error-message"></a>Possíveis correções para a mensagem de erro "você não pode chegar lá daqui"

Ao acessar os aplicativos Web ou serviços internos da sua organização, você pode receber uma mensagem de erro dizendo que **não pode chegar lá daqui**. Essa mensagem significa que sua organização colocou uma política em vigor que impede que o dispositivo acesse os recursos da sua organização. Embora você possa acabar precisando entrar em contato com a assistência técnica para corrigir esse problema, aqui estão algumas coisas que você pode tentar primeiro.

## <a name="make-sure-youre-using-a-supported-browser"></a>Verifique se você está usando um navegador com suporte
Se você receber a mensagem **você não pode chegar lá daqui** , dizendo que está tentando acessar os sites de sua organização em um navegador sem suporte, verifique qual navegador você está executando.

![Mensagem de erro relacionada ao suporte do navegador](media/user-help-device-remediation/browser-version.png)

Para corrigir esse problema, você deve instalar e executar um navegador com suporte, com base no seu sistema operacional. Se você estiver usando o Windows 10, os navegadores com suporte incluem Microsoft Edge, Internet Explorer e Google Chrome. Se você estiver usando um sistema operacional diferente, poderá verificar a lista completa de [navegadores com suporte](../conditional-access/technical-reference.md#supported-browsers).

## <a name="make-sure-youre-using-a-supported-operating-system"></a>Verifique se você está usando um sistema operacional com suporte
Verifique se você está executando uma versão com suporte do sistema operacional, incluindo:

- **Cliente do Windows.** Windows 7 ou posterior.

- **Windows Server.** Windows Server 2008 R2 ou posterior.

- **macOS.** macOS X ou posterior

- **Android e iOS.** Versão mais recente dos sistemas operacionais móveis Android e iOS

Para corrigir esse problema, você deve instalar e executar um sistema operacional com suporte.

## <a name="make-sure-your-device-is-joined-to-your-network"></a>Verifique se o dispositivo ingressou em sua rede
Se você receber a mensagem **você não pode chegar lá daqui** , informando que o dispositivo está fora de conformidade com a política de acesso da sua organização, verifique se você ingressou no dispositivo na rede da sua organização.

![Mensagem de erro relacionada se você está em sua rede](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>Para verificar se o dispositivo está ingressado em sua rede
1. Entre no Windows usando sua conta corporativa ou de estudante. Por exemplo, alain@contoso.com.

2. Conecte-se à rede da sua organização por meio de uma VPN (rede virtual privada) ou DirectAccess.

3. Depois de se conectar, pressione a **tecla com o logotipo do Windows + L** para bloquear seu dispositivo.

4. Desbloqueie seu dispositivo usando sua conta corporativa ou de estudante e tente acessar o aplicativo ou serviço problemático novamente.

    Se você vir a mensagem de erro **não é possível obter aqui aqui** , selecione o link **mais detalhes** e entre em contato com a assistência técnica com os detalhes.

### <a name="to-join-your-device-to-your-network"></a>Para unir seu dispositivo à sua rede
Se o dispositivo não tiver ingressado na rede da sua organização, você poderá executar uma das duas ações a seguir:

- **Ingresse no seu dispositivo de trabalho.** Ingresse seu dispositivo Windows 10 de Propriedade do seu trabalho na rede da sua organização para que você possa acessar recursos potencialmente restritos. Para obter mais informações e instruções passo a passo, consulte [ingressar seu dispositivo de trabalho na rede da sua organização](user-help-join-device-on-network.md).

- **Registre seu dispositivo pessoal para o trabalho.** Registre seu dispositivo pessoal, normalmente um telefone ou Tablet, na rede da sua organização. Depois que o dispositivo é registrado, ele pode acessar os recursos restritos da sua organização. Para obter mais informações e instruções passo a passo, consulte [registrar seu dispositivo pessoal na rede da sua organização](user-help-register-device-on-network.md).

## <a name="next-steps"></a>Passos seguintes
- [O que é o portal do myapps?](active-directory-saas-access-panel-introduction.md)

- [Iniciar sessão com o telemóvel em vez da palavra-passe](user-help-auth-app-sign-in.md)
