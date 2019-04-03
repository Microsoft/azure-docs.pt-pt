---
title: Descrição geral do Azure Data Lake Storage Gen1 | Documentos da Microsoft
description: Compreender o que Gen1 de armazenamento do Data Lake é (anteriormente conhecido como Azure Data Lake Store) e sua importância em relação a outros arquivos de dados
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 438eab091fac103b66f0789beca0098b87ee44cd
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58885660"
---
# <a name="overview-of-azure-data-lake-storage-gen1"></a>Descrição geral do Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Geração de armazenamento 1 do Azure Data Lake é um repositório de hiperescala de toda a empresa para cargas de trabalho de análise de macrodados. O Azure Data Lake permite-lhe capturar dados de qualquer tamanho, tipo e velocidade de ingestão num único local para análises exploratórias e operacionais.

> [!TIP]
> Utilize o [percurso de aprendizagem do Data Lake Storage Gen1](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/) para começar a explorar o serviço de geração 1 de armazenamento do Data Lake.
> 
> 

Geração 1 de armazenamento do Data Lake pode ser acedido a partir do Hadoop (disponível com o cluster do HDInsight) com as APIs de REST compatíveis com WebHDFS. Foi concebido especificamente para ativar a análise nos dados armazenados e está otimizado para desempenho para cenários de análise de dados. Fácil de utilizar, inclui todas as funcionalidades de nível empresarial (segurança, capacidade de gestão, escalabilidade, fiabilidade e disponibilidade), essenciais para casos de utilização empresarial reais.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

Seguem-se alguns dos principais recursos de geração 1 de armazenamento do Data Lake.

### <a name="built-for-hadoop"></a>Incorporado para o Hadoop
Geração 1 de armazenamento do Data Lake é um sistema de ficheiros Apache Hadoop compatível com o Hadoop Distributed File System (HDFS) e funciona com o ecossistema Hadoop.  O HDInsight aplicações ou serviços que utilizam a API de WebHDFS podem integrar facilmente com geração 1 de armazenamento do Data Lake. Geração 1 de armazenamento do Data Lake também expõe uma interface REST compatível com WebHDFS para aplicações

Dados armazenados no Data Lake Storage Gen1 podem ser facilmente analisados utilizando as estruturas de análise de Hadoop, tais como MapReduce ou Hive. Clusters do Microsoft Azure HDInsight podem ser aprovisionados e configurados para aceder diretamente aos dados armazenados no Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Armazenamento ilimitado, ficheiros petabyte
Geração 1 de armazenamento do Data Lake fornece armazenamento ilimitado e é adequado para armazenar diversos dados para análise. Não tem quaisquer limites de tamanhos de conta, de ficheiros ou de volume de dados que podem ser armazenados num data lake. Os ficheiros individuais podem variar entre kilobytes e petabytes em termos de tamanho, sendo uma escolha ideal para armazenar qualquer tipo de dados. Os dados são armazenados de maneira duradoura, ao criar várias cópias e não existe limite de tempo para o armazenamento dos dados no data lake.

### <a name="performance-tuned-for-big-data-analytics"></a>Desempenho otimizado para análise de macrodados
Geração 1 de armazenamento do Data Lake foi concebido para executar sistemas de análise que requeiram débito em massa para consultar e analisar grandes quantidades de dados de grande escala. O data lake propaga partes de um ficheiro ao longo de um número de servidores de armazenamento individuais. Isto melhora o débito de leitura ao ler o ficheiro em paralelo para efetuar análise de dados.

### <a name="enterprise-ready-highly-available-and-secure"></a>Pronto para empresas: Elevada disponibilidade e seguro
Geração 1 de armazenamento do Data Lake fornece disponibilidade de norma da indústria e fiabilidade. Os recursos de dados são armazenados de maneira duradoura, ao fazer cópias redundantes para proteger contra quaisquer falhas inesperadas. As empresas podem utilizar a geração 1 de armazenamento do Data Lake nas respetivas soluções como uma parte importante da respetiva plataforma de dados.

