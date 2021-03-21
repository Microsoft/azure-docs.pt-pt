---
title: Identidade azul & acesso a boas práticas de segurança | Microsoft Docs
description: Este artigo fornece um conjunto de boas práticas para gestão de identidade e controlo de acessos usando as capacidades do Azure.
services: security
documentationcenter: na
author: terrylanfear
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
ms.author: terrylan
ms.openlocfilehash: f69fe97c33a17ade39f67078d5b035dac4d0bfaf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034171"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Gestão de Identidade Azure e controlo de segurança de acessos as melhores práticas

Neste artigo, discutimos uma coleção de gestão de identidade Azure e controle de segurança de acessos as melhores práticas. Estas boas práticas derivam da nossa experiência com [a Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) e das experiências de clientes como você.

Para cada boa prática, explicamos:

* Qual é a melhor prática
* Por que quer permitir a melhor prática
* Qual pode ser o resultado se não conseguires permitir as melhores práticas
* Possíveis alternativas às melhores práticas
* Como pode aprender a permitir as melhores práticas

Este artigo de gestão de identidade e controlo de acessos Azure baseia-se numa opinião consensual e nas capacidades e conjuntos de funcionalidades da plataforma Azure, tal como existem no momento em que este artigo foi escrito.

A intenção de escrever este artigo é fornecer um roteiro geral para uma postura de segurança mais robusta após a implementação guiada pela nossa "[5 passos para garantir a sua infraestrutura de identidade](steps-secure-identity.md)" lista de verificação, que o acompanha através de algumas das nossas principais funcionalidades e serviços.

As opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

As melhores práticas de segurança de gestão de identidade e acessos azure discutidas neste artigo incluem:

* Trate a identidade como o perímetro de segurança primário
* Centralizar a gestão de identidades
* Gerir inquilinos conectados
* Ativar um único sinal
* Ligue o acesso condicional
* Plano para melhorias de segurança de rotina
* Ativar a gestão de palavras-passe
* Impor a verificação de vários fatores para os utilizadores
* Utilizar o controlo de acesso baseado em funções
* Menor exposição de contas privilegiadas
* Locais de controlo onde os recursos estão localizados
* Utilize Azure AD para autenticação de armazenamento

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Trate a identidade como o perímetro de segurança primário

Muitos consideram a identidade o principal perímetro de segurança. Esta é uma mudança do foco tradicional na segurança da rede. Os perímetros da rede continuam a ficar mais porosos, e a defesa do perímetro não pode ser tão eficaz como era antes da explosão de dispositivos [BYOD](/mem/intune/fundamentals/byod-technology-decisions) e aplicações em nuvem.

[O Azure Ative Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) é a solução Azure para a gestão de identidade e acesso. Azure AD é um serviço multitenante, baseado na nuvem e gestão de identidade da Microsoft. Combina serviços de diretório base, gestão de acesso a aplicações e proteção de identidade numa única solução.

As secções seguintes listam as melhores práticas para a segurança de identidade e acesso utilizando o Azure AD.

**Melhores práticas**: Centros de controlos de segurança e deteções em torno das identidades do utilizador e do serviço.
**Detalhe**: Utilize a Azure AD para colocar controlos e identidades.

## <a name="centralize-identity-management"></a>Centralizar a gestão de identidades

Num cenário [de identidade híbrida](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) recomendamos que integre os seus diretórios no local e em nuvem. A integração permite à sua equipa de TI gerir contas a partir de um local, independentemente do local onde uma conta é criada. A integração também ajuda os seus utilizadores a serem mais produtivos, fornecendo uma identidade comum para aceder tanto a recursos na nuvem como no local.

**Melhores práticas**: Estabeleça uma única instância AD Azure. A coerência e uma única fonte autoritária aumentarão a clareza e reduzirão os riscos de segurança resultantes de erros humanos e complexidade de configuração.
**Detalhe**: Designar um único diretório AZure AD como fonte autoritária para contas corporativas e organizacionais.

**Melhores práticas**: Integre os seus diretórios no local com a Azure AD.  
**Detalhe**: Utilize [o Azure AD Connect](../../active-directory/hybrid/whatis-hybrid-identity.md) para sincronizar o seu diretório no local com o seu diretório em nuvem.

> [!Note]
> Existem [fatores que afetam o desempenho do Azure AD Connect](../../active-directory/hybrid/plan-connect-performance-factors.md). Certifique-se de que o Azure AD Connect tem capacidade suficiente para evitar que os sistemas de baixo desempenho impeçam a segurança e a produtividade. As organizações grandes ou complexas (organizações que adempem mais de 100.000 [objetos)](../../active-directory/hybrid/whatis-hybrid-identity.md) devem seguir as recomendações para otimizar a sua implementação Azure AD Connect.

