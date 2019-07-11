---
title: Tutorial - aceitar e receber dados através da pré-visualização do compartilhamento de dados do Azure
description: Tutorial - aceitar e receber dados através da pré-visualização do compartilhamento de dados do Azure
author: joannapea
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 2dc4994d88fc03c23a6d5722d6018c926e7d6b8c
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67788165"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share-preview"></a>Tutorial: Aceitar e receber dados através da pré-visualização do compartilhamento de dados do Azure

Neste tutorial, aprenderá como aceitar um convite de partilha de dados a utilizar a pré-visualização de partilhar dados do Azure. Aprenderá como receber dados a ser partilhados consigo, bem como ativar um intervalo de atualização regular garantir que tenha sempre o instantâneo mais recente dos dados que está a ser partilhados consigo. 

> [!div class="checklist"]
> * Como aceitar um convite de pré-visualização de partilha de dados do Azure
> * Criar uma conta de pré-visualização de partilha de dados do Azure
> * Especifique um destino para os seus dados
> * Criar uma subscrição para a partilha de dados para a atualização agendada

## <a name="prerequisites"></a>Pré-requisitos
Antes de poder aceitar um convite de partilha de dados, terá de aprovisionar um número de recursos do Azure, que estão listados abaixo. 

Certifique-se de que todos os pré-requisitos estão concluídos antes de aceitar um convite de partilha de dados. 

* Subscrição do Azure: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma conta de armazenamento do Azure: Se ainda não tiver uma, pode criar uma [conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Um convite de partilha de dados: Um convite do Microsoft Azure com um requerente intitulado "convite de partilha de dados do Azure partir **<yourdataprovider@domain.com>** ".

> [!IMPORTANT]
> Para aceitar e receber uma partilha de dados do Azure, primeiro tem de registar o fornecedor de recursos de Microsoft.DataShare e tem de ser um proprietário da conta de armazenamento que aceitar dados. Siga as instruções documentadas na [resolver problemas de pré-visualização do Azure dados partilhar](data-share-troubleshoot.md) para registar o fornecedor de recursos de partilha de dados, bem como adicionar a próprio como proprietário da conta de armazenamento. 

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="open-invitation"></a>Convite aberto

Verifique a sua caixa de entrada para um convite através do seu fornecedor de dados. O convite é do Microsoft Azure, intitulada **convite de partilha de dados do Azure partir <yourdataprovider@domain.com>** . Anote o nome da partilha para garantir que está a aceitar a partilha correta se existirem vários convites. 

Selecione no **ver o convite** para ver o seu convite no Azure. Isto leva-o à sua exibição de partilhas recebido.

![Convites](./media/invitations.png "lista de convites") 

Selecione a partilha que pretende ver. 

## <a name="accept-invitation"></a>Aceitar convite
Certifique-se de que todos os campos são revisados, incluindo o **termos de utilização**. Se concordar com os termos de utilização, precisará selecionar a caixa para indicar a que concorda. 

![Termos de utilização](./media/terms-of-use.png "termos de utilização") 

Sob *conta de partilha de dados de destino*, selecione a subscrição e grupo de recursos que esteja implantando a partilha de dados em. 

Para o **conta de partilha de dados** campo, selecione **criar nova** se não tiver uma conta de partilha de dados existente. Caso contrário, selecione uma conta de partilha de dados existente que gostaria de aceitar a partilha de dados em. 

Para o *nome da partilha recebido* campo, pode deixar a predefinição especificada por dados, fornecer ou especificar um novo nome para a partilha recebido. 

![Conta de partilha de dados de destino](./media/target-data-share.png "conta de partilhar dados de destino") 

Depois de aceitou os termos de utilização e especificar uma localização para a sua partilha, selecione no *Accept e configurar*. Se escolher esta opção, será criada uma subscrição de partilha e o ecrã seguinte irá pedir-lhe para selecionar uma conta de armazenamento de destino para os seus dados a ser copiado para. 

![Aceitar as opções](./media/accept-options.png "aceitar opções") 

Se preferir, mas aceitar o convite agora de configurar o armazenamento num momento posterior, selecione *aceite e configurar mais tarde*. Esta opção permite-lhe configurar a sua conta de armazenamento de destino mais tarde. Para continuar a configuração do armazenamento mais tarde, veja [como configurar a sua conta de armazenamento](how-to-configure-mapping.md) página para obter passos detalhados sobre como retomar a seus dados partilham a configuração. 

Se não pretender aceitar o convite, selecione *rejeitar*. 

## <a name="configure-storage"></a>Configurar o armazenamento
Sob *definições de armazenamento de destino*, selecione a subscrição, grupo de recursos e a conta de armazenamento que gostaria de recebem os dados para. 

![As definições de armazenamento de destino](./media/target-storage-settings.png "armazenamento de destino") 

Para receber atualizações regulares de seus dados, certifique-se de que ativar as definições de instantâneos. Tenha em atenção que apenas será apresentada uma agenda de definição do instantâneo se o seu fornecedor de dados está incluído na partilha de dados. 

![Definições de instantâneo](./media/snapshot-settings.png "as definições de instantâneo") 

Selecione *Guardar*. 

## <a name="trigger-a-snapshot"></a>Acionar um instantâneo

Pode acionar um instantâneo nos compartilhamentos recebido -> separador de detalhes, selecionando **instantâneo de Acionador**. Aqui, pode disparar um instantâneo completo ou incremental dos seus dados. Se for a primeira vez que receber dados do seu fornecedor de dados, selecione cópia completa. 

![Instantâneo do acionador](./media/trigger-snapshot.png "instantâneo de Acionador") 

Quando o último Estado de execução estiver *bem-sucedida*, abra a conta de armazenamento para ver os dados recebidos. 

Para verificar que conta de armazenamento utilizado, selecione ativado **conjuntos de dados**. 

![Conjuntos de dados do consumidor](./media/consumer-datasets.png "mapeamento do conjunto de dados de consumidor") 

## <a name="view-history"></a>Ver histórico
Para ver um histórico de sua instantâneos, navegue para partilhas de recebido -> histórico. Aqui encontrará um histórico de todos os instantâneos que foram gerados durante os últimos 60 dias. 

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, com aprendizagem como aceitar e receber uma partilha de dados do Azure. Para saber mais sobre os conceitos de partilha de dados do Azure, avance para [conceitos: Dados do Azure partilham a terminologia](terminology.md).