---
title: Implantar o armazenamento de BLOBs no módulo em seu dispositivo-Azure IoT Edge
description: Implante um módulo de armazenamento de BLOBs do Azure em seu dispositivo IoT Edge para armazenar dados na borda.
author: arduppal
ms.author: arduppal
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
manager: mchad
ms.openlocfilehash: b89532038b00e28eb7c43232683349652af6bc3f
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665870"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Implantar o armazenamento de BLOBs do Azure no módulo IoT Edge em seu dispositivo

Há várias maneiras de implantar módulos em um dispositivo IoT Edge e todos eles funcionam para o armazenamento de BLOBs do Azure em módulos IoT Edge. Os dois métodos mais simples são usar os modelos portal do Azure ou Visual Studio Code.

## <a name="prerequisites"></a>Pré-requisitos

- Um [Hub IOT](../iot-hub/iot-hub-create-through-portal.md) em sua assinatura do Azure.
- Um [dispositivo IOT Edge](how-to-register-device.md) com o IOT Edge Runtime instalado.
- [Visual Studio Code](https://code.visualstudio.com/) e as [ferramentas de IOT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) se estiver implantando do Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Implantar do portal do Azure

O portal do Azure orienta você durante a criação de um manifesto de implantação e o envio por push da implantação para um dispositivo IoT Edge.

### <a name="select-your-device"></a>Selecione seu dispositivo

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o Hub IOT.
1. Selecione **IOT Edge** no menu.
1. Clique na ID do dispositivo de destino na lista de dispositivos.
1. Selecione **Definir Módulos**.

### <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos serão implantados, como os dados fluem entre os módulos e as propriedades desejadas do módulo gêmeos. O portal do Azure tem um assistente que o orienta na criação de um manifesto de implantação, em vez de criar o documento JSON manualmente. Ele tem três etapas: **Adicionar módulos**, **especificar rotas**e **examinar a implantação**.

#### <a name="add-modules"></a>Adicionar módulos

1. Na seção **módulos de implantação** da página, selecione **Adicionar**.

1. Nos tipos de módulos na lista suspensa, selecione **IOT Edge módulo**.

1. Forneça um nome para o módulo e, em seguida, especifique a imagem de contêiner:

   - **Nome** -azureblobstorageoniotedge
   - **URI da imagem** -MCR.Microsoft.com/Azure-Blob-Storage:Latest

   > [!IMPORTANT]
   > Azure IoT Edge diferencia maiúsculas de minúsculas quando você faz chamadas para módulos e o SDK de armazenamento também usa como padrão letras minúsculas. Embora o nome do módulo no [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) seja **AzureBlobStorageonIoTEdge**, alterar o nome para minúsculas ajuda a garantir que suas conexões com o armazenamento de BLOBs do Azure no módulo IOT Edge não sejam interrompidas.

1. Os valores de **Opções de criação de contêiner** padrão definem as associações de porta que seu contêiner precisa, mas você também precisa adicionar as informações da conta de armazenamento e uma montagem para o armazenamento em seu dispositivo. Substitua o JSON padrão no portal pelo JSON abaixo:

   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage mount>"
       ],
       "PortBindings":{
         "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

1. Atualize o JSON que você copiou com as seguintes informações:

   - Substitua `<your storage account name>` por um nome que você possa lembrar. Os nomes de conta devem ter entre 3 e 24 caracteres, com letras minúsculas e números. Sem espaços.

   - Substitua `<your storage account key>` por uma chave Base64 de 64 bytes. Você pode gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64). Você usará essas credenciais para acessar o armazenamento de blobs de outros módulos.

   - Substitua `<storage mount>` de acordo com o sistema operacional do contêiner. Forneça o nome de um [volume](https://docs.docker.com/storage/volumes/) ou o caminho absoluto para um diretório no dispositivo IOT Edge em que você deseja que o módulo de blob armazene seus dados. A montagem de armazenamento mapeia um local em seu dispositivo que você fornece a um local definido no módulo.

     - Para contêineres do Linux, o formato é *\<caminho de armazenamento ou volume >:/blobroot*. Por exemplo
         - usar [montagem de volume](https://docs.docker.com/storage/volumes/): **My-volume:/blobroot** 
         - Use a [montagem de ligação](https://docs.docker.com/storage/bind-mounts/): **/SRV/containerdata:/blobroot**. Certifique-se de seguir as etapas para [conceder acesso ao diretório para o usuário do contêiner](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Para contêineres do Windows, o formato é *\<caminho de armazenamento ou volume >: C:/BlobRoot*. Por exemplo
         - usar [montagem de volume](https://docs.docker.com/storage/volumes/): **My-volume: C:/blobroot**. 
         - Use a [montagem de ligação](https://docs.docker.com/storage/bind-mounts/): **c:/ContainerData: c:/BlobRoot**.
         - Em vez de usar sua unidade local, você pode mapear seu local de rede SMB, para obter mais informações, consulte [usando o compartilhamento SMB como seu armazenamento local](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Não altere a segunda metade do valor de montagem de armazenamento, que aponta para um local específico no módulo. A montagem de armazenamento sempre deve terminar com **:/blobroot** para contêineres do Linux e **: C:/blobroot** para contêineres do Windows.

1. Defina as propriedades [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) para o seu módulo copiando o JSON a seguir e colando-o na caixa de **propriedades desejadas do módulo Set** . Configure cada propriedade com um valor apropriado, salve-a e continue com a implantação. Se você estiver usando o simulador de IoT Edge, defina os valores para as variáveis de ambiente relacionadas para essas propriedades, que você pode encontrar na seção de explicação de [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties).

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

   ![definir opções de criação de contêiner, Propriedades deviceAutoDeleteProperties e deviceToCloudUploadProperties](./media/how-to-deploy-blob/iotedge-custom-module.png)

   Para obter informações sobre como configurar o deviceToCloudUploadProperties e o deviceAutoDeleteProperties depois que o módulo tiver sido implantado, consulte [Editar o módulo](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)... Para obter mais informações sobre as propriedades desejadas, consulte [definir ou atualizar as propriedades desejadas](module-composition.md#define-or-update-desired-properties).

1. Selecione **Guardar**.

1. Selecione **Avançar** para continuar na seção rotas.

#### <a name="specify-routes"></a>Especificar rotas

Mantenha as rotas padrão e selecione **Avançar** para continuar na seção revisão.

#### <a name="review-deployment"></a>Examinar a implantação

A seção revisão mostra o manifesto de implantação JSON que foi criado com base em suas seleções nas duas seções anteriores. Também há dois módulos declarados que você não adicionou: **$edgeAgent** e **$edgeHub**. Esses dois módulos compõem o [tempo de execução de IOT Edge](iot-edge-runtime.md) e são padrões necessários em cada implantação.

Examine as informações de implantação e, em seguida, selecione **Enviar**.

### <a name="verify-your-deployment"></a>Verificar sua implantação

Depois de enviar a implantação, você retornará para a página **IOT Edge** do Hub IOT.

1. Selecione o dispositivo de IoT Edge de destino com a implantação para abrir seus detalhes.
1. Nos detalhes do dispositivo, verifique se o módulo armazenamento de BLOBs está listado como ambos **especificados na implantação** e **relatados pelo dispositivo**.

Pode levar alguns minutos para que o módulo seja iniciado no dispositivo e reportado de volta ao Hub IoT. Atualize a página para ver um status atualizado.

## <a name="deploy-from-visual-studio-code"></a>Implantar do Visual Studio Code

Azure IoT Edge fornece modelos no Visual Studio Code para ajudá-lo a desenvolver soluções de borda. Use as etapas a seguir para criar uma nova solução de IoT Edge com um módulo de armazenamento de BLOBs e configurar o manifesto de implantação.

1. Selecione **exibir** > **paleta de comandos**.

1. Na paleta de comandos, introduza e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**.

   ![Executar nova solução de IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Siga as instruções na paleta de comandos para criar a sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha o local no computador de desenvolvimento para Visual Studio Code criar os arquivos de solução. |
   | Indicar um nome para a solução | Insira um nome descritivo para sua solução ou aceite o **EdgeSolution**padrão. |
   | Selecionar modelo de módulo | Escolha o **módulo existente (Insira a URL da imagem completa)** . |
   | Indicar um nome para o módulo | Insira um nome com todas as minúsculas para o módulo, como **azureblobstorageoniotedge**.<br /><br />É importante usar um nome em minúsculas para o armazenamento de BLOBs do Azure no módulo IoT Edge. IoT Edge diferencia maiúsculas de minúsculas ao fazer referência a módulos e o SDK de armazenamento usa como padrão letras minúsculas. |
   | Forneça a imagem do Docker para o módulo | Forneça o URI da imagem: **MCR.Microsoft.com/Azure-Blob-Storage:Latest** |

   Visual Studio Code Obtém as informações fornecidas, cria uma solução de IoT Edge e a carrega em uma nova janela. O modelo de solução cria um modelo de manifesto de implantação que inclui a imagem do módulo de armazenamento de BLOBs, mas você precisa configurar as opções de criação do módulo.

1. Abra *Deployment. Template. JSON* em seu novo espaço de trabalho de solução e localize a seção **módulos** . Faça as seguintes alterações de configuração:

   1. Exclua o módulo **SimulatedTemperatureSensor** , pois ele não é necessário para essa implantação.

   1. Copie e cole o seguinte código no campo `createOptions`:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage mount>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Atualizar o módulo criaroptions-Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. Substitua `<your storage account name>` por um nome que você possa lembrar. Os nomes de conta devem ter entre 3 e 24 caracteres, com letras minúsculas e números. Sem espaços.

1. Substitua `<your storage account key>` por uma chave Base64 de 64 bytes. Você pode gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64). Você usará essas credenciais para acessar o armazenamento de blobs de outros módulos.

1. Substitua `<storage mount>` de acordo com o sistema operacional do contêiner. Forneça o nome de um [volume](https://docs.docker.com/storage/volumes/) ou o caminho absoluto para um diretório no dispositivo IOT Edge em que você deseja que o módulo de blob armazene seus dados. A montagem de armazenamento mapeia um local em seu dispositivo que você fornece a um local definido no módulo.  

      
     - Para contêineres do Linux, o formato é *\<caminho de armazenamento ou volume >:/blobroot*. Por exemplo
         - usar [montagem de volume](https://docs.docker.com/storage/volumes/): **My-volume:/blobroot** 
         - Use a [montagem de ligação](https://docs.docker.com/storage/bind-mounts/): **/SRV/containerdata:/blobroot**. Certifique-se de seguir as etapas para [conceder acesso ao diretório para o usuário do contêiner](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Para contêineres do Windows, o formato é *\<caminho de armazenamento ou volume >: C:/BlobRoot*. Por exemplo
         - usar [montagem de volume](https://docs.docker.com/storage/volumes/): **My-volume: C:/blobroot**. 
         - Use a [montagem de ligação](https://docs.docker.com/storage/bind-mounts/): **c:/ContainerData: c:/BlobRoot**.
         - Em vez de usar sua unidade local, você pode mapear seu local de rede SMB, para obter mais informações, consulte [usando o compartilhamento SMB como seu armazenamento local](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Não altere a segunda metade do valor de montagem de armazenamento, que aponta para um local específico no módulo. A montagem de armazenamento sempre deve terminar com **:/blobroot** para contêineres do Linux e **: C:/blobroot** para contêineres do Windows.

1. Configure o [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e o [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) para o módulo adicionando o JSON a seguir ao arquivo *Deployment. Template. JSON* . Configure cada propriedade com um valor apropriado e salve o arquivo. Se você estiver usando o simulador de IoT Edge, defina os valores para as variáveis de ambiente relacionadas para essas propriedades, que você pode encontrar na seção de explicação de [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)

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

   ![definir as propriedades desejadas para azureblobstorageoniotedge-Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   Para obter informações sobre como configurar o deviceToCloudUploadProperties e o deviceAutoDeleteProperties depois que o módulo tiver sido implantado, consulte [Editar o módulo](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)... Para obter mais informações sobre opções de criação de contêiner, política de reinicialização e status desejado, consulte [EdgeAgent Desired Properties](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Guarde o ficheiro *deployment.template.json*.

1. Clique com o botão direito do mouse em **Deployment. Template. JSON** e selecione **gerar IOT Edge manifesto de implantação**.

1. Visual Studio Code Obtém as informações fornecidas em *Deployment. Template. JSON* e as usa para criar um novo arquivo de manifesto de implantação. O manifesto de implantação é criado em uma nova pasta de **configuração** em seu espaço de trabalho de solução. Depois de ter esse arquivo, você pode seguir as etapas em [implantar módulos de Azure IOT Edge de Visual Studio Code](how-to-deploy-modules-vscode.md) ou [implantar módulos de Azure IoT Edge com CLI do Azure 2,0](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Implantar várias instâncias de módulo

Se desejar implantar várias instâncias do armazenamento de BLOBs do Azure no módulo IoT Edge, você precisará fornecer um caminho de armazenamento diferente e alterar o valor de `HostPort` ao qual o módulo é associado. Os módulos de armazenamento de BLOBs sempre expõem a porta 11002 no contêiner, mas você pode declarar a qual porta ela está vinculada no host.

Edite **as opções de criação de contêiner** (no portal do Azure) **ou o campo** (no arquivo *Deployment. Template. JSON* em Visual Studio Code) para alterar o valor `HostPort`:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Quando você se conectar a módulos de armazenamento de BLOBs adicionais, altere o ponto de extremidade para apontar para a porta de host atualizada.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o [armazenamento de BLOBs do Azure no IOT Edge](how-to-store-data-blob.md)

Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [entender como IOT Edge módulos podem ser usados, configurados e reutilizados](module-composition.md).
