---
title: O que é a identidade do dispositivo no Azure Active Directory?
description: Saiba como o gerenciamento de identidades de dispositivo pode ajudá-lo a gerenciar os dispositivos que estão acessando recursos em seu ambiente.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d593e5f27dbcd676e2b42975251778a6c48a7d79
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76511880"
---
# <a name="what-is-a-device-identity"></a>O que é uma identidade de dispositivo?

Com a proliferação de dispositivos de todas as formas e tamanhos e o conceito BYOD (Traga seu próprio dispositivo), os profissionais de ti enfrentam duas metas um tanto opostos:

- Permitir que os usuários finais sejam produtivos sempre que e quando
- Proteger os ativos da organização

Para proteger esses ativos, a equipe de ti precisa primeiro gerenciar as identidades do dispositivo. A equipe de ti pode se basear na identidade do dispositivo com ferramentas como Microsoft Intune para garantir que os padrões de segurança e conformidade sejam atendidos. O Azure Active Directory (AD do Azure) habilita o logon único para dispositivos, aplicativos e serviços de qualquer lugar por meio desses dispositivos.

- Os usuários obtêm acesso aos ativos de sua organização de que precisam. 
- Sua equipe de ti Obtém os controles de que precisam para proteger sua organização.

O gerenciamento de identidade do dispositivo é a base para o [acesso condicional baseado em dispositivo](../conditional-access/require-managed-devices.md). Com as políticas de acesso condicional com base no dispositivo, você pode garantir que o acesso aos recursos em seu ambiente só seja possível com dispositivos gerenciados.

## <a name="getting-devices-in-azure-ad"></a>Obtendo dispositivos no Azure AD

Para obter um dispositivo no Azure AD, você tem várias opções:

- **Azure AD registrado**
   - Dispositivos que são registrados no Azure AD normalmente são dispositivos pessoais ou de propriedade pessoal e são conectados a um conta Microsoft pessoal ou a outra conta local.
      - Windows 10
      - iOS
      - Android
      - MacOS
- **Ingressado no Azure AD**
   - Os dispositivos que são ingressados no Azure AD são de propriedade de uma organização e são conectados ao com uma conta do Azure AD que pertence a essa organização. Eles existem apenas na nuvem.
      - Windows 10 
- **associado ao Azure AD Híbrido**
   - Os dispositivos que são ingressados no Azure AD híbrido são de propriedade de uma organização e são conectados ao com uma conta do Azure AD que pertence a essa organização. Eles existem na nuvem e no local.
      - Windows 7, 8,1 ou 10
      - Windows Server 2008 ou mais recente

![Dispositivos exibidos na folha dispositivos do Azure AD](./media/overview/azure-active-directory-devices-all-devices.png)

## <a name="device-management"></a>Gestão de dispositivos

Os dispositivos no Azure AD podem ser gerenciados usando ferramentas de MDM (gerenciamento de dispositivo móvel), como Microsoft Intune, Microsoft Endpoint Configuration Manager, Política de Grupo (ingresso no Azure AD híbrido), ferramentas de MAM (gerenciamento de aplicativo móvel) ou outras ferramentas de terceiros.

## <a name="resource-access"></a>Acesso a recursos

Registrar e ingressar dispositivos no Azure AD oferece aos usuários o SSO (logon contínuo) para recursos de nuvem. Esse processo também permite aos administradores a capacidade de aplicar políticas de acesso condicional a recursos com base no dispositivo do qual são acessadas. 

> [!NOTE]
> As políticas de acesso condicional com base no dispositivo exigem dispositivos ingressados no Azure ad híbridos ou dispositivos registrados no Azure ad ou em conformidade do Azure AD.

Os dispositivos que são ingressados no Azure AD ou híbridos do Azure AD podem se beneficiar do SSO para os recursos locais de sua organização, bem como recursos de nuvem. Mais informações podem ser encontradas no artigo [como o SSO para recursos locais funciona em dispositivos ingressados no Azure ad](azuread-join-sso.md).

## <a name="device-security"></a>Segurança dos dispositivos

- Os **dispositivos registrados no Azure ad** utilizam uma conta gerenciada pelo usuário final, essa conta é uma conta Microsoft ou outra credencial gerenciada localmente protegida por uma ou mais das seguintes opções.
   - Palavra-passe
   - AFIXAR
   - Padrão
   - Windows Hello
- Os dispositivos ingressados no Azure ad **ingressados ou** não são usados por uma conta institucional no Azure ad protegidos com um ou mais dos itens a seguir.
   - Palavra-passe
   - Windows Hello para Empresas

## <a name="provisioning"></a>Aprovisionamento

A obtenção de dispositivos no Azure AD pode ser feita em uma maneira de autoatendimento ou um processo de provisionamento controlado por administradores.

## <a name="summary"></a>Resumo

Com o gerenciamento de identidade do dispositivo no Azure AD, você pode:

- Simplificar o processo de trazer e gerenciar dispositivos no Azure AD
- Proporcionar aos utilizadores um acesso fácil de utilizar aos recursos baseados na cloud da sua organização

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [dispositivos registrados no Azure ad](concept-azure-ad-register.md)
- Saiba mais sobre os [dispositivos adicionados ao Azure ad](concept-azure-ad-join.md)
- Saiba mais sobre os [dispositivos ingressados no Azure ad híbrido](concept-azure-ad-join-hybrid.md)
- Para obter uma visão geral de como gerenciar identidades de dispositivo no portal do Azure, consulte [Gerenciando identidades de dispositivo usando o portal do Azure](device-management-azure-portal.md).
- Para saber mais sobre o acesso condicional baseado em dispositivo, confira [configurar Azure Active Directory políticas de acesso condicional com base no dispositivo](../conditional-access/require-managed-devices.md).
