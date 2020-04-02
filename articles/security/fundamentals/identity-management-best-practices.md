---
title: Identidade azul & aceder às melhores práticas de segurança Microsoft Docs
description: Este artigo fornece um conjunto de boas práticas para a gestão de identidade e controlo de acesso utilizando capacidades Azure incorporadas.
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
ms.openlocfilehash: ffd9919092cdf2481767e58f10ba6525d56ca4a8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548464"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Melhores práticas de segurança de controlo de acesso e Gestão de Identidades do Azure

Neste artigo, discutimos uma coleção de práticas de gestão de identidade azure e de controlo de acesso. Estas boas práticas derivam da nossa experiência com a [Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) e das experiências de clientes como você.

Para cada melhor prática, explicamos:

* Qual é a melhor prática
* Por que quer ativar as melhores práticas
* Qual pode ser o resultado se não permitir as melhores práticas
* Possíveis alternativas às melhores práticas
* Como pode aprender a permitir as melhores práticas

Este artigo de gestão de identidade e controlo de acesso sintetiza as melhores práticas de segurança do controlo de identidade baseia-se numa opinião consensual e nas capacidades da plataforma Azure e nos conjuntos de funcionalidades, tal como existem no momento em que este artigo foi escrito.

A intenção de escrever este artigo é fornecer um roteiro geral para uma postura de segurança mais robusta após a implantação guiada pela nossa lista de verificação "[5 passos para garantir a sua infraestrutura de identidade](steps-secure-identity.md)", que o acompanha através de algumas das nossas principais funcionalidades e serviços.

As opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

As melhores práticas de gestão de identidade e controlo de acesso saem discutidas neste artigo:

* Trate a identidade como o perímetro de segurança primário
* Centralizar a gestão de identidade
* Gerir inquilinos conectados
* Ativar um único sinal
* Ativar acesso condicional
* Plano para melhorias de segurança de rotina
* Ativar a gestão de palavras-passe
* Impor verificação de vários fatores para os utilizadores
* Utilizar o controlo de acesso baseado em funções
* Menor exposição de contas privilegiadas
* Locais de controlo onde os recursos estão localizados
* Utilizar a AD Azure para autenticação de armazenamento

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Trate a identidade como o perímetro de segurança primário

