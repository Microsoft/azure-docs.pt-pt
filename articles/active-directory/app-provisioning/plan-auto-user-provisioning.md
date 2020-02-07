---
title: Planejar uma implantação de provisionamento de usuário automático para Azure Active Directory
description: Diretrizes para planejar e executar o provisionamento automático de usuário
services: active-directory
author: martincoetzer
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/17/2019
ms.author: martinco
ms.reviewer: arvindha
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0860c2e7b5be686ad5577436f41b321164853a0
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066515"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>Planear uma implementação de aprovisionamento automático de utilizadores

Muitas organizações contam com aplicativos SaaS (software como serviço), como ServiceNow, Zscaler e margem de atraso para produtividade do usuário final. Historicamente, a equipe de ti conta com métodos de provisionamento manual, como carregar arquivos CSV, ou usar scripts personalizados para gerenciar identidades de usuário com segurança em cada aplicativo SaaS. Esses processos são propensos a erros, inseguros e difíceis de gerenciar.

O provisionamento automático de usuário do Azure Active Directory (AD do Azure) simplifica esse processo ao automatizar com segurança a criação, a manutenção e a remoção de identidades de usuário em aplicativos SaaS com base em regras de negócio. Essa automação permite que você dimensione efetivamente seus sistemas de gerenciamento de identidades tanto em ambientes híbridos quanto em nuvem quanto você expande sua dependência em soluções baseadas em nuvem.

Consulte o [fornecimento e o desprovisionamento de utilizadores da Automatização para aplicações SaaS com o Diretório Ativo Azure](../app-provisioning/user-provisioning.md) para melhor compreender a funcionalidade.

## <a name="learn"></a>Saiba mais

O provisionamento de usuário cria uma base para o controle de identidade contínuo e aprimora a qualidade dos processos de negócios que dependem de dados de identidade autoritativa.

### <a name="key-benefits"></a>Principais vantagens

Os principais benefícios de habilitar o provisionamento automático de usuário são:

* **Aumento da produtividade.** Você pode gerenciar identidades de usuário em aplicativos SaaS com uma única interface de gerenciamento de provisionamento de usuário. Essa interface tem um único conjunto de políticas de provisionamento.

* **Gerir o risco.** Você pode aumentar a segurança automatizando as alterações com base no status do funcionário ou em associações de grupo que definem funções e/ou acesso.

* Abordar o **cumprimento e a governação.** O Azure AD dá suporte a logs de auditoria nativos para cada solicitação de provisionamento de usuário. As solicitações são executadas nos sistemas de origem e de destino. Isso permite que você controle quem tem acesso a aplicativos de uma única tela.

* **Reduzir o custo.** O provisionamento automático de usuário reduz os custos, evitando ineficiências e erros humanos associados ao provisionamento manual. Ele reduz a necessidade de soluções de provisionamento de usuário, scripts e logs de auditoria desenvolvidos de fato personalizado.

### <a name="licensing"></a>Licenciamento

