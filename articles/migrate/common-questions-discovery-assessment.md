---
title: Perguntas sobre a descoberta, avaliação e análise de dependência em Azure Migrate
description: Obtenha respostas a perguntas comuns sobre a descoberta, avaliação e análise de dependência em Azure Migrate.
author: rashijoshi
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: 629459d22b18b326307b45bb512d16622808b533
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562635"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Descoberta, avaliação e análise de dependência - Questões comuns

Este artigo responde a questões comuns sobre a descoberta, avaliação e análise de dependência em Azure Migrate. Se tiver outras questões, verifique estes recursos:

- [Perguntas gerais](resources-faq.md) sobre Azure Migrate
- Perguntas sobre o [aparelho Azure Migrate](common-questions-appliance.md)
- Perguntas sobre [migração de servidores](common-questions-server-migration.md)
- Obtenha perguntas respondidas no [fórum Azure Migrate](https://aka.ms/AzureMigrateForum)


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Que geografias são apoiadas para a descoberta e avaliação com Azure Migrate?

Reveja as regiões suportadas em [clouds públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e do [Azure Government](migrate-support-matrix.md#supported-geographies-azure-government).


## <a name="how-many-servers-can-i-discover-with-an-appliance"></a>Quantos servidores posso descobrir com um aparelho?

Você pode descobrir até 10.000 servidores do ambiente VMware, até 5.000 servidores do ambiente Hyper-V, e até 1000 servidores físicos usando um único aparelho. Se tiver mais servidores, leia sobre [o dimensionamento de uma avaliação de Hiper-V,](scale-hyper-v-assessment.md) [escalonando uma avaliação de VMware](scale-vmware-assessment.md)ou [escalando uma avaliação do servidor físico](scale-physical-assessment.md).

## <a name="how-do-i-choose-the-assessment-type"></a>Como devo proceder para escolher o tipo de avaliação?

- Utilize **avaliações de VM do Azure** quando pretender avaliar servidores a partir do ambiente [VMware](how-to-set-up-appliance-vmware.md) e [Hiper-V](how-to-set-up-appliance-hyper-v.md) no local, bem como [servidores físicos](how-to-set-up-appliance-physical.md) para migração para VMs Azure. [Saiba mais](concepts-assessment-calculation.md)

- Utilize o tipo de avaliação **Azure SQL** quando pretender avaliar o seu SqL Server no local a partir do seu ambiente VMware para migração para Azure SQL Database ou Azure SQL Managed Instance. [Saiba mais](concepts-assessment-calculation.md)

- Utilize avaliações **da Solução VMware Azure (AVS)** quando pretender avaliar os seus [VMS VMware](how-to-set-up-appliance-vmware.md) no local para migração para [Azure VMware Solution (AVS)](../azure-vmware/introduction.md) utilizando este tipo de avaliação. [Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)

- Pode utilizar um grupo comum com as máquinas virtuais VMware apenas para executar ambos os tipos de avaliações. Tenha em atenção que se estiver a fazer avaliações do AVS no Azure Migrate pela primeira vez, será aconselhável criar um novo grupo de máquinas virtuais VMware.
 

## <a name="why-is-performance-data-missing-for-someall-servers-in-my-azure-vm-andor-avs-assessment-report"></a>Porque é que faltam dados de desempenho para alguns/todos os servidores no meu relatório de avaliação do Azure VM e/ou AVS?

Para a avaliação "baseada no desempenho", a exportação do relatório de avaliação diz "PercentagemOfCoresUtilizedMissing" ou "PercentagemOfMemoryUtilizedMissing" quando o aparelho Azure Migrate não consegue recolher dados de desempenho para os servidores no local. Verifique:

- Se os servidores forem ligados durante a duração durante a qual está a criar a avaliação
- Se faltarem apenas contadores de memória e estiver a tentar avaliar os servidores em ambiente Hiper-V. Neste cenário, por favor, ative a memória dinâmica nos servidores e 'Recalcule' a avaliação para refletir as últimas alterações. O aparelho só pode recolher valores de utilização da memória para cortes no ambiente Hiper-V quando o servidor tiver memória dinâmica ativada.

- Se todos os contadores de desempenho estiverem em falta, certifique-se de que as ligações de saída nas portas 443 (HTTPS) são permitidas.

    > [!Note]
    > Se faltar algum dos contadores de desempenho, Azure Migrate: A Avaliação do Servidor recai sobre os núcleos/memória atribuídos no local e recomenda um tamanho VM em conformidade.


## <a name="why-is-performance-data-missing-for-someall-sql-instancesdatabases-in-my-azure-sql-assessment"></a>Porque é que faltam dados de desempenho para algumas/todas as sesagens/bases de dados sql na minha avaliação do Azure SQL?

Para garantir a recolha dos dados de desempenho, verifique:

- Se os Servidores SQL forem ligados durante a duração para a qual está a criar a avaliação
- Se o estado de ligação do agente SQL em Azure Migrate estiver 'Conectado' e verificar o último batimento cardíaco 
- Se o estado de ligação Azure Migrate para todos os casos SQL estiver 'Conectado' na lâmina de instância SQL descoberta
- Se todos os contadores de desempenho estiverem em falta, confirme que as ligações de saída nas portas 443 (HTTPS) são permitidas

Se faltar algum dos contadores de desempenho, a avaliação do Azure SQL recomenda a menor configuração Azure SQL para esse caso/base de dados.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>Porque é que a classificação de confiança da minha avaliação é baixa?

A classificação de confiança é calculada para as avaliações “Baseadas no desempenho” com base na percentagem de [pontos de dados disponíveis](./concepts-assessment-calculation.md#ratings), necessários para calcular a avaliação. Veja abaixo os motivos pelos quais uma avaliação pode obter uma classificação de confiança baixa:

- Não analisou o ambiente durante o tempo para a qual está a criar a avaliação. Por exemplo, se estiver a criar uma avaliação com a duração de desempenho definida para uma semana, terá de aguardar, pelo menos, uma semana após iniciar a deteção de todos os pontos de dados serem recolhidos. Se não puder esperar pela duração, altere a duração do desempenho para um período menor e **recalcule** a avaliação.
 
- A avaliação não é capaz de recolher os dados de desempenho de alguns ou todos os servidores no período de avaliação. Para uma alta classificação de confiança, certifique-se de que: 
    - Os servidores são ligados durante a duração da avaliação
    - São permitidas ligações de saída nas portas 443
    - Para os servidores hiper-V a memória dinâmica está ativada 
    - O estado de ligação dos agentes em Azure Migrate são 'Conectados' e verificam o último batimento cardíaco
    - Para avaliações de Azure SQL, o estado de ligação do Azure Migrate para todos os casos SQL é "Conectado" na lâmina de instância SQL descoberta

    **Recalcule** a avaliação para refletir as últimas alterações na classificação de confiança.

- Para avaliações de Azure VM e AVS, poucos servidores foram criados após a descoberta ter começado. Por exemplo, se estiver a criar uma avaliação para o histórico de desempenho do último mês, mas poucos servidores foram criados no ambiente apenas há uma semana. Neste caso, os dados de desempenho dos novos servidores não estarão disponíveis durante toda a duração e a classificação de confiança será baixa. [Saiba mais](./concepts-assessment-calculation.md#confidence-ratings-performance-based)

- Para avaliações do SQL do Azure, foram criadas poucas bases de dados ou instâncias do SQL após o início da deteção. Por exemplo, se estiver a criar uma avaliação para o histórico de desempenho do último mês, mas foram criados poucos casos ou bases de dados SQL no ambiente há apenas uma semana. Neste caso, os dados de desempenho dos novos servidores não estarão disponíveis durante toda a duração e a classificação de confiança será baixa. [Saiba mais](./concepts-azure-sql-assessment-calculation.md#confidence-ratings)

## <a name="i-want-to-try-out-the-new-azure-sql-assessment"></a>Quero experimentar a nova avaliação do Azure SQL
A descoberta e avaliação de instâncias e bases de dados do SQL Server em execução no seu ambiente VMware está agora em pré-visualização. Começa com [este tutorial.](tutorial-discover-vmware.md) Se pretender experimentar esta funcionalidade num projeto existente, certifique-se de que completou os [pré-requisitos](how-to-discover-sql-existing-project.md) deste artigo.

## <a name="i-cant-see-some-servers-when-i-am-creating-an-azure-sql-assessment"></a>Não consigo ver alguns servidores ao criar uma avaliação do SQL do Azure

- A avaliação do SQL do Azure só pode ser realizada em servidores em execução onde foram detetadas instâncias do SQL. Se não vir os servidores e as instâncias do SQL que deseja avaliar, aguarde algum tempo para que a deteção seja concluída e, em seguida, crie a avaliação. 
- Se não conseguir ver um grupo previamente criado durante a criando a avaliação, remova qualquer servidor não VMware ou qualquer servidor sem uma instância SQL do grupo.
- Se estiver a executar avaliações do SQL do Azure no Azure Migrate pela primeira vez, é aconselhável criar um novo grupo de servidores.

## <a name="i-want-to-understand-how-was-the-readiness-for-my-instance-computed"></a>Quero entender como foi calculada a prontidão para o meu exemplo?
A preparação das suas instâncias do SQL foi calculada depois de ser realizada uma verificação de compatibilidade de funcionalidades com o tipo de implementação do SQL do Azure de destino (Base de Dados SQL do Azure ou Azure SQL Managed Instance). [Saiba mais](./concepts-azure-sql-assessment-calculation.md#calculate-readiness)

## <a name="why-is-the-readiness-for-all-my-sql-instances-marked-as-unknown"></a>Por que a prontidão para todos os meus casos SQL é marcada como desconhecida?
Se a sua descoberta foi iniciada recentemente e ainda está em andamento, você pode ver a prontidão para alguns ou todos os casos SQL como desconhecidos. Recomendamos que aguarde algum tempo para a aplicação analisar o ambiente e, em seguida, recalcule a avaliação.
A descoberta SQL é realizada uma vez a cada 24 horas e pode ser necessário esperar até um dia para que as últimas alterações de configuração reflitam. 

## <a name="why-is-the-readiness-for-some-of-my-sql-instances-marked-as-unknown"></a>Por que a prontidão para alguns dos meus casos sql é marcada como desconhecida?
Isto pode acontecer se: 
- A descoberta ainda está em andamento. Recomendamos que aguarde algum tempo para a aplicação analisar o ambiente e, em seguida, recalcule a avaliação.
- Existem alguns problemas de deteção que precisa de corrigir no painel Erros e notificações.

A descoberta SQL é realizada uma vez a cada 24 horas e pode ser necessário esperar até um dia para que as últimas alterações de configuração reflitam.

## <a name="my-assessment-is-in-outdated-state"></a>A minha avaliação está no estado Desatualizada

### <a name="azure-vmavs-assessment"></a>Avaliação de Azure VM/AVS
Se houver alterações no local para servidores que estejam num grupo que foi avaliado, a avaliação está marcada desatualizada. Uma avaliação pode ser marcada como "Ultrapassada" devido a uma ou mais alterações nas propriedades abaixo:
- Número de núcleos de processador
- Memória alocada
- Tipo de bota ou firmware
- Nome do sistema operativo, versão e arquitetura
- Número de discos
- Número de adaptador de rede
- Alteração do tamanho do disco (GB Atribuído)
- Atualização de propriedades nic. Exemplo: Alterações no endereço mac, adição de endereço IP, etc.

Por favor, **recalcule** a avaliação para refletir as últimas alterações na avaliação.

### <a name="azure-sql-assessment"></a>Avaliação do SQL do Azure
Se ocorrerem alterações em bases de dados e instâncias do SQL no local que façam parte de um grupo que foi avaliado, a avaliação é marcada como **desatualizada**:
- Foi adicionada ou removida uma instância do SQL de um servidor
- Foi adicionada ou removida uma base de dados SQL de uma instância do SQL
- O tamanho total da base de dados numa instância do SQL sofreu uma alteração superior a 20%
- Alteração do número de núcleos de processador e/ou memória atribuída

Por favor, **recalcule** a avaliação para refletir as últimas alterações na avaliação.

## <a name="why-was-i-recommended-a-particular-target-deployment-type"></a>Por que motivo me recomendaram um tipo específico de implementação de destino?
O Azure Migrate recomenda um tipo específico de implementação do SQL do Azure compatível com a sua instância do SQL. Migrar para um destino recomendado pela Microsoft reduz o seu esforço de migração geral. Esta configuração do SQL do Azure (SKU) foi recomendada depois de serem consideradas as características de desempenho da sua instância do SQL e das bases de dados que esta gere. Se várias configurações do SQL do Azure forem elegíveis, recomendamos a mais económica. [Saiba mais](./concepts-azure-sql-assessment-calculation.md#calculate-sizing)

## <a name="what-deployment-target-should-i-choose-if-my-sql-instance-is-ready-for-azure-sql-db-and-azure-sql-mi"></a>Que destino de implementação devo escolher se a minha instância do SQL estiver preparada para a BD SQL do Azure e o Azure SQL MI? 
Se a sua instância estiver preparada para a BD SQL do Azure e o Azure SQL MI, recomendamos o tipo de implementação de destino cujo custo estimado de configuração do SQL do Azure seja menor.

## <a name="why-is-my-instance-marked-as-potentially-ready-for-azure-vm-in-my-azure-sql-assessment"></a>Porque é que o meu caso está potencialmente pronto para o Azure VM na minha avaliação do Azure SQL?
Tal pode acontecer quando o tipo de implementação de destino escolhido nas propriedades da avaliação é **Recomendado** e a instância do SQL não está preparada para a Base de Dados SQL do Azure e o Azure SQL Managed Instance. Recomenda-se ao utilizador que crie uma avaliação no Azure Migrate com o tipo de avaliação como **VM do Azure** para determinar se o Servidor no qual a instância está em execução está preparado para migrar para uma VM do Azure.
Recomenda-se ao utilizador a criação de uma avaliação em Azure Migrate com o tipo de avaliação como **Azure VM** para determinar se o servidor em que o caso está em execução está pronto para migrar para um VM Azure:
- As avaliações de Azure VM em Azure Migrate estão atualmente focadas no elevador e não considerarão as métricas de desempenho específicas para executar instâncias e bases de dados SQL na máquina virtual Azure. 
- Quando executa uma avaliação de VM do Azure num servidor, as estimativas recomendadas de tamanho e custo referem-se a todas as instâncias em execução no servidor e podem ser migradas para uma VM do Azure com a ferramenta de Migração do Servidor. Antes de migrar, [veja as diretrizes de desempenho](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) do SQL Server em máquinas virtuais do Azure.

## <a name="i-cant-see-some-databases-in-my-assessment-even-though-the-instance-is-part-of-the-assessment"></a>Não consigo ver algumas bases de dados na minha avaliação, embora a instância faça parte da avaliação

A avaliação do SQL do Azure inclui apenas bases de dados que estejam no estado online. Caso a base de dados esteja em qualquer outro estado, a avaliação ignora o respetivo cálculo de preparação, dimensionamento e custo. Se desejar avaliar essas bases de dados, altere o estado das mesmas e recalcule a avaliação após algum tempo.

## <a name="i-want-to-compare-costs-for-running-my-sql-instances-on-azure-vm-vs-azure-sql-databaseazure-sql-managed-instance"></a>Quero comparar os custos de execução das minhas instâncias SQL no Azure VM Vs Azure SQL Database/Azure SQL Managed Instance

Pode criar uma avaliação com o tipo **VM do Azure** no mesmo grupo utilizado na sua avaliação do **SQL do Azure**. Em seguida, pode comparar os dois relatórios lado a lado. No entanto, as avaliações de VM do Azure no Azure Migrate focam-se atualmente na migração lift-and-shift e não consideram as métricas de desempenho específicas da execução de bases de dados e instâncias do SQL na máquina virtual do Azure. Quando executa uma avaliação de VM do Azure num servidor, as estimativas recomendadas de tamanho e custo referem-se a todas as instâncias em execução no servidor e podem ser migradas para uma VM do Azure com a ferramenta de Migração do Servidor. Antes de migrar, [veja as diretrizes de desempenho](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) do SQL Server em máquinas virtuais do Azure.

## <a name="the-storage-cost-in-my-azure-sql-assessment-is-zero"></a>O custo de armazenamento na minha avaliação Azure SQL é zero
Para a Azure SQL Managed Instance, não há nenhum custo de armazenamento adicionado para o primeiro armazenamento de 32 GB/instância/mês e custo adicional de armazenamento é adicionado para armazenamento em incrementos de 32GB. [Saiba mais](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)

## <a name="i-cant-see-some-groups-when-i-am-creating-an-azure-vmware-solution-avs-assessment"></a>Não consigo ver alguns grupos quando estou a criar uma avaliação da Solução VMware (AVS) do Azure VMware

- A avaliação do AVS pode ser feita em grupos que têm apenas máquinas virtuais VMware. Remova as máquinas virtuais que não sejam VMware do grupo se quiser realizar uma avaliação do AVS.
- Se estiver a fazer avaliações do AVS no Azure Migrate pela primeira vez, será aconselhável criar um novo grupo de máquinas virtuais VMware.

## <a name="i-cant-see-some-vm-types-and-sizes-in-azure-government"></a>Não consigo ver alguns tipos e tamanhos de VM no Governo de Azure.

Os tipos e tamanhos de VM suportados para avaliação e migração dependem da disponibilidade na localização do Governo Azure. Pode [rever e comparar](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) tipos de VM no Governo Azure.

## <a name="the-size-of-my-server-changed-can-i-run-an-assessment-again"></a>O tamanho do meu servidor mudou. Posso fazer uma avaliação de novo?

O aparelho Azure Migrate recolhe continuamente informações sobre o ambiente no local.  Uma avaliação é uma imagem pontual dos servidores no local. Se alterar as definições de um servidor que pretende avaliar, utilize a opção de recalculação para atualizar a avaliação com as alterações mais recentes.

## <a name="how-do-i-discover-servers-in-a-multitenant-environment"></a>Como descubro servidores num ambiente multitenant?

- **VMware**: Se um ambiente é partilhado entre inquilinos e não quer descobrir os servidores de um inquilino na subscrição de outro inquilino, crie credenciais VMware vCenter Server que possam aceder apenas aos servidores que pretende descobrir. Em seguida, use estas credenciais quando começar a ser descoberto no aparelho Azure Migrate.
- **Hiper-V:** A Descoberta utiliza credenciais de anfitrião Hyper-V. Se os servidores partilham o mesmo anfitrião Hyper-V, não há forma de separar a descoberta.  

## <a name="do-i-need-vcenter-server"></a>Preciso do servidor vCenter?

Sim, a Azure Migrate requer o vCenter Server num ambiente VMware para realizar a descoberta. A Azure Migrate não suporta a descoberta de anfitriões ESXi que não são geridos pelo vCenter Server.

## <a name="what-are-the-sizing-options-in-an-azure-vm-assessment"></a>Quais são as opções de dimensionamento numa avaliação de VM Azure?

Com o tamanho do as-on-ins, a Azure Migrate não considera os dados de desempenho do servidor para avaliação. Azure Migrate avalia os tamanhos de VM com base na configuração no local. Com o dimensionamento baseado no desempenho, o dimensionamento baseia-se em dados de utilização.

Por exemplo, se um servidor no local tiver quatro núcleos e 8 GB de memória a 50% de utilização do CPU e 50% de utilização da memória:
- O tamanho do as-on-ins recomendará um Azure VM SKU que tem quatro núcleos e 8 GB de memória.
- O tamanho baseado no desempenho recomendará um SKU VM que tenha dois núcleos e 4 GB de memória porque a percentagem de utilização é considerada.

Da mesma forma, o tamanho do disco depende de critérios de dimensionamento e tipo de armazenamento:
- Se os critérios de dimensionamento forem baseados no desempenho e o tipo de armazenamento for automático, a Azure Migrate tem em conta os valores de IOPS e de produção do disco quando identifica o tipo de disco-alvo (Standard ou Premium).
- Se os critérios de dimensionamento forem baseados no desempenho e o tipo de armazenamento for Premium, a Azure Migrate recomenda um SKU de disco Premium baseado no tamanho do disco no local. A mesma lógica aplica-se ao tamanho do disco quando o dimensionamento é as-no-nas-instalações e o tipo de armazenamento é Standard ou Premium.

## <a name="does-performance-history-and-utilization-affect-sizing-in-an-azure-vm-assessment"></a>O histórico de desempenho e a utilização afetam o dimensionamento numa avaliação de VM do Azure?

Sim, o histórico de desempenho e a utilização afetam o dimensionamento numa avaliação de VM Azure.

### <a name="performance-history"></a>Histórico de desempenho

Apenas para o tamanho baseado no desempenho, a Azure Migrate recolhe o histórico de desempenho das máquinas no local e, em seguida, usa-a para recomendar o tamanho VM e o tipo de disco em Azure:

1. O aparelho traça o perfil contínuo do ambiente no local para recolher dados de utilização em tempo real a cada 20 segundos.
2. O aparelho enrola as amostras recolhidas de 20 segundos e utiliza-as para criar um único ponto de dados a cada 15 minutos.
3. Para criar o ponto de dados, o aparelho seleciona o valor máximo de todas as amostras de 20 segundos.
4. O aparelho envia o ponto de dados para Azure.

### <a name="utilization"></a>Utilização

Quando cria uma avaliação em Azure, dependendo da duração do desempenho e do valor percentil do desempenho que está definido, a Azure Migrate calcula o valor de utilização eficaz e, em seguida, usa-a para dimensionamento.

Por exemplo, se definir a duração do desempenho para um dia e o valor percentil para 95º percentil, Azure Migrate classifica os pontos de amostra de 15 minutos enviados pelo coletor para o último dia em ordem crescente. Escolhe o valor percentil 95 como utilização eficaz.

A utilização do valor percentil 95 garante que os forasteiros são ignorados. Os outliers podem ser incluídos se o seu Azure Migrate usar o percentil 99. Para escolher o pico de utilização para o período sem perder nenhum outliers, coloque Azure Migrate para usar o percentil 99.


## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>Como as avaliações baseadas nas importações são diferentes das avaliações com fonte de descoberta como aparelho?

As avaliações de VM Azure baseadas em importação são avaliações criadas com máquinas que são importadas para a Azure Migrate utilizando um ficheiro CSV. Apenas quatro campos são obrigatórios para importar: nome do servidor, núcleos, memória e sistema operativo. Aqui estão algumas coisas a notar: 
 - Os critérios de prontidão são menos rigorosos nas avaliações baseadas nas importações no parâmetro do tipo de arranque. Se o tipo de arranque não for fornecido, presume-se que a máquina tem o tipo de arranque BIOS e a máquina não está marcada como **Conditionally Ready**. Em avaliações com fonte de descoberta como aparelho, a prontidão é marcada como **Conditionally Ready** se o tipo de arranque estiver em falta. Esta diferença no cálculo da prontidão deve-se ao facto de os utilizadores não terem toda a informação sobre as máquinas nas fases iniciais do planeamento migratório quando as avaliações baseadas nas importações forem efetuadas. 
 - As avaliações de importação baseadas no desempenho utilizam o valor de utilização fornecido pelo utilizador para cálculos de tamanho correto. Uma vez que o valor de utilização é fornecido pelo utilizador, o histórico de **desempenho** e as opções **de utilização por percentil** são desativadas nas propriedades de avaliação. Em avaliações com fonte de descoberta como aparelho, o valor percentil escolhido é colhido a partir dos dados de desempenho recolhidos pelo aparelho.

## <a name="why-is-the-suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>Porque é que o instrumento de migração sugerido na avaliação do AVS baseado nas importações é marcado como desconhecido?

Para as máquinas importadas através de um ficheiro CSV, a ferramenta de migração padrão numa avaliação AVS é desconhecida. No entanto, para máquinas VMware, é aconselhável utilizar a solução VMware Hybrid Cloud Extension (HCX). [Saiba Mais](../azure-vmware/tutorial-deploy-vmware-hcx.md).


## <a name="what-is-dependency-visualization"></a>O que é visualização de dependência?

A visualização de dependência pode ajudá-lo a avaliar grupos de servidores para migrar com maior confiança. A visualização da dependência cruza as dependências das máquinas antes de fazer uma avaliação. Ajuda a garantir que nada é deixado para trás, e ajuda a evitar interrupções inesperadas quando migra para Azure. A Azure Migrate utiliza a solução de Mapa de Serviço no Monitor Azure para permitir a visualização da dependência. [Saiba mais](concepts-dependency-visualization.md).

> [!NOTE]
> A análise da dependência baseada em agentes não está disponível no Governo de Azure. Pode usar a análise de dependência sem agente

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Qual é a diferença entre agente e agente?

As diferenças entre visualização sem agente e visualização baseada em agentes são resumidas na tabela.

**Requisito** | **Sem agente** | **Baseada em agente**
--- | --- | ---
Suporte | Esta opção encontra-se atualmente em pré-visualização e só está disponível para servidores em ambiente VMware. [Reveja](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) os sistemas operativos suportados. | Em disponibilidade geral (GA).
Agente | Não é necessário instalar agentes em máquinas que pretende verificar. | Agentes a instalar em cada máquina no local que pretende analisar: O agente de monitorização da [Microsoft (MMA)](../azure-monitor/agents/agent-windows.md)e o [agente Desadependido](../azure-monitor/agents/agents-overview.md#dependency-agent). 
Pré-requisitos | [Reveja](concepts-dependency-visualization.md#agentless-analysis) os requisitos pré-requisitos e requisitos de implantação. | [Reveja](concepts-dependency-visualization.md#agent-based-analysis) os requisitos pré-requisitos e requisitos de implantação.
Log Analytics | Não necessárias. | A Azure Migrate utiliza a solução [de Mapa de Serviço](../azure-monitor/vm/service-map.md) nos [registos do Monitor Azure](../azure-monitor/logs/log-query-overview.md) para visualização da dependência. [Saiba mais](concepts-dependency-visualization.md#agent-based-analysis).
Como funciona | Captura dados de ligação TCP em máquinas ativadas para visualização de dependência. Após a descoberta, recolhe dados em intervalos de cinco minutos. | Os agentes do Mapa de Serviço instalados numa máquina recolhem dados sobre processos TCP e ligações de entrada/saída para cada processo.
Dados | Nome do servidor da máquina de origem, processo, nome da aplicação.<br/><br/> Nome do servidor da máquina de destino, processo, nome da aplicação e porta. | Nome do servidor da máquina de origem, processo, nome da aplicação.<br/><br/> Nome do servidor da máquina de destino, processo, nome da aplicação e porta.<br/><br/> O número de ligações, latência e informações de transferência de dados são recolhidos e disponíveis para consultas de Log Analytics. 
Visualização | O mapa de dependência de um único servidor pode ser visto durante uma hora a 30 dias. | Mapa de dependência de um único servidor.<br/><br/> O mapa pode ser visto apenas durante uma hora.<br/><br/> Mapa de dependência de um grupo de servidores.<br/><br/> Adicione e remova os servidores num grupo a partir da vista do mapa.
Exportação de dados | Os dados dos últimos 30 dias podem ser descarregados num formato CSV. | Os dados podem ser consultados com Log Analytics.


## <a name="do-i-need-to-deploy-the-appliance-for-agentless-dependency-analysis"></a>Preciso de colocar o aparelho para análise de dependência sem agentes?

Sim, o [aparelho Azure Migrate](migrate-appliance.md) deve ser acionado.

## <a name="do-i-pay-for-dependency-visualization"></a>Pago pela visualização da dependência?

N.º Saiba mais sobre [os preços da Azure Migrate.](https://azure.microsoft.com/pricing/details/azure-migrate/)

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>O que é que instalo para visualização de dependência baseada em agentes?

Para utilizar agentes de visualização de dependência baseados em agentes, descarregue e instale agentes em cada máquina no local que pretende avaliar:

- [Agente de monitorização da Microsoft (MMA)](../azure-monitor/agents/agent-windows.md)
- [Agente de Dependência](../azure-monitor/agents/agents-overview.md#dependency-agent)
- Se tiver máquinas que não tenham conectividade com a Internet, descarregue e instale o gateway Do Log Analytics nelas.

Só precisa destes agentes se utilizar a visualização de dependência baseada em agentes.

## <a name="can-i-use-an-existing-workspace"></a>Posso usar um espaço de trabalho existente?

Sim, para visualização de dependência baseada em agente, você pode anexar um espaço de trabalho existente ao projeto de migração e usá-lo para visualização de dependência. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Posso exportar o relatório de visualização de dependência?

Não, o relatório de visualização de dependência na visualização baseada em agentes não pode ser exportado. No entanto, a Azure Migrate utiliza o Mapa de Serviços e pode utilizar o [Mapa de Serviços REST API](/rest/api/servicemap/machines/listconnections) para recuperar as dependências no formato JSON.

## <a name="can-i-automate-agent-installation"></a>Posso automatizar a instalação do agente?

Para visualização de dependência baseada em agentes:

- Utilize um [script para instalar o agente Dependency](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent).
- Para MMA, [utilize a linha de comando ou automação,](../azure-monitor/agents/log-analytics-agent.md#installation-options)ou utilize um [script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- Além dos scripts, pode utilizar ferramentas de implementação como o Microsoft Endpoint Configuration Manager e [o Intigua](https://www.intigua.com/intigua-for-azure-migration) para implementar os agentes.

## <a name="what-operating-systems-does-mma-support"></a>Que sistemas operativos suporta o MMA?

- Veja a lista de [sistemas operativos Windows que o MMA suporta.](../azure-monitor/agents/log-analytics-agent.md#installation-options)
- Veja a lista dos [sistemas operativos Linux que o MMA suporta.](../azure-monitor/agents/log-analytics-agent.md#installation-options)

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>Posso visualizar as dependências por mais de uma hora?

Para visualização baseada em agentes, pode visualizar dependências até uma hora. Pode voltar até um mês até uma data específica na história, mas a duração máxima para visualização é de uma hora. Por exemplo, você pode usar a duração do tempo no mapa de dependência para ver dependências para ontem, mas você pode ver dependências apenas por uma janela de uma hora. No entanto, pode utilizar registos do Azure Monitor para [consultar dados de dependência](./how-to-create-group-machine-dependencies.md) por uma duração mais longa.

Para visualização sem agente, pode ver o mapa de dependência de um único servidor a partir de uma duração entre uma hora e 30 dias.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-servers"></a>Posso visualizar dependências para grupos de mais de 10 servidores?

Pode [visualizar dependências](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) para grupos que tenham até 10 servidores. Se tiver um grupo com mais de 10 servidores, recomendamos que divida o grupo em grupos mais pequenos e, em seguida, visualize as dependências.

## <a name="next-steps"></a>Passos seguintes

Leia a visão geral do [Azure Migrate](migrate-services-overview.md).