---
title: O que é o Azure Active Directory Identity Protection?
description: Detetar, remediar, investigar e analisar o risco com a Proteção de Identidade AZure AD
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 08/24/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.custom: contperfq1
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2dbb47371197d07b45f9144e2be66e992df4c97
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996531"
---
# <a name="what-is-identity-protection"></a>O que é o Identity Protection?

A Proteção de Identidade é uma ferramenta que permite às organizações realizar três tarefas-chave:

- Automatizar a deteção e reparação de riscos baseados na identidade.
- Investigar riscos usando dados no portal.
- Exportar dados de deteção de riscos para serviços públicos de terceiros para posterior análise.

A Identity Protection utiliza as aprendizagens que a Microsoft adquiriu da sua posição em organizações com a Azure AD, o espaço de consumo com contas da Microsoft e em jogos com a Xbox para proteger os seus utilizadores. A Microsoft analisa 6,5 biliões de sinais por dia para identificar e proteger os clientes de ameaças.

Os sinais gerados e alimentados para a Proteção de Identidade, podem ser alimentados em ferramentas como o Acesso Condicional para tomar decisões de acesso, ou alimentados de volta a uma ferramenta de informação de segurança e gestão de eventos (SIEM) para uma investigação mais aprofundada com base nas políticas aplicadas da sua organização.

## <a name="why-is-automation-important"></a>Por que a automação é importante?

No seu [blogue, em outubro de 2018,](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Eight-essentials-for-hybrid-identity-3-Securing-your-identity/ba-p/275843) Alex Weinert, que lidera a equipa de Segurança e Proteção de Identidade da Microsoft, explica porque é que a automação é tão importante quando se lida com o volume de eventos:

> Todos os dias, os nossos sistemas de machine learning e heurística fornecem pontuações de risco para 18 mil milhões de tentativas de login de mais de 800 milhões de contas distintas, das quais 300 milhões são feitas discernivelmente por adversários (entidades como: atores criminosos, hackers).
>
> No Ignite, no ano passado, falei dos 3 melhores ataques aos nossos sistemas de identidade. Aqui está o recente volume destes ataques
>   
>   - **Repetição de violação**: ataques de 4.6BN detetados em maio de 2018
>   - **Spray de senha**: 350k em abril de 2018
>   - **Phishing**: Isto é difícil de quantificar exatamente, mas vimos eventos de risco 23M em março de 2018, muitos dos quais estão relacionados com phish

## <a name="risk-detection-and-remediation"></a>Deteção e reparação de riscos

A Proteção de Identidade identifica os riscos nas seguintes classificações:

| Tipo de deteção de risco | Description |
| --- | --- |
| Viagem atípica | Inscreva-se a partir de uma localização atípica com base nas recentes insusagens do utilizador. |
| Endereço IP anónimo | Inscreva-se a partir de um endereço IP anónimo (por exemplo: navegador Tor, VPNs anonimizadores). |
| Propriedades de inícios de sessão desconhecidos | Inscreva-se com propriedades que não vimos recentemente para o utilizador dado. |
| Endereço IP ligado a malware | Inscreva-se a partir de um endereço IP ligado a malware. |
| Credenciais vazadas | Indica que as credenciais válidas do utilizador foram vazadas. |
| Spray de senha | Indica que vários nomes de utilizadores estão a ser atacados usando senhas comuns de forma unificada e bruta. |
| Inteligência de ameaça Azure AD | Fontes internas e externas da Microsoft identificaram um padrão de ataque conhecido. |

Mais detalhes sobre estes riscos e como/quando são calculados podem ser encontrados no artigo, [O que é o risco](concept-identity-protection-risks.md).

Os sinais de risco podem desencadear esforços de reparação, tais como exigir que os utilizadores realizem a autenticação multi-factor Azure, reiniciem a sua palavra-passe utilizando o reset da palavra-passe de autosserviço ou bloqueiem até que um administrador tome medidas.

## <a name="risk-investigation"></a>Investigação de risco

Os administradores podem rever as deteções e tomar medidas manuais sobre elas, se necessário. Existem três relatórios-chave que os administradores usam para investigações em Proteção de Identidade:

