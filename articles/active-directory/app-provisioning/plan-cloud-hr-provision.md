---
title: Planejar o aplicativo de RH de nuvem para Azure Active Directory provisionamento de usuário
description: Este artigo descreve o processo de implantação de integração de sistemas de RH de nuvem, como workday e SuccessFactors, com Azure Active Directory. A integração do Azure AD ao seu sistema de RH na nuvem resulta em um sistema completo de gerenciamento do ciclo de vida da identidade.
services: active-directory
author: martincoetzer
manager: CelesteDG
tags: azuread
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: martinco
ms.reviewer: arvindha
ms.openlocfilehash: 10bd6f1f8582ba048a3f77ff329d90f1b4463675
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066424"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Planejar o aplicativo de RH de nuvem para Azure Active Directory provisionamento de usuário

Historicamente, a equipe de ti dependia de métodos manuais para criar, atualizar e excluir funcionários. Eles usaram métodos como carregar arquivos CSV ou scripts personalizados para sincronizar dados de funcionários. Esses processos de provisionamento são propensos a erros, inseguros e difíceis de gerenciar.

Para gerir os ciclos de vida identitários de colaboradores, fornecedores ou trabalhadores contingentes, o serviço de prestação de [utilizadores azure Ative Directory (Azure AD)](../app-provisioning/user-provisioning.md) oferece integração com aplicações de recursos humanos (RH) baseadas na nuvem. Exemplos de aplicativos incluem workday ou SuccessFactors.

O Azure AD usa essa integração para habilitar os seguintes fluxos de trabalho do aplicativo de RH de nuvem (aplicativo):

- **Utilizadores de fornecimento para Diretório Ativo:** Disponibilize conjuntos selecionados de utilizadores de uma aplicação de HR em nuvem para um ou mais domínios de Diretório Ativo.
- **Fornecer utilizadores apenas em nuvem para AD Azure:** Em cenários em que o Ative Directory não é utilizado, os utilizadores disponibilizam utilizadores diretamente da aplicação de RH em nuvem para a AD Azure.
- **Escreva de volta para a aplicação de HR em nuvem:** Escreva os endereços de e-mail e os atributos de nome de utilizador do Azure AD de volta à aplicação de HR cloud.

