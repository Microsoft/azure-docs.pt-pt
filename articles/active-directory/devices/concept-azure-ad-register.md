---
title: O que são dispositivos registados Azure AD?
description: Saiba como a gestão da identidade do dispositivo pode ajudá-lo a gerir dispositivos que estão a aceder a recursos no seu ambiente.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1ce0723c0064cee711e851d16f4080a722e7539
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85554863"
---
# <a name="azure-ad-registered-devices"></a>Dispositivos registados no Azure AD

O objetivo dos dispositivos registados Azure AD é fornecer aos seus utilizadores suporte para os cenários do Bring Your Own Device (BYOD) ou dispositivos móveis. Nestes cenários, um utilizador pode aceder aos recursos controlados pelo Azure Ative Directory da sua organização utilizando um dispositivo pessoal.

| Azure AD Registado | Descrição |
| --- | --- |
| **Definição** | Registado em Azure AD sem necessidade de conta organizacional para iniciar seduca no dispositivo |
| **Público primário** | Aplicável a todos os utilizadores com os seguintes critérios: |
|   | Bring your own device (BYOD) |
|   | Dispositivos móveis |
| **Propriedade dos dispositivos** | Utilizador ou Organização |
| **Sistemas Operativos** | Windows 10, iOS, Android e MacOS |
| **Aprovisionamento** | Windows 10 – Definições |
|   | iOS/Android – Portal da Empresa ou app Microsoft Authenticator |
|   | MacOS – Portal da Empresa |
| **Sinal do dispositivo em opções** | Credenciais locais de utilizador final |
|   | Palavra-passe |
|   | Windows Hello |
|   | PIN |
|   | Biometria ou Padrão para outros dispositivos |
| **Gestão de dispositivos** | Gestão de Dispositivos Móveis (exemplo: Microsoft Intune) |
|   | Gestão de Aplicações Móveis |
| **Principais capacidades** | SSO para cloud resources |
|   | Acesso Condicional quando inscrito no Intune |
|   | Acesso Condicional através da política de proteção de aplicações |
|   | Ativa o sôs-in do Telefone com a aplicação Microsoft Authenticator |

![Dispositivos registados no Azure AD](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Os dispositivos registados Azure AD estão registados para usar uma conta local como uma conta Microsoft num dispositivo Windows 10, mas também têm uma conta AZure AD anexada para acesso a recursos organizacionais. O acesso aos recursos na organização pode ser ainda limitado com base na conta AD Azure e nas políticas de Acesso Condicional aplicadas à identidade do dispositivo.

Os administradores podem proteger e controlar ainda mais estes dispositivos registados Azure AD utilizando ferramentas de Gestão de Dispositivos Móveis (MDM) como o Microsoft Intune. O MDM fornece um meio para impor configurações exigidas pela organização, como exigir que o armazenamento seja encriptado, complexidade de palavras-passe e software de segurança mantido atualizado. 

O registo AD AZure pode ser realizado ao aceder a uma aplicação de trabalho pela primeira vez ou manualmente utilizando o menu Definições do Windows 10. 

## <a name="scenarios"></a>Cenários

Um utilizador da sua organização quer aceder a ferramentas para e-mail, reportar tempo-off e beneficiar a inscrição no seu PC doméstico. A sua organização tem estas ferramentas por trás de uma política de Acesso Condicional que requer acesso a partir de um dispositivo compatível com o Intune. O utilizador adiciona a sua conta de organização e regista o seu PC doméstico com Azure AD e as políticas Intune necessárias são aplicadas dando ao utilizador acesso aos seus recursos.

Outro utilizador quer aceder ao seu email organizacional no seu telemóvel Android pessoal que tem sido enraizado. A sua empresa necessita de um dispositivo compatível e criou uma política de conformidade intune para bloquear quaisquer dispositivos enraizados. O funcionário está impedido de aceder a recursos organizacionais neste dispositivo.

## <a name="next-steps"></a>Próximos passos

- [Gerir identidades do dispositivo utilizando o portal Azure](device-management-azure-portal.md)
- [Gerir dispositivos em Azure AD](manage-stale-devices.md)
