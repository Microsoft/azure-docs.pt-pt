---
title: Migrar dados do Amazon S3 para o Armazenamento do Azure
description: Use Azure Data Factory para migrar dados do Amazon S3 para o armazenamento do Azure.
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/04/2019
ms.openlocfilehash: 30990c3d1e3f885e8984227425d3e8e5c44b9286
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927481"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Use Azure Data Factory para migrar dados do Amazon S3 para o armazenamento do Azure 

O Azure Data Factory fornece um mecanismo de desempenho, robusto e econômico para migrar dados em escala do Amazon S3 para o armazenamento de BLOBs do Azure ou Azure Data Lake Storage Gen2.  Este artigo fornece as seguintes informações para desenvolvedores e engenheiros de dados: 

> [!div class="checklist"]
> * Desempenho 
> * Resiliência de cópia
> * Segurança da rede
> * Arquitetura de solução de alto nível 
> * Práticas recomendadas de implementação  

## <a name="performance"></a>Desempenho

O ADF oferece uma arquitetura sem servidor que permite paralelismo em diferentes níveis, o que permite aos desenvolvedores criar pipelines para utilizar totalmente a largura de banda da rede, bem como IOPS de armazenamento e largura de banda para maximizar a taxa de transferência de movimentação de dados para o seu ambiente. 

Os clientes migraram com êxito petabytes de dados consistindo em centenas de milhões de arquivos do Amazon S3 para o armazenamento de BLOBs do Azure, com uma taxa de transferência sustentada de 2 GBps e mais. 

![desempenho](media/data-migration-guidance-s3-to-azure-storage/performance.png)

A figura acima ilustra como você pode obter grandes velocidades de movimentação de dados por meio de diferentes níveis de paralelismo:
 