- Utilizadores de risco
- Inícios de sessão de risco
- Deteções de riscos

Mais informações podem ser encontradas no artigo, [Como: Investigar o risco.](howto-identity-protection-investigate-risk.md)

### <a name="risk-levels"></a>Níveis de risco

A Proteção de Identidade categoriza o risco em três níveis: baixo, médio e alto. 

Embora a Microsoft não forneça detalhes específicos sobre como o risco é calculado, diremos que cada nível traz maior confiança de que o utilizador ou o teste de saúde estão comprometidos. Por exemplo, algo como um exemplo de propriedades de inscrição desconhecidas para um utilizador pode não ser tão ameaçador como credenciais vazadas para outro utilizador.

## <a name="exporting-risk-data"></a>Dados de risco de exportação

Os dados da Proteção de Identidade podem ser exportados para outras ferramentas para arquivar e continuar a investigação e correlação. As APIs baseadas no Microsoft Graph permitem que as organizações recolham estes dados para posterior processamento numa ferramenta como o seu SIEM. Informações sobre como aceder à API de Proteção de Identidade podem ser encontradas no artigo, [Começar com a Azure Ative Directory Identity Protection e Microsoft Graph](howto-identity-protection-graph-api.md)

Informações sobre a integração de informações de Proteção de Identidade com Azure Sentinel podem ser encontradas no artigo, [Ligue os dados da Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md).

## <a name="permissions"></a>Permissões

A Proteção de Identidade requer que os utilizadores sejam um Leitor de Segurança, Operador de Segurança, Administrador de Segurança, Leitor Global ou Administrador Global para poderem aceder.

| Função | Pode fazer | Não posso fazer |
| --- | --- | --- |
| Administrador global | Acesso total à Proteção de Identidade |   |
| Administrador de segurança | Acesso total à Proteção de Identidade | Redefinir a palavra-passe para um utilizador |
| Operador de segurança | Ver todos os relatórios de Proteção de Identidade e folha de visão geral <br><br> Descartar o risco do utilizador, confirmar a entrada segura, confirmar compromisso | Configurar ou alterar políticas <br><br> Redefinir a palavra-passe para um utilizador <br><br> Configurar alertas |
| Leitor de segurança | Ver todos os relatórios de Proteção de Identidade e folha de visão geral | Configurar ou alterar políticas <br><br> Redefinir a palavra-passe para um utilizador <br><br> Configurar alertas <br><br> Dar feedback sobre deteções |

Atualmente, a função do operador de segurança não pode aceder ao relatório de inscrições de Risco.

Os administradores de acesso condicional também podem criar políticas que fator no risco de inscrição como condição. Mais informações no artigo [Acesso Condicionado: Condições.](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk)

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| Funcionalidade | Detalhes  | Azure AD Free / Microsoft 365 Apps | Azure AD Premium P1|Azure AD Premium P2 |
| --- | --- | --- | --- | --- |
| Políticas de risco | Política de risco do utilizador (via Proteção de Identidade)  | No | No |Yes | 
| Políticas de risco | Política de risco de acesso (via Proteção de Identidade ou Acesso Condicional)  | No |  No |Yes |
| Relatórios de segurança | Descrição geral |  No | No |Yes |
| Relatórios de segurança | Utilizadores de risco  | Informação Limitada | Informação Limitada | Acesso completo|
| Relatórios de segurança | Inícios de sessão de risco  | Informação Limitada | Informação Limitada | Acesso completo|
| Relatórios de segurança | Deteções de riscos   | No | Informação Limitada| Acesso completo|
| Notificações | Utilizadores em risco detetados alertas  | No | No |Yes |
| Notificações | Digestão semanal| No | No | Yes | 
| | Política de registo na MFA | No | No | Yes |

Mais informações sobre estes relatórios ricos podem ser encontradas no artigo, [Como: Investigar o risco](howto-identity-protection-investigate-risk.md#navigating-the-reports).

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral da segurança](concept-identity-protection-security-overview.md)

- [O que é o risco](concept-identity-protection-risks.md)

- [Políticas disponíveis para mitigar riscos](concept-identity-protection-policies.md)
