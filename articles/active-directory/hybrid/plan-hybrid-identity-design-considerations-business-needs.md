---
title: Requisitos de identidade para design de identidade de nuvem híbrida Azure | Microsoft Docs
description: Identifique as necessidades comerciais da empresa que o levarão a definir os requisitos para o design de identidade híbrida.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: de690978-84ef-41ad-9dfe-785722d343a1
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 074c203a0a5688855d4f7607a877b25ce6ee6ad7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89660560"
---
# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Determine os requisitos de identidade para a sua solução de identidade híbrida
O primeiro passo para a conceção de uma solução de identidade híbrida é determinar os requisitos para a organização empresarial que alavancará esta solução.  A identidade híbrida começa como um papel de suporte (suporta todas as outras soluções em nuvem fornecendo autenticação) e continua a fornecer novas e interessantes capacidades que desbloqueiam novas cargas de trabalho para os utilizadores.  Estas cargas de trabalho ou serviços que deseja adotar para os seus utilizadores ditarão os requisitos para o design de identidade híbrida.  Estes serviços e cargas de trabalho precisam de alavancar a identidade híbrida tanto no local como na nuvem.  

É preciso passar por estes aspetos fundamentais do negócio para perceber o que é uma exigência agora e o que a empresa planeia para o futuro. Se não tiver a visibilidade da estratégia de longo prazo para o design de identidade híbrida, é provável que a sua solução não seja escalável à medida que as necessidades do negócio crescem e mudam. O diagrama abaixo mostra um exemplo de uma arquitetura de identidade híbrida e as cargas de trabalho que estão a ser desbloqueadas para os utilizadores. Este é apenas um exemplo de todas as novas possibilidades que podem ser desbloqueadas e entregues com uma sólida estratégia de identidade híbrida. 

