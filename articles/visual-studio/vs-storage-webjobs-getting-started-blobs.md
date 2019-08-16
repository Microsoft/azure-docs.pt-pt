---
title: Introdução ao armazenamento de BLOBs e aos serviços conectados do Visual Studio (projetos de trabalho Web) | Microsoft Docs
description: Como começar a usar o armazenamento de BLOB em um projeto do WebJob depois de se conectar a um armazenamento do Azure usando os serviços conectados do Visual Studio.
services: storage
author: ghogen
manager: jillfra
ms.assetid: 324c9376-0225-4092-9825-5d1bd5550058
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 1e951fde7e47ccfcce5f64db4ef27ac767d63480
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510650"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Introdução ao armazenamento de BLOBs do Azure e aos serviços conectados do Visual Studio (projetos de trabalho Web)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Descrição geral
Este artigo fornece C# exemplos de código que mostram como disparar um processo quando um blob do Azure é criado ou atualizado. Os exemplos de código usam o [SDK de trabalhos](https://github.com/Azure/azure-webjobs-sdk/wiki) Web versão 1. x. Quando você adiciona uma conta de armazenamento a um projeto do WebJob usando a caixa de diálogo **Adicionar serviços conectados** do Visual Studio, o pacote NuGet do armazenamento do Azure apropriado é instalado, as referências do .net apropriadas são adicionadas ao projeto e cadeias de conexão para o a conta de armazenamento é atualizada no arquivo app. config.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Como disparar uma função quando um blob é criado ou atualizado
Esta seção mostra como usar o atributo **BlobTrigger** .

 **Nota:** O SDK de trabalhos Web examina arquivos de log para observar BLOBs novos ou alterados. Esse processo é inerentemente lento; uma função pode não ser disparada até vários minutos ou mais depois que o blob for criado.  Se seu aplicativo precisar processar BLOBs imediatamente, o método recomendado é criar uma mensagem da fila ao criar o blob e usar o atributo **QueueTrigger** em vez do atributo **BlobTrigger** na função que processa o blob .

### <a name="single-placeholder-for-blob-name-with-extension"></a>Espaço reservado único para o nome do blob com extensão
O exemplo de código a seguir copia blobs de texto que aparecem no contêiner de *entrada* para o contêiner de *saída* :

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

O construtor de atributo usa um parâmetro de cadeia de caracteres que especifica o nome do contêiner e um espaço reservado para o nome do blob. Neste exemplo, se um blob chamado *Blob1. txt* for criado no contêiner de *entrada* , a função criará um blob chamado *Blob1. txt* no contêiner de *saída* .

Você pode especificar um padrão de nome com o espaço reservado do nome do blob, conforme mostrado no exemplo de código a seguir:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Esse código copia somente os blobs que têm nomes que começam com "original-". Por exemplo, *original-blob1. txt* no contêiner de *entrada* é copiado para *Copy-Blob1. txt* no contêiner de *saída* .

Se você precisar especificar um padrão de nome para nomes de BLOB que têm chaves no nome, clique duas vezes nas chaves. Por exemplo, se você quiser localizar BLOBs no contêiner *imagens* que têm nomes como este:

        {20140101}-soundfile.mp3

Use isso para seu padrão:

        images/{{20140101}}-{name}

No exemplo, o valor do espaço reservado de *nome* seria *soundfile. mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Separar nome de BLOB e espaços reservados de extensão
O exemplo de código a seguir altera a extensão de arquivo à medida que copia os blobs que aparecem no contêiner de *entrada* para o contêiner de *saída* . O código registra a extensão do blob de *entrada* e define a extensão do blob de *saída* como *. txt*.

        public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
            [Blob("output/{name}.txt")] out string output,
            string name,
            string ext,
            TextWriter logger)
        {
            logger.WriteLine("Blob name:" + name);
            logger.WriteLine("Blob extension:" + ext);
            output = input.ReadToEnd();
        }

