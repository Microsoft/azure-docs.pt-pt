---
title: Práticas recomendadas para implantações de PaaS seguras-Microsoft Azure
description: Conheça as práticas recomendadas para projetar, criar e gerenciar aplicativos de nuvem seguros no Azure e entender as vantagens de segurança do PaaS em comparação com outros modelos de serviço de nuvem.
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
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: d20ea4a6e86bb889615d3ab9bfcac5aedf838ceb
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098632"
---
# <a name="securing-paas-deployments"></a>Proteger implementações de PaaS

Este artigo fornece informações que ajudam você a:

- Entenda as vantagens de segurança de hospedar aplicativos na nuvem
- Avaliar as vantagens de segurança de PaaS (plataforma como serviço) versus outros modelos de serviço de nuvem
- Altere o foco de segurança de uma abordagem de segurança centrada em rede para um perímetro centrado em identidade
- Implementar recomendações gerais de práticas recomendadas de segurança PaaS

O [desenvolvimento de aplicativos seguros no Azure](abstract-develop-secure-apps.md) é um guia Geral das perguntas e controles de segurança que você deve considerar em cada fase do ciclo de vida do desenvolvimento de software ao desenvolver aplicativos para a nuvem.

## <a name="cloud-security-advantages"></a>Vantagens de segurança na nuvem
Há vantagens de segurança para estar na nuvem. Em um ambiente local, as organizações provavelmente têm responsabilidades não atendidas e recursos limitados disponíveis para investir em segurança, o que cria um ambiente em que os invasores podem explorar vulnerabilidades em todas as camadas.

![Vantagens de segurança da era da nuvem](./media/paas-deployments/advantages-of-cloud.png)

As organizações podem melhorar a detecção de ameaças e os tempos de resposta usando os recursos de segurança baseados em nuvem do provedor e a inteligência de nuvem.  Ao mudar as responsabilidades para o provedor de nuvem, as organizações podem obter mais cobertura de segurança, o que permite realocar recursos de segurança e orçamento para outras prioridades de negócios.

## <a name="division-of-responsibility"></a>Divisão de responsabilidade
É importante entender a divisão de responsabilidade entre você e a Microsoft. No local, você possui a pilha inteira, mas à medida que passa para a nuvem, algumas responsabilidades são transferidas para a Microsoft. A matriz de responsabilidade a seguir mostra as áreas da pilha em uma implantação de SaaS, PaaS e IaaS para a qual você é responsável e a Microsoft é responsável.

![Zonas de responsabilidade](./media/paas-deployments/responsibility-zones.png)

Para todos os tipos de implantação de nuvem, você tem seus dados e identidades. Você é responsável por proteger a segurança de seus dados e identidades, recursos locais e os componentes de nuvem que você controla (que varia por tipo de serviço).

As responsabilidades que são sempre retidas por você, independentemente do tipo de implantação, são:

- Data
- Pontos Finais
- Conta
- Gerenciamento de acesso

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Vantagens de segurança de um modelo de serviço de nuvem PaaS
Usando a mesma matriz de responsabilidade, vamos examinar as vantagens de segurança de uma implantação de PaaS do Azure em relação ao local.

![Vantagens de segurança do PaaS](./media/paas-deployments/advantages-of-paas.png)

A partir da parte inferior da pilha, a infraestrutura física, a Microsoft reduz os riscos e as responsabilidades comuns. Como o Microsoft Cloud é continuamente monitorado pela Microsoft, é difícil de atacar. Não faz sentido para um invasor buscar a nuvem da Microsoft como um destino. A menos que o invasor tenha muito dinheiro e recursos, é provável que o invasor passe para outro destino.  

No meio da pilha, não há nenhuma diferença entre uma implantação de PaaS e local. Na camada de aplicativo e na camada de gerenciamento de acesso e conta, você tem riscos semelhantes. Na seção próximas etapas deste artigo, iremos orientá-lo nas práticas recomendadas para eliminar ou minimizar esses riscos.

Na parte superior da pilha, governança de dados e gerenciamento de direitos, você assume um risco que pode ser mitigado pelo gerenciamento de chaves. (O gerenciamento de chaves é abordado nas práticas recomendadas.) Embora o gerenciamento de chaves seja uma responsabilidade adicional, você tem áreas em uma implantação de PaaS que não precisa mais gerenciar para poder mudar os recursos para o gerenciamento de chaves.

