---
title: Implantar o módulo de armazenamento de Blobs do Azure para dispositivos - Azure IoT Edge | Documentos da Microsoft
description: Implemente um módulo de armazenamento de Blobs do Azure para o seu dispositivo IoT Edge para armazenar dados na periferia.
author: arduppal
ms.author: arduppal
ms.date: 06/19/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.reviewer: arduppal
manager: mchad
ms.openlocfilehash: 468e4fca5e67850949e7d5826e4bc88fa504b9d6
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295203"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Implementar o armazenamento de Blobs do Azure no módulo do IoT Edge no seu dispositivo

Existem várias formas de implementar módulos para um dispositivo IoT Edge e todos eles funcionam para armazenamento de Blobs do Azure em módulos do IoT Edge. Os dois métodos mais simples estão a utilizar os modelos de código do Visual Studio ou o portal do Azure.

> [!NOTE]
> Armazenamento de Blobs do Azure no IoT Edge está em [pré-visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

- Uma [IoT hub](../iot-hub/iot-hub-create-through-portal.md) na sua subscrição do Azure.
- Uma [dispositivo IoT Edge](how-to-register-device-portal.md) com o runtime do IoT Edge instalado.
- [Visual Studio Code](https://code.visualstudio.com/) e o [ferramentas de IoT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) se implementar a partir do Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Implementar a partir do portal do Azure

O portal do Azure orienta a criação de um manifesto de implantação e enviar a implementação para um dispositivo IoT Edge.

### <a name="select-your-device"></a>Selecione o seu dispositivo

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e navegue até ao seu hub IoT.
1. Selecione **IoT Edge** no menu.
1. Clique no ID de dispositivo de destino na lista de dispositivos.
1. Selecione **Definir Módulos**.

### <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos para implementar, como os dados fluem entre os módulos e propriedades pretendidas do duplos de módulo. O portal do Azure tem um assistente que explica como criar um manifesto de implantação, em vez de criar o documento JSON manualmente. Ele tem três passos: **Adicionar módulos**, **especificar rotas**, e **reveja implementação**.

#### <a name="add-modules"></a>Adicionar módulos

1. Na **módulos de implementação** secção da página, selecione **Add**.

1. Os tipos de módulos na lista pendente, selecione **módulo do IoT Edge**.

1. Forneça um nome para o módulo e, em seguida, especifique a imagem de contentor:

   - **Name** - azureblobstorageoniotedge
   - **URI da imagem** -mcr.microsoft.com/azure-blob-storage:latest

   > [!IMPORTANT]
   > O Azure IoT Edge diferencia maiúsculas de minúsculas, quando fazer chamadas para módulos e o SDK de armazenamento também por predefinição em minúsculas. Embora o nome do módulo na [do Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) é **AzureBlobStorageonIoTEdge**, alterar o nome em minúsculas ajuda a garantir que as suas ligações para o armazenamento de Blobs do Azure no módulo do IoT Edge não são interrompidos.

1. A predefinição **opções de criar contentor** valores definem as associações de porta que precisa do seu contentor, mas também tem de adicionar as informações da conta de armazenamento e uma ligação para o diretório de armazenamento no seu dispositivo. Substitua o padrão JSON no portal com o JSON abaixo:

   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage directory bind>"
       ],
     "PortBindings":{
       "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

1. Atualize o JSON que copiou com as seguintes informações:

   - Substitua `<your storage account name>` com um nome que pode se lembrar. Nomes de contas devem ser 3 a 24 carateres com letras minúsculas e números. Sem espaços.

   - Substitua `<your storage account key>` com uma chave de base64 de 64 bytes. Pode gerar uma chave com ferramentas como o [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Irá utilizar estas credenciais para aceder ao armazenamento de BLOBs a partir de outros módulos.

   - Substitua `<storage directory bind>` , de acordo com seu sistema operativo do contentor. Forneça o nome de um [volume](https://docs.docker.com/storage/volumes/) ou o caminho absoluto para um diretório no seu dispositivo IoT Edge onde pretende que o módulo de BLOBs para armazenar os dados. A associação de diretório de armazenamento mapeia uma localização no seu dispositivo que fornece uma localização do conjunto no módulo.

     - Para contentores do Linux, é o formato  *\<caminho de armazenamento >: / blobroot*. Por exemplo, **/srv/containerdata: / blobroot** ou **meu volume: / blobroot**.
     - Para contentores do Windows, é o formato  *\<caminho de armazenamento >: C: / BlobRoot*. Por exemplo, **c: / ContainerData:C: / BlobRoot** ou **meu-volume: C: / blobroot**.

     > [!IMPORTANT]
     > Não altere a segunda que metade do diretório de armazenamento vincular o valor, que aponta para uma localização específica no módulo. A associação de diretório de armazenamento sempre deve terminar com **: / blobroot** para contentores do Linux e **: C: / BlobRoot** para contentores do Windows.

1. Definir [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) propriedades para seu módulo, copiar o seguinte JSON e cole-o para o **pretendido de conjunto de módulos duplos propriedades** caixa. Configurar cada propriedade com um valor adequado, guardá-lo e continuar com a implementação.

   ```json
   {
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading":<true,false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload":<true,false>
       }
     }
   }

      ```

   ![contentor de conjunto criar propriedades de opções, deviceAutoDeleteProperties e deviceToCloudUploadProperties](./media/how-to-deploy-blob/iotedge-custom-module.png)

   Para obter informações sobre como configurar deviceToCloudUploadProperties e deviceAutoDeleteProperties após a implantação do seu módulo, consulte [editar o módulo duplo](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Para obter mais informações sobre as propriedades pretendidas, consulte [definir ou atualizar propriedades pretendidas](module-composition.md#define-or-update-desired-properties).

1. Selecione **Guardar**.

1. Selecione **seguinte** para continuar para a secção de rotas.

#### <a name="specify-routes"></a>Especificar rotas

Manter as rotas predefinidas e selecione **seguinte** para continuar para a secção de revisão.

#### <a name="review-deployment"></a>Implementação de revisão

A revisão de seção mostra que a implementação de JSON de manifesto que foi criado com base nas suas seleções nas duas secções anteriores. Também existem dois módulos declarado se não tiver adicionado: **$edgeAgent** e **$edgeHub**. Esses dois módulos formam o [runtime do IoT Edge](iot-edge-runtime.md) e são predefinições necessárias em todas as implementações.

Reveja as suas informações de implantação, em seguida, selecione **submeter**.

### <a name="verify-your-deployment"></a>Certifique-se a implementação

Depois de submeter a implementação, retornar para o **IoT Edge** página do IoT hub.

1. Selecione o dispositivo do IoT Edge direcionados com a implementação para abrir os respetivos detalhes.
1. Nos detalhes do dispositivo, certifique-se de que o módulo de armazenamento de BLOBs é listado como ambos **especificadas na implementação** e **comunicada pelo dispositivo**.

Pode demorar alguns minutos para o módulo ser iniciada no dispositivo e, em seguida, relatado ao IoT Hub. Atualize a página para ver o estado atualizado.

## <a name="deploy-from-visual-studio-code"></a>Implemente a partir do Visual Studio Code

O Azure IoT Edge fornece modelos no Visual Studio Code para ajudar a desenvolver soluções de ponta. Utilize os seguintes passos para criar uma nova solução de IoT Edge com um módulo de armazenamento de BLOBs e para configurar o manifesto de implantação.

1. Selecione **View** > **paleta de comandos**.

1. Na paleta de comandos, introduza e execute o comando **Azure IoT Edge: Nova solução de IoT Edge**.

   ![Executar a nova solução de IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Siga as instruções na paleta de comandos para criar a sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento para o Visual Studio Code criar os arquivos da solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução ou aceite a predefinição **EdgeSolution**. |
   | Selecionar modelo de módulo | Escolher **módulo existente (URL de imagem completa de Enter)** . |
   | Indicar um nome para o módulo | Introduza um nome em minúsculas para seu módulo, como **azureblobstorageoniotedge**.<br /><br />É importante utilizar um nome em minúsculas para o armazenamento de Blobs do Azure no módulo do IoT Edge. IoT Edge diferencia maiúsculas de minúsculas quando nos Referimos aos módulos e o SDK de armazenamento por predefinição em minúsculas. |
   | Forneça a imagem do Docker para o módulo | Forneça o URI da imagem: **mcr.microsoft.com/azure-blob-storage:latest** |

   Código do Visual Studio usa as informações fornecidas, cria uma solução de IoT Edge e, em seguida, carrega-o numa nova janela. O modelo de solução cria um modelo de manifesto de implantação que inclua a sua imagem de módulo de armazenamento de BLOBs, mas tem de configurar opções de criação do módulo.

1. Open *deployment.template.json* na sua nova área de trabalho da solução e localizar o **módulos** secção. Efetue as seguintes alterações de configuração:

   1. Eliminar a **tempSensor** módulo, como ele não do necessário para esta implementação.

   1. Copie e cole o código seguinte para o `createOptions` campo:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Atualizar o módulo createOptions - Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. Substitua `<your storage account name>` com um nome que pode se lembrar. Nomes de contas devem ser 3 a 24 carateres com letras minúsculas e números. Sem espaços.

1. Substitua `<your storage account key>` com uma chave de base64 de 64 bytes. Pode gerar uma chave com ferramentas como o [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Irá utilizar estas credenciais para aceder ao armazenamento de BLOBs a partir de outros módulos.

1. Substitua `<storage directory bind>` , de acordo com seu sistema operativo do contentor. Forneça o nome de um [volume](https://docs.docker.com/storage/volumes/) ou o caminho absoluto para um diretório no seu dispositivo IoT Edge onde pretende que o módulo de BLOBs para armazenar os dados. A associação de diretório de armazenamento mapeia uma localização no seu dispositivo que fornece uma localização do conjunto no módulo.  

      - Para contentores do Linux, é o formato  *\<caminho de armazenamento >: / blobroot*. Por exemplo, **/srv/containerdata: / blobroot** ou **meu volume: / blobroot**.
      - Para contentores do Windows, é o formato  *\<caminho de armazenamento >: C: / BlobRoot*. Por exemplo, **c: / ContainerData:C: / BlobRoot** ou **meu-volume: C: / blobroot**.

      > [!IMPORTANT]
      > Não altere a segunda que metade do diretório de armazenamento vincular o valor, que aponta para uma localização específica no módulo. A associação de diretório de armazenamento sempre deve terminar com **: / blobroot** para contentores do Linux e **: C: / BlobRoot** para contentores do Windows.

1. Configurar [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) para seu módulo, adicionando o seguinte JSON para o *deployment.template.json* ficheiro. Configure cada propriedade com um valor adequado e guarde o ficheiro.

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading": <true, false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload": <true, false>
       }
     }
   }
   ```

   ![definir as propriedades pretendidas para azureblobstorageoniotedge - Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   Para obter informações sobre como configurar deviceToCloudUploadProperties e deviceAutoDeleteProperties após a implantação do seu módulo, consulte [editar o módulo duplo](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Para obter mais informações sobre o contentor criar opções, reinicie a política e o estado pretendido, veja [propriedades pretendidas do EdgeAgent](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Guarde o ficheiro *deployment.template.json*.

1. Com o botão direito **deployment.template.json** e selecione **manifesto de implantação de gerar o IoT Edge**.

1. Código do Visual Studio usa as informações que indicou *deployment.template.json* e utiliza-o para criar um novo ficheiro de manifesto de implantação. O manifesto de implantação é criado numa nova **config** pasta na sua área de trabalho da solução. Assim que tiver esse ficheiro, pode seguir os passos em [módulos de implementar o Azure IoT Edge do Visual Studio Code](how-to-deploy-modules-vscode.md) ou [módulos de implementar o Azure IoT Edge com a CLI 2.0 do Azure](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Implementar várias instâncias de módulo

Se pretender implementar várias instâncias do armazenamento de Blobs do Azure no módulo do IoT Edge, terá de fornecer um caminho de armazenamento diferente e alterar o `HostPort` valor que liga o módulo. Os módulos de armazenamento de BLOBs expõem sempre a porta 11002 no contentor, mas pode declarar que porta está vinculado a no anfitrião.

Editar **opções de criar contentor** (no portal do Azure) ou o **createOptions** campo (no *deployment.template.json* ficheiro no Visual Studio Code) para alterar o `HostPort` valor:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Quando se liga aos módulos de armazenamento de BLOBs adicionais, altere o ponto final para apontar para a porta do anfitrião atualizado.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como o trabalho de manifestos de implantação e como criá-los, consulte [compreender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).
