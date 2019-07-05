---
title: Quatro etapas para uma base de identidade forte com o Azure Active Directory
description: Este tópico descreve as quatro etapas que os clientes de identidade híbrida podem tomar para criar uma base de identidade forte.
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
ms.author: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96b5e8ab63c1784ff073c7ba38cd4a6319db43c5
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452738"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Quatro etapas para uma base de identidade forte com o Azure Active Directory

Gerir o acesso a aplicações e dados pode já não contar com as estratégias de limites de segurança de rede tradicionais, como redes de perímetro e firewalls devido o movimento rápido de aplicações para a cloud. Agora as organizações devem confiar a sua solução de identidade para controlar quem sincroniza e o que tem acesso a aplicações e dados da organização. Mais organizações estão a permitir que os funcionários tragam os seus dispositivos para trabalhar e utilizar os seus dispositivos em qualquer lugar, que eles podem se conectar à Internet. Garantir que esses dispositivos são seguros e não compatíveis se tornou uma consideração importante na solução de identidade, que uma organização escolhesse implementar. Na área de trabalho digital de hoje em dia, [identidade é o plano de controlo principal](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) de qualquer organização mover para a cloud.

Da adoção de uma solução de identidade híbrida do Azure Active Directory (Azure AD), as organizações a obtém acesso às funcionalidades de premium que promovem a produtividade através de automatização, delegação, Self-serviços e únicos início de sessão em recursos. Ele permite que seus funcionários aos recursos da empresa a partir de onde quer que precisam para trabalhar enquanto permite que a sua equipa de TI regular que o acesso ao garantir que as pessoas certas tenham o acesso aos recursos certos para estabelecer a produtividade segura.

Com base em nossas aprendizagens sobre, esta lista de verificação de melhores práticas ajudarão a implantar rapidamente as ações recomendadas para criar uma *forte* foundation de identidade na sua organização:

* Ligue facilmente às aplicações
* Estabelecer uma identidade de todos os utilizadores automaticamente
* Permitir que os utilizadores de forma segura
* Operacionalizar suas informações

## <a name="step-1---connect-to-apps-easily"></a>Passo 1 - ligar facilmente às aplicações

Ao ligar as suas aplicações com o Azure AD, pode melhorar a produtividade do utilizador final e a segurança habilitando o início de sessão único (SSO) e fazer o aprovisionamento de utilizadores. Ao gerir as suas aplicações num único local, Azure AD, pode minimizar a sobrecarga administrativa e alcançar um ponto único de controle para as políticas de segurança e conformidade.

Esta secção abrange as opções para gerir o acesso de utilizador para aplicações, permitindo o acesso remoto seguro para aplicações internas e os benefícios de migrar as suas aplicações para o Azure AD.

### <a name="make-apps-available-to-your-users-seamlessly"></a>Disponibilizar aplicações aos seus utilizadores sem problemas