Muitos consideram a identidade o perímetro principal para a segurança. Esta é uma mudança do foco tradicional na segurança da rede. Os perímetros de rede continuam a ficar mais porosos, e essa defesa do perímetro não pode ser tão eficaz como era antes da explosão de dispositivos [BYOD](https://aka.ms/byodcg) e aplicações em nuvem.

O [Azure Ative Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) é a solução Azure para a gestão de identidade e acesso. A Azure AD é um serviço multiarrendatário, baseado na nuvem e gestão de identidade da Microsoft. Combina serviços de diretório base, gestão de acesso a aplicações e proteção de identidade numa única solução.

As seguintes secções listam as melhores práticas de segurança de identidade e acesso utilizando a AD Azure.

**Boas práticas**: Controlos de segurança do centro e deteções em torno das identidades do utilizador e do serviço.
**Detalhe**: Utilize a AD Azure para colocalizar controlos e identidades.

## <a name="centralize-identity-management"></a>Centralizar a gestão de identidade

Num cenário de [identidade híbrida](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) recomendamos que integre as suas direções no local e na nuvem. A integração permite à sua equipa de TI gerir contas a partir de um local, independentemente do local onde uma conta é criada. A integração também ajuda os seus utilizadores a serem mais produtivos, fornecendo uma identidade comum para aceder tanto aos recursos em nuvem como no local.

**Boas práticas**: Estabeleça uma única instância azure AD. A consistência e uma única fonte autoritária aumentarão a clareza e reduzirão os riscos de segurança dos erros humanos e da complexidade da configuração.
**Detalhe**: Designe um único diretório da AD Azure como fonte autoritária para contas corporativas e organizacionais.

**Boas práticas**: Integre os seus diretórios no local com a Azure AD.  
**Detalhe**: Utilize [o Azure AD Connect](/azure/active-directory/connect/active-directory-aadconnect) para sincronizar o seu diretório no local com o seu diretório de nuvem.

> [!Note]
> Existem [fatores que afetam o desempenho do Azure AD Connect](../../active-directory/hybrid/plan-connect-performance-factors.md). Certifique-se de que o Azure AD Connect tem capacidade suficiente para impedir que os sistemas de baixo desempenho impeçam a segurança e a produtividade. Organizações grandes ou complexas (organizações que aprovisionam mais de 100.000 objetos) devem seguir as [recomendações](../../active-directory/hybrid/whatis-hybrid-identity.md) para otimizar a sua implementação do Azure AD Connect.

**Boas práticas**: Não sincronize contas a Azure AD que tenham privilégios elevados na sua instância de Diretório Ativo existente.
**Detalhe**: Não altere a configuração padrão do [Azure AD Connect](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) que filtra estas contas. Esta configuração atenua o risco de os adversários se dedicarem da nuvem aos ativos no local (o que pode criar um incidente grave).

**Boas práticas**: Ligue a sincronização de hash de senha.  
**Detalhe**: A sincronização de hash de palavra-passe é uma funcionalidade utilizada para sincronizar as hashes de palavra-passe do utilizador de uma instância de Diretório Ativo no local para uma instância azure ad baseada na nuvem. Esta sincronização ajuda a proteger contra as credenciais vazadas que estão a ser repetidas de ataques anteriores.

Mesmo que decida utilizar a federação com serviços da Federação de Diretórios Ativos (AD FS) ou outros fornecedores de identidade, pode configurar opcionalmente a sincronização de hash de palavra-passe como cópia de segurança caso os seus servidores no local falhem ou fiquem temporariamente indisponíveis. Esta sincronização permite que os utilizadores acedam ao serviço utilizando a mesma palavra-passe que utilizam para iniciar sessão na sua instância de Diretório Ativo no local. Também permite que a Proteção de Identidade detete credenciais comprometidas comparando hashes de senha sincronizada com senhas conhecidas por estarem comprometidas, se um utilizador tiver usado o mesmo endereço de e-mail e senha em outros serviços que não estejam ligados ao Azure AD.

Para mais informações, consulte A sincronização de hash de [palavra-passe implemente com sincronização Azure AD Connect](/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization).

**Boas práticas**: Para o desenvolvimento de novas aplicações, utilize o Azure AD para autenticação.
**Detalhe**: Utilize as capacidades corretas para suportar a autenticação:

  - Azure AD para funcionários
  - [Azure AD B2B](../../active-directory/b2b/index.yml) para utilizadores convidados e parceiros externos
  - [Azure AD B2C](../../active-directory-b2c/index.yml) para controlar como os clientes se inscrevem, se inscrevem e gerem os seus perfis quando utilizam as suas aplicações

As organizações que não integram a sua identidade no local com a sua identidade na nuvem podem ter mais despesas na gestão de contas. Isto aumenta a probabilidade de erros e falhas de segurança.

> [!Note]
> Você precisa escolher em que listas contas críticas residará e se a estação de trabalho de administração utilizada é gerida por novos serviços na nuvem ou processos existentes. A utilização de processos de gestão e de fornecimento de identidade existentes pode diminuir alguns riscos, mas também pode criar o risco de um intruso comprometer uma conta no local e girar para a nuvem. É melhor utilizar uma estratégia diferente para diferentes funções (por exemplo, administrações de TI vs. administrações da unidade de negócio). Tem duas opções. A primeira opção é criar Contas AD Azure que não estejam sincronizadas com a sua instância de Diretório Ativo no local. Junte-se à sua estação de trabalho de administração para a Azure AD, que pode gerir e corrigir utilizando o Microsoft Intune. A segunda opção é utilizar as contas de administração existentes sincronizando-se com a sua instância de Diretório Ativo no local. Utilize estações de trabalho existentes no seu domínio de Diretório Ativo para gestão e segurança.

## <a name="manage-connected-tenants"></a>Gerir inquilinos conectados
A sua organização de segurança precisa de visibilidade para avaliar o risco e determinar se as políticas da sua organização, e quaisquer requisitos regulamentares, estão a ser seguidos. Deve garantir que a sua organização de segurança tem visibilidade em todas as subscrições ligadas ao seu ambiente de produção e rede (via [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) ou [vpN site-to-site](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)). Um [Administrador/Administrador Global](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) da Empresa em Azure AD pode elevar o seu acesso à função de Administrador de Acesso ao [Utilizador](../../role-based-access-control/built-in-roles.md#user-access-administrator) e ver todas as subscrições e grupos geridos ligados ao seu ambiente.

Consulte o [acesso elevado para gerir todas as subscrições e grupos de gestão do Azure](../../role-based-access-control/elevate-access-global-admin.md) para garantir que você e o seu grupo de segurança possam ver todas as subscrições ou grupos de gestão ligados ao seu ambiente. Deve remover este acesso elevado depois de ter avaliado os riscos.

## <a name="enable-single-sign-on"></a>Ativar um único sinal

Num mundo mobile-first, em primeira nuvem, você quer permitir um único sinal (SSO) para dispositivos, apps e serviços de qualquer lugar para que os seus utilizadores possam ser produtivos onde e quando quiser. Quando se tem múltiplas soluções de identidade para gerir, este torna-se um problema administrativo não só para ti, mas também para os utilizadores que têm de se lembrar de várias palavras-passe.

Ao utilizar a mesma solução de identidade para todas as suas apps e recursos, pode alcançar o SSO. E os seus utilizadores podem usar o mesmo conjunto de credenciais para iniciar sessão e aceder aos recursos de que necessitam, quer os recursos estejam localizados no local ou na nuvem.

**Boas práticas**: Habilitar sSo.  
**Detalhe**: Azure AD [estende-se no local Diretório Ativo](/azure/active-directory/connect/active-directory-aadconnect) à nuvem. Os utilizadores podem utilizar o seu trabalho primário ou a sua conta escolar para os seus dispositivos de domínio, recursos da empresa e todas as aplicações web e SaaS que precisam para fazer os seus trabalhos. Os utilizadores não têm de se lembrar de vários conjuntos de nomes de utilizadores e palavras-passe, e o seu acesso à aplicação pode ser automaticamente provisionado (ou desprovisionado) com base nos seus membros do grupo de organização e no seu estatuto de funcionário. E também pode controlar esse acesso para aplicações da galeria ou para as suas próprias aplicações no local que tenha desenvolvido e publicado através do [Proxy de Aplicações do AD](/azure/active-directory/active-directory-application-proxy-get-started).

Utilize o SSO para permitir aos utilizadores acederem às suas [aplicações SaaS](/azure/active-directory/active-directory-appssoaccess-whatis) com base no seu trabalho ou conta escolar em Azure AD. Isto aplica-se não só às aplicações do Microsoft SaaS, mas também a outras aplicações, como [o Google Apps](/azure/active-directory/active-directory-saas-google-apps-tutorial) e o [Salesforce.](/azure/active-directory/active-directory-saas-salesforce-tutorial) Pode configurar a sua aplicação para utilizar o Azure AD como fornecedor de [identidade baseado em SAML.](/azure/active-directory/fundamentals-identity) Como controlo de segurança, a Azure AD não emite um token que permita aos utilizadores iniciarem o contrato de aplicação a menos que lhes tenha sido concedido acesso através da AD Azure. Pode conceder acesso diretamente, ou através de um grupo do que os utilizadores são membros.

As organizações que não criam uma identidade comum para estabelecer SSO para os seus utilizadores e aplicações estão mais expostas a cenários em que os utilizadores têm várias palavras-passe. Estes cenários aumentam a probabilidade de os utilizadores reutilizarem palavras-passe ou usarem palavras-passe fracas.

## <a name="turn-on-conditional-access"></a>Ativar acesso condicional

Os utilizadores podem aceder aos recursos da sua organização utilizando uma variedade de dispositivos e aplicações de qualquer lugar. Como administrador de TI, pretende certificar-se de que estes dispositivos cumprem os seus padrões de segurança e conformidade. Concentrar-se em quem pode aceder a um recurso já não é suficiente.

Para equilibrar a segurança e a produtividade, é preciso pensar em como um recurso é acedido antes de poder tomar uma decisão sobre o controlo de acesso. Com acesso condicional Azure AD, pode responder a este requisito. Com acesso condicional, pode tomar decisões automatizadas de controlo de acesso com base em condições de acesso às suas aplicações na nuvem.

**Boas práticas**: Gerir e controlar o acesso aos recursos corporativos.  
**Detalhe**: Configure Azure AD [Acesso Condicional](/azure/active-directory/active-directory-conditional-access-azure-portal) com base num grupo, localização e sensibilidade de aplicação para aplicações SaaS e aplicações ligadas a AD Azure.

**Boas práticas**: Bloqueie protocolos de autenticação do legado.
**Detalhe**: Os atacantes exploram fraquezas em protocolos mais antigos todos os dias, especialmente para ataques com spray de palavra-passe. Configure acesso condicional para bloquear protocolos legados. Veja o vídeo [Azure AD: Do's e Don'ts](https://www.youtube.com/watch?v=wGk0J4z90GI) para mais informações.

## <a name="plan-for-routine-security-improvements"></a>Plano para melhorias de segurança de rotina

A segurança está sempre a evoluir, e é importante construir no seu quadro de cloud e gestão de identidade uma forma de mostrar regularmente crescimento e descobrir novas formas de proteger o seu ambiente.

Identity Secure Score é um conjunto de controlos de segurança recomendados que a Microsoft publica que trabalha para lhe fornecer uma pontuação numérica para medir objectivamente a sua postura de segurança e ajudar a planear futuras melhorias de segurança. Também pode ver a sua pontuação em comparação com as de outras indústrias, bem como as suas próprias tendências ao longo do tempo.

**Boas práticas**: Planeie revisões de segurança de rotina e melhorias baseadas nas melhores práticas do seu setor.
**Detalhe**: Utilize a função Identity Secure Score para classificar as suas melhorias ao longo do tempo.

## <a name="enable-password-management"></a>Ativar a gestão de palavras-passe

Se tem vários inquilinos ou pretende permitir que os utilizadores [reporem as suas próprias palavras-passe,](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md)é importante que utilize políticas de segurança adequadas para prevenir abusos.

**Boas práticas**: Configurar o reset de palavra-passe self-service (SSPR) para os seus utilizadores.  
**Detalhe**: Utilize a função de reset de [palavra-passe autosserviço](/azure/active-directory-b2c/active-directory-b2c-reference-sspr) Azure AD.

**Boas práticas**: Monitore como ou se o SSPR está realmente a ser utilizado.  
**Detalhe**: Monitorize os utilizadores que se registam utilizando o relatório de atividade de [redefinição da palavra-passe](/azure/active-directory/active-directory-passwords-get-insights)Azure AD . A funcionalidade de reporte que a Azure AD fornece ajuda-o a responder a perguntas utilizando relatórios pré-construídos. Se você estiver devidamente licenciado, você também pode criar consultas personalizadas.

**Boas práticas**: Alargar as políticas de senha baseadas na nuvem à sua infraestrutura no local.
**Detalhe**: Melhore as políticas de password na sua organização, executando as mesmas verificações para alterações de palavra-passe no local como para alterações de palavra-passe baseadas na nuvem. Instale a [proteção de passwords Azure AD](/azure/active-directory/authentication/concept-password-ban-bad) para os agentes de Diretório Ativo do Windows Server no local para alargar as listas de senhas proibidas à sua infraestrutura existente. Os utilizadores e administradores que alterem, definirem ou reporem senhas no local são obrigados a cumprir a mesma política de senhas que os utilizadores apenas na nuvem.

## <a name="enforce-multi-factor-verification-for-users"></a>Impor verificação de vários fatores para os utilizadores

Recomendamos que necessite de verificação em duas etapas para todos os seus utilizadores. Isto inclui administradores e outros da sua organização que podem ter um impacto significativo se a sua conta estiver comprometida (por exemplo, funcionários financeiros).

Existem várias opções para exigir verificação em duas etapas. A melhor opção para si depende dos seus objetivos, da edição AD Azure que está a executar e do seu programa de licenciamento. Consulte [como exigir uma verificação em duas etapas para que um utilizador](/azure/active-directory/authentication/howto-mfa-userstates) determine a melhor opção para si. Consulte as páginas de preços de [autenticação multifactor](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) [Azure](https://azure.microsoft.com/pricing/details/active-directory/) e Azure Para obter mais informações sobre licenças e preços.

Seguem-se opções e benefícios para permitir a verificação em duas etapas:

**Opção 1**: Ativar o MFA para todos os utilizadores e métodos de login com o **Benefício**de Falhas de Segurança AD Azure : Esta opção permite-lhe aplicar facilmente e rapidamente o MFA para todos os utilizadores do seu ambiente com uma política rigorosa para:

* Impugnar contas administrativas e mecanismos de logon administrativos
* Requerer desafio MFA via Microsoft Authenticator para todos os utilizadores
* Restringir os protocolos de autenticação do legado.

Este método está disponível para todos os níveis de licenciamento, mas não é capaz de ser misturado com as políticas de Acesso Condicional existentes. Pode encontrar mais informações em incumprimentos de segurança da AD Azure

**Opção 2**: Ativar a [autenticação de vários fatores alterando](../../active-directory/authentication/howto-mfa-userstates.md)o estado do utilizador .   
**Benefício**: Este é o método tradicional para exigir uma verificação em duas etapas. Funciona com a [autenticação de multi-factors Azure na nuvem e com o servidor de autenticação multi-factor Azure.](/azure/active-directory/authentication/concept-mfa-whichversion) A utilização deste método requer que os utilizadores realizem uma verificação em duas etapas sempre que entram e substituem as políticas de Acesso Condicional.

Para determinar onde é que a autenticação multi-factor precisa de ser ativada, veja [qual a versão do Azure MFA é a certa para](/azure/active-directory/authentication/concept-mfa-whichversion)a minha organização?

**Opção 3**: [Ativar a autenticação de vários fatores com a política de acesso condicional.](/azure/active-directory/authentication/howto-mfa-getstarted)
**Benefício**: Esta opção permite-lhe solicitar uma verificação em duas etapas em condições específicas utilizando o [Acesso Condicional](/azure/active-directory/active-directory-conditional-access-azure-portal). Condições específicas podem ser o utilizador de sessão a partir de diferentes locais, dispositivos não confiáveis ou aplicações que considere arriscadas. Definir condições específicas em que necessita de verificação em duas etapas permite evitar uma constante solicitação para os seus utilizadores, o que pode ser uma experiência desagradável do utilizador.

Esta é a forma mais flexível de permitir a verificação em duas etapas para os seus utilizadores. Ativar uma política de acesso condicional funciona apenas para a autenticação de multi-factores Azure na nuvem e é uma característica premium da Azure AD. Pode encontrar mais informações sobre este método na implementação da [autenticação azure multi-factor baseada na nuvem.](/azure/active-directory/authentication/howto-mfa-getstarted)

**Opção 4**: Ativar a autenticação multi-factor com políticas de acesso condicional, avaliando o risco de utilizador e de entrada de identidade [azure AD](/azure/active-directory/authentication/tutorial-risk-based-sspr-mfa).   
**Benefício**: Esta opção permite-lhe:

* Detete potenciais vulnerabilidades que afetam as identidades da sua organização.
* Configure respostas automatizadas a ações suspeitas detetadas que estejam relacionadas com as identidades da sua organização.
* Investigue incidentes suspeitos e tome as medidas apropriadas para resolvê-los.

Este método utiliza a avaliação de risco de proteção de identidade Azure AD para determinar se é necessária uma verificação em duas etapas com base no risco de utilizador e de entrada para todas as aplicações na nuvem. Este método requer licenciamento Azure Ative Directory P2. Pode encontrar mais informações sobre este método na Proteção de Identidade do [Diretório Ativo azure](/azure/active-directory/identity-protection/overview).

> [!Note]
> Opção 1, permitindo a autenticação multi-factor alterando o estado do utilizador, substitui as políticas de Acesso Condicional. Uma vez que as opções 2 e 3 utilizam as políticas de Acesso Condicional, não pode utilizar a opção 1 com elas.

Organizações que não adicionam camadas extras de proteção de identidade, como a verificação em duas etapas, são mais suscetíveis a ataques de roubo de credenciais. Um ataque de roubo credencial pode levar a um compromisso de dados.

## <a name="use-role-based-access-control"></a>Utilizar o controlo de acesso baseado em funções

A gestão de acesso sinuoso para recursos na nuvem é fundamental para qualquer organização que utilize a nuvem. O [controlo de acesso baseado em papéis (RBAC) ajuda-o](/azure/role-based-access-control/overview)a gerir quem tem acesso aos recursos do Azure, o que podem fazer com esses recursos e quais as áreas a que têm acesso.

Designar grupos ou funções individuais responsáveis por funções específicas no Azure ajuda a evitar confusões que podem levar a erros humanos e de automação que criam riscos de segurança. Restringir o acesso com base na [necessidade de conhecer](https://en.wikipedia.org/wiki/Need_to_know) e menos [privilegiar](https://en.wikipedia.org/wiki/Principle_of_least_privilege) os princípios de segurança é imperativo para as organizações que querem impor políticas de segurança para o acesso de dados.

A sua equipa de segurança precisa de visibilidade nos seus recursos Azure para avaliar e remediar o risco. Se a equipa de segurança tem responsabilidades operacionais, precisam de permissões adicionais para fazer o seu trabalho.

Pode utilizar o [RBAC](/azure/role-based-access-control/overview) para atribuir permissões a utilizadores, grupos e aplicações num determinado âmbito. O âmbito da atribuição de uma função pode ser uma subscrição, um grupo de recursos ou um único recurso.

**Boas práticas**: Segregar os deveres dentro da sua equipa e conceder apenas a quantidade de acesso aos utilizadores de que necessitam para desempenhar em seu trabalho. Em vez de dar permissões sem restrições a todos na sua subscrição ou recursos Do Azure, permita apenas certas ações num determinado âmbito.
**Detalhe**: Utilize [funções RBAC incorporadas](/azure/role-based-access-control/built-in-roles) em Azure para atribuir privilégios aos utilizadores.

> [!Note]
> Permissões específicas criam complexidade e confusão desnecessárias, acumulando-se numa configuração "legado" que é difícil de corrigir sem medo de quebrar algo. Evite permissões específicas de recursos. Em vez disso, utilize grupos de gestão para permissões e grupos de recursos em toda a empresa para obter permissões dentro de subscrições. Evite permissões específicas do utilizador. Em vez disso, atribua acesso a grupos no Azure AD.

**Boas práticas**: Conceder equipas de segurança com responsabilidades Azure acesso para ver os recursos do Azure para que possam avaliar e remediar o risco.
**Detalhe**: Grant equipas de segurança o papel rBAC [Security Reader.](/azure/role-based-access-control/built-in-roles#security-reader) Pode utilizar o grupo de gestão de raízes ou o grupo de gestão de segmentos, dependendo do âmbito de responsabilidades:

* **Grupo de gestão de raiz** para equipas responsáveis por todos os recursos empresariais
* **Grupo de gestão** de segmentos para equipas com âmbito limitado (geralmente devido a limites regulamentares ou outros organizacionais)

**Boas práticas**: Conceda as permissões adequadas às equipas de segurança que tenham responsabilidades operacionais diretas.
**Pormenor**: Reveja as funções integradas do RBAC para a atribuição de funções apropriadas. Se as funções incorporadas não atenderem às necessidades específicas da sua organização, pode criar [papéis personalizados para os recursos Azure.](/azure/role-based-access-control/custom-roles) Tal como acontece com as funções incorporadas, pode atribuir funções personalizadas a utilizadores, grupos e diretores de serviços em subscrição, grupo de recursos e âmbitos de recursos.

**Boas práticas**: Grant Azure Security Center acesso a funções de segurança que dela necessitem. O Centro de Segurança permite que as equipas de segurança identifiquem e remediam rapidamente os riscos.
**Detalhe**: Adicione as equipas de segurança com estas necessidades ao papel de [Administrador de Segurança](/azure/role-based-access-control/built-in-roles#security-admin) RBAC para que possam ver políticas de segurança, ver estados de segurança, editar políticas de segurança, ver alertas e recomendações, e rejeitar alertas e recomendações. Pode fazê-lo utilizando o grupo de gestão de raiz ou o grupo de gestão de segmentos, dependendo do âmbito de responsabilidades.

Organizações que não impõem o controlo de acesso de dados utilizando capacidades como o RBAC podem estar a dar mais privilégios do que o necessário aos seus utilizadores. Isto pode levar ao compromisso de dados, permitindo que os utilizadores acedam a tipos de dados (por exemplo, de alto impacto empresarial) que não deveriam ter.

## <a name="lower-exposure-of-privileged-accounts"></a>Menor exposição de contas privilegiadas

Garantir o acesso privilegiado é um primeiro passo crítico para proteger os ativos empresariais. Minimizar o número de pessoas que têm acesso a informações ou recursos seguros reduz a possibilidade de um utilizador malicioso ter acesso, ou de um utilizador autorizado afetar inadvertidamente um recurso sensível.

Contas privilegiadas são contas que administram e gerem sistemas informáticos. Os ciber-atacantes têm como alvo estas contas para terem acesso aos dados e sistemas de uma organização. Para garantir um acesso privilegiado, deve isolar as contas e sistemas do risco de ser exposto a um utilizador mal-intencionado.

Recomendamos que desenvolva e siga um roteiro para garantir o acesso privilegiado contra ciberataques. Para obter informações sobre a criação de um roteiro detalhado para garantir identidades e acessos que sejam geridos ou reportados em Azure AD, Microsoft Azure, Office 365 e outros serviços na nuvem, reveja [o acesso privilegiado para implementações híbridas e cloud em Azure AD.](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

O seguinte resume as melhores práticas encontradas na Garantia de [acesso privilegiado para implantações híbridas e em nuvem em Azure AD:](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Boas práticas**: Gerir, controlar e monitorizar o acesso a contas privilegiadas.   
**Detalhe**: Ligue a [Azure AD Privileged Identity Management](/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access). Depois de ativar a Gestão de Identidade Privilegiada, receberá mensagens de correio eletrónico de notificação para alterações de funções de acesso privilegiadas. Estas notificações fornecem um alerta precoce quando os utilizadores adicionais são adicionados a papéis altamente privilegiados no seu diretório.

**Boas práticas**: Certifique-se de que todas as contas de administração críticas são geridas contas Azure AD.
**Detalhe**: Remova quaisquer contas de consumo de funções críticas de administração (por exemplo, contas da Microsoft como hotmail.com, live.com e outlook.com).

**Boas práticas**: Garantir que todas as funções críticas de administração tenham uma conta separada para tarefas administrativas, a fim de evitar phishing e outros ataques para comprometer privilégios administrativos.
**Detalhe**: Criar uma conta administrativa separada que tenha atribuído os privilégios necessários para executar as tarefas administrativas. Bloqueie o uso destas contas administrativas para ferramentas diárias de produtividade como o Microsoft Office 365 e-mail ou a navegação arbitrária na Web.

**Boas práticas**: Identificar e categorizar contas que se encontram em papéis altamente privilegiados.   
**Detalhe**: Depois de ligar a Azure AD Privileged Identity Management, consulte os utilizadores que se encontram no administrador global, administrador de funções privilegiadas e outros papéis altamente privilegiados. Remova quaisquer contas que já não sejam necessárias nessas funções e categorize as restantes contas atribuídas às funções de administrador:

* Atribuído individualmente a utilizadores administrativos, e pode ser utilizado para fins não administrativos (por exemplo, e-mail pessoal)
* Individualmente atribuído aos utilizadores administrativos e designado apenas para fins administrativos
* Partilhado em vários utilizadores
* Para cenários de acesso de emergência
* Para scripts automatizados
* Para utilizadores externos

**Boas práticas**: Implementar o acesso "mesmo a tempo" (JIT) para reduzir ainda mais o tempo de exposição dos privilégios e aumentar a sua visibilidade para o uso de contas privilegiadas.   
**Detalhe**: Azure AD Privileged Identity Management permite-lhe:

* Limitar os utilizadores a assumira apenas os seus privilégios JIT.
* Atribuir funções por uma duração reduzida com confiança de que os privilégios são revogados automaticamente.

**Boas práticas**: Defina pelo menos duas contas de acesso de emergência.   
**Detalhe**: As contas de acesso de emergência ajudam as organizações a restringir o acesso privilegiado num ambiente de Diretório Ativo Azure existente. Estas contas são altamente privilegiadas e não são atribuídas a indivíduos específicos. As contas de acesso de emergência limitam-se a cenários em que as contas administrativas normais não podem ser utilizadas. As organizações devem limitar o uso da conta de emergência apenas ao tempo necessário.

Avalie as contas atribuídas ou elegíveis para o papel de administradora global. Se não vir contas apenas em nuvem `*.onmicrosoft.com` utilizando o domínio (destinado a acesso de emergência), crie-as. Para mais informações, consulte a Gestão de contas administrativas de [acesso de emergência em Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access).

**Boas práticas**: Tenha um processo de "quebrar vidros" em caso de emergência.
**Detalhe**: Siga os passos de segurança no [acesso privilegiado para implantações híbridas e em nuvem em Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure).

**Boas práticas**: Exigir que todas as contas críticas de administração sejam sem palavra-passe (preferida) ou exijam a autenticação multi-factor.
**Detalhe**: Utilize a [aplicação Microsoft Authenticator](/azure/active-directory/authentication/howto-authentication-phone-sign-in) para iniciar sessão em qualquer conta DaD Azure sem utilizar uma palavra-passe. Tal como o [Windows Hello for Business,](/windows/security/identity-protection/hello-for-business/hello-identity-verification)o Autenticador Microsoft utiliza a autenticação baseada em chaves para permitir uma credencial do utilizador que está ligada a um dispositivo e utiliza autenticação biométrica ou PIN.

Exija a autenticação multi-factor Azure no início do registo para todos os utilizadores individuais que estejam permanentemente atribuídos a uma ou mais funções de administrador ada do Azure: Administrador Global, Administrador de Funções Privilegiadas, Administrador Online de Intercâmbio e Administrador Online SharePoint. Ative a [Autenticação de Vários Fatores para as suas contas de administração e certifique-se](/azure/active-directory/authentication/howto-mfa-userstates) de que os utilizadores de contas de administração se registaram.

**Boas práticas**: Para contas de administração crítica, tenha uma estação de trabalho de administração onde não são permitidas tarefas de produção (por exemplo, navegação e e-mail). Isto protegerá as suas contas de administração de vetores de ataque que usam navegação e e-mail e reduzem significativamente o risco de um incidente grave.
**Detalhe**: Utilize uma estação de trabalho de administração. Escolha um nível de segurança da estação de trabalho:

- Dispositivos de produtividade altamente seguros fornecem segurança avançada para navegação e outras tarefas de produtividade.
- As Estações de Trabalho de [Acesso Privilegiado (PAWs)](/windows-server/identity/securing-privileged-access/privileged-access-workstations) fornecem um sistema operativo dedicado que está protegido contra ataques de internet e vetores de ameaças para tarefas sensíveis.

**Boas práticas**: Desprovisionamento contas de administrador quando os funcionários deixam a sua organização.
**Detalhe**: Tenha em curso um processo que desative ou apague as contas de administração quando os funcionários saem da sua organização.

**Boas práticas**: Teste regularmente as contas de administração utilizando técnicas de ataque atuais.
**Detalhe**: Use o Office 365 Attack Simulator ou uma oferta de terceiros para executar cenários de ataque realistas na sua organização. Isto pode ajudá-lo a encontrar utilizadores vulneráveis antes que ocorra um ataque real.

**Boas práticas**: Tome medidas para mitigar as técnicas atacadas mais usadas.  
**Detalhe**: [Identificar as contas da Microsoft em funções administrativas que precisam de ser transferidas para contas](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts) de trabalho ou escolares  

[Garantir contas separadas de utilizadores e reencaminhamento de correio para contas de administrador global](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)  

[Certifique-se de que as palavras-passe das contas administrativas mudaram recentemente](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Ligue a sincronização de hash de senha](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)  

[Exigir autenticação multi-factor para os utilizadores em todas as funções privilegiadas, bem como utilizadores expostos](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Obtenha o seu Office 365 Secure Score (se utilizar o Office 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#obtain-your-office-365-secure-score-if-using-office-365)  

[Reveja a orientação de segurança e conformidade do Office 365 (se utilizar o Office 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Configure Office 365 Activity Monitoring (se utilizar o Office 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#configure-office-365-activity-monitoring-if-using-office-365)  

[Estabelecer os proprietários de planos de resposta a incidentes/emergências](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#establish-incidentemergency-response-plan-owners)  

[Contas administrativas privilegiadas no local](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)

Se não conseguir um acesso privilegiado, poderá descobrir que tem demasiados utilizadores em papéis altamente privilegiados e está mais vulnerável a ataques. Atores maliciosos, incluindo ciberataques, muitas vezes visam contas de administração e outros elementos de acesso privilegiado para ter acesso a dados e sistemas sensíveis usando roubo de credenciais.

## <a name="control-locations-where-resources-are-created"></a>Locais de controlo onde os recursos são criados

Permitir que os operadores de nuvem realizem tarefas, evitando-os de quebrar convenções que são necessárias para gerir os recursos da sua organização é muito importante. As organizações que querem controlar os locais onde os recursos são criados devem codificar duramente estes locais.

Pode utilizar o Gestor de [Recursos Azure](/azure/azure-resource-manager/resource-group-overview) para criar políticas de segurança cujas definições descrevem as ações ou recursos que são especificamente negados. Atribui essas definições de política no âmbito pretendido, como a subscrição, o grupo de recursos ou um recurso individual.

> [!NOTE]
> As políticas de segurança não são as mesmas que o RBAC. Na verdade, utilizam o RBAC para autorizar os utilizadores a criar esses recursos.
>
>

As organizações que não controlam a forma como os recursos são criados são mais suscetíveis aos utilizadores que possam abusar do serviço criando mais recursos do que precisam. Endurecer o processo de criação de recursos é um passo importante para garantir um cenário multiarrendatário.

## <a name="actively-monitor-for-suspicious-activities"></a>Monitorizar ativamente para atividades suspeitas

Um sistema de monitorização de identidade ativa pode detetar rapidamente comportamentos suspeitos e desencadear um alerta para uma investigação mais aprofundada. A tabela seguinte lista duas capacidades ad-ad-azures que podem ajudar as organizações a monitorizar as suas identidades:

**Boas práticas**: Tenha um método para identificar:

- Tenta iniciar [sessão sem ser rastreado.](/azure/active-directory/active-directory-reporting-sign-ins-from-unknown-sources)
- [Ataques](/azure/active-directory/active-directory-reporting-sign-ins-after-multiple-failures) de força bruta contra um determinado relato.
- Tenta entrar em vários locais.
- Inscrições a partir de [dispositivos infetados](/azure/active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices).
- Endereços IP suspeitos.

**Detalhe**: Utilize [relatórios](/azure/active-directory/active-directory-view-access-usage-reports)de anomalia Saque AD Premium . Dispomos de processos e procedimentos para que os administradores de TI executem estes relatórios diariamente ou a pedido (geralmente num cenário de resposta a incidentes).

**Boas práticas**: Disponha de um sistema de monitorização ativo que o notifique dos riscos e possa ajustar o nível de risco (alto, médio ou baixo) aos seus requisitos de negócio.   
**Detalhe**: Utilize a [Proteção de Identidade Azure AD,](/azure/active-directory/active-directory-identityprotection)que sinaliza os riscos atuais no seu próprio painel de instrumentos e envia notificações sumárias diárias por e-mail. Para ajudar a proteger as identidades da sua organização, pode configurar políticas baseadas no risco que respondem automaticamente a problemas detetados quando um nível de risco especificado é atingido.

As organizações que não monitorizam ativamente os seus sistemas de identidade correm o risco de comprometer as credenciais dos utilizadores. Sem saber que atividades suspeitas estão a ocorrer através destas credenciais, as organizações não podem atenuar este tipo de ameaça.

## <a name="use-azure-ad-for-storage-authentication"></a>Utilizar a AD Azure para autenticação de armazenamento
[O Azure Storage](/azure/storage/common/storage-auth-aad) suporta a autenticação e autorização com a Azure AD para armazenamento blob e armazenamento de fila. Com a autenticação Azure AD, pode utilizar o controlo de acesso baseado em funções azure para conceder permissões específicas a utilizadores, grupos e aplicações até ao âmbito de um recipiente ou fila individual.

Recomendamos que utilize [a AD Azure para autenticar o acesso ao armazenamento.](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)

## <a name="next-step"></a>Passo seguinte

Consulte as [melhores práticas e padrões](best-practices-and-patterns.md) de segurança azure para obter mais práticas de segurança para usar quando está a projetar, implementar e gerir as suas soluções em nuvem utilizando o Azure.
