---
title: Planejar o aplicativo de RH de nuvem para Azure Active Directory provisionamento de usuário
description: Este artigo descreve o processo de implantação de integração de sistemas de RH de nuvem, como workday e Sucessfactors com Azure Active Directory. A integração do Azure AD ao seu sistema de RH na nuvem resulta em um sistema completo de gerenciamento do ciclo de vida da identidade.
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
ms.openlocfilehash: 5d55aafc29b3b022d1023077d2d8f459b0608ae7
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555655"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Planejar o aplicativo de RH de nuvem para Azure Active Directory provisionamento de usuário

Historicamente, a equipe de ti dependia de métodos manuais de criação, atualização e exclusão de funcionários, como o carregamento de arquivos CSV, ou scripts personalizados para sincronizar dados de funcionários. Esses processos de provisionamento são propensos a erros, inseguros e difíceis de gerenciar.

Para gerenciar de forma direta os ciclos de vida de identidade de ponta a ponta de funcionários, fornecedores ou funcionários contingentes, o [serviço de provisionamento de usuário do Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) oferece integração com aplicativos de RH (recursos humanos) baseados em nuvem, como workday ou SuccessFactors.

O Azure AD usa essa integração para habilitar os seguintes fluxos de trabalho do aplicativo de RH de nuvem (aplicativo):

- **Provisionamento de usuários para AD** -Provision-conjuntos selecionados de usuários de um aplicativo de RH na nuvem em um ou mais domínios do Active Directory (AD).
- **Provisionamento de usuários somente de nuvem para o Azure ad** – em cenários em que o AD não é usado, Provisione usuários diretamente do aplicativo de RH na nuvem para o Azure AD.
- **Faça write-back para o aplicativo de RH na nuvem.** -Escreva os endereços de email e os atributos de nome de usuário do Azure AD de volta para o aplicativo de RH na nuvem.

