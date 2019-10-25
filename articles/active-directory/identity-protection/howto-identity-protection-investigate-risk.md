---
title: Como investigar riscos no Azure Active Directory Identity Protection
description: Saiba como investigar os usuários arriscados, as detecções e as entradas no Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e04cadef1f6a4aee7d8f807fc7aaa999ba6da8d7
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887094"
---
# <a name="how-to-investigate-risk"></a>Como investigar o risco

A proteção de identidade fornece às organizações três relatórios que eles podem usar para investigar os riscos de identidade em seu ambiente. Esses relatórios são os **usuários arriscados**, **entradas arriscadas**e **detecções de risco**. A investigação de eventos é a chave para melhor compreensão e identificação de pontos fracos em sua estratégia de segurança.

Todos os três relatórios permitem o download de eventos no. Formato CSV para análise adicional fora do portal do Azure. Os relatórios de usuários arriscados e de logon arriscados permitem baixar as entradas 2500 mais recentes, enquanto o relatório de detecções de risco permite baixar os registros 5000 mais recentes.

As organizações podem aproveitar as integrações da API Microsoft Graph para agregar dados com outras fontes às quais podem ter acesso como uma organização.

Os três relatórios são encontrados no **portal do Azure** > **Azure Active Directory** > **segurança**.

## <a name="navigating-the-reports"></a>Navegando nos relatórios

Cada relatório é iniciado com uma lista de todas as detecções do período mostrado na parte superior do relatório. Cada relatório permite a adição ou remoção de colunas com base na preferência do administrador. Os administradores podem optar por baixar os dados no. Formato CSV. Os relatórios podem ser filtrados usando os filtros na parte superior do relatório.

A seleção de entradas individuais pode habilitar entradas adicionais na parte superior do relatório, como a capacidade de confirmar uma entrada como comprometida ou segura, confirmar um usuário como comprometido ou ignorar o risco do usuário.

A seleção de entradas individuais expande uma janela de detalhes abaixo das detecções. A exibição de detalhes permite que os administradores investiguem e executem ações em cada detecção. 

![Exemplo de relatório de proteção de identidade mostrando entradas e detalhes arriscados](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>Usuários arriscados

Com as informações fornecidas pelo relatório de usuários arriscados, os administradores podem encontrar:

- Quais usuários estão em risco, tiveram o risco corrigido ou tiveram o risco ignorado?
- Detalhes sobre as detecções
- Histórico de entradas arriscadas
- Histórico de risco
 
Os administradores podem optar por agir nesses eventos. Os administradores podem optar por:

- Redefinir a senha do usuário
- Confirmar o comprometimento do usuário
- Ignorar risco do usuário
- Bloquear o usuário de entrar
- Investigue ainda mais usando o Azure ATP

## <a name="risky-sign-ins"></a>Inícios de sessão de risco

O relatório de entradas arriscadas contém dados filtráveis para até os últimos 30 dias (1 mês).

Com as informações fornecidas pelo relatório de entradas arriscadas, os administradores podem encontrar:

- Quais entradas são classificadas como em risco, confirmado comprometido, confirmado seguro, descartado ou corrigido.
- Níveis de risco agregados e em tempo real associados a tentativas de entrada.
- Tipos de detecção disparados
- Políticas de acesso condicional aplicadas
- Detalhes da MFA
- Informações do dispositivo
- Informações da aplicação
- Informações de local

Os administradores podem optar por agir nesses eventos. Os administradores podem optar por:

- Confirmar comprometimento de entrada
- Confirmar a entrada segura

## <a name="risk-detections"></a>Deteções de riscos

O relatório de detecções de riscos contém dados filtráveis para até os últimos 90 dias (3 meses).

Com as informações fornecidas pelo relatório de detecções de risco, os administradores podem encontrar:

- Informações sobre cada detecção de risco, incluindo o tipo.
- Outros riscos disparados ao mesmo tempo
- Local da tentativa de entrada
- Link para mais detalhes de Microsoft Cloud App Security (MCAS).

Os administradores podem, então, optar por retornar ao relatório de risco ou de entradas do usuário para executar ações com base nas informações coletadas.

## <a name="next-steps"></a>Passos seguintes

- [Políticas disponíveis para mitigar riscos](concept-identity-protection-policies.md)

- [Habilitar as políticas de entrada e de risco do usuário](howto-identity-protection-configure-risk-policies.md)
