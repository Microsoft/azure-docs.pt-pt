---
title: Configurar o postmaster para chamadas de API REST de Serviços de Mídia do Azure
description: Este artigo descreve como configurar o postmaster para chamadas à API REST dos serviços de mídia.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: f55b3d5767f38c8fba2e79925f6f6e1e390aad2d
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74887798"
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>Configurar o postmaster para chamadas da API REST dos serviços de mídia  

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Veja a versão mais recente, [Serviços de Multimédia v3](https://docs.microsoft.com/azure/media-services/latest/). Além disso, consulte [diretrizes de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)

Este tutorial mostra como configurar o **postmaster** para que ele possa ser usado para chamar as APIs REST dos serviços de mídia do Azure (AMS). O tutorial mostra como importar arquivos de ambiente e de coleção para o **postmaster**. A coleção contém definições agrupadas de solicitações HTTP que chamam as APIs REST dos serviços de mídia do Azure (AMS). O ficheiro de ambiente contém variáveis que são utilizadas pela coleção.

Esse ambiente e a coleção são usados em artigos que mostram como obter várias tarefas com as APIs REST dos serviços de mídia do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Instale o cliente REST do [Postman](https://www.getpostman.com/) para executar as API REST mostradas em alguns dos tutoriais sobre AMS REST. 

    Estamos a utilizar o **Postman**, mas qualquer ferramenta REST seria adequada. Outras alternativas são: **Visual Studio Code** com o plug-in REST ou **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>Configurar o ambiente 

1. Crie um arquivo. JSON que contenha as variáveis de ambiente usadas nos tutoriais do AMS. Nomeie o arquivo (por exemplo, **AzureMediaServices. postman_environment. JSON**). Abra o arquivo e cole o código que define o ambiente do postmaster [nesta listagem de código](postman-environment.md). 
2. Abra o **Postman**.
3. À direita do ecrã, selecione a opção **Gerir ambiente**.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-create-env.png)
4. Na caixa de diálogo **Gerir ambiente**, clique em **Importar**.
5. Procure e selecione o arquivo **AzureMediaServices. postman_environment. JSON** .
6. O ambiente **AzureMedia** é adicionado.
7. Feche a caixa de diálogo.
8. Selecione o ambiente **AzureMedia** .

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Configurar a coleção

1. Crie um arquivo. JSON que contenha a coleção de **postmaster** com todas as operações necessárias para carregar um arquivo nos serviços de mídia. Nomeie o arquivo (por exemplo, **AzureMediaServicesOperations. postman_collection. JSON**). Abra o arquivo e cole o código que define a coleção de **postmaster** da [listagem de código](postman-collection.md).
2. Clique em **Importar** para importar o ficheiro de coleção.
3. Escolha o arquivo **AzureMediaServicesOperations. postman_collection. JSON** .

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Passos seguintes

Confira o artigo [carregar ativos](media-services-rest-upload-files.md) .  
