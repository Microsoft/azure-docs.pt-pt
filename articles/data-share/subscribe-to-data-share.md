---
title: 'Tutorial: Aceitar & receber dados-visualização do compartilhamento de dados do Azure'
description: Tutorial – aceitar e receber dados usando a visualização do compartilhamento de dados do Azure
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 235ef25b2d655c4388dee5bdcf88d179f3373697
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327402"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share-preview"></a>Tutorial: Aceitar e receber dados usando a visualização do compartilhamento de dados do Azure

Neste tutorial, você aprenderá a aceitar um convite de compartilhamento de dados usando a visualização do compartilhamento de dados do Azure. Você aprenderá a receber dados que estão sendo compartilhados com você, bem como a habilitar um intervalo de atualização regular para garantir que você sempre tenha o instantâneo mais recente dos dados que estão sendo compartilhados com você. 

> [!div class="checklist"]
> * Como aceitar um convite de visualização do compartilhamento de dados do Azure
> * Criar uma conta de visualização do compartilhamento de dados do Azure
> * Especifique um destino para seus dados
> * Criar uma assinatura para seu compartilhamento de dados para a atualização agendada

## <a name="prerequisites"></a>Pré-requisitos
Antes de aceitar um convite de compartilhamento de dados, você deve provisionar vários recursos do Azure, que são listados abaixo. 

Verifique se todos os pré-requisitos estão completos antes de aceitar um convite de compartilhamento de dados. 

* Assinatura do Azure: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma conta de armazenamento do Azure: Se você ainda não tiver uma, poderá criar uma [conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Um convite de compartilhamento de dados: Um convite de Microsoft Azure com um assunto intitulado "convite de compartilhamento de dados do Azure de **<yourdataprovider@domain.com>** ".
* Permissão para adicionar a atribuição de função à conta de armazenamento, que está presente na permissão *Microsoft. Authorization/role atribuitions/Write* . Essa permissão existe na função proprietário. 
* Registro do provedor de recursos para Microsoft. DataShare. Consulte a documentação dos [provedores de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) para obter informações sobre como fazer isso. 

> [!IMPORTANT]
> Para aceitar e receber um compartilhamento de dados do Azure, primeiro você deve registrar o provedor de recursos Microsoft. DataShare e deve ser um proprietário da conta de armazenamento na qual você aceita dados. Siga as instruções documentadas em [solucionar problemas de visualização do compartilhamento de dados do Azure](data-share-troubleshoot.md) para registrar o provedor de recursos de compartilhamento de dados, além de adicionar como proprietário da conta de armazenamento. 

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="open-invitation"></a>Abrir convite

Verifique sua caixa de entrada para obter um convite de seu provedor de dados. O convite é de Microsoft Azure, intitulado **convite de compartilhamento de dados do Azure de <yourdataprovider@domain.com>** . Anote o nome do compartilhamento para garantir que você está aceitando o compartilhamento correto se houver vários convites. 

Selecione **Exibir convite** para ver seu convite no Azure. Isso levará você para a exibição de compartilhamentos recebidos.

![](./media/invitations.png "Lista de convites de convites") 

Selecione o compartilhamento que você deseja exibir. 

## <a name="accept-invitation"></a>Aceitar convite
Verifique se todos os campos foram revisados, incluindo os **termos de uso**. Se você concordar com os termos de uso, será necessário marcar a caixa para indicar que concorda. 

![Termos de uso](./media/terms-of-use.png "termos de uso") 

Em *conta de compartilhamento de dados de destino*, selecione a assinatura e o grupo de recursos em que você implantará seu compartilhamento de dados. 

Para o campo **conta de compartilhamento de dados** , selecione **criar novo** se você não tiver uma conta de compartilhamento de dados existente. Caso contrário, selecione uma conta de compartilhamento de dados existente para a qual você gostaria de aceitar seu compartilhamento de dados. 

Para o campo *nome de compartilhamento recebido* , você pode deixar o padrão especificado pelos dados fornecidos ou especificar um novo nome para o compartilhamento recebido. 

(./media/target-data-share.png "Conta de compartilhamento de dados de destino") conta de compartilhamento de dados de ![destino] 

Depois de concordar com os termos de uso e especificar um local para seu compartilhamento, selecione em *aceitar e configurar*. Se você escolher essa opção, uma assinatura de compartilhamento será criada e a próxima tela solicitará que você selecione uma conta de armazenamento de destino para os dados a serem copiados. 

Opções de ![aceitar opções](./media/accept-options.png "aceitas") 

Se você preferir aceitar o convite agora, mas configurar seu armazenamento mais tarde, selecione *aceitar e configurar mais tarde*. Essa opção permite que você configure sua conta de armazenamento de destino mais tarde. Para continuar a configurar o armazenamento mais tarde, consulte a página [como configurar sua conta de armazenamento](how-to-configure-mapping.md) para obter etapas detalhadas sobre como retomar sua configuração de compartilhamento de dados. 

Se você não quiser aceitar o convite, selecione *rejeitar*. 

## <a name="configure-storage"></a>Configurar o armazenamento
Em *configurações de armazenamento de destino*, selecione a assinatura, o grupo de recursos e a conta de armazenamento na qual você gostaria de receber os dados. 

(./media/target-storage-settings.png "Armazenamento") de destino ![das configurações de armazenamento de destino] 

Para receber atualizações regulares de seus dados, certifique-se de habilitar as configurações de instantâneo. Observe que você verá apenas um agendamento de configuração de instantâneo se o provedor de dados o tiver incluído no compartilhamento de dados. 

Configurações ![de instantâneo de]configurações de(./media/snapshot-settings.png "instantâneo") 

Selecione *Guardar*. 

## <a name="trigger-a-snapshot"></a>Disparar um instantâneo

Você pode disparar um instantâneo na guia compartilhamentos recebidos-> detalhes selecionando **instantâneo de gatilho**. Aqui, você pode disparar um instantâneo completo ou incremental de seus dados. Se for a primeira vez que você recebe dados do seu provedor de dados, selecione cópia completa. 

![Disparar](./media/trigger-snapshot.png "instantâneo de gatilho") de instantâneo 

Quando o status da última execução for *bem-sucedido*, abra a conta de armazenamento para exibir os dados recebidos. 

Para verificar qual conta de armazenamento você usou, selecione em **conjuntos de valores**. 

(./media/consumer-datasets.png "Mapeamento de conjunto de clientes do consumidor") de conjuntos de ![clientes] 

## <a name="view-history"></a>Ver histórico
Para exibir um histórico de seus instantâneos, navegue até compartilhamentos recebidos-histórico de >. Aqui você encontrará um histórico de todos os instantâneos que foram gerados nos últimos 60 dias. 

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, você aprenderá a aceitar e receber um compartilhamento de dados do Azure. Para saber mais sobre os conceitos de compartilhamento de dados do Azure, continue em [Concepts: Terminologia do compartilhamento de dados do Azure @ no__t-0.