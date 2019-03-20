---
title: Contoso-configurar uma infraestrutura de migração | Documentos da Microsoft
description: Saiba como Contoso configura uma infraestrutura do Azure para a migração para o Azure.
services: azure-migrate
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/1/2018
ms.author: raynew
ms.openlocfilehash: 5dfe768ddb3509f896b90f913ffecdf33907357a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57876685"
---
# <a name="contoso---deploy-a-migration-infrastructure"></a>Contoso - implementar uma infraestrutura de migração

Neste artigo, a Contoso prepara a sua infraestrutura no local para migração e configura uma infraestrutura do Azure, em preparação para a migração e para a execução da empresa num ambiente híbrido.

- É uma arquitetura de exemplo que é específica para a Contoso.
- Se precisa de todos os elementos descritos neste artigo depende da sua estratégia de migração. Por exemplo, se estiver criando apenas aplicações nativas da cloud no Azure, poderá ter uma estrutura de rede menos complexa.

Este artigo faz parte de uma série de artigos que documente como a empresa fictícia Contoso migra os respetivos recursos no local para a cloud do Microsoft Azure. A série contém informações gerais e uma série de cenários de implementação que mostram como configurar uma infraestrutura de migração, avaliar a adequação de recursos no local para migração e executar diferentes tipos de migrações. Cenários de crescem em complexidade. Artigos serão adicionados à série ao longo do tempo.


