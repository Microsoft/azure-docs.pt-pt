---
title: 'Tutorial: aceitar & receber dados-compartilhamento de dados do Azure'
description: Tutorial – aceitar e receber dados usando o compartilhamento de dados do Azure
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 9c24f54fe846459187488b0a65b2582914e25e2a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499338"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Tutorial: aceitar e receber dados usando o compartilhamento de dados do Azure  

Neste tutorial, você aprenderá a aceitar um convite de compartilhamento de dados usando o compartilhamento de dados do Azure. Você aprenderá a receber dados que estão sendo compartilhados com você, bem como a habilitar um intervalo de atualização regular para garantir que você sempre tenha o instantâneo mais recente dos dados que estão sendo compartilhados com você. 

> [!div class="checklist"]
> * Como aceitar um convite de compartilhamento de dados do Azure
> * Criar uma conta do compartilhamento de dados do Azure
> * Especifique um destino para seus dados
> * Criar uma assinatura para seu compartilhamento de dados para a atualização agendada

## <a name="prerequisites"></a>Pré-requisitos
Antes de aceitar um convite de compartilhamento de dados, você deve provisionar vários recursos do Azure, que são listados abaixo. 

Verifique se todos os pré-requisitos estão completos antes de aceitar um convite de compartilhamento de dados. 

* Assinatura do Azure: se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Um convite de compartilhamento de dados: um convite de Microsoft Azure com uma entidade intitulada "convite de compartilhamento de dados do Azure da **<yourdataprovider@domain.com>** ".

### <a name="receive-data-into-a-storage-account"></a>Receber dados em uma conta de armazenamento: 

