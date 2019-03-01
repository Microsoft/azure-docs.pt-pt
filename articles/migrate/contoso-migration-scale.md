---
title: Contoso-dimensionamento uma migração para o Azure | Documentos da Microsoft
description: Saiba como a Contoso processa uma migração dimensionada para o Azure.
services: azure-migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: raynew
ms.openlocfilehash: bc4673a12ec5b752a513b4a95796f2aeb8b8ce5d
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56991253"
---
# <a name="contoso---scale-a-migration-to-azure"></a>Contoso - dimensionamento uma migração para o Azure

Neste artigo, a Contoso executa uma migração em escala para o Azure. Ao considerar como planejar e executar uma migração de cargas de trabalho mais de 3000, 8000 bases de dados e mais de 10.000 VMs.


Este artigo é um de uma série de artigos que documente como a empresa fictícia Contoso migra os respetivos recursos no local para a cloud do Microsoft Azure. A série contém em segundo plano e informações de planeamento e cenários de implementação que mostram como configurar uma infraestrutura de migração, avaliar a adequação de recursos no local para migração e executam diferentes tipos de migrações. Cenários de crescem em complexidade. Vamos adicionar artigos para a série ao longo do tempo.


**Artigo** | **Detalhes** | **Estado**
--- | --- | ---
[Artigo 1: Descrição geral](contoso-migration-overview.md) | Descrição geral da série de artigos, estratégia de migração da Contoso e as aplicações de exemplo que são utilizadas da série. | Disponível
[Artigo 2: Implementar uma infraestrutura do Azure](contoso-migration-infrastructure.md) | Contoso prepara a sua infraestrutura no local e a respetiva infraestrutura do Azure para a migração. A mesma infra-estrutura é utilizada para todos os artigos de migração da série. | Está disponível.
[Artigo 3: Avalie os recursos no local para migração para o Azure](contoso-migration-assessment.md)  | Contoso é executada uma avaliação da sua aplicação da SmartHotel360 no local em execução no VMware. Contoso avalia as VMs de aplicação com o serviço Azure Migrate e a base de dados de SQL Server do aplicativo com o Assistente de migração de dados. | Disponível
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
Artigo 14: Dimensionar uma migração para o Azure | Depois de a experimentar combinações de migração, o Contoso se prepara para dimensionar para uma migração completa para o Azure. | Este artigo

## <a name="business-drivers"></a>Fatores comerciais

A equipe de liderança de TI tem trabalhado em estreita colaboração com parceiros de negócios para compreender o que quer alcançar com esta migração:

- **Resolver o crescimento do negócio**: Contoso está a crescer, fazendo com que a pressão em sistemas no local e em infra-estrutura.
- **Aumentar a eficiência**: A Contoso precisa de remover procedimentos desnecessários e simplificar processos para desenvolvedores e usuários. Necessidades de negócio IT velocidade e não desperdício de tempo ou dinheiro, entregando assim mais rapidamente sobre os requisitos dos clientes.
- **Aumente a agilidade**: Contoso TI tem de ser mais reativos para com as necessidades do negócio. Tem de ser capaz de reagir mais rapidamente do que as alterações no marketplace, para ativar o sucesso numa economia global. Ele não pode obter na forma como ou se tornar um bloqueador de negócios.
- **Dimensionamento**: À medida que a empresa cresce com êxito, a equipe de TI da Contoso tem de fornecer sistemas que são capazes de aumentar ao ritmo mesmo.
- **Melhorar a modelos de custo**: A Contoso pretende reduzir os requisitos de capitais no orçamento de TI.  Contoso, além de utilizar as capacidades de cloud para aumentar e reduzir a necessidade de hardware dispendiosa.
- **Reduza os custos de licenciamento**: Contoso quer minimizar os custos da cloud.


## <a name="migration-goals"></a>Objetivos de migração

A equipe de cloud de Contoso tiver afixado para baixo de objetivos para essa migração. Essas metas foram utilizadas para determinar o melhor método de migração.

**Requisitos** | **Detalhes**
--- | --- 
**Mover para o Azure, o mais rapidamente** | Contoso quer mover as VMs e aplicações para o Azure o mais rapidamente possível.
**Compilar um inventário completo** | Contoso, além de um inventário completo de todas as aplicações, bases de dados e VMs na organização.
**Avaliar e classificar aplicações** | Contoso quer totalmente tirar partido da cloud. Como uma predefinição Contoso parte do princípio de que todos os serviços serão executado como PaaS. IaaS será utilizado em que a PaaS não é apropriada. 
**Dar formação e mover para DevOps** | Contoso quer mudar para um modelo de DevOps. Contoso irá fornecer formação do Azure e de DevOps e reorganizar as equipes conforme necessário. 


