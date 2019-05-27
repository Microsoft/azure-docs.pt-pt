---
title: Os requisitos de identidade híbrida na cloud design de identidade do Azure | Documentos da Microsoft
description: Identifica necessidades de negócio da empresa, que irão originar a definir os requisitos para o design de identidade híbrida.
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
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65950892"
---
# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Determinar os requisitos de identidade para a sua solução de identidade híbrida
A primeira etapa na criação de uma solução de identidade híbrida é determinar os requisitos para a organização de negócios que tirar partido desta solução.  Identidade híbrida é iniciado como uma função de suporte (suporta todas as outras soluções na cloud, fornecendo autenticação) e prosseguirá para fornecer recursos novos e interessantes, que desbloqueiam novas cargas de trabalho para os utilizadores.  Estas cargas de trabalho ou serviços que deseja adotar para os seus utilizadores vão ditar os requisitos para o design de identidade híbrida.  Estes serviços e cargas de trabalho precisam tirar partido de identidade híbrida no local e na cloud.  

Tem de rever estes aspetos-chave da empresa para compreender o que é um requisito agora e o que a empresa planeia para o futuro. Se não tiver a visibilidade da estratégia de longo prazo para o design da identidade híbrida, é provável que sua solução não será dimensionável à medida que a empresa necessidades mudam. O diagrama abaixo mostra um exemplo de uma arquitetura de identidade híbrida e as cargas de trabalho que estão a ser desbloqueadas para os utilizadores. Este é apenas um exemplo de todos os recursos novos que podem ser desbloqueadas e entregues com uma estratégia de identidade híbrida sólido. 

