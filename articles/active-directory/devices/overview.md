---
title: O que é a identidade de dispositivo no Azure Active Directory?
description: Saiba como gestão de identidades do dispositivo pode ajudá-lo a gerir os dispositivos que estão a aceder a recursos no seu ambiente.
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
ms.openlocfilehash: 5d38ca8bdf93ff201b3f5842f4cb0e8409dcd0c3
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481662"
---
# <a name="what-is-a-device-identity"></a>O que é uma identidade de dispositivo?

Com a proliferação de dispositivos de todas as formas e tamanhos e o conceito de Bring Your Own Device (BYOD), os profissionais de TI enfrentam com dois objetivos de certa forma opostos:

- Permitir que os utilizadores finais sejam produtivos, onde quer e sempre que
- Proteger os ativos da organização

Para proteger estes recursos, a equipe de TI precisa primeiro gerir as identidades de dispositivo. A equipe de TI pode se basear a identidade de dispositivo com ferramentas como o Microsoft Intune para garantir o cumprimento dos padrões de segurança e conformidade. Azure Active Directory (Azure AD) permite início de sessão único em dispositivos, aplicações e serviços a partir de qualquer lugar através destes dispositivos.

- Os utilizadores obtêm acesso a recursos da sua organização que necessitam. 
- Sua equipe de TI obter os controles que precisam para proteger a sua organização.

Gestão de identidades de dispositivo é a base para [acesso condicional com base no dispositivo](../conditional-access/require-managed-devices.md). Com as políticas de acesso condicional com base no dispositivo, pode certificar-se de que o acesso a recursos no seu ambiente apenas é possível com os dispositivos geridos.

## <a name="getting-devices-in-azure-ad"></a>Introdução de dispositivos no Azure AD

Para obter um dispositivo no Azure AD, tem várias opções:

- **Azure AD registado**
   - Dispositivos que são do Azure AD registado pessoalmente, normalmente, são dispositivos móveis ou de propriedade e iniciado sessão com uma conta Microsoft pessoal ou de outra conta local.
      - Windows 10
      - iOS
      - Android
      - MacOS
- **Associados ao Azure AD**
   - Dispositivos que estão associados ao Azure AD são propriedade de uma organização e a sessão iniciados com uma conta do Azure AD que pertencem a essa organização. Elas existem apenas na cloud.
      - Windows 10 
- **associado ao Azure AD Híbrido**
   - Dispositivos que estão associados ao Azure AD híbrido são propriedade de uma organização e a sessão iniciados com uma conta do Azure AD que pertencem a essa organização. Eles existem na cloud e 
      - Windows 7, 8.1 ou 10
      - Windows Server 2008 ou mais recente

![Dispositivos apresentados no painel de dispositivos do Azure AD](./media/overview/azure-ad-devices-all-devices-overview.png)

## <a name="device-management"></a>Gestão de dispositivos

Dispositivos no Azure AD podem ser geridos com ferramentas de gestão de dispositivos móveis (MDM), como o Microsoft Intune, System Center Configuration Manager, a política de grupo (associação do híbrida do Azure AD), ferramentas de gestão de aplicações móveis (MAM) ou outras ferramentas de terceiros.

## <a name="resource-access"></a>Acesso a recursos

Registar-se e aderir ao fornecem aos usuários logon simplificado (SSO) para a cloud os recursos e os administradores a capacidade de aplicar políticas de acesso condicional a esses recursos. 

Dispositivos que estão associados ao Azure AD ou Azure AD híbrido associado a um benefício de SSO da sua organização recursos no local, bem como recursos na cloud. Mais informações podem ser encontradas no artigo, [dispositivos associados ao como SSO para funciona de recursos no local no Azure AD](azuread-join-sso.md).

## <a name="device-security"></a>Segurança dos dispositivos

- **Dispositivos registados do Azure AD** utilizar uma conta gerida pelo utilizador final, esta conta é uma conta Microsoft ou outra credencial gerenciado localmente, protegido por um ou mais dos seguintes procedimentos.
   - Palavra-passe
   - PIN
   - Padrão
   - Windows Hello
- **Associado ao Azure AD ou dispositivos associados ao Azure AD híbrido** utilizar uma conta da organização no Azure AD, protegido por um ou mais dos seguintes procedimentos.
   - Palavra-passe
   - Windows Hello para Empresas

## <a name="provisioning"></a>Aprovisionamento

Obter dispositivos no Azure AD pode ser feito numa forma de self-service ou um processo de aprovisionamento controlado pelos administradores.

## <a name="summary"></a>Resumo

Com a gestão de identidade de dispositivo no Azure AD, pode:

- Simplificar o processo de género e a gestão de dispositivos no Azure AD
- Proporcionar aos utilizadores um acesso fácil de utilizar aos recursos baseados na cloud da sua organização

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [dispositivos registados do Azure AD](concept-azure-ad-register.md)
- Saiba mais sobre [dispositivos associados ao Azure AD](concept-azure-ad-join.md)
- Saiba mais sobre [dispositivos associados ao Azure AD híbrido](concept-azure-ad-join-hybrid.md)
- Para obter uma descrição geral de como gerir identidades de dispositivos no portal do Azure, veja [gerir identidades de dispositivos no portal do Azure](device-management-azure-portal.md).
- Para saber mais sobre o acesso condicional com base no dispositivo, veja [políticas de acesso condicional com base no dispositivo de configurar o Azure Active Directory](../conditional-access/require-managed-devices.md).
