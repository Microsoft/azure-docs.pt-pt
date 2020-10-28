---
title: Migrar dados do Amazon S3 para o Armazenamento do Azure
description: Utilize a Azure Data Factory para migrar dados do Amazon S3 para o Azure Storage.
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
ms.openlocfilehash: be1cb7abbc243e3f79e183223fbbb32380f5d02d
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638045"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Utilize a Azure Data Factory para migrar dados do Amazon S3 para o Azure Storage 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A Azure Data Factory fornece um mecanismo performante, robusto e rentável para migrar dados à escala de Amazon S3 para Azure Blob Storage ou Azure Data Lake Storage Gen2.  Este artigo fornece as seguintes informações para engenheiros de dados e desenvolvedores: 

> [!div class="checklist"]
> * Desempenho 
> * Copiar resiliência
> * Segurança da rede
> * Arquitetura de solução de alto nível 
> * Implementação das melhores práticas  

## <a name="performance"></a>Desempenho

A ADF oferece uma arquitetura sem servidor que permite o paralelismo a diferentes níveis, o que permite que os desenvolvedores construam oleodutos para utilizar plenamente a largura de banda da sua rede, bem como o armazenamento IOPS e largura de banda para maximizar a produção de movimento de dados para o seu ambiente. 

Os clientes migraram com sucesso petabytes de dados que consistem em centenas de milhões de ficheiros da Amazon S3 para a Azure Blob Storage, com uma produção sustentada de 2 GBps e superior. 

![O diagrama mostra várias divisórias de ficheiros numa loja A W S S3 com ações de cópia associadas ao Azure Blob Storage A D L S Gen2.](media/data-migration-guidance-s3-to-azure-storage/performance.png)

A imagem acima ilustra como você pode alcançar grandes velocidades de movimento de dados através de diferentes níveis de paralelismo:
 