**Melhores práticas**: Não sincronize as contas da AZure AD que têm privilégios elevados na sua instância de Ative Directory existente.
**Detalhe**: Não altere a configuração padrão do [Azure AD Connect](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) que filtra estas contas. Esta configuração atenua o risco de os adversários se dedicarem da nuvem aos ativos no local (o que pode criar um incidente grave).

**Melhores práticas**: Ligue a sincronização do hash da palavra-passe.  
**Detalhe**: A sincronização de hash de palavra-passe é uma funcionalidade usada para sincronizar as hashes da palavra-passe do utilizador de uma instância de Ative Directory no local para uma instância AD Azure baseada na nuvem. Esta sincronização ajuda a proteger contra credenciais vazadas que são reproduzidas de ataques anteriores.

Mesmo que decida utilizar a federação com serviços da Federação de Diretórios Ativos (AD FS) ou outros fornecedores de identidade, pode configurar opcionalmente a sincronização de hash de palavra-passe como uma cópia de segurança caso os seus servidores no local falhem ou fiquem temporariamente indisponíveis. Esta sincronização permite que os utilizadores entrem no serviço utilizando a mesma palavra-passe que usam para iniciar sômposições no seu local de acesso ative. Também permite que a Proteção de Identidade detete credenciais comprometidas comparando hashes de palavra-passe sincronizadas com palavras-passe conhecidas por estarem comprometidas, se um utilizador tiver usado o mesmo endereço de e-mail e palavra-passe em outros serviços que não estão ligados ao Azure AD.

