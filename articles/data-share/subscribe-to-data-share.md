---
title: 'Tutorial: Aceite & receber dados - Azure Data Share'
description: Tutorial - Aceitar e receber dados usando a Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 08/14/2020
ms.openlocfilehash: ce47bc5e880f15eaa1bbf07477673d2475e5a10a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89489923"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Tutorial: Aceitar e receber dados com o Azure Data Share  

Neste tutorial, você aprenderá a aceitar um convite de partilha de dados usando a Azure Data Share. Você vai aprender a receber dados que estão sendo partilhados com você, bem como como ativar um intervalo de atualização regular para garantir que você sempre tem a imagem mais recente dos dados que estão sendo partilhados com você. 

> [!div class="checklist"]
> * Como aceitar um convite Azure Data Share
> * Criar uma conta Azure Data Share
> * Especifique um destino para os seus dados
> * Crie uma subscrição da sua partilha de dados para atualização agendada

## <a name="prerequisites"></a>Pré-requisitos
Antes de aceitar um convite para partilha de dados, deve providenciar uma série de recursos Azure, que estão listados abaixo. 

Certifique-se de que todos os pré-requisitos estão completos antes de aceitar um convite de partilha de dados. 

* Subscrição Azure: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Um convite para partilha de dados: Um convite da Microsoft Azure com um tema intitulado "Azure Data Share invitation **<yourdataprovider@domain.com>** from".
* Registe o [fornecedor de recursos Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) na subscrição do Azure, onde irá criar um recurso Data Share e a subscrição Azure onde estão localizadas as lojas de dados Azure alvo.

### <a name="receive-data-into-a-storage-account"></a>Receber dados numa conta de armazenamento: 

