---
title: Migrar dados de um servidor netezza no local para O Azure
description: Utilize a Azure Data Factory para migrar dados de um servidor Netezza no local para o Azure.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 9/03/2019
ms.openlocfilehash: a0263880262da95f4d26ee8388da464e9a59efca
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416444"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-netezza-server-to-azure"></a>Utilize a Azure Data Factory para migrar dados de um servidor netezza no local para o Azure 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A Azure Data Factory fornece um mecanismo performativo, robusto e rentável para migrar dados em escala de um servidor Netezza no local para a sua conta de armazenamento Azure ou base de dados azure SQL Data Warehouse. 

Este artigo fornece as seguintes informações para engenheiros de dados e desenvolvedores:

> [!div class="checklist"]
> * Desempenho 
> * Copiar resiliência
> * Segurança da rede
> * Arquitetura de solução de alto nível 
> * Implementação de boas práticas  

## <a name="performance"></a>Desempenho

A Azure Data Factory oferece uma arquitetura sem servidores que permite o paralelismo a vários níveis. Se for um desenvolvedor, isto significa que pode construir oleodutos para utilizar totalmente a largura de banda da rede e da base de dados para maximizar a produção de movimento de dados para o seu ambiente.

![Diagrama de desempenho](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

O diagrama anterior pode ser interpretado da seguinte forma:

- Uma única atividade de cópia pode tirar partido dos recursos de computação escaláveis. Quando utilizar o Tempo de Execução de Integração Azure, pode especificar [até 256 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) para cada atividade de cópia de forma sem servidor. Com um tempo de funcionamento de integração auto-hospedado (IV auto-hospedado), pode aumentar manualmente a máquina ou escalar para várias máquinas[(até quatro nós)](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)e uma única atividade de cópia distribui a sua partição em todos os nós. 

- Uma única atividade de cópia lê e escreve para a loja de dados utilizando vários fios. 

- O fluxo de controlo da Azure Data Factory pode iniciar múltiplas atividades de cópia em paralelo. Por exemplo, pode iniciá-los utilizando um [loop For Each](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Para mais informações, consulte o [desempenho da atividade do Copie e o guia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)de escalabilidade .

## <a name="resilience"></a>Resiliência

No âmbito de uma única execução de atividade de cópia, a Azure Data Factory dispõe de um mecanismo de retry incorporado, que lhe permite lidar com um determinado nível de falhas transitórias nas lojas de dados ou na rede subjacente.

Com a atividade de cópia da Azure Data Factory, quando copia dados entre as lojas de dados de origem e pia, tem duas formas de lidar com linhas incompatíveis. Pode abortar e falhar a atividade da cópia ou continuar a copiar o resto dos dados ignorando as linhas de dados incompatíveis. Além disso, para saber a causa da falha, pode registar as linhas incompatíveis no armazenamento Azure Blob ou na Azure Data Lake Store, corrigir os dados na fonte de dados e voltar a tentar a atividade da cópia.

## <a name="network-security"></a>Segurança da rede 

Por padrão, a Azure Data Factory transfere dados do servidor Netezza no local para uma conta de armazenamento Azure ou uma base de dados do Armazém de Dados Azure SQL utilizando uma ligação encriptada sobre o Protocolo de Transferência de Hipertextos Seguro (HTTPS). HTTPS fornece encriptação de dados em trânsito e evita escutas e ataques man-in-the-middle.

Em alternativa, se não quiser que os dados sejam transferidos através da internet pública, pode ajudar a obter uma maior segurança transferindo dados através de uma ligação privada através da Rota Expresso do Azure. 

A próxima secção discute como alcançar uma maior segurança.

## <a name="solution-architecture"></a>Arquitetura de soluções

Esta secção discute duas formas de migrar os seus dados.

### <a name="migrate-data-over-the-public-internet"></a>Migrar dados através da internet pública

![Migrar dados através da internet pública](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

O diagrama anterior pode ser interpretado da seguinte forma:

- Nesta arquitetura, transfere dados de forma segura utilizando HTTPS através da internet pública.

- Para alcançar esta arquitetura, é necessário instalar o tempo de execução de integração da Azure Data Factory (auto-hospedado) numa máquina do Windows atrás de uma firewall corporativa. Certifique-se de que este tempo de execução de integração pode aceder diretamente ao servidor Netezza. Para utilizar totalmente a sua rede e os armazéns de dados para copiar dados, pode aumentar manualmente a sua máquina ou escalar para várias máquinas.

- Ao utilizar esta arquitetura, pode migrar dados instantâneos iniciais e dados delta.

### <a name="migrate-data-over-a-private-network"></a>Migrar dados através de uma rede privada 

![Migrar dados através de uma rede privada](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

O diagrama anterior pode ser interpretado da seguinte forma:

- Nesta arquitetura, você migra dados através de um link de observação privado via Rota Expresso Azure, e os dados nunca atravessam a internet pública. 

- Para alcançar esta arquitetura, é necessário instalar o tempo de execução de integração da Azure Data Factory (auto-hospedado) numa máquina virtual do Windows (VM) dentro da sua rede virtual Azure. Para utilizar totalmente a sua rede e as lojas de dados largura de banda para copiar dados, pode aumentar manualmente o seu VM ou escalar para vários VMs.

- Ao utilizar esta arquitetura, pode migrar dados instantâneos iniciais e dados delta.

## <a name="implement-best-practices"></a>Implementar as melhores práticas 

### <a name="manage-authentication-and-credentials"></a>Gerir a autenticação e credenciais 

- Para autenticar a Netezza, pode utilizar a [autenticação ODBC através](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties)de uma cadeia de ligação . 

- Autenticar o armazenamento da Blob Azure: 

   - Recomendamos vivamente a utilização [de identidades geridas para recursos Azure.](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) Construída sem uma identidade azure data factory gerida automaticamente no Azure Ative Directory (Azure AD), as identidades geridas permitem configurar os gasodutos sem ter de fornecer credenciais na definição de Serviço Linked.  

   - Em alternativa, pode autenticar o armazenamento do Azure Blob utilizando o diretor de [serviço,](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)uma assinatura de [acesso partilhado](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)ou uma chave de conta de [armazenamento.](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication) 

- Autenticar o Azure Data Lake Storage Gen2: 

   - Recomendamos vivamente a utilização [de identidades geridas para recursos Azure.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)
   
   - Também pode utilizar o diretor de [serviço](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) ou uma chave de conta de [armazenamento.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) 

- Autenticar o Armazém de Dados Azure SQL:

   - Recomendamos vivamente a utilização [de identidades geridas para recursos Azure.](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity)
   
   - Também pode utilizar o diretor de [serviço](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication) ou [a autenticação SQL.](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication)

- Quando não está a usar identidades geridas para os recursos Azure, recomendamos vivamente [armazenar as credenciais no Cofre de Chaves Azure](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) para facilitar a gestão central e rotação de chaves sem ter de modificar os serviços ligados à Azure Data Factory. Esta é também uma das [melhores práticas para ci/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="migrate-initial-snapshot-data"></a>Migrar dados instantâneos iniciais 

Para tabelas pequenas (isto é, tabelas com um volume inferior a 100 GB ou que podem ser migradas para Azure dentro de duas horas), pode fazer cada cópia de dados de carga de trabalho por tabela. Para uma maior produção, pode executar vários trabalhos de cópia da Azure Data Factory para carregar tabelas separadas simultaneamente. 

Dentro de cada trabalho de cópia, para executar consultas paralelas e copiar dados por divisórias, também pode atingir algum nível de paralelismo utilizando a [ `parallelCopies` definição](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy) de propriedade com qualquer uma das seguintes opções de partição de dados:

- Para ajudar a obter uma maior eficiência, encorajamo-lo a partir de uma fatia de dados.  Certifique-se de que `parallelCopies` o valor na definição é inferior ao número total de divisórias de fatias de dados na sua tabela no servidor Netezza.  

- Se o volume de cada partição de fatias de dados ainda for grande (por exemplo, 10 GB ou mais), encorajamo-lo a mudar para uma divisória dinâmica. Esta opção confere-lhe uma maior flexibilidade para definir o número de divisórias e o volume de cada divisória por coluna de partição, encadernado superior e inferior.

Para tabelas maiores (isto é, tabelas com um volume de 100 GB ou superior ou que *não possam* ser migradas para Azure dentro de duas horas), recomendamos que partique os dados por consulta personalizada e, em seguida, faça cada cópia de cópia de uma partição de cada vez. Para uma melhor produção, pode executar vários trabalhos de cópia da Azure Data Factory em simultâneo. Para cada alvo de trabalho de cópia de carregar uma divisória por consulta personalizada, pode aumentar a entrada permitindo o paralelismo através de uma fatia de dados ou gama dinâmica. 

Se qualquer trabalho de cópia falhar devido a um problema transitório de rede ou loja de dados, pode reexecutar o trabalho de cópia falhado para recarregar essa partição específica da tabela. Outros trabalhos de cópia que carregam outras divisórias não são afetados.

Quando você carrega dados numa base de dados do Armazém de Dados Azure SQL, sugerimos que você ative a PolyBase dentro do trabalho de cópia com o armazenamento Azure Blob como encenação.

### <a name="migrate-delta-data"></a>Migrar dados delta 

Para identificar as linhas novas ou atualizadas da sua tabela, utilize uma coluna de carimbo de tempo ou uma chave de incremento dentro do esquema. Em seguida, pode armazenar o valor mais recente como uma marca de água elevada numa tabela externa e depois usá-lo para filtrar os dados delta da próxima vez que carregar dados. 

Cada tabela pode utilizar uma coluna de marca de água diferente para identificar as suas linhas novas ou atualizadas. Sugerimos que crie uma mesa de controlo externo. Na tabela, cada linha representa uma tabela no servidor Netezza com o seu nome específico de coluna de marca de água e alto valor de marca de água. 

### <a name="configure-a-self-hosted-integration-runtime"></a>Configure um tempo de execução de integração auto-hospedado

Se estiver a migrar dados do servidor Netezza para o Azure, quer o servidor esteja no local por detrás da firewall da sua corporação ou dentro de um ambiente de rede virtual, precisa de instalar um IR auto-hospedado numa máquina do Windows ou VM, que é o motor que é usado para mover dados. À medida que está a instalar o IR auto-hospedado, recomendamos a seguinte abordagem:

- Para cada máquina windows ou VM, comece com uma configuração de 32 vCPU e memória de 128 GB. Pode continuar a monitorizar o CPU e o uso da memória da máquina de INFRAVERMELHOs durante a migração de dados para ver se precisa de aumentar ainda mais a máquina para um melhor desempenho ou reduzir a velocidade da máquina para economizar custos.

- Você também pode escalar associando até quatro nódosos com um único IR auto-hospedado. Uma única cópia que está a correr contra um IR auto-hospedado aplica automaticamente todos os nós vm para copiar os dados em paralelo. Para uma elevada disponibilidade, comece com quatro nós VM para evitar um único ponto de falha durante a migração de dados.

### <a name="limit-your-partitions"></a>Limite as suas divisórias

Como uma boa prática, realize uma prova de desempenho do conceito (POC) com um conjunto de dados representativo da amostra, para que possa determinar um tamanho de partição adequado para cada atividade de cópia. Sugerimos que carregue cada partição para Azure dentro de duas horas.  

Para copiar uma tabela, comece com uma única atividade de cópia com uma única máquina de infravermelhos auto-hospedada. Aumente gradualmente `parallelCopies` a definição com base no número de divisórias de fatias de dados na sua tabela. Veja se toda a tabela pode ser carregada para Azure dentro de duas horas, de acordo com a entrada que resulta do trabalho de cópia. 

Se não puder ser carregado para o Azure dentro de duas horas, e a capacidade do nó DE IR auto-hospedado e do armazenamento de dados não estiver totalmente utilizado, aumente gradualmente o número de atividades de cópia simultânea até atingir o limite da sua rede ou o limite de largura de banda das lojas de dados. 

Continue a monitorizar o CPU e o uso da memória na máquina de infravermelhos auto-hospedada e esteja pronto para escalar a máquina ou escalar para várias máquinas quando vir que o CPU e a memória estão totalmente utilizados. 

Quando se deparacom erros de estrangulamento, conforme relatado pela atividade de `parallelCopies` cópia da Azure Data Factory, reduza a conmoeda ou a definição na Fábrica de Dados Azure, ou considere aumentar a largura de banda ou as operações de I/S por segundo (IOPS) dos limites da rede e das lojas de dados. 


### <a name="estimate-your-pricing"></a>Estimar o seu preço 

Considere o seguinte oleoduto, que é construído para migrar dados do servidor Netezza no local para uma base de dados de Armazém de Dados Azure SQL:

![O gasoduto de preços](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

Vamos supor que as seguintes declarações são verdadeiras: 

- O volume total de dados é de 50 terabytes (TUBERCULOSE). 

- Estamos a migrar dados utilizando a arquitetura de primeira solução (o servidor Netezza está no local, atrás da firewall).

- O volume de 50 TB é dividido em 500 divisórias, e cada atividade de cópia move uma divisória.

- Cada atividade de cópia é configurada com um IR auto-hospedado contra quatro máquinas e obtém uma entrada de 20 megabytes por segundo (MBps). (No âmbito `parallelCopies` da atividade de cópia, é definido para 4, e cada fio para carregar dados da tabela obtém uma entrada de 5-MBps.)

- A conmoeda ForEach está definida para 3, e a produção agregada é de 60 MBps.

- No total, leva 243 horas para completar a migração.

Com base nos pressupostos anteriores, aqui está o preço estimado: 

![A tabela de preços](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> Os preços apresentados na tabela anterior são hipotéticos. O seu preço real depende da produção real no seu ambiente. O preço da máquina Windows (com o IV auto-hospedado instalado) não está incluído. 

### <a name="additional-references"></a>Referências adicionais

Para mais informações, consulte os seguintes artigos e guias:

- [Migrar dados de uma base de dados de datais relacionais no local para o Azure utilizando a Azure Data Factory](https://azure.microsoft.com/resources/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/)
- [Conector Netezza](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [Conector ODBC](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Conector de armazenamento Azure Blob](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Conector do Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Conector Azure SQL Data Warehouse](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [Guia de afinação de desempenho da atividade de cópia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Create and configure a self-hosted integration runtime](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime) (Criar e configurar um runtime de integração autoalojado)
- [Auto-hospedado integração tempo de execução HA e escalabilidade](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Considerações de segurança sobre movimento de dados](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Armazenar credenciais no Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Copiar dados incrementalmente a partir de uma tabela](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [Copiar dados incrementalmente a partir de várias tabelas](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Página de preços da Fábrica de Dados Azure](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Passos seguintes

- [Copiar ficheiros de vários contentores utilizando a Azure Data Factory](solution-template-copy-files-multiple-containers.md)
