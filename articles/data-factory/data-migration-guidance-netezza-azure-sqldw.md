---
title: Migrar dados de um servidor Netezza no local para Azure
description: Utilize a Azure Data Factory para migrar dados de um servidor Netezza no local para Azure.
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
ms.openlocfilehash: 8192b1351d54acbb553bacb8b36474cba271cb05
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638079"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-netezza-server-to-azure"></a>Utilize a Azure Data Factory para migrar dados de um servidor Netezza no local para a Azure 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A Azure Data Factory fornece um mecanismo performante, robusto e rentável para migrar dados à escala de um servidor Netezza no local para a sua conta de armazenamento Azure ou Azure Synapse Analytics (ex-SQL Data Warehouse). 

Este artigo fornece as seguintes informações para engenheiros de dados e desenvolvedores:

> [!div class="checklist"]
> * Desempenho 
> * Copiar resiliência
> * Segurança da rede
> * Arquitetura de solução de alto nível 
> * Implementação das melhores práticas  

## <a name="performance"></a>Desempenho

A Azure Data Factory oferece uma arquitetura sem servidor que permite o paralelismo a vários níveis. Se você é um desenvolvedor, isto significa que você pode construir oleodutos para usar totalmente a largura de banda de rede e base de dados para maximizar o rendimento do movimento de dados para o seu ambiente.

