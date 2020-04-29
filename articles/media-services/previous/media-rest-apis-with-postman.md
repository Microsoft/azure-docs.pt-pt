---
title: Configure Postman para Azure Media Services REST API chamadas
description: Este artigo descreve como configurar chamadas de Correio para Serviços de Media REST API.
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
ms.openlocfilehash: 11c9c26e7c0f36e1e3dba732e90a6aef95e6ee14
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76694995"
---
# <a name="configure-postman-for-media-services-v2-rest-api-calls"></a>Configure Postman for Media Services v2 REST API chamadas  

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [a orientação de migração da v2 para a v3](../latest/migrate-from-v2-to-v3.md)

Este tutorial mostra-lhe como configurar **o Carteiro** para que possa ser usado para ligar para a Azure Media Services (AMS) REST APIs. O tutorial mostra como importar ambiente e arquivos de recolha em **Carteiro.** A coleção contém definições agrupadas de pedidos HTTP que chamam Azure Media Services (AMS) REST APIs. O ficheiro de ambiente contém variáveis que são utilizadas pela coleção.

Este ambiente e recolha é usado em artigos que mostram como realizar várias tarefas com a Azure Media Services REST APIs.

## <a name="prerequisites"></a>Pré-requisitos

- Instale o cliente REST do [Postman](https://www.getpostman.com/) para executar as API REST mostradas em alguns dos tutoriais sobre AMS REST. 

    Estamos a utilizar o **Postman**, mas qualquer ferramenta REST seria adequada. Outras alternativas são: **Visual Studio Code** com o plugin REST ou **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>Configurar o ambiente 

1. Crie um ficheiro .json que contenha as variáveis ambientais utilizadas nos tutoriais da AMS. Nomeie o ficheiro (por exemplo, **AzureMediaServices.postman_environment.json**). Abra o ficheiro e colhe o código que define o ambiente do Carteiro a partir [desta listagem](postman-environment.md)de código . 
2. Abra o **Postman**.
3. À direita do ecrã, selecione a opção **Gerir ambiente**.

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-create-env.png)
4. Na caixa de diálogo **Gerir ambiente**, clique em **Importar**.
5. Navegue e selecione o ficheiro **AzureMediaServices.postman_environment.json.**
6. O ambiente **AzureMedia** é adicionado.
7. Feche a caixa de diálogo.
8. Selecione o ambiente **AzureMedia.**

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Configurar a coleção

1. Crie um ficheiro .json que contenha a coleção **Do Carteiro** com todas as operações necessárias para fazer o upload de um ficheiro para os Media Services. Nomeie o ficheiro (por exemplo, **AzureMediaServicesOperations.postman_collection.json**). Abra o ficheiro e cole o código que define a coleção **do Carteiro** a partir desta listagem de [código](postman-collection.md).
2. Clique em **Importar** para importar o ficheiro de coleção.
3. Escolha o ficheiro **AzureMediaServicesOperations.postman_collection.json.**

    ![Carregar um ficheiro](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Passos seguintes

Confira o artigo dos [ativos de upload.](media-services-rest-upload-files.md)  
