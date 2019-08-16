---
title: incluir ficheiro
description: incluir ficheiro
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/22/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: f88f6aa6daec342b24d165c4c4cc0a89a0805f7a
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558440"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Associar uma conta de armazenamento do Azure ao Hub IoT

Como o aplicativo de dispositivo simulado carrega um arquivo em um blob, você deve ter uma conta de [armazenamento do Azure](../articles/storage/common/storage-quickstart-create-account.md) associada ao seu hub IOT. Quando você associa uma conta de armazenamento do Azure a um hub IoT, o Hub IoT gera um URI de SAS. Um dispositivo pode usar esse URI de SAS para carregar um arquivo com segurança em um contêiner de BLOB. O serviço Hub IoT e os SDKs de dispositivo coordenam o processo que gera o URI de SAS e o torna disponível para um dispositivo usar para carregar um arquivo.

Siga as instruções em [Configurar carregamentos de arquivo usando o portal do Azure](../articles/iot-hub/iot-hub-configure-file-upload.md). Verifique se um contêiner de blob está associado ao Hub IoT e se as notificações de arquivo estão habilitadas.

![Habilitar notificações de arquivo no portal](./media/iot-hub-associate-storage/file-notifications-vs2019.png)
