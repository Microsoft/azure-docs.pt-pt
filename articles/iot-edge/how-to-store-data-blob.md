---
title: Store blobs de blocos em dispositivos - Azure IoT Edge | Documentos da Microsoft
description: Entenda as camadas e os recursos de vida útil, consulte operações de armazenamento de BLOBs com suporte e conecte-se à sua conta de armazenamento de BLOBs.
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: arduppal
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c90a0351c8c71f4fcafa58a422cc3566a0b29b03
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850100"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Store dados na periferia com o armazenamento de Blobs do Azure no IoT Edge (pré-visualização)

Armazenamento de Blobs do Azure no IoT Edge fornece uma [blob de blocos](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) solução de armazenamento na periferia. Um módulo de armazenamento de BLOBs no seu dispositivo IoT Edge se comporta como um serviço de BLOBs de bloco do Azure, mas os blobs de bloco são armazenados localmente no seu dispositivo IoT Edge. Pode aceder a blobs com os mesmos métodos SDK de armazenamento do Azure ou bloquear chamadas de API de BLOBs que já está habituado a.

Este módulo vem com os recursos **deviceToCloudUpload** e **deviceAutoDelete** .
> [!NOTE]
> Armazenamento de Blobs do Azure no IoT Edge está em [pré-visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Assista ao vídeo para obter uma introdução rápida
> [!VIDEO https://www.youtube.com/embed/QhCYCvu3tiM]

o **deviceToCloudUpload** é uma funcionalidade configurável, que permite que você carregue automaticamente os dados do seu armazenamento de BLOBs local para o Azure com suporte à conectividade intermitente com a Internet. Ele permite que você:

- Ativar/desativar o recurso deviceToCloudUpload.
- Escolha a ordem na qual os dados são copiados para o Azure, como NewestFirst ou OldestFirst.
- Especifique a conta de armazenamento do Azure para a qual você deseja que os dados sejam carregados.
- Especifique os contêineres que você deseja carregar no Azure. Esse módulo permite que você especifique nomes de contêiner de origem e de destino.
- Escolha a capacidade de excluir os BLOBs imediatamente, após a conclusão do carregamento para o armazenamento em nuvem
- Faça upload de blob completo ( `Put Blob` usando operação) e carregamento em nível de `Put Block` bloco `Put Block List` (usando operações e).

Esse módulo usa o carregamento em nível de bloco, quando seu blob é composto por blocos. Aqui estão alguns dos cenários comuns:

- Seu aplicativo atualiza alguns blocos de um blob carregado anteriormente, esse módulo carrega apenas os blocos atualizados e não o blob inteiro.
- O módulo está carregando o blob e a conexão com a Internet desaparece, quando a conectividade está novamente, ele carrega apenas os blocos restantes e não o blob inteiro.

Se um encerramento de processo inesperado (como falha de energia) ocorrer durante um upload de BLOB, todos os blocos que foram devidos ao carregamento serão carregados novamente, quando o módulo voltar a ficar online.

**deviceAutoDelete** é uma funcionalidade configurável em que o módulo exclui automaticamente os BLOBs do armazenamento local quando a duração especificada (medida em minutos) expira. Ele permite que você:

- Ativar/desativar o recurso deviceAutoDelete.
- Especifique o tempo em minutos (deleteAfterMinutes) após o qual os BLOBs serão excluídos automaticamente.
- Escolha a capacidade de reter o blob enquanto ele estiver sendo carregado se o valor de deleteAfterMinutes expirar.

Cenários em que dados como vídeos, imagens, dados de finanças, dados de hospital ou quaisquer dados que precisam ser armazenados localmente, posteriormente que poderiam ser processados localmente ou transferidos para a nuvem, são bons exemplos para usar esse módulo.

Este artigo explica os conceitos relacionados ao armazenamento de BLOBs do Azure no contêiner IoT Edge que executa um serviço blob em seu dispositivo IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Azure IoT Edge:

- Você pode usar seu computador de desenvolvimento ou uma máquina virtual como um dispositivo IoT Edge seguindo as etapas no guia de início rápido para [dispositivos](quickstart.md) [Linux](quickstart-linux.md) ou Windows.

- Armazenamento de Blobs do Azure no módulo do IoT Edge suporta as seguintes configurações de dispositivo:

  | Sistema operativo | AMD64 | ARM32v7 | ARM64 |
  | ---------------- | ----- | ----- | ---- |
  | Raspbian stretch | Não | Sim | Não |  
  | Ubuntu Server 16.04 | Sim | Não | Sim (disponível para [instalação](how-to-install-iot-edge-linux-arm.md#install-a-specific-version) com o [Azure IOT Edge 1.0.8-RC1 e posterior](https://github.com/Azure/azure-iotedge/releases)) |
  | Ubuntu Server 18.04 | Sim | Não | Sim (disponível para [instalação](how-to-install-iot-edge-linux-arm.md#install-a-specific-version) com o [Azure IOT Edge 1.0.8-RC1 e posterior](https://github.com/Azure/azure-iotedge/releases)) |
  | Windows 10 IoT Enterprise, Build 17763 | Sim | Não | Não |
  | Windows Server 2019, Build 17763 | Sim | Não | Não |
  

Recursos da cloud:

Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão standard no Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>Propriedades deviceToCloudUpload e deviceAutoDelete

Use as propriedades desejadas para definir deviceToCloudUploadProperties e deviceAutoDeleteProperties. Eles podem ser definidos durante a implantação ou alterados posteriormente editando-se o módulo e sem a necessidade de reimplantar. É recomendável verificar o "módulo `reported configuration` " e `configurationValidation` para garantir que os valores sejam propagados corretamente.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

O nome dessa configuração é`deviceToCloudUploadProperties`

| Campo | Valores Possíveis | Explicação | Variável de ambiente |
| ----- | ----- | ---- | ---- |
| carregar | true, false | Por padrão, ele é definido `false`como, se você quiser ativá-lo para`true`| `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | Permite que você escolha a ordem na qual os dados são copiados para o Azure. Por padrão, ele é definido `OldestFirst`como. A ordem é determinada pela hora da última modificação do blob | `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`é uma cadeia de conexão que permite especificar a conta de armazenamento do Azure para a qual você deseja que os dados sejam carregados. Especifique `Azure Storage Account Name`, `Azure Storage Account Key`, .`End point suffix` Adicione o EndpointSuffix apropriado do Azure em que os dados serão carregados, varia para o Azure global, o Azure governamental e o Microsoft Azure Stack. | `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Permite que você especifique os nomes de contêiner que deseja carregar no Azure. Esse módulo permite que você especifique nomes de contêiner de origem e de destino. Se você não especificar o nome do contêiner de destino, ele atribuirá automaticamente o nome `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`do contêiner como. Você pode criar cadeias de caracteres de modelo para o nome do contêiner de destino, confira a coluna de valores possíveis. <br>*% h-> nome do Hub IoT (3-50 caracteres). <br>*% d-> IoT Edge ID do dispositivo (1 a 129 caracteres). <br>*% m-> nome do módulo (1 a 64 caracteres). <br>*% c-> nome do contêiner de origem (3 a 63 caracteres). <br><br>O tamanho máximo do nome do contêiner é de 63 caracteres, enquanto atribui automaticamente o nome do contêiner de destino se o tamanho do contêiner exceder 63 caracteres, ele cortará cada seção (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName) como 15 personagens. | `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target: <targetName>` |
| deleteAfterUpload | true, false | Por padrão, ele é definido `false`como. Quando definido como `true`, ele excluirá automaticamente os dados quando o upload para o armazenamento em nuvem for concluído | `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` |


### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

O nome dessa configuração é`deviceAutoDeleteProperties`

| Campo | Valores Possíveis | Explicação | Variável de ambiente |
| ----- | ----- | ---- | ---- |
| deleteOn | true, false | Por padrão, ele é definido `false`como, se você quiser ativá-lo para`true`| `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Especifique o tempo em minutos. O módulo excluirá automaticamente seus BLOBs do armazenamento local quando esse valor expirar | `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true, false | Por padrão, ele é definido `true`como e manterá o blob enquanto estiver carregando para armazenamento em nuvem se deleteAfterMinutes expirar. Você pode defini-lo `false` como e ele excluirá os dados assim que deleteAfterMinutes expirar. Nota: Para que essa propriedade funcione, o upload deve ser definido como true| `deviceAutoDeleteProperties__retainWhileUploading={false,true}` |

## <a name="configure-log-files"></a>Configurar arquivos de log

Para obter informações sobre como configurar arquivos de log para seu módulo, consulte essas [práticas recomendadas de produção](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Ligar ao seu módulo de armazenamento de BLOBs

Pode utilizar o nome da conta e a chave de conta que configurou para o seu módulo aceder ao armazenamento de BLOBs no seu dispositivo IoT Edge.

Especifique o seu dispositivo IoT Edge como o ponto final do blob para o armazenamento de quaisquer pedidos que fizer nele. Pode [criar uma cadeia de ligação para um ponto de final de armazenamento explícita](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) usando as informações do dispositivo IoT Edge e o nome da conta que configurou.

- Para módulos que são implantados no mesmo dispositivo em que o armazenamento de BLOBs do Azure no módulo IoT Edge está em execução, `http://<module name>:11002/<account name>`o ponto de extremidade do blob é:.
- Para módulos ou aplicativos externos em execução em um dispositivo diferente daquele em que o armazenamento de BLOBs do Azure no módulo IoT Edge está em execução, dependendo da configuração de sua rede, de modo que o tráfego de dados do seu módulo ou aplicativo externo possa acessar o dispositivo executando o armazenamento de BLOBs do Azure no módulo IoT Edge, o ponto de extremidade do blob é um de:
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Exemplos de início rápido do armazenamento de BLOBs do Azure

A documentação do armazenamento de BLOBs do Azure inclui o código de exemplo de início rápido em vários idiomas. Você pode executar esses exemplos para testar o armazenamento de BLOBs do Azure em IoT Edge alterando o ponto de extremidade de BLOB para se conectar ao seu módulo de armazenamento de BLOBs local.

Os exemplos de início rápido a seguir usam idiomas que também são suportados pelo IoT Edge, portanto, você pode implantá-los como IoT Edge módulos juntamente com o módulo de armazenamento de BLOBs:

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Conecte-se ao seu armazenamento local com Gerenciador de Armazenamento do Azure

Você pode usar [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para se conectar à sua conta de armazenamento local.

1. Baixar e instalar o Gerenciador de Armazenamento do Azure

1. Conectar-se ao armazenamento do Azure usando uma cadeia de conexão

1. Forneça a cadeia de conexão:`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Percorra as etapas para se conectar.

1. Criar contêiner dentro de sua conta de armazenamento local

1. Comece a carregar arquivos como BLOBs de blocos.
   > [!NOTE]
   > Este módulo não oferece suporte a blobs de página.

1. Você pode optar por conectar suas contas de armazenamento do Azure em que você está carregando os dados. Ele fornece uma exibição única para a conta de armazenamento local e para a conta de armazenamento do Azure

## <a name="supported-storage-operations"></a>Operações de armazenamento suportadas

Os módulos de armazenamento de BLOBs no IoT Edge usam os mesmos SDKs de armazenamento do Azure e são consistentes com a versão 2017-04-17 da API de armazenamento do Azure para pontos de extremidade de blob de blocos. Versões posteriores são dependentes as necessidades dos clientes.

Como nem todas as operações de armazenamento de BLOBs do Azure têm suporte pelo armazenamento de BLOBs do Azure no IoT Edge, esta seção lista o status de cada um.

### <a name="account"></a>Conta

Suporte para:

- Listar contentores

Não suportado:

- Obter e definir as propriedades do serviço de BLOBs
- Solicitação de simulação de blob
- Obter estatísticas do serviço blob
- Obtenha informações de conta

### <a name="containers"></a>Contentores

Suporte para:

- Criar e eliminar o contentor
- Obter metadados e propriedades do contentor
- Listar blobs
- Obter e definir o contentor de ACL
- Metadados do conjunto de contentor

Não suportado:

- Contentor de concessão

### <a name="blobs"></a>Blobs

Suporte para:

- Colocar, obter e eliminar BLOBs
- Obter e definir propriedades de blob
- Obter e definir os metadados do blob

Não suportado:

- Blob de concessão
- Blob de instantâneo
- Copiar e anular o blob de cópia
- Anular a eliminação de BLOBs
- Definir camada de blob

### <a name="block-blobs"></a>Blobs de bloco

Suporte para:

- Colocar bloco
- Colocar e obter a lista de bloqueios

Não suportado:

- Colocar o bloco de URL

## <a name="release-notes"></a>Notas de Versão

Aqui estão as [notas de versão no Hub](https://hub.docker.com/_/microsoft-azure-blob-storage) do Docker para este módulo

## <a name="feedback"></a>Comentários

Seus comentários são importantes para que possamos tornar este módulo e seus recursos úteis e fáceis de usar. Compartilhe seus comentários e informe-nos como podemos melhorar.

Você pode entrar em contato conosco emabsiotfeedback@microsoft.com

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [implantar o armazenamento de BLOBs do Azure no IOT Edge](how-to-deploy-blob.md)
