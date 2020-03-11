---
title: O que é o risco? Azure AD Identity Protection
description: Explicar o risco na Proteção de Identidade da AD Azure
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
ms.openlocfilehash: 775ff6b3ba003bed22ccd5a42cb4da005c4dbb69
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082252"
---
# <a name="what-is-risk"></a>O que é o risco?

As deteções de risco na Proteção de Identidade Azure AD incluem quaisquer ações suspeitas identificadas relacionadas com contas de utilizador no diretório.

A Proteção de Identidade proporciona às organizações o acesso a recursos poderosos para ver e responder rapidamente a estas ações suspeitas. 

![Visão geral de segurança mostrando utilizadores arriscados e inscrições](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>Tipos de risco e deteção

Existem dois tipos de risco **User** e **Sign-in** e dois tipos de deteção ou cálculo em **tempo real** e **offline**.

### <a name="user-risk"></a>Risco do utilizador

Um risco de utilizador representa a probabilidade de uma determinada identidade ou conta estar comprometida. 

Estes riscos são calculados offline usando fontes de inteligência de ameaças internas e externas da Microsoft, incluindo investigadores de segurança, profissionais da aplicação da lei, equipas de segurança na Microsoft e outras fontes fidedignas.

| Deteção de riscos | Descrição |
| --- | --- |
| Credenciais vazadas | Este tipo de deteção de risco indica que as credenciais válidas do utilizador foram vazadas. Quando os cibercriminosos comprometem senhas válidas de utilizadores legítimos, muitas vezes partilham essas credenciais. Esta partilha é normalmente feita publicando publicamente na dark web, sites de pastas, ou negociando e vendendo as credenciais no mercado negro. Quando o serviço de credenciais da Microsoft vaza do serviço adquire credenciais de utilizador a partir da dark web, sites de pasta ou outras fontes, são verificados contra as credenciais válidas dos utilizadores da AD Azure para encontrar correspondências válidas. |
| Inteligência de ameaça da AD Azure | Este tipo de deteção de risco indica a atividade do utilizador que é incomum para o utilizador dado ou é consistente com padrões de ataque conhecidos baseados nas fontes internas e externas de inteligência de ameaça da Microsoft. |

### <a name="sign-in-risk"></a>Risco de inscrição

Um risco de entrada representa a probabilidade de um determinado pedido de autenticação não ser autorizado pelo proprietário da identidade. 

Estes riscos podem ser calculados em tempo real ou calculados offline usando fontes de inteligência de ameaças internas e externas da Microsoft, incluindo investigadores de segurança, profissionais de aplicação da lei, equipas de segurança da Microsoft e outras fontes fidedignas.

| Deteção de riscos | Tipo de deteção | Descrição |
| --- | --- | --- |
| Endereço IP anónimo | Tempo real | Este tipo de deteção de risco indica inscrições a partir de um endereço IP anónimo (por exemplo, navegador Tor ou VPN anónimo). Estes endereços IP são normalmente utilizados por atores que querem esconder a sua telemetria de login (endereço IP, localização, dispositivo, etc.) para intenções potencialmente maliciosas. |
| Viagem atípica | Offline | Este tipo de deteção de risco identifica dois sign-ins originários de locais geograficamente distantes, onde pelo menos um dos locais também pode ser atípico para o utilizador, dado o comportamento passado. Entre vários outros fatores, este algoritmo de aprendizagem automática tem em conta o tempo entre os dois sign-ins e o tempo que o utilizador teria demorado a viajar do primeiro local para o segundo, indicando que um utilizador diferente está a usar o mesmo credenciais. <br><br> O algoritmo ignora "falsos positivos" óbvios que contribuem para as condições de viagem impossíveis, como VPNs e locais regularmente utilizados por outros utilizadores da organização. O sistema tem um período de aprendizagem inicial de 14 dias ou 10 logins, durante o qual aprende o comportamento de entrada de um novo utilizador. |
| Endereço IP ligado a malware | Offline | Este tipo de deteção de risco indica inscrições a partir de endereços IP infetados com malware que é conhecido por comunicar ativamente com um servidor de bot. Esta deteção é determinada por correlacionar endereços IP do dispositivo do utilizador contra endereços IP que estavam em contacto com um servidor de bot enquanto o servidor bot estava ativo. |
| Propriedades de inscrição desconhecidas | Tempo real | Este tipo de deteção de risco considera a história do sign-in passado (IP, Latitude / Longitude e ASN) para procurar entradas anómalas. O sistema armazena informações sobre locais anteriores utilizados por um utilizador, e considera estas localizações "familiares". A deteção de risco é desencadeada quando o sinal de sessão ocorre a partir de um local que ainda não está na lista de locais familiares. Os utilizadores recém-criados estarão em "modo de aprendizagem" durante um período de tempo em que as deteções de risco de propriedades de entrada desconhecidas serão desligadas enquanto os nossos algoritmos aprendem o comportamento do utilizador. A duração do modo de aprendizagem é dinâmica e depende do tempo que o algoritmo leva para recolher informações suficientes sobre os padrões de inscrição do utilizador. A duração mínima é de cinco dias. Um utilizador pode voltar ao modo de aprendizagem após um longo período de inatividade. O sistema também ignora os sign-ins de dispositivos familiares, e locais que estão geograficamente perto de um local familiar. <br><br> Também executamos esta deteção para autenticação básica (ou protocolos legados). Como estes protocolos não têm propriedades modernas, como o ID do cliente, há telemetria limitada para reduzir falsos positivos. Recomendamos que os nossos clientes se mudem para a autenticação moderna. |
| Administrador confirmado utilizador comprometido | Offline | Esta deteção indica que um administrador selecionou 'Confirmar o utilizador comprometido' no UI dos utilizadores de risco ou utilizando API utilizadores arriscados. Para ver qual a administração que confirmou que este utilizador está comprometido, verifique o histórico de riscos do utilizador (via UI ou API). |
| Endereço IP malicioso | Offline | Esta deteção indica o início de um endereço IP malicioso. Um endereço IP é considerado malicioso com base em altas taxas de falha devido a credenciais inválidas recebidas do endereço IP ou de outras fontes de reputação IP. |
| Regras suspeitas de manipulação de caixas de entrada | Offline | Esta deteção é descoberta pelo [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules). Esta deteção os perfis de seu ambiente e aciona alertas quando as regras de suspeitas que eliminam ou movidos de pastas ou mensagens são definidas na caixa de entrada de um utilizador. Isto pode indicar que a conta do utilizador está comprometida, que as mensagens estão a ser ocultadas intencionalmente e que a caixa de correio está a ser usada para distribuir spam ou malware na sua organização. |
| Deslocação impossível | Offline | Esta deteção é descoberta pelo [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#impossible-travel). Esta deteção identifica duas atividades de utilizador (é uma única ou múltiplas sessões) provenientes de localizações distantes num período de tempo mais curto do que o tempo demoraria viajam de primeira localização para o segundo, que indica que o utilizador que um utilizador diferente está a utilizar as mesmas credenciais. |

### <a name="other-risk-detections"></a>Outras deteções de risco

| Deteção de riscos | Tipo de deteção | Descrição |
| --- | --- | --- |
| Risco adicional detetado | Em tempo real ou offline | Esta deteção indica que foi detetada uma das deteções premium acima referidas. Uma vez que as deteções premium são visíveis apenas para os clientes Azure AD Premium P2, são intitulados "risco adicional detetado" para clientes sem licençaS Azure AD Premium P2. |

## <a name="next-steps"></a>Passos seguintes

- [Políticas disponíveis para mitigar riscos](concept-identity-protection-policies.md)

- [Descrição geral da segurança](concept-identity-protection-security-overview.md)