Depois de fixar-se para baixo de objetivos e requisitos, Contoso revê os requisitos de espaço IT e identifica o processo de migração.

## <a name="current-deployment"></a>Implementação atual

Depois de planear e configurar uma [infraestrutura do Azure](contoso-migration-infrastructure.md) e experimentar combinações de migração do prova de conceito (POC) diferentes conforme detalhado na tabela acima, a Contoso está pronto para começar uma migração completa para o Azure em escala. Eis o que a Contoso pretende migrar.

**Item** | **Volume** | **Detalhes**
--- | --- | ---
**Cargas de trabalho** | Mais de 3.000 aplicações | Aplicações executadas nas VMs.<br/><br/>  As aplicações são Windows, baseado em SQL e sistemas operacionais LAMP.
**Bases de dados** | Cerca de 8,500 | Bases de dados incluem o SQL Server, MySQL, PostgreSQL.
**VMs** | Mais de 10 000 | As VMs executadas em anfitriões do VMware e gerido pelo vCenter servidores.


## <a name="migration-process"></a>Processo de migração

Agora que a Contoso tiver afixado fatores comerciais e os objetivos de migração, ele determina uma abordagem de quatro conceitos para o processo de migração:

- **Fase 1-avaliar**: Descubra os ativos atuais e descobrir se é adequadas para migração para o Azure.
- **Fase 2-migrar**: Mova os ativos para o Azure. Como eles mova as aplicações e a aplicação serão dependem de objetos para o Azure e o que quer alcançar.
- **Fase 3-otimizar**: Depois de mover recursos para o Azure, a Contoso precisa para melhorar e simplificá-los para o desempenho máximo e eficiência.
- **Fase 4-proteger e gerir**: Com tudo instalado, a Contoso agora utiliza serviços e recursos de gerenciamento e segurança do Azure para governar, proteger e monitorizar as suas aplicações na cloud no Azure.


Essas fases não são seriais em toda a organização. Cada parte do projeto de migração da Contoso estará num estágio diferente do processo de avaliação e migração. Otimização, segurança e gestão será contínuas ao longo do tempo.


## <a name="phase-1-assess"></a>Fase 1: Avaliar

Contoso inicia o processo ao detetar e avaliar aplicações no local, dados e infraestrutura. Eis o que fará Contoso:

- Contoso tem de detetar aplicações, mapeia as dependências entre aplicações e decidir qual a ordem de migração e a prioridade.
- Como Contoso avalia, ele irá criar um inventário abrangente de aplicações e recursos. Juntamente com o inventário de novo, Contoso irá utilizar e atualizar a base de dados existente de gerenciamento de configuração (CMDB) e o catálogo de serviços.
    - O CMDB contém configurações técnicas para aplicações da Contoso.
    - O catálogo de serviços documenta os detalhes operacionais de aplicações, incluindo parceiros de negócios associados e contratos de nível de serviço (SLAs)

### <a name="discover-apps"></a>Descobrir aplicações

Contoso é executado milhares de aplicações numa variedade de servidores. Além do CMDB e o catálogo de serviços, a Contoso precisa de ferramentas de deteção e avaliação. 

- As ferramentas tem de fornecer um mecanismo que possa fornecer dados de avaliação para o processo de migração.
- Ferramentas de avaliação tem de fornecer dados que ajudam a criar um inventário inteligente de recursos de físicos e virtuais da Contoso. Os dados devem incluir informações de perfil e métricas de desempenho.
- Quando a deteção esteja concluída, a Contoso deve ter um inventário completo dos recursos e metadados associados a eles. Esse inventário será utilizado para definir o plano de migração.

### <a name="identify-classifications"></a>Identificar classificações

Contoso identifica algumas categorias comuns para classificar a ativos no inventário. Estas classificações são fundamentais para tomada para a migração da Contoso. A lista de classificação ajuda a estabelecer prioridades de migração e identificar problemas complexos.

**Categoria** | **Valor atribuído** | **Detalhes**
--- | --- | ---
Grupo de negócio | Lista de nomes de grupos de negócios | O grupo a que é responsável pelo item de inventário?
Candidato a prova de conceito | Y/N | Pode a aplicação ser utilizada como uma prova de conceito ou uma das primeiras a adotar para migração para a cloud?
Dívida técnica | Nenhum/alguns/graves | O item de inventário está em execução ou através de um produto de fora de suporte, plataforma ou sistema operativo?
Implicações de firewall | Y/N | A aplicação comunicar com a Internet/fora de tráfego?  Ela é integrada com um firewall?
Problemas de segurança | Y/N | Se os problemas de segurança com a aplicação lá conhecidos?  A aplicação utilizar dados não encriptados ou plataformas Desatualizadas?


