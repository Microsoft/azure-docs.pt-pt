---
title: Proteger implementações PaaS | Documentos da Microsoft
description: " Compreenda as vantagens de segurança de PaaS versus outros modelos de serviço de nuvem e Aprenda as práticas recomendadas para proteger a sua implementação de PaaS do Azure. "
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: techlake
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: terrylan
ms.openlocfilehash: 497fc1dd5691b5aa33207c6a3943a51c473d2f6c
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57247210"
---
# <a name="securing-paas-deployments"></a>Proteger implementações de PaaS

Este artigo fornece informações que lhe permite:

- Compreender as vantagens de segurança de alojamento de aplicações na cloud
- Avaliar as vantagens de segurança da plataforma como serviço (PaaS) em relação a outros modelos de serviço em nuvem
- Alterar o foco de segurança de uma rede de centrada para uma abordagem de segurança de perímetro voltada para a identidade
- Implementar gerais PaaS melhores práticas recomendações de segurança

## <a name="cloud-security-advantages"></a>Vantagens de segurança da cloud
Há vantagens de segurança em ser na cloud. Num ambiente no local, as organizações prováveis ter responsabilidades por cumprir e recursos limitados disponíveis para investir em segurança, que cria um ambiente em que os invasores estejam habilitados a explorar vulnerabilidades em todas as camadas.

![Vantagens de segurança da era da cloud][1]

As organizações conseguem melhorar sua deteção de ameaças e tempos de resposta com capacidades de segurança com base na cloud de um fornecedor e inteligência da cloud.  Mudando responsabilidades para o fornecedor de cloud, as empresas podem obter mais cobertura de segurança, que permite que sejam realocar recursos de segurança e o orçamento para outras prioridades de negócios.

## <a name="division-of-responsibility"></a>Divisão de responsabilidade
É importante entender a divisão de responsabilidade entre e a Microsoft. No local, que possui a pilha inteira, mas como mover para a cloud algumas responsabilidades transferir para o Microsoft. A matriz de responsabilidade seguinte mostra as áreas da pilha numa implantação de SaaS, PaaS e IaaS, que é responsável por e a Microsoft é responsável.

![Zonas de responsabilidade][2]

Para todos os tipos de implementação de nuvem, que possui seus dados e identidades. É responsável por proteger a segurança dos seus dados e identidades, recursos no local e os componentes da nuvem que controle (que varia consoante o tipo de serviço).

Responsabilidades que são sempre retidas por si, independentemente do tipo de implementação, são:

- Dados
- Pontos Finais
- Conta
- Gestão de acesso

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Vantagens de segurança de um PaaS na cloud modelo de serviço
Utilizar a mesma matriz de responsabilidade, vamos analisar as vantagens de segurança de uma implementação de PaaS do Azure versus no local.

![Vantagens de segurança de PaaS][3]

A iniciar na parte inferior da pilha, a infraestrutura física, o Microsoft reduz riscos comuns e as responsabilidades. Uma vez que a nuvem da Microsoft é monitorizada continuamente pela Microsoft, é difícil a ataques. Não faz sentido para um atacante buscar a nuvem da Microsoft como um destino. A menos que o atacante tem muito dinheiro e recursos, o invasor provavelmente-se de que passar para outro destino.  

No meio da pilha, não há diferença entre uma implementação de PaaS e no local. Na camada da aplicação e a camada de gestão de acesso e de conta, terá dos riscos semelhantes. Na próxima seção passos deste artigo, iremos guiá-lo para as melhores práticas para eliminar ou minimizar esses riscos.

Na parte superior da pilha, a governação de dados e gestão de direitos, que tomar um risco que pode ser atenuado por gestão de chaves. (A gestão de chaves é descrita em práticas recomendadas.) Embora a gestão de chaves é uma responsabilidade adicional, tem áreas numa implantação de PaaS que já não tem de gerir, pelo que pode deslocar recursos para gestão de chaves.

