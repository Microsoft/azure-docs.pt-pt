---
title: Visualize os dados a partir do Explorador de dados do Azure com Sisense
description: Neste artigo, saiba como configurar o Explorador de dados do Azure como uma origem de dados para Sisense e visualize os dados.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: f0840b90e1036c23fa58d94515bfeb035299c07f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66358187"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>Visualizar dados do Azure Data Explorer no Sisense

Sisense é uma plataforma de informações de negócios de análise que lhe permite criar aplicações de análise que proporcionam experiências de usuário altamente interativas. O business intelligence e o dashboard, relatório de software permite-lhe aceder e combinar dados em apenas alguns cliques. Pode ligar aos dados estruturados e não estruturados de origens, Junte-se a tabelas de várias origens com um mínimo de criação de scripts e de codificação e criar web interativos dashboards e relatórios. Neste artigo, aprenderá como configurar o Explorador de dados do Azure como uma origem de dados para Sisense e visualizar dados de um cluster de exemplo.

## <a name="prerequisites"></a>Pré-requisitos

É necessário o seguinte para concluir este artigo:

* [Transferir e instalar a aplicação de Sisense](https://documentation.sisense.com/latest/getting-started/download-install.htm) 

* Crie um cluster e a base de dados que inclui os dados de exemplo StormEvents. Para obter mais informações, consulte [início rápido: Criar um cluster do Explorador de dados do Azure e a base de dados](create-cluster-database-portal.md) e [ingerir dados de exemplo no Explorador de dados do Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>Ligue a dashboards Sisense utilizando o conector do JDBC de Explorador de dados do Azure

1. Transferir e copiar as versões mais recentes dos seguintes ficheiros jar para *.... \Sisense\DataConnectors\jdbcdrivers\adx* 

    * activation-1.1.jar
    * adal4j-1.6.0.jar
    * commons-codec-1.10.jar
    * commons-collections4-4.1.jar
    * commons-lang3-3.5.jar
    * gson-2.8.0.jar
    * jcip-annotations-1.0-1.jar
    * json-smart-1.3.1.jar
    * lang-tag-1.4.4.jar
    * mail-1.4.7.jar
    * mssql-jdbc-7.2.1.jre8.jar
    * nimbus-jose-jwt-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar
    
1. Open **Sisense** aplicação.
1. Selecione **dados** separador e selecione **+ ElastiCube** para criar um novo modelo de ElastiCube.
    
    ![Selecione ElastiCube](media/sisense/data-select-elasticube.png)

1. Na **adicionar novo modelo de ElastiCube**, dê o nome do modelo de ElastiCube e **guardar**.
   
    ![Adicionar novo modelo de ElastiCube](media/sisense/add-new-elasticube-model.png)

1. Selecione **+ dados**.

    ![Selecione o botão de dados](media/sisense/select-data.png)

1. Na **selecione o conector** separador, selecione **JDBC genérico** conector.

    ![Escolha o conector JDBC](media/sisense/select-connector.png)

1. Na **Connect** separador, preencha os campos seguintes para o **genérico JDBC** conector e selecione **seguinte**.

    ![Definições do conector JDBC](media/sisense/jdbc-connector.png)

    |Campo |Descrição |
    |---------|---------|
    |Cadeia de Ligação     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |Pasta JDBC JARs  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |Nome da classe do controlador    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |Nome de utilizador   |    Nome de utilizador do AAD     |
    |Palavra-passe     |   Palavra-passe de utilizador do AAD      |

1. Na **selecionar dados** separador, pesquisar **selecionar base de dados** para selecionar a base de dados relevante para o qual tem permissões. Neste exemplo, selecione *test1*.

    ![Selecione a base de dados](media/sisense/select-database.png)

1. Na *testar* painel (nome de base de dados):
    1. Selecione o nome da tabela para pré-visualizar a tabela e ver os nomes de coluna de tabela. Pode remover colunas desnecessárias.
    1. Selecione a caixa de verificação da tabela relevante para selecionar essa tabela. 
    1. Selecione **Done** (Concluído).

    ![Selecionar tabela](media/sisense/select-table-see-columns.png)    

1. Selecione **criar** para criar o conjunto de dados. 

    * Na **crie** janela, selecione **criar**.

      ![Criar a janela](media/sisense/build-window.png)

    * Aguarde até que o processo de compilação é concluída e, em seguida, selecione **criar foi concluída com êxito**.

      ![Compilação efetuada com êxito](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>Criar dashboards de Sisense

1. Na **Analytics** separador, selecione **+**  >  **novo Dashboard** para criar dashboards neste conjunto de dados.

    ![Novo dashboard](media/sisense/new-dashboard.png)

1. Escolha um dashboard e selecione **criar**. 

    ![criar dashboard](media/sisense/create-dashboard.png)

1. Sob **Widget novos**, selecione **+ selecionar dados** para criar um novo widget. 

    ![Adicionar campos ao StormEvents dashboard](media/sisense/storm-dashboard-add-field.png)  

1. Selecione **+ adicionar mais dados** para adicionar colunas adicionais ao seu gráfico. 

    ![Adicionar mais dados ao gráfico](media/sisense/add-more-data.png)

1. Selecione **+ Widget** criar noutro widget. Widgets de arrastar e soltar reorganizar seu dashboard.

    ![Dashboard Storm](media/sisense/final-dashboard.png)

Agora, pode explorar os seus dados com o visual analytics, criar dashboards adicionais e transformar dados em informações acionáveis para causar impacto no seu negócio.

## <a name="next-steps"></a>Passos Seguintes

* [Escrever consultas do Azure Data Explorer](write-queries.md)

