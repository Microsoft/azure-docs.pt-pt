---
title: Políticas de risco - Azure Ative Directory Identity Protection
description: Ativar e configurar políticas de risco na Proteção de Identidade do Diretório Ativo do Azure
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75707010"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>Como: Configurar e ativar políticas de risco

Como aprendemos no artigo anterior, as políticas de [Proteção de Identidade](concept-identity-protection-policies.md) temos duas políticas de risco que podemos permitir no nosso diretório. 

- Política de risco de inscrição
- Política de risco do utilizador

![Página geral de segurança para permitir políticas de risco de utilizador e de sessão](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

Ambas as políticas funcionam para automatizar a resposta às deteções de risco no seu ambiente e permitir que os utilizadores se auto-remediam quando o risco é detetado. 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>Pré-requisitos 

Se a sua organização pretende permitir que os utilizadores se auto-remediam quando os riscos são detetados, os utilizadores devem estar registados tanto para redefinir a palavra-passe de autosserviço como para a Autenticação De Vários Fatores Azure. Recomendamos que permita a experiência combinada de registo de [informações](../authentication/howto-registration-mfa-sspr-combined.md) de segurança para a melhor experiência. Permitir que os utilizadores se auto-remediam leva-os de volta a um estado produtivo mais rapidamente sem exigir a intervenção do administrador. Os administradores ainda podem ver estes eventos e investigá-los depois do facto. 

## <a name="choosing-acceptable-risk-levels"></a>Escolher níveis de risco aceitáveis

As organizações devem decidir o nível de risco que estão dispostos a aceitar o equilíbrio da experiência do utilizador e da postura de segurança. 

A recomendação da Microsoft é definir o limiar da política de risco do utilizador para **High** e a política de risco de inscrição para **Médio e Superior**.

Escolher um limiar **elevado** reduz o número de vezes que uma apólice é desencadeada e minimiza o impacto para os utilizadores. No entanto, exclui as deteções de risco **baixo** e **médio** da política, o que pode não impedir um intruso de explorar uma identidade comprometida. A seleção de um limiar **baixo** introduz interrupções adicionais de utilizador, mas uma postura de segurança aumentada.

## <a name="exclusions"></a>Exclusões

Todas as políticas permitem excluir utilizadores como o seu [acesso de emergência ou contas de administrador de vidro partido](../users-groups-roles/directory-emergency-access.md). As organizações podem determinar que precisam de excluir outras contas de políticas específicas com base na forma como as contas são utilizadas. Todas as exclusões devem ser revistas regularmente para ver se ainda são aplicáveis.

As localizações de rede fidedignas configuradas são [utilizadas](../conditional-access/location-condition.md) pela Proteção de Identidade em algumas deteções de risco para reduzir falsos positivos.

## <a name="enable-policies"></a>Ativar políticas

Para permitir que as políticas de risco do utilizador e de sessão completem os seguintes passos.

1. Navegue para o [portal Azure.](https://portal.azure.com)
1. Navegue na**visão geral**da**Security** > **proteção da** > identidade do >  **diretório ativo do Azure.**
1. **Selecione Configure a política**de risco do utilizador .
   1. Em **Atribuições**
      1. **Utilizadores** - Escolha **todos os utilizadores** ou **Selecione indivíduos e grupos** se limitar o seu lançamento.
         1. Opcionalmente pode optar por excluir os utilizadores da apólice.
      1. **Condições** - **O risco** do utilizador a recomendação da Microsoft é definir esta opção para **High**.
   1. Sob **controlos**
      1. **Acesso** - A recomendação da Microsoft é **permitir o acesso** e exigir a alteração da **palavra-passe.**
   1. **Impor a política** - **On**
   1. **Save** - Esta ação irá devolvê-lo à página **de visão geral.**
1. **Selecione Configure política de risco de entrada**.
   1. Em **Atribuições**
      1. **Utilizadores** - Escolha **todos os utilizadores** ou **Selecione indivíduos e grupos** se limitar o seu lançamento.
         1. Opcionalmente pode optar por excluir os utilizadores da apólice.
      1. **Condições** - **O risco de inscrição** a recomendação da Microsoft é definir esta opção para Médio e **superior**.
   1. Sob **controlos**
      1. **Acesso** - A recomendação da Microsoft é **permitir o acesso** e exigir a **autenticação de vários fatores.**
   1. **Impor a política** - **On**
   1. **Guardar**

## <a name="next-steps"></a>Passos seguintes

- [Ativar a política de registo de autenticação multi-factor azure](howto-identity-protection-configure-mfa-policy.md)

- [O que é o risco](concept-identity-protection-risks.md)

- [Investigar deteções de riscos](howto-identity-protection-investigate-risk.md)

- [Simular deteções de risco](howto-identity-protection-simulate-risk.md)
