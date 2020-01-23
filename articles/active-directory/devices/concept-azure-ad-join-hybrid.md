---
title: O que é um dispositivo ingressado no Azure AD híbrido?
description: Saiba como o gerenciamento de identidades de dispositivo pode ajudá-lo a gerenciar dispositivos que estão acessando recursos em seu ambiente.
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
ms.openlocfilehash: 15cdaba7d63d72aab25757e7ba6f5eadc48e026a
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76512254"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Dispositivos híbridos associados ao Azure AD

Muitas organizações utilizam há mais de uma década a associação a um domínio no Active Directory no local para permitir:

- Aos departamentos de TI gerirem dispositivos detidos pela organização num local central.
- Aos utilizadores iniciarem sessão nos dispositivos deles com as contas escolares ou profissionais do Active Directory.

Normalmente, as organizações com um espaço local dependem de métodos de geração de imagens para provisionar dispositivos e geralmente usam **Configuration Manager** ou **GP (política de grupo)** para gerenciá-los.

Se o seu ambiente tiver uma infraestrutura do AD no local e também quiser tirar partido das capacidades que o Azure Active Directory oferece, pode implementar dispositivos híbridos associados ao Azure AD. Esses dispositivos são dispositivos que ingressaram em seu Active Directory local e registrados com seu Azure Active Directory.

|   | Ingresso no Azure AD híbrido |
| --- | --- |
| **Definição** | Ingressado no AD local e no Azure AD exigindo a conta institucional para entrar no dispositivo |
| **Público-alvo principal** | Adequado para organizações híbridas com a infraestrutura existente do AD local |
|   | Aplicável a todos os usuários em uma organização |
| **Propriedade do dispositivo** | Organização |
| **Sistemas operacionais** | Windows 10, 8,1 e 7 |
|   | Windows Server 2008/R2, 2012/R2, 2016 e 2019 |
| **Aprovisionamento** | Windows 10, Windows Server 2016/2019 |
|   | Ingresso no domínio por ti e autojunção via configuração do Azure AD Connect ou do ADFS |
|   | Ingresso no domínio pelo AutoPilot do Windows e autojunção via configuração do Azure AD Connect ou do ADFS |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2-exigir MSI |
| **Opções de entrada do dispositivo** | Contas organizacionais usando: |
|   | Palavra-passe |
|   | Windows Hello para empresas para Win10 |
| **Gestão de Dispositivos** | Política de Grupo |
|   | Configuration Manager autônomo ou cogerenciamento com Microsoft Intune |
| **Principais recursos** | SSO para recursos de nuvem e locais |
|   | Acesso condicional por meio do ingresso no domínio ou por meio do Intune, se for cogerenciado |
|   | Redefinição de senha por autoatendimento e redefinição de PIN do Windows Hello na tela de bloqueio |
|   | Enterprise State Roaming entre dispositivos |

![Dispositivos híbridos associados ao Azure AD](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Cenários

Use dispositivos ingressados no Azure AD híbrido se:

- Tiver aplicações Win32 implementadas nesses dispositivos que se baseiam na autenticação de máquinas do Active Directory.
- Você deseja continuar a usar Política de Grupo para gerenciar a configuração do dispositivo.
- Você deseja continuar a usar soluções de geração de imagens existentes para implantar e configurar dispositivos.
- Você deve oferecer suporte a dispositivos Windows 7 e 8,1 de nível inferior além do Windows 10

## <a name="next-steps"></a>Passos seguintes

- [Planear a sua implementação de associação ao Azure AD híbrido](hybrid-azuread-join-plan.md)
- [Gerenciar identidades de dispositivo usando o portal do Azure](device-management-azure-portal.md)
- [Gerenciar dispositivos obsoletos no Azure AD](manage-stale-devices.md)
