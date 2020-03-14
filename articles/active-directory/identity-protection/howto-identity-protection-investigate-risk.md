---
title: Investigar risco Azure Ative Directory Identity Protection
description: Saiba como investigar utilizadores, deteções e inscrições de risco na Proteção de Identidade do Diretório Ativo azure
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5191f05752c177aa29d121abe9d1aa29fde265a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79253484"
---
# <a name="how-to-investigate-risk"></a>Como: Investigar o risco

A Proteção de Identidade fornece às organizações três relatórios que podem usar para investigar riscos de identidade no seu ambiente. Estes relatórios são **os utilizadores de risco,** **os insins de risco**e as deteções de **risco.** A investigação dos acontecimentos é fundamental para uma melhor compreensão e identificação de quaisquer pontos fracos na sua estratégia de segurança.

Todos os três relatórios permitem o download de eventos em . Formato CSV para análise mais aprofundada fora do portal Azure. Os utilizadores de risco e os relatórios de entrada de risco permitem descarregar as mais recentes 2500 entradas, enquanto o relatório de deteção de riscos permite descarregar os mais recentes 5000 registos.

As organizações podem aproveitar as integrações da Microsoft Graph API para agregar dados com outras fontes a que possam ter acesso como organização.

Os três relatórios encontram-se no **portal Azure** > **Diretório Ativo** do Azure > **Security**.

## <a name="navigating-the-reports"></a>Navegar nos relatórios

Cada relatório é lançado com uma lista de todas as deteções para o período apresentado no topo do relatório. Cada relatório permite a adição ou remoção de colunas com base na preferência do administrador. Os administradores podem optar por fazer o download dos dados em . CSV ou . Formato JSON. Os relatórios podem ser filtrados utilizando os filtros na parte superior do relatório.

A seleção de entradas individuais pode permitir entradas adicionais na parte superior do relatório, tais como a capacidade de confirmar um início de sessão como comprometido ou seguro, confirmar um utilizador como comprometido ou descartar o risco do utilizador.

A seleção de entradas individuais expande uma janela de detalhes abaixo das deteções. A visão dos detalhes permite que os administradores investiguem e realizem ações em cada deteção. 

![Relatório de Proteção de Identidade de exemplo mostrando inscrições e detalhes arriscados](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>Utilizadores de risco

Com as informações fornecidas pelo relatório de utilizadores de risco, os administradores podem encontrar:

- Que utilizadores estão em risco, tiveram risco remediado ou tiveram risco de ser descartados?
- Detalhes sobre deteções
- História de todos os sign-ins arriscados
- Histórico de risco
 
Os administradores podem então optar por tomar medidas sobre estes eventos. Os administradores podem optar por:

- Redefinir a palavra-passe do utilizador
- Confirmar o compromisso do utilizador
- Descartar o risco de utilizador
- Bloquear o utilizador de iniciar sessão
- Investigar mais sobre o uso do ATP Azure

## <a name="risky-sign-ins"></a>Inícios de sessão de risco

O relatório de inscrição de risco contém dados filtrantes até aos últimos 30 dias (1 mês).

Com as informações fornecidas pelo relatório de inscrição de risco, os administradores podem encontrar:

- Que inscrições são classificadas como em risco, confirmadas comprometidas, confirmadas seguras, dispensadas ou remediadas.
- Níveis de risco em tempo real e agregados associados a tentativas de inscrição.
- Tipos de deteção desencadeados
- Políticas de Acesso Condicional aplicadas
- Detalhes do MFA
- Informações do dispositivo
- Informações da aplicação
- Informações de localização

Os administradores podem então optar por tomar medidas sobre estes eventos. Os administradores podem optar por:

- Confirmar compromisso de inscrição
- Confirmar o cofre de inscrição

## <a name="risk-detections"></a>Deteções de riscos

O relatório de deteção de riscos contém dados filtrantes até aos últimos 90 dias (3 meses).

Com as informações fornecidas pelo relatório de deteção de riscos, os administradores podem encontrar:

- Informação sobre cada deteção de risco, incluindo o tipo.
- Outros riscos desencadeados ao mesmo tempo
- Local de tentativa de inserção
- Ligue-se a mais detalhes a partir do Microsoft Cloud App Security (MCAS).

Os administradores podem então optar por voltar ao relatório de risco ou de inscrição do utilizador para tomar medidas com base nas informações recolhidas.

## <a name="next-steps"></a>Passos seguintes

- [Políticas disponíveis para mitigar riscos](concept-identity-protection-policies.md)

- [Ativar políticas de risco de inscrição e utilizador](howto-identity-protection-configure-risk-policies.md)
