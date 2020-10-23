---
title: Quatro passos para uma forte fundação de identidade - Azure AD
description: Este tópico descreve quatro passos que os clientes híbridos podem tomar para construir uma forte base de identidade.
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5dc931c461988daf9ff6fb2f954b52fa58ce94af
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369782"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Quatro passos para uma forte fundação de identidade com Azure Ative Directory

Gerir o acesso a apps e dados já não pode contar com as tradicionais estratégias de fronteira de segurança da rede, como redes de perímetro e firewalls, devido ao rápido movimento de apps para a nuvem. Agora, as organizações devem confiar na sua solução de identidade para controlar quem e o que tem acesso às aplicações e dados da organização. Mais organizações estão a permitir que os colaboradores tragam os seus próprios dispositivos para o trabalho e utilizem os seus dispositivos a partir de qualquer lugar que possam ligar-se à Internet. Garantir que estes dispositivos são compatíveis e seguros tornou-se uma consideração importante na solução de identidade que uma organização escolhe implementar. No local de trabalho digital de hoje, [a identidade é o plano](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) de controlo primário de qualquer organização que se mova para a nuvem.

Ao adotar uma solução de identidade híbrida Azure Ative (Azure AD), as organizações têm acesso a funcionalidades premium que desbloqueiam a produtividade através da automação, delegação, self-service e capacidades únicas de sinalização. Permite que os seus trabalhadores acedam aos recursos da empresa de onde quer que precisem de fazer o seu trabalho, permitindo ao mesmo tempo que a sua equipa de TI governe esse acesso, garantindo que as pessoas certas têm o acesso certo aos recursos certos para estabelecer uma produtividade segura.

Com base nas nossas aprendizagens, esta lista de boas práticas irá ajudá-lo a implementar rapidamente ações recomendadas para construir uma *forte* base de identidade na sua organização:

* Conecte-se facilmente a apps
* Estabelecer uma identidade para cada utilizador automaticamente
* Capacitar os seus utilizadores de forma segura
* Operacionalize as suas ideias

## <a name="step-1---connect-to-apps-easily"></a>Passo 1 - Conecte-se facilmente às aplicações

Ao ligar as suas aplicações com a AZure AD, pode melhorar a produtividade e segurança do utilizador final, permitindo um único sinal de acesso (SSO) e fazer o fornecimento do utilizador. Ao gerir as suas aplicações num único local, a Azure AD, pode minimizar as despesas administrativas e alcançar um único ponto de controlo para as suas políticas de segurança e conformidade.

Esta secção abrange as suas opções de gestão do acesso dos utilizadores a apps, permitindo um acesso remoto seguro a aplicações internas e os benefícios de migrar as suas apps para Azure AD.

### <a name="make-apps-available-to-your-users-seamlessly"></a>Disponibilizar as aplicações aos seus utilizadores sem problemas

