---
title: Store blobs de blocos em dispositivos - Azure IoT Edge | Documentos da Microsoft
description: Implemente um módulo de armazenamento de Blobs do Azure para o seu dispositivo IoT Edge para armazenar dados na periferia.
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 03/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 6f82f50ebaa7ad4440078d1fd4658109cf0e19b6
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57571291"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Store dados na periferia com o armazenamento de Blobs do Azure no IoT Edge (pré-visualização)

Armazenamento de Blobs do Azure no IoT Edge fornece uma [blob de blocos](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) solução de armazenamento na periferia. Um módulo de armazenamento de BLOBs no seu dispositivo IoT Edge se comporta como um serviço de BLOBs de bloco do Azure, mas os blobs de bloco são armazenados localmente no seu dispositivo IoT Edge. Pode aceder a blobs com os mesmos métodos SDK de armazenamento do Azure ou bloquear chamadas de API de BLOBs que já está habituado a. 

Este módulo é fornecido com **disposição em camadas automática** e **expiração automática** funcionalidades.

> [!NOTE]
> Atualmente disposição em camadas automática e a funcionalidade de expiração automática só estão disponíveis no Linux AMD64 e ARM32 de Linux.

**Disposição em camadas automática** é uma funcionalidade configurável, o que permite carregar automaticamente os dados do seu armazenamento de BLOBs local para o Azure com suporte de conectividade de internet intermitente. Permite-lhe:
- Ativar ativa/inativa a funcionalidade de camadas
- Escolha a ordem na qual os dados serão copiados para o Azure como NewestFirst ou OldestFirst
- Especifique a conta de armazenamento do Azure para o qual pretende que os dados carregados.
- Especifique os contentores que pretende carregar para o Azure. Este módulo permite-lhe especificar os nomes de contentor de origem e destino.
- Total de blob em camadas (usando `Put Blob` operação) e bloquear a criação de camadas ao nível (usando `Put Block` e `Put Block List` operações).

Este módulo utiliza blocos nível disposição em camadas, quando o blob é composta por blocos. Aqui estão alguns dos cenários comuns:
- Seu aplicativo atualizar alguns blocos de um blob carregado anteriormente, este módulo irá carregar apenas os blocos atualizados e não o blob inteiro.
- O módulo está a carregar blobs e a ligação à internet desaparece, quando a conectividade está de volta novamente irá carregar apenas os blocos restantes e não o blob inteiro.

Se ocorrer um encerramento inesperado do processo (como falha de energia) durante um carregamento de BLOBs, todos os blocos que foram devidos para o carregamento serão novamente, carregados quando o módulo de fica online novamente.

**Expiração automática** é uma funcionalidade configurável em que este módulo elimina automaticamente os blobs do armazenamento local quando expira o tempo Live (TTL). É medido em minutos. Permite-lhe:
- Ativar ativa/inativa a funcionalidade de expiração automática
- Especifique o valor de TTL em minutos

Cenários onde os dados, como vídeos, imagens, dados financeiros, dados de hospital ou quaisquer dados que tem de ser armazenada localmente, mais tarde que poderiam ser processados localmente ou transferidos para a cloud são bons exemplos para utilizar este módulo.

Este artigo fornece instruções para implementar um armazenamento de Blobs do Azure no contêiner do IoT Edge que executa um serviço de BLOBs no seu dispositivo IoT Edge. 

