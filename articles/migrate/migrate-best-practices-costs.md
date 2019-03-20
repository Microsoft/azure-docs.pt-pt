---
title: Melhores práticas para o orçamento e dimensionamento de cargas de trabalho migradas para o Azure | Documentos da Microsoft
description: Obtenha as práticas recomendadas para o orçamento e dimensionamento de cargas de trabalho migradas para o Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: raynew
ms.openlocfilehash: 6f6440e12840538614b4092b173ab25ae37a68a6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58110288"
---
# <a name="best-practices-for-costing-and-sizing-workloads-migrated-to-azure"></a>Melhores práticas para dimensionamento e custos de cargas de trabalho migradas para o Azure

Como planeja e design para a migração, concentrando-se nos custos garante o sucesso de longo prazo da sua migração do Azure. Durante um projeto de migração, é fundamental que todas as equipes (Finanças, gestão, as equipas de aplicações etc) compreender os custos associados.
- Antes da migração, estimar sua migração gastos, com uma linha de base para mensais, trimestrais, e destinos de orçamento anual é essencial para o sucesso.
- Após a migração, deve otimizar os custos, monitorizar continuamente a cargas de trabalho e planear os padrões de utilização futura. Os recursos migrados podem começam como um tipo de carga de trabalho, mas desenvolver dentro de outro tipo ao longo do tempo, com base na utilização, os custos e das demandas dos negócios.

Este artigo descreve as melhores práticas para o orçamento e dimensionamento antes e após a migração.  

> [!IMPORTANT]
> As melhores práticas e opiniões descritos neste artigo baseiam-se na plataforma Azure e funcionalidades disponíveis no momento da escrita de serviço. Funcionalidades e capacidades mudam ao longo do tempo. Nem todas as recomendações pode ser aplicável para a sua implementação, por isso, selecione o que funciona melhor para si.


## <a name="before-migration"></a>Antes da migração 

Antes de mover as cargas de trabalho para a cloud, estime o custo mensal de executá-los no Azure. Gerenciamento proativo de custos na cloud ajuda-o a cumprir seu orçamento de (OpEx) as despesas operacionais. Se o orçamento é limitado, leve isso em conta antes da migração.  Considere a conversão de cargas de trabalho para tecnologias sem servidor do Azure, quando apropriado, para reduzir os custos.

As práticas recomendadas nesta secção ajudá-lo a estimar os custos, efetuar o dimensionamento certo para VMs e armazenamento, tirar partido dos benefícios híbridos do Azure, utilize VMs reservadas e estimar gastos nas subscrições da cloud.



## <a name="best-practice-estimate-monthly-workload-costs"></a>Prática recomendada: Mensalmente estimar os custos de carga de trabalho
 
Para prever a fatura mensal para cargas de trabalho migradas, há uma série de ferramentas que pode utilizar.

- **Calculadora de preços do Azure**: Selecione os produtos que pretende fazer uma estimativa, por exemplo VMs e armazenamento. Introduza os custos para a Calculadora de preços, para criar uma estimativa.

  ![Calculadora de preços do Azure](./media/migrate-best-practices-costs/pricing.png) *Calculadora de preços do Azure*

- **Azure Migrate**: Para estimar os custos, terá de rever e de conta para todos os recursos necessários para executar cargas de trabalho no Azure. Para obter estes dados, crie inventário dos seus recursos, incluindo servidores, VMs, bases de dados e armazenamento. Pode utilizar o Azure Migrate para recolher estas informações.

  - O Azure Migrate Deteta e avalia seu ambiente no local para fornecer um inventário.
  - O Azure Migrate pode mapear e mostrar as dependências entre as VMs, para que tenha uma visão completa.
  - Uma avaliação do Azure Migrate contém o custo estimado.
    - Os custos de computação: Com o tamanho de VM do Azure recomendado quando criar uma avaliação, do Azure Migrate utiliza a API de faturação para calcular os custos VM mensais estimados. A estimativa considera o sistema operativo, do software assurance, instâncias reservadas, a VM de tempo de atividade, localização e as configurações de moeda. Agrega o custo em todas as VMs na avaliação e calcula um custo mensal total de computação.
    - Custo de armazenamento: O Azure Migrate calcula os custos de armazenamento mensal total ao agregar os custos de armazenamento de todas as VMs nas avaliações. Pode calcular o custo de armazenamento mensal de uma máquina específica ao agregar o custo mensal de todos os discos ligados à mesma. 

    ![O Azure Migrate](./media/migrate-best-practices-costs/assess.png)
    *avaliações do Azure Migrate*

