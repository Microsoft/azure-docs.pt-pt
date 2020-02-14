---
title: O que é o modo de relatório de acesso condicional? - Diretório Ativo Azure
description: Como pode informar apenas o modo de ajuda com a implementação da política de acesso condicional
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75880f4c533a503852d62ff940e53d4bcc30d218
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186122"
---
# <a name="what-is-conditional-access-report-only-mode"></a>O que é o modo de relatório de acesso condicional?

O Acesso Condicional é amplamente utilizado pelos nossos clientes para se manter seguro aplicando os controlos de acesso certos nas circunstâncias certas. No entanto, um dos desafios com a implementação de uma política de Acesso Condicional na sua organização é determinar o impacto para os utilizadores finais. Pode ser difícil antecipar o número e os nomes dos utilizadores impactados por iniciativas comuns de implantação, como bloquear a autenticação do legado, exigir a autenticação de vários fatores para uma população de utilizadores ou implementar políticas de risco de inscrição. 

O modo apenas de relatório é um novo estado de política de Acesso Condicional que permite aos administradores avaliar o impacto das políticas de Acesso Condicional antes de as permitir em seu ambiente.  Com o lançamento do modo apenas de relatório:

- As políticas de acesso condicional podem ser ativadas no modo apenas para relatórios.
- Durante o iniciar o adséris, as políticas em modo de relatório são avaliadas, mas não aplicadas.
- Os resultados são registados nos separadores de **acesso condicional** e apenas de **relatório (Pré-visualização)** dos dados de registo de início de sessão.
- Os clientes com uma subscrição do Monitor Azure podem monitorizar o impacto das suas políticas de Acesso Condicional utilizando o livro de insights de acesso condicional.

> [!WARNING]
> As políticas no modo apenas de relatório que exijam dispositivos conformes podem levar os utilizadores do Mac, iOS e Android a selecionarem um certificado de dispositivo durante a avaliação da política, mesmo que a conformidade do dispositivo não seja executada. Estas instruções podem repetir-se até que o dispositivo seja conforme. Para evitar que os utilizadores finais recebem solicitações durante o check-in, exclua as plataformas do dispositivo Mac, iOS e Android de políticas apenas de reporte que efetuem verificações de conformidade com o dispositivo.

![Separador apenas de relatório no registo de log in-sign-in Azure AD](./media/concept-conditional-access-report-only/report-only-detail-in-sign-in-log.png)

## <a name="policy-results"></a>Resultados da política

Quando uma política em modo de relatório é avaliada para um determinado sign-in, existem quatro novos valores de resultadopossíveis:

| Resultado | Descrição |
| --- | --- |
| Relatório apenas: Sucesso | Todas as condições de política configuradas, controlos de subvenções não interativos necessários e controlos de sessão foram satisfeitos. Por exemplo, um requisito de autenticação de vários fatores é satisfeito por uma alegação de MFA já presente no token, ou uma política de dispositivo compatível é satisfeita através da realização de uma verificação do dispositivo num dispositivo conforme. |
| Relatório apenas: Falha | Todas as condições de política configuradas foram satisfeitas, mas nem todos os controlos de subvenções não interativos necessários ou controlos de sessão foram satisfeitos. Por exemplo, uma política aplica-se a um utilizador em que um controlo de blocos é configurado, ou um dispositivo falha uma política de dispositivo compatível. |
| Apenas relatório: Ação do utilizador necessária | Todas as condições de política configuradas foram satisfeitas, mas a ação do utilizador seria necessária para satisfazer os controlos de subvenção ou controlos de sessão necessários. Com o modo apenas de relatório, o utilizador não é solicitado a satisfazer os controlos necessários. Por exemplo, os utilizadores não são solicitados para desafios de autenticação multifactor ou termos de uso.   |
| Relatório apenas: Não aplicado | Nem todas as condições políticas configuradas foram satisfeitas. Por exemplo, o utilizador é excluído da política ou a política aplica-se apenas a determinadas localizações de nome fidedignos. |

## <a name="conditional-access-insights-workbook"></a>Livro de trabalho De Acesso Condicional Insights

Os administradores têm a capacidade de criar múltiplas políticas no modo apenas para relatórios, pelo que é necessário compreender tanto o impacto individual de cada política como o impacto combinado de múltiplas políticas avaliadas em conjunto. O novo livro de informação do Conditional Access Insights permite aos administradores visualizar consultas de Acesso Condicional e monitorizar o impacto de uma política para um determinado intervalo de tempo, conjunto de aplicações e utilizadores. 
 
## <a name="next-steps"></a>Passos seguintes

[Configure o modo apenas de relatório numa política de acesso condicional](howto-conditional-access-report-only.md)
