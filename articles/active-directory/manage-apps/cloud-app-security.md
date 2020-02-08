---
title: Visibilidade e controlo da aplicação com o Microsoft Cloud App Security
description: Aprenda formas de identificar os níveis de risco da aplicação, parar as violações e fugas em tempo real e usar conectores de aplicações para tirar partido das APIs do fornecedor para visibilidade e governação.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 02/03/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 206e1a06acddae0973d5dbc7e64212026149f217
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069743"
---
# <a name="cloud-app-visibility-and-control"></a>Visibilidade e controlo de aplicativos de nuvem

Para obter o pleno benefício de aplicações e serviços na nuvem, uma equipa de TI deve encontrar o equilíbrio certo para apoiar o acesso, mantendo o controlo para proteger dados críticos. O Microsoft Cloud App Security fornece uma rica visibilidade, controlo sobre viagens de dados e análises sofisticadas para identificar e combater ameaças cibernéticas em todos os seus serviços de nuvem microsoft e de terceiros.

## <a name="discover-and-manage-shadow-it-in-your-network"></a>Descubra e gerencie a sombra de TI na sua rede

Quando os administradores de TI são questionados sobre quantas aplicações na nuvem pensam que os seus colaboradores usam, em média dizem 30 ou 40, quando na realidade, a média é de mais de 1.000 aplicações separadas que estão a ser usadas pelos colaboradores da sua organização. Shadow IT ajuda-o a saber e identificar quais as aplicações que estão a ser usadas e qual é o seu nível de risco. Oitenta por cento dos colaboradores usam apps não autorizadas que ninguém reviu e pode não estar em conformidade com as suas políticas de segurança e conformidade. E como os seus colaboradores são capazes de aceder aos seus recursos e aplicações de fora da sua rede corporativa, já não basta ter regras e políticas nas suas firewalls.

Utilize o Microsoft Cloud App Discovery (uma funcionalidade Azure Ative Directory Premium P1) para descobrir quais as aplicações que estão a ser utilizadas, explorar o risco destas aplicações, configurar políticas para identificar novas aplicações de risco e desanuviar estas aplicações de forma a bloqueá-las de forma nativa usando a sua proxy ou aparelho de firewall.

- Descubra e identifique shadow IT
- Avaliar e analisar
- Gerir as suas apps
- Relatório de descoberta de IT sombra avançada
- Controlar aplicações sancionadas
 
### <a name="learn-more"></a>Saiba mais

- [Descubra e gerencie a sombra de TI na sua rede](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)
- [Aplicativos descobertos com segurança de aplicações em nuvem](https://docs.microsoft.com/cloud-app-security/discovered-apps)
 
## <a name="user-session-visibility-and-control"></a>Visibilidade e controlo da sessão do utilizador 

Na área de trabalho de hoje em dia, muitas vezes, não é suficiente saber o que está a acontecer no seu ambiente de cloud após o fato. Pretende parar as violações e fugas em tempo real antes que os funcionários coloquem os seus dados e a sua organização em risco intencionalmente ou inadvertidamente. Junto com o Azure Active Directory (Azure AD), o Microsoft Cloud App Security fornece esses recursos em uma experiência holística e integrada com o Controle de Aplicativos de Acesso Condicional. 

O controlo de sessão utiliza uma arquitetura de procuração inversa e está exclusivamente integrado com o Azure AD Conditional Access. O acesso condicional do Azure AD permite que você aplique controles de acesso nos aplicativos da sua organização com base em determinadas condições. As condições definem quem (utilizador ou grupo de utilizadores) e o que (quais aplicações na nuvem) e onde (quais localizações e redes) é aplicada uma política de Acesso Condicional. Depois de determinar as condições, pode encaminhar os utilizadores para a Cloud App Security, onde pode proteger os dados em tempo real.  

Com este controlo pode:  
- Controlo de transferências de ficheiros
- Monitor B2B cenários  
- Controlar o acesso aos ficheiros  
- Proteja documentos sobre o download  
 
### <a name="learn-more"></a>Saiba mais

- [Proteja aplicativos com controlo de sessão na Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
 
## <a name="advanced-app-visibility-and-controls"></a>Visibilidade e controlos avançados de aplicações 

Conectores de aplicações utilizam as APIs dos fornecedores da aplicação para aumentar a visibilidade e controlo ao Microsoft Cloud App Security através de aplicações ligadas. Cloud App Security alavanca as APIs fornecidas pelo fornecedor de nuvem. Cada serviço tem sua própria arquitetura e limitações de API, como limitação, API limites, janelas de APIS "time-shifting" dinâmicas e outras pessoas. A equipa de produtos Cloud App Security trabalhou com estes serviços para otimizar o uso de APIs e proporcionar o melhor desempenho. Tendo em conta as diferentes limitações que os serviços impõem às suas APIs, os motores cloud App Security utilizam a sua capacidade máxima permitida. Algumas operações, como digitalizar todos os ficheiros do inquilino, requerem numerosas chamadas api, para que sejam distribuídas por um período mais longo. Espere que algumas políticas sejam executadas por várias horas ou dias. 
 
### <a name="learn-more"></a>Saiba mais  

- [Conectar aplicativos em Cloud App Security](https://docs.microsoft.com/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>Passos seguintes

- [Descubra e gerencie a sombra de TI na sua rede](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)
- [Aplicativos descobertos com segurança de aplicações em nuvem](https://docs.microsoft.com/cloud-app-security/discovered-apps)
- [Proteja aplicativos com controlo de sessão na Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Conectar aplicativos em Cloud App Security](https://docs.microsoft.com/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)
