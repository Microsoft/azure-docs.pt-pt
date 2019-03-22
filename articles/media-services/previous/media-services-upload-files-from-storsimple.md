---
title: Carregar ficheiros para uma conta dos Serviços de Multimédia do Azure a partir do Azure StorSimple | Microsoft Docs
description: Este artigo proporciona uma breve descrição geral do Azure StorSimple Data Manager. Também contém ligações para tutoriais que lhe mostram como extrair dados do StorSimple e carregar os elementos desses dados para uma conta dos Serviços de Multimédia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 1dd09328-262b-43ef-8099-73241b49a925
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 8a2972b8fbc31a3f945f40949128b3f37f2f4b77
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312483"
---
# <a name="upload-files-into-an-azure-media-services-account-from-azure-storsimple"></a>Carregar ficheiros para uma conta dos Serviços de Multimédia do Azure a partir do Azure StorSimple  

Este artigo proporciona uma breve descrição geral do Azure StorSimple Data Manager. Também contém ligações para tutoriais que lhe mostram como extrair dados do StorSimple e carregar esses dados como elementos para uma conta dos Serviços de Multimédia do Azure (AMS).

> 
> [!NOTE]
> O Azure StorSimple Data Manager está atualmente em pré-visualização privada. 
> 

## <a name="overview"></a>Descrição geral

Nos Serviços de Multimédia, os ficheiros digitais são carregados para um elemento. O Elemento pode conter ficheiros de vídeo, áudio, imagens, coleções de miniaturas, pistas de texto e legendas (e os metadados relativos a esses ficheiros). Assim que os ficheiros são carregados, o seu conteúdo é armazenado em segurança na nuvem para processamento adicional e a transmissão em fluxo.

O [Azure StorSimple](https://docs.microsoft.com/azure/storsimple/) utiliza o armazenamento na cloud como extensão da solução no local e categoriza os dados em camadas automaticamente no armazenamento no local e na cloud. O dispositivo do StorSimple deduz e comprime os dados antes de os enviar para a cloud, tornando muito eficiente de enviar ficheiros grandes para a cloud. O serviço do [Gestor de Dados do StorSimple](../../storsimple/storsimple-data-manager-overview.md) oferece APIs que lhe permitem extrair dados do StorSimple e apresentá-los como ativos de AMS.

## <a name="get-started"></a>Introdução

1. [Crie uma conta dos Serviços de Multimédia](media-services-portal-create-account.md) para a qual queira transferir os recursos.
2. Inscreva-se na pré-visualização do Data Manager, conforme descrito no artigo [StorSimple Data Manager](../../storsimple/storsimple-data-manager-overview.md).
3. Crie uma conta do StorSimple Data Manager.
4. Crie uma tarefa de transformação de dados que, quando for executada, extrai dados de um dispositivo StorSimple e os transfere para uma conta do AMS como recursos. 

    Quando a execução da tarefa for iniciada, é criada uma fila de armazenamento. Esta fila é preenchida com mensagens sobre os blobs transformados, à medida que ficam prontos. O nome desta fila é igual ao nome da definição da tarefa. Pode utilizá-la para determinar quando é que um recurso está pronto e chamar a operação dos Serviços de Multimédia pretendida para ser executada no mesmo. Por exemplo, pode utilizar a fila para acionar uma Função do Azure que contenha o código dos Serviços de Multimédia necessários.

## <a name="see-also"></a>Consulte também

[Utilizar o SDK de .NET para acionar tarefas no Gestor de dados](../../storsimple/storsimple-data-manager-dotnet-jobs.md)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos Seguintes

Agora, pode codificar os elementos que carregar. Para obter mais informações, veja [Codificar elementos](media-services-portal-encode.md)
