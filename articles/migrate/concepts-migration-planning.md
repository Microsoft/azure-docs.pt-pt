---
title: Construir um plano de migração com a Azure Migrate
description: Fornece orientação sobre a construção de um plano de migração com Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: 1c2be47060004d464003c00cbbddb3b58a136e3c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871151"
---
# <a name="build-migration-plan-with-azure-migrate"></a>Construir plano de migração com Azure Migrate

Siga este artigo para construir o seu plano de migração para Azure com [Azure Migrate.](migrate-services-overview.md) 

## <a name="define-cloud-migration-goals"></a>Definir objetivos de migração em nuvem

Antes de começar, compreender e avaliar a sua [motivação](/azure/cloud-adoption-framework/strategy/motivations) para se deslocar para a nuvem pode contribuir para um resultado de negócio de sucesso. Como explicado no Quadro de Adoção em [Nuvem,](/azure/cloud-adoption-framework)há uma série de gatilhos e resultados.   

**Evento de negócios** | **Resultado da migração**
--- | ---
Saída do Datacenter | Custo 
Fusão, aquisição ou alienação | Redução da complexidade do fornecedor/técnico
Redução das despesas de capital | Otimização das operações internas
Fim do apoio às tecnologias críticas da missão | Aumento da agilidade empresarial
Resposta às alterações de conformidade regulamentar | Preparação para novas capacidades técnicas
Novos requisitos de soberania de dados | Dimensionamento para satisfazer as exigências do mercado
Redução das perturbações e melhorias na estabilidade das TI | Dimensionamento para satisfazer exigências geográficas

Identificar a sua motivação ajuda-o a fixar os seus objetivos estratégicos de migração. O próximo passo é identificar e planear um caminho de migração adaptado para as suas cargas de trabalho. A ferramenta [Azure Migrate: Discovery and Assessment](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) ajuda-o a avaliar as cargas de trabalho no local e fornece orientação e ferramentas para o ajudar a migrar.

## <a name="understand-your-digital-estate"></a>Compreenda o seu espólio digital

Comece por identificar a sua infraestrutura, aplicações e dependências no local. Isto ajuda-o a identificar cargas de trabalho para a migração para Azure e a recolher projeções de custos otimizadas. A ferramenta Discovery e assessment ajuda-o a identificar as cargas de trabalho que tem em uso, dependências entre cargas de trabalho e otimização da carga de trabalho.

### <a name="workloads-in-use"></a>Cargas de trabalho em uso

A Azure Migrate utiliza um aparelho Azure Migrate leve para realizar a descoberta sem agentes de VMware VMware, VMs hiper-V, outros servidores virtualizados e servidores físicos. A descoberta contínua recolhe informações de configuração do servidor e metadados de desempenho, bem como dados de aplicação. Aqui está o que o aparelho recolhe dos servidores no local: 

- Servidor, disco e metadados NIC.

- Aplicações instaladas, funções e funcionalidades.

- Dados de desempenho, incluindo CPU e utilização da memória, IOPS de disco e produção.

Depois de recolher dados, pode exportar a lista de inventário de aplicações para encontrar aplicações e casos de SQL Server em execução nos seus servidores. Pode utilizar a ferramenta Azure Migrate: Database Assessment para compreender a prontidão do SQL Server.

 ![Inventário de aplicações no Portal](./media/concepts-migration-planning/application-inventory-portal.png)

 ![Exportação de inventário de aplicações](./media/concepts-migration-planning/application-inventory-export.png)

Juntamente com os dados descobertos com a ferramenta Discovery e assessment, pode utilizar os dados da Sua Base de Dados de Gestão de Configuração (CMDB) para construir uma visão do seu servidor e propriedade de base de dados, e para entender como os seus servidores são distribuídos por unidades de negócio, proprietários de aplicações, geografias, etc. Isto ajuda a decidir quais as cargas de trabalho a priorizar para a migração. 

### <a name="dependencies-between-workloads"></a>Dependências entre cargas de trabalho

Após a descoberta do servidor, pode [analisar dependências](concepts-dependency-visualization.md), visualizar e identificar dependências de servidores cruzados, e estratégias de otimização para mover servidores interdependentes para Azure. A visualização ajuda a entender se certos servidores estão a ser utilizados ou se podem ser desativados, em vez de serem migrados.  Analisar as dependências ajuda a garantir que nada seja deixado para trás e a surpreender as interrupções durante a migração. Com o inventário da sua aplicação e análise de dependência feita, pode criar grupos de alta confiança de servidores e começar a avaliá-los.

 ![Mapeamento de dependência](./media/concepts-migration-planning/expand-client-group.png)

