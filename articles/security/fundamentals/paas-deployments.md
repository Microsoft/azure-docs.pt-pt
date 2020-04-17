---
title: As melhores práticas para implementações seguras do PaaS - Microsoft Azure
description: Aprenda as melhores práticas para conceber, construir e gerir aplicações seguras em nuvem no Azure e compreenda as vantagens de segurança do PaaS contra outros modelos de serviço na nuvem.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: techlake
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 9adbe7b03283a00f78222ffdc77dca7aaadcbda0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461706"
---
# <a name="securing-paas-deployments"></a>Proteger implementações PaaS

Este artigo fornece informações que o ajudam:

- Compreenda as vantagens de segurança de hospedar aplicações na nuvem
- Avaliar as vantagens de segurança da plataforma como um serviço (PaaS) contra outros modelos de serviço na nuvem
- Mude o seu foco de segurança de uma rede centrada na rede para uma abordagem de segurança centrada na identidade
- Implementar recomendações gerais de boas práticas de segurança paaS

[O desenvolvimento de aplicações seguras no Azure](abstract-develop-secure-apps.md) é um guia geral para as questões e controlos de segurança que deve considerar em cada fase do ciclo de vida de desenvolvimento de software ao desenvolver aplicações para a nuvem.

## <a name="cloud-security-advantages"></a>Vantagens de segurança na nuvem
É importante compreender a [divisão de responsabilidade](shared-responsibility.md) entre si e a Microsoft. No local, és dono de toda a pilha, mas à medida que avanças para a nuvem, algumas responsabilidades transferem para a Microsoft.

