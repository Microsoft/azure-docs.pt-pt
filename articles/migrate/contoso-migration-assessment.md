---
title: Avaliar cargas de trabalho no local para a migração da Contoso para o Azure | Documentos da Microsoft
description: Saiba como o Contoso avalia as suas máquinas no local para migração para o Azure com o Azure Migrate e o Assistente de migração de dados.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: raynew
ms.openlocfilehash: df3ae6fbeceb85df6d6e5245a5bbad517179733c
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56991364"
---
# <a name="contoso-migration-assess-on-premises-workloads-for-migration-to-azure"></a>Migração de Contoso: Avaliar cargas de trabalho no local para migração no Azure

Neste artigo, a Contoso avalia seu aplicativo da SmartHotel360 de no local para migração para o Azure.

Este artigo faz parte de uma série que documenta como a empresa fictícia Contoso migra os respetivos recursos no local para a cloud do Microsoft Azure. A série contém informações gerais e cenários de implementação detalhados que mostram como configurar uma infraestrutura de migração, avaliar a adequação de recursos no local para migração e executar diferentes tipos de migrações. Cenários de crescem em complexidade. Artigos serão adicionados à série ao longo do tempo.

Artigo | Detalhes | Estado
--- | --- | ---
[Artigo 1: Descrição geral](contoso-migration-overview.md) | Descrição geral da série de artigos, estratégia de migração da Contoso e as aplicações de exemplo que são utilizadas da série. | Disponível
[Artigo 2: Implementar uma infraestrutura do Azure](contoso-migration-infrastructure.md) | Contoso prepara a sua infraestrutura no local e a respetiva infraestrutura do Azure para a migração. A mesma infra-estrutura é utilizada para todos os artigos da série. | Disponível
Artigo 3: Avalie os recursos no local para migração para o Azure | Contoso é executada uma avaliação da sua aplicação da SmartHotel360 no local em execução no VMware. Contoso avalia as VMs de aplicação com o serviço Azure Migrate e a base de dados de SQL Server do aplicativo com o Assistente de migração de dados. | Este artigo
[Artigo 4: Realojar a uma aplicação numa VM do Azure e a instância gerida da base de dados do SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso é executada uma migração lift-and-shift para o Azure para a sua aplicação da SmartHotel360 no local. Ele migra a aplicação de front-end com o serviço Azure Site Recovery. Ele migra a base de dados de aplicação para uma instância do Azure SQL da base de dados geridos com o serviço de migração de base de dados do Azure. | Disponível
[5 do artigo: Realojar a uma aplicação em VMs do Azure](contoso-migration-rehost-vm.md) | Contoso migra a sua aplicação da SmartHotel360 VMs para VMs do Azure com o serviço Site Recovery. | Disponível
[Artigo 6: Realojar a uma aplicação em VMs do Azure e, num grupo de Disponibilidade AlwaysOn do SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migra a aplicação da SmartHotel360, com o Site Recovery para migrar as VMs de aplicação e o serviço de migração de base de dados para migrar a base de dados de aplicação para um cluster do SQL Server que está protegido por um grupo de Disponibilidade AlwaysOn. | Disponível
[Artigo 7: Realojar uma aplicação do Linux em VMs do Azure](contoso-migration-rehost-linux-vm.md) | Contoso é concluída uma migração lift-and-shift de seu aplicativo de osTicket do Linux para VMs do Azure, com o serviço Site Recovery. | Disponível
[Artigo 8: Realojar uma aplicação do Linux em VMs do Azure e base de dados do Azure para MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migra a sua aplicação de osTicket do Linux para VMs do Azure com o Site Recovery. Ele migra a base de dados de aplicação à base de dados do Azure para MySQL com o MySQL Workbench. | Disponível
[Artigo 9: Refatorizar uma aplicação numa aplicação web do Azure e base de dados do Azure SQL](contoso-migration-refactor-web-app-sql.md) | Contoso migra a sua aplicação da SmartHotel360 para uma aplicação web do Azure e migra a base de dados de aplicação para uma instância de servidor SQL do Azure com o Assistente de migração de base de dados. | Disponível
[Artigo 10: Refatorizar uma aplicação do Linux numa aplicação web do Azure e base de dados do Azure para MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migra sua osTicket de aplicação do Linux para uma aplicação web do Azure em várias regiões do Azure utilizando o Gestor de tráfego do Azure, integrado com o GitHub para a entrega contínua. Contoso migra a base de dados de aplicação para uma base de dados do Azure para a instância do MySQL. | Disponível
[Artigo 11: Refatorar o Team Foundation Server nos serviços de DevOps do Azure](contoso-migration-tfs-vsts.md) | Contoso migra sua implantação do Team Foundation Server no local para serviços do Azure DevOps no Azure. | Disponível
[Artigo 12: Rearquitetar uma aplicação em contentores do Azure e base de dados do Azure SQL](contoso-migration-rearchitect-container-sql.md) | Contoso migra sua SmartHotel de aplicação para o Azure. Em seguida, rearchitects a camada de web de aplicação como um contentor do Windows em execução no Azure Service Fabric e base de dados com a base de dados do Azure SQL. | Disponível
[Artigo 13: Recriar uma aplicação no Azure](contoso-migration-rebuild.md) | Contoso recria o seu aplicativo de SmartHotel com uma variedade de capacidades do Azure e serviços, incluindo o serviço de aplicações do Azure, Azure Kubernetes Service (AKS), as funções do Azure, serviços cognitivos do Azure e Azure Cosmos DB. | Disponível
[Artigo 14: Dimensionar uma migração para o Azure](contoso-migration-scale.md) | Depois de a experimentar combinações de migração, o Contoso se prepara para dimensionar para uma migração completa para o Azure. | Disponível


## <a name="overview"></a>Descrição geral

Como a Contoso considera a migrar para o Azure, a empresa quer realizar uma avaliação técnica e financeira para determinar se as suas cargas de trabalho no local são adequadas para migração para a cloud. Em particular, a equipe de Contoso quer avaliar a compatibilidade de máquina e da base de dados para a migração. Quer estimar a capacidade e custos para executar a recursos da Contoso no Azure.

Para começar a utilizar e para compreender melhor as tecnologias envolvidas, a Contoso avalia dois dos seus locais aplicações, resumidas na tabela seguinte. A empresa avalia para cenários de migração que aplicações de realojamento e o refactor para migração. Saiba mais sobre o realojamento e refatoração no [descrição geral da migração da Contoso](contoso-migration-overview.md).

Nome da aplicação | Plataforma | Escalões de aplicação | Detalhes
--- | --- | --- | ---
SmartHotel360<br/><br/> (gerencia os requisitos de viagens da Contoso) | É executado no Windows com uma base de dados do SQL Server | Aplicação em duas camadas. O front-end Web site do ASP.NET é executado numa VM (**WEBVM**) e o SQL Server é executado em outra VM (**SQLVM**). | As VMs são VMware, em execução num anfitrião ESXi gerido pelo vCenter Server.<br/><br/> Pode transferir a aplicação de exemplo da [GitHub](https://github.com/Microsoft/SmartHotel360).
osTicket<br/><br/> (Aplicativo de suporte técnico do serviço de Contoso) | É executada no Linux/Apache com o MySQL PHP (LAMP) | Aplicação em duas camadas. Um front-end Web site em PHP é executado numa VM (**OSTICKETWEB**) e a base de dados do MySQL é executado em outra VM (**OSTICKETMYSQL**). | A aplicação é utilizada pelo cliente do serviço de aplicações para controlar problemas para os funcionários internos e clientes externos.<br/><br/> Pode baixar o exemplo a partir [GitHub](https://github.com/osTicket/osTicket).

## <a name="current-architecture"></a>Arquitetura atual

Este diagrama mostra a infra-estrutura de locais de Contoso atual:

![Arquitetura de Contoso atual](./media/contoso-migration-assessment/contoso-architecture.png)  

- A Contoso tiver um datacenter principal. O Centro de dados está localizado na cidade de Nova Iorque no Leste dos Estados Unidos.
- A Contoso tiver três ramos locais adicionais nos Estados Unidos.
- O datacenter principal está ligado à internet com uma ligação de Ethernet do Metro (500 MBps) de fibra.
- Cada ramificação é localmente ligada à internet através de ligações de classe empresarial com túneis de IPsec VPN para o datacenter principal. A configuração permite que toda a rede da Contoso permanentemente estar conectados e otimiza a conectividade à internet.
- O Centro de dados principal é totalmente virtualizado com o VMware. A Contoso tiver dois anfitriões de Virtualização de ESXi 6.5 que são geridos pelo vCenter Server 6.5.
- A Contoso utiliza o Active Directory para gestão de identidades. A Contoso utiliza servidores DNS na rede interna.
- Executam os controladores de domínio no Centro de dados em VMs de VMware. Executam os controladores de domínio em ramos locais em servidores físicos.

## <a name="business-drivers"></a>Fatores comerciais

Da Contoso equipa de liderança de TI tem trabalhado em estreita colaboração com parceiros de negócios da empresa para compreender o que a empresa quer alcançar com esta migração:

- **Resolver o crescimento do negócio**: Contoso está a crescer. Como resultado, pressão aumentou em sistemas no local e em infra-estrutura da empresa.
- **Aumentar a eficiência**: A Contoso precisa de remover procedimentos desnecessários e simplificar processos para os desenvolvedores e usuários. As necessidades de negócio IT para ser rápido e de não desperdício de tempo ou dinheiro, portanto, a empresa podem apresentar mais rapidamente sobre os requisitos dos clientes.
- **Aumente a agilidade**:  Contoso TI tem de ser mais reativos para com as necessidades do negócio. Tem de ser capaz de reagir mais rapidamente do que as alterações que ocorrem no mercado da empresa para ser bem-sucedido numa economia global. IT na Contoso não tem de obter na forma como ou se tornar um bloqueador de negócios.
- **Dimensionamento**: Como que a empresa o negócio cresce com êxito, TI da Contoso tem de fornecer sistemas que podem aumentar ao ritmo mesmo.

## <a name="assessment-goals"></a>Objetivos de avaliação

A equipe de cloud Contoso identificou os objetivos de suas avaliações de migração:

- Após a migração, os aplicativos no Azure devem ter as mesmas capacidades de desempenho que aplicações têm atualmente no ambiente de VMWare no local da Contoso. Mover para a nuvem não significa que o desempenho de aplicações é menos crítico.
- A Contoso precisa de compreender a compatibilidade dos seus aplicativos e bancos de dados com os requisitos do Azure. A Contoso também precisa de compreender as opções de alojamento no Azure.
- Administração de banco de dados da Contoso deve ser minimizada após a movimentação de aplicações para a cloud.  
- Contoso quer compreender não apenas as opções de migração, mas também os custos associados com a infraestrutura, depois, é movido para a cloud.

## <a name="assessment-tools"></a>Ferramentas de avaliação

A Contoso utiliza as ferramentas da Microsoft para sua avaliação da migração. As ferramentas alinham aos objetivos da empresa e devem fornecer Contoso com todas as informações necessárias.

Tecnologia | Descrição | Custo
--- | --- | ---
[Assistente de Migração de Dados](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Contoso utiliza o Assistente de migração de dados para avaliar e detetar problemas de compatibilidade que possam afetar a sua funcionalidade de base de dados no Azure. Assistente de migração de dados, avalia paridade de funcionalidades entre o SQL origens e destinos. Ele recomenda melhorias de desempenho e confiabilidade. | Assistente de migração de dados é uma ferramenta gratuita e transferível.
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | A Contoso utiliza o serviço Azure Migrate para avaliar as respetivas VMs de VMware. O Azure Migrate avalia a adequabilidade de migração das máquinas. Ele fornece estimativas de dimensionamento e custos para executar no Azure.  | A partir de Maio de 2018, o Azure Migrate é um serviço gratuito.
[Mapa do Serviço](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | O Azure Migrate utiliza o mapa de serviço para mostrar as dependências entre as máquinas que a empresa quer migrar. | Mapa de serviço faz parte de registos do Azure Monitor. Atualmente, a Contoso pode utilizar o mapa de serviço por 180 dias sem incorrer em custos.

Neste cenário, a Contoso transfere e executa Data Migration Assistant para avaliar a base de dados do SQL Server no local para a sua aplicação de viagens. A Contoso utiliza o Azure Migrate com o mapeamento da dependência para avaliar as VMs da aplicação antes da migração para o Azure.

## <a name="assessment-architecture"></a>Arquitetura da avaliação

![A arquitetura da avaliação da migração](./media/contoso-migration-assessment/migration-assessment-architecture.png)

- Contoso é um nome fictício que representa uma organização empresarial típica.
- A Contoso tiver um datacenter no local (**contoso-datacenter**) e os controladores de domínio (**CONTOSODC1**, **CONTOSODC2**).
- VMware VMs estão localizadas em anfitriões do VMware ESXi a executar a versão 6.5 (**contosohost1**, **contosohost2**).
- O ambiente do VMware é gerido pelo vCenter Server 6.5 (**vcenter.contoso.com**, em execução numa VM).
- A aplicação de viagens da SmartHotel360 tem estas características:
    - A aplicação está em camadas em duas VMs de VMware (**WEBVM** e **SQLVM**).
    - As VMs estão localizadas no anfitrião ESXi do VMware **contosohost1.contoso.com**.
    - As VMs estão em execução o Centro de dados do Windows Server 2008 R2 com SP1.
- O ambiente do VMware é gerido pelo vCenter Server (**vcenter.contoso.com**) em execução numa VM.
- A aplicação de suporte técnico do serviço de osTicket:
    - A aplicação está em camadas em duas VMs (**OSTICKETWEB** e **OSTICKETMYSQL**).
    - As VMs estão a executar o servidor do Linux Ubuntu 16.04-LTS.
    - **OSTICKETWEB** executa o PHP 7.0 e o Apache 2.
    - **OSTICKETMYSQL** está em execução MySQL 5.7.22.

## <a name="prerequisites"></a>Pré-requisitos

Contoso e outros utilizadores têm de cumprir os seguintes pré-requisitos para a avaliação:

- Permissões de proprietário ou contribuinte para a subscrição do Azure ou para um grupo de recursos na subscrição do Azure.
- Uma instância de servidor de vCenter no local a executar a versão 5.5, 6.0 ou 6.5.
- Uma conta só de leitura no vCenter Server ou permissões para criar uma.
- Permissões para criar uma VM na instância do vCenter Server com um modelo. ova.
- Pelo menos um anfitrião ESXi a executar a versão 5.5 ou posterior.
- Pelo menos, duas VMs do VMware no local, uma a executar uma base de dados do SQL Server.
- Permissões para instalar agentes do Azure Migrate em cada VM.
- As VMs devem ter conectividade à Internet direta.  
    - Pode restringir o acesso à internet para o [URLs necessários](https://docs.microsoft.com/azure/migrate/concepts-collector).  
    - Se as suas VMs não tem ligação à internet, o Azure [Log Analytics Gateway](../azure-monitor/platform/gateway.md) tem de estar instalado nos mesmos e o tráfego de agente direcionado através do mesmo.
- Do FQDN da VM a executar a instância do SQL Server, para avaliação da base de dados.
- Firewall do Windows em execução na VM do SQL Server deve permitir ligações externas na porta TCP 1433 (predefinição). Esta configuração permite que dados Migration Assistant para se ligar.

## <a name="assessment-overview"></a>Descrição geral da avaliação

Eis como Contoso realiza a sua avaliação:

> [!div class="checklist"]
> * **Passo 1: Baixe e instale o Assistente de migração de dados**: Contoso prepara o Assistente de migração de dados para a avaliação da base de dados do SQL Server no local.
> * **Passo 2: Avaliar a base de dados ao utilizar o Assistente de migração de dados**: Contoso é executado e analisa a avaliação da base de dados.
> * **Passo 3: Preparar a avaliação da VM com o Azure Migrate**: Contoso configura contas no local e ajusta as definições de VMware.
> * **Passo 4: Detetar VMs no local com o Azure Migrate**: Contoso cria uma VM do recoletor do Azure Migrate. Em seguida, a Contoso é executado o recoletor para detetar VMs para avaliação.
> * **Passo 5: Preparar para a análise de dependência com o Azure Migrate**: Contoso instala os agentes do Azure Migrate nas VMs, para que a empresa possa ver o mapeamento das dependências entre as VMs.
> * **Passo 6: Avaliar as VMs com o Azure Migrate**: Contoso verifica as dependências, grupos de VMs e executa a avaliação. Quando a avaliação estiver pronta, Contoso analisa a avaliação de preparação para a migração.

## <a name="step-1-download-and-install-data-migration-assistant"></a>Passo 1: Baixe e instale o Assistente de migração de dados

1. Contoso transfere dados Assistente de migração do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595).
    - Assistente de migração de dados pode ser instalado em qualquer máquina que possam ligar à instância do SQL Server. Contoso não precisa executá-lo na máquina do SQL Server.
    - Assistente de migração de dados não deve ser executado numa máquina de anfitrião do SQL Server.
2. Contoso executa o arquivo de configuração transferido (Downloadmigrationassistant) para iniciar a instalação.
3. Na **concluir** página, seleciona Contoso **iniciar Microsoft Data Migration Assistant** antes de concluir o assistente.

## <a name="step-2-run-and-analyze-the-database-assessment-for-smarthotel360"></a>Passo 2: Executar e analisar a avaliação da base de dados da SmartHotel360

Agora, a Contoso pode executar uma avaliação para analisar a respetiva base de dados do SQL Server no local para a aplicação da SmartHotel360.

1. No Data Migration Assistant, Contoso seleciona **New** > **avaliação**e, em seguida, atribui a avaliação de um nome de projeto.
2. Para **tipo de servidor de origem**, seleciona Contoso **SQL Server em Azure Virtual Machines**.

    ![Assistente de migração de dados - selecionar origem](./media/contoso-migration-assessment/dma-assessment-1.png)

    > [!NOTE]
      Atualmente, Assistente de migração de dados não suporta a avaliação para migrar para uma instância de gerida de base de dados do Azure SQL. Como solução, Contoso utiliza o SQL Server numa VM do Azure como suposto destino para a avaliação.

3. Na **versão de destino selecione**, Contoso seleciona o SQL Server 2017 como a versão de destino. A Contoso precisa de selecionar esta versão, pois é a versão utilizada pela instância gerida do SQL da base de dados.
4. Contoso seleciona relatórios para ajudá-lo a detetar informações sobre a compatibilidade e as novas funcionalidades:
    - **Problemas de compatibilidade** tenha em atenção as alterações que podem interromper a migração ou que exigem um pequeno Ajuste antes da migração. Este relatório mantém Contoso informado sobre quaisquer funcionalidades atualmente em utilização que foram preteridas. Os problemas estão organizados por nível de compatibilidade.
    - **Recomendação de novas funcionalidades** notas de novos recursos na plataforma do SQL Server de destino que podem ser utilizados para a base de dados após a migração. Novas recomendações de funcionalidades são organizadas de acordo com os cabeçalhos **desempenho**, **Security**, e **armazenamento**.

    ![Assistente de migração de dados - problemas de compatibilidade e as novas funcionalidades](./media/contoso-migration-assessment/dma-assessment-2.png)

2. Na **ligar a um servidor**, Contoso introduz o nome da VM que está a executar a base de dados e as credenciais para aceder ao mesmo. Contoso seleciona **certificado de servidor fidedigno** para se certificar de que a VM pode acessar o SQL Server. Em seguida, seleciona a Contoso **Connect**.

    ![O Assistente de migração de dados - ligar a um servidor](./media/contoso-migration-assessment/dma-assessment-3.png)

3. Na **Adicionar origem**, Contoso adiciona a base de dados que deseja avaliar e, em seguida, seleciona **próxima** para iniciar a avaliação.
4. A avaliação é criada.

    ![O Assistente de migração de dados - Criar avaliação](./media/contoso-migration-assessment/dma-assessment-4.png)

5. Na **resultados de revisão**, Contoso visualiza os resultados da avaliação.

### <a name="analyze-the-database-assessment"></a>Analisar a avaliação da base de dados

Os resultados são apresentados quando eles estão disponíveis. Se a Contoso corrige problemas, ele tem de selecionar **reiniciar avaliação** voltar a executar a avaliação.

1. Na **problemas de compatibilidade** comunicar, Contoso verifica a existência de quaisquer problemas em cada nível de compatibilidade. Os níveis de compatibilidade são mapeados para as versões do SQL Server da seguinte forma:

    - 100: Base de dados do SQL Server 2008/Azure SQL
    - 110: Base de dados do SQL Server 2012/Azure SQL
    - 120: Base de dados do SQL Server 2014/Azure SQL
    - 130: Base de dados do SQL Server 2016/Azure SQL
    - 140: Base de dados do SQL Server 2017/Azure SQL

    ![O Assistente de migração de dados - relatório de problemas de compatibilidade](./media/contoso-migration-assessment/dma-assessment-5.png)

2. Na **recomendações de funcionalidades** comunicar, a Contoso considera a funcionalidades de desempenho, segurança e armazenamento que a avaliação lhe recomenda após a migração. São recomendadas uma variedade de funcionalidades, incluindo OLTP na memória, os índices columnstore, Stretch Database, Always Encrypted, máscara de dados dinâmicos e encriptação de dados transparente.

    ![Assistente de migração de dados - relatório de recomendações de funcionalidade](./media/contoso-migration-assessment/dma-assessment-6.png)

    > [!NOTE]
    > Contoso deverá [ativar a encriptação de dados transparente](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) para todas as bases de dados do SQL Server. Isso é ainda mais crítico quando uma base de dados estiver na nuvem do que quando ela é hospedada no local. Encriptação de dados transparente deve ser ativada apenas após a migração. Se já estiver ativada a encriptação de dados transparente, Contoso tem de mover o certificado ou chave assimétrica para a base de dados mestra do servidor de destino. Saiba como [mover uma base de dados protegido por encriptação de dados transparente para outra instância do SQL Server](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).

2. Contoso pode exportar a avaliação no formato JSON ou CSV.

> [!NOTE]
> Para obter avaliações de larga escala:
> - Executar várias avaliações em simultâneo e ver o estado das mesmas na **todas as avaliações** página.
> - Consolidar avaliações numa [base de dados do SQL Server](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017).
> - Consolidar avaliações numa [relatório do Power BI](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).

## <a name="step-3-prepare-for-vm-assessment-by-using-azure-migrate"></a>Passo 3: Preparar a avaliação da VM com o Azure Migrate

A Contoso precisa de criar uma conta do Mvware que o Azure Migrate pode utilizar para detetar automaticamente as VMs para avaliação, verifique se os direitos para criar uma VM, tenha em atenção as portas que têm de ser abertas e definir o nível de definições de estatísticas.

### <a name="set-up-a-vmware-account"></a>Configurar uma conta do VMware

Deteção de VMS exige uma conta só de leitura no vCenter Server que tem as seguintes propriedades:

- **Tipo de utilizador**: Pelo menos um utilizador só de leitura.
- **Permissões**: Para o objeto de datacenter, selecione o **propagar a objetos filho** caixa de verificação. Para **função**, selecione **só de leitura**.
- **Detalhes**: O utilizador está atribuído ao nível do datacenter, com acesso a todos os objetos no Centro de dados.
- Para restringir o acesso, atribua a **sem acesso** função com o **propagar ao filho** objeto aos objetos subordinados (anfitriões vSphere, arquivos de dados, VMs e redes).

### <a name="verify-permissions-to-create-a-vm"></a>Verificar as permissões para criar VMs

Verifica se a Contoso, que tem permissões para criar uma VM ao importar um ficheiro no formato. ova. Saiba como [criar e atribuir uma função com privilégios](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>Verificar as portas

A avaliação de Contoso utiliza o mapeamento da dependência. Mapeamento de dependência requer um agente ser instalado nas VMs que serão avaliadas. O agente tem de conseguir ligar ao Azure a partir da porta TCP 443 em cada VM. Saiba mais sobre [requisitos de conexão](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid).

### <a name="set-statistics-settings"></a>Configurar as definições das estatísticas

Antes de Contoso começa a implementação, ele tem de definir as definições de estatísticas para o vCenter Server para o nível 3.

> [!NOTE]
> - Depois de definir o nível, Contoso têm de aguardar pelo menos um dia antes da execução da avaliação. Caso contrário, a avaliação poderá não funcionar conforme esperado.
> - Se o nível for superior a 3, a avaliação funciona, mas:
>    - Não são recolhidos dados de desempenho para discos e da rede.
>    - Quanto ao armazenamento, o Azure Migrate recomenda um disco padrão no Azure, com o mesmo tamanho que o disco no local.
>    - Funcionamento em rede, para cada adaptador de rede no local, recomenda-se um adaptador de rede no Azure.
>    - Para computação, o Azure Migrate analisa os núcleos VM e o tamanho da memória e recomenda uma VM do Azure com a mesma configuração. Se existirem vários tamanhos de VMs do Azure elegíveis, é recomendado aquele que tiver o custo mais baixo.
> - Para obter mais informações sobre o dimensionamento com o nível 3, consulte [dimensionamento](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing).

Para definir o nível:

1. No vSphere Web Client, Contoso, abre-se a instância de servidor do vCenter.
2. Contoso seleciona **Manage** > **definições** > **geral** > **editar**.
3. Na **estatísticas**, Contoso define a estatística definições de nível de como **nível 3**.

    ![nível de estatísticas do servidor vCenter](./media/contoso-migration-assessment/vcenter-statistics-level.png)

## <a name="step-4-discover-vms"></a>Passo 4: Detetar VMs

Para detetar VMs, a Contoso cria um projeto do Azure Migrate. Contoso transfere e configura a VM do recoletor. Em seguida, a Contoso é executado o recoletor para detetar as suas VMs no local.

### <a name="create-a-project"></a>Criar um projeto

1. Na [portal do Azure](https://portal.azure.com), Contoso procura **Azure Migrate**. Em seguida, a Contoso cria um projeto.
2. Contoso Especifica um nome de projeto (**ContosoMigration**) e a subscrição do Azure. É criado um novo grupo de recursos do Azure (**ContosoFailoverRG**).
    > [!NOTE]
    > - Pode criar um projeto do Azure Migrate só no e.u.a. Centro-Oeste ou região E.U.A. Leste.
    > - Pode planear uma migração para qualquer localização de destino.
    > - A localização de projeto é utilizada apenas para armazenar os metadados recolhidos de VMs no local.

    ![Azure Migrate - criar o projeto de migração](./media/contoso-migration-assessment/project-1.png)

### <a name="download-the-collector-appliance"></a>Transferir a aplicação recoletora

O Azure Migrate cria uma VM no local, conhecida como o *aplicação recoletora*. A VM Deteta as VMs de VMware no local e envia os metadados sobre as VMs para o serviço Azure Migrate. Para configurar a aplicação recoletora, Contoso transfere um modelo de OVA e, em seguida, importa para a instância de servidor do vCenter no local para criar a VM.

1. No projeto do Azure Migrate, Contoso seleciona **introdução** > **detetar e avaliar** > **detetar máquinas**. Contoso transfere o ficheiro de modelo OVA.
2. Contoso copia o ID do projeto e a chave. O projeto e o ID são necessários para configurar o recoletor.

    ![O Azure Migrate - transferir o ficheiro OVA](./media/contoso-migration-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Verificar a aplicação recoletora

Antes de implementar a VM, a Contoso verifica que o ficheiro OVA é seguro:

1. No computador no qual o ficheiro foi transferido, Contoso abre uma janela de linha de comandos de administrador.
2. Contoso é executado o seguinte comando para gerar o hash para o ficheiro OVA:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    **Exemplo**

    ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. O hash gerado deve corresponder os valores de hash listados [aqui](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware#continuous-discovery).

### <a name="create-the-collector-appliance"></a>Criar a aplicação recoletora

Agora, a Contoso pode importar o ficheiro transferido para a instância do servidor vCenter e aprovisionar a aplicação recoletora VM:

1. Na consola do vSphere Client, Contoso seleciona **arquivo** > **implementar o modelo OVF**.

    ![vSphere Web Client - modelo implementar OVF](./media/contoso-migration-assessment/vcenter-wizard.png)

2. No Assistente de modelo do OVF implementar, Contoso seleciona **origem**e, em seguida, especifica a localização do ficheiro OVA.
3. Na **nome e localização**, Contoso Especifica um nome a apresentar para a VM do recoletor. Em seguida, seleciona a localização do inventário na qual vai alojar a VM. Contoso também especifica o anfitrião ou cluster para executar a aplicação recoletora.
4. Na **armazenamento**, Contoso Especifica a localização de armazenamento. Na **formato de disco**, Contoso seleciona como quer aprovisionar o armazenamento.
5. Na **mapeamento da rede**, Contoso Especifica a rede no qual pretende ligar-se a VM do recoletor. A rede necessita de conectividade com a internet para enviar metadados para o Azure.
6. Contoso analisa as definições e, em seguida, seleciona **ligar após a implementação** > **concluir**. É apresentada uma mensagem que confirma a conclusão com êxito quando é criada a aplicação.

### <a name="run-the-collector-to-discover-vms"></a>Executar o recoletor para detetar VMs

Agora, a Contoso é executado o recoletor para detetar VMs. Atualmente, o recoletor atualmente suporta apenas **inglês (Estados Unidos)** como o idioma do sistema operativo e o idioma da interface do recoletor.

1. Na consola do vSphere Client, Contoso seleciona **abra a consola**. Contoso Especifica o idioma, fuso horário e preferências de palavra-passe para a VM do recoletor.
2. Na área de trabalho, Contoso seleciona os **executar recoletor** atalho.

    ![consola do vSphere Client - atalho do Recoletor](./media/contoso-migration-assessment/collector-shortcut.png)

3. No Recoletor do Azure Migrate, Contoso seleciona **configurar pré-requisitos**. Contoso aceita os termos de licenciamento e lê as informações de terceiros.
4. O recoletor verifica se a VM tem acesso à internet, que o tempo está sincronizado, e que o serviço do recoletor está em execução. (O serviço do recoletor está instalado por predefinição na VM.) Contoso também instala o VMware PowerCLI.

    > [!NOTE]
    > Pressupõe-se que a VM tem acesso direto à internet sem utilizar um proxy.

    ![O Azure Migrate Recoletor - verificar pré-requisitos](./media/contoso-migration-assessment/collector-verify-prereqs.png)

5. Na **especificar detalhes do vCenter Server**, Contoso introduz o nome (FQDN) ou o endereço IP da instância do vCenter Server e as credenciais só de leitura utilizado para a deteção.
6. Contoso seleciona um âmbito de deteção de VMS. O coletor pode detetar apenas as VMs que estejam no âmbito especificado. O âmbito pode ser definido para uma pasta específica, o Centro de dados ou o cluster. O âmbito não deve conter mais de 1500 VMs.

    ![Especificar detalhes do vCenter Server](./media/contoso-migration-assessment/collector-connect-vcenter.png)

7. Na **especificar projeto de migração**, Contoso entra o Azure Migrate ID do projeto e a chave que foram copiados a partir do portal. Para obter o ID do projeto e a chave, a Contoso pode ir para o projeto **descrição geral** página > **detetar máquinas**.  

    ![Especificar o projeto de migração](./media/contoso-migration-assessment/collector-connect-azure.png)

8. Na **ver o progresso da coleção**, Contoso pode monitorizar a deteção e verifique se os metadados recolhidos das VMs estão no âmbito. O recoletor fornece um período de deteção aproximado.

    ![Ver progresso da recolha](./media/contoso-migration-assessment/collector-collection-process.png)

### <a name="verify-vms-in-the-portal"></a>Verificar as VMs no portal

Quando a coleção estiver concluída, a Contoso verifica se as VMs aparecem no portal do:

1. No projeto do Azure Migrate, Contoso seleciona **Manage** > **máquinas**. Contoso verifica que as VMs que quer detetar são apresentadas.

    ![O Azure Migrate - máquinas detetadas](./media/contoso-migration-assessment/discovery-complete.png)

2. Atualmente, as máquinas não têm os agentes do Azure Migrate instalados. Contoso tem de instalar os agentes para ver as dependências.

    ![O Azure Migrate - é necessária a instalação do agente](./media/contoso-migration-assessment/machines-no-agent.png)

## <a name="step-5-prepare-for-dependency-analysis"></a>Passo 5: Preparar para a análise de dependência

Para ver as dependências entre as VMs que deseja avaliar, Contoso transfere e instala os agentes nas VMs da aplicação. Contoso instala os agentes em todas as VMs para as suas aplicações, tanto para Windows e Linux.

### <a name="take-a-snapshot"></a>Criar um Instantâneo

Para manter uma cópia das VMs antes de modificá-los, a Contoso tira um instantâneo antes dos agentes estiverem instalados.

![Instantâneo da máquina](./media/contoso-migration-assessment/snapshot-vm.png)

### <a name="download-and-install-the-vm-agents"></a>Transferir e instalar os agentes da VM

1. Na **máquinas**, Contoso seleciona a máquina. Na **dependências** Contoso de coluna, seleciona **requer instalação**.
2. Na **detetar máquinas** painel, Contoso:
    - Downloads do Microsoft Monitoring Agent (MMA) e o agente de dependência para cada VM do Windows.
    - Transfere o MMA e o agente de dependência para cada VM do Linux.
3. Contoso copia o ID de área de trabalho e a chave. A Contoso precisa do ID de área de trabalho e a chave de quando instalar o MMA.

    ![Transferência do agente](./media/contoso-migration-assessment/download-agents.png)

### <a name="install-the-agents-on-windows-vms"></a>Instalar os agentes em VMs do Windows

Contoso executa a instalação em cada VM.

#### <a name="install-the-mma-on-windows-vms"></a>Instalar o MMA em VMs do Windows

1. Contoso clica duas vezes no agente transferido.
2. Na **pasta de destino**, Contoso mantém a pasta de instalação predefinida e, em seguida, seleciona **próxima**.
3. Na **opções de configuração do agente**, seleciona Contoso **ligar o agente ao Azure Log Analytics** > **seguinte**.

    ![Configuração do agente - opções de configuração do agente de monitorização do Microsoft](./media/contoso-migration-assessment/mma-install.png)

4. Na **do Azure Log Analytics**, Contoso cola o ID de área de trabalho e a chave que ele copiados a partir do portal.

    ![Microsoft Monitoring Agent configuração - Log Analytics do Azure](./media/contoso-migration-assessment/mma-install2.png)

5. Na **pronto para instalar**, Contoso instala o MMA.

#### <a name="install-the-dependency-agent-on-windows-vms"></a>Instalar o agente de dependência em VMs do Windows

1. Contoso clica duas vezes no agente de dependência transferido.
2. Contoso aceita os termos de licenciamento e aguarda a conclusão da instalação.

    ![Configurar o agente de dependência - instalação](./media/contoso-migration-assessment/dependency-agent.png)

### <a name="install-the-agents-on-linux-vms"></a>Instalar os agentes em VMs do Linux

Contoso executa a instalação em cada VM.

#### <a name="install-the-mma-on-linux-vms"></a>Instalar o MMA em VMs do Linux

1. Contoso instala a biblioteca de ctypes de Python em cada VM com o seguinte comando:

    `sudo apt-get install python-ctypeslib`
2. Contoso tem de executar o comando para instalar o agente MMA como raiz. Para se tornar raiz, a Contoso é executado o comando seguinte e, em seguida, introduz a palavra-passe de raiz:

    `sudo -i`
3. Contoso instala o MMA:
    - Contoso aciona o ID de área de trabalho e a chave no comando.
    - Os comandos estão para 64 bits.
    - O ID de área de trabalho e a chave primária estão localizados na área de trabalho do Log Analytics no portal do Azure. Selecione **configurações**e, em seguida, selecione a **origens ligadas** separador.
    - Execute os seguintes comandos para transferir o agente do Log Analytics, validar a soma de verificação e instalar e carregar o agente:

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w 6b7fcaff-7efb-4356-ae06-516cacf5e25d -s k7gAMAw5Bk8pFVUTZKmk2lG4eUciswzWfYLDTxGcD8pcyc4oT8c6ZRgsMy3MmsQSHuSOcmBUsCjoRiG2x9A8Mg==
    ```

#### <a name="install-the-dependency-agent-on-linux-vms"></a>Instalar o agente de dependência em VMs do Linux

Após a instalação do MMA, a Contoso instala o agente de dependência nas VMs do Linux:

1. O agente de dependência é instalado em computadores com Linux utilizando InstallDependencyAgent-Linux64.bin, um script de shell que tenha um binário auto-extraível. Permissões para o próprio ficheiro de execução de execuções de contoso adiciona o ficheiro utilizando sh ou-lo.

2. Contoso instala o agente de dependência do Linux como raiz:

    ```
    wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin && sudo sh InstallDependencyAgent-Linux64.bin -s
    ```

## <a name="step-6-run-and-analyze-the-vm-assessment"></a>Passo 6: Executar e analisar a avaliação da VM

Contoso agora pode verificar as dependências das máquinas e crie um grupo. Em seguida, ele é executado a avaliação para o grupo.

### <a name="verify-dependencies-and-create-a-group"></a>Verifique as dependências e criar um grupo

1. Para determinar quais computadores para analisar, Contoso seleciona **ver dependências**.

    ![O Azure Migrate - ver dependências de máquina](./media/contoso-migration-assessment/view-machine-dependencies.png)

2. Para SQLVM, o mapa de dependências mostra os seguintes detalhes:

    - Grupos de processo ou processos que têm ligações de rede ativas em execução em SQLVM, durante o período de tempo especificado (uma hora, por predefinição).
    - Ligações TCP de entrada (cliente) e saída (servidor) de e para todas as máquinas dependentes
    - As máquinas dependentes com os agentes do Azure Migrate instalados são apresentadas como caixas separadas.
    - As máquinas que não têm os agentes instalados mostram a porta e informações de endereço IP.

3. Para computadores que têm o agente instalado (WEBVM), a Contoso seleciona a caixa de máquina para ver mais informações. As informações incluem o FQDN, o sistema operativo e o endereço MAC.

    ![O Azure Migrate - ver dependências de grupo](./media/contoso-migration-assessment/sqlvm-dependencies.png)

4. Contoso seleciona as VMs para adicionar ao grupo (SQLVM e WEBVM). A Contoso utiliza Ctrl + clique para selecionar várias VMs.
5. Contoso seleciona **criar grupo**e, em seguida, introduz um nome (**smarthotelapp**).

    > [!NOTE]
    > Para ver dependências mais granulares, pode expandir o intervalo de tempo. Pode selecionar uma duração específica ou selecione as datas de início e de fim.

### <a name="run-an-assessment"></a>Executar uma avaliação

1. No **grupos**, o grupo é aberta a Contoso (**smarthotelapp**) e, em seguida, seleciona **Criar avaliação**.

    ![Azure Migrate - criar uma avaliação](./media/contoso-migration-assessment/run-vm-assessment.png)

2. Para ver a avaliação, Contoso seleciona **Manage** > **avaliações**.

A Contoso utiliza as predefinições de avaliação, mas pode [personalizar definições](how-to-modify-assessment.md).

### <a name="analyze-the-vm-assessment"></a>Analisar a avaliação da VM

Uma avaliação do Azure Migrate incluem informações sobre a compatibilidade no local com o Azure, dimensionamento certo sugerido para VM do Azure e custos do Azure mensais estimados.

![O Azure Migrate - relatório de avaliação](./media/contoso-migration-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Rever a classificação de confiança

![O Azure Migrate - apresentação da avaliação](./media/contoso-migration-assessment/assessment-display.png)

Uma avaliação tem uma classificação de confiança de 1 estrela corresponde a 5 estrelas (1 estrela corresponde é a mais baixa e 5 estrelas é a mais alta).
- A classificação de confiança é atribuída a uma avaliação com base na disponibilidade dos pontos de dados que são necessários para calcular a avaliação.
- A classificação ajuda a calcular a fiabilidade das recomendações de tamanho fornecidas pelo Azure Migrate.
- A classificação de confiança é útil quando estiver fazendo *dimensionamento com base no desempenho*. O Azure Migrate poderá não ter pontos de dados suficientes para o dimensionamento com base na utilização. Para *como no local* de dimensionamento, a classificação de confiança é sempre 5 estrelas porque o Azure Migrate tem todos os pontos de dados que precisa para dimensionar a VM.
- Consoante a percentagem de pontos de dados disponíveis, é fornecida a classificação de confiança para a avaliação:

   Disponibilidade de pontos de dados | Classificação de confiança
   --- | ---
   0%-20% | 1 star
   21%-40% | 2 estrelas
   41%-60% | 3 estrelas
   61%-80% | 4 estrelas
   81%-100% | 5 estrelas

#### <a name="verify-azure-readiness"></a>Verificar a preparação do Azure

![O Azure Migrate - preparação para a avaliação](./media/contoso-migration-assessment/azure-readiness.png)  

O relatório da avaliação mostra as informações que são resumidas na tabela. Para mostrar o dimensionamento com base no desempenho, o Azure Migrate requer as seguintes informações. Se as informações não podem ser recolhidas, avaliação de dimensionamento poderá não ser preciso.

- Dados de utilização da CPU e da memória.
- IOPS de leitura/escrita e débito de cada disco ligado à VM.
- Informações de entrada e saída da rede de cada placa de rede ligada à VM.

Definição | Indicação | Detalhes
--- | --- | ---
**Preparado para a VM do Azure** | Indica se a VM está pronta para migração. | Estados possíveis:</br><br/>- Preparada para o Azure<br/><br/>- Preparada com condições <br/><br/>- Não está preparada para o Azure<br/><br/>- Preparação desconhecida<br/><br/> Se uma VM não estiver preparada, o Azure Migrate mostra alguns passos de remediação.
**Tamanho da VM do Azure** | Para VMs prontas, o Azure Migrate fornece uma recomendação de tamanho de VM do Azure. | A recomendação do tamanho depende das propriedades da avaliação:<br/><br/>- Se tiver utilizado o dimensionamento baseado no desempenho, é considerado o histórico do desempenho das VMs.<br/><br/>-Se tiver utilizado *como no local*, tamanho tem por base o tamanho de VM no local e dados de utilização não são utilizados.
**Ferramenta sugerida** | Porque os agentes estão a executar as máquinas do Azure, Azure Migrate analisa os processos que estão em execução no interior da máquina. Ele identifica se o computador é uma máquina de base de dados.
**Informações da VM** | O relatório mostra as definições para a VM no local, incluindo o sistema operativo, tipo de arranque e informações de disco e armazenamento.

#### <a name="review-monthly-cost-estimates"></a>Rever as estimativas de custo mensal

Esta vista mostra o custo de computação e armazenamento total da execução das VMs no Azure. Ela também mostra detalhes para cada máquina.

![O Azure Migrate - os custos do Azure](./media/contoso-migration-assessment/azure-costs.png)

- Estimativas de custos são calculadas com recomendações de tamanho de uma máquina.
- Os custos mensais estimados para computação e armazenamento são agregados para todas as VMs no grupo.

## <a name="clean-up-after-assessment"></a>Limpar após a avaliação

- Quando concluída a avaliação, a Contoso retém a aplicação do Azure Migrate para utilizar no futuro avaliações.
- Contoso se desligar a VM de VMware. Contoso vai utilizá-lo novamente quando ele avalia VMs adicionais.
- Contoso mantém o **migração da Contoso** projeto no Azure. O projeto está atualmente implementado na **ContosoFailoverRG** grupo de recursos na região do Azure do Leste-nos.
-  A VM do recoletor tem uma licença de avaliação de 180 dias. Se este limite expirar, Contoso terá de transferir o recoletor e configurá-lo novamente.

## <a name="conclusion"></a>Conclusão

Neste cenário, a Contoso avalia seu banco de dados de aplicações da SmartHotel360 utilizando a ferramenta de avaliação de migração de dados. Ele avalia as VMs no local com o serviço Azure Migrate. Contoso analisa as avaliações certificar-se de que recursos estão prontos para a migração para o Azure no local.

## <a name="next-steps"></a>Passos Seguintes

No próximo artigo da série, a Contoso realojar a aplicação da SmartHotel360 no Azure através de uma migração lift-and-shift. Contoso migra o WEBVM front-end da aplicação com o Azure Site Recovery. Ele migra a base de dados de aplicação para uma instância de gerida de base de dados do Azure SQL com o serviço de migração de base de dados. [Introdução ao](contoso-migration-rehost-vm-sql-managed-instance.md) com esta implementação.