- Uma única atividade de cópia pode tirar partido dos recursos de cálculo escaláveis: ao utilizar o Tempo de Execução da Integração Azure, pode especificar [até 256 DIUs](./copy-activity-performance.md#data-integration-units) para cada atividade de cópia de forma sem servidor; ao utilizar o tempo de funcionamento de integração auto-hospedado, pode escalar manualmente a máquina ou escalar para várias máquinas[(até 4 nós](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)), e uma única atividade de cópia dividirá o seu ficheiro definido em todos os nós. 
- Uma única atividade de cópia lê e escreve para a loja de dados utilizando vários fios. 
- O fluxo de controlo ADF pode iniciar várias atividades de cópia em paralelo, por exemplo, utilizando [para cada laço](./control-flow-for-each-activity.md). 

## <a name="resilience"></a>Resiliência

Dentro de uma única atividade de cópia executada, a ADF tem mecanismo de relemisão incorporado para que possa lidar com um certo nível de falhas transitórias nas lojas de dados ou na rede subjacente. 

Ao fazer cópias binárias de S3 a Blob e de S3 a ADLS Gen2, a ADF realiza automaticamente o checkpointing.  Se uma atividade de cópia tiver falhado ou tiver falhado, numa nova tentativa subsequente, a cópia retoma-se a partir do último ponto de falha em vez de começar do início. 

## <a name="network-security"></a>Segurança da rede 

Por padrão, a ADF transfere dados do Amazon S3 para o Azure Blob Storage ou Azure Data Lake Storage Gen2 utilizando a ligação encriptada sobre o protocolo HTTPS.  HTTPS fornece encriptação de dados em trânsito e evita escutas e ataques man-in-the-middle. 

Em alternativa, se não quiser que os dados sejam transferidos através da Internet pública, pode obter uma maior segurança transferindo dados através de uma ligação de pares privado entre a AWS Direct Connect e a Azure Express Route.  Consulte a arquitetura da solução abaixo sobre como isso pode ser alcançado. 

## <a name="solution-architecture"></a>Arquitetura de soluções

Migrar dados através da Internet pública:

![O diagrama mostra a migração através da Internet por H T T P de uma loja A W S S3 através do Tempo de Execução da Integração Azure em A D F Azure para Azure Storage. O tempo de funcionação tem um canal de controlo com a Data Factory.](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- Nesta arquitetura, os dados são transferidos de forma segura utilizando HTTPS através da Internet pública. 
- Tanto a fonte Amazon S3 como o destino Azure Blob Storage ou Azure Data Lake Storage Gen2 estão configurados para permitir o tráfego de todos os endereços IP da rede.  Consulte a segunda arquitetura abaixo sobre como pode restringir o acesso à rede a uma gama ip específica. 
- Pode facilmente aumentar a quantidade de potência de cavalos de forma sem servidor para utilizar plenamente a sua rede e largura de banda de armazenamento para que possa obter a melhor produção para o seu ambiente. 
- Tanto a migração instantânea inicial como a migração de dados delta podem ser alcançadas usando esta arquitetura. 

Migrar dados sobre ligação privada: 

![O diagrama mostra a migração através de uma ligação de observação privada de uma loja A W S S3 através do tempo de integração auto-hospedado em máquinas virtuais Azure para pontos finais de serviço V Net para Azure Storage. O tempo de funcionação tem um canal de controlo com a Data Factory.](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- Nesta arquitetura, a migração de dados é feita através de uma ligação de pares privado entre a AWS Direct Connect e a Azure Express Route de modo a que os dados nunca se cruzem através da Internet pública.  Requer a utilização da rede AWS VPC e Azure Virtual. 
- Você precisa instalar o tempo de integração auto-hospedado da ADF em um Windows VM dentro da sua rede virtual Azure para alcançar esta arquitetura.  Pode escalar manualmente os seus IVMs auto-hospedados ou escalar para vários VMs (até 4 nós) para utilizar totalmente a sua rede e armazenamento IOPS/largura de banda. 
- Se for aceitável transferir dados através de HTTPS mas pretender bloquear o acesso à rede à fonte S3 para uma gama IP específica, pode adotar uma variação desta arquitetura removendo o VPC AWS e substituindo o link privado por HTTPS.  Você vai querer manter O Azure Virtual e auto-hospedado IR em Azure VM para que você possa ter um IP estático publicamente encaminhável para fins de filtragem. 
- Tanto a migração inicial de dados instantâneos como a migração de dados delta podem ser alcançadas usando esta arquitetura. 

## <a name="implementation-best-practices"></a>Implementação das melhores práticas 

### <a name="authentication-and-credential-management"></a>Autenticação e gestão de credenciais 

- Para autenticar na conta Amazon S3, tem de usar a [chave de acesso para a conta IAM.](./connector-amazon-simple-storage-service.md#linked-service-properties) 
- Vários tipos de autenticação são suportados para se ligarem ao Armazenamento Azure Blob.  A utilização de [identidades geridas para recursos Azure](./connector-azure-blob-storage.md#managed-identity) é altamente recomendada: construída em cima de uma identificação ADF gerida automaticamente em Azure AD, permite-lhe configurar gasodutos sem fornecer credenciais na definição de Serviço Linked.  Em alternativa, pode autenticar o Azure Blob Storage utilizando [o Service Principal,](./connector-azure-blob-storage.md#service-principal-authentication) [a assinatura de acesso partilhado](./connector-azure-blob-storage.md#shared-access-signature-authentication)ou a chave da conta de [armazenamento.](./connector-azure-blob-storage.md#account-key-authentication) 
- Vários tipos de autenticação também são suportados para ligar ao Azure Data Lake Storage Gen2.  A utilização de [identidades geridas para recursos Azure](./connector-azure-data-lake-storage.md#managed-identity) é altamente recomendada, embora a chave principal de [serviço](./connector-azure-data-lake-storage.md#service-principal-authentication) ou [conta de armazenamento](./connector-azure-data-lake-storage.md#account-key-authentication) também possa ser usada. 
- Quando não está a utilizar identidades geridas para recursos Azure, [armazenar as credenciais no Azure Key Vault](./store-credentials-in-key-vault.md) é altamente recomendado para facilitar a gestão central e rotação das chaves sem modificar os serviços ligados à ADF.  Esta é também uma das [melhores práticas para o CI/CD](./continuous-integration-deployment.md#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migração inicial de dados instantâneos 

Recomenda-se a partição de dados especialmente quando migra mais de 100 TB de dados.  Para dividir os dados, aproveite a definição de 'prefixo' para filtrar as pastas e ficheiros no Amazon S3 pelo nome, e então cada trabalho de cópia ADF pode copiar uma partição de cada vez.  Pode executar vários trabalhos de cópia ADF simultaneamente para uma melhor produção. 

Se algum dos trabalhos de cópia falhar devido a problemas transitórios de rede ou de loja de dados, pode voltar a realizar o trabalho de cópia falhado para recarregar essa divisória específica a partir de AWS S3.  Todos os outros trabalhos de cópia que carregam outras divisórias não serão afetados. 

### <a name="delta-data-migration"></a>Migração de dados delta 

A forma mais performante de identificar ficheiros novos ou alterados a partir de AWS S3 é utilizando convenção de nomeação com partição de tempo – quando os seus dados em AWS S3 foram divididos com informações de fatias de tempo no ficheiro ou nome de pasta (por exemplo, /yyyy/mm/dd/file.csv), então o seu pipeline pode facilmente identificar quais ficheiros/pastas para copiar incrementalmente. 

Alternativamente, se os seus dados em AWS S3 não estiverem divididos no tempo, a ADF pode identificar ficheiros novos ou alterados através do seu Último Instante.   A forma como funciona é que a ADF irá digitalizar todos os ficheiros a partir de AWS S3, e apenas copiar o novo ficheiro atualizado cujo último tempotabo modificado é maior do que um determinado valor.  Esteja ciente de que se tiver um grande número de ficheiros no S3, a verificação inicial do ficheiro pode demorar muito tempo, independentemente de quantos ficheiros correspondam à condição do filtro.  Neste caso, é sugerido dividir os dados primeiro, utilizando a mesma definição de 'prefixo' para a migração inicial do instantâneo, para que a digitalização do ficheiro possa acontecer em paralelo.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>Para cenários que requerem o tempo de integração auto-hospedado no Azure VM 

Quer esteja a migrar dados sobre ligações privadas ou se pretende permitir uma gama IP específica na firewall do Amazon S3, tem de instalar o tempo de funcionamento da Integração auto-hospedada no Azure Windows VM. 

- A configuração de recomendação para começar para cada Azure VM é Standard_D32s_v3 com memória de 32 vCPU e 128-GB.  Pode continuar a monitorizar o CPU e a utilização da memória do VM durante a migração de dados para ver se precisa de aumentar ainda mais o VM para um melhor desempenho ou reduzir o VM para economizar custos. 
- Também pode escalar associando até 4 nós VM com um único IR auto-hospedado.  Um único trabalho de cópia que vai contra um IR auto-hospedado irá dividir automaticamente o conjunto de ficheiros e alavancar todos os nós VM para copiar os ficheiros em paralelo.  Para uma elevada disponibilidade, recomenda-se que comece com 2 nós VM para evitar um único ponto de falha durante a migração de dados. 

### <a name="rate-limiting"></a>Rate limiting (Limitação de taxa) 

Como uma boa prática, conduza um POC de desempenho com um conjunto de dados de amostra representativa, para que possa determinar um tamanho de partição apropriado. 

Comece com uma única partição e uma única atividade de cópia com a definição de DIU predefinido.  Aumente gradualmente a definição de DIU até atingir o limite de largura de banda da sua rede ou limite de largura de banda IOPS/banda dos armazéns de dados, ou atingiu o bricolage máximo permitido numa única atividade de cópia. 

Em seguida, aumente gradualmente o número de atividades de cópia simultânea até atingir os limites do seu ambiente. 

Quando encontrar erros de estrangulamento relatados pela atividade de cópiaS ADF, reduza a conucção ou a definição de DIU em ADF, ou considere aumentar os limites de largura de banda/IOPS da rede e das lojas de dados.  

### <a name="estimating-price"></a>Preço estimado 

> [!NOTE]
> Este é um exemplo hipotético de preços.  O seu preço real depende da produção real no seu ambiente.

Considere o seguinte oleoduto construído para a migração de dados de S3 para Azure Blob Storage: 

![O diagrama mostra um oleoduto para dados migratórios, com o gatilho manual a fluir para o Lookup, fluindo para o ForEach, fluindo para um sub-gasoduto para cada partição que contém cópia que flui para o Procedimento Armazenado. Fora do oleoduto, o Procedimento Armazenado flui para Azure SQL D B, que flui para o Lookup e um W S S3 flui para Copy, que flui para o armazenamento blob.](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

Assumamos o seguinte: 

- O volume total de dados é de 2 PB 
- Dados migratórios sobre HTTPS usando a arquitetura da primeira solução 
- 2 PB é dividido em divisórias de 1 K e cada cópia move uma partição 
- Cada cópia é configurada com DIU=256 e obtém 1 produção de GBps 
- ForEach concurrency está definido para 2 e a produção agregada é de 2 GBps 
- No total, leva 292 horas para completar a migração 

Aqui está o preço estimado com base nos pressupostos acima: 

![A imagem de uma tabela mostra um preço estimado.](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>Referências adicionais 
- [Conector de serviço de armazenamento simples Amazon](./connector-amazon-simple-storage-service.md)
- [Conector de armazenamento Azure Blob](./connector-azure-blob-storage.md)
- [Conector do Azure Data Lake Storage Gen2](./connector-azure-data-lake-storage.md)
- [Guia de afinação de desempenho da atividade de cópia](./copy-activity-performance.md)
- [Criação e configuração do tempo de execução da integração auto-hospedada](./create-self-hosted-integration-runtime.md)
- [Integração auto-hospedada runtime HA e escalabilidade](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)
- [Considerações de segurança sobre movimento de dados](./data-movement-security-considerations.md)
- [Armazenar credenciais no Azure Key Vault](./store-credentials-in-key-vault.md)
- [Copiar ficheiro incrementalmente baseado no nome do ficheiro dividido em tempo](./tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)
- [Copie ficheiros novos e alterados com base no LastModifiedDate](./tutorial-incremental-copy-lastmodified-copy-data-tool.md)
- [Página de preços da ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>Modelo

Aqui está o [modelo](solution-template-migration-s3-azure.md) para começar com a migração de petabytes de dados que consistem em centenas de milhões de ficheiros da Amazon S3 para a Azure Data Lake Storage Gen2.

## <a name="next-steps"></a>Passos seguintes

- [Copiar ficheiros de vários contentores com Azure Data Factory](solution-template-copy-files-multiple-containers.md)