Para obter mais informações, consulte [implementar a sincronização de hash de palavra-passe com a sincronização Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

**Melhores práticas**: Para o desenvolvimento de novas aplicações, utilize a Azure AD para autenticação.
**Detalhe**: Utilize as capacidades corretas para suportar a autenticação:

  - Azure AD para funcionários
  - [Azure AD B2B](../../active-directory/external-identities/index.yml) para utilizadores convidados e parceiros externos
  - [Azure AD B2C](../../active-directory-b2c/index.yml) para controlar a forma como os clientes se inscrevem, se inscrevem e gerem os seus perfis quando utilizam as suas aplicações

As organizações que não integram a sua identidade no local com a sua identidade em nuvem podem ter mais despesas gerais na gestão de contas. Isto aumenta a probabilidade de erros e falhas de segurança.

> [!Note]
> Você precisa escolher em que diretórios contas críticas vão residir e se a estação de trabalho de administração usada é gerida por novos serviços na nuvem ou processos existentes. A utilização dos processos de gestão e de provisão de identidade existentes pode diminuir alguns riscos, mas também pode criar o risco de um intruso comprometer uma conta no local e apostar na nuvem. Você pode querer usar uma estratégia diferente para diferentes funções (por exemplo, administradores de TI vs. administradores de unidades de negócio). Tem duas opções. A primeira opção é criar contas AD Azure que não estejam sincronizadas com a sua instância ative de diretório no local. Junte-se à sua estação de trabalho de administração para Azure AD, que pode gerir e corrigir utilizando o Microsoft Intune. A segunda opção é utilizar as contas de administração existentes sincronizando-as no local da instância Ative Directory. Utilize estações de trabalho existentes no seu domínio Ative Directory para gestão e segurança.

## <a name="manage-connected-tenants"></a>Gerir inquilinos conectados
A sua organização de segurança precisa de visibilidade para avaliar o risco e determinar se as políticas da sua organização, e quaisquer requisitos regulamentares, estão a ser seguidos. Deve certificar-se de que a sua organização de segurança tem visibilidade em todas as subscrições ligadas ao seu ambiente de produção e rede (via [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) ou [VPN site-to-site).](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) Um [Administrador Global](../../active-directory/roles/permissions-reference.md#global-administrator) em Azure AD pode elevar o seu acesso à função de Administrador de Acesso ao [Utilizador](../../role-based-access-control/built-in-roles.md#user-access-administrator) e ver todas as subscrições e grupos geridos ligados ao seu ambiente.

Consulte [o acesso elevado para gerir todas as subscrições e grupos de gestão da Azure](../../role-based-access-control/elevate-access-global-admin.md) para garantir que você e o seu grupo de segurança podem ver todas as subscrições ou grupos de gestão ligados ao seu ambiente. Deve remover este acesso elevado depois de avaliar os riscos.

## <a name="enable-single-sign-on"></a>Ativar um único sinal

Num mundo móvel, primeiro em nuvem, pretende permitir um único sign-on (SSO) a dispositivos, apps e serviços de qualquer lugar para que os seus utilizadores possam ser produtivos onde e quando quiser. Quando se tem múltiplas soluções de identidade para gerir, este torna-se um problema administrativo não só para TI mas também para utilizadores que têm de se lembrar de várias palavras-passe.

Ao utilizar a mesma solução de identidade para todas as suas apps e recursos, pode alcançar sSO. E os seus utilizadores podem usar o mesmo conjunto de credenciais para iniciar seduções e aceder aos recursos de que necessitam, quer os recursos estejam localizados no local ou na nuvem.

**Melhores práticas**: Ativar sSO.  
**Detalhe**: Azure AD [estende-se no local Ative Directy](../../active-directory/hybrid/whatis-hybrid-identity.md) para a nuvem. Os utilizadores podem usar o seu trabalho primário ou conta escolar para os seus dispositivos de domínio, recursos da empresa e todas as aplicações web e SaaS de que precisam para fazer o seu trabalho. Os utilizadores não têm de se lembrar de vários conjuntos de nomes de utilizador e palavras-passe, e o acesso à aplicação pode ser automaticamente aprovisionado (ou desprovisionado) com base nos membros do grupo de organização e no seu estatuto de funcionário. E também pode controlar esse acesso para aplicações da galeria ou para as suas próprias aplicações no local que tenha desenvolvido e publicado através do [Proxy de Aplicações do AD](../../active-directory/manage-apps/application-proxy.md).

Utilize sSO para permitir que os utilizadores acedam às suas [aplicações SaaS](../../active-directory/manage-apps/what-is-single-sign-on.md) com base no seu trabalho ou na conta escolar em Azure AD. Isto aplica-se não só às aplicações da Microsoft SaaS, mas também a outras aplicações, como [as Google Apps](../../active-directory/saas-apps/google-apps-tutorial.md) e [Salesforce.](../../active-directory/saas-apps/salesforce-tutorial.md) Pode configurar a sua aplicação para utilizar o Azure AD como fornecedor [de identidade baseado em SAML.](../../active-directory/fundamentals/active-directory-whatis.md) Como controlo de segurança, a Azure AD não emite um token que permita aos utilizadores iniciar súpite na aplicação, a menos que tenham tido acesso através do Azure AD. Pode conceder acesso diretamente, ou através de um grupo do que os utilizadores são membros.

As organizações que não criam uma identidade comum para estabelecer SSO para os seus utilizadores e aplicações estão mais expostas a cenários onde os utilizadores têm várias palavras-passe. Estes cenários aumentam a probabilidade de os utilizadores reutilizarem palavras-passe ou usarem senhas fracas.

## <a name="turn-on-conditional-access"></a>Ligue o acesso condicional

Os utilizadores podem aceder aos recursos da sua organização utilizando uma variedade de dispositivos e aplicações de qualquer lugar. Como administrador de TI, pretende certificar-se de que estes dispositivos cumprem os seus padrões de segurança e conformidade. Concentrar-me em quem pode aceder a um recurso já não é suficiente.

Para equilibrar a segurança e a produtividade, precisa pensar em como um recurso é acedido antes de poder tomar uma decisão sobre o controlo de acessos. Com acesso condicional Azure AD, pode endereçar este requisito. Com o Acesso Condicional, pode tomar decisões automatizadas de controlo de acesso com base em condições de acesso às suas aplicações na nuvem.

**Melhores práticas**: Gerir e controlar o acesso aos recursos corporativos.  
**Detalhe**: Configurar políticas comuns de [acesso condicionado](../../active-directory/conditional-access/concept-conditional-access-policy-common.md) Azure AD com base num grupo, localização e sensibilidade de aplicações para aplicações SaaS e aplicações ligadas a Azure.

**Boas práticas**: Bloqueie os protocolos de autenticação do legado.
**Detalhe**: Os atacantes exploram fraquezas em protocolos mais antigos todos os dias, especialmente para ataques de spray de palavras-passe. Configure acesso condicional para [bloquear protocolos legados](../../active-directory/conditional-access/howto-conditional-access-policy-block-legacy.md).

## <a name="plan-for-routine-security-improvements"></a>Plano para melhorias de segurança de rotina

A segurança está sempre a evoluir, e é importante construir no seu quadro de gestão de nuvens e identidade uma forma de mostrar regularmente crescimento e descobrir novas formas de proteger o seu ambiente.

Identity Secure Score é um conjunto de controlos de segurança recomendados que a Microsoft publica que funciona para lhe fornecer uma pontuação numérica para medir objectivamente a sua postura de segurança e ajudar a planear futuras melhorias de segurança. Você também pode ver a sua pontuação em comparação com as de outras indústrias, bem como as suas próprias tendências ao longo do tempo.

**Melhores práticas**: Planeie revisões de segurança de rotina e melhorias baseadas nas melhores práticas da sua indústria.
**Detalhe**: Utilize a função 'Pontuação Segura identidade' para classificar as suas melhorias ao longo do tempo.

## <a name="enable-password-management"></a>Ativar a gestão de palavras-passe

Se tem vários inquilinos ou pretende permitir que os utilizadores [repusem as suas próprias palavras-passe,](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md)é importante que utilize as políticas de segurança adequadas para evitar abusos.

**Melhores práticas**: Configurar o reset da palavra-passe de autosserviço (SSPR) para os seus utilizadores.  
**Detalhe**: Utilize a função [de redefinição da palavra-passe de autosserviço](../../active-directory-b2c/user-flow-self-service-password-reset.md) Azure.

**Melhores práticas**: Monitorize como ou se a SSPR está realmente a ser utilizada.  
**Detalhe**: Monitorize os utilizadores que se registam utilizando o [relatório de Atividade de Registo de Redefinição de Password](../../active-directory/authentication/howto-sspr-reporting.md)Azure AD . A funcionalidade de reporte que a Azure AD fornece ajuda-o a responder a perguntas utilizando relatórios pré-construídos. Se você estiver devidamente licenciado, também pode criar consultas personalizadas.

**Melhores práticas**: Alargar as políticas de senha baseadas em nuvem à sua infraestrutura no local.
**Detalhe**: Melhore as políticas de palavra-passe na sua organização realizando as mesmas verificações para alterações de senha no local como faz para alterações de senha baseadas na nuvem. Instale [a proteção de senha AZure AD](../../active-directory/authentication/concept-password-ban-bad.md) para agentes do Windows Server Ative Directory no local para estender as listas de senhas proibidas à sua infraestrutura existente. Os utilizadores e administradores que alterem, decidem ou repõem palavras-passe no local são obrigados a cumprir a mesma política de palavra-passe que os utilizadores apenas na nuvem.

## <a name="enforce-multi-factor-verification-for-users"></a>Impor a verificação de vários fatores para os utilizadores

Recomendamos que necessite de uma verificação em duas etapas para todos os seus utilizadores. Isto inclui administradores e outros na sua organização que podem ter um impacto significativo se a sua conta estiver comprometida (por exemplo, gestores financeiros).

Existem várias opções para exigir uma verificação em duas etapas. A melhor opção para si depende dos seus objetivos, da edição AD AZure que está a executar e do seu programa de licenciamento. Veja [como exigir uma verificação em duas etapas para que um utilizador](../../active-directory/authentication/howto-mfa-userstates.md) determine a melhor opção para si. Consulte as páginas de preços de [autenticação multi-factor Azure Ad Azure Ad](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) para obter mais informações sobre licenças e preços. [](https://azure.microsoft.com/pricing/details/active-directory/)

Seguem-se opções e benefícios para permitir a verificação em duas etapas:

**Opção 1**: Ativar o MFA para todos os utilizadores e métodos de login com **Azure** AD Security Defaults Benefit : Esta opção permite-lhe aplicar facilmente e rapidamente o MFA para todos os utilizadores do seu ambiente com uma política rigorosa para:

* Contestar contas administrativas e mecanismos de início de símia administrativo
* Exigir desafio MFA através do Microsoft Authenticator para todos os utilizadores
* Restringir os protocolos de autenticação do legado.

Este método está disponível para todos os níveis de licenciamento, mas não é capaz de ser misturado com as políticas de acesso condicional existentes. Pode encontrar mais informações em [Azure AD Security Defaults](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md)

**Opção 2**: [Ativar a autenticação multi-factor alterando o estado do utilizador](../../active-directory/authentication/howto-mfa-userstates.md).   
**Benefício**: Este é o método tradicional para exigir uma verificação em duas etapas. Funciona com [a autenticação multi-factor Azure AD na nuvem e com o Azure Multi-Factor Authentication Server](../../active-directory/authentication/concept-mfa-howitworks.md). A utilização deste método requer que os utilizadores realizem a verificação em duas etapas sempre que assinam e substituem as políticas de Acesso Condicional.

Para determinar onde a autenticação multi-factor precisa de ser ativada, veja [qual a versão do Azure AD MFA que é a certa para a minha organização?](../../active-directory/authentication/concept-mfa-howitworks.md)

**Opção 3**: [Ativar a autenticação multi-factor com a política de acesso condicional.](../../active-directory/authentication/howto-mfa-getstarted.md)
**Benefício**: Esta opção permite-lhe solicitar a verificação em duas etapas em condições específicas utilizando [o Acesso Condicional.](../../active-directory/conditional-access/concept-conditional-access-policy-common.md) Condições específicas podem ser o pedido de saúde de diferentes locais, dispositivos não fidedqui latas ou aplicações que considere arriscadas. Definir condições específicas onde necessita de uma verificação em duas etapas permite evitar solicitações constantes para os seus utilizadores, o que pode ser uma experiência desagradável do utilizador.

Esta é a forma mais flexível de permitir uma verificação em duas etapas para os seus utilizadores. Permitir uma política de acesso condicional funciona apenas para autenticação multi-factor Azure AD na nuvem e é uma característica premium do Azure AD. Pode encontrar mais informações sobre este método na [Implementação de autenticação multi-factor Azure AD baseada na nuvem.](../../active-directory/authentication/howto-mfa-getstarted.md)

**Opção 4**: Ativar a autenticação multi-factor com políticas de acesso condicional, avaliando [as políticas de acesso condicional baseados no risco.](../../active-directory/conditional-access/howto-conditional-access-policy-risk.md)   
**Benefício**: Esta opção permite-lhe:

* Detete potenciais vulnerabilidades que afetam as identidades da sua organização.
* Configure respostas automatizadas para detetar ações suspeitas relacionadas com as identidades da sua organização.
* Investigue incidentes suspeitos e tome as medidas apropriadas para resolvê-los.

Este método utiliza a avaliação do risco de proteção de identidade Azure AD para determinar se é necessária uma verificação em duas etapas com base no risco de acesso ao utilizador e ao risco de inscrição para todas as aplicações na nuvem. Este método requer o licenciamento do Azure Ative Directory P2. Pode encontrar mais informações sobre este método na [Azure Ative Directory Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md).

> [!Note]
> A opção 2, permitindo a autenticação multi-factor alterando o estado do utilizador, substitui as políticas de Acesso Condicional. Como as opções 3 e 4 utilizam as políticas de Acesso Condicional, não é possível utilizar a opção 2 com elas.

Organizações que não adicionam camadas extra de proteção de identidade, como a verificação em duas etapas, são mais suscetíveis para ataques de roubo credenciais. Um ataque de roubo de credencial pode levar a um compromisso de dados.

## <a name="use-role-based-access-control"></a>Utilizar o controlo de acesso baseado em funções

A gestão de acesso a recursos na nuvem é fundamental para qualquer organização que utilize a nuvem. [O controlo de acesso baseado em funções (Azure RBAC) ajuda-o](../../role-based-access-control/overview.md) a gerir quem tem acesso aos recursos Azure, o que podem fazer com esses recursos e a que áreas têm acesso.

Designar grupos ou funções individuais responsáveis por funções específicas em Azure ajuda a evitar confusões que podem levar a erros humanos e de automação que criam riscos de segurança. Restringir o acesso com base na [necessidade de conhecer](https://en.wikipedia.org/wiki/Need_to_know) e menos [privilégios](https://en.wikipedia.org/wiki/Principle_of_least_privilege) princípios de segurança é imperativo para as organizações que querem impor políticas de segurança para o acesso aos dados.

A sua equipa de segurança precisa de visibilidade nos seus recursos Azure para avaliar e remediar o risco. Se a equipa de segurança tem responsabilidades operacionais, precisam de autorizações adicionais para fazer o seu trabalho.

Pode utilizar [o Azure RBAC](../../role-based-access-control/overview.md) para atribuir permissões a utilizadores, grupos e aplicações num determinado âmbito. O âmbito da atribuição de uma função pode ser uma subscrição, um grupo de recursos ou um único recurso.

**Boas práticas**: Segregar os deveres dentro da sua equipa e conceder apenas a quantidade de acesso aos utilizadores de que necessitam para desempenharem os seus trabalhos. Em vez de dar a todos permissões ilimitadas na sua subscrição ou recursos Azure, permita apenas certas ações num determinado âmbito.
**Detalhe**: Utilize [funções incorporadas em Azure](../../role-based-access-control/built-in-roles.md) para atribuir privilégios aos utilizadores.

> [!Note]
> Permissões específicas criam complexidade e confusão não precisas, acumulando-se numa configuração "legado" que é difícil de corrigir sem medo de quebrar algo. Evite permissões específicas de recursos. Em vez disso, utilize grupos de gestão para permissões e grupos de recursos para permissões dentro das subscrições. Evite permissões específicas do utilizador. Em vez disso, atribua acesso a grupos no Azure AD.

**Melhores práticas**: Conceder às equipas de segurança com responsabilidades Azure o acesso a ver recursos Azure para que possam avaliar e remediar o risco.
**Detalhe**: Conceder às equipas de segurança o papel de [Leitor de Segurança](../../role-based-access-control/built-in-roles.md#security-reader) Azure RBAC. Pode utilizar o grupo de gestão de raízes ou o grupo de gestão de segmentos, dependendo do âmbito das responsabilidades:

* **Grupo de gestão de** raiz para equipas responsáveis por todos os recursos empresariais
* **Grupo de gestão de segmentos** para equipas com âmbito limitado (geralmente devido a limites regulamentares ou outros limites organizacionais)

**Melhores práticas**: Conceder as permissões adequadas às equipas de segurança que tenham responsabilidades operacionais diretas.
**Detalhe**: Reveja as funções incorporadas do Azure para a atribuição de funções apropriadas. Se as funções incorporadas não corresponderem às necessidades específicas da sua organização, pode criar [funções personalizadas Azure](../../role-based-access-control/custom-roles.md). Tal como acontece com as funções incorporadas, pode atribuir funções personalizadas aos utilizadores, grupos e principais de serviços nos âmbitos de subscrição, grupo de recursos e recursos.

**Boas práticas**: Grant Azure Security Center acesso a funções de segurança que precisam. O Centro de Segurança permite que as equipas de segurança identifiquem e remediam rapidamente os riscos.
**Detalhe**: Adicione as equipas de segurança com estas necessidades ao papel de [Administrador de Segurança](../../role-based-access-control/built-in-roles.md#security-admin) da Azure RBAC para que possam ver políticas de segurança, ver estados de segurança, editar políticas de segurança, ver alertas e recomendações e rejeitar alertas e recomendações. Pode fazê-lo utilizando o grupo de gestão de raiz ou o grupo de gestão de segmentos, dependendo do âmbito das responsabilidades.

Organizações que não impõem o controlo do acesso a dados usando capacidades como o Azure RBAC podem estar a dar mais privilégios do que o necessário aos seus utilizadores. Isto pode levar a um compromisso de dados, permitindo que os utilizadores acedam a tipos de dados (por exemplo, impacto de alto negócio) que não deveriam ter.

## <a name="lower-exposure-of-privileged-accounts"></a>Menor exposição de contas privilegiadas

Garantir o acesso privilegiado é um primeiro passo crítico para proteger os ativos empresariais. Minimizar o número de pessoas que têm acesso a informações ou recursos seguros reduz a possibilidade de um utilizador malicioso ter acesso, ou de um utilizador autorizado afetar inadvertidamente um recurso sensível.

As contas privilegiadas são contas que administram e gerem sistemas de TI. Os ciberataques têm como alvo estas contas para ter acesso aos dados e sistemas de uma organização. Para garantir um acesso privilegiado, deverá isolar as contas e sistemas do risco de serem expostos a um utilizador malicioso.

Recomendamos que desenvolva e siga um roteiro para garantir o acesso privilegiado contra os ciberataques. Para obter informações sobre a criação de um roteiro detalhado para garantir identidades e acessos geridos ou relatados em Azure AD, Microsoft Azure, Microsoft 365 e outros serviços na nuvem, [reveja a garantia de acesso privilegiado para implementações híbridas e em nuvem em Azure AD](../../active-directory/roles/security-planning.md).

O seguinte resume as melhores práticas encontradas na [garantia de acesso privilegiado para implantações híbridas e em nuvem em Azure AD:](../../active-directory/roles/security-planning.md)

**Melhores práticas**: Gerir, controlar e monitorizar o acesso a contas privilegiadas.   
**Detalhe**: Ligue [a Azure AD Gestão de Identidade Privilegiada](../../active-directory/roles/security-planning.md). Depois de ligar a Gestão de Identidade Privilegiada, receberá mensagens de correio de notificação para alterações privilegiadas na função de acesso. Estas notificações fornecem um alerta precoce quando utilizadores adicionais são adicionados a funções altamente privilegiadas no seu diretório.

**Melhores práticas**: Certifique-se de que todas as contas de administração crítica são geridas contas AZure AD.
**Detalhe**: Remova quaisquer contas de consumidores de funções de administração crítica (por exemplo, contas da Microsoft como hotmail.com, live.com e outlook.com).

**Boas práticas**: Garantir que todas as funções de administração crítica têm uma conta separada para tarefas administrativas, a fim de evitar phishing e outros ataques para comprometer privilégios administrativos.
**Detalhe**: Criar uma conta de administração separada que tenha atribuído os privilégios necessários para executar as tarefas administrativas. Bloqueie a utilização destas contas administrativas para ferramentas diárias de produtividade como o e-mail microsoft 365 ou a navegação arbitrária na Web.

**Melhores práticas**: Identifique e categorize contas que se encontram em funções altamente privilegiadas.   
**Detalhe**: Depois de ligar a Azure AD Privileged Identity Management, consulte os utilizadores que estão no administrador global, administrador privilegiado e outras funções altamente privilegiadas. Remova quaisquer contas que já não sejam necessárias nessas funções, e categorize as restantes contas que são atribuídas a funções de administrador:

* Individualmente atribuído a utilizadores administrativos, e pode ser utilizado para fins não administrativos (por exemplo, e-mail pessoal)
* Individualmente atribuído a utilizadores administrativos e designado apenas para fins administrativos
* Partilhado em vários utilizadores
* Para cenários de acesso de emergência
* Para scripts automatizados
* Para utilizadores externos

**Melhores práticas**: Implementar o acesso "just in time" (JIT) para reduzir ainda mais o tempo de exposição dos privilégios e aumentar a sua visibilidade na utilização de contas privilegiadas.   
**Detalhe**: Azure AD Gestão de Identidade Privilegiada permite-lhe:

* Limitar os utilizadores a assumirem apenas os seus privilégios JIT.
* Atribua funções por uma duração reduzida com confiança de que os privilégios são revogados automaticamente.

**Melhores práticas**: Defina pelo menos duas contas de acesso de emergência.   
**Detalhe**: As contas de acesso de emergência ajudam as organizações a restringir o acesso privilegiado num ambiente existente do Azure Ative Directory. Estas contas são altamente privilegiadas e não são atribuídas a indivíduos específicos. As contas de acesso de emergência limitam-se a cenários em que as contas administrativas normais não podem ser utilizadas. As organizações devem limitar o uso da conta de emergência apenas ao tempo necessário.

Avaliar as contas que são atribuídas ou elegíveis para o papel de administrador global. Se não vir contas apenas na nuvem utilizando o domínio (destinado a `*.onmicrosoft.com` acesso de emergência), crie-as. Para obter mais informações, consulte [gerir as contas administrativas de acesso de emergência em Azure AD](../../active-directory/roles/security-emergency-access.md).

**Melhores práticas**: Tenha um processo de "break glass" em caso de emergência.
**Detalhe**: Siga os passos na [garantia de acesso privilegiado para implantações híbridas e em nuvem em Azure AD](../../active-directory/roles/security-planning.md).

**Melhores práticas**: Exigir que todas as contas de administração críticas sejam sem palavra-passe (preferencial) ou exijam autenticação multi-factor.
**Detalhe**: Utilize a [aplicação Microsoft Authenticator](../../active-directory/authentication/howto-authentication-passwordless-phone.md) para iniciar serção em qualquer conta AD Azure sem utilizar uma palavra-passe. Tal como o [Windows Hello for Business,](/windows/security/identity-protection/hello-for-business/hello-identity-verification)o Microsoft Authenticator utiliza a autenticação baseada em chaves para permitir uma credencial do utilizador que está ligada a um dispositivo e utiliza a autenticação biométrica ou um PIN.

Exigir autenticação multi-factor Azure AD no início de sessão para todos os utilizadores individuais que estejam permanentemente atribuídos a uma ou mais funções de administração Admin da Azure: Administrador Global, Administrador de Função Privilegiada, Administrador Online de Intercâmbio e Administrador Online do SharePoint. Ativar [a autenticação multi-factor para as suas contas de administração](../../active-directory/authentication/howto-mfa-userstates.md) e garantir que os utilizadores de conta administração se registaram.

**Melhores práticas**: Para contas de administração crítica, tenha uma estação de trabalho de administração onde não são permitidas tarefas de produção (por exemplo, navegação e e-mail). Isto irá proteger as suas contas de administração de vetores de ataque que usam navegação e e-mail e reduzir significativamente o risco de um incidente grave.
**Detalhe**: Utilize uma estação de trabalho de administração. Escolha um nível de segurança da estação de trabalho:

- Dispositivos de produtividade altamente seguros proporcionam segurança avançada para a navegação e outras tarefas de produtividade.
- [As Estações de Trabalho de Acesso Privilegiadas (PAWs)](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) fornecem um sistema operativo dedicado que está protegido contra ataques de internet e vetores de ameaças para tarefas sensíveis.

**Melhores práticas**: Deprovision contas de administração quando os colaboradores deixam a sua organização.
**Detalhe**: Tenha em prática um processo que desativa ou elimina contas de administração quando os colaboradores deixam a sua organização.

**Melhores práticas**: Teste regularmente as contas de administração utilizando as técnicas de ataque atuais.
**Detalhe**: Utilize o Microsoft 365 Attack Simulator ou uma oferta de terceiros para executar cenários de ataque realistas na sua organização. Isto pode ajudá-lo a encontrar utilizadores vulneráveis antes que ocorra um ataque real.

**Melhores práticas**: Tome medidas para mitigar as técnicas atacadas mais utilizadas.  
**Detalhe**: [Identifique as contas da Microsoft em funções administrativas que precisam de ser mudadas para contas de trabalho ou escolas](../../active-directory/roles/security-planning.md#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Garantir contas de utilizador separadas e encaminhamento de correio para contas de administrador global](../../active-directory/roles/security-planning.md)  

[Certifique-se de que as palavras-passe das contas administrativas mudaram recentemente](../../active-directory/roles/security-planning.md#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Ligue a sincronização do hash da palavra-passe](../../active-directory/roles/security-planning.md#turn-on-password-hash-synchronization)  

[Requerem autenticação multi-factor para os utilizadores em todas as funções privilegiadas, bem como utilizadores expostos](../../active-directory/roles/security-planning.md#require-multi-factor-authentication-for-users-in-privileged-roles-and-exposed-users)  

[Obtenha a sua Microsoft 365 Secure Score (se utilizar o Microsoft 365)](../../active-directory/roles/security-planning.md#obtain-your-microsoft-365-secure-score-if-using-microsoft-365)  

[Reveja a orientação de segurança da Microsoft 365 (se utilizar o Microsoft 365)](../../active-directory/roles/security-planning.md#review-the-microsoft-365-security-and-compliance-guidance-if-using-microsoft-365)  

[Configure o Microsoft 365 Activity Monitoring (se utilizar o Microsoft 365)](../../active-directory/roles/security-planning.md#configure-microsoft-365-activity-monitoring-if-using-microsoft-365)  

[Estabelecer proprietários de planos de resposta a incidentes/emergências](../../active-directory/roles/security-planning.md#establish-incidentemergency-response-plan-owners)  

[Contas administrativas privilegiadas seguras no local](../../active-directory/roles/security-planning.md#turn-on-password-hash-synchronization)

Se não garantir acesso privilegiado, poderá descobrir que tem demasiados utilizadores em funções altamente privilegiadas e que são mais vulneráveis a ataques. Atores maliciosos, incluindo ciberataques, muitas vezes visam contas de administração e outros elementos de acesso privilegiado para ter acesso a dados e sistemas sensíveis usando roubo de credenciais.

## <a name="control-locations-where-resources-are-created"></a>Locais de controlo onde os recursos são criados

Permitir que os operadores de nuvem executem tarefas, impedindo-os de quebrar convenções necessárias para gerir os recursos da sua organização é muito importante. As organizações que querem controlar os locais onde os recursos são criados devem codificar estes locais.

Pode utilizar [o Azure Resource Manager](../../azure-resource-manager/management/overview.md) para criar políticas de segurança cujas definições descrevem as ações ou recursos que são especificamente negados. Atribui essas definições de política no âmbito pretendido, como a subscrição, o grupo de recursos ou um recurso individual.

> [!NOTE]
> As políticas de segurança não são as mesmas que a Azure RBAC. Eles realmente usam o Azure RBAC para autorizar os utilizadores a criar esses recursos.
>
>

As organizações que não controlam a forma como os recursos são criados são mais suscetíveis aos utilizadores que podem abusar do serviço criando mais recursos do que precisam. Endurecer o processo de criação de recursos é um passo importante para assegurar um cenário multitennte.

## <a name="actively-monitor-for-suspicious-activities"></a>Monitorize ativamente para atividades suspeitas

Um sistema de monitorização de identidade ativo pode rapidamente detetar comportamentos suspeitos e desencadear um alerta para uma investigação mais aprofundada. A tabela a seguir lista duas capacidades AD Azure que podem ajudar as organizações a monitorizar as suas identidades:

**Melhores práticas**: Ter um método para identificar:

- Tentativas de se inscrever [sem ser rastreada.](../../active-directory/reports-monitoring/howto-find-activity-reports.md)
- [Ataques de força bruta](../../active-directory/reports-monitoring/howto-find-activity-reports.md) contra uma determinada conta.
- Tentativas de entrar em vários locais.
- Insuposições de [dispositivos infetados.](../../active-directory/reports-monitoring/howto-find-activity-reports.md)
- Endereços IP suspeitos.

**Detalhe**: Utilize relatórios de [anomalias](../../active-directory/reports-monitoring/overview-reports.md)Azure AD Premium . Tenha em vigor processos e procedimentos para que os administradores de TI executem estes relatórios diariamente ou a pedido (geralmente num cenário de resposta a incidentes).

**Melhores práticas**: Tenha um sistema de monitorização ativo que o notifique dos riscos e possa ajustar o nível de risco (alto, médio ou baixo) às necessidades do seu negócio.   
**Detalhe**: Utilize [a Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md), que sinaliza os riscos atuais no seu próprio painel de instrumentos e envia notificações sumárias diárias por e-mail. Para ajudar a proteger as identidades da sua organização, pode configurar políticas baseadas no risco que respondam automaticamente a problemas detetados quando um nível de risco especificado é atingido.

As organizações que não monitorizam ativamente os seus sistemas de identidade correm o risco de ter as credenciais de utilizador comprometidas. Sem conhecimento de que atividades suspeitas estão a ocorrer através destas credenciais, as organizações não podem mitigar este tipo de ameaça.

## <a name="use-azure-ad-for-storage-authentication"></a>Utilize Azure AD para autenticação de armazenamento
[O Azure Storage](../../storage/common/storage-auth-aad.md) suporta a autenticação e autorização com Azure AD para armazenamento blob e armazenamento de fila. Com a autenticação Azure AD, pode utilizar o controlo de acesso baseado em funções Azure para conceder permissões específicas aos utilizadores, grupos e aplicações até ao âmbito de um recipiente ou fila de bolhas individuais.

Recomendamos que utilize [a Azure AD para autenticar o acesso ao armazenamento.](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)

## <a name="next-step"></a>Passo seguinte

Consulte as [melhores práticas e padrões](best-practices-and-patterns.md) de segurança da Azure para obter mais boas práticas de segurança quando estiver a desenhar, implementar e gerir as suas soluções em nuvem utilizando o Azure.