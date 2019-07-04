---
title: O que são do Azure AD registado dispositivos?
description: Saiba como gestão de identidades do dispositivo pode ajudá-lo a gerir dispositivos que estão a aceder a recursos no seu ambiente.
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
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462762"
---
# <a name="azure-ad-registered-devices"></a>Dispositivos registados no Azure AD

O objetivo de dispositivos do Azure AD registado é fornecer aos utilizadores com suporte para o Bring Your Own Device (BYOD) ou a cenários de dispositivos móveis. Nestes cenários, um utilizador pode aceder a recursos do Azure Active Directory controlada da sua organização através de um dispositivo pessoal.

|   | Azure AD registado |
| --- | --- |
| **Definição** | Registado com o Azure AD sem a necessidade de uma conta institucional para iniciar sessão no dispositivo |
| **Público-alvo principal** | Aplicável a todos os utilizadores com os seguintes critérios: |
|   | Bring your own device (BYOD) |
|   | Dispositivos móveis |
| **Propriedade do dispositivo** | Utilizador ou a organização |
| **Sistemas operativos** | Windows 10, iOS, Android e MacOS |
| **Aprovisionamento** | Windows 10 – definições |
|   | iOS/Android – da empresa a aplicação do Portal ou o Microsoft Authenticator |
|   | MacOS – Portal da empresa |
| **Início de sessão de dispositivo nas opções** | Credenciais locais do utilizador final |
|   | Palavra-passe |
|   | Windows Hello |
|   | PIN |
|   | Biometria ou padrão para outros dispositivos |
| **Gestão de Dispositivos** | Gestão de dispositivos móveis (exemplo: Microsoft Intune) |
|   | Gestão de Aplicações Móveis |
| **Principais capacidades** | SSO para recursos na cloud |
|   | Acesso condicional quando inscritos no Intune |
|   | Acesso condicional através da política de proteção de aplicações |
|   | Permite o início de sessão do telefone com a aplicação Microsoft Authenticator |

![Dispositivos registados no Azure AD](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Dispositivos do Azure AD registado sessão iniciados com uma conta local, como uma conta Microsoft num dispositivo Windows 10, mas além disso, ter uma conta do Azure AD ligada para o acesso aos recursos organizacionais. Acesso a recursos na organização pode ser mais limitada com base nessa conta do Azure AD e as políticas de acesso condicional aplicadas à identidade de dispositivo.

Os administradores podem proteger e controlar ainda mais estes dispositivos do Azure AD registado com as ferramentas de gestão de dispositivos móveis (MDM), como o Microsoft Intune. MDM fornece um meio para impor configurações necessárias de organização como exigir armazenamento sejam encriptados, a complexidade de palavra-passe e mantido atualizado de software de segurança. 

Registo do Azure AD pode ser realizado quando acedem a uma aplicação de trabalho para a primeira vez ou manualmente utilizando o menu de definições do Windows 10. 

## <a name="scenarios"></a>Cenários

Um utilizador na sua organização pretende aceder a ferramentas de e-mail, relatórios de tempo-off e inscrição de benefícios de seus PCs domésticos. A sua organização tem essas ferramentas por trás de uma política de acesso condicional que requer acesso a partir de um dispositivo em conformidade do Intune. O utilizador adiciona a conta de organização e registra seus PCs domésticos com o Azure AD e as políticas necessárias do Intune são impostas dar ao usuário acesso aos respetivos recursos.

Outro utilizador quer acedam ao respetivo e-mail organizacional no seu telemóvel Android pessoa que foi desbloqueado por Root. A sua empresa requer um dispositivo em conformidade e criou uma política de conformidade do Intune para bloquear todos os dispositivos com root. O funcionário é interrompido de aceder a recursos organizacionais neste dispositivo.

## <a name="next-steps"></a>Passos Seguintes

- [Gerir identidades de dispositivos no portal do Azure](device-management-azure-portal.md)
- [Gerir dispositivos obsoletos no Azure AD](manage-stale-devices.md)