* Uma conta de Armazenamento Azure: Se ainda não tiver uma, pode criar uma [conta de Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Permissão para escrever na conta de armazenamento, que está presente na *Microsoft.Storage/storageAccounts/write*. Esta permissão existe na função de Contribuidor. 
* Permissão para adicionar atribuição de funções à conta de armazenamento, que está presente na *Microsoft.Autorização/atribuições/escrita de funções.* Esta permissão existe na função de Proprietário.  

### <a name="receive-data-into-a-sql-based-target"></a>Receber dados num alvo baseado em SQL:

* Permissão para escrever para bases de dados no servidor SQL, que está presente no *Microsoft.Sql/servers/databases/write*. Esta permissão existe na função de Contribuidor. 
* Permissão para a identidade gerida do recurso de partilha de dados para aceder à Base de Dados Azure SQL ou Azure Synapse Analytics. Isto pode ser feito através dos seguintes passos: 
    1. Coloque-se como o Azure Ative Directory Admin para o servidor SQL.
    1. Ligue-se à Base de Dados/Armazém de Dados Azure SQL utilizando o Azure Ative Directory.
    1. Utilize o Editor de Consulta (pré-visualização) para executar o seguinte script para adicionar a Identidade Gerida de Partilha de Dados como uma "db_datareader, db_datawriter, db_ddladmin". Tem de se ligar utilizando o Ative Directory e não a autenticação do SQL Server. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Note que o *<share_acc_name>* é o nome do seu recurso Data Share. Se ainda não criou um recurso De Partilha de Dados, poderá voltar a este pré-requisito mais tarde.         

* Acesso ao Servidor IP SQL do cliente. Isto pode ser feito através dos seguintes passos: 
    1. No servidor SQL no portal Azure, navegue para *Firewalls e redes virtuais*
    1. Clique **no** alternador para permitir o acesso aos Serviços Azure.
    1. Clique **em +Adicionar IP ao cliente** e clique em **Guardar.** O endereço IP do cliente está sujeito a alterações. Este processo poderá ter de ser repetido da próxima vez que estiver a receber dados num alvo SQL a partir do portal Azure. Também pode adicionar uma gama de IP. 


### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Receba dados num cluster Azure Data Explorer: 

* Um cluster Azure Data Explorer no mesmo centro de dados Azure que o cluster data Explorer do fornecedor de dados: Se ainda não tiver um, pode criar um [cluster Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal). Se não conhecer o centro de dados Azure do cluster do fornecedor de dados, pode criar o cluster mais tarde no processo.
* Permissão para escrever para o cluster Azure Data Explorer, que está presente na *Microsoft.Kusto/clusters/write*. Esta permissão existe na função de Contribuidor. 
* Permissão para adicionar atribuição de funções ao cluster Azure Data Explorer, que está presente na *Microsoft.Authorization/role assignments/write*. Esta permissão existe na função de Proprietário. 

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="open-invitation"></a>Convite aberto

1. Pode abrir o convite a partir de e-mail ou diretamente do portal Azure. 

   Para abrir o convite a partir de e-mail, consulte a sua caixa de entrada para obter um convite do seu fornecedor de dados. O convite é da Microsoft Azure, intitulada **Azure <yourdataprovider@domain.com> Data Share invitation from **. Clique em **Ver convite** para ver o seu convite em Azure. 

   Para abrir diretamente o convite do portal Azure, procure convites de **partilha de dados** no portal Azure. Isto leva-o à lista de convites da Partilha de Dados.

   ![Lista de Convites](./media/invitations.png "Lista de convites") 

1. Selecione a partilha que gostaria de ver. 

## <a name="accept-invitation"></a>Aceite convite
1. Certifique-se de que todos os campos são revistos, incluindo os **Termos de Utilização.** Se concordar com os termos de uso, será obrigado a verificar a caixa para indicar que concorda. 

   ![Termos de Utilização](./media/terms-of-use.png "Termos de utilização") 

1. Na *Conta De Partilha de Dados-Alvo,* selecione o Grupo de Subscrição e Recursos em que irá implementar a sua Partilha de Dados. 

   Para o campo **Conta Partilha de Dados,** selecione **Criar novo** se não tiver uma conta de Partilha de Dados existente. Caso contrário, selecione uma conta de Partilha de Dados existente que gostaria de aceitar a sua partilha de dados. 

   Para o campo **Nome de Partilha Recebida,** pode deixar o padrão especificado pelos dados fornecerem ou especificar um novo nome para a partilha recebida. 

   Uma vez acordado com os termos de utilização e especificado uma conta De partilha de dados para gerir a sua parte recebida, **Selecione Aceitar e configurar**. Será criada uma subscrição de ações. 

   ![Aceitar opções](./media/accept-options.png "Aceitar opções") 

   Isto leva-o até à sua conta de Partilha de Dados. 

   Se não quiser aceitar o convite, Selecione *Rejeitar*. 

## <a name="configure-received-share"></a>Configure recebeu parte
Siga os passos abaixo para configurar onde pretende receber dados.

1. Selecione **datasets** separador. Verifique a caixa ao lado do conjunto de dados para o qual pretende atribuir um destino. Selecione **+ Map para o alvo** para escolher uma loja de dados alvo. 

   ![Mapa para alvo](./media/dataset-map-target.png "Mapa para alvo") 

1. Selecione um tipo de loja de dados alvo que gostaria que os dados aterrassem. Quaisquer ficheiros de dados ou tabelas na loja de dados-alvo com o mesmo caminho e nome serão substituídos. 

   Para a partilha no local, selecione uma loja de dados na Localização especificada. A Localização é o centro de dados Azure onde a loja de dados do fornecedor de dados está localizada. Uma vez mapeado o conjunto de dados, pode seguir o link no Caminho-alvo para aceder aos dados.

   ![Conta de armazenamento alvo](./media/dataset-map-target-sql.png "Armazenamento de destino") 

1. Para a partilha baseada em instantâneos, se o fornecedor de dados criou um calendário instantâneo para fornecer uma atualização regular aos dados, também pode ativar o horário de instantâneo selecionando o separador **'Agenda snapshot'.** Verifique a caixa ao lado do horário de instantâneo e selecione **+ Ativar**.

   ![Ativar o horário do instantâneo](./media/enable-snapshot-schedule.png "Ativar o horário do instantâneo")

## <a name="trigger-a-snapshot"></a>Desencadear um instantâneo
Estes passos aplicam-se apenas à partilha baseada em instantâneos.

1. Pode ativar uma imagem instantânea selecionando o separador **Detalhes** seguido do **instantâneo Trigger**. Aqui, pode desencadear uma imagem completa ou incremental dos seus dados. Se for a primeira vez que recebe dados do seu fornecedor de dados, selecione cópia completa. 

   ![Instantâneo do gatilho](./media/trigger-snapshot.png "Instantâneo do gatilho") 

1. Quando o estado da última execução for *bem sucedido,* vá à loja de dados de destino para ver os dados recebidos. Selecione **Conjuntos de dados**e clique no link no Caminho Alvo. 

   ![Conjuntos de dados do consumidor](./media/consumer-datasets.png "Mapeamento do conjunto de dados do consumidor") 

## <a name="view-history"></a>Ver histórico
Este passo aplica-se apenas à partilha baseada em instantâneos. Para ver o histórico das suas fotos, selecione **Histórico.** Aqui encontrará a história de todos os instantâneos que foram gerados nos últimos 30 dias. 

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a aceitar e receber uma Partilha de Dados Azure. Para saber mais sobre os conceitos Azure Data Share, continue a [Conceitos: Azure Data Share Terminology](terminology.md).