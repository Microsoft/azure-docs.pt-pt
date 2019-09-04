---
title: Usar Azure Data Factory para migrar dados do servidor Netezza local para o Azure | Microsoft Docs
description: Use Azure Data Factory para migrar dados do servidor Netezza local para o Azure.
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
ms.date: 9/03/2019
ms.openlocfilehash: 4690fd81247035267861b06c204c6db7a052eba5
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259564"
---
# <a name="use-azure-data-factory-to-migrate-data-from-on-premises-netezza-server-to-azure"></a>Usar Azure Data Factory para migrar dados do servidor Netezza local para o Azure 

O Azure Data Factory fornece um mecanismo de desempenho, robusto e econômico para migrar dados em escala do servidor Netezza local para o armazenamento do Azure ou o Azure SQL Data Warehouse. Este artigo fornece as seguintes informações para desenvolvedores e engenheiros de dados:

> [!div class="checklist"]
> * Desempenho 
> * Resiliência de cópia
> * Segurança da rede
> * Arquitetura de solução de alto nível 
> * Práticas recomendadas de implementação  

## <a name="performance"></a>Desempenho

O Azure Data Factory oferece uma arquitetura sem servidor que permite o paralelismo em diferentes níveis, o que permite aos desenvolvedores criar pipelines para utilizar totalmente a largura de banda da rede, bem como a largura de banda do banco de dados, para maximizar a taxa de transferência de movimento para o seu ambiente.

