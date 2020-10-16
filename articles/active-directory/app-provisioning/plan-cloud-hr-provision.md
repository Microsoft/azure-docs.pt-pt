---
title: Planeie a aplicação de RH em nuvem para o provisionamento do utilizador do Azure Ative Directory
description: Este artigo descreve o processo de implementação de sistemas de RH em nuvem, tais como Workday e SuccessFactors, com O Diretório Ativo Azure. A integração do Azure AD com o seu sistema de RH em nuvem resulta num sistema completo de gestão do ciclo de vida da identidade.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: kenwith
ms.reviewer: arvindha, celested
ms.openlocfilehash: cb36366143286c05603a8d14b5ad56ebb6544bda
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92070389"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Planeie a aplicação de RH em nuvem para o provisionamento do utilizador do Azure Ative Directory

Historicamente, o pessoal de TI tem confiado em métodos manuais para criar, atualizar e apagar funcionários. Usaram métodos como o upload de ficheiros CSV ou scripts personalizados para sincronizar dados dos funcionários. Estes processos de provisionamento são propensos a erros, inseguros e difíceis de gerir.

Para gerir os ciclos de vida identitários dos colaboradores, fornecedores ou trabalhadores contingentes, o serviço de prestação de serviços de fornecimento de [utilizadores Azure Ative (Azure AD)](../app-provisioning/user-provisioning.md) oferece integração com aplicações de recursos humanos (RH) baseadas na nuvem. Exemplos de aplicações incluem Workday ou SuccessFactors.

A Azure AD utiliza esta integração para permitir os seguintes fluxos de trabalho de aplicação hr em nuvem (app):

- **Fornecimento de utilizadores ao Ative Directory:** Fornecendo conjuntos selecionados de utilizadores de uma aplicação de HR em nuvem em um ou mais domínios do Ative Directory.
- **Fornecimento de utilizadores apenas em nuvem para Azure AD:** Em cenários onde o Ative Directory não é utilizado, fornecendo utilizadores diretamente da aplicação cloud HR para Azure AD.
- **Escreva de volta para a aplicação hr em nuvem:** Escreva os endereços de e-mail e os atributos de nome de utilizador do Azure AD de volta para a aplicação hr na nuvem.

> [!NOTE]
> Este plano de implementação mostra-lhe como implementar os fluxos de trabalho de aplicações de RH em nuvem com o fornecimento de utilizadores AZure AD. Para obter informações sobre como implementar o fornecimento automático de utilizadores a software como aplicações de serviço (SaaS), consulte [Planear uma implementação automática de provisionamento do utilizador](./plan-auto-user-provisioning.md).

## <a name="enabled-hr-scenarios"></a>Cenários de RH habilitados

O serviço de fornecimento de utilizadores Azure AD permite automatizar os seguintes cenários de gestão do ciclo de vida de identidade baseados em RH:

- **Contratação de novos funcionários:** Quando um novo funcionário é adicionado à aplicação cloud HR, uma conta de utilizador é criada automaticamente no Ative Directory e Azure AD com a opção de escrever de volta o endereço de e-mail e o nome de utilizador atribui à aplicação hr na nuvem.
- **Atributo do empregado e atualizações de perfil:** Quando um registo de empregados como nome, título ou gestor é atualizado na aplicação cloud HR, a sua conta de utilizador é automaticamente atualizada no Ative Directory e no Azure AD.
- **Rescisões dos empregados:** Quando um empregado é encerrado na aplicação cloud HR, a sua conta de utilizador é automaticamente desativada em Ative Directory e Azure AD.
- **Recontrações de empregados:** Quando um empregado é recontratado na aplicação cloud HR, a sua conta antiga pode ser automaticamente reativada ou reprovisionada para Ative Directory e Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>Para quem é que esta integração é mais adequada?

A integração de aplicativos hr em nuvem com o fornecimento de utilizadores AZure AD é ideal para organizações que:

- Pretenda uma solução pré-construída baseada na nuvem para o fornecimento de rh em nuvem.
- Requerer o fornecimento direto do utilizador da aplicação hr em nuvem para Ative Directory ou Azure AD.
- Exigir que os utilizadores sejam aprovisionados utilizando dados obtidos a partir da aplicação cloud HR.
- Exija que os utilizadores se juntem, se movam e que os utilizadores sejam sincronizados com uma ou mais florestas, domínios e OUs do Ative Directory, com base apenas em informações de alteração detetadas na aplicação cloud HR.
- Utilize o Microsoft 365 para e-mail.

## <a name="learn"></a>Learn

O fornecimento de utilizadores cria uma base para a governação da identidade em curso. Melhora a qualidade dos processos de negócio que dependem de dados de identidade autoritários.

### <a name="terms"></a>Termos

Este artigo utiliza os seguintes termos:

- **Sistema de origem**: O repositório de utilizadores de que a Azure AD prevê. Um exemplo é uma aplicação de HR em nuvem, como Workday ou SuccessFactors.
- **Sistema-alvo**: O repositório de utilizadores a que o AZure AD prevê. Exemplos são Ative Directory, Azure AD, Microsoft 365 ou outras aplicações SaaS.
- **Processo Joiners-Movers-Leavers**: Um termo usado para novas contratações, transferências e rescisão utilizando uma aplicação de RH em nuvem como um sistema de registos. O processo termina quando o serviço fornece com sucesso os atributos necessários ao sistema alvo.

### <a name="key-benefits"></a>Principais vantagens

Esta capacidade de provisionamento de TI orientado por RH oferece os seguintes benefícios significativos para o negócio:

- **Aumentar a produtividade:** Pode agora automatizar a atribuição de contas de utilizador e licenças microsoft 365 e fornecer acesso a grupos-chave. Automatizar tarefas dá às novas contratações acesso imediato às suas ferramentas de trabalho e aumenta a produtividade.
- **Gerir o risco:** Pode aumentar a segurança automatizando alterações com base no estado dos funcionários ou membros do grupo com dados que fluem a partir da aplicação cloud HR. Automatizar alterações garante que as identidades dos utilizadores e o acesso às principais aplicações atualizam-se automaticamente quando os utilizadores fazem a transição ou saem da organização.
- **Abordar a conformidade e a governação:** O Azure AD suporta registos de auditoria nativos para pedidos de provisionamento de utilizadores realizados por aplicações de sistemas de origem e alvo. Com a auditoria, é possível rastrear quem tem acesso às aplicações a partir de um único ecrã.
- **Gerir o custo:** O provisionamento automático reduz os custos evitando ineficiências e erros humanos associados ao provisionamento manual. Reduz a necessidade de soluções de fornecimento de utilizadores desenvolvidas por medida construídas ao longo do tempo utilizando plataformas antigas e desatualizadas.

### <a name="licensing"></a>Licensing

Para configurar a aplicação de HR em nuvem para a integração de fornecimento de utilizadores Azure AD, você precisa de uma [licença Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) válida e uma licença para a aplicação de HR em nuvem, como Workday ou SuccessFactors.

Você também precisa de uma licença de subscrição Azure AD Premium P1 válida ou superior para cada utilizador que será obtido a partir da aplicação cloud HR e aprovisionado para o Ative Directory ou Azure AD. Qualquer número impróprio de licenças detidas na aplicação cloud HR pode levar a erros durante o fornecimento do utilizador.

### <a name="prerequisites"></a>Pré-requisitos

