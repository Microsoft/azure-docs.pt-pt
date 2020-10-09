---
title: Implemente o armazenamento de blob no módulo para o seu dispositivo - Azure IoT Edge
description: Coloque um módulo de armazenamento Azure Blob no seu dispositivo IoT Edge para armazenar dados na borda.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: da163e902d06bd98ac47a24256cb809cb222173b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80804627"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Implementar o Armazenamento de Blobs do Azure no módulo do IoT Edge no dispositivo

Existem várias formas de implantar módulos para um dispositivo IoT Edge e todos eles trabalham para o Azure Blob Storage em módulos IoT Edge. Os dois métodos mais simples são usar o portal Azure ou modelos de Código do Estúdio Visual.

## <a name="prerequisites"></a>Pré-requisitos

- Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) na sua assinatura Azure.
- Um [dispositivo IoT Edge](how-to-register-device.md) com o tempo de execução IoT Edge instalado.
- [Código de Estúdio Visual](https://code.visualstudio.com/) e as [Ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) se forem implantados a partir do Código do Estúdio Visual.

## <a name="deploy-from-the-azure-portal"></a>Implantação a partir do portal Azure

O portal Azure guia-o através da criação de um manifesto de implantação e empurrando a implementação para um dispositivo IoT Edge.

### <a name="select-your-device"></a>Selecione o seu dispositivo

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue até ao seu hub IoT.
1. Selecione **IoT Edge** no menu.
1. Clique no ID do dispositivo alvo a partir da lista de dispositivos.'
1. Selecione **módulos de conjunto**.

### <a name="configure-a-deployment-manifest"></a>Configure um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos a implementar, como os dados fluem entre os módulos e as propriedades desejadas dos gémeos módulos. O portal Azure tem um assistente que o acompanha através da criação de um manifesto de implantação. Tem três passos organizados em separadores: **Módulos,** **Rotas**e **Revisão + Criar.**

#### <a name="add-modules"></a>Adicionar módulos

1. Na secção **módulos IoT Edge modules** da página, clique no **Add** dropdown e selecione **O Módulo de Borda IoT** para exibir a página do Módulo de Borda Add **IoT.**

2. No **separador Definições** do Módulo, forneça um nome para o módulo e, em seguida, especifique a imagem do recipiente URI:

   Exemplos:
  
   - **Nome do módulo IoT Edge**: `azureblobstorageoniotedge`
   - **Imagem URI:**`mcr.microsoft.com/azure-blob-storage:latest`

   ![Configurações twin do módulo](./media/how-to-deploy-blob/addmodule-tab1.png)

   Não **selecione Adicionar** até ter especificado **valores**nas Definições do Módulo , **Opções de Criação de Recipientes**e Separadores  **de Configurações Gémeas do Módulo,** conforme descrito neste procedimento.

   > [!IMPORTANT]
   > O Azure IoT Edge é sensível a casos quando faz chamadas para módulos, e o SDK de armazenamento também falha em minúsculas. Embora o nome do módulo no [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) seja **AzureBlobStorageonIoTEdge,** mudar o nome para minúscula ajuda a garantir que as suas ligações com o Azure Blob Storage no módulo IoT Edge não sejam interrompidas.

3. Abra o **separador Opções de Criação de Recipientes.**

   ![Configurações twin do módulo](./media/how-to-deploy-blob/addmodule-tab3.png)

   Copie e cole o seguinte JSON na caixa, para fornecer informações de conta de armazenamento e um suporte para o armazenamento no seu dispositivo.
  
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

4. Atualize o JSON que copiou para **o Container Create Options** com as seguintes informações:

   - Substitua `<your storage account name>` por um nome de que se lembre. Os nomes das contas devem ter entre 3 a 24 caracteres, com letras e números minúsculos. Sem espaços.

   - `<your storage account key>`Substitua-a por uma tecla base64 de 64 bytes. Pode gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64). Você usará estas credenciais para aceder ao armazenamento de bolhas de outros módulos.

   - Substitua `<storage mount>` de acordo com o seu sistema de funcionamento do contentor. Forneça o nome de um [volume](https://docs.docker.com/storage/volumes/) ou o caminho absoluto para um diretório existente no seu dispositivo IoT Edge onde o módulo blob irá armazenar os seus dados. O suporte de armazenamento mapeia uma localização no seu dispositivo que fornece para uma localização definida no módulo.

     - Para os recipientes Linux, o formato é ** \<your storage path or volume> :/blobroot**. Por exemplo:
         - montagem [de volume de](https://docs.docker.com/storage/volumes/)utilização: `my-volume:/blobroot`
         - utilizar [o suporte do encaixe](https://docs.docker.com/storage/bind-mounts/): `/srv/containerdata:/blobroot` . Certifique-se de seguir as etapas para [conceder acesso ao diretório ao utilizador do contentor](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Para os recipientes windows, o formato é ** \<your storage path or volume> :C:/BlobRoot**. Por exemplo:
         - montagem [de volume de](https://docs.docker.com/storage/volumes/)utilização : . `my-volume:C:/BlobRoot` .
         - utilizar [o suporte do encaixe](https://docs.docker.com/storage/bind-mounts/): `C:/ContainerData:C:/BlobRoot` .
         - Em vez de utilizar a sua unidade local, pode mapear a sua localização da rede SMB, para obter mais informações, ver [usando a partilha SMB como o seu armazenamento local](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Não altere a segunda metade do valor do suporte de armazenamento, que aponta para uma localização específica no bloco blob armazenamento no módulo IoT Edge. O suporte de armazenamento deve sempre terminar com **:/blobroot** para recipientes Linux e **:C:/BlobRoot** para recipientes Windows.

5. No **separador Definições Gémeas** do Módulo, copie o JSON seguinte e cole-o na caixa.

   ![Configurações twin do módulo](./media/how-to-deploy-blob/addmodule-tab4.png)

   Configure cada imóvel com um valor adequado, conforme indicado pelos espaços reservados. Se estiver a utilizar o simulador IoT Edge, defina os valores para as variáveis ambientais relacionadas para estas propriedades, conforme descrito pelo [dispositivoToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [dispositivoAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties).

   ```json
   {
     "deviceAutoDeleteProperties": {
       "deleteOn": <true, false>,
       "deleteAfterMinutes": <timeToLiveInMinutes>,
       "retainWhileUploading": <true,false>
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
       "deleteAfterUpload": <true,false>
     }
   }
   ```

   Para obter informações sobre a configuração do dispositivoToCloudUploadProperties e dispositivoAutoDeleteProperties após a implementação do seu módulo, consulte [Editar o Módulo Twin](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Para obter mais informações sobre as propriedades desejadas, consulte [Definir ou atualizar as propriedades desejadas.](module-composition.md#define-or-update-desired-properties)

6. Selecione **Adicionar**.

7. Selecione **Seguinte: Rotas** para continuar até à secção de rotas.

#### <a name="specify-routes"></a>Especificar rotas

Mantenha as rotas predefinidos e selecione **Seguinte: Revisão + criar** para continuar na secção de revisão.

#### <a name="review-deployment"></a>Implementação de revisão

A secção de revisão mostra-lhe o manifesto de implantação JSON que foi criado com base nas suas seleções nas duas secções anteriores. Há também dois módulos declarados que não acrescentou: **$edgeAgent** e **$edgeHub**. Estes dois módulos compõem o [tempo de funcionamento do IoT Edge](iot-edge-runtime.md) e são necessários predefinidos em todas as implementações.

Reveja as suas informações de implementação e, em seguida, **selecione Criar**.

### <a name="verify-your-deployment"></a>Verifique a sua implantação

Depois de criar a implementação, volte à página **IoT Edge** do seu hub IoT.

1. Selecione o dispositivo IoT Edge que direcionou com a implementação para abrir os seus detalhes.
1. Nos dados do dispositivo, verifique se o módulo de armazenamento de bolhas está listado como **especificado na implementação** e **reportado pelo dispositivo**.

Pode levar alguns momentos para que o módulo seja iniciado no dispositivo e depois reportado de volta ao IoT Hub. Refresque a página para ver um estado atualizado.

## <a name="deploy-from-visual-studio-code"></a>Implementar a partir do Código do Estúdio Visual

Azure IoT Edge fornece modelos no Código do Estúdio Visual para ajudá-lo a desenvolver soluções de borda. Utilize os seguintes passos para criar uma nova solução IoT Edge com um módulo de armazenamento de bolhas e para configurar o manifesto de implantação.

1. Selecione **Ver**  >  **Paleta de Comando**.

1. Na paleta de comandos, introduza e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**.

   ![Executar nova solução IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Siga as instruções na paleta de comandos para criar a sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização na sua máquina de desenvolvimento para o Código do Estúdio Visual para criar os ficheiros de solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução ou aceite a **solução edges padrão**. |
   | Selecionar modelo de módulo | Escolha **o módulo existente (Introduza o URL de imagem completo)**. |
   | Indicar um nome para o módulo | Introduza um nome minúsculo para o seu módulo, como **azureblobstorageoniote.**<br/><br/>É importante usar um nome minúsculo para o Azure Blob Storage no módulo IoT Edge. IoT Edge é sensível a casos quando se refere a módulos, e o SDK de armazenamento predefine para minúsculas. |
   | Forneça a imagem do Docker para o módulo | Fornecer a imagem URI: **mcr.microsoft.com/azure-blob-storage:latest** |

   Visual Studio Code pega na informação que forneceu, cria uma solução IoT Edge e, em seguida, carrega-as numa nova janela. O modelo de solução cria um modelo manifesto de implementação que inclui a imagem do módulo de armazenamento de bolhas, mas é necessário configurar as opções de criação do módulo.

1. Abra *deployment.template.jsno* seu novo espaço de trabalho de solução e encontre a secção de **módulos.** Faça as seguintes alterações de configuração:

   1. Elimine o módulo **SimulaçãoSteperatureSensor,** uma vez que não é necessário para esta implementação.

   1. Copiar e colar o seguinte código no `createOptions` campo:

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

      ![Módulo de atualização criaOpções - Código do Estúdio Visual](./media/how-to-deploy-blob/create-options.png)

1. Substitua `<your storage account name>` por um nome de que se lembre. Os nomes das contas devem ter entre 3 a 24 caracteres, com letras e números minúsculos. Sem espaços.

1. `<your storage account key>`Substitua-a por uma tecla base64 de 64 bytes. Pode gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64). Você usará estas credenciais para aceder ao armazenamento de bolhas de outros módulos.

1. Substitua `<storage mount>` de acordo com o seu sistema de funcionamento do contentor. Forneça o nome de um [volume](https://docs.docker.com/storage/volumes/) ou o caminho absoluto para um diretório no seu dispositivo IoT Edge onde pretende que o módulo blob armazene os seus dados. O suporte de armazenamento mapeia uma localização no seu dispositivo que fornece para uma localização definida no módulo.  

     - Para os recipientes Linux, o formato é ** \<your storage path or volume> :/blobroot**. Por exemplo:
         - montagem [de volume de](https://docs.docker.com/storage/volumes/)utilização: `my-volume:/blobroot`
         - utilizar [o suporte do encaixe](https://docs.docker.com/storage/bind-mounts/): `/srv/containerdata:/blobroot` . Certifique-se de seguir as etapas para [conceder acesso ao diretório ao utilizador do contentor](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Para os recipientes windows, o formato é ** \<your storage path or volume> :C:/BlobRoot**. Por exemplo
         - montagem [de volume de](https://docs.docker.com/storage/volumes/)utilização : . `my-volume:C:/BlobRoot` .
         - utilizar [o suporte do encaixe](https://docs.docker.com/storage/bind-mounts/): `C:/ContainerData:C:/BlobRoot` .
         - Em vez de utilizar a sua unidade local, pode mapear a sua localização da rede SMB, para obter mais informações, ver [usando a partilha SMB como o seu armazenamento local](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Não altere a segunda metade do valor do suporte de armazenamento, que aponta para uma localização específica no bloco blob armazenamento no módulo IoT Edge. O suporte de armazenamento deve sempre terminar com **:/blobroot** para recipientes Linux e **:C:/BlobRoot** para recipientes Windows.

1. Configure [o dispositivoToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [dispositivoAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) para o seu módulo adicionando o seguinte JSON à *deployment.template.jsno* ficheiro. Configure cada propriedade com um valor apropriado e guarde o ficheiro. Se estiver a utilizar o simulador IoT Edge, defina os valores para as variáveis ambientais relacionadas para estas propriedades, que pode encontrar na secção de explicação do [dispositivoToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)

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

   ![definir propriedades desejadas para azureblobstorageoniotedge - Código de Estúdio Visual](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   Para obter informações sobre a configuração do dispositivoToCloudUploadProperties e dispositivoAutoDeleteProperties após a implementação do seu módulo, consulte [Editar o Módulo Twin](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Para obter mais informações sobre o recipiente criar opções, reiniciar a política e o estado desejado, consulte [as propriedades desejadas pela EdgeAgent](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Guarde o ficheiro *deployment.template.json*.

1. **Clique**deployment.template.jse selecione **o manifesto de implementação de Aresta de IoT**.

1. O Visual Studio Code retira as informações que forneceu no *deployment.template.js* e utiliza-as para criar um novo ficheiro manifesto de implantação. O manifesto de implantação é criado numa nova pasta **config** no espaço de trabalho da sua solução. Assim que tiver esse ficheiro, pode seguir os passos nos [módulos Deploy Azure IoT Edge a partir do Código do Estúdio Visual](how-to-deploy-modules-vscode.md) ou dos [módulos Deploy Azure IoT Edge com Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Implementar várias instâncias de módulos

Se pretender implementar várias instâncias do Azure Blob Storage no módulo IoT Edge, tem de fornecer uma via de armazenamento diferente e alterar o `HostPort` valor a que o módulo se liga. Os módulos de armazenamento blob expõem sempre a porta 11002 no recipiente, mas pode declarar a que porta está ligada ao hospedeiro.

Editar **Opções de Criar Recipientes** (no portal Azure) ou no campo **createOptions** (no *deployment.template.jsem* ficheiro no Código do Estúdio Visual) para alterar o `HostPort` valor:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Quando ligar a módulos adicionais de armazenamento de bolhas, altere o ponto final para apontar para a porta hospedeira atualizada.

## <a name="configure-proxy-support"></a>Configurar o suporte de proxy

Se a sua organização estiver a utilizar um servidor proxy, terá de configurar suporte de procuração para os módulos de tempo de execução edgeAgent e edgeHub. Este processo envolve duas tarefas:

- Configure os daemons de tempo de execução e o agente IoT Edge no dispositivo.
- Desaprove a variável ambiente HTTPS_PROXY para módulos no ficheiro JSON manifesto de implantação.

Este processo é descrito em [Configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md).

Além disso, um módulo de armazenamento de bolhas também requer a definição HTTPS_PROXY no ficheiro de implantação manifesto. Pode editar diretamente o ficheiro manifesto de implementação ou utilizar o portal Azure.

1. Navegue até ao seu hub de iot no portal Azure e selecione **Iot Edge** a partir do menu do painel esquerdo.

1. Selecione o dispositivo com o módulo para configurar.

1. Selecione **módulos de conjunto**.

1. Na secção **módulos IoT Edge da** página, selecione o módulo de armazenamento de bolhas.

1. Na página do **Módulo IoT Edge de atualização,** selecione o **separador Variáveis ambiente.**

1. Adicione `HTTPS_PROXY` para o **Nome** e o seu URL de procuração para o **Valor**.

      ![Definir variável de ambiente HTTPS_PROXY](./media/how-to-deploy-blob/https-proxy-config.png)

1. Clique **em Atualização**e, em seguida, **Reveja + Criar**.

1. Note que o proxy é adicionado ao módulo no manifesto de implantação e selecione **Criar**.

1. Verifique a definição selecionando o módulo na página de detalhes do dispositivo e na parte inferior da página **IoT Edge Modules Details** selecione o separador **Variáveis ambiente.**

      ![Definir variável de ambiente HTTPS_PROXY](./media/how-to-deploy-blob/verify-proxy-config.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o Azure Blob Storage no IoT Edge](how-to-store-data-blob.md).

Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte como os [módulos IoT Edge podem ser usados, configurados e reutilizados.](module-composition.md)
