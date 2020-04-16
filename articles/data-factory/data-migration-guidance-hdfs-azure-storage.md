---
title: Migrar dados de um aglomerado hadoop no local para o Armazenamento Azure
description: Aprenda a usar a Azure Data Factory para migrar dados do cluster Hadoop no local para o Armazenamento Azure.
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/30/2019
ms.openlocfilehash: 63b657e77172282225a9bc890b2f185b0f4d42a1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417137"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-hadoop-cluster-to-azure-storage"></a>Utilize a Azure Data Factory para migrar dados de um cluster hadoop no local para o Armazenamento Azure 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A Azure Data Factory fornece um mecanismo performativo, robusto e rentável para a migração de dados em escala, desde o HDFS até ao armazenamento azure Blob ou ao Azure Data Lake Storage Gen2. 

Data Factory oferece duas abordagens básicas para a migração de dados do HDFS no local para Azure. Pode selecionar a abordagem com base no seu cenário. 

- **Modo DistCp da fábrica** de dados (recomendado): Na Fábrica de Dados, pode utilizar [o DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) (cópia distribuída) para copiar ficheiros como está no armazenamento Azure Blob (incluindo [cópia encenada)](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)ou na Loja de Dados Azure Gen2. Utilize a Data Factory integrada com a DistCp para tirar partido de um cluster poderoso existente para obter a melhor cópia de produção. Você também obtém o benefício de agendamento flexível e uma experiência de monitorização unificada da Data Factory. Dependendo da configuração da Data Factory, a atividade de cópia constrói automaticamente um comando DistCp, submete os dados ao seu cluster Hadoop e, em seguida, monitoriza o estado da cópia. Recomendamos o modo DistCp da Fábrica de Dados para a migração de dados de um cluster Hadoop no local para O Azure.
- Modo de funcionamento da **integração nativa da Fábrica**de Dados : O DistCp não é uma opção em todos os cenários. Por exemplo, num ambiente de Redes Virtuais Azure, a ferramenta DistCp não suporta o peering privado Azure ExpressRoute com um ponto final de rede virtual de Armazenamento Azure. Além disso, em alguns casos, não quer usar o seu aglomerado Hadoop existente como motor para dados migratórios para não colocar cargas pesadas no seu cluster, o que pode afetar o desempenho dos empregos Existentes da ETL. Em vez disso, pode utilizar a capacidade nativa do tempo de execução da integração da Fábrica de Dados como o motor que copia dados do HDFS no local para o Azure.

Este artigo fornece as seguintes informações sobre ambas as abordagens:
> [!div class="checklist"]
> * Desempenho 
> * Copiar resiliência
> * Segurança da rede
> * Arquitetura de solução de alto nível 
> * Implementação de boas práticas  

## <a name="performance"></a>Desempenho

No modo DistCp da Fábrica de Dados, a entrada é a mesma que se utilizar a ferramenta DistCp de forma independente. O modo DistCp da Fábrica de Dados maximiza a capacidade do seu cluster Hadoop existente. Pode utilizar o DistCp para uma cópia inter-cluster ou intra-cluster de grande dimensão. 

A DistCp utiliza o MapReduce para efetuar a sua distribuição, manipulação e recuperação de erros e reportagens. Expande uma lista de ficheiros e diretórios em entrada para mapeamento de tarefas. Cada tarefa copia uma partição de ficheiros especificada na lista de origem. Pode utilizar a Data Factory integrada com o DistCp para construir oleodutos para utilizar totalmente a largura de banda da rede, o iOPS de armazenamento e a largura de banda para maximizar a produção de movimento de dados para o seu ambiente.  

O modo de funcionamento da integração nativa da Fábrica de Dados também permite o paralelismo a diferentes níveis. Pode utilizar o paralelismo para utilizar totalmente a largura de banda da rede, o iOPS de armazenamento e a largura de banda para maximizar a produção de movimento de dados:

