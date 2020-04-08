---
title: Implemente o armazenamento de blob no módulo para o seu dispositivo - Azure IoT Edge
description: Implemente um módulo de armazenamento De Blob Azure no seu dispositivo IoT Edge para armazenar dados na borda.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: da163e902d06bd98ac47a24256cb809cb222173b
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804627"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Implementar o Armazenamento de Blobs do Azure no módulo do IoT Edge no dispositivo

Existem várias formas de implementar módulos para um dispositivo IoT Edge e todos eles trabalham para o Armazenamento De Blob Azure em módulos IoT Edge. Os dois métodos mais simples são usar os modelos de código do portal Azure ou do Estúdio Visual.

## <a name="prerequisites"></a>Pré-requisitos

- Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) na sua assinatura Azure.
- Um [dispositivo IoT Edge](how-to-register-device.md) com o tempo de execução do IoT Edge instalado.
- [Visual Studio Code](https://code.visualstudio.com/) e as [Ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) se forem implantados a partir do Código do Estúdio Visual.

## <a name="deploy-from-the-azure-portal"></a>Implantação a partir do portal Azure

O portal Azure guia-o através da criação de um manifesto de implantação e empurrando a implementação para um dispositivo IoT Edge.

### <a name="select-your-device"></a>Selecione o seu dispositivo

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue até ao seu hub IoT.
1. Selecione **IoT Edge** do menu.
1. Clique na identificação do dispositivo alvo a partir da lista de dispositivos.»
1. Selecione **Módulos de Conjunto**.

### <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos a implantar, como os dados fluem entre os módulos e as propriedades desejadas dos gémeos módulos. O portal Azure tem um assistente que o acompanha através da criação de um manifesto de implantação. Tem três passos organizados em separadores: **Módulos,** **Rotas**e **Revisão + Criar**.

#### <a name="add-modules"></a>Adicionar módulos

1. Na secção **Módulos IoT Edge** da página, clique no **dropdown e** selecione **IoT Edge Module** para exibir a página Add **IoT Edge Module.**

2. No separador Definições do **Módulo,** forneça um nome para o módulo e, em seguida, especifique a imagem do recipiente URI:

   Exemplos:
  
   - Nome do **módulo IoT Edge:**`azureblobstorageoniotedge`
   - **Imagem URI:**`mcr.microsoft.com/azure-blob-storage:latest`

   ![Definições gémeas do módulo](./media/how-to-deploy-blob/addmodule-tab1.png)

   Não selecione **Adicionar** até especificar valores nas **Definições**do Módulo, **Criar Opções**de Criação de Recipientes e separadores de **Definições Gémeas** do Módulo, conforme descrito neste procedimento.

   > [!IMPORTANT]
   > O Azure IoT Edge é sensível a casos quando faz chamadas para módulos, e o SDK de armazenamento também falha na minúscula. Embora o nome do módulo no [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) seja **AzureBlobStorageonIoTEdge,** mudar o nome para minúscula ajuda a garantir que as suas ligações ao armazenamento de Blob Azure no módulo IoT Edge não sejam interrompidas.

3. Abra o separador Criação de **Recipientes.**

   ![Definições gémeas do módulo](./media/how-to-deploy-blob/addmodule-tab3.png)

   Copie e cole o seguinte JSON na caixa, para fornecer informações da conta de armazenamento e um suporte para o armazenamento no seu dispositivo.
  
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

4. Atualize o JSON que copiou em Opções de Criação de **Contentores** com as seguintes informações:

   - Substitua-o `<your storage account name>` por um nome de que se lembre. Os nomes das contas devem ter 3 a 24 caracteres de comprimento, com letras minúsculas e números. Sem espaços.

   - Substitua-a `<your storage account key>` por uma tecla base 64 byte64. Pode gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64). Você usará estas credenciais para aceder ao armazenamento de bolhas de outros módulos.

   - Substitua de `<storage mount>` acordo com o seu sistema de funcionamento do contentor. Forneça o nome de um [volume](https://docs.docker.com/storage/volumes/) ou o caminho absoluto para um diretório existente no seu dispositivo IoT Edge onde o módulo blob armazenará os seus dados. O suporte de armazenamento mapeia uma localização no seu dispositivo que fornece a uma localização definida no módulo.

     - Para os recipientes Linux, o formato é ** \<o seu caminho de armazenamento ou volume>:/blobroot**. Por exemplo:
         - [montagem de volume](https://docs.docker.com/storage/volumes/)de utilização:`my-volume:/blobroot`
         - utilizar o `/srv/containerdata:/blobroot`suporte de [ligação:](https://docs.docker.com/storage/bind-mounts/). Certifique-se de seguir os passos para [conceder acesso ao diretório ao utilizador do contentor](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Para os recipientes Windows, o formato é ** \<o seu caminho de armazenamento ou volume>:C:/BlobRoot**. Por exemplo:
         - montagem [de](https://docs.docker.com/storage/volumes/) `my-volume:C:/BlobRoot`volume de utilização: .
         - utilizar o `C:/ContainerData:C:/BlobRoot`suporte de [ligação:](https://docs.docker.com/storage/bind-mounts/).
         - Em vez de utilizar a sua unidade local, pode mapear a sua localização da rede SMB, para mais informações, ver [usando a partilha SMB como o seu armazenamento local](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Não altere a segunda metade do valor do suporte de armazenamento, o que aponta para uma localização específica no armazenamento blob no módulo IoT Edge. O suporte de armazenamento deve terminar sempre com **:/blobroot** para recipientes Linux e **:C:/BlobRoot** para recipientes Windows.

5. No separador **Definições Twin Module,** copie o seguinte JSON e cole-o na caixa.

   ![Definições gémeas do módulo](./media/how-to-deploy-blob/addmodule-tab4.png)

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

   Para obter informações sobre o dispositivo de configuraçãoToCloudUploadProperties e dispositivoAutoDeleteProperties depois de o seu módulo ter sido implementado, consulte [Editar o Módulo Twin](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Para mais informações sobre as propriedades desejadas, consulte [Definir ou atualizar as propriedades desejadas](module-composition.md#define-or-update-desired-properties).

6. Selecione **Adicionar**.

7. Selecione **Seguinte: Rotas** para continuar na secção rotas.

#### <a name="specify-routes"></a>Especificar rotas

Mantenha as rotas predefinidas e selecione **Seguinte: Rever + criar** para continuar na secção de revisão.

#### <a name="review-deployment"></a>Revisão da implantação

A secção de revisão mostra-lhe o manifesto de implantação jSON que foi criado com base nas suas seleções nas duas secções anteriores. Há também dois módulos declarados que não adicionou: **$edgeAgent** e **$edgeHub.** Estes dois módulos compõem o tempo de [funcionamento do IoT Edge](iot-edge-runtime.md) e são exigidos predefinidos em todas as implementações.

Reveja as informações de implementação e, em seguida, selecione **Criar**.

### <a name="verify-your-deployment"></a>Verifique a sua implantação

Depois de criar a implementação, volte à página **IoT Edge** do seu hub IoT.

1. Selecione o dispositivo IoT Edge que apontou com a implementação para abrir os seus detalhes.
1. Nos detalhes do dispositivo, verifique se o módulo de armazenamento blob está listado como **especificado na implementação** e **reportado pelo dispositivo**.

Pode levar alguns momentos para o módulo ser iniciado no dispositivo e depois reportado de volta ao IoT Hub. Refresque a página para ver um estado atualizado.

## <a name="deploy-from-visual-studio-code"></a>Implementação do Código do Estúdio Visual

O Azure IoT Edge fornece modelos no Código do Estúdio Visual para ajudá-lo a desenvolver soluções de borda. Utilize os seguintes passos para criar uma nova solução IoT Edge com um módulo de armazenamento blob e para configurar o manifesto de implantação.

1. Selecione **Ver** > Paleta de**Comando**.

1. Na paleta de comandos, introduza e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**.

   ![Executar nova solução de borda iot](./media/how-to-develop-csharp-module/new-solution.png)

   Siga as instruções na paleta de comandos para criar a sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização na sua máquina de desenvolvimento para Visual Studio Code para criar os ficheiros de solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução ou aceite o **EdgeSolution**predefinido . |
   | Selecionar modelo de módulo | Escolha o **Módulo Existente (Introduza o URL de imagem completa)**. |
   | Indicar um nome para o módulo | Introduza um nome minúsculo para o seu módulo, como **azureblobstorageoniotedge**.<br/><br/>É importante usar um nome minúsculo para o armazenamento de blob Azure no módulo IoT Edge. O IoT Edge é sensível a casos quando se refere a módulos, e o SDK de armazenamento não se aplica à minúscula. |
   | Forneça imagem do Docker para o módulo | Fornecer a imagem URI: **mcr.microsoft.com/azure-blob-storage:latest** |

   O Visual Studio Code pega na informação que forneceu, cria uma solução IoT Edge e, em seguida, carrega-a numa nova janela. O modelo de solução cria um modelo de manifesto de implementação que inclui a imagem do módulo de armazenamento de bolhas, mas é necessário configurar as opções de criação do módulo.

1. Abra *a implementação.template.json* no seu novo espaço de trabalho de solução e encontre a secção de **módulos.** Faça as seguintes alterações de configuração:

   1. Elimine o módulo **SimuladoTemperatureSensor,** uma vez que não é necessário para esta implementação.

   1. Copiar e colar o seguinte `createOptions` código no campo:

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

      ![Atualização do módulo criaOpÇões - Código de Estúdio Visual](./media/how-to-deploy-blob/create-options.png)

1. Substitua-o `<your storage account name>` por um nome de que se lembre. Os nomes das contas devem ter 3 a 24 caracteres de comprimento, com letras minúsculas e números. Sem espaços.

1. Substitua-a `<your storage account key>` por uma tecla base 64 byte64. Pode gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64). Você usará estas credenciais para aceder ao armazenamento de bolhas de outros módulos.

1. Substitua de `<storage mount>` acordo com o seu sistema de funcionamento do contentor. Forneça o nome de um [volume](https://docs.docker.com/storage/volumes/) ou o caminho absoluto para um diretório no seu dispositivo IoT Edge onde pretende que o módulo blob guarde os seus dados. O suporte de armazenamento mapeia uma localização no seu dispositivo que fornece a uma localização definida no módulo.  

     - Para os recipientes Linux, o formato é ** \<o seu caminho de armazenamento ou volume>:/blobroot**. Por exemplo:
         - [montagem de volume](https://docs.docker.com/storage/volumes/)de utilização:`my-volume:/blobroot`
         - utilizar o `/srv/containerdata:/blobroot`suporte de [ligação:](https://docs.docker.com/storage/bind-mounts/). Certifique-se de seguir os passos para [conceder acesso ao diretório ao utilizador do contentor](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Para os recipientes Windows, o formato é ** \<o seu caminho de armazenamento ou volume>:C:/BlobRoot**. Por exemplo
         - montagem [de](https://docs.docker.com/storage/volumes/) `my-volume:C:/BlobRoot`volume de utilização: .
         - utilizar o `C:/ContainerData:C:/BlobRoot`suporte de [ligação:](https://docs.docker.com/storage/bind-mounts/).
         - Em vez de utilizar a sua unidade local, pode mapear a sua localização da rede SMB, para mais informações ver [usando a partilha SMB como o seu armazenamento local](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Não altere a segunda metade do valor do suporte de armazenamento, o que aponta para uma localização específica no armazenamento blob no módulo IoT Edge. O suporte de armazenamento deve terminar sempre com **:/blobroot** para recipientes Linux e **:C:/BlobRoot** para recipientes Windows.

1. Configure [o dispositivoToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [dispositivoAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) para o seu módulo adicionando o seguinte JSON ao ficheiro *implementação.template.json.* Configure cada propriedade com um valor adequado e guarde o ficheiro. Se estiver a utilizar o simulador IoT Edge, defina os valores para as variáveis ambientais relacionadas para estas propriedades, que pode encontrar na secção de explicação do [dispositivoToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [dispositivoAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)

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

   ![definir propriedades desejadas para azureblobstorageoniotedge - Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   Para obter informações sobre o dispositivo de configuraçãoToCloudUploadProperties e dispositivoAutoDeleteProperties depois de o seu módulo ter sido implementado, consulte [Editar o Módulo Twin](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Para obter mais informações sobre o recipiente criar opções, reiniciar a política e o estado desejado, consulte [as propriedades desejadas](module-edgeagent-edgehub.md#edgeagent-desired-properties)pelo EdgeAgent .

1. Guarde o ficheiro *deployment.template.json*.

1. Implementação do clique **direito.template.json** e **selecione Generate IoT Edge deployment manifesto**.

1. O Visual Studio Code pega na informação que forneceu em *deployment.template.json* e usa-a para criar um novo ficheiro manifesto de implementação. O manifesto de implantação é criado numa nova pasta **de config** no espaço de trabalho da sua solução. Assim que tiver esse ficheiro, pode seguir os passos em [módulos De implantação Azure IoT Edge a partir do Visual Studio Code](how-to-deploy-modules-vscode.md) ou de implantar [módulos Azure IoT Edge com Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Implementar várias instâncias de módulos

Se pretender implementar várias instâncias do Armazenamento De Blob Azure no módulo IoT Edge, precisa de fornecer um caminho de armazenamento diferente e alterar o `HostPort` valor a que o módulo se liga. Os módulos de armazenamento blob expõem sempre a porta 11002 no recipiente, mas pode declarar a que porta está ligada ao hospedeiro.

Editar **O Recipiente Criar Opções** (no portal Azure) ou o campo **createOptions** (no ficheiro *deployment.template.json* no Visual Studio Code) para alterar o `HostPort` valor:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Quando se ligar a módulos adicionais de armazenamento de bolhas, altere o ponto final para apontar para a porta hospedeira atualizada.

## <a name="configure-proxy-support"></a>Configurar o suporte de proxy

Se a sua organização estiver a utilizar um servidor proxy, terá de configurar o suporte proxy para os módulos de tempo de execução edgeAgent e edgeHub. Este processo envolve duas tarefas:

- Configure os daemons de tempo de execução e o agente IoT Edge no dispositivo.
- Detete a variável ambiente HTTPS_PROXY para módulos no ficheiro JSON manifesto de implantação.

Este processo é descrito na [Configuração de um dispositivo IoT Edge para comunicar através](how-to-configure-proxy-support.md)de um servidor proxy .

Além disso, um módulo de armazenamento de bolhas também requer a definição HTTPS_PROXY no ficheiro de implantação manifesto. Pode editar diretamente o ficheiro manifesto de implantação ou utilizar o portal Azure.

1. Navegue até ao seu hub no portal Azure e selecione **O Edge do** menu do painel esquerdo.

1. Selecione o dispositivo com o módulo para configurar.

1. Selecione **Módulos de Conjunto**.

1. Na secção **Módulos IoT Edge** da página, selecione o módulo de armazenamento blob.

1. Na página **'Módulo 'Borda',** selecione o separador **Variáveis Ambientais.**

1. Adicione `HTTPS_PROXY` para o **Nome** e o seu URL proxy para o **valor**.

      ![Definir HTTPS_PROXY variável ambiental](./media/how-to-deploy-blob/https-proxy-config.png)

1. Clique em **Atualizar,** depois **reveja + criar**.

1. Note que o proxy é adicionado ao módulo no manifesto de implantação e selecione **Criar**.

1. Verifique a definição selecionando o módulo a partir da página de detalhes do dispositivo e na parte inferior da página detalhes dos **módulos IoT Edge** selecione o separador **Variáveis ambientais.**

      ![Definir HTTPS_PROXY variável ambiental](./media/how-to-deploy-blob/verify-proxy-config.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o Armazenamento de Blob Azure na Borda IoT](how-to-store-data-blob.md).

Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [Entenda como os módulos IoT Edge podem ser usados, configurados e reutilizados](module-composition.md).