### <a name="discover-app-dependencies"></a>Detetar dependências de aplicações

Como parte do processo de avaliação, a Contoso precisa identificar onde as aplicações são executadas e descobrir as dependências e as ligações entre servidores de aplicações. Contoso mapeia o ambiente em etapas.

1. Como primeiro passo, a Contoso Deteta como servidores e máquinas mapeiam para aplicações individuais, grupos e localizações de rede.
2. Com essas informações, Contoso pode identificar aplicações que têm algumas dependências e, portanto, são adequados para uma migração rápida.
3. Contoso pode usar mapeamento para os ajudar a identificar mais complexas dependências e comunicações entre servidores de aplicações. Contoso, em seguida, pode agrupar esses servidores logicamente para representar a aplicações e planejar uma estratégia de migração com base nestes grupos.


Com o mapeamento foi concluído, a Contoso pode garantir que todos os componentes de aplicação estão identificados e considerados ao criar o plano de migração. 

![Mapeamento de dependências](./media/contoso-migration-scale/dependency-map.png)


### <a name="evaluate-apps"></a>Avaliar aplicações

Como última etapa no processo de deteção e avaliação, a Contoso pode avaliar os resultados de avaliação e o mapeamento para saber como migrar cada aplicação no catálogo de serviços. 

Para capturar esse processo de avaliação, eles adicionar duas classificações adicionais para o inventário.

