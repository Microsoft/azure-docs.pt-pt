---
title: Planos de implantação - Azure Active Directory | Documentos da Microsoft
description: Documentação de orientação de ponta a ponta sobre como implementar muitos recursos do Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a19c78efb7d81dcdcd3c221f58d4e4cc7be5ccd1
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722199"
---
# <a name="azure-active-directory-deployment-plans"></a>Planos de implementação do Azure Active Directory
Está à procura de orientações completas para implementar algumas das capacidades do Azure Active Directory (Azure AD)? Os planos de implementação que se seguem orientam-no ao longo do valor comercial, das considerações de planeamento, de design e dos procedimentos operacionais necessários para implementar algumas das capacidades mais comuns do Azure AD. 

Nos documentos, vai encontrar modelos de e-mails, diagramas da arquitetura do sistema, casos de teste comuns, entre outras informações. 

Gostaríamos muito de saber a sua opinião sobre os documentos. Responda a este breve [inquérito](https://aka.ms/deploymentplanfeedback) para nos dizer se os documentos lhe foram úteis. 

## <a name="include-the-right-stakeholders"></a>Incluir os participantes certos

Ao iniciar o planejamento da implantação para um novo recurso, é importante incluir os principais acionistas em sua organização. Recomendamos que você identifique e documente a pessoa ou as pessoas que atendem a cada uma das funções a seguir e trabalhe com elas para determinar seu envolvimento no projeto.  

As funções podem incluir o seguinte 

|Role |Descrição |
|-|-|
|Usuário final|Um grupo representativo de usuários para os quais a funcionalidade será implementada. Geralmente, visualiza as alterações em um programa piloto.
|Gerente de suporte de ti|O departamento de ti oferece suporte à organização que pode fornecer informações sobre a compatibilidade dessa mudança de uma perspectiva de assistência técnica.  
|Arquiteto de identidade ou administrador global do Azure|Representante da equipe de gerenciamento de identidades responsável pela definição de como essa alteração é alinhada com a infra-estrutura principal de gerenciamento de identidades em sua organização.|
|Proprietário da empresa do aplicativo |O proprietário geral da empresa dos aplicativos afetados, que podem incluir o gerenciamento de acesso.  Também pode fornecer informações sobre a experiência do usuário e a utilidade dessa alteração da perspectiva de um usuário final.
|Proprietário da segurança|Um representante da equipe de segurança que pode se desconectar que o plano atenderá aos requisitos de segurança de sua organização.|
|Gerente de conformidade|A pessoa na sua organização responsável por garantir a conformidade com requisitos corporativos, industriais ou governamentais.|

**Os níveis de envolvimento podem incluir:**

- **R**esponsible para implementar o plano e o resultado do projeto 

- **Um**pproval do plano e do resultado do projeto 

- **C**para o plano e o resultado do projeto 

- **Eu**nformed o plano e o resultado do projeto
 
## <a name="deployment-plans"></a>Planos de implantação



|Cenário |Descrição |
|-|-|
|[Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)|A Azure Multi-Factor Authentication (MFA) é uma solução de verificação em dois passos da Microsoft. Ao utilizar métodos de autenticação aprovados pelo administrador, o MFA do Azure ajuda a salvaguardar o acesso a dados e aplicações, enquanto cumpre a exigência de um processo de início de sessão simples.|
|[Acesso condicional](https://aka.ms/deploymentplans/ca)|Com o acesso condicional, você pode implementar decisões de controle de acesso automatizadas para quem pode acessar seus aplicativos de nuvem, com base em condições.|
|[Reposição personalizada de palavras-passe](https://aka.ms/deploymentplans/sspr)|A reposição personalizada de palavras-passe ajuda os utilizadores a repor as respetivas palavras-passe, sem qualquer intervenção do administrador, quando e onde precisarem.|
|[Privileged Identity Management](https://aka.ms/deploymentplans/pim)|Azure AD Privileged Identity Management (PIM) ajuda a gerenciar funções administrativas privilegiadas no Azure AD, recursos do Azure e outros serviços online da Microsoft. O PIM fornece soluções como acesso just-in-time, fluxos de trabalho de aprovação de solicitação e revisões de acesso totalmente integradas para que você possa identificar, descobrir e impedir atividades mal-intencionadas de funções privilegiadas em tempo real.|
|[Início de sessão único](https://aka.ms/deploymentplans/sso)|O início de sessão único ajuda-o a aceder a todos os recursos e aplicações de que precisa para trabalhar ao iniciar sessão só uma vez e com uma única conta de utilizador. Depois de iniciar sessão, pode utilizar o Microsoft Office, o SalesForce ou o Box sem ter de se autenticar (por exemplo, introduzir uma palavra-passe) uma segunda vez.|
|[SSO totalmente integrado](https://aka.ms/SeamlessSSODPDownload)|O Início de Sessão Único Totalmente Integrado do Azure Active Directory (SSO Totalmente Integrado do Azure AD) inicia sessão automaticamente pelos utilizadores quando estão a utilizar os dispositivos da empresa ligados à sua rede empresarial. Depois de ativar esta funcionalidade, os utilizadores não precisam de introduzir as palavras-passe para iniciar sessão no Azure AD e, por norma, nem sequer têm de introduzir os nomes de utilizador. Esta funcionalidade dá aos utilizadores acesso fácil às aplicações baseadas na cloud sem serem precisos componentes no local adicionais.|
|[Painel de Acesso](https://aka.ms/AccessPanelDPDownload)|Proporcionar aos usuários um hub simple para detetar e aceder a todos os seus aplicativos. Ativá-los para serem mais produtivos com capacidades self-service, como a capacidade de solicitar acesso a aplicações de novas e grupos, ou gerir o acesso a estes recursos em nome de outros.|
|[ADFS para Sincronização Hash de Palavras-passe](https://aka.ms/deploymentplans/adfs2phs)|Com a Sincronização Hash de Palavras-passe, são sincronizados hashes das palavras-passe dos utilizadores do Active Directory no local com o Azure AD, o que permite que este último autentique os utilizadores sem interagir com o Active Directory no local.|
|[ADFS para Autenticação Pass-through](https://aka.ms/deploymentplans/adfs2pta)|A Autenticação Pass-through do Azure AD ajuda os utilizadores a iniciar sessão em aplicações no local e na cloud com as mesmas palavras-passe. Esta funcionalidade proporciona uma experiência melhor aos seus utilizadores - menos uma palavra-passe para memorizar - e reduz os custos com o suporte de TI, uma vez que é menos provável que os utilizadores se esqueçam de como iniciar sessão. Quando as pessoas iniciam sessão com o Azure AD, esta funcionalidade valida as respetivas palavras-passe diretamente no seu Active Directory no local.|
|[Proxy de Aplicações do Azure AD](https://aka.ms/deploymentplans/appproxy)|Os colaboradores dos nossos dias querem ser produtivos em qualquer sítio, em qualquer altura e em qualquer dispositivo. Querem trabalhar nos seus próprios dispositivos, quer sejam tablets, telemóveis ou portáteis. E também esperam poder aceder a todas as aplicações, quer aplicações SaaS na cloud, quer aplicações da empresa no local. A disponibilização de acesso a aplicações no local envolvia, tradicionalmente, redes privadas virtuais (VPNs) ou zonas desmilitarizadas (DMZs). Estas soluções não só são complexas e difíceis de proteger, como também são caras em termos de configuração e gestão. Existe uma forma melhor! - Proxy de aplicações do Azure AD|
|[Aprovisionamento de utilizadores](https://aka.ms/UserProvisioningDPDownload)|O Azure AD ajuda-o a automatizar a criação, a manutenção e a remoção de identidades de utilizadores em aplicações da cloud (SaaS), como o Dropbox, o Salesforce, o ServiceNow, entre outras.|
|[Aprovisionamento de utilizadores de entrada baseadas no workday](https://aka.ms/WorkdayDeploymentPlan)|Controlado por workday Inbound aprovisionamento do utilizador do Active Directory cria uma base para a governação de identidade em curso e melhora a qualidade dos processos de negócios que se baseiam nos dados de identidade autoritativas. Utilizar esta funcionalidade, pode facilmente gerir o ciclo de vida de identidade dos funcionários e os operadores de contingência ao configurar as regras que mapeiam os processos de participante-Movimentador-Leaver (por exemplo, transferência de nova contratação, Terminate,) para ações de aprovisionamento de IT (como criar, Enable, Desativar e eliminar contas).|
|[Relatórios e monitoramento](https://aka.ms/deploymentplans/reporting)| O design de sua solução de monitoramento e relatório do Azure AD depende dos seus requisitos legais, de segurança e operacionais, bem como do ambiente e dos processos existentes. Este artigo apresenta as várias opções de design e orienta você para a estratégia de implantação certa.|
