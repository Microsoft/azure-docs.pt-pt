---
title: O que é o Azure Data Lake Storage Gen1? | Microsoft Docs
description: Visão geral da Data Lake Storage Gen1 (anteriormente conhecida como Azure Data Lake Store), e o valor que fornece sobre outras lojas de dados
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: overview
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 52c00359fb4e759d9517dee958e31c00feb2b33e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92149238"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>O que é o Azure Data Lake Storage Gen1?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 é um repositório de hiperescimento em toda a empresa para grandes cargas analíticas de dados. O Azure Data Lake permite-lhe capturar dados de qualquer tamanho, tipo e velocidade de ingestão num único local para análises exploratórias e operacionais.

Data Lake Storage Gen1 pode ser acedido a partir de Hadoop (disponível com cluster HDInsight) usando as APIs DE REST compatíveis com o WebHDFS. Foi projetado para permitir a análise dos dados armazenados e é sintonizado para o desempenho para cenários de análise de dados. Data Lake Storage Gen1 inclui todas as capacidades de nível da empresa: segurança, gestão, escalabilidade, fiabilidade e disponibilidade.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>Principais capacidades

Algumas das principais capacidades da Data Lake Storage Gen1 incluem as seguintes.

### <a name="built-for-hadoop"></a>Incorporado para o Hadoop

Data Lake Storage Gen1 é um sistema de ficheiros Apache Hadoop que é compatível com o Hadoop Distributed File System (HDFS), e trabalha com o ecossistema Hadoop. As suas aplicações ou serviços HDInsight existentes que utilizam a API WebHDFS podem facilmente integrar-se com a Data Lake Storage Gen1. Data Lake Storage Gen1 também expõe uma interface REST compatível com WebHDFS para aplicações.

Você pode facilmente analisar dados armazenados em Data Lake Storage Gen1 usando estruturas analíticas Hadoop, tais como MapReduce ou Hive. Você pode providenciar clusters Azure HDInsight e configurá-los para aceder diretamente aos dados armazenados na Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Armazenamento ilimitado, ficheiros petabyte

Data Lake Storage Gen1 fornece armazenamento ilimitado e pode armazenar uma variedade de dados para análise. Não impõe limites aos tamanhos das contas, tamanhos de ficheiros ou à quantidade de dados que podem ser armazenados num lago de dados. Os ficheiros individuais podem variar de kilobyte a petabytes de tamanho. Os dados são armazenados duravelmente fazendo várias cópias. Não existe limite para a duração do tempo para o qual os dados podem ser armazenados no lago de dados.

### <a name="performance-tuned-for-big-data-analytics"></a>Desempenho otimizado para análise de macrodados

Data Lake Storage Gen1 é construído para executar sistemas analíticos de grande escala que requerem uma produção massiva para consultar e analisar grandes quantidades de dados. O data lake propaga partes de um ficheiro ao longo de um número de servidores de armazenamento individuais. Isto melhora o débito de leitura ao ler o ficheiro em paralelo para efetuar análise de dados.

### <a name="enterprise-ready-highly-available-and-secure"></a>Empresa pronta: Altamente disponível e segura

Data Lake Storage Gen1 fornece disponibilidade e fiabilidade padrão da indústria. Os recursos de dados são armazenados de maneira duradoura, ao fazer cópias redundantes para proteger contra quaisquer falhas inesperadas.