A plataforma do Azure também fornece a proteção contra DDoS forte, utilizando várias tecnologias baseadas na rede. No entanto, todos os tipos de métodos de proteção de DDoS baseados em rede tem seus limites numa base por ligação e por centro de dados. Para ajudar a evitar o impacto dos ataques de DDoS grandes, pode tirar partido core cloud capacidade do Azure de permitir a rápida e automaticamente aumentar horizontalmente para se Defender contra ataques de DDoS. Vamos em mais detalhes sobre como pode fazer isso nos artigos de práticas recomendadas.

## <a name="modernizing-the-defenders-mindset"></a>Modernizar a mentalidade do defender
Com a PaaS implementações vêm uma mudança na sua abordagem geral para segurança. Mudar a necessidade de controlar tudo o que mesmo a responsabilidade de partilha com a Microsoft.

Outra diferença significativa entre PaaS e as implementações tradicionais no local, é uma nova vista do que define o perímetro segurança principal. Historicamente, o perímetro de segurança no local primário foi a sua rede e a maioria das estruturas de segurança no local utilizam a rede como seu pivot de segurança principal. Para implementações de PaaS, é mais bem atendidas Considerando a identidade a ser perímetro segurança principal.

## <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Adotar uma política de identidade como perímetro segurança principal
Um das cinco características essenciais de computação em nuvem é amplo acesso à rede, que torna a centrada em rede pensando menos relevante. O objetivo de muita computação em nuvem é permitir que os utilizadores acedam aos recursos, independentemente da localização. Para a maioria dos usuários, a respetiva localização vai ser em algum lugar na Internet.

A figura seguinte mostra como o perímetro de segurança deixou de ser um perímetro de rede para um perímetro de identidade. A segurança se torna menos sobre defesa de rede e mais informações sobre a Defender seus dados, bem como gerir a segurança das suas aplicações e os utilizadores. A principal diferença é que deseja enviar mais perto de segurança para o que é importante para a sua empresa.

![Identidade como novo perímetro de segurança][4]

Inicialmente, os serviços de PaaS do Azure (por exemplo, funções da web e SQL do Azure) fornecido pouca ou nenhuma rede tradicional defesas no perímetro. Ele foi compreendido que finalidade do elemento era para ser exposto à Internet (função web) e que a autenticação fornece o novo perímetro (por exemplo, o BLOB ou o SQL do Azure).

Práticas de segurança moderno partem do princípio de que o adversário invadiu o perímetro da rede. Por conseguinte, as práticas de defesa modernos foram movidas para identidade. As organizações tem de estabelecer um perímetro de segurança baseada em identidades com forte higienização de autenticação e autorização (melhores práticas).

Princípios e padrões para o perímetro da rede estão disponíveis há décadas. Por outro lado, o mercado dispõe relativamente menos experiência com o uso de identidade como perímetro segurança principal. Dito isso, temos acumulado suficiente experiência para fornecer algumas recomendações gerais que comprovado no campo e aplicam-se a quase todos os serviços de PaaS.

Seguem-se as práticas recomendadas para gerenciamento do perímetro de identidade.

**Melhor prática**: Proteja as chaves e as credenciais para proteger a sua implementação de PaaS.   
**Detalhe**: Perda de chaves e as credenciais é um problema comum. Pode utilizar uma solução centralizada onde podem ser armazenados chaves e segredos em módulos de segurança de hardware. O Azure disponibiliza um HSM na cloud com o [do Azure Key Vault](../key-vault/key-vault-whatis.md).

**Melhor prática**: Não coloque as credenciais e outros segredos no código-fonte ou o GitHub.   
**Detalhe**: A única coisa que pior do que perder as chaves e as credenciais é fazer com que uma entidade não autorizada obter acesso aos mesmos. Os atacantes podem aproveitar bot tecnologias para localizar as chaves e segredos armazenados nos repositórios de código, como o GitHub. Não coloque chaves e segredos nestes repositórios de código público.

