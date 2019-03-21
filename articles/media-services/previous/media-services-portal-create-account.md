---
title: Criar uma conta dos Serviços de Multimédia do Azure no portal do Azure | Microsoft Docs
description: Este tutorial orienta-o ao longo dos passos para criar uma conta dos Serviços de Multimédia do Azure no portal do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 3e754d81a18b683e601608b856bbc9da1b790dbd
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259385"
---
# <a name="create-a-media-services-account-using-the-azure-portal"></a>Criar uma conta de serviços de multimédia com o portal do Azure

O portal do Azure proporciona uma forma para criar rapidamente uma conta dos Serviços de Multimédia do Azure (AMS). Pode utilizar a sua conta para aceder aos Media Services que permitem armazenar, encriptar, codificar, gerir e transmitir conteúdo no Azure. No momento em que cria uma conta de Media Services, também criar uma conta do storage associada (ou utilize um já existente). Se eliminar uma conta de Media Services, os blobs na sua conta do Storage relacionada não são eliminados.

Pode ter Fins Gerais v1 ou Fins Gerais v2 como a sua conta de armazenamento primária. Atualmente, o portal do Azure permite escolher apenas o v1, mas pode adicionar o v2, ao criar a conta com a API ou o Powershell. Para obter mais informações sobre as contas de armazenamento, veja [Acerca das contas de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

A conta de Media Services e todas as contas de armazenamento associado tem de ser na mesma subscrição do Azure. É vivamente recomendado para utilizar contas de armazenamento na mesma localização que a conta de serviços de multimédia para evitar custos de saída de latência e dados adicionais.

Este artigo mostra como criar uma conta de serviços de multimédia no portal do Azure.

> [!NOTE]
> Para obter informações sobre a disponibilidade das funcionalidades dos Serviços de Multimédia do Azure em regiões diferentes, veja [disponibilidade das funcionalidades do AMS em datacenters](scenarios-and-availability.md#availability).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma conta do Azure. Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="create-an-ams-account"></a>Criar uma conta do AMS

Os passos nesta secção explicam como criar uma conta dos AMS.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **+Novo** > **Web + Móvel** > **Serviços de Multimédia**.
   
    ![Criar Media Services](./media/media-services-create-account/media-services-new1.png)
3. Em **CRIAR CONTA DE MEDIA SERVICES**, Introduza os valores necessários.
   
    ![Criar Media Services](./media/media-services-create-account/media-services-new3.png)
   
   1. Em **Nome da Conta**, introduza o nome da nova conta de AMS. Um nome de conta dos Serviços de Multimédia é composto por letras minúsculas ou números sem espaços, com 3 a 24 carateres de comprimento.
   2. Na subscrição, selecione entre as diferentes subscrições do Azure disponíveis para si.
   3. Em **Grupo de Recursos**, selecione o recurso novo ou existente.  Um grupo de recursos é uma coleção de recursos que partilham o ciclo de vida, as permissões e as políticas. Sabia mais [aqui](../../azure-resource-manager/resource-group-overview.md#resource-groups).
   4. Em **Localização**, selecione a região geográfica que será utilizada para armazenar os registos de metadados da conta de Media Services. Esta região será utilizada para processar e transmitir em fluxo a sua multimédia. Apenas as regiões dos Media Services disponíveis são apresentadas na caixa de lista pendente. 
   5. Em **Conta do Storage**, selecione uma conta do Storage para fornecer o Blob Storage do conteúdo de multimédia da conta de Media Services. Pode selecionar uma conta de armazenamento existente na mesma região geográfica da conta dos Serviços de Multimédia ou pode criar uma conta de armazenamento. É criada uma nova conta do Storage na mesma região. As regras para os nomes da conta do Storage são iguais às das contas dos Media Services.
      
       Saiba mais sobre armazenamento [aqui](../../storage/common/storage-introduction.md).
   6. Selecione **Afixar no dashboard** para ver o progresso da implementação da conta.
4. Clique em **Criar** na parte inferior do formulário.
   
    Depois de criar a conta com êxito, a página de descrição geral é carregada. Na tabela do ponto final de transmissão em fluxo, a conta terá um ponto final de transmissão em fluxo predefinido no estado **Parado**. 

    >[!NOTE]
    >Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 
   
## <a name="to-manage-your-ams-account"></a>Para gerir a sua conta do AMS

Para gerir a sua conta do AMS (por exemplo, ligar à API AMS através de programação, carregar vídeos, codificar elementos, configurar a proteção de conteúdos, monitorizar o progresso da tarefa) selecione **Definições** no lado esquerdo do portal. Partir do **definições**, navegue até um dos painéis disponíveis (por exemplo: **Acesso à API**, **ativos**, **tarefas**, **proteção de conteúdo**).


## <a name="next-steps"></a>Passos Seguintes

Agora, pode carregar ficheiros para a sua conta do AMS. Para obter mais informações, veja [Carregar ficheiros](media-services-portal-upload-files.md).

Se pretender aceder à API AMS através de programação, veja [Aceder à API dos Serviços de Multimédia do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

