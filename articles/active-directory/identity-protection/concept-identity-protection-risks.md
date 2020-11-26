---
title: O que é o risco? Azure AD Identity Protection
description: Explicando o risco na Proteção de Identidade AZure AD
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: f10d8a94be53780f732112c012600a7fb840642b
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180647"
---
# <a name="what-is-risk"></a>O que é o risco?

As deteções de risco na Azure AD Identity Protection incluem quaisquer ações suspeitas identificadas relacionadas com contas de utilizador no diretório.

A Proteção de Identidade proporciona às organizações acesso a recursos poderosos para ver e responder rapidamente a estas ações suspeitas. 

![Visão geral de segurança mostrando utilizadores arriscados e insusagens](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

> [!NOTE]
> A Proteção de Identidade só gera deteções de risco quando as credenciais corretas são utilizadas. Se forem utilizadas credenciais incorretas numa sessão de inscrição, não representa um risco de compromisso credencial.

## <a name="risk-types-and-detection"></a>Tipos de risco e deteção

Existem dois tipos de **risco Utilizador** e **Iniciar sção** e dois tipos de deteção ou cálculo **em tempo real** e **offline**.

As deteções em tempo real podem não aparecer nos relatórios durante 5 a 10 minutos. As deteções offline podem não aparecer nos relatórios durante duas a vinte e quatro horas.

### <a name="user-risk"></a>Risco de utilizador

O risco de utilizador representa a probabilidade de uma determinada identidade ou conta estar comprometida. 

Estes riscos são calculados offline usando as fontes internas e externas de inteligência da Microsoft, incluindo investigadores de segurança, profissionais da aplicação da lei, equipas de segurança na Microsoft e outras fontes fidedignas.

| Deteção de riscos | Description |
| --- | --- |
| Fuga de credenciais | Este tipo de deteção de risco indica que as credenciais válidas do utilizador foram vazadas. Quando os cibercriminosos comprometem senhas válidas de utilizadores legítimos, muitas vezes partilham essas credenciais. Esta partilha é normalmente feita publicamente publicamente na dark web, pasta sites, ou através da negociação e venda das credenciais no mercado negro. Quando o serviço de credenciais vazado da Microsoft adquire credenciais de utilizador a partir da web escura, sites de pasta ou outras fontes, eles são verificados contra as credenciais válidas atuais dos utilizadores do AD AZure para encontrar correspondências válidas. Para obter mais informações sobre credenciais vazadas, consulte [questões comuns.](#common-questions) |
| Inteligência de ameaça Azure AD | Este tipo de deteção de risco indica atividade de utilizador que é incomum para o utilizador dado ou é consistente com padrões de ataque conhecidos com base nas fontes internas e externas de inteligência da Microsoft. |

### <a name="sign-in-risk"></a>Risco de início de sessão

Um risco de entrada representa a probabilidade de um dado pedido de autenticação não ser autorizado pelo proprietário da identidade. 

Estes riscos podem ser calculados em tempo real ou calculados offline usando as fontes internas e externas de inteligência da Microsoft, incluindo investigadores de segurança, profissionais da aplicação da lei, equipas de segurança na Microsoft e outras fontes fidedignas.

| Deteção de riscos | Tipo de deteção | Description |
| --- | --- | --- |
| Endereço IP anónimo | Em tempo real | Este tipo de deteção de risco indica insusores de um endereço IP anónimo (por exemplo, navegador Tor ou VPN anónimo). Estes endereços IP são normalmente utilizados por atores que pretendem ocultar a sua telemetria de login (endereço IP, localização, dispositivo, etc.) para intenção potencialmente maliciosa. |
| Viagem atípica | Offline | Este tipo de deteção de risco identifica dois sign-ins originários de locais geograficamente distantes, onde pelo menos um dos locais pode também ser atípico para o utilizador, dado o comportamento passado. Entre vários outros fatores, este algoritmo de aprendizagem automática tem em conta o tempo entre os dois sign-ins e o tempo que o utilizador levaria a viajar do primeiro para o segundo, indicando que um utilizador diferente está a usar as mesmas credenciais. <br><br> O algoritmo ignora "falsos positivos" óbvios, contribuindo para as condições impossíveis de viagem, como VPNs e locais regularmente utilizados por outros utilizadores da organização. O sistema tem um período de aprendizagem inicial dos primeiros 14 dias ou 10 logins, durante os quais aprende o comportamento de entrada de um novo utilizador. |
| Endereço IP ligado a malware | Offline | Este tipo de deteção de risco indica insusões de endereços IP infetados com malware que é conhecido por comunicar ativamente com um servidor de bot. Esta deteção é determinada por correlacionar endereços IP do dispositivo do utilizador contra endereços IP que estavam em contacto com um servidor de bot enquanto o servidor bot estava ativo. |
| Propriedades de inícios de sessão desconhecidos | Em tempo real | Este tipo de deteção de risco considera o histórico de inícios de súpdion (IP, Latitude/ Longitude e ASN) para procurar insus máximos. O sistema armazena informações sobre localizações anteriores usadas por um utilizador e considera estas localizações "familiares". A deteção de risco é desencadeada quando a inscrição ocorre a partir de um local que ainda não está na lista de locais familiares. Os utilizadores recém-criados estarão em "modo de aprendizagem" por um período de tempo em que as deteções de risco de propriedades de inscrição não são desconheçadas enquanto os nossos algoritmos aprendem o comportamento do utilizador. A duração do modo de aprendizagem é dinâmica e depende do tempo que o algoritmo leva para recolher informações suficientes sobre os padrões de insusição do utilizador. A duração mínima é de cinco dias. Um utilizador pode voltar ao modo de aprendizagem após um longo período de inatividade. O sistema também ignora as entradas de dispositivos familiares e locais geograficamente próximos de um local familiar. <br><br> Também executámos esta deteção para autenticação básica (ou protocolos legados). Como estes protocolos não têm propriedades modernas, como iD do cliente, há telemetria limitada para reduzir falsos positivos. Recomendamos que os nossos clientes passem para a autenticação moderna. |
| Admin confirmou utilizador comprometido | Offline | Esta deteção indica que um administrador selecionou "Confirme o utilizador comprometido" na UI dos utilizadores arriscados ou utilizando a API dos RiskyUsers. Para ver qual administrador confirmou que este utilizador comprometeu, verifique o histórico de risco do utilizador (via UI ou API). |
| Endereço IP malicioso | Offline | Esta deteção indica o início de sção de um endereço IP malicioso. Um endereço IP é considerado malicioso com base em altas taxas de falha devido a credenciais inválidas recebidas do endereço IP ou outras fontes de reputação IP. |
| Regras suspeitas de manipulação de caixas de entrada | Offline | Esta deteção é descoberta pela [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules). Esta deteção perfis do seu ambiente e dispara alertas quando regras suspeitas que apagam ou movem mensagens ou pastas são definidas na caixa de entrada de um utilizador. Esta deteção pode indicar que a conta do utilizador está comprometida, que as mensagens estão a ser intencionalmente ocultadas e que a caixa de correio está a ser usada para distribuir spam ou malware na sua organização. |
| Spray de senha | Offline | Um ataque de spray de palavra-passe é onde vários nomes de utilizadores são atacados usando senhas comuns de forma bruta unificada para obter acesso não autorizado. Esta deteção de risco é desencadeada quando um ataque de spray de palavra-passe foi realizado. |
| Deslocação impossível | Offline | Esta deteção é descoberta pela [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#impossible-travel). Esta deteção identifica duas atividades do utilizador (é uma única ou múltipla sessões) originárias de locais geograficamente distantes num período de tempo mais curto do que o tempo que o utilizador levaria a viajar do primeiro para o segundo, indicando que um utilizador diferente está a usar as mesmas credenciais. |

### <a name="other-risk-detections"></a>Outras deteções de risco

| Deteção de riscos | Tipo de deteção | Description |
| --- | --- | --- |
| Risco adicional detetado | Em tempo real ou offline | Esta deteção indica que foi detetada uma das deteções de prémios acima. Uma vez que as deteções de prémios são visíveis apenas para os clientes Azure AD Premium P2, são intituladas "risco adicional detetado" para clientes sem licença Azure AD Premium P2. |

## <a name="common-questions"></a>Perguntas comuns

### <a name="risk-levels"></a>Níveis de risco

A Proteção de Identidade categoriza o risco em três níveis: baixo, médio e alto. Ao configurar [as políticas de proteção de identidade personalizadas,](./concept-identity-protection-policies.md#custom-conditional-access-policy)também pode configurá-la para desencadear sem nível de **risco.** Sem Risco significa que não há indicação ativa de que a identidade do utilizador tenha sido comprometida.

Embora a Microsoft não forneça detalhes específicos sobre como o risco é calculado, diremos que cada nível traz maior confiança de que o utilizador ou o teste de saúde estão comprometidos. Por exemplo, algo como um exemplo de propriedades de inscrição desconhecidas para um utilizador pode não ser tão ameaçador como credenciais vazadas para outro utilizador.

### <a name="password-hash-synchronization"></a>Sincronização de hash de palavra-passe

Deteções de risco, como credenciais vazadas e spray de senha, requerem a presença de hashes de palavra-passe para a deteção. Para obter mais informações sobre a sincronização de hash de palavra-passe, consulte o artigo, [Implemente a sincronização de hash de palavra-passe com a sincronização Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

### <a name="leaked-credentials"></a>Fuga de credenciais

#### <a name="where-does-microsoft-find-leaked-credentials"></a>Onde é que a Microsoft encontra credenciais vazadas?

A Microsoft encontra credenciais vazadas em vários locais, incluindo:

- Sites de pasta pública como pastebin.com e paste.ca onde os maus atores normalmente publicam tal material. Este local é a primeira paragem dos atores maus na sua caça para encontrar credenciais roubadas.
- Agências de aplicação da lei.
- Outros grupos na Microsoft a fazer pesquisa web escura.

#### <a name="why-arent-i-seeing-any-leaked-credentials"></a>Por que não vejo nenhuma credencial vazada?

As credenciais vazadas são processadas sempre que a Microsoft encontra um novo lote disponível ao público. Devido à natureza sensível, as credenciais vazadas são eliminadas logo após o processamento. Apenas novas credenciais vazadas encontradas depois de permitir a sincronização de hash de palavra-passe (PHS) serão processadas contra o seu inquilino. A verificação contra pares credenciais anteriormente encontrados não é realizada. 

#### <a name="i-havent-seen-any-leaked-credential-risk-events-for-quite-some-time"></a>Há algum tempo que não vejo fugas de risco de credenciais?

Se não viu nenhum evento de risco credencial vazado, é por causa das seguintes razões:

- Não tem PHS habilitado para o seu inquilino.
- A Microsoft não encontrou nenhum par de credenciais vazados que correspondam aos seus utilizadores.

#### <a name="how-often-does-microsoft-process-new-credentials"></a>Com que frequência a Microsoft processa novas credenciais?

As credenciais são processadas imediatamente após terem sido encontradas, normalmente em vários lotes por dia.

## <a name="next-steps"></a>Passos seguintes

- [Políticas disponíveis para mitigar riscos](concept-identity-protection-policies.md)
- [Descrição geral da segurança](concept-identity-protection-security-overview.md)