**Melhor prática**: Proteger suas interfaces de gerenciamento de VM híbrida PaaS e IaaS serviços utilizando uma interface de gestão que lhe permite remoto para gerir estas VMs diretamente.   
**Detalhe**: Protocolos de gestão remota, como [SSH](https://en.wikipedia.org/wiki/Secure_Shell), [RDP](https://support.microsoft.com/kb/186607), e [comunicação remota do PowerShell](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) pode ser utilizado. Em geral, recomendamos que não ativar acesso remoto direto para VMs da internet.

Se possível, utilize abordagens alternativas, como a utilização de redes privadas virtuais numa rede virtual do Azure. Se não estiverem disponíveis abordagens alternativas, certifique-se de que utiliza as frases de acesso complexas e autenticação de dois fatores (como [multi-factor Authentication](../active-directory/authentication/multi-factor-authentication.md)).

**Melhor prática**: Utilize plataformas fortes de autenticação e autorização.   
**Detalhe**: Utilize identidades federadas no Azure AD em vez de arquivos de utilizador personalizada. Quando utilizar identidades federadas, pode tirar partido de uma abordagem baseada na plataforma e delegar a gestão de identidades autorizadas para os seus parceiros. Uma abordagem de identidade federada é especialmente importante quando os funcionários estão terminados e que informações necessita de ser refletido através de vários sistemas de identidade e autorização.

Utilize mecanismos de autenticação e autorização fornecidos pela plataforma, em vez de código personalizado. O motivo é que a desenvolver o código de autenticação personalizado pode estar sujeita a erros. A maioria dos seus desenvolvedores não é especialistas em segurança e é pouco provável que tenha em atenção as sutilezas e os desenvolvimentos mais recentes na autenticação e autorização. Código comercial (por exemplo, da Microsoft) é, muitas vezes, extensivamente segurança revista.

Utilize a autenticação de dois fatores. Autenticação de dois fatores é o padrão atual para autenticação e autorização porque evita as vulnerabilidades de segurança inerentes a tipos de nome de utilizador e palavra-passe de autenticação. Acesso às interfaces de gestão do Azure (portal/remota do PowerShell) e serviços ao cliente deve ser criado e configurado para utilizar [multi-factor Authentication](../active-directory/authentication/multi-factor-authentication.md).

Utilize protocolos de autenticação padrão, como o OAuth2 e Kerberos. Esses protocolos foram extensivamente ponto a ponto revista e provavelmente são implementados como parte de suas bibliotecas de plataforma para autenticação e autorização.

## <a name="use-threat-modeling-during-application-design"></a>Utilizar a Modelagem de ameaças durante o design do aplicativo
A Microsoft [ciclo de vida de desenvolvimento de segurança](https://www.microsoft.com/en-us/sdl) Especifica que as equipes devem participar de um processo chamado durante a fase de design de modelagem de ameaças. Para ajudar a facilitar esse processo, a Microsoft criou o [ferramenta de modelagem de ameaças do SDL](../security/azure-security-threat-modeling-tool.md). O design do aplicativo de modelagem e enumerar [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) ameaças em toda a confiança de todos os limites podem identificar erros de design desde o início.

A tabela seguinte lista as ameaças STRIDE e fornece algumas mitigações de exemplo que utilizam as funcionalidades do Azure. Essas atenuações não funcionarão em todas as situações.

| Ameaças | Propriedade de segurança | Possíveis mitigações de plataforma do Azure |
| --- | --- | --- |
| Spoofing | Authentication | Exigir ligações HTTPS. |
| Adulteração | Integridade | Valide certificados SSL. |
| Rejeição | Não repudiação | Ativar o Azure [monitorização e diagnóstico](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). |
| Divulgação de informações | Confidencialidade | Encriptar dados confidenciais Inativos utilizando [certificados de serviço](https://docs.microsoft.com/rest/api/appservice/certificates). |
| Negação de serviço | Disponibilidade | Monitorizar métricas de desempenho para possíveis condições de negação de serviço. Implemente filtros de conexão. |
| Elevação de privilégio | Autorização | Uso [Privileged Identity Management](../active-directory/privileged-identity-management/subscription-requirements.md). |

## <a name="develop-on-azure-app-service"></a>Desenvolver no serviço de aplicações do Azure
[Serviço de aplicações do Azure](../app-service/overview.md) é uma oferta de PaaS permite-lhe criar aplicações web e móveis para qualquer plataforma ou dispositivo e ligue-se aos dados em qualquer local e na cloud ou no local. Serviço de aplicações inclui as capacidades web e móveis que anteriormente foram entregues em separado como Web sites do Azure e serviços móveis do Azure. Também inclui novas capacidades para automatizar processos de negócio e o alojar APIs da nuvem. Como um serviço integrado único, o serviço de aplicações fornece um conjunto avançado de capacidades para a web, móveis e cenários de integração.

Seguem-se melhores práticas para utilizar o serviço de aplicações.

**Melhor prática**: [Autenticar através do Azure Active Directory](../app-service/overview-authentication-authorization.md).   
**Detalhe**: Serviço de aplicações fornece um serviço OAuth 2.0 para o seu fornecedor de identidade. OAuth 2.0 se concentra na simplicidade de desenvolvedor do cliente ao mesmo tempo, os fluxos de autorização específico para aplicações web, aplicativos de desktop e celulares. Azure AD utiliza o OAuth 2.0 para lhe permitir autorizar o acesso a dispositivos móveis e aplicações web.

**Melhor prática**: Restringir o acesso com base na necessidade de conhecer e princípios de segurança de privilégio mínimo.   
**Detalhe**: Restringir o acesso é fundamental para as organizações que desejam aplicar políticas de segurança para acesso a dados. Pode utilizar o RBAC para atribuir permissões a utilizadores, grupos e aplicações num determinado âmbito. Para saber mais sobre como conceder aos utilizadores acesso a aplicações, veja [introdução à gestão de acesso](../role-based-access-control/overview.md).

**Melhor prática**: Protege as suas chaves.   
**Detalhe**: O Azure Key Vault ajuda a salvaguardar chaves criptográficas e segredos na cloud a aplicações e serviços utilizam. Com o Key Vault, pode encriptar chaves e segredos (tal como chaves de autenticação, chaves de conta de armazenamento, chaves de encriptação de dados. Ficheiros PFX e palavras-passe) utilizando as teclas que estão protegidas por módulos de segurança de hardware (HSMs). Para maior segurança, pode importar ou gerar chaves nos HSMs. Ver [do Azure Key Vault](../key-vault/key-vault-whatis.md) para saber mais. Também pode utilizar o Key Vault para gerir os certificados TLS com renovação automática.

**Melhor prática**: Restringir os endereços IP de origem recebidos.   
**Detalhe**: [Ambiente de serviço de aplicações](../app-service/environment/intro.md) tem um recurso de integração de rede virtual que lhe permite restringir os endereços IP de origem recebidos por meio dos grupos de segurança de rede. Redes virtuais permitem-lhe colocar recursos do Azure numa rede não internet encaminhável que controlam o acesso a. Para obter mais informações, consulte [integrar a sua aplicação com uma Azure virtual network](../app-service/web-sites-integrate-with-vnet.md).

**Melhor prática**: Monitorize o estado de segurança dos seus ambientes de serviço de aplicações.   
**Detalhe**: Centro de segurança do Azure utilizado para monitorizar os seus ambientes de serviço de aplicações. Quando o Centro de segurança identifica potenciais vulnerabilidades de segurança, ele cria [recomendações](../security-center/security-center-virtual-machine-recommendations.md) que guiá-lo pelo processo de configuração de controlos necessários.

> [!NOTE]
> Monitorização do serviço de aplicações está em pré-visualização e disponível apenas nos [escalão Standard](../security-center/security-center-pricing.md) do Centro de segurança.
>
>

## <a name="install-a-web-application-firewall"></a>Instale uma firewall de aplicações web
Cada vez mais, as aplicações Web são alvo de ataques maliciosos que exploram vulnerabilidades conhecidas comuns. Destas vulnerabilidades, são frequentes os ataques de injeção de SQL, scripting entre sites, entre muitas outras. Impedir este tipo de ataques ao código das aplicações constitui um desafio e exige uma manutenção, correção e monitorização rigorosas em muitas camadas da topologia da aplicação. Uma firewall de aplicações Web centralizada ajuda a simplificar em muito a gestão da segurança e confere aos administradores de aplicações uma maior garantia de proteção contra as ameaças ou intrusões. Uma solução WAF também pode reagir mais rapidamente a uma ameaça de segurança ao corrigir uma vulnerabilidade conhecida numa localização central, em vez de proteger cada uma das aplicações Web individualmente. Os gateways de aplicações existentes podem ser facilmente convertidos num gateway de aplicação com firewall de aplicações Web ativada.

[Firewall de aplicações Web (WAF)](../application-gateway/waf-overview.md) é um recurso do Gateway de aplicação que fornece proteção centralizada das suas aplicações web de exploits e vulnerabilidades comuns. WAF baseia-se nas regras da [conjuntos de regras de núcleo de Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9.

## <a name="monitor-the-performance-of-your-applications"></a>Monitorizar o desempenho das suas aplicações
A monitorização é o ato de recolha e análise de dados para determinar o desempenho, o estado de funcionamento e a disponibilidade da sua aplicação. Uma estratégia de monitorização efetiva ajuda-o a compreender o funcionamento em detalhe dos componentes da sua aplicação. Ele ajuda a aumentar o tempo de atividade ao notificá-lo dos problemas críticos para que possa resolver antes que se tornem problemas. Ele também ajuda a detetar anomalias que possam estar relacionados à segurança.

Uso [do Azure Application Insights](https://azure.microsoft.com/documentation/services/application-insights) para monitorizar a disponibilidade, desempenho e utilização da sua aplicação, se está alojado na cloud ou no local. Ao utilizar o Application Insights, pode rapidamente identificar e diagnosticar erros em seu aplicativo sem esperar por um utilizador para comunicá-las. Com as informações que recolher, pode fazer escolhas informadas sobre a manutenção e as melhorias da aplicação.

O Application Insights tem um vasto conjunto ferramentas para interagir com os dados que recolhe. O Application Insights armazena os dados num repositório comum. Pode tirar partido da funcionalidade partilhada, como alertas, dashboards e análises detalhadas com a linguagem de consulta de Kusto.



## <a name="next-steps"></a>Passos Seguintes
Neste artigo, nos concentramos nos vantagens de segurança de uma implementação de PaaS do Azure e melhores práticas de segurança para aplicações na cloud. Em seguida, saiba práticas recomendadas para proteger o seu PaaS web e as soluções móveis usando os serviços específicos do Azure. Vamos começar com o serviço de aplicações do Azure, base de dados SQL do Azure e Azure SQL Data Warehouse e armazenamento do Azure. Como artigos sobre práticas recomendadas para outros serviços do Azure tornam-se disponível, links serão fornecidos na lista seguinte:

- [Serviço de Aplicações do Azure](security-paas-applications-using-app-services.md)
- [Base de dados SQL do Azure e o armazém de dados SQL do Azure](security-paas-applications-using-sql.md)
- [Armazenamento do Azure](security-paas-applications-using-storage.md)
- Cache do Azure para Redis
- Service Bus do Azure
- Firewalls de aplicações Web

Ver [padrões e práticas recomendadas de segurança do Azure](security-best-practices-and-patterns.md) para obter mais melhores práticas de segurança a utilizar quando estiver conceber, implementar e gerir soluções na cloud ao utilizar o Azure.

Os seguintes recursos estão disponíveis para fornecer informações mais gerais acerca da segurança do Azure e serviços Microsoft relacionados:
* [Blogue de equipa de segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – para obter informações atualizadas sobre as mais recentes da segurança do Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – onde podem ser comunicadas as vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, ou por e-mail para secure@microsoft.com

<!--Image references-->
[1]: ./media/security-paas-deployments/advantages-of-cloud.png
[2]: ./media/security-paas-deployments/responsibility-zones.png
[3]: ./media/security-paas-deployments/advantages-of-paas.png
[4]: ./media/security-paas-deployments/identity-perimeter.png
