---
title: Migrar dados do Amazon S3 para o Armazenamento do Azure
description: Utilize a Azure Data Factory para migrar dados da Amazon S3 para o Armazenamento Azure.
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
ms.openlocfilehash: 3f40ad7346219b48a38ade38b2a75ddf71940875
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416421"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Utilize a Fábrica de Dados Azure para migrar dados da Amazon S3 para o Armazenamento Azure 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A Azure Data Factory fornece um mecanismo performativo, robusto e rentável para migrar dados em escala da Amazon S3 para o Azure Blob Storage ou Azure Data Lake Storage Gen2.  Este artigo fornece as seguintes informações para engenheiros de dados e desenvolvedores: 

> [!div class="checklist"]
> * Desempenho 
> * Copiar resiliência
> * Segurança da rede
> * Arquitetura de solução de alto nível 
> * Implementação de boas práticas  

## <a name="performance"></a>Desempenho

A ADF oferece uma arquitetura sem servidores que permite o paralelismo a diferentes níveis, o que permite que os desenvolvedores construam oleodutos para utilizar totalmente a largura de banda da sua rede, bem como o armazenamento de IOPS e largura de banda para maximizar a produção de movimento de dados para o seu ambiente. 

Os clientes migraram com sucesso petabytes de dados constituídos por centenas de milhões de ficheiros da Amazon S3 para o Azure Blob Storage, com uma entrada sustentada de 2 GBps e superior. 

![desempenho](media/data-migration-guidance-s3-to-azure-storage/performance.png)

A imagem acima ilustra como você pode alcançar grandes velocidades de movimento de dados através de diferentes níveis de paralelismo:
 
