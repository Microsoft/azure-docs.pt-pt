---
title: O que são dispositivos registados pela Azure AD?
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
ms.openlocfilehash: 7e2a8cad7cd4410a95a6ebd60ada22de456737bf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67462762"
---
# <a name="azure-ad-registered-devices"></a>Dispositivos registados no Azure AD

O objetivo dos dispositivos registados pela Azure AD é fornecer aos seus utilizadores suporte para os cenários bring your own device (BYOD) ou mobile device. Nestes cenários, um utilizador pode aceder aos recursos controlados pelo Azure Ative Directory da sua organização utilizando um dispositivo pessoal.

|   | Azure AD Registado |
| --- | --- |
| **Definição** | Registrado na Azure AD sem exigir que conta organizacional assine o dispositivo |
| **Audiência primária** | Aplicável a todos os utilizadores com os seguintes critérios: |
|   | Bring your own device (BYOD) |
|   | Dispositivos móveis |
| **Propriedade dos dispositivos** | Utilizador ou Organização |
| **Sistemas Operativos** | Windows 10, iOS, Android e MacOS |
| **Aprovisionamento** | Windows 10 – Definições |
|   | iOS/Android – Portal da Empresa ou aplicação do Autenticador Microsoft |
|   | MacOS – Portal da Empresa |
| **Sinal de dispositivo em opções** | Credenciais locais de utilizador final |
|   | Palavra-passe |
|   | Windows Hello |
|   | PIN |
|   | Biometria ou Padrão para outros dispositivos |
| **Gestão de dispositivos** | Gestão de Dispositivos Móveis (exemplo: Microsoft Intune) |
|   | Gestão de Aplicações Móveis |
| **Principais capacidades** | SSO para ensombrar recursos |
|   | Acesso Condicional quando matriculado em Intune |
|   | Acesso Condicional através da política de proteção de aplicações |
|   | Ativa o registo do Telefone com a aplicação Microsoft Authenticator |

![Dispositivos registados no Azure AD](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Os dispositivos registados pela Azure AD são assinados para usar uma conta local como uma conta Microsoft num dispositivo Windows 10, mas além disso têm uma conta Azure AD anexada para acesso a recursos organizacionais. O acesso aos recursos na organização pode ser ainda limitado com base nessa conta Azure AD e nas políticas de Acesso Condicional aplicadas à identidade do dispositivo.

Os administradores podem proteger e controlar ainda mais estes dispositivos registados pela Azure AD utilizando ferramentas de Gestão de Dispositivos Móveis (MDM) como o Microsoft Intune. O MDM fornece um meio para impor configurações exigidas pela organização, como exigir que o armazenamento seja encriptado, complexidade de palavras-passe e software de segurança mantido atualizado. 

O registo da AD Azure pode ser realizado ao aceder a uma aplicação de trabalho pela primeira vez ou utilizar manualmente o menu definições do Windows 10. 

## <a name="scenarios"></a>Cenários

Um utilizador da sua organização quer aceder a ferramentas para e-mail, reportar tempo de folga e beneficiar a inscrição do seu PC doméstico. A sua organização tem estas ferramentas por trás de uma política de Acesso Condicional que requer acesso a partir de um dispositivo compatível com intune. O utilizador adiciona a sua conta de organização e regista o seu PC doméstico com a Azure AD e as políticas intune exigidas são aplicadas dando ao utilizador acesso aos seus recursos.

Outro utilizador quer aceder ao seu email organizacional no seu telemóvel Android pessoal que está enraizado. A sua empresa necessita de um dispositivo compatível e criou uma política de conformidade Intune para bloquear quaisquer dispositivos enraizados. O funcionário está impedido de aceder a recursos organizacionais neste dispositivo.

## <a name="next-steps"></a>Passos seguintes

- [Gerir identidades do dispositivo utilizando o portal Azure](device-management-azure-portal.md)
- [Gerir dispositivos velhos em Azure AD](manage-stale-devices.md)
