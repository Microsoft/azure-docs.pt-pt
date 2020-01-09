---
title: O que é risco? Azure AD Identity Protection
description: Explicando o risco no Azure AD Identity Protection
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
ms.openlocfilehash: c169c1eb6511cb05a7b46320b74ae293834cca86
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609150"
---
# <a name="what-is-risk"></a>O que é risco?

As detecções de risco no Azure AD Identity Protection incluem quaisquer ações suspeitas identificadas relacionadas a contas de usuário no diretório.

A proteção de identidade fornece às organizações acesso a recursos avançados para ver e responder rapidamente a essas ações suspeitas. 

![Visão geral de segurança mostrando usuários e entradas arriscadas](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>Tipos de risco e detecção

Há dois tipos de **usuário** de risco e **entrada** e dois tipos de detecção ou cálculo em **tempo real** e **offline**.

### <a name="user-risk"></a>Risco do usuário

Um risco de usuário representa a probabilidade de que uma determinada identidade ou conta seja comprometida. 

Esses riscos são calculados offline usando fontes de inteligência contra ameaças internas e externas da Microsoft, incluindo pesquisadores de segurança, profissionais de imposição de leis, equipes de segurança da Microsoft e outras fontes confiáveis.

| Detecção de riscos | Descrição |
| --- | --- |
| Credenciais vazadas | Esse tipo de detecção de risco indica que as credenciais válidas do usuário foram vazadas. Quando o cibercriminosos compromete senhas válidas de usuários legítimos, eles geralmente compartilham essas credenciais. Normalmente, esse compartilhamento é feito postando-se publicamente na Web escura, colando sites ou negociando e vendendo as credenciais no mercado preto. Quando o serviço de credenciais vazadas da Microsoft adquire credenciais de usuário da Web escura, colar sites ou outras fontes, eles são verificados em relação às credenciais válidas atuais dos usuários do Azure AD para encontrar correspondências válidas. |
| Inteligência contra ameaças do Azure AD | Esse tipo de detecção de risco indica a atividade do usuário que é incomum para o usuário determinado ou é consistente com padrões de ataque conhecidos com base nas fontes de inteligência contra ameaças internas e externas da Microsoft. |

### <a name="sign-in-risk"></a>Início de sessão de risco

Um risco de entrada representa a probabilidade de que uma determinada solicitação de autenticação não seja autorizada pelo proprietário da identidade. 

Esses riscos podem ser calculados em tempo real ou calculados offline usando fontes de inteligência contra ameaças internas e externas da Microsoft, incluindo pesquisadores de segurança, profissionais de imposição de leis, equipes de segurança da Microsoft e outras fontes confiáveis.

| Detecção de riscos | Tipo de detecção | Descrição |
| --- | --- | --- |
| Endereço IP anônimo | Tempo real | Esse tipo de detecção de risco indica entradas de um endereço IP anônimo (por exemplo, navegador Tor ou VPN anônima). Esses endereços IP normalmente são usados por atores que desejam ocultar sua telemetria de logon (endereço IP, local, dispositivo, etc.) para uma intenção potencialmente mal-intencionada. |
| Viagem de atípicos | Offline | Esse tipo de detecção de risco identifica duas entradas provenientes de locais geograficamente distantes, em que pelo menos um dos locais também pode ser atípicos para o usuário, dado o comportamento passado. Entre vários outros fatores, esse algoritmo de aprendizado de máquina leva em conta o tempo entre as duas entradas e o tempo que seria levado para o usuário viajar do primeiro local para o segundo, indicando que um usuário diferente está usando o mesmo fornecidas. <br><br> O algoritmo ignora os "falsos positivos" óbvios que contribuem para as condições de viagem impossíveis, como VPNs e locais usados regularmente por outros usuários na organização. O sistema tem um período de aprendizado inicial do anterior de 14 dias ou 10 logons, durante o qual ele aprende o comportamento de entrada de um novo usuário. |
| Endereço IP vinculado a malware | Offline | Esse tipo de detecção de risco indica entradas de endereços IP infectados com malware que é conhecido por se comunicar ativamente com um servidor de bot. Essa detecção é determinada pela correlação de endereços IP do dispositivo do usuário em endereços IP que estavam em contato com um servidor de bot enquanto o servidor de bot estava ativo. |
| Propriedades de entrada desconhecidas | Tempo real | Esse tipo de detecção de risco considera o histórico de entrada anterior (IP, latitude/longitude e ASN) para procurar por entradas anormais. O sistema armazena informações sobre os locais anteriores usados por um usuário e considera esses locais "familiares". A detecção de risco é disparada quando a entrada ocorre de um local que ainda não está na lista de locais familiares. Os usuários recém-criados estarão no "modo de aprendizado" por um período em que as propriedades de entrada desconhecidas as detecções de risco serão desativadas enquanto nossos algoritmos aprendem o comportamento do usuário. A duração do modo de aprendizado é dinâmica e depende de quanto tempo leva o algoritmo para reunir informações suficientes sobre os padrões de entrada do usuário. A duração mínima é de cinco dias. Um usuário pode voltar para o modo de aprendizado após um longo período de inatividade. O sistema também ignora entradas de dispositivos familiares e locais que estão geograficamente próximos a um local familiar. <br><br> Também executamos essa detecção para autenticação básica (ou protocolos herdados). Como esses protocolos não têm propriedades modernas, como ID do cliente, há telemetria limitada para reduzir falsos positivos. Recomendamos que nossos clientes migrem para a autenticação moderna. |
| Usuário confirmado pelo administrador comprometido | Offline | Essa detecção indica que um administrador selecionou ' confirmar o usuário comprometido ' na interface do usuário de usuários arriscados ou usando a API riskyUsers. Para ver qual administrador confirmou que esse usuário está comprometido, verifique o histórico de risco do usuário (por meio da interface de usuário ou API). |
| Endereço IP mal-intencionado | Offline | Essa detecção indica a entrada de um endereço IP mal-intencionado. Um endereço IP é considerado mal-intencionado com base em taxas de falha altas devido a credenciais inválidas recebidas do endereço IP ou outras fontes de reputação de IP. |
| Regras de manipulação de caixa de entrada suspeitas | Offline | Essa detecção é descoberta pelo [Microsoft Cloud app Security (MCAS)](https://docs.microsoft.com/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules). Esta deteção os perfis de seu ambiente e aciona alertas quando as regras de suspeitas que eliminam ou movidos de pastas ou mensagens são definidas na caixa de entrada de um utilizador. Isto pode indicar que a conta de utilizador for comprometido, as mensagens estão a ser o intencionalmente ocultos e que a caixa de correio está a ser utilizada para distribuir o spam ou de software maligno na sua organização. |
| Deslocação impossível | Offline | Essa detecção é descoberta pelo [Microsoft Cloud app Security (MCAS)](https://docs.microsoft.com/cloud-app-security/anomaly-detection-policy#impossible-travel). Esta deteção identifica duas atividades de utilizador (é uma única ou múltiplas sessões) provenientes de localizações distantes num período de tempo mais curto do que o tempo demoraria viajam de primeira localização para o segundo, que indica que o utilizador que um utilizador diferente está a utilizar as mesmas credenciais. |

### <a name="other-risk-detections"></a>Outras detecções de risco

| Detecção de riscos | Tipo de detecção | Descrição |
| --- | --- | --- |
| Risco adicional detectado | Em tempo real ou offline | Essa detecção indica que uma das detecções Premium acima foi detectada. Como as detecções Premium são visíveis apenas para clientes Azure AD Premium P2, elas são intituladas "risco adicional detectado" para clientes sem licenças Azure AD Premium P2. |

## <a name="next-steps"></a>Passos seguintes

- [Políticas disponíveis para mitigar riscos](concept-identity-protection-policies.md)

- [Descrição geral da segurança](concept-identity-protection-security-overview.md)
