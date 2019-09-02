---
title: Usar Azure Data Factory para migrar dados do cluster Hadoop local para o armazenamento do Azure | Microsoft Docs
description: Use Azure Data Factory para migrar dados do cluster Hadoop local para o armazenamento do Azure.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 8/30/2019
ms.openlocfilehash: 1b26b22fa9cdf9f6671702ceb9640aa39a6ecf17
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211610"
---
# <a name="use-azure-data-factory-to-migrate-data-from-on-premises-hadoop-cluster-to-azure-storage"></a>Usar Azure Data Factory para migrar dados do cluster Hadoop local para o armazenamento do Azure 

O Azure Data Factory fornece um mecanismo de desempenho, robusto e econômico para migrar dados em escala do HDFS local para o armazenamento de BLOBs do Azure ou Azure Data Lake Storage Gen2. Basicamente, Azure Data Factory contém duas abordagens para migrar dados do HDFS local para o Azure. Você pode selecionar cada um deles com base em seu cenário. 

- Modo de DistCp do ADF. Suporte a ADF usando [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) para copiar arquivos no estado em que se encontram no blob do Azure (incluindo [cópia em etapas](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)) ou Azure data Lake Store; nesse caso, ele pode aproveitar totalmente a potência do cluster em vez de executar no ir (Integration Runtime) auto-hospedado do ADF. Ele fornecerá uma melhor taxa de transferência de cópia, especialmente quando o cluster for muito potente. Com base em sua configuração no Azure Data Factory, a atividade de cópia cria automaticamente um comando DistCp, envia para o cluster Hadoop e monitora o status da cópia. Ao usar o ADF integrado ao DistCp, o cliente não só pode aproveitar seu cluster potente existente para obter a melhor taxa de transferência de cópia, mas também obter o benefício da programação flexível e da experiência de monitoramento unificada do ADF. Por padrão, o modo de DistCp do ADF é a maneira recomendada de migrar dados do cluster Hadoop local para o Azure.
- Modo IR nativo do ADF. Em alguns cenários, o DistCp não pode funcionar para seus casos (por exemplo, no ambiente de VNET, a ferramenta DistCp não dá suporte ao emparelhamento privado de rota expressa + ponto de extremidade de VNet do armazenamento do Azure). Além disso, em algum momento você não deseja usar seu cluster Hadoop existente como o mecanismo para migrar os dados, pois ele trará cargas pesadas no cluster, o que pode afetar o desempenho dos trabalhos ETL existentes. Se esse caso, você pode usar a funcionalidade nativa do ADF, em que o tempo de execução de integração do ADF (IR) pode ser o mecanismo para copiar os dados do HDFS local para o Azure.

Este artigo fornece as seguintes informações de ambas as abordagens para engenheiros de dados e desenvolvedores:
> [!div class="checklist"]
> * Desempenho 
> * Resiliência de cópia
> * Segurança da rede
> * Arquitetura de solução de alto nível 
> * Práticas recomendadas de implementação  

## <a name="performance"></a>Desempenho

No modo de DistCp do ADF, a taxa de transferência é a mesma que usar a ferramenta DistCp de forma independente, o que aproveita a capacidade do cluster Hadoop existente. DistCp (cópia distribuída) é uma ferramenta usada para grandes cópias entre clusters. Ele usa o MapReduce para afetar sua distribuição, tratamento de erros e recuperação e relatórios. Ele expande uma lista de arquivos e diretórios em entrada para mapear tarefas, cada um dos quais copiará uma partição dos arquivos especificados na lista de origem. Usando o ADF integrado ao DistCp, você pode criar pipelines para utilizar totalmente sua largura de banda de rede, bem como IOPS de armazenamento e largura de banda para maximizar a taxa de transferência de movimentação de dados para seu ambiente.  

No modo IR nativo do ADF, ele também permite paralelismo em diferentes níveis, o que pode utilizar totalmente sua largura de banda de rede, bem como IOPS de armazenamento e largura de banda para maximizar a taxa de transferência de movimentação de dados, expandindo manualmente a máquina IR hospedada internamente ou escalar horizontalmente para vários computadores de IR hospedados internamente.

