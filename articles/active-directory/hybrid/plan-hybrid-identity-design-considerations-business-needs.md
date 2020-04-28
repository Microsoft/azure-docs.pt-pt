---
title: Requisitos de identidade para design de identidade de nuvem híbrida Azure [ Microsoft Docs
description: Identifique as necessidades de negócio da empresa que o levarão a definir os requisitos para o design de identidade híbrida.
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
ms.openlocfilehash: 9ecc90e13f49c231d8d3ab0cff1de91443b80f21
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "65950892"
---
# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Determine os requisitos de identidade para a sua solução de identidade híbrida
O primeiro passo para a conceção de uma solução de identidade híbrida é determinar os requisitos para a organização empresarial que irá alavancar esta solução.  A identidade híbrida começa como uma função de suporte (suporta todas as outras soluções na nuvem fornecendo autenticação) e continua a fornecer novas e interessantes capacidades que desbloqueiam novas cargas de trabalho para os utilizadores.  Estas cargas de trabalho ou serviços que deseja adotar para os seus utilizadores ditarão os requisitos para o design de identidade híbrida.  Estes serviços e cargas de trabalho precisam de alavancar a identidade híbrida tanto no local como na nuvem.  

É preciso rever estes aspectos-chave do negócio para perceber o que é agora um requisito e o que a empresa planeia para o futuro. Se não tiver a visibilidade da estratégia de longo prazo para o design de identidade híbrida, é provável que a sua solução não seja escalável à medida que o negócio precisa crescer e mudar. O diagrama abaixo mostra um exemplo de uma arquitetura de identidade híbrida e as cargas de trabalho que estão sendo desbloqueadas para os utilizadores. Este é apenas um exemplo de todas as novas possibilidades que podem ser desbloqueadas e entregues com uma sólida estratégia de identidade híbrida. 

Alguns componentes que fazem parte ![da arquitetura híbrida de identidade híbrida de identidade híbrida](./media/plan-hybrid-identity-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Determinar as necessidades empresariais
Cada empresa terá requisitos diferentes, mesmo que estas empresas façam parte do mesmo setor, os requisitos reais do negócio podem variar. Ainda pode aproveitar as melhores práticas da indústria, mas, em última análise, são as necessidades de negócio da empresa que o levarão a definir os requisitos para o design de identidade híbrida. 

Certifique-se de responder às seguintes questões para identificar as necessidades do seu negócio:

* A sua empresa está a tentar reduzir os custos operacionais de TI?
* A sua empresa está à procura de ativos na nuvem (aplicações SaaS, infraestrutura)?
* A sua empresa está a tentar modernizar a sua TI?
  * Os seus utilizadores são mais móveis e exigem que as TI criem exceções no seu DMZ para permitir que diferentes tipos de tráfego acedam a diferentes recursos?
  * A sua empresa tem aplicações antigas que precisavam de ser publicadas para estes utilizadores modernos, mas não são fáceis de reescrever?
  * A sua empresa precisa de realizar todas estas tarefas e de a controlar ao mesmo tempo?
* A sua empresa está a tentar proteger as identidades dos utilizadores e reduzir o risco trazendo novas ferramentas que aproveitem a experiência da experiência de segurança azure da Microsoft no local?
* A sua empresa está a tentar livrar-se das temidas contas "externas" nas instalações e movê-las para a nuvem onde já não são uma ameaça adormecida dentro do seu ambiente no local?

## <a name="analyze-on-premises-identity-infrastructure"></a>Analisar infraestruturas de identidade no local
Agora que tem uma ideia sobre os requisitos do negócio da sua empresa, precisa avaliar a sua infraestrutura de identidade no local. Esta avaliação é importante para definir os requisitos técnicos para integrar a sua atual solução de identidade no sistema de gestão de identidade na nuvem. Certifique-se de responder às seguintes perguntas:

* Que solução de autenticação e autorização a sua empresa utiliza no local? 
* A sua empresa tem atualmente algum serviço de sincronização no local?
* A sua empresa utiliza fornecedores de identidade de terceiros (IDP)?

Também precisa de estar ciente dos serviços na nuvem que a sua empresa pode ter. Realizar uma avaliação para compreender a atual integração com os modelos SaaS, IaaS ou PaaS no seu ambiente é muito importante. Certifique-se de responder às seguintes perguntas durante esta avaliação:

* A sua empresa tem alguma integração com um fornecedor de serviços na nuvem?
* Se sim, que serviços estão a ser usados?
* Esta integração está atualmente em produção ou é piloto?

> [!NOTE]
> O Cloud Discovery analisa os seus registos de tráfego contra o catálogo de aplicações cloud da Microsoft Cloud App Security de mais de 16.000 aplicações em nuvem que estão classificadas e pontuadas com base em mais de 70 fatores de risco, para lhe proporcionar visibilidade contínua para o uso da nuvem, Shadow IT, e o risco que shadow IT coloca na sua organização. Para começar a ver [Configurar a Cloud Discovery.](/cloud-app-security/set-up-cloud-discovery)
> 
> 

## <a name="evaluate-identity-integration-requirements"></a>Avaliar os requisitos de integração de identidade
Em seguida, é necessário avaliar os requisitos de integração de identidade. Esta avaliação é importante para definir os requisitos técnicos para a forma como os utilizadores vão autenticar, como a presença da organização vai ficar na nuvem, como a organização permitirá a autorização e qual será a experiência do utilizador. Certifique-se de responder às seguintes perguntas:

* A sua organização estará a usar a federação, a autenticação padrão ou ambas?
* A federação é um requisito?  Por causa do seguinte:
  * Kerberos-based SSO (SSO baseado no Kerberos)
  * A sua empresa tem aplicações no local (construídas internamente ou em terceiro) que utilizam capacidades saml ou federação semelhantes.
  * MFA via Smart Cards. RSA SecurID, etc.
  * Regras de acesso ao cliente que abordam as questões abaixo:
    1. Posso bloquear todo o acesso externo ao Office 365 com base no endereço IP do cliente?
    2. Posso bloquear todo o acesso externo ao Office 365, exceto o Exchange ActiveSync?
    3. Posso bloquear todo o acesso externo ao Office 365, exceto para aplicações baseadas no navegador (OWA, SPO)
    4. Posso bloquear todo o acesso externo ao Office 365 para membros de grupos addesignados
* Preocupações de segurança/auditoria
* Já existente investimento na autenticação federada
* Que nome usará a nossa organização para o nosso domínio na nuvem?
* A organização tem um domínio personalizado?
  1. Este domínio é público e facilmente verificável via DNS?
  2. Se não for, então tem um domínio público que pode ser usado para registar uma UPN alternativa em AD?
* Os identificadores do utilizador são consistentes para a representação na nuvem? 
* A organização tem aplicações que requerem integração com serviços na nuvem?
* A organização tem vários domínios e todos usarão a autenticação padrão ou federada?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Avaliar aplicações que funcionam no seu ambiente
Agora que tem uma ideia sobre as suas instalações e infraestruturas em nuvem, precisa avaliar as aplicações que funcionam nestes ambientes. Esta avaliação é importante para definir os requisitos técnicos para integrar estas aplicações no sistema de gestão de identidade na nuvem. Certifique-se de responder às seguintes perguntas:

* Onde vão viver as nossas candidaturas?
* Os utilizadores vão aceder às aplicações no local?  Na nuvem? Ou os dois?
* Há planos para pegar nas cargas de trabalho de aplicação existentes e movê-las para a nuvem?
* Existem planos para desenvolver novas aplicações que residam no local ou na nuvem que usarão a autenticação em nuvem?

## <a name="evaluate-user-requirements"></a>Avaliar os requisitos do utilizador
Também tem de avaliar os requisitos do utilizador. Esta avaliação é importante para definir os passos que serão necessários para o embarque e assistência aos utilizadores à medida que transitem para a nuvem. Certifique-se de responder às seguintes perguntas:

* Os utilizadores vão aceder às aplicações no local?
* Os utilizadores vão aceder a aplicações na nuvem?
* Como é que os utilizadores normalmente iniciam sessão no seu ambiente no local?
* Como é que os utilizadores vão entrar na nuvem?

> [!NOTE]
> Certifique-se de que anota cada resposta e que compreende os fundamentos das mesmas. Determinar os requisitos de resposta a [incidentes](plan-hybrid-identity-design-considerations-incident-response-requirements.md) irá passar pelas opções disponíveis e prós/contras de cada opção.  Ao responder a essas perguntas, irá selecionar qual a opção que melhor se adequa às suas necessidades de negócio.
> 
> 

## <a name="next-steps"></a>Passos seguintes
[Determinar os requisitos de sincronização do diretório](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Consulte também
[Visão geral das considerações de conceção](plan-hybrid-identity-design-considerations-overview.md)