A Azure AD permite aos administradores [adicionar aplicações](../manage-apps/add-application-portal.md) à galeria de aplicações da Enterprise no [portal Azure](https://portal.azure.com/). A adição de aplicações à galeria de aplicações da Enterprise facilita a configuração de aplicações para utilizar a Azure AD como fornecedor de identidade. Também permite gerir o acesso do utilizador à aplicação com políticas de Acesso Condicional e configurar um único sign-on (SSO) para aplicações para que os utilizadores não tenham de introduzir as suas palavras-passe repetidamente e sejam automaticamente inscritos em aplicações no local e em nuvem.

Uma vez que as aplicações são adicionadas à galeria AZure AD, os utilizadores podem ver aplicações que lhes são atribuídas e pesquisar e solicitar outras aplicações conforme necessário. A Azure AD fornece [vários métodos](../manage-apps/end-user-experiences.md) para os utilizadores acederem às suas apps:

* Painel de acesso/Minhas Apps
* Iniciador de aplicações do Microsoft 365
* Início de sessão direto em aplicações federadas
* Links de acesso direto

Para saber mais sobre o acesso dos utilizadores às aplicações, consulte **o Passo 3 -- Empower Your Users** neste artigo.

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Migrar aplicativos dos Serviços da Federação de Diretórios Ativos para Azure AD

A migração de uma única configuração de sinalização dos Serviços da Federação de Diretório Ativo (ADFS) para a Azure AD permite capacidades adicionais em segurança, uma gestão e colaboração mais consistentes. Para obter os melhores resultados, recomendamos que emigure as suas aplicações de AD FS para Azure AD. Trazer a autenticação e autorização da sua aplicação para a Azure AD proporciona-lhe os seguintes benefícios:

* Gestão de custos
* Gestão do risco
* Aumento da produtividade
* Abordar a conformidade e a governação

Para saber mais, consulte o papel branco [migrando as suas aplicações para o Azure Ative Directory.](https://aka.ms/migrateapps/whitepaper)

### <a name="enable-secure-remote-access-to-apps"></a>Permitir acesso remoto seguro a apps

[O Azure AD Application Proxy](../manage-apps/what-is-application-proxy.md) fornece uma solução simples para as organizações publicarem no local aplicações para a nuvem para utilizadores remotos que precisam de acesso a aplicações internas de forma segura. Depois de um único s-on para Azure AD, os utilizadores podem aceder a aplicações tanto em nuvem como no local através de URLs externos ou de um portal de aplicações internas.

A Azure AD Application Proxy oferece os seguintes benefícios:

* Alargar a Azure a recursos no local
  * Segurança e proteção em escala de nuvem
  * Funcionalidades como Acesso Condicional e Autenticação Multi-Factor que são fáceis de ativar
* Nenhum componente na rede de perímetros, como a VPN e as soluções tradicionais de procuração inversa
* Não são necessárias ligações de entrada
* Único sign-on (SSO) em dispositivos, recursos e aplicativos na nuvem e no local
* Capacita os utilizadores finais a serem produtivos a qualquer momento e em qualquer lugar

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Descubra Shadow IT com segurança de aplicações da Microsoft Cloud

Nas empresas modernas, os departamentos de TI muitas vezes não estão cientes de todas as aplicações em nuvem que são usadas pelos utilizadores para fazer o seu trabalho. Quando os administradores de TI são questionados sobre quantas aplicações na nuvem pensam que os seus funcionários usam, em média dizem 30 ou 40. Na realidade, a média é de mais de 1.000 aplicações separadas sendo usadas por funcionários da sua organização. 80% dos colaboradores usam aplicações não sancionadas que ninguém reviu e que podem não estar em conformidade com as suas políticas de segurança e conformidade.

[O Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) (MCAS) pode ajudá-lo a identificar aplicações úteis que são populares entre os utilizadores que o IT pode sancionar e adicionar à galeria de aplicações da Enterprise para que os utilizadores beneficiem de capacidades como SSO e Acesso Condicional.

<em>"**Cloud App Security** ajuda-nos a garantir que o nosso povo está a usar corretamente as nossas aplicações cloud e SaaS, de forma a apoiar as políticas de segurança fundacionais que ajudam a proteger a Accenture."</em> --- [John Blasi, Diretor-geral, Segurança da Informação, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

Além de detetar TI sombra, o MCAS também pode determinar o nível de risco das aplicações, impedir o acesso não autorizado a dados corporativos, possíveis fugas de dados e outros riscos de segurança inerentes às aplicações.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>Passo 2 - Estabelecer uma identidade para cada utilizador automaticamente

Reunir no local e diretórios baseados em nuvem numa solução de identidade híbrida Azure AD permitir-lhe-á reutilizar o investimento ativo existente no local, fornecendo as suas identidades existentes na nuvem. A solução sincroniza as identidades no local com a Azure AD, enquanto a TI mantém o Ative Directory no local a funcionar com quaisquer soluções de governação existentes como a principal fonte de verdade para identidades. A solução de identidade híbrida Azure AD da Microsoft abrange as capacidades no local e baseada na nuvem, criando uma identidade comum de utilizador para autenticação e autorização a todos os recursos, independentemente da sua localização.

A integração dos seus diretórios no local com o Azure AD torna os seus utilizadores mais produtivos e impede que os utilizadores utilizem várias contas através de apps e serviços, fornecendo uma identidade comum para aceder a recursos tanto na nuvem como no local. A utilização de várias contas é um ponto de dor tanto para os utilizadores finais como para as TI. Do ponto de vista do utilizador final, ter várias contas significa ter de se lembrar de várias palavras-passe. Para evitar isto, muitos utilizadores reutilizam a mesma palavra-passe para cada conta, o que é mau do ponto de vista da segurança. Do ponto de vista das TI, a reutilização conduz frequentemente a mais resets de palavra-passe e a custos de ajuda, juntamente com as queixas do utilizador final.

Azure AD Connect é a ferramenta para a qual é usada para sincronizar as suas identidades no local para Azure AD, que pode ser usada para aceder a aplicações em nuvem. Uma vez que as identidades estejam em Azure AD, podem providenciar aplicações SaaS como Salesforce ou Concur.

Nesta secção, listamos recomendações para fornecer alta disponibilidade, autenticação moderna para a nuvem e reduzir a sua pegada no local.

> [!NOTE]
> Se quiser saber mais sobre o Azure AD Connect, veja [o que é Azure AD Connect Sync?](./how-to-connect-sync-whatis.md)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Crie um servidor de preparação para O AZure AD Connect e mantenha-o atualizado

O Azure AD Connect desempenha um papel fundamental no processo de provisionamento. Se o Sync Server ficar offline por qualquer motivo, as alterações nas instalações não serão atualizadas na nuvem e causarão problemas de acesso aos utilizadores. É importante definir uma estratégia de failover que permita aos administradores retomar rapidamente a sincronização após o servidor de sincronização ficar offline.

Para fornecer alta disponibilidade no caso de o seu servidor AZURE AD Connect primário ficar offline, recomenda-se que implemente um servidor de [paragem](./how-to-connect-sync-staging-server.md) separado para Azure AD Connect. A implementação de um servidor permite ao administrador "promover" a produção do servidor de produção através de um simples interruptor de configuração. Ter um servidor de espera configurado no modo de preparação também permite testar e implementar novas alterações de configuração e introduzir um novo servidor se desativar o antigo.

> [!TIP]
> O Azure AD Connect é atualizado regularmente. Por isso, é fortemente recomendado que mantenha o servidor de paragem atualmente, de forma a tirar partido das melhorias de desempenho, correções de bugs e novas capacidades que cada nova versão fornece.

### <a name="enable-cloud-authentication"></a>Ativar a autenticação em nuvem

As organizações com diretório ativo no local devem estender o seu diretório à Azure AD utilizando o Azure AD Connect e configurar o método de autenticação adequado. [Escolher o método de autenticação correto](./choose-ad-authn.md) para a sua organização é o primeiro passo na sua jornada de mover aplicações para a nuvem. É um componente crítico, uma vez que controla o acesso a todos os dados e recursos da nuvem.

O método mais simples e recomendado para permitir a autenticação em nuvem para objetos de diretório no local em Azure AD é para ativar a [sincronização de hash de palavra-passe](./how-to-connect-password-hash-synchronization.md) (PHS). Em alternativa, algumas organizações podem considerar permitir a [autenticação pass-through](./how-to-connect-pta-quick-start.md) (PTA).

Quer escolha PHS ou PTA, não se esqueça de ativar [o Seamless Single Sign-on](./how-to-connect-sso.md) para permitir que os utilizadores acedam a aplicações na nuvem sem introduzir constantemente o seu nome de utilizador e palavra-passe na aplicação quando utilizarem dispositivos Windows 7 e 8 na sua rede corporativa. Sem uma única sincê-in, os utilizadores devem lembrar-se de senhas específicas da aplicação e assinar em cada aplicação. Da mesma forma, o pessoal de TI precisa de criar e atualizar contas de utilizadores para cada aplicação, como o Microsoft 365, Box e Salesforce. Os utilizadores precisam de se lembrar das suas palavras-passe, além de gastar o tempo para se inscreverem em cada aplicação. Fornecer um mecanismo de inscrição único padronizado a toda a empresa é crucial para a melhor experiência do utilizador, redução de risco, capacidade de reportar e governação.

Para organizações que já utilizam FS AD ou outro fornecedor de autenticação no local, mudar-se para a Azure AD como seu fornecedor de identidade pode reduzir a complexidade e melhorar a disponibilidade. A menos que tenha casos de uso específico para usar a federação, recomendamos migrar da autenticação federada para PHS e Seamless SSO ou PTA e Seamless SSO para desfrutar dos benefícios de uma pegada reduzida no local e da flexibilidade que a nuvem oferece com experiências melhoradas do utilizador. Para obter mais informações, consulte [Migrar da federação para sincronização de haxixe de palavra-passe para o Diretório Ativo Azure](./plan-migrate-adfs-password-hash-sync.md).

### <a name="enable-automatic-deprovisioning-of-accounts"></a>Permitir a desprovisionamento automático das contas

Permitir o fornecimento automatizado e desprovisionamento às suas aplicações é a melhor estratégia para governar o ciclo de vida das identidades em vários sistemas. A Azure AD suporta [o fornecimento automatizado, baseado em políticas e desprovisionamento](../app-provisioning/configure-automatic-user-provisioning-portal.md) de contas de utilizadores para uma variedade de aplicações populares do SaaS, como o ServiceNow e a Salesforce, e outras que implementam o [protocolo SCIM 2.0](../app-provisioning/use-scim-to-provision-users-and-groups.md). Ao contrário das soluções tradicionais de avisão, que requerem código personalizado ou carregamento manual de ficheiros CSV, o serviço de fornecimento é hospedado na nuvem, e possui conectores pré-integrados que podem ser configurados e geridos através do portal Azure. Um dos principais benefícios da desprovisionamento automático é que ajuda a proteger a sua organização removendo instantaneamente as identidades dos utilizadores das principais aplicações SaaS quando saem da organização.

Para saber mais sobre o fornecimento automático de conta de utilizador e como funciona, consulte [automatizar o Provisionamento do Utilizador e Deprovisionamento para Aplicações SaaS com o Azure Ative Directory](../app-provisioning/user-provisioning.md).

## <a name="step-3---empower-your-users-securely"></a>Passo 3 - Capacitar os seus utilizadores de forma segura

No local de trabalho digital de hoje, é importante equilibrar a segurança com a produtividade. No entanto, os utilizadores finais muitas vezes empurram para trás medidas de segurança que retardam a sua produtividade e acesso a aplicações na nuvem. Para ajudar a resolver este problema, o Azure AD fornece capacidades de autosserviço que permitem aos utilizadores permanecer produtivos, minimizando as despesas administrativas.

Esta secção lista recomendações para remover o atrito da sua organização, capacitando os seus utilizadores enquanto permanece vigilante.

### <a name="enable-self-service-password-reset-for-all-users"></a>Ativar Self-Service redefinição de palavra-passe para todos os utilizadores

O reset da [palavra-passe de autosserviço](../authentication/tutorial-enable-sspr.md) da Azure (SSPR) oferece um meio simples para os administradores de TI permitirem que os utilizadores reiniciem e desbloqueiem as suas palavras-passe ou contas sem intervenção do administrador. O sistema inclui relatórios detalhados que controlam quando os utilizadores acedem ao sistema, juntamente com notificações para o alertar quanto a utilizações indevidas ou abusos.

Por padrão, o Azure AD desbloqueia as contas quando executa uma redefinição de palavra-passe. No entanto, quando ativa a integração do Azure AD Connect [no local,](../authentication/concept-sspr-howitworks.md#on-premises-integration)também tem a opção de separar essas duas operações, que permitem aos utilizadores desbloquear a sua conta sem ter de redefinir a palavra-passe.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>Certifique-se de que todos os utilizadores estão registados para MFA e SSPR

O Azure fornece relatórios que podem ser utilizados por si e pela sua organização para garantir que os utilizadores estão registados para MFA e SSPR. Os utilizadores que não se registaram podem ter de ser educados sobre o processo.

O relatório de inscrições do MFA inclui informações sobre o uso do MFA e [dá-lhe](../authentication/howto-mfa-reporting.md) informações sobre como a MFA está a trabalhar na sua organização. Ter acesso a atividade de inscrição (auditorias e deteções de riscos) para a Azure AD é crucial para a resolução de problemas, análises de uso e investigações forenses.

Da mesma forma, o [relatório de Gestão de Passwords self-service](../authentication/howto-sspr-reporting.md) pode ser usado para determinar quem tem (ou não) registado para SSPR.

### <a name="self-service-app-management"></a>Gestão de aplicativos self-service

Antes que os seus utilizadores possam auto-descobrir aplicações a partir do seu painel de acesso, é necessário permitir o acesso de [aplicações de autosserviço](../manage-apps/access-panel-manage-self-service-access.md) a quaisquer aplicações que deseje permitir que os utilizadores se auto-descubram e solicitem acesso. O acesso a aplicações self-service é uma ótima forma de permitir que os utilizadores se auto-descubram e opcionalmente permitam ao grupo empresarial aprovar o acesso a essas aplicações. Pode permitir que o grupo empresarial gere as credenciais atribuídas a esses utilizadores para [Single-Sign Password nas Aplicações](../manage-apps/application-sign-in-problem-password-sso-gallery.md#configure-password-sso-for-an-azure-ad-gallery-app) a partir dos seus painéis de acesso.

### <a name="self-service-group-management"></a>Gestão de grupos self-service

Atribuir os utilizadores às aplicações é melhor mapeado quando se utilizam grupos, porque permitem uma grande flexibilidade e capacidade de gestão em escala:

* Baseado em atributos usando a adesão dinâmica do grupo
* Delegação aos proprietários de aplicações

O Azure AD fornece a capacidade de gerir o acesso a recursos usando grupos de segurança e grupos Microsoft 365. Estes grupos podem ser geridos por um dono de grupo que pode aprovar ou negar pedidos de adesão e delegar o controlo da adesão ao grupo. Conhecida como [gestão de grupos de self-service,](../enterprise-users/groups-self-service-management.md)esta funcionalidade poupa tempo ao permitir aos proprietários de grupos que não lhes seja atribuído um papel administrativo para criar e gerir grupos sem terem de confiar nos administradores para lidarem com os seus pedidos.

## <a name="step-4---operationalize-your-insights"></a>Passo 4 - Operacionalizar as suas ideias

A auditoria e registo de eventos relacionados com a segurança e alertas relacionados são componentes essenciais de uma estratégia eficiente para garantir que os utilizadores permanecem produtivos e a sua organização está segura. Registos de segurança e relatórios podem ajudar a responder a perguntas como:

* Estás a usar aquilo por que estás a pagar?
* Passa-se alguma coisa suspeita ou maliciosa no meu inquilino?
* Quem foi atingido durante um incidente de segurança?

Os registos de segurança e os relatórios fornecem-lhe um registo eletrónico de atividades suspeitas e ajudam-no a detetar padrões que podem indicar uma tentativa ou sucesso da penetração externa da rede e ataques internos. Pode utilizar a auditoria para monitorizar a atividade do utilizador, documentar a conformidade regulatória, fazer análises forenses e muito mais. Os alertas fornecem notificações de eventos de segurança.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>Atribuir funções de administração menos privilegiadas para operações

Ao pensar na sua abordagem às operações, há alguns níveis de administração a considerar. O primeiro nível coloca o fardo da administração no seu administrador(s) global. Utilizar sempre o papel de administrador global, pode ser apropriado para empresas mais pequenas. Mas para as organizações maiores com pessoal de secretária e administradores responsáveis por tarefas específicas, atribuir o papel de administrador global pode ser um risco para a segurança, uma vez que proporciona a esses indivíduos a capacidade de gerir tarefas que estão acima e além do que deveriam ser capazes de fazer.

Neste caso, deve considerar o próximo nível de administração. Utilizando o Azure AD, pode designar os utilizadores finais como "administradores limitados" que podem gerir tarefas em funções menos privilegiadas. Por exemplo, pode atribuir ao seu pessoal de secretária de ajuda a função de leitor de [segurança](../roles/permissions-reference.md#security-reader) para fornecer-lhes a capacidade de gerir funcionalidades relacionadas com segurança com acesso apenas de leitura. Ou talvez faça sentido atribuir o papel [de administrador de autenticação](../roles/permissions-reference.md#authentication-administrator) a indivíduos para lhes dar a capacidade de repor credenciais de não-senha ou ler e configurar a Azure Service Health.

Para saber mais, consulte [permissões de função de Administrador no Diretório Ativo Azure](../roles/permissions-reference.md).

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Monitorar componentes híbridos (Azure AD Connect Sync, AD FS) usando Azure AD Connect Health

Azure AD Connect e AD FS são componentes críticos que podem potencialmente quebrar a gestão e autenticação do ciclo de vida e, em última análise, levar a interrupções. Por isso, deve implantar a Azure AD Connect Health para monitorização e reporte destes componentes.

Para saber mais, vá ler [Monitor AD FS usando Azure AD Connect Health](./how-to-connect-health-adfs.md).

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Utilize o Monitor Azure para recolher registos de dados para análise

[O Azure Monitor](../../azure-monitor/overview.md) é um portal de monitorização unificado para todos os registos AD Azure, que fornece insights profundos, análise avançada e aprendizagem de máquinas inteligentes. Com o Azure Monitor, pode consumir métricas e registos dentro do portal e através de APIs para ganhar mais visibilidade no estado e desempenho dos seus recursos. Permite uma única vidraça de experiência de vidro dentro do portal, permitindo ao mesmo tempo uma vasta gama de integrações de produtos através de APIs e opções de exportação de dados que suportam sistemas siem tradicionais de terceiros. O Azure Monitor também lhe dá a capacidade de configurar regras de alerta para ser notificado ou tomar ações automatizadas em questões que impactam os seus recursos.

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>Crie dashboards personalizados para a sua liderança e o seu dia-a-dia

As organizações que não têm uma solução SIEM podem descarregar o [Power BI Content Pack](../reports-monitoring/howto-use-azure-monitor-workbooks.md) para Azure AD. O pacote de conteúdos Power BI contém relatórios pré-construídos para ajudá-lo a entender como os seus utilizadores adotam e utilizam funcionalidades AZure AD, o que lhe permite obter informações sobre todas as atividades dentro do seu diretório. Também pode criar o seu próprio [dashboard personalizado](/power-bi/service-dashboards) e partilhar com a sua equipa de liderança para relatar as atividades do dia-a-dia. Os dashboards são uma ótima maneira de monitorizar o seu negócio e ver todas as suas métricas mais importantes num ápice. As visualizações num dashboard podem ser provenientes de um ou vários conjuntos de dados subjacentes ou de um ou vários relatórios subjacentes. Um dashboard combina dados no local e na cloud ao fornecer uma vista consolidada, independentemente de onde os dados residem.

![Painel personalizado power BI](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>Compreenda o seu apoio aos motoristas de chamadas

Quando implementar uma solução de identidade híbrida, tal como delineada neste artigo, deverá notar uma redução nas suas chamadas de apoio. Questões comuns como palavras-passe esquecidas e bloqueios de contas são atenuadas implementando o reset da palavra-passe de autosserviço da Azure, ao mesmo tempo que permite que o acesso à aplicação de self-service permita que os utilizadores se auto-descubram e solicitem o acesso às aplicações sem depender do seu pessoal de TI.

Se não observar uma redução nas chamadas de apoio, recomendamos que analise os seus controladores de chamada de apoio numa tentativa de confirmar se o acesso a SSPR ou aplicação de self-service foi configurado corretamente ou se existem outros problemas novos que possam ser sistematicamente resolvidos.

*"Na nossa jornada de transformação digital, precisávamos de um fornecedor de gestão de identidade e acesso fiável para facilitar a integração perfeita mas segura entre nós, parceiros e fornecedores de serviços na nuvem, para um ecossistema eficaz; A Azure AD foi a melhor opção, oferecendo-nos as capacidades e visibilidade necessárias que nos permitiram detetar e responder aos riscos."* --- [Yazan Almasri, Diretor Global de Segurança da Informação, Aramex](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>Monitorize o seu uso de apps para impulsionar insights

Além de descobrir o Shadow IT, monitorizar o uso de aplicações em toda a sua organização usando [o Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) pode ajudar a sua organização à medida que se move para tirar o máximo partido da promessa de aplicações na nuvem. Pode ajudar a mantê-lo no controlo dos seus ativos através de uma melhor visibilidade na atividade e aumentar a proteção de dados críticos através de aplicações na nuvem. Monitorizar o uso de aplicações na sua organização usando o MCAS pode ajudá-lo a responder às seguintes questões:

* Que aplicações não estão a ser criadas para armazenar dados?
* Onde e quando os dados sensíveis estão a ser armazenados na nuvem?
* Quem está a aceder a dados sensíveis na nuvem?

*"Com a Cloud App Security, podemos rapidamente detetar anomalias e tomar medidas."* --- [Eric LePenske, Gerente Sénior, Segurança da Informação, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>Resumo

Existem muitos aspetos para implementar uma solução híbrida de identidade, mas esta lista de verificação em quatro etapas irá ajudá-lo a realizar rapidamente uma infraestrutura de identidade que permitirá aos utilizadores serem mais produtivos e seguros.

* Conecte-se facilmente a apps
* Estabelecer uma identidade para cada utilizador automaticamente
* Capacitar os seus utilizadores de forma segura
* Operacionalize as suas ideias

Esperamos que este documento seja um roteiro útil para estabelecer uma forte base de identidade para a sua organização.

## <a name="identity-checklist"></a>Lista de verificação de identidades

Recomendamos que imprima a seguinte lista de verificação para referência à medida que inicia a sua viagem a uma base de identidade mais sólida na sua organização.

### <a name="today"></a>Hoje

|Feito?|Item|
|:-|:-|
||Redefinição da palavra-passe de autosserviço piloto (SSPR) para um grupo|
||Monitorize componentes híbridos usando Azure AD Connect Health|
||Atribuir funções de administração menos privilegiadas para a operação|
||Descubra Shadow IT com segurança de aplicações da Microsoft Cloud|
||Utilize o Monitor Azure para recolher registos de dados para análise|

### <a name="next-two-weeks"></a>Próximas duas semanas

|Feito?|Item|
|:-|:-|
||Disponibilizar uma aplicação para os seus utilizadores|
||Pilot Azure AD provisão para uma app saaS de eleição|
||Configurar um servidor de encenação para Azure AD Connect e mantê-lo atualizado|
||Comece a migrar aplicativos de ADFS para Azure AD|
||Crie dashboards personalizados para a sua liderança e o seu dia-a-dia|

### <a name="next-month"></a>No próximo mês

|Feito?|Item|
|:-|:-|
||Monitorize o seu uso de apps para impulsionar insights|
||Pilot seguro acesso remoto a apps|
||Certifique-se de que todos os utilizadores estão registados para MFA e SSPR|
||Ativar a autenticação em nuvem|

### <a name="next-three-months"></a>Próximos três meses

|Feito?|Item|
|:-|:-|
||Permitir a gestão de aplicativos de self-service|
||Permitir a gestão do grupo de self-service|
||Monitorize o seu uso de apps para impulsionar insights|
||Compreenda o seu apoio aos motoristas de chamadas|

## <a name="next-steps"></a>Passos seguintes

Saiba como pode aumentar a sua postura segura utilizando as capacidades do Azure Ative Directory e esta lista de verificação de cinco etapas - [Cinco passos para garantir a sua infraestrutura de identidade](https://aka.ms/securitysteps).

Saiba como as funcionalidades de identidade no AD Azure podem ajudá-lo a acelerar a sua transição para a gestão governada em nuvem, fornecendo as soluções e capacidades que permitem às organizações adotar e mover mais rapidamente a sua gestão de identidade dos sistemas tradicionais de acesso ao Azure AD - [Como o AD AZure oferece a Cloud Governed Management for On-Premises Workloads](https://aka.ms/cloudgoverned).