A plataforma Azure também fornece uma forte proteção contra DDoS usando várias tecnologias baseadas em rede. No entanto, todos os tipos de métodos de proteção contra DDoS baseados em rede têm seus limites em uma base por link e por datacenter. Para ajudar a evitar o impacto de grandes ataques de DDoS, você pode aproveitar a capacidade de nuvem principal do Azure de permitir que você escale de forma rápida e automática para se defender contra ataques de DDoS. Entraremos em mais detalhes sobre como você pode fazer isso nos artigos de práticas recomendadas.

## <a name="modernizing-the-defenders-mindset"></a>Modernizando a mentalidade do defender
Com as implantações de PaaS, surgem uma mudança na abordagem geral da segurança. Você passa da necessidade de controlar tudo para compartilhar responsabilidade com a Microsoft.

Outra diferença significativa entre as implantações locais tradicionais e de PaaS, é uma nova exibição do que define o perímetro de segurança primário. Historicamente, o perímetro de segurança local primário era sua rede e a maioria dos designs de segurança locais usa a rede como sua dinâmica de segurança primária. Para implantações de PaaS, você é mais bem atendido por considerar a identidade como o principal perímetro de segurança.

## <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Adote uma política de identidade como o perímetro de segurança primário
Uma das cinco características essenciais da computação em nuvem é o amplo acesso à rede, o que torna a preocupação centrada na rede menos relevante. A meta de grande parte da computação em nuvem é permitir que os usuários acessem recursos, independentemente do local. Para a maioria dos usuários, sua localização estará em algum lugar na Internet.

A figura a seguir mostra como o perímetro de segurança evoluiu de um perímetro de rede para um perímetro de identidade. A segurança se torna menos informando a defesa da sua rede e mais sobre como proteger seus dados, bem como gerenciar a segurança de seus aplicativos e usuários. A principal diferença é que você deseja enviar a segurança por push mais próximo do que é importante para sua empresa.

![Identidade como novo perímetro de segurança](./media/paas-deployments/identity-perimeter.png)

Inicialmente, os serviços de PaaS do Azure (por exemplo, funções Web e Azure SQL) forneciam pouca ou nenhuma defesa de perímetro de rede tradicional. Foi entendido que a finalidade do elemento deveria ser exposta à Internet (função Web) e que a autenticação fornece o novo perímetro (por exemplo, BLOB ou SQL do Azure).

As práticas de segurança modernas supõem que o adversário violou o perímetro da rede. Portanto, as práticas de defesa modernas foram movidas para identidade. As organizações devem estabelecer um perímetro de segurança baseado em identidade com uma forte autenticação e higiene de autorização (práticas recomendadas).

Os princípios e padrões para o perímetro de rede estão disponíveis há décadas. Por outro lado, o setor tem uma experiência relativamente menor com o uso da identidade como o perímetro de segurança primário. Dito isso, acumulamos experiência suficiente para fornecer algumas recomendações gerais que são comprovadas no campo e se aplicam a quase todos os serviços de PaaS.

Veja a seguir as práticas recomendadas para gerenciar o perímetro de identidade.

**Prática recomendada**: Proteja suas chaves e credenciais para proteger sua implantação de PaaS.   
**Detalhe**: Perder chaves e credenciais é um problema comum. Você pode usar uma solução centralizada na qual as chaves e os segredos podem ser armazenados em HSMs (módulos de segurança de hardware). O [Azure Key Vault](../../key-vault/key-vault-whatis.md) protege suas chaves e segredos criptografando chaves de autenticação, chaves de conta de armazenamento, chaves de criptografia de dados, arquivos. pfx e senhas usando chaves que são protegidas por hsms.

**Prática recomendada**: Não coloque as credenciais e outros segredos no código-fonte ou no GitHub.   
**Detalhe**: A única coisa pior do que perder suas chaves e credenciais é ter uma parte não autorizada ter acesso a elas. Os invasores podem aproveitar as tecnologias de bot para encontrar chaves e segredos armazenados em repositórios de código, como o GitHub. Não coloque a chave e os segredos nesses repositórios de código público.

