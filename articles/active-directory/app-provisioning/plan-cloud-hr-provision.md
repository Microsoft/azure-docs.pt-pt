---
title: Planeie aplicação de RH em nuvem para fornecimento de utilizadores de Diretório Ativo Azure
description: Este artigo descreve o processo de implementação de sistemas de RH em nuvem, como Workday e SuccessFactors, com o Azure Ative Directory. Integrar o Azure AD com o seu sistema de RH em nuvem resulta num sistema completo de gestão do ciclo de vida de identidade.
services: active-directory
author: martincoetzer
manager: CelesteDG
tags: azuread
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: martinco
ms.reviewer: arvindha
ms.openlocfilehash: 28abe2dfa5a1a13ba09e20202180cb5e47d94072
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77522437"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Planeie aplicação de RH em nuvem para fornecimento de utilizadores de Diretório Ativo Azure

Historicamente, os funcionários de TI confiaram em métodos manuais para criar, atualizar e eliminar funcionários. Usaram métodos como o upload de ficheiros CSV ou scripts personalizados para sincronizar os dados dos funcionários. Estes processos de fornecimento são propensos a erros, inseguros e difíceis de gerir.

Para gerir os ciclos de vida identitários de colaboradores, fornecedores ou trabalhadores contingentes, o serviço de prestação de [utilizadores azure Ative Directory (Azure AD)](../app-provisioning/user-provisioning.md) oferece integração com aplicações de recursos humanos (RH) baseadas na nuvem. Exemplos de aplicações incluem Workday ou SuccessFactors.

A Azure AD utiliza esta integração para permitir os seguintes fluxos de trabalho da aplicação de RH em nuvem (app):

- **Utilizadores de fornecimento para Diretório Ativo:** Disponibilize conjuntos selecionados de utilizadores de uma aplicação de HR em nuvem para um ou mais domínios de Diretório Ativo.
- **Fornecer utilizadores apenas em nuvem para AD Azure:** Em cenários em que o Ative Directory não é utilizado, os utilizadores disponibilizam utilizadores diretamente da aplicação de RH em nuvem para a AD Azure.
- **Escreva de volta para a aplicação de HR em nuvem:** Escreva os endereços de e-mail e os atributos de nome de utilizador do Azure AD de volta à aplicação de HR cloud.