## <a name="types-that-you-can-bind-to-blobs"></a>Tipos que você pode associar a BLOBs
Você pode usar o atributo **BlobTrigger** nos seguintes tipos:

* **string**
* **TextReader**
* **Fluxo**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Outros tipos desserializados por [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Se você quiser trabalhar diretamente com a conta de armazenamento do Azure, também poderá adicionar um parâmetro **CloudStorageAccount** à assinatura do método.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Obtendo conteúdo de blob de texto por associação à cadeia de caracteres
Se os blobs de texto forem esperados, **BlobTrigger** poderá ser aplicado a um parâmetro de **cadeia de caracteres** . O exemplo de código a seguir associa um blob de texto a um parâmetro de **cadeia de caracteres** chamado **logMessage**. A função usa esse parâmetro para gravar o conteúdo do blob no painel do SDK de trabalhos Web.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Obtendo conteúdo de BLOB serializado usando ICloudBlobStreamBinder
O exemplo de código a seguir usa uma classe que implementa **ICloudBlobStreamBinder** para permitir que o atributo **BlobTrigger** vincule um blob ao tipo webimage.

        public static void WaterMark(
            [BlobTrigger("images3/{name}")] WebImage input,
            [Blob("images3-watermarked/{name}")] out WebImage output)
        {
            output = input.AddTextWatermark("WebJobs SDK",
                horizontalAlign: "Center", verticalAlign: "Middle",
                fontSize: 48, opacity: 50);
        }
        public static void Resize(
            [BlobTrigger("images3-watermarked/{name}")] WebImage input,
            [Blob("images3-resized/{name}")] out WebImage output)
        {
            var width = 180;
            var height = Convert.ToInt32(input.Height * 180 / input.Width);
            output = input.Resize(width, height);
        }

O código de associação da **imagem** da webimage é fornecido em uma classe **WebImageBinder** que deriva de **ICloudBlobStreamBinder**.

        public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
        {
            public Task<WebImage> ReadFromStreamAsync(Stream input,
                System.Threading.CancellationToken cancellationToken)
            {
                return Task.FromResult<WebImage>(new WebImage(input));
            }
            public Task WriteToStreamAsync(WebImage value, Stream output,
                System.Threading.CancellationToken cancellationToken)
            {
                var bytes = value.GetBytes();
                return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
            }
        }

## <a name="how-to-handle-poison-blobs"></a>Como lidar com BLOBs suspeitos
Quando uma função **BlobTrigger** falha, o SDK a chama novamente, caso a falha tenha sido causada por um erro transitório. Se a falha for causada pelo conteúdo do blob, a função falhará toda vez que tentar processar o blob. Por padrão, o SDK chama uma função até 5 vezes para um determinado BLOB. Se a quinta tentativa falhar, o SDK adicionará uma mensagem a uma fila chamada *webjobs-blobtrigger-suspeita*.

O número máximo de novas tentativas é configurável. A mesma configuração **MaxDequeueCount** é usada para tratamento de BLOBs suspeitos e tratamento de mensagens de fila suspeita.

A mensagem da fila para BLOBs inviabilizados é um objeto JSON que contém as seguintes propriedades:

* FunctionID (no formato *{nome do trabalho Web}* . Funções. *{Nome da função}* , por exemplo: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão de BLOB, por exemplo: "0x8D1DC6E70A277EF")

No exemplo de código a seguir, a função **CopyBlob** tem um código que faz com que ele falhe toda vez que for chamado. Depois que o SDK o chama para o número máximo de repetições, uma mensagem é criada na fila de BLOBs suspeitas e essa mensagem é processada pela função **LogPoisonBlob** .

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("textblobs/output-{name}")] out string output)
        {
            throw new Exception("Exception for testing poison blob handling");
            output = input.ReadToEnd();
        }

        public static void LogPoisonBlob(
        [QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
            TextWriter logger)
        {
            logger.WriteLine("FunctionId: {0}", message.FunctionId);
            logger.WriteLine("BlobType: {0}", message.BlobType);
            logger.WriteLine("ContainerName: {0}", message.ContainerName);
            logger.WriteLine("BlobName: {0}", message.BlobName);
            logger.WriteLine("ETag: {0}", message.ETag);
        }

O SDK desserializa automaticamente a mensagem JSON. Aqui está a classe **PoisonBlobMessage** :

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Algoritmo de sondagem de BLOB
O SDK de trabalhos Web examina todos os contêineres especificados por atributos **BlobTrigger** na inicialização do aplicativo. Em uma conta de armazenamento grande, essa verificação pode levar algum tempo, portanto, pode ser um tempo antes que novos BLOBs sejam encontrados e as funções **BlobTrigger** sejam executadas.

Para detectar BLOBs novos ou alterados após a inicialização do aplicativo, o SDK lê periodicamente os logs de armazenamento de BLOBs. Os logs de blob são armazenados em buffer e são gravados fisicamente apenas a cada 10 minutos. portanto, pode haver um atraso significativo depois que um blob é criado ou atualizado antes da execução da função **BlobTrigger** correspondente.

Há uma exceção para os blobs que você cria usando o atributo **blob** . Quando o SDK de trabalhos Web cria um novo BLOB, ele passa o novo BLOB imediatamente para qualquer função **BlobTrigger** correspondente. Portanto, se você tiver uma cadeia de entradas e saídas de BLOB, o SDK poderá processá-las com eficiência. Mas se você quiser uma baixa latência executando suas funções de processamento de BLOB para BLOBs criados ou atualizados por outros meios, é recomendável usar **QueueTrigger** em vez de **BlobTrigger**.

### <a name="blob-receipts"></a>Recebimentos de BLOB
O SDK de trabalhos Web garante que nenhuma função **BlobTrigger** é chamada mais de uma vez para o mesmo blob novo ou atualizado. Ele faz isso mantendo os *recibos de blob* para determinar se uma determinada versão de blob foi processada.

Os recebimentos de blob são armazenados em um contêiner chamado *Azure-webjobs-hosts* na conta de armazenamento do Azure especificada pela cadeia de conexão AzureWebJobsStorage. Um recebimento de blob tem as seguintes informações:

* A função que foi chamada para o blob (" *{nome do WebJob}* . Funções. *{Nome da função}* ", por exemplo: "WebJob1.Functions.CopyBlob")
* O nome do contêiner
* O tipo de BLOB ("BlockBlob" ou "PageBlob")
* O nome do blob
* A ETag (um identificador de versão de BLOB, por exemplo: "0x8D1DC6E70A277EF")

Se você quiser forçar o reprocessamento de um blob, poderá excluir manualmente o recebimento de BLOB para esse blob do contêiner *Azure-webjobs-hosts* .

## <a name="related-topics-covered-by-the-queues-article"></a>Tópicos relacionados abordados no artigo filas
Para obter informações sobre como lidar com o processamento de blob disparado por uma mensagem da fila ou para cenários do SDK de trabalhos Web não específicos do processamento de BLOBs, consulte [como usar o armazenamento de filas do Azure com o SDK de trabalhos](https://github.com/Azure/azure-webjobs-sdk/wiki)Web.

Os tópicos relacionados abordados neste artigo incluem o seguinte:

* Funções assíncronas
* Múltiplas instâncias
* Desligamento normal
* Usar atributos do SDK de trabalhos Web no corpo de uma função
* Defina as cadeias de conexão do SDK no código.
* Definir valores para parâmetros do construtor do SDK de trabalhos Web no código
* Configure o **MaxDequeueCount** para tratamento de BLOBs suspeitos.
* Disparar uma função manualmente
* Gravar logs

## <a name="next-steps"></a>Passos Seguintes
Este artigo forneceu exemplos de código que mostram como lidar com cenários comuns para trabalhar com BLOBs do Azure. Para obter mais informações sobre como usar Azure WebJobs e o SDK de trabalhos Web, consulte [Azure WebJobs recursos de documentação](https://go.microsoft.com/fwlink/?linkid=390226).

