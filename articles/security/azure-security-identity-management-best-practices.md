---
title: Identidade e acesso segurança melhores práticas do Azure | Documentos da Microsoft
description: Este artigo fornece um conjunto de melhores práticas para gestão de identidades e recursos do Azure através do controlo de acesso internos.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2019
ms.author: barclayn
ms.openlocfilehash: 2a669f5b46db4d5de7d1d6863b94e6c117667aee
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153247"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Melhores práticas de segurança de controlo de acesso e gestão de identidades do Azure
Neste artigo, discutimos a uma coleção de gestão de identidades do Azure e práticas recomendadas de segurança do controlo de acesso. Essas práticas recomendadas são derivadas da nossa experiência com [do Azure AD](../active-directory/fundamentals/active-directory-whatis.md) e as experiências dos clientes, como mesmo.

Para cada melhor prática, Explicamos:

* O que é a melhor prática
* Por que pretende ativar essa prática recomendada
* Se efetuar a ativação para ativar a melhor prática, o que pode ser o resultado
* Possíveis alternativas para a prática recomendada
* Como pode aprender permitir a melhor prática

Esta gestão de identidades do Azure e a segurança de controlo de acesso ao artigo de melhores práticas se baseia numa opinião de consenso e capacidades da plataforma Azure e conjuntos de recursos, conforme existem no momento, este artigo foi escrito. Opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

Identidade do Azure gestão e acesso controlo melhores práticas de segurança discutidas neste artigo incluem:

* Tratar a identidade como perímetro segurança principal
* Centralize a gestão de identidade
* Gerir inquilinos ligados
* Ativar o início de sessão único
* Ativar o acesso condicional
* Ativar a gestão de palavras-passe
* Impor verificação multifator para utilizadores
* Utilizar o controlo de acesso baseado em funções
* Exposição inferior de contas com privilégios
* Controlar as localizações onde se encontram os recursos
* Utilizar o Azure AD para autenticação de armazenamento

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Tratar a identidade como perímetro segurança principal

