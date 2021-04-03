---
title: Políticas de risco - Azure Ative Directory Identity Protection
description: Permitir e configurar políticas de risco na Proteção de Identidade do Diretório Ativo Azure
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 366d68be1a7f115980973015e363da6095876754
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997635"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>Como: Configurar e permitir políticas de risco

Como aprendemos no artigo anterior, as [políticas de Proteção de Identidade](concept-identity-protection-policies.md) temos duas políticas de risco que podemos permitir no nosso diretório. 

- Política de risco do início de sessão
- Política de risco do utilizador

![Página geral de segurança para permitir políticas de risco de utilizador e de inscrição](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

Ambas as políticas funcionam para automatizar a resposta às deteções de risco no seu ambiente e permitir que os utilizadores se auto-remediarem quando o risco é detetado. 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>Pré-requisitos 

Se a sua organização quiser permitir que os utilizadores se auto-remediarem quando os riscos são detetados, os utilizadores devem estar registados tanto para o reset da palavra-passe de autosserviço como para a autenticação multi-factor Azure AD. Recomendamos [que se permita a experiência de registo de informação de segurança combinada](../authentication/howto-registration-mfa-sspr-combined.md) para a melhor experiência. Permitir que os utilizadores se auto-remediarem leva-os de volta a um estado produtivo mais rapidamente sem exigir a intervenção do administrador. Os administradores ainda podem ver estes eventos e investigá-los após o facto. 

## <a name="choosing-acceptable-risk-levels"></a>Escolha níveis de risco aceitáveis

As organizações devem decidir o nível de risco que estão dispostos a aceitar o equilíbrio entre a experiência do utilizador e a postura de segurança. 

A recomendação da Microsoft é definir o limiar da política de risco do utilizador para **a High** e a política de risco de entrada para Medium **e acima**.

A escolha de um limiar **elevado** reduz o número de vezes que uma política é desencadeada e minimiza o impacto para os utilizadores. No entanto, exclui as deteções de risco **baixo** e **médio** da política, o que não pode impedir um intruso de explorar uma identidade comprometida. A seleção de um limiar **Low** introduz interrupções adicionais do utilizador, mas uma postura de segurança aumentada.

## <a name="exclusions"></a>Exclusões

Todas as políticas permitem excluir utilizadores como o [acesso de emergência ou contas de administrador de break-glass](../roles/security-emergency-access.md). As organizações podem determinar que precisam de excluir outras contas de políticas específicas baseadas na forma como as contas são utilizadas. Todas as exclusões devem ser revistas regularmente para ver se ainda são aplicáveis.

As localizações de rede fidedignas configuradas são [usadas](../conditional-access/location-condition.md) pela Proteção de Identidade em algumas deteções de risco para reduzir falsos positivos.

## <a name="enable-policies"></a>Permitir políticas

Para permitir que as políticas de risco de utilização e de risco de inscrição completem os seguintes passos.

1. Navegue até ao [portal Azure.](https://portal.azure.com)
1. Consulte a Visão Geral de Proteção de Identidade do **Diretório Ativo Azure**  >    >    >  .
1. Selecione **a política de risco do utilizador**.
   1. Em **Atribuições**
      1. **Utilizadores** - Escolha **todos os utilizadores** ou **selecione indivíduos e grupos** se limitar o seu lançamento.
         1. Opcionalmente pode optar por excluir os utilizadores da apólice.
      1. **Condições**  -  **Risco de utilizador** A recomendação da Microsoft é definir esta opção para **High**.
   1. Sob **controlo**
      1. **Acesso** - A recomendação da Microsoft é **permitir o acesso** e exigir a alteração da **palavra-passe.**
   1. **Fazer cumprir a política**  -  **Em**
   1. **Guardar** - Esta ação irá devolvê-lo à página **geral.**
1. Selecione **a política de risco de inscrição**.
   1. Em **Atribuições**
      1. **Utilizadores** - Escolha **todos os utilizadores** ou **selecione indivíduos e grupos** se limitar o seu lançamento.
         1. Opcionalmente pode optar por excluir os utilizadores da apólice.
      1. **Condições**  -  **Risco de inscrição** A recomendação da Microsoft é definir esta opção para **Medium e acima**.
   1. Sob **controlo**
      1. **Acesso** - A recomendação da Microsoft é **permitir o acesso** e exigir a **autenticação de vários fatores.**
   1. **Fazer cumprir a política**  -  **Em**
   1. **Guardar**

## <a name="next-steps"></a>Passos seguintes

- [Ativar a política de registo de autenticação multi-factor Azure AD](howto-identity-protection-configure-mfa-policy.md)

- [O que é o risco](concept-identity-protection-risks.md)

- [Investigar deteções de riscos](howto-identity-protection-investigate-risk.md)

- [Simular deteções de riscos](howto-identity-protection-simulate-risk.md)
