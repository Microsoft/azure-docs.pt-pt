---
title: O que é a identidade do dispositivo no Diretório Ativo Azure?
description: Saiba como a gestão da identidade do dispositivo pode ajudá-lo a gerir os dispositivos que estão a aceder a recursos no seu ambiente.
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
ms.openlocfilehash: 594f0ed55b5ce5c31e87fd2011f3bc1522a12380
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78378278"
---
# <a name="what-is-a-device-identity"></a>O que é uma identidade de dispositivo?

Com a proliferação de dispositivos de todas as formas e tamanhos e o conceito Bring Your Own Device (BYOD), os profissionais de TI são confrontados com dois objetivos um pouco opostos:

- Permitir que os utilizadores finais sejam produtivos onde e quando
- Proteja os bens da organização

Para proteger estes ativos, o pessoal de TI precisa primeiro de gerir as identidades do dispositivo. Os colaboradores de TI podem basear-se na identidade do dispositivo com ferramentas como o Microsoft Intune para garantir que os padrões de segurança e conformidade sejam cumpridos. O Azure Ative Directory (Azure AD) permite um único sinal de entrada em dispositivos, apps e serviços de qualquer lugar através destes dispositivos.

- Os seus utilizadores têm acesso aos bens da sua organização de que necessitam. 
- O seu pessoal de TI obtém os controlos necessários para proteger a sua organização.

A gestão da identidade do dispositivo é a base para o [acesso condicional baseado no dispositivo.](../conditional-access/require-managed-devices.md) Com as políticas de Acesso Condicional baseadas em dispositivos, pode garantir que o acesso aos recursos no seu ambiente só é possível com dispositivos geridos.

## <a name="getting-devices-in-azure-ad"></a>Obtenção de dispositivos em Azure AD

Para obter um dispositivo em Azure AD, tem várias opções:

- **Azure AD registado**
   - Os dispositivos registados pela Azure AD são normalmente propriedade pessoal ou dispositivos móveis, e são assinados com uma conta pessoal da Microsoft ou outra conta local.
      - Windows 10
      - iOS
      - Android
      - MacOS
- **Azure AD juntou-se**
   - Os dispositivos que são Azure AD aderidos são propriedade de uma organização, e são assinados com uma conta Azure AD pertencente a essa organização. Só existem na nuvem.
      - Windows 10 
- **associado ao Azure AD Híbrido**
   - Os dispositivos híbridos Azure AD aqueridos são propriedade de uma organização, e são assinados com uma conta Azure AD pertencente a essa organização. Existem na nuvem e no local.
      - Windows 7, 8.1 ou 10
      - Windows Server 2008 ou mais recente

![Dispositivos exibidos na lâmina de dispositivos AD Azure](./media/overview/azure-active-directory-devices-all-devices.png)

## <a name="device-management"></a>Gestão de dispositivos

Os dispositivos em Azure AD podem ser geridos utilizando ferramentas de Gestão de Dispositivos Móveis (MDM) como microsoft Intune, Microsoft Endpoint Configuration Manager, Group Policy (ad.

## <a name="resource-access"></a>Acesso a recursos

Registar e juntar dispositivos ao Azure AD dá aos seus utilizadores um Sinal perfeito (SSO) para cloud recursos. Este processo também permite aos administradores a capacidade de aplicar políticas de Acesso Condicional aos recursos com base no dispositivo a que são acedidos. 

> [!NOTE]
> As políticas de acesso condicional baseadas no dispositivo requerem dispositivos híbridos Azure AD ou dispositivos de ad ad adesão ao Azure AD ou Azure AD.

Dispositivos que são Azure AD aderiu ou híbrido Azure AD juntou-se a benefício do SSO para os recursos no local da sua organização, bem como recursos na nuvem. Mais informações podem ser encontradas no artigo, [como o SSO para os recursos no local funciona em dispositivos ligados à Azure AD](azuread-join-sso.md).

## <a name="device-security"></a>Segurança do dispositivo

- **Os dispositivos registados pela Azure AD** utilizam uma conta gerida pelo utilizador final, esta conta é ou uma conta Microsoft ou outra credencial gerida localmente, segura com um ou mais dos seguintes.
   - Palavra-passe
   - PIN
   - Padrão
   - Windows Hello
- A **Azure AD aderiu ou híbrida Azure AD a dispositivos unidos** utilizam uma conta organizacional em Azure AD segura com um ou mais dos seguintes dispositivos.
   - Palavra-passe
   - Windows Hello para empresas

## <a name="provisioning"></a>Aprovisionamento

A entrada de dispositivos no Azure AD pode ser feita de forma auto-prestatória ou de um processo de provisionamento controlado por parte dos administradores.

## <a name="summary"></a>Resumo

Com a gestão de identidade do dispositivo em Azure AD, pode:

- Simplificar o processo de trazer e gerir dispositivos em Azure AD
- Proporcionar aos utilizadores um acesso fácil de utilizar aos recursos baseados na cloud da sua organização

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [dispositivos registados da Azure AD](concept-azure-ad-register.md)
- Saiba mais sobre [dispositivos azure AD](concept-azure-ad-join.md)
- Saiba mais sobre [dispositivos híbridos Azure AD](concept-azure-ad-join-hybrid.md)
- Para obter uma visão geral de como gerir as identidades do dispositivo no portal Azure, consulte a Gestão das [identidades do dispositivo utilizando o portal Azure](device-management-azure-portal.md).
- Para saber mais sobre o Acesso Condicional baseado no dispositivo, consulte as políticas de acesso condicional baseados no [dispositivo Configure Azure Ative Diretório](../conditional-access/require-managed-devices.md).