**Prática recomendada**: Proteja suas interfaces de gerenciamento de VM em serviços de PaaS e IaaS híbridos usando uma interface de gerenciamento que permite que você gerencie remotamente essas VMs diretamente.   
**Detalhe**: Protocolos de gerenciamento remoto, como [SSH](https://en.wikipedia.org/wiki/Secure_Shell), [RDP](https://support.microsoft.com/kb/186607)e [comunicação remota do PowerShell](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) , podem ser usados. Em geral, recomendamos que você não habilite o acesso remoto direto às VMs da Internet.

Se possível, use abordagens alternativas como usar redes privadas virtuais em uma rede virtual do Azure. Se abordagens alternativas não estiverem disponíveis, certifique-se de usar frases secretas complexas e autenticação de dois fatores (como a [autenticação multifator do Azure](/azure/active-directory/authentication/multi-factor-authentication)).

**Prática recomendada**: Use plataformas de autenticação e autorização fortes.   
**Detalhe**: Use identidades federadas no Azure AD em vez de armazenamentos de usuário personalizados. Ao usar identidades federadas, você aproveita uma abordagem baseada em plataforma e delega o gerenciamento de identidades autorizadas para seus parceiros. Uma abordagem de identidade federada é especialmente importante quando os funcionários são encerrados e essas informações precisam ser refletidas por meio de vários sistemas de identidade e autorização.

Use mecanismos de autenticação e autorização fornecidos pela plataforma em vez de código personalizado. O motivo é que o desenvolvimento de código de autenticação personalizado pode ser propenso a erros. A maioria dos seus desenvolvedores não são especialistas em segurança e são improvável de estar cientes das sutilezas e dos desenvolvimentos mais recentes em autenticação e autorização. O código comercial (por exemplo, da Microsoft) geralmente é amplamente revisado pela segurança.

Use a autenticação de dois fatores. A autenticação de dois fatores é o padrão atual para autenticação e autorização porque evita as fraquezas de segurança inerentes aos tipos de senha e de nome de usuário de autenticação. O acesso às interfaces de gerenciamento do Azure (Portal/PowerShell remoto) e serviços voltados para o cliente deve ser projetado e configurado para usar a [autenticação multifator do Azure](/azure/active-directory/authentication/multi-factor-authentication).

Use protocolos de autenticação padrão, como OAuth2 e Kerberos. Esses protocolos foram extensivamente revisados e provavelmente são implementados como parte de suas bibliotecas de plataforma para autenticação e autorização.

## <a name="use-threat-modeling-during-application-design"></a>Usar a modelagem de ameaças durante o design do aplicativo
O [ciclo de vida do desenvolvimento de segurança](https://www.microsoft.com/en-us/sdl) da Microsoft especifica que as equipes devem se envolver em um processo chamado modelagem de ameaças durante a fase de design. Para ajudar a facilitar esse processo, a Microsoft criou o [Threat Modeling Tool do SDL](/azure/security/azure-security-threat-modeling-tool). Modelar o design do aplicativo e enumerar as ameaças [Stride](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) em todos os limites de confiança pode detectar erros de design no início.

A tabela a seguir lista as ameaças STRIDE e fornece algumas atenuações de exemplo que usam os recursos do Azure. Essas atenuações não funcionarão em todas as situações.

| Ataque | Propriedade de segurança | Possíveis mitigações da plataforma Azure |
| --- | --- | --- |
| Falsificação | Authentication | Exigir conexões HTTPS. |
| Adulteração | Integridade | Validar certificados SSL. |
| Repúdio | Não repúdio | Habilite o [monitoramento e o diagnóstico](/azure/architecture/best-practices/monitoring)do Azure. |
| Divulgação de informações | Confidencialidade | Criptografe dados confidenciais em repouso usando [certificados de serviço](/rest/api/appservice/certificates). |
| Negação de serviço | Disponibilidade | Monitore as métricas de desempenho para possíveis condições de negação de serviço. Implementar filtros de conexão. |
| Elevação de privilégio | Autorização | Use [Privileged Identity Management](/azure/active-directory/privileged-identity-management/subscription-requirements). |

## <a name="develop-on-azure-app-service"></a>Desenvolver no serviço Azure App
[Azure app serviço](/azure/app-service/overview) é uma oferta de PaaS que permite criar aplicativos Web e móveis para qualquer plataforma ou dispositivo e conectar-se a dados em qualquer lugar, na nuvem ou localmente. O serviço de aplicativo inclui os recursos Web e móveis que foram fornecidos anteriormente separadamente como sites do Azure e serviços móveis do Azure. Também inclui novas capacidades para automatizar processos de negócio e o alojar APIs da nuvem. Como um único serviço integrado, o serviço de aplicativo traz um rico conjunto de recursos para cenários da Web, móveis e de integração.

A seguir estão as práticas recomendadas para usar o serviço de aplicativo.

**Prática recomendada**: [Autenticar por meio de Azure Active Directory](/azure/app-service/overview-authentication-authorization).   
**Detalhe**: O serviço de aplicativo fornece um serviço OAuth 2,0 para seu provedor de identidade. O OAuth 2,0 concentra-se na simplicidade do desenvolvedor do cliente ao fornecer fluxos de autorização específicos para aplicativos Web, aplicativos de área de trabalho e celulares. O Azure AD usa o OAuth 2,0 para permitir que você autorize o acesso a aplicativos móveis e Web.

**Prática recomendada**: Restrinja o acesso com base na necessidade de conhecer os princípios de segurança de privilégios mínimos.   
**Detalhe**: Restringir o acesso é imperativo para organizações que desejam impor políticas de segurança para acesso a dados. Você pode usar o RBAC para atribuir permissões a usuários, grupos e aplicativos em um determinado escopo. Para saber mais sobre como conceder acesso a aplicativos aos usuários, confira [introdução ao gerenciamento de acesso](/azure/role-based-access-control/overview).

**Prática recomendada**: Proteja suas chaves.   
**Detalhe**: Azure Key Vault ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços de nuvem. Com o Key Vault, você pode criptografar chaves e segredos (como chaves de autenticação, chaves de conta de armazenamento, chaves de criptografia de dados,. Arquivos PFX e senhas) usando chaves que são protegidas por HSMs (módulos de segurança de hardware). Para maior segurança, pode importar ou gerar chaves nos HSMs. Consulte [Azure Key Vault](/azure/key-vault/key-vault-whatis) para saber mais. Você também pode usar Key Vault para gerenciar seus certificados TLS com renovação automática.

**Prática recomendada**: Restrinja os endereços IP de origem de entrada.   
**Detalhe**: O [ambiente do serviço de aplicativo](/azure/app-service/environment/intro) tem um recurso de integração de rede virtual que ajuda a restringir endereços IP de origem de entrada por meio de grupos de segurança de rede. As redes virtuais permitem que você coloque recursos do Azure em uma rede roteável que não seja da Internet para a qual você controla o acesso. Para saber mais, consulte [integrar seu aplicativo a uma rede virtual do Azure](/azure/app-service/web-sites-integrate-with-vnet).

**Prática recomendada**: Monitore o estado de segurança de seus ambientes de serviço de aplicativo.   
**Detalhe**: Use a central de segurança do Azure para monitorar seus ambientes de serviço de aplicativo. Quando a central de segurança identifica possíveis vulnerabilidades de segurança, ele cria [recomendações](../../security-center/security-center-virtual-machine-protection.md) que o orientam durante o processo de configuração dos controles necessários.

> [!NOTE]
> O monitoramento do serviço de aplicativo está em visualização e está disponível apenas na [camada Standard](/azure/security-center/security-center-pricing) da central de segurança.
>
>

## <a name="install-a-web-application-firewall"></a>Instalar um firewall do aplicativo Web
Cada vez mais, as aplicações Web são alvo de ataques maliciosos que exploram vulnerabilidades conhecidas comuns. Destas vulnerabilidades, são frequentes os ataques de injeção de SQL, scripting entre sites, entre muitas outras. Impedir este tipo de ataques ao código das aplicações constitui um desafio e exige uma manutenção, correção e monitorização rigorosas em muitas camadas da topologia da aplicação. Uma firewall de aplicações Web centralizada ajuda a simplificar em muito a gestão da segurança e confere aos administradores de aplicações uma maior garantia de proteção contra as ameaças ou intrusões. Uma solução WAF também pode reagir mais rapidamente a uma ameaça de segurança ao corrigir uma vulnerabilidade conhecida numa localização central, em vez de proteger cada uma das aplicações Web individualmente. Os gateways de aplicações existentes podem ser facilmente convertidos num gateway de aplicação com firewall de aplicações Web ativada.

O [WAF (firewall do aplicativo Web)](/azure/frontdoor/waf-overview) é um recurso do gateway de aplicativo que fornece proteção centralizada de seus aplicativos Web contra explorações e vulnerabilidades comuns. O WAF é baseado em regras dos [conjuntos de regras principais do OWASP (projeto de segurança de aplicativos Web) do Open](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3,0 ou 2.2.9.

## <a name="monitor-the-performance-of-your-applications"></a>Monitorar o desempenho de seus aplicativos
O monitoramento é o ato de coletar e analisar dados para determinar o desempenho, a integridade e a disponibilidade do seu aplicativo. Uma estratégia de monitorização efetiva ajuda-o a compreender o funcionamento em detalhe dos componentes da sua aplicação. Ele ajuda a aumentar seu tempo de atividade notificando você sobre problemas críticos para que você possa resolvê-los antes que eles se tornem problemas. Ele também ajuda a detectar anomalias que podem estar relacionadas à segurança.

Use [aplicativo Azure](https://azure.microsoft.com/documentation/services/application-insights) insights para monitorar a disponibilidade, o desempenho e o uso do seu aplicativo, seja ele hospedado na nuvem ou no local. Usando Application Insights, você pode rapidamente identificar e diagnosticar erros em seu aplicativo sem esperar que um usuário os relate. Com as informações que recolher, pode fazer escolhas informadas sobre a manutenção e as melhorias da aplicação.

O Application Insights tem um vasto conjunto ferramentas para interagir com os dados que recolhe. O Application Insights armazena os dados num repositório comum. Ele pode aproveitar a funcionalidade compartilhada, como alertas, painéis e análise profunda com a linguagem de consulta Kusto.

## <a name="perform-security-penetration-testing"></a>Executar teste de penetração de segurança
A validação de defesas de segurança é tão importante quanto testar qualquer outra funcionalidade. Faça um [teste](pen-testing.md) de penetração de uma parte padrão do processo de compilação e implantação. Agende testes de segurança regulares e a verificação de vulnerabilidade em aplicativos implantados e monitore para portas abertas, pontos de extremidade e ataques.

O teste de difusão é um método para localizar falhas de programa (erros de código) fornecendo dados de entrada malformados para interfaces de programa (pontos de entrada) que analisam e consomem esses dados. A [detecção de riscos de segurança da Microsoft](https://www.microsoft.com/en-us/security-risk-detection/) é uma ferramenta baseada em nuvem que você pode usar para procurar bugs e outras vulnerabilidades de segurança em seu software antes de implantá-lo no Azure. A ferramenta foi projetada para detectar vulnerabilidades antes de implantar o software para que você não precise corrigir um bug, lidar com panes ou responder a um ataque após o lançamento do software.


## <a name="next-steps"></a>Passos Seguintes
Neste artigo, nos concentramos nas vantagens de segurança de uma implantação de PaaS do Azure e nas práticas recomendadas de segurança para aplicativos em nuvem. Em seguida, conheça as práticas recomendadas para proteger suas soluções Web e móveis de PaaS usando serviços específicos do Azure. Começaremos com o serviço Azure App, o banco de dados SQL do Azure e o Azure SQL Data Warehouse e o armazenamento do Azure. Como artigos sobre práticas recomendadas para outros serviços do Azure se tornam disponíveis, os links serão fornecidos na lista a seguir:

- [Serviço de Aplicações do Azure](paas-applications-using-app-services.md)
- [Banco de dados SQL do Azure e Azure SQL Data Warehouse](paas-applications-using-sql.md)
- [Armazenamento do Azure](paas-applications-using-storage.md)
- Cache do Azure para Redis
- Azure Service Bus
- Firewalls do aplicativo Web

Consulte [desenvolvendo aplicativos seguros no Azure](abstract-develop-secure-apps.md) para questões de segurança e controles que você deve considerar em cada fase do ciclo de vida do desenvolvimento de software ao desenvolver aplicativos para a nuvem.

Veja [padrões e práticas recomendadas de segurança do Azure](best-practices-and-patterns.md) para obter mais práticas recomendadas de segurança para usar ao projetar, implantar e gerenciar suas soluções de nuvem usando o Azure.

Os recursos a seguir estão disponíveis para fornecer informações mais gerais sobre segurança do Azure e serviços da Microsoft relacionados:
* [Blog da equipe de segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – para obter informações atualizadas sobre o mais recente na segurança do Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – em que vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, podem ser relatadas ou enviadas por email parasecure@microsoft.com


