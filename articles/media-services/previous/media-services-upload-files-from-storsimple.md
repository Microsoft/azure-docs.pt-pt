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
ms.openlocfilehash: d1d43f11c1a90456b24f02a5ec43982d5fdc3de7
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98694526"
---
# <a name="upload-files-into-an-azure-media-services-account-from-azure-storsimple"></a>Carregar ficheiros para uma conta dos Serviços de Multimédia do Azure a partir do Azure StorSimple 

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)
>
> 
> O Azure StorSimple Data Manager está atualmente em pré-visualização privada. 
> 

## <a name="overview"></a>Descrição geral

Nos Serviços de Multimédia, os ficheiros digitais são carregados para um elemento. O Ativo pode conter vídeo, áudio, imagens, recolhas de miniaturas, faixas de texto e ficheiros de legendas fechados (e os metadados sobre estes ficheiros.) Uma vez que os ficheiros são carregados, o seu conteúdo é armazenado de forma segura na nuvem para posterior processamento e streaming.

O [Azure StorSimple](../../storsimple/index.yml) utiliza o armazenamento na cloud como extensão da solução no local e categoriza os dados em camadas automaticamente no armazenamento no local e na cloud. O dispositivo do StorSimple deduz e comprime os dados antes de os enviar para a cloud, tornando muito eficiente de enviar ficheiros grandes para a cloud. O serviço do [Gestor de Dados do StorSimple](../../storsimple/storsimple-data-manager-overview.md) oferece APIs que lhe permitem extrair dados do StorSimple e apresentá-los como ativos de AMS.

## <a name="get-started"></a>Introdução

1. [Crie uma conta dos Serviços de Multimédia](media-services-portal-create-account.md) para a qual queira transferir os recursos.
2. Inscreva-se na pré-visualização do Data Manager, conforme descrito no artigo [StorSimple Data Manager](../../storsimple/storsimple-data-manager-overview.md).
3. Crie uma conta do StorSimple Data Manager.
4. Crie uma tarefa de transformação de dados que, quando for executada, extrai dados de um dispositivo StorSimple e os transfere para uma conta do AMS como recursos. 

    Quando a execução da tarefa for iniciada, é criada uma fila de armazenamento. Esta fila é preenchida com mensagens sobre os blobs transformados, à medida que ficam prontos. O nome desta fila é igual ao nome da definição da tarefa. Pode utilizá-la para determinar quando é que um recurso está pronto e chamar a operação dos Serviços de Multimédia pretendida para ser executada no mesmo. Por exemplo, pode utilizar a fila para acionar uma Função do Azure que contenha o código dos Serviços de Multimédia necessários.

## <a name="see-also"></a>Ver também

[Utilize o .NET SDK para desencadear postos de trabalho no Gestor de Dados](../../storsimple/storsimple-data-manager-dotnet-jobs.md)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos seguintes

Agora, pode codificar os elementos que carregar. Para obter mais informações, veja [Codificar elementos](media-services-portal-encode.md)
