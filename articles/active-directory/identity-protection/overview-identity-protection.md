---
title: O que é Azure Active Directory Identity Protection?
description: Detectar, corrigir, investigar e analisar os riscos com Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e928d67ba7102df3d342e77705ea895f9230ff3
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887661"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>O que é Azure Active Directory Identity Protection?

A proteção de identidade é uma ferramenta que permite que as organizações realizem três tarefas principais:

- Automatize a detecção e a correção de riscos baseados em identidade.
- Investigue os riscos usando os dados no Portal.
- Exporte dados de detecção de riscos para utilitários de terceiros para análise adicional.

A proteção de identidades usa os aprendizados que a Microsoft adquiriu de sua posição em organizações com o Azure AD, o espaço do consumidor com contas da Microsoft e em jogos com o Xbox para proteger seus usuários. A Microsoft analisa 6.500.000.000.000 sinais por dia para identificar e proteger os clientes contra ameaças.

Os sinais gerados pelo e alimentados para a proteção de identidade podem ser mais inseridos em ferramentas como o acesso condicional para tomar decisões de acesso ou voltados para uma ferramenta SIEM (gerenciamento de informações e eventos de segurança) para uma investigação mais aprofundada com base nas suas organizações políticas impostas.

## <a name="why-is-automation-important"></a>Por que a automação é importante?

Em sua [postagem de blog em outubro de 2018](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Eight-essentials-for-hybrid-identity-3-Securing-your-identity/ba-p/275843) Alex Weinert, que lidera a equipe de segurança e proteção de identidade da Microsoft, explica por que a automação é tão importante ao lidar com o volume de eventos:

> Todos os dias, nossos sistemas de aprendizado de máquina e heurística fornecem pontuações de risco para 18.000.000.000 tentativas de logon para mais de 800 milhões contas distintas, 300 milhões das que são discernibly feitas por adversários (entidades como: atores criminais, hackers).
>
> No Ignite do ano passado, eu falou sobre os três principais ataques em nossos sistemas de identidade. Este é o volume recente desses ataques
>   
>   - **Reprodução de violação**: bilhão ataques de 4.6 detectados em maio de 2018
>   - **Irrigação de senha**: 350K em abril de 2018
>   - **Phishing**: é difícil quantificar exatamente, mas vimos eventos de risco 23m em março de 2018, muitos dos quais estão relacionados ao Phish

## <a name="risk-detection-and-remediation"></a>Detecção de riscos e correção

O Identity Protection identifica os riscos nas seguintes classificações:

| Tipo de detecção de risco | Descrição |
| --- | --- |
| Viagem de atípicos | Entre em um local atípicos com base nas entradas recentes do usuário. |
| Endereço IP anônimo | Entre em um endereço IP anônimo (por exemplo: Tor browser, Anonymizer VPNs). |
| Propriedades de entrada desconhecidas | Entre com propriedades que não vimos recentemente para o usuário determinado. |
| Endereço IP vinculado a malware | Entrar de um endereço IP vinculado por malware |
| Credenciais vazadas | Essa detecção de risco indica que as credenciais válidas do usuário foram vazadas |
| Inteligência contra ameaças do Azure AD | As fontes de inteligência contra ameaças internas e externas da Microsoft identificaram um padrão de ataque conhecido |

Mais detalhes sobre esses riscos e como/quando eles são calculados podem ser encontrados no artigo [o que é risco](concept-identity-protection-risks.md).

Os sinais de risco podem disparar esforços de correção, como exigir que os usuários: executem a autenticação multifator do Azure, redefinam sua senha usando a redefinição de senha de autoatendimento ou o bloqueio até que um administrador execute uma ação.

## <a name="risk-investigation"></a>Investigação de risco

Os administradores podem examinar as detecções e tomar uma ação manual sobre elas, se necessário. Há três relatórios principais que os administradores usam para investigações na proteção de identidade:

- Usuários arriscados
- Inícios de sessão de risco
- Deteções de riscos

Mais informações podem ser encontradas no artigo [como: investigar risco](howto-identity-protection-investigate-risk.md).

## <a name="exporting-risk-data"></a>Exportando dados de risco

Os dados da proteção de identidade podem ser exportados para outras ferramentas para arquivamento e outras investigações e Corelações. As APIs baseadas em Microsoft Graph permitem que as organizações coletem esses dados para processamento adicional em uma ferramenta como o SIEM. Informações sobre como acessar a API de proteção de identidade podem ser encontradas no artigo introdução [ao Azure Active Directory Identity Protection e Microsoft Graph](howto-identity-protection-graph-api.md)

Informações sobre como integrar informações de proteção de identidade com o Azure Sentinel podem ser encontradas no artigo [conectar dados de Azure ad Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md).

## <a name="permissions"></a>Permissões

A proteção de identidade exige que os usuários sejam um leitor de segurança, um operador de segurança, um administrador de segurança, um leitor global ou um administrador global para acessar o.

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| Capacidade | Detalhes | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Basic/gratuito |
| --- | --- | --- | --- | --- |
| Políticas de risco | Política de risco do usuário (por meio da proteção de identidade) | Sim | Não | Não |
| Políticas de risco | Política de risco de entrada (por meio da proteção de identidade ou acesso condicional) | Sim | Não | Não |
| Relatórios de segurança | Visão geral | Sim | Não | Não |
| Relatórios de segurança | Usuários arriscados | Acesso completo | Informações limitadas | Informações limitadas |
| Relatórios de segurança | Inícios de sessão de risco | Acesso completo | Informações limitadas | Informações limitadas |
| Relatórios de segurança | Deteções de riscos | Acesso completo | Informações limitadas | Não |
| Notificações | Alertas de usuários em risco detectados | Sim | Não | Não |
| Notificações | Resumo semanal | Sim | Não | Não |
| | Política de registro de MFA | Sim | Não | Não |

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral da segurança](concept-identity-protection-security-overview.md)

- [O que é risco](concept-identity-protection-risks.md)

- [Políticas disponíveis para mitigar riscos](concept-identity-protection-policies.md)