* Uma conta de armazenamento do Azure: se você ainda não tiver uma, poderá criar uma [conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Permissão para adicionar a atribuição de função à conta de armazenamento, que está presente na permissão *Microsoft. Authorization/role atribuitions/Write* . Essa permissão existe na função proprietário. 
* Registro do provedor de recursos para Microsoft. DataShare. Consulte a documentação dos [provedores de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) para obter informações sobre como fazer isso. 

> [!IMPORTANT]
> Para aceitar e receber um compartilhamento de dados do Azure, primeiro você deve registrar o provedor de recursos Microsoft. DataShare e deve ser um proprietário da conta de armazenamento na qual você aceita dados. Siga as instruções documentadas em [solucionar problemas do compartilhamento de dados do Azure](data-share-troubleshoot.md) para registrar o provedor de recursos de compartilhamento de dados, bem como adicionar a si mesmo como um proprietário da conta de armazenamento. 

### <a name="receive-data-into-a-sql-based-source"></a>Receber dados em uma fonte baseada em SQL:

* Permissão para o data share MSI acessar o banco de dados SQL do Azure ou SQL Data Warehouse do Azure. Isso pode ser feito por meio das seguintes etapas: 
    1. Defina-se como o administrador do Azure Active Directory para o servidor.
    1. Conecte-se ao banco de dados SQL do Azure/data warehouse usando Azure Active Directory.
    1. Use o editor de consultas (versão prévia) para executar o script a seguir para adicionar o MSI de compartilhamento de dados como um db_owner. Você deve se conectar usando Active Directory e não SQL Server autenticação. 

```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```      
Observe que o *< share_acc_name >* é o nome da sua conta de compartilhamento de dados. Se você ainda não criou uma conta de compartilhamento de dados, poderá voltar para esse pré-requisito posteriormente.         

* IP do cliente SQL Server acesso ao firewall: isso pode ser feito por meio das seguintes etapas: 1. Navegue até *firewalls e redes virtuais* 1. Clique no **botão de alternância para** permitir o acesso aos serviços do Azure. 

Depois que esses pré-requisitos forem concluídos, você estará pronto para receber dados em seu SQL Server.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Iniciar sessão no [portal do Azure](https://portal.azure.com/).

## <a name="open-invitation"></a>Abrir convite

Verifique sua caixa de entrada para obter um convite de seu provedor de dados. O convite é de Microsoft Azure, intitulado **convite de compartilhamento de dados do Azure de <yourdataprovider@domain.com>** . Anote o nome do compartilhamento para garantir que você está aceitando o compartilhamento correto se houver vários convites. 

Selecione **Exibir convite** para ver seu convite no Azure. Isso levará você para a exibição de compartilhamentos recebidos.

![Festas](./media/invitations.png "Lista de convites") 

Selecione o compartilhamento que você deseja exibir. 

## <a name="accept-invitation"></a>Aceitar convite
Verifique se todos os campos foram revisados, incluindo os **termos de uso**. Se você concordar com os termos de uso, será necessário marcar a caixa para indicar que concorda. 

![Termos de utilização](./media/terms-of-use.png "Termos de utilização") 

Em *conta de compartilhamento de dados de destino*, selecione a assinatura e o grupo de recursos em que você implantará seu compartilhamento de dados. 

Para o campo **conta de compartilhamento de dados** , selecione **criar novo** se você não tiver uma conta de compartilhamento de dados existente. Caso contrário, selecione uma conta de compartilhamento de dados existente para a qual você gostaria de aceitar seu compartilhamento de dados. 

Para o campo *nome de compartilhamento recebido* , você pode deixar o padrão especificado pelos dados fornecidos ou especificar um novo nome para o compartilhamento recebido. 

![Conta de compartilhamento de dados de destino](./media/target-data-share.png "Conta de compartilhamento de dados de destino") 

Depois de concordar com os termos de uso e especificar um local para seu compartilhamento, selecione em *aceitar e configurar*. Se você escolher essa opção, uma assinatura de compartilhamento será criada e a próxima tela solicitará que você selecione uma conta de armazenamento de destino para os dados a serem copiados. 

![Opções de aceitação](./media/accept-options.png "Opções de aceitação") 

Se você preferir aceitar o convite agora, mas configurar seu armazenamento mais tarde, selecione *aceitar e configurar mais tarde*. Essa opção permite que você configure sua conta de armazenamento de destino mais tarde. Para continuar a configurar o armazenamento mais tarde, consulte a página [como configurar sua conta de armazenamento](how-to-configure-mapping.md) para obter etapas detalhadas sobre como retomar sua configuração de compartilhamento de dados. 

Se você não quiser aceitar o convite, selecione *rejeitar*. 

## <a name="configure-storage"></a>Configurar o armazenamento
Em *configurações de armazenamento de destino*, selecione a assinatura, o grupo de recursos e a conta de armazenamento na qual você gostaria de receber os dados. 

![Configurações de armazenamento de destino](./media/target-storage-settings.png "Armazenamento de destino") 

Para receber atualizações regulares de seus dados, certifique-se de habilitar as configurações de instantâneo. Observe que você verá apenas um agendamento de configuração de instantâneo se o provedor de dados o tiver incluído no compartilhamento de dados. 

![Configurações de instantâneo](./media/snapshot-settings.png "Configurações de instantâneo") 

Selecione *Guardar*. 

> [!IMPORTANT]
> Se você estiver recebendo dados baseados em SQL e quiser receber esses dados em uma fonte baseada em SQL, visite nosso guia de instruções para [configurar um mapeamento de conjunto](how-to-configure-mapping.md) para aprender a configurar um SQL Server como o destino do conjunto de dados. 

## <a name="trigger-a-snapshot"></a>Disparar um instantâneo

Você pode disparar um instantâneo na guia compartilhamentos recebidos-> detalhes selecionando **instantâneo de gatilho**. Aqui, você pode disparar um instantâneo completo ou incremental de seus dados. Se for a primeira vez que você recebe dados do seu provedor de dados, selecione cópia completa. 

![Disparar instantâneo](./media/trigger-snapshot.png "Disparar instantâneo") 

Quando o status da última execução for *bem-sucedido*, abra a conta de armazenamento para exibir os dados recebidos. 

Para verificar qual conta de armazenamento você usou, selecione em **conjuntos de valores**. 

![Conjuntos de clientes do consumidor](./media/consumer-datasets.png "Mapeamento de conjunto de clientes do consumidor") 

## <a name="view-history"></a>Ver histórico
Para exibir um histórico de seus instantâneos, navegue até compartilhamentos recebidos-histórico de >. Aqui você encontrará um histórico de todos os instantâneos que foram gerados nos últimos 60 dias. 

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, você aprenderá a aceitar e receber um compartilhamento de dados do Azure. Para saber mais sobre os conceitos de compartilhamento de dados do Azure, continue com os [conceitos: terminologia do compartilhamento de dados do Azure](terminology.md).