> [!NOTE]
> Este plano de implantação mostra como implantar seus fluxos de trabalho de aplicativo de RH na nuvem com o provisionamento de usuário do Azure AD. Para obter informações sobre como implementar o fornecimento automático de utilizadores ao software como aplicações de serviço (SaaS), consulte [Plan uma implementação automática](https://aka.ms/deploymentplans/provisioning)de fornecimento de utilizadores .

## <a name="enabled-hr-scenarios"></a>Cenários de RH habilitados

O serviço de provisionamento de usuários do Azure AD permite a automação dos seguintes cenários de gerenciamento de ciclo de vida de identidade com base em RH:

- **Contratação de novos empregados:** Quando um novo funcionário é adicionado à aplicação de HR na nuvem, uma conta de utilizador é automaticamente criada em Ative Directory e Azure AD com a opção de reescrever o endereço de e-mail e os atributos de username para a aplicação de HR na nuvem.
- **Atributo sie e atualizações de perfil:** Quando um registo de empregados, como nome, título ou gestor, é atualizado na aplicação de HR na nuvem, a sua conta de utilizador é automaticamente atualizada em Ative Directory e Azure AD.
- **Rescisões de funcionários:** Quando um funcionário é rescindido na aplicação de RH em nuvem, a sua conta de utilizador é automaticamente desativada em Ative Directory e Azure AD.
- **Recontratações de empregados:** Quando um empregado é recontratado na aplicação de RH em nuvem, a sua conta antiga pode ser automaticamente reativada ou reprovisionada para Ative Directory e Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>A quem é essa integração mais adequada?

A integração do aplicativo de RH de nuvem com o provisionamento de usuário do Azure AD é ideal para as organizações que:

- Deseja uma solução predefinida baseada em nuvem para o provisionamento de usuário de RH na nuvem.
- Exigir o provisionamento direto de usuário do aplicativo de RH na nuvem para Active Directory ou o Azure AD.
- Exigir que os usuários sejam provisionados usando os dados obtidos do aplicativo de RH na nuvem.
- Exigir ingressar, mover e deixar os usuários serem sincronizados com um ou mais Active Directory florestas, domínios e UOs com base apenas nas informações de alteração detectadas no aplicativo de RH de nuvem.
- Use o Office 365 para email.

## <a name="learn"></a>Saiba mais

O provisionamento de usuário cria uma base para a governança de identidade contínua. Ele aprimora a qualidade dos processos de negócios que dependem de dados de identidade autoritativa.

### <a name="terms"></a>Termos

Este artigo usa os seguintes termos:

- **Sistema de origem**: O repositório de utilizadores que o Azure AD prevê. Um exemplo é um aplicativo de RH de nuvem, como workday ou SuccessFactors.
- **Sistema de destino**: O repositório de utilizadores a que o Azure AD prevê. Os exemplos são Active Directory, Azure AD, Office 365 ou outros aplicativos SaaS.
- **Processo Joiners-Movers-Leavers**: Um termo usado para novas contratações, transferências e rescisão utilizando uma aplicação de RH na nuvem como um sistema de registos. O processo é concluído quando o serviço provisiona com êxito os atributos necessários para o sistema de destino.

### <a name="key-benefits"></a>Principais vantagens

Esse recurso de provisionamento de ti controlado por RH oferece os seguintes benefícios de negócios significativos:

- **Aumentar a produtividade:** Agora pode automatizar a atribuição de contas de utilizador e licenças do Office 365 e fornecer acesso a grupos-chave. A automação de atribuições dá ao novo contratado acesso imediato às suas ferramentas de trabalho e aumenta a produtividade.
- **Gerir o risco:** Pode aumentar a segurança automatizando alterações com base no estado dos funcionários ou membros do grupo com dados provenientes da aplicação de RH cloud. Automatizar as alterações garante que as identidades do usuário e o acesso a aplicativos de chave sejam atualizados automaticamente quando os usuários fizerem a transição ou a saída da organização.
- **Abordar o cumprimento e a governação:** A Azure AD suporta registos de auditoria nativas para pedidos de fornecimento de utilizadores realizados por aplicações de sistemas de origem e alvo. Com a auditoria, você pode controlar quem tem acesso aos aplicativos de uma única tela.
- **Gerir o custo:** O fornecimento automático reduz os custos evitando ineficiências e erros humanos associados ao provisionamento manual. Ele reduz a necessidade de soluções de provisionamento de usuário desenvolvidas personalizadas criadas com o passar do tempo usando plataformas herdadas e desatualizadas.

### <a name="licensing"></a>Licenciamento

Para configurar a aplicação de RH em nuvem para a integração de fornecimento de utilizadores da AD Azure, você precisa de uma [licença Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) válida e uma licença para a app de RH na nuvem, como Workday ou SuccessFactors.

Você também precisa de uma licença de assinatura Azure AD Premium P1 ou superior válida para cada usuário que será originado do aplicativo de RH na nuvem e provisionado para o Active Directory ou o Azure AD. Qualquer número inadequado de licenças pertencentes ao aplicativo de RH na nuvem pode levar a erros durante o provisionamento do usuário.

### <a name="prerequisites"></a>Pré-requisitos

- Acesso de administrador global do Azure AD para configurar o agente de provisionamento do Azure AD Connect.
- Uma instância de teste e produção do aplicativo de RH de nuvem.
- Permissões de administrador no aplicativo de RH de nuvem para criar um usuário de integração do sistema e fazer alterações para testar dados de funcionários para fins de teste.
- Para o fornecimento de utilizadores ao Ative Directory, é necessário um servidor que execute o Windows Server 2012 ou superior com um prazo de funcionamento .NET 4.7.1+ para acolher o agente de [provisionamento Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=847801).
- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) para sincronizar utilizadores entre o Ative Directory e o Azure AD.

### <a name="training-resources"></a>Recursos de treinamento

