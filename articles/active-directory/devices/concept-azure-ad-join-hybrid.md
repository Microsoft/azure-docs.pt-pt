---
title: O que é um dispositivo híbrido Azure AD?
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
ms.openlocfilehash: 259a1324c412dad40d32a8b8e026d84e6f5aa066
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85554923"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Dispositivos híbridos associados ao Azure AD

Muitas organizações utilizam há mais de uma década a associação a um domínio no Active Directory no local para permitir:

- Aos departamentos de TI gerirem dispositivos detidos pela organização num local central.
- Aos utilizadores iniciarem sessão nos dispositivos deles com as contas escolares ou profissionais do Active Directory.

Normalmente, as organizações com uma pegada no local dependem de métodos de imagem para dispositivos de fornecimento, e muitas vezes usam **o Gestor de Configuração** ou **a política de grupo (GP)** para os gerir.

Se o seu ambiente tiver uma infraestrutura do AD no local e também quiser tirar partido das capacidades que o Azure Active Directory oferece, pode implementar dispositivos híbridos associados ao Azure AD. Estes dispositivos são dispositivos que se juntam ao seu Ative Directory no local e registados no seu Diretório Ativo Azure.

| Híbrido Azure AD Join | Description |
| --- | --- |
| **Definição** | Juntou-se aos locais AD e AZure AD exigindo que a conta organizacional se inscreva no dispositivo |
| **Público primário** | Adequado para organizações híbridas com infraestruturas AD existentes no local |
|   | Aplicável a todos os utilizadores de uma organização |
| **Propriedade dos dispositivos** | Organização |
| **Sistemas Operativos** | Windows 10, 8.1 e 7 |
|   | Windows Server 2008/R2, 2012/R2, 2016 e 2019 |
| **Aprovisionamento** | Windows 10, Windows Server 2016/2019 |
|   | O domínio junta-se a TI e autojoderin via Azure AD Connect ou ADFS config |
|   | Unição de domínios por Windows Autopilot e autojoderin via Azure AD Connect ou ADFS config |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2 - Requerem MSI |
| **Sinal do dispositivo em opções** | Contas organizacionais utilizando: |
|   | Palavra-passe |
|   | Windows Hello for Business for Win10 |
| **Gestão de dispositivos** | Política de Grupo |
|   | Gestor de Configuração autónomo ou cogestão com o Microsoft Intune |
| **Principais capacidades** | SSO para recursos de nuvem e no local |
|   | Acesso Condicional através do Domínio aderir ou através do Intune se cogerido |
|   | Redefinição de password de autosserviço e reset do Windows Hello PIN no ecrã de bloqueio |
|   | Roaming do Estado da Empresa através de dispositivos |

![Dispositivos híbridos associados ao Azure AD](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Cenários

Utilize dispositivos híbridos AD AZure se:

- Tiver aplicações Win32 implementadas nesses dispositivos que se baseiam na autenticação de máquinas do Active Directory.
- Pretende continuar a utilizar a Política de Grupo para gerir a configuração do dispositivo.
- Pretende continuar a utilizar as soluções de imagem existentes para implantar e configurar dispositivos.
- Tem de suportar dispositivos Windows 7 e 8.1 de nível inferior, para além do Windows 10.

## <a name="next-steps"></a>Passos seguintes

- [Planear a sua implementação de associação ao Azure AD híbrido](hybrid-azuread-join-plan.md)
- [Gerir identidades de dispositivos com o portal do Azure](device-management-azure-portal.md)
- [Gerir dispositivos obsoletos no AAD](manage-stale-devices.md)
