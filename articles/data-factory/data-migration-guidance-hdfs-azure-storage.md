---
title: Migrar dados de um cluster Hadoop no local para o armazenamento de Azure
description: Saiba como usar a Azure Data Factory para migrar dados do cluster Hadoop para o Azure Storage.
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
ms.openlocfilehash: 3e691244c4c03635eb87a7905eff6756da5c04f9
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638130"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-hadoop-cluster-to-azure-storage"></a>Utilize a Azure Data Factory para migrar dados de um cluster Hadoop no local para o armazenamento Azure 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A Azure Data Factory fornece um mecanismo performante, robusto e rentável para migrar dados à escala desde o armazenamento de HDFS no local até ao armazenamento Azure Blob ou Azure Data Lake Storage Gen2. 

A Data Factory oferece duas abordagens básicas para a migração de dados de HDFS para Azure no local. Pode selecionar a abordagem com base no seu cenário. 

- **Modo DistCp da Fábrica de Dados** (recomendado): Na Data Factory, pode utilizar [o DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) (cópia distribuída) para copiar ficheiros como é para o armazenamento de Azure Blob (incluindo [cópia encenada)](./copy-activity-performance.md#staged-copy)ou Azure Data Lake Store Gen2. Utilize a Data Factory integrada com o DistCp para aproveitar um cluster poderoso existente para obter o melhor rendimento de cópia. Você também obtém o benefício de um agendamento flexível e uma experiência de monitorização unificada da Data Factory. Dependendo da configuração da data Factory, a atividade de cópia constrói automaticamente um comando DistCp, envia os dados para o seu cluster Hadoop e, em seguida, monitoriza o estado da cópia. Recomendamos o modo DistCp da Fábrica de Dados para migrar dados de um cluster Hadoop no local para Azure.
- **Data Factory modo de execução de integração nativa** : O DistCp não é uma opção em todos os cenários. Por exemplo, num ambiente de Redes Virtuais Azure, a ferramenta DistCp não suporta o azure ExpressRoute com um ponto final de rede virtual Azure Storage. Além disso, em alguns casos, você não quer usar o seu cluster Hadoop existente como um motor para migrar dados para que você não coloque cargas pesadas no seu cluster, o que pode afetar o desempenho dos empregos ETL existentes. Em vez disso, pode utilizar a capacidade nativa do tempo de integração da Data Factory como o motor que copia dados de HDFS no local para Azure.

Este artigo fornece as seguintes informações sobre ambas as abordagens:
> [!div class="checklist"]
> * Desempenho 
> * Copiar resiliência
> * Segurança da rede
> * Arquitetura de solução de alto nível 
> * Implementação das melhores práticas  

## <a name="performance"></a>Desempenho

No modo DistCp da Data Factory, a produção é a mesma que se utilizar a ferramenta DistCp de forma independente. O modo DistCp da Data Factory maximiza a capacidade do seu cluster Hadoop existente. Pode utilizar o DistCp para uma cópia inter-cluster ou intra-cluster. 

O DistCp utiliza o MapReduce para efetuar a sua distribuição, tratamento e recuperação de erros e reporte. Expande uma lista de ficheiros e diretórios para a entrada para o mapeamento de tarefas. Cada tarefa copia uma partição de ficheiros especificada na lista de origem. Pode utilizar a Data Factory integrada com o DistCp para construir oleodutos para utilizar plenamente a largura de banda da sua rede, o IOPS de armazenamento e a largura de banda para maximizar o movimento de dados para o seu ambiente.  

O modo de execução nativo da Data Factory também permite o paralelismo a diferentes níveis. Pode utilizar o paralelismo para utilizar totalmente a largura de banda da rede, o IOPS de armazenamento e a largura de banda para maximizar a produção de movimento de dados:

- Uma única atividade de cópia pode tirar partido dos recursos de computação escaláveis. Com um tempo de integração auto-hospedado, pode escalar manualmente a máquina ou escalar para várias máquinas[(até quatro nosdes).](./create-self-hosted-integration-runtime.md#high-availability-and-scalability) Uma única cópia de atividade partitions o seu ficheiro definido em todos os nós. 
- Uma única atividade de cópia lê e escreve para a loja de dados utilizando vários fios. 
- O fluxo de controlo da Data Factory pode iniciar múltiplas atividades de cópia em paralelo. Por exemplo, pode utilizar um [para cada laço](./control-flow-for-each-activity.md). 

Para mais informações, consulte o guia de desempenho da atividade da [cópia.](./copy-activity-performance.md)

## <a name="resilience"></a>Resiliência

No modo DistCp da Data Factory, pode utilizar diferentes parâmetros de linha de comando DistCp (por exemplo, `-i` ignorar falhas ou , escrever `-update` dados quando o ficheiro de origem e o ficheiro de destino diferem em tamanho) para diferentes níveis de resiliência.

No modo de funcionamento da integração nativa da Data Factory, numa única execução de atividade de cópia, a Data Factory dispõe de um mecanismo de relagem incorporado. Pode lidar com um certo nível de falhas transitórias nas lojas de dados ou na rede subjacente. 

Ao fazer cópias binárias de armazenamento HDFS no local e de HDFS no local para Data Lake Store Gen2, a Data Factory realiza automaticamente o checkpointing em grande parte. Se uma atividade de cópia falhar ou esgotar o tempo, numa nova tentativa subsequente (certifique-se de que a contagem de repetição é > 1), a cópia retoma do último ponto de falha em vez de começar no início.

## <a name="network-security"></a>Segurança da rede 

Por padrão, a Data Factory transfere dados de HDFS no local para o armazenamento blob ou Azure Data Lake Storage Gen2 utilizando uma ligação encriptada sobre o protocolo HTTPS. HTTPS fornece encriptação de dados em trânsito e evita escutas e ataques man-in-the-middle. 

Em alternativa, se não quiser que os dados sejam transferidos através da internet pública, para maior segurança, pode transferir dados através de um link de observação privado via ExpressRoute. 

## <a name="solution-architecture"></a>Arquitetura de soluções

Esta imagem retrata dados migratórios através da internet pública:

![Diagrama que mostra a arquitetura da solução para migrar dados sobre uma rede pública](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- Nesta arquitetura, os dados são transferidos de forma segura através do HTTPS através da internet pública. 
- Recomendamos a utilização do modo DistCp da Data Factory num ambiente de rede pública. Você pode aproveitar um poderoso cluster existente para obter o melhor rendimento de cópia. Você também obtém o benefício de agendamento flexível e experiência de monitorização unificada da Data Factory.
- Para esta arquitetura, deve instalar o tempo de integração auto-hospedado na Data Factory numa máquina Windows atrás de uma firewall corporativa para submeter o comando DistCp ao seu cluster Hadoop e monitorizar o estado da cópia. Como a máquina não é o motor que move os dados (apenas para fins de controlo), a capacidade da máquina não afeta a produção do movimento de dados.
- Os parâmetros existentes do comando DistCp são suportados.

Esta imagem retrata dados migratórios sobre um link privado: 

![Diagrama que mostra a arquitetura da solução para migrar dados sobre uma rede privada](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- Nesta arquitetura, os dados são migrados através de Azure ExpressRoute. Os dados nunca atravessam a internet pública.
- A ferramenta DistCp não suporta o espreitamento privado ExpressRoute com um ponto final de rede virtual Azure Storage. Recomendamos que utilize a capacidade nativa da Data Factory através do tempo de integração para migrar os dados.
- Para esta arquitetura, deve instalar o tempo de integração auto-hospedado na Data Factory num Windows VM na sua rede virtual Azure. Pode escalar manualmente o seu VM ou escalar para vários VMs para utilizar totalmente a sua rede e armazenamento IOPS ou largura de banda.
- A configuração recomendada para começar por cada Azure VM (com o tempo de integração auto-hospedado instalado) é Standard_D32s_v3 com um 32 vCPU e 128 GB de memória. Pode monitorizar o CPU e o uso da memória do VM durante a migração de dados para ver se precisa de aumentar o VM para um melhor desempenho ou para reduzir o VM para reduzir o custo.
- Também pode escalar associando até quatro nós VM com um único tempo de integração auto-hospedado. Um único trabalho de cópia que corre contra um tempo de integração auto-hospedado partilha automaticamente o conjunto de ficheiros e faz uso de todos os nós VM para copiar os ficheiros em paralelo. Para uma elevada disponibilidade, recomendamos que comece com dois nós VM para evitar um cenário único de falha durante a migração de dados.
- Quando utilizar esta arquitetura, a migração inicial de dados instantâneos e a migração de dados delta estão disponíveis para si.

## <a name="implementation-best-practices"></a>Implementação das melhores práticas

Recomendamos que siga estas boas práticas quando implementar a migração de dados.

### <a name="authentication-and-credential-management"></a>Autenticação e gestão de credenciais 

- Para autenticar para HDFS, pode utilizar [o Windows (Kerberos) ou o Anonymous](./connector-hdfs.md#linked-service-properties). 
- Vários tipos de autenticação são suportados para a ligação ao armazenamento Azure Blob.  Recomendamos vivamente a utilização [de identidades geridas para recursos Azure.](./connector-azure-blob-storage.md#managed-identity) Construídas sobre uma identidade de Data Factory gerida automaticamente no Azure Ative Directory (Azure AD), identidades geridas permitem configurar oleodutos sem fornecer credenciais na definição de serviço ligada. Em alternativa, pode autenticar para o armazenamento blob utilizando um [principal serviço,](./connector-azure-blob-storage.md#service-principal-authentication)uma [assinatura de acesso partilhado](./connector-azure-blob-storage.md#shared-access-signature-authentication)ou uma chave de conta de [armazenamento.](./connector-azure-blob-storage.md#account-key-authentication) 
- Vários tipos de autenticação também são suportados para a ligação ao Data Lake Storage Gen2.  Recomendamos vivamente a utilização [de identidades geridas para recursos Azure,](./connector-azure-data-lake-storage.md#managed-identity)mas também pode utilizar um [principal serviço](./connector-azure-data-lake-storage.md#service-principal-authentication) ou uma chave de conta [de armazenamento.](./connector-azure-data-lake-storage.md#account-key-authentication) 
- Quando não está a usar identidades geridas para recursos Azure, recomendamos vivamente [o armazenamento das credenciais no Azure Key Vault](./store-credentials-in-key-vault.md) para facilitar a gestão central e a rotação das chaves sem modificar os serviços ligados à Data Factory. Esta é também uma [das melhores práticas para o CI/CD.](./continuous-integration-deployment.md#best-practices-for-cicd) 

### <a name="initial-snapshot-data-migration"></a>Migração inicial de dados instantâneos 

No modo DistCp da Data Factory, pode criar uma atividade de cópia para submeter o comando DistCp e utilizar diferentes parâmetros para controlar o comportamento inicial de migração de dados. 

No modo de execução nativo da Data Factory, recomendamos a partição de dados, especialmente quando migra mais de 10 TB de dados. Para dividir os dados, utilize os nomes das pastas no HDFS. Em seguida, cada trabalho de cópia da Data Factory pode copiar uma divisória de cada vez. Pode executar vários trabalhos de cópia da Data Factory simultaneamente para uma melhor produção.

Se algum dos trabalhos de cópia falhar devido a problemas transitórios de rede ou de armazenamento de dados, pode refazer o trabalho de cópia falhado para recarregar essa partição específica do HDFS. Outros trabalhos de cópia que carregam outras divisórias não são afetados.

### <a name="delta-data-migration"></a>Migração de dados delta 

No modo DistCp da Data Factory, pode utilizar o parâmetro da linha de comando DistCp, `-update` escrever dados quando o ficheiro de origem e o ficheiro de destino diferem em tamanho, para a migração de dados delta.

No modo de integração nativa da Data Factory, a forma mais performante de identificar ficheiros novos ou alterados do HDFS é utilizando uma convenção de nomeação com divisórias de tempo. Quando os seus dados em HDFS foram divididos por tempo com informações de fatias de tempo no ficheiro ou nome da pasta (por exemplo, */yyyy/mm/dd/file.csv),* o seu pipeline pode facilmente identificar quais ficheiros e pastas copiar gradualmente.

Em alternativa, se os seus dados em HDFS não forem divididos em tempo, a Data Factory pode identificar ficheiros novos ou alterados utilizando o seu valor **LastModifiedDate.** A Data Factory digitaliza todos os ficheiros do HDFS e copia apenas ficheiros novos e atualizados que tenham um último timetamp modificado que seja superior a um valor definido. 

Se tiver um grande número de ficheiros em HDFS, a verificação inicial do ficheiro pode demorar muito tempo, independentemente de quantos ficheiros correspondam à condição do filtro. Neste cenário, recomendamos que parta primeiro os dados utilizando a mesma partição que utilizou para a migração instantânea inicial. Em seguida, a verificação de ficheiros pode ocorrer em paralelo.

### <a name="estimate-price"></a>Preço estimado 

Considere o seguinte oleoduto para a migração de dados de HDFS para a azure Blob armazenamento: 

![Diagrama que mostra o gasoduto de preços](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

Vamos assumir as seguintes informações: 

- O volume total de dados é de 1 PB.
- Migrar dados utilizando o modo de execução nativo da Data Factory.
- 1 PB é dividido em 1.000 divisórias e cada cópia move uma divisória.
- Cada atividade de cópia é configurada com um tempo de integração auto-hospedado que está associado a quatro máquinas e que alcança 500 MBps de produção.
- ForEach concurrency está definido para **4** e a produção agregada é de 2 GBps.
- No total, são precisas 146 horas para completar a migração.

Aqui está o preço estimado com base nos nossos pressupostos: 

![Tabela que mostra cálculos de preços](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Este é um exemplo hipotético de preços. O seu preço real depende da produção real no seu ambiente.
> O preço de um Azure Windows VM (com tempo de execução de integração auto-hospedado instalado) não está incluído.

### <a name="additional-references"></a>Referências adicionais

- [Conector HDFS](./connector-hdfs.md)
- [Conector de armazenamento Azure Blob](./connector-azure-blob-storage.md)
- [Conector do Azure Data Lake Storage Gen2](./connector-azure-data-lake-storage.md)
- [Guia de afinação de desempenho da atividade de cópia](./copy-activity-performance.md)
- [Create and configure a self-hosted integration runtime](./create-self-hosted-integration-runtime.md) (Criar e configurar um runtime de integração autoalojado)
- [Integração auto-hospedada, elevada disponibilidade e escalabilidade](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)
- [Considerações de segurança sobre movimento de dados](./data-movement-security-considerations.md)
- [Armazenar credenciais no Azure Key Vault](./store-credentials-in-key-vault.md)
- [Copie um ficheiro incrementalmente baseado num nome de ficheiro dividido pelo tempo](./tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)
- [Copie ficheiros novos e alterados com base no LastModifiedDate](./tutorial-incremental-copy-lastmodified-copy-data-tool.md)
- [Página de preços da Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Passos seguintes

- [Copie ficheiros de vários contentores utilizando a Azure Data Factory](solution-template-copy-files-multiple-containers.md)