- Uma única atividade de cópia pode tirar partido dos recursos de computação escaláveis: ao utilizar o Tempo de Execução de Integração Azure, pode especificar [até 256 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) para cada atividade de cópia de forma sem servidores; ao utilizar o tempo de funcionamento de integração auto-hospedado, pode aumentar manualmente a máquina ou escalar para várias máquinas[(até 4 nós](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)), e uma única atividade de cópia irá dividir o seu ficheiro definido em todos os nós. 
- Uma única atividade de cópia lê e escreve para a loja de dados usando vários fios. 
- O fluxo de controlo ADF pode iniciar múltiplas atividades de cópia em paralelo, por exemplo, utilizando [Para cada loop](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

## <a name="resilience"></a>Resiliência

No âmbito de uma única execução de atividade de cópia, a ADF tem um mecanismo de retry incorporado para que possa lidar com um determinado nível de falhas transitórias nas lojas de dados ou na rede subjacente. 

Ao fazer cópias binárias de S3 a Blob e de S3 a ADLS Gen2, a ADF executa automaticamente o checkpointing.  Se uma execução de atividade de cópia tiver falhado ou cronometrado, numa retentativa subsequente, a cópia retoma a partir do último ponto de falha em vez de começar desde o início. 

## <a name="network-security"></a>Segurança da rede 

Por padrão, a ADF transfere dados da Amazon S3 para o Azure Blob Storage ou o Azure Data Lake Storage Gen2 utilizando uma ligação encriptada através do protocolo HTTPS.  HTTPS fornece encriptação de dados em trânsito e evita escutas e ataques man-in-the-middle. 

Em alternativa, se não quiser que os dados sejam transferidos através da Internet pública, pode obter uma maior segurança transferindo dados através de uma ligação de pares privado seletiva entre a AWS Direct Connect e a Azure Express Route.  Consulte a arquitetura de solução abaixo sobre como isso pode ser alcançado. 

## <a name="solution-architecture"></a>Arquitetura de soluções

Migrar dados através da Internet pública:

![solução-arquitetura-rede pública](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- Nesta arquitetura, os dados são transferidos de forma segura usando HTTPS através da Internet pública. 
- Tanto a fonte Amazon S3 como o destino Azure Blob Storage ou Azure Data Lake Storage Gen2 estão configurados para permitir o tráfego de todos os endereços IP da rede.  Consulte a segunda arquitetura abaixo sobre como pode restringir o acesso à rede a uma gama IP específica. 
- Pode facilmente aumentar a quantidade de potência de cavalo de forma sem servidores para utilizar totalmente a sua rede e largura de banda de armazenamento para que possa obter a melhor entrada para o seu ambiente. 
- Tanto a migração instantânea inicial como a migração de dados delta podem ser alcançadas usando esta arquitetura. 

Migrar dados através de ligações privadas: 

![solução-arquitetura-rede privada](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- Nesta arquitetura, a migração de dados é feita através de uma ligação privada entre a AWS Direct Connect e a Azure Express Route de tal forma que os dados nunca atravessam a Internet pública.  Requer a utilização da rede AWS VPC e Azure Virtual. 
- Você precisa instalar o tempo de execução de integração auto-hospedado a ADF em um Windows VM dentro da sua rede virtual Azure para alcançar esta arquitetura.  Pode aumentar manualmente os seus VMs de INFRAVERMELHOS auto-hospedados ou escalar para vários VMs (até 4 nós) para utilizar totalmente a sua rede e armazenamento IOPS/largura de banda. 
- Se for aceitável transferir dados através de HTTPS mas pretender bloquear o acesso à rede à fonte S3 para uma gama IP específica, pode adotar uma variação desta arquitetura removendo AWS VPC e substituindo o link privado por HTTPS.  Você vai querer manter O Iv-Virtual e o IR auto-hospedado no Azure VM para que você possa ter um IP estática publicamente routable para finalidade de whitelisting. 
- Tanto a migração inicial de dados instantâneos como a migração de dados delta podem ser alcançadas usando esta arquitetura. 

## <a name="implementation-best-practices"></a>Implementação de boas práticas 

### <a name="authentication-and-credential-management"></a>Autenticação e gestão da credencial 

- Para autenticar a conta Amazon S3, deve utilizar a chave de [acesso para a conta IAM](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service#linked-service-properties). 
- Vários tipos de autenticação são suportados para ligar ao Armazenamento De Blob Azure.  A utilização de [identidades geridas para recursos Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) é altamente recomendada: construída em cima de uma identificação ADF gerida automaticamente em Azure AD, permite configurar gasodutos sem fornecer credenciais na definição de Serviço Linked.  Em alternativa, pode autenticar o Armazenamento De Blob Azure utilizando [o Diretor de Serviço,](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication) [a assinatura de acesso partilhado](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)ou a chave da conta de [armazenamento.](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication) 
- Vários tipos de autenticação também são suportados para ligar ao Azure Data Lake Storage Gen2.  A utilização de [identidades geridas para recursos Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) é altamente recomendada, embora o principal de [serviço](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) ou a [chave da conta](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) de armazenamento também possam ser usados. 
- Quando não está a utilizar identidades geridas para os recursos Do Azure, [armazenar as credenciais no Cofre chave azure](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) é altamente recomendado para facilitar a gestão central e rotação de chaves sem modificar os serviços ligados à ADF.  Esta é também uma das [melhores práticas para ci/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migração inicial de dados instantâneos 

A partilha de dados é recomendada especialmente quando migra mais de 100 TB de dados.  Para dividir os dados, aproveite a definição de 'prefixo' para filtrar as pastas e ficheiros no Amazon S3 pelo nome, e em seguida, cada trabalho de cópia ADF pode copiar uma partição de cada vez.  Pode executar vários trabalhos de cópia ADF simultaneamente para uma melhor utilização. 

Se algum dos trabalhos de cópia falhar devido a problema transitório de rede ou loja de dados, pode reexecutar o trabalho de cópia falhado para recarregar essa partição específica novamente da AWS S3.  Todos os outros trabalhos de cópia que carregam outras divisórias não serão afetados. 

### <a name="delta-data-migration"></a>Migração de dados delta 

A forma mais performativa de identificar ficheiros novos ou alterados da AWS S3 é utilizando a convenção de nomeação com divisórias – quando os seus dados no AWS S3 foram divididos com informações de fatias de tempo no ficheiro ou nome da pasta (por exemplo, /yyyy/mm/dd/file.csv), então o seu pipeline pode facilmente identificar quais ficheiros/pastas para copiar incrementalmente. 

Em alternativa, se os seus dados no AWS S3 não estiverem divididos no tempo, a ADF pode identificar novos ou alterados ficheiros pelo seu LastModifiedDate.   A forma como funciona é que a ADF irá digitalizar todos os ficheiros da AWS S3, e apenas copiar o novo e atualizado ficheiro cujo último carimbo de tempo modificado é superior a um determinado valor.  Esteja ciente de que se tiver um grande número de ficheiros no S3, a verificação inicial de ficheiros pode demorar muito tempo, independentemente de quantos ficheiros correspondem à condição do filtro.  Neste caso, sugere-se que partiparta os dados primeiro, utilizando a mesma definição de 'prefixo' para a migração instantânea inicial, para que a verificação de ficheiros possa acontecer em paralelo.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>Para cenários que requerem tempo de integração auto-hospedado no Azure VM 

Quer esteja a migrar dados através de link privado ou se pretende permitir uma gama IP específica na firewall Amazon S3, precisa de instalar o tempo de execução de integração auto-hospedado no Azure Windows VM. 

- A configuração recomendada para começar para cada VM Azure é Standard_D32s_v3 com 32 vCPU e memória de 128 GB.  Pode continuar a monitorizar a CPU e a utilização da memória do VM de IR durante a migração de dados para ver se precisa de aumentar ainda mais o VM para um melhor desempenho ou reduzir o VM para economizar custos. 
- Você também pode escalar associando até 4 vM nodes com um único IR auto-hospedado.  Uma única cópia que corre contra um IR auto-hospedado irá automaticamente dividir o conjunto de ficheiros e alavancar todos os nós VM para copiar os ficheiros em paralelo.  Para uma elevada disponibilidade, recomenda-se que comece com 2 nós vM para evitar um único ponto de falha durante a migração de dados. 

### <a name="rate-limiting"></a>Rate limiting (Limitação de taxa) 

Como uma boa prática, conduza um POC de desempenho com um conjunto de dados representativo da amostra, para que possa determinar um tamanho apropriado da partição. 

Comece com uma única partição e uma única atividade de cópia com a definição padrão de DIU.  Aumente gradualmente a definição de DIU até atingir o limite de largura de banda da sua rede ou limite de largura de banda iOPS/largura de banda das lojas de dados, ou atingiu o max 256 DIU permitido numa única atividade de cópia. 

Em seguida, aumente gradualmente o número de atividades de cópia simultânea até atingir limites do seu ambiente. 

Quando se deparacom erros de estrangulamento reportados pela atividade de cópia ADF, reduza a regulação da moeda ou do DIU na ADF, ou considere aumentar os limites de largura de banda/IOPS da rede e das lojas de dados.  

### <a name="estimating-price"></a>Preço estimado 

> [!NOTE]
> Este é um exemplo hipotético de preços.  O seu preço real depende da produção real no seu ambiente.

Considere o seguinte gasoduto construído para dados migratórios de S3 para O Armazenamento de Blob Azure: 

![pipeline de preços](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

Assumamos o seguinte: 

- O volume total de dados é de 2 PB 
- Dados migratórios sobre HTTPS usando a arquitetura de primeira solução 
- 2 PB é dividido em divisórias de 1 K e cada cópia move uma divisória 
- Cada cópia é configurada com DIU=256 e obtém 1 gBps de entrada 
- ForEach concurrency é definido para 2 e a produção agregada é de 2 GBps 
- No total, leva 292 horas para completar a migração 

Aqui está o preço estimado com base nos pressupostos acima referidos: 

![tabela de preços](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>Referências adicionais 
- [Conector do Serviço de Armazenamento Simples da Amazon](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service)
- [Conector de armazenamento Azure Blob](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Conector do Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Guia de afinação de desempenho da atividade de cópia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Criação e configuração do tempo de execução de integração auto-hospedado](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Auto-hospedado integração tempo de execução HA e escalabilidade](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Considerações de segurança sobre movimento de dados](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Armazenar credenciais no Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Copiar ficheiro incrementalmente com base no nome de ficheiro dividido pelo tempo](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Copiar ficheiros novos e alterados com base no LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Página de preços ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>Modelo

Aqui está o [modelo](solution-template-migration-s3-azure.md) para começar a migrar petabytes de dados que consistem em centenas de milhões de ficheiros da Amazon S3 para o Azure Data Lake Storage Gen2.

## <a name="next-steps"></a>Passos seguintes

- [Copiar ficheiros de vários contentores com fábrica de dados Azure](solution-template-copy-files-multiple-containers.md)