**Saiba mais:**
- [Utilize](https://azure.microsoft.com/pricing/calculator/) a Calculadora de preços do Azure.
- [Obtenha uma visão geral](https://docs.microsoft.com/azure/migrate/migrate-overview) do Azure Migrate.
- [Leia sobre](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation) avaliações do Azure Migrate.
- [Saiba mais](https://docs.microsoft.com/azure/dms/dms-overview) sobre o serviço de migração de base de dados (DMS).

## <a name="best-practice-right-size-vms"></a>Prática recomendada: Dimensionar VMs

Pode escolher um número de opções ao implementar VMs do Azure para suportar cargas de trabalho. Cada tipo VM tem funcionalidades específicas e diferentes combinações de CPU, memória e discos. As VMs são agrupadas da seguinte forma.

**Tipo** | **Detalhes** | **Utilizar**
--- | --- | ---
**Fins gerais** | CPU-para-memória equilibrada. | Ideal para teste e desenvolvimento, pequeno às bases de dados de médias, baixas para servidores web com tráfego médio.
**Computação otimizada** | CPU-para-memória elevada. | Ideal para o servidor de web de tráfego médio, aplicações de rede, processos em lotes, servidores de aplicações.
**Com otimização de memória** | Memória-para-CPU elevada. | Ideal para bases de dados relacionais, cache média a grandes e análise dentro da memória.
**Com otimização de armazenamento** | Débito e E/S de disco elevados. | Bom para grandes volumes de dados, bases de dados SQL e NoSQL.
**Com otimização de GPU** | VMs especializadas. GPUs únicas ou múltiplas. | Gráficos pesados e edição de vídeo.
**Elevado desempenho** | CPU mais rápida e poderosa. VMs com interfaces de rede opcional de alto débito (RDMA) | Aplicações de alto desempenho críticas.

- É importante compreender as diferenças nos preços entre estas VMs e os efeitos de orçamento de longo prazo.
- Cada tipo tem um número de série VM dentro do mesmo.
- Além disso, quando seleciona uma VM dentro de uma série, pode apenas a VM ou reduza verticalmente dentro da série. Por exemplo, um DSv2\_2 pode aumentar verticalmente para DSv2\_4, mas não pode ser alterada para uma série de diferente, como Fsv2\_2.

**Saiba mais:**
- [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) sobre tipos de VM e o dimensionamento e tamanhos de mapa para tipos.
- [Planear](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs) dimensionamento de VM.
- [Revisão](https://docs.microsoft.com/azure/migrate/contoso-migration-assessment) uma avaliação de exemplo para a empresa fictícia de Contoso.

## <a name="best-practice-select-the-right-storage"></a>Prática recomendada: Selecione o armazenamento correto

Ajuste e a manutenção de armazenamento no local (SAN ou NAS) e as redes para suportá-los, podem ser dispendiosa e demorada. Dados de ficheiros (armazenamento) normalmente são migrados para a cloud para ajudar a aliviar operacionais e dores de cabeça de gestão. A Microsoft fornece várias opções para mover dados para o Azure, e precisa para tomar decisões sobre essas opções. Escolher o tipo de armazenamento correta para dados pode salvar sua organização vários milhares de dólares por mês. Algumas considerações:

- Aceder aos dados que não seja muito e não é crítico para a empresa, não precisa ser colocados no armazenamento mais caro.
- Por outro lado, os dados críticos de negócios importantes devem estar localizados nas opções de armazenamento de escalão superior.
- Durante o planejamento de migração, fazer um inventário de dados e classificá-los por importância, para mapeá-lo para o armazenamento mais adequado. Considere o orçamento e os custos, bem como desempenho. Custo necessariamente não deve ser o principal fator de tomada de decisões. Escolher a opção menos cara poderia expor a carga de trabalho para o desempenho e os riscos de disponibilidade. 

### <a name="storage-data-types"></a>Tipos de dados de armazenamento

O Azure fornece diferentes tipos de dados de armazenamento.

| **Tipo de dados** | **Detalhes** | **Utilização** |
|--- | --- |  --- |
|**Blobs** | Otimizado para armazenar grandes quantidades de objetos não estruturados, como texto ou dados binários<br/>Aceder aos dados a partir de qualquer lugar através de HTTP/HTTPS. | Uso para cenários de acesso aleatório e transmissão em fluxo. Por exemplo, para fornecer imagens e documentos diretamente a um browser, transmita em fluxo vídeo e áudio e armazenar dados de recuperação após desastre e cópia de segurança.|
|**Ficheiros** | Partilhas de ficheiros acedidas através de SMB 3.0 geridas | Utilize quando a migração no local partilhas de ficheiros bem como para fornecer várias/ligações de acesso a dados de ficheiros.|
|**Discos** | Com base em blobs de páginas.<br/><br/> Tipo de disco (velocidade): Standard (HDD ou SSD) ou Premium (SSD).<br/><br/>Gestão de discos: Não-gerenciado (Gerir as definições do disco e armazenamento) ou gerida (selecionar o tipo de disco e Azure gere o disco por si). | Utilize os discos Premium para as VMs. Utilize discos geridos para uma gestão simples e dimensionamento.|
|**filas** | Store e obter grandes quantidades de mensagens acedidas através de chamadas autenticadas (HTTP ou HTTPS) | Componentes de aplicação conecte-se a colocação em fila de mensagens assíncronas.|
|**Tabelas** | Store tabelas. | Agora parte da API de tabela do Azure Cosmos DB.|



### <a name="access-tiers"></a>Camadas de acesso
Armazenamento do Azure oferece opções diferentes para aceder aos dados de BLOBs de bloco. Selecionar o escalão de acesso ajuda a garantir a que armazene dados de blob de blocos da maneira mais econômica.

**Tipo** | **Detalhes** | **Utilização**
--- | --- | ---
**acesso frequente** | Custo de acesso esporádico de armazenamento superior. Custos mais baixos de acesso de acesso esporádico.<br/><br/>Esta é a camada predefinida. | Utilize para os dados em utilização ativa, que é acedido com frequência.
**Acesso esporádico** | Reduza o custo de armazenamento de acesso frequente. Custos mais elevados de acesso de acesso frequente.<br/><br/> Store para o mínimo de 30 dias. | Store curto prazo, os dados estão disponíveis mas acedidos com pouca frequência.
**Arquivo** | Utilizado para blobs de blocos individuais.<br/><br/> Opção mais económica para armazenamento. Acesso a dados é mais caro que frequente e esporádica. | Utilize para dados que podem tolerar servidor horas de latência de obtenção e irão permanecer no escalão por, pelo menos, 180 dias. 

### <a name="storage-account-types"></a>Tipos de conta de armazenamento

O Azure fornece diferentes tipos de contas de armazenamento e de escalões de desempenho.

**Tipo de conta** | **Detalhes** | **Utilização**
--- | --- | ---
**Fins gerais v2 Standard** | Suporta blobs (bloco, página, acrescentar) ficheiros, discos, filas e tabelas.<br/><br/> Suporta as camadas de acesso frequente, esporádico e arquivo. O ZRS é suportado. | Utilize para a maioria dos cenários e a maioria dos tipos de dados. Contas de armazenamento Standard podem ser HDD ou baseado em SSD.
**Premium v2 de fins gerais** | Suporta dados de Blob de armazenamento (blobs de páginas). Suporta as camadas de acesso frequente, esporádico e arquivo. O ZRS é suportado.<br/><br/> Armazenados em SSD. | A Microsoft recomenda utilizar para todas as VMs.
**Fins gerais v1** | Não é suportada a criação de camadas de acesso. Não suporta o ZRS | Utilize se precisam de aplicações do modelo de implementação clássica do Azure.
**Blob** | Conta de armazenamento especializada para armazenar objetos não estruturados. Fornece os blobs de blocos e de acréscimo apenas (não existem serviços armazenamento ficheiro, fila, tabela ou disco). Fornece a mesma durabilidade, disponibilidade, escalabilidade e desempenho de fins gerais v2. | Não é possível armazenar blobs de páginas nestas contas e, portanto, não é possível armazenar ficheiros VHD. Pode definir uma camada de acesso frequente ou esporádico.

### <a name="storage-redundancy-options"></a>Opções de redundância dos dados

Contas de armazenamento podem utilizar diferentes tipos de redundância para elevada disponibilidade e resiliência.

**Tipo** | **Detalhes** | **Utilização**
--- | --- | ---
**Armazenamento localmente redundante (LRS)** | Protege contra uma falha do local através da replicação de dentro de uma unidade de armazenamento única a um domínio de falha e o domínio de atualização. Mantém várias cópias dos seus dados num único datacenter. Fornece, pelo menos, 99,999999999% (11 9\'s) a durabilidade dos objetos ao longo de um determinado ano. | Considere se a aplicação armazena os dados que podem facilmente ser reconstruídos.
**Armazenamento com redundância de zona (ZRS)** | Protege novamente uma falha do datacenter ao replicar em três clusters de armazenamento numa única região. Cada cluster de armazenamento é fisicamente separado e localizado na sua própria zona de disponibilidade. Fornece, pelo menos, 99.9999999999% (12 9\'s) a durabilidade dos objetos ao longo de um determinado ano ao manter várias cópias dos seus dados em vários datacenters ou regiões. | Considere se precisa de consistência, durabilidade e elevada disponibilidade. Não pode proteger contra um desastre regional quando várias zonas de forma permanente afetadas.
**Armazenamento geograficamente redundante (GRS)** | Protege contra uma falha de região inteira ao replicar dados para uma região secundária a centenas de quilómetros de distância da primária. Fornece, pelo menos, 99,99999999999999% (16 9\'s) a durabilidade dos objetos ao longo de um determinado ano. | Dados de réplica não estão disponíveis, a menos que a Microsoft inicie uma ativação pós-falha para a região secundária. Se ocorrer a ativação pós-falha, acesso de escrita e leitura está disponível.
**Armazenamento geograficamente georredundante com acesso de leitura (RA-GRS)** | Semelhante ao GRS. Fornece, pelo menos, 99,99999999999999% (16 9\'s) a durabilidade dos objetos ao longo de um determinado ano | Fornece e 99,99% de disponibilidade de leitura ao permitir o acesso de leitura a partir da segunda região utilizada para o GRS.

**Saiba mais:**
- [Revisão](https://azure.microsoft.com/pricing/details/storage/) preços de armazenamento do Azure.
- [Saiba mais sobre](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) importar/exportar do Azure para grandes quantidades de migração de dados para os ficheiros e blobs do Azure. 
- [Comparar](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) blobs, ficheiros e os tipos de dados de armazenamento do disco.
- [Saiba mais](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) sobre escalões de acesso.
- [Revisão](https://docs.microsoft.com/azure/storage/common/storage-account-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) diferentes tipos de contas de armazenamento.
- Saiba mais sobre [redundância de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-redundancy), [LRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), [ZRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), [GRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), e [GRS de acesso de leitura](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#read-access-geo-redundant-storage).
- [Saiba mais](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) sobre ficheiros do Azure.



## <a name="best-practice-leverage-azure-hybrid-benefits"></a>Prática recomendada: Tirar partido dos benefícios híbridos do Azure

Devido a anos de investimentos de software em sistemas, como o Windows Server e SQL Server, a Microsoft está numa posição única para oferecer aos clientes valor na cloud, com descontos substanciais que outros fornecedores de cloud não não necessariamente fornecem. 

Um portfólio de produtos no local/Azure Microsoft integrado gera vantagens competitivas e de custos. Se tiver atualmente um sistema operativo ou outros licenciamento de software através do software assurance (SA), pode tirar essas licenças com para a cloud para com o benefício híbrido do Azure.

**Saiba mais:**

- [Dê uma olhada](https://azure.microsoft.com/pricing/hybrid-benefit/) a Calculadora de poupanças do benefício híbrido.
- [Saiba mais](https://azure.microsoft.com/pricing/hybrid-benefit/) sobre o benefício híbrido para o Windows Server.
- [Revisão](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) preços orientações para VMs do Azure do SQL Server.


## <a name="best-practice-use-reserved-vm-instances"></a>Prática recomendada: Utilizar instâncias de VM reservadas

A maioria das plataformas de cloud são configuradas como pay as you go. Desvantagens de apresenta este modelo, uma vez que não saiba, necessariamente cargas de trabalho como dinâmicas será. Quando especificar intenções claras para cargas de trabalho, contribuir com o planejamento da infraestrutura.

Utilizar instâncias de VMS reservadas do Azure, paga previamente para um ou instância de VM de prazo de três anos. 

- Pré-pagamento fornece um desconto sobre os recursos que utiliza.
- Pode reduzir significativamente VM, a computação de base de dados SQL, Azure Cosmos DB ou outros custos do recurso até 72% em preços pay as you go. 
- Reservas de fornecem um desconto de faturação e não afetam o estado de tempo de execução dos seus recursos.
- Pode cancelar as instâncias reservadas.

![Instâncias reservada](./media/migrate-best-practices-costs/reserve.png)
*VMs de reservada do Azure*

**Saiba mais:**
- [Saiba mais sobre](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) reservas do Azure.
- [Leitura](https://azure.microsoft.com/pricing/reserved-vm-instances/#faq) as instâncias reservadas FAQ.
- [Obtenha preços orientações](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) para VMs do SQL Server do Azure.


## <a name="best-practice-aggregate-cloud-spend-across-subscriptions"></a>Prática recomendada: Gastos com a cloud agregada entre subscrições

É inevitável que, eventualmente, terá mais do que uma subscrição do Azure. Por exemplo, poderá ter uma subscrição adicional para separar os limites de desenvolvimento e produção, ou poderá ter uma plataforma que requer uma subscrição separada para cada cliente. Ter a capacidade de relatórios em todas as subscrições numa única plataforma de dados de agregação é um recurso valioso.

Para fazer isso, pode usar APIs de gestão de custos do Azure. Em seguida, após a agregação de dados numa única origem, tais como o SQL do Azure, pode utilizar ferramentas como o Power BI para apresentar os dados agregados. Pode criar relatórios de subscrição agregados e relatórios granulares. Por exemplo, para os utilizadores que precisam de proativa informações sobre a gestão de custos, pode criar vistas específicas dos custos, com base no departamento, grupo de recursos etc. Não terá de fornecê-los com acesso total aos dados de faturas do Azure.

**Saiba mais:**

- [Obtenha uma visão geral](https://docs.microsoft.com/azure/billing/billing-consumption-api-overview) da API de consumo do Azure.
- [Saiba mais sobre](https://docs.microsoft.com/power-bi/desktop-connect-azure-consumption-insights) ligar ao Azure Consumption Insights no Power BI Desktop.
- [Saiba como](https://docs.microsoft.com/azure/billing/billing-manage-access) gerir o acesso a informações de faturação para o Azure com o controlo de acesso baseado em funções (RBAC).
 
## <a name="after-migration"></a>Após a migração

Depois de uma migração bem-sucedida de cargas de trabalho e algumas semanas de recolha de dados de consumo, terá uma idéia clara dos custos de recursos.
- Analisar dados, pode começar a gerar uma linha de base de orçamento para grupos de recursos do Azure e recursos.
- Em seguida, compreende onde é gasto seu orçamento de cloud, pode analisar como reduzir ainda mais os custos.

Melhores práticas nesta secção incluem o uso do Azure Cost Management para o orçamento de custo e a análise, monitorização de recursos e implementar os orçamentos de grupo de recursos e otimizar a monitorização, armazenamento e as VMs.

## <a name="best-practice-use-azure-cost-management"></a>Prática recomendada: Utilizar a gestão de custos do Azure

A Microsoft fornece o Azure Cost Management para ajudar a monitorizar os gastos, da seguinte forma:

- Ajuda-o a monitorizar e controlar os gastos do Azure e otimizar a utilização de recursos.
- Analisa a sua subscrição completa e todos os respetivos recursos e faz recomendações.
- Fornece uma API completa, para integrar ferramentas externas e sistemas financeiros para relatórios.
- Controla a utilização de recursos e gerir os custos da cloud com uma única vista unificada.
- Fornece informações operacionais e financeiras diversificadas para o ajudar a tomar decisões informadas.

Gestão de custos, pode:


- **Criar um orçamento**: Crie um orçamento para atribuir responsabilidades financeiros.
  - Podem contribuir para os serviços de consumir ou subscrever para um período específico (mensalmente, trimestralmente, anualmente) e um âmbito (grupos de subscrições/recursos). Por exemplo, pode criar um orçamento de subscrição do Azure durante um período de mensais, trimestrais, ou anual.
  - Depois de criar um orçamento, ela é mostrada na análise de custos. Visualizar o seu orçamento em relação a gastos atual é uma das primeiras etapas necessárias ao analisar os custos e despesas.
  - Podem ser enviadas notificações por e-mail quando os limiares de orçamento são atingidos.
  - Pode exportar os dados de gestão de custos para o armazenamento do Azure, para análise.

    ![Orçamento de gestão de custo](./media/migrate-best-practices-costs/budget.png)
    *orçamento do Azure Cost Management*

- **Fazer uma análise de custo**: Obter uma análise de custo para explorar e analisar os seus custos organizacionais, para ajudar a compreender como são acumulados os custos e a identificar as tendências de gastos.
  - Análise de custos estão disponível para utilizadores EA.
  - Pode ver os dados de análise de custo para vários âmbitos, incluindo por departamento, conta, subscrição ou grupo de recursos.
  - Pode obter uma análise de custos mostra os custos totais para o mês atual e custos diários acumulados. 

    ![Análise de gestão de custos](./media/migrate-best-practices-costs/analysis.png)
    *análise do Azure Cost Management*
- **Obtenha recomendações**: Obtenha recomendações do assistente que lhe mostram como pode otimizar e melhorar a eficiência.


**Saiba mais:**

- [Obtenha uma visão geral](https://docs.microsoft.com/azure/cost-management/overview) do Azure Cost Management.
- [Saiba como](https://docs.microsoft.com/azure/cost-management/cost-mgt-best-practices) otimizar seu investimento na cloud com o Azure Cost Management.
- [Saiba como](https://docs.microsoft.com/azure/cost-management/use-reports) utilizar relatórios do Azure Cost Management.
- [Obter um tutorial](https://docs.microsoft.com/azure/cost-management/tutorial-acm-opt-recommendations?toc=/azure/billing/TOC.json) sobre como otimizar os custos de recomendações.
- [Revisão](https://docs.microsoft.com/rest/api/consumption/budgets) o API de consumo do Azure.

## <a name="best-practice-monitor-resource-utilization"></a>Prática recomendada: Utilização de recursos do monitor

No Azure paga o que utilizar, quando os recursos são consumidos e não paga quando não estiverem. Para as VMs, a faturação ocorre quando uma VM é alocada e não é cobrada depois de uma VM é desalocada. Com isso em mente deve monitorizar as VMs em utilização e certifique-se de dimensionamento de VM.

- Avalie continuamente suas cargas de trabalho VM para determinar as linhas de base.
- Por exemplo, se a sua carga de trabalho é utilizada intensamente segunda-feira a sexta-feira, 8 00 às 18:00, mas mal utilizada fora desse horário, poderia mudar a versão de VMs fora de pico. Isso pode significar a alteração de tamanhos de VM ou utilizar o dimensionamento de máquina virtual define como o dimensionamento automático VMs ou reduzir verticalmente.
- Algumas empresas "suspender", as VMs ao colocá-las num calendário que especifica quando deve estar disponíveis e, quando não forem necessários. 
- Para além de monitorização de VM, deve monitorar a outros recursos de rede, como o ExpressRoute e gateways de rede virtual para em e sobre a utilização.
- Pode monitorizar a utilização VM com várias ferramentas Microsoft, incluindo o Azure Cost Management, o Azure Monitor e o Assistente do Azure. Ferramentas de terceiros também estão disponíveis.  

**Saiba mais:**
- Obtenha uma visão geral dos [do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) e [do Assistente do Azure](https://docs.microsoft.com/azure/advisor/advisor-overview).
- [Obter](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations) recomendações de custos do Advisor.
- [Saiba como [otimizar os custos de recomendações](https://docs.microsoft.com/azure/cost-management/tutorial-acm-opt-recommendations?toc=/azure/billing/TOC.json), e [evitar custos inesperados](https://docs.microsoft.com/azure/billing/billing-getting-started).
- [Saiba mais sobre](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit/) o Kit de ferramentas de otimização (ARO) de recursos do Azure

## <a name="best-practice-implement-resource-group-budgets"></a>Prática recomendada: Implementar os orçamentos de grupo de recursos

Muitas vezes, os grupos de recursos são usados para representar os limites de custo. Em conjunto com este padrão de utilização, a equipa do Azure continua a desenvolver novas e aprimoradas formas de controle e analise os recursos, gastos em níveis diferentes, incluindo a capacidade de criar orçamentos no grupo de recursos e recursos.  

- Um orçamento de grupo de recursos ajuda-o a controlar os custos associados um grupo de recursos.
- Pode acionar alertas e executar uma grande variedade de playbooks, conforme o orçamento é atingido ou excedido. 

**Saiba mais:**

- [Saiba como](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario) gerir os custos com orçamentos do Azure.
- [Siga um tutorial](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?toc=/azure/billing/TOC.json) para criar e gerir um orçamento do Azure.


## <a name="best-practice-optimize-azure-monitor-retention"></a>Prática recomendada: Otimizar a retenção de Azure Monitor

À medida que move recursos para o Azure e ativar o registo de diagnóstico para eles, gerar uma grande quantidade de dados de registo. Normalmente, estes dados de registo são enviados para uma conta de armazenamento que está mapeada para uma área de trabalho do Log Analytics.

- Mais tempo o período de retenção de dados de registo, os dados mais que tenha.
- Nem todos os dados de registo é igual, e alguns recursos irão gerar mais dados de registo que outros.
- Devido a regulamentos e conformidade, é provável que precisará manter os dados de registo para alguns recursos mais tempo do que outras pessoas.
- Deve movimentar uma linha de cuidado entre otimizar os custos de armazenamento de registo bem como manter os dados de registo que tem de.
- Recomendamos avaliar e configurar o registo imediatamente depois de concluir uma migração, para que não gastar dinheiro, retendo os registos de sem importância.

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs) monitorizar a utilização e custos estimados.
 
## <a name="best-practice-optimize-storage"></a>Prática recomendada: Otimizar o armazenamento

Se tiver seguido as práticas recomendadas para a seleção de armazenamento antes da migração, são provavelmente de obter alguns benefícios. No entanto, existem os custos de armazenamento provavelmente adicional que ainda pode otimizar. Ao longo do tempo blobs e ficheiros se tornam obsoletos. Dados podem não ser mais utilizados, mas os requisitos de regulamentação podem significar que precisa mantê-lo para um determinado período. Como tal, não poderá ter de armazená-los no armazenamento de elevado desempenho que utilizou para a migração original.

Identificar e mover dados obsoletos para áreas de armazenamento mais baratas, podem ter um impacto enorme no seu orçamento de armazenamento mensal e reduções de custos. O Azure fornece várias formas de ajudar a identificar e, em seguida, armazenar estes dados obsoletos.

- Tire partido das camadas de acesso para o armazenamento para fins gerais v2, movendo dados menos importantes de acesso frequente para escalões de acesso esporádico e arquivado.
- Utilize o StorSimple para o ajudar a mover dados obsoletos com base em políticas personalizadas. 

**Saiba mais:**
- [Saiba mais](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) sobre escalões de acesso.
- [Obtenha uma descrição geral](https://docs.microsoft.com/azure/azure-monitor/overview) do StorSimple, e [preços do StorSimple](https://azure.microsoft.com/pricing/details/storsimple/).

## <a name="best-practice-automate-vm-optimization"></a>Prática recomendada: Automatizar a otimização de VM

O objetivo final de uma VM em execução na cloud é maximizar a CPU, memória e disco que utiliza. Se detetar VMs que não são otimizadas, ou ter frequentes períodos quando as VMs não são utilizadas, faz sentido para encerrá-las ou reduzir verticalmente o-los com os conjuntos de dimensionamento VM.

Pode otimizar a uma VM com a automatização do Azure, conjuntos de dimensionamento VM, encerramento automático e soluções de terceiros com script ou 3. 

**Saiba mais:**

- [Saiba como](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision) utilizar o dimensionamento automático vertical.
- [Agenda](https://azure.microsoft.com/updates/azure-devtest-labs-schedule-vm-auto-start/) um início automático VM.
- [Saiba como](https://docs.microsoft.com/azure/automation/automation-solution-vm-management) iniciar ou parar VMs fora das horas na automatização do Azure.
- [Obter mais informações] [do Assistente do Azure](https://docs.microsoft.com/azure/advisor/advisor-overview)e o [Kit de ferramentas de otimização de recursos do Azure (ARO)](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit/).

## <a name="best-practices-use-logic-apps-and-runbooks-with-budgets-api"></a>Melhores práticas: Utilize o Logic Apps e runbooks com orçamentos de API

O Azure fornece uma API REST que tem acesso às suas informações de faturação do inquilino.

- Pode usar a API de orçamentos para integrar sistemas externos e fluxos de trabalho que são acionados por métricas que criar a partir dos dados de API.
- Pode extrair dados de utilização e de recursos em suas ferramentas de análise de dados preferencial.
- As APIs de Utilização de Recursos do Azure e de RateCard podem ajudá-lo a prever e gerir os seus custos com precisão.
- As APIs são implementadas como um fornecedor de recursos e estão incluídas nas APIs expostas pelo Azure Resource Manager.
- A API de orçamentos podem ser integrada no Azure Logic Apps e Runbooks.

**Saiba mais:**

- [Saiba mais](https://docs.microsoft.com/rest/api/consumption/budgets) sobre a API de orçamentos.
- [Obtenha informações](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) na utilização do Azure com a API de faturação.


## <a name="best-practice-implement-serverless-technologies"></a>Prática recomendada: Implementar tecnologias sem servidor

Cargas de trabalho VM, muitas vezes, são migradas "como está" para evitar o tempo de inatividade. Muitas vezes, as VMs podem alojar tarefas que são intermitentes, tendo um curto período de tempo para executar, ou em alternativa muitas horas. Por exemplo, as VMs que executam as tarefas agendadas, como Windows tarefas scheduler ou de scripts do PowerShell. No entanto, quando não estão a executar estas tarefas, estiver absorver VM e os custos de armazenamento de disco da mesma.

Após a migração, após uma revisão completa desses tipos de tarefas poderá considerar migrá-las para tecnologias sem servidor, como as funções do Azure ou do Azure Batch tarefas. Com esta solução, não precisa gerenciar e manter as VMs, género, uma poupança de custos adicionais. 

**Saiba mais:**
- [Saiba mais sobre](https://azure.microsoft.com/services/functions/) as funções do Azure
- [Saiba mais sobre](https://azure.microsoft.com/en-us/services/batch/) do Azure Batch
  
## <a name="next-steps"></a>Passos Seguintes 

Reveja a outras práticas recomendadas:

- [Melhores práticas](migrate-best-practices-security-management.md) para segurança e gestão após a migração.
- [Melhores práticas](migrate-best-practices-networking.md) para funcionamento em rede após a migração.