| **Recursos** | **Link e descrição** |
|:-|:-|
| Vídeos | [O que é o fornecimento de utilizadores no Diretório Ative Azure?](https://youtu.be/_ZjARPpI6NI) |
| | [Como implementar o fornecimento de utilizadores no Diretório Ative Azure](https://youtu.be/pKzyts6kfrw) |
| Tutoriais | [Lista de tutoriais sobre como integrar aplicações do SaaS com a Azure AD](../saas-apps/tutorial-list.md) |
| | [Tutorial: Configure workday para fornecimento automático de utilizadores](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| FAQ | [Fornecimento automatizado de utilizadores](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Prestação do Dia de Trabalho para a AD Azure](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Arquitetura de soluções

O exemplo a seguir descreve a arquitetura da solução de provisionamento de usuário de ponta a ponta para ambientes híbridos comuns e inclui:

- **Os dados de RH autoritários fluem da aplicação de RH em nuvem para o Diretório Ativo.** Nesse fluxo, o evento de RH (emissores-movimentadores-perprocessors-periões) é iniciado no locatário do aplicativo de RH na nuvem. O serviço de provisionamento do Azure AD e o agente de provisionamento do Azure AD Connect provisionar os dados do usuário do locatário do aplicativo de RH na nuvem no Active Directory. Dependendo do evento, ele pode levar a criar, atualizar, habilitar e desabilitar operações no Active Directory.
- **Sync with Azure AD e escreva e-mail e username de on-premises Ative Directory para cloud HR app.** Depois que as contas são atualizadas no Active Directory, elas são sincronizadas com o Azure AD por meio de Azure AD Connect. Os endereços de email e os atributos de nome de usuário podem ser gravados no locatário do aplicativo de RH na nuvem.

![Diagrama de fluxo de trabalho](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>Descrição do fluxo de trabalho

As seguintes etapas principais são indicadas no diagrama:  

1. **A equipa de RH** realiza as transações no inquilino da aplicação de RH em nuvem.
2. O serviço de provisionamento da **Azure AD** executa os ciclos programados a partir do inquilino da aplicação de aplicação de RH em nuvem e identifica alterações que precisam de ser processadas para sincronização com o Ative Directory.
3. O serviço de provisionamento da **Azure AD AD** invoca o agente de provisionamento Azure AD Connect com uma carga útil de pedido que contém operações de diretório ativo que criam, atualizam, ativam e desativam as operações.
4. O agente de **provisionamento Azure AD Connect** utiliza uma conta de serviço para gerir os dados da conta do Diretório Ativo.
5. **O Azure AD Connect** executa a [delta sync](../hybrid/how-to-connect-sync-whatis.md) para puxar atualizações no Ative Directory.
6. As atualizações **de Diretório Ativo** estão sincronizadas com a AD Azure.
7. O serviço de provisionamento da **Azure AD** reescreve o atributo de e-mail e o nome de utilizador de Azure AD para o inquilino da aplicação de aplicações de RH em nuvem.

## <a name="plan-the-deployment-project"></a>Planejar o projeto de implantação

Considere suas necessidades organizacionais enquanto determina a estratégia para essa implantação em seu ambiente.

### <a name="engage-the-right-stakeholders"></a>Envolva os participantes certos

Quando os projetos de tecnologia falham, normalmente fazem isso devido a expectativas incompatíveis com o impacto, os resultados e as responsabilidades. Para evitar estas armadilhas, [certifique-se de que está a envolver as partes interessadas certas.](https://aka.ms/deploymentplans) Além disso, certifique-se de que as funções de Stakeholder no projeto sejam bem compreendidas. Documente os participantes e suas entradas e responsabilidades de projeto.

Inclua um representante da organização de RH que possa fornecer entradas sobre processos de negócios e identidade de trabalho existentes, além de requisitos de processamento de dados de trabalho.

### <a name="plan-communications"></a>Plan communications

A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunique-se proativamente com seus usuários sobre quando e como sua experiência será alterada. Avise-os sobre como obter suporte se eles tiverem problemas.

### <a name="plan-a-pilot"></a>Planejar um piloto

A integração dos processos de negócios de RH e dos fluxos de trabalho de identidade do aplicativo de RH na nuvem aos sistemas de destino requer uma quantidade considerável de validação de dados, transformação de dados, limpeza de dados e teste de ponta a ponta antes que você possa implantar a solução na produção.

Execute a configuração inicial num [ambiente piloto](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) antes de a dimensionar para todos os utilizadores em produção.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Selecionar aplicativos do conector de provisionamento de RH de nuvem

Para facilitar o provisionamento de fluxos de trabalho do Azure AD entre o aplicativo de RH de nuvem e Active Directory, você pode adicionar vários aplicativos de conector de provisionamento da Galeria de aplicativos do Azure AD:

- **Aplicação Cloud HR para o fornecimento**de utilizadores do Diretório Ativo : Esta aplicação de provisionamento de conector facilita o fornecimento de conta de utilizador da aplicação cloud HR para um único domínio de Diretório Ativo. Se você tiver vários domínios, poderá adicionar uma instância desse aplicativo da Galeria de aplicativos do Azure AD para cada domínio de Active Directory ao qual você precisa provisionar.
- Aplicação Cloud HR para fornecimento de utilizadores da **AD Azure**: Enquanto o Azure AD Connect é a ferramenta que deve ser usada para sincronizar utilizadores de Diretório Ativo para o Azure AD, esta aplicação de conector de provisionamento pode ser usada para facilitar o fornecimento de utilizadores apenas na nuvem da aplicação de RH cloud a um único inquilino da AD Azure.
- **Redação da aplicação Cloud HR**: Esta aplicação de conector de fornecimento facilita a devolução dos endereços de e-mail do utilizador do Azure AD para a aplicação cloud HR.

Por exemplo, a imagem a seguir lista os aplicativos do conector do workday que estão disponíveis na Galeria de aplicativos do Azure AD.

![Galeria de aplicativos do portal Azure Active Directory](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>Gráfico de fluxo de decisão

Use o gráfico de fluxo de decisão a seguir para identificar quais aplicativos de provisionamento de RH de nuvem são relevantes para seu cenário.

![Gráfico de fluxo de decisão](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>Criar a topologia de implantação do agente de provisionamento do Azure AD Connect

A integração de provisionamento entre o aplicativo de RH na nuvem e o Active Directory requer quatro componentes:

- Locatário do aplicativo de RH na nuvem
- Aplicativo do conector de provisionamento
- Agente de provisionamento do Azure AD Connect
- Domínio do Active Directory

A topologia de implantação do agente de provisionamento do Azure AD Connect depende do número de locatários do aplicativo de RH de nuvem e Active Directory domínios filho que você planeja integrar. Se tiver vários domínios de Diretório Ativo, depende se os domínios do Diretório Ativo são contíguos ou [disconjuntos.](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/disjoint-namespace)

Com base em sua decisão, escolha um dos cenários de implantação:

- Locatário de aplicativo de RH de nuvem única-> direcionar um ou vários domínios filho Active Directory em uma floresta confiável
- Locatário de aplicativo de RH de nuvem única-> direcionar vários domínios filho em uma floresta de Active Directory não contíguo

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>Locatário de aplicativo de RH de nuvem única-> direcionar um ou vários domínios filho Active Directory em uma floresta confiável

Recomendamos a seguinte configuração de produção:

|Requisito|Recomendação|
|:-|:-|
|Número de agentes de provisionamento de Azure AD Connect a serem implantados|Dois (para alta disponibilidade e failover)
|Número de aplicativos do conector de provisionamento a serem configurados|Um aplicativo por domínio filho|
|Host do servidor para o agente de provisionamento do Azure AD Connect|Windows 2012 R2 + com linha de visão de Active Directory controladores de domínio geolocalizados</br>Pode coexistir com o serviço Azure AD Connect|

![Fluxo para agentes locais](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>Locatário de aplicativo de RH de nuvem única-> direcionar vários domínios filho em uma floresta de Active Directory não contíguo

Esse cenário envolve o provisionamento de usuários do aplicativo de RH na nuvem para domínios em florestas de Active Directory não contíguos.

Recomendamos a seguinte configuração de produção:

|Requisito|Recomendação|
|:-|:-|
|Número de agentes de provisionamento de Azure AD Connect para implantar no local|Dois por floresta de Active Directory não contíguo|
|Número de aplicativos do conector de provisionamento a serem configurados|Um aplicativo por domínio filho|
|Host do servidor para o agente de provisionamento do Azure AD Connect|Windows 2012 R2 + com linha de visão de Active Directory controladores de domínio geolocalizados</br>Pode coexistir com o serviço Azure AD Connect|

![Floresta de Active Directory de locatário de aplicativo de RH de nuvem única](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Requisitos do agente de provisionamento do Azure AD Connect

O aplicativo de RH na nuvem para Active Directory solução de provisionamento de usuário requer que você implante um ou mais Azure AD Connect agentes de provisionamento em servidores que executam o Windows 2012 R2 ou superior. Os servidores devem ter, no mínimo, 4 GB de RAM e o tempo de execução do .NET 4.7.1 +. Verifique se o servidor host tem acesso à rede para o domínio de Active Directory de destino.

Para preparar o ambiente no local, o assistente de configuração do agente de configuração do Agente Azure AD Connect regista o agente com o seu inquilino Azure AD, [abre portas,](../manage-apps/application-proxy-add-on-premises-application.md#open-ports) [permite o acesso aos URLs,](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls)e suporta a configuração de proxy HTTPS de [saída.](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)

O agente de provisionamento usa uma conta de serviço para se comunicar com os domínios de Active Directory. Antes de instalar o agente, crie uma conta de serviço em Active Directory usuários e computadores que atendam aos seguintes requisitos:

- Uma senha que não expira
- Permissões de controle delegado para ler, criar, excluir e gerenciar contas de usuário

Você pode selecionar controladores de domínio que devem lidar com solicitações de provisionamento. Se você tiver vários controladores de domínio distribuídos geograficamente, instale o agente de provisionamento no mesmo site que seus controladores de domínio preferenciais. Esse posicionamento melhora a confiabilidade e o desempenho da solução de ponta a ponta.

Para alta disponibilidade, você pode implantar mais de um agente de provisionamento do Azure AD Connect. Registre o agente para lidar com o mesmo conjunto de domínios de Active Directory locais.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Planejar filtros de escopo e mapeamento de atributos

Quando você habilita o provisionamento do aplicativo de RH na nuvem para Active Directory ou o Azure AD, o portal do Azure controla os valores de atributo por meio do mapeamento de atributos.

### <a name="define-scoping-filters"></a>Definir filtros de escopo

Utilize filtros de [deteção](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) para definir as regras baseadas em atributos que determinam quais os utilizadores que devem ser aprovisionados a partir da aplicação de RH em nuvem para Ative Directory ou Azure AD.

Ao iniciar o processo de ingressos, reúna os seguintes requisitos:

- O aplicativo de RH em nuvem é usado para trazer tanto funcionários quanto trabalhadores contingentes?
- Você planeja usar o aplicativo de RH de nuvem para o provisionamento de usuário do Azure AD para gerenciar funcionários e operadores contingentes?
- Você planeja distribuir o aplicativo de RH de nuvem para o provisionamento de usuário do Azure AD apenas para um subconjunto dos usuários do aplicativo de RH na nuvem? Um exemplo pode ser apenas funcionários.

Dependendo dos seus requisitos, quando configurar mapeamentos de atributos, pode definir o campo **Source Object Scope** para selecionar quais os conjuntos de utilizadores na aplicação de RH cloud devem estar disponíveis para o fornecimento ao Ative Directory. Para obter mais informações, consulte o tutorial do aplicativo de RH na nuvem para filtros de escopo mais usados.

### <a name="determine-matching-attributes"></a>Determinar atributos correspondentes

Com o provisionamento, você obtém a capacidade de corresponder as contas existentes entre o sistema de origem e de destino. Quando integrar a aplicação de RH em nuvem com o serviço de provisionamento de Anúncios Azure, pode [configurar o mapeamento de atributos](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings) para determinar quais os dados dos utilizadores que devem fluir da aplicação de RH em nuvem para Ative Directory ou Azure AD.

Ao iniciar o processo de ingressos, reúna os seguintes requisitos:

- Qual é a ID exclusiva neste aplicativo de RH de nuvem que é usada para identificar cada usuário?
- De uma perspectiva do ciclo de vida da identidade, como lidar com recontratações? As recontratações mantêm suas IDs de funcionário antigas?
- Você processa contratações no futuro e cria Active Directory contas com antecedência?
- De uma perspectiva do ciclo de vida de identidade, como você lida com a conversão de funcionário para um trabalho contingente ou de outra forma?
- Os usuários convertidos mantêm suas contas Active Directory antigas ou obtêm novos?

Dependendo dos seus requisitos, a AD Azure suporta o mapeamento direto de atributo-a-atributo, fornecendo valores constantes ou [expressões de escrita para mapeamento de atributos](../app-provisioning/functions-for-customizing-application-data.md). Essa flexibilidade lhe dá um controle definitivo do que é populado no atributo de aplicativo de destino. Pode utilizar o [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) e o Graph Explorer para exportar mapeamentos e esquemas para um ficheiro JSON e importá-lo de volta para AD Azure.

Por padrão, o atributo na aplicação de HR cloud que representa o ID único do empregado é usado como o atributo correspondente *mapeado ao atributo único no Diretório Ativo.* Por exemplo, no cenário da aplicação Workday, o atributo **workday** **WorkerID** é mapeado para o atributo **de funcionário** do Ative Directory.

Você pode definir vários atributos correspondentes e atribuir precedência correspondente. Elas são avaliadas na precedência correspondente. Assim que uma correspondência for encontrada, nenhum atributo correspondente será avaliado.

Também pode [personalizar os mapeamentos de atributos padrão,](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types)tais como alterar ou apagar mapeamentos de atributos existentes. Você também pode criar novos mapeamentos de atributo de acordo com suas necessidades de negócios. Para mais informações, consulte o tutorial da aplicação de CLOUD HR (como [o Workday)](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)para obter uma lista de atributos personalizados ao mapa.

### <a name="determine-user-account-status"></a>Determinar o status da conta de usuário

Por padrão, o aplicativo do conector de provisionamento mapeia o status do perfil de usuário de RH para o status da conta de usuário no Active Directory ou no Azure AD para determinar se a conta de usuário deve ser habilitada ou desabilitada.

Quando você inicia o processo dos emissores-periões, reúna os seguintes requisitos.

| Processo | Requisitos |
| - | - |
| **Marcinadores** | De uma perspectiva do ciclo de vida da identidade, como lidar com recontratações? As recontratações mantêm suas IDs de funcionário antigas? |
| | Você processa contratações no futuro e cria Active Directory contas com antecedência? Essas contas são criadas em um estado habilitado ou desabilitado? |
| | De uma perspectiva do ciclo de vida de identidade, como você lida com a conversão de funcionário para um trabalho contingente ou de outra forma? |
| | Os usuários convertidos mantêm suas contas Active Directory antigas ou obtêm novos? |
| **Desistentes** | Os encerramentos são tratados de forma diferente para funcionários e funcionários contingentes no Active Directory? |
| | Quais datas efetivas são consideradas para processar o encerramento do usuário? |
| | Como as conversões de funcionário e de operador de contingentes afetam contas de Active Directory existentes? |
| | Como processar a operação de rescindir no Active Directory? As operações de rescindir precisam ser manipuladas se contratações com data futuras são criadas no Active Directory como parte do processo do associador. |

Dependendo dos seus requisitos, poderá personalizar a lógica de mapeamento utilizando [expressões AD Azure](../app-provisioning/functions-for-customizing-application-data.md) para que a conta Ative Directy esteja ativada ou desativada com base numa combinação de pontos de dados.

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>Mapear aplicativo de RH de nuvem para Active Directory atributos de usuário

Cada aplicativo de RH na nuvem é fornecido com o aplicativo de RH de nuvem padrão para mapeamentos de Active Directory.

Ao iniciar o processo de Complementos-movimentadores-periões, reúna os seguintes requisitos.

| Processo | Requisitos |
| - | - |
| **Marcinadores** | O processo de criação de conta Active Directory manual, automatizado ou parcialmente automatizado? |
| | Você planeja propagar atributos personalizados do aplicativo de RH de nuvem para Active Directory? |
| **Mudanças** | Quais atributos você gostaria de processar sempre que uma operação de movimentação ocorre no aplicativo de RH de nuvem? |
| | Você realiza qualquer validação de atributo específica no momento das atualizações do usuário? Em caso afirmativo, forneça detalhes. |
| **Desistentes** | Os encerramentos são tratados de forma diferente para funcionários e funcionários contingentes no Active Directory? |
| | Quais datas efetivas são consideradas para processar o encerramento do usuário? |
| | Como as conversões de funcionário e de operador contingent afetam as contas de Active Directory existentes? |

Dependendo dos seus requisitos, você pode modificar os mapeamentos para atender às metas de integração. Para mais informações, consulte o tutorial específico da aplicação de CLOUD HR (como [o Workday)](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)para obter uma lista de atributos personalizados ao mapa.

### <a name="generate-a-unique-attribute-value"></a>Gerar um valor de atributo exclusivo

Ao iniciar o processo dos ASSOCIADORES, talvez seja necessário gerar valores de atributos exclusivos ao definir atributos como CN, samAccountName e o UPN, que tem restrições exclusivas.

A função Azure AD [SelectUniqueValues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) avalia cada regra e, em seguida, verifica o valor gerado para a singularidade no sistema alvo. Por exemplo, consulte Gerar valor único para o atributo do [utilizadorPrincipalName (UPN).](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute)

> [!NOTE]
> Atualmente, essa função só tem suporte para o workday Active Directory provisionamento de usuário. Ele não pode ser usado com outros aplicativos de provisionamento.

### <a name="configure-active-directory-ou-container-assignment"></a>Configurar atribuição de contêiner de UO de Active Directory

É um requisito comum para posicionar Active Directory contas de usuário em contêineres com base em unidades de negócios, locais e departamentos. Quando você inicia um processo de movimentação e, se houver uma alteração na organização, talvez seja necessário mover o usuário de uma UO para outra em Active Directory.

Utilize a função [Switch()](../app-provisioning/functions-for-customizing-application-data.md#switch) para configurar a lógica de negócio para a atribuição de OU e mapeá-la para o design de designado **do**Diretório Ativo .

Por exemplo, se quiser criar utilizadores em Ou com base no atributo de RH **Município,** pode usar a seguinte expressão:

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

Com essa expressão, se o valor de Municipal for Dallas, Austin, Seattle ou Londres, a conta de usuário será criada na UO correspondente. Se não houver correspondência, a conta será criada na UO padrão.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Planejar a entrega de senha de novas contas de usuário

Ao iniciar o processo dos ASSOCIADORES, você precisa definir e entregar uma senha temporária de novas contas de usuário. Com o fornecimento de utilizadores de AD em nuvem para AD Azure, pode lançar a capacidade de redefinição de senha de [autosserviço](../authentication/quickstart-sspr.md) azure AD (SSPR) para o utilizador no primeiro dia.

O SSPR é um meio simples para os administradores de ti permitirem que os usuários redefinam suas senhas ou desbloqueiem suas contas. Pode fornecer o atributo do **Número Móvel** da aplicação cloud HR para Ative Directory e sincronizá-lo com O AD Azure. Depois do **atributo** do Número Móvel estar em Azure AD, pode ativar a SSPR para a conta do utilizador. Em seguida, no primeiro dia, o novo usuário pode usar o número de celular registrado e verificado para autenticação.

## <a name="plan-for-initial-cycle"></a>Planejar o ciclo inicial

Quando o serviço de provisionamento de AD Azure funciona pela primeira vez, realiza um [ciclo inicial](../app-provisioning/how-provisioning-works.md#initial-cycle) contra a aplicação de HR em nuvem para criar uma imagem de todos os objetos de utilizador na aplicação de HR cloud. O tempo necessário para os ciclos iniciais é diretamente dependente de quantos usuários estão presentes no sistema de origem. O ciclo inicial de alguns locatários do aplicativo de RH na nuvem com mais de 100.000 usuários pode levar muito tempo.

Para grandes inquilinos de aplicações de RH em **nuvem (>30.000 utilizadores),** executar o ciclo inicial em fases progressivas. Inicie as atualizações incrementais somente depois de validar que os atributos corretos estão definidos em Active Directory para diferentes cenários de provisionamento de usuário. Siga a ordem aqui.

1. Executar o ciclo inicial apenas para um conjunto limitado de utilizadores, definindo o filtro de [deteção](#plan-scoping-filters-and-attribute-mapping).
2. Verifique Active Directory provisionamento de conta e os valores de atributo definidos para os usuários selecionados para a primeira execução. Se o resultado atender às suas expectativas, expanda o filtro de escopo para incluir progressivamente mais usuários e verificar os resultados da segunda execução.

Depois de estar satisfeito com os resultados do ciclo inicial para os utilizadores de testes, inicie as [atualizações incrementais](../app-provisioning/how-provisioning-works.md#incremental-cycles).

## <a name="plan-testing-and-security"></a>Teste e segurança de planos

Em cada estágio de sua implantação do piloto inicial por meio da habilitação do provisionamento de usuário, verifique se você está testando se os resultados estão conforme o esperado e auditando os ciclos de provisionamento.

### <a name="plan-testing"></a>Teste de plano

Depois de configurar o aplicativo de RH na nuvem para o provisionamento de usuário do Azure AD, execute os casos de teste para verificar se essa solução atende aos requisitos da sua organização.

|Cenários|Resultados esperados|
|:-|:-|
|O novo funcionário é contratado no aplicativo de RH na nuvem.| -A conta de usuário é provisionada no Active Directory.</br>-O usuário pode fazer logon em aplicativos de Active Directory domínio e executar as ações desejadas.</br>-Se Azure AD Connect sincronização estiver configurada, a conta de usuário também será criada no Azure AD.
|O usuário é encerrado no aplicativo de RH na nuvem.|-A conta de usuário está desabilitada no Active Directory.</br>-O usuário não pode fazer logon em nenhum aplicativo corporativo protegido pelo Active Directory.
|A organização de supervisão do usuário é atualizada no aplicativo de RH de nuvem.|Com base no mapeamento de atributo, a conta de usuário passa de uma UO para outra em Active Directory.|
|O HR atualiza o gerente do usuário no aplicativo de RH na nuvem.|O campo gerente no Active Directory é atualizado para refletir o nome do novo gerente.|
|O HR recontrata um funcionário em uma nova função.|O comportamento depende de como o aplicativo de RH de nuvem está configurado para gerar IDs de funcionário:</br>-Se a ID antiga do funcionário for recontratada, o conector habilitará a conta de Active Directory existente para o usuário.</br>-Se o novo contratação receber uma nova ID de funcionário, o conector criará uma nova conta de Active Directory para o usuário.|
|HR converte o funcionário em um funcionário de contrato ou vice-versa.|Uma nova conta de Active Directory é criada para a nova pessoa e a conta antiga é desabilitada na data de efetivação da conversão.|

Use os resultados anteriores para determinar como fazer a transição de sua implementação de provisionamento automático de usuário para produção com base em suas linhas do tempo estabelecidas.

> [!TIP]
> Use técnicas como a redução de dados e a depuração de dados ao atualizar o ambiente de teste com dados de produção para remover ou mascarar dados pessoais confidenciais para atender aos padrões de privacidade e segurança. 

### <a name="plan-security"></a>Planear a segurança

É comum que uma revisão de segurança seja exigida como parte da implantação de um novo serviço. Se for necessária uma revisão de segurança ou não tiver sido realizada, consulte os muitos [documentos brancos](https://www.microsoft.com/download/details.aspx?id=36391) da AD Azure que fornecem uma visão geral da identidade como um serviço.

### <a name="plan-rollback"></a>Reversão do plano

A implementação de provisionamento de usuário de RH na nuvem pode falhar ao funcionar conforme desejado no ambiente de produção. Nesse caso, as etapas de reversão a seguir podem ajudá-lo a reverter para um estado bom conhecido anterior.

1. Reveja o [relatório sumário](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) de fornecimento e os [registos de fornecimento](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) para determinar que operações incorretas foram realizadas nos utilizadores ou grupos afetados. Para obter mais informações sobre o relatório e registos de fornecimento, consulte gerir o fornecimento de utilizadores de [aplicações em nuvem HR](#manage-your-configuration).
2. O último estado bom conhecido dos usuários ou grupos afetados pode ser determinado por meio dos logs de auditoria de provisionamento ou revisando os sistemas de destino (Azure AD ou Active Directory).
3. Trabalhe com o proprietário do aplicativo para atualizar os usuários ou grupos afetados diretamente no aplicativo usando os últimos valores de estado válidos conhecidos.

## <a name="deploy-the-cloud-hr-app"></a>Implantar o aplicativo de RH na nuvem

Escolha o aplicativo de RH de nuvem que se alinha aos seus requisitos de solução.

**Dia de Trabalho**: Para importar perfis de trabalhador do Dia de Trabalho para Diretório Ativo e Anúncio Azure, consulte [Tutorial: Configure Workday para o fornecimento automático de utilizadores](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment). Opcionalmente, você pode gravar novamente o endereço de email e o nome de usuário para workday.

## <a name="manage-your-configuration"></a>Gerenciar sua configuração

O Azure AD pode fornecer informações adicionais sobre o uso de provisionamento de usuário da sua organização e a integridade operacional por meio de relatórios e logs de auditoria.

### <a name="gain-insights-from-reports-and-logs"></a>Obter informações de relatórios e logs

Após um [ciclo inicial](../app-provisioning/how-provisioning-works.md#initial-cycle)bem sucedido, o serviço de provisionamento de AD Azure continua a executar atualizações incrementais de back-to-back indefinidamente, em intervalos definidos nos tutoriais específicos de cada aplicação, até que um dos seguintes eventos ocorra:

- O serviço é interrompido manualmente. Um novo ciclo inicial é desencadeado através do [portal Azure](https://portal.azure.com/) ou do comando API do [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) apropriado.
- Um novo ciclo inicial é disparado devido a uma alteração nos mapeamentos de atributo ou em filtros de escopo.
- O processo de provisionamento entra em quarentena devido a uma alta taxa de erros. Ele permanece em quarentena por mais de quatro semanas, quando é automaticamente desabilitado.

Para rever estes eventos e todas as outras atividades realizadas pelo serviço de provisionamento, [aprenda a rever os registos e obtenha relatórios sobre a atividade de provisionamento.](../app-provisioning/check-status-user-account-provisioning.md)

#### <a name="azure-monitor-logs"></a>Registos do Azure Monitor

Todas as atividades executadas pelo serviço de provisionamento são registradas nos logs de auditoria do Azure AD. Você pode rotear os logs de auditoria do Azure AD para Azure Monitor logs para análise posterior. Com os logs de Azure Monitor (também conhecidos como Log Analytics espaço de trabalho), você pode consultar dados para localizar eventos, analisar tendências e executar a correlação entre várias fontes de dados. Veja este [vídeo](https://youtu.be/MP5IaCTwkQg) para saber os benefícios de utilizar registos do Azure Monitor para registos de Anúncios Azure em cenários práticos de utilizador.

Instale as vistas de análise de registos de atividade do [Azure AD para](../reports-monitoring/howto-install-use-log-analytics-views.md) ter acesso a [relatórios pré-construídos](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) em torno de eventos de provisionamento no seu ambiente.

Para mais informações, consulte como analisar os registos de [atividade da AD Azure com os seus registos do Monitor Azure](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md).

### <a name="manage-personal-data"></a>Gerir dados pessoais

O agente de provisionamento do Azure AD Connect instalado no Windows Server cria logs no log de eventos do Windows que podem conter dados pessoais, dependendo do seu aplicativo de RH na nuvem para Active Directory mapeamentos de atributo. Para estar em conformidade com as obrigações de privacidade do usuário, configure uma tarefa agendada do Windows para limpar o log de eventos e garantir que nenhum dado seja mantido além de 48 horas.

O serviço de provisionamento do Azure AD não gera relatórios, executa análises ou fornece informações além de 30 dias, pois o serviço não armazena, processa nem mantém dados além de 30 dias.

### <a name="troubleshoot"></a>Resolver Problemas

Para solucionar quaisquer problemas que possam ser reativados durante o provisionamento, consulte os seguintes artigos:

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