**Categoria** | **Valor atribuído** | **Detalhes**
--- | --- | ---
Grupo de negócio | Lista de nomes de grupos de negócios | O grupo a que é responsável pelo item de inventário?
Candidato a prova de conceito | Y/N | Pode a aplicação ser utilizada como uma prova de conceito ou uma das primeiras a adotar para migração para a cloud?
Dívida técnica | Nenhum/alguns/graves | O item de inventário está em execução ou através de um produto de fora de suporte, plataforma ou sistema operativo?
Implicações de firewall | Y/N | A aplicação comunicar com a Internet/fora de tráfego?  Ela é integrada com um firewall?
Problemas de segurança | Y/N | Se os problemas de segurança com a aplicação lá conhecidos?  A aplicação utilizar dados não encriptados ou plataformas Desatualizadas?
Estratégia de migração | Rehost/Refactor/Rearchitect/Rebuild | Que tipo de migração é necessário para a aplicação? Como será a aplicação ser implementada no Azure? [Saiba mais](contoso-migration-overview.md#migration-strategies).
Complexidade técnica | 1-5 | Complexidade é a migração? Este valor deve ser definido por DevOps da Contoso e parceiros relevantes.
Nível de importância de negócios | 1-5 | Quão importante é a aplicação para a empresa? Por exemplo, uma pequeno grupo de trabalho aplicação pode ser atribuída uma pontuação igual a um, enquanto um aplicativo crítico utilizado em toda a organização possa ser atribuído uma pontuação igual a cinco. Esta pontuação terá impacto sobre o nível de prioridade de migração.
Prioridade de migração | 1/2/3 | O que a migração prioridade para a aplicação?
Risco de migração  | 1-5 | O que é o nível de risco para migrar a aplicação? Este valor deve ser combinado entre Contoso DevOps e parceiros relevantes.




### <a name="figure-out-costs"></a>Descobrir os custos

Para descobrir os custos e a economia potencial de migração do Azure, a Contoso pode utilizar o [Calculadora de TCO Custo Total de propriedade ()](https://azure.microsoft.com/pricing/tco/calculator/) para calcular e comparar o custo total de propriedade para o Azure para uma implementação de comparável no local.

### <a name="identify-assessment-tools"></a>Identificar as ferramentas de avaliação

Contoso decide qual ferramenta a utilizar para a deteção, avaliação e criar o inventário. Contoso identifica uma combinação de ferramentas do Azure e serviços, ferramentas de aplicação nativa e scripts e ferramentas de parceiros. Em particular, Contoso quer saber como migrar do Azure pode ser utilizado para avaliar em escala.


#### <a name="azure-migrate"></a>Azure Migrate


O serviço Azure Migrate ajuda-o a detetar e avaliar VMs de VMware no local, em preparação para a migração para o Azure. Eis o que o Azure Migrate faz:

1. Detete: Detete VMs de VMware no local.
    - O Azure Migrate suporta a deteção do vCenter vários servidores (em série) e pode executar deteções em projetos separados do Azure Migrate.
    - O Azure Migrate efetua a descoberta por meio de uma VM de VMware com coletor de migrar. O mesmo recoletor pode detetar VMs no vCenter diferentes servidores e enviar dados para projetos diferentes.
1. Avalie a preparação: Avalie se a máquinas no local são adequadas para execução no Azure. Inclui a avaliação:
    - Recomendações de tamanho: Obter recomendações de tamanho das VMs do Azure, com base no histórico de desempenho das VMs no local.
    - Custos mensais estimados: Obtenha custos estimados para executar máquinas no local no Azure.
2. Identificar dependências:  Visualize as dependências de máquinas no local, para criar grupos de máquinas ideal para avaliação e migração.


![Azure Migrate](./media/contoso-migration-scale/azure-migrate.png)


##### <a name="migrate-at-scale"></a>Migre à escala

Contoso tem de utilizar o Azure Migrate corretamente dar a escala desta migração. 

- Contoso fará uma avaliação da aplicação-aplicações com o Azure Migrate. Isto garante que a Azure Migrate retorna dados oportunos para o portal do Azure.
- Os administradores da Contoso, leia sobre [implementar o Azure Migrate em escala](how-to-scale-assessment.md)
- Contoso anota os limites do Azure Migrate resumidos na tabela seguinte.


**Ação** | **Limite**
--- | ---
Criar projeto do Azure Migrate | 1500 VMs
Deteção | 1500 VMs
Avaliação | 1500 VMs

Contoso irá utilizar o Azure Migrate da seguinte forma:

- No vCenter Contoso irá organizar as VMs em pastas. Isto torna fácil para os mesmos concentrar-se à medida que execute uma avaliação nas VMs numa pasta específica.
- O Azure Migrate utiliza o mapa de serviço do Azure para avaliar as dependências entre máquinas. Isso requer que os agentes de ser instalado em VMs para ser avaliada. 
    - Contoso usará o scripts automatizados para instalar os agentes do Windows ou Linux necessários.
    - Por meio de script, Contoso pode enviar por push a instalação para as VMs dentro de uma pasta do vCenter.


#### <a name="database-tools"></a>Ferramentas de bases de dados

Para além do Azure Migrate, Contoso irá focar-se sobre como utilizar ferramentas especificamente para avaliação da base de dados. As ferramentas como o [Assistente de migração de base de dados](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) ajudará a avaliar as bases de dados do SQL Server para a migração.

O Assistente de dados de migração (DMA) pode ajudar a Contoso para saber se as bases de dados no local são compatíveis com um soluções de base de dados de intervalo do Azure, como o SQL Database do Azure, SQL Server em execução numa VM de IaaS do Azure e a instância gerida do SQL do Azure. 

Para além do DMS, a Contoso tem alguns outros scripts que utilizam a detetar e documentar as bases de dados do SQL Server. Eles estão localizados no repositório do GitHub.

#### <a name="partner-tools"></a>Ferramentas de parceiros

Existem várias outras ferramentas de parceiros que podem ajudar a Contoso avaliar o ambiente no local para migração para o Azure. [Saiba mais](https://azure.microsoft.com/migration/partners/) sobre parceiros de migração do Azure.  

## <a name="phase-2-migrate"></a>Fase 2: Migrar

Com sua avaliação completa a Contoso precisa de identificar as ferramentas para mover seus aplicativos, dados e a infraestrutura para o Azure. 




### <a name="migration-strategies"></a>Estratégias de migração

Existem quatro estratégias de migração abrangente que pode considerar a Contoso. 

**Estratégia** | **Detalhes** | **Utilização**
--- | --- | ---
**Rehost**  | Muitas vezes referido como migração "lift- and -shift", esta é uma opção sem códigos para migrar aplicações existentes para o Azure rapidamente.<br/><br/> Uma aplicação é migrada como-é, com os benefícios da cloud, sem os riscos ou os custos associados a alterações de código. | Contoso pode realojar aplicações menos estratégico, que requerem sem alterações de código.
**Refactor** |  Também conhecido como "reempacotamento", esta estratégia requer aplicação muito simples de código ou alterações de configuração necessitam para ligar a aplicação à PaaS do Azure e tirar melhor partido das capacidades de cloud. | Contoso refatorar aplicações estratégicas para manter a mesma funcionalidade básica, mas movê-los para executar numa plataforma do Azure como os serviços de aplicações do Azure.<br/><br/> Isso requer alterações de código mínimo.<br/><br/> Por outro lado, a Contoso tem de manter uma plataforma VM, uma vez que isto não ser gerenciado pela Microsoft.
**Rearquitete** | Esta estratégia modifica ou expande uma base para otimizar a arquitetura de aplicações para recursos de nuvem e de dimensionamento de código de aplicação.<br/><br/> Ele moderniza uma aplicação numa arquitetura resiliente, altamente dimensionável, implementável independentemente.<br/><br/> Serviços do Azure podem acelerar o processo, dimensionar aplicações com confiança e gerir aplicações com facilidade.
**Reconstruir** | Esta estratégia recria uma aplicação do zero usando tecnologias nativas da cloud.<br/><br/> A plataforma Azure como um serviço (PaaS) fornece um ambiente completo de desenvolvimento e implementação na cloud. Ele elimina alguns custos e a complexidade de licenças de software e remove a necessidade de uma infraestrutura subjacente de aplicações, middleware e outros recursos. | Contoso reescrever as aplicações críticas de raiz, para tirar partido das tecnologias de cloud, como microsserviços ou computador sem servidor.<br/><br/> Contoso irá gerir as aplicações e os serviços que desenvolve e o Azure gere tudo o resto.


Dados também devem ser considerados, especialmente com o volume de bases de dados que a Contoso tiver. Abordagem de predefinição da Contoso consiste em utilizar serviços de PaaS como o SQL Database do Azure para tirar partido das funcionalidades da cloud. Ao migrar para um serviço de PaaS para bases de dados, Contoso apenas terá de manter dados, deixando a plataforma subjacente à Microsoft.

### <a name="evaluate-migration-tools"></a>Avaliar as ferramentas de migração

Contoso são principalmente usando algumas ferramentas e serviços do Azure para a migração:

- [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview): Orquestra a recuperação após desastre e efetua a migração de VMs no local para o Azure.
- [Serviço de migração de base de dados do Azure](https://docs.microsoft.com/azure/dms/dms-overview): Efetua a migração de bases de dados no local, como o SQL Server, MySQL e Oracle para o Azure.


#### <a name="azure-site-recovery"></a>Azure Site Recovery

O Azure Site Recovery é o principal serviço do Azure para orquestrar a recuperação após desastre e a migração a partir de dentro do Azure e a partir de sites no local para o Azure.

1. Recuperação de sites permite, orquestra a replicação a partir de seus sites no local para o Azure.
2. Quando a replicação está configurada e em execução, no local podem ser realizada nas máquinas para o Azure, concluir a migração.

Contoso já [concluir uma prova de conceito](contoso-migration-rehost-vm.md) para ver como o Site Recovery pode ajudá-los para migrar para a cloud.

##### <a name="using-site-recovery-at-scale"></a>Com o Site Recovery em escala

Planos de contoso sobre como executar várias migrações lift-and-shift. Para garantir que isso funciona, Site Recovery irá ser replicar lotes de cerca de 100 VMs por vez. Para saber como isto funciona, a Contoso precisa de efetuar o planeamento da capacidade para a migração do Site Recovery proposta.

- A Contoso precisa de recolher informações sobre os volumes de tráfego. Em particular:
    - A Contoso precisa de determinar a taxa de alteração para as VMs que quer replicar.
    - A Contoso precisa também considerar a conectividade de rede do site no local para o Azure.
- Em resposta a capacidade e os requisitos do volume, Contoso tem de alocar largura de banda suficiente com base na taxa de alteração de dados diário para as VMs necessárias, para satisfazer o seu objetivo de ponto de recuperação (RPO).
- Por último, eles precisam descobrir o número de servidores é necessários para executar componentes do Site Recovery, que são necessários para a implementação.

###### <a name="gather-on-premises-information"></a>Recolher informações no local
Contoso pode utilizar o [Site Recovery Deployment Planner](https://docs.microsoft.com/azure/site-recovery/site-recovery-deployment-planner) ferramenta para concluir estes passos:

- Contoso pode utilizar a ferramenta para criar remotamente o perfil VMs sem um impacto sobre o ambiente de produção. Isto ajuda a identificar os requisitos de largura de banda e armazenamento para a replicação e ativação pós-falha.
- Contoso pode executar a ferramenta sem instalar qualquer Site Recovery componentes no local.
- A ferramenta recolhe informações sobre VMs compatíveis e incompatíveis, discos por VM, e alterações a dados por disco. Ele também identifica os requisitos de largura de banda de rede e a infraestrutura do Azure necessários para replicação com êxito e ativação pós-falha.
- A Contoso precisa para se certificar de que, em seguida, execute a ferramenta Planeador de implementações num máquinas do Windows Server que satisfaça os requisitos mínimos para o servidor de configuração do Site Recovery. O servidor de configuração é uma máquina de recuperação de sites necessárias para replicar VMs de VMware no local.


###### <a name="identify-site-recovery-requirements"></a>Identificar requisitos de recuperação de sites

Para além das VMs a serem replicadas, o Site Recovery requer um número de componentes para a migração do VMware.

**Componente** | **Detalhes**
--- | ---
**Servidor de configuração** | Normalmente, uma VM do VMware configurado com um modelo OVF.<br/><br/> O componente do servidor de configuração coordena as comunicações entre o local e o Azure, e gere a replicação de dados.
**Servidor de processos** | Instalado por predefinição no servidor de configuração.<br/><br/> O componente de servidor de processos recebe dados de replicação; otimiza-os com colocação em cache, compressão e encriptação; e envia-os para o armazenamento do Azure.<br/><br/> O servidor de processos também instala o serviço de mobilidade de recuperação de sites do Azure nas VMs que pretende replicar, e efetua a deteção automática de máquinas no local.<br/><br/> Necessidade do dimensionado implementações adicionais, servidores de processos de autónomo para lidar com grandes volumes de tráfego de replicação.
**Serviço de mobilidade** | O agente do serviço de mobilidade está instalado em cada VM de VMware que será migrado com o Site Recovery.  

A Contoso precisa de saber como implementar estes componentes, com base em considerações de capacidade.

**Componente** | **Requisitos de capacidade**
--- | ---
**Taxa de alteração diária máximo** | Um servidor de processos único pode lidar com um valor diário máximo de 2 TB de taxa de alteração. Uma vez que uma VM só pode utilizar um servidor de processos, a taxa de alteração de dados de diário máximo suportado para uma VM replicada é 2 TB.
**Débito máximo** | Uma conta de armazenamento do Azure standard pode processar um máximo de 20 000 pedidos por segundo e operações de entrada/saída por segundo (IOPS) numa VM de replicação devem estar neste limite. Por exemplo, se uma VM tem 5 discos, e cada disco gera 120 IOPS (8K de tamanho) na VM, em seguida, ele será dentro do Azure por limite de IOPS de disco de 500.<br/><br/> Tenha em atenção que o número de contas de armazenamento necessário é igual à máquina de origem total IOPS, dividida por 20.000. Uma máquina replicada só pode pertencer a uma conta de armazenamento única no Azure.
**Servidor de configuração** | Com base na estimativa da Contoso da replicação desses 100 = 200 VMs em conjunto e o [requisitos de dimensionamento do servidor de configuração](../site-recovery/site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server), Contoso prever necessidades de uma máquina do servidor de configuração da seguinte forma:<br/><br/> CPU: 16 vCPUs (2 sockets * 8 núcleos @ 2,5 GHz)<br/><br/> Memória: 32 GB<br/><br/> Disco de cache: 1 TB<br/><br/> Taxa de alteração de dados: 1 TB para 2 TB.<br/><br/> Além dos requisitos de dimensionamento Contoso tem de certificar-se de que o servidor de configuração é ideal localizado na mesma rede e segmento de LAN que as VMs que serão migrados.
**Servidor de processos** | Contoso irá implementar um servidor de processos dedicados autónomo com a capacidade de replicar VMs de 100-200:<br/><br/> CPU: 16 vCPUs (2 sockets * 8 núcleos @ 2,5 GHz)<br/><br/> Memória: 32 GB<br/><br/> Disco de cache: 1 TB<br/><br/> Taxa de alteração de dados: 1 TB para 2 TB.<br/><br/> O servidor de processos será difícil de trabalhar e como tal, deve estar localizado num anfitrião ESXi que pode manipular a e/s de disco, o tráfego de rede e a CPU necessária para a replicação. Contoso considerará um anfitrião dedicado para esta finalidade. 
**Redes** | Contoso tem de rever a infraestrutura VPN de site a site atual e decidiu implementar o Azure ExpressRoute. A implementação é fundamental porque será menor latência e melhorar a largura de banda para a região de Azure de 2 do Leste E.u.a. primária da Contoso.<br/><br/> **Monitorização**: Contoso precisa de monitorizar cuidadosamente os dados transmitidos de servidor de processos. Se os dados sobrecarrega a largura de banda de rede Contoso considerará [a largura de banda do servidor de processo de limitação](../site-recovery/site-recovery-plan-capacity-vmware.md#control-network-bandwidth).
**Armazenamento do Azure** | Para a migração, Contoso tem de identificar o tipo correto e o número de contas de armazenamento do Azure de destino.  O site Recovery replica os dados da VM para o armazenamento do Azure.<br/><br/> Recuperação de sites pode replicar para contas de armazenamento (SSD) standard ou premium.<br/><br/> Para decidir sobre o armazenamento, Contoso tem de rever [limites de armazenamento](../virtual-machines/windows/disks-types.md)e o fator de crescimento esperado e aumento na utilização ao longo do tempo. Dada a velocidade e a prioridade das migrações, Contoso decidiu utilizar premium SSDs<br/><br/>
Contoso tiver feito a decisão de usar os discos geridos para todas as VMs que são implementadas no Azure.  O IOPS necessário irá determinar se os discos Standard HDD, Standard SSD ou Premium (SSD).<br/><br/>

#### <a name="data-migration-service"></a>Serviço de migração de dados

O Azure Database Migration Service (DMS) é um serviço totalmente gerido que permite migrações totalmente integradas de várias origens de dados para plataformas de dados do Azure, com o período de indisponibilidade mínimo.

- O DMS integra a funcionalidade de serviços e ferramentas existentes. Ele usa os dados de Assistente de migração (DMA), para gerar relatórios de avaliação que identificar recomendações sobre a compatibilidade de base de dados e quaisquer modificações necessárias.
- O DMS utiliza um processo de migração simples e autoexplicativo, com avaliação inteligente que ajuda a resolver os problemas potenciais antes da migração.
- O DMS pode migrar à escala de várias origens para a base de dados do Azure de destino.
- O DMS fornece o suporte do SQL Server 2005 para SQL Server 2017.

O DMS não é a única ferramenta de migração de base de dados de Microsoft. Obter um [comparação de ferramentas e serviços](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

###### <a name="using-dms-at-scale"></a>Com o DMS à escala

Contoso usará DMS ao migrar do SQL Server.  

- Quando o aprovisionamento DMS, a Contoso precisa para se certificar de que tem a capacidade correta e definido para otimizar o desempenho para migrações de dados. Contoso irá selecionar a opção "escalão crítico para a empresa com 4 vCores", permitindo assim que o serviço tirar partido das várias vCPUs para paralelização e transferência de dados mais rápida.

    ![DMS dimensionamento](./media/contoso-migration-scale/dms.png)

- Outra tática de dimensionamento da Contoso está temporariamente aumentar verticalmente a instância de destino do Azure SQL ou base de dados MySQL para o SKU de escalão Premium durante a migração de dados. Isso minimiza a limitação da base de dados que podem afetar a atividades de transferência de dados ao utilizar os SKUs de nível inferior.



##### <a name="using-other-tools"></a>Com outras ferramentas

Para além do DMS, Contoso pode utilizar outras ferramentas e serviços para identificar informações da VM.

- Eles têm de script para ajudar com as migrações manuais. Estas estão disponíveis no repositório do GitHub.
- Diversas [ferramentas de parceiros](https://azure.microsoft.com/migration/partners/) também pode ser utilizado para a migração.


## <a name="phase-3-optimize"></a>Fase 3: Otimizar

Depois de Contoso move os recursos para o Azure, que precisam para simplificá-los para melhorar o desempenho e maximizar o ROI com ferramentas de gestão de custos. Tendo em conta que o Azure é um serviço de pagamento para utilização, é fundamental para a Contoso para compreender o desempenho dos sistemas e certifique-se de que eles são dimensionados adequadamente.


### <a name="azure-cost-management"></a>Gestão de custos do Azure

Para tirar o máximo proveito do seu investimento na cloud, Contoso irá tirar partido da ferramenta gratuita do Azure Cost Management.

- Esta solução licenciada criada pela Cloudyn, uma subsidiária da Microsoft, permite que a Contoso gerir gastos com transparência e precisão da cloud.  Ele fornece ferramentas para monitorizar, alocar e cortar custos na cloud.
- O Azure Cost Management fornece relatórios de dashboard simples para ajudar na alocação de custos, showbacks e estornos.
- Gestão de custos pode otimizar os gastos da cloud ao identificar recursos subutilizados que Contoso, em seguida, pode gerir e ajustar.
- [Saiba mais](https://docs.microsoft.com/azure/cost-management/overview) sobre o Azure Cost Management.

    
![Gestão de custos](./media/contoso-migration-scale/cost-management.png)  
    
 
### <a name="native-tools"></a>Ferramentas nativas

Contoso também irá utilizar scripts para localizar recursos não utilizados.

- Durante as migrações de grandes dimensões, muitas vezes, existem restantes partes de dados, tais como discos rígidos virtuais (VHDs), que incorrem numa cobrança, mas não fornecem nenhum valor para a empresa. Scripts estão disponíveis no repositório do GitHub.
- Contoso aproveitará o trabalho realizado pela Microsoft e considere implementar o Kit de ferramentas de otimização de recursos do Azure (ARO) do departamento de TI.
- Contoso pode implementar uma conta de automatização do Azure com runbooks pré-configurada e agendas na sua subscrição e começa a poupar dinheiro. Otimização de recursos do Azure ocorre automaticamente numa subscrição depois de uma agenda é ativada ou criada, incluindo otimização sobre os novos recursos.
- Isso fornece capacidades de automatização descentralizado para reduzir os custos. As funcionalidades incluem:
    - Suspender automaticamente VMs do Azure com base na CPU baixa.
    - Agendar VMs do Azure para suspender e unsnooze.
    - Agendar VMs do Azure para suspender ou unsnooze ascendente e por ordem descendente utilizando etiquetas do Azure.
    - Eliminação em massa de recurso agrupa a pedido.
- Comece com o Kit de ferramentas ARO desta [repositório do GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit).

### <a name="partner-tools"></a>Ferramentas de parceiros

Ferramentas, tais como de parceiros [Hanu](https://hanu.com/insight/) e [Scalr]( https://www.scalr.com/cost-optimization/) pode ser aproveitado.


## <a name="phase-4-secure--manage"></a>Fase 4: Proteger e gerir

Nesta fase, a Contoso utiliza recursos de gerenciamento e segurança do Azure para governar, proteger e monitorizar aplicações na cloud no Azure. Esses recursos ajudarão a executar um ambiente seguro e bem gerido ao utilizar os produtos disponíveis no portal do Azure. Contoso começa a utilizar estes serviços durante a migração e, com suporte híbrido do Azure, continua a utilizar muitos para uma experiência consistente entre a cloud híbrida.


### <a name="security"></a>Segurança
Contoso dependerá do Centro de segurança do Azure para gestão de segurança unificada e proteção avançada contra ameaças entre cargas de trabalho híbrida na cloud.

- O Centro de segurança fornece visibilidade total para e controle sobre, a segurança de aplicações na cloud no Azure.
- Contoso rapidamente pode detetar e tomar medidas em resposta a ameaças e reduzir a exposição de segurança ao ativar a proteção contra ameaças adaptáveis.

[Saiba mais](https://azure.microsoft.com/services/security-center/) sobre o Centro de segurança. 


### <a name="monitoring"></a>Monitorização

A Contoso precisa de visibilidade sobre o estado de funcionamento e o desempenho de aplicações recentemente migradas, a infraestrutura e a dados agora em execução no Azure. Contoso irá tirar partido de cloud do Azure incorporada, ferramentas como o Azure Monitor, área de trabalho do Log Analytics e Application Insights de monitorização.
 
- Com estas ferramentas Contoso pode facilmente recolher dados de fontes e obter informações valiosas. Por exemplo, a Contoso pode medir a utilização de memória e disco da CPU para as VMs, ver aplicações e dependências de rede em várias VMs e acompanhar o desempenho do aplicativo.
- Contoso irá utilizar estas ferramentas de monitorização de nuvem para tomar ação e integrar com soluções de serviço.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) sobre a monitorização do Azure.

### <a name="bcdr"></a>BCDR 

Contoso será necessária uma estratégia de recuperação (BCDR) de desastre e continuidade empresarial para seus recursos do Azure.

- O Azure disponibiliza [recursos internos de BCDR](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications) para manter os dados seguros e aplicações/serviços em funcionamento. 
- Além das funcionalidades incorporadas, o Contoso pretende garantir que ele pode recuperar de falhas, evitar interrupções de negócio dispendiosas, atingir os objetivos de conformidade e proteger dados contra ransomware e erros humanos. Para fazê-lo
    - Contoso irá implementar uma cópia de segurança do Azure como uma solução económica para cópia de segurança de recursos do Azure. Porque ele é interno, Contoso pode configurar cópias de segurança na cloud em poucos passos simples.
    - Contoso definirá a recuperação após desastre para VMs do Azure com o Azure Site Recovery para a replicação, ativação pós-falha e reativação pós-falha entre regiões do Azure, que especifica.  Isto garante que as aplicações em execução em VMs do Azure permanecerão disponíveis numa região secundária da Contoso escolher se ocorrer uma falha na região primária. [Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).

 
## <a name="conclusion"></a>Conclusão

Neste artigo, a Contoso planeadas para uma migração do Azure em escala.  O processo de migração eles dividido em quatro estágios. Da avaliação e migração, por meio de otimização, segurança e gestão após a migração foi concluída. Em grande parte, é importante planejar um projeto de migração como um todo processo, mas para migrar sistemas dentro de uma organização ao dividir os conjuntos de para baixo em classificações e os números que façam sentido para a empresa. Ao analisar dados e aplicar classificações, e o projeto pode ser dividido numa série de migrações mais pequenas, que podem ser executadas com segurança e rapidamente.  A soma dessas migrações menores rapidamente se transforma numa grande migração com êxito para o Azure.
