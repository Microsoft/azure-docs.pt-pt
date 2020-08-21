---
ms.openlocfilehash: be983a643b45847d2a44db018b1383aa56ab2302
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691195"
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
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"      
    ```
    