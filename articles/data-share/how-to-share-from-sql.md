---
title: Partilhar e receber dados da Base de Dados SQL do Azure e do Azure Synapse Analytics
description: Saiba como partilhar e receber dados da Azure SQL Database e da Azure Synapse Analytics
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 10/15/2020
ms.openlocfilehash: 85ddda4bbb6702ed8c82a40d603c8ca87ffb7053
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92217546"
---
# <a name="share-and-receive-data-from-azure-sql-database-and-azure-synapse-analytics"></a>Partilhar e receber dados da Base de Dados SQL do Azure e do Azure Synapse Analytics

[!INCLUDE[appliesto-sql](includes/appliesto-sql.md)]

A Azure Data Share suporta a partilha de dados Azure SQL e Azure Synapse Analytics (anteriormente Azure SQL DW). Este artigo explica como partilhar e receber dados destas fontes.

A Azure Data Share suporta a partilha de tabelas ou vistas da Azure SQL Database e da Azure Synapse Analytics (anteriormente Azure SQL DW). Os consumidores de dados podem optar por aceitar os dados em Azure Data Lake Storage Gen2 ou Azure Blob Storage como ficheiro csv ou parquet, bem como na Base de Dados Azure SQL e Azure Synapse Analytics como tabelas.

Ao aceitar dados no Azure Data Lake Store Gen2 ou no Azure Blob Storage, as imagens completas substituem o conteúdo do ficheiro-alvo se já existirem.
Quando os dados são recebidos em tabela e se a tabela-alvo já não existir, a Azure Data Share cria a tabela SQL com o esquema de origem. Se já existir uma tabela-alvo com o mesmo nome, será largada e substituída com o último instantâneo completo. As imagens incrementais não são suportadas atualmente.

## <a name="share-data"></a>Partilhar dados

### <a name="prerequisites-to-share-data"></a>Pré-requisitos para a partilha de dados

* Subscrição Azure: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* O endereço de e-mail Azure do seu destinatário (usando o seu pseudónimo de e-mail não funciona).
* Se a loja de dados Azure de origem estiver numa subscrição Azure diferente da que utilizará para criar o recurso Data Share, registe o [fornecedor de recursos Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) na subscrição onde está localizada a loja de dados Azure. 

