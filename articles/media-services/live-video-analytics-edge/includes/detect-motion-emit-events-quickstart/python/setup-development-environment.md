---
ms.openlocfilehash: 94111ae3b02da897b9544adaad384fd281a91b02
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530864"
---
1. Clone o repo a partir deste local: https://github.com/Azure-Samples/live-video-analytics-iot-edge-python .
1. No Código do Estúdio Visual, abra a pasta onde o repo foi descarregado.
1. No Código do Estúdio Visual, aceda à pasta *src/cloud-to-device-console-app.* Lá, crie um ficheiro e diga-lhe *appsettings.js.* Este ficheiro conterá as definições necessárias para executar o programa.
1. Copie o conteúdo da *amostra /lva/lva/appsettings.jsno* ficheiro que gerou anteriormente neste arranque rápido.

    O texto deve parecer a seguinte saída.

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Vá à pasta *src/edge* e crie um ficheiro chamado *.env*.
1. Copie o conteúdo do ficheiro */clouddrive/lva-sample/edge-deployment/.env.* O texto deve parecer o seguinte código.

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    VIDEO_INPUT_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"      
    ```
    