- Uma única atividade de cópia pode aproveitar os recursos de computação escalonáveis. Com o tempo de execução de integração auto-hospedado, você pode escalar verticalmente o computador manualmente ou escalar horizontalmente para vários computadores ([até 4 nós](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)) e uma única atividade de cópia particionará seu conjunto de arquivos em todos os nós. 
- Uma única atividade de cópia lê e grava no armazenamento de dados usando vários threads. 
- O fluxo de controle do ADF pode iniciar várias atividades de cópia em paralelo, por exemplo, usando [loop for each](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Você pode obter mais detalhes no [Guia de desempenho da atividade de cópia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)

## <a name="resilience"></a>Resiliência

No modo de DistCp do ADF, você pode aproveitar diferentes parâmetros de linha de comando do DistCp (por exemplo,-i, ignorar falhas;-atualizar, gravar dados quando o arquivo de origem e o arquivo de destino diferem no tamanho) para atingir níveis diferentes de resiliência.

No modo IR nativo do ADF, em uma única execução da atividade de cópia, o ADF tem um mecanismo de repetição interno para que possa lidar com um certo nível de falhas transitórias nos armazenamentos de dados ou na rede subjacente. Ao fazer cópia binária do HDFS local para o blob e do HDFS local para ADLS Gen2, o ADF executa automaticamente o ponto de verificação em uma grande extensão. Se uma execução de atividade de cópia tiver falhado ou atingido o tempo limite, em uma nova tentativa subsequente (certifique-se de repetir a contagem > 1), a cópia será retomada do último ponto de falha em vez de começar do início.

## <a name="network-security"></a>Segurança da rede 

Por padrão, o ADF transfere dados do HDFS local para o armazenamento de BLOBs do Azure ou Azure Data Lake Storage Gen2 usando conexão criptografada via protocolo HTTPS.  O HTTPS fornece criptografia de dados em trânsito e impede ataques de interceptação e Man-in-the-Middle. 

Como alternativa, se você não quiser que os dados sejam transferidos pela Internet pública, poderá obter maior segurança transferindo dados por um link de emparelhamento privado por meio da rota expressa do Azure. Consulte a arquitetura da solução abaixo sobre como isso pode ser feito.

## <a name="solution-architecture"></a>Arquitetura da solução

Migrar dados pela Internet pública:

![solução-arquitetura-pública-rede](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- Nessa arquitetura, os dados são transferidos com segurança usando HTTPS pela Internet pública. 
- O modo DistCp do ADF é recomendado para uso em um ambiente de rede pública. Ao fazer isso, você não só pode aproveitar o cluster poderoso existente para obter a melhor taxa de transferência de cópia, mas também obter o benefício da programação flexível e da experiência de monitoramento unificada do ADF.
- Você precisa instalar o tempo de execução de integração auto-hospedado do ADF em um computador Windows por trás do firewall corporativo para enviar o comando DistCp para o cluster Hadoop e monitorar o status da cópia. Considerando que este computador não será o mecanismo para mover dados (somente para fins de controle), a capacidade do computador não afeta a taxa de transferência da movimentação de dados.
- Há suporte para os parâmetros existentes do comando DistCp.


Migrar dados por link privado: 

![solução-arquitetura-privada-rede](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- Nessa arquitetura, a migração de dados é feita por meio de um link de emparelhamento privado por meio da rota expressa do Azure, de modo que os dados nunca percorram pela Internet pública.
- A ferramenta DistCp não dá suporte ao emparelhamento privado de rota expressa + ponto de extremidade de VNet do armazenamento do Azure, portanto, você é incentivado a usar o recurso nativo do ADF por meio do Integration Runtime para migrar os dados.
- Você precisa instalar o tempo de execução de integração auto-hospedado do ADF em uma VM do Windows em sua rede virtual do Azure para obter essa arquitetura. Você pode escalar verticalmente sua VM ou escalar horizontalmente para várias VMs a fim de utilizar totalmente sua rede e IOPS/largura de banda de armazenamento.
- A configuração recomendada para começar com o para cada VM do Azure (com o tempo de execução de integração auto-hospedado do ADF instalado) é Standard_D32s_v3 com 32 vCPU e 128-GB de memória. Você pode continuar monitorando a utilização de CPU e memória da VM durante a migração de dados para ver se você precisa escalar verticalmente a VM para melhorar o desempenho ou reduzir verticalmente a VM para economizar o custo.
- Você também pode escalar horizontalmente associando até quatro nós de VM com um único IR de hospedagem interna. Um único trabalho de cópia em execução em um IR de hospedagem interna particionará automaticamente o conjunto de arquivos e utilizará todos os nós de VM para copiar os arquivos em paralelo. Para alta disponibilidade, é recomendável iniciar com dois nós de VM para evitar um único ponto de falha durante a migração de dados.
- A migração de dados de instantâneo inicial e a migração de dados Delta podem ser obtidas usando essa arquitetura.


## <a name="implementation-best-practices"></a>Práticas recomendadas de implementação 

### <a name="authentication-and-credential-management"></a>Autenticação e gerenciamento de credenciais 

- Para autenticar no HDFS, você pode usar [o Windows (Kerberos) ou anônimo](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties). 
- Há suporte para vários tipos de autenticação para se conectar ao armazenamento de BLOBs do Azure.  O uso de [identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) é altamente recomendável: criado com base em um ADF gerenciado automaticamente no Azure AD, ele permite que você configure pipelines sem fornecer credenciais na definição de serviço vinculado.  Como alternativa, você pode autenticar no armazenamento de BLOBs do Azure usando a [entidade de serviço](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), a [assinatura de acesso compartilhado](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)ou a [chave da conta de armazenamento](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Também há suporte para vários tipos de autenticação para se conectar ao Azure Data Lake Storage Gen2.  O uso de [identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) é altamente recomendado, embora a [entidade de serviço](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) ou a chave de [conta de armazenamento](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) também possa ser usada. 
- Quando você não estiver usando identidades gerenciadas para recursos do Azure, [o armazenamento das credenciais em Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) é altamente recomendável para facilitar o gerenciamento e a rotação centralizado das chaves sem modificar os serviços vinculados do ADF.  Essa também é uma das [práticas recomendadas para CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migração de dados de instantâneo inicial 

No modo de DistCp do ADF, você pode criar uma atividade de cópia para enviar o comando DistCp com parâmetros diferentes para controlar o comportamento inicial de migração de dados. 

No modo IR nativo do ADF, a partição de dados é recomendada especialmente ao migrar mais de 10 TB de dados. Para particionar os dados, aproveite os nomes de pasta no HDFS e, em seguida, cada trabalho de cópia do ADF pode copiar uma partição de pasta de cada vez. Você pode executar vários trabalhos de cópia do ADF simultaneamente para obter uma melhor taxa de transferência.
Se qualquer um dos trabalhos de cópia falhar devido a um problema transitório de rede ou de armazenamento de dados, você poderá executar novamente o trabalho de cópia com falha para recarregar essa partição específica novamente no HDFS. Todos os outros trabalhos de cópia que carregam outras partições não serão afetados.

### <a name="delta-data-migration"></a>Migração de dados Delta 

No modo de DistCp do ADF, você pode aproveitar os parâmetros de linha de comando do DistCp "-Update, gravar dados quando o arquivo de origem e o arquivo de destino diferem em tamanho" para obter a migração de dados Delta.

No modo IR nativo do ADF, a maneira mais eficaz de identificar arquivos novos ou alterados do HDFS é usar a Convenção de nomenclatura com particionamento de tempo – quando os dados no HDFS têm tempo particionado com informações de fatias de tempo no nome do arquivo ou da pasta (por exemplo,/yyyy/mm/dd/ File. csv), então seu pipeline pode identificar facilmente quais arquivos/pastas copiar incrementalmente.
Como alternativa, se seus dados no HDFS não forem particionados por tempo, o ADF poderá identificar arquivos novos ou alterados por seus LastModifiedDate. A maneira como ele funciona é que o ADF examinará todos os arquivos do HDFS e copiará apenas o arquivo novo e atualizado cujo carimbo de data/hora da última modificação seja maior que um determinado valor. Lembre-se de que, se você tiver um grande número de arquivos no HDFS, a verificação de arquivo inicial poderá levar muito tempo, independentemente de quantos arquivos corresponderem à condição do filtro. Nesse caso, é recomendável particionar os dados primeiro, usando a mesma partição para a migração de instantâneo inicial, para que a verificação de arquivo possa ocorrer em paralelo.

### <a name="estimating-price"></a>Estimando o preço 

Considere o pipeline a seguir construído para migrar dados do HDFS para o armazenamento de BLOBs do Azure: 

![preço-pipeline](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

Vamos supor o seguinte: 

- O volume de dados total é de 1 PB
- Migrando dados usando a segunda arquitetura de solução (modo IR nativo do ADF)
- 1 PB é dividido em 1000 partições e cada cópia move uma partição
- Cada atividade de cópia é configurada com um IR hospedado internamente associado a 4 computadores e alcança a taxa de transferência de 500 MBps.
- A simultaneidade ForEach está definida como 4 e a taxa de transferência agregada é de 2 GBps
- No total, leva 146 horas para concluir a migração.


Este é o preço estimado com base nas suposições acima: 

![preço-tabela](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Este é um exemplo de preço hipotético.  Seu preço real depende da taxa de transferência real em seu ambiente.
> O preço da VM do Windows do Azure (com o tempo de execução de integração auto-hospedado instalado) não está incluído.

### <a name="additional-references"></a>Referências adicionais 
- [Conector do HDFS](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
- [Conector de armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Conector de Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Guia de ajuste de desempenho da atividade de cópia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Criando e configurando Integration Runtime auto-hospedados](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Escalabilidade e alta disponibilidade do tempo de execução de integração auto-hospedado](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Considerações de segurança de movimentação de dados](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Armazenar credenciais no Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Copiar arquivo incrementalmente com base no nome do arquivo particionado](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Copiar arquivos novos e alterados com base em LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Página de preços do ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Passos Seguintes

- [Copiar arquivos de vários contêineres com Azure Data Factory](solution-template-copy-files-multiple-containers.md)