Alguns componentes que fazem parte da arquitetura híbrida de ![ identidade híbrida arquitetura](./media/plan-hybrid-identity-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Determinar as necessidades do negócio
Cada empresa terá requisitos diferentes, mesmo que estas empresas façam parte da mesma indústria, os requisitos reais do negócio podem variar. Ainda pode aproveitar as melhores práticas da indústria, mas no final são as necessidades de negócio da empresa que o levarão a definir os requisitos para o design de identidade híbrida. 

Certifique-se de responder às seguintes questões para identificar as necessidades do seu negócio:

* A sua empresa quer reduzir os custos operacionais de TI?
* A sua empresa está à procura de garantir ativos em nuvem (aplicações SaaS, infraestrutura)?
* A sua empresa quer modernizar o seu TI?
  * Os seus utilizadores são mais móveis e exigentes em TI para criar exceções no seu DMZ para permitir que diferentes tipos de tráfego acedam a diferentes recursos?
  * A sua empresa possui aplicações antigas que precisavam de ser publicadas para estes utilizadores modernos, mas não são fáceis de reescrever?
  * A sua empresa precisa de realizar todas estas tarefas e de a controlar ao mesmo tempo?
* A sua empresa procura garantir as identidades dos utilizadores e reduzir o risco trazendo novas ferramentas que alavancam a experiência da Microsoft em termos de segurança no local?
* A sua empresa está a tentar livrar-se das temidas contas "externas" no local e movê-las para a nuvem onde já não são uma ameaça dormente dentro do seu ambiente no local?

## <a name="analyze-on-premises-identity-infrastructure"></a>Analisar infraestruturas de identidade no local
Agora que tem uma ideia sobre os requisitos do seu negócio da empresa, precisa de avaliar a sua infraestrutura de identidade no local. Esta avaliação é importante para definir os requisitos técnicos para integrar a sua atual solução de identidade para o sistema de gestão de identidade em nuvem. Certifique-se de responder às seguintes perguntas:

* Que solução de autenticação e autorização utiliza a sua empresa no local? 
* A sua empresa tem atualmente algum serviço de sincronização no local?
* A sua empresa utiliza fornecedores de identidade de terceiros (IdP)?

Também precisa de estar atento aos serviços na nuvem que a sua empresa pode ter. É muito importante fazer uma avaliação para compreender a integração atual com os modelos SaaS, IaaS ou PaaS no seu ambiente. Certifique-se de responder às seguintes perguntas durante esta avaliação:

* A sua empresa tem alguma integração com um fornecedor de serviços na nuvem?
* Se sim, que serviços estão a ser usados?
* Esta integração está atualmente em produção ou é um piloto?

> [!NOTE]
> O Cloud Discovery analisa os seus registos de tráfego contra o catálogo de aplicações cloud security da Microsoft Cloud App Security de mais de 16.000 aplicações cloud que estão classificadas e pontuadas com base em mais de 70 fatores de risco, para lhe proporcionar uma visibilidade contínua no uso da nuvem, Shadow IT, e o risco que shadow IT representa na sua organização. Para começar, consulte [Configurar Cloud Discovery.](/cloud-app-security/set-up-cloud-discovery)
> 
> 

## <a name="evaluate-identity-integration-requirements"></a>Avaliar os requisitos de integração de identidade
Em seguida, é necessário avaliar os requisitos de integração de identidade. Esta avaliação é importante para definir os requisitos técnicos para a forma como os utilizadores vão autenticar, como a presença da organização vai olhar na nuvem, como a organização vai permitir a autorização e qual será a experiência do utilizador. Certifique-se de responder às seguintes perguntas:

* A sua organização vai usar a federação, a autenticação padrão ou ambos?
* A federação é um requisito?  Por causa do seguinte:
  * Kerberos-based SSO (SSO baseado no Kerberos)
  * A sua empresa tem aplicações no local (ou incorporadas dentro ou 3ª parte) que utilizam capacidades de SAML ou de federação semelhantes.
  * MFA via Smart Cards. RSA SecurID, etc.
  * Regras de acesso ao cliente que abordam as questões abaixo:
    1. Posso bloquear todo o acesso externo ao Microsoft 365 com base no endereço IP do cliente?
    2. Posso bloquear todo o acesso externo ao Microsoft 365, exceto o Exchange ActiveSync?
    3. Posso bloquear todo o acesso externo ao Microsoft 365, exceto para aplicações baseadas no navegador (OWA, SPO)
    4. Posso bloquear todo o acesso externo ao Microsoft 365 para membros de grupos de AD designados
* Preocupações de segurança/auditoria
* Já já existe investimento na autenticação federada
* Que nome usará a nossa organização para o nosso domínio na nuvem?
* A organização tem um domínio personalizado?
  1. Esse domínio é público e facilmente verificável via DNS?
  2. Se não for, tem um domínio público que pode ser usado para registar uma UPN alternativa em AD?
* Os identificadores de utilizador são consistentes para a representação na nuvem? 
* A organização tem aplicações que requerem integração com serviços na nuvem?
* A organização tem vários domínios e todos eles usarão a autenticação padrão ou federada?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Avaliar aplicações que funcionam no seu ambiente
Agora que tem uma ideia sobre as suas infraestruturas no local e em nuvem, precisa de avaliar as aplicações que funcionam nestes ambientes. Esta avaliação é importante para definir os requisitos técnicos para integrar estas aplicações no sistema de gestão de identidade em nuvem. Certifique-se de responder às seguintes perguntas:

* Onde vão viver as nossas candidaturas?
* Os utilizadores vão aceder às aplicações no local?  Na nuvem? Ou os dois?
* Há planos para pegar nas cargas de trabalho da aplicação existentes e movê-las para a nuvem?
* Existem planos para desenvolver novas aplicações que residam no local ou na nuvem que utilizará a autenticação em nuvem?

## <a name="evaluate-user-requirements"></a>Avaliar os requisitos do utilizador
Também tem de avaliar os requisitos do utilizador. Esta avaliação é importante para definir os passos que serão necessários para o embarque e assistência aos utilizadores na transição para a nuvem. Certifique-se de responder às seguintes perguntas:

* Os utilizadores vão aceder às aplicações no local?
* Os utilizadores vão aceder às aplicações na nuvem?
* Como é que os utilizadores normalmente fazem login no seu ambiente no local?
* Como é que os utilizadores vão entrar na nuvem?

> [!NOTE]
> Certifique-se de que anota cada resposta e que compreende os fundamentos das mesmas. [Determine os requisitos de resposta](plan-hybrid-identity-design-considerations-incident-response-requirements.md) a incidentes irá sobre as opções disponíveis e prós/contras de cada opção.  Ao responder a essas perguntas, irá selecionar qual a opção que melhor se adequa às necessidades do seu negócio.
> 
> 

## <a name="next-steps"></a>Passos seguintes
[Determinar os requisitos de sincronização do diretório](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Ver também
[Visão geral de considerações de design](plan-hybrid-identity-design-considerations-overview.md)

