---
title: Configure Carteiro para Azure Media Services REST chamadas API
description: Este artigo descreve como configurar chamadas de Carteiro para Serviços de Media REST API.
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
ms.date: 08/31/2020
ms.author: juliako
ms.openlocfilehash: d235316ad6a16cdc535c705ec88ec13d6963fb41
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92910292"
---
# <a name="configure-postman-for-media-services-v2-rest-api-calls"></a>Configure Carteiro para Serviços de Mídia v2 REST API chama

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)

Este tutorial mostra-lhe como configurar **o Carteiro** para que possa ser usado para chamar APIs de REST da Azure Media Services (AMS). O tutorial mostra como importar ambiente e coleções de ficheiros para **o Carteiro.** A coleção contém definições agrupadas de pedidos HTTP que chamam Azure Media Services (AMS) REST APIs. O ficheiro de ambiente contém variáveis que são utilizadas pela coleção.

Este ambiente e coleção é usado em artigos que mostram como realizar várias tarefas com Azure Media Services REST APIs.

## <a name="prerequisites"></a>Pré-requisitos

- Instale o cliente REST do [Postman](https://www.getpostman.com/) para executar as API REST mostradas em alguns dos tutoriais sobre AMS REST. 

    Estamos a utilizar o **Postman** , mas qualquer ferramenta REST seria adequada. Outras alternativas são: **Código de Estúdio Visual** com o plugin REST ou o **Fiddler Telerik** . 

## <a name="configure-the-environment"></a>Configurar o ambiente 

1. Crie um ficheiro .json que contenha as variáveis ambientais utilizadas nos tutoriais da AMS. Nomeie o ficheiro (por exemplo, **AzureMediaServices.postman_environment.js).** Abra o ficheiro e cole o código que define o ambiente do Carteiro a partir [desta listagem](postman-environment.md)de código . 
2. Abra o **Postman** .
3. À direita do ecrã, selecione a opção **Gerir ambiente** .

    ![A screenshot mostra a opção Manage Environment selecionada.](./media/media-services-rest-upload-files/postman-create-env.png)
4. Na caixa de diálogo **Gerir ambiente** , clique em **Importar** .
5. Navegue e selecione as **AzureMediaServices.postman_environment.jsno** ficheiro.
6. O ambiente **AzureMedia** é adicionado.
7. Feche a caixa de diálogo.
8. Selecione o ambiente **AzureMedia.**

    ![A screenshot mostra o ambiente AzureMedia selecionado.](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Configurar a coleção

1. Crie um ficheiro .json que contenha a coleção **Do Carteiro** com todas as operações necessárias para o upload de um ficheiro para os Media Services. Nomeie o ficheiro (por exemplo, **AzureMediaServicesOperations.postman_collection.js).** Abra o ficheiro e cole o código que define a coleção **do Carteiro** a partir [desta listagem](postman-collection.md)de código .
2. Clique em **Importar** para importar o ficheiro de coleção.
3. Escolha o **AzureMediaServicesOperations.postman_collection.jsarquivado.**

    ![A screenshot mostra a caixa de diálogo IMPORT com Choose Files selecionada.](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Passos seguintes

Confira o artigo [do upload de ativos.](media-services-rest-upload-files.md)  