**Artigo** | **Detalhes** | **Estado**
--- | --- | ---
[Artigo 1: Descrição geral](contoso-migration-overview.md) | Descrição geral da série de artigos, estratégia de migração da Contoso e as aplicações de exemplo que são utilizadas da série. | Disponível
Artigo 2: Implementar uma infraestrutura do Azure | Contoso prepara a sua infraestrutura no local e a respetiva infraestrutura do Azure para a migração. A mesma infra-estrutura é utilizada para todos os artigos de migração da série. | Este artigo
[Artigo 3: Avalie os recursos no local para migração para o Azure](contoso-migration-assessment.md)  | SContoso executa uma avaliação da sua aplicação da SmartHotel360 no local em execução no VMware. Contoso avalia as VMs de aplicação com o serviço Azure Migrate e a base de dados de SQL Server do aplicativo com o Assistente de migração de dados. | Disponível
[Artigo 4: Realojar a uma aplicação numa VM do Azure e a instância gerida da base de dados do SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso é executada uma migração lift-and-shift para o Azure para a sua aplicação da SmartHotel360 no local. Contoso migra a aplicação front-end VM com [do Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migra a base de dados de aplicação para uma instância gerida da base de dados SQL do Azure utilizando o [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Disponível   
[5 do artigo: Realojar a uma aplicação em VMs do Azure](contoso-migration-rehost-vm.md) | Contoso migra a sua aplicação da SmartHotel360 VMs para VMs do Azure com o serviço Site Recovery. | Disponível
[Artigo 6: Realojar a uma aplicação em VMs do Azure e, num grupo de Disponibilidade AlwaysOn do SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migra a aplicação, com o Site Recovery para migrar as VMs de aplicação e o serviço de migração de base de dados para migrar a base de dados de aplicação para um cluster do SQL Server que está protegido por um grupo de Disponibilidade AlwaysOn. | Disponível
[Artigo 7: Realojar uma aplicação do Linux em VMs do Azure](contoso-migration-rehost-linux-vm.md) | Contoso é concluída uma migração lift-and-shift de seu aplicativo de osTicket do Linux para VMs do Azure, com o serviço Site Recovery. | Disponível
[Artigo 8: Realojar uma aplicação do Linux em VMs do Azure e base de dados do Azure para MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migra sua osTicket de aplicação do Linux para VMs do Azure com o Site Recovery. Ele migra a base de dados de aplicação à base de dados do Azure para MySQL com o MySQL Workbench. | Disponível
[Artigo 9: Refatorizar uma aplicação numa aplicação web do Azure e base de dados do Azure SQL](contoso-migration-refactor-web-app-sql.md) | Contoso migra a sua aplicação da SmartHotel360 para uma aplicação web do Azure e migra a base de dados de aplicação para uma instância de servidor SQL do Azure com o Assistente de migração de base de dados. | Disponível    
[Artigo 10: Refatorizar uma aplicação do Linux numa aplicação web do Azure e base de dados do Azure para MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migra sua osTicket de aplicação do Linux para uma aplicação web do Azure em vários sites. A aplicação web está integrada com o GitHub para a entrega contínua. Ele migra a base de dados de aplicação para uma base de dados do Azure para a instância do MySQL. | Disponível
[Artigo 11: Refatorar o Team Foundation Server nos serviços de DevOps do Azure](contoso-migration-tfs-vsts.md) | Contoso migra sua implantação do Team Foundation Server no local para serviços do Azure DevOps no Azure. | Disponível
[Artigo 12: Rearquitetar uma aplicação em contentores do Azure e base de dados do Azure SQL](contoso-migration-rearchitect-container-sql.md) | Contoso migra sua SmartHotel de aplicação para o Azure. Em seguida, rearchitects a camada de web de aplicação como um contentor do Windows em execução no Azure Service Fabric e a base de dados de aplicação com a base de dados do Azure SQL. | Disponível    
[Artigo 13: Recriar uma aplicação no Azure](contoso-migration-rebuild.md) | Contoso recria o seu aplicativo de SmartHotel com uma variedade de capacidades do Azure e serviços, incluindo o serviço de aplicações do Azure, Azure Kubernetes Service (AKS), as funções do Azure, serviços cognitivos do Azure e Azure Cosmos DB. | Disponível
[Artigo 14: Dimensionar uma migração para o Azure](contoso-migration-scale.md) | Depois de a experimentar combinações de migração, o Contoso se prepara para dimensionar para uma migração completa para o Azure. | Disponível 

Neste artigo que Contoso configura todos os elementos de infra-estrutura, ele precisa concluir todos os cenários de migração. 


## <a name="overview"></a>Descrição geral

Antes de Contoso pode migrar para o Azure, é fundamental para preparar uma infraestrutura do Azure.  Em geral, existem cinco áreas principais, a Contoso precisa pensar sobre:

**Passo 1: As subscrições do Azure**: Como irá Contoso comprar o Azure e interagir com a plataforma do Azure e serviços?  
**Passo 2: Identidade híbrida**: Como irá gerir e controlar o acesso a aplicações no local e de recursos do Azure após a migração? Como Contoso estender ou mover a gestão de identidade para a cloud?  
**Passo 3: Recuperação após desastre e resiliência**: Como Contoso garantirá que as suas aplicações e infraestrutura são resilientes se ocorrerem interrupções e desastres?  
**Passo 4: Funcionamento em rede**: Como deve Contoso conceber uma infraestrutura de rede e estabelecer conectividade entre o datacenter no local e o Azure?  
**Passo 5: Segurança**: Como será sua segura a implementação híbrida/Azure?  
**Passo 6: Governação**: Como é que a Contoso vai manter a implementação alinhada com os requisitos de segurança e governação?

## <a name="before-you-start"></a>Antes de começar

Antes de começarmos a olhar para a infraestrutura, pode querer ler algumas informações gerais sobre as capacidades do Azure que discutimos neste artigo:

- Há várias opções disponíveis para compra acesso do Azure, incluindo o pay as you go, contratos de Enterprise (EA), licenciamento Open revendedores da Microsoft ou a partir de Microsoft Partners conhecido como fornecedores de soluções Cloud (CSPs). Saiba mais sobre [opções de compra](https://azure.microsoft.com/pricing/purchase-options/)e leia sobre como [subscrições do Azure são organizadas](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/).
- Obtenha uma visão geral do Azure [gestão de identidades e acesso](https://www.microsoft.com/trustcenter/security/identity). Em particular, saiba mais sobre [do Azure AD e estendendo o AD no local para a cloud](https://docs.microsoft.com/azure/active-directory/identity-fundamentals). Há um útil que pode ser baixado e-book de sobre [gestão de identidades e acesso (IAM) num ambiente híbrido](https://azure.microsoft.com/resources/hybrid-cloud-identity/).
- O Azure fornece uma infraestrutura de rede robusta com opções para conectividade híbrida. Obtenha uma visão geral dos [controlo de acesso de rede e de rede](https://docs.microsoft.com/azure/security/security-network-overview).
- Obtenha uma introdução aos [segurança do Azure](https://docs.microsoft.com/azure/security/azure-security)e leia sobre a criação de um plano para [governação](https://docs.microsoft.com/azure/security/governance-in-azure).


## <a name="on-premises-architecture"></a>Arquitetura de no local

Aqui está um diagrama que mostra a infra-estrutura de locais de Contoso atual.

 ![Arquitetura de contoso](./media/contoso-migration-infrastructure/contoso-architecture.png)  

- A Contoso tiver um Data Center principal localizado na cidade de Nova Iorque no Leste dos Estados Unidos.
- Existem três ramos locais adicionais nos Estados Unidos.
- O datacenter principal está ligado à internet com uma ligação de ethernet metro fiber (500 mbps).
- Cada ramo está ligado localmente para a internet através de ligações de classe de negócio, com túneis de IPSec VPN para o datacenter principal. Isso permite que toda a rede estar conectada permanentemente e otimiza a conectividade à internet.
- O Centro de dados principal é totalmente virtualizado com o VMware. A Contoso tiver dois anfitriões de Virtualização do ESXi 6.5, geridas pelo vCenter Server 6.5.
- A Contoso utiliza o Active Directory para gestão de identidades e servidores DNS na rede interna.
- Executam os controladores de domínio no Centro de dados em VMs de VMware. Executam os controladores de domínio em ramos locais em servidores físicos.


## <a name="step-1-buy-and-subscribe-to-azure"></a>Passo 1: Comprar e subscreva o Azure

A Contoso precisa de saber como comprar o Azure, como arquitetar subscrições e como licenciar os serviços e recursos.

### <a name="buy-azure"></a>Comprar o Azure

Contoso vai com um [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). Isso exige um compromisso monetário adiantado para o Azure, Contoso entitling para recebem benefícios fantásticos, incluindo opções de faturação flexíveis e com a otimização de preços.

- Contoso estimado o que será sua anual gastos do Azure. Quando ele assinado o contrato, Contoso pagos no primeiro ano por completo.
- A Contoso precisa da utilizar todos os compromissos antes do ano é sobre ou perder o valor para essas dólares.
- Se por algum motivo Contoso excede o seu compromisso e gasta muito mais, Microsoft irá criar uma fatura-los para a diferença.
- Qualquer custo incorrido acima da alocação serão as mesmas tarifas e aqueles no contrato de Contoso. Não existem sem passar pela utilização.

### <a name="manage-subscriptions"></a>Gerir subscrições

Depois de pagar para o Azure, a Contoso precisa de saber como gerir as subscrições do Azure. A Contoso tiver um EA, e, portanto, sem limite no número de subscrições do Azure pode configurar.

- Uma inscrição de Enterprise do Azure define como uma empresa molda e utiliza os serviços do Azure e define uma estrutura de governação de núcleo.
- Como primeiro passo, a Contoso determinou uma estrutura (conhecida como uma estrutura de enterprise para a inscrição empresarial. Contoso utilizado [este artigo](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-governance) para ajudar a compreender e uma estrutura de design.
- Por enquanto, Contoso decidiu usar uma abordagem funcional para gerir as subscrições.
  - Dentro da empresa irá utilizar um único departamento de TI, que controla o orçamento do Azure. Este será o grupo de apenas com as assinaturas.
  - Contoso irá expandir no futuro, este modelo para que outros grupos empresariais podem associar uma vez que os departamentos na inscrição empresarial.
  - Dentro do departamento de TI Contoso tem estruturada duas subscrições, desenvolvimento e produção.
  - Se a Contoso requer subscrições adicionais no futuro, ele precisa de gerir o acesso, políticas e conformidade para nessas subscrições. Contoso fará isso por meio da introdução [grupos de gestão do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management-groups-overview), como uma camada adicional acima de subscrições.

    ![Estrutura de Enterprise](./media/contoso-migration-infrastructure/enterprise-structure.png) 

### <a name="examine-licensing"></a>Examine o licenciamento

Com as assinaturas configuradas, a Contoso pode procurar nos licenciamento da Microsoft. A estratégia de licenciamento dependerá os recursos que a Contoso pretende migrar para o Azure e como as VMs do Azure e serviços são selecionados e implementados. 

#### <a name="azure-hybrid-benefit"></a>Benefício Híbrido do Azure

Quando implementar VMs no Azure, imagens padrão incluem uma licença que vai ser debitada Contoso por minuto para o software a ser utilizado. No entanto, a Contoso tem sido um cliente da Microsoft de longo prazo e é mantida EAs e as licenças do open License com Software Assurance (SA). 

Benefício híbrido do Azure fornece um método rentável para a migração da Contoso, permitindo que ela guardar em VMs do Azure e o SQL Server de cargas de trabalho ao converter ou reutilizar o Windows Server Datacenter e licenças da edição Standard abrangidas por Software Assurance. Isso permitirá Contoso pagar uma taxa de computação com base mais baixa para VMs e o SQL Server. [Saiba mais](https://azure.microsoft.com/pricing/hybrid-benefit/).


#### <a name="license-mobility"></a>Mobilidade de licenças

Mobilidade de licenças através do SA proporciona aos clientes de licenciamento em Volume da Microsoft, como Contoso a flexibilidade para implementar aplicações de servidor elegíveis com SA ativo no Azure. Isso elimina a necessidade de comprar licenças novo. Com sem tarifas de mobilidade, o licenças existentes podem ser facilmente implementadas no Azure. [Saiba mais](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="reserve-instances-for-predictable-workloads"></a>Instâncias de reserva para cargas de trabalho previsíveis

Cargas de trabalho previsíveis são aqueles que sempre precisam estar disponíveis com as VMs em execução. Por exemplo, line-of-business apps, como um sistema de SAP ERP.  Por outro lado, as cargas de trabalho imprevisíveis são aqueles que são variáveis. Por exemplo, VMs que estão em durante a alta demandam e desativado em momentos de pico.

![Instância reservada](./media/contoso-migration-infrastructure/reserved-instance.png) 

Em troca utilizando as instâncias reservadas para VM específica instâncias deve ser mantido para grandes durações de tempo, Console pode obter um desconto e capacidade prioritária. Usando [instâncias reservadas do Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/), juntamente com o benefício híbrido do Azure, poderá de Contoso poupar até 82% de desconto pay as you go normal preços (Abril de 2018).


## <a name="step-2-manage-hybrid-identity"></a>Passo 2: Gerir a identidade híbrida

Dando e controlar o acesso de utilizador aos recursos do Azure com a gestão de identidades e acessos (IAM) é uma etapa importante na unindo-se de uma infraestrutura do Azure.  

- Contoso decide expandir o Active Directory no local para a cloud, em vez de criar um novo sistema separado no Azure.
- Ele cria um baseada no Azure Active Directory para fazer isso.
- Contoso não tem o Office 365 no local, pelo que tem de aprovisionar um novo do Azure AD.
- Office 365 utiliza o Azure AD para gestão de utilizadores. Se a Contoso estava a utilizar o Office 365, já teria um inquilino do Azure AD e pode utilizá-lo como o AD primário.
- [Saiba mais](https://support.office.com/article/understanding-office-365-identity-and-azure-active-directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9) sobre o Azure AD para o Office 365 e Aprenda [como adicionar uma subscrição](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) para um inquilino do Azure AD existente.

### <a name="create-an-azure-ad"></a>Criar um Azure AD

Contoso é usar a edição gratuita do Azure AD que está incluído com uma subscrição do Azure. Administradores de contoso, configure um diretório do AD da seguinte forma:

1. Na [portal do Azure](https://portal.azure.com/), que navegam até **criar um recurso** > **identidade** > **Azure Active Directory**.
2. Na **criar diretório**, eles especificam um nome para o diretório, um nome de domínio inicial e a região na qual deve ser criado o diretório do Azure AD.

    ![Criar o Azure AD](./media/contoso-migration-infrastructure/azure-ad-create.png) 

    > [!NOTE]
    > O diretório que é criado tem um nome de domínio inicial no formulário **domainname.onmicrosoft.com**. O nome não pode ser alterado ou eliminado. Em vez disso, precisa de adicionar o seu nome de domínio registado para o Azure AD.

### <a name="add-the-domain-name"></a>Adicione o nome de domínio

Para utilizar o respetivo nome de domínio padrão, os administradores da Contoso tem de adicioná-lo como um nome de domínio personalizado para o Azure AD. Esta opção permite-lhes atribuir nomes de usuário familiar. Por exemplo, um utilizador pode iniciar sessão com o endereço de e-mail billg@contoso.com, em vez de precisar billg@contosomigration.microsoft.com. 

Para configurar um nome de domínio personalizado que eles adicioná-lo para o diretório, adicione uma entrada DNS em, em seguida, verifique se o nome no Azure AD.

1. Na **nomes de domínio personalizado** > **Adicionar domínio personalizado**, eles adicionam o domínio.
2. Para utilizar uma entrada DNS no Azure que precisam para registá-lo com a sua entidade de registo do domínio. 

    - Na **nomes de domínio personalizado** lista, tenha em atenção as informações de DNS para o nome. Ele está usando uma entrada de MX.
    - Eles precisam de acesso para o nome do servidor para fazer isso. Inicie sessão no domínio Contoso.com e criar um novo registo MX para a entrada DNS fornecida pelo Azure AD, com os detalhes indicados.  
1. Depois de propagarem os registos DNS, o nome de detalhes do domínio, pode clicar em **Verifique se** para verificar o nome de domínio personalizado.

     ![Azure AD DNS](./media/contoso-migration-infrastructure/azure-ad-dns.png) 

### <a name="set-up-on-premises-and-azure-groups-and-users"></a>Configurar utilizadores e grupos do Azure e no local

Agora que o Azure AD está em execução, os administradores precisam de adicionar os funcionários do Contoso no local grupos do AD que será sincronizado com o Azure AD. Devem utilizar nomes de grupos locais que correspondem aos nomes dos grupos de recursos no Azure. Isto torna mais fácil de identificar as correspondências para fins de sincronização.

#### <a name="create-resource-groups-in-azure"></a>Criar grupos de recursos no Azure

Grupos de recursos do Azure reunir recursos do Azure. Com um ID de grupo de recursos permite que o Azure para realizar operações nos recursos dentro do grupo.

- Uma subscrição do Azure pode ter vários grupos de recursos, mas um grupo de recursos só pode existir dentro de uma única subscrição.
- Além disso, um grupo de recursos pode ter vários recursos, mas um recurso só pode pertencer a um grupo de recursos.

Administradores da Contoso, configure os grupos de recursos do Azure, conforme resumido na tabela seguinte.

**Grupo de recursos** | **Detalhes**
--- | ---
**ContosoCobRG** | Este grupo contém todos os recursos relacionados a continuidade de negócio (COB).  Ele inclui cofres que Contoso irá utilizar para o serviço Azure Site Recovery e o serviço de cópia de segurança do Azure.<br/><br/> Ele também irão incluir recursos utilizados para a migração, incluindo o Azure Migrate e os serviços de migração de base de dados.
**ContosoDevRG** | Este grupo contém recursos de desenvolvimento e teste.
**ContosoFailoverRG** | Este grupo serve como uma zona de destino para efetuar a ativação pós-falha de recursos.
**ContosoNetworkingRG** | Este grupo contém todos os recursos de rede.
**ContosoRG** | Este grupo contém recursos relacionados com aplicações de produção e de bases de dados.

Eles criaram grupos de recursos da seguinte forma:

1. No portal do Azure > **grupos de recursos**, adicionam um grupo.
2. Para cada grupo, eles especificam um nome, a subscrição a que pertence ao grupo e a região.
3. Grupos de recursos aparecem na **grupos de recursos** lista.

    ![Grupos de recursos](./media/contoso-migration-infrastructure/resource-groups.png) 

##### <a name="scaling-resource-groups"></a>Grupos de recursos de dimensionamento

No futuro, a Contoso vai adicionar outros grupos de recursos com base nas necessidades. Por exemplo, pode definir um grupo de recursos para cada aplicação ou serviço, para que eles podem ser geridos e proteger de forma independente.

#### <a name="create-matching-security-groups-on-premises"></a>Criar correspondente segurança grupos no local

1. No diretório do Active Directory no local, os administradores de Contoso definir os grupos de segurança com nomes que correspondem aos nomes dos grupos de recursos do Azure.
 
    ![Grupos de segurança do AD no local](./media/contoso-migration-infrastructure/on-prem-ad.png) 

2. Para fins de gestão, criaram um grupo adicional que será adicionado a todos os outros grupos. Este grupo terá direitos para todos os grupos de recursos no Azure. Um número limitado de administradores globais será adicionado a este grupo.

### <a name="synchronize-ad"></a>Sincronizar o AD

Contoso, além de fornecer uma identidade comum para aceder a recursos no local e na cloud. Para tal, integrará o Active Directory no local com o Azure AD. Com esse modelo:

- Os utilizadores e organizações podem tirar partido de uma única identidade para aceder a aplicações no local e serviços cloud, como o Office 365 ou milhares de outros sites na internet.
- Os administradores podem tirar partido os grupos no AD para implementar [controlo de acesso baseado em ' (RBAC) da função](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) no Azure.

Para facilitar a integração, a Contoso utiliza a [ferramenta Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). Quando instalar e configurar a ferramenta num controlador de domínio, sincronizá-lo local identidades de AD para o Azure AD no local. 

### <a name="download-the-tool"></a>Transferir a ferramenta


1. No portal do Azure, os administradores da Contoso aceda a **do Azure Active Directory** > **do Azure AD Connect**e transferir a versão mais recente da ferramenta para o servidor que estão a utilizar para sincronização.

    ![Transferir o AD Connect](./media/contoso-migration-infrastructure/download-ad-connect.png) 

2. São iniciados a **Azureadconnect** instalação, com **utilizar definições rápidas**. Esta é a instalação mais comuns e pode ser utilizada para uma topologia de floresta única com sincronização de hash de palavra-passe para autenticação.

    ![Assistente para conectar do AD](./media/contoso-migration-infrastructure/ad-connect-wiz1.png) 

3. Na **ligar para o Azure AD**, eles especificam as credenciais para ligar ao Azure AD (na forma CONTOSO\admin ou contoso.com\admin).

    ![Assistente para conectar do AD](./media/contoso-migration-infrastructure/ad-connect-wiz2.png) 

4. Na **ligar ao AD DS**, eles especificam as credenciais para no local AD.

     ![Assistente para conectar do AD](./media/contoso-migration-infrastructure/ad-connect-wiz3.png) 

5. Na **pronto para configurar**, pode clicar em **iniciar o processo de sincronização quando tiver concluído a configuração** para iniciar a sincronização imediatamente. Em seguida, instalar.

Tenha em atenção que:
- A Contoso tiver uma ligação direta para o Azure. Se o ambiente AD está atrás de um proxy, leia este [artigo](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-connectivity).
- Após a primeira sincronização, no local objetos do AD podem ser vistos no Azure AD.

    ![AD no Azure no local](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 

- A equipe de TI da Contoso é representada em cada grupo, com base na sua função.

    ![Os membros do AD no Azure no local](./media/contoso-migration-infrastructure/on-prem-ad-group-members.png) 

### <a name="set-up-rbac"></a>Configurar o RBAC

Azure [controlo de acesso baseado em funções (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) permite a gestão de acessos detalhada para o Azure. Utilizando o RBAC, pode conceder apenas a quantidade de acesso que os utilizadores precisam realizar tarefas. Atribuir a função RBAC adequada para os utilizadores, grupos e aplicações num nível de âmbito. O âmbito de uma atribuição de função pode ser uma subscrição, um grupo de recursos ou um único recurso. 

Os administradores da Contoso agora atribui funções para os grupos do AD que eles sincronizados no local.

1. Na **ControlCobRG** grupo de recursos, pode clicar em **controlo de acesso (IAM)** > **adicionar atribuição de função**.
2. Na **adicionar atribuição de função** > **função**, > **contribuinte**, eles selecionam o **ContosoCobRG** grupo do AD da lista. O grupo, em seguida, é apresentado na **membros selecionados** lista. 
3. Eles repetir isso com as mesmas permissões para os outros grupos de recursos (exceto para **ContosoAzureAdmins**), ao adicionar as permissões de Contribuidor à conta do AD que corresponde ao grupo de recursos.
4. Para o **ContosoAzureAdmins** grupo do AD, atribuir os **proprietário** função.

    ![Os membros do AD no Azure no local](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 


## <a name="step-3-design-for-resilience-and-disaster"></a>Passo 3: Design para após desastre e resiliência

### <a name="set-up-regions"></a>Configurar regiões

Recursos do Azure são implementados dentro de regiões.

- Regiões estão organizadas em geografias e requisitos de residência, soberania de dados, conformidade e resiliência de dados são honrados dentro dos limites geográficos.
- Uma região é composta por um conjunto de datacenters. Estes datacenters são implantadas dentro de um perímetro definido por latência e ligados através de uma rede de baixa latência dedicada regional.
- Cada região do Azure está emparelhada com uma região diferente para resiliência.
- Leia sobre [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/)e compreender [como regiões estão emparelhadas](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).


Contoso tiver decidido a escolher a E.U.A. Leste 2 (localizado em Virgínia) como a região primária e centro dos E.U.A. (localizado no Iowa) como a região secundária. Existem algumas razões para isso:

- O Centro de dados da Contoso está localizado em nova York e Contoso considerado latência para o datacenter mais próximo.
- A região E.U.A. Leste 2 tem todos os serviços e produtos que a Contoso precisa de utilizar. Nem todas as regiões do Azure são os mesmos em termos de produtos e serviços disponíveis. Pode rever [produtos do Azure por região](https://azure.microsoft.com/global-infrastructure/services/).
- Centro dos E.U.A. é a Azure região emparelhada nos e.u.a. Leste 2.

À medida que ele pensa sobre o ambiente híbrido, a Contoso precisa de considerar como criar uma estratégia de recuperação após desastre e resiliência para o design de região. De forma ampla, o intervalo de estratégias de uma implementação numa única região, que se baseia na plataforma Azure como domínios de falha e regional de emparelhamento para ser resiliente, por meio de um ativo-ativo completa do modelo nos quais serviços cloud e a base de dados de recursos são implementados e manutenção utilizadores de duas regiões.

Contoso decidiu tirar um meio caminho. Ele irá implementar aplicações e recursos numa região primária e manter uma cópia completa da infra-estrutura na região secundária, para que fique pronto para agir como uma cópia de segurança completa em caso de desastre da aplicação completa, ou a falha regional.

### <a name="set-up-availability-zones"></a>Configure as zonas de disponibilidade

As zonas de disponibilidade ajudam a proteger aplicações e dados de falhas de datacenter.

- Cada zona de disponibilidade é uma localização física exclusiva dentro de uma região do Azure.
- Cada zona é constituída por um ou mais datacenters equipados com energia, refrigeração e redes. 
- Existe um mínimo de três zonas separadas em todas as regiões ativadas.
- A separação física das zonas dentro de uma região protege as aplicações e dados de falhas de datacenter.

Contoso irá implementar disponibilidade zonas como aplicações, ligue para escalabilidade, elevada disponibilidade e resiliência. [Saiba mais](https://docs.microsoft.com/azure/availability-zones/az-overview). 


## <a name="step-4-design-a-network-infrastructure"></a>Passo 4: Estruturar uma infraestrutura de rede

Com o design regional em vigor, a Contoso está pronta para considerar uma estratégia de rede. Ele precisa pensar em como o datacenter no local e o Azure, ligarem e comunicarem entre si e como conceber a infraestrutura de rede no Azure. Especificamente a Contoso precisa de:

- **Planear a conectividade de rede híbrida**: Descobrir como ela será para ligar as redes no local e o Azure.
- **Estruturar uma infraestrutura de rede do Azure**: Decida como irá implementar redes através de regiões. Como comunicará redes na mesma região e entre regiões?
- **Criar e configurar redes do Azure**: Configurar redes do Azure e de sub-redes e decidir o que irá residir nos mesmos.

### <a name="plan-hybrid-network-connectivity"></a>Planear a conectividade de rede híbrida

Contoso considerado um [número de arquiteturas](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) para híbrida entre o Azure e o datacenter no local de rede. [Leia mais](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations) sobre como comparar as opções.

Como lembrete, a infraestrutura de rede no local do Contoso consiste atualmente o Centro de dados em nova York e local ramos na parte do Leste dos EUA.  Todas as localizações de tem uma conexão de classe de negócios para a internet.  Em seguida, cada uma das ramificações está ligada ao centro de dados por meio de um túnel IPSec VPN através da internet.

![Rede de contoso](./media/contoso-migration-infrastructure/contoso-networking.png) 

Eis como Contoso decidiu implementar uma conectividade híbrida:

1. Configure uma nova ligação de VPN de site a site entre o datacenter de Contoso em nova York e as duas regiões do Azure em E.U.A. Leste 2 e EUA Central.
2. Tráfego do office de ramo vinculado para redes virtuais do Azure irá encaminhar através do Centro de dados principal do Contoso. 
3. Como Contoso dimensiona-se a implementação do Azure, ele irá estabelecer uma ligação ExpressRoute entre o datacenter e regiões do Azure. Quando isto acontecer, a Contoso irá reter a ligação de site-site VPN finalidades de ativação pós-falha.
    - [Saiba mais](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations) sobre como escolher entre uma solução híbrida VPN e ExpressRoute.
    - Certifique-se [ExpressRoute localizações e suporte](https://docs.microsoft.com/azure/expressroute/expressroute-locations-providers).


**Apenas VPN**

![VPN da Contoso](./media/contoso-migration-infrastructure/hybrid-vpn.png) 


**ExpressRoute e VPN**

![Contoso VPN/ExpressRoute](./media/contoso-migration-infrastructure/hybrid-vpn-expressroute.png) 


### <a name="design-the-azure-network-infrastructure"></a>Conceber a infraestrutura de rede do Azure

É fundamental que Contoso coloca redes no local de forma que faz a implementação híbrida segura e dimensionável. Para tal, Contoso está a demorar uma abordagem de longo prazo e está a conceber redes virtuais (VNets) para que seja resiliente e pronto para as empresas. [Saiba mais](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) sobre o planeamento de VNets.

Para ligar as duas regiões, Contoso decidiu implementar um modelo de rede hub-para-hub:

- Em cada região, a Contoso usará um modelo de hub-and-spoke.
- Para ligar a redes e hubs, Contoso usará o peering de rede do Azure.

#### <a name="network-peering"></a>Peering de rede

O Azure disponibiliza o peering de rede para ligar VNets e os hubs. Global peering permite ligações entre VNets/hubs em regiões diferentes. Local peering liga as VNets na mesma região. O VNet peering fornecem uma série de vantagens:

- Tráfego de rede entre VNets em modo de peering é privado.
- O tráfego entre as VNets é mantido na rede backbone do Microsoft. Não existem internet pública, gateways ou encriptação é necessária a comunicação entre as VNets.
- Peering fornece um padrão, a ligação de baixa latência e largura de banda alta entre recursos em VNets diferentes.

[Saiba mais](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) sobre o peering de rede.

#### <a name="hub-to-hub-across-regions"></a>Hub-para-hub em várias regiões

Contoso irá implementar um hub em cada região. Um hub é uma rede virtual (VNet) no Azure que age como um ponto central de conectividade à sua rede no local. O hub VNets irá ligar entre si através de global VNet peering. Global VNet peering liga-se de VNets entre regiões do Azure.

- O hub em cada região está em modo de peering para o seu hub de parceiros na outra região.
- O hub é executado o peering para cada rede na sua região e pode ligar-se a todos os recursos de rede.

    ![Global peering](./media/contoso-migration-infrastructure/global-peering.png) 

#### <a name="hub-and-spoke-within-a-region"></a>Hub-and-spoke dentro de uma região

Em cada região, Contoso irá implementar VNets com finalidades diferentes, como redes spoke do hub de região. VNets dentro de uma região utilizar peering para ligar ao seu hub e entre si.

#### <a name="design-the-hub-network"></a>Conceção da rede de hub

Dentro do modelo de hub- and -spoke que escolheu Contoso, tem de pensar sobre como o tráfego a partir do Centro de dados no local e da internet, será encaminhado. Eis como Contoso tenha decidido processam o encaminhamento para os E.U.A. Leste 2 e a hubs E.U.A. Central:

- Contoso é criar uma rede conhecida como "c inversa", uma vez que este é o caminho que os pacotes siga a partir da rede de entrada para a saída.
- A arquitetura de rede tem dois limites, de uma zona de perímetro de front-end não fidedigno e de uma zona de confiança de back-end.
- Uma firewall terão um adaptador de rede em cada zona, controlar o acesso a zonas fidedignos.
- Da internet:
    - Tráfego de Internet atingirá um com balanceamento de carga endereço IP na rede de perímetro.
    - Este tráfego é encaminhado através da firewall e sujeita a regras de firewall.
    - Depois de controlos de acesso de rede são implementados, o tráfego será encaminhado para o local apropriado na zona fidedigna.
    - O tráfego de saída da VNet será encaminhado para a internet através de rotas definidas pelo utilizador (UDRs). O tráfego é forçado através da firewall e inspecioná-los em conformidade com políticas de Contoso.
- Entre o datacenter da Contoso:
    - Tráfego de entrada através da VPN site a site (ou ExpressRoute) atinge o endereço IP público do gateway de VPN do Azure.
    - O tráfego é encaminhado através da firewall e sujeita a regras de firewall.
    - Depois de aplicar regras de firewall, o tráfego é encaminhado para um balanceador de carga interno (Standard SKU) na sub-rede interna de zona fidedigna.
    - Tráfego de saída da sub-rede fidedigno para o datacenter no local através de VPN é encaminhado através da firewall e regras aplicadas, antes de passar pela conexão VPN site a site.



### <a name="design-and-set-up-azure-networks"></a>Criar e configurar redes do Azure

Com uma rede e a topologia de roteamento no local, a Contoso está pronto para configurar sub-redes e redes do Azure.

- Contoso implementará uma rede privada da classe A no Azure (0.0.0.0 para 127.255.255.255). Isso funciona, desde locais atualmente tem uma classe B endereço privado espaço 172.160.0/16 para que a Contoso pode ter a certeza, não haverá nenhuma sobreposição entre intervalos de endereços.
- Vai implementar VNets nas regiões primárias e secundárias.
- Contoso usará uma convenção de nomenclatura que inclua o prefixo **VNET** e a abreviatura de região **EUS2** ou **CUS**. Usando esse padrão, as redes de hub serão designadas **VNET-HUB-EUS2** (E.U.A. Leste 2) e **VNET-HUB-CUS** (EUA Central).
- Contoso não tem um [solução IPAM](https://docs.microsoft.com/windows-server/networking/technologies/ipam/ipam-top), pelo que tem de planear para o encaminhamento de rede sem NAT.


#### <a name="virtual-networks-in-east-us-2"></a>Redes virtuais na região E.U.A. Leste 2

E.U.A. Leste 2 é a região primária que Contoso irá utilizar para implementar recursos e serviços. Eis como Contoso será arquitetar redes dentro da mesma:

- **Hub**: O hub VNet na região E.U.A. Leste 2 é o ponto central de conectividade primário para o datacenter no local.
- **VNets**: VNets spoke na região E.U.A. Leste 2 pode ser utilizado para isolar cargas de trabalho, se necessário. Além da VNet do Hub, a Contoso terá spoke duas VNets na região E.U.A. Leste 2:
    - **VNET-DEV-EUS2**. Esta VNet irá fornecer o desenvolvimento e teste da Equipe com uma rede totalmente funcional para projetos de desenvolvimento. Ele funcionará como uma área de piloto de produção e irá contar com a infraestrutura de produção para a função.
    - **VNET-PROD-EUS2**: Componentes de produção de IaaS do Azure estarão localizados na rede. 
    -  Cada VNet tem seu próprio espaço de endereço exclusivo, sem sobreposição. Contoso pretende configurar o encaminhamento sem a necessidade de NAT.
- **Sub-redes**:
    - Haverá uma sub-rede em cada rede para cada camada de aplicação
    - Cada sub-rede na rede de produção terão uma sub-rede correspondente na VNet em modo de desenvolvimento.
    - Além disso, a rede de produção tem uma sub-rede para controladores de domínio.

VNets na região E.U.A. Leste 2 estão resumidos na tabela seguinte.

**VNet** | **Range** | **Ponto a ponto**
--- | --- | ---
**VNET-HUB-EUS2** | 10.240.0.0/20 | VNET-HUB-CUS2, VNET-DEV-EUS2, VNET-PROD-EUS2
**VNET-DEV-EUS2** | 10.245.16.0/20 | VNET-HUB-EUS2
**VNET-PROD-EUS2** | 10.245.32.0/20 | VNET-HUB-EUS2, VNET-PROD-CUS

![Spoke/hub na região primária](./media/contoso-migration-infrastructure/primary-hub-peer.png) 


#### <a name="subnets-in-the-east-us-2-hub-network-vnet-hub-eus2"></a>Sub-redes na rede do concentrador de 2 do Leste E.u.a. (VNET-HUB-EUS2)

**Sub-rede/zona** | **CIDR** | * * Utilizáveis endereços IP
--- | --- | ---
**IB-UntrustZone** | 10.240.0.0/24 | 251
**IB-TrustZone** | 10.240.1.0/24 | 251
**OB-UntrustZone** | 10.240.2.0/24 | 251
**OB-TrustZone** | 10.240.3.0/24 | 251
**GatewaySubnets** | 10.240.10.0/24 | 251


#### <a name="subnets-in-the-east-us-2-dev-network-vnet-dev-eus2"></a>Sub-redes na rede de desenvolvimento de 2 do Leste E.u.a. (VNET-DEV-EUS2)

A VNet de desenvolvimento é usada pela equipe de desenvolvimento como uma área de piloto de produção. Ele tem três sub-redes.

**Sub-rede** | **CIDR** | **Endereços** | **Na sub-rede**
--- | --- | --- | ---
**DEV-FE-EUS2** | 10.245.16.0/22 | 1019 | VMs de camada de front-ends/web
**DEV-APP-EUS2** | 10.245.20.0/22 | 1019 | VMs de camada de aplicação
**DEV-DB-EUS2** | 10.245.24.0/23 | 507 | VMs de base de dados


#### <a name="subnets-in-the-east-us-2-production-network-vnet-prod-eus2"></a>Sub-redes na rede de produção de 2 do Leste E.u.a. (VNET-PROD-EUS2)

Componentes de IaaS do Azure estão localizados na rede de produção. Cada camada de aplicação tem sua própria sub-rede. Sub-redes correspondem na rede de desenvolvimento, com a adição de uma sub-rede para controladores de domínio.

**Sub-rede** | **CIDR** | **Endereços** | **Na sub-rede**
--- | --- | --- | ---
**FE-PROD-EUS2** | 10.245.32.0/22 | 1019 | VMs de camada de front-ends/web
**PROD-APP-EUS2** | 10.245.36.0/22 | 1019 | VMs de camada de aplicação
**PROD-DB-EUS2** | 10.245.40.0/23 | 507 | VMs de base de dados
**PROD-DC-EUS2** | 10.245.42.0/24 | 251 | As VMs do controlador de domínio


![Arquitetura de rede hub](./media/contoso-migration-infrastructure/azure-networks-eus2.png)


#### <a name="virtual-networks-in-central-us-secondary-region"></a>Redes virtuais na região E.U.A. Central (região secundária)

E.U.A. central é a região secundária da Contoso. Eis como Contoso será arquitetar redes dentro da mesma:

- **Hub**: O hub que vnet na região E.U.A. Leste 2 é o ponto central de conectividade para o datacenter no local e o spoke que vnets na região E.U.A. Leste 2 pode ser utilizado para isolar cargas de trabalho se for necessário, geridas separadamente dos outros spokes.
- **VNets**: Contoso terão duas VNets na região E.U.A. Central:
    - VNET-PROD-CUS. Esta VNet é uma rede de produção, semelhante à VNET PROD_EUS2. 
    - VNET-ASR-CUS. Esta VNet atuará como uma localização na qual as VMs são criadas após a ativação pós-falha no local ou como uma localização para VMs do Azure com ativação pós-falha dos principais para a região secundária. Esta rede é semelhante para as redes de produção, mas sem quaisquer controladores de domínio no mesmo.
    -  Cada VNet na região tem seu próprio espaço de endereços, sem sobreposição. Contoso irá configurar o encaminhamento sem NAT.
- **Sub-redes**: As sub-redes irão ser arquitetadas de forma semelhante para neste dos E.U.A. 2. A exceção é que Contoso não precisa de uma sub-rede para controladores de domínio.

As VNets na região E.U.A. Central estão resumidas na tabela seguinte.

**VNet** | **Range** | **Ponto a ponto**
--- | --- | ---
**VNET-HUB-CUS** | 10.250.0.0/20 | VNET-HUB-EUS2, VNET-ASR-CUS, VNET-PROD-CUS
**VNET-ASR-CUS** | 10.255.16.0/20 | VNET-HUB-CUS, VNET-PROD-CUS
**VNET-PROD-CUS** | 10.255.32.0/20 | VNET-HUB-CUS, VNET-ASR-CUS, VNET-PROD-EUS2  


![Spoke/hub na região emparelhada](./media/contoso-migration-infrastructure/paired-hub-peer.png)


#### <a name="subnets-in-the--central-us-hub-network-vnet-hub-cus"></a>Sub-redes na rede de nós Hub Central (VNET-HUB-CUS)

**Sub-rede** | **CIDR** | **Endereços IP utilizáveis**
--- | --- | ---
**IB-UntrustZone** | 10.250.0.0/24 | 251
**IB-TrustZone** | 10.250.1.0/24 | 251
**OB-UntrustZone** | 10.250.2.0/24 | 251
**OB-TrustZone** | 10.250.3.0/24 | 251
**GatewaySubnet** | 10.250.2.0/24 | 251


#### <a name="subnets-in-the-central-us-production-network-vnet-prod-cus"></a>Sub-redes na rede Central nos produção (VNET-PROD-CUS)

Em paralelo para a rede de produção na região E.U.A. Leste 2 primária, existe uma rede de produção na região E.U.A. Central secundária. 

**Sub-rede** | **CIDR** | **Endereços** | **Na sub-rede**
--- | --- | --- | ---
**PROD-FE-CUS** | 10.255.32.0/22 | 1019 | VMs de camada de front-ends/web
**PROD-APP-CUS** | 10.255.36.0/22 | 1019 | VMs de camada de aplicação
**PROD-DB-CUS** | 10.255.40.0/23 | 507 | VMs de base de dados
**PROD-DC-CUS** | 10.255.42.0/24 | 251 | As VMs do controlador de domínio

#### <a name="subnets-in-the-central-us-failoverrecovery-network-in-central-us-vnet-asr-cus"></a>Sub-redes na rede de ativação pós-falha/recuperação de centro dos E.U.A. na região E.U.A. Central (CUS de ASR de VNET)

A rede de VNET-ASR-CUS é utilizada para fins de ativação pós-falha entre regiões. Recuperação de site será utilizada para replicar e efetuar a ativação pós-falha de VMs do Azure entre regiões. Ele também funciona como um centro de dados da Contoso a rede do Azure para cargas de trabalho protegidas que permanecem no local, mas a ativação pós-falha para o Azure para recuperação após desastre.

VNET-ASR-CUS é a mesma sub-rede básica que a VNet na região E.U.A. Leste 2, mas sem a necessidade de uma sub-rede do controlador de domínio de produção.

**Sub-rede** | **CIDR** | **Endereços** | **Na sub-rede**
--- | --- | --- | ---
**ASR-FE-CUS** | 10.255.16.0/22 | 1019 | VMs de camada de front-ends/web
**ASR-APP-CUS** | 10.255.20.0/22 | 1019 | VMs de camada de aplicação
**ASR-DB-CUS** | 10.255.24.0/23 | 507 | VMs de base de dados

![Arquitetura de rede hub](./media/contoso-migration-infrastructure/azure-networks-cus.png)

#### <a name="configure-peered-connections"></a>Configurar ligações de peering

O hub em cada região será possível configurar o peering para o hub a outra região e todas as VNets dentro da região do hub. Assim, para os hubs de comunicar e para ver todas as VNets dentro de uma região. Tenha em atenção que:

- Peering cria uma ligação bilaterais. Um do ponto de início na primeira VNet e um outro na VNet em modo de segundo.
- Numa implementação híbrida, o tráfego que passa entre itens de mesmo nível tem de ser vistos a partir da ligação VPN entre o datacenter no local e o Azure. Para ativar esta opção, existem algumas definições específicas que devem ser definidas em ligações de peering.

Para todas as ligações de VNets spoke através do hub para o datacenter no local, a Contoso precisa de permitir tráfego a ser reencaminhada e atravessa as os gateways de VPN.

##### <a name="domain-controller"></a>Controlador de domínio

Para os controladores de domínio na rede de VNET-PROD-EUS2, Contoso, além de tráfego flua entre a rede de produção/hub EUS2 tanto através da ligação de VPN no local. Para fazer este it administradores da Contoso tem de permitir o seguinte:

1. **Permitir tráfego reencaminhado** e **permitir que as configurações de trânsito de gateway** na conexão em modo de peering. No nosso exemplo, isso seria VNET-HUB-EUS2 para ligação de VNET-PROD-EUS2.

    ![Peering](./media/contoso-migration-infrastructure/peering1.png)

2. **Permitir tráfego reencaminhado** e **utilizar gateways remotos** no outro lado do peering, na VNET-PROD-EUS2 para ligação de VNET-HUB-EUS2.

    ![Peering](./media/contoso-migration-infrastructure/peering2.png)

3. No local que irá configurar uma rota estática que direciona o tráfego local para encaminhar entre o túnel VPN para a VNet. Seria possível concluir a configuração no gateway que fornece o túnel VPN da Contoso para o Azure. Eles usam o RRAS para isso.

    ![Peering](./media/contoso-migration-infrastructure/peering3.png)

##### <a name="production-networks"></a>Redes de produção 

Uma rede de mesmo nível spoked não pode ver uma rede de mesmo nível spoked noutra região através de um hub.

Para redes de produção da Contoso em ambas as regiões para ver uns aos outros, os administradores da Contoso tem de criar uma ligação direta em modo de peering de VNET-PROD-EUS2 e de eventos-PROD-CUS. 

![Peering](./media/contoso-migration-infrastructure/peering4.png)

### <a name="set-up-dns"></a>Configurar o DNS

Ao implementar recursos em redes virtuais, tem duas opções para resolução de nomes de domínio. Pode utilizar a resolução de nomes fornecida pelo Azure, ou fornecer servidores DNS para resolução. O tipo de resolução de nomes que utiliza depende de como os seus recursos precisam de comunicar entre si. Obtenha [mais informações](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#azure-provided-name-resolution) sobre o serviço de DNS do Azure.

Os administradores da Contoso decidiu que o serviço de DNS do Azure não é uma boa opção no ambiente híbrido. Em vez disso, eles serão tirar partido dos servidores DNS no local.

- Como se trata de uma rede híbrida de todas a VMs no local e no Azure tem de conseguir resolver nomes para funcionar corretamente. Isso significa que as definições de DNS personalizadas devem ser aplicadas a todas as VNets.
- Atualmente, a Contoso tiver controladores de domínio implementados no Centro de dados Contoso e das filiais. Os servidores DNS primários são CONTOSODC1(172.16.0.10) e CONTOSODC2(172.16.0.1)
- Quando as VNets são implementadas, os controladores de domínio no local serão definidos para ser utilizada como servidores DNS nas redes. 
- Para configurar isso, quando utilizar DNS personalizado na VNet, o endereço IP de resoluções de recursiva do Azure (por exemplo, 168.63.129.16) tem de ser adicionado à lista de DNS.  Para fazer isso, o Contoso configura as definições do servidor DNS em cada VNet. Por exemplo, as definições de DNS personalizadas para a rede de VNET-HUB-EUS2 seria da seguinte forma:
    
    ![DNS Personalizado](./media/contoso-migration-infrastructure/custom-dns.png)

Além dos controladores de domínio no local, Contoso irá implementar mais quatro para oferecer suporte as redes do Azure, dois para cada região. Eis o que Contoso irá implementar no Azure.

**Região** | **DC** | **VNet** | **Sub-rede** | **Endereço IP**
--- | --- | --- | --- | ---
EUS2 | CONTOSODC3 | VNET-PROD-EUS2 | PROD-DC-EUS2 | 10.245.42.4
EUS2 | CONTOSODC4 | VNET-PROD-EUS2 | PROD-DC-EUS2 | 10.245.42.5
CUS | CONTOSODC5 | VNET-PROD-CUS | CONTROLADOR DE DOMÍNIO-PROD-CUS | 10.255.42.4
CUS | CONTOSODC6 | VNET-PROD-CUS | CONTROLADOR DE DOMÍNIO-PROD-CUS | 10.255.42.4

Depois de implementar os controladores de domínio no local, a Contoso precisa de atualizar as definições de DNS em redes em qualquer região para incluir novos controladores de domínio na lista de servidores DNS.



#### <a name="set-up-domain-controllers-in-azure"></a>Configurar controladores de domínio no Azure

Depois de atualizar as definições de rede, os administradores da Contoso estão prontos para criar os controladores de domínio no Azure.

1. No portal do Azure, que implementam uma nova VM do Windows Server para a VNet apropriada.
2. É possível criar conjuntos de disponibilidade em cada local para a VM. Conjuntos de disponibilidade, faça o seguinte:
   - Certifique-se de que os recursos de infraestrutura do Azure separa as VMs em diferentes estruturas na região do Azure. 
   - Permite que a Contoso ser elegível para o SLA de 99,95% para as VMs no Azure.  [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).

     ![Grupo de disponibilidade](./media/contoso-migration-infrastructure/availability-group.png) 
3. Depois de implementada a VM, que abrem a interface de rede para a VM. Definir o endereço IP privado para estático e especifique um endereço válido.

    ![NIC DE VM](./media/contoso-migration-infrastructure/vm-nic.png)

4. Agora, eles anexar um disco de dados nova para a VM. Este disco contém a base de dados do Active Directory e a partilha sysvol. 
   - O tamanho do disco determina o número de IOPS que suporta.
   - Ao longo do tempo, o tamanho do disco talvez seja necessário aumentar à medida que cresce o ambiente.
   - A unidade não deve ser definida como leitura/escrita para colocação em cache do anfitrião. Bases de dados de diretório Active Directory não oferecem suporte isso.

     ![Disco do Active Directory](./media/contoso-migration-infrastructure/ad-disk.png)

5. Depois do disco é adicionado, eles ligam à VM através do ambiente de trabalho remoto e abra o Gestor de servidor.
6. Em seguida, em **serviços de armazenamento de ficheiros e**, que executam o Assistente de novo Volume, garantindo que a unidade é dada a letra f: ou acima na local VM.

     ![Assistente de novo Volume](./media/contoso-migration-infrastructure/volume-wizard.png)

7. No Gestor de servidores, adicione a **serviços de domínio do Active Directory** função. Em seguida, configurar a VM como o controlador de domínio.

      ![Função de servidor](./media/contoso-migration-infrastructure/server-role.png)  

9. Depois da VM é configurada como um controlador de domínio e reiniciada, eles abrir o Gestor de DNS e configurar o Resolvedor de DNS do Azure como um reencaminhador.  Isso permite que o controlador de domínio reencaminhar as consultas DNS que não é possível resolver no DNS do Azure.

    ![Reencaminhador DNS](./media/contoso-migration-infrastructure/dns-forwarder.png)

10. Agora, eles atualizar as definições de DNS personalizadas para cada VNet com o controlador de domínio apropriadas para a região da VNet. Eles incluem controladores de domínio no local na lista.

### <a name="set-up-active-directory"></a>Configurar o Active Directory

AD é um serviço crítico no funcionamento em rede e tem de ser configurado corretamente. Os administradores da Contoso irão criar sites de AD para o datacenter da Contoso e para as regiões EUS2 e CUS.  

1. Eles criaram dois novos sites (EUS2 do AZURE e AZURE CUS), juntamente com o site do Centro de dados (ContosoDatacenter).
2. Depois de criar os sites, sub-redes, criam nos sites, de acordo com as VNets e o Centro de dados.

    ![Sub-redes de DC](./media/contoso-migration-infrastructure/dc-subnets.png)

3. Em seguida, eles criaram duas ligações de site para ligar tudo. Os controladores de domínio, em seguida, devem ser movidos para a respetiva localização.

    ![Ligações de DC](./media/contoso-migration-infrastructure/dc-links.png)

4. Depois de tudo o que é configurado, é a topologia de replicação do Active Directory no local.
    
    ![Replicação do controlador de domínio](./media/contoso-migration-infrastructure/ad-resolution.png)

5. Com tudo completa, uma lista dos controladores de domínio e sites são mostrados no Centro de administração do diretório de Active Directory no local.

    ![Centro de administração do AD](./media/contoso-migration-infrastructure/ad-center.png)

## <a name="step-5-plan-for-governance"></a>Passo 5: Plano de governação

O Azure fornece uma gama de controlos de governação em serviços e a plataforma do Azure. [Leia mais](https://docs.microsoft.com/azure/security/governance-in-azure) para ter uma compreensão básica das opções.

Tal como configurar a identidade e controlo de acesso, Contoso já começou a colocar alguns aspetos de segurança e governação no local. De forma ampla, existem três áreas, que ele precisa considerar:

- **Política**: Aplica-se de política no Azure e impõe regras e efeitos aos recursos, para que os recursos mantêm-se em conformidade com os requisitos empresariais e SLAs.
- **Bloqueia**: Azure permite-lhe para subscrições de bloqueio, grupos de recursos e outros recursos, para que eles só podem ser modificados por aqueles com autoridade para fazer isso.
- **Etiquetas**: Recursos podem ser controlados, auditados e geridos com etiquetas. Etiquetas de anexar metadados a recursos, fornecendo informações sobre recursos ou proprietários.

### <a name="set-up-policies"></a>Definir políticas

O serviço do Azure Policy avalia os recursos, analisando aqueles que não em conformidade com as definições de política que tem no local. Por exemplo, pode ter uma política que apenas permite que determinados tipos de VMs ou exige recursos para ter uma etiqueta específica. 

Políticas do Azure, especifique uma definição de política e uma atribuição de política especifica o âmbito em que deve ser aplicada uma política. O âmbito pode ir desde um grupo de gestão a um grupo de recursos. [Saiba](../governance/policy/tutorials/create-and-manage.md) sobre a criação e gestão de políticas.

A Contoso pretende começar com duas diretivas:

- Ele quer uma política para garantir que os recursos só podem ser implementados nas regiões EUS2 e CUS.
- Quer limitar os SKUs de VM para apenas SKUs aprovados. A intenção é para garantir que não são utilizadas a cara SKUs de VM.

#### <a name="limit-resources-to-regions"></a>Recursos de limite para regiões

A Contoso utiliza a definição de política incorporada **localizações permitidas** para limitar as regiões de recursos.

1. No portal do Azure, clique em **todos os serviços**e procure **política**.
2. Selecione **atribuições** > **atribuir política**.
3. Na lista de políticas, selecione **localizações permitidas**.
4. Definir **âmbito** para o nome da subscrição do Azure e selecione as duas regiões na lista de permitidos.

    ![Política de permissão regiões](./media/contoso-migration-infrastructure/policy-region.png)

5. Por predefinição, a política estiver definida com **negar**, o que significa que, se alguém inicia uma implementação na subscrição que não esteja na EUS2 ou CUS, a implementação irá falhar. Eis o que acontece se alguém na subscrição Contoso tentar configurar uma implementação em E.U.A. oeste.

    ![Política de falha](./media/contoso-migration-infrastructure/policy-failed.png)

#### <a name="allow-specific-vm-skus"></a>Permitir que os SKUs de VM específicos

Contoso usará a definição de política incorporada **permitir a máquinas virtuais SKUs** para limitar o tipo de VMs que podem ser criadas na subscrição. 

![Política de SKU](./media/contoso-migration-infrastructure/policy-sku.png)



#### <a name="check-policy-compliance"></a>Verificação de conformidade de política

As políticas entram em vigor imediatamente e Contoso pode verificar os recursos de conformidade. 

1. No portal do Azure, clique nas **conformidade** ligação.
2. É apresentado o dashboard de conformidade. Pode desagregar para obter mais detalhes.

    ![Conformidade com a política](./media/contoso-migration-infrastructure/policy-compliance.png)


### <a name="set-up-locks"></a>Configurar bloqueios

Contoso longa esteve usando a estrutura ITIL para a gestão dos respetivos sistemas. Um dos aspectos mais importantes do framework é o controle de alterações e Contoso quer ter a certeza de que o controle de alterações está implementado na implementação do Azure.

Contoso vai implementar bloqueios da seguinte forma:

- Qualquer componente de ativação pós-falha ou de produção deve estar no grupo de recursos que tem um bloqueio só de leitura.  Isso significa que para modificar ou eliminar itens de produção, o bloqueio tem de ser removido. 
- Grupos de recursos de não produção terão CanNotDelete bloqueios. Isso significa que autorizado os utilizadores podem ler ou modificar um recurso, mas não é possível eliminá-lo.

[Saiba mais](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) sobre bloqueios.

### <a name="set-up-tagging"></a>Configurar a etiquetagem

Para monitorizar os recursos como estes serão adicionados, será cada vez mais importante da Contoso associar recursos um departamento apropriado, o cliente e o ambiente. 

Além de fornecer informações sobre recursos e os proprietários, etiquetas permitirá Contoso, a agregação e de grupo de recursos e usar esses dados para fins de estorno.

A Contoso precisa de visualizar os seus recursos do Azure de forma que faça sentido para a empresa. Por exemplo, por função ou departamento. Tenha em atenção que os recursos não precisam de residir no mesmo grupo de recursos para partilhar uma etiqueta. Contoso criará uma taxonomia de etiquetas simples para que todos usam as mesmo etiquetas.

**Nome da etiqueta** | **Valor**
--- | ---
Centro de Custo | 12345: Tem de ser um centro de custos válido do SAP.
BusinessUnit | Nome da unidade de negócios (a partir do SAP). Correspondências de CostCenter.
ApplicationTeam | Alias de e-mail da equipa que possui suporte para a aplicação.
CatalogName | Nome da aplicação ou ShareServices, pelo catálogo de serviços que suporte o recurso.
Service Manager | Alias de e-mail do Service Manager a ITIL para o recurso.
COBPriority | Prioridade definida pela empresa para BCDR. Valores de 1 a 5.
ENV | Desenvolvimento, STG, PROD são os valores possíveis. Que representa o desenvolvimento, teste e produção.

Por exemplo: 

 ![Etiquetas do Azure](./media/contoso-migration-infrastructure/azure-tag.png)

Depois de criar a marca, Contoso irá voltar atrás e criar novas definições de política do Azure e atribuições, para impor a utilização das etiquetas necessárias em toda a organização.


## <a name="step-6-consider-security"></a>Passo 6: Considere a segurança

A segurança é crucial na cloud e o Azure disponibiliza uma vasta gama de capacidades e ferramentas de segurança. Isso o ajudará a criar soluções seguras, na plataforma do Azure segura. Leia [confiança na cloud fidedigna](https://azure.microsoft.com/overview/trusted-cloud/) para saber mais sobre a segurança do Azure.

Existem alguns aspetos da Contoso a considerar:

- **Centro de segurança do Azure**: O Centro de Segurança do Azure oferece gestão de segurança unificada e proteção contra ameaças avançada entre cargas de trabalho na cloud híbrida. Com o Centro de Segurança, pode aplicar políticas de segurança pelas suas cargas de trabalho, limitar a sua exposição a ameaças e detetar e responder a ataques.  [Saiba mais](https://docs.microsoft.com/azure/security-center/security-center-intro).
- **Grupos de segurança (NSGs) de rede**: Um NSG é um filtro (firewall) que contém uma lista de segurança de regras que, quando aplicada, permitir ou negar o tráfego de rede para recursos ligados a VNets do Azure. [Saiba mais](https://docs.microsoft.com/azure/virtual-network/security-overview).
- **Encriptação de dados**: O Azure Disk Encryption é um recurso que lhe permite encriptar discos da máquina virtual Windows e Linux IaaS. [Saiba mais](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest).

### <a name="work-with-the-azure-security-center"></a>Trabalhar com o Centro de segurança do Azure

Contoso está à procura de uma vista rápida da postura de segurança da sua cloud híbrida novo e especificamente suas cargas de trabalho do Azure.  Como resultado, Contoso decidiu implementar o Centro de segurança do Azure a partir das seguintes funcionalidades: 

- Gestão de políticas centralizada
- Avaliação contínua
- Recomendações acionáveis 

#### <a name="centralize-policy-management"></a>Centralize a gestão de política

Com a gestão da política centralizada, Contoso irá garantir a conformidade com requisitos de segurança ao gerir centralmente políticas de segurança em todo o ambiente. Ele pode simplesmente e rapidamente implementar uma política que se aplica a todos os respetivos recursos do Azure.

![Política de segurança](./media/contoso-migration-infrastructure/security-policy.png)

#### <a name="assess-and-action"></a>Avaliar e ação

Contoso irá tirar partido da avaliação de segurança contínua que monitoriza a segurança de máquinas, redes, armazenamento, dados e aplicativos; para detetar potenciais problemas de segurança. 

- Centro de segurança irá analisar o estado de segurança de computação, a infraestrutura e os recursos de dados da Contoso e de aplicações do Azure e serviços.
- A avaliação contínua ajuda a equipe de operações da Contoso para detetar potenciais problemas de segurança, como sistemas com atualizações de segurança em falta ou expostos portas de rede. 
- Em particular, Contoso quer certificar-se de que todas as VMs estão protegidas. Centro de segurança ajuda com esta opção, verificar o estado de funcionamento da VM e a apresentação de recomendações prioritárias e acionáveis para remediar vulnerabilidades de segurança antes de que estão a ser exploradas.

![Monitorização](./media/contoso-migration-infrastructure/monitoring.png)

### <a name="work-with-nsgs"></a>Trabalhar com NSGs

Contoso pode limitar o tráfego de rede a recursos numa rede virtual com grupos de segurança de rede.

- Os grupos de segurança de rede contêm uma lista das regras de segurança que permitem ou recusam o tráfego de rede de entrada ou saída com base no endereço IP de destino, na porta e no protocolo.
- Quando aplicado a uma sub-rede, as regras são aplicadas a todos os recursos na sub-rede. Para além das interfaces de rede, isto inclui a instâncias dos serviços do Azure implementadas na sub-rede.
- Grupos de segurança de aplicação (ASGs) permitem-lhe configurar a segurança de rede como uma extensão natural de uma estrutura de aplicação, permitindo que para agrupar VMs e definir políticas de segurança de rede com base nesses grupos.
    - Grupos de segurança de aplicação significam que a Contoso pode reutilizar a política de segurança em escala, sem manutenção manual de endereços IP explícitos. A plataforma lida com a complexidade dos endereços IP explícitos e dos múltiplos conjuntos de regras, permitindo-lhe focar-se na lógica de negócio.
    - Contoso pode especificar um grupo de segurança de aplicações como a origem e destino numa regra de segurança. Depois de uma política de segurança está definida, Contoso pode criar VMs e atribuir os NICs de VM a um grupo. 


Contoso implementará uma combinação de NSGs e ASGs. Contoso está preocupado com a gestão de NSG. Ele é também preocupado com o uso excessivo de NSGs e a complexidade adicional para a equipe de operações. Eis o que fará Contoso:

- Todo o tráfego de e para todas as sub-redes (Norte-Sul), estará sujeito a uma regra NSG, exceto o GatewaySubnets nas redes de Hub.
- Quaisquer firewalls ou controlador de domínio será protegido pelo NSGs de sub-rede e NSGs de NIC.
- Todas as aplicações de produção terão ASGs aplicadas.


Contoso criou um modelo de como isso ficarão para seus aplicativos.

![Segurança](./media/contoso-migration-infrastructure/asg.png)


Os NSGs associados com os ASGs serão configurados com privilégios mínimos para se certificar de que só é permitido pacotes podem circular de uma parte da rede para o destino.

**Ação** | **Nome** | **Origem** | **Target** | **Porta**
--- | --- | --- | --- | --- 
Permitir | AllowiInternetToFE | VNET-HUB-EUS1/IB-TrustZone | APP1-FE 80, 443
Permitir | AllowWebToApp | APP1-FE | APP1-DB | 1433
Permitir | AllowAppToDB | APP1-APP | Qualquer | Qualquer
Negar | DenyAllInbound | Qualquer | Qualquer | Qualquer

### <a name="encrypt-data"></a>Encriptar dados

O Azure Disk Encryption integra-se com o Azure Key Vault para ajudar a controlar e gerir as chaves de encriptação de disco e segredos numa subscrição de Cofre de chaves. Ele garante que todos os dados em discos VM são encriptados em inatividade no armazenamento do Azure.  

- Contoso determinou que VMs específicas exigem encriptação.
- Contoso aplicará a encriptação para as VMs com o cliente, confidencial, ou dados PPI.


## <a name="conclusion"></a>Conclusão

Neste artigo, a Contoso configurar uma infraestrutura do Azure e a política para a subscrição do Azure e híbrido identificar, recuperação após desastre, redes, governação e segurança. 

Nem todos os passos que Contoso efetuar aqui são necessários para uma migração para a cloud. Neste caso, ele queria planear uma infraestrutura de rede que pode ser utilizada para todos os tipos de migrações e é segura, flexível e escalável. 

Com esta infraestrutura no local, a Contoso está pronta para avançar e experimentar a migração.

## <a name="next-steps"></a>Passos Seguintes

Como um primeiro cenário de migração, Contoso vai [avaliar a aplicação de duas camadas da SmartHotel360 no local para migração para o Azure](contoso-migration-assessment.md). 