O Azure AD permite que os administradores [adicionar aplicativos](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) na Galeria de aplicações empresariais no [portal do Azure](https://portal.azure.com/). Adicionar aplicações para a Galeria de aplicações do Enterprise torna mais fácil para configurar aplicações para utilizar o Azure AD como fornecedor de identidade. Ele também permite-lhe gerir o acesso de utilizador para a aplicação com políticas de acesso condicional e configurar início de sessão único (SSO) para aplicações, para que os utilizadores não tenham de introduzir as palavras-passe repetidamente e automaticamente sessão iniciados em ambos os locais e aplicativos baseados na nuvem.

Assim que os aplicativos são adicionados à galeria do Azure AD, os utilizadores podem ver aplicações que lhes foram atribuídas e procuram e solicitar outras aplicações, conforme necessário. O Azure AD proporciona [vários métodos](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences) para os utilizadores aceder às suas aplicações:

* Painel de acesso / as minhas aplicações
* Iniciador de aplicações do Office 365
* Início de sessão direto em aplicações federadas
* Ligações de início de sessão em diretas

Para saber mais sobre o acesso de utilizador para aplicações, veja **passo 3 – Capacite seus usuários** neste artigo.

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Migrar aplicações dos serviços de Federação do Active Directory para o Azure AD

Migrando única início de sessão em configuração do Active Directory Federation Services (ADFS) para o Azure AD permite capacidades adicionais em segurança, uma capacidade de gerenciamento mais consistente e colaboração. Para obter melhores resultados, recomendamos que migre as suas aplicações do AD FS para o Azure AD. Trazer a sua autorização e autenticação de aplicação para o Azure AD fornece as seguintes vantagens:

* Gestão de custos
* Gestão de riscos
* Aumentando a produtividade
* Governação e conformidade de endereçamento

Para obter mais informações, consulte a [migrar seus aplicativos para o Azure Active Directory](https://aka.ms/migrateapps/whitepaper) White Paper.

### <a name="enable-secure-remote-access-to-apps"></a>Ativar o acesso remoto seguro a aplicações

[Proxy de aplicações do Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) fornece uma solução simples para as organizações a publicar aplicações no local para a cloud para os utilizadores remotos que precisam de acesso para aplicações internas de forma segura. Após um início de sessão único para o Azure AD, os utilizadores podem aceder aplicações na cloud e no local através de URLs externos ou um portal de aplicação interna.

Proxy de aplicações do Azure AD oferece as seguintes vantagens:

* Estendendo o Azure AD para recursos no local
  * Proteção e segurança de escala da cloud
  * Recursos como o acesso condicional e multi-factor Authentication que são fáceis de ativar
* Não existem componentes na rede de perímetro, como VPN e as soluções tradicionais de proxy inverso
* Não existem ligações de entrada necessárias
* Início de sessão único (SSO) em vários dispositivos, recursos e aplicações na cloud e no local
* Permite aos utilizadores finais sejam produtivos em qualquer altura e em qualquer lugar

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Detetar o Shadow IT com o Microsoft Cloud App Security

Nas empresas modernas, a departamentos de TI, muitas vezes, não têm conhecimento de todos os aplicativos de cloud que são utilizados pelos utilizadores para trabalhar. Quando os administradores de TI perguntado cloud quantos aplicativos eles acham que os funcionários usam, em média, eles Digamos 30 ou 40. Na realidade, a média é de mais de 1000 aplicações separadas, que está a ser utilizadas pelos funcionários na sua organização. 80% dos funcionários utilizar aplicações não aprovadas que ninguém tiver sido analisado e não pode estar em conformidade com as políticas de segurança e conformidade.

[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) pode ajudá-lo a identificar aplicações úteis que são populares entre os utilizadores que ele pode aprovar e adicionar à Galeria de aplicações do Enterprise, para que os utilizadores beneficiam de capacidades como o SSO e o acesso condicional.

*"* * Cloud App Security** ajuda-na garantir que nossos pessoas estão corretamente usando nossa cloud e aplicações SaaS, de formas que suportam as políticas de segurança fundamentais que ajudam a proteger a Accenture." *--- [John Blasi, gerir a Accenture diretor, segurança de informações,](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

Além de detectar shadow IT, MCAS também pode determinar o nível de risco de aplicações, impedir o acesso não autorizado para os dados da empresa, a fuga de dados e outros riscos de segurança inerentes nas aplicações.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>Passo 2 – estabelecer uma identidade de todos os utilizadores automaticamente

Reunir no local e diretórios baseado na nuvem numa solução de identidade híbrida do Azure AD permitirá reutilizar o investimento existente de Active Directory no local ao aprovisionar as identidades existentes na cloud. A solução sincroniza as identidades no local com o Azure AD, enquanto mantém IT do Active Directory no local em execução com quaisquer soluções de governação existentes, como a principal fonte de verdade para identidades. Solução de identidade híbrida da Microsoft do Azure AD abrange no local e de recursos com base na cloud, criação de uma identidade de utilizador comum para autenticação e autorização para todos os recursos, independentemente da respetiva localização.

Integração dos diretórios no local com o Azure AD torna os seus utilizadores mais produtivos e impede os utilizadores de utilizar várias contas em aplicações e serviços ao fornecer uma identidade comum para o acesso a ambos os cloud e recursos no local. Utilizar várias contas é um ponto problemático para os utilizadores finais e IT semelhante. Da perspectiva do usuário final, ter vários meios de contas ter de memorizar várias palavras-passe. Para evitar isto, o número de utilizadores que reutilizar a mesma palavra-passe para cada conta, o que é ruim de uma perspectiva de segurança. Da perspectiva de TI, reutilização, muitas vezes, nos leva a mais reposições de palavra-passe e os custos de suporte técnico, juntamente com as reclamações de utilizador final.

O Azure AD Connect é a ferramenta que é utilizada para sincronizar as identidades no local ao Azure AD, que, em seguida, pode ser utilizado para aceder a aplicações na cloud. Assim que são as identidades no Azure AD, eles podem aprovisionar a aplicações SaaS, como o Salesforce ou Concur.

Nesta secção, vamos lista recomendações para fornecer elevada disponibilidade, a autenticação moderna para a nuvem e a redução da pegada no local.

> [!NOTE]
> Se quiser saber mais sobre o Azure AD Connect, consulte o artigo [o que é o Azure AD Connect Sync?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Configurar um servidor de preparação para o Azure AD Connect e mantê-la atualizada

O Azure AD Connect desempenha um papel fundamental no processo de aprovisionamento. Se o servidor de sincronização ficar offline por qualquer motivo, as alterações para o local não serão atualizadas na cloud e causam problemas de acesso aos utilizadores. É importante definir uma estratégia de ativação pós-falha que permite aos administradores retomar a sincronização rapidamente depois do servidor de sincronização fica offline.

Para proporcionar elevada disponibilidade no evento do primário Azure AD Connect servidor ficar offline, recomenda-se que implemente um separado [servidor intermediário](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server) do Azure AD Connect. Implementar um servidor permite ao administrador para "promover" servidor de preparação para a produção por uma simples alteração de configuração. Ter um servidor em espera configurado no modo de teste também permite-lhe testar e implementar novas alterações de configuração e introduzir um novo servidor, se desativar o antigo.

> [!TIP]
> O Azure AD Connect é atualizado regularmente. Por conseguinte, é vivamente recomendado que mantenha o servidor de preparação atual para aproveitar as melhorias de desempenho, correções de erros e novas capacidades que fornece a cada nova versão.

### <a name="enable-cloud-authentication"></a>Ativar a autenticação na nuvem

As organizações com o Active Directory no local devem expandir o seu diretório para o Azure AD com o Azure AD Connect e configurar o método de autenticação adequado. [Escolher o método de autenticação corretas](https://docs.microsoft.com/azure/security/azure-ad-choose-authn) para sua organização é a primeira etapa no seu percurso de migração de aplicações para a nuvem. É um componente crítico, uma vez que ele controla o acesso a todos os dados na cloud e recursos.

O método mais simples e recomendado para ativar a autenticação de cloud para objetos de diretório no local no Azure AD é habilitar [sincronização de Hash de palavra-passe](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS). Em alternativa, algumas organizações podem considerar a ativação [autenticação pass-through](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA).

Se escolher PHS ou PTA, não se esqueça de ativar [o início de sessão único totalmente integrado](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) para permitir que os utilizadores acedam a aplicações na cloud sem constantemente introduzir o nome de utilizador e palavra-passe na aplicação ao utilizar o Windows 7 e 8 dispositivos na sua empresa rede. Sem início de sessão único, os utilizadores devem se lembrar palavras-passe de aplicação específicos e inicie sessão em cada aplicativo. Da mesma forma, a equipe de TI precisa para criar e atualizar as contas de utilizador para cada aplicativo, como o Office 365, a caixa e o Salesforce. Os utilizadores têm de se lembrar de suas senhas, além de perder tempo a iniciar sessão em cada aplicativo. Fornecer um padronizado único início de sessão mecanismo para toda a empresa é crucial para a melhor experiência de utilizador, a redução de risco, capacidade de gerar relatórios e governação.

Para organizações que já a utilizar o AD FS ou de outro fornecedor de autenticação no local, mudar para o Azure AD como fornecedor de identidade pode reduzir a complexidade e, melhorar a disponibilidade. A menos que tenha casos de uso específico para utilizar o Federação, recomendamos migrar de autenticação federada PHS e SSO totalmente integrado ou PTA e SSO totalmente integrado para aproveitar os benefícios de um requisito de espaço reduzido no local e a flexibilidade que a cloud oferece com experiências de usuário aprimorada. Para obter mais informações, consulte [migrar da Federação para a sincronização de hash de palavra-passe do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync).

### <a name="enable-automatic-deprovisioning-of-accounts"></a>Ativar desaprovisionamento automática de contas

Ativação automática de aprovisionamento e desaprovisionamento às suas aplicações é a melhor estratégia para que regem o ciclo de vida de identidades em vários sistemas. O Azure AD suporta [automatizada, baseada em política de aprovisionamento e desaprovisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) das contas de utilizador a uma variedade de aplicações SaaS populares, como o ServiceNow e o Salesforce e outros que implementam o [SCIM 2.0 protocolo](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups). Ao contrário das soluções de aprovisionamento tradicionais, que requerem o código personalizado ou o carregamento manual ou ficheiros CSV, o serviço de aprovisionamento está alojado na cloud e funcionalidades de pré-integrado conectores que podem ser configurados e geridas no portal do Azure. Das principais vantagens do desaprovisionamento automática é que ele ajuda a proteger a sua organização removendo instantaneamente as identidades dos utilizadores de aplicações de SaaS chave quando deixam a organização.

Para saber mais sobre o aprovisionamento de contas de utilizadores automático e como ele funciona, veja [automatizar o aprovisionamento do utilizador e cancelar o aprovisionamento a aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

## <a name="step-3---empower-your-users-securely"></a>Passo 3 - permitir que os utilizadores de forma segura

Na área de trabalho digital de hoje em dia, é importante equilibrar a segurança com a produtividade. No entanto, os utilizadores finais, muitas vezes, voltar a emitir em medidas de segurança que lento sua produtividade e o acesso a aplicações na cloud. Para ajudar a resolver isso, o Azure AD fornece capacidades self-service que permitem aos utilizadores para se manter produtivo enquanto minimiza a sobrecarga administrativa.

Esta seção apresenta uma lista de recomendações para remover o atrito da sua organização, capacitando seus usuários ao mesmo tempo restante vigilante.

### <a name="enable-self-service-password-reset-for-all-users"></a>Ativar a reposição de palavra-passe Self-Service para todos os utilizadores

Do Azure [reposição de palavra-passe self-service](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) (SSPR) oferece uma forma simples para os administradores de TI permitir aos utilizadores repor e desbloquear as suas palavras-passe ou contas sem intervenção do administrador. O sistema inclui relatórios detalhados que controlam quando os utilizadores acedem ao sistema, juntamente com notificações para o alertar quanto a utilizações indevidas ou abusos.

Por predefinição, o Azure AD desbloqueia contas quando ele efetuar uma reposição de palavra-passe. No entanto, quando ativa do Azure AD Connect [integração no local](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration), também tem a opção para separar as duas operações, que permitem aos utilizadores desbloquear a conta sem ter de repor a palavra-passe.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>Certifique-se de que todos os utilizadores estão registados para MFA e o SSPR

O Azure fornece relatórios que podem ser utilizados por e sua organização para garantir que os utilizadores estão registados para MFA e o SSPR. Os utilizadores que ainda não está registado poderão ter de ser instruídos sobre o processo.

O MFA [relatório de inícios de sessão](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting) inclui informações sobre a utilização MFA e fornece informações sobre como esta está a funcionar na sua organização. Ter acesso de início de sessão atividade (e as auditorias e eventos de risco) para o Azure AD é crucial para resolução de problemas, análise de utilização e as investigações forenses.

Da mesma forma, o [relatório de gestão de palavra-passe Self-Service](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) pode ser utilizado para determinar quem tem (ou não) registado para SSPR.

### <a name="self-service-app-management"></a>Gestão de aplicações self-service

Antes dos utilizadores Self-podem detetar aplicações a partir de seu painel de acesso, tem de ativar [acesso da aplicação self-service](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access) para todos os aplicativos que deseja permitir que os utilizadores Self-detetar e pedir acesso. Acesso de aplicações self-service é uma excelente forma de permitir que os utilizadores Self-detetar aplicações e, opcionalmente, permitir que o grupo de negócio para aprovar o acesso a esses aplicativos. Pode permitir que o grupo de empresas gerir as credenciais atribuídas a esses utilizadores para [palavra-passe de início de sessão único em aplicações](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-the-application-for-password-single-sign-on) diretamente a partir de seus painéis de acesso.

### <a name="self-service-group-management"></a>Gestão de grupos self-service

Atribuir utilizadores a aplicações é melhor mapeado quando a utilização de grupos, porque permitem que grande flexibilidade e capacidade de gerenciar em escala:

* Usando a associação de grupo dinâmico com base no atributo
* Delegação para os proprietários da aplicação

Azure AD fornece a capacidade para gerir o acesso a recursos através de grupos de segurança e grupos do Office 365. Estes grupos podem ser geridos por um proprietário de grupo que pode aprovar ou negar pedidos de associação e delegar o controle de associação de grupo. Conhecido como [gestão de grupos self-service](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management), esta funcionalidade poupa tempo ao permitir que os proprietários do grupo que não são atribuídos uma função administrativa para criar e gerir grupos sem ter de depender de administradores para processar os pedidos.

## <a name="step-4---operationalize-your-insights"></a>Passo 4 - Operacionalizar suas informações

Auditoria e Registro em log de eventos relacionados com a segurança e alertas relacionados são componentes essenciais de uma estratégia eficiente para garantir que os utilizadores permanecem produtivos e sua organização é segura. Registos de segurança e os relatórios podem ajudar a responder a perguntas como:

* Está a utilizar o que está pagando para?
* Existe algo suspeito ou malicioso ocorrem no meu inquilino?
* Quem foi afetada durante um incidente de segurança?

Registos de segurança e de relatórios proporcionam um registo eletrónico de atividades suspeitas e ajuda detectar padrões que podem indicar uma tentativa ou com êxito penetração externa da rede e ataques internos. Pode utilizar a auditoria para monitorizar a atividade do utilizador, a conformidade a normas documento, fazer análise forense e muito mais. Os alertas fornecem notificações de eventos de segurança.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>Atribuir funções de administrador com privilégios mínimos para operações

Como a pensar sobre sua abordagem para operações, existem dois níveis de administração a serem considerados. O primeiro nível coloca a responsabilidade de administração no seu infotrabalhador global. Utilizar sempre a função de administrador global, poderá ser adequada para empresas mais pequenas. Mas para as grandes organizações com a equipe de suporte técnico e administradores responsáveis por tarefas específicas, atribuir a função de administrador global pode ser um risco de segurança, uma vez que ele fornece esses indivíduos com a capacidade para gerir as tarefas que são o suficiente o que eles devem ser capazes de fazer.

Neste caso, deve considerar o próximo nível de administração. Utilizar o Azure AD, pode designar os utilizadores finais como "limitados administradores" quem podem gerir tarefas em funções com menos privilégios. Por exemplo, poderá atribuir sua ajuda pessoal do suporte técnico a [leitor de segurança](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader) função para lhes dar a capacidade de gerir as funcionalidades relacionadas com segurança com acesso só de leitura. Ou talvez faz sentido para atribuir a [administrador authentication](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator) função aos indivíduos para lhes dar a capacidade de repor as credenciais de palavra-passe ou ler e configurar o Azure Service Health.

Para obter mais informações, consulte [permissões da função de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Monitorizar componentes de híbrida (sincronização do Azure AD Connect, do AD FS) com o Azure AD Connect Health

O Azure AD Connect e o AD FS são componentes críticos que podem potencialmente quebrar o ciclo de vida gestão e a autenticação e, por fim, levar a falhas. Por conseguinte, deve implementar o Azure AD Connect Health para a monitorização e relatórios desses componentes.

Para obter mais informações, aceda leitura [FS de Monitor AD utilizando o Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs).

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Utilizar o Azure Monitor para recolher registos de dados para análise

[O Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) é um unificada portal monitorização de todos os registos do Azure AD, que fornece informações aprofundadas, análises avançadas e aprendizagem automática inteligente. Com o Azure Monitor, pode consumir métricas e registos no portal e através de APIs para obter mais visibilidade sobre o estado e o desempenho dos seus recursos. Permite que um único painel de vidro experiência no portal, enquanto permite que uma grande variedade de integrações de produtos através de APIs e os dados opções de exportação que suportam os sistemas SIEM tradicionais de terceiros. Monitor do Azure também oferece a capacidade de configurar regras de alerta para ser notificado ou tome medidas automatizadas nos problemas que afetem seus recursos.

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>Criar dashboards personalizados para sua liderança e seu dia para dia

Organizações que não tenham uma solução SIEM podem transferir o [o pacote de conteúdos do Power BI](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack) para o Azure AD. O Power BI conteúdo de pacote contém relatórios criados previamente para o ajudar a compreender como os utilizadores adotam e utilizam as funcionalidades do Azure AD, que permite que obter informações sobre todas as atividades no seu diretório. Também pode criar seus próprios [dashboard personalizado](https://docs.microsoft.com/power-bi/service-dashboards) e partilhar com a equipa de liderança para gerar relatórios sobre atividades diárias. Os dashboards são uma excelente forma de monitorizar o seu negócio e ver todas as suas métricas mais importantes num instante. As visualizações num dashboard podem vir de um conjunto de dados subjacente ou muitos e de um relatório subjacente ou muitos. Um dashboard combina no local e na cloud de dados, fornecer uma vista consolidada, independentemente de onde os dados residem.

![Dashboard personalizado do Power BI](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>Compreender os controladores de chamada de suporte

Quando implementa uma solução de identidade híbrida conforme descrito neste artigo, deve observar, por fim, uma redução em suas chamadas de suporte. Problemas comuns, tais como palavras-passe esquecidas e bloqueios de conta são atenuados ao implementar a reposição de palavra-passe self-service do Azure, ao ativar o acesso de aplicações self-service permite aos utilizadores Self-Descubra e solicitar acesso a aplicações da entidade confiadora sua equipe de TI.

Se não observar uma redução nas chamadas de suporte, recomendamos que analise seus controladores de chamada de suporte numa tentativa para confirmar se SSPR ou acesso de aplicações self-service tiver sido configurado corretamente ou se existem outros novos problemas que podem ser sistematicamente resolvido.

*"No nosso percurso de transformação digital, precisávamos de uma identidade confiável e o fornecedor de gestão de acesso para facilitar a integração totalmente integrada, embora segura entre nós, parceiros e fornecedores de serviços cloud, para um ecossistema em vigor; O Azure AD foi a melhor opção, oferecendo-nos recursos necessários e a visibilidade ativado-nos detetar e responder a riscos."* --- [Aramex Yazan Almasri, diretor de segurança de informações globais,](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>Monitorizar a utilização de aplicações para impulsionar informações

Para além de detetar Shadow IT, monitorizar a utilização da aplicação em sua organização ao utilizar [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) pode ajudar sua organização quando migra para tirar partido da promessa das aplicações na cloud. Ele pode ajudar a manter o controlo dos seus ativos através da visibilidade melhorada para a atividade e aumentar a proteção dos dados críticos nas aplicações na cloud. Monitorizar a utilização de aplicações na sua organização com MCAS pode ajudá-lo a responder às seguintes perguntas:

* Que aplicações não aprovadas são os funcionários a utilizar para armazenar dados em?
* Onde e quando são dados confidenciais que está a ser armazenados na cloud?
* Quem está a aceder a dados confidenciais na cloud?

*"Com o Cloud App Security, pode rapidamente detetar anomalias e tomar medidas."* --- [Eric LePenske, sénior Manager, informações de segurança, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>Resumo

Há muitos aspectos à implementação de uma solução de identidade híbrida, mas esta lista de verificação de quatro etapas irá ajudá-lo a realizar com rapidez uma infraestrutura de identidade que permitirá que os utilizadores sejam mais produtivos e seguros.

* Ligue facilmente às aplicações
* Estabelecer uma identidade de todos os utilizadores automaticamente
* Permitir que os utilizadores de forma segura
* Operacionalizar suas informações

Esperamos que este documento é uma estratégia útil para estabelecer uma base de identidade forte para a sua organização.

## <a name="identity-checklist"></a>Lista de verificação de identidade

Recomendamos que imprimir a lista de verificação seguinte para referência, como deve começar por um mais sólida Fundação de identidade na sua organização.

### <a name="today"></a>Hoje em dia

|Feito?|Item|
|:-|:-|
||Piloto Self - Service palavra-passe de reposição (SSPR) para um grupo|
||Monitorizar os componentes de híbrida com o Azure AD Connect Health|
||Atribuir funções de administrador com privilégios mínimos para a operação|
||Detetar o Shadow IT com o Microsoft Cloud App Security|
||Utilizar o Azure Monitor para recolher registos de dados para análise|

### <a name="next-two-weeks"></a>Próximas duas semanas

|Feito?|Item|
|:-|:-|
||Criar uma aplicação disponível para os seus utilizadores|
||Criar um piloto de aprovisionamento do Azure AD para uma aplicação SaaS de escolha|
||Configurar um servidor de preparação para o Azure AD Connect e mantê-la atualizada|
||Iniciar a migração de aplicações do AD FS para o Azure AD|
||Criar dashboards personalizados para sua liderança e seu dia para dia|

### <a name="next-month"></a>No próximo mês

|Feito?|Item|
|:-|:-|
||Monitorizar a utilização de aplicações para impulsionar informações|
||Piloto acesso remoto seguro a aplicações|
||Certifique-se de que todos os utilizadores estão registados para MFA e o SSPR|
||Ativar a autenticação na nuvem|

### <a name="next-three-months"></a>Três meses

|Feito?|Item|
|:-|:-|
||Ativar a gestão de aplicações self-service|
||Ativar a gestão de grupos self-service|
||Monitorizar a utilização de aplicações para impulsionar informações|
||Compreender os controladores de chamada de suporte|

## <a name="next-steps"></a>Passos Seguintes

Saiba como pode aumentar a sua postura segura, utilizando as capacidades do Azure Active Directory e esta lista de verificação de cinco etapas - [cinco etapas para proteger a sua infraestrutura de identidade](https://aka.ms/securitysteps).

Saiba como as funcionalidades de identidade no Azure AD podem ajudá-lo a acelerar a sua transição para a cloud de gestão regulado, fornecendo as soluções e capacidades que permitem às organizações rapidamente adotar e mover mais o gerenciamento de identidade do tradicional em sistemas no local para o Azure AD - [como o Azure AD proporciona gestão na Cloud regem-se para cargas de trabalho no local](https://aka.ms/cloudgoverned).
