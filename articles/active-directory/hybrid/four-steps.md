---
title: Quatro passos para uma forte fundação de identidade - Azure AD
description: Este tópico descreve quatro passos de identidade híbrida que os clientes podem tomar para construir uma forte base de identidade.
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
ms.openlocfilehash: d3eb98f543e17981be0d5b9ab08fa4e146659b47
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74206783"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Quatro passos para uma forte fundação de identidade com o Azure Ative Directory

Gerir o acesso a apps e dados já não pode contar com as tradicionais estratégias de fronteira de segurança da rede, como redes de perímetro e firewalls devido ao rápido movimento de apps para a nuvem. Agora, as organizações devem confiar na sua solução de identidade para controlar quem e o que tem acesso às aplicações e dados da organização. Mais organizações estão a permitir que os colaboradores tragam os seus próprios dispositivos para trabalhar e utilizem os seus dispositivos a partir de qualquer lugar que possam ligar à Internet. Garantir que esses dispositivos são compatíveis e seguros tornou-se uma consideração importante na solução de identidade que uma organização escolhe implementar. No local de trabalho digital de hoje, [a identidade é o principal plano](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) de controlo de qualquer organização que se desloque para a nuvem.

Ao adotar uma solução de identidade híbrida Azure Ative Directory (Azure AD), as organizações têm acesso a funcionalidades premium que desbloqueiam a produtividade através da automação, delegação, self-service e capacidades de inscrição única. Permite aos seus trabalhadores aceder aos recursos da empresa a partir de onde quer que precisem de fazer o seu trabalho, permitindo que a sua equipa de TI governe esse acesso, garantindo que as pessoas certas têm o acesso certo aos recursos certos para estabelecer uma produtividade segura.

Com base nas nossas aprendizagens, esta lista de verificação de boas práticas irá ajudá-lo a implementar rapidamente ações recomendadas para construir uma *forte* base de identidade na sua organização:

* Conectar-se a apps facilmente
* Estabelecer uma identidade para cada utilizador automaticamente
* Capacitar os seus utilizadores de forma segura
* Operacionalize os seus insights

## <a name="step-1---connect-to-apps-easily"></a>Passo 1 - Ligar-se facilmente a apps

Ao ligar as suas aplicações ao Azure AD, pode melhorar a produtividade e a segurança dos utilizadores finais, permitindo um único sinal de inscrição (SSO) e fazer o fornecimento de utilizadores. Ao gerir as suas aplicações num único local, o Azure AD, pode minimizar as despesas administrativas e alcançar um único ponto de controlo para as suas políticas de segurança e conformidade.

Esta secção cobre as suas opções de gestão do acesso dos utilizadores a apps, permitindo o acesso remoto seguro a aplicações internas, e os benefícios de migrar as suas aplicações para a AD Azure.

### <a name="make-apps-available-to-your-users-seamlessly"></a>Disponibilize as aplicações aos seus utilizadores sem problemas