- Uma única atividade de cópia pode aproveitar os recursos de computação escalonáveis: ao usar Azure Integration Runtime, você pode especificar [até 256 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) para cada atividade de cópia de maneira sem servidor; ao usar o Integration Runtime auto-hospedado, você pode escalar verticalmente o computador manualmente ou escalar horizontalmente para vários computadores ([até 4 nós](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)) e uma única atividade de cópia particionará seu conjunto de arquivos em todos os nós. 
- Uma única atividade de cópia lê e grava no armazenamento de dados usando vários threads. 
- O fluxo de controle do ADF pode iniciar várias atividades de cópia em paralelo, por exemplo, usando [loop for each](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

## <a name="resilience"></a>Resiliência

Dentro de uma única execução da atividade de cópia, o ADF tem um mecanismo de repetição interno para que possa lidar com um certo nível de falhas transitórias nos armazenamentos de dados ou na rede subjacente. 

Ao fazer cópias binárias de S3 para BLOB e de S3 para ADLS Gen2, o ADF executa automaticamente o ponto de verificação.  Se uma execução de atividade de cópia tiver falhado ou atingido o tempo limite, em uma nova tentativa subsequente (certifique-se de repetir a contagem > 1), a cópia será retomada do último ponto de falha em vez de começar do início. 

## <a name="network-security"></a>Segurança da rede 

Por padrão, o ADF transfere dados do Amazon S3 para o armazenamento de BLOBs do Azure ou Azure Data Lake Storage Gen2 usando a conexão criptografada via protocolo HTTPS.  O HTTPS fornece criptografia de dados em trânsito e impede ataques de interceptação e Man-in-the-Middle. 

Como alternativa, se você não quiser que os dados sejam transferidos pela Internet pública, poderá obter maior segurança transferindo dados por um link de emparelhamento privado entre o AWS Direct Connect e o Azure Express Route.  Consulte a arquitetura da solução abaixo sobre como isso pode ser feito. 

## <a name="solution-architecture"></a>Arquitetura de soluções

Migrar dados pela Internet pública:

![solução-arquitetura-pública-rede](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- Nessa arquitetura, os dados são transferidos com segurança usando HTTPS pela Internet pública. 
- O Amazon S3 de origem, bem como o armazenamento de BLOBs do Azure de destino ou Azure Data Lake Storage Gen2 estão configurados para permitir o tráfego de todos os endereços IP de rede.  Consulte a segunda arquitetura abaixo sobre como você pode restringir o acesso à rede para um intervalo de IP específico. 
- Você pode escalar verticalmente facilmente a quantidade de potência em maneira sem servidor para utilizar totalmente sua rede e largura de banda de armazenamento para que você possa obter a melhor taxa de transferência para seu ambiente. 
- A migração de instantâneo inicial e a migração de dados Delta podem ser obtidas usando essa arquitetura. 

Migrar dados por link privado: 

![solução-arquitetura-privada-rede](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- Nessa arquitetura, a migração de dados é feita por meio de um link de emparelhamento privado entre o AWS Direct Connect e o Azure Express Route, de modo que os dados nunca percorram pela Internet pública.  Ele requer o uso do VPC AWS e da rede virtual do Azure. 
- Você precisa instalar o tempo de execução de integração auto-hospedado do ADF em uma VM do Windows em sua rede virtual do Azure para obter essa arquitetura.  Você pode escalar verticalmente suas VMs de IR hospedadas automaticamente ou escalar horizontalmente para várias VMs (até 4 nós) para utilizar totalmente sua rede e IOPS/largura de banda de armazenamento. 
- Se for aceitável transferir dados por HTTPS, mas você quiser bloquear o acesso à rede S3 para um intervalo de IP específico, você pode adotar uma variação dessa arquitetura removendo o VPC AWS e substituindo o link privado por HTTPS.  Convém manter o IR do Azure virtual e auto-Hosted na VM do Azure para que você possa ter um IP roteável publicamente estático para fins de lista de permissões. 
- A migração de dados de instantâneo inicial e a migração de dados Delta podem ser obtidas usando essa arquitetura. 

## <a name="implementation-best-practices"></a>Práticas recomendadas de implementação 

### <a name="authentication-and-credential-management"></a>Autenticação e gerenciamento de credenciais 

- Para autenticar a conta do Amazon S3, você deve usar a [chave de acesso para a conta iam](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service#linked-service-properties). 
- Há suporte para vários tipos de autenticação para se conectar ao armazenamento de BLOBs do Azure.  O uso de [identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) é altamente recomendável: criado com base em um ADF gerenciado automaticamente no Azure AD, ele permite que você configure pipelines sem fornecer credenciais na definição de serviço vinculado.  Como alternativa, você pode autenticar no armazenamento de BLOBs do Azure usando a [entidade de serviço](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), a [assinatura de acesso compartilhado](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)ou a [chave da conta de armazenamento](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Também há suporte para vários tipos de autenticação para se conectar ao Azure Data Lake Storage Gen2.  O uso de [identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) é altamente recomendado, embora a [entidade de serviço](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) ou a chave de conta de [armazenamento](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) também possa ser usada. 
- Quando você não estiver usando identidades gerenciadas para recursos do Azure, [o armazenamento das credenciais em Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) é altamente recomendável para facilitar o gerenciamento e a rotação centralizado das chaves sem modificar os serviços vinculados do ADF.  Essa também é uma das [práticas recomendadas para CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migração de dados de instantâneo inicial 

A partição de dados é recomendada especialmente ao migrar mais de 10 TB de dados.  Para particionar os dados, aproveite a configuração ' prefix ' para filtrar as pastas e os arquivos no Amazon S3 por nome e, em seguida, cada trabalho de cópia do ADF pode copiar uma partição de cada vez.  Você pode executar vários trabalhos de cópia do ADF simultaneamente para obter uma melhor taxa de transferência. 

Se qualquer um dos trabalhos de cópia falhar devido a um problema transitório de rede ou de armazenamento de dados, você poderá executar novamente o trabalho de cópia com falha para recarregar essa partição específica novamente do AWS S3.  Todos os outros trabalhos de cópia que carregam outras partições não serão afetados. 

### <a name="delta-data-migration"></a>Migração de dados Delta 

A maneira mais eficaz de identificar arquivos novos ou alterados do AWS S3 é usar a Convenção de nomenclatura com particionamento de tempo – quando os dados no AWS S3 tiverem sido particionados com informações de fatia de tempo no nome do arquivo ou da pasta (por exemplo,/yyyy/mm/dd/File.csv), o pipeline poderá identificar facilmente quais arquivos/pastas serão copiados incrementalmente. 

Como alternativa, se seus dados no AWS S3 não estiverem com o tempo particionado, o ADF poderá identificar arquivos novos ou alterados por seu LastModifiedDate.   A maneira como ele funciona é que o ADF examinará todos os arquivos do AWS S3 e copiará apenas o arquivo novo e atualizado cujo carimbo de data/hora da última modificação seja maior que um determinado valor.  Lembre-se de que, se você tiver um grande número de arquivos no S3, a verificação de arquivo inicial poderá demorar muito tempo, independentemente de quantos arquivos corresponderem à condição do filtro.  Nesse caso, é recomendável particionar os dados primeiro, usando a mesma configuração de "prefixo" para a migração de instantâneo inicial, para que a verificação de arquivo possa ocorrer em paralelo.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>Para cenários que exigem o tempo de execução de integração auto-hospedado na VM do Azure 

Se você estiver migrando dados sobre o link privado ou se quiser permitir um intervalo IP específico no firewall do Amazon S3, será necessário instalar o tempo de execução de integração auto-hospedado na VM do Windows do Azure. 

- A configuração recomendada para começar com o para cada VM do Azure é Standard_D32s_v3 com 32 vCPU e 128 GB de memória.  Você pode continuar monitorando a utilização de CPU e de memória da VM IR durante a migração de dados para ver se você precisa escalar verticalmente a VM para melhorar o desempenho ou reduzir verticalmente a VM para economizar custo. 
- Você também pode escalar horizontalmente associando até quatro nós de VM com um único IR de hospedagem interna.  Um único trabalho de cópia em execução em um IR de hospedagem interna particionará automaticamente o conjunto de arquivos e utilizará todos os nós de VM para copiar os arquivos em paralelo.  Para alta disponibilidade, é recomendável começar com 2 nós de VM para evitar um único ponto de falha durante a migração de dados. 

### <a name="rate-limiting"></a>Limitação de taxa 

Como prática recomendada, realize uma POC de desempenho com um conjunto de exemplo representativo, para que você possa determinar um tamanho de partição apropriado. 

Comece com uma única partição e uma única atividade de cópia com a configuração padrão DIU.  Aumente gradualmente a configuração de DIU até atingir o limite de largura de banda de sua rede ou o limite de IOPS/largura de banda dos armazenamentos de dados, ou se você tiver atingido o máximo de 256 DIU permitido em uma única atividade de cópia. 

Em seguida, Aumente gradualmente o número de atividades de cópia simultâneas até atingir os limites do seu ambiente. 

Quando você encontrar erros de limitação relatados pela atividade de cópia do ADF, reduza a configuração de simultaneidade ou DIU no ADF ou considere aumentar os limites de largura de banda/IOPS da rede e dos armazenamentos de dados.  

### <a name="estimating-price"></a>Estimando o preço 

> [!NOTE]
> Este é um exemplo de preço hipotético.  Seu preço real depende da taxa de transferência real em seu ambiente.

Considere o pipeline a seguir construído para migrar dados do S3 para o armazenamento de BLOBs do Azure: 

![preço-pipeline](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

Vamos supor o seguinte: 

- O volume de dados total é 2 PB 
- Migrando dados por HTTPS usando a primeira arquitetura da solução 
- 2 PB são divididos em partições de 1 K e cada cópia move uma partição 
- Cada cópia é configurada com DIU = 256 e atinge uma taxa de transferência de 1 GBps 
- A simultaneidade ForEach está definida como 2 e a taxa de transferência agregada é de 2 GBps 
- No total, leva 292 horas para concluir a migração 

Este é o preço estimado com base nas suposições acima: 

![preço-tabela](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>Referências adicionais 
- [Conector de serviço de armazenamento simples da Amazon](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service)
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

## <a name="template"></a>Modelo

Aqui está o [modelo](solution-template-migration-s3-azure.md) para começar a migrar petabytes de dados que consistem em centenas de milhões de arquivos do Amazon S3 para Azure data Lake Storage Gen2.

## <a name="next-steps"></a>Passos seguintes

- [Copiar arquivos de vários contêineres com Azure Data Factory](solution-template-copy-files-multiple-containers.md)