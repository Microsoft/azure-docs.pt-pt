---
title: Visão geral do Armazenamento do Lago Azure em HDInsight
description: Visão geral do Data Lake Storage Gen1 em HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 947dd125cf9c5f5874eed380b3d69cff11509e31
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187250"
---
# <a name="azure-data-lake-storage-gen1-overview-in-hdinsight"></a>Visão geral do Armazenamento do Lago Azure em HDInsight

Azure Data Lake Storage Gen1 é um repositório de hiperescala em toda a empresa para grandes cargas de trabalho analíticas de dados. Utilizando o Lago de Dados Azure, pode capturar dados de qualquer tamanho, tipo e velocidade de ingestão. E num lugar para análiseoperacional e exploratória.

Aceda ao Data Lake Storage Gen1 a partir de Hadoop (disponível com um cluster HDInsight) utilizando as APIs REST compatíveis com webHDFS. Data Lake Storage Gen1 foi concebido para permitir análises nos dados armazenados e está sintonizado para o desempenho em cenários de análise de dados. A Gen1 inclui as capacidades que são essenciais para casos de uso de empresas no mundo real. Estas capacidades incluem segurança, gestão, adaptabilidade, fiabilidade e disponibilidade.

Para obter mais informações sobre o Azure Data Lake Storage Gen1, consulte a visão detalhada do Armazenamento do [Lago De dados Azure Gen1](../data-lake-store/data-lake-store-overview.md).

As principais capacidades do Data Lake Storage Gen1 incluem as seguintes.

## <a name="compatibility-with-hadoop"></a>Compatibilidade com Hadoop

Data Lake Storage Gen1 é um sistema de ficheiros Apache Hadoop compatível com o ambiente HDFS e Hadoop.  As aplicações ou serviços HDInsight que utilizam a API WebHDFS podem facilmente integrar-se com data Lake Storage Gen1. Data Lake Storage Gen1 também expõe uma interface REST compatível com WebHDFS para aplicações.

Os dados armazenados em Data Lake Storage Gen1 podem ser facilmente analisados usando quadros analíticos Hadoop. Quadros como MapReduce ou Hive. Os clusters Azure HDInsight podem ser provisionados e configurados para aceder diretamente aos dados armazenados em Data Lake Storage Gen1.

## <a name="unlimited-storage-petabyte-files"></a>Armazenamento ilimitado, ficheiros petabyte

Data Lake Storage Gen1 fornece armazenamento ilimitado e é adequado para armazenar diferentes tipos de dados para análise. Não impõe limites ao tamanho das contas, nem tamanhos de ficheiros. Ou a quantidade de dados que podem ser armazenados num lago de dados. Os ficheiros individuais variam em tamanho, desde quilobytes a petabytes, tornando o Data Lake Storage Gen1 uma ótima escolha para armazenar qualquer tipo de dados. Os dados são armazenados de forma duradoura fazendo várias cópias. E não há limites para quanto tempo os dados podem ser armazenados no lago de dados.

## <a name="performance-tuning-for-big-data-analytics"></a>Sintonização de desempenho para análise de big data

Data Lake Storage Gen1 é projetado para sistemas analíticos. Sistemas que requerem uma entrada massiva para consultar e analisar grandes quantidades de dados. O lago de dados espalha partes de um ficheiro sobre vários servidores de armazenamento individuais. Ao analisar dados, esta configuração melhora a leitura da entrada quando o ficheiro é lido em paralelo.

## <a name="readiness-for-enterprise-highly-available-and-secure"></a>Prontidão para empresa: Altamente disponível e seguro

Data Lake Storage Gen1 fornece disponibilidade e fiabilidade padrão da indústria. Os ativos de dados são armazenados de forma duradoura: cópias redundantes protegem contra falhas inesperadas. As empresas podem utilizar o Data Lake Storage Gen1 nas suas soluções como parte importante da sua plataforma de dados existente.

