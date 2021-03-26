---
title: Avaliações do Azure SQL na Azure Migrate Discovery e ferramenta de avaliação
description: Saiba mais sobre avaliações do Azure SQL na Azure Migrate Discovery e ferramenta de avaliação
author: rashi-ms
ms.author: rajosh
ms.topic: conceptual
ms.date: 02/07/2021
ms.openlocfilehash: c2e739a45c7915c957ca89e5b01b98afa945d03e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557195"
---
# <a name="assessment-overview-migrate-to-azure-sql"></a>Visão geral da avaliação (migrar para Azure SQL)

Este artigo fornece uma visão geral das avaliações para a migração no local exemplos de servidor SQL de um ambiente VMware para bases de dados Azure SQL ou Instâncias Geridas usando a [ferramenta Azure Migrate: Discovery and assessment](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool)tool .

## <a name="whats-an-assessment"></a>O que é uma avaliação?
Uma avaliação com a ferramenta Discovery e assessment é um ponto no tempo instantâneo de dados e mede a prontidão e estima o efeito da migração de servidores no local para Azure.

## <a name="types-of-assessments"></a>Tipos de avaliações

Existem três tipos de avaliações que pode criar utilizando a ferramenta Azure Migrate: Discovery and assessment tool.

**Tipo de Avaliação** | **Detalhes**
--- | --- 
**VM do Azure** | Avaliações para migrar os seus servidores no local para máquinas virtuais do Azure. <br/><br/> Pode avaliar os seus servidores no local em ambiente [VMware](how-to-set-up-appliance-vmware.md) e [Hiper-V](how-to-set-up-appliance-hyper-v.md) e [servidores físicos](how-to-set-up-appliance-physical.md) para migração para VMs Azure usando este tipo de avaliação.
**SQL do Azure** | Avaliações para migrar os seus servidores SQL no local do seu ambiente VMware para Azure SQL Database ou Azure SQL Managed Instance.
**Solução VMware no Azure (AVS)** | Avaliações para migrar os seus servidores no local para o [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Pode avaliar os seus [VMS VMware](how-to-set-up-appliance-vmware.md) no local para migração para Azure VMware Solution (AVS) utilizando este tipo de avaliação. [Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)

Uma avaliação Azure SQL fornece um único critério de dimensionamento:

**Critérios de dimensionamento** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações que fazem recomendações com base nos dados de desempenho recolhidos | A configuração Azure SQL baseia-se em dados de desempenho de instâncias e bases de dados SQL, que incluem: utilização de CPU, utilização de memória, IOPS (ficheiros de dados e registos), produção e latência de operações de IO.

## <a name="how-do-i-assess-my-on-premises-sql-servers"></a>Como posso avaliar os meus servidores SQL no local?

Pode avaliar as suas instâncias no local DO SQL Server utilizando os dados de configuração e utilização recolhidos por um aparelho Azure Migrate leve. O aparelho descobre no local casos e bases de dados de servidorES SQL e envia os dados de configuração e desempenho para a Azure Migrate. [Saiba mais](how-to-set-up-appliance-vmware.md)

## <a name="how-do-i-assess-with-the-appliance"></a>Como avalio com o aparelho?
Se estiver a implantar um aparelho Azure Migrate para descobrir servidores no local, faça os seguintes passos:
1.  Instale o Azure e o seu ambiente no local para trabalhar com a Azure Migrate.
2.  Para a sua primeira avaliação, crie um projeto Azure Migrate e adicione-lhe a ferramenta Azure Migrate: Discovery and assessment.
3.  Desloque um aparelho Azure Migrate leve. O aparelho descobre continuamente servidores no local e envia dados de configuração e desempenho para a Azure Migrate. Desloque o aparelho como VM ou servidor físico. Não precisa de instalar nada nos servidores que queira avaliar.

Após o início da descoberta do aparelho, pode recolher servidores que pretende avaliar em grupo e fazer uma avaliação para o grupo com o tipo de avaliação **Azure SQL**.

Siga o nosso tutorial para avaliar as [instâncias do SQL Server](tutorial-assess-sql.md) para experimentar estes passos.

## <a name="how-does-the-appliance-calculate-performance-data-for-sql-instances-and-databases"></a>Como calcula o aparelho dados de desempenho para casos de SQL e bases de dados?

O aparelho recolhe dados de desempenho para configurações de cálculo com estes passos:
1. O aparelho recolhe um ponto de amostra em tempo real. Para os servidores SQL, é recolhido um ponto de amostra a cada 30 segundos.
2. O aparelho agrega os pontos de dados da amostra recolhidos a cada 30 segundos em 10 minutos. Para criar o ponto de dados, o aparelho seleciona os valores de pico de todas as amostras. Envia o máximo, médio e variação para cada contador para Azure.
3. A Azure Migrate armazena todos os pontos de dados de 10 minutos do último mês.
4. Ao criar uma avaliação, a Azure Migrate identifica o ponto de dados apropriado a utilizar para a utilização de direitos. A identificação baseia-se nos valores percentil para o histórico de desempenho e utilização por percentil.
    - Por exemplo, se o histórico de desempenho for de uma semana e a utilização percentil for o percentil 95, a avaliação classifica os pontos de amostra de 10 minutos da última semana. Classifica-os por ordem ascendente e escolhe o valor percentil 95 para a deposição de direitos.
    - O valor percentil 95 faz com que ignore qualquer outliers, que podem ser incluídos se escolher o percentil 99.
    - Se quiser escolher o pico de utilização para o período e não quiser perder nenhum outliers, selecione o percentil 99 para utilização percentil.
5. Este valor é multiplicado pelo fator de conforto para obter os dados de utilização eficazes do desempenho para estas métricas que o aparelho recolhe:
    - Utilização do CPU (%)
    - Utilização da memória (%)
    - Leia IO/s e Escreva IO/s (ficheiros de dados e registo)
    - Leia MB/s e Escreva MB/s (Produção)
    - Latência das operações de OI

## <a name="what-properties-are-used-to-create-and-customize-an-azure-sql-assessment"></a>Que propriedades são usadas para criar e personalizar uma avaliação Azure SQL?

Aqui está o que está incluído nas propriedades de avaliação Azure SQL:

**Propriedade** | **Detalhes**
--- | ---
**Localização de destino** | A região de Azure para onde quer migrar. A configuração e recomendações de custos do Azure SQL baseiam-se na localização que especifica.
**Tipo de implantação de alvo** | O tipo de implementação de alvo que pretende executar a avaliação em: <br/><br/> Selecione **Recomendado**, se quiser que a Azure Migrate avalie a prontidão dos seus servidores SQL para migrar para Azure SQL MI e Azure SQL DB, e recomendar a opção de implementação do alvo mais adequada, o nível de destino, a configuração do SQL Azure e as estimativas mensais.<br/><br/>Selecione **Azure SQL DB**, se quiser avaliar os seus servidores SQL para migrar apenas para Azure SQL Databases e rever o nível alvo, configuração DB Azure SQL e estimativas mensais.<br/><br/>Selecione **Azure SQL MI,** se quiser avaliar os seus servidores SQL para migrar apenas para Azure SQL Databases e rever o nível alvo, configuração Azure SQL MI e estimativas mensais.
**Capacidade reservada** | Especifica a capacidade reservada de modo a que as estimativas de custos na avaliação as levem em conta.<br/><br/> Se selecionar uma opção de capacidade reservada, não pode especificar "Desconto (%)".
**Critérios de dimensionamento** | Esta propriedade é usada para o tamanho certo da configuração Azure SQL. <br/><br/> Está em incumprimento **em termos de desempenho,** o que significa que a avaliação recolherá as métricas de desempenho do SQL Server e bases de dados para recomendar uma recomendação de nível/configuração do Azure SQL De tamanho ideal.
**Histórico de desempenho** | O histórico de desempenho especifica a duração utilizada quando os dados de desempenho são avaliados.
**Utilização de percentil** | A utilização percentil especifica o valor percentil da amostra de desempenho utilizada para a direito.
**Fator de conforto** | O tampão utilizado durante a avaliação. Explica questões como o uso sazonal, o histórico de desempenho curto e, provavelmente, o aumento do uso futuro.<br/><br/> Por exemplo, uma instância de 10 núcleos com 20% de utilização normalmente resulta num caso de dois núcleos. Com um fator de conforto de 2.0, o resultado é um caso de quatro núcleos.
**Programa de Oferta/Licenciamento** | A [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) em que está matriculado. Atualmente, só pode escolher entre Pay-as-you-go e Pay-as-you-go Dev/Test. Note que pode obter desconto adicional aplicando capacidade reservada e Benefício Híbrido Azure em cima da oferta Pay-as-you-go.
**Escalão de serviço** | A opção de nível de serviço mais adequada para acomodar as necessidades do seu negócio para a migração para a Base de Dados Azure SQL e/ou Instância Gerida Azure SQL:<br/><br/>**Recomendado** se quiser que a Azure Migrate recomende o nível de serviço mais adequado para os seus servidores. Isto pode ser um propósito geral ou um negócio crítico. <br/><br/> **Finalidade Geral** Se quiser uma configuração Azure SQL projetada para cargas de trabalho orientadas para o orçamento. [Saiba mais](../azure-sql/database/service-tier-general-purpose.md) <br/><br/> **Critical de negócios** Se quiser uma configuração Azure SQL projetada para cargas de trabalho de baixa latência com alta resiliência a falhas e falhas rápidas. [Saiba mais](../azure-sql/database/service-tier-business-critical.md)
**Moeda** | A moeda de faturação da sua conta.
**Desconto (%)** | Quaisquer descontos específicos por subscrição que receba em cima da oferta Azure. A predefinição é 0%.
**Benefício Híbrido do Azure** | Especifica se já tem uma licença SQL Server. <br/><br/> Se o fizer e estiver coberto com uma garantia de software ativa das subscrições do servidor SQL, pode candidatar-se ao Benefício Híbrido Azure quando levar licenças ao Azure.

[Reveja as melhores práticas](best-practices-assessment.md) para criar uma avaliação com a Azure Migrate.

## <a name="calculate-readiness"></a>Calcular prontidão

> [!NOTE]
A avaliação inclui apenas bases de dados que estão em estado online. Caso a base de dados esteja em qualquer outro estado, a avaliação ignora o respetivo cálculo de preparação, dimensionamento e custo. Se desejar avaliar essas bases de dados, altere o estado das mesmas e recalcule a avaliação após algum tempo.

### <a name="azure-sql-readiness"></a>Prontidão Azure SQL

A prontidão do Azure SQL para instâncias e bases de dados SQL baseia-se numa verificação de compatibilidade de recursos com a Base de Dados Azure SQL e a Azure SQL Managed Instance:
1. A avaliação do SQL do Azure considera as funcionalidades de instância do SQL Server que são atualmente utilizadas pelas cargas de trabalho do SQL Server (empregos de agente SQL, servidores ligados, etc.) e os esquemas de bases de dados dos utilizadores (tabelas, vistas, gatilhos, procedimentos armazenados, etc.) para identificar problemas de compatibilidade.
1. Se não houver problemas de compatibilidade encontrados, a prontidão é marcada como **Ready** for the target deployment type (Azure SQL Database ou Azure SQL Managed Instance)
1. Se existirem problemas de compatibilidade não críticos, tais como funcionalidades degradadas ou não apoiadas que não bloqueiem a migração para um tipo específico de implantação de alvo, a prontidão é marcada como **Ready** (ícone de informação hiperligada e azul) com detalhes de **aviso** e orientação de remediação recomendada.
1. Se existirem problemas de compatibilidade que possam bloquear a migração para um tipo específico de implantação de alvo, a prontidão está marcada como **Não pronta** com detalhes **de emissão** e orientação de remediação recomendada.
    - Se houver mesmo uma base de dados num caso SQL que não esteja pronta para um determinado tipo de implantação de alvos, a instância está marcada como **Não pronta** para esse tipo de implantação.
1. Se a descoberta ainda estiver em andamento ou existirem quaisquer problemas de descoberta para um exemplo ou base de dados SQL, a prontidão é marcada como **Desconhecida,** uma vez que a avaliação não conseguiu calcular a prontidão para esse exemplo do SQL.

### <a name="recommended-deployment-type"></a>Tipo de implantação recomendado

Se selecionar o tipo de implementação de destino como **Recomendado** nas propriedades de avaliação Azure SQL, a Azure Migrate recomenda um tipo de implantação Azure SQL que seja compatível com a sua instância SQL. Migrar para um alvo recomendado pela Microsoft reduz o seu esforço global de migração. 

#### <a name="recommended-deployment-type-based-on-azure-sql-readiness"></a>Tipo de implementação recomendado com base na prontidão Azure SQL

 **Prontidão Azure SQL DB** | **Prontidão Azure SQL MI** | **Tipo de implantação recomendado** | **Configuração Azure SQL e estimativas de custos calculadas?**
 --- | --- | --- | --- |
 Pronto | Pronto | Azure SQL DB ou <br/>Azure SQL MI | Yes
 Pronto | Não está pronto ou<br/> Desconhecido | BD SQL do Azure | Yes
 Não está pronto ou<br/>Desconhecido | Pronto | Azure SQL MI | Yes
 Não está pronto. | Não está pronto. | Potencialmente pronto para Azure VM | No
 Não está pronto ou<br/>Desconhecido | Não está pronto ou<br/>Desconhecido | Desconhecido | No

> [!NOTE]
> Se o tipo de implementação recomendado for selecionado como Recomendado em propriedades **de** avaliação e se o servidor SQL de origem for adequado tanto para a base de dados única Azure SQL DB como para a Azure SQL Managed Instance, a avaliação recomenda uma opção específica que otimize o seu custo e se ajuste dentro dos limites de tamanho e desempenho.

#### <a name="potentially-ready-for-azure-vm"></a>Potencialmente pronto para Azure VM

Se a instância SQL não estiver pronta para a Base de Dados Azure SQL e para a Instância Gerida Azure SQL, o tipo de implementação recomendado está marcado como *potencialmente pronto para Azure VM*.
- Recomenda-se ao utilizador a criação de uma avaliação em Azure Migrate com o tipo de avaliação como "Azure VM" para determinar se o servidor em que o caso está a decorrer está pronto a migrar para um VM Azure. Tenha em atenção que:
    - As avaliações de Azure VM em Azure Migrate estão atualmente focadas no elevador e na mudança e não considerarão as métricas de desempenho específicas para executar instâncias e bases de dados SQL na máquina virtual Azure. 
    - Quando executa uma avaliação de VM do Azure num servidor, as estimativas recomendadas de tamanho e custo referem-se a todas as instâncias em execução no servidor e podem ser migradas para uma VM do Azure com a ferramenta de Migração do Servidor. Antes de migrar, [veja as diretrizes de desempenho](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) do SQL Server em máquinas virtuais do Azure.


## <a name="calculate-sizing"></a>Calcular o tamanho

### <a name="azure-sql-configuration"></a>Configuração Azure SQL

Após a avaliação determinar a prontidão e o tipo de implantação Azure SQL recomendado, calcula um nível de serviço específico e configuração Azure SQL (tamanho SKU) que pode cumprir ou exceder o desempenho do exemplo SQL no local:
1. Durante o processo de descoberta, a Azure Migrate recolhe a configuração e desempenho de instância SQL que inclui:
    - vCores (atribuídos) e utilização de CPU (%)
        - A utilização do CPU para um caso SQL é a percentagem de CPU atribuído utilizado pelo caso no servidor SQL
        - A utilização do CPU para uma base de dados é a percentagem de CPU atribuído utilizado pela base de dados no caso SQL
    - Memória (alocada) e utilização da memória (%)
    - Leia IO/s e Escreva IO/s (ficheiros de dados e registo)
        - A leitura IO/s e Write IO/s a um nível de instância SQL é calculada adicionando o IO/s de leitura e write IO/s de todas as bases de dados descobertas nesse caso.
    - Leia MB/s e Escreva MB/s (Produção)
    - Latência das operações de OI
    - Total de tamanhos de DB e organizações de ficheiros de base de dados
        - O tamanho da base de dados é calculado adicionando todos os dados e ficheiros de registo.
1. A avaliação agrega todos os dados de configuração e desempenho e tenta encontrar a melhor correspondência entre vários níveis e configurações de serviço Azure SQL, e escolhe uma configuração que pode corresponder ou exceder os requisitos de desempenho da instância SQL, otimizando o custo.

### <a name="confidence-ratings"></a>Classificações de confiança 
Cada avaliação do Azure SQL está associada a uma classificação de confiança. A classificação varia entre uma (mais baixa) e cinco estrelas (mais altas). A classificação de confiança ajuda-o a estimar a fiabilidade das recomendações de tamanho que a Azure Migrate fornece.
- A classificação de confiança é atribuída a uma avaliação. O rating baseia-se na disponibilidade de pontos de dados necessários para calcular a avaliação.
- Para dimensionamento baseado no desempenho, a avaliação recolhe dados de desempenho de todas as instâncias e bases de dados SQL, que incluem:
    - Utilização do CPU (%)
    - Utilização da memória (%)
    - Leia IO/s e Escreva IO/s (ficheiros de dados e registo)
    - Leia MB/s e Escreva MB/s (Produção)
    - Latência das operações de OI
    
Se algum destes números de utilização não estiver disponível, as recomendações de tamanho podem não ser fiáveis.
Este quadro mostra as classificações de confiança da avaliação, que dependem da percentagem de pontos de dados disponíveis:

**Disponibilidade de pontos de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 estrela
21%-40% | 2 estrelas
41%-60% | 3 estrelas
61%-80% | 4 estrelas
81%-100% | 5 estrelas

#### <a name="low-confidence-ratings"></a>Baixas classificações de confiança
Eis algumas razões pelas quais uma avaliação pode obter uma classificação de baixa confiança:
- Não analisou o ambiente durante o período para o qual está a criar a avaliação. Por exemplo, se criar a avaliação com a duração de desempenho definida para um dia, deve esperar pelo menos um dia depois de começar a descobrir todos os pontos de dados para ser recolhido.
- A avaliação não é capaz de recolher os dados de desempenho de alguns ou todos os servidores no período de avaliação. Para uma alta classificação de confiança, certifique-se de que:
    - Os servidores são ligados durante a duração da avaliação
    - São permitidas ligações de saída nas portas 443
    - Se o estado de ligação Azure Migrate do agente SQL em Azure Migrate estiver 'Conectado' e verificar o último batimento cardíaco 
    - Se o estado de ligação no Azure Migrate de todas as instâncias do SQL é “Ligado” no painel da instância do SQL detetada

    “Recalcule” a avaliação para refletir as últimas alterações na classificação de confiança.
- Algumas bases de dados ou instâncias foram criadas durante o período para o qual a avaliação foi calculada. Por exemplo, assuma que criou uma avaliação para o histórico de desempenho do mês passado, mas algumas bases de dados ou casos foram criados apenas há uma semana. Neste caso, os dados de desempenho dos novos servidores não estarão disponíveis durante toda a duração e a classificação de confiança será baixa.

> [!NOTE]
> Como as avaliações do Azure SQL são avaliações baseadas no desempenho, se a classificação de confiança de qualquer avaliação for inferior a cinco estrelas, recomendamos que espere pelo menos um dia para que o aparelho perfile o ambiente e, em seguida, recalcule a avaliação. Caso contrário, o tamanho baseado no desempenho pode não ser fiável.

## <a name="calculate-monthly-costs"></a>Calcular os custos mensais
Após a conclusão das recomendações de dimensionamento, a avaliação do Azure SQL calcula os custos de cálculo e armazenamento das configurações Azure SQL recomendadas utilizando uma API de preços internos. Agrega o custo de cálculo e armazenamento em todas as instâncias para calcular o custo total mensal do cálculo. 
### <a name="compute-cost"></a>Custo do cálculo
- Para calcular o custo do cálculo para uma configuração Azure SQL, a avaliação considera as seguintes propriedades:
    - Benefício Híbrido Azure para licenças SQL
    - Capacidade reservada
    - Localização do alvo de Azure
    - Moeda
    - Programa de Oferta/Licenciamento
    - Desconto (%)

### <a name="storage-cost"></a>Custo do armazenamento
- As estimativas de custos de armazenamento incluem apenas ficheiros de dados e não ficheiros de registo. 
- Para calcular o custo de armazenamento de uma configuração Azure SQL, a avaliação considera as seguintes propriedades:
    - Localização do alvo de Azure
    - Moeda
    - Programa de Oferta/Licenciamento
    - Desconto (%)
- O custo de armazenamento de cópia de segurança não está incluído na avaliação.
- **Base de Dados SQL do Azure**
    - Um custo mínimo de armazenamento de 5GB é adicionado na estimativa de custos e o custo adicional de armazenamento é adicionado para armazenamento em incrementos de 1GB. [Saiba mais](https://azure.microsoft.com/pricing/details/sql-database/single/)
- **Instância Gerida do SQL no Azure**
    - Não há custo de armazenamento adicionado para o primeiro armazenamento de 32 GB/instância/mês e custo adicional de armazenamento é adicionado para armazenamento em incrementos de 32GB. [Saiba mais](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)
        
## <a name="next-steps"></a>Passos seguintes
- [Reveja as](best-practices-assessment.md) melhores práticas para criar avaliações. 
- Saiba como fazer uma [avaliação Azure SQL](how-to-create-azure-sql-assessment.md).