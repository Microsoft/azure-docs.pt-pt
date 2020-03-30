---
title: Planeie uma implementação automática de fornecimento de utilizadores para o Diretório Ativo do Azure
description: Orientação para planeamento e execução automática de fornecimento de utilizadores
services: active-directory
author: martincoetzer
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/17/2019
ms.author: martinco
ms.reviewer: arvindha
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d2f284fddfc49632e467adbf5877856b40a81dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522415"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>Planear uma implementação de aprovisionamento automático de utilizadores

Muitas organizações dependem de software como um serviço (SaaS) aplicações como ServiceNow, Zscaler e Slack para a produtividade do utilizador final. Historicamente, os funcionários de TI confiaram em métodos de provisionamento manual, tais como o upload de ficheiros CSV, ou usando scripts personalizados para gerir de forma segura as identidades dos utilizadores em cada aplicação SaaS. Estes processos são propensos a erros, inseguros e difíceis de gerir.

O fornecimento automático de utilizadores do Azure Ative Directory (Azure AD) simplifica este processo automatizando de forma segura a criação, manutenção e remoção das identidades dos utilizadores nas aplicações SaaS com base nas regras empresariais. Esta automatização permite-lhe escalar eficazmente os seus sistemas de gestão de identidade em ambientes apenas em nuvem e híbridos à medida que expande a sua dependência de soluções baseadas na nuvem.

Consulte o [fornecimento e o desprovisionamento de utilizadores da Automatização para aplicações SaaS com o Diretório Ativo Azure](../app-provisioning/user-provisioning.md) para melhor compreender a funcionalidade.

## <a name="learn"></a>Saiba mais

O fornecimento de utilizadores cria uma base para a governação de identidade em curso e melhora a qualidade dos processos empresariais que dependem de dados de identidade autoritários.

### <a name="key-benefits"></a>Principais vantagens

Os principais benefícios de permitir o fornecimento automático de utilizadores são:

* **Aumento da produtividade.** Pode gerir as identidades dos utilizadores através das aplicações SaaS com uma única interface de gestão de fornecimento de utilizadores. Esta interface tem um único conjunto de políticas de provisionamento.

* **Gerir o risco.** Pode aumentar a segurança automatizando alterações com base no estatuto do colaborador ou membros do grupo que definem funções e/ou acesso.

* Abordar o **cumprimento e a governação.** A Azure AD suporta registos de auditoria nativas para cada pedido de fornecimento de utilizadores. Os pedidos são executados tanto nos sistemas de origem como nos sistemas-alvo. Isto permite-lhe rastrear quem tem acesso a aplicações a partir de um único ecrã.

* **Reduzir o custo.** O fornecimento automático de utilizadores reduz os custos evitando ineficiências e erros humanos associados ao fornecimento manual. Reduz a necessidade de soluções de fornecimento de utilizadores, scripts e registos de auditoria desenvolvidos sob medida.

### <a name="licensing"></a>Licenciamento