![Diagrama de desempenho](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

O diagrama anterior pode ser interpretado da seguinte forma:

- Uma única atividade de cópia pode tirar partido dos recursos de computação escaláveis. Quando utilizar o Tempo de Execução da Integração Azure, pode especificar [até 256 DIUs](./copy-activity-performance.md#data-integration-units) para cada atividade de cópia de forma sem servidor. Com um tempo de integração auto-hospedado (IR auto-hospedado), pode escalar manualmente a máquina ou escalar para várias máquinas[(até quatro nós](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)) e uma única atividade de cópia distribui a sua partição por todos os nós. 

- Uma única atividade de cópia lê e escreve para a loja de dados utilizando vários fios. 

- O fluxo de controlo da Azure Data Factory pode iniciar múltiplas atividades de cópia em paralelo. Por exemplo, pode iniciá-los utilizando um [loop para cada.](./control-flow-for-each-activity.md) 

Para mais informações, consulte [o guia de desempenho e escalabilidade da copia](./copy-activity-performance.md).

## <a name="resilience"></a>Resiliência

Dentro de uma única atividade de cópia executada, a Azure Data Factory dispõe de um mecanismo de retenção incorporado, que lhe permite lidar com um certo nível de falhas transitórias nas lojas de dados ou na rede subjacente.

Com a atividade de cópia da Azure Data Factory, quando copia dados entre lojas de dados de origem e afundamento, tem duas formas de lidar com linhas incompatíveis. Pode abortar e falhar a atividade de cópia ou continuar a copiar os restantes dados ignorando as linhas de dados incompatíveis. Além disso, para saber a causa da falha, pode registar as linhas incompatíveis no armazenamento Azure Blob ou na Azure Data Lake Store, corrigir os dados na fonte de dados e voltar a tentar a atividade da cópia.

## <a name="network-security"></a>Segurança da rede 

Por padrão, a Azure Data Factory transfere dados do servidor Netezza para uma conta de armazenamento Azure ou base de dados Azure Synapse Analytics utilizando uma ligação encriptada sobre o Hypertext Transfer Protocol Secure (HTTPS). HTTPS fornece encriptação de dados em trânsito e evita escutas e ataques man-in-the-middle.

Em alternativa, se não quiser que os dados sejam transferidos através da internet pública, pode ajudar a obter uma maior segurança transferindo dados através de um link de observação privado via Azure Express Route. 

A próxima secção discute como alcançar uma maior segurança.

## <a name="solution-architecture"></a>Arquitetura de soluções

Esta secção discute duas formas de migrar os seus dados.

### <a name="migrate-data-over-the-public-internet"></a>Migrar dados através da internet pública

![Migrar dados através da internet pública](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

O diagrama anterior pode ser interpretado da seguinte forma:

- Nesta arquitetura, você transfere dados de forma segura utilizando HTTPS através da internet pública.

- Para alcançar esta arquitetura, você precisa instalar o tempo de integração da Azure Data Factory (auto-hospedado) numa máquina Windows atrás de uma firewall corporativa. Certifique-se de que este tempo de execução de integração pode aceder diretamente ao servidor Netezza. Para utilizar totalmente a sua rede e a largura de banda de lojas de dados para copiar dados, pode escalar manualmente a sua máquina ou escalar para várias máquinas.

- Ao utilizar esta arquitetura, pode migrar tanto os dados instantâneos iniciais como os dados delta.

### <a name="migrate-data-over-a-private-network"></a>Migrar dados sobre uma rede privada 

![Migrar dados sobre uma rede privada](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

O diagrama anterior pode ser interpretado da seguinte forma:

- Nesta arquitetura, você migra dados sobre um link de observação privado via Azure Express Route, e os dados nunca atravessam a internet pública. 

- Para alcançar esta arquitetura, é necessário instalar o tempo de integração da Azure Data Factory (auto-hospedado) numa máquina virtual Windows (VM) dentro da sua rede virtual Azure. Para utilizar totalmente a sua rede e a largura de banda dos dados para copiar dados, pode escalar manualmente o seu VM ou escalar para vários VMs.

- Ao utilizar esta arquitetura, pode migrar tanto os dados instantâneos iniciais como os dados delta.

## <a name="implement-best-practices"></a>Implementar as melhores práticas 

### <a name="manage-authentication-and-credentials"></a>Gerir a autenticação e as credenciais 

- Para autenticar o Netezza, pode utilizar [a autenticação ODBC através da cadeia de ligação](./connector-netezza.md#linked-service-properties). 

- Para autenticar para o armazenamento da Azure Blob: 

   - Recomendamos vivamente a utilização [de identidades geridas para recursos Azure.](./connector-azure-blob-storage.md#managed-identity) Construídas sobre uma identidade de Azure Data Factory gerida automaticamente no Azure Ative Directory (Azure AD), identidades geridas permitem configurar oleodutos sem ter de fornecer credenciais na definição de Serviço Linked.  

   - Em alternativa, pode autenticar o armazenamento do Azure Blob utilizando [o principal do serviço,](./connector-azure-blob-storage.md#service-principal-authentication)uma [assinatura de acesso partilhado](./connector-azure-blob-storage.md#shared-access-signature-authentication)ou uma chave de conta [de armazenamento.](./connector-azure-blob-storage.md#account-key-authentication) 

- Para autenticar a Azure Data Lake Storage Gen2: 

   - Recomendamos vivamente a utilização [de identidades geridas para recursos Azure.](./connector-azure-data-lake-storage.md#managed-identity)
   
   - Também pode utilizar [o principal de serviço](./connector-azure-data-lake-storage.md#service-principal-authentication) ou uma chave de conta de [armazenamento.](./connector-azure-data-lake-storage.md#account-key-authentication) 

- Para autenticar a Azure Synapse Analytics:

   - Recomendamos vivamente a utilização [de identidades geridas para recursos Azure.](./connector-azure-sql-data-warehouse.md#managed-identity)
   
   - Também pode utilizar a autenticação principal de [serviço](./connector-azure-sql-data-warehouse.md#service-principal-authentication) ou [SQL.](./connector-azure-sql-data-warehouse.md#sql-authentication)

- Quando não está a usar identidades geridas para recursos Azure, recomendamos vivamente [o armazenamento das credenciais no Azure Key Vault](./store-credentials-in-key-vault.md) para facilitar a gestão central e a rotação das chaves sem ter de modificar os serviços ligados à Azure Data Factory. Esta é também uma das [melhores práticas para o CI/CD](./continuous-integration-deployment.md#best-practices-for-cicd). 

### <a name="migrate-initial-snapshot-data"></a>Migrar dados instantâneos iniciais 

Para tabelas pequenas (isto é, tabelas com um volume inferior a 100 GB ou que podem ser migradas para Azure dentro de duas horas), pode fazer cada cópia de dados de carga de trabalho por tabela. Para uma maior produção, pode executar vários trabalhos de cópia da Azure Data Factory para carregar mesas separadas simultaneamente. 

Dentro de cada trabalho de cópia, para executar consultas paralelas e copiar dados por partições, também pode atingir algum nível de paralelismo utilizando a [ `parallelCopies` definição](./copy-activity-performance.md#parallel-copy) de propriedade com qualquer uma das seguintes opções de partição de dados:

- Para ajudar a alcançar uma maior eficiência, encorajamos-te a partir de uma fatia de dados.  Certifique-se de que o valor na `parallelCopies` definição é inferior ao número total de divisórias de fatia de dados na sua tabela no servidor Netezza.  

- Se o volume de cada partição de fatias de dados ainda for grande (por exemplo, 10 GB ou superior), encorajamo-lo a mudar para uma divisória dinâmica de alcance. Esta opção confere-lhe maior flexibilidade para definir o número de divisórias e o volume de cada partição por coluna de partição, limite superior e inferior.

Para tabelas maiores (isto é, tabelas com um volume de 100 GB ou superior ou que *não podem* ser migradas para Azure dentro de duas horas), recomendamos que se parta os dados por consulta personalizada e, em seguida, faça cada cópia de cópia uma partição de cada vez. Para uma melhor produção, pode executar vários trabalhos de cópia da Azure Data Factory simultaneamente. Para cada alvo de trabalho de cópia de carregamento de uma partição por consulta personalizada, pode aumentar a produção, permitindo o paralelismo através da fatia de dados ou da gama dinâmica. 

Se algum trabalho de cópia falhar devido a um problema transitório de rede ou loja de dados, pode voltar a repetir a função de cópia falhada para recarregar essa divisória específica da tabela. Outros trabalhos de cópia que carregam outras divisórias não são afetados.

Quando carrega dados numa base de dados Azure Synapse Analytics, sugerimos que ative a PolyBase dentro do trabalho de cópia com o armazenamento Azure Blob como encenação.

### <a name="migrate-delta-data"></a>Migrar dados delta 

Para identificar as linhas novas ou atualizadas da sua mesa, utilize uma coluna de horatamp ou uma chave de incremento dentro do esquema. Em seguida, pode armazenar o valor mais recente como uma marca de água elevada numa tabela externa e depois usá-lo para filtrar os dados delta da próxima vez que carregar dados. 

Cada mesa pode usar uma coluna de marca de água diferente para identificar as suas novas linhas ou atualizadas. Sugerimos que crie uma mesa de controlo externa. Na tabela, cada linha representa uma tabela no servidor Netezza com o seu nome específico da coluna de marca de água e elevado valor de marca de água. 

### <a name="configure-a-self-hosted-integration-runtime"></a>Configure um tempo de integração auto-hospedado

Se estiver a migrar dados do servidor Netezza para o Azure, quer o servidor esteja no local por detrás da firewall da sua empresa ou dentro de um ambiente de rede virtual, precisa de instalar um IR auto-hospedado numa máquina Windows ou VM, que é o motor que é usado para mover dados. Ao instalar o IR auto-hospedado, recomendamos a seguinte abordagem:

- Para cada máquina Windows ou VM, comece com uma configuração de 32 vCPU e memória de 128 GB. Pode continuar a monitorizar o CPU e o uso da memória da máquina de infravermelhos durante a migração de dados para ver se precisa de aumentar ainda mais a máquina para um melhor desempenho ou reduzir a escala da máquina para economizar custos.

- Você também pode escalar associando até quatro nós com um único IR auto-hospedado. Um único trabalho de cópia que está a decorrer contra um IR auto-hospedado aplica automaticamente todos os nós VM para copiar os dados em paralelo. Para uma elevada disponibilidade, comece com quatro nós VM para evitar um único ponto de falha durante a migração de dados.

### <a name="limit-your-partitions"></a>Limite as suas divisórias

Como uma boa prática, conduza uma prova de desempenho do conceito (POC) com um conjunto de dados de amostra representativa, para que possa determinar um tamanho de partição adequado para cada atividade de cópia. Sugerimos que carregue cada divisória para Azure em duas horas.  

Para copiar uma tabela, comece com uma única atividade de cópia com uma única máquina de infravermelhos auto-hospedada. Aumente gradualmente a `parallelCopies` definição com base no número de divisórias de fatia de dados na sua tabela. Veja se toda a mesa pode ser carregada para Azure dentro de duas horas, de acordo com a produção que resulta do trabalho de cópia. 

Se não puder ser carregado para Azure dentro de duas horas, e a capacidade do nó DE IR auto-hospedado e da loja de dados não for totalmente utilizada, aumente gradualmente o número de atividades de cópia simultâneas até chegar ao limite da sua rede ou ao limite de largura de banda das lojas de dados. 

Continue a monitorizar o CPU e o uso da memória na máquina de infravermelhos auto-hospedada e esteja pronto para escalar a máquina ou escalar para várias máquinas quando vir que o CPU e a memória estão totalmente utilizados. 

Quando se depara com erros de estrangulamento, conforme relatado pela atividade de cópia da Azure Data Factory, reduz a concordância ou `parallelCopies` a definição na Azure Data Factory, ou considera aumentar os limites de largura de banda ou I/S por segundo (IOPS) da rede e das lojas de dados. 


### <a name="estimate-your-pricing"></a>Estime o seu preço 

Considere o seguinte oleoduto, que é construído para migrar dados do servidor Netezza no local para uma base de dados Azure Synapse Analytics:

![O gasoduto de preços](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

Vamos supor que as seguintes declarações são verdadeiras: 

- O volume total de dados é de 50 terabytes (Tuberculose). 

- Estamos a migrar dados usando a arquitetura de primeira solução (o servidor Netezza está no local, atrás da firewall).

- O volume de 50-TB é dividido em 500 divisórias, e cada atividade de cópia move uma partição.

- Cada atividade de cópia é configurada com um IR auto-hospedado contra quatro máquinas e consegue uma produção de 20 megabytes por segundo (MBps). (Dentro da atividade da cópia, `parallelCopies` é definido para 4, e cada fio para carregar dados da tabela obtém uma produção de 5 MBps.)

- A conusncy ForEach está definida para 3, e a produção agregada é de 60 MBps.

- No total, são precisas 243 horas para completar a migração.

Com base nos pressupostos anteriores, aqui está o preço estimado: 

![A tabela de preços](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> O preço indicado na tabela anterior é hipotético. O seu preço real depende da produção real no seu ambiente. O preço da máquina Do Windows (com o IR auto-hospedado instalado) não está incluído. 

### <a name="additional-references"></a>Referências adicionais

Para mais informações, consulte os seguintes artigos e guias:

- [Migrar dados de uma base de dados relacional do Data Warehouse para Azure utilizando a Azure Data Factory](https://azure.microsoft.com/resources/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/)
- [Conector Netezza](./connector-netezza.md)
- [Conector ODBC](./connector-odbc.md)
- [Conector de armazenamento Azure Blob](./connector-azure-blob-storage.md)
- [Conector do Azure Data Lake Storage Gen2](./connector-azure-data-lake-storage.md)
- [Conector Azure Synapse Analytics](./connector-azure-sql-data-warehouse.md)
- [Guia de afinação de desempenho da atividade de cópia](./copy-activity-performance.md)
- [Create and configure a self-hosted integration runtime](./create-self-hosted-integration-runtime.md) (Criar e configurar um runtime de integração autoalojado)
- [Integração auto-hospedada runtime HA e escalabilidade](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)
- [Considerações de segurança sobre movimento de dados](./data-movement-security-considerations.md)
- [Armazenar credenciais no Azure Key Vault](./store-credentials-in-key-vault.md)
- [Copiar dados incrementalmente de uma tabela](./tutorial-incremental-copy-portal.md)
- [Copiar dados incrementalmente de várias tabelas](./tutorial-incremental-copy-multiple-tables-portal.md)
- [Página de preços da Fábrica de Dados Azure](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Passos seguintes

- [Copie ficheiros de vários contentores utilizando a Azure Data Factory](solution-template-copy-files-multiple-containers.md)