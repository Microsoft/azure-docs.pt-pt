---
title: Investigar risco Azure Ative Directory Identity Protection
description: Saiba como investigar utilizadores, deteções e inscrições arriscadas na Azure Ative Directory Identity Protection
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
ms.openlocfilehash: be72c2152bdb8e1155d2dd29547f93ba3605d462
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95251098"
---
# <a name="how-to-investigate-risk"></a>How To: Investigar riscos

A Proteção de Identidade fornece às organizações três relatórios que podem usar para investigar riscos de identidade no seu ambiente. Estes relatórios são **os utilizadores arriscados**, **as entradas de risco** e as **deteções de riscos.** A investigação dos acontecimentos é fundamental para uma melhor compreensão e identificação de quaisquer pontos fracos na sua estratégia de segurança.

Todos os três relatórios permitem o download de eventos em . Formato CSV para análise posterior fora do portal Azure. Os utilizadores de risco e os relatórios de insiná-ins de risco permitem o descarregamento das 2500 entradas mais recentes, enquanto o relatório de deteções de risco permite descarregar os registos mais recentes de 5000.

As organizações podem tirar partido das integrações da Microsoft Graph API para agregar dados com outras fontes a que possam ter acesso como organização.

Os três relatórios encontram-se no **portal**  >  **Azure Ative Directory**  >  **Security**.

## <a name="navigating-the-reports"></a>Navegando nos relatórios

Cada relatório lança uma lista de todas as deteções para o período apresentado no topo do relatório. Cada relatório permite a adição ou remoção de colunas com base na preferência do administrador. Os administradores podem optar por descarregar os dados em . CSV ou . . Formato JSON. Os relatórios podem ser filtrados utilizando os filtros em toda a parte superior do relatório.

A seleção de entradas individuais pode permitir entradas adicionais no topo do relatório, tais como a capacidade de confirmar uma sing-in como comprometida ou segura, confirmar um utilizador como comprometido ou descartar o risco do utilizador.

A seleção de entradas individuais expande uma janela de detalhes abaixo das deteções. A visão de detalhes permite que os administradores investiguem e realizem ações em cada deteção. 

![Relatório de Proteção de Identidade de exemplo mostrando ins e detalhes de risco](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>Utilizadores de risco

Com as informações fornecidas pelo relatório de utilizadores arriscados, os administradores podem encontrar:

- Que utilizadores estão em risco, tiveram riscos remediados ou tiveram o risco de ser dispensados?
- Detalhes sobre deteções
- História de todos os insus máximos de risco
- História do risco
 
Os administradores podem então optar por tomar medidas sobre estes eventos. Os administradores podem escolher:

- Redefinir a senha do utilizador
- Confirmar o compromisso do utilizador
- Descartar o risco do utilizador
- Bloquear o utilizador de iniciar sessão
- Investigue mais além usando Azure ATP

## <a name="risky-sign-ins"></a>Inícios de sessão de risco

O relatório de inscrições de risco contém dados filtrantes até aos últimos 30 dias (1 mês).

Com as informações fornecidas pelo relatório de inscrições de risco, os administradores podem encontrar:

- Que inscrições são classificadas como em risco, confirmadas comprometidas, confirmadas seguras, dispensadas ou remediadas.
- Níveis de risco em tempo real e agregados associados a tentativas de inscrição.
- Tipos de deteção desencadeados
- Políticas de acesso condicional aplicadas
- Detalhes do MFA
- Informações do dispositivo
- Informações da aplicação
- Informações de localização

Os administradores podem então optar por tomar medidas sobre estes eventos. Os administradores podem escolher:

- Confirmar compromisso de entrada
- Confirme o cofre de entrada

> [!NOTE] 
> A Proteção de Identidade avalia o risco para todos os fluxos de autenticação, sejam interativos ou não interativos. No entanto, o relatório de inscrição mostra apenas os relatórios interativos. Pode ver insusimentos arriscados que ocorreram em insiná-ins não interativos, mas o sinal não aparecerá no relatório de inscrições da Azure AD.

## <a name="risk-detections"></a>Deteções de riscos

O relatório de deteção de riscos contém dados filtrantes até aos últimos 90 dias (3 meses).

Com as informações fornecidas pelo relatório de deteção de riscos, os administradores podem encontrar:

- Informação sobre cada deteção de risco, incluindo o tipo.
- Outros riscos desencadeados ao mesmo tempo
- Localização da tentativa de inscrição
- Link para mais detalhes a partir de Microsoft Cloud App Security (MCAS).

Os administradores podem então optar por voltar ao relatório de risco ou de inscrição do utilizador para tomar medidas com base nas informações recolhidas.

> [!NOTE] 
> O nosso sistema pode detetar que o evento de risco que contribuiu para a pontuação de risco do utilizador foi um falso positivo ou o risco do utilizador foi remediado com a aplicação de políticas, tais como completar uma alteração de senha rápida ou segura do MFA. Por isso, o nosso sistema irá descartar o estado de risco e surgirá um detalhe de risco de "AI confirmou o cofre de inscrição" e deixará de contribuir para o risco do utilizador. 


## <a name="next-steps"></a>Passos seguintes

- [Políticas disponíveis para mitigar riscos](concept-identity-protection-policies.md)

- [Ativar políticas de insusição e risco de utilizador](howto-identity-protection-configure-risk-policies.md)
