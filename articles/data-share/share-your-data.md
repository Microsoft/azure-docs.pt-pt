---
title: 'Tutorial: compartilhar fora de sua organização-compartilhamento de dados do Azure'
description: Tutorial – compartilhar dados com clientes e parceiros usando o compartilhamento de dados do Azure
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 4ef9256404b0d0d4d6379e4f5a76c0d41a38c7cd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499307"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Tutorial: compartilhar dados usando o compartilhamento de dados do Azure  

Neste tutorial, você aprenderá a configurar um novo compartilhamento de dados do Azure e a começar a compartilhar seus dados com clientes e parceiros fora da sua organização do Azure. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um compartilhamento de dados.
> * Adicione conjuntos de dados ao seu compartilhamento de dado.
> * Habilite um agendamento de sincronização para seu compartilhamento de dados. 
> * Adicione destinatários ao seu compartilhamento de dados. 

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure: se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* O endereço de email de logon do Azure do destinatário (usando seu alias de email não funcionará).

### <a name="share-from-a-storage-account"></a>Compartilhar de uma conta de armazenamento:

* Uma conta de armazenamento do Azure: se você ainda não tiver uma, poderá criar uma [conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Permissão para adicionar a atribuição de função à conta de armazenamento, que está presente na permissão *Microsoft. Authorization/role atribuitions/Write* . Essa permissão existe na função proprietário. 

### <a name="share-from-a-sql-based-source"></a>Compartilhar de uma fonte baseada em SQL:

* Um banco de dados SQL do Azure ou o Azure SQL Data Warehouse com tabelas e exibições que você deseja compartilhar.
* Permissão para o compartilhamento de dados acessar o data warehouse. Isso pode ser feito por meio das seguintes etapas: 
    1. Defina-se como o administrador do Azure Active Directory para o servidor.
    1. Conecte-se ao banco de dados SQL do Azure/data warehouse usando Azure Active Directory.
    1. Use o editor de consultas (versão prévia) para executar o script a seguir para adicionar o MSI de compartilhamento de dados como um db_owner. Você deve se conectar usando Active Directory e não SQL Server autenticação. 
    
```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```                   
Observe que o *< share_acc_name >* é o nome da sua conta de compartilhamento de dados. Se você ainda não criou uma conta de compartilhamento de dados, poderá voltar para esse pré-requisito posteriormente.  

* IP do cliente SQL Server acesso ao firewall: isso pode ser feito por meio das seguintes etapas: 1. Navegue até *firewalls e redes virtuais* 1. Clique no **botão de alternância para** permitir o acesso aos serviços do Azure. 

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Iniciar sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Criar uma conta de compartilhamento de dados

Crie um recurso de compartilhamento de dados do Azure em um grupo de recursos do Azure.

1. Selecione o botão (+) **Criar um recurso**, no canto superior esquerdo do portal.

1. Pesquise por *compartilhamento de dados*.

1. Selecione compartilhamento de dados e selecione **criar**.

1. Preencha os detalhes básicos do recurso de compartilhamento de dados do Azure com as informações a seguir. 

     **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Nome | *datashareacount* | Especifique um nome para sua conta de compartilhamento de dados. |
    | Subscrição | A sua subscrição | Selecione a assinatura do Azure que você deseja usar para sua conta de compartilhamento de dados.|
    | Grupo de recursos | *test-resource-group* | Use um grupo de recursos existente ou crie um novo grupo de recursos. |
    | Localização | *Leste dos EUA 2* | Selecione uma região para sua conta de compartilhamento de dados.
    | | |

1. Selecione **criar** para provisionar sua conta de compartilhamento de dados. O provisionamento de uma nova conta de compartilhamento de dados normalmente leva cerca de 2 minutos ou menos. 

1. Quando a implantação for concluída, selecione **ir para o recurso**.

## <a name="create-a-data-share"></a>Criar um compartilhamento de dados

1. Navegue até a página Visão geral do compartilhamento de dados.

    ![Compartilhar seus dados](./media/share-receive-data.png "Partilhar os seus dados") 

1. Selecione **começar a compartilhar seus dados**.

1. Selecione **Criar**.   

1. Preencha os detalhes do seu compartilhamento de dados. Especifique um nome, uma descrição do conteúdo do compartilhamento e os termos de uso (opcional). 

    ![EnterShareDetails](./media/enter-share-details.png "Inserir detalhes do compartilhamento") 

1. Selecione **continuar**

1. Para adicionar conjuntos de dados ao seu compartilhamento de dado, selecione **Add DataSets**. 

    ![Conjuntos de dados](./media/datasets.png "Conjuntos de dados")

1. Selecione o tipo de conjunto de texto que você deseja adicionar. 

    ![Adddatasets](./media/add-datasets.png "Adicionar conjuntos de os")    

1. Navegue até o objeto que você deseja compartilhar e selecione ' Adicionar conjuntos de os '. 

    ![SelectDatasets](./media/select-datasets.png "Selecionar conjuntos de os")    

1. Na guia destinatários, insira os endereços de email do consumidor de dados selecionando ' + Adicionar destinatário '. 

    ![Hiperdestinatários](./media/add-recipient.png "Adicionar destinatários") 

1. Selecione **continuar**

1. Se você quiser que seu consumidor de dados possa obter atualizações incrementais de seus dados, habilite o agendamento do instantâneo. 

    ![EnableSnapshots](./media/enable-snapshots.png "Habilitar instantâneos") 

1. Selecione uma hora de início e um intervalo de recorrência. 

1. Selecione **continuar**

1. Na guia revisar + criar, examine o conteúdo do pacote, as configurações, os destinatários e as configurações de sincronização. Selecione **Criar**

Seu compartilhamento de dados do Azure foi criado e o destinatário do seu compartilhamento de dados agora está pronto para aceitar seu convite. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprende como criar um compartilhamento de dados do Azure e convidar destinatários. Para saber mais sobre como um consumidor de dados pode aceitar e receber um compartilhamento de dados, continue no tutorial [aceitar e receber dados](subscribe-to-data-share.md) . 
