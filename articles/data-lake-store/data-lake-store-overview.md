---
title: O que é a geração 1 de armazenamento do Azure Data Lake? | Microsoft Docs
description: Descrição geral do Data Lake Storage Gen1 (anteriormente conhecido como o Azure Data Lake Store) e sua importância em relação a outros arquivos de dados
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 518c129aedf3161ab761d09139e0c4d988dd2cbc
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681833"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>O que é a geração 1 de armazenamento do Azure Data Lake?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Geração de armazenamento 1 do Azure Data Lake é um repositório de hiperescala de toda a empresa para cargas de trabalho de análise de macrodados. O Azure Data Lake permite-lhe capturar dados de qualquer tamanho, tipo e velocidade de ingestão num único local para análises exploratórias e operacionais.

Geração 1 de armazenamento do Data Lake pode ser acedido a partir do Hadoop (disponível com o cluster do HDInsight) com as APIs de REST compatíveis com WebHDFS. Ele foi concebido para ativar a análise nos dados armazenados e está otimizado para desempenho para cenários de análises de dados. Geração 1 de armazenamento do Data Lake inclui todas as capacidades de nível empresarial: segurança, capacidade de gerenciamento, escalabilidade, fiabilidade e disponibilidade.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>Principais capacidades

Seguem-se alguns dos principais recursos de geração 1 de armazenamento do Data Lake.

### <a name="built-for-hadoop"></a>Incorporado para o Hadoop

Geração 1 de armazenamento do Data Lake é um sistema de ficheiros Apache Hadoop que é compatível com o Hadoop Distributed File System (HDFS) e funciona com o ecossistema Hadoop. O HDInsight aplicações ou serviços que utilizam a API de WebHDFS podem integrar facilmente com geração 1 de armazenamento do Data Lake. Geração 1 de armazenamento do Data Lake também expõe uma interface REST compatível com WebHDFS para aplicações.

Pode analisar facilmente dados armazenados no Data Lake Storage Gen1 usando estruturas de análise de Hadoop, tais como MapReduce ou Hive. Pode aprovisionar clusters do HDInsight do Azure e configurá-las para acessar diretamente os dados armazenados no Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Armazenamento ilimitado, ficheiros petabyte

Geração 1 de armazenamento do Data Lake fornece armazenamento ilimitado e pode armazenar uma variedade de dados para análise. Ele não impõe quaisquer limites de tamanhos de conta, tamanhos de ficheiro ou a quantidade de dados que podem ser armazenados num data lake. Ficheiros individuais podem variar entre kilobytes e petabytes de tamanho. Dados são armazenados de maneira duradoura, fazendo várias cópias. Não existe nenhum limite na duração de tempo para o qual os dados podem ser armazenados no data lake.

### <a name="performance-tuned-for-big-data-analytics"></a>Desempenho otimizado para análise de macrodados

Geração 1 de armazenamento do Data Lake foi concebido para executar sistemas de análise em grande escala que requeiram débito em massa para consultar e analisar grandes quantidades de dados. O data lake propaga partes de um ficheiro ao longo de um número de servidores de armazenamento individuais. Isto melhora o débito de leitura ao ler o ficheiro em paralelo para efetuar análise de dados.

### <a name="enterprise-ready-highly-available-and-secure"></a>Pronto para as empresas: Elevada disponibilidade e segura

Geração 1 de armazenamento do Data Lake fornece disponibilidade de norma da indústria e fiabilidade. Os recursos de dados são armazenados de maneira duradoura, ao fazer cópias redundantes para proteger contra quaisquer falhas inesperadas.

