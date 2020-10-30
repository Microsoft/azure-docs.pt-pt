---
title: Planeie uma implementação automática de fornecimento de utilizadores para o Azure Ative Directory
description: Orientações para o planeamento e execução do provisionamento automático do utilizador
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/17/2019
ms.author: kenwith
ms.reviewer: arvindha, celested
ms.openlocfilehash: 010e2a206ef08677651572c274cfc38fd516ecc9
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041116"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>Planear uma implementação de aprovisionamento automático de utilizadores

Muitas organizações confiam no software como aplicações de serviço (SaaS) como ServiceNow, Zscaler e Slack para a produtividade do utilizador final. Historicamente, o pessoal de TI tem confiado em métodos de fornecimento manual, tais como o upload de ficheiros CSV, ou usando scripts personalizados para gerir de forma segura as identidades dos utilizadores em cada aplicação SaaS. Estes processos são propensos a erros, inseguros e difíceis de gerir.

O provisionamento automático do utilizador (Azure AD) simplifica este processo automatizando de forma segura a criação, manutenção e remoção de identidades dos utilizadores em aplicações SaaS com base nas regras de negócio. Esta automatização permite-lhe escalar eficazmente os seus sistemas de gestão de identidade em ambientes apenas em nuvem e híbridos à medida que expande a sua dependência em soluções baseadas em nuvem.

Consulte [automatizar o fornecimento de utilizadores e desprovisionar as aplicações saaS com o Azure Ative Directory](../app-provisioning/user-provisioning.md) para entender melhor a funcionalidade.

## <a name="learn"></a>Learn

O fornecimento de utilizadores cria uma base para a governação de identidade em curso e melhora a qualidade dos processos de negócio que dependem de dados de identidade autorizados.

### <a name="key-benefits"></a>Principais vantagens

Os principais benefícios de permitir o fornecimento automático de utilizadores são:

* **Aumento da produtividade.** Pode gerir as identidades dos utilizadores através das aplicações SaaS com uma única interface de gestão de fornecimento de utilizador. Esta interface tem um único conjunto de políticas de provisionamento.

* **Gerir o risco** . Pode aumentar a segurança automatizando alterações com base no estado dos colaboradores ou membros do grupo que definem funções e/ou acesso.

* **Abordar a conformidade e a governação.** A Azure AD suporta registos de auditoria nativos para cada pedido de provisionamento do utilizador. Os pedidos são executados tanto nos sistemas de origem como nos sistemas-alvo. Isto permite-lhe rastrear quem tem acesso a aplicações a partir de um único ecrã.

* **Reduzir o custo.** O fornecimento automático de utilizadores reduz os custos evitando ineficiências e erros humanos associados ao fornecimento manual. Reduz a necessidade de soluções de fornecimento de utilizadores desenvolvidas por medida, scripts e registos de auditoria.

### <a name="licensing"></a>Licensing

