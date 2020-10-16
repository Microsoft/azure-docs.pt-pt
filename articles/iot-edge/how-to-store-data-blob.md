---
title: Armazenar blocos de blocos em dispositivos - Azure IoT Edge / Microsoft Docs
description: Compreenda as funcionalidades de tiering e time-to-live, consulte as operações de armazenamento de bolhas suportadas e ligue-se à sua conta de armazenamento de bolhas.
author: kgremban
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6de96b9913b70dd1b2d423e00c58b95ccb8dcb07
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048156"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>Armazenar dados na periferia com o Armazenamento de Blobs do Azure no IoT Edge

O Azure Blob Storage on IoT Edge fornece uma [solução de](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) armazenamento de bolhas de bloco e [apêndice](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) na borda. Um módulo de armazenamento de bolhas no seu dispositivo IoT Edge comporta-se como um serviço de bolhas Azure, exceto que as bolhas são armazenadas localmente no seu dispositivo IoT Edge. Pode aceder às suas bolhas utilizando os mesmos métodos SDK de armazenamento Azure ou chamadas API blob a que já está habituado. Este artigo explica os conceitos relacionados com o Azure Blob Storage no recipiente IoT Edge que executa um serviço de bolhas no seu dispositivo IoT Edge.

Este módulo é útil em cenários:

* onde os dados precisam ser armazenados localmente até que possa ser processado ou transferido para a nuvem. Estes dados podem ser vídeos, imagens, dados financeiros, dados hospitalares ou quaisquer outros dados não estruturados.
* quando os dispositivos estão localizados num local com conectividade limitada.
* quando pretende processar os dados de forma eficiente localmente para obter um acesso de baixa latência aos dados, de modo a que possa responder a emergências o mais rapidamente possível.
* quando pretende reduzir os custos de largura de banda e evitar transferir terabytes de dados para a nuvem. Pode processar os dados localmente e enviar apenas os dados processados para a nuvem.

Veja o vídeo para uma rápida introdução
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

Este módulo vem com **funcionalidades deviceToCloudUpload** e **deviceAutoDelete.**

**deviceToCloudUpload** é uma funcionalidade configurável. Esta função envia automaticamente os dados do seu armazenamento de bolhas locais para Azure com suporte intermitente de conectividade à Internet. Permite-lhe:

* Ligue/DESLIGUE a função dispositivoToCloudUpload.
* Escolha a ordem na qual os dados são copiados para Azure como NewestFirst ou Mais Antigo Primeiro.
* Especifique a conta de Armazenamento Azure para a qual pretende que os seus dados sejam carregados.
* Especifique os recipientes que pretende enviar para Azure. Este módulo permite especificar os nomes dos recipientes de origem e alvo.
* Escolha a capacidade de apagar as bolhas imediatamente, depois de o upload para o armazenamento na nuvem estar terminado
* Faça o upload completo do blob (utilizando o `Put Blob` funcionamento) e bloqueie o upload do nível `Put Block` (utilizando, `Put Block List` e `Append Block` operações).

Este módulo utiliza o upload do nível do bloco, quando a sua bolha é composta por blocos. Eis alguns dos cenários comuns:

* A sua aplicação atualiza alguns blocos de uma bolha de bloco previamente carregada ou anexa novos blocos a uma bolha de apêndice, este módulo carrega apenas os blocos atualizados e não toda a bolha.
* O módulo está a carregar bolhas e a ligação à Internet desaparece, quando a conectividade está de volta, carrega apenas os blocos restantes e não toda a bolha.

Se uma interrupção inesperada do processo (como falha de energia) acontecer durante um upload de bolhas, todos os blocos que eram devidos para o upload serão carregados novamente assim que o módulo voltar a funcionar.

**deviceAutoDelete** é uma funcionalidade configurável. Esta função elimina automaticamente as suas bolhas do armazenamento local quando a duração especificada (medida em minutos) expirar. Permite-lhe:

* Ligue/DESLIGUE o dispositivoAutoDelete.
* Especifique o tempo em minutos (eliminarMinutes) após o qual as bolhas serão automaticamente eliminadas.
* Escolha a capacidade de reter a bolha enquanto está a carregar se o valor de DeleteAfterMinutes expirar.

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Azure IoT Edge:

* Pode utilizar a sua máquina de desenvolvimento ou uma máquina virtual como dispositivo IoT Edge seguindo os passos no arranque rápido para [dispositivos](quickstart.md) [Linux](quickstart-linux.md) ou Windows .

* Consulte os [sistemas suportados Azure IoT Edge](support.md#operating-systems) para obter uma lista de sistemas operativos e arquiteturas suportados. O Azure Blob Storage no módulo IoT Edge suporta as seguintes arquiteturas:
  * Windows AMD64
  * Linux AMD64
  * Linux ARM32
  * Linux ARM64 (pré-visualização)

Recursos da cloud:

Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão standard no Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>deviceToCloudUpload e deviceAutoDelete propriedades

Utilize as propriedades desejadas do módulo para definir **dispositivoToCloudUploadProperties** e **dispositivoAutoDeleteProperties**. As propriedades desejadas podem ser definidas durante a implementação ou alteradas posteriormente editando o módulo twin sem a necessidade de reimplantar. Recomendamos verificar o "Módulo Twin" `reported configuration` e `configurationValidation` certificar-nos de que os valores são corretamente propagados.

### <a name="devicetoclouduploadproperties"></a>dispositivoToCloudUploadProperties

O nome desta definição é `deviceToCloudUploadProperties` . Se estiver a utilizar o simulador IoT Edge, desaprote os valores para as variáveis ambientais relacionadas para estas propriedades, que pode encontrar na secção de explicação.

| Propriedade | Valores Possíveis | Explicação |
| ----- | ----- | ---- |
| uploadOn | TRUE, false | Definido por `false` defeito. Se quiser ligar a função, desa um campo para `true` . <br><br> Variável ambiental: `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, O Mais Velho Primeiro | Permite-lhe escolher a ordem em que os dados são copiados para a Azure. Definido por `OldestFirst` defeito. A ordem é determinada pelo último tempo modificado da Blob. <br><br> Variável ambiental: `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` é uma cadeia de ligação que lhe permite especificar a conta de armazenamento para a qual pretende que os seus dados sejam carregados. `Azure Storage Account Name` `Azure Storage Account Key` Especificar, . `End point suffix` . Adicione o sufixo EndpointS adequado do Azure onde os dados serão carregados, varia para Global Azure, Government Azure e Microsoft Azure Stack. <br><br> Pode optar por especificar a cadeia de ligação SAS do Azure Storage Sas aqui. Mas tem que atualizar esta propriedade quando expirar. <br><br> Variável ambiental: `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Permite especificar os nomes dos recipientes que pretende enviar para Azure. Este módulo permite especificar os nomes dos recipientes de origem e alvo. Se não especificar o nome do recipiente-alvo, atribuirá automaticamente o nome do recipiente como `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>` . Pode criar cordas de modelo para o nome do recipiente-alvo, verificar a coluna de valores possíveis. <br>* %h -> IoT Hub Name (3-50 caracteres). <br>* %d -> IoT Edge Device ID (1 a 129 caracteres). <br>* %m -> Nome do módulo (1 a 64 caracteres). <br>* %c -> Nome do Recipiente de Origem (3 a 63 caracteres). <br><br>O tamanho máximo do nome do recipiente é de 63 caracteres, ao mesmo tempo que atribui automaticamente o nome do recipiente-alvo se o tamanho do recipiente exceder 63 caracteres, cortará cada secção (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName) a 15 caracteres. <br><br> Variável ambiental: `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| excluirAfterUpload | TRUE, false | Definido por `false` defeito. Quando estiver `true` programado, eliminará automaticamente os dados quando o upload para o armazenamento na nuvem estiver terminado. <br><br> **ATENÇÃO**: Se estiver a utilizar bolhas de apêndice, esta definição eliminará as bolhas de apêndice do armazenamento local após um upload bem sucedido, e quaisquer futuras operações do Bloco de Apêndice para essas bolhas falharão. Utilize esta definição com cuidado, não o ative se a sua aplicação não for frequente as operações de apêndice ou não suportar operações de apêndice contínuos<br><br> Variável ambiental: `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` . |

### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

O nome desta definição é `deviceAutoDeleteProperties` . Se estiver a utilizar o simulador IoT Edge, desaprote os valores para as variáveis ambientais relacionadas para estas propriedades, que pode encontrar na secção de explicação.

| Propriedade | Valores Possíveis | Explicação |
| ----- | ----- | ---- |
| deleteOn | TRUE, false | Definido por `false` defeito. Se quiser ligar a função, desa um campo para `true` . <br><br> Variável ambiental: `deviceAutoDeleteProperties__deleteOn={false,true}` |
| excluirAfterMinutes | `<minutes>` | Especifique o tempo em minutos. O módulo apagará automaticamente as suas bolhas do armazenamento local quando este valor expirar. <br><br> Variável ambiental: `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| manter o Uploading | TRUE, false | Por predefinição, é definido para `true` , e irá reter o blob enquanto estiver a ser carregado para armazenamento na nuvem se o Apagar Depois expirar. Pode defini-lo `false` e apagará os dados assim que o Eliminar Minuto expirar. Nota: Para que esta propriedade funcione uploadOn deve ser definido como verdadeiro.  <br><br> **ATENÇÃO:** Se estiver a utilizar bolhas de apêndice, esta definição eliminará as bolhas de apêndice do armazenamento local quando o valor expirar, e quaisquer futuras operações do Bloco de Apêndice a essas bolhas falharão. É melhor certificar-se de que o valor de validade é suficientemente grande para a frequência esperada das operações de apêndice realizadas pela sua aplicação.<br><br> Variável ambiental: `deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>Usando a partilha SMB como o seu armazenamento local

Pode fornecer a partilha SMB como a sua trajetória de armazenamento local, quando implementar o contentor Windows deste módulo no anfitrião do Windows.

Certifique-se de que a partilha SMB e o dispositivo IoT estão em domínios de confiança mútua.

Pode executar `New-SmbGlobalMapping` o comando PowerShell para mapear a partilha do SMB localmente no dispositivo IoT que executa o Windows.

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

Este comando utilizará as credenciais para autenticar com o servidor SMB remoto. Em seguida, mapear o caminho da partilha remota para G: letra de unidade (pode ser qualquer outra letra de unidade disponível). O dispositivo IoT tem agora o volume de dados mapeado para um caminho no G: unidade.

Certifique-se de que o utilizador no dispositivo IoT pode ler/escrever para a partilha remota de SMB.

Para a sua implantação o valor de `<storage mount>` pode ser **G:/ContainerData:C:/BlobRoot**.

## <a name="granting-directory-access-to-container-user-on-linux"></a>Concessão de acesso de diretório ao utilizador de contentores em Linux

Se utilizou [o suporte de volume](https://docs.docker.com/storage/volumes/) para armazenamento nas suas opções de criação para recipientes Linux, então não precisa de fazer quaisquer passos adicionais, mas se usou [o suporte de fixação,](https://docs.docker.com/storage/bind-mounts/) então estes passos são necessários para executar o serviço corretamente.

Seguindo o princípio de menor privilégio de limitar os direitos de acesso dos utilizadores a permissões mínimas de que necessitam para realizar o seu trabalho, este módulo inclui um utilizador (nome: absie, ID: 11000) e um grupo de utilizadores (nome: absie, ID: 11000). Se o recipiente for iniciado como **raiz** (o utilizador predefinido é **raiz),** o nosso serviço será iniciado como utilizador **absie** de baixo privilégio.

Este comportamento torna a configuração das permissões no caminho do anfitrião crucial para que o serviço funcione corretamente, caso contrário o serviço falhará com o acesso negado erros. O caminho utilizado na ligação de diretórios deve ser acessível pelo utilizador do contentor (exemplo: absie 11000). Pode conceder ao utilizador do contentor acesso ao diretório executando os comandos abaixo no anfitrião:

```terminal
sudo chown -R 11000:11000 <blob-dir>
sudo chmod -R 700 <blob-dir>
```

Por exemplo:

```terminal
sudo chown -R 11000:11000 /srv/containerdata
sudo chmod -R 700 /srv/containerdata
```

Se precisar de executar o serviço como um utilizador diferente do **absie,** pode especificar o seu ID de utilizador personalizado na criaçãoOpções em propriedade "Utilizador" no seu manifesto de implementação. Neste caso, é necessário utilizar o ID do grupo padrão ou raiz `0` .

```json
"createOptions": {
  "User": "<custom user ID>:0"
}
```

Agora, conceder ao utilizador do contentor acesso ao diretório

```terminal
sudo chown -R <user ID>:<group ID> <blob-dir>
sudo chmod -R 700 <blob-dir>
```

## <a name="configure-log-files"></a>Configurar ficheiros de registo

Para obter informações sobre a configuração dos ficheiros de registo para o seu módulo, consulte estas [boas práticas de produção](./production-checklist.md#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Ligue-se ao seu módulo de armazenamento de bolhas

Pode utilizar o nome da conta e a chave de conta que configura para o seu módulo aceder ao armazenamento de bolhas no seu dispositivo IoT Edge.

Especifique o seu dispositivo IoT Edge como o ponto final da bolha para quaisquer pedidos de armazenamento que lhe faça. Pode [criar um fio de ligação para um ponto final de armazenamento explícito](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) utilizando as informações do dispositivo IoT Edge e o nome da conta que configuraste.

* Para os módulos que são implantados no mesmo dispositivo que o Azure Blob Storage no módulo IoT Edge, o ponto final blob é: `http://<module name>:11002/<account name>` .
* Para módulos ou aplicações em execução num dispositivo diferente, tem de escolher o ponto final certo para a sua rede. Dependendo da configuração da rede, escolha um formato de ponto final de modo a que o tráfego de dados do seu módulo ou aplicação externo possa chegar ao dispositivo que executa o Azure Blob Storage no módulo IoT Edge. O ponto final blob para este cenário é um dos:
  * `http://<device IP >:11002/<account name>`
  * `http://<IoT Edge device hostname>:11002/<account name>`
  * `http://<fully qualified domain name>:11002/<account name>`
 
 > [!IMPORTANT]
 > O Azure IoT Edge é sensível a casos quando faz chamadas para módulos, e o SDK de armazenamento também falha em minúsculas. Embora o nome do módulo no [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) seja **AzureBlobStorageonIoTEdge,** mudar o nome para minúscula ajuda a garantir que as suas ligações com o Azure Blob Storage no módulo IoT Edge não sejam interrompidas.
 
## <a name="azure-blob-storage-quickstart-samples"></a>Amostras rápidas de arranque de armazenamento Azure Blob

A documentação de armazenamento Azure Blob inclui código de amostra de arranque rápido em vários idiomas. Pode executar estas amostras para testar o Azure Blob Storage na IoT Edge alterando o ponto final da bolha para ligar ao módulo de armazenamento de bolhas locais.

As seguintes amostras de arranque rápido utilizam idiomas que também são suportados pelo IoT Edge, para que possa implantá-los como módulos IoT Edge ao lado do módulo de armazenamento de bolhas:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
  * Versões antes de V2.1 do Python SDK têm um problema conhecido onde o módulo não devolve o tempo de criação de blob. Por causa desta questão, alguns métodos como as bolhas de lista não funcionam. Como solução alternativa, coloque explicitamente a versão API no cliente blob para '2017-04-17'. Exemplo:  `block_blob_service._X_MS_VERSION = '2017-04-17'`
  * [Amostra de Blob de Apêndice](https://github.com/Azure/azure-storage-python/blob/master/samples/blob/append_blob_usage.py)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-legacy.md)
* [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-legacy.md)
* [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
* [Ir](../storage/blobs/storage-quickstart-blobs-go.md)
* [PHP](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Conecte-se ao seu armazenamento local com o Azure Storage Explorer

Pode utilizar [o Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) para se ligar à sua conta de armazenamento local.

1. Transferir e instalar o Explorador de Armazenamento do Azure

1. Ligue ao Azure Storage usando uma cadeia de ligação

1. Fornecer cadeia de ligação: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Assemem os degraus para se conectarem.

1. Crie um recipiente dentro da sua conta de armazenamento local

1. Comece a carregar ficheiros como blobs de bloco ou Blobs de Apêndice.
   > [!NOTE]
   > Este módulo não suporta bolhas page.

1. Também pode optar por ligar as suas contas de armazenamento Azure no Storage Explorer. Esta configuração dá-lhe uma única visão para a sua conta de armazenamento local e conta de armazenamento Azure

## <a name="supported-storage-operations"></a>Operações de armazenamento apoiadas

Os módulos de armazenamento blob no IoT Edge utilizam os SDKs de armazenamento Azure e são consistentes com a versão 2017-04-17 da API de armazenamento Azure para pontos finais blob de bloco.

Como nem todas as operações de Armazenamento Azure Blob são suportadas pelo Azure Blob Storage on IoT Edge, esta secção lista o estado de cada um.

### <a name="account"></a>Conta

Apoiado:

* Listar contentores

Sem apoio:

* Obter e definir propriedades de serviço blob
* Pedido de bolha de pré-voo
* Obtenha estatísticas de serviço blob
* Obter informações sobre contas

### <a name="containers"></a>Contentores

Apoiado:

* Criar e apagar recipiente
* Obtenha propriedades de contentores e metadados
* Listar blobs
* Obter e definir recipiente ACL
* Definir metadados de contentores

Sem apoio:

* Recipiente de arrendamento

### <a name="blobs"></a>Blobs

Apoiado:

* Colocar, obter e apagar bolha
* Obter e definir propriedades blob
* Obter e definir metadados blob

Sem apoio:

* Bolha de arrendamento
* Bolha de instantâneo
* Copiar e abortar bolha de cópia
* Bolha undelete
* Definir nível de blob

### <a name="block-blobs"></a>Blobs de blocos

Apoiado:

* Coloque o bloco
* Colocar e obter lista de blocos

Sem apoio:

* Coloque o bloco da URL

### <a name="append-blobs"></a>Blobs de acréscimo

Apoiado:

* Bloco de apêndice

Sem apoio:

* Bloco de apêndice de URL

## <a name="event-grid-on-iot-edge-integration"></a>Grelha de eventos na integração de IoT Edge

> [!CAUTION]
> A integração com a Grade de Eventos no IoT Edge está em pré-visualização

Este módulo Azure Blob Storage on IoT Edge proporciona agora integração com a Grade de Eventos em IoT Edge. Para obter informações detalhadas sobre esta integração, consulte o [tutorial para implementar os módulos, publicar eventos e verificar a entrega do evento.](../event-grid/edge/react-blob-storage-events-locally.md)

## <a name="release-notes"></a>Notas de Versão

Aqui estão as [notas de lançamento no estivador hub](https://hub.docker.com/_/microsoft-azure-blob-storage) para este módulo

## <a name="suggestions"></a>Sugestões

O seu feedback é importante para nós para tornar este módulo e as suas funcionalidades úteis e fáceis de usar. Por favor, partilhe o seu feedback e diga-nos como podemos melhorar.

Pode nos alcançar em absiotfeedback@microsoft.com

## <a name="next-steps"></a>Passos seguintes

Saiba como [implementar o armazenamento de blob Azure na borda IoT](how-to-deploy-blob.md)

Mantenha-se atualizado com as recentes atualizações e anúncio no [Azure Blob Storage no blog IoT Edge](https://aka.ms/abs-iot-blogpost)