> [!NOTE]
> Este plano de implementação mostra-lhe como implementar os seus fluxos de trabalho de aplicações de RH em nuvem com o fornecimento de utilizadores da AD Azure. Para obter informações sobre como implementar o fornecimento automático de utilizadores ao software como aplicações de serviço (SaaS), consulte [Plan uma implementação automática](https://aka.ms/deploymentplans/provisioning)de fornecimento de utilizadores .

## <a name="enabled-hr-scenarios"></a>Cenários de RH habilitados

O serviço de prestação de utilizadores da AD Azure permite a automatização dos seguintes cenários de gestão do ciclo de vida de identidade baseado sugh:

- **Contratação de novos empregados:** Quando um novo funcionário é adicionado à aplicação de HR na nuvem, uma conta de utilizador é automaticamente criada em Ative Directory e Azure AD com a opção de reescrever o endereço de e-mail e os atributos de username para a aplicação de HR na nuvem.
- **Atributo sie e atualizações de perfil:** Quando um registo de empregados, como nome, título ou gestor, é atualizado na aplicação de HR na nuvem, a sua conta de utilizador é automaticamente atualizada em Ative Directory e Azure AD.
- **Rescisões de funcionários:** Quando um funcionário é rescindido na aplicação de RH em nuvem, a sua conta de utilizador é automaticamente desativada em Ative Directory e Azure AD.
- **Recontratações de empregados:** Quando um empregado é recontratado na aplicação de RH em nuvem, a sua conta antiga pode ser automaticamente reativada ou reprovisionada para Ative Directory e Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>Para quem é esta integração mais adequada?

A integração de aplicações em nuvem HR com o fornecimento de utilizadores da AD Azure é ideal para organizações que:

- Quer uma solução pré-construída baseada na nuvem para o fornecimento de utilizadores de RH em nuvem.
- Exija o fornecimento direto de utilizadores da aplicação de RH em nuvem para Ative Directory ou Azure AD.
- Exigir que os utilizadores sejam aprovisionados utilizando dados obtidos a partir da aplicação cloud HR.
- Requerem juntar-se, mover e deixar os utilizadores sincronizados com uma ou mais florestas de Diretórios Ativos, domínios e OUs com base apenas em informações de mudança detetadas na aplicação de RH na nuvem.
- Use o Office 365 para e-mail.

## <a name="learn"></a>Saiba mais

O fornecimento de utilizadores cria uma base para a governação de identidade em curso. Melhora a qualidade dos processos empresariais que dependem de dados de identidade autoritárias.

### <a name="terms"></a>Termos

Este artigo utiliza os seguintes termos:

- **Sistema de origem**: O repositório de utilizadores que o Azure AD prevê. Um exemplo é uma aplicação de HR em nuvem, como Workday ou SuccessFactors.
- **Sistema de destino**: O repositório de utilizadores a que o Azure AD prevê. Exemplos são Ative Directory, Azure AD, Office 365 ou outras aplicações SaaS.
- **Processo Joiners-Movers-Leavers**: Um termo usado para novas contratações, transferências e rescisão utilizando uma aplicação de RH na nuvem como um sistema de registos. O processo completa-se quando o serviço forprovisiona com sucesso os atributos necessários ao sistema-alvo.

### <a name="key-benefits"></a>Principais vantagens

Esta capacidade de fornecimento de TI orientado por RH oferece os seguintes benefícios significativos para o negócio:

- **Aumentar a produtividade:** Agora pode automatizar a atribuição de contas de utilizador e licenças do Office 365 e fornecer acesso a grupos-chave. A automatização de atribuições dá às novas contratações acesso imediato às suas ferramentas de trabalho e aumenta a produtividade.
- **Gerir o risco:** Pode aumentar a segurança automatizando alterações com base no estado dos funcionários ou membros do grupo com dados provenientes da aplicação de RH cloud. Automatizar alterações garante que as identidades dos utilizadores e o acesso às aplicações chave atualizam automaticamente quando os utilizadores transitam ou saem da organização.
- **Abordar o cumprimento e a governação:** A Azure AD suporta registos de auditoria nativas para pedidos de fornecimento de utilizadores realizados por aplicações de sistemas de origem e alvo. Com a auditoria, pode rastrear quem tem acesso às aplicações a partir de um único ecrã.
- **Gerir o custo:** O fornecimento automático reduz os custos evitando ineficiências e erros humanos associados ao provisionamento manual. Reduz a necessidade de soluções de fornecimento de utilizadores desenvolvidas sob medida construídas ao longo do tempo, utilizando plataformas antigas e desatualizadas.

### <a name="licensing"></a>Licenciamento

Para configurar a aplicação de RH em nuvem para a integração de fornecimento de utilizadores da AD Azure, você precisa de uma [licença Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) válida e uma licença para a app de RH na nuvem, como Workday ou SuccessFactors.

Também precisa de uma licença de subscrição Azure AD Premium P1 ou superior para cada utilizador que será proveniente da aplicação de RH em nuvem e aprovisionado para o Ative Directory ou Azure AD. Qualquer número impróprio de licenças detidas na aplicação de RH na nuvem pode levar a erros durante o fornecimento do utilizador.

### <a name="prerequisites"></a>Pré-requisitos

- Acesso global do administrador da Azure AD para configurar o agente de provisionamento Azure AD Connect.
- Uma instância de teste e produção da aplicação cloud HR.
- Permissões de administrador na aplicação de HR na nuvem para criar um utilizador de integração do sistema e fazer alterações para testar os dados dos funcionários para fins de teste.
- Para o fornecimento de utilizadores ao Ative Directory, é necessário um servidor que execute o Windows Server 2012 ou superior com um prazo de funcionamento .NET 4.7.1+ para acolher o agente de [provisionamento Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=847801).
- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) para sincronizar utilizadores entre o Ative Directory e o Azure AD.

### <a name="training-resources"></a>Recursos de formação

