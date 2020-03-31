---
title: Visualizar dados do Azure Data Explorer usando o Sisense
description: Neste artigo, aprenda a configurar o Azure Data Explorer como fonte de dados para o Sisense e visualize os dados.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: f0840b90e1036c23fa58d94515bfeb035299c07f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66358187"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>Visualizar dados do Azure Data Explorer em Sisense

A Sisense é uma plataforma de inteligência empresarial de análise que lhe permite construir aplicações de análise que oferecem experiências de utilizadores altamente interativas. O software de informação de negócios e relatórios de dashboard permite-lhe aceder e combinar dados em alguns cliques. Pode ligar-se a fontes de dados estruturadas e não estruturadas, juntar tabelas de múltiplas fontes com o mínimo de scripts e codificação e criar dashboards e relatórios web interativos. Neste artigo, você vai aprender a configurar o Azure Data Explorer como uma fonte de dados para o Sisense, e visualizar dados de um cluster de amostras.

## <a name="prerequisites"></a>Pré-requisitos

Precisa do seguinte para completar este artigo:

* [Descarregue e instale a aplicação Sisense](https://documentation.sisense.com/latest/getting-started/download-install.htm) 

* Crie um cluster e base de dados que inclua os dados da amostra StormEvents. Para mais informações, consulte [Quickstart: Crie um cluster e base de dados do Azure Data Explorer e](create-cluster-database-portal.md) [os dados da amostra ingest no Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>Ligue-se aos dashboards Sisense utilizando o conector Azure Data Explorer JDBC

1. Descarregue e copie as versões mais recentes dos seguintes ficheiros de frascos para *.. \Sisense\DataConnectors\jdbcdrivers\adx* 

    * ativação-1.1.jar
    * adal4j-1.6.0.jar
    * commons-codec-1.10.jar
    * commons-collections4-4.1.jar
    * commons-lang3-3.5.jar
    * gson-2.8.0.jar
    * jcip-anotações-1.0-1.jar
    * json-smart-1.3.1.jar
    * lang-tag-1.4.4.jar
    * correio-1.4.7.jar
    * mssql-jdbc-7.2.1.jre8.jar
    * nimbus-jose-jwt-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar
    
1. Abra a aplicação **Sisense.**
1. Selecione o separador **Dados** e selecione **+ElastiCube** para criar um novo modelo ElastiCube.
    
    ![Selecione ElastiCube](media/sisense/data-select-elasticube.png)

1. Em **Adicionar novo Modelo ElastiCube,** nomeie o modelo ElastiCube e **guarde**.
   
    ![Adicione o novo modelo ElastiCube](media/sisense/add-new-elasticube-model.png)

1. Selecione **+ Dados**.

    ![Selecione botão de dados](media/sisense/select-data.png)

1. No **separador Selecione Conector,** selecione conector **Genérico JDBC.**

    ![Escolha o conector JDBC](media/sisense/select-connector.png)

1. No separador **Ligar,** preencha os seguintes campos para o conector **Genérico JDBC** e selecione **Seguinte**.

    ![Definições do conector JDBC](media/sisense/jdbc-connector.png)

    |Campo |Descrição |
    |---------|---------|
    |Fio de ligação     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |Pasta JDBC JARs  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |Nome da classe do motorista    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |Nome de Utilizador   |    Nome de utilizador AAD     |
    |Palavra-passe     |   Senha de utilizador AAD      |

1. No separador **'Dados** Selecionados', procure **selecione Base de Dados** para selecionar a base de dados relevante para a qual tem permissões. Neste exemplo, selecione *test1*.

    ![selecionar base de dados](media/sisense/select-database.png)

1. No painel de *ensaio* (nome da base de dados):
    1. Selecione o nome da tabela para pré-visualizar a tabela e veja os nomes das colunas da tabela. Pode remover colunas desnecessárias.
    1. Selecione a caixa de verificação da tabela relevante para selecionar essa tabela. 
    1. Selecione **Done** (Concluído).

    ![selecionar tabela](media/sisense/select-table-see-columns.png)    

1. Selecione **Construir** para construir o seu conjunto de dados. 

    * Na janela **Build,** selecione **Construir**.

      ![Construir janela](media/sisense/build-window.png)

    * Aguarde até que o processo de construção esteja concluído e, em seguida, **selecione Build Succeeded**.

      ![Construção conseguiu](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>Criar dashboards Sisense

1. No separador **+**  >  **Analytics,** selecione **New Dashboard** para criar dashboards neste conjunto de dados.

    ![Novo dashboard](media/sisense/new-dashboard.png)

1. Escolha um painel de instrumentos e selecione **Criar**. 

    ![Criar painel de instrumentos](media/sisense/create-dashboard.png)

1. Em **New Widget,** selecione **+ Selecione Dados** para criar um novo widget. 

    ![Adicione campos ao dashboard StormEvents](media/sisense/storm-dashboard-add-field.png)  

1. Selecione **+ Adicione mais Dados** para adicionar colunas adicionais ao seu gráfico. 

    ![Adicione mais dados ao gráfico](media/sisense/add-more-data.png)

1. Selecione **+ Widget** para criar outro widget. Arraste e deixe cair widgets para reorganizar o seu painel de instrumentos.

    ![Dashboard Storm](media/sisense/final-dashboard.png)

Agora pode explorar os seus dados com análise visual, construir dashboards adicionais e transformar dados em insights atuais para causar impacto no seu negócio.

## <a name="next-steps"></a>Passos seguintes

* [Escrever consultas do Azure Data Explorer](write-queries.md)