A Azure AD proporciona integração de autosserviço de qualquer aplicação utilizando modelos fornecidos no menu da galeria de aplicações. Para obter uma lista completa dos requisitos de licença, consulte a página de [licenciamento da Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

#### <a name="application-licensing"></a>Licenciamento de candidatura

Necessitará das licenças adequadas para o(s) pedido(s) que pretende fornecer automaticamente. Discuta com os proprietários da aplicação se os utilizadores atribuídos à aplicação têm as licenças adequadas para as suas funções de aplicação. Se a Azure AD gerir o fornecimento automático com base em funções, as funções atribuídas em Azure AD devem alinhar-se com as licenças de aplicação. As licenças incorretas detidas na aplicação podem levar a erros durante o fornecimento/atualização de um utilizador.

### <a name="terms"></a>Termos

Este artigo utiliza os seguintes termos:

* Operações CRUD - Ações tomadas nas contas dos utilizadores: Criar, Ler, Atualizar, Eliminar.

* Inscrição única (SSO) - A capacidade de um utilizador iniciar sessão uma vez e aceder a todas as aplicações ativadas pelo SSO. No contexto do fornecimento de utilizadores, o SSO resulta de os utilizadores terem uma única conta para acederem a todos os sistemas que utilizam o fornecimento automático de utilizadores.

* Sistema de origem - O repositório de utilizadores a partir do fornecimento da AD Azure. A Azure AD é o sistema de origem para a maioria dos conectores de provisionamento pré-integrados. No entanto, existem algumas exceções para aplicações em nuvem como SAP, Workday e AWS. Por exemplo, consulte o [fornecimento do Utilizador do Dia de Trabalho para o AD](../saas-apps/workday-inbound-tutorial.md).

* Sistema de destino - O repositório de utilizadores a que o Azure AD prevê. O sistema Target é tipicamente uma aplicação SaaS como ServiceNow, Zscaler e Slack. O sistema alvo também pode ser um sistema no local, como o AD.

* [Sistema de Gestão de Identidade de Domínio Transversal (SCIM)](https://aka.ms/scimoverview) - Um padrão aberto que permite a automatização do fornecimento de utilizadores. O SCIM comunica dados de identidade de utilizadores entre fornecedores de identidade, como a Microsoft, e fornecedores de serviços como o Salesforce ou outras aplicações SaaS que requerem informações sobre a identidade do utilizador.

### <a name="training-resources"></a>Recursos de formação

| Recursos| Link e Descrição |
| - | - |
| Webinars a pedido| [Gerencie as suas Aplicações Empresariais com a Azure AD](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>Saiba como o Azure AD pode ajudá-lo a alcançar o SSO nas aplicações SaaS da sua empresa e as melhores práticas para controlar o acesso. |
| Vídeos| [O que é o fornecimento de utilizadores no Diretório Ative Azure?](https://youtu.be/_ZjARPpI6NI) <br> [Como implementar o fornecimento de utilizadores no Diretório Ative Azure?](https://youtu.be/pKzyts6kfrw) <br> [Integração salesforce com Azure AD: Como automatizar o fornecimento de utilizadores](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| Cursos online| SkillUp Online: [Identidades de Gestão](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> Saiba como integrar o Azure AD com muitas aplicações SaaS e garantir o acesso dos utilizadores a essas aplicações. |
| Livros| [Autenticação Moderna com Diretório Ativo Azure para Aplicações Web (Referência de Desenvolvimento) 1ª Edição](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0).  <br> Este é um guia autoritário e de mergulho profundo para a construção de soluções de autenticação ative diretório para estes novos ambientes. |
| Tutoriais| Consulte a [lista de tutoriais sobre como integrar aplicações SaaS com a Azure AD](../saas-apps/tutorial-list.md). |
| FAQ| [Perguntas frequentes](../app-provisioning/user-provisioning.md) sobre o fornecimento automatizado de utilizadores |

### <a name="solution-architectures"></a>Soluções arquiteturas

O serviço de provisionamento de AD Azure fornece utilizadores a aplicações SaaS e outros sistemas, conectando-se aos pontos finais da API de gestão de utilizadores fornecidos por cada fornecedor de aplicações. Estes pontos finais de gestão de utilizadores API permitem que a AD Azure crie, atualize e remova os utilizadores programáticamente.

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>Fornecimento automático de utilizadores para empresas híbridas

Neste exemplo, os utilizadores e ou grupos são criados numa base de dados de RH ligada a um diretório no local. O serviço de provisionamento de AD Azure gere o fornecimento automático de utilizadores às aplicações saaS-alvo.

 ![fornecimento de utilizadores](./media/plan-auto-user-provisioning/hybridprovisioning.png)

**Descrição do fluxo de trabalho:**

1. Os utilizadores/grupos são criados numa aplicação/sistema DE RH no local, como o SAP. 

1. O **agente Azure AD Connect** executa sincronizações programadas de identidades (utilizadores e grupos) desde a AD local até à AD Azure.

1. O serviço de **provisionamento da AD Azure** inicia um [ciclo inicial](../app-provisioning/user-provisioning.md) contra o sistema de origem e o sistema de destino. 

1. O serviço de **provisionamento de AD Azure** questiona o sistema de origem para quaisquer utilizadores e grupos alterados desde o ciclo inicial, e empurra alterações nos [ciclos incrementais.](../app-provisioning/user-provisioning.md)

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>Fornecimento automático de utilizadores para empresas apenas em nuvem

Neste exemplo, a criação de utilizadores ocorre em Azure AD e o serviço de provisionamento da AD Azure gere o fornecimento automático de utilizadores às aplicações-alvo (SaaS).

![Imagem 2](./media/plan-auto-user-provisioning/cloudprovisioning.png)

**Descrição do fluxo de trabalho:**

1. Os utilizadores/grupos são criados em Azure AD.

1. O serviço de **provisionamento da AD Azure** inicia um [ciclo inicial](../app-provisioning/user-provisioning.md) contra o sistema de origem e o sistema de destino. 

1. O serviço de **provisionamento da Azure AD** questiona o sistema de origem para quaisquer utilizadores e grupos atualizados desde o ciclo inicial, e executa quaisquer [ciclos incrementais.](../app-provisioning/user-provisioning.md)

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>Fornecimento automático de utilizadores para aplicações de RH em nuvem 

Neste exemplo, os utilizadores e ou grupos são criados numa aplicação de RH na nuvem, como workday e SuccessFactors. O serviço de provisionamento de AD Azure e o agente de provisionamento Azure AD Connect disponibilizam os dados do utilizador do inquilino da aplicação de APLICAÇÃO em nuvem HR em AD. Uma vez que as contas são atualizadas em AD, é sincronizada com Azure AD Através do Azure AD Connect, e os endereços de e-mail e atributos de nome de utilizador podem ser reescritos para o inquilino da aplicação de APLICAÇÃO DE RH cloud.

![Imagem 2](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  **A equipa de RH** realiza as transações no inquilino da aplicação de RH em nuvem.
2.  O serviço de provisionamento da **Azure AD** executa os ciclos programados a partir do inquilino da aplicação de aplicação de RH em nuvem e identifica alterações que precisam de ser processadas para sincronização com AD.
3.  O serviço de **provisionamento da Azure AD Invoca** o agente de provisionamento Azure AD Connect com uma carga útil de pedido contendo operações de criação/atualização/ativação/desativação da conta AD.
4.  O agente de **provisionamento Azure AD Connect** utiliza uma conta de serviço para gerir os dados da conta AD.
5.  **O Azure AD Connect** executa a delta sync para puxar atualizações em AD.
6.  As atualizações **da AD** estão sincronizadas com a AD Azure. 
7.  O serviço de provisionamento de **AD Azure** reescreve o atributo de e-mail e o nome de utilizador de Azure AD para o inquilino da aplicação de aplicações de RH em nuvem.

## <a name="plan-the-deployment-project"></a>Planear o projeto de implantação

Considere as suas necessidades organizacionais para determinar a estratégia de implementação do fornecimento de utilizadores no seu ambiente.

### <a name="engage-the-right-stakeholders"></a>Envolver as partes interessadas certas

Quando os projetos tecnológicos falham, é tipicamente devido a expectativas desajustadas sobre impacto, resultados e responsabilidades. Para evitar estas armadilhas, [certifique-se](https://aka.ms/deploymentplans) de que está a envolver as partes interessadas certas e que as partes interessadas no projeto são bem compreendidas documentando as partes interessadas e as suas entradas e responsabilidades do projeto.

### <a name="plan-communications"></a>Planear as comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunicar proativamente com os seus utilizadores como a sua experiência vai mudar, quando irá mudar, e como obter suporte se experimentarem problemas.

### <a name="plan-a-pilot"></a>Planeie um piloto

Recomendamos que a configuração inicial do fornecimento automático de utilizadores esteja num ambiente de teste com um pequeno subconjunto de utilizadores antes de o escalonar para todos os utilizadores em produção. Consulte [as melhores práticas](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) para dirigir um piloto.

#### <a name="best-practices-for-a-pilot"></a>Boas práticas para um piloto  

Um piloto permite-lhe testar com um pequeno grupo antes de implementar uma capacidade para todos. Certifique-se de que, como parte dos seus testes, cada caso de utilização dentro da sua organização é cuidadosamente testado.

Na sua primeira onda, o alvo de TI, a usabilidade e outros utilizadores apropriados que possam testar e fornecer feedback. Utilize este feedback para desenvolver ainda mais as comunicações e instruções que envia aos seus utilizadores e para dar informações sobre os tipos de problemas que a sua equipa de suporte pode ver.

Alargar o lançamento a grupos maiores de utilizadores, aumentando o âmbito do(s) grupo(s) visado. Isto pode ser feito através da [adesão dinâmica do grupo](../users-groups-roles/groups-dynamic-membership.md), ou adicionando manualmente os utilizadores ao(s) grupo(s) visado.

## <a name="plan-application-connections-and-administration"></a>Plano de ligações e administração de aplicação

Utilize o portal Azure AD para visualizar e gerir todas as aplicações que suportam o provisionamento. Veja [encontrar as suas aplicações no portal.](../app-provisioning/configure-automatic-user-provisioning-portal.md)

### <a name="determine-the-type-of-connector-to-use"></a>Determinar o tipo de conector a utilizar

Os passos reais necessários para ativar e configurar o fornecimento automático variam consoante a aplicação. Se a aplicação que pretende fornecer automaticamente está listada na galeria de [aplicações Azure AD SaaS,](../saas-apps/tutorial-list.md)então deve selecionar o [tutorial de integração específico](../saas-apps/tutorial-list.md) da aplicação para configurar o seu conector de fornecimento de utilizador pré-integrado.

Caso contrário, siga os passos abaixo:

1. [Crie um pedido](../develop/howto-app-gallery-listing.md) para um conector de fornecimento de utilizador pré-integrado. A nossa equipa trabalhará consigo e com o desenvolvedor de aplicações para embarcar na sua aplicação para a nossa plataforma se apoiar o SCIM.

1. Utilize o suporte genérico de fornecimento de utilizadores [BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) para a aplicação. Esta é uma exigência para que a AD Azure aprovisione os utilizadores da app sem um conector de provisionamento pré-integrado.

1. Se a aplicação for capaz de utilizar o conector BYOA SCIM, consulte o tutorial de [integração BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) para configurar o conector BYOA SCIM para a aplicação.

Para mais informações, consulte que aplicações e sistemas posso utilizar com o fornecimento automático de utilizadores da [Azure AD?](../app-provisioning/user-provisioning.md)

### <a name="collect-information-to-authorize-application-access"></a>Recolher informações para autorizar o acesso à aplicação

A criação de um fornecimento automático de utilizadores é um processo por aplicação. Para cada aplicação, é necessário fornecer [credenciais](../app-provisioning/configure-automatic-user-provisioning-portal.md) de administrador para se ligar ao ponto final de gestão do utilizador do sistema alvo.

A imagem abaixo mostra uma versão das credenciais de administração necessárias:

![O rastreio de provisionamento para gerir as definições de provisionamento da conta do utilizador](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

Embora algumas aplicações exijam o nome de utilizador e a palavra-passe do administrador, outras podem necessitar de um símbolo do portador.

## <a name="plan-user-and-group-provisioning"></a>Plan user and group provisioning

Se ativar o fornecimento de utilizadores para aplicações empresariais, o [portal Azure](https://portal.azure.com/) controla os seus valores de atributo através do mapeamento de atributos.

### <a name="determine-operations-for-each-saas-app"></a>Determine operações para cada app SaaS

Cada aplicação pode ter atributos únicos de utilizador ou grupo que devem ser mapeados para os atributos do seu AD Azure. A aplicação pode ter apenas um subconjunto de operações CRUD disponíveis.

Para cada aplicação, documente as seguintes informações:

* Operações de provisionamento CRUD a realizar no utilizador e ou objetos do Grupo para os sistemas-alvo. Por exemplo, cada empresário de aplicações SaaS pode não querer todas as operações possíveis.

* Atributos disponíveis no sistema de origem

* Atributos disponíveis no sistema alvo

* Mapeamento de atributos entre sistemas.

### <a name="choose-which-users-and-groups-to-provision"></a>Escolha quais utilizadores e grupos a fornecer

Antes de implementar o fornecimento automático de utilizadores, deve determinar os utilizadores e grupos a fornecer à sua aplicação.

* Utilize filtros de [deteção](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) para definir regras baseadas em atributos que determinam quais os utilizadores que são provisionados a uma aplicação.

* Em seguida, utilize [as atribuições](../manage-apps/assign-user-or-group-access-portal.md) de utilizador e grupo conforme necessário para filtrar adicionalmente.

### <a name="define-user-and-group-attribute-mapping"></a>Definir mapeamento de atributode utilizador e grupo

Para implementar o fornecimento automático de utilizadores, é necessário definir os atributos do utilizador e do grupo que são necessários para a aplicação. Há um conjunto pré-configurado de atributos e [mapeamento de atributos](../app-provisioning/configure-automatic-user-provisioning-portal.md) entre objetos de utilizador da AD Azure e objetos de utilizador de cada aplicação SaaS. Nem todas as aplicações SaaS permitem atributos de grupo.

A AD Azure suporta através de mapeamento direto de atributo-a-atributo, fornecendo valores [constantes, ou expressões de escrita para mapeamento de atributos](../app-provisioning/functions-for-customizing-application-data.md). Esta flexibilidade confere-lhe um bom controlo do que será povoado no atributo do sistema visado. Pode utilizar o [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) e o Graph Explorer para exportar mapeamentos e esquemas para um ficheiro JSON e importá-lo de volta para AD Azure.

Para mais informações, consulte personalizar o [fornecimento de atributos de atributos para aplicações SaaS no Diretório Ativo Azure](../app-provisioning/customize-application-attributes.md).

### <a name="special-considerations-for-user-provisioning"></a>Considerações especiais para o provisionamento dos utilizadores

Considere o seguinte para reduzir as questões após a implantação:

* Certifique-se de que os atributos utilizados para mapear objetos de utilizador/grupo entre aplicações de origem e alvo são resistentes. Não devem fazer com que os utilizadores/grupos sejam aprovisionados incorretamente se os atributos mudarem (por exemplo, um utilizador muda para outra parte da empresa).

* As aplicações podem ter restrições e/ou requisitos específicos que precisam de ser cumpridos para que o fornecimento do utilizador funcione corretamente. Por exemplo, Slack trunca valores para certos atributos. Consulte [tutoriais automáticos](../saas-apps/tutorial-list.md) de fornecimento de utilizadores específicos de cada aplicação.

* Confirme a consistência do esquema entre os sistemas de origem e alvo. Questões comuns incluem atributos como UPN ou correio que não corresponde. Por exemplo, upn em Azure *john_smith@contoso.com* AD definido como *jsmith@contoso.com*e na app, é . Para mais informações, consulte o Utilizador e o [grupo de referência do esquema](../app-provisioning/use-scim-to-provision-users-and-groups.md).

## <a name="plan-testing-and-security"></a>Teste de planos e segurança

Em cada fase da sua implementação, certifique-se de que está a testar que os resultados são como esperado e auditando os ciclos de provisionamento.

### <a name="plan-testing"></a>Teste de planos

Depois de configurar o fornecimento automático de utilizadores para a aplicação, executará casos de teste para verificar se esta solução satisfaz os requisitos da sua organização.

| Cenários| Resultados esperados |
| - | - |
| O utilizador é adicionado a um grupo atribuído ao sistema alvo | O objeto do utilizador é aprovisionado no sistema de destino. <br>O utilizador pode iniciar sessão no sistema de destino e executar as ações desejadas. |
| O utilizador é removido de um grupo que é atribuído ao sistema de destino | O objeto do utilizador é desprovisionado no sistema-alvo.<br>O utilizador não pode iniciar sessão no sistema de destino. |
| A informação do utilizador é atualizada em Azure AD por qualquer método | Os atributos de utilizador atualizados refletem-se no sistema alvo após um ciclo incremental |
| O utilizador está fora de alcance | O objeto do utilizador é desativado ou eliminado. <br>Nota: Este comportamento é ultrapassado para [o fornecimento](skip-out-of-scope-deletions.md)do Dia de Trabalho . |

### <a name="plan-security"></a>Segurança do plano

É comum que uma revisão de segurança seja necessária como parte de um destacamento. Se necessitar de uma revisão de segurança, consulte os muitos [whitepapers](https://www.microsoft.com/download/details.aspx?id=36391) da AD Azure que fornecem uma visão geral da identidade como serviço.

### <a name="plan-rollback"></a>Plano de reversão

Se a implementação automática de fornecimento de utilizadores não funcionar como desejado no ambiente de produção, os seguintes passos de retrocesso abaixo podem ajudá-lo a reverter para um bom estado anterior:

1. Reveja o [relatório sumário](../app-provisioning/check-status-user-account-provisioning.md) de fornecimento e os [registos de fornecimento](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) para determinar as operações incorretas que ocorreram nos utilizadores e/ou grupos afetados.

1. Utilize registos de auditoria de provisionamento para determinar o último bom estado conhecido dos utilizadores e/ou grupos afetados. Consulte também os sistemas de origem (Azure AD ou AD).

1. Trabalhar com o proprietário da aplicação para atualizar os utilizadores e/ou grupos afetados diretamente na aplicação usando os últimos valores de bom estado conhecidos.

## <a name="deploy-automatic-user-provisioning-service"></a>Implementar serviço automático de fornecimento de utilizadores

Escolha os passos que se alinham aos requisitos da sua solução.

### <a name="prepare-for-the-initial-cycle"></a>Preparar para o ciclo inicial

Quando o serviço de provisionamento de AD Azure funciona pela primeira vez, o ciclo inicial contra o sistema de origem e os sistemas-alvo cria uma imagem de todos os objetos do utilizador para cada sistema alvo.

Ao permitir o fornecimento automático para uma aplicação, o ciclo inicial pode demorar entre 20 minutos e várias horas. A duração depende da dimensão do diretório DaD Azure e do número de utilizadores no âmbito do provisionamento. Ver [Como melhorar o desempenho do provisionamento.](../app-provisioning/application-provisioning-when-will-provisioning-finish.md)

O serviço de provisionamento armazena o estado de ambos os sistemas após o ciclo inicial, melhorando o desempenho dos ciclos incrementais subsequentes.

### <a name="configure-automatic-user-provisioning"></a>Configurar o aprovisionamento automático de utilizadores

Utilize o [portal Azure](https://portal.azure.com/) para gerir o fornecimento automático de conta de utilizador e o desprovisionamento para aplicações que o suportem. Seguir os passos em [Como configurar o fornecimento automático a uma aplicação?](../app-provisioning/user-provisioning.md)

O serviço de fornecimento de utilizadores AD Azure também pode ser configurado e gerido utilizando o [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).

## <a name="manage-automatic-user-provisioning"></a>Gerir o fornecimento automático de utilizadores

Agora que estás destacado, tens de gerir a solução.

### <a name="monitor-user-provisioning-operation-health"></a>Monitorizar a operação de fornecimento de utilizadores para a saúde

Após um [ciclo inicial](../app-provisioning/user-provisioning.md)bem sucedido, o serviço de provisionamento de AD Azure executará atualizações incrementais indefinidamente, em intervalos específicos de cada aplicação, até que um dos seguintes eventos ocorra:

* O serviço é interrompido manualmente e um novo ciclo inicial é acionado utilizando o [portal Azure,](https://portal.azure.com/)ou utilizando o comando API do [Microsoft Graph.](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

* Um novo ciclo inicial é desencadeado por uma mudança nos mapeamentos de atributos ou filtros de deteção.

* O processo de fornecimento entra em quarentena devido a uma elevada taxa de erro e permanece em quarentena por mais de quatro semanas quando será automaticamente desativado.

Para rever estes eventos, e todas as outras atividades realizadas pelo serviço de provisionamento, consulte os registos de [provisionamento](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)da Azure AD.

Para compreender quanto tempo demoram os ciclos de provisionamento e monitorizar o progresso do trabalho de provisionamento, pode [verificar o estado do fornecimento de utilizadores](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).

### <a name="gain-insights-from-reports"></a>Obtenha informações a partir de relatórios

A Azure AD pode fornecer [informações adicionais](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) sobre o fornecimento de utilização e saúde operacional do utilizador da sua organização através de registos e relatórios de auditoria.

Os administradores devem verificar o relatório sumário de fornecimento para monitorizar a saúde operacional do trabalho de provisionamento. Todas as atividades realizadas pelo serviço de provisionamento são registadas nos registos de auditoria da AD Azure. Ver [Tutorial: Relatório sobre o fornecimento automático de conta](../app-provisioning/check-status-user-account-provisioning.md)de utilizador .

Recomendamos que assuma a propriedade e consuma estes relatórios sobre uma cadência que satisfaça os requisitos da sua organização. A Azure AD retém a maioria dos dados de auditoria durante 30 dias.

### <a name="troubleshoot"></a>Resolução de problemas

Consulte os seguintes links para resolver problemas quaisquer problemas que possam surgir durante o fornecimento:

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

* [Fornecer comentários sobre o produto](https://feedback.azure.com/forums/169401-azure-active-directory)

* [Mantenha-se atualizado sobre o que há de novo com a Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

* [Stack overflow Azure AD Forum](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="next-steps"></a>Passos seguintes
* [Configurar o fornecimento automático de utilizadores](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [Exportar ou importar a sua configuração de provisionamento utilizando a Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md)

* [Expressões de escrita para mapeamento de atributos no diretório Azure Ative](../app-provisioning/functions-for-customizing-application-data.md)