A Azure AD proporciona integração de autosserviço de qualquer aplicação usando modelos fornecidos no menu da galeria de aplicações. Para obter uma lista completa dos requisitos de licença, consulte [a página de licenciamento da Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

#### <a name="application-licensing"></a>Licenciamento de pedidos

Necessitará das licenças adequadas para o(s) pedido(s) que pretende obter automaticamente. Discuta com os proprietários da aplicação se os utilizadores designados para a aplicação têm as licenças adequadas para as suas funções de candidatura. Se a Azure AD gere o provisionamento automático com base em funções, as funções atribuídas no Azure AD devem alinhar-se com as licenças de aplicação. As licenças incorretas detidas no pedido podem conduzir a erros durante o fornecimento/atualização de um utilizador.

### <a name="terms"></a>Termos

Este artigo utiliza os seguintes termos:

* Operações CRUD - Ações tomadas em contas de utilizador: Criar, Ler, Atualizar, Eliminar.

* Único sinal de sso (SSO) - A capacidade de um utilizador iniciar uma s-on uma vez e aceder a todas as aplicações ativadas pelo SSO. No contexto do fornecimento de utilizadores, o SSO é o resultado de os utilizadores terem uma única conta para aceder a todos os sistemas que utilizam o fornecimento automático de utilizadores.

* Sistema de origem - O repositório de utilizadores de que a Azure AD prevê. Azure AD é o sistema de origem da maioria dos conectores de fornecimento pré-integrados. No entanto, existem algumas exceções para aplicações em nuvem como SAP, Workday e AWS. Por exemplo, consulte [o fornecimento do Utilizador de Workday a AD](../saas-apps/workday-inbound-tutorial.md).

* Sistema-alvo - O repositório de utilizadores a que a Azure AD prevê. O sistema Target é tipicamente uma aplicação SaaS como ServiceNow, Zscaler e Slack. O sistema alvo também pode ser um sistema no local, como a AD.

* [Sistema de Gestão de Identidade de Domínio Cruzado (SCIM)](https://aka.ms/scimoverview) - Uma norma aberta que permite a automatização do fornecimento de utilizadores. O SCIM comunica dados de identidade dos utilizadores entre fornecedores de identidade, como a Microsoft, e fornecedores de serviços como o Salesforce ou outras aplicações SaaS que requerem informações de identidade do utilizador.

### <a name="training-resources"></a>Recursos de formação

| Recursos| Link e Descrição |
| - | - |
| Webinars a pedido| [Gerir as suas Aplicações Empresariais com Azure AD](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>Saiba como o Azure AD pode ajudá-lo a alcançar SSO para as aplicações saaS da sua empresa e as melhores práticas para controlar o acesso. |
| Vídeos| [O que é o fornecimento de utilizadores no Ative Azure Directory?](https://youtu.be/_ZjARPpI6NI) <br> [Como implementar o provisionamento do utilizador no Ative Azure Directory?](https://youtu.be/pKzyts6kfrw) <br> [Integração da Salesforce com Azure AD: Como automatizar o Provisionamento do Utilizador](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| Cursos online| SkillUp Online:  [Gestão de Identidades](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> Saiba como integrar o Azure AD com muitas aplicações SaaS e garantir o acesso dos utilizadores a essas aplicações. |
| Livros| [Autenticação moderna com Diretório Ativo Azure para Aplicações Web (Referência do Programador) 1ª Edição](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0).  <br> Este é um guia autoritário e de mergulho profundo para a construção de soluções de autenticação ative directy para estes novos ambientes. |
| Tutoriais| Consulte a [lista de tutoriais sobre como integrar aplicações SaaS com Azure AD.](../saas-apps/tutorial-list.md) |
| FAQ| [Perguntas frequentes](../app-provisioning/user-provisioning.md) sobre o fornecimento automatizado de utilizadores |

### <a name="solution-architectures"></a>Arquiteturas de soluções

O serviço de fornecimento de Azure AD fornece os utilizadores a aplicações SaaS e outros sistemas, conectando-se aos pontos finais da API de gestão de utilizadores fornecidos por cada fornecedor de aplicações. Estes pontos finais da API de gestão de utilizadores permitem ao Azure AD criar, atualizar e remover os utilizadores.

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>Fornecimento automático de utilizadores para empresas híbridas

Neste exemplo, os utilizadores e ou grupos são criados numa base de dados de RH ligada a um diretório no local. O serviço de fornecimento Azure AD gere o fornecimento automático de utilizadores às aplicações SaaS alvo.

 ![fornecimento de utilizadores](./media/plan-auto-user-provisioning/hybridprovisioning.png)

**Descrição do fluxo de trabalho:**

1. Os utilizadores/grupos são criados numa aplicação/sistema HR no local, como o SAP. 

1. **O agente Azure AD Connect** executa sincronizações programadas de identidades (utilizadores e grupos) da AD local à Azure AD.

1. **O serviço de fornecimento de AD AZure** inicia um [ciclo inicial](../app-provisioning/user-provisioning.md) contra o sistema de origem e o sistema alvo. 

1. **O serviço de fornecimento de AD AZure** consulta o sistema de origem para quaisquer utilizadores e grupos alterados desde o ciclo inicial, e impulsiona alterações nos [ciclos incrementais](../app-provisioning/user-provisioning.md).

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>Fornecimento automático de utilizadores para empresas que só na nuvem

Neste exemplo, a criação de utilizadores ocorre em Azure AD e o serviço de fornecimento AZure AD gere o fornecimento automático de utilizadores às aplicações-alvo (SaaS).

![Diagrama que mostra o processo de criação do utilizador/grupo a partir de uma aplicação H R no local através do Serviço de Provisionamento Azure A D para as aplicações S a S alvo.](./media/plan-auto-user-provisioning/cloudprovisioning.png)

**Descrição do fluxo de trabalho:**

1. Os utilizadores/grupos são criados em Azure AD.

1. **O serviço de fornecimento de AD AZure** inicia um [ciclo inicial](../app-provisioning/user-provisioning.md) contra o sistema de origem e o sistema alvo. 

1. **O serviço de fornecimento de AD AZure** consulta o sistema de origem para quaisquer utilizadores e grupos atualizados desde o ciclo inicial, e executa quaisquer [ciclos incrementais](../app-provisioning/user-provisioning.md).

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>Fornecimento automático de utilizadores para aplicações de RH em nuvem 

Neste exemplo, os utilizadores e ou grupos são criados numa aplicação de RH em nuvem como o Workday e o SuccessFactors. O serviço de fornecimento Azure AD e o agente de provisionamento Azure AD Connect fornece os dados do utilizador do inquilino da aplicação HR em nuvem para AD. Uma vez que as contas são atualizadas em AD, é sincronizado com Azure AD através do Azure AD Connect, e os endereços de e-mail e atributos de nome de utilizador podem ser escritos de volta para o inquilino da aplicação HR na nuvem.

![Imagem 2](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  **A equipa de RH** realiza as transações no inquilino de aplicações hr em nuvem.
2.  **O serviço de fornecimento de AD AD Azure** executa os ciclos programados a partir do inquilino da aplicação HR em nuvem e identifica alterações que precisam de ser processadas para sincronização com AD.
3.  **O serviço de fornecimento de AZure AD** invoca o agente de provisionamento Azure AD Connect com uma carga útil de pedido contendo operações de criação/atualização/ativação/desativação/ desativação.
4.  **O agente de provisionamento AZure AD Connect** utiliza uma conta de serviço para gerir os dados da conta AD.
5.  **O Azure AD Connect** executa a sincronização delta para puxar atualizações em AD.
6.  **As** atualizações de anúncios estão sincronizadas com o Azure AD. 
7.  **Azure AD fornece serviço de** fornecimento de e-mail atributo e nome de utilizador de Azure AD para o inquilino de aplicações hr em nuvem.

## <a name="plan-the-deployment-project"></a>Planear o projeto de implantação

Considere as suas necessidades organizacionais para determinar a estratégia de implantação do fornecimento de utilizadores no seu ambiente.

### <a name="engage-the-right-stakeholders"></a>Envolver as partes interessadas certas

Quando os projetos tecnológicos falham, é tipicamente devido a expectativas desajustadas no impacto, resultados e responsabilidades. Para evitar estas armadilhas, [certifique-se de que está a envolver as partes interessadas certas](../fundamentals/active-directory-deployment-plans.md) e que as partes interessadas no projeto são bem compreendidas documentando as partes interessadas e os seus contributos e contas do projeto.

### <a name="plan-communications"></a>Planear as comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunicar proativamente com os seus utilizadores como a sua experiência vai mudar, quando vai mudar, e como ganhar apoio se eles experimentarem problemas.

### <a name="plan-a-pilot"></a>Planeie um piloto

Recomendamos que a configuração inicial do fornecimento automático do utilizador esteja num ambiente de teste com um pequeno subconjunto de utilizadores antes de o escalonar a todos os utilizadores em produção. Consulte [as melhores práticas](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) para dirigir um piloto.

#### <a name="best-practices-for-a-pilot"></a>Melhores práticas para um piloto  

Um piloto permite-lhe testar com um pequeno grupo antes de implementar uma capacidade para todos. Certifique-se de que, como parte do seu teste, cada caso de utilização dentro da sua organização é completamente testado.

Na sua primeira onda, direcione a TI, a usabilidade e outros utilizadores apropriados que possam testar e fornecer feedback. Utilize este feedback para desenvolver as comunicações e instruções que envia aos seus utilizadores e para fornecer informações sobre os tipos de problemas que o seu pessoal de suporte pode ver.

Alargar o lançamento a grupos maiores de utilizadores, aumentando o âmbito do(s) grupo(s) visado. Isto pode ser feito através da [adesão dinâmica do grupo,](../enterprise-users/groups-dynamic-membership.md)ou adicionando manualmente os utilizadores ao(s) grupo(s) alvo.

## <a name="plan-application-connections-and-administration"></a>Ligações de aplicação do plano e administração

Utilize o portal AZure AD para visualizar e gerir todas as aplicações que suportam o provisionamento. Consulte [encontrar as suas aplicações no portal.](../app-provisioning/configure-automatic-user-provisioning-portal.md)

### <a name="determine-the-type-of-connector-to-use"></a>Determinar o tipo de conector a utilizar

Os passos reais necessários para permitir e configurar o fornecimento automático variam consoante a aplicação. Se a aplicação que pretende fornecer automaticamente estiver listada na galeria de [aplicações Azure AD SaaS,](../saas-apps/tutorial-list.md)deverá selecionar o [tutorial de integração específico](../saas-apps/tutorial-list.md) da aplicação para configurar o conector de provisionamento pré-integrado do utilizador.

Caso contrário, siga os passos abaixo:

1. [Crie um pedido](../azuread-dev/howto-app-gallery-listing.md) para um conector de provisionamento pré-integrado do utilizador. A nossa equipa trabalhará consigo e com o desenvolvedor de aplicações para embarcar na sua aplicação na nossa plataforma se apoiar o SCIM.

1. Utilize o suporte genérico de fornecimento de utilizadores [genéricos BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) para a aplicação. Este é um requisito para que a Azure AD provisa os utilizadores para a aplicação sem um conector de provisionamento pré-integrado.

1. Se a aplicação for capaz de utilizar o conector BYOA SCIM, consulte o [tutorial de integração BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) para configurar o conector BYOA SCIM para a aplicação.

Para obter mais informações, consulte [quais aplicações e sistemas posso utilizar com o fornecimento automático de utilizadores Azure AD?](../app-provisioning/user-provisioning.md)

### <a name="collect-information-to-authorize-application-access"></a>Recolher informações para autorizar o acesso à aplicação

A criação de fornecimento automático de utilizadores é um processo por aplicação. Para cada aplicação, é necessário fornecer [credenciais](../app-provisioning/configure-automatic-user-provisioning-portal.md) de administrador para se ligar ao ponto final de gestão do utilizador do sistema alvo.

A imagem abaixo mostra uma versão das credenciais de administração necessárias:

![Rastreio de provisionamento para gerir as definições de provisionamento de conta de utilizador](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

Embora algumas aplicações exijam o nome de utilizador e a palavra-passe admin, outras podem necessitar de um sinal ao portador.

## <a name="plan-user-and-group-provisioning"></a>Plano de fornecimento de utilizadores e grupos

Se permitir o fornecimento de utilizadores para aplicações empresariais, o [portal Azure](https://portal.azure.com/) controla os seus valores de atributos através do mapeamento de atributos.

### <a name="determine-operations-for-each-saas-app"></a>Determine as operações para cada app SaaS

Cada aplicação pode ter atributos únicos de utilizador ou grupo que devem ser mapeados para os atributos no seu AD Azure. A aplicação pode ter apenas um subconjunto de operações CRUD disponíveis.

Para cada pedido, documente as seguintes informações:

* Operações de provisionamento CRUD a serem realizadas no utilizador e ou objetos de grupo para os sistemas-alvo. Por exemplo, cada dono de uma aplicação SaaS pode não querer todas as operações possíveis.

* Atributos disponíveis no sistema de origem

* Atributos disponíveis no sistema alvo

* Mapeamento de atributos entre sistemas.

### <a name="choose-which-users-and-groups-to-provision"></a>Escolha quais utilizadores e grupos a providenciar

Antes de implementar o fornecimento automático do utilizador, deve determinar os utilizadores e grupos a serem a provisionados à sua aplicação.

* Utilize filtros de deteção para definir [regras](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) baseadas em atributos que determinam quais os utilizadores que estão a forcê-lo a uma aplicação.

* Em seguida, utilize [as atribuições de utilizador e grupo,](../manage-apps/assign-user-or-group-access-portal.md) conforme necessário para filtragem adicional.

### <a name="define-user-and-group-attribute-mapping"></a>Defina o mapeamento do atributo do utilizador e do grupo

Para implementar o fornecimento automático do utilizador, é necessário definir os atributos do utilizador e do grupo que são necessários para a aplicação. Há um conjunto pré-configurado de atributos e [mapeamentos de atributos](../app-provisioning/configure-automatic-user-provisioning-portal.md) entre objetos de utilizador AZure AD e objetos de utilizador de cada aplicação SaaS. Nem todas as aplicações SaaS permitem atributos de grupo.

A AZure AD suporta por mapeamento de atributos diretos, fornecendo [valores constantes ou escrevendo expressões para mapeamentos de atributos](../app-provisioning/functions-for-customizing-application-data.md). Esta flexibilidade dá-lhe um bom controlo do que será preenchido no atributo do sistema direcionado. Pode utilizar [a Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) e o Graph Explorer para exportar os seus mapeamentos e esquemas de a provisionamento do utilizador para um ficheiro JSON e importá-lo de volta para Azure AD.

Para mais informações, consulte [personalizar a Attribute-Mappings de Provisionamento do Utilizador para aplicações SaaS no Diretório Ativo Azure](../app-provisioning/customize-application-attributes.md).

### <a name="special-considerations-for-user-provisioning"></a>Considerações especiais para o provisionamento dos utilizadores

Considere o seguinte para reduzir problemas pós-implantação:

* Certifique-se de que os atributos utilizados para mapear objetos de utilizador/grupo entre aplicações de origem e alvo são resistentes. Não devem fazer com que os utilizadores/grupos sejam apresentados incorretamente se os atributos mudarem (por exemplo, um utilizador se move para uma parte diferente da empresa).

* As aplicações podem ter restrições específicas e/ou requisitos que precisam de ser cumpridos para que o fornecimento do utilizador funcione corretamente. Por exemplo, Slack trunca valores para certos atributos. Consulte [os tutoriais de fornecimento automático de utilizadores específicos](../saas-apps/tutorial-list.md) de cada aplicação.

* Confirme a consistência do esquema entre os sistemas de origem e alvo. As questões comuns incluem atributos como UPN ou correio não correspondente. Por exemplo, UPN em AZure AD definido como *john_smith@contoso.com* e na aplicação, é *jsmith@contoso.com* . Para obter mais informações, consulte a [referência de esquema do Utilizador e do grupo](../app-provisioning/use-scim-to-provision-users-and-groups.md).

## <a name="plan-testing-and-security"></a>Testes e segurança do plano

Em cada fase da sua implantação, certifique-se de que está a testar os resultados como esperado e a auditar os ciclos de avisionamento.

### <a name="plan-testing"></a>Testes de plano

Uma vez configurado o fornecimento automático do utilizador para a aplicação, irá executar casos de teste para verificar se esta solução satisfaz os requisitos da sua organização.

| Cenários| Resultados esperados |
| - | - |
| O utilizador é adicionado a um grupo atribuído ao sistema alvo | O objeto do utilizador é a provisionado no sistema-alvo. <br>O utilizador pode iniciar sôms para o sistema de destino e executar as ações desejadas. |
| O utilizador é removido de um grupo que é atribuído ao sistema alvo | O objeto do utilizador é desprovisionado no sistema alvo.<br>O utilizador não pode iniciar sôms no sistema de alvos. |
| As informações do utilizador são atualizadas em Azure AD por qualquer método | Os atributos atualizados do utilizador refletem-se no sistema alvo após um ciclo incremental |
| O utilizador está fora de alcance | O objeto do utilizador é desativado ou eliminado. <br>Nota: Este comportamento é ultrapassado para [o provisionamento do dia de trabalho](skip-out-of-scope-deletions.md). |

### <a name="plan-security"></a>Segurança do plano

É comum que uma revisão de segurança seja necessária como parte de um destacamento. Se necessitar de uma revisão de segurança, consulte os muitos [livros brancos](https://www.microsoft.com/download/details.aspx?id=36391) Azure AD que fornecem uma visão geral da identidade como serviço.

### <a name="plan-rollback"></a>Plano de reversão

Se a implementação automática de provisionamento do utilizador não funcionar como desejado no ambiente de produção, os seguintes passos de reversão abaixo podem ajudá-lo a reverter para um estado de bem conhecido anterior:

1. Reveja o [relatório de provisão](../app-provisioning/check-status-user-account-provisioning.md) e [os registos de provisionamento](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) para determinar que operações incorretas ocorreram nos utilizadores e/ou grupos afetados.

1. Utilize registos de auditoria de provisionamento para determinar o último bom estado conhecido dos utilizadores e/ou grupos afetados. Reveja também os sistemas de origem (Azure AD ou AD).

1. Trabalhe com o proprietário da aplicação para atualizar os utilizadores e/ou grupos afetados diretamente na aplicação usando os últimos valores de estado de bem conhecidos.

## <a name="deploy-automatic-user-provisioning-service"></a>Implementar serviço automático de fornecimento de utilizadores

Escolha os passos que se alinham com os requisitos da sua solução.

### <a name="prepare-for-the-initial-cycle"></a>Preparar para o ciclo inicial

Quando o serviço de fornecimento AZure AD funciona pela primeira vez, o ciclo inicial contra o sistema de origem e os sistemas-alvo cria uma imagem de todos os objetos do utilizador para cada sistema alvo.

Ao permitir o provisionamento automático para uma aplicação, o ciclo inicial pode demorar entre 20 minutos e várias horas. A duração depende da dimensão do diretório Azure AD e do número de utilizadores em possibilidade de provisionamento.

O serviço de fornecimento armazena o estado de ambos os sistemas após o ciclo inicial, melhorando o desempenho dos ciclos incrementais subsequentes.

### <a name="configure-automatic-user-provisioning"></a>Configurar o aprovisionamento automático de utilizadores

Utilize o [portal Azure](https://portal.azure.com/) para gerir o fornecimento automático de conta de utilizador e desatentação de aplicações que o suportem. Siga os passos em [Como configurar o provisionamento automático para uma aplicação?](../app-provisioning/user-provisioning.md)

O serviço de fornecimento de utilizadores Azure AD também pode ser configurado e gerido usando a API do [Microsoft Graph](/graph/api/resources/synchronization-overview).

## <a name="manage-automatic-user-provisioning"></a>Gerir o fornecimento automático do utilizador

Agora que se desdobrou, precisa gerir a solução.

### <a name="monitor-user-provisioning-operation-health"></a>Monitorizar a saúde da operação de fornecimento de utilizadores

Após um [ciclo inicial](../app-provisioning/user-provisioning.md)bem sucedido, o serviço de fornecimento AZure AD executará atualizações incrementais indefinidamente, em intervalos específicos de cada aplicação, até que ocorra um dos seguintes eventos:

* O serviço é interrompido manualmente e um novo ciclo inicial é acionado através do [portal Azure](https://portal.azure.com/), ou utilizando o comando API do [Microsoft Graph](/graph/api/resources/synchronization-overview) apropriado.

* Um novo ciclo inicial é desencadeado por uma alteração nos mapeamentos de atributos ou filtros de deteção.

* O processo de provisionamento entra em quarentena devido a uma elevada taxa de erro e permanece em quarentena por mais de quatro semanas quando será automaticamente desativado.

Para rever estes eventos, e todas as outras atividades realizadas pelo serviço de fornecimento, consulte [os registos de fornecimento de](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)AD Azure .

Para compreender quanto tempo demoram os ciclos de provisionamento e acompanhar o andamento do trabalho de provisionamento, pode [verificar o estado do provisionamento do utilizador](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).

### <a name="gain-insights-from-reports"></a>Obtenha informações dos relatórios

O Azure AD pode fornecer [informações adicionais sobre](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) o uso do utilizador e saúde operacional da sua organização através de registos e relatórios de auditoria.

Os administradores devem verificar o relatório de resumo do provisionamento para monitorizar a saúde operacional do trabalho de a provisionamento. Todas as atividades realizadas pelo serviço de fornecimento são registadas nos registos de auditoria da AZure AD. Ver [Tutorial: Relatório sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

Recomendamos que assuma a posse e consuma estes relatórios sobre uma cadência que satisfaça os requisitos da sua organização. A Azure AD retém a maioria dos dados de auditoria durante 30 dias.

### <a name="troubleshoot"></a>Resolução de problemas

Consulte os seguintes links para resolver problemas que possam surgir durante o provisionamento:

* [Problema de configuração do fornecimento de um utilizador a uma aplicação da Galeria AD Azure](../app-provisioning/application-provisioning-config-problem.md)

* [Sync um atributo do seu Ative Directy no local para Azure AD para provisionamento a uma aplicação](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)

* [Credenciais de administrador de poupança de problemas ao mesmo tempo que configura o fornecimento do utilizador a uma aplicação da Galeria do Diretório Ativo Azure](../app-provisioning/application-provisioning-config-problem-storage-limit.md)

* [Nenhum utilizadores está a ser a provisionado para uma aplicação da Galeria AD Azure](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

* [O conjunto errado de utilizadores está a ser a provisionado para uma aplicação da Galeria AD Azure](../app-provisioning/application-provisioning-config-problem-wrong-users-provisioned.md)

### <a name="helpful-documentation"></a>Documentação útil

* [Expressãos de escrita para mapeamentos de atributos](../app-provisioning/functions-for-customizing-application-data.md)

* [Visão geral da API de sincronização AZure AD](/graph/api/resources/synchronization-overview)

* [Ignorar a eliminação das contas de utilizador que ficam fora de alcance](skip-out-of-scope-deletions.md)

* [Azure AD Connect Provisioning Agent: O histórico de lançamento da versão](provisioning-agent-release-version-history.md)

#### <a name="resources"></a>Recursos

* [Fornecer comentários sobre o produto](https://feedback.azure.com/forums/169401-azure-active-directory)

* [Mantenha-se atualizado sobre as novidades com Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

* [Stack transbordar Azure AD fórum](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="next-steps"></a>Passos seguintes
* [Configure o provisionamento automático do utilizador](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [Exporte ou importe a sua configuração de provisionamento utilizando a Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md)

* [Expressãos de escrita para mapeamentos de atributos no diretório Azure Ative](../app-provisioning/functions-for-customizing-application-data.md)