Data Lake Storage Gen1 também fornece segurança de nível empresarial para dados armazenados. Para mais informações, consulte A Segurança dos Dados no Armazenamento de [Lagos Azure Gen1](#data-security-in-data-lake-storage-gen1).

## <a name="flexible-data-structures"></a>Estruturas de dados flexíveis

Data Lake Storage Gen1 pode armazenar quaisquer dados no seu formato nativo, como é, sem exigir transformações prévias. Data Lake Storage Gen1 não requer que um esquema seja definido antes de os dados serem carregados. O quadro analítico individual interpreta os dados e define um esquema no momento da análise. Data Lake Storage Gen1 pode lidar com dados estruturados. E dados semiestruturados e não estruturados.

Recipientes Gen1 de armazenamento de data Lake para dados são essencialmente pastas e ficheiros. Opera os dados armazenados utilizando SDKs, o portal Azure e o Azure PowerShell. Os dados colocados na loja com estas interfaces e contentores podem armazenar qualquer tipo de dados. Data Lake Storage Gen1 não faz nenhum tratamento especial de dados com base no tipo de dados.

## <a name="data-security-in-data-lake-storage-gen1"></a>Segurança de dados em Data Lake Storage Gen1

Data Lake Storage Gen1 utiliza o Diretório Ativo Azure para autenticação e utiliza listas de controlo de acesso (ACLs) para gerir o acesso aos seus dados.

| **Funcionalidade** | **Descrição** |
| --- | --- |
| Autenticação |Data Lake Storage Gen1 integra-se com o Azure Ative Directory (Azure AD) para gestão de identidade e acesso para todos os dados armazenados em Data Lake Storage Gen1. Devido à integração, data Lake Storage Gen1 beneficia de todas as funcionalidades da Azure AD. Estas funcionalidades incluem: autenticação multifactor, Acesso Condicional e controlo de acesso baseado em papéis. Além disso, monitorização de utilização de aplicações, monitorização de segurança e alerta, e assim por diante. Data Lake Storage Gen1 suporta o protocolo OAuth 2.0 para autenticação dentro da interface REST. Ver Autenticação dentro do [Azure Data Lake Storage Gen1 usando o Diretório Ativo Azure](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Controlo de acesso |Data Lake Storage Gen1 fornece controlo de acesso suportando permissões ao estilo POSIX que são expostas pelo protocolo WebHDFS. As ACLs podem ser ativadas na pasta raiz, nas subpastas e nos ficheiros individuais. Para obter mais informações sobre como os ACLs funcionam no contexto do Data Lake Storage Gen1, consulte o controlo de [acesso em Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Encriptação |Data Lake Storage Gen1 também fornece encriptação para dados que são armazenados na conta. Especifica as definições de encriptação ao criar uma conta Gen1 de Armazenamento de Data Lake. Pode optar por ter os seus dados encriptados ou optar por nenhuma encriptação. Para mais informações, consulte [encriptação em Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). Para obter instruções sobre como fornecer uma configuração relacionada com encriptação, consulte Iniciar com [o Azure Data Lake Storage Gen1 utilizando o portal Azure](../data-lake-store/data-lake-store-get-started-portal.md). |

Para saber mais sobre a obtenção de dados no Data Lake Storage Gen1, consulte [a Segurança dos Dados Armazenados em Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Aplicações compatíveis com Data Lake Storage Gen1

Data Lake Storage Gen1 é compatível com a maioria dos componentes de código aberto no ambiente Hadoop. Também é integrado corretamente com outros serviços do Azure.  Siga os links abaixo para saber mais sobre como o Data Lake Storage Gen1 pode ser usado tanto com componentes de código aberto como outros serviços Azure.

* Consulte [aplicações de big data de código aberto que funcionam com o Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md).
* Consulte a Integração de Dados [Azure Storage Gen1 com outros serviços Azure](../data-lake-store/data-lake-store-integrate-with-other-services.md) para entender como usar data Lake Storage Gen1 com outros serviços Azure para permitir uma ampla gama de cenários.
* Consulte [a utilização do Azure Data Lake Storage Gen1 para obter grandes requisitos](../data-lake-store/data-lake-store-data-scenarios.md)de dados .

## <a name="data-lake-storage-gen1-file-system-adl"></a>Sistema de ficheiros Gen1 de Armazenamento de Data Lake (adl://)

Em ambientes Hadoop, pode aceder ao Data Lake Storage Gen1 através do novo sistema de ficheiros, o Sistema AzureDataLakeFile (adl://). O desempenho de aplicações `adl://` e serviços que utilizam pode ser otimizado de formas que não estão atualmente disponíveis no WebHDFS. Como resultado, obtém-se a flexibilidade para utilizar o melhor desempenho utilizando o adl:// recomendado. Ou manter o código existente continuando a utilizar diretamente a API WebHDFS. O Azure HDInsight aproveita ao máximo o sistema AzureDataLakeFile para proporcionar o melhor desempenho no Data Lake Storage Gen1.

Aceda aos seus dados no Data Lake Storage Gen1 utilizando o seguinte URI:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Para obter mais informações sobre como aceder aos dados em Data Lake Storage Gen1, consulte [Ações disponíveis nos dados armazenados](../data-lake-store/data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Armazenamento de Lagos De Dados Azure Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md)
* [Descrição geral do Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)