Há [vantagens](shared-responsibility.md#cloud-security-advantages)de segurança em estar na nuvem. Num ambiente no local, as organizações provavelmente têm responsabilidades não atendidas e recursos limitados disponíveis para investir em segurança, o que cria um ambiente onde os atacantes são capazes de explorar vulnerabilidades em todas as camadas.

As organizações são capazes de melhorar os seus tempos de deteção e resposta de ameaças usando as capacidades de segurança baseadas na nuvem de um fornecedor e inteligência em nuvem.  Ao transferir responsabilidades para o fornecedor de nuvem, as organizações podem obter mais cobertura de segurança, o que lhes permite realocar recursos de segurança e orçamento para outras prioridades do negócio.

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Vantagens de segurança de um modelo de serviço em nuvem PaaS
Vamos ver as vantagens de segurança de um destacamento azure PaaS contra no local.

![Vantagens de segurança do PaaS](./media/paas-deployments/advantages-of-paas.png)

Começando na parte inferior da pilha, a infraestrutura física, a Microsoft atenua riscos e responsabilidades comuns. Como a nuvem da Microsoft é continuamente monitorizada pela Microsoft, é difícil de atacar. Não faz sentido um intruso perseguir a nuvem da Microsoft como alvo. A menos que o agressor tenha muito dinheiro e recursos, é provável que o agressor passe para outro alvo.  

No meio da pilha, não há diferença entre uma implantação do PaaS e no local. Na camada de aplicação e na camada de conta e de gestão de acesso, tem riscos semelhantes. Na secção de passos seguintes deste artigo, vamos guiá-lo às melhores práticas para eliminar ou minimizar estes riscos.

No topo da pilha, governação de dados e gestão de direitos, assume um risco que pode ser atenuado pela gestão chave. (A gestão chave está coberta de boas práticas.) Embora a gestão chave seja uma responsabilidade adicional, você tem áreas numa implementação paaS que você já não precisa gerir para que você possa transferir recursos para uma gestão chave.

A plataforma Azure também lhe fornece uma forte proteção DDoS utilizando várias tecnologias baseadas em rede. No entanto, todos os tipos de métodos de proteção de DDoS baseados em rede têm os seus limites numa base por ligação e por datacenter. Para ajudar a evitar o impacto de grandes ataques dDoS, pode aproveitar a capacidade de nuvem central do Azure de permitir que você se esforce rapidamente e automaticamente para se defender contra ataques DDoS. Vamos entrar em mais detalhes sobre como pode fazer isso nos artigos de práticarecomendados.

## <a name="modernizing-the-defenders-mindset"></a>Modernizar a mentalidade do defensor
Com as implementações do PaaS surgem uma mudança na sua abordagem geral à segurança. Mudas de precisar de controlar tudo sozinho para partilhar responsabilidades com a Microsoft.

Outra diferença significativa entre o PaaS e as tradicionais implantações no local, é uma nova visão do que define o perímetro de segurança primário. Historicamente, o principal perímetro de segurança no local era a sua rede e a maioria dos projetos de segurança no local usam a rede como o seu principal pivô de segurança. Para os destacamentos paas, é melhor servido considerando a identidade como o perímetro de segurança primário.

## <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Adotar uma política de identidade como o perímetro de segurança principal
Uma das cinco características essenciais da computação em nuvem é o acesso amplo à rede, o que torna o pensamento centrado na rede menos relevante. O objetivo de grande parte da computação em nuvem é permitir que os utilizadores acedam a recursos independentemente da localização. Para a maioria dos utilizadores, a sua localização estará algures na Internet.

O seguinte número mostra como o perímetro de segurança evoluiu de um perímetro de rede para um perímetro de identidade. A segurança torna-se menos sobre defender a sua rede e mais sobre a defesa dos seus dados, bem como sobre a gestão da segurança das suas apps e utilizadores. A principal diferença é que quer aproximar a segurança do que é importante para a sua empresa.

![Identidade como novo perímetro de segurança](./media/paas-deployments/identity-perimeter.png)

Inicialmente, os serviços Azure PaaS (por exemplo, funções web e Azure SQL) forneceram pouca ou nenhuma defesa tradicional do perímetro da rede. Entendeu-se que o objetivo do elemento era ser exposto à Internet (papel web) e que a autenticação fornece o novo perímetro (por exemplo, BLOB ou Azure SQL).

As práticas de segurança modernas assumem que o adversário invadiu o perímetro da rede. Por conseguinte, as práticas modernas de defesa mudaram-se para a identidade. As organizações devem estabelecer um perímetro de segurança baseado na identidade com forte autenticação e higiene de autorização (boas práticas).

Há décadas que os princípios e padrões para o perímetro da rede estão disponíveis. Em contraste, a indústria tem relativamente menos experiência em usar a identidade como perímetro de segurança primário. Com isto dito, acumulamos experiência suficiente para fornecer algumas recomendações gerais que estão comprovadas no campo e se aplicam a quase todos os serviços paaS.

Seguem-se as melhores práticas de gestão do perímetro de identidade.

**Boas práticas**: Proteja as chaves e credenciais para proteger a sua implementação paaS.   
**Detalhe**: Perder chaves e credenciais é um problema comum. Pode utilizar uma solução centralizada onde chaves e segredos podem ser armazenados em módulos de segurança de hardware (HSMs). [O Azure Key Vault](../../key-vault/general/overview.md) salvaguarda as suas chaves e segredos encriptando chaves de autenticação, chaves de conta de armazenamento, chaves de encriptação de dados, ficheiros .pfx e palavras-passe usando chaves protegidas por HSMs.

**Boas práticas**: Não coloque credenciais e outros segredos no código fonte ou gitHub.   
**Detalhe**: A única coisa pior do que perder as chaves e credenciais é ter uma parte não autorizada a ter acesso a elas. Os atacantes podem aproveitar as tecnologias do bot para encontrar chaves e segredos armazenados em repositórios de código como o GitHub. Não coloque segredos e segredos nestes repositórios de código público.

**Boas práticas**: Proteja as interfaces de gestão vM nos serviços híbridos PaaS e IaaS utilizando uma interface de gestão que lhe permite gerir remotamente estes VMs diretamente.   
**Detalhe**: Podem ser utilizados protocolos de gestão remota como [SSH,](https://en.wikipedia.org/wiki/Secure_Shell) [RDP](https://support.microsoft.com/kb/186607)e [Remo PowerShell.](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) Em geral, recomendamos que não permita o acesso remoto direto a VMs a partir da internet.

Se possível, utilize abordagens alternativas como a utilização de redes privadas virtuais numa rede virtual Azure. Se não houver abordagens alternativas, certifique-se de que utiliza frases-passe complexas e autenticação de dois fatores (como a [autenticação de multifactor estoque azure).](/azure/active-directory/authentication/multi-factor-authentication)

**Boas práticas**: Utilize plataformas de autenticação e autorização fortes.   
**Detalhe**: Utilize identidades federadas em Azure AD em vez de lojas de utilizadores personalizadas. Quando utiliza identidades federadas, aproveita-se de uma abordagem baseada na plataforma e delega a gestão de identidades autorizadas aos seus parceiros. Uma abordagem de identidade federada é especialmente importante quando os colaboradores são despedidos e essa informação deve ser refletida através de múltiplos sistemas de identidade e autorização.

Utilize mecanismos de autenticação e autorização fornecidos pela plataforma em vez de código personalizado. A razão é que desenvolver código de autenticação personalizado pode ser propenso a erros. A maioria dos seus desenvolvedores não são especialistas em segurança e é improvável que estejam cientes das subtilezas e dos mais recentes desenvolvimentos na autenticação e autorização. O código comercial (por exemplo, da Microsoft) é frequentemente amplamente revisto em segurança.

Use a autenticação de dois fatores. A autenticação de dois fatores é a norma atual para a autenticação e autorização porque evita as falhas de segurança inerentes ao nome de utilizador e tipos de autenticação de palavra-passe. O acesso às interfaces de gestão Azure (portal/powerShell remoto) e aos serviços orientados para o cliente deve ser concebido e configurado para utilizar a [Autenticação Multi-Factor Azure](/azure/active-directory/authentication/multi-factor-authentication).

Utilize protocolos de autenticação padrão, tais como OAuth2 e Kerberos. Estes protocolos foram amplamente revistos pelos pares e são provavelmente implementados como parte das bibliotecas da sua plataforma para autenticação e autorização.

## <a name="use-threat-modeling-during-application-design"></a>Utilize modelação de ameaça durante o design da aplicação
O Microsoft [Security Development Lifecycle](https://www.microsoft.com/en-us/sdl) especifica que as equipas devem envolver-se num processo chamado modelação de ameaças durante a fase de design. Para ajudar a facilitar este processo, a Microsoft criou a Ferramenta de [Modelação de Ameaças SDL](/azure/security/azure-security-threat-modeling-tool). Modelar o design da aplicação e enumerar ameaças [de STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) em todos os limites de confiança pode pegar erros de design desde cedo.

A tabela seguinte enumera as ameaças do STRIDE e dá alguns exemplos de atenuações que utilizam as características do Azure. Estas mitigações não funcionarão em todas as situações.

| Ameaça | Propriedade de segurança | Potenciais mitigações da plataforma Azure |
| --- | --- | --- |
| Spoofing | Autenticação | Requerer ligações HTTPS. |
| Adulteração | Integridade | Valide os certificados TLS/SSL. |
| Repúdio | Não-repústação | Ativar [a monitorização e diagnóstico](/azure/architecture/best-practices/monitoring)do Azure. |
| Divulgação de informação | Confidencialidade | Criptografe dados sensíveis em repouso utilizando [certificados](/rest/api/appservice/certificates)de serviço . |
| Denial of service | Disponibilidade | Monitorize as métricas de desempenho para potenciais condições de negação de serviço. Implementar filtros de ligação. |
| Elevação do privilégio | Autorização | Utilizar [a Gestão de Identidade Privilegiada.](/azure/active-directory/privileged-identity-management/subscription-requirements) |

## <a name="develop-on-azure-app-service"></a>Desenvolver no Serviço de Aplicações Azure
[O Azure App Service](/azure/app-service/overview) é uma oferta PaaS que permite criar aplicações web e móveis para qualquer plataforma ou dispositivo e conectar-se a dados em qualquer lugar, na nuvem ou no local. O Serviço de Aplicações inclui as capacidades web e móveis que foram previamente entregues separadamente como Websites Azure e Serviços Móveis Azure. Também inclui novas capacidades para automatizar processos de negócio e o alojar APIs da nuvem. Como um único serviço integrado, o App Service traz um conjunto rico de capacidades para cenários web, móveis e integração.

Seguem-se as melhores práticas para utilizar o Serviço de Aplicações.

**Boas práticas**: [Autenticar através do Diretório Ativo Azure.](/azure/app-service/overview-authentication-authorization)   
**Detalhe**: O Serviço de Aplicações fornece um serviço OAuth 2.0 para o seu fornecedor de identidade. O OAuth 2.0 foca-se na simplicidade do desenvolvedor de clientes, ao mesmo tempo que fornece fluxos de autorização específicos para aplicações web, aplicações de desktop e telemóveis. A Azure AD utiliza o OAuth 2.0 para permitir autorizar o acesso a aplicações móveis e web.

**Boas práticas**: Restringir o acesso com base na necessidade de conhecer e menos privilegiar os princípios de segurança.   
**Detalhe**: Restringir o acesso é imperativo para as organizações que querem impor políticas de segurança para o acesso de dados. Pode utilizar o RBAC para atribuir permissões a utilizadores, grupos e aplicações num determinado âmbito. Para saber mais sobre a concessão de acesso aos utilizadores a aplicações, consulte [Iniciar-se com a gestão de acesso.](/azure/role-based-access-control/overview)

**Boas práticas**: Proteja as chaves.   
**Detalhe**: O Cofre chave Azure ajuda a salvaguardar chaves criptográficas e segredos que as aplicações e serviços em nuvem usam. Com o Key Vault, pode encriptar chaves e segredos (tais como chaves de autenticação, chaves de conta de armazenamento, chaves de encriptação de dados, . Ficheiros PFX e palavras-passe) utilizando chaves protegidas por módulos de segurança de hardware (HSMs). Para maior segurança, pode importar ou gerar chaves nos HSMs. Consulte [o Cofre chave Azure](/azure/key-vault/key-vault-overview) para saber mais. Também pode utilizar o Key Vault para gerir os seus certificados TLS com renovação automática.

**Boas práticas**: Restringir os endereços IP de origem.   
**Detalhe**: [App Service Environment](/azure/app-service/environment/intro) tem uma funcionalidade de integração de rede virtual que o ajuda a restringir os endereços IP de origem através de grupos de segurança de rede. As redes virtuais permitem colocar os recursos do Azure numa rede não internet e enredaível a que controla o acesso. Para saber mais, consulte [Integrar a sua app com uma rede virtual Azure.](/azure/app-service/web-sites-integrate-with-vnet)

**Boas práticas**: Monitorize o estado de segurança dos ambientes do seu Serviço de Aplicações.   
**Detalhe**: Utilize o Centro de Segurança Azure para monitorizar os ambientes do serviço de aplicações. Quando o Security Center identifica potenciais vulnerabilidades de segurança, cria [recomendações](../../security-center/security-center-virtual-machine-protection.md) que o guiam através do processo de configuração dos controlos necessários.

> [!NOTE]
> O Serviço de Aplicações de Monitorização encontra-se em pré-visualização e está disponível apenas no [nível Standard](/azure/security-center/security-center-pricing) de Security Center.
>
>

## <a name="install-a-web-application-firewall"></a>Instale uma firewall de aplicação web
Cada vez mais, as aplicações Web são alvo de ataques maliciosos que exploram vulnerabilidades conhecidas comuns. Destas vulnerabilidades, são frequentes os ataques de injeção de SQL, scripting entre sites, entre muitas outras. Impedir este tipo de ataques ao código das aplicações constitui um desafio e exige uma manutenção, correção e monitorização rigorosas em muitas camadas da topologia da aplicação. Uma firewall de aplicações Web centralizada ajuda a simplificar em muito a gestão da segurança e confere aos administradores de aplicações uma maior garantia de proteção contra as ameaças ou intrusões. Uma solução WAF também pode reagir mais rapidamente a uma ameaça de segurança ao corrigir uma vulnerabilidade conhecida numa localização central, em vez de proteger cada uma das aplicações Web individualmente. Os gateways de aplicações existentes podem ser facilmente convertidos num gateway de aplicação com firewall de aplicações Web ativada.

Firewall de [aplicação web (WAF)](/azure/frontdoor/waf-overview) é uma característica do Application Gateway que fornece proteção centralizada das suas aplicações web a partir de explorações e vulnerabilidades comuns. A WAF baseia-se em regras da regra central do Projeto de Segurança de [Aplicações Web Abertas (OWASP) definidas](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9.

## <a name="monitor-the-performance-of-your-applications"></a>Monitorize o desempenho das suas aplicações
A monitorização é o ato de recolher e analisar dados para determinar o desempenho, saúde e disponibilidade da sua aplicação. Uma estratégia de monitorização efetiva ajuda-o a compreender o funcionamento em detalhe dos componentes da sua aplicação. Ajuda-o a aumentar o seu tempo de uptime notificando-o de questões críticas para que possa resolvê-los antes que se tornem problemas. Também ajuda a detetar anomalias que possam estar relacionadas com a segurança.

Utilize insights de [aplicação Azure](https://azure.microsoft.com/documentation/services/application-insights) para monitorizar a disponibilidade, desempenho e utilização da sua aplicação, quer esteja alojada na nuvem ou no local. Ao utilizar os Insights da Aplicação, pode identificar e diagnosticar rapidamente erros na sua aplicação sem esperar que um utilizador os reporte. Com as informações que recolher, pode fazer escolhas informadas sobre a manutenção e as melhorias da aplicação.

O Application Insights tem um vasto conjunto ferramentas para interagir com os dados que recolhe. O Application Insights armazena os dados num repositório comum. Pode tirar partido da funcionalidade partilhada, como alertas, dashboards e análise profunda com a linguagem de consulta kusto.

## <a name="perform-security-penetration-testing"></a>Realizar testes de penetração de segurança
Validar defesas de segurança é tão importante como testar qualquer outra funcionalidade. Faça dos testes de [penetração](pen-testing.md) uma parte padrão do seu processo de construção e implementação. Agende testes regulares de segurança e verificação de vulnerabilidades em aplicações implementadas e monitorize para portas abertas, pontos finais e ataques.

O teste fuzz é um método para encontrar falhas no programa (erros de código) fornecendo dados de entrada mal formados para interfaces de programa (pontos de entrada) que analisam e consomem estes dados. [A Microsoft Security Risk Detection](https://www.microsoft.com/en-us/security-risk-detection/) é uma ferramenta baseada na nuvem que pode utilizar para procurar bugs e outras vulnerabilidades de segurança no seu software antes de a implementar no Azure. A ferramenta foi concebida para capturar vulnerabilidades antes de implementar software para que não tenha de corrigir um bug, lidar com falhas ou responder a um ataque após o lançamento do software.


## <a name="next-steps"></a>Passos seguintes
Neste artigo, focamo-nos nas vantagens de segurança de uma implantação do Azure PaaS e das melhores práticas de segurança para aplicações na nuvem. Em seguida, aprenda as práticas recomendadas para garantir as suas soluções web e móveis PaaS utilizando serviços azure específicos. Começaremos com o Azure App Service, Azure SQL Database e Azure SQL Data Warehouse e Azure Storage. À medida que os artigos sobre práticas recomendadas para outros serviços do Azure ficarem disponíveis, serão fornecidas ligações na seguinte lista:

- [Serviço de Aplicações do Azure](paas-applications-using-app-services.md)
- [Base de Dados Azure SQL e Armazém de Dados Azure SQL](paas-applications-using-sql.md)
- [Armazenamento Azure](paas-applications-using-storage.md)
- Cache do Azure para Redis
- Service Bus do Azure
- Firewalls de aplicação web

Consulte [o Desenvolvimento de aplicações seguras no Azure](abstract-develop-secure-apps.md) para questões e controlos de segurança que deve considerar em cada fase do ciclo de vida de desenvolvimento de software ao desenvolver aplicações para a nuvem.

Consulte as [melhores práticas e padrões](best-practices-and-patterns.md) de segurança azure para obter mais práticas de segurança para usar quando está a projetar, implementar e gerir as suas soluções em nuvem utilizando o Azure.

Os seguintes recursos estão disponíveis para fornecer informações mais gerais sobre segurança Azure e serviços relacionados com a Microsoft:
* [Azure Security Team Blog](https://blogs.msdn.microsoft.com/azuresecurity/) - para informações atualizadas sobre as últimas novidades em Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) - onde as vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, podem ser reportadas ou via e-mail parasecure@microsoft.com