### <a name="prerequisites-for-sql-source"></a>Pré-requisitos para a fonte SQL
Abaixo está a lista de pré-requisitos para a partilha de dados de fonte SQL. Também pode seguir a [demonstração passo](https://youtu.be/hIE-TjJD8Dc) a passo para configurar os pré-requisitos.

* Uma Base de Dados Azure SQL ou Azure Synapse Analytics (anteriormente SQL Data Warehouse) com tabelas e vistas que pretende partilhar.
* Permissão para escrever nas bases de dados do servidor SQL, que está presente no *Microsoft.Sql/servers/databases/write*. Esta permissão existe na função de Contribuidor.
* Permissão para que a partilha de dados aceda ao armazém de dados. Isto pode ser feito através dos seguintes passos: 
    1. No portal Azure, navegue para o servidor SQL e coloque-se como O Administrador Ativo Azure.
    1. Conecte-se à Base de Dados/Armazém de Dados Azure SQL utilizando [o Editor de Consulta](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) ou o SQL Server Management Studio com a autenticação do Azure Ative Directory. 
    1. Execute o seguinte script para adicionar o recurso Data Share Identidade Gerida como um db_datareader. Tem de se ligar utilizando o Ative Directory e não a autenticação do SQL Server. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Note que o *<share_acc_name>* é o nome do seu recurso Data Share. Se ainda não criou um recurso De Partilha de Dados, poderá voltar a este pré-requisito mais tarde.  

* Um Utilizador da Base de Dados Azure SQL com acesso "db_datareader" para navegar e selecione as tabelas e/ou vistas que deseja partilhar. 

* Acesso sql Server Firewall. Isto pode ser feito através dos seguintes passos: 
    1. No servidor SQL no portal Azure, navegue para *Firewalls e redes virtuais*
    1. Clique **em Sim** para permitir que os *serviços e recursos do Azure acedam a este servidor.*
    1. Clique **em +Adicionar IP ao cliente.** O endereço IP do cliente está sujeito a alterações. Este processo poderá ter de ser repetido da próxima vez que estiver a partilhar dados SQL do portal Azure. Também pode adicionar uma gama de IP.
    1. Clique em **Guardar**. 

### <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Criar uma conta de partilha de dados

Crie um recurso Azure Data Share num grupo de recursos Azure.

1. Selecione o botão de menu no canto superior esquerdo do portal e, em seguida, selecione **Criar um recurso** (+).

1. Procurar *por Partilha de Dados.*

1. Selecione partilha de dados e **selecione Criar**.

1. Preencha os detalhes básicos do seu recurso Azure Data Share com as seguintes informações. 

     **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Subscrição | A sua subscrição | Selecione a subscrição Azure que pretende utilizar para a sua conta de partilha de dados.|
    | Grupo de recursos | *grupo de recursos de teste* | Utilize um grupo de recursos existente ou crie um novo grupo de recursos. |
    | Localização | *E.U.A. Leste 2* | Selecione uma região para a sua conta de partilha de dados.
    | Name | *conta de datashare* | Especifique um nome para a sua conta de partilha de dados. |
    | | |

1. Selecione **Rever + criar**e, em seguida, **criar** para obter a sua conta de partilha de dados. O fornecimento de uma nova conta de partilha de dados normalmente demora cerca de 2 minutos ou menos. 

1. Quando a implementação estiver concluída, selecione **Ir para o recurso**.

### <a name="create-a-share"></a>Criar uma parte

1. Navegue para a sua página de visão geral da partilha de dados.

    ![Partilhar os seus dados](./media/share-receive-data.png "Partilhar os seus dados") 

1. Selecione **Comece a partilhar os seus dados.**

1. Selecione **Criar**.   

1. Preencha os detalhes da sua parte. Especifique um nome, tipo de partilha, descrição do conteúdo da partilha e termos de utilização (opcional). 

    ![EnterShareDetails](./media/enter-share-details.png "Insira detalhes de partilha") 

1. Selecione **Continuar**.

1. Para adicionar Conjuntos de Dados à sua parte, selecione **Adicionar Conjuntos de Dados**. 

    ![Adicione conjuntos de dados à sua parte](./media/datasets.png "Conjuntos de dados")

1. Selecione o tipo de conjunto de dados que gostaria de adicionar. Verá uma lista diferente de tipos de conjuntos de dados dependendo do tipo de partilha (instantâneo ou no local) que selecionou no passo anterior. 

    ![AdicionarDatasets](./media/add-datasets.png "Adicionar conjuntos de dados")    

1. Selecione o seu servidor SQL, forneça credenciais e selecione **Seguinte** para navegar no objeto que pretende partilhar e selecione 'Adicionar conjuntos de dados'. 

    ![SelecioneDatasets](./media/select-datasets-sql.png "Selecione conjuntos de dados")    

1. No separador Destinatários, insira nos endereços de e-mail do seu Consumidor de Dados selecionando '+ Adicionar Destinatário'. 

    ![AdicionarRecipients](./media/add-recipient.png "Adicionar destinatários") 

1. Selecione **Continuar**.

1. Se tiver selecionado o tipo de partilha snapshot, pode configurar o calendário de instantâneos para fornecer atualizações dos seus dados ao seu consumidor de dados. 

    ![Ativar Fotos](./media/enable-snapshots.png "Ativar instantâneos") 

1. Selecione um intervalo de tempo de início e recorrência. 

1. Selecione **Continuar**.

1. No separador 'Rever + Criar', rever os conteúdos, definições, destinatários e definições de sincronização do pacote. Selecione **Criar**.

A sua Azure Data Share foi agora criada e o destinatário da sua Partilha de Dados está agora pronto para aceitar o seu convite. 

## <a name="receive-data"></a>Receber dados

### <a name="prerequisites-to-receive-data"></a>Pré-requisitos para receber dados
Antes de aceitar um convite para partilha de dados, deve providenciar uma série de recursos Azure, que estão listados abaixo. 

Certifique-se de que todos os pré-requisitos estão completos antes de aceitar um convite de partilha de dados. 

* Subscrição Azure: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Um convite para partilha de dados: Um convite da Microsoft Azure com um tema intitulado "Azure Data Share invitation **<yourdataprovider@domain.com>** from".
* Registe o [fornecedor de recursos Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) na subscrição do Azure, onde irá criar um recurso Data Share e a subscrição Azure onde estão localizadas as lojas de dados Azure alvo.

### <a name="prerequisites-for-target-storage-account"></a>Pré-requisitos para a conta de armazenamento alvo
Se optar por receber dados no Azure Storage, abaixo está a lista de pré-requisitos.

* Uma conta de Armazenamento Azure: Se ainda não tiver uma, pode criar uma [conta de Armazenamento Azure](../storage/common/storage-account-create.md). 
* Permissão para escrever na conta de armazenamento, que está presente na *Microsoft.Storage/storageAccounts/write*. Esta permissão existe na função de Contribuidor. 
* Permissão para adicionar atribuição de funções à conta de armazenamento, que está presente na *Microsoft.Autorização/atribuições/escrita de funções.* Esta permissão existe na função de Proprietário.  

### <a name="prerequisites-for-sql-target"></a>Pré-requisitos para o alvo SQL
Se optar por receber dados na Base de Dados Azure SQL, a Azure Synapse Analytics, abaixo está a lista de pré-requisitos. Também pode seguir a [demonstração passo](https://youtu.be/aeGISgK1xro) a passo para configurar os pré-requisitos.

* Permissão para escrever para bases de dados no servidor SQL, que está presente no *Microsoft.Sql/servers/databases/write*. Esta permissão existe na função de Contribuidor. 
* Permissão para a identidade gerida do recurso de partilha de dados para aceder à Base de Dados Azure SQL ou Azure Synapse Analytics. Isto pode ser feito através dos seguintes passos: 
    1. No portal Azure, navegue para o servidor SQL e coloque-se como O Administrador Ativo Azure.
    1. Conecte-se à Base de Dados/Armazém de Dados Azure SQL utilizando [o Editor de Consulta](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) ou o SQL Server Management Studio com a autenticação do Azure Ative Directory. 
    1. Execute o seguinte script para adicionar a Identidade Gerida de Partilha de Dados como um "db_datareader, db_datawriter, db_ddladmin". Tem de se ligar utilizando o Ative Directory e não a autenticação do SQL Server. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Note que o *<share_acc_name>* é o nome do seu recurso Data Share. Se ainda não criou um recurso De Partilha de Dados, poderá voltar a este pré-requisito mais tarde.         

* Acesso sql Server Firewall. Isto pode ser feito através dos seguintes passos: 
    1. No servidor SQL no portal Azure, navegue para *Firewalls e redes virtuais*
    1. Clique **em Sim** para permitir que os *serviços e recursos do Azure acedam a este servidor.*
    1. Clique **em +Adicionar IP ao cliente.** O endereço IP do cliente está sujeito a alterações. Este processo poderá ter de ser repetido da próxima vez que estiver a partilhar dados SQL do portal Azure. Também pode adicionar uma gama de IP.
    1. Clique em **Guardar**. 

### <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

### <a name="open-invitation"></a>Convite aberto

1. Pode abrir o convite a partir de e-mail ou diretamente do portal Azure. 

   Para abrir o convite a partir de e-mail, consulte a sua caixa de entrada para obter um convite do seu fornecedor de dados. O convite é da Microsoft Azure, intitulada **Azure <yourdataprovider@domain.com> Data Share invitation from **. Clique em **Ver convite** para ver o seu convite em Azure. 

   Para abrir diretamente o convite do portal Azure, procure convites de **partilha de dados** no portal Azure. Isto leva-o à lista de convites da Partilha de Dados.

   ![Lista de Convites](./media/invitations.png "Lista de convites") 

1. Selecione a partilha que gostaria de ver. 

### <a name="accept-invitation"></a>Aceite convite
1. Certifique-se de que todos os campos são revistos, incluindo os **Termos de Utilização.** Se concordar com os termos de uso, será obrigado a verificar a caixa para indicar que concorda. 

   ![Termos de Utilização](./media/terms-of-use.png "Termos de utilização") 

1. Na *Conta De Partilha de Dados-Alvo,* selecione o Grupo de Subscrição e Recursos em que irá implementar a sua Partilha de Dados. 

   Para o campo **Conta Partilha de Dados,** selecione **Criar novo** se não tiver uma conta de Partilha de Dados existente. Caso contrário, selecione uma conta de Partilha de Dados existente que gostaria de aceitar a sua partilha de dados. 

   Para o campo **Nome de Partilha Recebida,** pode deixar o padrão especificado pelos dados fornecerem ou especificar um novo nome para a partilha recebida. 

   Uma vez acordado com os termos de utilização e especificado uma conta De partilha de dados para gerir a sua parte recebida, **Selecione Aceitar e configurar**. Será criada uma subscrição de ações. 

   ![Aceitar opções](./media/accept-options.png "Aceitar opções") 

   Isto leva-o até à sua conta de Partilha de Dados. 

   Se não quiser aceitar o convite, Selecione *Rejeitar*. 

### <a name="configure-received-share"></a>Configure recebeu parte
Siga os passos abaixo para configurar onde pretende receber dados.

1. Selecione **datasets** separador. Verifique a caixa ao lado do conjunto de dados para o qual pretende atribuir um destino. Selecione **+ Map para o alvo** para escolher uma loja de dados alvo. 

   ![Mapa para alvo](./media/dataset-map-target.png "Mapa para alvo") 

1. Selecione uma loja de dados-alvo que gostaria que os dados aterrassem. Quaisquer ficheiros de dados ou tabelas na loja de dados-alvo com o mesmo caminho e nome serão substituídos. 

   ![Conta de armazenamento alvo](./media/dataset-map-target-sql.png "Loja de Dados-Alvo") 

1. Para a partilha baseada em instantâneos, se o fornecedor de dados criou um calendário instantâneo para fornecer uma atualização regular aos dados, também pode ativar o horário de instantâneo selecionando o separador **'Agenda snapshot'.** Verifique a caixa ao lado do horário de instantâneo e selecione **+ Ativar**.

   ![Ativar o horário do instantâneo](./media/enable-snapshot-schedule.png "Ativar o horário do instantâneo")

### <a name="trigger-a-snapshot"></a>Desencadear um instantâneo
Estes passos aplicam-se apenas à partilha baseada em instantâneos.

1. Pode ativar uma imagem instantânea selecionando o separador **Detalhes** seguido do **instantâneo Trigger**. Aqui, pode desencadear uma imagem completa ou incremental dos seus dados. Se for a primeira vez que recebe dados do seu fornecedor de dados, selecione cópia completa. Para as fontes SQL, apenas o instantâneo completo é suportado.

   ![Instantâneo do gatilho](./media/trigger-snapshot.png "Instantâneo do gatilho") 

1. Quando o estado da última execução for *bem sucedido,* vá à loja de dados de destino para ver os dados recebidos. Selecione **Conjuntos de dados**e clique no link no Caminho Alvo. 

   ![Conjuntos de dados do consumidor](./media/consumer-datasets.png "Mapeamento do conjunto de dados do consumidor") 

### <a name="view-history"></a>Ver histórico
Este passo aplica-se apenas à partilha baseada em instantâneos. Para ver o histórico das suas fotos, selecione **Histórico.** Aqui encontrará a história de todos os instantâneos que foram gerados nos últimos 30 dias. 

## <a name="supported-data-types"></a>Tipos de dados suportados
Quando partilha dados a partir de fonte SQL, os seguintes mapeamentos são usados desde os tipos de dados do SQL Server até aos tipos de dados provisórios do Azure Data Share durante o processo de instantâneo. 

| Tipo de dados do SQL Server | Tipo de dados provisórios Azure Data Share |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Booleano |
| char |String, Char[] |
| data |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datatimeoff |Início de execução de tempo de data |
| Decimal |Decimal |
| Atributo FILESTREAM (varbinário(máx)) |Byte[] |
| Float |Double (Duplo) |
| image |Byte[] |
| int |Int32 |
| dinheiro |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Único |
| rowversão |Byte[] |
| hora pequena |DateTime |
| smallint |Int16 |
| pequeno dinheiro |Decimal |
| sql_variant |Objeto |
| texto |String, Char[] |
| hora |TimeSpan |
| carimbo de data/hora |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |GUID |
| varbinário |Byte[] |
| varchar |String, Char[] |
| xml |String |

>[!NOTE]
> 1. Para os tipos de dados que mapeiam para o tipo de decimal provisório, atualmente o instantâneo suporta precisão até 28. Se tiver dados que exijam precisão superior a 28, considere converter-se numa corda. 
> 1.  Se estiver a partilhar dados da base de dados Azure SQL para a Azure Synapse Analytics, nem todos os tipos de dados são suportados. Consulte os [tipos de dados de tabela na piscina Synapse SQL](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-data-types.md) para obter mais detalhes. 

## <a name="sql-always-encrypted-or-dynamic-data-masking"></a>SQL Sempre Encriptado ou Dinâmico Mascaramento de Dados
Atualmente, a Azure Data Share não suporta bases de dados Azure SQL com sempre configurado. 

Para as tabelas SQL de origem com máscara de dados dinâmica, os dados aparecerão mascarados no lado do destinatário.

## <a name="sql-snapshot-performance"></a>Desempenho instantâneo SQL
O desempenho do instantâneo SQL é impactado por uma série de fatores. É sempre recomendado realizar o seu próprio teste de desempenho. Abaixo estão alguns fatores de exemplo que impactam o desempenho.

* Configuração de hardware (por exemplo, vCores, memória, DWU) da loja de dados sql de origem e alvo. 
* Acesso simultâneo às lojas de dados de origem e alvo. Se estiver a partilhar várias tabelas e vistas da mesma loja de dados SQL, ou receber várias tabelas e vistas para a mesma loja de dados SQL, o desempenho será impactado.   
* Localização das lojas de dados de origem e alvo. 

## <a name="troubleshoot-sql-snapshot-failure"></a>Falha de instantâneo SQL de resolução de problemas
A causa mais comum de falha instantânea é que a Data Share não tem permissão para a fonte ou loja de dados alvo. Para conceder permissão de Partilha de Dados à loja de dados de origem ou SQL alvo, deve executar o script SQL fornecido ao ligar-se à base de dados SQL utilizando a autenticação do Azure Ative Directory. Para resolver problemas adicionais de falha de instantâneo SQL, consulte a [falha do instantâneo de resolução de problemas](data-share-troubleshoot.md#snapshot-failed).

## <a name="next-steps"></a>Passos seguintes
Aprendeu a partilhar e a receber dados de fontes SQL utilizando o serviço Azure Data Share. Para saber mais sobre a partilha de outras fontes de dados, continue a [apoiar as lojas de dados.](supported-data-stores.md)