---
title: Resolver problemas relacionados com o erro "não pode aceder aí a partir daqui" - Azure Active Directory | Documentos da Microsoft
description: Resolver problemas relacionados com os possíveis motivos pelos quais esteja tirando a mensagem de erro "não pode aceder aí a partir daqui".
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
ms.openlocfilehash: a317680a39d4594aacdf84ccdf963bb84bfbf07b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57452042"
---
# <a name="potential-reasons-for-the-you-cant-get-there-from-here-error-message"></a>Razões possíveis para a mensagem de erro "não pode aceder aí a partir daqui"
Ao aceder a aplicações web internas ou serviços da sua organização, poderá receber uma mensagem de erro que diz **não pode aceder aí a partir daqui**. Esta mensagem indica que sua organização colocou uma política que está a impedir o seu dispositivo de acesso a recursos da sua organização. Enquanto acabar, poderá ter de contactar o suporte técnico para corrigir este problema, aqui estão algumas coisas que pode tentar primeiro.

## <a name="make-sure-youre-using-a-supported-browser"></a>Certifique-se de que está a utilizar um browser suportado
Se obtiver a **não pode aceder aí a partir daqui** mensagem a indicar que está a tentar aceder a sites da sua organização a partir de um browser não suportado, verifique qual navegador o que está a executar.

![Mensagem de erro relacionados com o suporte de browser](media/user-help-device-remediation/browser-version.png)

Para corrigir este problema, tem de instalar e executar um browser suportado, com base no seu sistema operativo. Se estiver a utilizar o Windows 10, browsers suportados incluem o Microsoft Edge, Internet Explorer e Google Chrome. Se estiver a utilizar um sistema operacional diferente, pode verificar a lista completa de [browsers suportados](../conditional-access/technical-reference.md#supported-browsers).

## <a name="make-sure-youre-using-a-supported-operating-system"></a>Certifique-se de que está a utilizar um sistema operativo suportado
Certifique-se de que está a executar uma versão suportada do sistema operacional, incluindo:

- **Windows Client.** Windows 7 ou posterior.

- **Windows Server.** Windows Server 2008 R2 ou posterior.

- **macOS.** macOS X ou posterior

- **Android e iOS.** Versão mais recente de sistemas operativos móveis Android e iOS

Para corrigir este problema, tem de instalar e executar um sistema operativo suportado.

## <a name="make-sure-your-device-is-joined-to-your-network"></a>Certifique-se de que o seu dispositivo está associado à sua rede
Se obtiver a **não pode aceder aí a partir daqui** mensagem a indicar que o dispositivo está fora de conformidade com a política de acesso da sua organização, certifique-se de se associar o seu dispositivo à rede da sua organização.

![Mensagem de erro relacionados com esteja na sua rede](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>Para verificar se o seu dispositivo está associado à sua rede
1. Inicie sessão no Windows com a sua conta escolar ou profissional. Por exemplo, alain@contoso.com.

2. Ligar à rede da sua organização através de uma rede privada virtual (VPN) ou DirectAccess.

3. Quando estiver ligado, prima a **logótipo do Windows principais + L** bloquear o dispositivo.

4. Desbloqueie o dispositivo com a sua ou conta escolar e, em seguida, tente aceder à aplicação problemática ou serviço novamente.

    Se vir a **não pode aceder aí a partir daqui** mensagem de erro mais uma vez, selecione a **obter mais detalhes** associar e, em seguida, contacte o suporte técnico com os detalhes.

### <a name="to-join-your-device-to-your-network"></a>Para associar o seu dispositivo à sua rede
Se o dispositivo não está associado à rede da sua organização, pode fazer uma das duas coisas:

- **Associe o seu dispositivo de trabalho.** Associe o seu dispositivo de Windows 10 pertencentes à trabalho à rede da sua organização para que possa aceder a recursos potencialmente restritos. Para obter mais informações e instruções passo a passo, consulte [associar o seu dispositivo de trabalho para a rede da sua organização](user-help-join-device-on-network.md).

- **Registe o seu dispositivo pessoal para o trabalho.** Registe o seu dispositivo pessoal, normalmente, um telefone ou tablet, na rede da sua organização. Depois do dispositivo estiver registado, pode aceder a recursos restritos da sua organização. Para obter mais informações e instruções passo a passo, consulte [registar o seu dispositivo pessoal na rede da sua organização](user-help-register-device-on-network.md).

## <a name="next-steps"></a>Passos Seguintes
- [O que é o portal MyApps?](active-directory-saas-access-panel-introduction.md)

- [Iniciar sessão com o telemóvel em vez da palavra-passe](user-help-auth-app-sign-in.md)
