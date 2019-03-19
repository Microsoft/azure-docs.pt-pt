---
title: Carregar ficheiros para uma conta dos Serviços de Multimédia do Azure com Aspera | Microsoft Docs
description: Este tutorial orienta-o ao longo dos passos para carregar ficheiros para uma conta de armazenamento que está associada a uma conta dos Serviços de Multimédia com o serviço **Aspera Server On Demand** no Azure.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.assetid: 8812623a-b425-4a0f-9e05-0ee6c839b6f9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: 28b6732745947043847ce944243bc87398e159fc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57892152"
---
# <a name="upload-files-into-a-media-services-account-using-the-aspera-server-on-demand-service-on-azure"></a>Carregar ficheiros para uma conta dos Serviços de Multimédia com o serviço Aspera Server On Demand no Azure 

## <a name="overview"></a>Descrição geral

O **Aspera** é um software de transferência de ficheiros de alta velocidade. O **Aspera Server On Demand** para o Azure permite o carregamento e a transferência em alta velocidade de ficheiros grandes diretamente para o armazenamento de objetos BLOB do Azure. Para obter informações sobre o **Aspera Server On Demand**, veja o site [Aspera Cloud](http://cloud.asperasoft.com/). 
  
O **Aspera Server On Demand** par o Azure está disponível para compra no [Azure Marketplace](https://azure.microsoft.com/marketplace/). Para concluir a compra do **Aspera Server On Demand** para o Azure, inicie sessão no Azure Marketplace com o seu Windows Live ID.

Este tutorial orienta-o ao longo dos passos para carregar ficheiros para uma conta de armazenamento que está associada a uma conta dos Serviços de Multimédia com o serviço **Aspera Server On Demand** no Azure. 

Pode encontrar um exemplo que mostra como utilizar funções de Azure com o Aspera e os Serviços de Multimédia [aqui](https://github.com/Azure-Samples/media-services-dotnet-functions-integration/tree/master/103-aspera-ingest).

>[!NOTE]
>Existe um limite para o tamanho máximo dos ficheiros suportado para processamento nos processadores de multimédia (MPs) dos Serviços de Multimédia do Azure. Veja [este](media-services-quotas-and-limitations.md) artigo para obter detalhes sobre as limitações relativas aos tamanhos de ficheiros.
>

## <a name="prerequisites"></a>Pré-requisitos 

Para concluir este tutorial, precisa de:

* Um Windows Live ID
* Uma [conta do Azure](https://azure.microsoft.com). Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
* Uma [conta dos Serviços de Multimédia do Azure](media-services-portal-create-account.md).

## <a name="purchase-aspera-on-demand-for-azure"></a>Comprar o Aspera On Demand para o Azure

Depois de iniciar sessão no Azure Marketplace, siga estes passos básicos para concluir a compra do Aspera On Demand para o Azure.

1. Procure Aspera e selecione “Server On Demand”.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera001.png)

2. Reveja os planos de subscrição e clique em “Inscrever-se”

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera002.png)

3. Preencha as especificações da sua subscrição do Server on-Demand.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera003.png)

4. Clique em **Escalão de Preço** e selecione o volume mensal pretendido no subpainel. No painel **Detalhes do plano**, selecione **OK**. Em seguida, no painel **Escolher o Escalão de Preço**, clique em **Selecionar**.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera004.png)

5. Clique em **Termos legais** para ver e aceitar os termos legais no subpainel. Depois de rever os termos legais, clique em **Comprar**.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera005.png)

6. Clique em **Criar** para concluir a compra.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera006.png)

7. O dashboard do Azure anunciará que está a aprovisionar o serviço.  Depois de concluído o aprovisionamento, pode procurar pelo nome do serviço nos seus recursos para encontrar a subscrição nova. Quando encontrar o serviço, faça duplo clique no mesmo para iniciar o portal de gestão do serviço.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera007.png)

8. Inicie o portal de gestão do Aspera. Depois de encontrar o serviço Aspera novo, pode clicar no mesmo para obter acesso ao portal de gestão.  Será iniciado um painel novo. Nesse painel, tem de clicar no **Nome do Recurso** do serviço novo.  Na captura de ecrã seguinte, o nome do recurso é “AsperaTransferDemo”. Depois de clicar no nome do recurso, é iniciado outro painel. Neste painel, verá uma ligação que diz “Manage” (Gerir). Clique na ligação “Manage” para iniciar o portal de gestão do Aspera.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera008.png)

9. Ao clicar na ligação “Manage”, é encaminhado para a página de registo, que é obrigatório para aceder ao serviço.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera009.png)

10. Nesta fase, deverá ter acesso ao portal de gestão do serviço Aspera, onde pode criar chaves de acesso, transferir clientes e licenças da Aspera, ver a utilização e saber mais sobre as APIs.

    A captura de ecrã seguinte mostra a criação do acesso. 

    ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera010.png)

    A captura de ecrã seguinte mostra as interfaces de relatórios de utilização no portal. 

    ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera011.png)

## <a name="upload-files-with-aspera"></a>Carregar ficheiros com o Aspera

1. Transferir e instalar o software de cliente Aspera:
    
    * [Plug-in do browser](https://downloads.asperasoft.com/connect2/)
    * [Cliente avançado](https://downloads.asperasoft.com/en/downloads/2)

2. Faça a sua primeira transferência. Para poder utilizar o cliente Aspera para transferência com o serviço de transferência Aspera, tem de concluir o seguinte: 

   1. Crie uma chave de acesso no portal do Aspera.  
   2. Transfira, instale e licencie o cliente Aspera (o software está disponível no portal do Aspera).  

      >[!NOTE]
      >Leia o manual do cliente Aspera para obter informações de configuração.
    
   3. Obtenha algumas informações da sua conta de armazenamento que está associada à sua conta de Multimédia do Azure com o [portal do Azure](https://portal.azure.com/). Mais concretamente, o nome e a chave e o nome do contentor de blobs de armazenamento no qual quer colocar o seu conteúdo. 

       * Para obter as informações do armazenamento a partir do portal: localize a conta de armazenamento, clique nas Chaves de acesso e copie o nome e a chave da conta.
       * Para obter o nome do contentor: localize a conta de armazenamento, selecione **Blobs** e selecione o nome do contentor para o qual quer carregar o conteúdo. 

      Abaixo, pode ver a captura de ecrã do **Connection Manager (Gestor de Ligação)** do cliente Aspera, onde tem de especificar o tipo de armazenamento “Azure” e as credenciais, bem como o contentor de blobs.

      ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera012.png)

## <a name="resources"></a>Recursos

Os seguintes recursos foram mencionados neste artigo. 

* [Plug-in de Ligação do Browser](https://downloads.asperasoft.com/connect2/)
* [Guia de Ligação](https://downloads.asperasoft.com/en/documentation/8)
* [Cliente Aspera](https://downloads.asperasoft.com/en/downloads/2)
* [Guia de Cliente](https://downloads.asperasoft.com/en/documentation/2)

## <a name="next-steps"></a>Passos Seguintes

Agora, pode [copiar blobs a partir de uma conta de armazenamento para uma conta do AMS](media-services-copying-existing-blob.md#copy-blobs-from-a-storage-account-into-an-ams-account).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

