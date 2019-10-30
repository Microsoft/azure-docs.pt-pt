---
title: Práticas recomendadas de segurança do Azure Identity & Access | Microsoft Docs
description: Este artigo fornece um conjunto de práticas recomendadas para gerenciamento de identidade e controle de acesso usando recursos internos do Azure.
services: security
documentationcenter: na
author: barclayn
manager: RKarlin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: barclayn
ms.openlocfilehash: 52ef3a9b1df058d5d2e954b424094f9dbaeba15b
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053348"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Práticas recomendadas de segurança de controle de acesso e gerenciamento de identidade do Azure

Neste artigo, discutiremos uma coleção de práticas recomendadas de segurança de controle de acesso e gerenciamento de identidades do Azure. Essas práticas recomendadas derivam de nossa experiência com o [Azure ad](../../active-directory/fundamentals/active-directory-whatis.md) e as experiências de clientes como você.

Para cada prática recomendada, explicamos:

* Qual é a prática recomendada
* Por que você deseja habilitar essa prática recomendada
* O que pode ser o resultado se você não conseguir habilitar a prática recomendada
* Possíveis alternativas à prática recomendada
* Como você pode aprender a habilitar a prática recomendada

Este artigo de práticas recomendadas de segurança de controle de acesso e gerenciamento de identidade do Azure baseia-se em uma opinião de consenso e em recursos e conjuntos de recursos da plataforma Azure, como eles existem no momento em que este artigo foi escrito.

A intenção de escrever este artigo é fornecer um roteiro geral para uma postura de segurança mais robusta após a implantação guiada por nossa lista de verificação de "[cinco etapas para proteger sua infraestrutura de identidade](steps-secure-identity.md)", que orienta você por alguns dos nossos principais recursos e serviços.

As opiniões e tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

As práticas recomendadas de segurança de controle de acesso e gerenciamento de identidade do Azure discutidas neste artigo incluem:

* Tratar a identidade como o perímetro de segurança primário
* Centralize o gerenciamento de identidade
* Gerenciar locatários conectados
* Habilitar logon único
* Ativar o acesso condicional
* Planejar melhorias de segurança de rotina
* Ativar a gestão de palavras-passe
* Impor a verificação multifator para usuários
* Utilizar o controlo de acesso baseado em funções
* Menor exposição de contas com privilégios
* Locais de controle onde os recursos estão localizados
* Usar o Azure AD para autenticação de armazenamento

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Tratar a identidade como o perímetro de segurança primário