![desempenho](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

- Uma única atividade de cópia pode aproveitar os recursos de computação escalonáveis: ao usar Azure Integration Runtime, você pode especificar [até 256 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) para cada atividade de cópia de maneira sem servidor; ao usar o Integration Runtime auto-hospedado, você pode escalar verticalmente o computador manualmente ou escalar horizontalmente para vários computadores ([até 4 nós](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)), e uma única atividade de cópia distribuirá sua partição em todos os nós. 
- Uma única atividade de cópia lê e grava no armazenamento de dados usando vários threads. 
- Azure Data Factory fluxo de controle pode iniciar várias atividades de cópia em paralelo, por exemplo, usando [for Each loop](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Você pode obter mais detalhes no [Guia de desempenho da atividade de cópia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)

## <a name="resilience"></a>Resiliência

Em uma execução de atividade de cópia única, Azure Data Factory tem um mecanismo de repetição interno para que possa lidar com um certo nível de falhas transitórias nos armazenamentos de dados ou na rede subjacente.

Azure Data Factory atividade de cópia também oferece duas maneiras de lidar com linhas incompatíveis ao copiar dados entre armazenamentos de dados de origem e de coletor. Você pode anular e reprovar a atividade de cópia quando dados incompatíveis forem encontrados ou continuar copiando os dados REST ignorando linhas de dados incompatíveis. Além disso, você pode registrar em log as linhas incompatíveis no armazenamento de BLOBs do Azure ou Azure Data Lake Store para saber a causa da falha, corrigir os dados na fonte de dados e repetir a atividade de cópia.

## <a name="network-security"></a>Segurança da rede 

Por padrão, Azure Data Factory transfere dados do servidor Netezza local para o armazenamento do Azure ou o Azure SQL Data Warehouse usando a conexão criptografada via protocolo HTTPS. Ele fornece criptografia de dados em trânsito e impede ataques de interceptação e Man-in-the-Middle.

Como alternativa, se você não quiser que os dados sejam transferidos pela Internet pública, poderá obter maior segurança transferindo dados por um link de emparelhamento privado por meio da rota expressa do Azure. Consulte a arquitetura da solução abaixo sobre como isso pode ser feito.

## <a name="solution-architecture"></a>Arquitetura da solução

Migrar dados pela Internet pública:

![solução-arquitetura-pública-rede](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

- Nessa arquitetura, os dados são transferidos com segurança usando HTTPS pela Internet pública.
- Você precisa instalar Azure Data Factory o tempo de execução de integração auto-hospedado em um computador Windows por trás do firewall corporativo para alcançar essa arquitetura. Verifique se seu tempo de execução de integração do Azure Data Factory auto-hospedado em um computador com Windows pode obter acesso diretamente ao servidor Netezza. Você pode escalar verticalmente seu computador manualmente ou escalar horizontalmente para vários computadores a fim de utilizar totalmente sua rede e a largura de banda de armazenamento de dados para copiar dados.
- A migração de dados de instantâneo inicial e a migração de dados Delta podem ser obtidas usando essa arquitetura.

Migrar dados por link privado: 

![solução-arquitetura-privada-rede](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

- Nessa arquitetura, a migração de dados é feita por meio de um link de emparelhamento privado por meio da rota expressa do Azure, de modo que os dados nunca percorram pela Internet pública. 
- Você precisa instalar Azure Data Factory o tempo de execução de integração auto-hospedado em uma VM do Windows em sua rede virtual do Azure para obter essa arquitetura. Você pode escalar verticalmente suas VMs manualmente ou escalar horizontalmente para várias VMs para utilizar totalmente sua rede e a largura de banda de armazenamento de dados para copiar dados.
- A migração de dados de instantâneo inicial e a migração de dados Delta podem ser obtidas usando essa arquitetura.

## <a name="implementation-best-practices"></a>Práticas recomendadas de implementação 

### <a name="authentication-and-credential-management"></a>Autenticação e gerenciamento de credenciais 

- Para autenticar no Netezza, você pode usar a [autenticação ODBC por meio da cadeia de conexão](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties). 
- Há suporte para vários tipos de autenticação para se conectar ao armazenamento de BLOBs do Azure.  O uso de [identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) é altamente recomendável: criado com base em um Azure data Factory gerenciado automaticamente identificado no Azure AD, ele permite que você configure pipelines sem fornecer credenciais na definição de serviço vinculado.  Como alternativa, você pode autenticar no armazenamento de BLOBs do Azure usando a [entidade de serviço](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), a [assinatura de acesso compartilhado](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)ou a [chave da conta de armazenamento](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Também há suporte para vários tipos de autenticação para se conectar ao Azure Data Lake Storage Gen2.  O uso de [identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) é altamente recomendado, embora a [entidade de serviço](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) ou a chave de [conta de armazenamento](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) também possa ser usada. 
- Também há suporte para vários tipos de autenticação para se conectar ao Azure SQL Data Warehouse. O uso de [identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity) é altamente recomendado, embora a [entidade de serviço](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication) ou a [autenticação SQL](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication) também possam ser usadas.
- Quando você não estiver usando identidades gerenciadas para recursos do Azure, é altamente recomendável [armazenar as credenciais no Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) para facilitar o gerenciamento centralizado e a rotação das chaves sem modificar Azure data Factory serviços vinculados.  Essa também é uma das [práticas recomendadas para CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migração de dados de instantâneo inicial 

Para tabelas pequenas quando o tamanho do volume é menor que 100 GB ou pode ser migrado para o Azure em 2 horas, você pode fazer com que cada trabalho de cópia carregue dados por tabela. Você pode executar vários Azure Data Factory trabalhos de cópia para carregar diferentes tabelas simultaneamente para obter uma melhor taxa de transferência. 

Dentro de cada trabalho de cópia, você também pode alcançar algum nível de paralelismo usando a opção [parallelCopies](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy) com partição de dados para executar consultas paralelas e copiar dados por partições. Há duas opções de partição de dados a serem escolhidas com detalhes abaixo.
- Você é incentivado a começar da fatia de dados, pois ela é mais eficiente.  Verifique se o número de paralelismo na configuração parallelCopies está abaixo do número total de partições de fatia de dados em sua tabela no servidor Netezza.  
- Se o tamanho do volume de cada partição de fatia de dados ainda for grande (por exemplo, maior que 10 GB), você será incentivado a alternar para a partição de intervalo dinâmico, na qual terá mais flexibilidade para definir o número de partições e o tamanho do volume para cada partição por coluna de partição, limite superior e limite inferior.

Para as tabelas grandes quando o tamanho do volume é maior que 100 GB ou não pode ser migrado para o Azure em 2 horas, é recomendável particionar os dados por consulta personalizada e, em seguida, fazer cada trabalho de cópia copiar uma partição por vez. Você pode executar vários Azure Data Factory trabalhos de cópia simultaneamente para obter uma melhor taxa de transferência. Lembre-se de que, para cada destino de trabalho de cópia carregar uma partição por consulta personalizada, você ainda poderá habilitar o paralelismo por meio de uma fatia de dados ou de um intervalo dinâmico para aumentar a taxa de transferência. 

Se qualquer um dos trabalhos de cópia falhar devido a um problema transitório de rede ou de armazenamento de dados, você poderá executar novamente o trabalho de cópia com falha para recarregar a partição específica novamente da tabela. Todos os outros trabalhos de cópia que carregam outras partições não serão afetados.

Ao carregar dados no Azure SQL Data Warehouse, o polybase é sugerido para ser habilitado no trabalho de cópia com um armazenamento de BLOBs do Azure como preparo.

### <a name="delta-data-migration"></a>Migração de dados Delta 

A maneira de identificar as linhas novas ou atualizadas de sua tabela é usar uma coluna timestamp ou incrementar a chave no esquema e, em seguida, armazenar o valor mais recente como marca-d ' água alta em uma tabela externa, que pode ser usada para filtrar os dados Delta para o carregamento de dados da próxima vez. 

Tabelas diferentes podem usar coluna de marca d' água diferente para identificar as linhas novas ou atualizadas. Sugerimos que você crie uma tabela de controle externo em que cada linha representa uma tabela no servidor Netezza com seu nome de coluna de marca d' água específico e valor de marca d' água alta. 

### <a name="self-hosted-integration-runtime-configuration-on-azure-vm-or-machine"></a>Configuração de tempo de execução de integração auto-hospedado no computador ou VM do Azure

Considerando que você está migrando dados do servidor Netezza para o Azure, independentemente de o Netezza Server estar sob a promessa por trás do firewall da sua empresa ou em um ambiente de VNET, você precisa instalar o tempo de execução de integração auto-hospedado no computador ou VM do Windows, que é o mecanismo a ser movido dado.

- A configuração recomendada para começar para cada máquina ou VM é com 32 vCPU e 128 GB de memória. Você pode continuar monitorando a utilização de CPU e de memória do computador IR durante a migração de dados para ver se você precisa escalar verticalmente o computador para melhorar o desempenho ou reduzir verticalmente o computador para economizar o custo.
- Você também pode escalar horizontalmente associando até quatro nós com um único IR auto-hospedado. Um único trabalho de cópia em execução em um IR de hospedagem interna aproveitará automaticamente todos os nós de VM para copiar os dados em paralelo. Para alta disponibilidade, é recomendável começar com 2 nós de VM para evitar um único ponto de falha durante a migração de dados.

### <a name="rate-limiting"></a>Limitação de taxa

Como prática recomendada, realize uma POC de desempenho com um conjunto de exemplo representativo, para que você possa determinar um tamanho de partição apropriado para cada atividade de cópia. Sugerimos que você faça com que cada partição seja carregada no Azure dentro de duas horas.  

Comece com uma única atividade de cópia com uma única máquina de IR hospedada internamente para copiar uma tabela. Aumente gradualmente a configuração de parallelCopies com base no número de partições de fatia de dados em sua tabela e veja se toda a tabela pode ser carregada no Azure dentro de 2 horas de acordo com a taxa de transferência que você vê do trabalho de cópia. 

Se não puder ser obtida e, ao mesmo tempo, a capacidade do nó IR auto-hospedado e o armazenamento de dados não for totalmente utilizado, Aumente gradualmente o número de atividades de cópia simultâneas até atingir os limites de sua rede ou limite de largura de banda dos armazenamentos de dados. 

Continue monitorando a utilização de CPU/memória no computador IR auto-hospedado e esteja pronto para escalar verticalmente o computador ou escalar horizontalmente para vários computadores quando você vir a CPU/memória totalmente utilizada. 

Quando você encontrar erros de limitação relatados por Azure Data Factory atividade de cópia, reduza a configuração de simultaneidade ou parallelCopies em Azure Data Factory ou considere aumentar os limites de largura de banda/IOPS da rede e dos armazenamentos de dados. 


### <a name="estimating-price"></a>Estimando o preço 

Considere o pipeline a seguir construído para migrar dados do servidor Netezza local para o Azure SQL data warehouse:

![preço-pipeline](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

Vamos supor o seguinte: 

- O volume de dados total é de 50 TB. 
- Migrando dados usando a primeira arquitetura da solução (o servidor Netezza é local por trás do firewall)
- 50 TB são divididos em partições 500 e cada atividade de cópia move uma partição.
- Cada atividade de cópia é configurada com um IR auto-hospedado em até 4 computadores e alcança a taxa de transferência de 20 MBps. (Na atividade de cópia, parallelCopies é definido como 4 e cada thread para carregar dados da tabela alcança a taxa de transferência de 5 MBps)
- A simultaneidade ForEach é definida como 3 e a taxa de transferência agregada é de 60 MBps.
- No total, leva 243 horas para concluir a migração.

Este é o preço estimado com base nas suposições acima: 

![preço-tabela](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> Este é um exemplo de preço hipotético. Seu preço real depende da taxa de transferência real em seu ambiente. O preço do computador com Windows (com o tempo de execução de integração auto-hospedado instalado) não está incluído. 

### <a name="additional-references"></a>Referências adicionais 
- [Migração de dados de data warehouse relacionais locais para o Azure usando Azure Data Factory](https://azure.microsoft.com/mediahandler/files/resourcefiles/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/Data_migration_from_on-prem_RDW_to_ADLS_using_ADF.pdf)
- [Conector do Netezza](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [Conector ODBC](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Conector de armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Conector de Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Conector de SQL Data Warehouse do Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [Guia de ajuste de desempenho da atividade de cópia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Criando e configurando Integration Runtime auto-hospedados](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Escalabilidade e alta disponibilidade do tempo de execução de integração auto-hospedado](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Considerações de segurança de movimentação de dados](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Armazenar credenciais no Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Copiar dados incrementalmente de uma tabela](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [Copiar dados incrementalmente de várias tabelas](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Página de preços do Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Passos Seguintes

- [Copiar arquivos de vários contêineres com Azure Data Factory](solution-template-copy-files-multiple-containers.md)