Geração 1 de armazenamento do Data Lake também fornece segurança de nível empresarial para os dados armazenados. Para obter mais informações, consulte [proteger dados no Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Todos os Dados
Geração 1 de armazenamento do Data Lake pode armazenar quaisquer dados no seu formato nativo, tal como está, sem a necessidade de qualquer transformações anteriores. Geração 1 de armazenamento do Data Lake não requer um esquema antes dos dados são carregados, deixá-lo para a estrutura de análise individual interpreta os dados e definir um esquema no momento da análise. A capacidade para armazenar os arquivos de formatos e tamanhos arbitrários torna possível para a geração 1 de armazenamento do Data Lake processar dados estruturados, semiestruturados e não estruturados.

Contentores de Lake Gen1 de armazenamento de dados para os dados são essencialmente pastas e ficheiros. Utiliza os dados armazenados através do SDK, do Portal do Azure e do Azure Powershell. Desde que coloque os dados num arquivo utilizando estas interfaces e utilize os contentores adequados, pode armazenar qualquer tipo de dados. Geração 1 de armazenamento do Data Lake não efetua qualquer processamento especial de dados com base no tipo de dados que armazena.

## <a name="DataLakeStoreSecurity"></a>Proteger dados no Data Lake Storage Gen1
Utiliza o Data Lake Gen1 de armazenamento do Azure Active Directory para autenticação e acesso controla listas (ACLs) para gerir o acesso aos seus dados.

| Funcionalidade | Descrição |
| --- | --- |
| Authentication |Gen1 de armazenamento do Data Lake está integrado com o Azure Active Directory (AAD) para a gestão de identidades e acessos para todos os dados armazenados no Data Lake Storage Gen1. Como resultado de integração, benefícios de geração 1 de armazenamento do Data Lake com todas as funcionalidades do AAD incluindo autenticação multifator, acesso condicional, controlo de acesso baseado em funções, monitorização da utilização de aplicações, segurança de monitorização e alertas, etc. Geração 1 de armazenamento do Data Lake suporta o protocolo de OAuth 2.0 para a autenticação com na REST interface. Consulte [autenticação de geração 1 de armazenamento do Data Lake](data-lakes-store-authentication-using-azure-active-directory.md)|
| Controlo de acesso |Geração 1 de armazenamento do Data Lake fornece controlo de acesso ao suportar permissões do estilo POSIX expostas pelo protocolo WebHDFS. As ACLs podem ser ativadas na pasta raiz, nas subpastas e nos ficheiros individuais. Para obter mais informações sobre como as ACLs funcionam no contexto da geração 1 de armazenamento do Data Lake, veja [controlo de acesso no Data Lake Storage Gen1](data-lake-store-access-control.md). |
| Encriptação |Geração 1 de armazenamento do Data Lake também fornece encriptação para dados armazenados na conta. Especifique as definições de encriptação durante a criação de uma conta de geração 1 de armazenamento do Data Lake. Pode optar por encriptar os seus dados ou por não utilizar encriptação. Para obter mais informações, consulte [encriptação no Data Lake Storage Gen1](data-lake-store-encryption.md). Para obter instruções sobre como fornecer configuração relacionada com encriptação, consulte [introdução ao Azure Data Lake Storage Gen1 através do Portal do Azure](data-lake-store-get-started-portal.md). |

Quer saber mais sobre como proteger dados no Data Lake Storage Gen1? Siga as ligações abaixo.

* Para obter instruções sobre como proteger os dados na geração 1 de armazenamento do Data Lake, veja [proteger dados no Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).
* Prefere vídeos? [Assista a este vídeo](https://mix.office.com/watch/1q2mgzh9nn5lx) sobre como proteger os dados armazenados na geração 1 de armazenamento do Data Lake.

## <a name="applications-compatible-with-data-lake-storage-gen1"></a>Aplicações compatíveis com o Data Lake Storage Gen1
Geração 1 de armazenamento do Data Lake é compatível com mais componentes de origem no ecossistema do Hadoop. Também é integrado corretamente com outros serviços do Azure. Isso torna a geração 1 de armazenamento do Data Lake opção perfeita para as suas necessidades de armazenamento de dados. Siga os links abaixo para obter mais informações sobre como geração 1 de armazenamento do Data Lake pode ser utilizado tanto com componentes de código-fonte aberto, bem como outros serviços do Azure.

* Ver [aplicativos e serviços compatíveis com o Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md) para obter uma lista de aplicações de código aberto interoperáveis com geração 1 de armazenamento do Data Lake.
* Ver [integração com outros serviços do Azure](data-lake-store-integrate-with-other-services.md) para compreender como geração 1 de armazenamento do Data Lake pode ser utilizado com outros serviços do Azure para ativar uma vasta gama de cenários.
* Ver [cenários de utilização de geração 1 de armazenamento do Data Lake](data-lake-store-data-scenarios.md) para aprender a utilizar o Data Lake Storage Gen1 em cenários como a ingestão de dados, processamento de dados, transferência de dados e visualização de dados.

## <a name="what-is-data-lake-storage-gen1-file-system-adl"></a>O que é o sistema de ficheiros de geração 1 de armazenamento do Data Lake (adl: / /)?
Geração 1 de armazenamento do Data Lake pode ser acedido através do novo sistema de ficheiros, o AzureDataLakeFilesystem (adl: / /), em ambientes do Hadoop (disponíveis com o cluster do HDInsight). As aplicações e serviços que utilizam adl: / / são capazes de tirar partido da otimização do desempenho que não estão atualmente disponíveis no WebHDFS. Como resultado, Gen1 de armazenamento do Data Lake dá-lhe a flexibilidade de beneficiar do melhor desempenho com a opção recomendada de utilização adl: / / ou manter o código existente ao continuar a utilizar a API WebHDFS diretamente. O Azure HDInsight tira total partido do AzureDataLakeFilesystem para proporcionar o melhor desempenho na geração 1 de armazenamento do Data Lake.

Pode aceder aos seus dados através da geração 1 de armazenamento do Data Lake `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. Para obter mais informações sobre como acessar os dados no Data Lake Storage Gen1, consulte [ver as propriedades dos dados armazenados](data-lake-store-get-started-portal.md#properties)

## <a name="next-steps"></a>Passos Seguintes

* [Introdução ao Data Lake Storage Gen1 através do Portal do Azure](data-lake-store-get-started-portal.md)
* [Introdução à geração 1 de armazenamento do Azure Data Lake com o .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Utilizar o Azure HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)