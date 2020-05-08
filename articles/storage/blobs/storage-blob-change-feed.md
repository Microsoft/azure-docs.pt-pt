---
title: Alterar o feed no Armazenamento de Blob Azure (Pré-visualização) / Microsoft Docs
description: Saiba mais sobre os registos de alimentação de mudança no Armazenamento De Blob Azure e como usá-los.
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: b712148b9e619cbf5c6886bf0510b4015183d018
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82883346"
---
# <a name="change-feed-support-in-azure-blob-storage-preview"></a>Alterar suporte para alimentação em Armazenamento de Blob Azure (Pré-visualização)

O objetivo do feed de alteração é fornecer registos de transações de todas as alterações que ocorrem às bolhas e metadados blob na sua conta de armazenamento. O feed de alteração fornece **registo supérno,** **garantido,** **durável,** **imutável,** **apenas lido** destas alterações. As aplicações do cliente podem ler estes registos a qualquer momento, seja no streaming ou no modo de lote. O feed de mudança permite-lhe construir soluções eficientes e escaláveis que processam eventos de mudança que ocorrem na sua conta de Armazenamento Blob a um custo baixo.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

O feed de mudança é armazenado como [bolhas](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) num recipiente especial na sua conta de armazenamento a um custo padrão de [preços blob.](https://azure.microsoft.com/pricing/details/storage/blobs/) Pode controlar o período de retenção destes ficheiros com base nos seus requisitos (Consulte as [condições](#conditions) da versão atual). Os eventos de alteração são anexados ao feed de mudança como registos na especificação do formato [Apache Avro:](https://avro.apache.org/docs/1.8.2/spec.html) um formato compacto, rápido e binário que fornece estruturas de dados ricas com esquemas inline. Este formato é amplamente utilizado no ecossistema Hadoop, Stream Analytics e Azure Data Factory.

Pode processar estes troncos de forma assíncrona, incremental ou na íntegra. Qualquer número de aplicações de clientes pode ler de forma independente o feed de mudança, em paralelo, e ao seu próprio ritmo. Aplicações de análise como [Apache Drill](https://drill.apache.org/docs/querying-avro-files/) ou [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) podem consumir registos diretamente como ficheiros Avro, que permitem processá-los a baixo custo, com alta largura de banda, e sem ter que escrever uma aplicação personalizada.

Alterar o suporte para feed é adequado para cenários que processam dados baseados em objetos que mudaram. Por exemplo, as aplicações podem:

  - Atualizar um índice secundário, sincronizar com uma cache, motor de busca ou qualquer outro cenário de gestão de conteúdos.
  
  - Extrai aanálise de negócios e métricas, com base em alterações que ocorrem aos seus objetos, seja de forma de streaming ou de modo lote.
  
  - Armazenar, auditar e analisar alterações nos seus objetos, durante qualquer período de tempo, para segurança, conformidade ou inteligência para a gestão de dados da empresa.

  - Construa soluções para backup, espelho ou estado de objeto de replicação na sua conta para gestão ou conformidade de desastres.

  - Construa gasodutos de aplicação conectados que reagem para alterar eventos ou agendar execuções com base em objetos criados ou alterados.

> [!NOTE]
> A alimentação por alterações fornece um modelo de registo durável e ordenado das alterações que ocorrem a uma bolha. As alterações são escritas e disponibilizadas no seu registo de alimentação de alterações dentro de uma ordem de alguns minutos da alteração. Se a sua aplicação tiver de reagir a eventos muito mais rapidamente do que este, considere utilizar eventos de [armazenamento blob.](storage-blob-event-overview.md) [Blob Storage Events](storage-blob-event-overview.md) fornece eventos únicos em tempo real que permitem às suas Funções Ou aplicações Azure reagir rapidamente a alterações que ocorrem a uma bolha. 

## <a name="enable-and-disable-the-change-feed"></a>Ativar e desativar o feed de mudança

Tem de ativar o feed de alteração na sua conta de armazenamento para começar a capturar e registar alterações. Desative o feed de alteração para parar de capturar alterações. Pode ativar e desativar as alterações utilizando modelos do Gestor de Recursos Azure no Portal ou powerShell.

Aqui estão algumas coisas a ter em mente quando ativar a mudança.

- Há apenas um feed de mudança para o serviço de blob em cada conta de armazenamento e é armazenado no recipiente **$blobchangefeed.**

- As alterações Create, Update e Delete são capturadas apenas ao nível do serviço blob.

- O feed de mudança capta *todas as* alterações para todos os eventos disponíveis que ocorrem na conta. As aplicações do cliente podem filtrar os tipos de eventos conforme necessário. (Ver as [condições](#conditions) da libertação atual).

- Apenas as contas de armazenamento GPv2 e Blob podem ativar o feed de alteração. As contas Premium BlockBlobStorage e as contas hierárquicas ativadas pelo espaço de nome não são suportadas atualmente. As contas de armazenamento GPv1 não são suportadas, mas podem ser atualizadas para GPv2 sem tempo de inatividade, consulte upgrade para uma conta de [armazenamento GPv2](../common/storage-account-upgrade.md) para obter mais informações.

> [!IMPORTANT]
> A mudança de alimentos está em pré-visualização pública, e está disponível nas regiões **centro-oeste** e **oeste2.** Consulte a secção [de condições](#conditions) deste artigo. Para se inscrever na pré-visualização, consulte a secção [de subscrição](#register) deste artigo. Tem de registar a sua subscrição antes de poder ativar a alteração do feed nas suas contas de armazenamento.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Ativar o feed de alteração na sua conta de armazenamento utilizando o portal Azure:

1. No [portal Azure,](https://portal.azure.com/)selecione a sua conta de armazenamento.

2. Navegue para a opção **de Proteção** de Dados no **âmbito do Serviço Blob**.

3. Clique **ativado** sob a alimentação de **alterações blob**.

4. Escolha o botão **Guardar** para confirmar as definições de **Proteção de Dados.**

    ![](media/soft-delete-enable/storage-blob-soft-delete-portal-configuration.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ativar o feed de alteração utilizando o PowerShell:

1. Instale o mais recente PowershellGet.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Feche e, em seguida, reabra a consola PowerShell.

3. Instale o módulo de pré-visualização **Az.Storage.**

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.8.1-preview –AllowPrerelease –AllowClobber –Force
   ```

4. Inscreva-se na sua subscrição Azure com o `Connect-AzAccount` comando e siga as instruções no ecrã para autenticar.

   ```powershell
   Connect-AzAccount
   ```

5. Ative o feed de alteração para a sua conta de armazenamento.

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[Modelo](#tab/template)
Utilize um modelo de Gestor de Recursos Azure para ativar o feed de alteração na sua conta de armazenamento existente através do portal Azure:

1. No portal Azure, escolha **Criar um recurso.**

2. Em **Pesquisar no Marketplace**, escreva **implementação de modelo** e prima **ENTER**.

3. Escolha **[implementar um modelo personalizado](https://portal.azure.com/#create/Microsoft.Template)** e, em seguida, escolha Construir o seu próprio modelo no **editor**.

4. No editor de modelo, cola no seguinte json. Substitua `<accountName>` o espaço reservado com o nome da sua conta de armazenamento.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts/blobServices",
           "apiVersion": "2019-04-01",
           "name": "<accountName>/default",
           "properties": {
               "changeFeed": {
                   "enabled": true
               }
           } 
        }]
   }
   ```
    
5. Escolha o botão **Guardar,** especifique o grupo de recursos da conta e, em seguida, escolha o botão **'Comprar'** para implementar o modelo e ativar o feed de alteração.

---

## <a name="consume-the-change-feed"></a>Consumir o feed de mudança

O feed de alteração produz vários metadados e ficheiros de registo. Estes ficheiros estão localizados no recipiente **$blobchangefeed** da conta de armazenamento. 

> [!NOTE]
> No lançamento atual, o recipiente **$blobchangefeed** não é visível no Azure Storage Explorer ou no portal Azure. Atualmente não consegue ver o $blobchangefeed contentor quando liga para a ListContainers API, mas pode ligar diretamente para a API da ListBlobs diretamente no recipiente para ver as bolhas.

As aplicações do seu cliente podem consumir o feed de mudança utilizando a biblioteca do processador de feed de mudança blob que é fornecida com o processador de feed Change SDK. 

Consulte os registos de alimentação de alteração de [processos no Armazenamento de Blob Azure](storage-blob-change-feed-how-to.md).

## <a name="understand-change-feed-organization"></a>Compreender a organização de feed de mudança

<a id="segment-index"></a>

### <a name="segments"></a>Segmentos

O feed de mudança é um registo de alterações que são organizadas em *segmentos* **de hora a hora,** mas anexadas e atualizadas a cada poucos minutos. Estes segmentos só são criados quando há eventos de mudança de bolha que ocorrem naquela hora. Isto permite ao seu cliente consumir alterações que ocorram dentro de intervalos de tempo específicos sem ter que pesquisar através de todo o registo. Para saber mais, consulte as [Especificações.](#specifications)

Um segmento horária disponível do feed de mudança é descrito num ficheiro manifesto que especifica os caminhos para os ficheiros de alimentação de alteração para esse segmento. A listagem `$blobchangefeed/idx/segments/` do diretório virtual mostra estes segmentos encomendados pelo tempo. O percurso do segmento descreve o início da gama horária que o segmento representa. Pode utilizar essa lista para filtrar os segmentos de registos que lhe interessam.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> O `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` é automaticamente criado quando ativa o feed de alteração. Pode ignorar com segurança este ficheiro. É um ficheiro de inicialização sempre vazio. 

O ficheiro`meta.json`manifesto do segmento mostra o caminho dos ficheiros de feed de alteração para esse segmento na `chunkFilePaths` propriedade. Aqui está um exemplo de um ficheiro manifesto de segmento.

```json
{
    "version": 0,
    "begin": "2019-02-22T18:10:00.000Z",
    "intervalSecs": 3600,
    "status": "Finalized",
    "config": {
        "version": 0,
        "configVersionEtag": "0x8d698f0fba563db",
        "numShards": 2,
        "recordsFormat": "avro",
        "formatSchemaVersion": 1,
        "shardDistFnVersion": 1
    },
    "chunkFilePaths": [
        "$blobchangefeed/log/00/2019/02/22/1810/",
        "$blobchangefeed/log/01/2019/02/22/1810/"
    ],
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-22T18:11:01.187Z",
        "data": {
            "aid": "55e507bf-8006-0000-00d9-ca346706b70c"
        }
    }
}
```

> [!NOTE]
> O `$blobchangefeed` recipiente só aparece depois de ter ativado a funcionalidade de alimentação de alterações na sua conta. Terá de esperar alguns minutos depois de ativar a alimentação antes de poder listar as bolhas no recipiente. 

<a id="log-files"></a>

### <a name="change-event-records"></a>Alterar os registos de eventos

Os ficheiros de transmissão de alteração contêm uma série de registos de eventos de alteração. Cada registo de eventos de alteração corresponde a uma alteração a uma bolha individual. Os registos são serializados e escritos no ficheiro utilizando a especificação do formato [Apache Avro.](https://avro.apache.org/docs/1.8.2/spec.html) Os registos podem ser lidos utilizando a especificação do formato de ficheiro Avro. Existem várias bibliotecas disponíveis para processar ficheiros nesse formato.

Os ficheiros de alimentação de alteração são armazenados no `$blobchangefeed/log/` diretório virtual como bolhas de [apêndice](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs). O primeiro ficheiro de alimentação `00000` de alteração em `00000.avro`cada caminho terá no nome do ficheiro (por exemplo). O nome de cada ficheiro de registo subsequente adicionado a `00001.avro`esse caminho irá aumentar em 1 (por exemplo: ).

Aqui está um exemplo de alteração do registo de eventos do ficheiro de feed de mudança convertido para Json.

```json
{
     "schemaVersion": 1,
     "topic": "/subscriptions/dd40261b-437d-43d0-86cf-ef222b78fd15/resourceGroups/sadodd/providers/Microsoft.Storage/storageAccounts/mytestaccount",
     "subject": "/blobServices/default/containers/mytestcontainer/blobs/mytestblob",
     "eventType": "BlobCreated",
     "eventTime": "2019-02-22T18:12:01.079Z",
     "id": "55e5531f-8006-0000-00da-ca3467000000",
     "data": {
         "api": "PutBlob",
         "clientRequestId": "edf598f4-e501-4750-a3ba-9752bb22df39",
         "requestId": "00000000-0000-0000-0000-000000000000",
         "etag": "0x8D698F13DCB47F6",
         "contentType": "application/octet-stream",
         "contentLength": 128,
         "blobType": "BlockBlob",
         "url": "",
         "sequencer": "000000000000000100000000000000060000000000006d8a",
         "storageDiagnostics": {
             "bid": "11cda41c-13d8-49c9-b7b6-bc55c41b3e75",
             "seq": "(6,5614,28042,28038)",
             "sid": "591651bd-8eb3-c864-1001-fcd187be3efd"
         }
  }
}
```

Para obter uma descrição de cada propriedade, consulte o esquema do [evento Azure Event Grid para armazenamento de blob](https://docs.microsoft.com/azure/event-grid/event-schema-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties).

> [!NOTE]
> Os ficheiros de feed de alteração para um segmento não aparecem imediatamente após a criação de um segmento. A duração do atraso encontra-se dentro do intervalo normal de publicação da latência do alimento de mudança, que se encontra a poucos minutos da alteração.

<a id="specifications"></a>

## <a name="specifications"></a>Especificações

- Alterar os registos de eventos só estão anexados ao feed de mudança. Uma vez anexados estes registos, são imutáveis e a posição de registo é estável. As aplicações do cliente podem manter o seu próprio ponto de verificação na posição de leitura do feed de mudança.

- Alterar os registos de eventos são anexados dentro de uma ordem de poucos minutos da mudança. As aplicações do cliente podem optar por consumir registos, uma vez que estão anexados para acesso ao streaming ou a granel em qualquer outro momento.

- Alterar os registos de eventos são encomendados por ordem de modificação **por bolha**. A ordem de alterações entre bolhas é indefinida no Armazenamento De Blob Azure. Todas as alterações num segmento anterior são antes de quaisquer alterações nos segmentos seguintes.

- Alterar os registos de eventos são serializados no ficheiro de registo utilizando a especificação do formato [Apache Avro 1.8.2.](https://avro.apache.org/docs/1.8.2/spec.html)

- Altere os `eventType` registos de `Control` eventos onde os registos internos do sistema estão a não refletir uma alteração nos objetos da sua conta. Podeignorar com segurança esses registos.

- Os valores no saco de `storageDiagnonstics` propriedade são apenas para uso interno e não foram concebidos para uso pela sua aplicação. As suas aplicações não devem ter uma dependência contratual desse saque. Pode ignorar com segurança essas propriedades.

- O tempo representado pelo segmento é **aproximado** com limites de 15 minutos. Assim, para garantir o consumo de todos os registos num prazo determinado, consumir o segmento anterior e próximo consecutivo.

- Cada segmento pode ter `chunkFilePaths` um número diferente de devido à divisão interna do fluxo de registo para gerir a entrada de publicação. Os ficheiros `chunkFilePath` de registo em cada um deles são garantidos que contêm bolhas mutuamente exclusivas, e podem ser consumidos e processados em paralelo sem violar a ordem de modificações por bolha durante a iteração.

- Os Segmentos começam `Publishing` em estado. Uma vez concluída a despesa dos registos para `Finalized`o segmento, será . Os ficheiros de registo em qualquer `LastConsumable` segmento datado após a data da propriedade no `$blobchangefeed/meta/Segments.json` ficheiro, não devem ser consumidos pela sua aplicação. Aqui está um exemplo `LastConsumable`da `$blobchangefeed/meta/Segments.json` propriedade em um arquivo:

```json
{
    "version": 0,
    "lastConsumable": "2019-02-23T01:10:00.000Z",
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-23T02:24:00.556Z",
        "data": {
            "aid": "55e551e3-8006-0000-00da-ca346706bfe4",
            "lfz": "2019-02-22T19:10:00.000Z"
        }
    }
}

```

<a id="register"></a>

## <a name="register-your-subscription-preview"></a>Registe a sua subscrição (Pré-visualização)

Como o feed de alteração está apenas na pré-visualização pública, terá de registar a sua subscrição para utilizar a funcionalidade.

### <a name="register-by-using-powershell"></a>Registe-se utilizando o PowerShell

Numa consola PowerShell, execute estes comandos:

```powershell
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
   
### <a name="register-by-using-azure-cli"></a>Registe-se utilizando o Azure CLI

Em Azure Cloud Shell, execute estes comandos:

```azurecli
az feature register --namespace Microsoft.Storage --name Changefeed
az provider register --namespace 'Microsoft.Storage'
```

<a id="conditions"></a>

## <a name="conditions-and-known-issues-preview"></a>Condições e questões conhecidas (Pré-visualização)

Esta secção descreve questões e condições conhecidas na atual pré-visualização pública do feed de mudança. 
- Para pré-visualização, primeiro deve [registar a sua subscrição](#register) antes de poder ativar o feed de alteração para a sua conta de armazenamento nas regiões centro-oeste ou westus2. 
- O feed de alteração captura apenas criar, atualizar, eliminar e copiar operações. As atualizações de metadados não são atualmente captadas na pré-visualização.
- Alterar os registos de eventos para qualquer alteração pode aparecer mais de uma vez no seu feed de mudança.
- Ainda não é possível gerir a vida útil da alteração dos ficheiros de registo de feed, definindo a política de retenção baseada no tempo e não pode eliminar as bolhas 
- A `url` propriedade do ficheiro de registo está atualmente sempre vazia.
- A `LastConsumable` propriedade do ficheiro segments.json não enumera o primeiro segmento que o feed de alteração finaliza. Esta questão só ocorre após a finalção do primeiro segmento. Todos os segmentos subsequentes após a `LastConsumable` primeira hora são capturados com precisão na propriedade.
- Atualmente não pode ver o recipiente **$blobchangefeed** quando liga para a ListContainers API e o recipiente não aparece no portal Azure ou no Storage Explorer
- As contas de armazenamento que tenham iniciado anteriormente uma falha de [conta](../common/storage-disaster-recovery-guidance.md) podem ter problemas com o ficheiro de registo não aparecer. Quaisquer falhas futuras de conta também podem ter impacto no ficheiro de registo durante a pré-visualização.

## <a name="faq"></a>FAQ

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Qual é a diferença entre alterar o feed e o armazenamento analytics logging?
Os registos de análise têm registos de todas as operações de leitura, escrita, lista e eliminação de operações com pedidos bem sucedidos e falhados em todas as operações. Os registos de análise são o melhor esforço e não é garantida qualquer encomenda.

Alterar o feed é uma solução que fornece registo transacional de mutações bem sucedidas ou alterações na sua conta, tais como criação de bolhas, modificações e supressões. A alteração do feed garante que todos os eventos devem ser registados e exibidos na ordem das alterações bem sucedidas por bolha, pelo que não é preciso filtrar o ruído de um grande volume de operações de leitura ou pedidos falhados. O feed de mudança é fundamentalmente projetado e otimizado para o desenvolvimento de aplicações que requerem certas garantias.

### <a name="should-i-use-change-feed-or-storage-events"></a>Devo usar eventos de "Change feed" ou de armazenamento?
Pode aproveitar ambas as funcionalidades como os eventos de feed change e [blob](storage-blob-event-overview.md) fornecer as mesmas informações com a mesma garantia de fiabilidade de entrega, sendo a principal diferença a latência, encomenda e armazenamento de registos de eventos. A alteração do feed publica registos no registo dentro de poucos minutos da alteração e também garante a ordem de alteração das operações por bolha. Os eventos de armazenamento são empurrados em tempo real e podem não ser encomendados. Os eventos de alimentação de alterações são armazenados de forma duradoura dentro da sua conta de armazenamento como registos estáveis de leitura com a sua própria retenção definida, enquanto os eventos de armazenamento são transitórios para serem consumidos pelo manipulador de eventos, a menos que os guarde explicitamente. Com o feed Change, qualquer número de suas aplicações pode consumir os registos por sua própria conveniência usando APIs ou SDKs blob. 

## <a name="next-steps"></a>Passos seguintes

- Consulte um exemplo de como ler o feed de mudança utilizando uma aplicação de cliente .NET. Consulte os registos de alimentação de alteração de [processos no Armazenamento de Blob Azure](storage-blob-change-feed-how-to.md).
- Saiba como reagir aos acontecimentos em tempo real. Ver [Reagir a eventos de armazenamento blob](storage-blob-event-overview.md)
- Saiba mais sobre informações detalhadas sobre o registo de registos para operações bem sucedidas e falhadas para todos os pedidos. Ver [exploração de analíticos de armazenamento Azure](../common/storage-analytics-logging.md)
