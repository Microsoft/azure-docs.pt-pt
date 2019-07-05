---
title: O que é um Azure AD híbrido associado a um dispositivo?
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
ms.openlocfilehash: 5c57180ba10322cb790c05b3f8f48043ca08b545
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462749"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Dispositivos híbridos associados ao Azure AD

Muitas organizações utilizam há mais de uma década a associação a um domínio no Active Directory no local para permitir:

- Aos departamentos de TI gerirem dispositivos detidos pela organização num local central.
- Aos utilizadores iniciarem sessão nos dispositivos deles com as contas escolares ou profissionais do Active Directory.

Geralmente, as organizações que têm uma infraestrutura no local utilizam métodos de imagens para aprovisionar os dispositivos e, muitas vezes utilizam o **System Center Configuration Manager (SCCM)** ou a **política de grupo (GP)** para os gerir.

Se o seu ambiente tiver uma infraestrutura do AD no local e também quiser tirar partido das capacidades que o Azure Active Directory oferece, pode implementar dispositivos híbridos associados ao Azure AD. Estes dispositivos são dispositivos que estão associados ao Active Directory no local e registados com o Azure Active Directory.

|   | Associação ao Azure AD híbrido |
| --- | --- |
| **Definição** | Associado ao local AD e o Azure AD que requerem uma conta organizacional para iniciar sessão no dispositivo |
| **Público-alvo principal** | Adequado para organizações híbridas com o existente no local a infraestrutura de AD |
|   | Aplicável a todos os utilizadores numa organização |
| **Propriedade do dispositivo** | Organização |
| **Sistemas operativos** | Windows 10, 8.1 e 7 |
|   | Windows Server 2008/R2, 2012/R2, 2016 e de 2019 |
| **Aprovisionamento** | Windows 10, Windows Server 2016/2019 |
|   | Associação a um domínio ao IT e autojoin por meio da configuração de AD Connect ou AD FS do Azure |
|   | Associação a um domínio Windows Autopilot e autojoin por meio da configuração do Azure AD Connect ou AD FS |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2 – requerem o MSI |
| **Início de sessão de dispositivo nas opções** | Contas organizacionais com: |
|   | Palavra-passe |
|   | Windows Hello para empresas para Win10 |
| **Gestão de Dispositivos** | Política de grupo |
|   | System Center Configuration Manager autónomo ou de cogestão com o Microsoft Intune |
| **Principais capacidades** | SSO para recursos na nuvem e no local |
|   | Acesso condicional através da associação a um domínio ou através do Intune se conjuntamente geridos |
|   | Self-service de reposição de palavra-passe e o Windows Hello PIN, repor no ecrã de bloqueio |
|   | Roaming de estado empresarial em dispositivos |

![Dispositivos híbridos associados ao Azure AD](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Cenários

Híbrido do Azure AD de utilizar dispositivos associados ao se:

- Tiver aplicações Win32 implementadas nesses dispositivos que se baseiam na autenticação de máquinas do Active Directory.
- Pretende continuar a utilizar a política de grupo para gerir a configuração do dispositivo.
- Pretende continuar a utilizar soluções de geração de imagens existentes para implementar e configurar os dispositivos.
- Tem de suportar 7 do Windows de nível inferior e 8.1 dispositivos, além do Windows 10

## <a name="next-steps"></a>Passos Seguintes

- [Planear a sua implementação de associação ao Azure AD híbrido](hybrid-azuread-join-plan.md)
- [Gerir identidades de dispositivos no portal do Azure](device-management-azure-portal.md)
- [Gerir dispositivos obsoletos no Azure AD](manage-stale-devices.md)