- Uma única atividade de cópia pode tirar partido dos recursos de computação escaláveis. Com um tempo de funcionamento de integração auto-hospedado, pode aumentar manualmente a máquina ou escalar para várias máquinas[(até quatro nós).](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability) Uma única repartição de atividade de cópia o seu ficheiro definido em todos os nós. 
- Uma única atividade de cópia lê e escreve para a loja de dados utilizando vários fios. 
- O fluxo de controlo da Fábrica de Dados pode iniciar múltiplas atividades de cópia em paralelo. Por exemplo, pode utilizar um [loop Para Cada loop](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Para mais informações, consulte o guia de desempenho da [atividade da cópia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance).

## <a name="resilience"></a>Resiliência

No modo DistCp da Fábrica de Dados, pode utilizar diferentes parâmetros de linha de comando DistCp (por exemplo, `-i`ignorar falhas ou `-update`escrever dados quando o ficheiro de origem e o ficheiro de destino diferem em tamanho) para diferentes níveis de resiliência.

No modo de funcionamento de integração nativa da Fábrica de Dados, numa única execução de atividade de cópia, a Data Factory dispõe de um mecanismo de retry incorporado. Pode lidar com um certo nível de falhas transitórias nas lojas de dados ou na rede subjacente. 

Ao fazer cópias binárias do HDFS no local para o armazenamento blob e do HDFS no local para data Lake Store Gen2, a Data Factory executa automaticamente o checkpointing em grande medida. Se uma execução de atividade de cópia falhar ou passar horas, numa retentativa subsequente (certifique-se de que a contagem de retry é > 1), a cópia retoma a partir do último ponto de falha em vez de começar no início.

## <a name="network-security"></a>Segurança da rede 

Por padrão, a Data Factory transfere dados do HDFS no local para o armazenamento blob ou o Azure Data Lake Storage Gen2 utilizando uma ligação encriptada através do protocolo HTTPS. HTTPS fornece encriptação de dados em trânsito e evita escutas e ataques man-in-the-middle. 

Em alternativa, se não quiser que os dados sejam transferidos através da internet pública, para maior segurança, pode transferir dados através de um link de verificação privada via ExpressRoute. 

## <a name="solution-architecture"></a>Arquitetura de soluções

Esta imagem retrata dados migratórios através da internet pública:

![Diagrama que mostra a arquitetura de solução para migrar dados através de uma rede pública](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- Nesta arquitetura, os dados são transferidos de forma segura através da utilização de HTTPS através da internet pública. 
- Recomendamos a utilização do modo DistCp da Fábrica de Dados num ambiente de rede pública. Você pode aproveitar um poderoso cluster existente para obter a melhor cópia de entrada. Você também obtém o benefício de agendamento flexível e experiência de monitorização unificada da Data Factory.
- Para esta arquitetura, deve instalar o tempo de execução de integração auto-hospedado da Data Factory numa máquina windows atrás de uma firewall corporativa para submeter o comando DistCp ao seu cluster Hadoop e monitorizar o estado da cópia. Como a máquina não é o motor que irá mover dados (apenas para fins de controlo), a capacidade da máquina não afeta a produção de movimento de dados.
- Os parâmetros existentes do comando DistCp são suportados.

Esta imagem retrata dados migratórios através de um link privado: 

![Diagrama que mostra a arquitetura de solução para migrar dados através de uma rede privada](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- Nesta arquitetura, os dados são migrados através de um elo de observação privado via Azure ExpressRoute. Os dados nunca atravessam a internet pública.
- A ferramenta DistCp não suporta o peering privado ExpressRoute com um ponto final de rede de armazenamento azure. Recomendamos que utilize a capacidade nativa da Data Factory através do tempo de integração para migrar os dados.
- Para esta arquitetura, deve instalar o tempo de integração auto-hospedado da Data Factory num VM Windows na sua rede virtual Azure. Pode aumentar manualmente o seu VM ou dimensionar para vários VMs para utilizar totalmente a sua rede e armazenamento IOPS ou largura de banda.
- A configuração recomendada para começar por cada VM Azure (com o tempo de integração auto-hospedado instalado na Fábrica de Dados) é Standard_D32s_v3 com um vCPU de 32 vCPU e 128 GB de memória. Pode monitorizar o CPU e o uso da memória do VM durante a migração de dados para ver se precisa de aumentar o VM para um melhor desempenho ou para reduzir o VM para reduzir os custos.
- Você também pode escalar associando até quatro nódosos VM com um único tempo de integração auto-hospedado. Um único trabalho de cópia contra um tempo de funcionamento de integração auto-hospedado automaticamente divisória o conjunto de ficheiros e faz uso de todos os nós VM para copiar os ficheiros em paralelo. Para uma elevada disponibilidade, recomendamos que comece com dois nós VM para evitar um cenário de um ponto de falha durante a migração de dados.
- Quando utilizar esta arquitetura, a migração inicial de dados instantâneos e a migração de dados delta estão disponíveis para si.

## <a name="implementation-best-practices"></a>Implementação de boas práticas

Recomendamos que siga estas boas práticas quando implementar a migração de dados.

### <a name="authentication-and-credential-management"></a>Autenticação e gestão da credencial 

- Para autenticar o HDFS, pode utilizar [o Windows (Kerberos) ou o Anonymous](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties). 
- Vários tipos de autenticação são suportados para a ligação ao armazenamento Do Blob Azure.  Recomendamos vivamente a utilização [de identidades geridas para recursos Azure.](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) Construída supérbio de uma identidade de Data Factory gerida automaticamente no Azure Ative Directory (Azure AD), as identidades geridas permitem configurar os gasodutos sem fornecer credenciais na definição de serviço ligada. Em alternativa, pode autenticar o armazenamento blob utilizando um diretor de [serviço,](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)uma [assinatura de acesso partilhado](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)ou uma chave de conta de [armazenamento.](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication) 
- Vários tipos de autenticação também são suportados para a ligação ao Data Lake Storage Gen2.  Recomendamos vivamente a utilização de [identidades geridas para recursos Azure,](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)mas também pode utilizar um diretor de [serviço](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) ou uma chave de conta de [armazenamento.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) 
- Quando não está a usar identidades geridas para os recursos Azure, recomendamos vivamente [armazenar as credenciais no Cofre de Chaves Azure](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) para facilitar a gestão central e rotação de chaves sem modificar os serviços ligados à Data Factory. Esta é também uma [boa prática para CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migração inicial de dados instantâneos 

No modo DistCp da Fábrica de Dados, pode criar uma atividade de cópia para submeter o comando DistCp e utilizar diferentes parâmetros para controlar o comportamento inicial de migração de dados. 

No modo de funcionamento da integração nativa da Data Factory, recomendamos a partilha de dados, especialmente quando migra mais de 10 TB de dados. Para dividir os dados, utilize os nomes das pastas no HDFS. Em seguida, cada trabalho de cópia da Data Factory pode copiar uma partição de uma pasta de cada vez. Pode executar vários trabalhos de cópia da Data Factory simultaneamente para uma melhor produção.

Se algum dos trabalhos de cópia falhar devido a problemas transitórios de rede ou loja de dados, pode reexecutar o trabalho de cópia falhado para recarregar essa partição específica do HDFS. Outros trabalhos de cópia que estão a carregar outras divisórias não são afetados.

### <a name="delta-data-migration"></a>Migração de dados delta 

No modo DistCp da Fábrica de Dados, pode utilizar `-update`o parâmetro da linha de comando DistCp, escrever dados quando o ficheiro de origem e o ficheiro de destino diferem em tamanho, para a migração de dados delta.

No modo de integração nativa da Data Factory, a forma mais performativa de identificar ficheiros novos ou alterados do HDFS é utilizando uma convenção de nomeação com divisória seletiva. Quando os seus dados em HDFS foram divididos com informações de fatia de tempo no nome do ficheiro ou da pasta (por exemplo, */yyyy/mm/dd/file.csv),* o seu pipeline pode facilmente identificar quais ficheiros e pastas para copiar incrementalmente.

Alternativamente, se os seus dados em HDFS não estiverem divididos no tempo, a Data Factory pode identificar novos ou alterados ficheiros utilizando o seu valor **LastModifiedDate.** Data Factory digitaliza todos os ficheiros do HDFS e copia apenas ficheiros novos e atualizados que tenham um último carimbo de tempo modificado que seja maior do que um valor definido. 

Se tiver um grande número de ficheiros em HDFS, a verificação inicial do ficheiro pode demorar muito tempo, independentemente de quantos ficheiros correspondem à condição do filtro. Neste cenário, recomendamos que partique primeiro os dados utilizando a mesma partição que utilizou para a migração instantânea inicial. Em seguida, a verificação de ficheiros pode ocorrer em paralelo.

### <a name="estimate-price"></a>Preço estimado 

Considere o seguinte gasoduto para dados migratórios de HDFS para armazenamento de Blob Azure: 

![Diagrama que mostra o pipeline de preços](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

Vamos assumir as seguintes informações: 

- O volume total de dados é de 1 PB.
- Migra dados utilizando o modo de funcionamento de integração nativa da Fábrica de Dados.
- 1 PB é dividido em 1.000 divisórias e cada cópia move uma divisória.
- Cada atividade de cópia é configurada com um tempo de execução de integração auto-hospedado que está associado a quatro máquinas e que consegue uma entrada de 500 MBps.
- ForEach concurrency é definido para **4** e a produção agregada é de 2 GBps.
- No total, leva 146 horas para completar a migração.

Aqui está o preço estimado com base nos nossos pressupostos: 

![Tabela que mostra cálculos de preços](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Este é um exemplo hipotético de preços. O seu preço real depende da produção real no seu ambiente.
> O preço de um VM Do Windows Azure (com o tempo de execução de integração auto-hospedado instalado) não está incluído.

### <a name="additional-references"></a>Referências adicionais

- [Conector HDFS](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
- [Conector de armazenamento Azure Blob](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Conector do Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Guia de afinação de desempenho da atividade de cópia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Create and configure a self-hosted integration runtime](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime) (Criar e configurar um runtime de integração autoalojado)
- [Integração auto-hospedada tempo de execução alta disponibilidade e escalabilidade](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Considerações de segurança sobre movimento de dados](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Armazenar credenciais no Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Copiar um ficheiro incrementalmente com base num nome de ficheiro dividido pelo tempo](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Copiar ficheiros novos e alterados com base no LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Página de preços da fábrica de dados](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Passos seguintes

- [Copiar ficheiros de vários contentores utilizando a Azure Data Factory](solution-template-copy-files-multiple-containers.md)