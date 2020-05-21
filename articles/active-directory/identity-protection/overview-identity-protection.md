---
title: O que é o Azure Active Directory Identity Protection?
description: Detetar, remediar, investigar e analisar o risco com a Proteção de Identidade da AD Azure
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 03/17/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27adab85f684143db29e248739492b68af869404
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647350"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>O que é o Azure Active Directory Identity Protection?

A Proteção de Identidade é uma ferramenta que permite às organizações realizar três tarefas-chave:

- Automatizar a deteção e remediação de riscos baseados na identidade.
- Investigue riscos usando dados no portal.
- Dados de deteção de riscos de exportação para serviços públicos de terceiros para análise mais aprofundada.

A Identity Protection utiliza as aprendizagens que a Microsoft adquiriu a partir da sua posição em organizações com a Azure AD, o espaço de consumo com as Contas Microsoft e em jogos com a Xbox para proteger os seus utilizadores. A Microsoft analisa 6,5 biliões de sinais por dia para identificar e proteger os clientes de ameaças.

Os sinais gerados e alimentados à Proteção de Identidade podem ser ainda alimentados em ferramentas como o Acesso Condicional para tomar decisões de acesso, ou devolvidos a uma ferramenta de informação de segurança e gestão de eventos (SIEM) para uma investigação mais aprofundada com base nas políticas impostas pela sua organização.

## <a name="why-is-automation-important"></a>Porque é que a automação é importante?

Na sua [publicação de blogue em outubro de 2018,](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Eight-essentials-for-hybrid-identity-3-Securing-your-identity/ba-p/275843) Alex Weinert, que lidera a equipa de Segurança e Proteção de Identidade da Microsoft, explica porque é que a automação é tão importante quando se lida com o volume de eventos:

> Todos os dias, os nossos sistemas de machine learning e heurística fornecem pontuações de risco para 18 mil milhões de tentativas de login para mais de 800 milhões de contas distintas, das quais 300 milhões são feitas de forma percetível por adversários (entidades como: atores criminosos, hackers).
>
> Na Ignite, no ano passado, falei dos 3 melhores ataques aos nossos sistemas de identidade. Aqui está o volume recente destes ataques
>   
>   - **Repetição de violações**: 4.6BN ataques detetados em maio de 2018
>   - **Spray de senha**: 350k em abril de 2018
>   - **Phishing**: Isto é difícil de quantificar exatamente, mas vimos eventos de risco de 23M em março de 2018, muitos dos quais estão relacionados com phish

## <a name="risk-detection-and-remediation"></a>Deteção e reparação de riscos

A Proteção de Identidade identifica riscos nas seguintes classificações:

| Tipo de deteção de risco | Descrição |
| --- | --- |
| Viagem atípica | Inscreva-se num local atípico com base nos recentes registos do utilizador. |
| Endereço IP anónimo | Inscreva-se num endereço IP anónimo (por exemplo: Tor browser, ANonymizer VPNs). |
| Propriedades de inscrição desconhecidas | Inscreva-se em propriedades que não vimos recentemente para o utilizador. |
| Endereço IP ligado a malware | Inscreva-se num endereço IP ligado a malware |
| Credenciais vazadas | Esta deteção de risco indica que as credenciais válidas do utilizador foram vazadas |
| Inteligência de ameaça da AD Azure | Fontes internas e externas de inteligência de ameaças da Microsoft identificaram um padrão de ataque conhecido |

Mais detalhes sobre estes riscos e como/quando são calculados podem ser encontrados no artigo, [O que é risco](concept-identity-protection-risks.md).

Os sinais de risco podem desencadear esforços de reparação, tais como exigir que os utilizadores realizem: executar a Autenticação Multi-Factor Azure, redefinir a sua palavra-passe usando o reset da palavra-passe self-service ou bloquear até que um administrador tome medidas.

## <a name="risk-investigation"></a>Investigação de risco

Os administradores podem rever as deteções e tomar medidas manuais sobre elas, se necessário. Existem três relatórios-chave que os administradores usam para investigações em Proteção de Identidade:

- Utilizadores de risco
- Inícios de sessão de risco
- Deteções de riscos

Mais informações podem ser encontradas no artigo, [Como: Investigar](howto-identity-protection-investigate-risk.md)o risco.

## <a name="exporting-risk-data"></a>Dados de risco de exportação

Os dados provenientes da Proteção de Identidade podem ser exportados para outras ferramentas de arquivamento e posterior investigação e corelação. As APIs baseadas em Microsoft Graph permitem que as organizações recolham estes dados para posterior processamento numa ferramenta como o seu SIEM. Informações sobre como aceder à API de Proteção de Identidade podem ser encontradas no artigo, Começar com a Proteção de [Identidade do Diretório Ativo azure e](howto-identity-protection-graph-api.md) o Microsoft Graph

Informações sobre a integração de informações de Proteção de Identidade com o Azure Sentinel podem ser encontradas no artigo, [Connect dados da Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md).

## <a name="permissions"></a>Permissões

A Proteção de Identidade requer que os utilizadores sejam um Leitor de Segurança, Operador de Segurança, Administrador de Segurança, Leitor Global ou Administrador Global para aceder.

| Função | Pode fazer | Não posso fazer. |
| --- | --- | --- |
| Administrador global | Acesso total à Proteção de Identidade |   |
| Administrador de segurança | Acesso total à Proteção de Identidade | Redefinir a palavra-passe para um utilizador |
| Operador de segurança | Ver todos os relatórios de Proteção de Identidade e lâmina de visão geral <br><br> Descartar o risco do utilizador, confirmar o insessão seguro, confirmar o compromisso | Configurar ou alterar políticas <br><br> Redefinir a palavra-passe para um utilizador <br><br> Configurar alertas |
| Leitor de segurança | Ver todos os relatórios de Proteção de Identidade e lâmina de visão geral | Configurar ou alterar políticas <br><br> Redefinir a palavra-passe para um utilizador <br><br> Configurar alertas <br><br> Dar feedback sobre deteções |

Atualmente, a função do operador de segurança não pode aceder ao relatório de entrada de risco.

Os administradores de Acesso Condicional também podem criar políticas que fator de risco de inscrição como condição, encontrar mais informações no artigo [Acesso Condicional: Condições](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk).

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| Capacidade | Detalhes | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Basic/Free |
| --- | --- | --- | --- | --- |
| Políticas de risco | Política de risco do utilizador (via Proteção de Identidade) | Sim | Não | Não |
| Políticas de risco | Política de risco de inscrição (via Proteção de Identidade ou Acesso Condicional) | Sim | Não | Não |
| Relatórios de segurança | Descrição geral | Sim | Não | Não |
| Relatórios de segurança | Utilizadores de risco | Acesso completo | Informação Limitada | Informação Limitada |
| Relatórios de segurança | Inícios de sessão de risco | Acesso completo | Informação Limitada | Informação Limitada |
| Relatórios de segurança | Deteções de riscos | Acesso completo | Informação Limitada | Não |
| Notificações | Utilizadores em risco detetaram alertas | Sim | Não | Não |
| Notificações | Digestão semanal | Sim | Não | Não |
| | Política de registo de MFA | Sim | Não | Não |

## <a name="next-steps"></a>Próximos passos

- [Descrição geral da segurança](concept-identity-protection-security-overview.md)

- [O que é o risco](concept-identity-protection-risks.md)

- [Políticas disponíveis para mitigar riscos](concept-identity-protection-policies.md)
