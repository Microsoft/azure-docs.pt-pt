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
ms.date: 09/17/2018
ms.author: barclayn
ms.openlocfilehash: f872c61ad0597d2307cd244668fdfc258f7a45cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611256"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Melhores práticas de segurança de controlo de acesso e gestão de identidades do Azure

Muitos consideram a identidade para a nova camada de limites para segurança, assumindo essa função do ponto de vista centrado em rede tradicional. Esta evolução do eixo principal para a atenção de segurança e os investimentos vêm do fato de que se tornaram cada vez mais porosos perímetros de rede e essa defesa do perímetro não pode ser tão eficaz quanto eles vez foram antes da explosão de [BYOD ](https://aka.ms/byodcg) dispositivos e aplicações na cloud.

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
* Ativar o início de sessão único
* Ativar o acesso condicional
* Ativar a gestão de palavras-passe
* Impor verificação multifator para utilizadores
* Utilizar o controlo de acesso baseado em funções
* Exposição inferior de contas com privilégios
* Controlar as localizações onde se encontram os recursos

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Tratar a identidade como perímetro segurança principal

Muitos consideram a identidade a ser o primário perímetro de segurança. Essa é uma mudança do tradicional foco na segurança de rede. Perímetros de rede continuar a receber mais porosos, e essa defesa do perímetro não pode ser tão eficaz que eram anteriormente a explosão de [BYOD](https://aka.ms/byodcg) dispositivos e aplicações na cloud.
[Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) é a solução do Azure para gestão de identidades e acesso. Azure AD é um multi-inquilino, com base na cloud diretório e identidade do serviço de gestão da Microsoft. Combina serviços de diretório centrais, gestão de acesso de aplicações e proteção de identidade numa única solução.

As secções seguintes listam as práticas recomendadas para segurança de acesso e identidades com o Azure AD.

## <a name="centralize-identity-management"></a>Centralize a gestão de identidade

Num [identidade híbrida](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) cenário, recomendamos que integre no local e diretórios da cloud. Integração permite que a sua equipa de TI gerir contas de uma localização única, independentemente de onde é criada uma conta. Integração também ajuda os usuários sejam mais produtivos ao fornecer uma identidade comum para acederem a recursos na cloud e no local.


**Melhor prática**: Integre os seus diretórios no local com o Azure AD.  
**Detalhe**: Uso [do Azure AD Connect](../active-directory/connect/active-directory-aadconnect.md) para sincronizar o seu diretório no local com o diretório na cloud.

**Melhor prática**: Ative a sincronização de hash de palavra-passe.  
**Detalhe**: Sincronização de hash de palavra-passe é uma funcionalidade utilizada para sincronizar os hashes de hashes de palavra-passe de utilizador de uma instância do Active Directory no local para um Azure baseado na nuvem de instância do AD.

Mesmo se decidir usar a Federação com os serviços de Federação do Active Directory (AD FS) ou de outros fornecedores de identidade, pode, opcionalmente, configurar sincronização de hash de palavra-passe como uma cópia de segurança em caso de seus servidores no local falharam ou ficam temporariamente indisponíveis. Isto permite aos utilizadores iniciar sessão serviço ao utilizar a mesma palavra-passe utilizam para iniciar sessão sua instância do Active Directory no local. Ela também permite a proteção de identidade detetar credenciais comprometidas, ao comparar os hashes de palavra-passe com palavras-passe conhecidas para ficar comprometida, se um utilizador tiver utilizado o mesmo endereço de e-mail e palavra-passe noutros serviços não ligada ao Azure AD.

Para obter mais informações, consulte [implementar a sincronização de hash de palavra-passe com o Azure AD Connect sync](../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).

As organizações que não integram a sua identidade no local com a respetiva identidade de nuvem podem ter mais sobrecarga no gerenciamento de contas. Essa sobrecarga aumenta a probabilidade de erros e falhas de segurança.

## <a name="enable-single-sign-on"></a>Ativar o início de sessão único

Num mundo de dispositivos móveis e da cloud, que pretende ativar início de sessão único (SSO) para dispositivos, aplicações e serviços de qualquer lugar para que os utilizadores possam ser produtivos onde quer e sempre que. Quando tiver várias soluções de identidade para gerir, isso se torna um problema administrativo não só para IT, mas também para os utilizadores que têm de se lembrar de várias palavras-passe.

Ao utilizar a mesma solução de identidade para todas as suas aplicações e recursos, pode conseguir SSO. E os utilizadores podem utilizar o mesmo conjunto de credenciais para iniciar sessão e aceder aos recursos que precisam, independentemente dos recursos estão localizados no local ou na cloud.

**Melhor prática**: Ative o SSO.  
**Detalhe**: O Azure AD [expande o Active Directory no local](../active-directory/connect/active-directory-aadconnect.md) para a cloud. Os utilizadores podem utilizar a sua conta escolar ou profissional de principal para seus dispositivos associados a domínios, recursos da empresa e todas as web e aplicações SaaS que precisam para realizar seu trabalho. Os utilizadores não têm não se esqueça de vários conjuntos de nomes de utilizador e palavras-passe e o acesso de aplicativo pode ser automaticamente aprovisionado (ou desprovisionada) com base em suas associações de grupo da organização e o respetivo estado como um funcionário. E também pode controlar esse acesso para aplicações da galeria ou para as suas próprias aplicações no local que tenha desenvolvido e publicado através do [Proxy de Aplicações do AD](../active-directory/active-directory-application-proxy-get-started.md).

Utilizar o SSO para permitir que os utilizadores aceder aos respetivos [aplicações SaaS](../active-directory/active-directory-appssoaccess-whatis.md) com base na respetiva conta profissional ou escolar no Azure AD. Isto é aplicável não só para aplicações SaaS da Microsoft, mas também outras aplicações, tal como [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) e [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md). Pode configurar a sua aplicação para utilizar o Azure AD como um [identidade baseada no SAML](../active-directory/fundamentals-identity.md) fornecedor. Como um controle de segurança do Azure AD não emite um token que permite aos utilizadores iniciar sessão na aplicação, a menos que tenham sido concedidos acesso através do Azure AD. Pode conceder acesso diretamente ou através de um grupo que os utilizadores são membros.

As organizações que não criam uma identidade comum para estabelecer o SSO para seus usuários e aplicativos são mais expostas a cenários em que os utilizadores têm várias palavras-passe. Estes cenários aumentam a probabilidade dos utilizadores a reutilização de palavras-passe ou utilizar palavras-passe fracas.

## <a name="turn-on-conditional-access"></a>Ativar o acesso condicional

Os utilizadores podem aceder a recursos da sua organização ao utilizar uma variedade de dispositivos e aplicações em qualquer lugar. Como um administrador de TI, pretende certificar-se de que estes dispositivos cumpram as normas de segurança e conformidade. Se concentrar apenas na que pode aceder a um recurso não é suficiente mais.

Para balancear a segurança e produtividade, precisa pensar como um recurso é acedido antes de poder fazer uma decisão de controlo de acesso. Com o acesso condicional do Azure AD, pode solucionar este requisito. Com acesso condicional, pode tomar decisões de controlo de acesso automatizado para aceder às suas aplicações na cloud que se baseiam em condições.

**Melhor prática**: Gerir e controlar o acesso aos recursos empresariais.  
**Detalhe**: Configurar o Azure AD [acesso condicional](../active-directory/active-directory-conditional-access-azure-portal.md) com base na sensibilidade das aplicações para aplicações SaaS e aplicações do Azure AD – ligado, a localização e grupo.

## <a name="enable-password-management"></a>Ativar a gestão de palavras-passe

Se tiver vários inquilinos ou se pretender permitir que os utilizadores [repor a palavra-passe](../active-directory/active-directory-passwords-update-your-own-password.md), é importante que use as políticas de segurança apropriado para evitar abusos.

**Melhor prática**: Configure a reposição de palavra-passe self-service (SSPR) para os seus utilizadores.  
**Detalhe**: Utilizar o Azure AD [reposição de palavra-passe self-service](../active-directory-b2c/active-directory-b2c-reference-sspr.md) funcionalidade.

**Melhor prática**: Monitor como ou se realmente está a ser utilizada a SSPR.  
**Detalhe**: Monitorize os utilizadores quem estão a registar utilizando o Azure AD [relatório de atividade de registo de reposição de palavra-passe](../active-directory/active-directory-passwords-get-insights.md). A funcionalidade de relatórios que o Azure AD fornece ajuda a responder a perguntas através de relatórios criados previamente. Se está corretamente licenciado, também pode criar consultas personalizadas.

## <a name="enforce-multi-factor-verification-for-users"></a>Impor verificação multifator para utilizadores

Recomendamos que exigem a verificação de dois passos para todos os seus utilizadores. Isto inclui os administradores e outros utilizadores na sua organização que pode ter um impacto significativo se a conta for comprometida (por exemplo, responsáveis financeiros).

Existem várias opções para exigir a verificação de dois passos. A melhor opção para depende de suas metas, a edição do Azure AD que está a executar e seu programa de licenciamento. Ver [como requerer verificação de dois passos para um utilizador](../active-directory/authentication/howto-mfa-userstates.md) para determinar a melhor opção para. Consulte a [do Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) e [multi-factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) páginas para obter mais informações sobre as licenças de preços e preços.

Seguem-se as opções e os benefícios para ativar a verificação de dois passos:

**Opção 1**: [Ativar a multi-factor Authentication, alterando o estado do utilizador](../active-directory/authentication/howto-mfa-userstates.md).   
**Benefício**: Este é o método tradicional para exigir a verificação de dois passos. Funciona com ambos [multi-factor Authentication na cloud e no servidor do Azure multi-factor Authentication](../active-directory/authentication/concept-mfa-whichversion.md). Ao utilizar este método requer que os utilizadores efetuar a verificação de dois passos sempre que iniciam sessão e substitui as políticas de acesso condicional.

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

## <a name="use-role-based-access-control-rbac"></a>Utilizar o controlo de acesso baseado em funções (RBAC)

Restringir o acesso com base na [precisa de saber](https://en.wikipedia.org/wiki/Need_to_know) e [menor privilégio](https://en.wikipedia.org/wiki/Principle_of_least_privilege) princípios de segurança é fundamental para as organizações que desejam aplicar políticas de segurança para acesso a dados. Pode usar [controlo de acesso baseado em funções (RBAC)](../role-based-access-control/overview.md) para atribuir permissões a utilizadores, grupos e aplicações num determinado âmbito. O âmbito de uma atribuição de função pode ser uma subscrição, um grupo de recursos ou um único recurso.

Pode usar [incorporada RBAC](../role-based-access-control/built-in-roles.md) funções no Azure para atribuir privilégios a utilizadores. As organizações que não é impor o controlo de acesso de dados ao utilizar as capacidades, como o RBAC podem ser a dar mais privilégios do que o necessário para seus usuários. Isto pode levar ao comprometimento de dados ao permitir acesso de utilizador a determinados tipos de dados (por exemplo, elevado impacto comercial) que eles não devem ter.

## <a name="lower-exposure-of-privileged-accounts"></a>Exposição inferior de contas com privilégios

Com privilégios de proteção do acesso é um primeiro passo crítico para proteger os ativos empresariais. Minimizar o número de pessoas que têm acesso para proteger informações ou recursos reduz a chance de um utilizador mal intencionado obter acesso, ou um utilizador autorizado inadvertidamente afetar um recurso confidencial.

Contas com privilégios são contas que administram e gerem os sistemas de TI. Os atacantes de Cibersegurança estas contas para obter acesso a dados e sistemas de uma organização de destino. Para proteger o acesso privilegiado, deve isolar as contas e os sistemas do risco de que está a ser exposto a um utilizador mal intencionado.

Recomendamos que desenvolve e siga um plano para proteger o acesso privilegiado contra invasores de cibersegurança. Para obter informações sobre como criar um plano detalhado para proteger identidades e acesso que são geridos ou comunicado no Azure AD, Microsoft Azure, Office 365 e outros serviços cloud, reveja [proteger o acesso privilegiado para implementações híbridas e na cloud O Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md).

O seguinte resume as melhores práticas encontradas na [proteger o acesso privilegiado para implementações híbridas e na cloud no Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md):

**Melhor prática**: Gerir, controlar e monitorizar o acesso a contas com privilégios.   
**Detalhe**: Ative [do Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/active-directory-securing-privileged-access.md). Depois de ativar a Privileged Identity Management, irá receber uma notificação de e-mail mensagens para acesso privilegiado alterações de função. Estas notificações fornecem aviso antecipado quando os utilizadores adicionais são adicionados a funções com privilégios elevados no seu diretório.

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

Avalie as contas que são atribuídas ou elegíveis para a função de administrador global. Se não vir quaisquer contas apenas na cloud utilizando o `*.onmicrosoft.com` domínio (previstas para acesso de emergência), criá-los. Para obter mais informações, consulte Gerir contas de administrador de acesso de emergência no Azure AD.

**Melhor prática**: Ativar o multi-factor Authentication e registe todas as outras contas de administrador de não federadas com privilégios elevados de utilizador único.  
**Detalhe**: Exigir multi-factor Authentication no início de sessão para todos os utilizadores individuais que estão permanentemente atribuídos a uma ou mais das funções de administrador do Azure AD: administrador global, administrador com função privilegiada, administrador do Exchange Online e SharePoint Online administrador. Utilize o guia para habilitar [multi-factor Authentication para as suas contas de administrador](../active-directory/authentication/howto-mfa-userstates.md) e certifique-se de que todos esses utilizadores tiverem [registado](https://aka.ms/mfasetup).

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

## <a name="next-step"></a>Passo seguinte

Ver [padrões e práticas recomendadas de segurança do Azure](security-best-practices-and-patterns.md) para obter mais melhores práticas de segurança a utilizar quando estiver conceber, implementar e gerir soluções na cloud ao utilizar o Azure.
