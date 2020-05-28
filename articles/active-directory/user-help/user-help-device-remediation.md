---
title: Correções para o erro "Não pode chegar lá daqui" - Azure AD
description: Encontre potenciais correções para o porquê de receber a mensagem de erro "Não pode chegar lá daqui".
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 10/10/2018
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: b76e84f6ae2449347604ab823fde5b74d5c68f07
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83741000"
---
# <a name="possible-fixes-for-the-you-cant-get-there-from-here-error-message"></a>Possíveis correções para a mensagem de erro "Não pode chegar lá daqui"

Ao aceder às aplicações ou serviços internos da sua organização, poderá receber uma mensagem de erro que diz: **"Não pode chegar lá a partir daqui**. Esta mensagem significa que a sua organização pôs em prática uma política que impede o seu dispositivo de aceder aos recursos da sua organização. Embora possa acabar por ter de contactar o seu Helpdesk para resolver este problema, aqui estão algumas coisas que pode tentar primeiro.

## <a name="make-sure-youre-using-a-supported-browser"></a>Certifique-se de que está a usar um navegador suportado
Se obtém a mensagem **Não pode chegar a partir daqui** dizendo que está a tentar aceder aos sites da sua organização a partir de um navegador não suportado, verifique qual o navegador que está a executar.

![Mensagem de erro relacionada com suporte ao navegador](media/user-help-device-remediation/browser-version.png)

Para corrigir este problema, tem de instalar e executar um navegador suportado, com base no seu sistema operativo. Se estiver a utilizar o Windows 10, os navegadores suportados incluem O Microsoft Edge, O Internet Explorer e o Google Chrome. Se estiver a utilizar um sistema operativo diferente, pode verificar a lista completa de [navegadores suportados.](../conditional-access/concept-conditional-access-conditions.md#supported-browsers)

## <a name="make-sure-youre-using-a-supported-operating-system"></a>Certifique-se de que está a utilizar um sistema operativo suportado
Certifique-se de que está a executar uma versão suportada do sistema operativo, incluindo:

- **Cliente windows.** Windows 7 ou mais tarde.

- **Servidor windows.** Windows Server 2008 R2 ou mais tarde.

- **macOS.** macOS X ou mais tarde

- **Android e iOS.** Versão mais recente dos sistemas operativos móveis Android e iOS

Para corrigir este problema, tem de instalar e executar um sistema operativo suportado.

## <a name="make-sure-your-device-is-joined-to-your-network"></a>Certifique-se de que o seu dispositivo está unido à sua rede
Se receber a mensagem Não **pode chegar a partir daqui** dizendo que o seu dispositivo está desconformidade com a política de acesso da sua organização, certifique-se de que se juntou ao seu dispositivo na rede da sua organização.

![Mensagem de erro relacionada com se está na sua rede](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>Para verificar se o seu dispositivo está unido à sua rede
1. Inscreva-se no Windows utilizando o seu trabalho ou conta escolar. Por exemplo, alain@contoso.com.

2. Ligue-se à rede da sua organização através de uma rede privada virtual (VPN) ou DirectAccess.

3. Depois de conectado, prima a **tecla de logótipo do Windows+L** para bloquear o seu dispositivo.

4. Desbloqueie o seu dispositivo utilizando o seu trabalho ou conta escolar e tente aceder novamente à aplicação ou serviço problemático.

    Se vir a Mensagem de erro não pode chegar lá a **partir daqui,** selecione o link **Mais detalhes** e, em seguida, contacte o seu Helpdesk com os detalhes.

### <a name="to-join-your-device-to-your-network"></a>Para se juntar ao seu dispositivo na sua rede
Se o seu dispositivo não estiver unido à rede da sua organização, pode fazer uma de duas coisas:

- **Junte-se ao seu dispositivo de trabalho.** Junte-se ao seu dispositivo Windows 10 de propriedade própria na rede da sua organização para que possa aceder a recursos potencialmente restritos. Para obter mais informações e instruções passo a passo, consulte Junte-se ao seu dispositivo de [trabalho na rede da sua organização](user-help-join-device-on-network.md).

- **Registe o seu dispositivo pessoal para o trabalho.** Registe o seu dispositivo pessoal, normalmente um telefone ou tablet, na rede da sua organização. Depois de registado o seu dispositivo, pode aceder aos recursos restritos da sua organização. Para obter mais informações e instruções passo a passo, consulte [Registar o seu dispositivo pessoal na rede da sua organização](user-help-register-device-on-network.md).

## <a name="next-steps"></a>Próximos passos
- [O que é o portal MyApps?](active-directory-saas-access-panel-introduction.md)

- [Iniciar sessão com o telemóvel em vez da palavra-passe](user-help-auth-app-sign-in.md)
