---
title: Melhores práticas para implementações seguras do PaaS - Microsoft Azure
description: Aprenda as melhores práticas para projetar, construir e gerir aplicações seguras em nuvem no Azure e compreenda as vantagens de segurança do PaaS contra outros modelos de serviços em nuvem.
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
ms.openlocfilehash: f5ae1f7f84293fdf23b680a407a5a168316f3163
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94407993"
---
# <a name="securing-paas-deployments"></a>Proteger implementações PaaS

Este artigo fornece informações que o ajudam:

- Compreenda as vantagens de segurança de hospedar aplicações na nuvem
- Avalie as vantagens de segurança da plataforma como um serviço (PaaS) contra outros modelos de serviços em nuvem
- Mude o seu foco de segurança de uma abordagem de segurança centrada na rede para uma abordagem de segurança centrada na identidade
- Implementar recomendações gerais de segurança PaaS

[O desenvolvimento de aplicações seguras no Azure](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/) é um guia geral para as questões e controlos de segurança que deve considerar em cada fase do ciclo de vida de desenvolvimento do software ao desenvolver aplicações para a nuvem.

## <a name="cloud-security-advantages"></a>Vantagens de segurança na nuvem
É importante compreender a [divisão de responsabilidade](shared-responsibility.md) entre si e a Microsoft. No local, és dono de toda a pilha, mas à medida que avanças para a nuvem, algumas responsabilidades transferem-se para a Microsoft.

