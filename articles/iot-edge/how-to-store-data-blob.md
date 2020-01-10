---
title: Store blobs de blocos em dispositivos - Azure IoT Edge | Documentos da Microsoft
description: Entenda as camadas e os recursos de vida útil, consulte operações de armazenamento de BLOBs com suporte e conecte-se à sua conta de armazenamento de BLOBs.
author: kgremban
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3496e0942488a881dbb376d0e53228956e10a7f3
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75729446"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>Armazene dados na borda com o armazenamento de BLOBs do Azure no IoT Edge

O armazenamento de BLOBs do Azure no IoT Edge fornece um [blob de blocos](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) e a solução de armazenamento de [blobs de acréscimo](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) na borda. Um módulo de armazenamento de BLOBs em seu dispositivo IoT Edge se comporta como um serviço blob do Azure, exceto que os BLOBs são armazenados localmente em seu dispositivo IoT Edge. Você pode acessar seus BLOBs usando os mesmos métodos do SDK de armazenamento do Azure ou chamadas de API de BLOB para as quais você já está acostumado. Este artigo explica os conceitos relacionados ao armazenamento de BLOBs do Azure no contêiner IoT Edge que executa um serviço blob em seu dispositivo IoT Edge.

Esse módulo é útil em cenários:

* onde os dados precisam ser armazenados localmente até que possam ser processados ou transferidos para a nuvem. Esses dados podem ser vídeos, imagens, dados de finanças, dados de hospital ou quaisquer outros dados não estruturados.
* Quando os dispositivos estão localizados em um local com conectividade limitada.
* Quando você quiser processar os dados com eficiência localmente para obter acesso de baixa latência aos dados, de modo que você possa responder a emergências o mais rápido possível.
* Quando você quiser reduzir os custos de largura de banda e evitar a transferência de terabytes de dados para a nuvem. Você pode processar os dados localmente e enviar somente os dados processados para a nuvem.

Assista ao vídeo para obter uma introdução rápida
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

Este módulo vem com os recursos **deviceToCloudUpload** e **deviceAutoDelete** .

**deviceToCloudUpload** é uma funcionalidade configurável. Essa função carrega automaticamente os dados do seu armazenamento de BLOBs local para o Azure com suporte à conectividade intermitente com a Internet. Ele permite que você:

* Ativar/desativar o recurso deviceToCloudUpload.
* Escolha a ordem na qual os dados são copiados para o Azure, como NewestFirst ou OldestFirst.
* Especifique a conta de armazenamento do Azure para a qual você deseja que os dados sejam carregados.
* Especifique os contêineres que você deseja carregar no Azure. Esse módulo permite que você especifique nomes de contêiner de origem e de destino.
* Escolha a capacidade de excluir os BLOBs imediatamente, após a conclusão do carregamento para o armazenamento em nuvem
* Faça upload de blob completo (usando `Put Blob` operação) e carregamento em nível de bloco (usando as operações `Put Block`, `Put Block List` e `Append Block`).

Esse módulo usa o carregamento em nível de bloco, quando seu blob é composto por blocos. Aqui estão alguns dos cenários comuns:

* Seu aplicativo atualiza alguns blocos de um blob de blocos carregado anteriormente ou acrescenta novos blocos a um blob de acréscimo, esse módulo carrega apenas os blocos atualizados e não o blob inteiro.
* O módulo está carregando o blob e a conexão com a Internet desaparece, quando a conectividade está novamente, ele carrega apenas os blocos restantes e não o blob inteiro.

Se um encerramento de processo inesperado (como falha de energia) ocorrer durante um upload de BLOB, todos os blocos que foram devidos ao carregamento serão carregados novamente quando o módulo voltar a ficar online.

**deviceAutoDelete** é uma funcionalidade configurável. Essa função exclui automaticamente os BLOBs do armazenamento local quando a duração especificada (medida em minutos) expira. Ele permite que você:

* Ativar/desativar o recurso deviceAutoDelete.
* Especifique o tempo em minutos (deleteAfterMinutes) após o qual os BLOBs serão excluídos automaticamente.
* Escolha a capacidade de reter o blob enquanto ele estiver sendo carregado se o valor de deleteAfterMinutes expirar.

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Azure IoT Edge:

* Você pode usar seu computador de desenvolvimento ou uma máquina virtual como um dispositivo IoT Edge seguindo as etapas no guia de início rápido para [dispositivos](quickstart.md) [Linux](quickstart-linux.md) ou Windows.