### <a name="optimization-and-sizing"></a>Otimização e dimensionamento

O Azure proporciona flexibilidade para redimensionar a sua capacidade de nuvem ao longo do tempo, e a migração proporciona-lhe uma oportunidade para otimizar o CPU e os recursos de memória alocados aos seus servidores. Criar uma avaliação nos servidores que tem identidade ajuda-o a compreender o seu histórico de desempenho da carga de trabalho. Isto é crucial para o tamanho certo Azure VM SKUs, e recomendações de disco em Azure.

## <a name="assess-migration-readiness"></a>Avaliar a prontidão migratória


### <a name="readinesssuitability-analysis"></a>Análise de prontidão/adequação

Pode exportar o relatório de avaliação e filtrar estas categorias para entender a prontidão do Azure:

- **Pronto para O Azure**: Os servidores podem ser migrados como está para Azure, sem alterações. 
- **Condicionalmente pronto para Azure**: Os servidores podem ser migrados para Azure, mas precisam de pequenas alterações, de acordo com as orientações de reparação fornecidas na avaliação.
- **Não está pronto para o Azure:** Os servidores não podem ser migrados para Azure como está. As questões devem ser corrigidas de acordo com as orientações de reparação, antes da migração. 
- **Prontidão desconhecida**: A Azure Migrate não pode determinar a prontidão do servidor, devido a metadados insuficientes.