Existem [vantagens de segurança em estar na nuvem.](shared-responsibility.md#cloud-security-advantages) Num ambiente no local, as organizações provavelmente têm responsabilidades insatisfeitas e recursos limitados disponíveis para investir em segurança, o que cria um ambiente onde os atacantes são capazes de explorar vulnerabilidades em todas as camadas.

As organizações são capazes de melhorar os seus tempos de deteção e resposta de ameaças usando as capacidades de segurança baseadas na nuvem de um fornecedor e a inteligência na nuvem.  Ao transferir responsabilidades para o fornecedor de nuvem, as organizações podem obter mais cobertura de segurança, o que lhes permite realocar recursos de segurança e orçamento para outras prioridades do negócio.

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Vantagens de segurança de um modelo de serviço de nuvem PaaS
Vamos ver as vantagens de segurança de um destacamento Azure PaaS contra no local.

![Vantagens de segurança do PaaS](./media/paas-deployments/advantages-of-paas.png)

A partir da parte inferior da pilha, a infraestrutura física, a Microsoft atenua os riscos e responsabilidades comuns. Como a nuvem da Microsoft é continuamente monitorizada pela Microsoft, é difícil de atacar. Não faz sentido um intruso perseguir a nuvem da Microsoft como alvo. A menos que o agressor tenha muito dinheiro e recursos, é provável que o agressor passe para outro alvo.  

No meio da pilha, não há diferença entre uma implantação paaS e no local. Na camada de aplicação e na camada de gestão de conta e acesso, você tem riscos semelhantes. Na secção de próximos passos deste artigo, vamos guiá-lo para as melhores práticas para eliminar ou minimizar estes riscos.

No topo da pilha, governação de dados e gestão de direitos, assume-se um risco que pode ser atenuado pela gestão chave. (A gestão das chaves está abrangida por boas práticas.) Embora a gestão chave seja uma responsabilidade adicional, você tem áreas numa implementação paaS que você já não tem que gerir para que você possa transferir recursos para a gestão chave.

A plataforma Azure também lhe fornece uma forte proteção DDoS utilizando várias tecnologias baseadas em rede. No entanto, todos os tipos de métodos de proteção DDoS baseados em rede têm os seus limites numa base por ligação e por datacenter. Para ajudar a evitar o impacto de grandes ataques DDoS, pode aproveitar a capacidade de nuvem central da Azure de lhe permitir escalar de forma rápida e automática para se defender contra ataques DDoS. Vamos entrar em mais detalhes sobre como pode fazê-lo nos artigos de práticas recomendados.

## <a name="modernizing-the-defenders-mindset"></a>Modernizar a mentalidade do defensor
Com as implementações do PaaS vem uma mudança na sua abordagem geral à segurança. Passas de precisar de controlar tudo sozinho para partilhar responsabilidades com a Microsoft.

Outra diferença significativa entre o PaaS e os tradicionais destacamentos no local, é uma nova visão do que define o perímetro de segurança primário. Historicamente, o principal perímetro de segurança no local era a sua rede e a maioria dos desenhos de segurança no local usam a rede como o seu principal pivô de segurança. Para os destacamentos do PaaS, é melhor servido considerando a identidade como o principal perímetro de segurança.

## <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Adotar uma política de identidade como o perímetro de segurança primário
Uma das cinco características essenciais da computação em nuvem é o acesso amplo à rede, o que torna o pensamento centrado na rede menos relevante. O objetivo de grande parte da computação em nuvem é permitir que os utilizadores acedam a recursos independentemente da localização. Para a maioria dos utilizadores, a sua localização vai estar algures na Internet.

O seguinte número mostra como o perímetro de segurança evoluiu de um perímetro de rede para um perímetro de identidade. A segurança torna-se menos sobre a defesa da sua rede e mais sobre a defesa dos seus dados, bem como a gestão da segurança das suas apps e utilizadores. A principal diferença é que quer aproximar a segurança do que é importante para a sua empresa.

![Identidade como novo perímetro de segurança](./media/paas-deployments/identity-perimeter.png)

Inicialmente, os serviços Azure PaaS (por exemplo, funções web e Azure SQL) forneceram pouca ou nenhuma defesa tradicional do perímetro de rede. Entendeu-se que o objetivo do elemento era ser exposto à Internet (função web) e que a autenticação fornece o novo perímetro (por exemplo, BLOB ou Azure SQL).

As práticas de segurança modernas assumem que o adversário invadiu o perímetro da rede. Portanto, as práticas modernas de defesa mudaram para a identidade. As organizações devem estabelecer um perímetro de segurança baseado na identidade com forte autenticação e higiene de autorização (boas práticas).

Há décadas que os princípios e padrões do perímetro da rede estão disponíveis. Em contraste, a indústria tem relativamente menos experiência com a utilização da identidade como o principal perímetro de segurança. Com isto dito, acumulámos experiência suficiente para fornecer algumas recomendações gerais que são comprovadas no terreno e que se aplicam a quase todos os serviços paaS.

Seguem-se as melhores práticas para a gestão do perímetro de identidade.

**Melhores práticas** : Fixe as suas chaves e credenciais para garantir a sua implantação paaS.   
**Detalhe** : Perder chaves e credenciais é um problema comum. Pode utilizar uma solução centralizada onde as chaves e segredos podem ser armazenados em módulos de segurança de hardware (HSMs). [O Azure Key Vault](../../key-vault/general/overview.md) protege as suas chaves e segredos encriptando chaves de autenticação, chaves de conta de armazenamento, chaves de encriptação de dados, ficheiros .pfx e palavras-passe usando chaves protegidas por HSMs.

**Melhores práticas** : Não coloque credenciais e outros segredos em código fonte ou GitHub.   
**Detalhe** : A única coisa pior do que perder as chaves e credenciais é ter uma parte não autorizada a ter acesso a elas. Os atacantes podem aproveitar as tecnologias bot para encontrar chaves e segredos armazenados em repositórios de código, como o GitHub. Não coloque chaves e segredos nestes repositórios de código público.

**Melhores práticas** : Proteja as suas interfaces de gestão VM nos serviços híbridos PaaS e IaaS utilizando uma interface de gestão que lhe permite gerir remotamente estes VMs diretamente.   
**Detalhe** : Podem ser utilizados protocolos de gestão remota como [SSH,](https://en.wikipedia.org/wiki/Secure_Shell) [RDP](https://support.microsoft.com/kb/186607)e [PowerShell.](/powershell/module/microsoft.powershell.core/enable-psremoting) Em geral, recomendamos que não permita o acesso remoto direto aos VMs a partir da internet.

Se possível, utilize abordagens alternativas como a utilização de redes privadas virtuais numa rede virtual Azure. Se não estiverem disponíveis abordagens alternativas, certifique-se de que utiliza frases-passe complexas e autenticação de dois fatores (como [a autenticação multi-factor Azure).](../../active-directory/authentication/concept-mfa-howitworks.md)

**Melhores práticas** : Utilize plataformas de autenticação e autorização fortes.   
**Detalhe** : Utilize identidades federadas no Azure AD em vez de lojas de utilizadores personalizadas. Quando utiliza identidades federadas, aproveita-se uma abordagem baseada na plataforma e delega a gestão das identidades autorizadas aos seus parceiros. Uma abordagem de identidade federada é especialmente importante quando os funcionários são despedidos e que a informação precisa de ser refletida através de múltiplos sistemas de identidade e autorização.

Utilize mecanismos de autenticação e autorização fornecidos pela plataforma em vez de código personalizado. A razão é que o desenvolvimento de código de autenticação personalizado pode ser propenso a erros. A maioria dos seus desenvolvedores não são especialistas em segurança e é improvável que estejam cientes das subtilezas e dos mais recentes desenvolvimentos na autenticação e autorização. O código comercial (por exemplo, da Microsoft) é frequentemente amplamente revisto pela segurança.

Utilize a autenticação de dois fatores. A autenticação de dois fatores é a norma atual para a autenticação e autorização, pois evita as deficiências de segurança inerentes ao nome de utilizador e tipos de senha de autenticação. O acesso às interfaces de gestão Azure (portal/remote PowerShell) e aos serviços virados para o cliente deve ser concebido e configurado para utilizar a [Autenticação Multi-Factor Azure](../../active-directory/authentication/concept-mfa-howitworks.md).

Utilize protocolos de autenticação padrão, tais como OAuth2 e Kerberos. Estes protocolos foram amplamente revistos pelos pares e são provavelmente implementados como parte das bibliotecas da sua plataforma para autenticação e autorização.

## <a name="use-threat-modeling-during-application-design"></a>Use modelação de ameaças durante o design da aplicação
O Ciclo [de Vida para o Desenvolvimento da Segurança da](https://www.microsoft.com/en-us/sdl) Microsoft especifica que as equipas devem participar num processo chamado modelação de ameaças durante a fase de conceção. Para facilitar este processo, a Microsoft criou a [Ferramenta de Modelação de Ameaças SDL.](../develop/threat-modeling-tool.md) Modelar o design da aplicação e enumerar ameaças [de STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) em todos os limites de confiança pode apanhar erros de design desde cedo.

A tabela que se segue lista as ameaças stride e dá alguns exemplos de mitigação que usam funcionalidades Azure. Estas atenuações não vão funcionar em todas as situações.

| Ameaça | Propriedade de segurança | Potenciais mitigações da plataforma Azure |
| --- | --- | --- |
| Spoofing | Autenticação | Requer ligações HTTPS. |
| Adulteração | Integridade | Validar certificados TLS/SSL. |
| Rejeição | Não rejeição | Ativar [a monitorização e diagnósticos do](/azure/architecture/best-practices/monitoring)Azure . |
| Divulgação de informações | Confidencialidade | Criptografe os dados sensíveis em repouso utilizando [certificados de serviço.](/rest/api/appservice/certificates) |
| Denial-of-service | Disponibilidade | Monitorize as métricas de desempenho para potenciais condições de negação de serviço. Implementar filtros de ligação. |
| Elevação de privilégios | Autorização | Utilizar [gestão de identidade privilegiada.](../../active-directory/privileged-identity-management/subscription-requirements.md) |

## <a name="develop-on-azure-app-service"></a>Desenvolver no Azure App Service
[O Azure App Service](../../app-service/overview.md) é uma oferta paaS que permite criar aplicações web e móveis para qualquer plataforma ou dispositivo e conectar-se a dados em qualquer lugar, na nuvem ou no local. O Serviço de Aplicações inclui as capacidades web e móveis que foram previamente entregues separadamente como Websites Azure e Azure Mobile Services. Também inclui novas capacidades para automatizar processos de negócio e o alojar APIs da nuvem. Como um único serviço integrado, o App Service traz um rico conjunto de capacidades para cenários web, mobile e de integração.

Seguem-se as melhores práticas para a utilização do Serviço de Aplicações.

**Melhores práticas** : [Autenticar através do Azure Ative Directory](../../app-service/overview-authentication-authorization.md).   
**Detalhe** : O Serviço de Aplicações fornece um serviço OAuth 2.0 para o seu fornecedor de identidade. O OAuth 2.0 foca-se na simplicidade do desenvolvedor de clientes, ao mesmo tempo que fornece fluxos de autorização específicos para aplicações web, aplicações de desktop e telemóveis. A Azure AD utiliza o OAuth 2.0 para permitir o acesso a aplicações móveis e web.

**Boas práticas** : Restringir o acesso com base na necessidade de conhecer e menos privilégios princípios de segurança.   
**Detalhe** : Restringir o acesso é imperativo para as organizações que pretendem impor políticas de segurança para o acesso aos dados. Pode utilizar o RBAC para atribuir permissões a utilizadores, grupos e aplicações num determinado âmbito. Para saber mais sobre a concessão de acesso aos utilizadores às aplicações, consulte Começar com a [gestão de acessos.](../../role-based-access-control/overview.md)

**Melhor prática:** Proteja as suas chaves.   
**Detalhe** : Azure Key Vault ajuda a salvaguardar chaves criptográficas e segredos que aplicações e serviços em nuvem usam. Com o Key Vault, pode encriptar chaves e segredos (tais como chaves de autenticação, chaves de conta de armazenamento, chaves de encriptação de dados, . Ficheiros PFX e palavras-passe) utilizando chaves protegidas por módulos de segurança de hardware (HSMs). Para maior segurança, pode importar ou gerar chaves nos HSMs. Consulte [o Azure Key Vault](../../key-vault/general/overview.md) para saber mais. Também pode utilizar o Key Vault para gerir os seus certificados TLS com renovação automática.

**Boas práticas** : Restringir os endereços IP de origem de entrada.   
**Detalhe** : [O App Service Environment](../../app-service/environment/intro.md) tem uma funcionalidade de integração de rede virtual que o ajuda a restringir os endereços IP de origem recebida através de grupos de segurança de rede. As redes virtuais permitem-lhe colocar recursos Azure numa rede não internet, encaminhável a que controla o acesso. Para saber mais, consulte [Integrar a sua aplicação com uma rede virtual Azure.](../../app-service/web-sites-integrate-with-vnet.md)

**Melhores práticas** : Monitorize o estado de segurança dos seus ambientes de Serviço de Aplicações.   
**Detalhe** : Utilize o Centro de Segurança Azure para monitorizar os ambientes do serviço de aplicações. Quando o Centro de Segurança identifica potenciais vulnerabilidades de segurança, cria [recomendações](../../security-center/asset-inventory.md) que o orientam através do processo de configuração dos controlos necessários.

> [!NOTE]
> O Serviço de Aplicações de Monitorização está em pré-visualização e está disponível apenas no [nível padrão](../../security-center/security-center-pricing.md) do Centro de Segurança.
>
>

## <a name="install-a-web-application-firewall"></a>Instale uma firewall de aplicação web
Cada vez mais, as aplicações Web são alvo de ataques maliciosos que exploram vulnerabilidades conhecidas comuns. Destas vulnerabilidades, são frequentes os ataques de injeção de SQL, scripting entre sites, entre muitas outras. Impedir este tipo de ataques ao código das aplicações constitui um desafio e exige uma manutenção, correção e monitorização rigorosas em muitas camadas da topologia da aplicação. Uma firewall de aplicações Web centralizada ajuda a simplificar em muito a gestão da segurança e confere aos administradores de aplicações uma maior garantia de proteção contra as ameaças ou intrusões. Uma solução WAF também pode reagir mais rapidamente a uma ameaça de segurança ao corrigir uma vulnerabilidade conhecida numa localização central, em vez de proteger cada uma das aplicações Web individualmente. Os gateways de aplicações existentes podem ser facilmente convertidos num gateway de aplicação com firewall de aplicações Web ativada.

[A firewall de aplicações web (WAF)](../../web-application-firewall/afds/afds-overview.md) é uma funcionalidade do Application Gateway que fornece proteção centralizada das suas aplicações web contra explorações e vulnerabilidades comuns. A WAF baseia-se nas regras do [Open Web Application Security Project (OWASP) que define](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9.

## <a name="monitor-the-performance-of-your-applications"></a>Monitorize o desempenho das suas aplicações
A monitorização é o ato de recolher e analisar dados para determinar o desempenho, saúde e disponibilidade da sua aplicação. Uma estratégia de monitorização efetiva ajuda-o a compreender o funcionamento em detalhe dos componentes da sua aplicação. Ajuda-o a aumentar o seu tempo de funcionamento notificando-o de problemas críticos para que possa resolvê-los antes que se tornem problemas. Também ajuda a detetar anomalias que podem estar relacionadas com a segurança.

Utilize [o Azure Application Insights](https://azure.microsoft.com/documentation/services/application-insights) para monitorizar a disponibilidade, desempenho e utilização da sua aplicação, quer esteja hospedado na nuvem ou no local. Ao utilizar o Application Insights, pode identificar e diagnosticar rapidamente erros na sua aplicação sem esperar que um utilizador os reporte. Com as informações que recolher, pode fazer escolhas informadas sobre a manutenção e as melhorias da aplicação.

O Application Insights tem um vasto conjunto ferramentas para interagir com os dados que recolhe. O Application Insights armazena os dados num repositório comum. Pode tirar partido da funcionalidade partilhada, como alertas, dashboards e análises profundas com a linguagem de consulta kusto.

## <a name="perform-security-penetration-testing"></a>Realizar testes de penetração de segurança
Validar defesas de segurança é tão importante como testar qualquer outra funcionalidade. Faça [do teste de penetração](pen-testing.md) uma parte padrão do seu processo de construção e implantação. Agende testes de segurança regulares e verificação de vulnerabilidades em aplicações implementadas, e monitorize para portas abertas, pontos finais e ataques.

O teste de fuzz é um método para encontrar falhas de programa (erros de código) fornecendo dados de entrada mal formados para interfaces de programa (pontos de entrada) que analisam e consomem estes dados. [A Deteção](https://www.microsoft.com/en-us/security-risk-detection/) de Riscos de Segurança da Microsoft é uma ferramenta baseada na nuvem que pode usar para procurar bugs e outras vulnerabilidades de segurança no seu software antes de o implementar no Azure. A ferramenta foi concebida para capturar vulnerabilidades antes de implementar software para que não tenha de corrigir um bug, lidar com falhas ou responder a um ataque após o lançamento do software.


## <a name="next-steps"></a>Passos seguintes
Neste artigo, focamo-nos nas vantagens de segurança de uma implementação do Azure PaaS e nas melhores práticas de segurança para aplicações em nuvem. Em seguida, aprenda práticas recomendadas para garantir as suas soluções web e móveis PaaS utilizando serviços Azure específicos. Começaremos com o Azure App Service, Azure SQL Database e Azure Synapse Analytics e Azure Storage. À medida que os artigos sobre práticas recomendadas para outros serviços Azure ficarem disponíveis, os links serão fornecidos na seguinte lista:

- [Serviço de Aplicações do Azure](paas-applications-using-app-services.md)
- [Azure SQL Database e Azure Synapse Analytics](paas-applications-using-sql.md)
- [Armazenamento do Azure](paas-applications-using-storage.md)
- Cache do Azure para Redis
- Azure Service Bus
- Firewalls de aplicações web

Consulte [o Desenvolvimento de aplicações seguras no Azure](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/) para questões de segurança e controlos que deve considerar em cada fase do ciclo de vida do desenvolvimento do software ao desenvolver aplicações para a nuvem.

Consulte as [melhores práticas e padrões](best-practices-and-patterns.md) de segurança da Azure para obter mais boas práticas de segurança quando estiver a desenhar, implementar e gerir as suas soluções em nuvem utilizando o Azure.

Estão disponíveis os seguintes recursos para fornecer informações mais gerais sobre a segurança da Azure e serviços relacionados com a Microsoft:
* [Azure Security Team Blog](/archive/blogs/azuresecurity/) - para informações atualizadas sobre as últimas informações em Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) - onde as vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, podem ser reportadas ou via e-mail para secure@microsoft.com