Geração 1 de armazenamento do Data Lake também fornece segurança de nível empresarial para os dados armazenados. Para obter mais informações, consulte [proteger dados no Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Todos os dados

Geração 1 de armazenamento do Data Lake pode armazenar quaisquer dados em seu formato nativo, sem a necessidade de qualquer transformações anteriores. Geração 1 de armazenamento do Data Lake não requer um esquema antes dos dados são carregados, deixá-lo para a estrutura de análise individual interpreta os dados e definir um esquema no momento da análise. A capacidade de armazenar os arquivos de formatos e tamanhos arbitrários torna possível para a geração 1 de armazenamento do Data Lake processar dados estruturados, semiestruturados e não estruturados.

Contentores de Lake Gen1 de armazenamento de dados para os dados são essencialmente pastas e ficheiros. Utilizar os dados armazenados através de SDKs, o portal do Azure e o Azure Powershell. Se colocar seus dados para o arquivo utilizando estas interfaces e utilize os contentores adequados, pode armazenar qualquer tipo de dados. Geração 1 de armazenamento do Data Lake não efetua qualquer processamento especial de dados com base no tipo de dados que armazena.

## <a name="DataLakeStoreSecurity"></a>Proteção de dados

Utiliza o Data Lake Gen1 de armazenamento do Azure Active Directory (Azure AD) para autenticação e acesso controla listas (ACLs) para gerir o acesso aos seus dados.

| Funcionalidade | Descrição |
| --- | --- |
| Authentication |Gen1 de armazenamento do Data Lake está integrado no Azure AD para a gestão de identidades e acessos para todos os dados armazenados no Data Lake Storage Gen1. Devido à integração, benefícios de geração 1 de armazenamento do Data Lake do todos os Azure AD, como a autenticação multifator, acesso condicional, controlo de acesso baseado em funções, monitorização da utilização de aplicações, monitorização de segurança e alertas de recursos e assim por diante. Geração 1 de armazenamento do Data Lake suporta o protocolo OAuth 2.0 para a autenticação dentro da interface REST. Ver [autenticação de geração 1 de armazenamento do Data Lake](data-lakes-store-authentication-using-azure-active-directory.md).|
| Controlo de acesso |Geração 1 de armazenamento do Data Lake fornece controlo de acesso ao suportar permissões do estilo POSIX expostas pelo protocolo WebHDFS. Pode ativar as ACLs na pasta raiz, em subpastas e em ficheiros individuais. Para obter mais informações sobre como as ACLs funcionam no contexto de geração 1 de armazenamento do Data Lake, veja [controlo de acesso no Data Lake Storage Gen1](data-lake-store-access-control.md). |
| Encriptação |Geração 1 de armazenamento do Data Lake também fornece encriptação para dados armazenados na conta. Especifique as definições de encriptação durante a criação de uma conta de geração 1 de armazenamento do Data Lake. Pode optar por encriptar os seus dados ou por não utilizar encriptação. Para obter mais informações, consulte [encriptação no Data Lake Storage Gen1](data-lake-store-encryption.md). Para obter instruções sobre como fornecer configuração relacionada com encriptação, consulte [introdução à geração 1 de armazenamento do Data Lake com o portal do Azure](data-lake-store-get-started-portal.md). |

Para obter instruções sobre como proteger os dados na geração 1 de armazenamento do Data Lake, veja [proteger dados no Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="application-compatibility"></a>Compatibilidade de aplicativos

Geração 1 de armazenamento do Data Lake é compatível com a maioria dos componentes de código-fonte aberto no ecossistema do Hadoop. Ele também se integra bem com outros serviços do Azure. Para saber mais sobre como pode utilizar o Data Lake Storage Gen1 com componentes de código aberto e outros serviços do Azure, utilize as seguintes ligações:

- Ver [aplicativos e serviços compatíveis com o Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md) para obter uma lista de aplicações de código aberto interoperáveis com geração 1 de armazenamento do Data Lake.
- Ver [integração com outros serviços do Azure](data-lake-store-integrate-with-other-services.md) para compreender como utilizar Gen1 de armazenamento do Data Lake com outros serviços do Azure para ativar uma vasta gama de cenários.
- Ver [cenários de utilização de geração 1 de armazenamento do Data Lake](data-lake-store-data-scenarios.md) para aprender a utilizar o Data Lake Storage Gen1 em cenários como a ingestão de dados, processamento de dados, transferência de dados e visualização de dados.

## <a name="data-lake-storage-gen1-file-system"></a>Sistema de ficheiros do Data Lake Storage Gen1

Geração 1 de armazenamento do Data Lake pode ser acedido através do sistema de ficheiros AzureDataLakeFilesystem (adl: / /) em ambientes do Hadoop (disponíveis com o cluster do HDInsight). Aplicações e serviços que utilizam adl: / / pode tirar partido de mais otimizações de desempenho que não estão atualmente disponíveis no WebHDFS. Como resultado, Gen1 de armazenamento do Data Lake dá a a flexibilidade para o fazer é utilizar do melhor desempenho com a opção recomendada de utilização adl: / / ou manter o código existente ao continuar a utilizar a API WebHDFS diretamente. O Azure HDInsight tira total partido do AzureDataLakeFilesystem para proporcionar o melhor desempenho na geração 1 de armazenamento do Data Lake.

Pode aceder aos seus dados através da geração 1 de armazenamento do Data Lake `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. Para obter mais informações sobre como acessar os dados na geração 1 de armazenamento do Data Lake, veja [ver as propriedades dos dados armazenados](data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Passos Seguintes

- [Introdução à geração 1 de armazenamento do Data Lake com o portal do Azure](data-lake-store-get-started-portal.md)
- [Introdução à geração 1 de armazenamento do Data Lake com o .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Utilizar o Azure HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)