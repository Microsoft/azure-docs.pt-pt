---
title: Visão geral do Azure Data Lake Storage Gen1 em HDInsight
description: Visão geral do Data Lake Storage Gen1 em HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 947dd125cf9c5f5874eed380b3d69cff11509e31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82187250"
---
# <a name="azure-data-lake-storage-gen1-overview-in-hdinsight"></a>Visão geral do Azure Data Lake Storage Gen1 em HDInsight

Azure Data Lake Storage Gen1 é um repositório de hiperescala em toda a empresa para grandes cargas analíticas de dados. Utilizando o Lago de Dados Azure, pode capturar dados de qualquer tamanho, tipo e velocidade de ingestão. E num lugar para análise operacional e exploratória.

Aceder ao Data Lake Storage Gen1 de Hadoop (disponível com um cluster HDInsight) utilizando as APIs de REST compatíveis com o WebHDFS. Data Lake Storage Gen1 é projetado para permitir a análise sobre os dados armazenados e é sintonizado para desempenho em cenários de análise de dados. A Gen1 inclui as capacidades que são essenciais para casos de uso de empresas no mundo real. Estas capacidades incluem segurança, gestão, adaptabilidade, fiabilidade e disponibilidade.

Para obter mais informações sobre a Azure Data Lake Storage Gen1, consulte a visão geral detalhada [da Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

As principais capacidades da Data Lake Storage Gen1 incluem as seguintes.

## <a name="compatibility-with-hadoop"></a>Compatibilidade com Hadoop

Data Lake Storage Gen1 é um sistema de ficheiros Apache Hadoop compatível com o ambiente HDFS e Hadoop.  As aplicações ou serviços HDInsight que utilizam a API WebHDFS podem facilmente integrar-se com a Data Lake Storage Gen1. Data Lake Storage Gen1 também expõe uma interface REST compatível com WebHDFS para aplicações.

Os dados armazenados na Data Lake Storage Gen1 podem ser facilmente analisados usando estruturas analíticas Hadoop. Quadros como MapReduce ou Hive. Os clusters Azure HDInsight podem ser a provisionados e configurados para aceder diretamente aos dados armazenados na Data Lake Storage Gen1.

## <a name="unlimited-storage-petabyte-files"></a>Armazenamento ilimitado, ficheiros petabyte

Data Lake Storage Gen1 fornece armazenamento ilimitado e é adequado para armazenar diferentes tipos de dados para análise. Não impõe limites aos tamanhos das contas, nem ao tamanho dos ficheiros. Ou a quantidade de dados que podem ser armazenados num lago de dados. Os ficheiros individuais variam em tamanho, desde quilobytes a petabytes, tornando a Data Lake Storage Gen1 uma ótima escolha para armazenar qualquer tipo de dados. Os dados são armazenados duravelmente fazendo várias cópias. E não há limites para quanto tempo os dados podem ser armazenados no lago de dados.

## <a name="performance-tuning-for-big-data-analytics"></a>Afinação de desempenho para análise de big data

Data Lake Storage Gen1 é projetado para sistemas analíticos. Sistemas que requerem uma produção massiva para consultar e analisar grandes quantidades de dados. O lago de dados espalha partes de um ficheiro sobre vários servidores de armazenamento individuais. Quando está a analisar dados, esta configuração melhora a produção de leitura quando o ficheiro é lido em paralelo.

## <a name="readiness-for-enterprise-highly-available-and-secure"></a>Prontidão para empresa: Altamente disponível e seguro

Data Lake Storage Gen1 fornece disponibilidade e fiabilidade padrão da indústria. Os ativos de dados são armazenados de forma duradoura: cópias redundantes protegem contra falhas inesperadas. As empresas podem utilizar a Data Lake Storage Gen1 nas suas soluções como parte importante da sua plataforma de dados existente.

Data Lake Storage Gen1 também fornece segurança de nível empresarial para dados armazenados. Para obter mais informações, consulte [a Garantia de dados na Azure Data Lake Storage Gen1](#data-security-in-data-lake-storage-gen1).

## <a name="flexible-data-structures"></a>Estruturas de dados flexíveis

Data Lake Storage Gen1 pode armazenar quaisquer dados no seu formato nativo, como é, sem exigir transformações prévias. Data Lake Storage Gen1 não requer que um esquema seja definido antes de os dados serem carregados. O quadro analítico individual interpreta os dados e define um esquema no momento da análise. Data Lake Storage Gen1 pode lidar com dados estruturados. E dados semi-estruturados e desestruturados.

Os contentores gen1 de armazenamento de dados para dados são essencialmente pastas e ficheiros. Opera nos dados armazenados utilizando SDKs, o portal Azure e a Azure PowerShell. Os dados colocados na loja com estas interfaces e contentores, podem armazenar qualquer tipo de dado. Data Lake Storage Gen1 não faz qualquer tratamento especial de dados com base no tipo de dados.

## <a name="data-security-in-data-lake-storage-gen1"></a>Segurança de dados em Data Lake Storage Gen1

Data Lake Storage Gen1 utiliza O Diretório Ativo Azure para autenticação e utiliza listas de controlo de acesso (ACLs) para gerir o acesso aos seus dados.

| **Funcionalidade** | **Descrição** |
| --- | --- |
| Autenticação |Data Lake Storage Gen1 integra-se com Azure Ative Directory (Azure AD) para gestão de identidade e acesso para todos os dados armazenados na Data Lake Storage Gen1. Devido à integração, a Data Lake Storage Gen1 beneficia de todas as funcionalidades Azure AD. Estas funcionalidades incluem: autenticação multifactor, Acesso Condicional e controlo de acesso baseado em funções. Além disso, monitorização de utilização de aplicações, monitorização de segurança e alerta, e assim por diante. Data Lake Storage Gen1 suporta o protocolo OAuth 2.0 para autenticação dentro da interface REST. Ver [autenticação dentro da Azure Data Lake Storage Gen1 usando o Diretório Ativo Azure](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Controlo de acesso |A Data Lake Storage Gen1 fornece controlo de acesso suportando permissões ao estilo POSIX que são expostas pelo protocolo WebHDFS. As ACLs podem ser ativadas na pasta raiz, nas subpastas e nos ficheiros individuais. Para obter mais informações sobre como os ACLs funcionam no contexto da Data Lake Storage Gen1, consulte o controlo de [acesso na Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Encriptação |Data Lake Storage Gen1 também fornece encriptação para dados que são armazenados na conta. Especifica as definições de encriptação ao criar uma conta Gen1 de armazenamento de data lake. Pode optar por ter os seus dados encriptados ou optar por nenhuma encriptação. Para obter mais informações, consulte [a encriptação na Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). Para obter instruções sobre como fornecer uma configuração relacionada com encriptação, consulte [Começar com a Azure Data Lake Storage Gen1 utilizando o portal Azure](../data-lake-store/data-lake-store-get-started-portal.md). |

Para saber mais sobre a obtenção de dados na Data Lake Storage Gen1, consulte [os dados de segurança armazenados na Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Aplicações compatíveis com data lake storage gen1

Data Lake Storage Gen1 é compatível com a maioria dos componentes de código aberto no ambiente Hadoop. Também é integrado corretamente com outros serviços do Azure.  Siga os links abaixo para saber mais sobre como a Data Lake Storage Gen1 pode ser usada tanto com componentes de código aberto como outros serviços Azure.

* Consulte [aplicações de big data de código aberto que funcionam com a Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md).
* Consulte [a Integração do Azure Data Lake Storage Gen1 com outros serviços Azure](../data-lake-store/data-lake-store-integrate-with-other-services.md) para entender como usar a Data Lake Storage Gen1 com outros serviços Azure para permitir uma maior variedade de cenários.
* Consulte [a Utilização de Azure Data Lake Storage Gen1 para obter grandes requisitos de dados](../data-lake-store/data-lake-store-data-scenarios.md).

## <a name="data-lake-storage-gen1-file-system-adl"></a>Sistema de ficheiros Gen1 de armazenamento de dados (adl://)

Nos ambientes Hadoop, pode aceder à Data Lake Storage Gen1 através do novo sistema de ficheiros, o AzureDataLakeFilesystem (adl://). O desempenho de aplicações e serviços que usam `adl://` pode ser otimizado de formas que não estão atualmente disponíveis no WebHDFS. Como resultado, obtém-se a flexibilidade para obter o melhor desempenho utilizando o adl:// recomendado. Ou manter o código existente continuando a utilizar a API WebHDFS diretamente. A Azure HDInsight aproveita ao máximo o Sistema AzureDataLakeFile para proporcionar o melhor desempenho na Data Lake Storage Gen1.

Aceda aos seus dados na Data Lake Storage Gen1 utilizando o seguinte URI:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Para obter mais informações sobre como aceder aos dados na Data Lake Storage Gen1, consulte [as Ações disponíveis nos dados armazenados.](../data-lake-store/data-lake-store-get-started-portal.md#properties)

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md)
* [Descrição geral do Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)