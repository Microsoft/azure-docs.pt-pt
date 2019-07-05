---
title: Store blobs de blocos em dispositivos - Azure IoT Edge | Documentos da Microsoft
description: Compreender as funcionalidades de camadas e time-to-live, consulte as operações de armazenamento de BLOBs suportados e ligar à sua conta de armazenamento de Blobs.
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: arduppal
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: dabaa06e224c6498c0080c4546c04f40e3919bb6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448539"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Store dados na periferia com o armazenamento de Blobs do Azure no IoT Edge (pré-visualização)

Armazenamento de Blobs do Azure no IoT Edge fornece uma [blob de blocos](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) solução de armazenamento na periferia. Um módulo de armazenamento de BLOBs no seu dispositivo IoT Edge se comporta como um serviço de BLOBs de bloco do Azure, mas os blobs de bloco são armazenados localmente no seu dispositivo IoT Edge. Pode aceder a blobs com os mesmos métodos SDK de armazenamento do Azure ou bloquear chamadas de API de BLOBs que já está habituado a.

Este módulo é fornecido com **deviceToCloudUpload** e **deviceAutoDelete** funcionalidades.
> [!NOTE]
> Armazenamento de Blobs do Azure no IoT Edge está em [pré-visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Veja o vídeo para introdução rápida
> [!VIDEO https://www.youtube.com/embed/QhCYCvu3tiM]

**deviceToCloudUpload** é uma funcionalidade configurável, o que permite carregar automaticamente os dados do seu armazenamento de BLOBs local para o Azure com suporte de conectividade de internet intermitente. Permite-lhe:

- Ativar ativa/inativa a funcionalidade de deviceToCloudUpload.
- Escolha a ordem na qual os dados são copiados para o Azure como NewestFirst ou OldestFirst.
- Especifique a conta de armazenamento do Azure para o qual pretende que os dados carregados.
- Especifique os contentores que pretende carregar para o Azure. Este módulo permite-lhe especificar os nomes de contentor de origem e destino.
- Escolha a capacidade de eliminar os blobs imediatamente, após a conclusão do carregamento para o armazenamento na cloud
- Total de carregamento de BLOBs (usando `Put Blob` operação) e o carregamento de nível de bloco (usando `Put Block` e `Put Block List` operações).

Este módulo utiliza o carregamento de nível de bloco, quando o blob é composta por blocos. Aqui estão alguns dos cenários comuns:

- Seu aplicativo atualizar alguns blocos de um blob carregado anteriormente, este módulo carrega apenas os blocos atualizados e não o blob inteiro.
- O módulo está a carregar blobs e a ligação à internet desaparece, quando a conectividade está de volta novamente carrega apenas os blocos restantes e não o blob inteiro.

Se ocorrer um encerramento inesperado do processo (como falha de energia) durante um carregamento de BLOBs, todos os blocos que foram devidos para o carregamento serão novamente, carregados quando o módulo de fica online novamente.

**deviceAutoDelete** é uma funcionalidade configurável em que o módulo elimina automaticamente os blobs do armazenamento local quando expira a duração especificada (medida em minutos). Permite-lhe:

- Ativar ativa/inativa a funcionalidade de deviceAutoDelete.
- Especifica o tempo em minutos (deleteAfterMinutes) após o qual os blobs serão eliminados automaticamente.
- Escolha a capacidade de reter o blob enquanto está a carregar o se o valor de deleteAfterMinutes expirar.

Cenários onde os dados, como vídeos, imagens, dados financeiros, dados de hospital ou quaisquer dados que tem de ser armazenada localmente, mais tarde que poderiam ser processados localmente ou transferidos para a cloud são bons exemplos para utilizar este módulo.

Este artigo explica os conceitos relacionados com o armazenamento de Blobs do Azure no contêiner do IoT Edge que executa um serviço de BLOBs no seu dispositivo IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Azure IoT Edge:

- Pode utilizar o seu computador de desenvolvimento ou uma máquina virtual como um dispositivo IoT Edge ao seguir os passos no guia de introdução para [Linux](quickstart-linux.md) ou [dispositivos Windows](quickstart.md).

- Armazenamento de Blobs do Azure no módulo do IoT Edge suporta as seguintes configurações de dispositivo:

  | Sistema operativo | Arquitetura |
  | ---------------- | ----- | ----- |
  | Ubuntu Server 16.04 | AMD64 |
  | Ubuntu Server 18.04 | AMD64 |
  | Windows 10 IoT Enterprise | AMD64 |
  | Windows Server de 2019 | AMD64 |
  | Raspbian stretch | ARM32 |

Recursos da cloud:

Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão standard no Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>Propriedades deviceToCloudUpload e deviceAutoDelete

Utilize as propriedades pretendidas para definir deviceToCloudUploadProperties e deviceAutoDeleteProperties. Pode ser definidos durante a implementação ou alteradas mais tarde editando o duplo do módulo sem a necessidade de voltar a implementar. Recomendamos que verifique o "módulo duplo" para `reported configuration` e `configurationValidation` para se certificar de que os valores sejam corretamente propagados.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

O nome desta definição é `deviceToCloudUploadProperties`

| Campo | Valores Possíveis | Explicação | Variável de ambiente |
| ----- | ----- | ---- | ---- |
| uploadOn | true, false | Por predefinição está definida `false`, se pretender ativá-la no defini-lo como `true`| `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | Permite-lhe escolher a ordem na qual os dados são copiados para o Azure. Por predefinição está definida `OldestFirst`. A ordem é determinada pela hora da última modificação de Blob | `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` uma cadeia de ligação que permite que especifique a conta de armazenamento do Azure aos quais pretende que os seus dados é carregada. Especifique `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`. Adicionar apropriado EndpointSuffix do Azure onde os dados serão carregados, varia para Global do Azure, Government Azure e o Microsoft Azure Stack. | `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Permite-lhe especificar os nomes de contentor que pretende carregar para o Azure. Este módulo permite-lhe especificar os nomes de contentor de origem e destino. Se não especificar o nome do contentor de destino, ele irá atribuir automaticamente o nome do contentor como `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`. Pode criar cadeias de caracteres de modelo para o nome do contentor de destino, consulte a coluna de valores possíveis. <br>* %h -> nome do Hub IoT (3 a 50 carateres). <br>* %d -> ID de dispositivo do IoT Edge (1 a 129 caracteres). <br>* %m -> nome do módulo (1 a 64 carateres). <br>* %c -> nome do contentor de origem (3 a 63 carateres). <br><br>O tamanho máximo do nome do contentor é 63 carateres, ao automaticamente atribuir o nome do contentor de destino se o tamanho do contentor de exceder a 63 carateres irá limitar cada seção (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName) para 15 carateres. | `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target: <targetName>` |
| deleteAfterUpload | true, false | Por predefinição está definida `false`. Quando é definido como `true`, será automaticamente eliminar os dados quando a conclusão do carregamento para o armazenamento na cloud | `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` |


### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

O nome desta definição é `deviceAutoDeleteProperties`

| Campo | Valores Possíveis | Explicação | Variável de ambiente |
| ----- | ----- | ---- | ---- |
| deleteOn | true, false | Por predefinição está definida `false`, se pretender ativá-la no defini-lo como `true`| `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Especifique o tempo em minutos. O módulo automaticamente irá eliminar os blobs do armazenamento local quando este valor expirar | `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true, false | Por predefinição está definida `true`, e ele irá reter o blob enquanto é carregar para o armazenamento na cloud se deleteAfterMinutes expirar. Pode defini-la `false` e vai eliminar os dados assim que deleteAfterMinutes expira. Nota: Para essa propriedade funcione uploadOn deve ser definida como verdadeiro| `deviceAutoDeleteProperties__retainWhileUploading={false,true}` |

## <a name="configure-log-files"></a>Configurar ficheiros de registo

Para obter informações sobre como configurar ficheiros de registo para seu módulo, vê-los [melhores práticas de produção](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Ligar ao seu módulo de armazenamento de BLOBs

Pode utilizar o nome da conta e a chave de conta que configurou para o seu módulo aceder ao armazenamento de BLOBs no seu dispositivo IoT Edge.

Especifique o seu dispositivo IoT Edge como o ponto final do blob para o armazenamento de quaisquer pedidos que fizer nele. Pode [criar uma cadeia de ligação para um ponto de final de armazenamento explícita](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) usando as informações do dispositivo IoT Edge e o nome da conta que configurou.

- Para os módulos que são implementados no mesmo dispositivo, como em que o armazenamento de Blobs do Azure no módulo do IoT Edge é executado, é o ponto final do blob: `http://<module name>:11002/<account name>`.
- Para módulos externos ou aplicações são executadas num dispositivo diferente do que em que o armazenamento de Blobs do Azure no módulo do IoT Edge está em execução, em seguida, consoante a configuração de rede, de modo a que o tráfego de dados do seu módulo externo ou uma aplicação possa alcançar o dispositivo a executar o armazenamento de Blobs do Azure no módulo do IoT Edge, o ponto final do blob é um dos:
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Exemplos de início rápido de armazenamento de Blobs do Azure

A documentação do armazenamento de Blobs do Azure inclui código de exemplo de início rápido em várias linguagens. Pode executar estes exemplos para testar o armazenamento de Blobs do Azure no IoT Edge ao alterar o ponto final do blob para ligar ao seu módulo de armazenamento de BLOBs locais.

Os seguintes exemplos de início rápido utilizarem outros idiomas que também são suportados pela IoT Edge, para que pode implementá-los como módulos do IoT Edge, juntamente com o módulo de armazenamento de BLOBs:

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Ligar ao seu armazenamento local com o Explorador de armazenamento do Azure

Pode usar [Explorador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para ligar à sua conta de armazenamento local.

1. Baixe e instale o Explorador de armazenamento do Azure

1. Ligar ao armazenamento do Azure com uma cadeia de ligação

1. Fornece cadeia de ligação: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Percorra os passos para ligar.

1. Criar contentor dentro de sua conta de armazenamento local

1. Inicie o carregamento de ficheiros como blobs de blocos.
   > [!NOTE]
   > Este módulo não suporta blobs de páginas.

1. Pode optar por ligar as suas contas de armazenamento do Azure onde está a carregar os dados. Dá-lhe uma vista única para a sua conta de armazenamento local e a conta de armazenamento do Azure

## <a name="supported-storage-operations"></a>Operações de armazenamento suportadas

Módulos de armazenamento de Blobs do IoT Edge utilizam o mesmo SDKs de armazenamento do Azure e são consistentes com a versão 2017-04-17 da API de armazenamento do Azure para pontos finais de blob de bloco. Versões posteriores são dependentes as necessidades dos clientes.

Uma vez que nem todas as operações de armazenamento de Blobs do Azure são suportadas pelo armazenamento de Blobs do Azure no IoT Edge, esta secção lista o estado de cada.

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

- Colocar o bloco
- Colocar e obter a lista de bloqueios

Não suportado:

- Colocar o bloco de URL

## <a name="release-notes"></a>Notas de Versão

Seguem-se a [notas no docker hub](https://hub.docker.com/_/microsoft-azure-blob-storage) para este módulo

## <a name="feedback"></a>Comentários

Seus comentários são importantes para nós para tornar este módulo e as respetivas funcionalidades úteis e fácil de usar. Partilhe os seus comentários e informe-nos como podemos melhorar.

Pode contatá-nos em absiotfeedback@microsoft.com

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [implementar o armazenamento de Blobs do Azure no IoT Edge](how-to-deploy-blob.md)