A Azure AD permite que os administradores [adicionem aplicações](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) à galeria de aplicações da Enterprise no [portal Azure.](https://portal.azure.com/) A adição de aplicações à galeria de aplicações da Enterprise facilita a configuração de aplicações para utilizar o Azure AD como fornecedor de identidade. Permite também gerir o acesso dos utilizadores à aplicação com políticas de Acesso Condicional e configurar o único registo (SSO) para aplicações para que os utilizadores não tenham de introduzir as suas palavras-passe repetidamente e sejam automaticamente assinados tanto no local como nas aplicações baseadas na nuvem.

Uma vez que as aplicações são adicionadas à galeria Azure AD, os utilizadores podem ver aplicações que lhes são atribuídas e pesquisar e solicitar outras aplicações conforme necessário. A Azure AD fornece [vários métodos](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences) para os utilizadores acederem às suas apps:

* Painel de acesso/Minhas Apps
* Iniciador da aplicações do Office 365
* Início de sessão direto em aplicações federadas
* Links de sinalização direto

Para saber mais sobre o acesso dos utilizadores a apps, consulte o **Passo 3 -- Capacitar os Seus Utilizadores** neste artigo.

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Migrar aplicativos dos Serviços da Federação de Diretórios Ativos para a AD Azure

A migração de uma configuração de inscrição única dos Serviços da Federação de Diretórios Ativos (ADFS) para a AD Azure permite capacidades adicionais em segurança, uma gestão mais consistente e colaboração. Para obter os melhores resultados, recomendamos que emigra as suas aplicações de AD FS para Azure AD. Trazer a autenticação e autorização da sua aplicação à Azure AD proporciona-lhe os seguintes benefícios:

* Gerir custos
* Gestão de riscos
* Aumento da produtividade
* Abordar o cumprimento e a governação

Para saber mais, consulte o livro branco do [Diretório Ativo Azure.](https://aka.ms/migrateapps/whitepaper)

### <a name="enable-secure-remote-access-to-apps"></a>Ativar acesso remoto seguro a aplicações

[A Aplicação Azure AD Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) fornece uma solução simples para as organizações publicarem aplicações no local para utilizadores remotos que precisam de acesso a aplicações internas de forma segura. Após uma única entrada na AD Azure, os utilizadores podem aceder a aplicações em nuvem e no local através de URLs externos ou de um portal de aplicações interna.

A Procuração de Aplicações Azure AD oferece os seguintes benefícios:

* Alargar a AD Azure aos recursos no local
  * Segurança e proteção em escala de nuvem
  * Funcionalidades como Acesso Condicional e Autenticação Multi-Factor que são fáceis de ativar
* Não há componentes na rede de perímetro, tais como VPN e soluções tradicionais de procuração inversa
* Não são necessárias ligações de entrada
* Inscrição única (SSO) entre dispositivos, recursos e aplicações na nuvem e no local
* Capacita os utilizadores finais a serem produtivos a qualquer momento e em qualquer lugar

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Descubra shadow IT com a Microsoft Cloud App Security

Nas empresas modernas, os departamentos de TI muitas vezes não têm conhecimento de todas as aplicações em nuvem que são usadas pelos utilizadores para fazer o seu trabalho. Quando os administradores de TI são questionados sobre quantas aplicações na nuvem pensam que os seus funcionários usam, em média dizem 30 ou 40. Na realidade, a média é de mais de 1.000 aplicações separadas que estão a ser usadas pelos colaboradores da sua organização. 80% dos colaboradores usam aplicações não sancionadas que ninguém reviu e pode não estar em conformidade com as suas políticas de segurança e conformidade.

O [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) pode ajudá-lo a identificar aplicações úteis que são populares entre os utilizadores que o IT pode sancionar e adicionar à galeria de aplicações da Enterprise para que os utilizadores beneficiem de capacidades como SSO e Acesso Condicional.

<em>"**Cloud App Security** ajuda-nos a garantir que o nosso povo está a usar corretamente as nossas aplicações cloud e SaaS, de forma a apoiar as políticas de segurança fundamentais que ajudam a proteger a Accenture."</em> --- [John Blasi, Diretor-geral, Segurança da Informação, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

Além de detetar TI sombra, o MCAS também pode determinar o nível de risco das aplicações, impedir o acesso não autorizado a dados corporativos, possíveis fugas de dados e outros riscos de segurança inerentes às aplicações.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>Passo 2 - Estabelecer uma identidade para cada utilizador automaticamente

Reunir diretórios no local e baseados na nuvem numa solução de identidade híbrida Azure AD permitirá reutilizar o seu investimento ativo de diretório no local, aprovisionando as suas identidades existentes na nuvem. A solução sincroniza as identidades no local com a Azure AD, enquanto a TI mantém o Diretório Ativo no local a funcionar com quaisquer soluções de governação existentes como principal fonte de verdade para identidades. A solução de identidade híbrida Azure AD da Microsoft abrange as capacidades no local e baseadas na nuvem, criando uma identidade comum de utilizador para autenticação e autorização a todos os recursos, independentemente da sua localização.

A integração das suas diretórios no local com a AD Azure torna os seus utilizadores mais produtivos e impede os utilizadores de utilizarem várias contas através de apps e serviços, fornecendo uma identidade comum para aceder tanto aos recursos em nuvem como no local. Usar várias contas é um ponto de dor tanto para os utilizadores finais como para as TI. Do ponto de vista do utilizador final, ter várias contas significa ter de se lembrar de várias palavras-passe. Para evitar isso, muitos utilizadores reutilizam a mesma palavra-passe para cada conta, o que é mau do ponto de vista da segurança. Do ponto de vista da TI, a reutilização leva frequentemente a mais resets de passwords e custos de helpdesk juntamente com as reclamações dos utilizadores finais.

O Azure AD Connect é a ferramenta que é usada para sincronizar as suas identidades no local com o Azure AD, que pode ser usado para aceder a aplicações na nuvem. Uma vez que as identidades estejam em Azure AD, podem fornecer a aplicações SaaS como Salesforce ou Concur.

Nesta secção, listamos recomendações para fornecer alta disponibilidade, autenticação moderna para a nuvem, e reduzir a sua pegada no local.

> [!NOTE]
> Se quiser saber mais sobre o Azure AD Connect, veja [o que é Azure AD Connect Sync?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Configurar um servidor de encenação para o Azure AD Connect e mantê-lo atualizado

O Azure AD Connect desempenha um papel fundamental no processo de provisionamento. Se o Sync Server ficar offline por qualquer motivo, as alterações às instalações não serão atualizadas na nuvem e causarão problemas de acesso aos utilizadores. É importante definir uma estratégia de failover que permite que os administradores retomem rapidamente a sincronização após o servidor de sincronização ficar offline.

Para fornecer alta disponibilidade no caso do seu servidor Principal Azure AD Connect ficar offline, recomenda-se que implemente um servidor de [encenação](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server) separado para o Azure AD Connect. A implementação de um servidor permite ao administrador "promover" o servidor de encenação para a produção através de um simples interruptor de configuração. Ter um servidor de espera configurado no modo de preparação também permite testar e implementar novas alterações de configuração e introduzir um novo servidor se desativar o antigo.

> [!TIP]
> O Azure AD Connect é atualizado regularmente. Por isso, recomenda-se vivamente que mantenha a corrente do servidor de encenação de forma a aproveitar as melhorias de desempenho, correções de bugs e novas capacidades que cada nova versão fornece.

### <a name="enable-cloud-authentication"></a>Ativar a autenticação em nuvem

As organizações com diretório ativo no local devem estender o seu diretório ao Azure AD, utilizando o Azure AD Connect e configurar o método de autenticação adequado. [Escolher o método de autenticação correto](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) para a sua organização é o primeiro passo na sua jornada de mover aplicações para a nuvem. É um componente crítico, uma vez que controla o acesso a todos os dados e recursos da nuvem.

O método mais simples e recomendado para permitir a autenticação em nuvem para objetos de diretório no local em Azure AD é permitir a sincronização de [Hash Password](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS). Alternativamente, algumas organizações podem considerar permitir a [autenticação pass-through](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA).

Quer escolha PHS ou PTA, não se esqueça de ativar o [Single Sign-on Seamless](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) para permitir que os utilizadores acedam a aplicações na nuvem sem introduzir constantemente o seu nome de utilizador e palavra-passe na aplicação ao utilizar dispositivos Windows 7 e 8 na sua rede corporativa. Sem um único sinal, os utilizadores devem lembrar-se de senhas específicas da aplicação e assinar em cada aplicação. Da mesma forma, o pessoal de TI precisa de criar e atualizar as contas dos utilizadores para cada aplicação, como o Office 365, Box e Salesforce. Os utilizadores precisam de se lembrar das suas palavras-passe, além de passarem o tempo a assinar em cada aplicação. Fornecer um mecanismo de inscrição única padronizado a toda a empresa é crucial para a melhor experiência do utilizador, redução de risco, capacidade de reporte e governação.

Para organizações que já utilizam AD FS ou outro fornecedor de autenticação no local, mudar-se para a Azure AD uma vez que o seu fornecedor de identidade pode reduzir a complexidade e melhorar a disponibilidade. A menos que tenha casos de uso específico para a utilização da federação, recomendamos que a migração da autenticação federada para PHS e SSO sem emenda ou PTA e Seamless SSO desfrute dos benefícios de uma pegada no local reduzida e da flexibilidade que a nuvem oferece com experiências melhoradas do utilizador. Para mais informações, consulte [Migrate da federação para a sincronização de hash de senha para o Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync).

### <a name="enable-automatic-deprovisioning-of-accounts"></a>Permitir o desprovisionamento automático das contas

Permitir o fornecimento automatizado e o desprovisionamento às suas aplicações é a melhor estratégia para governar o ciclo de vida das identidades em vários sistemas. A Azure AD suporta o fornecimento automatizado e baseado em políticas e o [desprovisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) das contas dos utilizadores a uma variedade de aplicações populares do SaaS, como serviceNow e Salesforce, e outras que implementam o [protocolo SCIM 2.0.](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups) Ao contrário das soluções tradicionais de provisionamento, que requerem código personalizado ou upload manual de ficheiros CSV, o serviço de provisionamento está hospedado na nuvem, e possui conectores pré-integrados que podem ser configurados e geridos através do portal Azure. Um dos principais benefícios da desprovisionamento automática é que ajuda a proteger a sua organização removendo instantaneamente as identidades dos utilizadores das principais aplicações SaaS quando deixam a organização.

Para saber mais sobre o fornecimento automático de conta de utilizador e como funciona, consulte o [Fornecimento de Utilizadores automáticos e o Deprovisionamento às Aplicações SaaS com o Diretório Ativo Do Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

## <a name="step-3---empower-your-users-securely"></a>Passo 3 - Capacitar os seus utilizadores de forma segura

No local de trabalho digital de hoje, é importante equilibrar a segurança com a produtividade. No entanto, os utilizadores finais muitas vezes recuam em medidas de segurança que retardam a sua produtividade e acesso a aplicações na nuvem. Para ajudar a resolver esta questão, a Azure AD fornece capacidades de self-service que permitem aos utilizadores permanecer produtivos, minimizando as despesas administrativas.

Esta secção lista recomendações para remover o atrito da sua organização, capacitando os seus utilizadores enquanto permanece vigilante.

### <a name="enable-self-service-password-reset-for-all-users"></a>Ativar o reset de palavra-passe self-service para todos os utilizadores

O reset de [palavra-passe self-service](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) da Azure (SSPR) oferece um meio simples para os administradores de TI permitirem que os utilizadores resete e desbloqueieas as suas palavras-passe ou contas sem a intervenção do administrador. O sistema inclui relatórios detalhados que controlam quando os utilizadores acedem ao sistema, juntamente com notificações para o alertar quanto a utilizações indevidas ou abusos.

Por padrão, o Azure AD desbloqueia contas quando executa uma reposição de palavra-passe. No entanto, quando ativa a integração do Azure AD Connect [no local,](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration)também tem a opção de separar essas duas operações, que permitem aos utilizadores desbloquear a sua conta sem terem de redefinir a palavra-passe.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>Certifique-se de que todos os utilizadores estão registados para MFA e SSPR

O Azure fornece relatórios que podem ser utilizados por si e pela sua organização para garantir que os utilizadores estão registados para MFA e SSPR. Os utilizadores que não se registaram podem ter de ser educados sobre o processo.

O relatório de inscrições do MFA inclui informações sobre o uso de MFA e [dá-lhe](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting) informações sobre como o MFA está a trabalhar na sua organização. Ter acesso à atividade de inscrição (auditorias e deteções de risco) para a AD Azure é crucial para investigações de resolução de problemas, análises de uso e investigações forenses.

Da mesma forma, o relatório de Gestão de [Passwords self-service](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) pode ser usado para determinar quem tem (ou não) registado para SSPR.

### <a name="self-service-app-management"></a>Gestão de aplicativos self-service

Antes de os seus utilizadores poderem autodescobrir as aplicações a partir do seu painel de acesso, é necessário permitir o acesso à [aplicação self-service](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access) a quaisquer aplicações que deseje permitir aos utilizadores auto-descobrirem e solicitarem acesso. O acesso a aplicações self-service é uma ótima forma de permitir que os utilizadores se autodescubram aplicações e facultem opcionalmente o grupo empresarial a aprovar o acesso a essas aplicações. Pode permitir que o grupo empresarial gere as credenciais atribuídas aos utilizadores para [aplicações](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-password-sso-for-an-azure-ad-gallery-app) de assinatura única de password a partir dos seus painéis de acesso.

### <a name="self-service-group-management"></a>Gestão de grupos self-service

Atribuir utilizadores a aplicações é melhor mapeado quando se utilizam grupos, pois permitem uma grande flexibilidade e capacidade de gestão à escala:

* Baseado em atributos usando a filiação dinâmica do grupo
* Delegação aos proprietários de aplicações

A Azure AD oferece a capacidade de gerir o acesso aos recursos utilizando grupos de segurança e grupos do Office 365. Estes grupos podem ser geridos por um proprietário de grupo que pode aprovar ou negar pedidos de adesão e delegar o controlo da adesão ao grupo. Conhecida como [gestão de grupode self-service](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management), esta funcionalidade poupa tempo ao permitir que os proprietários de grupos que não tenham um papel administrativo para criar e gerir grupos sem terem de contar com administradores para lidar com os seus pedidos.

## <a name="step-4---operationalize-your-insights"></a>Passo 4 - Operacionalize os seus insights

A auditoria e o registo de eventos relacionados com a segurança e alertas relacionados são componentes essenciais de uma estratégia eficiente para garantir que os utilizadores se mantenham produtivos e a sua organização esteja segura. Registos e relatórios de segurança podem ajudar a responder a questões como:

* Estás a usar o que estás a pagar?
* Há algo suspeito ou malicioso a acontecer no meu inquilino?
* Quem foi atingido durante um incidente de segurança?

Registos e relatórios de segurança fornecem-lhe um registo eletrónico de atividades suspeitas e ajudam-no a detetar padrões que podem indicar uma tentativa ou sucesso de penetração externa da rede, e ataques internos. Pode utilizar auditorias para monitorizar a atividade do utilizador, documentar a conformidade regulamentar, fazer análises forenses e muito mais. Os alertas fornecem notificações de eventos de segurança.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>Atribuir funções de administrador menos privilegiadas para operações

Ao pensar na sua abordagem às operações, há alguns níveis de administração a considerar. O primeiro nível coloca o ónus da administração no seu administrador global. Usar sempre o papel de administrador global, pode ser apropriado para empresas mais pequenas. Mas para organizações maiores com pessoal de secretária de ajuda e administradores responsáveis por tarefas específicas, atribuir o papel de administrador global pode ser um risco de segurança, uma vez que fornece a esses indivíduos a capacidade de gerir tarefas que estão acima e além do que devem ser capazes de fazer.

Neste caso, deve considerar o próximo nível de administração. Utilizando o Azure AD, pode designar os utilizadores finais como "administradores limitados" que podem gerir tarefas em funções menos privilegiadas. Por exemplo, pode atribuir ao seu pessoal de secretária de ajuda a função do leitor de [segurança](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader) para lhes fornecer a capacidade de gerir funcionalidades relacionadas com a segurança com acesso apenas para leitura. Ou talvez faça sentido atribuir o papel de administrador de [autenticação](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator) a indivíduos para lhes dar a capacidade de redefinir credenciais não-senhas ou ler e configurar a Azure Service Health.

Para saber mais, consulte [permissões de papel do Administrador no Diretório Ativo azure.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Monitorize componentes híbridos (Azure AD Connect sync, AD FS) utilizando a Azure AD Connect Health

Azure AD Connect e AD FS são componentes críticos que podem potencialmente quebrar a gestão e autenticação do ciclo de vida e, em última análise, levar a interrupções. Por isso, deve implementar a Azure AD Connect Health para monitorização e comunicação destes componentes.

Para saber mais, vá ler [Monitor AD FS utilizando a Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs).

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Utilizar o Monitor Azure para recolher registos de dados para análise

[O Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) é um portal de monitorização unificado para todos os registos da AD Azure, que fornece insights profundos, análises avançadas e aprendizagem automática inteligente. Com o Monitor Azure, pode consumir métricas e registos dentro do portal e através de APIs para ganhar mais visibilidade no estado e desempenho dos seus recursos. Permite uma única experiência de vidro dentro do portal, permitindo uma vasta gama de integrações de produtos através de APIs e opções de exportação de dados que suportam sistemas Tradicionais de SIEM de terceiros. O Azure Monitor também lhe dá a capacidade de configurar regras de alerta para ser notificado ou tomar ações automatizadas em questões que impactam os seus recursos.

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>Crie dashboards personalizados para a sua liderança e o seu dia-a-dia

As organizações que não têm uma solução SIEM podem descarregar o [Power BI Content Pack](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack) para o Azure AD. O pacote de conteúdo power BI contém relatórios pré-construídos para ajudá-lo a entender como os seus utilizadores adotam e utilizam funcionalidades Azure AD, o que lhe permite obter informações sobre todas as atividades dentro do seu diretório. Você também pode criar seu próprio [dashboard personalizado](https://docs.microsoft.com/power-bi/service-dashboards) e partilhar com sua equipe de liderança para reportar sobre atividades do dia-a-dia. Os dashboards são uma ótima maneira de monitorizar o seu negócio e ver todas as suas métricas mais importantes num ápice. As visualizações num dashboard podem ser provenientes de um ou vários conjuntos de dados subjacentes ou de um ou vários relatórios subjacentes. Um dashboard combina dados no local e na cloud ao fornecer uma vista consolidada, independentemente de onde os dados residem.

![Painel personalizado Power BI](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>Compreenda o seu apoio chamar motoristas

Ao implementar uma solução de identidade híbrida, tal como delineado neste artigo, deverá, em última análise, notar uma redução das suas chamadas de apoio. Questões comuns como senhas esquecidas e bloqueios de conta são atenuadas através da implementação da redefinição da palavra-passe do autosserviço do Azure, permitindo ao mesmo tempo o acesso à aplicação self-service permite que os utilizadores se autodescubram e solicitem acesso a aplicações sem depender do seu pessoal de TI.

Se não observar uma redução nas chamadas de apoio, recomendamos que analise os seus controladores de chamada de suporte, numa tentativa de confirmar se o acesso à aplicação SSPR ou self-service foi configurado corretamente ou se existem outros problemas novos que possam ser sistematicamente abordados.

*"Na nossa jornada de transformação digital, precisávamos de um fornecedor de gestão de identidade e acesso fiável para facilitar uma integração perfeita, mas segura, entre nós, parceiros e fornecedores de serviços na nuvem, para um ecossistema eficaz; A Azure AD foi a melhor opção oferecendo-nos as capacidades e visibilidade necessárias que nos permitiram detetar e responder aos riscos."* --- [Yazan Almasri, Diretor Global de Segurança da Informação, Aramex](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>Monitorize o seu uso de apps para impulsionar insights

Além de descobrir o Shadow IT, monitorizar o uso de aplicações em toda a sua organização usando [o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) pode ajudar a sua organização à medida que se move para tirar o máximo partido da promessa de aplicações na nuvem. Pode ajudar a manter o controlo dos seus ativos através de uma melhor visibilidade na atividade e aumentar a proteção de dados críticos através de aplicações na nuvem. Monitorizar o uso de aplicações na sua organização utilizando MCAS pode ajudá-lo a responder às seguintes questões:

* Que aplicações não autorizadas estão os empregados a usar para armazenar dados?
* Onde e quando é que os dados sensíveis estão a ser armazenados na nuvem?
* Quem está a aceder a dados sensíveis na nuvem?

*"Com a Cloud App Security, podemos detetar rapidamente anomalias e tomar medidas."* --- [Eric LePenske, Gerente Sénior, Segurança da Informação, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>Resumo

Existem muitos aspetos para implementar uma solução de Identidade Híbrida, mas esta lista de verificação de quatro etapas irá ajudá-lo a realizar rapidamente uma infraestrutura de identidade que permitirá aos utilizadores ser mais produtivos e seguros.

* Conectar-se a apps facilmente
* Estabelecer uma identidade para cada utilizador automaticamente
* Capacitar os seus utilizadores de forma segura
* Operacionalize os seus insights

Esperamos que este documento seja um roteiro útil para estabelecer uma forte base de identidade para a sua organização.

## <a name="identity-checklist"></a>Lista de verificação de identidade

Recomendamos que imprima a seguinte lista de verificação para referência no início da sua viagem a uma fundação de identidade mais sólida na sua organização.

### <a name="today"></a>Hoje

|Feito?|Item|
|:-|:-|
||Reset de palavra-passe de autosserviço piloto (SSPR) para um grupo|
||Monitorize componentes híbridos usando a Azure AD Connect Health|
||Atribuir funções de administrador menos privilegiadas para o funcionamento|
||Descubra shadow IT com a Microsoft Cloud App Security|
||Utilizar o Monitor Azure para recolher registos de dados para análise|

### <a name="next-two-weeks"></a>Próximas duas semanas

|Feito?|Item|
|:-|:-|
||Disponibilize uma aplicação para os seus utilizadores|
||Pilot Azure AD provisioning para uma app SaaS de eleição|
||Configurar um servidor de encenação para Azure AD Connect e mantê-lo atualizado|
||Comece a migrar aplicativos da ADFS para a AD Azure|
||Crie dashboards personalizados para a sua liderança e o seu dia-a-dia|

### <a name="next-month"></a>No próximo mês

|Feito?|Item|
|:-|:-|
||Monitorize o seu uso de apps para impulsionar insights|
||Piloto segura acesso remoto a apps|
||Certifique-se de que todos os utilizadores estão registados para MFA e SSPR|
||Ativar a autenticação em nuvem|

### <a name="next-three-months"></a>Próximos três meses

|Feito?|Item|
|:-|:-|
||Ativar a gestão de aplicações self-service|
||Ativar a gestão do grupo de self-service|
||Monitorize o seu uso de apps para impulsionar insights|
||Compreenda o seu apoio chamar motoristas|

## <a name="next-steps"></a>Passos seguintes

Saiba como pode aumentar a sua postura segura utilizando as capacidades do Azure Ative Directory e esta lista de verificação de cinco etapas - [Cinco passos para garantir a sua infraestrutura de identidade](https://aka.ms/securitysteps).

Saiba como as funcionalidades de identidade em Azure AD podem ajudá-lo a acelerar a sua transição para a gestão governada pela nuvem, fornecendo as soluções e capacidades que permitem às organizações adotar rapidamente e mover mais da sua gestão de identidade dos sistemas tradicionais no local para O AD Azure - [Como a Azure AD fornece gestão governada em nuvem para cargas de trabalho no local.](https://aka.ms/cloudgoverned)
