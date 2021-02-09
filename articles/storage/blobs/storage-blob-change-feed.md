---
title: Mude o feed em Azure Blob Storage | Microsoft Docs
description: Saiba mais sobre alterar os registos de alimentação no Azure Blob Storage e como usá-los.
author: normesta
ms.author: normesta
ms.date: 02/08/2021
ms.topic: how-to
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 9a439541880cc8e20457edc8d24c5600ba2747c8
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979231"
---
# <a name="change-feed-support-in-azure-blob-storage"></a>Alterar suporte de alimentação no armazenamento de blob Azure

O objetivo do feed de alteração é fornecer registos de transações de todas as alterações que ocorrem às bolhas e aos metadados blob na sua conta de armazenamento. O feed de alteração fornece registo **de leitura,** **garantido,** **durável,** **imutável,** **apenas de leitura** destas alterações. As aplicações do cliente podem ler estes registos a qualquer momento, seja em streaming ou em modo de lote. O feed de alteração permite-lhe construir soluções eficientes e escaláveis que processam eventos que ocorrem na sua conta Blob Storage a baixo custo.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-the-change-feed-works"></a>Como funciona o feed de mudança

O feed de alteração é armazenado como [bolhas](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) num recipiente especial na sua conta de armazenamento a [um](https://azure.microsoft.com/pricing/details/storage/blobs/) preço normal. Pode controlar o período de retenção destes ficheiros com base nas suas necessidades (Ver as [condições](#conditions) da libertação atual). Os eventos de alteração são anexados ao feed de mudança como registos na especificação do formato [Apache Avro:](https://avro.apache.org/docs/1.8.2/spec.html) um formato compacto, rápido e binário que fornece estruturas de dados ricas com esquema inline. Este formato é amplamente utilizado no ecossistema Hadoop, Stream Analytics e Azure Data Factory.

Pode processar estes registos assíncronos, incrementalmente ou em pleno. Qualquer número de aplicações de clientes podem ler independentemente o feed de mudança, em paralelo, e ao seu próprio ritmo. Aplicações analíticas como [Apache Drill](https://drill.apache.org/docs/querying-avro-files/) ou [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) podem consumir registos diretamente como ficheiros Avro, que permitem processá-los a baixo custo, com largura de banda alta, e sem ter que escrever uma aplicação personalizada.

O diagrama que se segue mostra como os registos são adicionados ao feed de alteração:

:::image type="content" source="media/storage-blob-change-feed/change-feed-diagram.png" alt-text="Diagrama mostrando como o feed de mudança funciona para fornecer um registo ordenado de alterações às bolhas":::

O suporte de feed de alteração é adequado para cenários que processam dados com base em objetos que mudaram. Por exemplo, as aplicações podem:

  - Atualizar um índice secundário, sincronizar com uma cache, mecanismo de pesquisa ou qualquer outro cenário de gestão de conteúdo.
  
  - Extrair informações e métricas de análise de negócios com base nas alterações que ocorrem nos objetos, seja em lote seja por transmissão em fluxo.
  
  - Armazenar, auditar e analisar alterações nos objetos, ao longo de qualquer período de tempo, por motivos de segurança, conformidade ou inteligência para a gestão de dados empresariais.

  - Construa soluções para backup, espelho ou replicar o estado do objeto na sua conta para a gestão ou conformidade de desastres.

  - Construa oleodutos de aplicação conectados que reagem a alterar eventos ou programar execuções com base em objeto criado ou alterado.
  
O feed de alteração é uma função pré-requisito para [a replicação de objetos](object-replication-overview.md) e [a restauração pontual para bolhas de blocos](point-in-time-restore-overview.md).

> [!NOTE]
> O feed de alteração fornece um modelo de registo durável e ordenado das alterações que ocorrem a uma bolha. As alterações são escritas e disponibilizadas no seu registo de feed de alteração dentro de uma ordem de alguns minutos da alteração. Se a sua aplicação tiver de reagir a eventos muito mais rápidos do que este, considere usar [eventos blob storage.](storage-blob-event-overview.md) [Os Eventos de Armazenamento blob](storage-blob-event-overview.md) proporcionam eventos únicos em tempo real que permitem às suas Funções ou aplicações Azure reagir rapidamente às alterações que ocorrem a uma bolha. 

## <a name="enable-and-disable-the-change-feed"></a>Ativar e desativar o feed de alteração

Tem de ativar o feed de alteração na sua conta de armazenamento para começar a capturar e a registar alterações. Desative o feed de alteração para parar de capturar alterações. Pode ativar e desativar as alterações utilizando os modelos do Gestor de Recursos Azure no Portal ou no PowerShell.

Eis algumas coisas a ter em mente quando ativar o feed de mudança.

- Há apenas uma alteração no serviço de bolhas em cada conta de armazenamento e é armazenado no recipiente **$blobchangefeed.**

- As alterações de criar, atualizar e eliminar são capturadas apenas ao nível do serviço blob.

- O feed de alteração captura *todas as* alterações para todos os eventos disponíveis que ocorrem na conta. As aplicações do cliente podem filtrar os tipos de eventos conforme necessário. (Ver as [condições](#conditions) da libertação atual).

- Apenas as contas de armazenamento GPv2 e Blob podem ativar o feed change. As contas premium BlockBlobStorage e as contas ativadas pelo espaço hierárquico não são suportadas atualmente. As contas de armazenamento GPv1 não são suportadas, mas podem ser atualizadas para GPv2 sem tempo de inatividade, consulte [a atualização para uma conta de armazenamento GPv2](../common/storage-account-upgrade.md) para obter mais informações.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Ativar o feed de alteração na sua conta de armazenamento utilizando o portal Azure:

1. No [portal Azure,](https://portal.azure.com/)selecione a sua conta de armazenamento.

2. Navegue para a opção **de Proteção de Dados** no **âmbito do Serviço Blob**.

3. Clique em **Ativado** no **feed de alteração blob**.

4. Escolha o botão **Guardar** para confirmar as definições **de Proteção de Dados.**

    ![Screenshot que mostra as definições de proteção de dados.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-configuration.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ativar o feed de alteração utilizando o PowerShell:

1. Instale o mais recente PowershellGet.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Feche e, em seguida, reabra a consola PowerShell.

3. Instale a versão 2.5.0 ou posterior do módulo **Az.Storage.**

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 2.5.0 –AllowClobber –Force
   ```

4. Inscreva-se na sua subscrição Azure com o `Connect-AzAccount` comando e siga as instruções no ecrã para autenticar.

   ```powershell
   Connect-AzAccount
   ```

5. Ativar o feed de alteração para a sua conta de armazenamento.

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[Modelo](#tab/template)
Utilize um modelo de Gestor de Recursos Azure para ativar alterar o feed da sua conta de armazenamento existente através do portal Azure:

1. No portal Azure, escolha **Criar um recurso.**

2. Em **Search the Marketplace**, **digitar a implementação do modelo** e, em seguida, premir **ENTER**.

3. Escolha **[Implementar um modelo personalizado](https://portal.azure.com/#create/Microsoft.Template)** e, em seguida, escolher Construir o seu próprio modelo no **editor**.

4. No editor de modelo, cole no json seguinte. Substitua o `<accountName>` espaço reservado pelo nome da sua conta de armazenamento.

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
    
5. Escolha o botão **Guardar,** especifique o grupo de recursos da conta e, em seguida, escolha o botão **'Comprar'** para implantar o modelo e ativar o feed de alteração.

---

## <a name="consume-the-change-feed"></a>Consumir o feed de mudança

O feed de alteração produz vários metadados e ficheiros de registo. Estes ficheiros estão localizados no **recipiente $blobchangefeed** da conta de armazenamento. 

> [!NOTE]
> Na versão atual, o recipiente $blobchangefeed é visível apenas no portal Azure, mas não é visível no Azure Storage Explorer. Atualmente não pode ver o recipiente $blobchangefeed quando liga para a API listContainers, mas é capaz de ligar diretamente para a API ListBlobs diretamente no recipiente para ver as bolhas

As aplicações do seu cliente podem consumir o feed de alteração utilizando a biblioteca do processador de feed de alteração blob que é fornecida com o processador de feed SDK change. 

Consulte [os registos de alimentação de alteração de processo no armazenamento de blob Azure](storage-blob-change-feed-how-to.md).

## <a name="understand-change-feed-organization"></a>Compreender a organização de feed de mudança

<a id="segment-index"></a>

### <a name="segments"></a>Segmentos

O feed de alteração é um registo de alterações que são organizadas em *segmentos* de hora a **hora,** mas anexadas e atualizadas a cada poucos minutos. Estes segmentos são criados apenas quando há eventos de mudança de bolha que ocorrem nessa hora. Isto permite que a aplicação do seu cliente consuma alterações que ocorrem dentro de intervalos de tempo específicos sem ter de pesquisar por todo o registo. Para saber mais, consulte as [Especificações.](#specifications)

Um segmento horária disponível do feed de alteração é descrito num ficheiro manifesto que especifica os caminhos para os ficheiros de feed de alteração desse segmento. A listagem do `$blobchangefeed/idx/segments/` diretório virtual mostra estes segmentos encomendados pelo tempo. O percurso do segmento descreve o início do intervalo de tempo horário que o segmento representa. Pode utilizar essa lista para filtrar os segmentos de registos que lhe interessam.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> O `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` é criado automaticamente quando ativa o feed de alteração. Pode ignorar com segurança este ficheiro. É um ficheiro de inicialização sempre vazio. 

O ficheiro manifesto de segmento `meta.json` mostra o caminho dos ficheiros de feed de alteração para esse segmento na `chunkFilePaths` propriedade. Aqui está um exemplo de um ficheiro manifesto de segmento.

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
> O `$blobchangefeed` recipiente só aparece depois de ter ativado a funcionalidade de feed de alteração na sua conta. Terá de esperar alguns minutos depois de ativar a transmissão antes de poder listar as bolhas no recipiente. 

<a id="log-files"></a>

### <a name="change-event-records"></a>Alterar registos de eventos

Os ficheiros de alteração contêm uma série de registos de eventos de alteração. Cada registo de evento de alteração corresponde a uma alteração a uma bolha individual. Os registos são serializados e escritos para o ficheiro utilizando a especificação do formato [Apache Avro.](https://avro.apache.org/docs/1.8.2/spec.html) Os registos podem ser lidos utilizando a especificação do formato de ficheiro Avro. Existem várias bibliotecas disponíveis para processar ficheiros nesse formato.

Os ficheiros de alimentação de alteração são armazenados no `$blobchangefeed/log/` diretório virtual como [bolhas de apêndice](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs). O primeiro ficheiro de alimentação de alteração em cada caminho terá `00000` no nome do ficheiro (por `00000.avro` exemplo). O nome de cada ficheiro de registo subsequente adicionado a esse caminho irá aumentar por 1 (Por exemplo: `00001.avro` ).

Os seguintes tipos de eventos são capturados nos registos de feed change:
- BlobCreated
- BlobDeleted
- BlobPropertiesUpdated
- BlobSnapshotCreated

Aqui está um exemplo de registo de evento de mudança de ficheiro de feed de alteração convertido para Json.

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

Para obter uma descrição de cada propriedade, consulte [o esquema de eventos da Azure Event Grid para o Blob Storage](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties). Os eventos BlobPropertiesUpdated e BlobSnapshotCreated são atualmente exclusivos para alterar feed e ainda não suportados para Eventos de Armazenamento Blob.

> [!NOTE]
> Os ficheiros de feed de alteração de um segmento não aparecem imediatamente após a criação de um segmento. A duração do atraso encontra-se dentro do intervalo normal de publicação da latência do feed de alteração, que se encontra a poucos minutos da alteração.

<a id="specifications"></a>

## <a name="specifications"></a>Especificações

- Os registos de eventos de alteração são apenas anexados ao feed de alteração. Uma vez anexados estes registos, são imutáveis e a posição de registo é estável. As aplicações do cliente podem manter o seu próprio ponto de verificação na posição de leitura do feed de alteração.

- Os registos de eventos de alteração são anexados numa ordem de poucos minutos da alteração. As aplicações do cliente podem optar por consumir registos, uma vez que são anexadas para o acesso em streaming ou a granel em qualquer outro momento.

- Os registos de eventos de alteração são encomendados por ordem de modificação **por bolha**. A ordem de alterações através de bolhas é indefinida no Armazenamento Azure Blob. Todas as alterações num segmento anterior são antes de quaisquer alterações nos segmentos subsequentes.

- Os registos de eventos de alteração são serializados no ficheiro de registo utilizando a especificação do formato [Apache Avro 1.8.2.](https://avro.apache.org/docs/1.8.2/spec.html)

- Altere os registos de eventos onde `eventType` o valor tenha são registos internos do sistema e não reflita uma alteração de `Control` objetos na sua conta. Podes ignorar os registos com segurança.

- Os valores no `storageDiagnonstics` saco de propriedade são apenas para uso interno e não projetados para uso pela sua aplicação. As suas aplicações não devem ter uma dependência contratual desses dados. Pode ignorar com segurança essas propriedades.

- O tempo representado pelo segmento é **aproximado** com limites de 15 minutos. Assim, para garantir o consumo de todos os registos dentro de um determinado tempo, consuma o segmento de horas anteriores e próximas consecutivos.

- Cada segmento pode ter um número diferente de `chunkFilePaths` devido à divisão interna do fluxo de log para gerir a produção de publicação. Os ficheiros de registo em cada um `chunkFilePath` deles são garantidos para conter bolhas mutuamente exclusivas, e podem ser consumidos e processados em paralelo sem violar a ordem de modificações por bolha durante a iteração.

- Os Segmentos começam em `Publishing` estado. Uma vez concluída a anexação dos registos ao segmento, `Finalized` será. Registar ficheiros em qualquer segmento datado da data da `LastConsumable` propriedade no `$blobchangefeed/meta/Segments.json` ficheiro, não deve ser consumido pela sua aplicação. Aqui está um exemplo da `LastConsumable` propriedade em um `$blobchangefeed/meta/Segments.json` arquivo:

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

<a id="conditions"></a>

## <a name="conditions-and-known-issues"></a>Condições e questões conhecidas

Esta secção descreve problemas e condições conhecidos na versão atual do feed de alteração. 

- Alterar os registos de eventos para qualquer alteração pode aparecer mais de uma vez no seu feed de alteração.
- Ainda não é possível gerir o período de vida dos ficheiros de registo de ficheiros de registo de ficheiros de alteração, definindo a política de retenção baseada no tempo e não pode eliminar as bolhas.
- A `url` propriedade do ficheiro de registo está sempre vazia.
- A `LastConsumable` propriedade do segments.jsem arquivo não enumera o primeiro segmento que o feed de alteração finaliza. Esta questão ocorre apenas após o primeiro segmento ser finalizado. Todos os segmentos subsequentes após a primeira hora são capturados com precisão na `LastConsumable` propriedade.
- Atualmente, não é possível ver **o** $blobchangefeed recipiente quando liga para a API dos ListContainers e o contentor não aparece no portal Azure ou no Storage Explorer. Pode ver o conteúdo ligando diretamente para a API ListBlobs no recipiente $blobchangefeed.
- As contas de armazenamento que iniciaram previamente uma [falha na conta](../common/storage-disaster-recovery-guidance.md) podem ter problemas com o ficheiro de registo que não aparece. Quaisquer falhas futuras na conta também podem ter impacto no ficheiro de registo.

## <a name="faq"></a>FAQ

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Qual é a diferença entre alterar o feed e o armazenamento de analíticas?
Os registos de análise têm registos de todas as operações de leitura, escrita, lista e eliminação de operações com pedidos bem sucedidos e falhados em todas as operações. Os registos de análise são o melhor esforço e nenhuma encomenda está garantida.

O feed de alteração é uma solução que fornece registo transacional de mutações bem-sucedidas ou alterações na sua conta, tais como criação de bolhas, modificações e supressões. O feed de alteração garante que todos os eventos sejam gravados e exibidos na ordem de alterações bem sucedidas por bolha, pelo que não é necessário filtrar o ruído de um grande volume de operações de leitura ou pedidos falhados. O feed de mudança é fundamentalmente projetado e otimizado para o desenvolvimento de aplicações que requerem certas garantias.

### <a name="should-i-use-change-feed-or-storage-events"></a>Devo utilizar eventos de feed ou armazenamento de alterações?
Pode aproveitar ambas as funcionalidades, uma vez que os eventos de feed change e blob fornecem as [mesmas](storage-blob-event-overview.md) informações com a mesma garantia de fiabilidade de entrega, sendo a principal diferença a latência, encomenda e armazenamento de registos de eventos. O feed de alteração publica registos no registo dentro de poucos minutos após a alteração e também garante a ordem de mudança de operações por blob. Os eventos de armazenamento são empurrados em tempo real e podem não ser encomendados. Os eventos de feed change são conservados duravelmente dentro da sua conta de armazenamento como registos estáveis apenas de leitura com a sua própria retenção definida, enquanto os eventos de armazenamento são transitórios para serem consumidos pelo manipulador de eventos, a menos que os armazene explicitamente. Com o feed Change, qualquer número das suas aplicações pode consumir os registos à sua própria conveniência usando APIs blob ou SDKs. 

## <a name="next-steps"></a>Passos seguintes

- Veja um exemplo de como ler o feed de alteração utilizando uma aplicação de cliente .NET. Consulte [os registos de alimentação de alteração de processo no armazenamento de blob Azure](storage-blob-change-feed-how-to.md).
- Saiba como reagir aos acontecimentos em tempo real. Ver [reagir a eventos de armazenamento de blob](storage-blob-event-overview.md)
- Saiba mais sobre informações detalhadas de registo para operações bem sucedidas e falhadas para todos os pedidos. Ver [registo de análise de armazenamento Azure](../common/storage-analytics-logging.md)