| **Recursos** | **Link e descrição** |
|:-|:-|
| Vídeos | [O que é o fornecimento de utilizadores no Diretório Ative Azure?](https://youtu.be/_ZjARPpI6NI) |
| | [Como implementar o fornecimento de utilizadores no Diretório Ative Azure](https://youtu.be/pKzyts6kfrw) |
| Tutoriais | [Lista de tutoriais sobre como integrar aplicações do SaaS com a Azure AD](../saas-apps/tutorial-list.md) |
| | [Tutorial: Configure workday para fornecimento automático de utilizadores](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| FAQ | [Fornecimento automatizado de utilizadores](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Prestação do Dia de Trabalho para a AD Azure](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Arquitetura de soluções

O exemplo seguinte descreve a arquitetura de solução de solução de fornecimento de utilizadores de ponta a ponta para ambientes híbridos comuns e inclui:

- **Os dados de RH autoritários fluem da aplicação de RH em nuvem para o Diretório Ativo.** Neste fluxo, o evento HR (processo Joiners-Movers-Leavers) é iniciado no inquilino da aplicação de RH em nuvem. O serviço de provisionamento de AD Azure e o agente de provisionamento Azure AD Connect disponibilizam os dados dos utilizadores do inquilino da aplicação de APLICAÇÃO cloud HR para o Ative Directory. Dependendo do evento, pode levar a criar, atualizar, ativar e desativar operações no Diretório Ativo.
- **Sync with Azure AD e escreva e-mail e username de on-premises Ative Directory para cloud HR app.** Depois de as contas serem atualizadas no Ative Directory, está sincronizada com o Azure AD Através do Azure AD Connect. Os endereços de e-mail e os atributos de nome de utilizador podem ser reescritos para o inquilino da aplicação de APLICAÇÃO DE RH em nuvem.

![Diagrama de fluxo de trabalho](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>Descrição do fluxo de trabalho

Os seguintes passos-chave são indicados no diagrama:  

1. **A equipa de RH** realiza as transações no inquilino da aplicação de RH em nuvem.
2. O serviço de provisionamento da **Azure AD** executa os ciclos programados a partir do inquilino da aplicação de aplicação de RH em nuvem e identifica alterações que precisam de ser processadas para sincronização com o Ative Directory.
3. O serviço de provisionamento da **Azure AD AD** invoca o agente de provisionamento Azure AD Connect com uma carga útil de pedido que contém operações de diretório ativo que criam, atualizam, ativam e desativam as operações.
4. O agente de **provisionamento Azure AD Connect** utiliza uma conta de serviço para gerir os dados da conta do Diretório Ativo.
5. **O Azure AD Connect** executa a [delta sync](../hybrid/how-to-connect-sync-whatis.md) para puxar atualizações no Ative Directory.
6. As atualizações **de Diretório Ativo** estão sincronizadas com a AD Azure.
7. O serviço de provisionamento da **Azure AD** reescreve o atributo de e-mail e o nome de utilizador de Azure AD para o inquilino da aplicação de aplicações de RH em nuvem.

## <a name="plan-the-deployment-project"></a>Planear o projeto de implantação

Considere as suas necessidades organizacionais enquanto determina a estratégia para esta implantação no seu ambiente.

### <a name="engage-the-right-stakeholders"></a>Envolver as partes interessadas certas

Quando os projetos tecnológicos falham, normalmente fazem-no devido a expectativas desajustadas sobre impacto, resultados e responsabilidades. Para evitar estas armadilhas, [certifique-se de que está a envolver as partes interessadas certas.](https://aka.ms/deploymentplans) Certifique-se também de que as funções das partes interessadas no projeto são bem compreendidas. Documentar as partes interessadas e as suas entradas e contas do projeto.

Inclua um representante da organização de RH que pode fornecer inputs sobre os processos de negócio st. existentes e identidade do trabalhador mais requisitos de processamento de dados de trabalho.

### <a name="plan-communications"></a>Planear as comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunique-se proativamente com os seus utilizadores sobre quando e como a sua experiência irá mudar. Deixe-os saber como obter apoio se tiverem problemas.

### <a name="plan-a-pilot"></a>Planeie um piloto

A integração de processos de negócio de RH e fluxos de trabalho de identidade da app de RH em nuvem para sistemas-alvo requer uma quantidade considerável de validação de dados, transformação de dados, limpeza de dados e testes de ponta a ponta antes de poder implementar a solução em produção.

Execute a configuração inicial num [ambiente piloto](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) antes de a dimensionar para todos os utilizadores em produção.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Selecione aplicativos de conector de fornecimento de H em nuvem

Para facilitar os fluxos de trabalho de fornecimento de AD Azure entre a aplicação de RH em nuvem e o Ative Directory, pode adicionar várias aplicações de conector de provisionamento a partir da galeria de aplicações Azure AD:

- **Aplicação Cloud HR para o fornecimento**de utilizadores do Diretório Ativo : Esta aplicação de provisionamento de conector facilita o fornecimento de conta de utilizador da aplicação cloud HR para um único domínio de Diretório Ativo. Se tiver vários domínios, pode adicionar uma instância desta aplicação a partir da galeria de aplicações Azure AD para cada domínio de Diretório Ativo a que precisa fornecer.
- Aplicação Cloud HR para fornecimento de utilizadores da **AD Azure**: Enquanto o Azure AD Connect é a ferramenta que deve ser usada para sincronizar utilizadores de Diretório Ativo para o Azure AD, esta aplicação de conector de provisionamento pode ser usada para facilitar o fornecimento de utilizadores apenas na nuvem da aplicação de RH cloud a um único inquilino da AD Azure.
- **Redação da aplicação Cloud HR**: Esta aplicação de conector de fornecimento facilita a devolução dos endereços de e-mail do utilizador do Azure AD para a aplicação cloud HR.

Por exemplo, a imagem seguinte lista as aplicações de conector Workday que estão disponíveis na galeria de aplicações Azure AD.

![Galeria de aplicativos do portal Azure Ative Directory](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>Gráfico de fluxo de decisão

Utilize o gráfico de fluxo de decisão seguinte para identificar quais as aplicações de fornecimento de RH em nuvem relevantes para o seu cenário.

![Gráfico de fluxo de decisão](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>Conceber a topologia de implantação do agente de provisionamento Azure AD Connect

A integração de provisionamento entre a aplicação de RH em nuvem e o Diretório Ativo requer quatro componentes:

- Inquilino de aplicativo sh da nuvem
- Aplicação de conector de provisionamento
- Agente de provisionamento Azure AD Connect
- Domínio do Active Directory

A topologia de implementação do agente de fornecimento Azure AD Connect depende do número de inquilinos de aplicações de RH em nuvem e domínios infantis do Ative Directory que pretende integrar. Se tiver vários domínios de Diretório Ativo, depende se os domínios do Diretório Ativo são contíguos ou [disconjuntos.](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/disjoint-namespace)

Com base na sua decisão, escolha um dos cenários de implementação:

- Único inquilino de aplicativo de nuvem de nuvem -> alvo de domínios infantis single ou multiple Ative Directory em uma floresta de confiança
- Único inquilino de aplicativo de nuvem de nuvem -> alvo múltiplos domínios de crianças em uma floresta de diretório ativo desarticulado

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>Único inquilino de aplicativo de nuvem de nuvem -> alvo de domínios infantis single ou multiple Ative Directory em uma floresta de confiança

Recomendamos a seguinte configuração de produção:

|Requisito|Recomendação|
|:-|:-|
|Número de agentes de provisionamento Azure AD Connect para implantar|Dois (para alta disponibilidade e falha)
|Número de aplicações de conector de provisionamento para configurar|Uma aplicação por domínio infantil|
|Anfitrião do servidor para o agente de provisionamento Azure AD Connect|Windows 2012 R2+ com linha de visão para controladores de domínio ative diretório geolocalizados</br>Pode coexistir com o serviço Azure AD Connect|

![Fluxo para agentes no local](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>Único inquilino de aplicativo de nuvem de nuvem -> alvo múltiplos domínios de crianças em uma floresta de diretório ativo desarticulado

Este cenário envolve o fornecimento de utilizadores da aplicação de RH em nuvem para domínios em florestas de Diretório Ativo desarticuladas.

Recomendamos a seguinte configuração de produção:

|Requisito|Recomendação|
|:-|:-|
|Número de agentes de provisionamento Azure AD Connect para implantar no local|Duas por floresta de diretório ativo desarticulada|
|Número de aplicações de conector de provisionamento para configurar|Uma aplicação por domínio infantil|
|Anfitrião do servidor para o agente de provisionamento Azure AD Connect|Windows 2012 R2+ com linha de visão para controladores de domínio ative diretório geolocalizados</br>Pode coexistir com o serviço Azure AD Connect|

![Único inquilino de aplicativo de nuvem de nuvem disjoint Ative Directory forest](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Requisitos do agente de provisionamento Azure AD Connect

A aplicação cloud HR para a solução de provisionamento de utilizadores do Ative Directory requer que implemente um ou mais agentes de provisionamento Azure AD Connect em servidores que executem o Windows 2012 R2 ou superior. Os servidores devem ter um tempo mínimo de 4-GB de RAM e .NET 4.7.1+. Certifique-se de que o servidor anfitrião tem acesso à rede ao domínio de Diretório Ativo alvo.

Para preparar o ambiente no local, o assistente de configuração do agente de configuração do Agente Azure AD Connect regista o agente com o seu inquilino Azure AD, [abre portas,](../manage-apps/application-proxy-add-on-premises-application.md#open-ports) [permite o acesso aos URLs,](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls)e suporta a configuração de proxy HTTPS de [saída.](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)

O agente de provisionamento utiliza uma conta de serviço para comunicar com os domínios do Diretório Ativo. Antes de instalar o agente, crie uma conta de serviço em Utilizadores e Computadores de Diretório Ativo que satisfaça os seguintes requisitos:

- Uma senha que não expira
- Permissões de controlo delegadas para ler, criar, excluir e gerir contas de utilizadores

Pode selecionar controladores de domínio que devem lidar com pedidos de provisionamento. Se tiver vários controladores de domínio distribuídos geograficamente, instale o agente de provisionamento no mesmo local que os seus controladores de domínio preferidos. Este posicionamento melhora a fiabilidade e o desempenho da solução de ponta a ponta.

Para uma elevada disponibilidade, pode implementar mais do que um agente de provisionamento Azure AD Connect. Registe o agente para lidar com o mesmo conjunto de domínios de Diretório Ativo no local.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Plano de filtragem de filtros e mapeamento de atributos

Quando permite o fornecimento da aplicação de RH em nuvem para Ative Directory ou Azure AD, o portal Azure controla os valores do atributo através do mapeamento de atributos.

### <a name="define-scoping-filters"></a>Defina filtros de deteção

Utilize filtros de [deteção](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) para definir as regras baseadas em atributos que determinam quais os utilizadores que devem ser aprovisionados a partir da aplicação de RH em nuvem para Ative Directory ou Azure AD.

Quando iniciar o processo DeSjuntores, reúna os seguintes requisitos:

- A aplicação de RH em nuvem é usada para trazer a bordo tanto funcionários como trabalhadores contingentes?
- Planeia utilizar a aplicação de RH em nuvem para o fornecimento de utilizadores da Azure AD para gerir tanto os colaboradores como os trabalhadores contingentes?
- Planeia lançar a aplicação cloud HR para o fornecimento de utilizadores da AD Azure apenas para um subconjunto dos utilizadores da aplicação de RH em nuvem? Um exemplo pode ser apenas empregados.

Dependendo dos seus requisitos, quando configurar mapeamentos de atributos, pode definir o campo **Source Object Scope** para selecionar quais os conjuntos de utilizadores na aplicação de RH cloud devem estar disponíveis para o fornecimento ao Ative Directory. Para obter mais informações, consulte o tutorial da aplicação cloud HR para filtros de deteção comumente usados.

### <a name="determine-matching-attributes"></a>Determinar atributos correspondentes

Com o fornecimento, obtém-se a capacidade de combinar as contas existentes entre a fonte e o sistema de destino. Quando integrar a aplicação de RH em nuvem com o serviço de provisionamento de Anúncios Azure, pode [configurar o mapeamento de atributos](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings) para determinar quais os dados dos utilizadores que devem fluir da aplicação de RH em nuvem para Ative Directory ou Azure AD.

Quando iniciar o processo DeSjuntores, reúna os seguintes requisitos:

- Qual é o ID único nesta aplicação de HR cloud que é usada para identificar cada utilizador?
- Do ponto de vista do ciclo de vida da identidade, como lida com as recontratações? Os recontratados mantêm as suas antigas iDs de empregado?
- Processa contratações futuras e cria contas de Diretório Ativo para elas com antecedência?
- Do ponto de vista do ciclo de vida da identidade, como lida com o trabalhador até à conversão contingente do trabalhador, ou não?
- Os utilizadores convertidos mantêm as suas antigas contas de Diretório Ativo ou recebem novas?

Dependendo dos seus requisitos, a AD Azure suporta o mapeamento direto de atributo-a-atributo, fornecendo valores constantes ou [expressões de escrita para mapeamento de atributos](../app-provisioning/functions-for-customizing-application-data.md). Esta flexibilidade dá-lhe o controlo final do que é povoado no atributo da aplicação direcionada. Pode utilizar o [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) e o Graph Explorer para exportar mapeamentos e esquemas para um ficheiro JSON e importá-lo de volta para AD Azure.

Por padrão, o atributo na aplicação de HR cloud que representa o ID único do empregado é usado como o atributo correspondente *mapeado ao atributo único no Diretório Ativo.* Por exemplo, no cenário da aplicação Workday, o atributo **workday** **WorkerID** é mapeado para o atributo **de funcionário** do Ative Directory.

Pode definir vários atributos correspondentes e atribuir precedência correspondente. São avaliados pela precedência correspondente. Assim que um jogo é encontrado, não são avaliados mais atributos correspondentes.

Também pode [personalizar os mapeamentos de atributos padrão,](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types)tais como alterar ou apagar mapeamentos de atributos existentes. Também pode criar novos mapeamentos de atributos de acordo com as suas necessidades de negócio. Para mais informações, consulte o tutorial da aplicação de CLOUD HR (como [o Workday)](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)para obter uma lista de atributos personalizados ao mapa.

### <a name="determine-user-account-status"></a>Determinar o estado da conta do utilizador

Por predefinição, a aplicação de conector de provisionamento mapeia o estado do perfil do utilizador para o estado da conta de utilizador no Ative Directory ou Azure AD para determinar se ativa ou desativa a conta de utilizador.

Quando iniciar o processo DeSjuntore-Leavers, reúna os seguintes requisitos.

| Processo | Requisitos |
| - | - |
| **Marcinadores** | Do ponto de vista do ciclo de vida da identidade, como lida com as recontratações? Os recontratados mantêm as suas antigas iDs de empregado? |
| | Processa contratações futuras e cria contas de Diretório Ativo para elas com antecedência? Estas contas são criadas num estado habilitado ou deficiente? |
| | Do ponto de vista do ciclo de vida da identidade, como lida com o trabalhador até à conversão contingente do trabalhador, ou não? |
| | Os utilizadores convertidos mantêm as suas antigas contas de Diretório Ativo, ou recebem novas? |
| **Desistentes** | As rescisões são tratadas de forma diferente para os trabalhadores e trabalhadores contingentes no Diretório Ativo? |
| | Que datas efetivas são consideradas para o processamento da rescisão do utilizador? |
| | Como é que as conversões de trabalhadores e trabalhadores contingentes afetam as contas de Diretório Ativo existentes? |
| | Como processa a operação Rescindir em Diretório Ativo? As operações de rescisão têm de ser tratadas se futuras contratações datadas forem criadas no Ative Directory como parte do processo Joiner. |

Dependendo dos seus requisitos, poderá personalizar a lógica de mapeamento utilizando [expressões AD Azure](../app-provisioning/functions-for-customizing-application-data.md) para que a conta Ative Directy esteja ativada ou desativada com base numa combinação de pontos de dados.

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>Map cloud HR app para atributos de utilizador do Diretório Ativo

Cada aplicação de CLOUD HR envia com aplicação de HR em nuvem padrão para mapeamento sinuoso ativo.

Quando iniciar o processo Joiners-Movers-Leavers, reúna os seguintes requisitos.

| Processo | Requisitos |
| - | - |
| **Marcinadores** | O processo de criação de conta Ative Directy é manual, automatizado ou parcialmente automatizado? |
| | Planeia propagar atributos personalizados da aplicação de HR cloud para Ative Directory? |
| **Mudanças** | Que atributos gostaria de processar sempre que uma operação movers ocorre na aplicação de HR em nuvem? |
| | Executa alguma validação específica de atributos no momento das atualizações do utilizador? Se sim, forneça detalhes. |
| **Desistentes** | As rescisões são tratadas de forma diferente para os trabalhadores e trabalhadores contingentes no Diretório Ativo? |
| | Que datas efetivas são consideradas para o processamento da rescisão do utilizador? |
| | Como é que as conversões de trabalhadores e trabalhadores contingentes têm impacto nas contas de Diretório Ativo existentes? |

Dependendo dos seus requisitos, pode modificar os mapeamentos para cumprir os seus objetivos de integração. Para mais informações, consulte o tutorial específico da aplicação de CLOUD HR (como [o Workday)](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)para obter uma lista de atributos personalizados ao mapa.

### <a name="generate-a-unique-attribute-value"></a>Gerar um valor de atributo único

Ao iniciar o processo DeSjuntores, poderá ter de gerar valores de atributo únicos quando define atributos como CN, samAccountName e upN, que tem constrangimentos únicos.

A função Azure AD [SelectUniqueValues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) avalia cada regra e, em seguida, verifica o valor gerado para a singularidade no sistema alvo. Por exemplo, consulte Gerar valor único para o atributo do [utilizadorPrincipalName (UPN).](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute)

> [!NOTE]
> Esta função é atualmente suportada apenas para o fornecimento de utilizadores de diretório ativo. Não pode ser usado com outras aplicações de provisionamento.

### <a name="configure-active-directory-ou-container-assignment"></a>Configure atribuição de contentores de Diretório Ativo OU

É um requisito comum colocar contas de utilizadores de Diretório sonoridade em contentores com base em unidades de negócio, localizações e departamentos. Quando iniciar um processo de Movers, e se houver uma mudança de organização de supervisão, poderá ter de mover o utilizador de um OU para outro no Ative Directory.

Utilize a função [Switch()](../app-provisioning/functions-for-customizing-application-data.md#switch) para configurar a lógica de negócio para a atribuição de OU e mapeá-la para o design de designado **do**Diretório Ativo .

Por exemplo, se quiser criar utilizadores em Ou com base no atributo de RH **Município,** pode usar a seguinte expressão:

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

Com esta expressão, se o valor do Município for Dallas, Austin, Seattle ou Londres, a conta de utilizador será criada na ou sug. Se não houver correspondência, então a conta é criada na Ou padrão.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Plano para entrega de palavra-passe de novas contas de utilizador

Quando iniciar o processo DeSjuntores, tem de definir e entregar uma senha temporária de novas contas de utilizador. Com o fornecimento de utilizadores de AD em nuvem para AD Azure, pode lançar a capacidade de redefinição de senha de [autosserviço](../authentication/quickstart-sspr.md) azure AD (SSPR) para o utilizador no primeiro dia.

O SSPR é um meio simples para os administradores de TI permitirem que os utilizadores redefinirem as suas palavras-passe ou desbloqueiem as suas contas. Pode fornecer o atributo do **Número Móvel** da aplicação cloud HR para Ative Directory e sincronizá-lo com O AD Azure. Depois do **atributo** do Número Móvel estar em Azure AD, pode ativar a SSPR para a conta do utilizador. Em seguida, no primeiro dia, o novo utilizador pode utilizar o número de telemóvel registado e verificado para autenticação.

## <a name="plan-for-initial-cycle"></a>Plano para o ciclo inicial

Quando o serviço de provisionamento de AD Azure funciona pela primeira vez, realiza um [ciclo inicial](../app-provisioning/how-provisioning-works.md#initial-cycle) contra a aplicação de HR em nuvem para criar uma imagem de todos os objetos de utilizador na aplicação de HR cloud. O tempo devida para os ciclos iniciais depende diretamente do número de utilizadores presentes no sistema de origem. O ciclo inicial para alguns inquilinos de aplicações de RH em nuvem com mais de 100.000 utilizadores pode demorar muito tempo.

Para grandes inquilinos de aplicações de RH em **nuvem (>30.000 utilizadores),** executar o ciclo inicial em fases progressivas. Inicie as atualizações incrementais apenas depois de validar que os atributos corretos são definidos no Diretório Ativo para diferentes cenários de fornecimento de utilizadores. Siga a ordem aqui.

1. Executar o ciclo inicial apenas para um conjunto limitado de utilizadores, definindo o filtro de [deteção](#plan-scoping-filters-and-attribute-mapping).
2. Verifique o fornecimento de conta de Diretório Ativo e os valores de atributo definidos para os utilizadores selecionados para a primeira execução. Se o resultado corresponde às suas expectativas, expanda o filtro de digitalização para incluir progressivamente mais utilizadores e verifique os resultados da segunda execução.

Depois de estar satisfeito com os resultados do ciclo inicial para os utilizadores de testes, inicie as [atualizações incrementais](../app-provisioning/how-provisioning-works.md#incremental-cycles).

## <a name="plan-testing-and-security"></a>Teste de planos e segurança

Em cada fase da sua implementação a partir do piloto inicial através da ativação do fornecimento do utilizador, certifique-se de que está a testar que os resultados são como esperado e auditando os ciclos de provisionamento.

### <a name="plan-testing"></a>Teste de planos

Depois de configurar a aplicação de RH em nuvem para o fornecimento de utilizadores da AD Azure, faça casos de teste para verificar se esta solução satisfaz os requisitos da sua organização.

|Cenários|Resultados esperados|
|:-|:-|
|Novo empregado é contratado na aplicação de HR cloud.| - A conta de utilizador é disponibilizada em Diretório Ativo.</br>- O utilizador pode iniciar sessão em aplicações de domínio ative de domínio e realizar as ações desejadas.</br>- Se o sincronizado Azure AD Connect estiver configurado, a conta de utilizador também é criada em Azure AD.
|O utilizador é encerrado na aplicação de RH em nuvem.|- A conta de utilizador é desativada no Diretório Ativo.</br>- O utilizador não pode iniciar sessão em nenhuma aplicação empresarial protegida pelo Ative Directory.
|A organização de supervisão de utilizadores é atualizada na aplicação de HR cloud.|Com base no mapeamento do atributo, a conta de utilizador passa de uma OU para outra no Diretório Ativo.|
|A HR atualiza o gestor do utilizador na aplicação cloud HR.|O campo de gestor no Ative Directory é atualizado para refletir o nome do novo gestor.|
|A HR recontrata um empregado para um novo papel.|O comportamento depende de como a aplicação de RH em nuvem é configurada para gerar IDs dos empregados:</br>- Se o id do antigo empregado for reutilizado para um realuguer, o conector ativa a conta de Diretório Ativo existente para o utilizador.</br>- Se o realuguer obtiver um novo ID do empregado, o conector cria uma nova conta de Diretório Ativo para o utilizador.|
|A HR converte o empregado a um trabalhador contratado ou vice-versa.|É criada uma nova conta de Diretório Ativo para a nova persona e a conta antiga fica desativada na data efetiva de conversão.|

Utilize os resultados anteriores para determinar como transitar a sua implementação automática de fornecimento de utilizadores para a produção com base nos seus prazos estabelecidos.

> [!TIP]
> Utilize técnicas como a redução de dados e a limpeza de dados quando atualizar o ambiente de teste com dados de produção para remover ou mascarar dados pessoais sensíveis para cumprir as normas de privacidade e segurança. 

### <a name="plan-security"></a>Segurança do plano

É comum que uma revisão de segurança seja necessária como parte da implementação de um novo serviço. Se for necessária uma revisão de segurança ou não tiver sido realizada, consulte os muitos [documentos brancos](https://www.microsoft.com/download/details.aspx?id=36391) da AD Azure que fornecem uma visão geral da identidade como um serviço.

### <a name="plan-rollback"></a>Plano de reversão

A implementação do fornecimento de utilizadores de RH em nuvem pode não funcionar como desejado no ambiente de produção. Em caso afirmativo, os seguintes passos de retrocesso podem ajudá-lo a reverter para um bom estado anterior conhecido.

1. Reveja o [relatório sumário](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) de fornecimento e os [registos de fornecimento](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) para determinar que operações incorretas foram realizadas nos utilizadores ou grupos afetados. Para obter mais informações sobre o relatório e registos de fornecimento, consulte gerir o fornecimento de utilizadores de [aplicações em nuvem HR](#manage-your-configuration).
2. O último bom estado conhecido dos utilizadores ou grupos afetados pode ser determinado através dos registos de auditoria de provisionamento ou através da revisão dos sistemas-alvo (Azure AD ou Ative Directory).
3. Trabalhe com o proprietário da aplicação para atualizar os utilizadores ou grupos afetados diretamente na app, utilizando os últimos valores de bom estado conhecidos.

## <a name="deploy-the-cloud-hr-app"></a>Implementar a aplicação de HR em nuvem

Escolha a aplicação cloud HR que se alinha com os seus requisitos de solução.

**Dia de Trabalho**: Para importar perfis de trabalhador do Dia de Trabalho para Diretório Ativo e Anúncio Azure, consulte [Tutorial: Configure Workday para o fornecimento automático de utilizadores](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment). Opcionalmente, pode reescrever o endereço de e-mail e o nome de utilizador para o Dia de Trabalho.

## <a name="manage-your-configuration"></a>Gerir a sua configuração

A Azure AD pode fornecer informações adicionais sobre o fornecimento de utilização e saúde operacional do utilizador da sua organização através de registos e relatórios de auditoria.

### <a name="gain-insights-from-reports-and-logs"></a>Obtenha informações de relatórios e registos

Após um [ciclo inicial](../app-provisioning/how-provisioning-works.md#initial-cycle)bem sucedido, o serviço de provisionamento de AD Azure continua a executar atualizações incrementais de back-to-back indefinidamente, em intervalos definidos nos tutoriais específicos de cada aplicação, até que um dos seguintes eventos ocorra:

- O serviço está parado manualmente. Um novo ciclo inicial é desencadeado através do [portal Azure](https://portal.azure.com/) ou do comando API do [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) apropriado.
- Um novo ciclo inicial é desencadeado devido a uma mudança nos mapeamentos de atributos ou filtros de deteção.
- O processo de provisionamento entra em quarentena devido a uma elevada taxa de erro. Fica em quarentena por mais de quatro semanas, altura em que é automaticamente desativado.

Para rever estes eventos e todas as outras atividades realizadas pelo serviço de provisionamento, [aprenda a rever os registos e obtenha relatórios sobre a atividade de provisionamento.](../app-provisioning/check-status-user-account-provisioning.md)

#### <a name="azure-monitor-logs"></a>Registos do Azure Monitor

Todas as atividades realizadas pelo serviço de provisionamento são registadas nos registos de auditoria da AD Azure. Pode encaminhar os registos de auditoria da Azure AD para os registos do Monitor Azure para uma análise mais aprofundada. Com registos do Monitor Azure (também conhecido como Log Analytics workspace), pode consultar dados para encontrar eventos, analisar tendências e realizar a correlação entre várias fontes de dados. Veja este [vídeo](https://youtu.be/MP5IaCTwkQg) para saber os benefícios de utilizar registos do Azure Monitor para registos de Anúncios Azure em cenários práticos de utilizador.

Instale as vistas de análise de registos de atividade do [Azure AD para](../reports-monitoring/howto-install-use-log-analytics-views.md) ter acesso a [relatórios pré-construídos](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) em torno de eventos de provisionamento no seu ambiente.

Para mais informações, consulte como analisar os registos de [atividade da AD Azure com os seus registos do Monitor Azure](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md).

### <a name="manage-personal-data"></a>Gerir dados pessoais

O agente de provisionamento Azure AD Connect instalado no servidor Windows cria registos no registo de eventos do Windows que podem conter dados pessoais dependendo da sua aplicação de RH em nuvem para mapeamentos de atributos do Diretório Ativo. Para cumprir as obrigações de privacidade do utilizador, configurar uma tarefa programada pelo Windows para limpar o registo do evento e garantir que nenhum dado seja mantido para além das 48 horas.

O serviço de provisionamento de AD Azure não gera relatórios, realiza análises ou fornece insights para além de 30 dias porque o serviço não armazena, processa ou guarda quaisquer dados para além de 30 dias.

### <a name="troubleshoot"></a>Resolução de problemas

Para resolver problemas quaisquer problemas que possam surgir durante o provisionamento, consulte os seguintes artigos:

- [Problema de configuração do fornecimento de utilizadores a uma aplicação da Galeria AD Azure](application-provisioning-config-problem.md)
- [Sincronizar um atributo do seu Diretório Ativo no local para a AD Azure para o fornecimento a uma aplicação](user-provisioning-sync-attributes-for-mapping.md)
- [O fornecimento de utilizadores a uma aplicação da Galeria AD Azure está a demorar horas ou mais](application-provisioning-when-will-provisioning-finish.md)
- [Credenciais de administrador de poupança de problemas ao configurar o fornecimento de utilizadores a uma aplicação da Galeria de Diretórios Ativos Do Azure](application-provisioning-config-problem-storage-limit.md)
- [Nenhum utilizador está a ser provisionado para uma aplicação da Galeria AD Azure](application-provisioning-config-problem-no-users-provisioned.md)
- [Conjunto errado de utilizadores estão sendo provisionados para uma aplicação da Galeria AD Azure](application-provisioning-config-problem-wrong-users-provisioned.md)
- [Configuração do Espectador de Eventos do Windows para resolução de problemas do agente](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Criação de registos de auditoria do portal Azure para resolução de problemas de serviço](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [A compreensão dos registos para a Conta de Utilizador AD cria operações](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [Compreensão de registos para operações de atualização do Gestor](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [Resolução de erros comummente encontrados](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Passos seguintes

- [Expressões de escrita para mapeamento de atributos](functions-for-customizing-application-data.md)
- [Visão geral da API de sincronização da AD Azure](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
- [Sem a eliminação das contas de utilizador que ficam fora de âmbito](skip-out-of-scope-deletions.md)
- [Agente de provisionamento de ligação Azure AD: histórico de lançamento de versão](provisioning-agent-release-version-history.md)
