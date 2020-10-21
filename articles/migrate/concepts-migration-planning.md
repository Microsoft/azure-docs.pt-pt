---
title: Construir plano de migração com Azure Migrate Microsoft Docs
description: Fornece orientação para construir o seu plano de migração com Azure Migrate.
author: rashi-ms
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/21/2020
ms.author: rajosh
ms.openlocfilehash: ce6f0ff97b1dfa8757b05b13dff653a1d34f4531
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92315297"
---
# <a name="build-migration-plan-with-azure-migrate"></a>Construir plano de migração com Azure Migrate

Este artigo fornece um guia rápido para ajudar a construir o seu plano de migração para Azure com [Azure Migrate.](migrate-services-overview.md)Se tiver outras questões, verifique estes recursos:

- [Perguntas gerais](resources-faq.md) sobre Azure Migrate
- Perguntas sobre o [aparelho Azure Migrate](common-questions-appliance.md)
- Perguntas sobre [migração de servidores](common-questions-server-migration.md)
- Obtenha perguntas respondidas no [fórum Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="define-the-goals-of-cloud-migration"></a>Definir os objetivos da migração em nuvem

Antes de elaborar um plano de migração, é importante compreender e avaliar a [motivação](/azure/cloud-adoption-framework/strategy/motivations) para passar para a nuvem que pode ajudar a produzir resultados de negócio mais bem-sucedidos. Como explica o Quadro de Adoção de [Nuvens para O Azure,](/azure/cloud-adoption-framework) pode haver diferentes gatilhos e abordagens de migração adequadas para o seu negócio:  

**Eventos de negócios críticos** | **Resultado da migração**
--- | ---
Saída do Datacenter | Poupança de custos
Fusão, aquisição ou alienação | Redução do fornecedor ou complexidade técnica
Redução das despesas de capital | Otimização das operações internas
Fim do apoio às tecnologias críticas da missão | Aumento da agilidade empresarial
Resposta às alterações de conformidade regulamentar | Preparação para novas capacidades técnicas
Novos requisitos de soberania de dados | Dimensionamento para satisfazer as exigências do mercado
Redução das perturbações e melhoria da estabilidade das TI | Dimensionamento para satisfazer exigências geográficas

A sua motivação migratória também pode ajudá-lo a refletir sobre os objetivos estratégicos e os resultados que gostaria de alcançar migrando para Azure. O próximo passo é identificar e planear um caminho de migração para Azure que seja adaptado para as suas cargas de trabalho. Azure Migrate: A ferramenta de avaliação do servidor ajuda-o a avaliar as cargas de trabalho no local e fornece orientação e ferramentas para o ajudar a migrar.

## <a name="understand-your-digital-estate"></a>Compreenda o seu espólio digital

Comece por compreender as suas infraestruturas, aplicações e dependências no local para ajudar a identificar cargas de trabalho que deseja migrar para Azure e obter projeções de custos otimizadas. A ferramenta de avaliação do servidor irá ajudá-lo a responder às seguintes perguntas:

### <a name="what-workloads-are-in-use"></a>Que cargas de trabalho estão a ser utilizadas?

Utilize o aparelho Azure Migrate leve para realizar uma descoberta sem agentes dos seus VMS VMware no local, VMs hiper-V e servidores físicos. A descoberta contínua recolhe a configuração da máquina e metadados de desempenho e também pode ser usada para obter o inventário de aplicações instaladas, e funções/funcionalidades em execução nas suas máquinas no local. O aparelho Azure Migrate recolhe:

- Detalhes dos metadados das máquinas, discos e NICs

- Aplicações instaladas, incluindo aplicações e funções/funcionalidades  

- Dados de desempenho, incluindo CPU e utilização da memória, IOPS de disco e produção

Em seguida, exporte a lista de inventário de aplicações para descobrir todas as instâncias do SQL Server que estão a correr nas suas cargas de trabalho e utilize a ferramenta Azure Migrate: Database Assessment para entender a sua prontidão.

 ![Inventário de aplicações no Portal](./media/concepts-migration-planning/application-inventory-portal.png)

 ![Exportação de inventário de aplicações](./media/concepts-migration-planning/application-inventory-export.png)

Juntamente com os dados de descoberta da ferramenta de Avaliação do Servidor, utilize os dados CMDB existentes para construir a nossa visão do seu servidor e propriedade de base de dados e compreenda a distribuição de servidores através de unidades de negócio, proprietários de aplicações, geografias, etc., o que por sua vez pode ajudar a priorizar as cargas de trabalho a migrar.

### <a name="what-dependencies-exist-between-workloads"></a>Que dependências existem entre cargas de trabalho?

Assim que descobrir os seus servidores, utilize mapeamento de dependência sem agente para visualizar e identificar dependências de servidores cruzados e estratégias de otimização para mover servidores interdependentes para a Azure. A visualização ajuda a entender se certas máquinas estão a ser utilizadas ou se podem ser desativadas em vez de serem migradas.  Certifique-se de que analisa as dependências para garantir que nada seja deixado para trás e evite interrupções surpresa durante a migração. Uma vez realizado o inventário de aplicações e o mapeamento de dependência, pode criar grupos de alta confiança e começar a avaliar os seus servidores.

 ![Mapeamento de dependência](./media/concepts-migration-planning/expand-client-group.png)

### <a name="are-they-optimized-and-sized-properly"></a>São otimizados e dimensionados adequadamente?

Como o Azure proporciona flexibilidade para redimensionar a sua capacidade de nuvem ao longo do tempo, a migração é uma oportunidade para otimizar o CPU e os recursos de memória alocados aos seus servidores. Crie uma avaliação sobre o grupo identificado anteriormente para entender o histórico de desempenho das cargas de trabalho, o que será muito crucial nas recomendações de VM SKUs e discos sobre Azure.

## <a name="assess-your-readiness-for-migration"></a>Avalie a sua prontidão para a migração

### <a name="readiness-and-suitability-analysis-for-azure"></a>Análise de prontidão e adequação para a Azure
Exportar o relatório de avaliação do Azure VM e filtrar pelas seguintes categorias de prontidão para compreender a prontidão em VM para o Azure:

- **Ready for Azure**: Pode migrar estas máquinas como está para Azure sem alterações  

- **Condicionalmente pronto para O Azure**: Pode migrar estas máquinas para Azure, mas vai precisar de pequenas alterações nestes servidores de acordo com a orientação de remediação fornecida na avaliação

- **Não está pronto para o Azure**: Não é possível migrar estas máquinas para Azure como está e precisa corrigir os problemas de acordo com a orientação de remediação antes da migração

- **Prontidão desconhecida**: A Azure Migrate não consegue determinar a prontidão da máquina devido a metadados insuficientes

Utilizando as avaliações da base de dados, pode avaliar a prontidão para migrar a sua propriedade de dados SQL Server para Azure SQL Database ou Azure SQL Managed Instances. Pode ver a percentagem de estado de prontidão de migração para cada uma das suas instâncias de servidor SQL. Além disso, para cada um dos casos, pode ver o alvo recomendado em Azure, potenciais bloqueadores de migração, quebra de alterações contando, prontidão para Azure SQL DB / Azure SQL VM, e nível de compatibilidade. Você pode cavar mais fundo para entender o impacto dos bloqueadores de migração e recomendações para corrigi-los.

 ![Avaliações de bases de dados](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>Recomendações de dimensionamento

Depois de a máquina estar marcada como pronta para o Azure, a Avaliação do Servidor faz recomendações de dimensionamento para identificar o Azure VM e o disco SKU para os seus VMs. Pode optar por ver a recomendação de dimensionamento com base no histórico de desempenho (para que esteja a otimizar os recursos à medida que migra), ou como base na sua configuração no local sem considerar o histórico de desempenho. Para bases de dados, pode ver as recomendações em torno da base de dados SKU, nível de preços e nível de cálculo na sua avaliação de base de dados.  

### <a name="compute-assessments-to-get-estimated-costs-for-running-the-workloads-in-azure"></a>Avaliações computacional para obter custos estimados para a execução das cargas de trabalho em Azure

A opção de dimensionamento certo *baseada no desempenho* nas avaliações permite-lhe otimizar cargas de trabalho para o Azure. Além da direito à direito, existem poucas outras vias para ajudar a poupar custos:

- **Instâncias Reservadas**: Com Instâncias Reservadas, pode reduzir significativamente os custos em comparação com os preços de pagamento com prazos de 1 ano ou 3 anos no Windows e Linux (VMs)

- **Benefícios Híbridos Azure**: Pode trazer licenças do Windows Server no local com a Software Assurance para a Azure e combiná-lo com opções de instâncias reservadas

- **Oferta do Acordo Empresarial (EA)**: O Acordo Empresarial oferece poupanças incorporadas aplicáveis à sua subscrição

- **Ofertas**: Existem várias Ofertas Azure, por exemplo, Pay-As-You-Go Dev/Test e Enterprise Dev/Test que fornece tarifas mais baixas para Dev ou Test VMs

- **Tempo de uptime VM**: pode mencionar a duração em dias por mês e horas por dia quando os VMs Azure funcionarão para reduzir os seus custos (não aplicável a RI)

- **Região-Alvo**: Pode criar múltiplas avaliações em diferentes regiões para comparar se migrar para uma determinada região numa geografia pode ser mais rentável

- **Recomendações baseadas no desempenho**: Como uma boa prática, tente usar recomendações de VM Azure com direitos que o ajudarão a poupar custos na nuvem

### <a name="visualize-data"></a>Visualizar os dados

Pode ver o relatório de Avaliação do Servidor com prontidão e distribuição mensal de custos no portal, bem como exportar a avaliação para aplicar mais visualizações nos dados de descoberta e avaliação para tornar o plano de migração mais rico. Pode criar múltiplas avaliações para diferentes combinações de propriedades e escolher o conjunto de imóveis que funcionam melhor para o seu negócio.  

 ![Descrição geral das avaliações](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gaps-and-potential-blockers"></a>Avaliar lacunas e potenciais bloqueadores

Ao mesmo tempo que determina as aplicações e infraestruturas subjacentes para migrar, identifique os constrangimentos de tempo de inatividade para estas aplicações, e procure quaisquer dependências operacionais, entre as suas aplicações e a infraestrutura subjacente. Esta análise pode ajudá-lo a planear migrações que cumpram o seu objetivo de tempo de recuperação (RTO) e garantir a perda mínima de dados a zero. Antes de migrar, recomendamos que reveja e mitigue quaisquer problemas de compatibilidade ou funcionalidades não apoiadas que possam bloquear a migração dos seus servidores e bases de dados SQL com a ajuda do relatório de Avaliação do Servidor e do Azure Migrate: Recomendações de avaliação da base de dados.

### <a name="first-workloads-to-target-and-approach"></a>Primeiras cargas de trabalho para direcionar e aproximar

Agora que tem toda a informação crucial para tomar a sua decisão de migração, deve priorizar quais aplicações e cargas de trabalho devem ser migradas primeiro. Desenvolva uma abordagem de "aplicar e aprender" para migrar as suas aplicações pretendidas de forma sistemática e controlável para que possa resolver quaisquer falhas nesta estratégia antes de embarcar numa migração em larga escala. Também pode utilizar fatores estratégicos como - complexidade e tempo para migrar, urgência empresarial, ambiente de produção/não produção, conformidade e requisitos de segurança, conhecimentos de aplicações, etc. para priorizar quais grupos de aplicação migrar.

Poucas estratégias de migração recomendadas são:

- **Priorize as suas vitórias rápidas**: Pode utilizar os relatórios de avaliação para identificar frutas de baixa suspensão, incluindo os servidores e bases de dados que estão totalmente prontos e exigem o mínimo esforço para migrar para Azure:
    - Azure Ready: Exporte o seu relatório de avaliação e filtre todas as máquinas que estejam "Prontas para O Azure". Este pode ser o seu primeiro grupo de máquinas que pode levantar e deslocar com a ferramenta Azure Migrate: Server Migration.
    - Fim do suporte do OS: Exporte o seu relatório de avaliação e filtre todas as máquinas que estão a executar os sistemas operativos Windows Server 2008 e Windows Server 2008 R2. Estes SKUs são o fim do suporte e apenas o Azure fornece-lhe 3 anos gratuitos de atualizações de segurança quando os migra para Azure. Quando você combinar, Azure Hybrid Benefit e usar Instâncias Reservadas, a poupança poderia ser muito maior.
    - Migração do servidor SQL: Utilize as recomendações de avaliação da base de dados para migrar as bases de dados prontas para as bases de dados Azure SQL utilizando o Azure Migrate: Migração de bases de dados e as bases de dados prontas para O VM Azure SQL utilizando o Azure Migrate: Migração do servidor.
    - Fim do suporte do software: Exporte o inventário da sua aplicação e filtre qualquer um dos softwares/extensões que possam estar a atingir o fim do suporte. Devia dar prioridade a estas candidaturas.
    - VMs sobre-a provisionados: Exporte o seu relatório de avaliação e filtre as máquinas com baixa utilização do CPU (%) e utilização da memória (%).  Você pode usar esta oportunidade para migrar para um VM ressartado em Azure e salvar o que você tem pago por recursos subutilizados.
    - Restrições de capacidade: Exporte o seu relatório de avaliação e filtre máquinas com elevada utilização de CPU (%) e utilização da memória (%).  Pode evitar que os VMs sobretreinados quebram e aumentem o desempenho migrando-os para Azure e utilize a capacidade de auto-dimensionamento para satisfazer a procura. Também pode olhar para o relatório de avaliação para entender as suas restrições de armazenamento, analisando o disco IOPS e a produção e encontrando o tipo de disco recomendado que melhor se adapte às suas necessidades.

- **Comece pequeno e depois vá em grande**: Comece com aplicações móveis e cargas de trabalho que são de risco mínimo e menos complexas, para criar confiança na sua estratégia de migração. Também pode intersectar as suas recomendações de avaliação do Azure Migrate com o repositório CMDB das suas organizações para encontrar e migrar cargas de trabalho ambientais Dev/Test nas suas migrações piloto. As aprendizagens destes pilotos podem ser utilizadas na migração das cargas de trabalho de produção.  

- **Cumprir os seus requisitos regulamentares/industriais**: A Azure mantém a maior carteira de conformidade em todo o setor, em termos de amplitude e profundidade de ofertas. Use isto como uma oportunidade para priorizar as suas migrações para Azure e cumprir com os seus padrões e leis nacionais, regionais e específicos da indústria. Isto é especialmente verdade para organizações que lidam com informações críticas às empresas ou que possuem informações sensíveis ou que se encontram em indústrias fortemente regulamentadas, onde as normas e regulamentos abundam e, em certos casos, podem mudar frequentemente, dificultando o seu atenção.  

## <a name="finalize-the-migration-planandprepare-formigration"></a>Finalizar o plano de migração e preparar-se para a migração

Antes de finalizar o seu plano de migração, certifique-se de que estas principais considerações de migração não são um obstáculo ao seu planeamento migratório:

- Avalie os constrangimentos da largura de banda da rede e da latência que podem causar atrasos imprevistos e perturbar a velocidade de replicação da migração.

- Tampão a tempo de realizar testes de desempenho e aceitação do utilizador nas aplicações migradas ou executar quaisquer ajustes de aplicações pós-migração, tais como atualizar cadeias de conexão de base de dados e configurações de servidor web, executar cutover e limpeza, etc.

- Reveja as permissões recomendadas do Azure e as funções e permissões de acesso ao servidor/base de dados necessários para a migração.

- Prepare a sua organização e garanta o alinhamento da força de trabalho para a transformação digital. Uma base de formação sólida é importante para uma mudança organizacional bem sucedida. Confira a formação gratuita disponível no [Microsoft Learn,](/learn/azure/?ocid=CM_Discovery_Checklist_PDF)incluindo cursos sobre fundamentos Azure, arquitetura de soluções e segurança. Encoraje a sua equipa a explorar também a [certificação Azure.](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF)    

- Obtenha apoio para a sua implementação, se necessário. Muitas organizações optam por ajuda externa para apoiar a sua migração em nuvem. Para se deslocar para Azure de forma rápida e confiante com assistência personalizada, considere um [Fornecedor de Serviços Geridos Azure Expert](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF)ou    [FastTrack para a Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF).  

Crie um plano eficaz de migração em nuvem que inclua informações detalhadas sobre a lista/grupos de aplicações que pretende migrar, a disponibilidade da sua app e a disponibilidade de bases de dados e as restrições de tempo de inatividade e os marcos de migração desejados. O seu plano de migração também deve ter em conta o tempo que a cópia de dados irá demorar e incluir um tampão atencioso para testes pós-migração e atividades de corte. Os testes pós-migração devem incluir casos funcionais, de integração, segurança e testes de desempenho para garantir que as aplicações migradas funcionam como esperado e todos os objetos de base de dados e relações de dados foram transferidos com sucesso para a Cloud.  

Utilize esta análise para construir um roteiro de migração e declare uma janela de manutenção para migrar as suas aplicações e bases de dados com o mínimo a zero tempo de inatividade e limitar o potencial impacto operacional/negócio durante a migração.  

Recomendamos que teste e proceda sempre com a capacidade de migração de *testes* do Azure Migrate antes de iniciar migrações em larga escala para Azure. Estes dados reais irão ajudá-lo a estimar o tempo real envolvido e a fazer os ajustes necessários ao seu plano de migração. A migração de testes também proporciona uma oportunidade para descobrir quaisquer problemas potenciais com o plano de migração e corrigi-los antes da migração real.  

Assim que estiver pronto para migrar, utilize a *ferramenta de migração* de servidores da Azure Migrate e o *Serviço de Migração* de Dados da Azure Migrate para uma experiência de migração perfeita e integrada com o rastreio final. A ferramenta de migração do servidor suporta a migração de VMs e servidores alojados no local no datacenter dos clientes ou em qualquer outra nuvem privada ou pública, incluindo AWS, GCP, etc. com cerca de zero tempo de inatividade. O Azure Database Migration Service é um serviço totalmente gerido projetado para permitir migrações sem emenda de múltiplas fontes de base de dados para plataformas de dados Azure com tempo de inatividade mínimo.  

> [!NOTE]
> Para VMware VMs, a Avaliação do Servidor utiliza o sistema operativo especificado para o VM no vCenter Server para lidar com a análise do SO do hóspede. Para os VMs Linux em execução em VMware, atualmente não identifica a versão exata do núcleo do SO convidado.

## <a name="next-steps"></a>Passos seguintes

- Investigue a [jornada de migração](/azure/architecture/cloud-adoption/getting-started/migrate)em nuvem   no Quadro de Azure Cloud Adopt.
- [Começa](https://youtu.be/wFfq3YPxYHE) com o Azure Migrate.
- Crie uma avaliação para [VMware VMs](./tutorial-assess-vmware-azure-vm.md) ou [Hiper-VMs](tutorial-assess-hyper-v.md).