- Administrador [de identidade híbrida](../users-groups-roles/directory-assign-admin-roles.md#hybrid-identity-administrator)  Azure AD para configurar o agente de provisionamento Azure AD Connect.
- Papel [de administrador de aplicação](../users-groups-roles/directory-assign-admin-roles.md#application-administrator) AZure AD para configurar a app de provisionamento no portal Azure
- Um teste e instância de produção da aplicação cloud HR.
- Permissões de administrador na aplicação cloud HR para criar um utilizador de integração do sistema e fazer alterações para testar dados dos funcionários para fins de teste.
- Para o fornecimento de utilizadores ao Ative Directory, é necessário um servidor com o Windows Server 2012 ou superior a .NET 4.7.1+ para hospedar o agente de provisionamento Azure AD Connect
- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) para sincronizar utilizadores entre Ative Directory e Azure AD.

### <a name="training-resources"></a>Recursos de formação

| **Recursos** | **Ligação e descrição** |
|:-|:-|
| Vídeos | [O que é o fornecimento de utilizadores no Ative Azure Directory?](https://youtu.be/_ZjARPpI6NI) |
| | [Como implementar o provisionamento de utilizadores no Ative Azure Directory](https://youtu.be/pKzyts6kfrw) |
| Tutoriais | [Lista de tutoriais sobre como integrar apps saaS com Azure AD](../saas-apps/tutorial-list.md) |
| | [Tutorial: Configurar o Dia de Trabalho para o fornecimento automático de utilizadores](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| FAQ | [Fornecimento automatizado de utilizadores](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Provisionamento de Workday a Azure AD](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Arquitetura de soluções

O exemplo a seguir descreve a arquitetura de solução de aprovisionamento de utilizadores de ponta a ponta para ambientes híbridos comuns e inclui:

- **Os dados de RH autoritários fluem da aplicação de RH em nuvem para o Ative Directory.** Neste fluxo, o evento HR (processo Joiners-Movers-Leavers) é iniciado no inquilino de aplicações hr em nuvem. O serviço de fornecimento de Azure AD e o agente de provisionamento Azure AD Connect prestam os dados do utilizador do inquilino da aplicação HR em nuvem para o Ative Directory. Dependendo do evento, pode levar a criar, atualizar, ativar e desativar operações no Ative Directory.
- **Sync com Azure AD e escreva e-mail e nome de utilizador de on-ins Ative Directory para cloud HR app.** Depois de as contas serem atualizadas no Ative Directory, é sincronizada com a Azure AD através do Azure AD Connect. Os endereços de e-mail e os atributos do nome de utilizador podem ser escritos de volta para o inquilino da aplicação HR na nuvem.

![Diagrama de fluxo de trabalho](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>Descrição do fluxo de trabalho

Os seguintes passos-chave são indicados no diagrama:  

1. **A equipa de RH** realiza as transações no inquilino de aplicações hr em nuvem.
2. **O serviço de fornecimento de AD AD Azure** executa os ciclos programados a partir do inquilino da aplicação HR em nuvem e identifica alterações que precisam de ser processadas para sincronização com o Ative Directory.
3. **O serviço de fornecimento de Ad AD Azure** invoca o agente de provisionamento Azure AD Connect com uma carga útil de pedido que contém a conta Ative Directory criar, atualizar, ativar e desativar operações.
4. **O agente de provisionamento Azure AD Connect** utiliza uma conta de serviço para gerir os dados da conta ative diretoria.
5. **O Azure AD Connect** executa [a sincronização](../hybrid/how-to-connect-sync-whatis.md) delta para puxar atualizações no Ative Directory.
6. As atualizações **do Diretório Ativo** estão sincronizadas com a Azure AD.
7. **O serviço de fornecimento de Ad AD Azure** escreve o atributo de e-mail e o nome de utilizador de Azure AD para o inquilino da aplicação HR na nuvem.

## <a name="plan-the-deployment-project"></a>Planear o projeto de implantação

Considere as suas necessidades organizacionais enquanto determina a estratégia para esta implantação no seu ambiente.

### <a name="engage-the-right-stakeholders"></a>Envolver as partes interessadas certas

Quando os projetos tecnológicos falham, normalmente fazem-no devido a expectativas desajustadas sobre o impacto, os resultados e as responsabilidades. Para evitar estas armadilhas, [certifique-se de que está a envolver as partes interessadas certas.](../fundamentals/active-directory-deployment-plans.md) Certifique-se também de que as funções das partes interessadas no projeto são bem compreendidas. Documente as partes interessadas e os seus contributos e contas do projeto.

Inclua um representante da organização de RH que pode fornecer inputs sobre os processos comerciais de RH existentes e a identidade do trabalhador, além dos requisitos de processamento de dados de trabalho.

### <a name="plan-communications"></a>Planear as comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunicar proativamente com os seus utilizadores sobre quando e como a sua experiência vai mudar. Informe-os de como obter apoio se experimentarem problemas.

### <a name="plan-a-pilot"></a>Planeie um piloto

A integração de processos de negócio de RH e fluxos de trabalho de identidade da app cloud HR para sistemas-alvo requer uma quantidade considerável de validação de dados, transformação de dados, limpeza de dados e testes de ponta a ponta antes de poder implementar a solução na produção.

Execute a configuração inicial num [ambiente piloto](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) antes de a escalar para todos os utilizadores em produção.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Selecione aplicativos de conector de RH em nuvem

Para facilitar o fornecimento de fluxos de trabalho da Azure AD entre a aplicação cloud HR e o Ative Directory, pode adicionar várias aplicações de conector de provisionamento a partir da galeria de aplicações AD Azure:

- **Aplicação Cloud HR para o fornecimento de utilizadores do Ative Directory**: Esta aplicação de conector de provisionamento facilita o fornecimento de conta de utilizador da aplicação hr em nuvem para um único domínio ative directory. Se tiver vários domínios, pode adicionar uma instância desta aplicação na galeria de aplicações AD AZure para cada domínio ative directory a que necessita.
- **Aplicação Cloud HR para a aprovisionamento do utilizador Azure AD**: Enquanto o Azure AD Connect é a ferramenta que deve ser usada para sincronizar utilizadores de Ative Directory para Azure AD, esta aplicação de conector de provisionamento pode ser usada para facilitar o fornecimento de utilizadores apenas em nuvem da aplicação de RH em nuvem a um único inquilino AZure AD.
- **Cloud HR app write-back**: Esta aplicação de conector de provisionamento facilita a reta finalização dos endereços de e-mail do utilizador de Azure AD para a aplicação hr em nuvem.

Por exemplo, a imagem que se segue lista as aplicações de conector Workday que estão disponíveis na galeria de aplicações AZure AD.

![Galeria de aplicativos do portal Azure Ative Directory](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>Gráfico de fluxo de decisão

Utilize o seguinte gráfico de fluxo de decisão para identificar quais aplicações de fornecimento de RH em nuvem são relevantes para o seu cenário.

![Gráfico de fluxo de decisão](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>Desenhe a topologia de implantação do agente de provisionamento Azure AD Connect

A integração de provisionamento entre a app hr em nuvem e o Ative Directory requer quatro componentes:

- Inquilino de aplicativo Cloud HR
- Aplicação de conector de provisionamento
- Agente de provisionamento Azure AD Connect
- Domínio do Active Directory

A topologia de implantação de agente de provisionamento Azure AD Connect depende do número de inquilinos de aplicativos hr em nuvem e domínios infantis do Ative Directory que pretende integrar. Se tiver vários domínios de Diretório Ativo, depende se os domínios do Diretório Ativo são contíguos ou [desarticulantes](/windows-server/identity/ad-ds/plan/disjoint-namespace).

Com base na sua decisão, escolha um dos cenários de implantação:

- Inquilino de aplicativo HR de nuvem única - > alvo de domínios individuais ou múltiplos de crianças ative direy em uma floresta de confiança
- Inquilino de aplicativo HR de nuvem única - > alvo de vários domínios infantis em uma floresta de diretório ativo desarticulado

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>Inquilino de aplicativo HR de nuvem única - > alvo de domínios individuais ou múltiplos de crianças ative direy em uma floresta de confiança

Recomendamos a seguinte configuração de produção:

|Requisito|Recomendação|
|:-|:-|
|Número de agentes de provisionamento AZure AD Connect para implantar|Dois (para alta disponibilidade e failover)
|Número de aplicações de conector de provisionamento para configurar|Uma aplicação por domínio infantil|
|Servidor anfitrião do agente de provisionamento Azure AD Connect|Windows 2012 R2+ com linha de visão para controladores de domínio ative de diretório geolocalizado</br>Pode coexistir com o serviço Azure AD Connect|

![Fluxo para agentes no local](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>Inquilino de aplicativo HR de nuvem única - > alvo de vários domínios infantis em uma floresta de diretório ativo desarticulado

Este cenário envolve o fornecimento de utilizadores da app hr em nuvem para domínios em florestas de diretórios ativos desarint.

Recomendamos a seguinte configuração de produção:

|Requisito|Recomendação|
|:-|:-|
|Número de agentes de provisionamento Azure AD Connect para implantar no local|Dois por disjoint Ative Directory forest|
|Número de aplicações de conector de provisionamento para configurar|Uma aplicação por domínio infantil|
|Servidor anfitrião do agente de provisionamento Azure AD Connect|Windows 2012 R2+ com linha de visão para controladores de domínio ative de diretório geolocalizado</br>Pode coexistir com o serviço Azure AD Connect|

![Único nuvem de app HR inquilino desarintar floresta de Diretório Ativo](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Requisitos do agente de provisionamento Azure AD Connect

A aplicação cloud HR para a solução de provisionamento de utilizadores do Ative Directory requer que você implemente um ou mais agentes de provisionamento AZure AD Connect em servidores que executam o Windows 2012 R2 ou maior. Os servidores devem ter um tempo de execução mínimo de 4 GB e .NET 4.7.1+. Certifique-se de que o servidor anfitrião tem acesso à rede ao domínio do Ative Directory alvo.

Para preparar o ambiente no local, o assistente de configuração do agente de provisionamento Azure AD Connect regista o agente com o seu inquilino AZure AD, [abre portas,](../manage-apps/application-proxy-add-on-premises-application.md#open-ports) [permite o acesso aos URLs,](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls)e suporta [a configuração de procuração HTTPS de saída.](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)

O agente de provisionamento utiliza uma conta de serviço para comunicar com os domínios do Ative Directory. Antes de instalar o agente, crie uma conta de serviço em Utilizadores e Computadores de Diretório Ativo que satisfaça os seguintes requisitos:

- Uma senha que não expira
- Permissões de controlo delegadas para ler, criar, excluir e gerir contas de utilizador

Pode selecionar controladores de domínio que devem lidar com pedidos de provisionamento. Se tiver vários controladores de domínio distribuídos geograficamente, instale o agente de provisionamento no mesmo local que os controladores de domínio preferidos. Este posicionamento melhora a fiabilidade e o desempenho da solução de ponta a ponta.

Para uma alta disponibilidade, pode implementar mais de um agente de provisionamento Azure AD Connect. Registe o agente para manusear o mesmo conjunto de domínios ative directory no local.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Planeie filtrar filtros e atribuir mapeamento

Quando permite o fornecimento da app HR em nuvem para Ative Directory ou AD AZure, o portal Azure controla os valores do atributo através do mapeamento de atributos.

### <a name="define-scoping-filters"></a>Definir filtros de escotagem

Utilize filtros de deteção para definir as [regras baseadas](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) no atributo que determinam quais os utilizadores que devem ser aprovisionados da aplicação HR em nuvem para Ative Directory ou Azure AD.

Quando iniciar o processo Dederequistão, reúna os seguintes requisitos:

- A aplicação cloud HR é usada para trazer a bordo tanto funcionários como trabalhadores contingentes?
- Planeia utilizar a app cloud HR para o fornecimento de utilizadores AZure AD para gerir tanto os funcionários como os trabalhadores contingentes?
- Planeia lançar a aplicação hr em nuvem para o utilizador Azure AD, que se provisões apenas para um subconjunto dos utilizadores de aplicações hr em nuvem? Um exemplo pode ser apenas empregados.

Dependendo dos seus requisitos, quando configurar mapeamentos de atributos, pode definir o campo **De âmbito de objetos de origem** para selecionar quais os conjuntos de utilizadores na aplicação hr em nuvem que devem estar em possibilidade de provisão para o Ative Directory. Para obter mais informações, consulte o tutorial de aplicativo hr em nuvem para filtros de scoping comumente usados.

### <a name="determine-matching-attributes"></a>Determinar atributos correspondentes

Com o provisionamento, obtém-se a capacidade de combinar as contas existentes entre o sistema de origem e alvo. Quando integrar a aplicação de HR em nuvem com o serviço de fornecimento AZure AD, pode [configurar o mapeamento de atributos](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings) para determinar que dados do utilizador devem fluir da aplicação de HR em nuvem para Ative Directory ou Azure AD.

Quando iniciar o processo Dederequistão, reúna os seguintes requisitos:

- Qual é o ID único nesta aplicação de RH em nuvem que é usada para identificar cada utilizador?
- Do ponto de vista do ciclo de vida da identidade, como lida com as recontratadas? As recontratadas mantêm as suas antigas identificações de empregados?
- Processa contratações futuras e cria contas de Ative Directory para elas com antecedência?
- Do ponto de vista do ciclo de vida identitário, como lida com o trabalhador até à conversão de trabalhadores contingentes, ou não?
- Os utilizadores convertidos mantêm as suas antigas contas de Ative Directory ou recebem novas?

Dependendo dos seus requisitos, a Azure AD suporta o mapeamento de atributos diretos, fornecendo [valores constantes ou expressões de escrita para mapeamentos de atributos](../app-provisioning/functions-for-customizing-application-data.md). Esta flexibilidade dá-lhe o controlo final do que é preenchido no atributo de aplicação direcionado. Pode utilizar o [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) e o Graph Explorer para exportar os mapeamentos e esquemas de a provisionamento do utilizador para um ficheiro JSON e importá-lo de volta para Azure AD.

Por padrão, o atributo na aplicação cloud HR que representa o ID do funcionário único é usado como o atributo correspondente *mapeado ao atributo único no Ative Directory.* Por exemplo, no cenário da aplicação **Workday,** o atributo Workday **WorkerID** é mapeado para o **atributoId do funcionário do Ative** Directory.

Pode definir vários atributos correspondentes e atribuir precedência correspondente. São avaliados em precedência correspondente. Assim que um fósforo é encontrado, não são avaliados mais atributos correspondentes.

Também pode [personalizar os mapeamentos de atributos predefinidos,](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types)tais como alterar ou eliminar mapeamentos de atributos existentes. Também pode criar novos mapeamentos de atributos de acordo com as necessidades do seu negócio. Para obter mais informações, consulte o tutorial da aplicação HR em nuvem (como [Workday)](../saas-apps/workday-inbound-tutorial.md#managing-your-configuration)para obter uma lista de atributos personalizados para mapear.

### <a name="determine-user-account-status"></a>Determinar o estado da conta do utilizador

Por predefinição, a aplicação de conector de provisionamento mapeia o estado do perfil do utilizador HR para o estado da conta de utilizador em Ative Directory ou Azure AD para determinar se permite ou desativa a conta do utilizador.

Quando iniciar o processo de Joiners-Leavers, reúna os seguintes requisitos.

| Processo | Requisitos |
| - | - |
| **Marceneiros** | Do ponto de vista do ciclo de vida da identidade, como lida com as recontratadas? As recontratadas mantêm as suas antigas identificações de empregados? |
| | Processa contratações futuras e cria contas de Ative Directory para elas com antecedência? Estas contas são criadas num estado ativado ou deficiente? |
| | Do ponto de vista do ciclo de vida identitário, como lida com o trabalhador até à conversão de trabalhadores contingentes, ou não? |
| | Os utilizadores convertidos mantêm as suas antigas contas de Ative Directory ou recebem novas? |
| **Leavers** | As rescisões são tratadas de forma diferente para os trabalhadores e trabalhadores contingentes no Ative Directory? |
| | Que datas efetivas são consideradas para o processamento da rescisão do utilizador? |
| | Como é que as conversões de trabalhadores e trabalhadores contingentes afetam as contas existentes do Ative Directory? |
| | Como processa a operação Rescind em Ative Directory? As operações rescinde devem ser tratadas se futuras contratações datadas forem criadas no Ative Directory como parte do processo Joiner. |

Dependendo dos seus requisitos, poderá personalizar a lógica de mapeamento utilizando [expressões AD do Azure](../app-provisioning/functions-for-customizing-application-data.md) para que a conta Ative Directory seja ativada ou desativada com base numa combinação de pontos de dados.

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>Aplicativo de HR em nuvem de mapa para atributos de utilizador do Ative Directory

Cada aplicativo hr em nuvem envia com app de RH em nuvem padrão para mapeamentos de Diretório Ativo.

Quando iniciar o processo Joiners-Movers-Leavers, reúna os seguintes requisitos.

| Processo | Requisitos |
| - | - |
| **Marceneiros** | O processo de criação de conta Ative Directory é manual, automatizado ou parcialmente automatizado? |
| | Planeia propagar atributos personalizados da aplicação cloud HR para Ative Directory? |
| **Mudanças** | Que atributos gostaria de processar sempre que uma operação da Movers ocorre na aplicação cloud HR? |
| | Executa validações específicas de atributos no momento das atualizações do utilizador? Se sim, forneça detalhes. |
| **Leavers** | As rescisões são tratadas de forma diferente para os trabalhadores e trabalhadores contingentes no Ative Directory? |
| | Que datas efetivas são consideradas para o processamento da rescisão do utilizador? |
| | Como é que as conversões de trabalhadores e trabalhadores contingentes têm impacto nas contas existentes do Ative Directory? |

Dependendo dos seus requisitos, pode modificar os mapeamentos para cumprir os seus objetivos de integração. Para obter mais informações, consulte o tutorial específico da aplicação HR em nuvem (como [Workday)](../saas-apps/workday-inbound-tutorial.md#part-4-configure-attribute-mappings)para obter uma lista de atributos personalizados para mapear.

### <a name="generate-a-unique-attribute-value"></a>Gerar um valor de atributo único

Quando inicia o processo Dederers, poderá ter de gerar valores únicos de atributos quando definir atributos como CN, samAccountName e UPN, que tem constrangimentos únicos.

A função AD AD [SelectUniqueValues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) avalia cada regra e, em seguida, verifica o valor gerado para a singularidade no sistema alvo. Por exemplo, consulte [Gerar valor único para o atributo userPrincipalName (UPN).](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute)

> [!NOTE]
> Atualmente, esta função é suportada apenas para o fornecimento de utilizadores do Workday to Ative Directory. Não pode ser usado com outras aplicações de provisionamento.

### <a name="configure-active-directory-ou-container-assignment"></a>Configurar atribuição de contentores ou do Diretório Ativo

É um requisito comum colocar contas de utilizadores do Ative Directory em contentores baseados em unidades de negócio, localizações e departamentos. Quando inicia um processo de Mudança, e se houver uma mudança de organização de supervisão, poderá ter de transferir o utilizador de um OU para outro no Ative Directory.

Utilize a função [Switch()](../app-provisioning/functions-for-customizing-application-data.md#switch) para configurar a lógica de negócio para a atribuição de OU, e mapeá-la para o Ative Directory atribui **o nome parentalDistinguished .**

Por exemplo, se pretender criar utilizadores em U com base no atributo HR **Município,** pode utilizar a seguinte expressão:

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

Com esta expressão, se o valor do Município for Dallas, Austin, Seattle ou Londres, a conta de utilizador será criada na omissão correspondente. Se não houver correspondência, então a conta é criada na U.U. predefinido.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Plano de entrega de senha de novas contas de utilizador

Quando inicia o processo 'Joiners', tem de definir e entregar uma senha temporária de novas contas de utilizador. Com o fornecimento de utilizadores de AD em nuvem para Azure, pode lançar a capacidade [de redefinição da palavra-passe de autosserviço](../authentication/tutorial-enable-sspr.md) AD (SSPR) para o utilizador no primeiro dia.

O SSPR é um meio simples para os administradores de TI permitirem que os utilizadores repusem as suas palavras-passe ou desbloqueiem as suas contas. Pode fornecê-lo do **atributo Número Móvel** da aplicação hr em nuvem ao Ative Directory e sincronizá-lo com Azure AD. Depois do atributo **Número Móvel** estar em Azure AD, pode ativar sSPR para a conta do utilizador. Em seguida, no primeiro dia, o novo utilizador pode utilizar o número de telemóvel registado e verificado para autenticação.

## <a name="plan-for-initial-cycle"></a>Plano para o ciclo inicial

Quando o serviço de fornecimento de AD AZure é executado pela primeira vez, executa um [ciclo inicial](../app-provisioning/how-provisioning-works.md#initial-cycle) contra a aplicação hr em nuvem para criar uma imagem de todos os objetos do utilizador na aplicação hr na nuvem. O tempo de curso para os ciclos iniciais depende diretamente do número de utilizadores presentes no sistema de origem. O ciclo inicial para alguns inquilinos de aplicativos hr em nuvem com mais de 100.000 utilizadores pode demorar muito tempo.

**Para os grandes inquilinos de aplicativos HR em nuvem (>30.000 utilizadores),** executar o ciclo inicial em estágios progressivos. Inicie as atualizações incrementais apenas depois de validar que os atributos corretos são definidos no Ative Directory para diferentes cenários de provisionamento do utilizador. Siga a ordem aqui.

1. Executar o ciclo inicial apenas para um conjunto limitado de utilizadores definindo o [filtro de deteção](#plan-scoping-filters-and-attribute-mapping).
2. Verifique o provisionamento da conta ative e os valores de atributos definidos para os utilizadores selecionados para a primeira execução. Se o resultado corresponde às suas expectativas, expanda o filtro de scoping para incluir progressivamente mais utilizadores e verifique os resultados da segunda execução.

Depois de estar satisfeito com os resultados do ciclo inicial para os utilizadores de teste, inicie as [atualizações incrementais](../app-provisioning/how-provisioning-works.md#incremental-cycles).

## <a name="plan-testing-and-security"></a>Testes e segurança do plano

Em cada fase da sua implementação do piloto inicial através da ativação do fornecimento do utilizador, certifique-se de que está a testar que os resultados são como esperado e auditando os ciclos de provisionamento.

### <a name="plan-testing"></a>Testes de plano

Depois de configurar a aplicação de HR em nuvem para o provisionamento do utilizador Azure AD, execute casos de teste para verificar se esta solução satisfaz os requisitos da sua organização.

|Cenários|Resultados esperados|
|:-|:-|
|O novo empregado é contratado na aplicação cloud HR.| - A conta de utilizador é aussada no Ative Directory.</br>- O utilizador pode iniciar sessão em aplicações de domínio ative directory e executar as ações desejadas.</br>- Se a sincronização Azure AD Connect estiver configurada, a conta de utilizador também é criada em Azure AD.
|O utilizador é encerrado na aplicação hr em nuvem.|- A conta de utilizador está desativada no Ative Directory.</br>- O utilizador não pode iniciar sessão em nenhuma aplicação empresarial protegida pelo Ative Directory.
|A organização de supervisão dos utilizadores é atualizada na aplicação cloud HR.|Com base no mapeamento do atributo, a conta de utilizador passa de um OU para outro no Ative Directory.|
|O RH atualiza o gestor do utilizador na aplicação cloud HR.|O campo de gestores em Ative Directory é atualizado para refletir o nome do novo gestor.|
|A HR recontraia um empregado para um novo papel.|O comportamento depende de como a aplicação hr em nuvem é configurada para gerar IDs dos funcionários:</br>- Se o antigo ID do empregado for reutilizado para uma recontratada, o conector permite a conta ative existente para o utilizador.</br>- Se a recontratar receber uma nova identificação do funcionário, o conector cria uma nova conta ative para o utilizador.|
|A HR converte o trabalhador a um trabalhador contratado ou vice-versa.|Uma nova conta Ative Directory é criada para a nova persona e a conta antiga é desativada na data efetiva da conversão.|

Utilize os resultados anteriores para determinar como transitar a implementação automática do fornecimento do utilizador para a produção com base nos prazos estabelecidos.

> [!TIP]
> Utilize técnicas como a redução de dados e a eliminação de dados quando atualizar o ambiente de teste com dados de produção para remover ou mascarar dados pessoais sensíveis para cumprir as normas de privacidade e segurança. 

### <a name="plan-security"></a>Segurança do plano

É comum que uma revisão de segurança seja necessária como parte da implantação de um novo serviço. Se for necessária ou não for realizada uma revisão de segurança, consulte os muitos [documentos brancos](https://www.microsoft.com/download/details.aspx?id=36391) da AZure AD que fornecem uma visão geral da identidade como serviço.

### <a name="plan-rollback"></a>Plano de reversão

A implementação do fornecimento de recursos humanos em nuvem pode não funcionar como desejado no ambiente de produção. Em caso afirmativo, os seguintes passos de reversão podem ajudá-lo a voltar a um estado de bem conhecido anterior.

1. Reveja o [relatório de provisão](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) e [os registos de provisionamento](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) para determinar que operações incorretas foram realizadas nos utilizadores ou grupos afetados. Para obter mais informações sobre o relatório e registos de resumo do provisionamento, consulte [gerir o fornecimento de aplicações hr em nuvem](#manage-your-configuration).
2. O último bom estado conhecido dos utilizadores ou grupos afetados pode ser determinado através dos registos de auditoria de provisionamento ou através da revisão dos sistemas-alvo (Azure AD ou Ative Directory).
3. Trabalhe com o proprietário da aplicação para atualizar os utilizadores ou grupos afetados diretamente na aplicação, utilizando os últimos valores de estado de bom estado conhecidos.

## <a name="deploy-the-cloud-hr-app"></a>Implementar a app hr em nuvem

Escolha a aplicação hr em nuvem que se alinha aos seus requisitos de solução.

**Dias úteis**: Para importar perfis de trabalhador de Workday para Ative Directory e Azure AD, consulte [Tutorial: Configure Workday para fornecimento automático de utilizadores](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment). Opcionalmente, pode rescrever o endereço de e-mail, nome de utilizador e número de telefone para workday.

**SAP SuccessFactors**: Para importar perfis de trabalhadores de SuccessFactors para Ative Directory e Ad AD, consulte [Tutorial: Configurar SAP SuccessFactors para fornecimento automático de utilizadores](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md). Opcionalmente, pode retornar o endereço de e-mail e o nome de utilizador para o SuccessFactors.

## <a name="manage-your-configuration"></a>Gerencie a sua configuração

O Azure AD pode fornecer informações adicionais sobre o uso do utilizador e saúde operacional da sua organização através de registos e relatórios de auditoria.

### <a name="gain-insights-from-reports-and-logs"></a>Obter informações de relatórios e registos

Após um [ciclo inicial](../app-provisioning/how-provisioning-works.md#initial-cycle)bem sucedido, o serviço de fornecimento de AD AZure continua a executar indefinidamente atualizações incrementais consecutivas, em intervalos definidos nos tutoriais específicos de cada aplicação, até que ocorra um dos seguintes eventos:

- O serviço está parado manualmente. Um novo ciclo inicial é desencadeado através da utilização do [portal Azure](https://portal.azure.com/) ou do comando API apropriado do [Microsoft Graph.](/graph/api/resources/synchronization-overview)
- Um novo ciclo inicial é desencadeado devido a uma alteração nos mapeamentos de atributos ou filtros de deteção.
- O processo de provisionamento entra em quarentena devido a uma elevada taxa de erro. Fica em quarentena por mais de quatro semanas, altura em que é automaticamente desativada.

Para rever estes eventos e todas as outras atividades realizadas pelo serviço de fornecimento, [aprenda a rever os registos e obtenha relatórios sobre a atividade de provisionamento.](../app-provisioning/check-status-user-account-provisioning.md)

#### <a name="azure-monitor-logs"></a>Registos do Azure Monitor

Todas as atividades realizadas pelo serviço de fornecimento são registadas nos registos de auditoria da AZure AD. Pode encaminhar os registos de auditoria do Azure AD para os registos do Azure Monitor para uma análise mais aprofundada. Com registos do Azure Monitor (também conhecido como Log Analytics workspace), pode consultar dados para encontrar eventos, analisar tendências e realizar a correlação entre várias fontes de dados. Veja este [vídeo](https://youtu.be/MP5IaCTwkQg) para saber os benefícios de usar registos do Azure Monitor para registos Azure AD em cenários práticos do utilizador.

Instale as [vistas de análise de registo para registos de atividades Azure AD](../reports-monitoring/howto-install-use-log-analytics-views.md) para ter acesso a [relatórios pré-construídos](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) em torno de eventos de provisionamento no seu ambiente.

Para obter mais informações, consulte como [analisar os registos de atividade do AZure AD com os seus registos do Azure Monitor](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md).

### <a name="manage-personal-data"></a>Gerir dados pessoais

O agente de provisionamento AZure AD Connect instalado no servidor windows cria registos no registo de eventos do Windows que podem conter dados pessoais dependendo da sua aplicação de HR em nuvem para mapeamentos de atributos ative Directory. Para cumprir as obrigações de privacidade dos utilizadores, crie uma tarefa agendada para o Windows para limpar o registo do evento e garantir que nenhum dado seja mantido para além de 48 horas.

O serviço de fornecimento de AD AZure não gera relatórios, realiza análises ou fornece insights para além de 30 dias porque o serviço não armazena, processa ou guarda quaisquer dados para além de 30 dias.

### <a name="troubleshoot"></a>Resolução de problemas

Para resolver problemas que possam surgir durante o provisionamento, consulte os seguintes artigos:

- [Problema de configuração do fornecimento de um utilizador a uma aplicação da Galeria AD Azure](application-provisioning-config-problem.md)
- [Sync um atributo do seu Ative Directy no local para Azure AD para provisionamento a uma aplicação](user-provisioning-sync-attributes-for-mapping.md)
- [Credenciais de administrador de poupança de problemas ao mesmo tempo que configura o fornecimento do utilizador a uma aplicação da Galeria do Diretório Ativo Azure](application-provisioning-config-problem-storage-limit.md)
- [Nenhum utilizadores está a ser a provisionado para uma aplicação da Galeria AD Azure](application-provisioning-config-problem-no-users-provisioned.md)
- [O conjunto errado de utilizadores está a ser a provisionado para uma aplicação da Galeria AD Azure](application-provisioning-config-problem-wrong-users-provisioned.md)
- [Configuração do Visualizador de Eventos do Windows para resolução de problemas do agente](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Criação de registos de auditoria do portal Azure para resolução de problemas de serviço](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [Compreender registos para conta de utilizador AD criar operações](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [Compreensão de registos para operações de atualização do Gestor](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [Resolução de erros geralmente encontrados](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Passos seguintes

- [Expressãos de escrita para mapeamentos de atributos](functions-for-customizing-application-data.md)
- [Visão geral da API de sincronização AZure AD](/graph/api/resources/synchronization-overview)
- [Ignorar a eliminação das contas de utilizador que ficam fora de alcance](skip-out-of-scope-deletions.md)
- [Azure AD Connect Provisioning Agent: O histórico de lançamento da versão](provisioning-agent-release-version-history.md)