>[!NOTE]
>Armazenamento de Blobs do Azure no IoT Edge está em [pré-visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Veja o vídeo para introdução rápida
> [!VIDEO https://www.youtube.com/embed/wkprcfVidyM]

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Azure IoT Edge:

* Pode seguir os passos no início rápido para [dispositivos Linux](quickstart-linux.md) ou [Windows](quickstart.md) para utilizar o seu computador de desenvolvimento ou uma máquina virtual como um dispositivo Edge.
* Armazenamento de Blobs do Azure no módulo do IoT Edge suporta as seguintes configurações de dispositivo:

   | Sistema operativo | Arquitetura |
   | ---------------- | ------------ |
   | Ubuntu Server 16.04 | AMD64 |
   | Ubuntu Server 18.04 | AMD64 |
   | Windows 10 IoT Core (atualização de Outubro) | AMD64 |
   | Windows 10 IoT Enterprise (atualização de Outubro) | AMD64 |
   | Windows Server de 2019 | AMD64 |
   | Raspbian Stretch | ARM32 |

Recursos da cloud:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão standard no Azure. 


## <a name="deploy-blob-storage-to-your-device"></a>Implementar o armazenamento de BLOBs para o seu dispositivo

Existem várias formas de implementar módulos para um dispositivo IoT Edge e todos eles funcionam para armazenamento de Blobs do Azure em módulos do IoT Edge. Os dois métodos mais simples estão a utilizar os modelos de código do Visual Studio ou o portal do Azure. 

### <a name="azure-portal"></a>Portal do Azure

O Azure Marketplace proporciona o IoT Edge módulos que podem ser implementados diretamente em seus dispositivos IoT Edge, incluindo o armazenamento de Blobs do Azure no IoT Edge. Siga estes passos para implementar o módulo a partir do portal do Azure.

1. Na [portal do Azure](https://portal.azure.com), procure "Azure Blob de armazenamento no IoT Edge". E **selecione** o resultado da pesquisa no Marketplace.

   ![Criar o módulo de pesquisa de mercado](./media/how-to-store-data-blob/marketplace-module.png)

2. Escolha um dispositivo IoT Edge para receber este módulo. Sobre o **dispositivos de destino para o módulo do IoT Edge** página, forneça as seguintes informações:

   1. Selecione o **subscrição** que contém o hub IoT que está a utilizar.

   2. Selecione seu **IoT Hub**.

   3. Se conhece suas **nome de dispositivo do IoT Edge**, introduza-o na caixa de texto. Em alternativa, selecione **localizar dispositivo** para escolher a partir de uma lista de dispositivos do IoT Edge no seu IoT hub. 
   
   4. Selecione **Criar**.

   Agora que optou por um módulo do IoT Edge do Azure Marketplace e escolhido um dispositivo IoT Edge para receber o módulo, é levado para um Assistente de três etapas que o ajuda a definir exatamente como o módulo será implementado.

3. Na **adicionar módulos** passo do Assistente de módulos conjunto, tenha em atenção que o **AzureBlobStorageonIoTEdge** módulo já está listado em **módulos de implementação**. 

2. Selecione o módulo de armazenamento de BLOBs na lista de módulos de implementação para abrir os detalhes do módulo. 

   ![Selecione o nome do módulo para abrir os detalhes de módulos](./media/how-to-store-data-blob/open-module-details.png)

3. Sobre o **módulos personalizados do IoT Edge** página, atualize o armazenamento de Blobs do Azure no módulo do IoT Edge com os seguintes passos:

   1. Alterar o módulo **nome** para estar em minúsculas. Pode mudar o nome do módulo se assim o desejar, ou utilizar `azureblobstorageoniotedge`. 

      >[!IMPORTANT]
      >O Azure IoT Edge diferencia maiúsculas de minúsculas, quando fazer chamadas para módulos e o SDK de armazenamento por predefinição em minúsculas. Para garantir que as suas ligações para o armazenamento de Blobs do Azure no módulo do IoT Edge não sejam interrompidas, dar ele um nome em minúsculas. 

   2. A predefinição **opções de criar contentor** os enlaces de porta que precisa do seu contentor, mas também tem de adicionar as informações da conta de armazenamento e uma ligação para o diretório de armazenamento no seu dispositivo. Substitua o JSON no portal com o JSON abaixo:
    
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
   3. Atualize o JSON que copiou com as seguintes informações: 

      * Substitua `<your storage account name>` com um nome que pode se lembrar. Nomes de contas devem ser três para vinte e quatro carateres com letras minúsculas e números.
      * Substitua `<your storage account key>` com uma chave de base64 de 64 bytes. Pode gerar uma chave com ferramentas como o [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Irá utilizar estas credenciais para aceder ao armazenamento de BLOBs a partir de outros módulos.
      * Substitua `<storage directory bind>` consoante o sistema operativo do contentor. Forneça o nome de um [volume](https://docs.docker.com/storage/volumes/) ou o caminho absoluto para um diretório no seu dispositivo IoT Edge onde pretende que o módulo de BLOBs para armazenar os dados. A associação de diretório de armazenamento mapeia uma localização no seu dispositivo que fornece uma localização do conjunto no módulo. 

         * Contentores do Linux:  **\<caminho de armazenamento >: / blobroot**. Por exemplo, / srv/containerdata: / blobroot. Ou, meu volume: / blobroot. 
         * Contentores do Windows:  **\<caminho de armazenamento >: C: / BlobRoot**. Por exemplo, c: / ContainerData:C: / BlobRoot. Ou, meu-volume: C: / blobroot.
   
      > [!IMPORTANT]
      > Não altere a segunda que metade do diretório de armazenamento vincular o valor, que aponta para uma localização específica no módulo. A associação de diretório de armazenamento sempre deve terminar com **: / blobroot** para contentores do Linux e **: C: / BlobRoot** para contentores do Windows.

      ![Atualização do contentor de módulo criar opções - portais](./media/how-to-store-data-blob/edit-module.png)

   4. Definir [disposição em camadas automática e de expiração automática](#configure-auto-tiering-and-auto-expiration-via-azure-portal) nas propriedades pretendidas. Lista de [disposição em camadas automática](#auto-tiering-properties) e [expiração automática](#auto-expiration-properties) propriedades e os respetivos valores possíveis. 

   5. Selecione **Guardar**. 

4. Selecione **seguinte** para continuar para o passo seguinte do assistente.
5. Na **especificar rotas** passo do assistente, selecione **próxima**.
6. Na **implementação de revisão** passo do assistente, selecione **submeter**.
7. Depois de submeter a implementação, retornar para o **IoT Edge** página do IoT hub. Selecione o dispositivo do IoT Edge direcionados com a implementação para abrir os respetivos detalhes. 
8. Nos detalhes do dispositivo, certifique-se de que o módulo de armazenamento de BLOBs é listado como ambos **especificadas na implementação** e **comunicada pelo dispositivo**. Pode demorar alguns minutos para o módulo ser iniciada no dispositivo e, em seguida, relatado ao IoT Hub. Atualize a página para ver o estado atualizado. 

### <a name="visual-studio-code-templates"></a>Modelos do Visual Studio Code

O Azure IoT Edge fornece modelos no Visual Studio Code para ajudar a desenvolver soluções de ponta. Estes passos requerem que tenha [Visual Studio Code](https://code.visualstudio.com/) instalado no computador de desenvolvimento e configurado com o [ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Utilize os seguintes passos para criar uma nova solução de IoT Edge com um módulo de armazenamento de BLOBs e configure o manifesto de implantação. 

1. Selecione **View** > **paleta de comandos**. 

2. Na paleta de comandos, introduza e execute o comando **Azure IoT Edge: Nova solução de IoT Edge**. Siga as instruções na paleta de comandos para criar a sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução ou aceite a predefinição **EdgeSolution**. |
   | Selecionar modelo de módulo | Escolher **módulo existente (URL de imagem completa de Enter)**. |
   | Indicar um nome para o módulo | Introduza um nome em minúsculas para seu módulo, como **azureblobstorage**.<br><br>É importante utilizar um nome em minúsculas para o armazenamento de Blobs do Azure no módulo do IoT Edge. IoT Edge diferencia maiúsculas de minúsculas quando nos Referimos aos módulos e o SDK de armazenamento por predefinição em minúsculas. |
   | Forneça a imagem do Docker para o módulo | Forneça o URI da imagem: **mcr.microsoft.com/azure-blob-storage:latest** |

   Código VS usa as informações fornecidas, cria uma solução de IoT Edge e, em seguida, carrega-o numa nova janela. O modelo de solução cria um modelo de manifesto de implantação que inclua a sua imagem de módulo de armazenamento de BLOBs, mas tem de configurar opções de criação do módulo. 

3. Open **deployment.template.json** na sua nova área de trabalho da solução e localizar o **módulos** secção. Efetue as seguintes alterações de configuração:

   1. Eliminar a **tempSensor** módulo, como ele não do necessário para esta implementação. 

   2. Copie e cole o código seguinte para o **createOptions** campo do seu módulo de armazenamento de BLOBs: 

      ```json
      "Env": [
        "LOCAL_STORAGE_ACCOUNT_NAME=$STORAGE_ACCOUNT_NAME","LOCAL_STORAGE_ACCOUNT_KEY=$STORAGE_ACCOUNT_KEY"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Atualizar o módulo createOptions - VS Code](./media/how-to-store-data-blob/create-options.png)

4. As opções de criar, JSON, atualizar `<storage directory bind>` consoante o sistema operativo do contentor. Forneça o nome de um [volume](https://docs.docker.com/storage/volumes/) ou o caminho absoluto para um diretório no seu dispositivo IoT Edge onde pretende que o módulo de BLOBs para armazenar os dados. A associação de diretório de armazenamento mapeia uma localização no seu dispositivo que fornece uma localização do conjunto no módulo.  

   * Contentores do Linux:  **\<caminho de armazenamento >: / blobroot**. Por exemplo, / srv/containerdata: / blobroot. Ou, meu volume: / blobroot.
   * Contentores do Windows:  **\<caminho de armazenamento >: C: / BlobRoot**. Por exemplo, c: / ContainerData:C: / BlobRoot. Ou, meu-volume: C: / blobroot.
   
   > [!IMPORTANT]
   > Não altere a segunda que metade do diretório de armazenamento vincular o valor, que aponta para uma localização específica no módulo. A associação de diretório de armazenamento sempre deve terminar com **: / blobroot** para contentores do Linux e **: C: / BlobRoot** para contentores do Windows.

5. Configurar [disposição em camadas automática e de expiração automática](#configure-auto-tiering-and-auto-expiration-via-vscode). Lista de [disposição em camadas automática](#auto-tiering-properties) e [expiração automática](#auto-expiration-properties) propriedades

6. Guarde o ficheiro **deployment.template.json**.

7. Abra o **. env** ficheiro na sua área de trabalho da solução. 

8. O ficheiro. env é configurado para receber as credenciais do registo de contentor, mas não precisa desse para a imagem de armazenamento de BLOBs, uma vez que está disponível ao público. Em vez disso, substitua o ficheiro com duas novas variáveis de ambiente: 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

9. Forneça um valor para `STORAGE_ACCOUNT_NAME`, nomes de contas devem ser três para vinte e quatro carateres com letras minúsculas e números. Forneça uma chave de 64 bytes base64 para o `STORAGE_ACCOUNT_KEY`. Pode gerar uma chave com ferramentas como o [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Irá utilizar estas credenciais para aceder ao armazenamento de BLOBs a partir de outros módulos. 

   Não inclua espaços ou aspas em redor os valores que fornecer. 

10. Guarde o ficheiro **.env**. 

11. Com o botão direito **deployment.template.json** e selecione **manifesto de implantação de gerar o IoT Edge**. 

12. Código do Visual Studio usa as informações que fornecidos no deployment.template.json e. env e utiliza-o para criar um novo arquivo de manifesto de implantação. O manifesto de implantação é criado numa nova **config** pasta na sua área de trabalho da solução. Assim que tiver esse ficheiro, pode seguir os passos em [módulos de implementar o Azure IoT Edge do Visual Studio Code](how-to-deploy-modules-vscode.md) ou [módulos de implementar o Azure IoT Edge com a CLI 2.0 do Azure](how-to-deploy-modules-cli.md).

## <a name="auto-tiering-and-auto-expiration-properties-and-configuration"></a>Disposição em camadas automática e propriedades de expiração automática e configuração

Utilize as propriedades pretendidas para definir propriedades de expiração automática e colocação em camadas automática. Pode ser definidos durante a implementação ou alteradas mais tarde editando o duplo do módulo sem a necessidade de voltar a implementar. Recomendamos que verifique o "módulo duplo" para `reported configuration` e `configurationValidation` para se certificar de que os valores sejam corretamente propagados.

### <a name="auto-tiering-properties"></a>Propriedades de colocação em camadas automática 
O nome desta definição é `tieringSettings`
| Campo | Valores Possíveis | Explicação |
| ----- | ----- | ---- |
| tieringOn | VERDADEIRO, FALSO | Por predefinição está definida `false`, se pretender ativá-la no defini-lo como `true`|
| backlogPolicy | NewestFirst, OldestFirst | Permite-lhe escolher a ordem na qual os dados serão copiados para o Azure. Por predefinição está definida `OldestFirst`. A ordem é determinada pela hora da última modificação de Blob |
| remoteStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` uma cadeia de ligação que permite que especifique a conta de armazenamento do Azure aos quais pretende que os seus dados é carregada. Especifique `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`. Adicionar apropriado EndpointSuffix do Azure onde os dados serão carregados, varia para Global do Azure, Government Azure e o Microsoft Azure Stack. |
| tieredContainers | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Permite-lhe especificar os nomes de contentor que pretende carregar para o Azure. Este módulo permite-lhe especificar os nomes de contentor de origem e destino. Se não especificar o nome do contentor de destino, ele irá atribuir automaticamente o nome do contentor como `<IoTHubName>-<IotEdgeDeviceName>-<ModuleName>-<ContainerName>`. Pode criar cadeias de caracteres de modelo para o nome do contentor de destino, a coluna de valores possíveis de Check-out. <br>* %h -> nome do Hub IoT (3 a 50 carateres). <br>* %d -> (1 a 129 caracteres) de Id de dispositivo de IoT. <br>* %m -> nome do módulo (1 a 64 carateres). <br>* %c -> nome do contentor de origem (3 a 63 carateres). <br><br>Tamanho máximo do nome do contentor é 63 carateres, ao automaticamente atribuir o nome do contentor de destino se o tamanho do contentor de exceder a 63 carateres irá limitar cada seção (IoTHubName, IotEdgeDeviceName, ModuleName, ContainerName) a 15 carateres. |

### <a name="auto-expiration-properties"></a>Propriedades de expiração automática
O nome desta definição é `ttlSettings`
| Campo | Valores Possíveis | Explicação |
| ----- | ----- | ---- |
| ttlOn | VERDADEIRO, FALSO | Por predefinição está definida `false`, se pretender ativá-la no defini-lo como `true`|
| timeToLiveInMinutes | `<minutes>` | Especifique o valor de TTL em minutos. O módulo automaticamente irá eliminar os blobs do armazenamento local quando o valor de TTL expire |

### <a name="configure-auto-tiering-and-auto-expiration-via-azure-portal"></a>Configurar a disposição em camadas automática e de expiração automática através do portal do Azure

Defina as propriedades pretendidas para ativar a disposição em camadas automática e expiração automática, pode definir estes valores:

- **Durante a implementação inicial**: Copie o JSON na **duplo do módulo de conjunto de propriedades pretendidas** caixa. Configurar cada propriedade com o valor apropriado, guardá-lo e continuar com a implementação.

   ```json
   {
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>, 
         "timeToLiveInMinutes": <timeToLiveInMinutes> 
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }

   ```

 ![definir as propriedades de colocação em camadas automática e de expiração automática](./media/how-to-store-data-blob/iotedge_custom_module.png)

- **Depois do módulo é implementado por meio da funcionalidade de "Módulo duplo de identidade"**: Aceda a "Módulo duplo de identidade" deste módulo, copie o JSON em propriedades pretendidas, configurar cada propriedade com o valor apropriado e guardar. No Json de "Módulo duplo de identidade", certifique-se de que sempre que adicionar ou atualizar qualquer pretendido de propriedade, o `reported configuration` secção reflete as alterações e o `configurationValidation` secção comunica êxito para cada propriedade.

   ```json 
    "ttlSettings": {
        "ttlOn": <true, false>, 
        "timeToLiveInMinutes": <timeToLiveInMinutes> 
    },
    "tieringSettings": {
        "tieringOn": <true, false>,
        "backlogPolicy": "<NewestFirst, OldestFirst>",
        "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
        "tieredContainers": {
            "<source container name1>": {
                "target": "<target container name1>"
            }
        }
    }

   ```

![tiering+ttl module_identity_twin](./media/how-to-store-data-blob/module_identity_twin.png) 

### <a name="configure-auto-tiering-and-auto-expiration-via-vscode"></a>Configurar a disposição em camadas automática e de expiração automática através de VSCode

- **Durante a implementação inicial**: Adicionar o abaixo JSON no seu deployment.template.json para definir as propriedades pretendidas para este módulo. Configure cada propriedade com o valor apropriado e guardá-lo.

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>, 
         "timeToLiveInMinutes": <timeToLiveInMinutes> 
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }

   ```

Eis um exemplo de propriedades pretendidas para este módulo: ![definir as propriedades pretendidas para azureblobstorageoniotedge - VS Code](./media/how-to-store-data-blob/tiering_ttl.png)

- **Depois do módulo é implementado por meio de "Duplo do módulo"**: [Editar o módulo duplo](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin) deste módulo, copie o JSON em propriedades pretendidas, configurar cada propriedade com o valor apropriado e guarde. No Json de "Duplo do módulo", certifique-se de que sempre que adicionar ou atualizar qualquer pretendido de propriedade, o `reported configuration` secção reflete as alterações e o `configurationValidation` secção comunica êxito para cada propriedade.

   ```json 
    "ttlSettings": {
        "ttlOn": <true, false>, 
        "timeToLiveInMinutes": <timeToLiveInMinutes> 
    },
    "tieringSettings": {
        "tieringOn": <true, false>,
        "backlogPolicy": "<NewestFirst, OldestFirst>",
        "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
        "tieredContainers": {
            "<source container name1>": {
                "target": "<target container name1>"
            }
        }
    }

   ```

## <a name="connect-to-your-blob-storage-module"></a>Ligar ao seu módulo de armazenamento de BLOBs

Pode utilizar o nome da conta e a chave de conta que configurou para o seu módulo aceder ao armazenamento de BLOBs no seu dispositivo IoT Edge. 

Especifique o seu dispositivo IoT Edge como o ponto final do blob para o armazenamento de quaisquer pedidos que fizer nele. Pode [criar uma cadeia de ligação para um ponto de final de armazenamento explícita](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) usando as informações do dispositivo IoT Edge e o nome da conta que configurou. 

1. Para módulos, o que são implementados no mesmo dispositivo edge onde "Azure Blob de armazenamento no IoT Edge" está em execução, é o ponto final do blob: `http://<module name>:11002/<account name>`. 
2. Para módulos, que são implementadas no dispositivo do edge diferentes, que o dispositivo de limite em que está a executar o "Azure Blob de armazenamento no IoT Edge", então consoante a configuração do ponto final do blob é: `http://<device IP >:11002/<account name>` ou `http://<IoT Edge device hostname>:11002/<account name>` ou `http://<FQDN>:11002/<account name>`

## <a name="deploy-multiple-instances"></a>Implementar várias instâncias

Se pretender implementar várias instâncias do armazenamento de Blobs do Azure no IoT Edge, terá de fornecer o caminho de armazenamento diferente e altere o HostPort que liga o módulo. Os módulos de armazenamento de BLOBs expõem sempre a porta 11002 no contentor, mas pode declarar que porta está vinculado a no anfitrião. 

Editar o módulo criar opções para alterar o valor de HostPort:

```json
\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"<port number>\"}]}
```

Quando se liga aos módulos de armazenamento de BLOBs adicionais, altere o ponto final para apontar para a porta do anfitrião atualizado. 

## <a name="try-it-out"></a>Experimentar

### <a name="azure-blob-storage-quickstart-samples"></a>Exemplos de início rápido de armazenamento de Blobs do Azure
A documentação do armazenamento de Blobs do Azure inclui guias de introdução que fornecem código de exemplo em várias linguagens. Pode executar estes exemplos para testar o armazenamento de Blobs do Azure no IoT Edge ao alterar o ponto final do blob para apontar para o módulo de armazenamento de Blobs. Siga os passos para [ligar ao seu módulo de armazenamento de BLOBs](#connect-to-your-blob-storage-module)

Inícios rápidos utilizarem outros idiomas que também são suportados pela IoT Edge, para que pode implementá-los como módulos do IoT Edge, juntamente com o módulo de armazenamento de BLOBs:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Java](../storage/blobs/storage-quickstart-blobs-java.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md) 

### <a name="azure-storage-explorer"></a>Explorador do Armazenamento do Azure
Também pode tentar [Explorador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para ligar à sua conta de armazenamento local. Tentámos com [versão anterior 1.5.0](https://go.microsoft.com/fwlink/?LinkId=809306&clcid=0x409) do Explorador do Azure.
> [!NOTE]
> Poderá encontrar erros durante a realização de passos abaixo, ignorar e atualizar. 

1. Baixe e instale o Explorador de armazenamento do Azure
2. Ligar ao armazenamento do Azure com uma cadeia de ligação
3. Fornece cadeia de ligação: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`
4. Percorra os passos para ligar.
5. Criar contentor dentro de sua conta de armazenamento local
6. Inicie o carregamento de ficheiros como blobs de blocos.
> [!NOTE]
> Desmarque a caixa de verificação para carregá-lo como blobs de páginas. Este módulo não suporta blobs de páginas. Obterá esta linha de comandos ao carregar ficheiros, como o. ISO, ficheiros. vhd, vhdx ou qualquer grandes.

7. Pode optar por ligar as suas contas de armazenamento do Azure onde está a carregar os dados. Dá-lhe uma vista única para a sua conta de armazenamento local e a conta de armazenamento do Azure

## <a name="supported-storage-operations"></a>Operações de armazenamento suportadas

Módulos de armazenamento de Blobs do IoT Edge utilizam o mesmo SDKs de armazenamento do Azure e são consistentes com a versão 2017-04-17 da API de armazenamento do Azure para pontos finais de blob de bloco. Versões posteriores são dependentes as necessidades dos clientes.

Nem todas as operações de armazenamento de Blobs do Azure são suportadas pelo armazenamento de Blobs do Azure no IoT Edge. A seção a seguir lista as operações suportadas e não suportadas.

### <a name="account"></a>Conta

Suporte para: 
* Listar contentores

Não suportado: 
* Obter e definir as propriedades do serviço de BLOBs
* Solicitação de simulação de blob
* Obter estatísticas do serviço blob
* Obtenha informações de conta

### <a name="containers"></a>Contentores

Suporte para: 
* Criar e eliminar o contentor
* Obter metadados e propriedades do contentor
* Listar blobs
* Obter e definir o contentor de ACL
* Metadados do conjunto de contentor

Não suportado:
* Contentor de concessão

### <a name="blobs"></a>Blobs

Suporte para: 
* Colocar, obter e eliminar BLOBs
* Obter e definir propriedades de blob
* Obter e definir os metadados do blob

Não suportado: 
* Blob de concessão
* Blob de instantâneo
* Copiar e anular o blob de cópia
* Anular a eliminação de BLOBs
* Definir camada de blob

### <a name="block-blobs"></a>Blobs de bloco

Suporte para: 
* Colocar o bloco
* Colocar e obter a lista de bloqueios

Não suportado:
* Colocar o bloco de URL

##<a name="feedback"></a>Comentários:
Seus comentários são muito importantes para nós, para tornar este módulo e as respetivas funcionalidades úteis e fácil de usar. Partilhe os seus comentários e informe-nos como podemos melhorar.

Pode contactar-em absiotfeedback@microsoft.com 

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md)