* Consulte [Azure IOT Edge sistemas com suporte](support.md#operating-systems) para obter uma lista de sistemas operacionais e arquiteturas com suporte. O armazenamento de BLOBs do Azure no módulo IoT Edge dá suporte às seguintes arquiteturas:
  * Windows AMD64
  * Linux AMD64
  * Linux ARM32
  * Linux ARM64 (versão prévia)

Recursos da cloud:

Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão standard no Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>Propriedades deviceToCloudUpload e deviceAutoDelete

Use as propriedades desejadas do módulo para definir **deviceToCloudUploadProperties** e **deviceAutoDeleteProperties**. As propriedades desejadas podem ser definidas durante a implantação ou alteradas posteriormente editando-se o módulo e sem a necessidade de reimplantar. É recomendável verificar o "módulo"/"para `reported configuration` e `configurationValidation` para garantir que os valores sejam propagados corretamente.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

O nome dessa configuração é `deviceToCloudUploadProperties`. Se você estiver usando o simulador de IoT Edge, defina os valores para as variáveis de ambiente relacionadas para essas propriedades, que podem ser encontradas na seção explicação.

| Propriedade | Valores Possíveis | Explicação |
| ----- | ----- | ---- |
| carregar | VERDADEIRO, FALSO | Defina como `false` por padrão. Se você quiser ativar o recurso, defina esse campo como `true`. <br><br> Variável de ambiente: `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | Permite que você escolha a ordem na qual os dados são copiados para o Azure. Defina como `OldestFirst` por padrão. A ordem é determinada pela hora da última modificação do blob. <br><br> Variável de ambiente: `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` é uma cadeia de conexão que permite especificar a conta de armazenamento para a qual você deseja que os dados sejam carregados. Especifique `Azure Storage Account Name`, `Azure Storage Account Key``End point suffix`. Adicione o EndpointSuffix apropriado do Azure em que os dados serão carregados, varia para o Azure global, o Azure governamental e o Microsoft Azure Stack. <br><br> Você pode optar por especificar a cadeia de conexão SAS do armazenamento do Azure aqui. Mas você precisa atualizar essa propriedade quando ela expirar. <br><br> Variável de ambiente: `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Permite que você especifique os nomes de contêiner que deseja carregar no Azure. Esse módulo permite que você especifique nomes de contêiner de origem e de destino. Se você não especificar o nome do contêiner de destino, ele atribuirá automaticamente o nome do contêiner como `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`. Você pode criar cadeias de caracteres de modelo para o nome do contêiner de destino, confira a coluna de valores possíveis. <br>*% h-> nome do Hub IoT (3-50 caracteres). <br>*% d-> IoT Edge ID do dispositivo (1 a 129 caracteres). <br>*% m-> nome do módulo (1 a 64 caracteres). <br>*% c-> nome do contêiner de origem (3 a 63 caracteres). <br><br>O tamanho máximo do nome do contêiner é de 63 caracteres, enquanto atribui automaticamente o nome do contêiner de destino se o tamanho do contêiner exceder 63 caracteres, ele cortará cada seção (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName) como 15 personagens. <br><br> Variável de ambiente: `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| deleteAfterUpload | VERDADEIRO, FALSO | Defina como `false` por padrão. Quando estiver definido como `true`, ele excluirá automaticamente os dados quando o carregamento para o armazenamento em nuvem for concluído. <br><br> **Cuidado**: se você estiver usando blobs de acréscimo, essa configuração excluirá blobs de acréscimo do armazenamento local após um upload bem-sucedido e quaisquer operações futuras de bloco de acréscimo a esses BLOBs falharão. Use essa configuração com cuidado, não habilite esta opção se seu aplicativo não tiver operações de acréscimo frequentes ou não oferecer suporte a operações de acréscimo contínuas<br><br> Variável de ambiente: `deviceToCloudUploadProperties__deleteAfterUpload={false,true}`. |


### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

O nome dessa configuração é `deviceAutoDeleteProperties`. Se você estiver usando o simulador de IoT Edge, defina os valores para as variáveis de ambiente relacionadas para essas propriedades, que podem ser encontradas na seção explicação.

| Propriedade | Valores Possíveis | Explicação |
| ----- | ----- | ---- |
| deleteOn | VERDADEIRO, FALSO | Defina como `false` por padrão. Se você quiser ativar o recurso, defina esse campo como `true`. <br><br> Variável de ambiente: `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Especifique o tempo em minutos. O módulo excluirá automaticamente os BLOBs do armazenamento local quando esse valor expirar. <br><br> Variável de ambiente: `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | VERDADEIRO, FALSO | Por padrão, ele é definido como `true`e manterá o blob enquanto estiver carregando para armazenamento em nuvem se deleteAfterMinutes expirar. Você pode defini-lo como `false` e ele excluirá os dados assim que deleteAfterMinutes expirar. Observação: para que essa propriedade funcione, o upload deve ser definido como true.  <br><br> **Cuidado**: se você estiver usando blobs de acréscimo, essa configuração excluirá blobs de acréscimo do armazenamento local quando o valor expirar e quaisquer operações futuras de bloco de acréscimo a esses BLOBs falharão. Talvez você queira garantir que o valor de expiração seja grande o suficiente para a frequência esperada de operações de acréscimo executadas pelo seu aplicativo.<br><br> Variável de ambiente: `deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>Usando o compartilhamento SMB como seu armazenamento local
Você pode fornecer o compartilhamento SMB como seu caminho de armazenamento local, ao implantar o contêiner do Windows desse módulo no host do Windows.

Verifique se o compartilhamento SMB e o dispositivo IoT estão em domínios mutuamente confiáveis.

Você pode executar `New-SmbGlobalMapping` comando do PowerShell para mapear o compartilhamento SMB localmente no dispositivo IoT que executa o Windows.

Abaixo estão as etapas de configuração:
```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```
Exemplo: <br>
`$creds = Get-Credential` <br>
`New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G:`

Este comando usará as credenciais para autenticar com o servidor SMB remoto. Em seguida, mapeie o caminho do compartilhamento remoto para a letra da unidade G: (pode ser qualquer outra letra da unidade disponível). O dispositivo IoT agora tem o volume de dados mapeado para um caminho na unidade G:. 

Verifique se o usuário no dispositivo IoT pode ler/gravar no compartilhamento SMB remoto.

Para sua implantação, o valor de `<storage mount>` pode ser **G:/ContainerData: C:/BlobRoot**. 

## <a name="granting-directory-access-to-container-user-on-linux"></a>Concedendo acesso ao diretório para o usuário de contêiner no Linux
Se você tiver usado a [montagem de volume](https://docs.docker.com/storage/volumes/) para armazenamento em suas opções de criação para contêineres do Linux, não precisará realizar etapas adicionais, mas se você usou a montagem de [Associação](https://docs.docker.com/storage/bind-mounts/) , essas etapas serão necessárias para executar o serviço corretamente.

Seguindo o princípio de menos privilégios para limitar os direitos de acesso para os usuários a permissões mínimas necessárias para executar seu trabalho, esse módulo inclui um usuário (nome: Absie, ID: 11000) e um grupo de usuários (nome: Absie, ID: 11000). Se o contêiner for iniciado como **raiz** (o usuário padrão é **raiz**), nosso serviço será iniciado como o usuário **Absie** de baixo privilégio. 

Esse comportamento torna a configuração das permissões em caminho do host associações cruciais para que o serviço funcione corretamente, caso contrário, o serviço falhará com erros de acesso negado. O caminho usado na associação de diretório precisa ser acessível pelo usuário do contêiner (exemplo: Absie 11000). Você pode conceder ao usuário do contêiner acesso ao diretório executando os comandos abaixo no host:

```terminal
sudo chown -R 11000:11000 <blob-dir> 
sudo chmod -R 700 <blob-dir> 
```

Exemplo:<br>
`sudo chown -R 11000:11000 /srv/containerdata` <br>
`sudo chmod -R 700 /srv/containerdata`


Se você precisar executar o serviço como um usuário que não seja o **Absie**, poderá especificar sua ID de usuário personalizada em na propriedade "user" em seu manifesto de implantação. Nesse caso, você precisa usar a ID de grupo raiz ou padrão `0`.

```json
"createOptions": { 
  "User": "<custom user ID>:0" 
} 
```
Agora, conceda ao usuário do contêiner acesso ao diretório
```terminal
sudo chown -R <user ID>:<group ID> <blob-dir> 
sudo chmod -R 700 <blob-dir> 
```

## <a name="configure-log-files"></a>Configurar arquivos de log

Para obter informações sobre como configurar arquivos de log para seu módulo, consulte essas [práticas recomendadas de produção](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Ligar ao seu módulo de armazenamento de BLOBs

Pode utilizar o nome da conta e a chave de conta que configurou para o seu módulo aceder ao armazenamento de BLOBs no seu dispositivo IoT Edge.

Especifique o seu dispositivo IoT Edge como o ponto final do blob para o armazenamento de quaisquer pedidos que fizer nele. Pode [criar uma cadeia de ligação para um ponto de final de armazenamento explícita](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) usando as informações do dispositivo IoT Edge e o nome da conta que configurou.

- Para módulos que são implantados no mesmo dispositivo em que o armazenamento de BLOBs do Azure no módulo IoT Edge está em execução, o ponto de extremidade do blob é: `http://<module name>:11002/<account name>`.
- Para módulos ou aplicativos em execução em um dispositivo diferente, você precisa escolher o ponto de extremidade certo para sua rede. Dependendo da configuração de sua rede, escolha um formato de ponto de extremidade, de modo que o tráfego de dados do seu módulo ou aplicativo externo possa acessar o dispositivo que executa o armazenamento de BLOBs do Azure no módulo IoT Edge. O ponto de extremidade do blob para este cenário é um de:
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Exemplos de início rápido do armazenamento de BLOBs do Azure

A documentação do armazenamento de BLOBs do Azure inclui o código de exemplo de início rápido em vários idiomas. Você pode executar esses exemplos para testar o armazenamento de BLOBs do Azure em IoT Edge alterando o ponto de extremidade de BLOB para se conectar ao seu módulo de armazenamento de BLOBs local.

Os exemplos de início rápido a seguir usam idiomas que também são suportados pelo IoT Edge, portanto, você pode implantá-los como IoT Edge módulos juntamente com o módulo de armazenamento de BLOBs:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
  * As versões anteriores à V 2.1 do SDK do Python têm um problema conhecido em que o módulo não retorna o tempo de criação de BLOB. Devido a esse problema, alguns métodos como BLOBs de lista não funcionam. Como alternativa, defina explicitamente a versão da API no cliente de blob como ' 2017-04-17 '. Exemplo: `block_blob_service._X_MS_VERSION = '2017-04-17'`
  * [Exemplo de acréscimo de BLOB](https://github.com/Azure/azure-storage-python/blob/master/samples/blob/append_blob_usage.py)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-v10.md)
* [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-v10.md)
* [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
* [Go](../storage/blobs/storage-quickstart-blobs-go.md)
* [PHP](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Conecte-se ao seu armazenamento local com Gerenciador de Armazenamento do Azure

Você pode usar [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para se conectar à sua conta de armazenamento local.

1. Transferir e instalar o Explorador de Armazenamento do Azure

1. Conectar-se ao armazenamento do Azure usando uma cadeia de conexão

1. Forneça a cadeia de conexão: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Percorra as etapas para se conectar.

1. Criar contêiner dentro de sua conta de armazenamento local

1. Comece a carregar arquivos como BLOBs de blocos ou BLOBs de acréscimo.
   > [!NOTE]
   > Este módulo não oferece suporte a blobs de página.

1. Você também pode optar por conectar suas contas de armazenamento do Azure em Gerenciador de Armazenamento. Essa configuração fornece uma exibição única para a conta de armazenamento local e para a conta de armazenamento do Azure

## <a name="supported-storage-operations"></a>Operações de armazenamento suportadas

Os módulos de armazenamento de BLOBs no IoT Edge usam os SDKs de armazenamento do Azure e são consistentes com a versão 2017-04-17 da API de armazenamento do Azure para pontos de extremidade de blob de blocos. 

Como nem todas as operações de armazenamento de BLOBs do Azure têm suporte pelo armazenamento de BLOBs do Azure no IoT Edge, esta seção lista o status de cada um.

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

* Colocar bloco
* Colocar e obter a lista de bloqueios

Não suportado:

* Colocar o bloco de URL

### <a name="append-blobs"></a>Blobs de acréscimo

Suporte para:

* Bloco de acréscimo

Não suportado:

* Anexar bloco da URL

## <a name="event-grid-on-iot-edge-integration"></a>Grade de eventos na integração de IoT Edge
> [!CAUTION]
> A integração com a grade de eventos no IoT Edge está em versão prévia

Este armazenamento de BLOBs do Azure no módulo IoT Edge agora fornece integração com a grade de eventos no IoT Edge. Para obter informações detalhadas sobre essa integração, consulte o [tutorial para implantar os módulos, publicar eventos e verificar a entrega de eventos](../event-grid/edge/react-blob-storage-events-locally.md).

## <a name="release-notes"></a>Notas de Lançamento

Aqui estão as [notas de versão no Hub do Docker](https://hub.docker.com/_/microsoft-azure-blob-storage) para este módulo

## <a name="feedback"></a>Comentários

Seus comentários são importantes para que possamos tornar este módulo e seus recursos úteis e fáceis de usar. Compartilhe seus comentários e informe-nos como podemos melhorar.

Você pode entrar em contato conosco em absiotfeedback@microsoft.com

## <a name="next-steps"></a>Passos seguintes

Saiba como [implantar o armazenamento de BLOBs do Azure no IOT Edge](how-to-deploy-blob.md)

Mantenha-se atualizado com as atualizações recentes e o comunicado no [armazenamento de BLOBs do Azure no blog IOT Edge](https://aka.ms/abs-iot-blogpost)
