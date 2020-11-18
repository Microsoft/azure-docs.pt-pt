---
title: Visibilidade e controlo da app com a Microsoft Cloud App Security
description: Aprenda formas de identificar os níveis de risco de aplicação, parar as falhas e fugas em tempo real, e usar conectores de aplicações para aproveitar as APIs do fornecedor para visibilidade e governação.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e3e0c10a7ec6b42420db30955ae4911bca27cc0
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651231"
---
# <a name="cloud-app-visibility-and-control"></a>Visibilidade e controlo de aplicações na cloud

Para obter o pleno benefício de aplicações e serviços na nuvem, uma equipa de TI deve encontrar o equilíbrio certo de apoio ao acesso, mantendo o controlo para proteger dados críticos. O Microsoft Cloud App Security proporciona uma visibilidade rica, controlo sobre viagens de dados e análises sofisticadas para identificar e combater ameaças cibernéticas em todos os seus serviços de cloud microsoft e terceiros.

## <a name="discover-and-manage-shadow-it-in-your-network"></a>Detetar e gerir TI sombra na sua rede

Quando os administradores de TI são questionados sobre quantas aplicações na nuvem pensam que os seus colaboradores usam, em média dizem 30 ou 40, quando na realidade, a média é de mais de 1.000 aplicações separadas que são usadas por funcionários da sua organização. Shadow IT ajuda-o a saber e a identificar quais aplicações estão a ser usadas e qual é o seu nível de risco. Oitenta por cento dos colaboradores usam aplicações não higidessidas que ninguém reviu e que podem não estar em conformidade com as suas políticas de segurança e conformidade. E como os seus colaboradores são capazes de aceder aos seus recursos e aplicações de fora da sua rede corporativa, já não é suficiente ter regras e políticas nas suas firewalls.

Utilize o Microsoft Cloud App Discovery (uma funcionalidade Azure Ative Directory Premium P1) para descobrir quais as aplicações que estão a ser utilizadas, explorar o risco destas aplicações, configurar políticas para identificar novas aplicações de risco e desaplicá-las a estas aplicações de forma a bloqueá-las de forma nativa usando o seu dispositivo de procuração ou firewall.

- Descobrir e identificar a TI Sombra
- Avaliar e analisar
- Gerir as suas aplicações
- Relatório avançado de descoberta de TI shadow
- Controlar aplicações sancionadas
 
### <a name="learn-more"></a>Saber mais

- [Descubra e gere ti sombra na sua rede ](/cloud-app-security/tutorial-shadow-it)
- [Aplicativos descobertos com Segurança de Aplicações cloud ](/cloud-app-security/discovered-apps)
 
## <a name="user-session-visibility-and-control"></a>Visibilidade e controlo da sessão do utilizador 

No local de trabalho de hoje, muitas vezes não é suficiente saber o que está a acontecer no seu ambiente de nuvens depois do facto. Pretende parar as falhas e fugas em tempo real antes que os colaboradores coloquem intencionalmente ou inadvertidamente os seus dados e a sua organização em risco. Juntamente com o Azure Ative Directory (Azure AD), o Microsoft Cloud App Security oferece estas capacidades numa experiência holística e integrada com o Controlo de Aplicações de Acesso Condicional. 

O controlo de sessão utiliza uma arquitetura de procuração inversa e está exclusivamente integrado com o Acesso Condicionado AZURE AD. O Azure AD Conditional Access permite-lhe impor controlos de acesso nas aplicações da sua organização com base em determinadas condições. As condições definem quem (utilizador ou grupo de utilizadores) e o que (que aplicações na nuvem) e onde (a que localizações e redes) é aplicada uma política de Acesso Condicional. Depois de ter determinado as condições, pode encaminhar os utilizadores para a Cloud App Security, onde pode proteger os dados em tempo real.  

Com este controlo pode:  
- Downloads de ficheiros de controlo
- Monitor cenários B2B  
- Controlar o acesso aos ficheiros  
- Proteja os documentos no download  
 
### <a name="learn-more"></a>Saber mais

- [Proteja aplicativos com controlo de sessão na Segurança da Aplicação cloud ](/cloud-app-security/proxy-intro-aad)
 
## <a name="advanced-app-visibility-and-controls"></a>Visibilidade e controlos avançados da aplicação 

Os conectores de aplicações utilizam as APIs dos fornecedores de aplicações para permitir uma maior visibilidade e controlo por parte da Microsoft Cloud App Security sobre as aplicações a que se conecta. Cloud App Security aproveita as APIs fornecidas pelo fornecedor de nuvem. Cada serviço tem o seu próprio enquadramento e limitações de API tais como estrangulamento, limites de API, janelas dinâmicas de mudança de tempo API, entre outros. A equipa de produtos cloud App Security trabalhou com estes serviços para otimizar o uso de APIs e proporcionar o melhor desempenho. Tendo em conta as diferentes limitações que os serviços impõem às suas APIs, os motores Cloud App Security utilizam a sua capacidade máxima permitida. Algumas operações, como a digitalização de todos os ficheiros do inquilino, requerem numerosas chamadas de API para que estejam espalhadas por um período mais longo. Espere algumas políticas para funcionar por várias horas ou dias. 
 
### <a name="learn-more"></a>Saber mais  

- [Conecte aplicativos em Cloud App Security ](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>Próximos passos

- [Descubra e gere ti sombra na sua rede ](/cloud-app-security/tutorial-shadow-it)
- [Aplicativos descobertos com Segurança de Aplicações cloud ](/cloud-app-security/discovered-apps)
- [Proteja aplicativos com controlo de sessão na Segurança da Aplicação cloud ](/cloud-app-security/proxy-intro-aad)
- [Conecte aplicativos em Cloud App Security ](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)