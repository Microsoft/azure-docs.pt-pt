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
ms.openlocfilehash: df3941c895ce67862eb53b8e96bc7a6d53c1ed02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88799422"
---
# <a name="possible-fixes-for-the-you-cant-get-there-from-here-error-message"></a>Possíveis correções para a mensagem de erro "Não pode chegar lá daqui"

Ao aceder às aplicações ou serviços internos da sua organização, pode receber uma mensagem de erro que diz: **"Não pode chegar lá a partir daqui.** Esta mensagem significa que a sua organização colocou uma política que impede o seu dispositivo de aceder aos recursos da sua organização. Embora possa ter de contactar o seu Helpdesk para resolver este problema, aqui estão algumas coisas que pode tentar primeiro.

## <a name="make-sure-youre-using-a-supported-browser"></a>Certifique-se de que está a usar um browser suportado
Se obtém a mensagem **De que não pode chegar a partir daqui,** a dizer que está a tentar aceder aos sites da sua organização a partir de um navegador não suportado, verifique qual o navegador que está a executar.

![Mensagem de erro relacionada com o suporte do navegador](media/user-help-device-remediation/browser-version.png)

Para corrigir este problema, tem de instalar e executar um browser suportado, com base no seu sistema operativo. Se estiver a utilizar o Windows 10, os navegadores suportados incluem o Microsoft Edge, o Internet Explorer e o Google Chrome. Se estiver a utilizar um sistema operativo diferente, pode verificar a lista completa de [navegadores suportados.](../conditional-access/concept-conditional-access-conditions.md#supported-browsers)

## <a name="make-sure-youre-using-a-supported-operating-system"></a>Certifique-se de que está a utilizar um sistema operativo suportado
Certifique-se de que está a executar uma versão suportada do sistema operativo, incluindo:

- **Cliente do Windows.** Windows 7 ou mais tarde.

- **Servidor windows.** Windows Server 2008 R2 ou posterior.

- **macOS.** macOS X ou mais tarde

- **Android e iOS.** Versão mais recente dos sistemas operativos móveis Android e iOS

Para corrigir este problema, tem de instalar e executar um sistema operativo suportado.

## <a name="make-sure-your-device-is-joined-to-your-network"></a>Certifique-se de que o seu dispositivo está associado à sua rede
Se conseguir a mensagem **de You't get there from here** saying that your device is out-compliance with your organization's access policy, certifique-se de que se juntou ao seu dispositivo na rede da sua organização.

![Mensagem de erro relacionada com o facto de estar na sua rede](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>Para verificar se o seu dispositivo está associado à sua rede
1. Inscreva-se no Windows utilizando o seu trabalho ou conta escolar. Por exemplo, alain@contoso.com.

2. Conecte-se à rede da sua organização através de uma rede privada virtual (VPN) ou DirectAccess.

3. Depois de ligar, prima a tecla do logótipo do **Windows+L** para bloquear o seu dispositivo.

4. Desbloqueie o seu dispositivo utilizando o seu trabalho ou conta escolar e, em seguida, tente aceder novamente à aplicação ou serviço problemático.

    Se vir a mensagem de erro **de novo,** selecione o link **Mais detalhes** e contacte o seu Helpdesk com os detalhes.

### <a name="to-join-your-device-to-your-network"></a>Para juntar o seu dispositivo à sua rede
Se o seu dispositivo não estiver associado à rede da sua organização, pode fazer uma de duas coisas:

- **Junte-se ao seu dispositivo de trabalho.** Junte-se ao seu dispositivo windows 10 de propriedade laboral na rede da sua organização para que possa aceder a recursos potencialmente restritos. Para obter mais informações e instruções passo a passo, consulte [junte o seu dispositivo de trabalho à rede da sua organização.](user-help-join-device-on-network.md)

- **Registe o seu dispositivo pessoal para trabalhar.** Registe o seu dispositivo pessoal, normalmente um telefone ou tablet, na rede da sua organização. Depois de o seu dispositivo estar registado, pode aceder aos recursos restritos da sua organização. Para obter mais informações e instruções passo a passo, consulte [Registar o seu dispositivo pessoal na rede da sua organização.](user-help-register-device-on-network.md)

## <a name="next-steps"></a>Passos seguintes
- [O que é o portal MyApps?](./my-apps-portal-end-user-access.md)

- [Iniciar sessão com o telemóvel em vez da palavra-passe](user-help-auth-app-sign-in.md)