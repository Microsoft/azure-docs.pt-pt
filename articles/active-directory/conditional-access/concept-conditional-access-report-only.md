---
title: O que é o modo de relatório de acesso condicional? - Diretório Ativo Azure
description: Como pode relatar modo apenas ajudar com a implementação da política de acesso condicional
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: deced953089fcbb2640cbf3e402db24cb511e769
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935891"
---
# <a name="what-is-conditional-access-report-only-mode"></a>O que é o modo de relatório de acesso condicional?

O Acesso Condicional é amplamente utilizado pelos nossos clientes para se manter seguro aplicando os controlos de acesso certos nas circunstâncias certas. No entanto, um dos desafios com a implementação de uma política de Acesso Condicional na sua organização é determinar o impacto para os utilizadores finais. Pode ser difícil antecipar o número e os nomes dos utilizadores afetados por iniciativas comuns de implementação, como bloquear a autenticação de legados, exigir a autenticação de vários fatores para uma população de utilizadores ou implementar políticas de risco de inscrição. 

O modo apenas de relatório é um novo estado de política de acesso condicional que permite aos administradores avaliar o impacto das políticas de Acesso Condicional antes de as permitirem no seu ambiente.  Com o lançamento do modo apenas de relatório:

- As políticas de acesso condicional podem ser ativadas apenas no modo reporte.
- Durante a entrada, as políticas em modo apenas de relatório são avaliadas, mas não aplicadas.
- Os resultados são registados nos separadores **de acesso condicional** e **apenas relatório** dos dados de registo de início de sessão.
- Os clientes com uma subscrição do Azure Monitor podem monitorizar o impacto das suas políticas de Acesso Condicional utilizando o livro de insights de acesso condicional.

> [!WARNING]
> As políticas no modo apenas de relatórios que exijam dispositivos compatíveis podem levar os utilizadores de Mac, iOS e Android a selecionarem um certificado de dispositivo durante a avaliação da política, mesmo que a conformidade com o dispositivo não seja aplicada. Estas indicações podem repetir-se até que o dispositivo fique em conformidade. Para evitar que os utilizadores finais recebam solicitações durante a entrada, exclua as plataformas de dispositivos Mac, iOS e Android de políticas apenas de relatórios que realizem verificações de conformidade com dispositivos. Note que o modo apenas de relatório não é aplicável para políticas de acesso condicional com âmbito de "Ações do utilizador".

![Separador apenas de relatório no registo de login Azure AD](./media/concept-conditional-access-report-only/report-only-detail-in-sign-in-log.png)

## <a name="policy-results"></a>Resultados da política

Quando uma política em modo apenas de relatório é avaliada para uma determinada entrada, existem quatro novos valores possíveis de resultados:

| Resultado | Descrição |
| --- | --- |
| Apenas relatório: Sucesso | Todas as condições de política configuradas, os controlos de subvenções não interativos necessários e os controlos de sessão foram satisfeitos. Por exemplo, um requisito de autenticação de vários fatores é preenchido por uma alegação de MFA já presente no token, ou uma política de dispositivo em conformidade é satisfeita com a realização de uma verificação do dispositivo num dispositivo em conformidade. |
| Apenas relatório: Falha | Todas as condições políticas configuradas foram satisfeitas, mas nem todos os controlos de subvenção não interativos necessários ou os controlos de sessão foram satisfeitos. Por exemplo, uma política aplica-se a um utilizador em que um controlo de blocos é configurado, ou um dispositivo falha uma política de dispositivo conforme. |
| Apenas relatório: Ação do utilizador necessária | Todas as condições de política configuradas foram satisfeitas, mas seria necessária uma ação do utilizador para satisfazer os controlos de subvenção ou os controlos de sessão necessários. Com o modo apenas de relatório, o utilizador não é solicitado para satisfazer os controlos necessários. Por exemplo, os utilizadores não são solicitados para desafios de autenticação de vários fatores ou termos de utilização.   |
| Apenas relatório: Não aplicado | Nem todas as condições políticas configuradas foram satisfeitas. Por exemplo, o utilizador está excluído da apólice ou a política aplica-se apenas a determinadas localizações nomeadas de confiança. |

## <a name="conditional-access-insights-workbook"></a>Manual de insights de acesso condicional

Os administradores têm a capacidade de criar múltiplas políticas apenas no modo relatório, pelo que é necessário compreender tanto o impacto individual de cada política como o impacto combinado de múltiplas políticas avaliadas em conjunto. O novo manual de acesso condicional permite aos administradores visualizar consultas de Acesso Condicional e monitorizar o impacto de uma política para um determinado intervalo de tempo, conjunto de aplicações e utilizadores. 
 
## <a name="next-steps"></a>Passos seguintes

[Configurar o modo apenas de relatório numa política de acesso condicional](howto-conditional-access-insights-reporting.md)