Alguns componentes que fazem parte da arquitetura de identidade híbrida ![arquitetura de identidade híbrida](./media/plan-hybrid-identity-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Determinar as necessidades de negócio
Cada empresa tem requisitos diferentes, mesmo que essas empresas façam parte da mesma indústria, os requisitos podem variar de negócios real. Ainda pode tirar partido das melhores práticas da indústria, mas acaba sendo necessidades de negócio da empresa, que irão originar a definir os requisitos para o design de identidade híbrida. 

Certifique-se responder às perguntas seguintes para identificar as necessidades da sua empresa:

* A sua empresa está à procura de cortar custos operacionais de TI?
* É da sua empresa está à procura para proteger recursos na cloud (aplicações SaaS, infra-estrutura)?
* É da sua empresa está à procura de modernizar o seu departamento de TI?
  * Os seus utilizadores estão mais exigentes e móveis que a TI criar exceções em sua rede de Perímetro para permitir que um tipo diferente de tráfego para aceder a recursos diferentes?
  * A sua empresa tem aplicações legadas que precisavam ser publicado para estes utilizadores modernas, mas não são fáceis de reescrever?
  * A sua empresa precisa realizar todas essas tarefas e colocá-lo sob o controle ao mesmo tempo?
* A sua empresa está à procura para proteger as identidades dos utilizadores e reduzir o risco com trazendo novas ferramentas que tiram partido dos conhecimentos do segurança do Azure conhecimentos no local da Microsoft?
* É a sua empresa tentar se livrar das contas abominável "externas" no local e movê-los para a cloud em que já não são uma ameaça inativo dentro de seu ambiente no local?

## <a name="analyze-on-premises-identity-infrastructure"></a>Analisar a infraestrutura de identidade no local
Agora que tem uma ideia sobre os requisitos de negócio da empresa, terá de avaliar a sua infraestrutura de identidade no local. Essa avaliação é importante para definir os requisitos técnicos para integrar a sua solução de identidade atual para o sistema de gestão de identidade na cloud. Certifique-se responder às seguintes perguntas:

* Que solução de autenticação e autorização faz a sua empresa utilizam no local? 
* A sua empresa tem atualmente qualquer serviço de sincronização no local?
* A sua empresa utiliza quaisquer fornecedores de identidade (IdP) de terceiros?

Terá também de ter em consideração os serviços cloud que sua empresa pode ter. Realizar uma avaliação para compreender a integração atual com modelos de SaaS, IaaS ou PaaS no seu ambiente é muito importante. Certifique-se responder às seguintes questões durante essa avaliação:

* A sua empresa tem qualquer integração com o fornecedor de serviços cloud?
* Se Sim, quais serviços estão a ser utilizados?
* Se esta integração atualmente em produção ou é um piloto?

> [!NOTE]
> Cloud Discovery analisa os seus registos de tráfego no catálogo de aplicações da Microsoft Cloud App Security cloud da cloud mais de 16 000 aplicações que são classificadas e pontuadas com base em mais de 70 fatores de risco, para fornecer visibilidade contínua para cloud utilizar, Shadow IT e o risco Desafio IT cópias sombra em sua organização. Para começar, consulte [configurar a Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).
> 
> 

## <a name="evaluate-identity-integration-requirements"></a>Avaliar requisitos de integração de identidade
Em seguida, terá de avaliar os requisitos de integração de identidade. Essa avaliação é importante definir os requisitos técnicos para como os usuários serão autenticados, como a presença da organização será exibida na cloud, como a organização permitirá que a autorização e o que a experiência de utilizador vai ser. Certifique-se responder às seguintes perguntas:

* Sua organização utilizará Federação, a autenticação padrão ou ambos?
* É um requisito de Federação?  Devido às seguintes:
  * SSO com base em Kerberos
  * A sua empresa tem uma aplicações no local (seja criado internamente ou 3rd party) que utiliza o SAML ou as funcionalidades de Federação semelhantes.
  * MFA através de cartões inteligentes. RSA SecurID, etc.
  * Regras de acesso de cliente que abordam as perguntas abaixo:
    1. Pode bloquear todo o acesso externo ao Office 365 com base no endereço IP do cliente?
    2. Pode bloquear todo o acesso externo ao Office 365, exceto Exchange ActiveSync?
    3. Posso bloquear todos os acessos externos ao Office 365, exceto aplicações baseadas no browser (OWA, SPO)
    4. Pode bloquear todo o acesso externo ao Office 365 para membros de grupos do AD designados
* Preocupações com a auditoria de segurança /
* Investimento já existente em autenticação federada
* O nome de nossa organização irá utilizar para nosso domínio na cloud?
* A organização tem um domínio personalizado?
  1. É nesse domínio público e facilmente verificável através de DNS?
  2. Se não for, em seguida, tem um domínio público que pode ser utilizado para registar um UPN alternativo no AD?
* São os identificadores de utilizador consistente para representação de cloud? 
* A organização tem aplicações que requerem integração com os serviços cloud?
* A organização tem vários domínios e todos eles utiliza autenticação federada ou padrão?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Avaliar aplicativos executados no seu ambiente
Agora que tiver uma idéia quanto no local e infraestrutura na cloud, é necessário avaliar os aplicativos executados nesses ambientes. Essa avaliação é importante definir os requisitos técnicos para integrar estas aplicações para o sistema de gestão de identidade na cloud. Certifique-se responder às seguintes perguntas:

* Onde encontrará a nossos aplicativos?
* Os utilizadores acederão a aplicações no local?  Na cloud? Ou ambos?
* Existem planos para levar as cargas de trabalho de aplicação existente e movê-los para a cloud?
* Existem planos para desenvolver novos aplicativos que residem no local ou na cloud que irá utilizar na cloud autenticação?

## <a name="evaluate-user-requirements"></a>Avaliar requisitos de utilizador
Também tem que avaliar os requisitos de utilizador. Essa avaliação é importante definir os passos que serão necessários para inclusão e prestar assistência aos utilizadores como eles fazem a transição para a cloud. Certifique-se responder às seguintes perguntas:

* Os utilizadores acederão a aplicações no local?
* Os usuários acessarão os aplicativos em nuvem?
* Como fazer os utilizadores, normalmente, inicie sessão no respetivo ambiente no local?
* Como irão os utilizadores início de sessão para a cloud?

> [!NOTE]
> Lembre-se de que anota cada resposta e compreender as razões implícitas para a resposta. [Determinar os requisitos de resposta a incidentes](plan-hybrid-identity-design-considerations-incident-response-requirements.md) abordará as opções disponíveis e os prós/contras de cada opção.  Ao responder a essas questões, vai selecionar qual opção melhor se adequa aos seu negócio precisa.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
[Determinar requisitos de sincronização de diretórios](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Consulte também
[Descrição geral das considerações de design](plan-hybrid-identity-design-considerations-overview.md)