Data Lake Storage Gen1 também fornece segurança de nível empresarial para os dados armazenados. Para obter mais informações, consulte [a Garantia de dados na Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Todos os dados

Data Lake Storage Gen1 pode armazenar quaisquer dados no seu formato nativo, sem exigir quaisquer transformações anteriores. A Data Lake Storage Gen1 não requer que seja definido um esquema antes de os dados serem carregados, deixando-o à altura da estrutura analítica individual para interpretar os dados e definir um esquema no momento da análise. A capacidade de armazenar ficheiros de tamanhos e formatos arbitrários permite que a Data Lake Storage Gen1 manuseie dados estruturados, semi-estruturados e não estruturados.

Os contentores gen1 de armazenamento de dados para dados são essencialmente pastas e ficheiros. Opera nos dados armazenados utilizando SDKs, o portal Azure e Azure Powershell. Se colocar os seus dados na loja utilizando estas interfaces e utilizando os recipientes apropriados, pode armazenar qualquer tipo de dados. A Data Lake Storage Gen1 não realiza qualquer tratamento especial de dados com base no tipo de dados que armazena.

## <a name="securing-data"></a><a name="DataLakeStoreSecurity"></a>Proteção de dados

Data Lake Storage Gen1 utiliza Azure Ative Directory (Azure AD) para autenticação e listas de controlo de acesso (ACLs) para gerir o acesso aos seus dados.

| Funcionalidade | Descrição |
| --- | --- |
| Autenticação |Data Lake Storage Gen1 integra-se com Azure AD para gestão de identidade e acesso para todos os dados armazenados na Data Lake Storage Gen1. Devido à integração, a Data Lake Storage Gen1 beneficia de todas as funcionalidades AZure AD, tais como autenticação de vários fatores, Acesso Condicional, controlo de acesso baseado em funções Azure, monitorização de utilização de aplicações, monitorização de segurança e alerta, e assim por diante. Data Lake Storage Gen1 suporta o protocolo OAuth 2.0 para autenticação dentro da interface REST. Consulte a [autenticação gen1 de armazenamento de dados.](data-lakes-store-authentication-using-azure-active-directory.md)|
| Controlo de acesso |Data Lake Storage Gen1 fornece controlo de acesso suportando permissões de estilo POSIX expostas pelo protocolo WebHDFS. Pode ativar ACLs na pasta raiz, nas sub-dobradizas e nos ficheiros individuais. Para obter mais informações sobre como os ACLs funcionam no contexto da Data Lake Storage Gen1, consulte o controlo de [acesso na Data Lake Storage Gen1](data-lake-store-access-control.md). |
| Encriptação |Data Lake Storage Gen1 também fornece encriptação para dados que são armazenados na conta. Especifica as definições de encriptação ao criar uma conta Gen1 de armazenamento de data lake. Pode optar por ter os seus dados encriptados ou optar por nenhuma encriptação. Para obter mais informações, consulte [a encriptação na Data Lake Storage Gen1](data-lake-store-encryption.md). Para obter instruções sobre como fornecer configuração relacionada com encriptação, consulte [Começar com a Data Lake Storage Gen1 utilizando o portal Azure](data-lake-store-get-started-portal.md). |

Para obter instruções sobre como proteger os dados na Data Lake Storage Gen1, consulte [os dados de segurança na Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="application-compatibility"></a>Compatibilidade de aplicações

Data Lake Storage Gen1 é compatível com a maioria dos componentes de código aberto no ecossistema Hadoop. Integra também bem com outros serviços Azure. Para saber mais sobre como pode utilizar a Data Lake Storage Gen1 com componentes de código aberto e outros serviços Azure, utilize os seguintes links:

- Consulte [aplicações e serviços compatíveis com a Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md) para obter uma lista de aplicações de código aberto interoperáveis com a Data Lake Storage Gen1.
- Consulte [a Integração com outros serviços Azure](data-lake-store-integrate-with-other-services.md) para entender como usar a Data Lake Storage Gen1 com outros serviços Azure para permitir uma ampla gama de cenários.
- Consulte [cenários para utilizar a Data Lake Storage Gen1](data-lake-store-data-scenarios.md) para aprender a usar a Data Lake Storage Gen1 em cenários como ingerir dados, processar dados, descarregar dados e visualizar dados.

## <a name="data-lake-storage-gen1-file-system"></a>Sistema de ficheiros Gen1 de armazenamento de dados lake

Data Lake Storage Gen1 pode ser acedido através do sistema de ficheiros AzureDataLakeFilesystem (adl://) em ambientes Hadoop (disponível com cluster HDInsight). Aplicações e serviços que usam adl:// podem tirar partido de novas otimizações de desempenho que não estão atualmente disponíveis no WebHDFS. Como resultado, a Data Lake Storage Gen1 dá-lhe a flexibilidade para fazer o melhor desempenho com a opção recomendada de usar adl:// ou manter o código existente, continuando a utilizar a API WebHDFS diretamente. O Azure HDInsight aproveita totalmente o Sistema AzureDataLakeFile para proporcionar o melhor desempenho na Data Lake Storage Gen1.

Pode aceder aos seus dados na Data Lake Storage Gen1 utilizando `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net` . Para obter mais informações sobre como aceder aos dados na Data Lake Storage Gen1, consulte [as propriedades dos dados armazenados.](data-lake-store-get-started-portal.md#properties)

## <a name="next-steps"></a>Passos seguintes

- [Começar com data lake storage gen1 usando o portal Azure](data-lake-store-get-started-portal.md)
- [Começar com data lake storage gen1 usando .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Use Azure HDInsight com data lake storage gen1](data-lake-store-hdinsight-hadoop-use-portal.md)