> [!NOTE]
> Este plano de implantação orienta como implantar seus fluxos de trabalho de aplicativo de RH na nuvem com o provisionamento de usuário do Azure AD. Para obter informações sobre como implantar o provisionamento automático de usuário em aplicativos SaaS, consulte [planejar uma implantação de provisionamento de usuário automático](https://aka.ms/deploymentplans/provisioning).

## <a name="enabled-hr-scenarios"></a>Cenários de RH habilitados

O serviço de provisionamento de usuários do Azure AD permite a automação dos seguintes cenários de gerenciamento de ciclo de vida de identidade com base em RH:

- **Contratando novos funcionários** – quando um novo funcionário é adicionado ao aplicativo de RH na nuvem, uma conta de usuário é criada automaticamente no AD e no Azure AD com a opção de gravar o endereço de email e os atributos de nome de usuário no aplicativo de RH de nuvem.
- **Atualizações de perfil e atributo de funcionário** – quando um registro de funcionário é atualizado no aplicativo de RH de nuvem (como seu nome, título ou gerente), sua conta de usuário será atualizada automaticamente no AD e no Azure AD.
- **Encerramentos de funcionários** – quando um funcionário é encerrado no aplicativo de RH de nuvem, sua conta de usuário é desabilitada automaticamente no AD e no Azure AD.
- **Recontratação de funcionário** -quando um funcionário é recontratado no aplicativo de RH de nuvem, sua conta antiga pode ser reativada automaticamente ou reprovisionada para o AD e o Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>A quem é essa integração mais adequada?

A integração do aplicativo de RH de nuvem com o provisionamento de usuário do Azure AD é ideal para as organizações que:

- deseja uma solução predefinida baseada em nuvem para o provisionamento de usuário de RH na nuvem
- exigir o provisionamento direto de usuário do aplicativo de RH de nuvem para o AD ou o Azure AD
- exigir que os usuários sejam provisionados usando os dados obtidos do aplicativo de RH na nuvem
- exigir ingressar, mover e deixar os usuários serem sincronizados com uma ou mais florestas, domínios e UOs do AD com base apenas nas informações de alteração detectadas no aplicativo de RH de nuvem
- usar o Office 365 para email

## <a name="learn"></a>Aprenda

O provisionamento de usuário cria uma base para o controle de identidade contínuo e aprimora a qualidade dos processos de negócios que dependem de dados de identidade autoritativa.

### <a name="terms"></a>Termos

Este artigo usa os seguintes termos:

- **Sistema de origem** -o repositório de usuários do qual o Azure ad provisiona (por exemplo, um aplicativo de RH na nuvem, como workday e SuccessFactors.)
- **Sistema de destino** – o repositório de usuários aos quais o Azure ad provisiona (por exemplo, AD, Azure AD, Office365 ou outros aplicativos SaaS).
- Emissores **-movimentadores – processo de pertração** -um termo usado para novas contratações, transferências e rescisão usando um aplicativo de RH na nuvem como um sistema de registros. O processo é concluído quando o serviço provisiona com êxito os atributos necessários para o sistema de destino.

### <a name="key-benefits"></a>Principais vantagens

Esse recurso de provisionamento de ti controlado por RH oferece benefícios comerciais significativos, conforme listado abaixo:

- **Aumentar a produtividade** – agora você pode automatizar a atribuição de contas de usuário, licenças do Office365 e fornecer acesso a grupos de chaves. A automação de atribuições dá ao novo contratado acesso imediato às suas ferramentas de trabalho e aumenta a produtividade.
- **Gerenciar riscos** – você pode aumentar a segurança automatizando as alterações com base no status do funcionário ou em associações de grupo com dados fluindo do aplicativo de RH na nuvem. Automatizar as alterações garante que as identidades do usuário e o acesso a aplicativos de chave sejam atualizados automaticamente quando os usuários fizerem a transição ou a saída da organização.
- **Atender à conformidade e governança** -o Azure ad dá suporte a logs de auditoria nativos para solicitações de provisionamento de usuário executadas por aplicativos de sistemas de origem e de destino. A auditoria permite que você acompanhe quem tem acesso aos aplicativos de uma única tela.
- **Gerenciar o custo** -provisionamento automático reduz os custos, evitando ineficiências e erros humanos associados ao provisionamento manual. Ele reduz a necessidade de soluções de provisionamento de usuário desenvolvidas personalizadas criadas ao longo do tempo usando plataformas herdadas e desatualizadas.

### <a name="licensing"></a>Licenças

Para configurar o aplicativo de RH de nuvem para a integração de provisionamento de usuário do Azure AD, você precisa de uma [licença de Azure ad Premium](https://azure.microsoft.com/pricing/details/active-directory/) válida e uma licença para o aplicativo de RH de nuvem, como workday ou SuccessFactors.

Você também precisa de uma licença de assinatura Azure AD Premium P1 ou superior válida para cada usuário que será originado do aplicativo de RH na nuvem e provisionado para o AD ou o Azure AD. Qualquer número inadequado de licenças pertencentes ao aplicativo de RH na nuvem pode levar a erros durante o provisionamento do usuário.

### <a name="prerequisites"></a>Pré-requisitos

- Acesso de administrador global do Azure AD para configurar o agente de provisionamento do Azure AD Connect.
- Uma instância de teste e produção do aplicativo de RH de nuvem.
- Permissões de administrador no aplicativo de RH de nuvem para criar um usuário de integração do sistema e fazer alterações para testar dados de funcionários para fins de teste.
- Para o provisionamento de usuário no AD, um servidor executando o Windows Server 2012 ou superior com o .NET 4.7.1 + Runtime é necessário para hospedar o [agente de provisionamento do Azure ad Connect](https://go.microsoft.com/fwlink/?linkid=847801).
- [Azure ad Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect) para sincronizar usuários entre o AD e o Azure AD.

### <a name="training-resources"></a>Recursos de treinamento

| **Recursos** | **Link e descrição** |
|:-|:-|
| Vídeos | [O que é o provisionamento de usuário no diretório ativo do Azure?](https://youtu.be/_ZjARPpI6NI) |
| | [Como implantar o provisionamento de usuário no diretório ativo do Azure?](https://youtu.be/pKzyts6kfrw) |
| Tutoriais | Consulte a [lista de tutoriais sobre como integrar aplicativos SaaS com o Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) |
| | [Tutorial: configurar o WORKDAY para provisionamento automático de usuário](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#frequently-asked-questions-faq) |
| FAQ | [Provisionamento automatizado de usuários](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#frequently-asked-questions) |
| | [Provisionamento do WORKDAY para o Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Arquitetura de soluções

O exemplo a seguir descreve a arquitetura da solução de provisionamento de usuário de ponta a ponta para ambientes híbridos comuns e inclui:

- **Fluxo de dados de RH autoritativo – do aplicativo de RH de nuvem para o AD** – nesse fluxo, o evento de RH (emissores-movimentadores-periões-de-mover) é iniciado no locatário do aplicativo de RH na nuvem. O serviço de provisionamento do Azure AD e o agente de provisionamento do Azure AD Connect provisiona os dados do usuário do locatário do aplicativo de RH na nuvem para o AD. Dependendo do evento, ele pode levar a criar/atualizar/habilitar/desabilitar operações no AD.
- **Sincronizar com o Azure AD e o email de write-back e o nome de usuário do AD local para o aplicativo de RH na nuvem** -depois que as contas são atualizadas no AD, elas são sincronizadas com o Azure ad por meio do Azure ad Connect e os endereços de email e os atributos de nome de usuário podem ser gravados no locatário do aplicativo de RH na nuvem.

![Diagrama de fluxo de trabalho](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>Descrição do fluxo de trabalho

As principais etapas indicadas no diagrama são:  

1. A **equipe de RH** executa as transações no locatário do aplicativo de RH na nuvem.
2. O **serviço de provisionamento do Azure ad** executa os ciclos agendados do locatário do aplicativo de RH na nuvem e identifica as alterações que precisam ser processadas para sincronização com o AD.
3. O **serviço de provisionamento do Azure ad** invoca o agente de provisionamento do Azure ad Connect com uma carga de solicitação que contém as operações criar/atualizar/habilitar/desabilitar conta do AD.
4. **Azure ad Connect agente de provisionamento** usa uma conta de serviço para gerenciar os dados da conta do AD.
5. **Azure ad Connect** executa a [sincronização](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) Delta para efetuar pull de atualizações no AD.
6. As atualizações do **ad** são sincronizadas com o Azure AD.
7. O **serviço de provisionamento do Azure ad** write-backs o atributo de email e o nome de usuário do Azure ad para o locatário do aplicativo de RH na nuvem.

## <a name="plan-the-deployment-project"></a>Planejar o projeto de implantação

Considere suas necessidades organizacionais enquanto determina a estratégia para essa implantação em seu ambiente.

### <a name="engage-the-right-stakeholders"></a>Envolva os participantes certos

Quando os projetos de tecnologia falham, eles normalmente fazem isso devido a expectativas incompatíveis com o impacto, os resultados e as responsabilidades. Para evitar essas armadilhas, [certifique-se de que você esteja participando dos participantes certos](https://aka.ms/deploymentplans) e que as funções de Stakeholder no projeto sejam bem compreendidas ao documentar os participantes e sua entrada e responsabilidades de projeto.

Você deve incluir um representante da organização de RH que pode fornecer entradas de processos de negócios existentes e requisitos de processamento de dados de trabalho e identidades do trabalho.

### <a name="plan-communications"></a>Planejar comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Você deve se comunicar proativamente com seus usuários como sua experiência será alterada, quando ele será alterado e como obter suporte se eles tiverem problemas.

### <a name="plan-a-pilot"></a>Planejar um piloto

A integração dos processos de negócios de RH e dos fluxos de trabalho de identidade do aplicativo de RH na nuvem aos sistemas de destino requer uma quantidade considerável de validação de dados, transformação de dados, limpeza de dados e teste de ponta a ponta antes de implantar a solução na produção.

É recomendável que a configuração inicial seja executada em um [ambiente piloto](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans#best-practices-for-a-pilot) antes de dimensioná-la para todos os usuários em produção.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Selecionar aplicativos do conector de provisionamento de RH de nuvem

Para facilitar o provisionamento de fluxos de trabalho do Azure AD entre o aplicativo de RH de nuvem e o AD, há vários aplicativos de conector de provisionamento que você pode adicionar da Galeria de aplicativos do Azure AD:

- **Aplicativo de RH na nuvem para provisionamento de usuário do AD** – esse aplicativo do conector de provisionamento facilita o provisionamento de contas de usuário do aplicativo de RH na nuvem para um único domínio do AD. Se você tiver vários domínios, poderá adicionar uma instância desse aplicativo da Galeria de aplicativos do Azure AD para cada domínio do AD ao qual você precisa provisionar.
- **Aplicativo de RH na nuvem para provisionamento de usuário do Azure ad** -embora Azure ad Connect seja a ferramenta que deve ser usada para sincronizar os usuários do AD com o Azure AD, esse aplicativo de conector de provisionamento pode ser usado para facilitar o provisionamento de usuários somente de nuvem do aplicativo de RH de nuvem para um único locatário do Azure AD.
- **Write-back do aplicativo de RH na nuvem** -este aplicativo do conector de provisionamento facilita o Write-back dos endereços de email do usuário do Azure ad para o aplicativo de RH na nuvem.

Por exemplo, a imagem abaixo lista os aplicativos do conector do workday disponíveis na Galeria de aplicativos do Azure AD.

![Galeria de aplicativos do portal Azure Active Directory](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flowchart"></a>Fluxograma de decisão

Use o gráfico de fluxo de decisão abaixo para identificar quais aplicativos de provisionamento de RH de nuvem são relevantes para seu cenário.

![Fluxograma de decisão](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img3.png)

## <a name="design-azure-ad-connect-provisioning-agent-deployment-topology"></a>Criar Azure AD Connect topologia de implantação do agente de provisionamento

A integração de provisionamento entre o aplicativo de RH de nuvem e o AD requer estes quatro componentes:

- Locatário do aplicativo de RH na nuvem
- Aplicativo do conector de provisionamento
- Agente de provisionamento do Azure AD Connect
- Domínio do AD

A topologia de implantação do agente de provisionamento do Azure AD Connect depende do número de locatários do aplicativo de RH de nuvem e domínios filho do AD que você planeja integrar. Se você tiver vários domínios do AD, dependerá se os domínios do AD são contíguos [ou não](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/disjoint-namespace).

Com base em sua decisão, escolha um dos cenários de implantação:

- Locatário de aplicativo de RH de nuvem única-> direcionar um único ou vários domínios filho do AD em uma floresta confiável
- Locatário de aplicativo de RH de nuvem única-> direcionar vários domínios filho em uma floresta de AD não contíguo

### <a name="single-cloud-hr-app-tenant---single-or-multiple-target-ad-child-domains-in-a-trusted-forest"></a>Locatário de aplicativo de RH de nuvem única-> um único ou vários domínios filho do AD de destino em uma floresta confiável

Recomendamos a seguinte configuração de produção:

|Requisito|Recomendação|
|:-|:-|
|Número de agentes de provisionamento de Azure AD Connect a serem implantados|2 (para alta disponibilidade e failover)
|Número de aplicativos do conector de provisionamento a serem configurados|um aplicativo por domínio filho|
|Host do servidor para o agente de provisionamento do Azure AD Connect|Windows 2012 R2 + com linha de visão para controladores de domínio do AD localizados geograficamente</br>Pode coexistir com o serviço Azure AD Connect|

![Fluxo para agentes locais](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-ad-forest"></a>Locatário de aplicativo de RH de nuvem única-> direcionar vários domínios filho em uma floresta de AD não contíguo

Esse cenário envolve o provisionamento de usuários do aplicativo de RH na nuvem para domínios em florestas do AD não contíguos.

Recomendamos a seguinte configuração de produção:

|Requisito|Recomendação|
|:-|:-|
|Número de agentes de provisionamento de Azure AD Connect para implantar no local|2 por floresta de AD não contíguo|
|Número de aplicativos do conector de provisionamento a serem configurados|um aplicativo por domínio filho|
|Host do servidor para o agente de provisionamento do Azure AD Connect|Windows 2012 R2 + com linha de visão para controladores de domínio do AD localizados geograficamente</br>Pode coexistir com o serviço Azure AD Connect|

![Floresta do AD não contíguo do locatário de aplicativo de RH de nuvem única](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Requisitos do agente de provisionamento do Azure AD Connect

O aplicativo de RH na nuvem para a solução de provisionamento de usuário do AD requer a implantação de um ou mais agentes de provisionamento de Azure AD Connect em servidores que executam o Windows 2012 R2 ou superior com um mínimo de 4 GB de RAM e do tempo de execução do .NET 4.7.1 +. Verifique se o servidor host tem acesso à rede para o domínio do AD de destino.

Para preparar o ambiente local, o assistente de configuração do agente de provisionamento do Azure AD Connect registra o agente com seu locatário do Azure AD, [abre portas](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#open-ports), [permite o acesso a URLs](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#allow-access-to-urls)e dá suporte à [configuração de proxy HTTPS de saída](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).

O agente de provisionamento usa uma conta de serviço para se comunicar com os domínios do AD. Antes da instalação do agente, recomendamos que você crie uma conta de serviço em usuários e computadores do AD que atendam aos seguintes requisitos:

- Uma senha que não expira
- Permissões de controle delegado para ler, criar, excluir e gerenciar contas de usuário

Você pode selecionar controladores de domínio que devem lidar com solicitações de provisionamento. Se você tiver vários controladores de domínio geograficamente distribuídos, instale o agente de provisionamento no mesmo site que seu (s) controlador (es) de domínio preferido para melhorar a confiabilidade e o desempenho da solução de ponta a ponta.

Para alta disponibilidade, você pode implantar mais de um agente de provisionamento do Azure AD Connect e registrá-lo para lidar com o mesmo conjunto de domínios do AD local.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Planejar filtros de escopo e mapeamento de atributos

Quando você habilita o provisionamento do aplicativo de RH na nuvem para o AD ou o Azure AD, o portal do Azure controla os valores de atributo por meio do mapeamento de atributos.

### <a name="define-scoping-filters"></a>Definir filtros de escopo

Use [filtros de escopo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters) para definir as regras baseadas em atributo que determinam quais usuários devem ser provisionados do aplicativo de RH de nuvem para o AD ou o Azure AD.

Ao iniciar o processo de ingressos, reúna os seguintes requisitos:

- O aplicativo de RH de nuvem é usado para integrar funcionários e trabalhadores contingentes?
- Você planeja usar o aplicativo de RH de nuvem para o provisionamento de usuário do Azure AD para gerenciar funcionários e operadores contingentes?
- Você planeja distribuir o aplicativo de RH de nuvem para o provisionamento de usuário do Azure AD apenas para um subconjunto dos usuários do aplicativo de RH na nuvem (por exemplo, somente funcionários)?

Dependendo dos seus requisitos, ao configurar mapeamentos de atributo, você pode definir o campo **escopo do objeto de origem** para selecionar quais conjuntos de usuários no aplicativo de RH em nuvem devem estar no escopo para provisionamento no AD. Consulte o tutorial do aplicativo de RH na nuvem para filtros de escopo mais usados.

### <a name="determine-matching-attributes"></a>Determinar atributos correspondentes

Com o provisionamento, você obtém a capacidade de corresponder as contas existentes entre o sistema de origem e de destino. Ao integrar o aplicativo de RH na nuvem com o serviço de provisionamento do Azure AD, você pode [Configurar o mapeamento de atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal#mappings) para determinar quais dados do usuário devem fluir do aplicativo de RH na nuvem para o AD ou o Azure AD.

Ao iniciar o processo de ingressos, reúna os seguintes requisitos:

- Qual é a ID exclusiva neste aplicativo de RH de nuvem que é usada para identificar cada usuário?
- De uma perspectiva do ciclo de vida da identidade, como lidar com recontratações? Recontratações retêm sua antiga ID de funcionário?
- Você processa contratações no futuro e criou contas do AD com antecedência?
- De uma perspectiva do ciclo de vida de identidade, como você lida com a conversão de funcionário para um trabalho contingente ou de outra forma?
- Os usuários convertidos retêm sua conta antiga do AD ou obtêm novos?

Dependendo do seu requisito, o Azure AD dá suporte ao mapeamento direto de atributo para atributo, fornecendo valores constantes ou [gravando expressões para mapeamentos de atributos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-writing-expressions-for-attribute-mappings). Essa flexibilidade oferece controle definitivo do que será populado no atributo do aplicativo de destino. Você pode usar [Microsoft Graph API](https://docs.microsoft.com/azure/active-directory/manage-apps/export-import-provisioning-configuration) e o explorador do Graph para exportar os mapeamentos e o esquema de atributo de provisionamento de usuário para um arquivo JSON e importá-los de volta para o Azure AD.

**Por padrão**, o atributo no aplicativo de RH de nuvem que representa a ID de funcionário exclusiva é usado como o atributo correspondente *mapeado para o atributo exclusivo no AD.* Por exemplo, no cenário de aplicativo do workday, o atributo *workermanager* do *workday* é mapeado para o atributo *EmployeeID* do AD.

Você pode definir vários atributos correspondentes e atribuir precedência correspondente. Elas são avaliadas na precedência correspondente. Assim que uma correspondência for encontrada, nenhum atributo correspondente será avaliado.

Você também pode [Personalizar os mapeamentos de atributo padrão](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes#understanding-attribute-mapping-types) , como alterar ou excluir mapeamentos de atributo existentes, ou criar novos mapeamentos de atributo de acordo com suas necessidades de negócios. Consulte o tutorial do aplicativo de RH na nuvem (como [workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)) para obter uma lista de atributos personalizados a serem mapeados.

### <a name="determine-user-account-status"></a>Determinar o status da conta de usuário

Por padrão, o aplicativo do conector de provisionamento mapeia o **status do perfil de usuário de RH** para o **status da conta de usuário** no AD/Azure ad para determinar se a conta de usuário deve ser habilitada ou desabilitada.

Ao iniciar o processo de junções/periões, reúna os seguintes requisitos:

| Processo | Requisitos |
| - | - |
| **Entradas** | De uma perspectiva do ciclo de vida da identidade, como lidar com recontratações? Recontratações retêm sua antiga ID de funcionário? |
| | Você processa contratações no futuro e cria contas do AD com antecedência? Essas contas são criadas no estado habilitado/desabilitado? |
| | De uma perspectiva do ciclo de vida de identidade, como você lida com a conversão de funcionário para um trabalho contingente ou de outra forma? |
| | Os usuários convertidos retêm sua conta antiga do AD ou obtêm novos? |
| **Partidas** | Os encerramentos são tratados de forma diferente para funcionários e funcionários concontingentes no AD? |
| | Quais datas efetivas são consideradas para processar o encerramento do usuário? |
| | Como as conversões de funcionário e de operador contingent afetam as contas existentes do AD? |
| | Como processar a operação "rescindir" no AD? As operações de rescindir precisam ser manipuladas se contratações com data futuras são criadas no AD como parte do processo do associador. |

Dependendo dos seus requisitos, você pode personalizar a lógica de mapeamento usando [expressões do Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data) para que a conta do AD seja habilitada ou desabilitada com base em uma combinação de pontos de dados.

### <a name="map-cloud-hr-app-to-ad-user-attributes"></a>Mapear aplicativo de RH de nuvem para atributos de usuário do AD

Cada aplicativo de RH na nuvem é fornecido com o aplicativo de RH de nuvem padrão para mapeamentos do AD.

Quando você inicia o processo de junções/movimentadores/pertração, reúna os seguintes requisitos:

| Processo | Requisitos |
| - | - |
| **Entradas** | O processo de criação de conta do AD é manual, automatizado ou parcialmente automatizado? |
| | Você planeja propagar atributos personalizados do aplicativo de RH de nuvem para o AD? |
| **Chuvas** | Quais atributos você gostaria de processar sempre que uma operação de "movimentadores" ocorrer no aplicativo de RH de nuvem? |
| | Você realiza qualquer validação de atributo específica no momento das atualizações do usuário? Em caso afirmativo, forneça detalhes. |
| **Partidas** | Os encerramentos são tratados de forma diferente para funcionários e funcionários concontingentes no AD? |
| | Quais datas efetivas são consideradas para processar o encerramento do usuário? |
| | Como as conversões de funcionário e de operador contingent afetam as contas existentes do AD? |

Dependendo dos seus requisitos, você pode modificar os mapeamentos para atender às metas de integração. Consulte o tutorial específico do aplicativo de RH na nuvem (como [workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)) para obter uma lista de atributos personalizados a serem mapeados.

### <a name="generate-unique-attribute-value"></a>Gerar valor de atributo exclusivo

Ao iniciar o processo dos ASSOCIADORES, talvez seja necessário gerar valores de atributos exclusivos ao definir atributos como CN, samAccountName e UPN que tenham restrições exclusivas.

A função [SelectUniqueValues](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#selectuniquevalue) do Azure ad avalia cada regra e, em seguida, verifica o valor gerado quanto à exclusividade no sistema de destino. Consulte o exemplo [gerar valor exclusivo para o atributo userPrincipalName (UPN)](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#generate-unique-value-for-userprincipalname-upn-attribute).

> [!NOTE]
> Atualmente, essa função só tem suporte para "WORKDAY para Active Directory provisionamento de usuário". Ele não pode ser usado com outros aplicativos de provisionamento.

### <a name="configure-ad-ou-container-assignment"></a>Configurar atribuição de contêiner da UO do AD

É um requisito comum para a colocação de contas de usuário do AD em contêineres com base em unidades de negócios, locais e departamentos. Quando você inicia um processo de movimentação e, se houver uma alteração de organização de supervisão, talvez seja necessário mover o usuário de uma UO para outra no AD.

Use a função [switch ()](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#switch) para configurar a lógica de negócios para a atribuição de UO e mapeá-la para o atributo do AD *parentDistinguishedName*.

Por exemplo, se você quiser criar usuários no UO com base no atributo de RH "municipal", poderá usar a expressão a seguir.

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

Com essa expressão, se o valor de município for Dallas, Austin, Seattle ou Londres, a conta de usuário será criada na UO correspondente. Se não houver nenhuma correspondência, a conta será criada na UO padrão.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Planejar a entrega de senha de novas contas de usuário

Ao iniciar o processo dos ASSOCIADORES, você precisará definir e entregar uma senha temporária de novas contas de usuário. Com a nuvem de RH para o provisionamento de usuários do Azure AD, você pode distribuir o recurso de [redefinição de senha de autoatendimento](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) (SSPR) do Azure ad para o usuário no dia 1.

O SSPR é um meio simples para os administradores de ti permitirem que os usuários redefinam suas senhas ou desbloqueiem suas contas. Você pode provisionar o atributo de **número móvel** do aplicativo de RH de nuvem para o AD e sincronizá-lo com o Azure AD. Depois que o atributo de **número móvel** estiver no Azure AD, você poderá habilitar o SSPR para a conta do usuário para que no dia 1, os novos usuários possam usar o número de celular registrado e verificado para autenticação.

## <a name="plan-for-initial-cycle"></a>Planejar o ciclo inicial

Quando o serviço de provisionamento do Azure AD é executado pela primeira vez, ele executa um [ciclo inicial](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#what-happens-during-provisioning) em relação ao aplicativo de RH na nuvem para criar um instantâneo de todos os objetos de usuário no aplicativo de RH na nuvem. O tempo necessário para os ciclos iniciais é diretamente dependente de quantos usuários estão presentes no sistema de origem. O ciclo inicial de alguns locatários do aplicativo de RH na nuvem com mais de 100.000 usuários pode levar muito tempo.

**Para locatários de aplicativo de RH de nuvem grande (> usuários 30.000), recomendamos** que você execute o ciclo inicial em estágios progressivos e inicie as atualizações incrementais somente após a validação de que os atributos corretos estão definidos no AD para diferentes cenários de provisionamento de usuário. Siga a ordem abaixo:

1. Execute o ciclo inicial somente para um conjunto limitado de usuários definindo o [filtro de escopo](#plan-scoping-filters-and-attribute-mapping).
2. Verifique o provisionamento de conta do AD e os valores de atributo definidos para os usuários selecionados para a primeira execução. Se o resultado atender às suas expectativas, expanda o filtro de escopo para incluir progressivamente mais usuários e verificar os resultados da segunda execução.

Quando estiver satisfeito com os resultados do ciclo inicial dos usuários de teste, você poderá iniciar as [atualizações incrementais](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#incremental-cycles).

## <a name="plan-testing-and-security"></a>Teste e segurança de planos

Em cada estágio de sua implantação do piloto inicial por meio da habilitação do provisionamento de usuário, verifique se você está testando se os resultados estão conforme o esperado e auditando os ciclos de provisionamento.

### <a name="plan-testing"></a>Teste de plano

Depois de configurar o aplicativo de RH na nuvem para o provisionamento de usuário do Azure AD, você precisará executar casos de teste para verificar se essa solução atende aos requisitos da sua organização.

|Cenários|Resultados esperados|
|:-|:-|
|Novo contratação de funcionário no aplicativo de RH em nuvem| -A conta de usuário é provisionada no AD.</br>-Os usuários podem fazer logon no AD-Domain apps e executar as ações desejadas.</br>-Se a sincronização do AAD Connect estiver configurada, a conta de usuário também será criada no Azure AD.
|O usuário foi encerrado no aplicativo de RH de nuvem|-A conta de usuário está desabilitada no AD.</br>-O usuário não pode fazer logon em nenhum aplicativo empresarial protegido pelo AD.
|A organização do supervisão do usuário é atualizada no aplicativo de RH na nuvem|Com base no mapeamento de atributo, a conta de usuário passa de uma UO para outra no AD.|
|Gerente do usuário de atualizações de RH no aplicativo de RH na nuvem|O campo gerente no AD é atualizado para refletir o nome do novo gerente.|
|O HR recontrata um funcionário em uma nova função.|O comportamento depende de como o aplicativo de RH de nuvem está configurado para gerar IDs de funcionário:</br>-Se a ID antiga do funcionário for reutilizada para recontratações, o conector habilitará a conta do AD existente para o usuário.</br>-Se recontratações obter uma nova ID de funcionário, o conector criará uma nova conta do AD para o usuário.|
|O HR converte o funcionário para o funcionário de contrato ou vice-versa|Uma nova conta do AD é criada para a nova pessoa e a conta antiga é desabilitada na data de efetivação da conversão.|

Use os resultados acima para determinar como fazer a transição de sua implementação de provisionamento automático de usuário para produção com base em suas linhas do tempo estabelecidas.

> [!TIP]
> É recomendável usar técnicas como a redução de dados e a depuração de dados ao atualizar o ambiente de teste com dados de produção para remover/mascarar dados confidenciais de PII (informações de identificação pessoal) para atender aos padrões de privacidade e segurança.

### <a name="plan-security"></a>Segurança do plano

É comum que uma revisão de segurança seja exigida como parte da implantação de um novo serviço. Se uma análise de segurança for necessária ou ainda não tiver sido realizada, examine os muitos [White papers](https://www.microsoft.com/download/details.aspx?id=36391) do Azure AD que fornecem uma visão geral da identidade como um serviço.

### <a name="plan-rollback"></a>Reversão do plano

Se a implementação de provisionamento de usuário de RH da nuvem não funcionar conforme o desejado no ambiente de produção, as etapas de reversão a seguir podem ajudá-lo a reverter para um estado válido anterior conhecido:

1. Examine o [relatório de Resumo de provisionamento](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting#getting-provisioning-reports-from-the-azure-management-portal) e [os logs de provisionamento](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting#provisioning-audit-logs) (consulte [gerenciar provisionamento de usuário do aplicativo de RH de nuvem](#manage-your-configuration)) para determinar quais são as operações incorretas executadas nos usuários e/ou grupos afetados.
2. O último estado bom conhecido dos usuários e/ou grupos afetados pode ser determinado por meio dos logs de auditoria de provisionamento ou revisando os sistemas de destino (Azure AD ou AD).
3. Trabalhe com o proprietário do aplicativo para atualizar os usuários e/ou grupos afetados diretamente no aplicativo usando os últimos valores de estado válidos conhecidos.

## <a name="deploy-the-cloud-hr-app"></a>Implantar o aplicativo de RH na nuvem

Escolha o aplicativo de RH na nuvem que se alinha aos seus requisitos de solução.

**Workday** -consulte [tutorial: configurar o WORKDAY para o provisionamento automático de usuário](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-your-deployment) para as etapas que você precisa executar para importar perfis de trabalho do WORKDAY para o AD e o Azure AD, com write-back opcional do endereço de email e nome de usuário para workday.

## <a name="manage-your-configuration"></a>Gerenciar sua configuração

O Azure AD pode fornecer informações adicionais sobre o uso de provisionamento de usuário da sua organização e a integridade operacional por meio de relatórios e logs de auditoria.

### <a name="gain-insights-from-reports-and-logs"></a>Obter informações de relatórios e logs

Após um [ciclo inicial](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#what-happens-during-provisioning)bem-sucedido, o serviço de provisionamento do Azure ad continuará a executar atualizações incrementais de back-to-back indefinidamente, em intervalos definidos nos tutoriais específicos de cada aplicativo, até que um dos seguintes eventos ocorra:

- O serviço é interrompido manualmente e um novo ciclo inicial é disparado usando o [portal do Azure](https://portal.azure.com/) ou usando o comando da [API Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) apropriado.
- Um novo ciclo inicial é disparado devido a uma alteração nos mapeamentos de atributo ou filtros de escopo.
- O processo de provisionamento entra em quarentena devido a uma alta taxa de erros e permanece em quarentena por mais de quatro semanas em que ela será automaticamente desabilitada.

Para examinar esses eventos e todas as outras atividades executadas pelo serviço de provisionamento, [saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/check-status-user-account-provisioning).

#### <a name="azure-monitor-logs"></a>Registos do Azure Monitor

Todas as atividades executadas pelo serviço de provisionamento são registradas nos **logs de auditoria do Azure ad**. Você pode rotear os logs de auditoria do Azure AD para Azure Monitor logs para análise posterior. **Os logs de Azure monitor (também conhecidos como log Analytics espaço de trabalho)** permitem consultar dados para localizar eventos, analisar tendências e executar a correlação entre várias fontes de dados. Assista a este [vídeo](https://youtu.be/MP5IaCTwkQg) para aprender os benefícios de usar os logs de Azure monitor para logs do Azure AD em cenários de usuário práticos.

Você pode instalar os [modos de exibição do log Analytics para logs de atividades do Azure ad](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views) para obter acesso a [relatórios pré-criados](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) sobre o provisionamento de eventos em seu ambiente.

Para obter mais informações, consulte [como analisar os logs de atividade do Azure AD com seus logs de Azure monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

### <a name="manage-personal-data"></a>Gerir dados pessoais

O agente de provisionamento do Azure AD Connect instalado no Windows Server cria logs no log de eventos do Windows que podem conter dados pessoais, dependendo do seu aplicativo de RH na nuvem para mapeamentos de atributo do AD. Para estar em conformidade com as obrigações de privacidade do usuário, você pode configurar uma tarefa agendada do Windows para limpar o log de eventos e garantir que nenhum dado seja mantido além de 48 horas.

O serviço de provisionamento do Azure AD não gera relatórios, executa análises ou fornece informações além de 30 dias. Portanto, o serviço de provisionamento do Azure AD não armazena, processa nem retém dados além de 30 dias. 

### <a name="troubleshoot"></a>Resolução de problemas

Consulte os links a seguir para solucionar quaisquer problemas que possam ser reativados durante o provisionamento:

- [Problema ao configurar o provisionamento de usuário para um aplicativo da galeria do Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)
- [Sincronizar um atributo do seu Active Directory local com o Azure AD para provisionamento para um aplicativo](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning-sync-attributes-for-mapping)
- [O provisionamento de usuário para um aplicativo da galeria do Azure AD está demorando horas ou mais](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish)
- [Problema ao salvar as credenciais de administrador ao configurar o provisionamento de usuário para um aplicativo da Galeria de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-storage-limit)
- [Nenhum usuário está sendo provisionado para um aplicativo da galeria do Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned)
- [Um conjunto incorreto de usuários está sendo provisionado para um aplicativo da galeria do Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-wrong-users-provisioned)
- [Configurando o Windows Visualizador de Eventos para solução de problemas do agente](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Configurando portal do Azure logs de auditoria para solução de problemas de serviço](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [Noções básicas sobre logs para operações de criação de conta de usuário do AD](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#understanding-logs-for-ad-user-account-create-operations)
- [Noções básicas sobre logs para operações de atualização do Gerenciador](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#understanding-logs-for-manager-update-operations)
- [Resolvendo erros frequentemente encontrados](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Passos seguintes

- [Gravando expressões para mapeamentos de atributo](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)
- [Visão geral da API de sincronização do Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
- [Ignorar a exclusão de contas de usuário que saem do escopo](https://docs.microsoft.com/azure/active-directory/manage-apps/skip-out-of-scope-deletions)
- [Agente de provisionamento do Azure AD Connect: histórico de lançamento de versão](https://docs.microsoft.com/azure/active-directory/manage-apps/provisioning-agent-release-version-history)