Muitos consideram a identidade a ser o primário perímetro de segurança. Essa é uma mudança do tradicional foco na segurança de rede. Perímetros de rede continuar a receber mais porosos, e essa defesa do perímetro não pode ser tão eficaz que eram anteriormente a explosão de [BYOD](https://aka.ms/byodcg) dispositivos e aplicações na cloud.

[Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) é a solução do Azure para gestão de identidades e acesso. Azure AD é um multi-inquilino, com base na cloud diretório e identidade do serviço de gestão da Microsoft. Combina serviços de diretório centrais, gestão de acesso de aplicações e proteção de identidade numa única solução.

As secções seguintes listam as práticas recomendadas para segurança de acesso e identidades com o Azure AD.

## <a name="centralize-identity-management"></a>Centralize a gestão de identidade

Num [identidade híbrida](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) cenário, recomendamos que integre no local e diretórios da cloud. Integração permite que a sua equipa de TI gerir contas de um local, independentemente de onde é criada uma conta. Integração também ajuda os utilizadores a serem mais produtivos ao fornecer uma identidade comum para acederem a recursos na cloud e no local.

**Melhor prática**: Estabelecer uma única instância do Azure AD. Consistência e um único origens autoritativa irão aumentar a clareza e reduzir os riscos de segurança a partir de erros humanos e a complexidade de configuração.
**Detalhe**: Designar um único Azure AD diretório como a fonte autoritativa para contas organizacionais e empresariais.

**Melhor prática**: Integre os seus diretórios no local com o Azure AD.  
**Detalhe**: Uso [do Azure AD Connect](../active-directory/connect/active-directory-aadconnect.md) para sincronizar o seu diretório no local com o diretório na cloud.

> [!Note]
> Existem [fatores que afetam o desempenho do Azure AD Connect](../active-directory/hybrid/plan-connect-performance-factors.md). Certifique-se de que o Azure AD Connect tem capacidade suficiente para manter com um desempenho baixo sistemas da segurança impeding e produtividade. As organizações de grandes ou complexas (organizações mais de 100 000 objetos de aprovisionamento) devem seguir a [recomendações](../active-directory/hybrid/whatis-hybrid-identity.md) para otimizar a sua implementação do Azure AD Connect.

**Melhor prática**: Não sincronize as contas para o Azure AD que têm privilégios elevados na sua instância existente do Active Directory.
**Detalhe**: Não altere a predefinição [configuração do Azure AD Connect](../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) que filtra estas contas. Esta configuração reduz o risco dos adversários dinamização da cloud a ativos no local (o que podem criar um incidente principal).

**Melhor prática**: Ative a sincronização de hash de palavra-passe.  
**Detalhe**: Sincronização de hash de palavra-passe é uma funcionalidade utilizada para hashes de palavra-passe de utilizador de uma instância do Active Directory no local para um Azure baseado na nuvem de sincronização instância do AD. Esta sincronização ajuda a proteger contra fugas de credenciais que está a ser reproduzidas contra ataques anteriores.

Mesmo se decidir usar a Federação com os serviços de Federação do Active Directory (AD FS) ou de outros fornecedores de identidade, pode, opcionalmente, configurar sincronização de hash de palavra-passe como uma cópia de segurança em caso de seus servidores no local falharam ou ficam temporariamente indisponíveis. Esta sincronização permite aos utilizadores iniciar sessão serviço ao utilizar a mesma palavra-passe utilizam para iniciar sessão sua instância do Active Directory no local. Ela também permite a proteção de identidade detetar credenciais comprometidas, ao comparar hashes de palavra-passe sincronizadas com palavras-passe conhecidas para ficar comprometida, se um utilizador tiver utilizado o mesmo endereço de e-mail e palavra-passe noutros serviços que não estão ligadas ao Azure AD.

Para obter mais informações, consulte [implementar a sincronização de hash de palavra-passe com o Azure AD Connect sync](../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).

**Melhor prática**: Para programação de aplicações de novas, utilize o Azure AD para autenticação.
**Detalhe**: Utilize as capacidades de corretas para suportar a autenticação:

  - Azure AD para funcionários
  - [O Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/) para utilizadores convidados e parceiros externos
  - [O Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/) para controlar como os clientes se inscrevem, inicie sessão e gerem os respetivos perfis quando utilizam seus aplicativos

As organizações que não integram a sua identidade no local com a respetiva identidade de nuvem podem ter mais sobrecarga no gerenciamento de contas. Essa sobrecarga aumenta a probabilidade de erros e falhas de segurança.

> [!Note]
> Tem de escolher quais diretórios críticos contas residirão no e se a estação de trabalho de administrador utilizada é gerenciado por novos serviços em nuvem ou existente processa. Utilização da gestão existente e processos de aprovisionamento de identidade pode diminuir a alguns riscos, mas também pode criar o risco de um atacante comprometer a uma conta no local e dinamização para a cloud. Pode querer utilizar uma estratégia diferente para funções diferentes (por exemplo, os administradores de TI versus os administradores de unidade de negócios). Tem duas opções. Primeira opção é criar as contas do Azure AD que não estão sincronizadas com a instância do Active Directory no local. Junte-se a sua estação de trabalho de administrador para o Azure AD, que pode gerir e corrigir com o Microsoft Intune. Segunda opção consiste em utilizar existentes contas de administrador ao sincronizar a sua instância do Active Directory no local. Utilize estações de trabalho existentes no seu domínio do Active Directory para gestão e segurança.

## <a name="manage-connected-tenants"></a>Gerir inquilinos ligados
Sua organização de segurança tem visibilidade para avaliar o risco e para determinar se as políticas da sua organização e os requisitos de regulamentação, estão a ser seguidas. Deve garantir que a sua organização de segurança tem visibilidade para todas as subscrições ligado ao seu ambiente de produção e de rede (via [Azure ExpressRoute](../expressroute/expressroute-introduction.md) ou [VPN site a site](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)). Uma [Administrador Global do administrador/da empresa](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator) no Azure AD, pode elevar o acesso para o [administrador de acesso de utilizador](../role-based-access-control/built-in-roles.md#user-access-administrator) função e ver todas as subscrições e grupos geridos ligados ao seu ambiente.

Ver [elevar o acesso para gerir todas as subscrições do Azure e grupos de gestão](../role-based-access-control/elevate-access-global-admin.md) para se certificar de que e seu grupo de segurança podem ver todas as subscrições ou grupos de gestão ligado ao seu ambiente. Depois de avaliar os riscos, deve remover este acesso elevado.

## <a name="enable-single-sign-on"></a>Ativar o início de sessão único

Num mundo de dispositivos móveis e da cloud, que pretende ativar início de sessão único (SSO) para dispositivos, aplicações e serviços de qualquer lugar para que os utilizadores possam ser produtivos onde quer e sempre que. Quando tiver várias soluções de identidade para gerir, isso se torna um problema administrativo não só para IT, mas também para os utilizadores que têm de se lembrar de várias palavras-passe.

Ao utilizar a mesma solução de identidade para todas as suas aplicações e recursos, pode conseguir SSO. E os utilizadores podem utilizar o mesmo conjunto de credenciais para iniciar sessão e aceder aos recursos que precisam, independentemente dos recursos estão localizados no local ou na cloud.

**Melhor prática**: Ative o SSO.  
**Detalhe**: O Azure AD [expande o Active Directory no local](../active-directory/connect/active-directory-aadconnect.md) para a cloud. Os utilizadores podem utilizar a sua conta escolar ou profissional de principal para seus dispositivos associados a domínios, recursos da empresa e todas as web e aplicações SaaS que precisam para realizar seu trabalho. Os utilizadores não têm não se esqueça de vários conjuntos de nomes de utilizador e palavras-passe e o acesso de aplicativo pode ser automaticamente aprovisionado (ou desprovisionada) com base em suas associações de grupo da organização e o respetivo estado como um funcionário. E também pode controlar esse acesso para aplicações da galeria ou para as suas próprias aplicações no local que tenha desenvolvido e publicado através do [Proxy de Aplicações do AD](../active-directory/active-directory-application-proxy-get-started.md).

Utilizar o SSO para permitir que os utilizadores aceder aos respetivos [aplicações SaaS](../active-directory/active-directory-appssoaccess-whatis.md) com base na respetiva conta profissional ou escolar no Azure AD. Isto é aplicável não só para aplicações SaaS da Microsoft, mas também outras aplicações, tal como [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) e [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md). Pode configurar a sua aplicação para utilizar o Azure AD como um [identidade baseada no SAML](../active-directory/fundamentals-identity.md) fornecedor. Como um controle de segurança do Azure AD não emite um token que permite aos utilizadores iniciar sessão na aplicação, a menos que tenham sido concedidos acesso através do Azure AD. Pode conceder acesso diretamente ou através de um grupo que os utilizadores são membros.

As organizações que não criam uma identidade comum para estabelecer o SSO para seus usuários e aplicativos são mais expostas a cenários em que os utilizadores têm várias palavras-passe. Estes cenários aumentam a probabilidade dos utilizadores a reutilização de palavras-passe ou utilizar palavras-passe fracas.

## <a name="turn-on-conditional-access"></a>Ativar o acesso condicional

Os utilizadores podem aceder a recursos da sua organização ao utilizar uma variedade de dispositivos e aplicações em qualquer lugar. Administrador de TI, pretende certificar-se de que estes dispositivos cumpram as normas de segurança e conformidade. Se concentrar apenas na que pode aceder a um recurso não é suficiente mais.

Para balancear a segurança e produtividade, precisa pensar como um recurso é acedido antes de poder fazer uma decisão sobre o controlo de acesso. Com o acesso condicional do Azure AD, pode solucionar este requisito. Com acesso condicional, pode tomar decisões de controlo de acesso automatizados com base nas condições para aceder às suas aplicações na cloud.

**Melhor prática**: Gerir e controlar o acesso aos recursos empresariais.  
**Detalhe**: Configurar o Azure AD [acesso condicional](../active-directory/active-directory-conditional-access-azure-portal.md) com base na sensibilidade das aplicações para aplicações SaaS e aplicações do Azure AD – ligado, a localização e grupo.

**Melhor prática**: Bloquear protocolos de autenticação de legado.
**Detalhe**: Os invasores exploram pontos fracos em protocolos mais antigos, todos os dias, particularmente para ataques de spray de palavra-passe. Configure o acesso condicional para bloquear protocolos de legado. Veja o vídeo [do Azure AD: Afazeres e que não deve fazer](https://www.youtube.com/watch?v=wGk0J4z90GI) para obter mais informações.

## <a name="enable-password-management"></a>Ativar a gestão de palavras-passe

Se tiver vários inquilinos ou se pretender permitir que os utilizadores [repor a palavra-passe](../active-directory/active-directory-passwords-update-your-own-password.md), é importante que use as políticas de segurança apropriado para evitar abusos.

**Melhor prática**: Configure a reposição de palavra-passe self-service (SSPR) para os seus utilizadores.  
**Detalhe**: Utilizar o Azure AD [reposição de palavra-passe self-service](../active-directory-b2c/active-directory-b2c-reference-sspr.md) funcionalidade.

**Melhor prática**: Monitor como ou se realmente está a ser utilizada a SSPR.  
**Detalhe**: Monitorize os utilizadores quem estão a registar utilizando o Azure AD [relatório de atividade de registo de reposição de palavra-passe](../active-directory/active-directory-passwords-get-insights.md). A funcionalidade de relatórios que o Azure AD fornece ajuda a responder a perguntas através de relatórios criados previamente. Se está corretamente licenciado, também pode criar consultas personalizadas.

**Melhor prática**: Expanda as políticas de palavra-passe baseada na cloud à sua infraestrutura no local.
**Detalhe**: Melhore a políticas de palavra-passe da sua organização ao executar as verificações de mesmo de alterações de palavra-passe no local para alterações de palavra-passe baseada na cloud. Instale [proteção de palavra-passe do Azure AD](../active-directory/authentication/concept-password-ban-bad.md) para Windows Server Active Directory agentes no local expandir as listas de palavra-passe banidas à sua infraestrutura existente. Os utilizadores e administradores que alterar, definir ou repor palavras-passe no local é necessário para estar em conformidade com a mesma política de palavra-passe como os utilizadores apenas na cloud.

## <a name="enforce-multi-factor-verification-for-users"></a>Impor verificação multifator para utilizadores

Recomendamos que exigem a verificação de dois passos para todos os seus utilizadores. Isto inclui os administradores e outros utilizadores na sua organização que pode ter um impacto significativo se a conta for comprometida (por exemplo, responsáveis financeiros).

Existem várias opções para exigir a verificação de dois passos. A melhor opção para depende de suas metas, a edição do Azure AD que está a executar e seu programa de licenciamento. Ver [como requerer verificação de dois passos para um utilizador](../active-directory/authentication/howto-mfa-userstates.md) para determinar a melhor opção para. Consulte a [do Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) e [multi-factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) páginas para obter mais informações sobre as licenças de preços e preços.

Seguem-se as opções e os benefícios para ativar a verificação de dois passos:

**Opção 1**: [Ativar a multi-factor Authentication, alterando o estado do utilizador](../active-directory/authentication/howto-mfa-userstates.md).   
**Benefício**: Este é o método tradicional para exigir a verificação de dois passos. Funciona com ambos [multi-factor Authentication na cloud e no servidor do Azure multi-factor Authentication](../active-directory/authentication/concept-mfa-whichversion.md). Ao utilizar este método requer que os utilizadores efetuar a verificação de dois passos sempre que iniciam sessão e substitui as políticas de acesso condicional.

Para determinar onde multi-factor Authentication tem de ser ativado, veja [qual é a versão do MFA do Azure é adequada para a minha organização?](../active-directory/authentication/concept-mfa-whichversion.md).

**Opção 2**: [Ativar a multi-factor Authentication com a política de acesso condicional](../active-directory/authentication/howto-mfa-getstarted.md).
**Benefício**: Esta opção permite-lhe pedir para verificação de dois passos em condições específicas através de [acesso condicional](../active-directory/active-directory-conditional-access-azure-portal.md). Condições específicas podem ser utilizador inícios de sessão de localizações diferentes, os dispositivos não fidedignos ou aplicações que considera duvidosos. Definir condições específicas em que exija uma verificação permite-lhe evitar avisar constante para os seus utilizadores, que podem ser uma experiência de usuário desagradáveis.

Essa é a maneira mais flexível para ativar a verificação de dois passos para os seus utilizadores. Ativar uma política de acesso condicional funciona apenas para o Azure multi-factor Authentication na cloud e é uma funcionalidade premium do Azure AD. Pode encontrar mais informações sobre este método na [implementar com base na cloud do Azure multi-factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md).

**Opção 3**: Ativar o multi-factor Authentication com políticas de acesso condicional através da avaliação de risco de utilizador e início de sessão de [do Azure AD Identity Protection](../active-directory/authentication/tutorial-risk-based-sspr-mfa.md).   
**Benefício**: Esta opção permite-lhe:

- Detete potenciais vulnerabilidades que afetam as identidades da sua organização.
- Configure respostas automáticas para as ações suspeitas detetadas relacionadas com identidades da sua organização.
- Investigar incidentes suspeitos e tomar as medidas adequadas para resolvê-los.

Este método utiliza a avaliação de risco do Azure AD Identity Protection para determinar se é necessária uma verificação de dois passos com base no utilizador e o risco de início de sessão para todas as aplicações na cloud. Este método requer licenciamento do Azure Active Directory P2. Pode encontrar mais informações sobre este método na [do Azure Active Directory Identity Protection](../active-directory/identity-protection/overview.md).

> [!Note]
> Opção 1, ativar a multi-factor Authentication, alterando o estado do utilizador, substitui as políticas de acesso condicional. Uma vez que as opções de 2 e 3 utilizam políticas de acesso condicional, não é possível utilizar a opção 1 com eles.

As organizações que não adicionam camadas adicionais de proteção de identidade, por exemplo, a verificação de dois passos, são mais suscetíveis de ataque de roubo de credenciais. Um ataque de roubo de credenciais pode levar ao comprometimento de dados.

## <a name="use-role-based-access-control"></a>Utilizar o controlo de acesso baseado em funções
Gestão de acesso para recursos na cloud é essencial para qualquer organização que utiliza a cloud. [Controlo de acesso baseado em funções (RBAC)](../role-based-access-control/overview.md) ajuda-o a gerir quem tem acesso aos recursos do Azure, o que podem fazer com esses recursos e quais áreas que têm acesso.

Designar grupos ou funções individuais responsáveis por funções específicas no Azure ajuda a evitar erros de automatização que criar os riscos de segurança e a confusão que pode levar à humana. Restringir o acesso com base na [precisa de saber](https://en.wikipedia.org/wiki/Need_to_know) e [menor privilégio](https://en.wikipedia.org/wiki/Principle_of_least_privilege) princípios de segurança é fundamental para as organizações que desejam aplicar políticas de segurança para acesso a dados.

A equipa de segurança precisa de visibilidade sobre os recursos do Azure para avaliar e remediar o risco. Se a equipe de segurança tem responsabilidades operacionais, terá de permissões adicionais para realizar seus trabalhos.

Pode usar [RBAC](../role-based-access-control/overview.md) para atribuir permissões a utilizadores, grupos e aplicações num determinado âmbito. O âmbito de uma atribuição de função pode ser uma subscrição, um grupo de recursos ou um único recurso.

**Melhor prática**: Segregar funções na sua equipa e conceder apenas a quantidade de acesso a utilizadores que precisam para desempenhar as suas funções. Em vez de dar todas as pessoas permissões sem restrições na sua subscrição do Azure ou recursos, permitir que apenas determinadas ações num determinado âmbito.
**Detalhe**: Uso [funções RBAC incorporadas](../role-based-access-control/built-in-roles.md) no Azure para atribuir privilégios a utilizadores.

> [!Note]
> Permissões específicas criar complexidade desnecessária e a confusão, acumular numa configuração "herdada", que é difícil corrigir sem medo de quebrar alguma coisa. Evite as permissões de recursos específicos. Em alternativa, utilize grupos de gestão de permissões de toda a empresa e grupos de recursos para as permissões nas subscrições. Evite permissões específicas do usuário. Em vez disso, atribua acesso a grupos no Azure AD.

**Melhor prática**: Conceda as equipes de segurança com acesso de responsabilidades do Azure para ver os recursos do Azure para que possam avaliar e remediar o risco.
**Detalhe**: Conceda as equipes de segurança o RBAC [leitor de segurança](../role-based-access-control/built-in-roles.md#security-reader) função. Pode utilizar o grupo de gestão de raiz ou o grupo de gestão de segmento, dependendo do escopo das responsabilidades:

- **Grupo de gestão de raiz** para as equipas responsáveis por todos os recursos do enterprise
- **Grupo de gestão de segmento** para as equipes com âmbito limitado (normalmente por causa de regulamentação ou outros limites organizacionais)

**Melhor prática**: Conceda as permissões adequadas para as equipes de segurança que têm responsabilidades operacionais diretas.
**Detalhe**: Reveja as funções incorporadas do RBAC para a atribuição de função adequados. Se as funções incorporadas não atenderem às necessidades específicas da sua organização, pode criar [funções personalizadas para recursos do Azure](../role-based-access-control/custom-roles.md). Como com funções incorporadas, pode atribuir funções personalizadas a utilizadores, grupos e principais de serviço na subscrição, grupo de recursos e os âmbitos de recursos.

**Melhores práticas**: Acesso de centro de segurança do Azure de concessão para funções de segurança que precisam dele. Centro de segurança permite que as equipes de segurança identificar e remediar riscos rapidamente.
**Detalhe**: Adicionar as equipes de segurança com essas necessidades para o RBAC [administrador de segurança](../role-based-access-control/built-in-roles.md#security-admin) função para que possam ver as políticas de segurança, visualizar os Estados de segurança, editar as políticas de segurança, ver alertas e recomendações e dispensar alertas e recomendações. Pode fazê-lo ao utilizar o grupo de gestão de raiz ou o grupo de gestão de segmento, dependendo do escopo das responsabilidades.

Organizações que não a impor o controlo de acesso de dados ao utilizar as capacidades, como o RBAC pode ser a dar mais privilégios do que o necessário para seus usuários. Isto pode levar ao comprometimento de dados, permitindo que os utilizadores para aceder a tipos de dados (por exemplo, elevado impacto comercial) que eles não devem ter.

## <a name="lower-exposure-of-privileged-accounts"></a>Exposição inferior de contas com privilégios

Com privilégios de proteção do acesso é um primeiro passo crítico para proteger os ativos empresariais. Minimizar o número de pessoas que têm acesso para proteger informações ou recursos reduz a chance de um utilizador mal intencionado obter acesso, ou um utilizador autorizado inadvertidamente afetar um recurso confidencial.

Contas com privilégios são contas que administram e gerem os sistemas de TI. Os atacantes de Cibersegurança estas contas para obter acesso a dados e sistemas de uma organização de destino. Para proteger o acesso privilegiado, deve isolar as contas e os sistemas do risco de que está a ser exposto a um utilizador mal intencionado.

Recomendamos que desenvolve e siga um plano para proteger o acesso privilegiado contra invasores de cibersegurança. Para obter informações sobre como criar um plano detalhado para proteger identidades e acesso que são geridos ou comunicado no Azure AD, Microsoft Azure, Office 365 e outros serviços cloud, reveja [proteger o acesso privilegiado para implementações híbridas e na cloud O Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md).

O seguinte resume as melhores práticas encontradas na [proteger o acesso privilegiado para implementações híbridas e na cloud no Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md):

**Melhor prática**: Gerir, controlar e monitorizar o acesso a contas com privilégios.   
**Detalhe**: Ative [do Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/active-directory-securing-privileged-access.md). Depois de ativar a Privileged Identity Management, irá receber uma notificação de e-mail mensagens para acesso privilegiado alterações de função. Estas notificações fornecem aviso antecipado quando os utilizadores adicionais são adicionados a funções com privilégios elevados no seu diretório.

**Melhor prática**: Certifique-se de que todas as contas de administrador críticas são geridas contas do Azure AD.
**Detalhe**: Remova as contas de consumidor de funções de administrador críticos (por exemplo, contas Microsoft, como hotmail.com, live.com e outlook.com).

**Melhor prática**: Certifique-se de que todas as funções de administrador crítica tem uma conta separada para tarefas administrativas para evitar o phishing e outros ataques para comprometer privilégios administrativos.
**Detalhe**: Crie uma conta de administrador separada que atribuiu os privilégios necessários para executar as tarefas administrativas. Bloquear a utilização destas contas administrativas para ferramentas de produtividade diários, como o e-mail do Microsoft Office 365 ou a navegação na web arbitrário.

**Melhor prática**: Identifique e categorizar as contas que são em funções com privilégios elevados.   
**Detalhe**: Depois de ativar o Azure AD Privileged Identity Management, veja os utilizadores que estão no administrador global, administrador com função privilegiada e outras funções com privilégios elevados. Remova as contas que já não são necessários nessas funções e categorizar as contas restantes são atribuídas às funções de administrador:

- Individualmente atribuídas a utilizadores administrativos e pode ser utilizado para fins não administrativos (por exemplo, e-mail pessoal)
- Individualmente designada para fins administrativos apenas e atribuídas a utilizadores administrativos
- Compartilhado entre vários utilizadores
- Para cenários de acesso de emergência
- Para scripts automatizados
- Para utilizadores externos

**Melhor prática**: Implementar "just-in-time" acesso (JIT) para reduzir o tempo de exposição dos privilégios e aumente a visibilidade sobre o uso de contas com privilégios.   
**Detalhe**: O Azure AD Privileged Identity Management permite-lhe:

- Limitar os utilizadores na somente a realização de em seus privilégios JIT.
- Atribua funções para um período de tempo abreviado com confiança de que os privilégios são revogados automaticamente.

**Melhor prática**: Defina, pelo menos, duas contas de acesso de emergência.   
**Detalhe**: Contas de acesso de emergência ajudam as organizações a restringir o acesso privilegiado num ambiente do Azure Active Directory existente. Estas contas são altamente privilegiadas e não estão atribuídas a indivíduos específicos. Contas de acesso de emergência estão limitadas a cenários em que não não possível utilizar contas administrativas normais. As organizações devem limitar a utilização da conta de emergência para a quantidade necessária de tempo.

Avalie as contas que são atribuídas ou elegíveis para a função de administrador global. Se não vir quaisquer contas apenas na cloud utilizando o `*.onmicrosoft.com` domínio (previstas para acesso de emergência), criá-los. Para obter mais informações, consulte [gerir contas de administrador de acesso de emergência no Azure AD](../active-directory/users-groups-roles/directory-emergency-access.md).

**Melhor prática**: Ter um processo "break glass" em caso de emergência.
**Detalhe**: Siga os passos em [proteger o acesso privilegiado para implementações híbridas e na cloud no Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md).

**Melhor prática**: Exigir a todas as contas de administrador críticos a sem palavra-passe (preferencial) ou exigir autenticação Multifator.
**Detalhe**: Utilize o [aplicação Microsoft Authenticator](../active-directory/authentication/howto-authentication-phone-sign-in.md) para iniciar sessão em qualquer conta do Azure AD sem utilizar uma palavra-passe. Como [Windows Hello para empresas](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification), o Microsoft Authenticator utiliza autenticação baseada em chave para ativar uma credencial de utilizador que está associada a um dispositivo e utiliza a autenticação biométrica ou um PIN.

Exigir multi-factor Authentication no início de sessão para todos os utilizadores individuais que são permanentemente atribuídos a um ou mais funções de administrador do Azure AD: Administrador global, o administrador com função privilegiada, o administrador do Exchange Online e o administrador do SharePoint Online. Ativar [multi-factor Authentication para as suas contas de administrador](../active-directory/authentication/howto-mfa-userstates.md) e certifique-se de que os utilizadores da conta de administrador tenham registado.

**Melhor prática**: Para contas de administrador críticas, têm uma estação de trabalho de administrador em que as tarefas de produção não são permitidas (por exemplo, navegar e e-mail). Isto irá proteger as suas contas de administrador vetores de ataque que utilizam a navegação e o e-mail e reduzir significativamente o risco de um incidente principal.
**Detalhe**: Utilize uma estação de trabalho do administrador. Escolha um nível de segurança da estação de trabalho:

- Os dispositivos de produtividade altamente segura fornecem segurança avançada para a navegação e outras tarefas de produtividade.
- [Privilegiado (PAWs) do estações de trabalho do acesso](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations) fornecem um sistema operativo dedicado que sejam protegido contra ataques de internet e vetores de ameaças para tarefas confidenciais.

**Melhor prática**: Desprovisionar contas de administrador quando os funcionários deixam a sua organização.
**Detalhe**: Ter um processo que desativa ou elimina contas de administrador quando os funcionários deixam a sua organização.

**Melhor prática**: Teste regularmente as contas de administrador através de técnicas de ataque atual.
**Detalhe**: Utilize o simulador de ataque do Office 365 ou um oferta para executar cenários de ataque realista em sua empresa de terceiros. Isso pode ajudar a encontrar utilizadores vulneráveis antes que ocorra um ataque real.

**Melhor prática**: Tome medidas para atenuar as técnicas de atacado utilizadas com mais frequência.  
**Detalhe**: [Identificar as contas Microsoft funções administrativas que têm de ser mudado para contas escolares ou](../active-directory/users-groups-roles/directory-admin-roles-secure.md#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Certifique-se de que as contas de utilizador em separado e reencaminhamento para contas de administrador global de correio](../active-directory/users-groups-roles/directory-admin-roles-secure.md)  

[Certifique-se de que as palavras-passe de contas administrativas tem alterado recentemente](../active-directory/users-groups-roles/directory-admin-roles-secure.md#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Ativar sincronização de hash de palavra-passe](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)  

[Exigir multi-factor Authentication para utilizadores nas funções privilegiadas tudo, bem como de utilizadores expostos](../active-directory/users-groups-roles/directory-admin-roles-secure.md#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Obter a sua pontuação de segurança do Office 365 (se utilizar o Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#obtain-your-office-365-secure-score-if-using-office-365)  

[Reveja as orientações de segurança e conformidade do Office 365 (se utilizar o Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Configurar a monitorização da atividade do Office 365 (se utilizar o Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#configure-office-365-activity-monitoring-if-using-office-365)  

[Estabelecer os proprietários de plano de resposta de incidente/emergência](../active-directory/users-groups-roles/directory-admin-roles-secure.md#establish-incidentemergency-response-plan-owners)  

[Proteger contas no local com privilégios administrativos](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)

Se não proteger o acesso privilegiado, pode achar que tem demasiados utilizadores nas funções com privilégios elevados e são mais vulneráveis a ataques. Atores maliciosos, incluindo os atacantes de cibersegurança, muitas vezes, contas de administrador de destino e outros elementos de acesso privilegiado para obter acesso a dados confidenciais e sistemas com o roubo de credenciais.

## <a name="control-locations-where-resources-are-created"></a>Controlar as localizações onde os recursos são criados

Ativar os operadores da nuvem executar tarefas, enquanto impede-los de danificar as convenções que são necessários para gerir os recursos da sua organização é muito importante. As organizações que desejam controlar os locais onde os recursos são criados devem muito código estas localizações.

Pode usar [do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) para criar políticas de segurança cujas definições descrevem as ações ou os recursos que são especificamente foi negados. Atribuir as definições de política no âmbito pretendido, como a subscrição, o grupo de recursos ou recurso individual.

> [!NOTE]
> As políticas de segurança não são os mesmos que o RBAC. Na verdade, eles utilizarem o RBAC para autorizar os utilizadores a criar esses recursos.
>
>

As organizações que não a controlar como os recursos são criados são mais suscetíveis a utilizadores que poderão abusar o serviço através da criação de mais recursos do que o necessário. O processo de criação de recursos de sistema de proteção é um passo importante para proteger um cenário de multi-inquilino.

## <a name="actively-monitor-for-suspicious-activities"></a>Monitorizar ativamente para atividades suspeitas

Uma sistema de monitoramento de identidade ativa rapidamente pode detetar um comportamento suspeito e acionar um alerta para uma investigação mais aprofundada. A tabela seguinte apresenta uma lista em duas capacidades do Azure AD que podem ajudar as organizações a monitorizar as suas identidades:

**Melhor prática**: Ter um método para identificar:

- Tenta iniciar sessão [sem estão a ser rastreados](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md).
- [Força bruta](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) ataques contra uma determinada conta.
- Tenta iniciar sessão a partir de várias localizações.
- Inícios de sessão do [dispositivos infetados](../active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices.md).
- Endereços IP suspeitos.

**Detalhe**: Utilizar o Azure AD Premium [anomalias relatórios](../active-directory/active-directory-view-access-usage-reports.md). Tem processos e procedimentos para os administradores de TI executar esses relatórios numa base diária ou a pedido (normalmente num cenário de resposta a incidentes).

**Melhor prática**: Ter um sistema de monitorização de Active Directory que notifica-o de riscos e pode ajustar o nível de risco (alta, média ou baixa) aos seus requisitos empresariais.   
**Detalhe**: Uso [do Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md), que sinaliza o atual arrisca-se no seu próprio dashboard e envia notificações de resumidas diário por e-mail. Para ajudar a proteger as identidades da sua organização, pode configurar as políticas baseadas em risco, que respondam automaticamente aos problemas detetados quando for atingido um nível de risco especificado.

As organizações que não Monitore ativamente os seus sistemas de identidade estão em risco de ter credenciais de utilizador comprometidas. Sem o conhecimento que atividades suspeitas ocorrem por meio destas credenciais, as organizações não podem mitigar este tipo de ameaça.

## <a name="use-azure-ad-for-storage-authentication"></a>Utilizar o Azure AD para autenticação de armazenamento
[O armazenamento do Azure](../storage/common/storage-auth-aad.md) suporta a autenticação e autorização com o Azure AD para o armazenamento de BLOBs e armazenamento de filas. Com a autenticação do Azure AD, pode utilizar o controlo de acesso baseado em funções do Azure para conceder permissões específicas para os utilizadores, grupos e aplicações para baixo para o âmbito de um contentor de BLOBs individuais ou a fila.

Recomendamos que utilize [do Azure AD para autenticar o acesso ao armazenamento](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/).

## <a name="next-step"></a>Passo seguinte

Ver [padrões e práticas recomendadas de segurança do Azure](security-best-practices-and-patterns.md) para obter mais melhores práticas de segurança a utilizar quando estiver conceber, implementar e gerir soluções na cloud ao utilizar o Azure.