Muitos consideram a identidade como o perímetro principal para segurança. Essa é uma mudança do foco tradicional na segurança da rede. Os perímetros de rede continuam a obter mais porosos, e essa defesa do perímetro não pode ser tão eficiente quanto era antes da explosão de dispositivos [BYOD](https://aka.ms/byodcg) e aplicativos em nuvem.

O [Azure Active Directory (AD do Azure)](../../active-directory/fundamentals/active-directory-whatis.md) é a solução do Azure para gerenciamento de identidade e acesso. O Azure AD é um serviço de gerenciamento de identidade e diretório baseado em nuvem multilocatário da Microsoft. Ele combina os principais serviços de diretório, o gerenciamento de acesso a aplicativos e a proteção de identidade em uma única solução.

As seções a seguir listam as práticas recomendadas para segurança de identidade e acesso usando o Azure AD.

**Prática recomendada**: Centralizar controles de segurança e detecções em relação a identidades de usuário e de serviço.
**Detalhe**: Use o Azure ad para colocar controles e identidades.

## <a name="centralize-identity-management"></a>Centralize o gerenciamento de identidade

Em um cenário de [identidade híbrida](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) , recomendamos que você integre seus diretórios locais e na nuvem. A integração permite que sua equipe de ti gerencie contas de um local, independentemente de onde uma conta é criada. A integração também ajuda os usuários a serem mais produtivos fornecendo uma identidade comum para acessar recursos de nuvem e locais.

**Prática recomendada**: estabelecer uma única instância do Azure AD. A consistência e uma única fonte autoritativa aumentarão a clareza e reduzirão os riscos de segurança de erros humanos e da complexidade da configuração.
**Detalhe**: designar um único diretório do Azure ad como a origem autoritativa para contas corporativas e organizacionais.

**Prática recomendada**: Integre seus diretórios locais com o Azure AD.  
**Detalhe**: use [Azure ad Connect](/azure/active-directory/connect/active-directory-aadconnect) para sincronizar seu diretório local com seu diretório de nuvem.

> [!Note]
> Há [fatores que afetam o desempenho do Azure ad Connect](../../active-directory/hybrid/plan-connect-performance-factors.md). Certifique-se de que Azure AD Connect tem capacidade suficiente para manter o desempenho de sistemas em execução para impedir a segurança e a produtividade. Organizações grandes ou complexas (organizações que provisionam mais de 100.000 objetos) devem seguir as [recomendações](../../active-directory/hybrid/whatis-hybrid-identity.md) para otimizar sua implementação de Azure ad Connect.

**Prática recomendada**: não sincronize contas para o Azure AD que têm altos privilégios em sua instância de Active Directory existente.
**Detalhe**: não altere a [configuração de Azure ad Connect](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) padrão que filtra essas contas. Essa configuração reduz o risco de os adversários dinamizarem de nuvem para ativos locais (que poderiam criar um incidente importante).

**Prática recomendada**: ativar a sincronização de hash de senha.  
**Detalhe**: a sincronização de hash de senha é um recurso usado para sincronizar hashes de senha de usuário de uma instância de Active Directory local para uma instância do Azure ad baseada em nuvem. Essa sincronização ajuda a proteger contra credenciais vazadas sendo reproduzidas de ataques anteriores.

Mesmo que você decida usar a Federação com Serviços de Federação do Active Directory (AD FS) (AD FS) ou outros provedores de identidade, você pode opcionalmente configurar a sincronização de hash de senha como um backup, caso os servidores locais falhem ou fiquem temporariamente indisponíveis. Essa sincronização permite que os usuários entrem no serviço usando a mesma senha que eles usam para entrar em sua instância de Active Directory local. Ele também permite que a proteção de identidade detecte credenciais comprometidas comparando hashes de senha sincronizada com senhas conhecidas para serem comprometidas, se um usuário tiver usado o mesmo endereço de email e senha em outros serviços que não estão conectados ao Azure AD.

Para obter mais informações, consulte [implementar a sincronização de hash de senha com Azure ad Connect sincronização](/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization).

**Prática recomendada**: para o desenvolvimento de novos aplicativos, use o Azure ad para autenticação.
**Detalhe**: Use os recursos corretos para dar suporte à autenticação:

  - Azure AD para funcionários
  - [B2B do Azure ad](../../active-directory/b2b/index.yml) para usuários convidados e parceiros externos
  - [Azure ad B2C](../../active-directory-b2c/index.yml) para controlar como os clientes se inscrevem, entram e gerenciam seus perfis quando usam seus aplicativos

As organizações que não integram sua identidade local à identidade de nuvem podem ter mais sobrecarga no gerenciamento de contas. Essa sobrecarga aumenta a probabilidade de erros e violações de segurança.

> [!Note]
> Você precisa escolher em quais diretórios as contas críticas residirão e se a estação de trabalho de administração usada é gerenciada por novos serviços de nuvem ou processos existentes. O uso de processos existentes de provisionamento de identidade e gerenciamento pode diminuir alguns riscos, mas também pode criar o risco de um invasor comprometer uma conta local e dinamizar para a nuvem. Talvez você queira usar uma estratégia diferente para funções diferentes (por exemplo, administradores de ti versus administradores de unidade de negócios). Você tem duas opções. A primeira opção é criar contas do Azure AD que não estejam sincronizadas com sua instância de Active Directory local. Ingresse sua estação de trabalho de administrador no Azure AD, que você pode gerenciar e aplicar patch usando Microsoft Intune. A segunda opção é usar contas de administrador existentes sincronizando com sua instância de Active Directory local. Use estações de trabalho existentes em seu Active Directory domínio para gerenciamento e segurança.

## <a name="manage-connected-tenants"></a>Gerenciar locatários conectados
Sua organização de segurança precisa de visibilidade para avaliar o risco e determinar se as políticas da sua organização e quaisquer requisitos regulatórios estão sendo seguidas. Você deve garantir que sua organização de segurança tenha visibilidade de todas as assinaturas conectadas ao seu ambiente de produção e rede (por meio [do Azure ExpressRoute](../../expressroute/expressroute-introduction.md) ou [VPN site a site](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)). Um [administrador de administrador/empresa global](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) no Azure AD pode elevar seu acesso à função de [administrador de acesso do usuário](../../role-based-access-control/built-in-roles.md#user-access-administrator) e ver todas as assinaturas e grupos gerenciados conectados ao seu ambiente.

Consulte [elevar o acesso para gerenciar todas as assinaturas e grupos de gerenciamento do Azure](../../role-based-access-control/elevate-access-global-admin.md) para garantir que você e seu grupo de segurança possam exibir todas as assinaturas ou grupos de gerenciamento conectados ao seu ambiente. Você deve remover esse acesso elevado depois de avaliar os riscos.

## <a name="enable-single-sign-on"></a>Habilitar logon único

Em um mundo em primeiro lugar, em nuvem, você deseja habilitar o SSO (logon único) para dispositivos, aplicativos e serviços de qualquer lugar, para que os usuários possam ser produtivos onde quer e sempre. Quando você tem várias soluções de identidade para gerenciar, isso se torna um problema administrativo não apenas para ele, mas também para os usuários que precisam se lembrar de várias senhas.

Usando a mesma solução de identidade para todos os seus aplicativos e recursos, você pode obter o SSO. E os usuários podem usar o mesmo conjunto de credenciais para entrar e acessar os recursos de que precisam, independentemente de os recursos estarem localizados no local ou na nuvem.

**Prática recomendada**: habilitar o SSO.  
**Detalhe**: o Azure ad [estende o Active Directory local](/azure/active-directory/connect/active-directory-aadconnect) para a nuvem. Os usuários podem usar sua conta corporativa ou de estudante primária para seus dispositivos ingressados no domínio, recursos da empresa e todos os aplicativos Web e SaaS de que precisam para realizar seus trabalhos. Os usuários não precisam se lembrar de vários conjuntos de nomes de usuário e senhas, e o acesso ao aplicativo pode ser automaticamente provisionado (ou desprovisionado) com base em suas associações de grupo da organização e seu status como funcionário. E também pode controlar esse acesso para aplicações da galeria ou para as suas próprias aplicações no local que tenha desenvolvido e publicado através do [Proxy de Aplicações do AD](/azure/active-directory/active-directory-application-proxy-get-started).

Use o SSO para permitir que os usuários acessem seus [aplicativos SaaS](/azure/active-directory/active-directory-appssoaccess-whatis) com base em sua conta corporativa ou de estudante no Azure AD. Isso é aplicável não apenas para aplicativos SaaS da Microsoft, mas também para outros aplicativos, como [Google Apps](/azure/active-directory/active-directory-saas-google-apps-tutorial) e [Salesforce](/azure/active-directory/active-directory-saas-salesforce-tutorial). Você pode configurar seu aplicativo para usar o Azure AD como um provedor de [identidade baseado em SAML](/azure/active-directory/fundamentals-identity) . Como um controle de segurança, o Azure AD não emite um token que permite aos usuários entrar no aplicativo, a menos que tenham sido concedidos acesso por meio do Azure AD. Você pode conceder acesso diretamente ou por meio de um grupo do qual os usuários são membros.

As organizações que não criam uma identidade comum para estabelecer o SSO para seus usuários e aplicativos são mais expostas a cenários em que os usuários têm várias senhas. Esses cenários aumentam a probabilidade de os usuários reutilizarem senhas ou usarem senhas fracas.

## <a name="turn-on-conditional-access"></a>Ativar o acesso condicional

Os usuários podem acessar os recursos da sua organização usando uma variedade de dispositivos e aplicativos de qualquer lugar. Como administrador de ti, você deseja garantir que esses dispositivos atendam aos seus padrões de segurança e conformidade. Apenas o foco em quem pode acessar um recurso não é mais suficiente.

Para balancear a segurança e a produtividade, você precisa pensar em como um recurso é acessado antes de tomar uma decisão sobre o controle de acesso. Com o acesso condicional do Azure AD, você pode atender a esse requisito. Com o acesso condicional, você pode tomar decisões de controle de acesso automatizadas com base nas condições para acessar seus aplicativos de nuvem.

**Prática recomendada**: gerenciar e controlar o acesso aos recursos corporativos.  
**Detalhe**: Configure o [acesso condicional](/azure/active-directory/active-directory-conditional-access-azure-portal) do Azure AD com base em um grupo, local e sensibilidade de aplicativo para aplicativos SaaS e aplicativos conectados ao Azure AD.

**Prática recomendada**: bloquear protocolos de autenticação herdados.
**Detalhe**: os invasores exploram os pontos fracos nos protocolos mais antigos todos os dias, especialmente para ataques de irrigação de senha. Configure o acesso condicional para bloquear protocolos herdados. Consulte o vídeo [Azure AD: do e não](https://www.youtube.com/watch?v=wGk0J4z90GI) para obter mais informações.

## <a name="plan-for-routine-security-improvements"></a>Planejar melhorias de segurança de rotina

A segurança está sempre em evolução, e é importante criar em sua estrutura de gerenciamento de identidade e nuvem uma maneira de mostrar regularmente o crescimento e descobrir novas maneiras de proteger seu ambiente.

A pontuação segura de identidade é um conjunto de controles de segurança recomendados que a Microsoft publica que funciona para fornecer uma pontuação numérica para medir objetivamente sua postura de segurança e ajudar a planejar futuras melhorias de segurança. Você também pode exibir sua pontuação em comparação com aquelas em outros setores, bem como suas próprias tendências ao longo do tempo.

**Prática recomendada**: planejar revisões e melhorias de segurança de rotina com base nas práticas recomendadas em seu setor.
**Detalhe**: Use o recurso de Pontuação segura de identidade para classificar suas melhorias ao longo do tempo.

## <a name="enable-password-management"></a>Ativar a gestão de palavras-passe

Se você tiver vários locatários ou se desejar permitir que os usuários [redefinam suas próprias senhas](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md), é importante que você use políticas de segurança apropriadas para evitar abusos.

**Prática recomendada**: configurar SSPR (redefinição de senha de autoatendimento) para seus usuários.  
**Detalhe**: Use o recurso de [redefinição de senha de autoatendimento](/azure/active-directory-b2c/active-directory-b2c-reference-sspr) do Azure AD.

**Prática recomendada**: monitorar como ou se SSPR está realmente sendo usado.  
**Detalhe**: Monitore os usuários que estão se registrando usando o [relatório de atividade de registro de redefinição de senha](/azure/active-directory/active-directory-passwords-get-insights)do Azure AD. O recurso de relatório que o Azure AD fornece ajuda a responder perguntas usando relatórios predefinidos. Se você estiver licenciado adequadamente, também poderá criar consultas personalizadas.

**Prática recomendada**: estenda as políticas de senha baseadas em nuvem para sua infraestrutura local.
**Detalhe**: aprimore as políticas de senha em sua organização executando as mesmas verificações para as alterações de senha no local como você faz para alterações de senha baseadas em nuvem. Instale a [proteção de senha do Azure ad](/azure/active-directory/authentication/concept-password-ban-bad) para Windows Server Active Directory agentes locais para estender listas de senhas banidas para sua infraestrutura existente. Os usuários e administradores que alteram, definem ou redefinem senhas locais são necessários para obedecer à mesma política de senha que os usuários somente na nuvem.

## <a name="enforce-multi-factor-verification-for-users"></a>Impor a verificação multifator para usuários

Recomendamos que você exija a verificação em duas etapas para todos os seus usuários. Isso inclui administradores e outros em sua organização que podem ter um impacto significativo se sua conta for comprometida (por exemplo, gerentes financeiros).

Há várias opções para exigir a verificação em duas etapas. A melhor opção para você depende de suas metas, da edição do Azure AD que você está executando e do seu programa de licenciamento. Consulte [como exigir a verificação em duas etapas para que um usuário](/azure/active-directory/authentication/howto-mfa-userstates) determine a melhor opção para você. Consulte as páginas de preços do [Azure ad](https://azure.microsoft.com/pricing/details/active-directory/) e a [autenticação multifator do Azure](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) para obter mais informações sobre licenças e preços.

Veja a seguir as opções e os benefícios para habilitar a verificação em duas etapas:

**Opção 1**: habilitar a MFA para todos os usuários e métodos de logon com o **benefício**de padrões de segurança do Azure AD: essa opção permite que você aplique a MFA de forma fácil e rápida para todos os usuários em seu ambiente com uma política rigorosa para:

* Desafio de contas administrativas e mecanismos de logon administrativo
* Exigir desafio de MFA por meio de Microsoft Authenticator para todos os usuários
* Restrinja os protocolos de autenticação herdados.

Esse método está disponível para todas as camadas de licenciamento, mas não pode ser misturado com políticas de acesso condicional existentes. Você pode encontrar mais informações nos padrões de segurança do Azure AD

**Opção 2**: [habilitar a autenticação multifator alterando o estado do usuário](../../active-directory/authentication/howto-mfa-userstates.md).   
**Benefício**: esse é o método tradicional para exigir a verificação em duas etapas. Ele funciona com [a autenticação multifator do Azure na nuvem e servidor de autenticação multifator do Azure](/azure/active-directory/authentication/concept-mfa-whichversion). O uso desse método exige que os usuários executem a verificação em duas etapas sempre que entrarem e substituam as políticas de acesso condicional.

Para determinar onde a autenticação multifator precisa ser habilitada, consulte [qual versão do Azure MFA é adequada para minha organização?](/azure/active-directory/authentication/concept-mfa-whichversion).

**Opção 3**: [habilitar a autenticação multifator com a política de acesso condicional](/azure/active-directory/authentication/howto-mfa-getstarted).
**Benefício**: essa opção permite solicitar a verificação em duas etapas em condições específicas usando o [acesso condicional](/azure/active-directory/active-directory-conditional-access-azure-portal). Condições específicas podem ser entrada do usuário de diferentes locais, dispositivos não confiáveis ou aplicativos que você considera arriscados. Definir condições específicas em que você exige a verificação em duas etapas permite evitar a solicitação de constantes para seus usuários, o que pode ser uma experiência de usuário desagradável.

Essa é a maneira mais flexível de habilitar a verificação em duas etapas para seus usuários. A habilitação de uma política de acesso condicional funciona apenas para a autenticação multifator do Azure na nuvem e é um recurso Premium do Azure AD. Você pode encontrar mais informações sobre esse método em [implantar a autenticação multifator do Azure baseada em nuvem](/azure/active-directory/authentication/howto-mfa-getstarted).

**Opção 4**: habilitar a autenticação multifator com políticas de acesso condicional, avaliando o risco de usuário e de entrada de [Azure ad Identity Protection](/azure/active-directory/authentication/tutorial-risk-based-sspr-mfa).   
**Benefício**: essa opção permite que você:

* Detecte possíveis vulnerabilidades que afetam as identidades da sua organização.
* Configure respostas automatizadas para detectar ações suspeitas que estão relacionadas às identidades da sua organização.
* Investigue incidentes suspeitos e tome as medidas necessárias para resolvê-los.

Esse método usa a avaliação de risco Azure AD Identity Protection para determinar se a verificação em duas etapas é necessária com base no risco de usuário e de entrada para todos os aplicativos na nuvem. Esse método requer o licenciamento do Azure Active Directory P2. Você pode encontrar mais informações sobre esse método no [Azure Active Directory Identity Protection](/azure/active-directory/identity-protection/overview).

> [!Note]
> Opção 1, habilitar a autenticação multifator alterando o estado do usuário, substitui as políticas de acesso condicional. Como as opções 2 e 3 usam políticas de acesso condicional, você não pode usar a opção 1 com elas.

As organizações que não adicionam camadas extras de proteção de identidade, como a verificação em duas etapas, são mais suscetíveis ao ataque de roubo de credenciais. Um ataque de roubo de credenciais pode levar a um comprometimento de dados.

## <a name="use-role-based-access-control"></a>Utilizar o controlo de acesso baseado em funções

O gerenciamento de acesso para recursos de nuvem é essencial para qualquer organização que usa a nuvem. O [RBAC (controle de acesso baseado em função)](/azure/role-based-access-control/overview)ajuda você a gerenciar quem tem acesso aos recursos do Azure, o que eles podem fazer com esses recursos e em quais áreas eles têm acesso.

A designação de grupos ou funções individuais responsáveis por funções específicas no Azure ajuda a evitar confusão que podem levar a erros humanos e de automação que criam riscos de segurança. Restringir o acesso com base na [necessidade de conhecer](https://en.wikipedia.org/wiki/Need_to_know) e aos princípios de segurança de [privilégios mínimos](https://en.wikipedia.org/wiki/Principle_of_least_privilege) é imperativo para organizações que desejam impor políticas de segurança para acesso a dados.

Sua equipe de segurança precisa de visibilidade dos recursos do Azure para avaliar e corrigir riscos. Se a equipe de segurança tiver responsabilidades operacionais, elas precisarão de permissões adicionais para realizar seus trabalhos.

Você pode usar o [RBAC](/azure/role-based-access-control/overview) para atribuir permissões a usuários, grupos e aplicativos em um determinado escopo. O âmbito da atribuição de uma função pode ser uma subscrição, um grupo de recursos ou um único recurso.

**Prática recomendada**: separar as tarefas dentro de sua equipe e conceder apenas a quantidade de acesso aos usuários de que eles precisam para executar seus trabalhos. Em vez de fornecer a todos permissões irrestritas em sua assinatura ou recursos do Azure, permita apenas determinadas ações em um determinado escopo.
**Detalhe**: use [funções RBAC internas](/azure/role-based-access-control/built-in-roles) no Azure para atribuir privilégios a usuários.

> [!Note]
> Permissões específicas criam complexidade e confusão desnecessárias, acumulando em uma configuração "herdada" que é difícil de corrigir sem medo de quebrar algo. Evite permissões específicas de recursos. Em vez disso, use grupos de gerenciamento para permissões de toda a empresa e grupos de recursos para permissões nas assinaturas. Evite permissões específicas do usuário. Em vez disso, atribua acesso a grupos no Azure AD.

**Prática recomendada**: conceder às equipes de segurança acesso de responsabilidades do Azure para ver os recursos do Azure para que eles possam avaliar e corrigir riscos.
**Detalhe**: conceder às equipes de segurança a função de [leitor de segurança](/azure/role-based-access-control/built-in-roles#security-reader) do RBAC. Você pode usar o grupo de gerenciamento raiz ou o grupo de gerenciamento de segmentos, dependendo do escopo das responsabilidades:

* **Grupo de gerenciamento raiz** para equipes responsáveis por todos os recursos da empresa
* **Grupo de gerenciamento de segmento** para equipes com escopo limitado (normalmente devido a regulamentações ou outros limites organizacionais)

**Prática recomendada**: conceda as permissões apropriadas para as equipes de segurança que têm responsabilidades operacionais diretas.
**Detalhe**: examine as funções internas de RBAC para a atribuição de função apropriada. Se as funções internas não atenderem às necessidades específicas de sua organização, você poderá criar [funções personalizadas para recursos do Azure](/azure/role-based-access-control/custom-roles). Assim como acontece com funções internas, você pode atribuir funções personalizadas a usuários, grupos e entidades de serviço na assinatura, grupo de recursos e escopos de recursos.

**Práticas recomendadas**: conceder acesso à central de segurança do Azure a funções de segurança que precisam dela. A central de segurança permite que as equipes de segurança identifiquem e corrijam riscos rapidamente.
**Detalhe**: Adicione equipes de segurança com essas necessidades à função [administrador de segurança](/azure/role-based-access-control/built-in-roles#security-admin) RBAC para que possam exibir políticas de segurança, exibir Estados de segurança, editar políticas de segurança, exibir alertas e recomendações e ignorar alertas e recomendações. Você pode fazer isso usando o grupo de gerenciamento raiz ou o grupo de gerenciamento de segmento, dependendo do escopo de responsabilidades.

As organizações que não impõem o controle de acesso a dados usando recursos como o RBAC podem estar concedendo mais privilégios do que o necessário para seus usuários. Isso pode levar ao comprometimento de dados, permitindo que os usuários acessem tipos de dados (por exemplo, alto impacto nos negócios) que não deveriam ter.

## <a name="lower-exposure-of-privileged-accounts"></a>Menor exposição de contas com privilégios

Proteger o acesso privilegiado é uma primeira etapa crítica para proteger os ativos de negócios. Minimizar o número de pessoas que têm acesso a informações ou recursos seguros reduz a chance de um usuário mal-intencionado obter acesso ou um usuário autorizado que afete inadvertidamente um recurso confidencial.

Contas com privilégios são contas que administram e gerenciam sistemas de ti. Os invasores cibernéticos visam essas contas para obter acesso aos dados e sistemas de uma organização. Para proteger o acesso privilegiado, você deve isolar as contas e os sistemas do risco de serem expostos a um usuário mal-intencionado.

Recomendamos que você desenvolva e siga um roteiro para proteger o acesso privilegiado contra invasores cibernéticos. Para obter informações sobre como criar um roteiro detalhado para proteger identidades e acesso que são gerenciados ou relatados no Azure AD, Microsoft Azure, Office 365 e outros serviços de nuvem, examine [protegendo o acesso privilegiado para implantações híbridas e na nuvem no Azure AD ](/azure/active-directory/users-groups-roles/directory-admin-roles-secure).

A seguir, é resumido as práticas recomendadas encontradas em [protegendo o acesso privilegiado para implantações híbridas e na nuvem no Azure ad](/azure/active-directory/users-groups-roles/directory-admin-roles-secure):

**Prática recomendada**: gerenciar, controlar e monitorar o acesso a contas com privilégios.   
**Detalhe**: Ative [Azure ad Privileged Identity Management](/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access). Depois de ativar Privileged Identity Management, você receberá mensagens de email de notificação para alterações de função de acesso privilegiado. Essas notificações fornecem um aviso antecipado quando usuários adicionais são adicionados a funções com altos privilégios em seu diretório.

**Prática recomendada**: Verifique se todas as contas de administrador críticas são contas gerenciadas do Azure AD.
**Detalhe**: Remova todas as contas de consumidor das funções de administrador críticas (por exemplo, contas da Microsoft, como hotmail.com, live.com e Outlook.com).

**Prática recomendada**: Verifique se todas as funções de administrador críticas têm uma conta separada para tarefas administrativas a fim de evitar phishing e outros ataques para comprometer os privilégios administrativos.
**Detalhe**: Crie uma conta de administrador separada que tenha atribuído os privilégios necessários para executar as tarefas administrativas. Bloqueie o uso dessas contas administrativas para ferramentas de produtividade diárias como Microsoft Office email 365 ou navegação na Web arbitrária.

**Prática recomendada**: identificar e categorizar contas que estão em funções altamente privilegiadas.   
**Detalhe**: depois de ativar Azure ad Privileged Identity Management, exiba os usuários que estão no administrador global, administrador de função com privilégios e outras funções altamente privilegiadas. Remova todas as contas que não são mais necessárias nessas funções e categorize as contas restantes atribuídas às funções de administrador:

* Atribuído individualmente a usuários administrativos e pode ser usado para fins não administrativos (por exemplo, email pessoal)
* Atribuído individualmente a usuários administrativos e designado apenas para fins administrativos
* Compartilhado entre vários usuários
* Cenários de acesso de emergência
* Para scripts automatizados
* Para usuários externos

**Prática recomendada**: implemente o acesso "Just in time" (JIT) para reduzir ainda mais o tempo de exposição de privilégios e aumentar sua visibilidade no uso de contas com privilégios.   
**Detalhe**: Azure ad Privileged Identity Management permite que você:

* Limite os usuários a assumirem apenas seus privilégios JIT.
* Atribua funções para uma duração reduzida com confiança de que os privilégios são revogados automaticamente.

**Prática recomendada**: defina pelo menos duas contas de acesso de emergência.   
**Detalhe**: as contas de acesso de emergência ajudam as organizações a restringir o acesso privilegiado em um ambiente de Azure Active Directory existente. Essas contas são altamente privilegiadas e não são atribuídas a indivíduos específicos. As contas de acesso de emergência são limitadas a cenários em que as contas administrativas normais não podem ser usadas. As organizações devem limitar o uso da conta de emergência apenas ao período necessário.

Avalie as contas atribuídas ou qualificadas para a função de administrador global. Se você não vir nenhuma conta somente na nuvem usando o `*.onmicrosoft.com` domínio (destinado ao acesso de emergência), crie-as. Para obter mais informações, consulte [Gerenciando contas administrativas de acesso de emergência no Azure ad](/azure/active-directory/users-groups-roles/directory-emergency-access).

**Prática recomendada**: ter um processo de "interrupção" em vigor no caso de uma emergência.
**Detalhe**: siga as etapas em [protegendo o acesso privilegiado para implantações híbridas e na nuvem no Azure ad](/azure/active-directory/users-groups-roles/directory-admin-roles-secure).

**Prática recomendada**: exigir que todas as contas de administrador críticas tenham menos senha (preferencial) ou exijam a autenticação multifator.
**Detalhe**: Use o [aplicativo Microsoft Authenticator](/azure/active-directory/authentication/howto-authentication-phone-sign-in) para entrar em qualquer conta do Azure ad sem usar uma senha. Como o [Windows Hello para empresas](/windows/security/identity-protection/hello-for-business/hello-identity-verification), o Microsoft Authenticator usa a autenticação baseada em chave para habilitar uma credencial de usuário vinculada a um dispositivo e usa a autenticação biométrica ou um PIN.

Exigir a autenticação multifator do Azure na entrada para todos os usuários individuais que são atribuídos permanentemente a uma ou mais das funções de administrador do Azure AD: administrador global, administrador de função com privilégios, administrador do Exchange Online e SharePoint Online Administradores. Habilite [a autenticação multifator para suas contas de administrador](/azure/active-directory/authentication/howto-mfa-userstates) e verifique se os usuários da conta de administrador se registraram.

**Prática recomendada**: para contas de administrador críticas, tenha uma estação de trabalho de administração em que as tarefas de produção não são permitidas (por exemplo, navegação e email). Isso protegerá suas contas de administrador de vetores de ataque que usam navegação e email e reduzirá significativamente o risco de um incidente importante.
**Detalhe**: Use uma estação de trabalho de administrador. Escolha um nível de segurança de estação de trabalho:

- Os dispositivos de produtividade altamente seguros fornecem segurança avançada para navegação e outras tarefas de produtividade.
- As [estações de trabalho com acesso privilegiado (PAWs)](/windows-server/identity/securing-privileged-access/privileged-access-workstations) fornecem um sistema operacional dedicado protegido contra ataques da Internet e vetores de ameaça para tarefas confidenciais.

**Prática recomendada**: desprovisionar contas de administrador quando os funcionários deixam sua organização.
**Detalhe**: tenha um processo em vigor que desabilite ou exclua contas de administrador quando os funcionários deixam sua organização.

**Prática recomendada**: teste regularmente as contas de administrador usando as técnicas de ataque atuais.
**Detalhe**: Use o simulador de ataque do Office 365 ou uma oferta de terceiros para executar cenários de ataque realistas em sua organização. Isso pode ajudá-lo a encontrar usuários vulneráveis antes que ocorra um ataque real.

**Prática recomendada**: siga as etapas para mitigar as técnicas de ataque mais usadas.  
**Detalhe**: [identificar contas da Microsoft em funções administrativas que precisam ser alternadas para contas corporativas ou de estudante](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Garantir contas de usuário separadas e encaminhamento de email para contas de administrador global](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)  

[Verifique se as senhas das contas administrativas foram alteradas recentemente](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Ativar a sincronização de hash de senha](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)  

[Exigir autenticação multifator para usuários em todas as funções privilegiadas, bem como usuários expostos](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Obtenha sua pontuação segura do Office 365 (se estiver usando o Office 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#obtain-your-office-365-secure-score-if-using-office-365)  

[Examine as diretrizes de segurança e conformidade do Office 365 (se estiver usando o Office 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Configurar o monitoramento de atividades do Office 365 (se estiver usando o Office 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#configure-office-365-activity-monitoring-if-using-office-365)  

[Estabelecer proprietários de planos de resposta a incidentes/emergência](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#establish-incidentemergency-response-plan-owners)  

[Proteger contas administrativas locais com privilégios](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)

Se você não proteger o acesso privilegiado, poderá descobrir que tem muitos usuários em funções altamente privilegiadas e que são mais vulneráveis a ataques. Atores mal-intencionados, incluindo invasores cibernéticos, geralmente direcionam contas de administrador e outros elementos de acesso privilegiado para obter acesso a dados e sistemas confidenciais usando roubo de credenciais.

## <a name="control-locations-where-resources-are-created"></a>Locais de controle onde os recursos são criados

A habilitação dos operadores de nuvem para executar tarefas, ao mesmo tempo em que os impede de dividir as convenções que são necessárias para gerenciar os recursos de sua organização é muito importante. As organizações que desejam controlar os locais onde os recursos são criados devem embutir em código esses locais.

Você pode usar [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) para criar políticas de segurança cujas definições descrevem as ações ou os recursos que são especificamente negados. Atribua essas definições de política no escopo desejado, como a assinatura, o grupo de recursos ou um recurso individual.

> [!NOTE]
> As políticas de segurança não são as mesmas do RBAC. Na verdade, eles usam o RBAC para autorizar os usuários a criarem esses recursos.
>
>

As organizações que não controlam como os recursos são criados são mais suscetíveis a usuários que podem abusar o serviço criando mais recursos do que precisam. A proteção do processo de criação de recursos é uma etapa importante para proteger um cenário multilocatário.

## <a name="actively-monitor-for-suspicious-activities"></a>Monitorar ativamente as atividades suspeitas

Um sistema de monitoramento de identidade ativo pode detectar rapidamente um comportamento suspeito e disparar um alerta para uma investigação mais aprofundada. A tabela a seguir lista dois recursos do Azure AD que podem ajudar as organizações a monitorar suas identidades:

**Prática recomendada**: ter um método para identificar:

- Tentativas de entrada [sem rastreamento](/azure/active-directory/active-directory-reporting-sign-ins-from-unknown-sources).
- Ataques de [força bruta](/azure/active-directory/active-directory-reporting-sign-ins-after-multiple-failures) contra uma conta específica.
- Tentativas de entrada de vários locais.
- Entradas de [dispositivos infectados](/azure/active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices).
- Endereços IP suspeitos.

**Detalhe**: Use Azure ad Premium [relatórios de anomalias](/azure/active-directory/active-directory-view-access-usage-reports). Ter processos e procedimentos em vigor para os administradores de ti executarem esses relatórios diariamente ou sob demanda (geralmente em um cenário de resposta a incidentes).

**Prática recomendada**: ter um sistema de monitoramento ativo que notifique você sobre riscos e possa ajustar o nível de risco (alto, médio ou baixo) para seus requisitos de negócios.   
**Detalhe**: use [Azure ad Identity Protection](/azure/active-directory/active-directory-identityprotection), que sinaliza os riscos atuais em seu próprio painel e envia notificações de resumo diário por email. Para ajudar a proteger as identidades de sua organização, você pode configurar políticas baseadas em risco que respondem automaticamente a problemas detectados quando um nível de risco especificado é atingido.

As organizações que não monitoram ativamente seus sistemas de identidade correm o risco de manter as credenciais do usuário comprometidas. Sem o conhecimento de que as atividades suspeitas estão ocorrendo por meio dessas credenciais, as organizações não podem mitigar esse tipo de ameaça.

## <a name="use-azure-ad-for-storage-authentication"></a>Usar o Azure AD para autenticação de armazenamento
O [armazenamento do Azure](/azure/storage/common/storage-auth-aad) dá suporte à autenticação e autorização com o Azure ad para armazenamento de BLOBs e armazenamento de filas. Com a autenticação do Azure AD, você pode usar o controle de acesso baseado em função do Azure para conceder permissões específicas a usuários, grupos e aplicativos para o escopo de um contêiner de BLOB ou fila individual.

Recomendamos que você use o [Azure ad para autenticar o acesso ao armazenamento](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/).

## <a name="next-step"></a>Passo seguinte

Veja [padrões e práticas recomendadas de segurança do Azure](best-practices-and-patterns.md) para obter mais práticas recomendadas de segurança para usar ao projetar, implantar e gerenciar suas soluções de nuvem usando o Azure.
