---
title: Configurar o Postman para chamadas de API de REST de serviços de multimédia do Azure
description: Saiba como configurar o Postman para chamadas de API de REST dos serviços de multimédia.
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
ms.openlocfilehash: 5d0b5a57f3fe587a06a102c958b17dbf2a73225c
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58805147"
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>Configurar o Postman para chamadas de API de REST dos serviços de multimédia  

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Veja a versão mais recente, [Serviços de Multimédia v3](https://docs.microsoft.com/azure/media-services/latest/). Além disso, veja [orientação de migração da v2 para a v3](../latest/migrate-from-v2-to-v3.md)

Este tutorial mostra-lhe como configurar **Postman** para que possa ser utilizada para chamar as APIs REST do Azure Media Services (AMS). O tutorial mostra como importar ficheiros para o ambiente e a coleção **Postman**. A coleção contém definições agrupadas de pedidos HTTP que chamam as APIs REST do Azure Media Services (AMS). O ficheiro de ambiente contém variáveis que são utilizadas pela coleção.

Neste ambiente e a coleção é utilizado nos artigos que mostram como atingir várias tarefas com APIs de REST de serviços de multimédia do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Instale o cliente REST do [Postman](https://www.getpostman.com/) para executar as API REST mostradas em alguns dos tutoriais sobre AMS REST. 

    Estamos a utilizar o **Postman**, mas qualquer ferramenta REST seria adequada. Outras alternativas: **Visual Studio Code** com o plug-in do REST ou **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>Configurar o ambiente 

1. Crie um ficheiro. JSON que contém as variáveis de ambiente utilizadas nos tutoriais do AMS. Nomeie o arquivo (por exemplo, **AzureMediaServices.postman_environment.json**). Abra o ficheiro e cole o código que define o ambiente de Postman partir [essa listagem de código](postman-environment.md). 
2. Abra o **Postman**.
3. À direita do ecrã, selecione a opção **Gerir ambiente**.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-create-env.png)
4. Na caixa de diálogo **Gerir ambiente**, clique em **Importar**.
5. Procure e selecione o **AzureMediaServices.postman_environment.json** ficheiro.
6. O **AzureMedia** ambiente é adicionado.
7. Feche a caixa de diálogo.
8. Selecione o **AzureMedia** ambiente.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Configurar a coleção

1. Crie um ficheiro. JSON que contém o **Postman** coleção com todas as operações que são necessários para carregar um ficheiro para os serviços de multimédia. Nomeie o arquivo (por exemplo, **AzureMediaServicesOperations.postman_collection.json**). Abra o ficheiro e cole o código que define a **Postman** coleção da [essa listagem de código](postman-collection.md).
2. Clique em **Importar** para importar o ficheiro de coleção.
3. Escolha o **AzureMediaServicesOperations.postman_collection.json** ficheiro.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Passos Seguintes

Veja a [carregue ativos](media-services-rest-upload-files.md) artigo.  