O Azure AD fornece integração de autoatendimento de qualquer aplicativo usando modelos fornecidos no menu da Galeria de aplicativos. Para obter uma lista completa dos requisitos de licença, consulte a página de [licenciamento da Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

#### <a name="application-licensing"></a>Licenciamento de aplicativos

Você precisará das licenças apropriadas para os aplicativos que deseja provisionar automaticamente. Discuta com os proprietários do aplicativo se os usuários atribuídos ao aplicativo têm as licenças apropriadas para suas funções de aplicativo. Se o Azure AD gerencia o provisionamento automático com base em funções, as funções atribuídas no Azure AD devem ser alinhadas às licenças de aplicativo. Licenças incorretas de Propriedade do aplicativo podem levar a erros durante o provisionamento/atualização de um usuário.

### <a name="terms"></a>Termos

Este artigo usa os seguintes termos:

* Operações CRUD – ações executadas em contas de usuário: criar, ler, atualizar, excluir.

* SSO (logon único) – a capacidade de um usuário entrar uma vez e acessar todos os aplicativos habilitados para SSO. No contexto do provisionamento do usuário, o SSO é um resultado dos usuários que têm uma única conta para acessar todos os sistemas que usam o provisionamento automático de usuário.

* Sistema de origem-o repositório de usuários do qual o Azure AD provisiona. O Azure AD é o sistema de origem para a maioria dos conectores de provisionamento previamente integrados. No entanto, há algumas exceções para aplicativos de nuvem, como SAP, workday e AWS. Por exemplo, consulte o [fornecimento do Utilizador do Dia de Trabalho para o AD](../saas-apps/workday-inbound-tutorial.md).

* Sistema de destino – o repositório de usuários ao qual o Azure AD provisiona. O sistema de destino é normalmente um aplicativo SaaS, como ServiceNow, Zscaler e margem de atraso. O sistema de destino também pode ser um sistema local, como o AD.

* [Sistema de Gestão de Identidade de Domínio Transversal (SCIM)](https://aka.ms/scimoverview) - Um padrão aberto que permite a automatização do fornecimento de utilizadores. O SCIM comunica dados de identidade do usuário entre provedores de identidade, como a Microsoft, e provedores de serviço como o Salesforce ou outros aplicativos SaaS que exigem informações de identidade do usuário.

### <a name="training-resources"></a>Recursos de treinamento

| Recursos| Link e descrição |
| - | - |
| Webinars a pedido| [Gerencie as suas Aplicações Empresariais com a Azure AD](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>Saiba como o Azure AD pode ajudá-lo a obter o SSO para seus aplicativos SaaS corporativos e as práticas recomendadas para controlar o acesso. |
| Vídeos| [O que é o fornecimento de utilizadores no Diretório Ative Azure?](https://youtu.be/_ZjARPpI6NI) <br> [Como implementar o fornecimento de utilizadores no Diretório Ative Azure?](https://youtu.be/pKzyts6kfrw) <br> [Integração salesforce com Azure AD: Como automatizar o fornecimento de utilizadores](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| Cursos online| SkillUp Online: [Identidades de Gestão](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> Saiba como integrar o Azure AD com muitos aplicativos SaaS e proteger o acesso do usuário a esses aplicativos. |
| Livros| [Autenticação Moderna com Diretório Ativo Azure para Aplicações Web (Referência de Desenvolvimento) 1ª Edição](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0).  <br> Este é um guia autoritativo e aprofundado para a criação de soluções de autenticação Active Directory para esses novos ambientes. |
| Tutoriais| Consulte a [lista de tutoriais sobre como integrar aplicações SaaS com a Azure AD](../saas-apps/tutorial-list.md). |
| FAQ| [Perguntas frequentes](../app-provisioning/user-provisioning.md) sobre o fornecimento automatizado de utilizadores |

### <a name="solution-architectures"></a>Soluções arquiteturas

O serviço de provisionamento do Azure AD provisiona usuários para aplicativos SaaS e outros sistemas conectando-se aos pontos de extremidade da API de gerenciamento de usuários fornecidos por cada fornecedor de aplicativos. Esses pontos de extremidade da API de gerenciamento de usuários permitem que o Azure AD crie, atualize e remova programaticamente usuários.

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>Provisionamento automático de usuário para empresas híbridas

Neste exemplo, os usuários e grupos são criados em um banco de dados de RH conectado a um diretório local. O serviço de provisionamento do Azure AD gerencia o provisionamento automático de usuário para os aplicativos SaaS de destino.

 ![provisionamento de usuário](./media/plan-auto-user-provisioning/hybridprovisioning.png)

**Descrição do fluxo de trabalho:**

1. Os usuários/grupos são criados em um aplicativo/sistema de RH local, como o SAP. 

1. O **agente Azure AD Connect** executa sincronizações programadas de identidades (utilizadores e grupos) desde a AD local até à AD Azure.

1. O serviço de **provisionamento da AD Azure** inicia um [ciclo inicial](../app-provisioning/user-provisioning.md) contra o sistema de origem e o sistema de destino. 

1. O serviço de **provisionamento de AD Azure** questiona o sistema de origem para quaisquer utilizadores e grupos alterados desde o ciclo inicial, e empurra alterações nos [ciclos incrementais.](../app-provisioning/user-provisioning.md)

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>Provisionamento automático de usuário para empresas somente em nuvem

Neste exemplo, a criação de usuário ocorre no Azure AD e o serviço de provisionamento do Azure AD gerencia o provisionamento automático de usuário para os aplicativos de destino (SaaS).

![Imagem 2](./media/plan-auto-user-provisioning/cloudprovisioning.png)

**Descrição do fluxo de trabalho:**

1. Usuários/grupos são criados no Azure AD.

1. O serviço de **provisionamento da AD Azure** inicia um [ciclo inicial](../app-provisioning/user-provisioning.md) contra o sistema de origem e o sistema de destino. 

1. O serviço de **provisionamento da Azure AD** questiona o sistema de origem para quaisquer utilizadores e grupos atualizados desde o ciclo inicial, e executa quaisquer [ciclos incrementais.](../app-provisioning/user-provisioning.md)

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>Provisionamento automático de usuário para aplicativos de RH na nuvem 

Neste exemplo, os usuários e ou grupos são criados em um aplicativo de RH de nuvem como o workday e o SuccessFactors. O serviço de provisionamento do Azure AD e o agente de provisionamento do Azure AD Connect provisiona os dados do usuário do locatário do aplicativo de RH na nuvem para o AD. Depois que as contas são atualizadas no AD, elas são sincronizadas com o Azure AD por meio do Azure AD Connect, e os endereços de email e os atributos de nome de usuário podem ser gravados no locatário do aplicativo de RH na nuvem.

![Imagem 2](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  **A equipa de RH** realiza as transações no inquilino da aplicação de RH em nuvem.
2.  O serviço de provisionamento da **Azure AD** executa os ciclos programados a partir do inquilino da aplicação de aplicação de RH em nuvem e identifica alterações que precisam de ser processadas para sincronização com AD.
3.  O serviço de **provisionamento da Azure AD Invoca** o agente de provisionamento Azure AD Connect com uma carga útil de pedido contendo operações de criação/atualização/ativação/desativação da conta AD.
4.  O agente de **provisionamento Azure AD Connect** utiliza uma conta de serviço para gerir os dados da conta AD.
5.  **O Azure AD Connect** executa a delta sync para puxar atualizações em AD.
6.  As atualizações **da AD** estão sincronizadas com a AD Azure. 
7.  O serviço de provisionamento de **AD Azure** reescreve o atributo de e-mail e o nome de utilizador de Azure AD para o inquilino da aplicação de aplicações de RH em nuvem.

## <a name="plan-the-deployment-project"></a>Planejar o projeto de implantação

Considere suas necessidades organizacionais para determinar a estratégia para implantar o provisionamento de usuários em seu ambiente.

### <a name="engage-the-right-stakeholders"></a>Envolva os participantes certos

Quando os projetos de tecnologia falham, normalmente é devido a expectativas incompatíveis com o impacto, os resultados e as responsabilidades. Para evitar estas armadilhas, [certifique-se](https://aka.ms/deploymentplans) de que está a envolver as partes interessadas certas e que as partes interessadas no projeto são bem compreendidas documentando as partes interessadas e as suas entradas e responsabilidades do projeto.

### <a name="plan-communications"></a>Plan communications

A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunique-se de forma proativa com seus usuários como sua experiência será alterada, quando ele será alterado e como obter suporte se eles tiverem problemas.

### <a name="plan-a-pilot"></a>Planejar um piloto

Recomendamos que a configuração inicial do provisionamento de usuário automático esteja em um ambiente de teste com um pequeno subconjunto de usuários antes de dimensioná-lo para todos os usuários em produção. Consulte [as melhores práticas](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) para dirigir um piloto.

#### <a name="best-practices-for-a-pilot"></a>Práticas recomendadas para um piloto  

Um piloto permite que você teste com um pequeno grupo antes de implantar um recurso para todos. Certifique-se de que, como parte de seu teste, cada caso de uso em sua organização seja totalmente testado.

Na sua primeira onda, destinada a ti, usabilidade e outros usuários apropriados que podem testar e fornecer comentários. Use esses comentários para desenvolver ainda mais as comunicações e as instruções que você envia para os usuários e para fornecer informações sobre os tipos de problemas que sua equipe de suporte pode ver.

Amplie a distribuição para grupos maiores de usuários aumentando o escopo dos grupos de destino. Isto pode ser feito através da [adesão dinâmica do grupo](../users-groups-roles/groups-dynamic-membership.md), ou adicionando manualmente os utilizadores ao(s) grupo(s) visado.

## <a name="plan-application-connections-and-administration"></a>Planejar conexões e administração de aplicativos

Use o portal do AD do Azure para exibir e gerenciar todos os aplicativos que dão suporte ao provisionamento. Veja [encontrar as suas aplicações no portal.](../app-provisioning/configure-automatic-user-provisioning-portal.md)

### <a name="determine-the-type-of-connector-to-use"></a>Determinar o tipo de conector a ser usado

As etapas reais necessárias para habilitar e configurar o provisionamento automático variam de acordo com o aplicativo. Se a aplicação que pretende fornecer automaticamente está listada na galeria de [aplicações Azure AD SaaS,](../saas-apps/tutorial-list.md)então deve selecionar o [tutorial de integração específico](../saas-apps/tutorial-list.md) da aplicação para configurar o seu conector de fornecimento de utilizador pré-integrado.

Caso contrário, siga as etapas abaixo:

1. [Crie um pedido](../develop/howto-app-gallery-listing.md) para um conector de fornecimento de utilizador pré-integrado. Nossa equipe trabalhará com você e o desenvolvedor de aplicativos para integrar seu aplicativo à nossa plataforma se ele der suporte a SCIM.

1. Utilize o suporte genérico de fornecimento de utilizadores [BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) para a aplicação. Esse é um requisito para o Azure AD provisionar usuários para o aplicativo sem um conector de provisionamento previamente integrado.

1. Se a aplicação for capaz de utilizar o conector BYOA SCIM, consulte o tutorial de [integração BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) para configurar o conector BYOA SCIM para a aplicação.

Para mais informações, consulte que aplicações e sistemas posso utilizar com o fornecimento automático de utilizadores da [Azure AD?](../app-provisioning/user-provisioning.md)

### <a name="collect-information-to-authorize-application-access"></a>Coletar informações para autorizar o acesso ao aplicativo

A configuração do provisionamento automático de usuário é um processo por aplicativo. Para cada aplicação, é necessário fornecer [credenciais](../app-provisioning/configure-automatic-user-provisioning-portal.md) de administrador para se ligar ao ponto final de gestão do utilizador do sistema alvo.

A imagem abaixo mostra uma versão das credenciais de administrador necessárias:

![Tela de provisionamento para gerenciar as configurações de provisionamento de conta de usuário](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

Embora alguns aplicativos exijam o nome de usuário e a senha do administrador, outros podem exigir um token de portador.

## <a name="plan-user-and-group-provisioning"></a>Planejar o provisionamento de usuários e grupos

Se ativar o fornecimento de utilizadores para aplicações empresariais, o [portal Azure](https://portal.azure.com/) controla os seus valores de atributo através do mapeamento de atributos.

### <a name="determine-operations-for-each-saas-app"></a>Determinar as operações para cada aplicativo SaaS

Cada aplicativo pode ter atributos exclusivos de usuário ou grupo que devem ser mapeados para os atributos no Azure AD. O aplicativo pode ter apenas um subconjunto de operações CRUD disponíveis.

Para cada aplicativo, documente as seguintes informações:

* As operações de provisionamento CRUD a serem executadas nos objetos de usuário e grupo para os sistemas de destino. Por exemplo, cada proprietário de negócios de aplicativo SaaS pode não querer todas as operações possíveis.

* Atributos disponíveis no sistema de origem

* Atributos disponíveis no sistema de destino

* Mapeamento de atributos entre sistemas.

### <a name="choose-which-users-and-groups-to-provision"></a>Escolha quais usuários e grupos provisionar

Antes de implementar o provisionamento automático de usuário, você deve determinar os usuários e grupos a serem provisionados para seu aplicativo.

* Utilize filtros de [deteção](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) para definir regras baseadas em atributos que determinam quais os utilizadores que são provisionados a uma aplicação.

* Em seguida, utilize [as atribuições](../manage-apps/assign-user-or-group-access-portal.md) de utilizador e grupo conforme necessário para filtrar adicionalmente.

### <a name="define-user-and-group-attribute-mapping"></a>Definir mapeamento de atributo de usuário e grupo

Para implementar o provisionamento automático de usuário, você precisa definir os atributos de usuário e grupo que são necessários para o aplicativo. Há um conjunto pré-configurado de atributos e [mapeamento de atributos](../app-provisioning/configure-automatic-user-provisioning-portal.md) entre objetos de utilizador da AD Azure e objetos de utilizador de cada aplicação SaaS. Nem todos os aplicativos SaaS habilitam atributos de grupo.

A AD Azure suporta através de mapeamento direto de atributo-a-atributo, fornecendo valores [constantes, ou expressões de escrita para mapeamento de atributos](../app-provisioning/functions-for-customizing-application-data.md). Essa flexibilidade lhe dá um controle fino do que será populado no atributo do sistema de destino. Pode utilizar o [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) e o Graph Explorer para exportar mapeamentos e esquemas para um ficheiro JSON e importá-lo de volta para AD Azure.

Para mais informações, consulte personalizar o [fornecimento de atributos de atributos para aplicações SaaS no Diretório Ativo Azure](../app-provisioning/customize-application-attributes.md).

### <a name="special-considerations-for-user-provisioning"></a>Considerações especiais para provisionamento de usuário

Considere o seguinte para reduzir os problemas após a implantação:

* Verifique se os atributos usados para mapear objetos de usuário/grupo entre os aplicativos de origem e de destino são resilientes. Eles não devem fazer com que os usuários/grupos sejam provisionados incorretamente se os atributos forem alterados (por exemplo, um usuário se mover para uma parte diferente da empresa).

* Os aplicativos podem ter restrições e/ou requisitos específicos que precisam ser atendidos para que o provisionamento de usuário funcione corretamente. Por exemplo, a margem de atraso trunca valores para determinados atributos. Consulte [tutoriais automáticos](../saas-apps/tutorial-list.md) de fornecimento de utilizadores específicos de cada aplicação.

* Confirme a consistência do esquema entre os sistemas de origem e de destino. Problemas comuns incluem atributos como UPN ou email não correspondentes. Por exemplo, a UPN em Azure AD definida como *john_smith@contoso.com* e na app, é *jsmith@contoso.com* . Para mais informações, consulte o Utilizador e o [grupo de referência do esquema](../app-provisioning/use-scim-to-provision-users-and-groups.md).

## <a name="plan-testing-and-security"></a>Teste e segurança de planos

Em cada estágio de sua implantação, verifique se você está testando se os resultados estão conforme o esperado e auditando os ciclos de provisionamento.

### <a name="plan-testing"></a>Teste de plano

Depois de configurar o provisionamento automático de usuário para o aplicativo, você executará casos de teste para verificar se essa solução atende aos requisitos da sua organização.

| Cenários| Resultados esperados |
| - | - |
| O usuário é adicionado a um grupo atribuído ao sistema de destino | O objeto de usuário é provisionado no sistema de destino. <br>O usuário pode entrar no sistema de destino e executar as ações desejadas. |
| O usuário é removido de um grupo atribuído ao sistema de destino | O objeto de usuário é desprovisionado no sistema de destino.<br>O usuário não pode entrar no sistema de destino. |
| As informações do usuário são atualizadas no Azure AD por qualquer método | Os atributos de usuário atualizados são refletidos no sistema de destino após um ciclo incremental |
| O usuário está fora do escopo | O objeto de usuário está desabilitado ou excluído. <br>Nota: Este comportamento é ultrapassado para [o fornecimento](skip-out-of-scope-deletions.md)do Dia de Trabalho . |

### <a name="plan-security"></a>Planear a segurança

É comum que uma revisão de segurança seja exigida como parte de uma implantação. Se necessitar de uma revisão de segurança, consulte os muitos [whitepapers](https://www.microsoft.com/download/details.aspx?id=36391) da AD Azure que fornecem uma visão geral da identidade como serviço.

### <a name="plan-rollback"></a>Reversão do plano

Se a implementação automática de provisionamento de usuário não funcionar conforme desejado no ambiente de produção, as seguintes etapas de reversão abaixo podem ajudá-lo a reverter para um estado válido anterior conhecido:

1. Reveja o [relatório sumário](../app-provisioning/check-status-user-account-provisioning.md) de fornecimento e os [registos de fornecimento](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) para determinar as operações incorretas que ocorreram nos utilizadores e/ou grupos afetados.

1. Use o provisionamento de logs de auditoria para determinar o último estado válido conhecido dos usuários e/ou grupos afetados. Examine também os sistemas de origem (Azure AD ou AD).

1. Trabalhe com o proprietário do aplicativo para atualizar os usuários e/ou grupos afetados diretamente no aplicativo usando os últimos valores de estado bom conhecidos.

## <a name="deploy-automatic-user-provisioning-service"></a>Implantar o serviço de provisionamento automático de usuário

Escolha as etapas que se alinham aos seus requisitos de solução.

### <a name="prepare-for-the-initial-cycle"></a>Preparar para o ciclo inicial

Quando o serviço de provisionamento do Azure AD é executado pela primeira vez, o ciclo inicial em relação ao sistema de origem e aos sistemas de destino cria um instantâneo de todos os objetos de usuário para cada sistema de destino.

Ao habilitar o provisionamento automático para um aplicativo, o ciclo inicial pode levar de 20 minutos a várias horas. A duração depende do tamanho do diretório do Azure AD e do número de usuários no escopo para provisionamento. Ver [Como melhorar o desempenho do provisionamento.](../app-provisioning/application-provisioning-when-will-provisioning-finish.md)

O serviço de provisionamento armazena o estado de ambos os sistemas após o ciclo inicial, melhorando o desempenho de ciclos incrementais subsequentes.

### <a name="configure-automatic-user-provisioning"></a>Configurar o aprovisionamento automático de utilizadores

Utilize o [portal Azure](https://portal.azure.com/) para gerir o fornecimento automático de conta de utilizador e o desprovisionamento para aplicações que o suportem. Seguir os passos em [Como configurar o fornecimento automático a uma aplicação?](../app-provisioning/user-provisioning.md)

O serviço de fornecimento de utilizadores AD Azure também pode ser configurado e gerido utilizando o [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).

## <a name="manage-automatic-user-provisioning"></a>Gerenciar o provisionamento automático de usuário

Agora que você implantou o, precisa gerenciar a solução.

### <a name="monitor-user-provisioning-operation-health"></a>Monitorar a integridade da operação de provisionamento do usuário

Após um [ciclo inicial](../app-provisioning/user-provisioning.md)bem sucedido, o serviço de provisionamento de AD Azure executará atualizações incrementais indefinidamente, em intervalos específicos de cada aplicação, até que um dos seguintes eventos ocorra:

* O serviço é interrompido manualmente e um novo ciclo inicial é acionado utilizando o [portal Azure,](https://portal.azure.com/)ou utilizando o comando API do [Microsoft Graph.](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

* Um novo ciclo inicial é disparado por uma alteração nos mapeamentos de atributo ou em filtros de escopo.

* O processo de provisionamento entra em quarentena devido a uma alta taxa de erros e permanece em quarentena por mais de quatro semanas quando ela será desabilitada automaticamente.

Para rever estes eventos, e todas as outras atividades realizadas pelo serviço de provisionamento, consulte os registos de [provisionamento](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)da Azure AD.

Para compreender quanto tempo demoram os ciclos de provisionamento e monitorizar o progresso do trabalho de provisionamento, pode [verificar o estado do fornecimento de utilizadores](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).

### <a name="gain-insights-from-reports"></a>Obter informações de relatórios

A Azure AD pode fornecer [informações adicionais](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) sobre o fornecimento de utilização e saúde operacional do utilizador da sua organização através de registos e relatórios de auditoria.

Os administradores devem verificar o relatório de Resumo de provisionamento para monitorar a integridade operacional do trabalho de provisionamento. Todas as atividades executadas pelo serviço de provisionamento são registradas nos logs de auditoria do Azure AD. Ver [Tutorial: Relatório sobre o fornecimento automático de conta](../app-provisioning/check-status-user-account-provisioning.md)de utilizador .

Recomendamos que você assuma a propriedade e consuma esses relatórios em uma cadência que atenda aos requisitos da sua organização. O Azure AD retém a maioria dos dados de auditoria por 30 dias.

### <a name="troubleshoot"></a>Resolver Problemas

Consulte os links a seguir para solucionar quaisquer problemas que possam ser reativados durante o provisionamento:

* [Problema de configuração do fornecimento de utilizadores a uma aplicação da Galeria AD Azure](../app-provisioning/application-provisioning-config-problem.md)

* [Sincronizar um atributo do seu Diretório Ativo no local para a AD Azure para o fornecimento a uma aplicação](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)

* [O fornecimento de utilizadores a uma aplicação da Galeria AD Azure está a demorar horas ou mais](../app-provisioning/application-provisioning-when-will-provisioning-finish.md)

* [Credenciais de administrador de poupança de problemas ao configurar o fornecimento de utilizadores a uma aplicação da Galeria de Diretórios Ativos Do Azure](../app-provisioning/application-provisioning-config-problem-storage-limit.md)

* [Nenhum utilizador está a ser provisionado para uma aplicação da Galeria AD Azure](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

* [Conjunto errado de utilizadores estão sendo provisionados para uma aplicação da Galeria AD Azure](../app-provisioning/application-provisioning-config-problem-wrong-users-provisioned.md)

### <a name="helpful-documentation"></a>Documentação útil

* [Expressões de escrita para mapeamento de atributos](../app-provisioning/functions-for-customizing-application-data.md)

* [Visão geral da API de sincronização da AD Azure](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

* [Sem a eliminação das contas de utilizador que ficam fora de âmbito](skip-out-of-scope-deletions.md)

* [Agente de provisionamento de ligação Azure AD: histórico de lançamento de versão](provisioning-agent-release-version-history.md)

#### <a name="resources"></a>Recursos

* [Fornecer feedback do produto](https://feedback.azure.com/forums/169401-azure-active-directory)

* [Mantenha-se atualizado sobre o que há de novo com a Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

* [Stack overflow Azure AD Forum](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="next-steps"></a>Passos seguintes
* [Configurar o fornecimento automático de utilizadores](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [Exportar ou importar a sua configuração de provisionamento utilizando a Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md)

* [Expressões de escrita para mapeamento de atributos no diretório Azure Ative](../app-provisioning/functions-for-customizing-application-data.md)
