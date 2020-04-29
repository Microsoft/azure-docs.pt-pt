---
title: Guarde bolhas de blocos em dispositivos - Azure IoT Edge Microsoft Docs
description: Compreenda as funcionalidades de tiering e time-to-live, consulte operações de armazenamento de blob suportadas e ligue-se à sua conta de armazenamento blob.
author: kgremban
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: bea00f429f31f2be62ee6a9c00f88873c595d94c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76509823"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>Armazenar dados na periferia com o Armazenamento de Blobs do Azure no IoT Edge

O armazenamento de blob azure em IoT Edge fornece uma solução de armazenamento de [blocos blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) e [apêndice](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) na borda. Um módulo de armazenamento de bolhas no seu dispositivo IoT Edge comporta-se como um serviço de blob Azure, exceto que as bolhas são armazenadas localmente no seu dispositivo IoT Edge. Pode aceder às suas bolhas utilizando os mesmos métodos De armazenamento Azure SDK ou chamadas de API blob a que já está habituado. Este artigo explica os conceitos relacionados com o Armazenamento De Blob Azure no recipiente IoT Edge que executa um serviço de blob no seu dispositivo IoT Edge.

Este módulo é útil em cenários:

* onde os dados precisam de ser armazenados localmente até que possam ser processados ou transferidos para a nuvem. Estes dados podem ser vídeos, imagens, dados financeiros, dados hospitalares ou quaisquer outros dados não estruturados.
* quando os dispositivos estão localizados num local com conectividade limitada.
* quando pretende processar eficientemente os dados localmente para obter um acesso de baixa latência aos dados, de modo a que possa responder a emergências o mais rapidamente possível.
* quando pretende reduzir os custos de largura de banda e evitar a transferência de terabytes de dados para a nuvem. Pode processar os dados localmente e enviar apenas os dados processados para a nuvem.

Veja o vídeo para uma introdução rápida
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

Este módulo vem com **dispositivoToCloudUpload** e **dispositivoAutoDelete.**

**dispositivoToCloudUpload** é uma funcionalidade configurável. Esta função envia automaticamente os dados do seu armazenamento de blob local para O Azure com suporte intermitente de conectividade à Internet. Permite-lhe:

* Ligue/DESLIGUE a função toCloudUpload do dispositivo.
* Escolha a ordem em que os dados são copiados para Azure como NewestFirst ou First.
* Especifique a conta de Armazenamento Azure para a qual pretende que os seus dados sejam carregados.
* Especifique os recipientes que pretende enviar para O Azure. Este módulo permite especificar os nomes de contentores de origem e alvo.
* Escolha a capacidade de eliminar as bolhas imediatamente, depois de o upload para o armazenamento em nuvem estar terminado
* Faça o upload completo `Put Blob` da bolha (utilizando `Put Block`o `Put Block List` `Append Block` funcionamento) e o carregamento do nível do bloco (utilizando , e operações).

Este módulo utiliza o upload do nível do bloco, quando a sua bolha é composta por blocos. Eis alguns dos cenários comuns:

* A sua aplicação atualiza alguns blocos de uma bolha de bloco previamente carregada ou anexa novos blocos para uma bolha de apêndice, este módulo carrega apenas os blocos atualizados e não toda a bolha.
* O módulo está a carregar blob e a ligação à internet desaparece, quando a conectividade está de volta, carrega apenas os blocos restantes e não toda a bolha.

Se uma rescisão inesperada de processo (como falha de energia) ocorrer durante um upload de blob, todos os blocos que deveriam ser carregados serão novamente carregados assim que o módulo voltar a funcionar.

**dispositivoAutoDelete** é uma funcionalidade configurável. Esta função elimina automaticamente as suas bolhas do armazenamento local quando a duração especificada (medida em minutos) expirar. Permite-lhe:

* Ligue/DESLIGUE a função AutoDelete do dispositivo.
* Especifique o tempo em minutos (eliminarAfterMinutes) após o qual as bolhas serão automaticamente eliminadas.
* Escolha a capacidade de reter a bolha enquanto está a carregar se o valor eliminar AfterMinutes expirar.

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Azure IoT Edge:

* Pode utilizar a sua máquina de desenvolvimento ou uma máquina virtual como um dispositivo IoT Edge seguindo os passos no arranque rápido para [dispositivos](quickstart.md) [Linux](quickstart-linux.md) ou Windows .

* Consulte os [sistemas suportados pelo Azure IoT Edge](support.md#operating-systems) para obter uma lista de sistemas operativos e arquiteturas suportados. O armazenamento de Blob Azure no módulo IoT Edge suporta as seguintes arquiteturas:
  * Janelas AMD64
  * Linux AMD64
  * Linux ARM32
  * Linux ARM64 (pré-visualização)

Recursos da cloud:

Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão standard no Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>dispositivoToCloudUpload e dispositivoAutoDelete propriedades

Utilize as propriedades desejadas do módulo para definir **o dispositivoToCloudUploadProperties** e **dispositivoAutoDeleteProperties**. As propriedades desejadas podem ser definidas durante a implementação ou alteradas posteriormente, editando o módulo gémeo sem a necessidade de reimplantação. Recomendamos verificar o "Módulo `reported configuration` Twin" e `configurationValidation` para garantir que os valores são corretamente propagados.

### <a name="devicetoclouduploadproperties"></a>dispositivoToCloudUploadProperties

O nome deste `deviceToCloudUploadProperties`cenário é . Se estiver a utilizar o simulador IoT Edge, detete os valores para as variáveis ambientais relacionadas para estas propriedades, que pode encontrar na secção de explicação.

| Propriedade | Valores Possíveis | Explicação |
| ----- | ----- | ---- |
| uploadOn | TRUE, false | Definido `false` por padrão. Se quiser ligar a funcionalidade, desloque este campo para `true`. <br><br> Variável ambiental:`deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, Mais Velho Primeiro | Permite-lhe escolher a ordem em que os dados são copiados para o Azure. Definido `OldestFirst` por padrão. A ordem é determinada pela última vez modificada de Blob. <br><br> Variável ambiental:`deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`é uma cadeia de ligação que lhe permite especificar a conta de armazenamento à qual deseja que os seus dados sejam carregados. `Azure Storage Account Name`Especificar, `Azure Storage Account Key` `End point suffix`. Adicione endpointSuffix apropriado do Azure onde os dados serão carregados, varia para Global Azure, Government Azure e Microsoft Azure Stack. <br><br> Pode optar por especificar a cadeia de ligação Azure Storage SAS aqui. Mas tem que atualizar esta propriedade quando expirar. <br><br> Variável ambiental:`deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| recipientes de armazenamentoForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Permite especificar os nomes do contentor que pretende enviar para o Azure. Este módulo permite especificar os nomes de contentores de origem e alvo. Se não especificar o nome do recipiente-alvo, atribuirá automaticamente `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`o nome do recipiente como . Pode criar cordas de modelo para o nome do recipiente-alvo, verificar a possível coluna de valores. <br>* %h -> Nome do Hub IoT (3-50 caracteres). <br>* %d -> IoT Edge Device ID (1 a 129 caracteres). <br>* %m -> Nome do módulo (1 a 64 caracteres). <br>* %c -> Nome do Recipiente de Origem (3 a 63 caracteres). <br><br>O tamanho máximo do nome do recipiente é de 63 caracteres, atribuindo automaticamente o nome do recipiente-alvo se o tamanho do recipiente exceder 63 caracteres que irá aparar cada secção (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName) a 15 caracteres. <br><br> Variável ambiental:`deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| eliminarAfterUpload | TRUE, false | Definido `false` por padrão. Quando estiver programado para `true`, eliminará automaticamente os dados quando o upload para o armazenamento em nuvem estiver terminado. <br><br> **ATENÇÃO**: Se estiver a utilizar bolhas de apêndice, esta definição eliminará as bolhas de apêndice do armazenamento local após um upload bem sucedido, e quaisquer futuras operações do Bloco de Apêndice para essas bolhas falharão. Utilize esta definição com cuidado, não o ative se a sua aplicação fizer operações de apêndice pouco frequentes ou se não suportar operações contínuas de apêndice<br><br> Variável `deviceToCloudUploadProperties__deleteAfterUpload={false,true}`ambiental: . |

### <a name="deviceautodeleteproperties"></a>dispositivoAutoDeleteProperties

O nome deste `deviceAutoDeleteProperties`cenário é . Se estiver a utilizar o simulador IoT Edge, detete os valores para as variáveis ambientais relacionadas para estas propriedades, que pode encontrar na secção de explicação.

| Propriedade | Valores Possíveis | Explicação |
| ----- | ----- | ---- |
| excluirOn | TRUE, false | Definido `false` por padrão. Se quiser ligar a funcionalidade, desloque este campo para `true`. <br><br> Variável ambiental:`deviceAutoDeleteProperties__deleteOn={false,true}` |
| excluirAfterMinutes | `<minutes>` | Especifique a hora em minutos. O módulo eliminará automaticamente as suas bolhas do armazenamento local quando este valor expirar. <br><br> Variável ambiental:`deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| reterO Uploading | TRUE, false | Por predefinição, `true`está programado para , e irá manter a bolha enquanto está a carregar para armazenamento em nuvem se o exclusorexpirar. Pode defini-lo `false` e eliminará os dados assim que expirar o Apague o AfterMinutes. Nota: Para que esta propriedade funcione, o uploadOn deve ser definido como verdadeiro.  <br><br> **ATENÇÃO**: Se estiver a utilizar bolhas de apêndice, esta definição eliminará as bolhas de apêndice do armazenamento local quando o valor expirar, e quaisquer futuras operações do Bloco apêndice a essas bolhas falharão. É dever certificar-se de que o valor de validade é suficientemente grande para a frequência esperada das operações de apêndice realizadas pela sua aplicação.<br><br> Variável ambiental:`deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>Usando a partilha de SMB como o seu armazenamento local

Pode fornecer a partilha de SMB como a sua trajetória de armazenamento local, quando implementar o recipiente windows deste módulo no anfitrião do Windows.

Certifique-se de que a partilha SMB e o dispositivo IoT estão em domínios mutuamente confiáveis.

Pode executar `New-SmbGlobalMapping` o comando PowerShell para mapear a partilha SMB localmente no dispositivo IoT que executa o Windows.

Abaixo estão os passos de configuração:

```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```

Por exemplo:

```powershell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G:
```

Este comando utilizará as credenciais para autenticar com o servidor SMB remoto. Em seguida, mapeie o caminho de partilha remota para G: carta de unidade (pode ser qualquer outra letra de unidade disponível). O dispositivo IoT tem agora o volume de dados mapeado para um caminho no G: unidade.

Certifique-se de que o utilizador no dispositivo IoT pode ler/escrever para a partilha remota de SMB.

Para a sua `<storage mount>` implementação, o valor pode ser **G:/ContainerData:C:/BlobRoot**.

## <a name="granting-directory-access-to-container-user-on-linux"></a>Concessão de acesso ao diretório ao utilizador de contentores em Linux

Se utilizou o suporte de [volume](https://docs.docker.com/storage/volumes/) para armazenamento nas suas opções de criação para os recipientes Linux, então não tem de fazer quaisquer passos extras, mas se usou o suporte de [ligação,](https://docs.docker.com/storage/bind-mounts/) então estes passos são necessários para executar o serviço corretamente.

Seguindo o princípio do menor privilégio de limitar os direitos de acesso dos utilizadores a permissões mínimas de que necessitam para realizar o seu trabalho, este módulo inclui um utilizador (nome: absie, ID: 11000) e um grupo de utilizadores (nome: absie, ID: 11000). Se o recipiente for iniciado como **raiz** (o utilizador predefinido é **raiz),** o nosso serviço será iniciado como o utilizador **absie** de baixo privilégio.

Este comportamento torna a configuração das permissões no caminho do hospedeiro que se liga crucial para que o serviço funcione corretamente, caso contrário o serviço irá falhar com erros de acesso negados. O caminho utilizado na encadernação do diretório tem de ser acessível pelo utilizador do contentor (exemplo: absie 11000). Pode conceder ao utilizador do contentor acesso ao diretório executando os comandos abaixo no anfitrião:

```terminal
sudo chown -R 11000:11000 <blob-dir>
sudo chmod -R 700 <blob-dir>
```

Por exemplo:

```terminal
sudo chown -R 11000:11000 /srv/containerdata
sudo chmod -R 700 /srv/containerdata
```

Se necessitar de executar o serviço como um utilizador que não seja **o absie,** pode especificar o seu ID de utilizador personalizado na createOptions no âmbito da propriedade "User" no seu manifesto de implementação. Neste caso, é necessário utilizar o `0`ID padrão ou de grupo raiz .

```json
"createOptions": {
  "User": "<custom user ID>:0"
}
```

Agora, conceda ao utilizador do contentor acesso ao diretório

```terminal
sudo chown -R <user ID>:<group ID> <blob-dir>
sudo chmod -R 700 <blob-dir>
```

## <a name="configure-log-files"></a>Configure ficheiros de registo

Para obter informações sobre a configuração de ficheiros de registo para o seu módulo, consulte estas [boas práticas](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics)de produção.

## <a name="connect-to-your-blob-storage-module"></a>Ligue-se ao seu módulo de armazenamento de bolhas

Pode utilizar o nome da conta e a chave de conta que configurapara o seu módulo aceder ao armazenamento de blob no seu dispositivo IoT Edge.

Especifique o seu dispositivo IoT Edge como ponto final da bolha para quaisquer pedidos de armazenamento que faça. Pode criar uma cadeia de [ligação para um ponto final de armazenamento explícito](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) utilizando as informações do dispositivo IoT Edge e o nome da conta que configura.

* Para os módulos que são implantados no mesmo dispositivo que o armazenamento de blob Azure no `http://<module name>:11002/<account name>`módulo IoT Edge, o ponto final da bolha é: .
* Para módulos ou aplicações em execução num dispositivo diferente, tem de escolher o ponto final certo para a sua rede. Dependendo da configuração da sua rede, escolha um formato de ponto final de modo a que o tráfego de dados do seu módulo ou aplicação externo possa chegar ao dispositivo que executa o Armazenamento De Blob Azure no módulo IoT Edge. O ponto final blob para este cenário é um dos:
  * `http://<device IP >:11002/<account name>`
  * `http://<IoT Edge device hostname>:11002/<account name>`
  * `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Amostras de arranque rápido de armazenamento de Blob Azure

A documentação de armazenamento de Blob Azure inclui código de amostra de arranque rápido em vários idiomas. Pode executar estas amostras para testar o armazenamento de Blob Azure no IoT Edge, alterando o ponto final da bolha para se ligar ao módulo de armazenamento de blob local.

As seguintes amostras de quickstart utilizam idiomas que também são suportados pelo IoT Edge, para que possa implantá-las como módulos IoT Edge ao lado do módulo de armazenamento de bolhas:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
  * As versões antes da V2.1 do Python SDK têm um problema conhecido onde o módulo não devolve o tempo de criação da bolha. Por causa desta questão, alguns métodos como as listas blobs não funcionam. Como supõe,definiu explicitamente a versão API no cliente blob para '2017-04-17'. Exemplo:`block_blob_service._X_MS_VERSION = '2017-04-17'`
  * [Amostra de bolhas de apêndice](https://github.com/Azure/azure-storage-python/blob/master/samples/blob/append_blob_usage.py)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-legacy.md)
* [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-legacy.md)
* [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
* [Ir](../storage/blobs/storage-quickstart-blobs-go.md)
* [PHP](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Conecte-se ao seu armazenamento local com o Azure Storage Explorer

Pode utilizar o [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) para se ligar à sua conta de armazenamento local.

1. Transferir e instalar o Explorador de Armazenamento do Azure

1. Ligue-se ao Armazenamento Azure utilizando uma corda de ligação

1. Fornecer cadeia de ligação:`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Atravesse os degraus para se conectar.

1. Crie um recipiente dentro da sua conta de armazenamento local

1. Comece a carregar ficheiros como Blocos ou Blocos de Apêndice.
   > [!NOTE]
   > Este módulo não suporta bolhas de página.

1. Também pode optar por ligar as suas contas de armazenamento Azure no Storage Explorer. Esta configuração dá-lhe uma única vista tanto para a sua conta de armazenamento local como para a conta de armazenamento Azure

## <a name="supported-storage-operations"></a>Operações de armazenamento apoiadas

Os módulos de armazenamento blob no IoT Edge utilizam os SDKs de Armazenamento Azure, e são consistentes com a versão 2017-04-17 da API de Armazenamento Azure para pontos finais de blocos.

Como nem todas as operações de armazenamento de Blob Azure são suportadas pelo Armazenamento De Blob Azure na Borda IoT, esta secção lista o estado de cada um.

### <a name="account"></a>Conta

Apoiado:

* Listar contentores

Sem apoio:

* Obtenha e definir propriedades de serviço blob
* Pedido de bolha pré-voo
* Obtenha estatísticas de serviço blob
* Obter informações sobre contas

### <a name="containers"></a>Contentores

Apoiado:

* Criar e eliminar recipientes
* Obtenha propriedades de contentores e metadados
* Listar blobs
* Obter e definir recipiente ACL
* Definir metadados de contentores

Sem apoio:

* Contentor de arrendamento

### <a name="blobs"></a>Blobs

Apoiado:

* Colocar, obter e apagar blob
* Obter e definir propriedades blob
* Obter e definir metadados blob

Sem apoio:

* Arrendamento blob
* Bolha instantânea
* Copiar e abortar a bolha de cópia
* Bolha de exclusão
* Definir nível de blob

### <a name="block-blobs"></a>Blobs de blocos

Apoiado:

* Coloque o bloco
* Colocar e obter lista de blocos

Sem apoio:

* Bloqueie a URL

### <a name="append-blobs"></a>Blobs de acréscimo

Apoiado:

* Bloco de apêndice

Sem apoio:

* Bloco de apêndice de URL

## <a name="event-grid-on-iot-edge-integration"></a>Grelha de Eventos na Integração IoT Edge

> [!CAUTION]
> A integração com a Grelha de Eventos no IoT Edge está em pré-visualização

Este armazenamento de Blob Azure no módulo IoT Edge agora proporciona integração com A Grelha de Eventos no IoT Edge. Para obter informações detalhadas sobre esta integração, consulte o [tutorial para implementar os módulos, publicar eventos e verificar](../event-grid/edge/react-blob-storage-events-locally.md)a entrega do evento.

## <a name="release-notes"></a>Notas de Versão

Aqui estão as notas de [lançamento no centro de estivadores](https://hub.docker.com/_/microsoft-azure-blob-storage) para este módulo

## <a name="feedback"></a>Comentários

O seu feedback é importante para nós para tornar este módulo e as suas funcionalidades úteis e fáceis de usar. Por favor, partilhe o seu feedback e deixe-nos saber como podemos melhorar.

Pode contactar-nos.absiotfeedback@microsoft.com

## <a name="next-steps"></a>Passos seguintes

Saiba como implantar o armazenamento de [blob azure na borda ioT](how-to-deploy-blob.md)

Mantenha-se atualizado com atualizações recentes e anúncio no armazenamento de [Blob Azure no blog IoT Edge](https://aka.ms/abs-iot-blogpost)