Utilizando avaliações de bases de dados, pode avaliar a prontidão da sua propriedade de dados do SQL Server para migração para Azure SQL Database, ou Azure SQL Managed Instances. A avaliação mostra percentagem de estado de prontidão de migração para cada uma das suas instâncias de servidor SQL. Além disso, para cada instância pode ver o alvo recomendado em Azure, potenciais bloqueadores de migração, uma contagem de alterações de rutura, prontidão para Azure SQL DB ou Azure SQL VM, e um nível de compatibilidade. Você pode cavar mais fundo para entender o impacto dos bloqueadores de migração, e recomendações para corrigi-los.

 ![Avaliações de bases de dados](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>Recomendações de dimensionamento

Depois de um servidor estar marcado como pronto para o Azure, o Discovery e a avaliação fazem recomendações de dimensionamento que identificam o Azure VM SKU e o tipo de disco para os seus servidores. Pode obter recomendações de dimensionamento com base no histórico de desempenho (para otimizar recursos à medida que migra), ou com base em configurações de servidores no local, sem histórico de desempenho. Numa avaliação de base de dados, pode ver recomendações para a base de dados SKU, nível de preços e nível de cálculo.  

### <a name="get-compute-costs"></a>Obtenha custos de computação

A opção de dimensionamento baseada no desempenho nas avaliações da Azure Migrate ajuda-o a obter VMs de tamanho certo, e deve ser usado como uma melhor prática para otimizar cargas de trabalho em Azure. Além do tamanho certo, existem algumas outras opções para ajudar a poupar custos do Azure: 

- **Instâncias reservadas**: Com [instâncias reservadas (RI)](https://azure.microsoft.com/pricing/reserved-vm-instances/), pode reduzir significativamente os custos em comparação com os preços de pagamento à [medida que vai.](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)
- **Azure Hybrid Benefit**: Com [Azure Hybrid Benefit,](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)pode trazer para o local licenças do Windows Server com subscrições ativas de Software Assurance, ou Linux, para a Azure, e combinar com opções de instâncias reservadas.
- **Acordo de** Empresa : Os [Acordos empresariais Azure (EA)](../cost-management-billing/manage/ea-portal-agreements.md) podem oferecer poupanças para subscrições e serviços da Azure.
- **Ofertas**: Existem [várias Ofertas Azure](https://azure.microsoft.com/support/legal/offer-details/). Por exemplo, [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/pricing/dev-test/), ou [oferta de Dev/Test da Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/), para fornecer tarifas mais baixas para VMs dev/teste
- **Tempo de funcionamento VM**: Pode rever dias por mês e horas por dia em que os VMs Azure funcionam. Desligar os servidores quando não estão a ser utilizados pode reduzir os seus custos (não aplicável às RIs).
- **Região-alvo**: Pode-se criar avaliações em diferentes regiões, para perceber se a migração para uma região específica pode ser mais rentável. 

### <a name="visualize-data"></a>Visualizar os dados

Pode ver relatórios de Descoberta e avaliação (com informações de prontidão do Azure e distribuição mensal de custos) no portal. Também pode exportar avaliação e enriquecer o seu plano de migração com visualizações adicionais. Pode criar múltiplas avaliações, com diferentes combinações de imóveis, e escolher o conjunto de imóveis que funcionam melhor para o seu negócio.  

 ![Descrição geral das avaliações](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gapsblockers"></a>Avaliar lacunas/bloqueadores

À medida que descobre as apps e cargas de trabalho que pretende migrar, identifique constrangimentos de tempo de inatividade para elas e procure quaisquer dependências operacionais entre as suas apps e a infraestrutura subjacente. Esta análise ajuda-o a planear migrações que cumpram o seu objetivo de tempo de recuperação (RTO), e garantir a perda mínima de dados a zero. Antes de migrar, recomendamos que reveja e mitigue quaisquer problemas de compatibilidade, ou funcionalidades não apoiadas, que possam bloquear a migração da base de dados do servidor/SQL. O relatório de avaliação e descoberta de Azure Migrate, e a avaliação da base de dados Azure Migrate, podem ajudar nisto. 

### <a name="prioritize-workloads"></a>Priorizar cargas de trabalho

Depois de recolher informações sobre o seu inventário, pode identificar quais apps e cargas de trabalho para migrar primeiro. Desenvolva uma abordagem de "aplicar e aprender" para migrar aplicações de forma sistemática e controlável, para que possa resolver quaisquer falhas antes de iniciar uma migração em larga escala.

Para priorizar a ordem de migração, pode utilizar fatores estratégicos como complexidade, tempo para migrar, urgência de negócios, considerações de produção/não produção, conformidade, requisitos de segurança, conhecimentos de aplicações, etc. 

Algumas recomendações:

- **Priorize vitórias rápidas**: Utilize os relatórios de avaliação para identificar os frutos de baixa suspensão, incluindo servidores e bases de dados que estão totalmente prontos, e requer o mínimo de esforço para migrar para Azure. A mesa resume algumas maneiras de fazer isto.

    **Estado** | **Ação**
    --- | ---
    **VMs prontos de azul** | Exporte o relatório de avaliação e filtre todos os servidores com estado *Pronto para Azure*. Este pode ser o primeiro grupo de servidores que levanta e muda para Azure, utilizando a ferramenta [Azure Migrate: Server Migration.](migrate-services-overview.md#azure-migrate-server-migration-tool)
    **Sistemas operativos de fim de suporte** | Exporte o relatório de avaliação e filtre todos os servidores que executam o Windows Server 2008 R2/Windows Server 2008. Estes sistemas operativos estão no fim do suporte, e apenas o Azure fornece três anos gratuitos de atualizações de segurança quando os migra para Azure. Se combinar o Azure Hybrid Benefit e utilizar RIs, as poupanças poderão ser muito mais elevadas.
    **Migração do Servidor SQL** | Utilize as recomendações de avaliação da base de dados para migrar bases de dados prontas para a Base de Dados Azure SQL, utilizando a ferramenta Azure Migrate: Database Migration. Migrar as bases de dados prontas para O Azure SQL VM utilizando a ferramenta Azure Migrate: Server Migration.
    **Software de fim de suporte** | Exporte o seu inventário de aplicações e filtre por qualquer software/extensões que possam estar a atingir o fim do suporte. Priorizar estes pedidos de migração.
    **Servidores sub-a provisionados** | Exportar o relatório de avaliação e filtrar para servidores com baixa utilização do CPU (%) e utilização da memória (%).  Migrar para um Azure VM de tamanho certo, e economizar em custos para recursos subutilizados.
    **Servidores sobre-a provisionados** | Exportar o relatório de avaliação e filtro para servidores com elevada utilização de CPU (%) e utilização da memória (%).  Resolver constrangimentos de capacidade, evitar que os servidores sobretreinados se rompam e aumentem o desempenho migrando estes servidores para Ozure. Em Azure, utilize capacidades de auto-cálculo para satisfazer a procura.<br/><br/> Analise relatórios de avaliação para investigar as restrições de armazenamento. Analise o IOPS do disco e a produção, e o tipo de disco recomendado.

- **Comece pequeno, depois vá em grande**: Comece por mover apps e cargas de trabalho que apresentem o mínimo de risco e complexidade, para criar confiança na sua estratégia de migração. Analise as recomendações de avaliação da Azure Migrate juntamente com o seu repositório CMDB, para encontrar e migrar cargas de trabalho dev/teste que possam ser candidatos a migrações-piloto. O feedback e as aprendizagens das migrações-piloto podem ser úteis à medida que se começa a migrar cargas de trabalho de produção.  
- **Cumprir**: A Azure mantém a maior carteira de conformidade do setor, em termos de amplitude e profundidade de ofertas. Utilize requisitos de conformidade para priorizar as migrações, de modo a que as aplicações e cargas de trabalho cumpram os seus padrões e leis nacionais, regionais e específicos da indústria. Isto é especialmente verdade para organizações que lidam com processos críticos de negócios, detêm informações sensíveis ou estão em indústrias fortemente regulamentadas. Neste tipo de organizações, abundam normas e regulamentos, e podem mudar muitas vezes, sendo difíceis de acompanhar.  

## <a name="finalize-the-migration-plan"></a>Finalizar o plano de migração

Antes de finalizar o seu plano de migração, certifique-se de que considera e atenua outros potenciais bloqueadores, da seguinte forma: 

- **Requisitos da rede**: Avaliar as restrições de largura de banda da rede e de latência, que podem causar atrasos imprevistos e perturbações na velocidade de replicação da migração.
- **Ajustes de teste/pós-migração**: Permita que um tampão de tempo realize testes de desempenho e aceitação do utilizador para aplicações migradas, ou para configurar/ajustar aplicações pós-migração, tais como atualizar cadeias de ligação de base de dados, configurar servidores web, realizar cortes/limpezas, etc.
- **Permissões**: Rever permissões recomendadas do Azure e funções de acesso ao servidor/base de dados e permissões necessárias para a migração.
- **Formação**: Prepare a sua organização para a transformação digital. Uma base de formação sólida é importante para uma mudança organizacional bem sucedida. Confira a formação gratuita no [Microsoft Learn,](/learn/azure/?ocid=CM_Discovery_Checklist_PDF)incluindo cursos sobre fundamentos do Azure, arquiteturas de soluções e segurança. Encoraje a sua equipa a explorar [as certificações Azure.](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF)  
- **Suporte para a implementação:** Obtenha apoio para a sua implementação se precisar. Muitas organizações optam por ajuda externa para apoiar a sua migração em nuvem. Para se mudar para a Azure de forma rápida e confiante com assistência personalizada, considere um [Fornecedor de Serviços Geridos Azure Expert](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF), ou [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF).  


Crie um plano eficaz de migração em nuvem que inclua informações detalhadas sobre as aplicações que pretende migrar, disponibilidade de apps/bases de dados, restrições de tempo de inatividade e marcos de migração. O plano considera quanto tempo a cópia de dados vai demorar, e inclui um tampão realista para testes pós-migração, e atividades de corte. 

Um plano de testes pós-migração deve incluir testes funcionais, de integração, segurança e testes de desempenho e utilização de casos, para garantir que as aplicações migradas funcionam como esperado, e que todos os objetos de base de dados, e relações de dados, são transferidos com sucesso para a nuvem.  

Construa um roteiro de migração e declare uma janela de manutenção para migrar as suas apps e bases de dados com o mínimo de tempo de inatividade e limitar o potencial impacto operacional e empresarial durante a migração.  

## <a name="migrate"></a>Migrate

Recomendamos que faça uma migração de teste em Azure Migrate, antes de iniciar uma migração em larga escala. Uma migração de testes ajuda-o a estimar o tempo envolvido e a ajustar o seu plano de migração. Proporciona uma oportunidade para descobrir quaisquer problemas potenciais, e corrigi-los antes da migração completa.

Quando estiver pronto para a migração, utilize a ferramenta Azure Migrate: Server Migration e o Azure Data Migration Service (DMS), para uma experiência de migração perfeita e integrada, com rastreio de ponta a ponta.

- Com a ferramenta de migração do servidor, pode migrar no local VMs e servidores, ou VMs localizados em outra nuvem privada ou pública (incluindo AWS, GCP) com cerca de zero tempo de inatividade.
- O Azure DMS fornece um serviço totalmente gerido que é projetado para permitir migrações sem emenda de várias fontes de base de dados para plataformas de Dados Azure, com tempo de inatividade mínimo.  

## <a name="next-steps"></a>Passos seguintes

- Investigue a [jornada de migração](/azure/architecture/cloud-adoption/getting-started/migrate)em nuvem   no Quadro de Azure Cloud Adopt.
- Obtenha uma [visão geral rápida](migrate-services-overview.md) de Azure Migrate, e assista a um [vídeo em início](https://youtu.be/wFfq3YPxYHE).
- Saiba mais sobre a avaliação de VMs para migração para [Azure VMs](concepts-assessment-calculation.md).
