---
title: Linha de base de segurança Azure para configuração de aplicativoS Azure
description: A linha de base de segurança da Configuração da Aplicação Azure fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a885f027250a915347fea758c7c7978af0f45f49
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302518"
---
# <a name="azure-security-baseline-for-azure-app-configuration"></a>Linha de base de segurança Azure para configuração de aplicativoS Azure

Esta linha de base de segurança aplica orientações da [versão 2.0 do Azure Security Benchmark](../security/benchmarks/overview.md) para a Configuração da Aplicação Azure. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure. O conteúdo é agrupado pelos controlos de **segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável à Configuração da Aplicação Azure. Foram excluídos **os controlos** não aplicáveis à Configuração da Aplicação Azure.

Para ver como a Configuração da Aplicação Azure mapeia completamente para o Benchmark de Segurança Azure, consulte o [ficheiro de mapeamento de base de base de configuração de segurança da Azure App.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Segurança de Rede

*Para mais informações, consulte o [Benchmark de Segurança Azure: Segurança da Rede](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: Implementar a segurança para o tráfego interno

**Orientação**: A Azure App Configuration não implanta quaisquer recursos diretamente numa rede virtual. Uma vez que o serviço não é implantado numa rede virtual, não é possível aproveitar certas funcionalidades de rede para garantir o tráfego interno do serviço, tais como: grupos de segurança de rede, tabelas de rotas ou outros aparelhos de rede, como um Azure Firewall. No entanto, a Configuração de Aplicações permite-lhe utilizar pontos finais privados para se conectar de forma segura à Configuração de Aplicações Azure a partir de uma rede virtual.

Utilize o Azure Sentinel para descobrir o uso de protocolos de insegurança como SSL/TLSv1, SMBv1, LM/NTLMv1, wDigest, Ligações LDAP não assinadas e cifras fracas em Kerberos.

- [Utilização de pontos finais privados para configuração de aplicações Azure](concept-private-endpoint.md)

- [Livro de protocolos inseguros de Azure Sentinel](../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="ns-2-connect-private-networks-together"></a>NS-2: Ligar redes privadas em conjunto

**Orientação**: A Azure App Configuration suporta usar pontos finais privados para enviar dados de forma segura através de um link privado. Utilize a rede privada virtual Azure ExpressRoute ou Azure (VPN) para criar ligações privadas entre centros de dados Azure e infraestruturas no local num ambiente de cosão. As ligações ExpressRoute não passam pela internet pública, e oferecem mais fiabilidade, velocidades mais rápidas e latências mais baixas do que as ligações típicas à Internet. Para VPN ponto a local e VPN local-a-local, pode ligar dispositivos ou redes no local a uma rede virtual usando qualquer combinação destas opções VPN e Azure ExpressRoute.

Para ligar duas ou mais redes virtuais em Azure, utilize o espreitamento de rede virtual. O tráfego de rede entre redes virtuais é privado e é mantido na rede de espinha dorsal Azure.

- [Quais são os modelos de conectividade ExpressRoute](../expressroute/expressroute-connectivity-models.md)

- [Visão geral da VPN de Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Peering de rede virtual](../virtual-network/virtual-network-peering-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Estabelecer acesso à rede privada aos serviços da Azure

**Orientação**: Utilize o Azure Private Link para permitir o acesso privado à Configuração de Aplicações Azure a partir das suas redes virtuais sem atravessar a internet.

O acesso privado é uma medida adicional de defesa aprofundada, além da autenticação e segurança de tráfego oferecida pelos serviços Azure.

- [Compreenda a ligação privada Azure](../private-link/private-link-overview.md)

- [Como configurar link privado na Configuração de Aplicações Azure](concept-private-endpoint.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Proteger aplicações e serviços contra ataques de rede externa

**Orientação**: Ao aceder a valores de configuração através de uma rede virtual, proteja os seus recursos contra ataques de redes externas, incluindo ataques de negação de serviço distribuídos (DDoS), ataques específicos de aplicações e tráfego de internet não solicitado e potencialmente malicioso. Utilize o Azure Firewall para proteger aplicações e serviços contra tráfego potencialmente malicioso da internet e de outros locais externos. Proteja os seus ativos contra ataques DDoS, permitindo a proteção padrão do DDoS nas suas redes virtuais Azure. Utilize o Azure Security Center para detetar riscos de configuração errada relacionados com os seus recursos relacionados com a rede.

A Configuração de Aplicações Azure não se destina a executar aplicações web, fornece a configuração para estas aplicações web. Não é obrigado a configurar quaisquer configurações adicionais ou a implementar quaisquer serviços de rede extra para protegê-lo de ataques de rede externos direcionados para aplicações web.

- [Documentação da Firewall Azure](../firewall/index.yml)

- [Gerir o Padrão de Proteção Azure DDoS utilizando o portal Azure](../ddos-protection/manage-ddos-protection.md)

- [Recomendações do Centro de Segurança do Azure](../security-center/recommendations-reference.md#recs-network)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: Implementar sistemas de deteção/prevenção de intrusões (IDS/IPS)

**Orientação**: Utilize o Azure Firewall com filtros baseados em inteligência de ameaça para alertar e/ou bloquear o tráfego de e/para endereços ip maliciosos conhecidos e domínios. Os domínios e endereços IP são obtidos a partir do feed das Informações sobre Ameaças da Microsoft. Quando a inspeção de carga útil é necessária, você pode implementar uma solução IDS/IPS de terceiros a partir de Azure Marketplace com capacidades de inspeção de carga útil. Alternadamente, pode optar por utilizar iDS/IPS baseado no hospedeiro ou uma solução de deteção e resposta de ponto final (EDR) baseada no hospedeiro em conjunto com ou em vez de IDS/IPS baseados em rede.

Nota: Se tiver um requisito regulamentar ou outro para a utilização do IDS/IPS, certifique-se de que está sempre sintonizado para fornecer alertas de alta qualidade à sua solução SIEM.

- [Como implantar a Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [O Azure Marketplace inclui capacidades de IDS de terceiros](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Capacidade ATP EDR do Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: Simplificar as regras de segurança da rede

**Orientação**: Utilize tags de serviço de rede virtual Azure para definir controlos de acesso à rede em grupos de segurança de rede ou Azure Firewall configurados para os seus recursos de Configuração de Aplicações. Pode utilizar a etiqueta de serviço "AppConfiguration" no lugar de endereços IP específicos ao criar regras de segurança para tráfego de saída na rede da sua aplicação. Ao especificar o nome da etiqueta de serviço no campo de origem ou destino apropriado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

- [Compreender e utilizar tags de serviço](../virtual-network/service-tags-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-management"></a>Gestão de Identidades

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão de Identidades](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Uniformizar o Azure Active Directory como o sistema central de identidade e autenticação

**Orientação**: A Azure App Configuration está integrada com o Azure Ative Directory (Azure AD) que é o serviço de gestão de identidade e acesso padrão da Azure. Deve normalizar a Azure AD para governar a gestão de identidade e acesso da sua organização em:
- Recursos da Microsoft Cloud, como o portal Azure, Azure Storage, Azure Virtual Machine (Linux e Windows), Azure Key Vault, PaaS e SaaS.
- recursos da sua organização, como as aplicações no Azure, ou os recursos da rede empresarial.

A proteção do Azure AD deve ser prioritária na prática de segurança da cloud para a sua organização. O Azure AD fornece uma pontuação segura de identidade para ajudá-lo a avaliar a postura de segurança de identidade em relação às recomendações de boas práticas da Microsoft. Utilize a pontuação para determinar até que ponto é que a sua configuração corresponde às recomendações de melhores práticas e para fazer melhorias à postura de segurança.

O Azure fornece as seguintes funções incorporadas do Azure para autorizar o acesso aos dados de Configuração de Aplicações utilizando Azure AD e OAuth:

- Proprietário de dados de configuração de aplicativos: Utilize esta função para dar acesso de leitura/escrita/exclusão aos dados de Configuração de Aplicações. Isto não permite o acesso ao recurso de Configuração de Aplicações.

- Leitor de Dados de Configuração de Aplicativos: Utilize esta função para dar acesso à leitura dos dados de Configuração da Aplicação. Isto não permite o acesso ao recurso de Configuração de Aplicações.

- Contribuinte: Utilize esta função para gerir o recurso de Configuração de Aplicações. Embora os dados de Configuração da Aplicação possam ser acedidos através de teclas de acesso, esta função não concede acesso direto aos dados utilizando o Azure AD.

- Leitor: Utilize esta função para dar acesso à leitura do recurso De configuração da aplicação. Isto não permite o acesso às teclas de acesso do recurso, nem aos dados armazenados na Configuração da Aplicação.

Para obter mais informações, veja as seguintes referências:

- [Como criar e configurar instâncias do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Qual é a pontuação de segurança de identidade no Azure Ative Directory](../active-directory/fundamentals/identity-secure-score.md)

- [Autorizar o acesso à Configuração de Aplicações Azure usando Azure AD](concept-enable-rbac.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Gerir as identidades da aplicação de forma segura e automática

**Orientação**: Utilize identidades geridas pelo Azure para aceder à Configuração da Aplicação Azure a partir de contas não humanas, como outros serviços Azure. Recomenda-se a utilização de funcionalidades de identidade geridas pelo Azure em vez de criar uma conta humana mais poderosa para aceder ou executar os seus recursos para limitar a necessidade de gerir credenciais adicionais. A Azure App Configuration também pode ser atribuída uma identidade gerida para autenticar de forma nativa a outros serviços/recursos Azure que suportam a autenticação AD do Azure. Isto pode ser útil para permitir um fácil acesso da Configuração de Aplicações ao Cofre de Chaves Azure ao recuperar segredos. Ao utilizar identidades geridas, a identidade é gerida pela plataforma Azure e não requer que forneça ou rode quaisquer segredos.

A Azure App Configuration suporta a sua aplicação recebendo dois tipos de identidades:
- Uma identidade atribuída ao sistema está ligada ao seu recurso de configuração. É eliminado se o seu recurso de configuração for eliminado. Um recurso de configuração só pode ter uma identidade atribuída ao sistema.
- Uma identidade atribuída ao utilizador é um recurso autónomo da Azure que pode ser atribuído ao seu recurso de configuração. Um recurso de configuração pode ter várias identidades atribuídas pelo utilizador.

Quando as identidades geridas não puderem ser alavancadas, crie um principal de serviço com permissões restritas ao nível de recursos de Configuração de Aplicação Azure. Configure estes princípios de serviço com credenciais de certificado e apenas recue para os segredos dos clientes. Em ambos os casos, o Azure Key Vault pode ser usado em conjunto com identidades geridas aZure, de modo que o ambiente de tempo de execução (por exemplo, uma função Azure) pode recuperar a credencial do cofre da chave.

- [Como utilizar identidades geridas para configuração de aplicações Azure](overview-managed-identity.md)

- [Identidades geridas por Azure](../active-directory/managed-identities-azure-resources/overview.md)

- [Serviços que suportam identidades geridas para recursos da Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Utilizar identidades geridas para aceder ao App Configuration](howto-integrate-azure-managed-service-identity.md)

- [Principal de serviço do Azure](/powershell/azure/create-azure-service-principal-azureps) 

- [Criar um principal de serviço com certificados](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [Use o cofre da chave Azure para o registo principal de segurança](../key-vault/general/authentication.md#app-identity-and-security-principals)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Utilizar o início de sessão único (SSO) do Azure AD para acesso a aplicações

**Orientação**: A Azure App Configuration usa O Azure Ative Directory (Azure AD) para fornecer gestão de identidade e acesso aos recursos Azure, aplicações em nuvem e aplicações no local. Essas identidades incluem identidades empresariais, como colaboradores, bem como identidades externas, como parceiros, fornecedores e distribuidores. O Azure AD permite que um único sign-on (SSO) gere o serviço de Configuração de Aplicações através do portal Azure utilizando quaisquer identidades sincronizadas do Ative Directory corporativo sincronizado. Ligue todos os seus utilizadores, aplicações e dispositivos ao Azure AD para beneficiar de acesso seguro e ininterrupto e de mais visibilidade e controlo.

- [Compreender o SSO de Aplicações com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Utilizar controlos de autenticação fortes para todos os acessos baseados no Azure Active Directory

**Orientação**: A Azure App Configuration usa O Azure Ative Directory que suporta controlos de autenticação forte através da autenticação de vários fatores (MFA) e métodos fortes sem palavras-passe.
- Autenticação multifator - ative o MFA do Azure AD e siga as recomendações de Gestão de Identidades e Acessos do Centro de Segurança do Azure para obter algumas melhores práticas na configuração do MFA. O MFA pode ser imposto em todos os utilizadores, utilizadores específicos ou utilizador a utilizador com base em condições de início de sessão e fatores de risco.
- Autenticação sem palavra-passe - estão disponíveis três opções de autenticação sem palavra-passe: Windows Hello para Empresas, aplicação Microsoft Authenticator e métodos de autenticação no local, como cartões inteligentes.

Para os utilizadores de administrador e privilegiados, certifique-se de que é utilizado o mais alto nível de método de autenticação forte, seguindo-se a aplicação da política de autenticação forte adequada a outros utilizadores.

Nota: O MFA pode ser aplicado nas contas de utilizador que estão a aceder e gerir a Configuração de Aplicações, mas não em contas de serviço programático. Utilize a autenticação sem palavras-passe, como identidades geridas, sempre que possível, e aplique MFA em quaisquer contas de utilizador.

- [Como ativar o MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Introdução às opções de autenticação sem palavra-passe para o Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorizar e alertar para anomalias em contas

**Orientação**: A configuração da aplicação Azure está integrada com o Azure Ative Directory no qual fornece as seguintes fontes de dados:

-   Inícios de sessão – o relatório de inícios de sessão faculta informações sobre a utilização de aplicações geridas e das atividades de início de sessão dos utilizadores.

-   Registos de auditoria - Fornece rastreabilidade através de registos para todas as alterações escoradas através de várias funcionalidades em Azure AD. Exemplos de alterações registadas nos registos de auditoria incluem adicionar ou remover utilizadores, apps, grupos, funções e políticas.

-   Inícios de sessão de risco – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador.

-   Utilizadores sinalizados para risco – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida.

Estas fontes de dados podem ser integradas com sistemas Azure Monitor, Azure Sentinel ou siem de terceiros.

O Azure Security Center também pode alertar para certas atividades suspeitas, como um número excessivo de tentativas de autenticação falhadas e contas precíídas na subscrição. 

A Azure Advanced Threat Protection (ATP) é uma solução de segurança que pode usar sinais de Ative Directory no local para identificar, detetar e investigar ameaças avançadas, identidades comprometidas e ações de insider maliciosas.

- [Relatórios de atividades de auditoria em Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como identificar utilizadores do Azure AD sinalizados por atividade de risco](../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorizar as atividades de identidade e acesso dos utilizadores no Centro de Segurança do Azure](../security-center/security-center-identity-access.md)

- [Alertas no módulo de proteção de informações sobre ameaças do Centro de Segurança do Azure](../security-center/alerts-reference.md)

- [Como integrar os registos de atividade do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Ligar dados da Azure AD Identity Protection](../sentinel/connect-azure-ad-identity-protection.md)

- [Azure Advanced Threat Protection](/azure-advanced-threat-protection/what-is-atp)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Restringir o acesso a recurso Azure com base em condições

**Orientação**: A Azure App Configuration suporta o acesso condicional do Azure Ative Directory (Azure AD) para um controlo de acesso mais granular baseado em condições definidas pelo utilizador, como logins de utilizadores de determinadas gamas IP terão de utilizar MFA para login. A política de gestão da sessão de autenticação granular também pode ser usada para diferentes casos de utilização. Estas políticas de acesso condicional aplicar-se-ão apenas às contas dos utilizadores que estejam a autenticar a AZure AD para aceder e gerir o serviço de Configuração de Aplicações, mas não se aplicarão aos principais serviços ou cadeias de ligação que se liguem ao seu recurso de configuração.

- [Visão geral do acesso condicional do Azure](../active-directory/conditional-access/overview.md)

- [Políticas comuns de acesso condicional](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Configurar gestão de sessão de autenticação com acesso condicional](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminar exposição credencial não intencional

**Orientação**: A configuração da aplicação Azure permite que os clientes armazenem configurações que possam conter identidades ou segredos. Recomenda-se implementar o Scanner Credencial para identificar credenciais dentro das configurações. O Scanner Credencial também encorajará a mudança de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

Utilize o serviço de configuração de aplicações Azure juntamente com o Cofre da Chave Azure. Guarde quaisquer credenciais no Key Vault e, em seguida, ligue-se a estas credenciais criando uma referência key Vault no seu recurso de configuração de aplicação. Quando a Configuração da Aplicação cria estas referências, armazena os URIs dos valores do Cofre chave em vez dos próprios valores. As aplicações podem ligar-se à Configuração de Aplicações para recuperar quaisquer credenciais do Key Vault.

Para o GitHub, pode utilizar funcionalidades de digitalização secretas nativas para identificar credenciais ou outra forma de segredo dentro do código.

- [Tutorial para usar referências de cofre de chaves em uma ASP.NET Core App](use-key-vault-references-dotnet-core.md)

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Digitalização secreta de GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="privileged-access"></a>Acesso Privilegiado

*Para obter mais informações, veja [Referência de Segurança do Azure: Acesso Privilegiado](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Proteger e limitar utilizadores com muitos privilégios

**Orientação**: Limitar o número de contas ou funções altamente privilegiadas e proteger estas contas a um nível elevado, porque os utilizadores com este privilégio podem ler e modificar direta ou indiretamente todos os recursos do seu ambiente Azure.

Pode ativar o acesso privilegiado just-in-time (JIT) nos recursos do Azure e no Azure AD com o Azure AD Privileged Identity Management (PIM). O JIT concede permissões temporárias para realizar tarefas privilegiadas apenas quando os utilizadores precisam. O PIM também pode gerar alertas de segurança em caso de atividades suspeitas ou inseguras na sua organização do Azure AD.

As Chaves de Acesso são altamente privilegiadas e devem ser rodadas regularmente como uma melhor prática de segurança. As teclas de acesso contêm cordas de ligação, que contêm informações credenciais e são consideradas segredos. Estes segredos precisam de ser guardados no Cofre da Chave Azure, e o seu código deve autenticar-se no Cofre de Chaves para os recuperar. As Chaves de Acesso podem dar leitura ou apenas ler o acesso a uma aplicação. Certifique-se de que o tipo correto de chave de acesso é emitido para evitar o acesso não autorizado. Para ser mais seguro, utilize as identidades geridas em Azure AD. Isto requer apenas que as aplicações tenham o URL de ponto final de configuração para aceder aos valores de configuração.

- [Configuração de aplicativos Melhores Práticas](howto-best-practices.md#app-configuration-bootstrap)

- [Utilizar identidades geridas para aceder ao App Configuration](howto-integrate-azure-managed-service-identity.md)
- [Permissões das funções de administrador no Azure AD](../active-directory/roles/permissions-reference.md)

- [Utilizar os alertas de segurança do Azure Privileged Identity Management](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Proteção de acesso privilegiado para implementações híbridas e na cloud no Azure AD](../active-directory/roles/security-planning.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Restringir o acesso administrativo a sistemas críticos de negócios

**Orientação**: A Azure App Configuration usa O RBAC Azure para isolar o acesso a sistemas críticos de negócio, limitando quais as contas que têm acesso privilegiado. O Azure RBAC é suportado pela Configuração de Aplicações ao nível dos recursos. Para obter configurações críticas de negócio silo de forma segura, armazenar esta informação no seu próprio recurso de Configuração de Aplicações. Dentro de um recurso, o acesso granular também está disponível através de contas ou chaves de acesso apenas de leitura, bem como rotulagem e marcação.

Todos os tipos de controlos de acesso devem ser alinhados com a sua estratégia de segmentação da empresa para garantir um controlo de acesso consistente.

- [Acesso ao Grupo de Gestão](../governance/management-groups/overview.md#management-group-access)

- [Administradores de subscrição da Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Para integrar o RBAC usando AZure AD com configuração de aplicação](concept-enable-rbac.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Rever e reconciliar o acesso dos utilizadores regularmente

**Orientação**: A Azure App Configuration utiliza contas Azure Ative (Azure AD) para gerir os seus recursos, rever as contas dos utilizadores e aceder regularmente para garantir que as contas e o seu acesso são válidos. 

O Azure fornece as seguintes funções incorporadas do Azure para autorizar o acesso aos dados de Configuração de Aplicações utilizando Azure AD e OAuth:

- Proprietário de dados de configuração de aplicativos: Utilize esta função para dar acesso de leitura/escrita/exclusão aos dados de Configuração de Aplicações. Isto não permite o acesso ao recurso de Configuração de Aplicações.

- Leitor de Dados de Configuração de Aplicativos: Utilize esta função para dar acesso à leitura dos dados de Configuração da Aplicação. Isto não dá acesso ao recurso de Configuração de Aplicações

Pode utilizar comentários de acesso Azure para rever membros do grupo, acesso a aplicações empresariais e atribuições de funções, como as funções de Configuração de Aplicações acima. O relatório AD AD do Azure pode fornecer registos para ajudar a descobrir contas velhas. Também pode utilizar a Azure AD Privileged Identity Management para criar o fluxo de trabalho do relatório de revisão de acessos para facilitar o processo de revisão.

Nota: As identidades geridas são sugeridas sempre que possível para autenticação na Configuração de Aplicações a partir de outro serviço ou aplicação. Será necessário gerir quaisquer princípios de serviço ou cordas de ligação configurados com acesso à Configuração de Aplicações separadamente quando utilizado.

- [Criar uma revisão de acesso das funções de recursos da Azure na Gestão de Identidade Privilegiada (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Como utilizar as revisões de identidades e acessos do Azure AD](/azure/active-directory/governance/access-reviews-overview)

- [Autorizar o acesso à Configuração de Aplicações Azure usando Azure AD](concept-enable-rbac.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Criar acesso de emergência em Azure AD

**Orientação**: A Azure App Configuration está integrada com o Azure Ative Directory para gerir os seus recursos. Para evitar que seja acidentalmente bloqueado fora da sua organização Azure AD, crie uma conta de acesso de emergência para acesso quando não puder ser utilizada uma conta administrativa normal. As contas de acesso de emergência são geralmente altamente privilegiadas, e não devem ser atribuídas a indivíduos específicos. As contas de acesso de emergência limitam-se a cenários de emergência ou "vidro quebrado", onde as contas administrativas normais não podem ser utilizadas.

Deve certificar-se de que as credenciais (como palavra-passe, certificado ou cartão inteligente) para contas de acesso de emergência são mantidas seguras e conhecidas apenas por indivíduos autorizados a usá-las apenas em caso de emergência.

- [Gerir contas de acesso de emergência em Azure AD](../active-directory/roles/security-emergency-access.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Gestão de direitos de automatização 

**Orientação**: A Azure App Configuration está integrada com o Azure Ative Directory para gerir os seus recursos. Utilize funcionalidades de gestão de direitos Azure AD para automatizar fluxos de trabalho de pedidos de acesso, incluindo atribuições de acesso, revisões e expiração. A aprovação dual ou multi-fase também é apoiada.

- [O que são avaliações de acesso AZure AD](../active-directory/governance/access-reviews-overview.md)

- [O que é a gestão de direitos da AD Azure](../active-directory/governance/entitlement-management-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Utilize postos de trabalho privilegiados de acesso

**Orientação**: Estações de trabalho seguras e isoladas são de importância crucial para a segurança de funções sensíveis como administradores, desenvolvedores e operadores de serviços críticos. Utilize estações de trabalho de utilizador altamente seguras e/ou Azure Bastion para tarefas administrativas relacionadas com a Configuração da Aplicação. Utilize o Azure Ative Directory, Microsoft Defender Advanced Threat Protection (ATP) e/ou Microsoft Intune para implementar uma estação de trabalho segura e gerida para tarefas administrativas. As estações de trabalho seguras podem ser geridas centralmente para impor uma configuração segura, incluindo bases de autenticação forte, software e hardware, restrições lógicos e acesso à rede.

- [Compreender estações de trabalho de acesso privilegiada](../active-directory/devices/concept-azure-managed-workstation.md) 

- [Implementar uma estação de trabalho de acesso privilegiada](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Seguir a abordagem de Administração Suficiente (princípio do privilégio mínimo) 

**Orientação**: A Configuração de aplicações Azure está integrada com o controlo de acesso baseado em funções Azure (RBAC) para gerir os seus recursos. O RBAC do Azure permite-lhe gerir o acesso aos recursos do Azure através da atribuição de funções. Pode atribuir estas funções aos utilizadores, grupos de diretores de serviço e identidades geridas. Existem papéis pré-definidos para configuração de aplicativos Azure, e estes papéis podem ser inventariados ou consultados através de ferramentas como Azure CLI, Azure PowerShell ou o portal Azure. Os privilégios que atribuir aos recursos através do RBAC do Azure devem estar sempre limitados àquilo que as funções requerem. Esta abordagem complementa a abordagem just-in-time (JIT) do Azure AD Privileged Identity Management (PIM) e deve ser revista periodicamente.

O Azure fornece as seguintes funções incorporadas do Azure para autorizar o acesso aos dados de Configuração de Aplicações utilizando Azure AD e OAuth:
- Proprietário de dados de configuração de aplicativos: Utilize esta função para dar acesso de leitura/escrita/exclusão aos dados de Configuração de Aplicações. Isto não permite o acesso ao recurso de Configuração de Aplicações.
- Leitor de Dados de Configuração de Aplicativos: Utilize esta função para dar acesso à leitura dos dados de Configuração da Aplicação. Isto não permite o acesso ao recurso de Configuração de Aplicações.

Utilize funções incorporadas para alocar a permissão e apenas criar funções personalizadas quando necessário. 

A Configuração da Aplicação suporta a configuração de armazenamento de múltiplas aplicações num recurso de Configuração de Aplicações. Para limitar o acesso à informação entre aplicações, crie um recurso de Configuração de Aplicação por aplicação e crie o Azure RBAC em conformidade.

- [O que é o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md)

- [Como configurar o RBAC no Azure](../role-based-access-control/role-assignments-portal.md)

- [Como utilizar as revisões de identidades e acessos do Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Autorizar o acesso à Configuração de Aplicações Azure usando Azure AD](concept-enable-rbac.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Escolha o processo de aprovação para suporte da Microsoft  

**Orientação**: Implementar um processo de aprovação organizacional para cenários de suporte onde a Microsoft possa precisar de acesso aos dados de Configuração da aplicação. O Bloqueio do Cliente não está atualmente disponível para cenários de suporte à configuração de aplicações.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1: Descubra, classifique e identifique dados sensíveis

**Orientação**: Descubra, classifique e rotule os seus dados sensíveis para que possa conceber os controlos apropriados para garantir que informações sensíveis são armazenadas, processadas e transmitidas de forma segura pelos sistemas tecnológicos da organização. A rotulagem para informações sensíveis, sob a forma de marcação, é suportada para os recursos de Configuração de Aplicações, mas não para valores de configuração contidos dentro deles. Uma vez que uma aplicação tenha lido ou lido/escrito acesso a uma loja de configuração, tem acesso total a qualquer uma das configurações dessa loja.

- [Etiquetar informações confidenciais com o Azure Information Protection](/azure/information-protection/what-is-information-protection)

- [Classificação de dados de marcação em Azure](/azure/cloud-adoption-framework/govern/policy-compliance/data-classification#tagging-data-classification-in-azure)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Proteger dados confidenciais

**Orientação**: Para a plataforma subjacente, que é gerida pela Microsoft, a Microsoft trata todos os conteúdos do cliente como sensíveis e protege contra a perda e exposição de dados do cliente. Para assegurar que os dados permanecem seguros no Azure, a Microsoft implementou alguns controlos e capacidades de proteção de dados predefinidos. Certifique-se de que está a rodar regularmente as teclas de acesso aos seus recursos de Configuração de Aplicação. As informações credenciais das cordas de ligação podem ser armazenadas no Cofre da Chave Azure, e o seu código deve autenticar-se no Cofre de Chaves para as recuperar. As Chaves de Acesso podem dar leitura ou apenas ler o acesso a uma aplicação. Certifique-se de que o tipo correto de chave de acesso é emitido para evitar o acesso não autorizado. Para ser mais seguro, utilize as identidades geridas em Azure AD. Isto requer apenas que as aplicações tenham o URL de ponto final de configuração para aceder aos valores de configuração.

Restringir o acesso utilizando o controlo de acesso baseado em funções Azure (Azure RBAC):

- Separar dados sensíveis no seu próprio recurso de configuração de aplicativos, em seguida, alocar as políticas do RBAC em conformidade para que apenas o acesso autorizado esteja ativado 

- Utilizar controlos de acesso baseados em rede

- Controlos específicos nos serviços Azure (como encriptação em SQL e outras bases de dados) e garantir um controlo de acesso consistente, todos os tipos de controlo de acesso devem ser alinhados com a sua estratégia de segmentação da empresa.

- A estratégia de segmentação da empresa também deve ter conhecimento da localização dos dados confidenciais e dos sistemas críticos para a empresa.

Para obter mais informações, veja as seguintes referências:

- [Autorizar o acesso à Configuração de Aplicações Azure utilizando o Azure Ative Directory](concept-enable-rbac.md)

- [Encriptação de dados de configuração de aplicativos](faq.md#does-app-configuration-encrypt-my-data)

- [Controlo de Acesso Baseado em Funções do Azure (RBAC)](../role-based-access-control/overview.md) 

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Criptografar informações sensíveis em trânsito

**Orientação**: Para complementar os controlos de acesso, os dados em trânsito devem ser protegidos contra ataques "fora da banda" utilizando encriptação. Isto ajuda a garantir que os atacantes não podem ler ou modificar facilmente os dados.

A Configuração de Aplicações Azure utiliza encriptação TLS para todos os pedidos HTTP. A infraestrutura Azure fornece uma camada adicional de encriptação de nível de rede para todos os pedidos entre centros de dados Azure. Certifique-se de que qualquer cliente que se conecte aos seus recursos de Configuração de Aplicações pode negociar TLS v1.2 ou maior.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Encripte os dados confidenciais inativos

**Orientação**: Para complementar os controlos de acesso, os dados em repouso devem ser protegidos contra ataques "fora da banda" (como aceder ao armazenamento subjacente) utilizando encriptação. Isto ajuda a garantir que os atacantes não podem ler ou modificar facilmente os dados.

O Azure fornece dados em repouso encriptação por padrão. Para dados altamente sensíveis, você tem opções para implementar encriptação adicional em repouso em todos os recursos Azure, sempre que disponível. O Azure gere as suas chaves de encriptação por padrão, mas o Azure oferece a opção de gerir as suas próprias chaves (teclas geridas pelo cliente) para a Configuração da Aplicação Azure.

- [Utilize chaves geridas pelo cliente para encriptar os seus dados na Configuração da Aplicação Azure](concept-customer-managed-keys.md)

- [Compreender a encriptação de dados inativos no Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Modelo de encriptação e tabela de gestão chave](../security/fundamentals/encryption-atrest.md#azure-resource-providers-encryption-model-support)

- [Dados em repouso dupla encriptação em Azure](../security/fundamentals/double-encryption.md#data-at-rest)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="asset-management"></a>Gestão de Recursos

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão de Ativos](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Certifique-se de que a equipa de segurança tem visibilidade para os riscos dos ativos

**Orientação**: Confirme que as equipas de segurança recebem as permissões Leitor de Segurança no seu inquilino e nas suas subscrições do Azure, para que possam monitorizar os riscos de segurança com o Centro de Segurança do Azure. 

Dependendo da forma como as responsabilidades destas equipas são estruturadas, a monitorização dos riscos de segurança pode ser da responsabilidade de uma equipa de segurança central ou de uma equipa local. Dito isto, as informações e os riscos de segurança têm de ser sempre agregados centralmente nas organizações. 

As permissões Leitor de Segurança podem ser aplicadas de um modo amplo em todo um inquilino (Grupo de Gestão Raiz) ou dentro de âmbitos, como grupos de gestão ou subscrições específicas. 

Nota: para obter visibilidade para cargas de trabalho e serviços, poderão ser necessárias permissões adicionais. 

- [Descrição Geral da Função de Leitor de Segurança](../role-based-access-control/built-in-roles.md#security-reader)

- [Descrição Geral dos Grupos de Gestão do Azure](../governance/management-groups/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Garantir que a equipa de segurança tem acesso ao inventário de ativos e metadados

**Orientação**: Certifique-se de que as equipas de segurança têm acesso a um inventário continuamente atualizado de ativos no Azure, como a Configuração da App Azure. As equipas de segurança precisam frequentemente deste inventário para avaliar a exposição potencial da sua organização a riscos emergentes, e como um contributo para melhorias contínuas de segurança. Crie um grupo de Diretório Ativo Azure para conter a equipa de segurança autorizada da sua organização e atribua-lhes o acesso de leitura a todos os recursos de Configuração de Aplicações Azure, isto pode ser simplificado por uma única atribuição de papel de alto nível dentro da sua subscrição.

A funcionalidade de inventário do Azure Security Center e o Azure Resource Graph podem consultar e descobrir todos os recursos nas suas subscrições, incluindo serviços Azure, aplicações e recursos de rede.

Aplique etiquetas nos seus recursos Azure, grupos de recursos e subscrições para organizá-las logicamente numa taxonomia. Cada etiqueta é composta por um nome e um par de valor. Por exemplo, pode aplicar o nome "Ambiente" e o valor "Produção" em todos os recursos na produção.

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Gestão de inventário de ativos do Azure Security Center](../security-center/asset-inventory.md)

- [Para obter mais informações sobre a marcação de ativos, consulte o guia de decisão de nomeação e marcação de recursos](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Utilize apenas serviços Azure aprovados

**Orientação**: A Azure App Configuration suporta implementações baseadas em recursos Azure e aplicação de configuração usando a Política de Azure. Utilize a Política Azure para auditar e restringir quais os serviços que os utilizadores podem prestação no seu ambiente. Utilize o Gráfico de Recursos Azure para consultar e descobrir recursos dentro das suas subscrições. Também pode utilizar o Azure Monitor para criar regras para desencadear alertas quando for detetado um serviço não aprovado.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/built-in-policies.md#general)

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Garanta a segurança da gestão do ciclo de vida dos ativos

**Orientação**: Crie um atualize as políticas de segurança que visam os processos de gestão do ciclo de vida dos ativos para modificações com um potencial grande impacto. Estas alterações incluem alterações, mas não se limitam a: fornecedores de identidade e acesso, sensibilidade de dados, configuração de rede e atribuição de privilégios administrativos.

Quando já não precisar dos recursos do Azure, remova-os. Certifique-se de que os administradores giram regularmente as teclas de acesso para garantir que apenas os utilizadores autenticados tenham acesso ao seu recurso de configuração.

- [Rode as teclas de encriptação utilizadas para a configuração da aplicação](concept-customer-managed-keys.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resources Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-threat-detection"></a>Registos e Deteção de Ameaças

*Para obter mais informações, veja [Referência de Segurança do Azure: Registos e Deteção de Ameaças](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Ative a deteção de ameaças para a gestão de identidades e acessos do Azure

**Orientação**: A Configuração da Aplicação integra-se com o Azure Ative Directory (Azure AD). Isto fornece os seguintes registos de utilizador que podem ser vistos em relatórios AD Azure ou integrados com Azure Monitor, Azure Sentinel ou outras ferramentas SIEM/monitoring para casos de monitorização e utilização analíticos mais sofisticados:
- Inícios de sessão – o relatório de inícios de sessão faculta informações sobre a utilização de aplicações geridas e das atividades de início de sessão dos utilizadores.
- Registos de auditoria - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. Os exemplos de registos de auditoria incluem as alterações feitas a quaisquer recursos no Azure AD, como adicionar ou remover utilizadores, aplicações, grupos, funções e políticas.
- Inícios de sessão de risco – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador.
- Utilizadores sinalizados para risco – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida.

O Centro de Segurança do Azure também pode alertar para determinadas atividades suspeitas, como tentativas excessivas de autenticações mal-sucedidas ou contas descontinuadas na subscrição. Além da monitorização básica da higiene de segurança, o módulo de Proteção de Ameaças do Azure Security Center também pode recolher alertas de segurança mais aprofundados das camadas de serviço Azure. Esta capacidade dá-lhe visibilidade sobre anomalias de conta dentro dos recursos individuais.

Outro método para aceder ao seu recurso de configuração de configuração de aplicação de aplicações está a utilizar as teclas de acesso. Estes necessitam de ser rodados regularmente para garantir que nenhum agente não autorizado tenha acesso ao seu recurso de configuração. Rodá-las pode ser feita diretamente no portal em "teclas de acesso".

- [Para permitir que a Configuração da App Azure aceda a outros recursos protegidos Azure AD usando uma identidade gerida](overview-managed-identity.md)

- [Utilize identidades geridas com configuração de aplicações Azure](howto-integrate-azure-managed-service-identity.md)

- [Relatórios de atividades de auditoria no Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Ativar o Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Autorizar o acesso à configuração de aplicativos Azure usando Azure AD](concept-enable-rbac.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Permitir o registo de atividades da rede Azure

**Orientação**: A Azure App Configuration não implanta quaisquer recursos diretamente numa rede virtual. No entanto, a Configuração de Aplicações permite-lhe utilizar pontos finais privados para se conectar de forma segura à Configuração de Aplicações Azure a partir de uma rede virtual. A Azure App Configuration também não produz ou processa registos de consulta de DNS que teriam de ser ativados.

Ativar o registo nos pontos finais privados de configuração de configuração de aplicações configurados configurados para capturar:
- Dados tratados pelo Private Endpoint (IN/OUT)
- Dados tratados pelo serviço Private Link (IN/OUT)
- Disponibilidade da porta NAT

Para obter mais informações, veja as seguintes referências:

- [Monitorização de ligações privadas Azure](../private-link/private-link-overview.md#logging-and-monitoring)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Permitir a exploração de madeira para recursos Azure

**Orientação**: Os registos de atividade, que estão automaticamente disponíveis, contêm todas as operações de escrita (PUT, POST, DELETE) para os seus recursos de Configuração de Aplicações, exceto operações de leitura (GET). Os registos de atividade podem ser utilizados para encontrar um erro na resolução de problemas ou para monitorizar como um utilizador na sua organização modificou um recurso. Para a Configuração da Aplicação, os registos de atividade só estão disponíveis no plano de controlo e são surgidos pelo Gestor de Recursos Azure (ARM). O registo de planos de dados para configuração de aplicações não é suportado pelo cliente. Os registos de recursos Azure também não estão disponíveis para serem configurados.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Compreenda o registo e diferentes tipos de registo em Azure](../azure-monitor/platform/platform-logs-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizar a gestão e análise de registos de segurança

**Orientação**: Centralizar o armazenamento e análise de registos para permitir a correlação. Para cada fonte de registo, certifique-se de que atribuiu um titular de dados, orientação de acesso, localização de armazenamento, que ferramentas são usadas para processar e aceder aos dados, e requisitos de retenção de dados.

Certifique-se de que está a integrar os registos de atividade do Azure na sua sessão central. Ingerir registos via Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto final, recursos de rede e outros sistemas de segurança. No Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de Armazenamento Azure para armazenamento de longo prazo e arquivo.

Além disso, ative e a bordo dados para Azure Sentinel ou um SIEM de terceiros. Muitas organizações optam por usar o Azure Sentinel para dados "quentes" que são usados frequentemente e o Azure Storage para dados "frios" que são usados com menos frequência.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Retenção de armazenamento de madeira configurada

**Orientação**: Certifique-se de que quaisquer contas de armazenamento ou espaços de trabalho do Log Analytics utilizados para armazenar registos de configuração de aplicações tem o período de retenção de registo definido de acordo com os regulamentos de conformidade da sua organização. Utilize o espaço de trabalho Azure Storage, Data Lake ou Log Analytics para armazenamento de longo prazo e arquivo.

No Azure Monitor, pode definir o período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização.

- [Como configurar o período de retenção do espaço de trabalho do Log Analytics](../azure-monitor/platform/manage-cost-storage.md)

- [Armazenar registos de recursos numa conta de armazenamento Azure](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparação – atualizar o processo de resposta a incidentes para o Azure

**Orientação**: Certifique-se de que a sua organização tem processos para responder a incidentes de segurança, que atualizou esses processos para o Azure e que os pratica regularmente para garantir a preparação.

- [Implementar a segurança em todo o ambiente empresarial](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de referência da resposta a incidentes](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Preparação – configurar a notificação de incidentes

**Orientação**: Configure as informações de contacto dos incidentes de segurança no Centro de Segurança do Azure. A Microsoft utiliza estas informações de contacto para o contactar caso o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos de forma ilícita ou não autorizada. Também tem opções para personalizar os alertas e as notificações de incidentes em diferentes serviços do Azure de acordo com as suas necessidades de resposta aos incidentes. 

- [Como definir o contacto de segurança do Centro de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Deteção e análise – criar incidentes com base em alertas de alta qualidade

**Orientação**: Certifique-se de que tem um processo para criar alertas de alta qualidade e medir a qualidade dos alertas. Desta forma, pode aprender lições com os incidentes passados e priorizar os alertas para os analistas, para que estes não percam tempo a lidar com falsos positivos. 

Alertas de alta qualidade podem ser construídos com base na experiência de incidentes passados, fontes comunitárias validadas e ferramentas projetadas para gerar e limpar alertas fundindo e correlacionando diversas fontes de sinal. 

O Azure Security Center fornece alertas de alta qualidade em muitos ativos da Azure. Pode utilizar o conector de dados ASC para transmitir os alertas para o Azure Sentinel. O Azure Sentinel permite-lhe criar regras de alertas avançadas para gerar incidentes automaticamente para investigações. 

Exporte os alertas e as recomendações do Centro de Segurança do Azure com a funcionalidade de exportação para ajudar a identificar riscos para os recursos do Azure. Exporte os alertas e as recomendações manualmente ou de forma contínua.

- [Como configurar a exportação](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Deteção e análise – investigar incidentes

**Orientação**: Certifique-se de que os analistas podem consultar e utilizar várias origens de dados para investigar potenciais incidentes e ter uma perspetiva geral do que aconteceu. Para monitorizar as atividades de um potencial atacante em toda a rede do ataque e evitar ângulos mortos, devem ser recolhidos diversos registos.  Também deve assegurar que são apreendidas informações e aprendizagens para outros analistas e para informação histórica futura.  

As origens de dados para investigação incluem as origens de registos centralizadas que já estão a ser recolhidas pelos serviços dentro do âmbito e pelos sistemas em execução, mas podem também incluir:

- Dados de rede – utilize os registos de fluxos dos grupos de segurança de rede, o Observador de Rede do Azure e o Azure Monitor para capturar os registos de fluxos de rede e outras informações analíticas. 

- Instantâneos dos sistemas em execução: 

    - Utilize a capacidade de instantâneos das Máquinas Virtuais do Azure para criar um instantâneo do disco do sistema em execução. 

    - Utilize a capacidade de captura da memória nativa do sistema operativo para criar um instantâneo da memória do sistema operativo.

    - Utilize a funcionalidade de instantâneos dos serviços do Azure ou a capacidade do seu próprio software para criar instantâneos dos sistemas em execução.

O Azure Sentinel disponibiliza uma grande quantidade de análises de dados em praticamente qualquer origem de registos e um portal de gestão de casos para gerir o ciclo de vida completo dos incidentes. As informações de inteligência durante uma investigação podem ser associadas a um incidente para fins de monitorização e reporte. 

- [Criar um instantâneo de um disco de uma máquina Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Criar um instantâneo de um disco de uma máquina Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Informações de diagnóstico e recolha da captura da memória do Suporte do Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Investigar incidentes com o Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Deteção e análise – priorizar incidentes

**Orientação**: Dê contexto aos analistas quanto aos incidentes em que se devem concentrar primeiro tendo por base a gravidade dos alertas e a sensibilidade dos ativos. 

O Centro de Segurança do Azure atribui uma gravidade a cada alerta para o ajudar a priorizar os que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou na análise usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque os recursos com etiquetas e crie um sistema de nomenclatura para identificar e categorizar os recursos do Azure, especialmente aqueles que processam dados confidenciais.  É da sua responsabilidade priorizar a remediação dos alertas de acordo com a criticalidade dos recursos do Azure e o ambiente em que os incidentes ocorreram.

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Contenção, erradicação e recuperação – automatizar o processamento dos incidentes

**Orientação**: Automatize tarefas manuais repetitivas para acelerar o tempo de resposta e reduzir a carga dos analistas. As tarefas manuais são mais morosas e atrasam todos os incidentes, reduzindo o número daqueles com que um analista pode lidar. Estas tarefas também aumentam a fadiga dos analistas, o que aumenta o risco de erro humanos e provoca atrasos, bem como degrada a capacidade de aqueles se concentrarem em tarefas complexas. Utilize as funcionalidades de automatização de fluxos de trabalho do Centro de Segurança do Azure e do Azure Sentinel para acionar automaticamente ações ou executar um manual de procedimentos para responder aos alertas de segurança recebidos. O manual de procedimentos efetua ações, como o envio de notificações, a desativação de contas e o isolamento de redes problemáticas. 

- [Configurar a automatização dos fluxos de trabalho no Centro de Segurança](../security-center/workflow-automation.md)

- [Configurar respostas automatizadas a ameaças no Centro de Segurança do Azure](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurar respostas automatizadas a ameaças no Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="posture-and-vulnerability-management"></a>Gestão da Postura e da Vulnerabilidade

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão da Postura e da Vulnerabilidade](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Estabelecer configurações seguras para os serviços Azure 

**Orientação**: A Azure App Configuration suporta abaixo políticas específicas do serviço que estão disponíveis no Azure Security Center para auditar e impor configurações dos seus recursos Azure. Isto pode ser configurado em iniciativas do Azure Security Center ou da Azure Policy.
- A Configuração da Aplicação deve utilizar uma chave gerida pelo cliente: as teclas geridas pelo cliente fornecem uma proteção de dados melhorada, permitindo-lhe gerir as suas chaves de encriptação. Isto é frequentemente necessário para satisfazer os requisitos de conformidade.
- A Configuração da Aplicação deve utilizar um link privado: As ligações de ponto final privado permitem que os clientes de uma rede virtual acedam de forma segura à Configuração da Aplicação Azure por um link privado.

Você pode usar Azure Blueprints para automatizar a implementação e configuração de serviços e ambientes de aplicação, incluindo modelos de gestores de recursos Azure, controlos rbac Azure e políticas, numa única definição de projeto.

- [Mais informações sobre as políticas de Configuração de Aplicativos](../governance/policy/samples/built-in-policies.md#app-configuration)

- [Trabalhar com políticas de segurança no Azure Security Center](../security-center/tutorial-security-policy.md)

- [Ilustração da implementação de Guardrails na Zona de Aterragem da Escala Empresarial](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Tutorial: Criar e gerir políticas para impor o cumprimento](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../governance/blueprints/overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Sustente configurações seguras para serviços Azure

**Orientação**: Utilize o Centro de Segurança Azure para monitorizar a sua linha de base de configuração e impor a política do Azure. A política do Azure para a configuração da aplicação inclui: 
- A Configuração da Aplicação deve utilizar uma chave gerida pelo cliente: as teclas geridas pelo cliente fornecem uma proteção de dados melhorada, permitindo-lhe gerir as suas chaves de encriptação. Isto é frequentemente necessário para satisfazer os requisitos de conformidade.
- A Configuração da Aplicação deve utilizar um link privado: As ligações de ponto final privado permitem que os clientes de uma rede virtual acedam de forma segura à Configuração da Aplicação Azure por um link privado.

- [Compreender os efeitos da Política Azure](../governance/policy/concepts/effects.md) 

- [Criar e gerir políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Realizar simulações de ataques regulares

**Orientação**: Conforme necessário, faça testes de penetração ou atividades de "equipa de ataque" nos seus recursos do Azure e assegure a remediação de todas as descobertas de segurança críticas.
Para ter a certeza de que os seus testes de penetração não infringem as políticas da Microsoft, siga as Regras de Interação para Testes de Penetração da Microsoft Cloud. Utilize a estratégia e a execução de "Equipas de Ataque" e os testes de penetração no local em direto da Microsoft na infraestrutura, nos serviços e nas aplicações cloud geridas pela Microsoft.

- [Testes de Penetração no Azure](../security/fundamentals/pen-testing.md)

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="backup-and-recovery"></a>Cópia de Segurança e Recuperação

*Para mais informações, consulte o [Benchmark de Segurança Azure: Backup and Recovery](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Mitigar o risco de chaves perdidas

**Orientação**: Certifique-se de que tem medidas para prevenir e recuperar da perda de chaves. Ativar a proteção de eliminação e purga suave no Cofre da Chave Azure para proteger as chaves contra a eliminação acidental ou maliciosa.

- [Como permitir a eliminação suave e a proteção de purga no Cofre de Chaves](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="governance-and-strategy"></a>Governação e Estratégia

*Para obter mais informações, veja [Referência de Segurança do Azure: Governação e Estratégia](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definir a gestão dos ativos e a estratégia de proteção de dados 

**Orientação**: Certifique-se de que documenta e comunica uma estratégia clara para a monitorização e proteção contínua dos sistemas e dos dados. Priorize a descoberta, a avaliação, a proteção e a monitorização de dados e sistemas críticos para a empresa. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Norma para a classificação de dados, de acordo com os riscos da atividade

-   Visibilidade da organização de segurança para os riscos e inventário de ativos 

-   Aprovação da organização de segurança dos serviços do Azure a utilizar 

-   Segurança dos ativos ao longo do ciclo de vida

-   Estratégia de controlo de acesso obrigatória, de acordo com a classificação dos dados organizacionais

-   Utilização de capacidades de proteção de dados nativas e de terceiros da Azure

-   Requisitos de encriptação de dados para casos de utilização de dados em trânsito e inativos

-   Normas criptográficas adequadas

Para obter mais informações, veja as seguintes referências:
- [Recomendação de Arquitetura de Segurança do Azure - Armazenamento, dados e encriptação](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Noções Básicas da Segurança do Azure - Segurança, encriptação e armazenamento de dados do Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework - Melhores práticas de segurança e encriptação de dados do Azure](../security/fundamentals/data-encryption-best-practices.md?amp;bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Referência de Segurança do Azure - Gestão de ativos](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Referência de Segurança do Azure - Proteção de dados](/azure/security/benchmarks/security-controls-v2-data-protection)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definir a estratégia de segmentação da empresa 

**Orientação**: Estabeleça uma estratégia ao nível de toda a empresa para segmentar o acesso aos ativos através de uma combinação de controlos de identidade, rede, aplicações, subscrições, grupos de gestão, entre outros.

Equilibre cuidadosamente a necessidade de separação da segurança com a necessidade de permitir o funcionamento diário dos sistemas que têm de comunicar entre si e aceder a dados.

Certifique-se de que a estratégia de segmentação está implementada de forma consistente em todos os tipos de controlos, incluindo segurança de rede, modelos de identidade e acesso e modelos de permissão/acesso a aplicações e controlos de processos humanos.

- [Orientação para a estratégia de segmentação no Azure (vídeo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Orientação para a estratégia de segmentação no Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Alinhar a segmentação de rede com a estratégia de segmentação empresarial](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definir a estratégia de gestão da postura de segurança

**Orientação**: Meça e mitigue continuamente os riscos para os seus ativos individuais e para o ambiente em que estão alojados. Dê prioridade aos ativos de valor alto e a superfícies de ataque muito expostas, como aplicações publicadas, pontos de entrada e saída de rede, pontos finais de utilizador e administrador, etc.

- [Referência de Segurança do Azure - Gestão da postura e das vulnerabilidades](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Alinhar as funções, as responsabilidades e as responsabilizações na organização

**Orientação**: Certifique-se de que documenta e comunica uma estratégia clara para as funções e responsabilidades na sua organização de segurança. Estabeleça prioridades ao indicar de forma clara a responsabilização quanto às decisões de segurança, explicando o modelo de responsabilização partilhada a todos e explicando às equipas técnicas a tecnologia para proteger a cloud.

- [Melhor Prática de Segurança do Azure 1 – Pessoas: Explicar às Equipas o Percurso da Segurança da Cloud](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Melhor Prática de Segurança do Azure 2 – Pessoas: Explicar às Equipas a Tecnologia de Segurança da Cloud](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Melhor Prática de Segurança do Azure 3 – Processo: Atribuir Responsabilização Quanto às Decisões de Segurança da Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definir uma estratégia de segurança de rede

**Orientação**: Crie uma abordagem à segurança de rede do Azure como parte da estratégia de controlo de acesso de segurança geral da sua organização.  

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Gestão centralizada da rede e responsabilidade quanto à segurança

-   Modelo de segmentação de rede virtual alinhado com a estratégia de segmentação empresarial

-   Estratégia de remediação em diferentes cenários de ameaças e ataques

-   Estratégia de entrada e saída do edge da Internet

-   Estratégia de interconectividade entre a cloud híbrida e o ambiente no local

-   Artefactos de segurança de rede atualizados (por exemplo, diagramas de rede, arquitetura de rede de referência)

Para obter mais informações, veja as seguintes referências:
- [Melhor Prática de Segurança do Azure 11 – Arquitetura. Estratégia de segurança única e unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Referência de Segurança do Azure - Segurança de Rede](/azure/security/benchmarks/security-controls-v2-network-security)

- [Descrição geral da segurança de rede do Azure](../security/fundamentals/network-overview.md)

- [Estratégia de arquitetura da rede empresarial](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definir a estratégia de identidades e acessos privilegiados

**Orientação**: Crie uma abordagem às identidades e aos acessos privilegiados do Azure como parte da estratégia de controlo de acesso de segurança geral da sua organização.  

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Um sistema de identidades e autenticação centralizado e respetiva interconectividade com outros sistemas de identidades internas e externas

-   Métodos de autenticação fortes em diferentes casos de utilização e condições

-   Proteção de utilizadores com muitos privilégios

-   Monitorização e processamento de atividades anómalas de utilizadores  

-   Processo de revisão e reconciliação de identidades e acessos dos utilizadores

Para obter mais informações, veja as seguintes referências:

- [Referência de Segurança do Azure - Gestão de identidades](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Referência de Segurança do Azure - Acesso privilegiado](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Melhor Prática de Segurança do Azure 11 – Arquitetura. Estratégia de segurança única e unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Descrição geral da segurança da gestão de identidades do Azure](../security/fundamentals/identity-management-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definir a estratégia de resposta a ameaças e registos

**Orientação**: Crie uma estratégia de resposta a ameaças e registos para detetar e remediar rapidamente as ameaças e cumprir, em paralelo, os requisitos de conformidade. Estabeleça prioridades ao disponibilizar aos analistas alertas de alta qualidade e experiências práticas para que se possam concentrar nas ameaças e não na integração e em passos manuais. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   As funções e as responsabilidades da organização quanto às operações de segurança (SecOps) 

-   Processo de resposta a incidentes bem definido e alinhado com as normas da agência norte-americana NIST ou outro framework da indústria 

-   Captura e retenção de registos para suportar a deteção de ameaças, a resposta a incidentes e as necessidades de conformidade

-   Visibilidade centralizada e correlação de informações sobre ameaças, mediante a utilização de SIEM, das capacidades nativas do Azure e de outras origens 

-   Plano de comunicação e notificação com os seus clientes, fornecedores e partes interessadas públicas

-   Utilização de plataformas nativas do Azure e de terceiros para processamento de incidentes, como registo e deteção de ameaças, análises forenses e remediação e erradicação de ataques

-   Processos para lidar com incidentes e atividades pós-incidentes, como lições aprendidas e retenção de provas

Para obter mais informações, veja as seguintes referências:

- [Referência de Segurança do Azure - Registos e deteção de ameaças](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Referência de Segurança do Azure - Resposta a incidentes](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Melhor Prática de Segurança do Azure 4 – Processo. Atualizar os Processos de Resposta a Incidentes para a Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework, registos e guia de decisão de relatórios](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Escala, gestão e monitorização empresarial do Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)