---
title: Políticas de risco-Azure Active Directory Identity Protection
description: Habilitar e configurar políticas de risco no Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ffa08f7ebf013d42d6da0589ce0f1ccc97289de
ms.sourcegitcommit: f2149861c41eba7558649807bd662669574e9ce3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75707010"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>Como configurar e habilitar políticas de risco

Como aprendemos no artigo anterior, [as políticas de proteção de identidade](concept-identity-protection-policies.md) temos duas políticas de risco que podemos habilitar em nosso diretório. 

- Política de risco de entrada
- Política de risco do usuário

![Página Visão geral de segurança para habilitar as políticas de risco de usuário e de entrada](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

Ambas as políticas funcionam para automatizar a resposta a detecções de risco em seu ambiente e permitir que os usuários se recorrem quando o risco é detectado. 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>Pré-requisitos 

Se sua organização quiser permitir que os usuários se recorrir automaticamente quando forem detectados riscos, os usuários deverão ser registrados para a redefinição de senha de autoatendimento e a autenticação multifator do Azure. É recomendável [habilitar a experiência combinada de registro de informações de segurança](../authentication/howto-registration-mfa-sspr-combined.md) para obter a melhor experiência. Permitir que os usuários se corrijam novamente para um estado produtivo mais rapidamente, sem a necessidade de intervenção do administrador. Os administradores ainda podem ver esses eventos e investigue-os após o fato. 

## <a name="choosing-acceptable-risk-levels"></a>Escolhendo níveis de risco aceitáveis

As organizações devem decidir o nível de risco que estão dispostos a aceitar o balanceamento da experiência do usuário e da postura de segurança. 

A recomendação da Microsoft é definir o limite de política de risco do usuário como **alto** e a política de risco de entrada como **média e posterior**.

Escolher um limite **alto** reduz o número de vezes que uma política é disparada e minimiza o impacto para os usuários. No entanto, ele exclui as detecções de risco **baixo** e **médio** da política, o que pode não impedir que um invasor Explore uma identidade comprometida. A seleção de um limite **baixo** introduz interrupções de usuário adicionais, mas uma postura de segurança aumenta.

## <a name="exclusions"></a>Exclusões

Todas as políticas permitem a exclusão de usuários, como [contas de administrador de acesso de emergência ou](../users-groups-roles/directory-emergency-access.md)de usuário de vidro. As organizações podem determinar que precisam excluir outras contas de políticas específicas com base na maneira como as contas são usadas. Todas as exclusões devem ser revisadas regularmente para ver se elas ainda são aplicáveis.

Os [locais de rede](../conditional-access/location-condition.md) confiáveis configurados são usados pela proteção de identidade em algumas detecções de risco para reduzir os falsos positivos.

## <a name="enable-policies"></a>Habilitar políticas

Para habilitar o risco do usuário e as políticas de risco de entrada, conclua as etapas a seguir.

1. Navegue para o [portal do Azure](https://portal.azure.com).
1. Navegue até **Azure Active Directory** > **Security** > **identidade Protection** > **visão geral**.
1. Selecione **Configurar política de risco do usuário**.
   1. Em **atribuições**
      1. **Usuários** – escolha **todos os usuários** ou **selecione indivíduos e grupos** se limitar a distribuição.
         1. Opcionalmente, você pode optar por excluir usuários da política.
      1. **Condições** - o **risco do usuário** que a recomendação da Microsoft é definir essa opção como **alta**.
   1. Em **controles**
      1. **Acesso** -a recomendação da Microsoft é **permitir o acesso** e **exigir alteração de senha**.
   1. **Impor** - **de política em**
   1. **Salvar** – esta ação retornará à página **visão geral** .
1. Selecione **Configurar política de risco de entrada**.
   1. Em **atribuições**
      1. **Usuários** – escolha **todos os usuários** ou **selecione indivíduos e grupos** se limitar a distribuição.
         1. Opcionalmente, você pode optar por excluir usuários da política.
      1. **Condições** - **risco de entrada** a recomendação da Microsoft é definir essa opção como **média e superior**.
   1. Em **controles**
      1. **Acesso** -a recomendação da Microsoft é **permitir o acesso** e **exigir a autenticação multifator**.
   1. **Impor** - **de política em**
   1. **Guardar**

## <a name="next-steps"></a>Passos seguintes

- [Habilitar a política de registro da autenticação multifator do Azure](howto-identity-protection-configure-mfa-policy.md)

- [O que é risco](concept-identity-protection-risks.md)

- [Investigar as detecções de risco](howto-identity-protection-investigate-risk.md)

- [Simular detecções de riscos](howto-identity-protection-simulate-risk.md)
