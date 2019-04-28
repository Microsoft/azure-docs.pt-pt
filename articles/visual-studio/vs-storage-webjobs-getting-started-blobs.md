---
title: Introdução ao blob storage e o Visual Studio ligados (projetos de trabalho Web) de serviços | Documentos da Microsoft
description: Como começar a utilizar o armazenamento de BLOBs num projeto do trabalho Web depois de ligar a um armazenamento do Azure com o Visual Studio serviços ligados.
services: storage
author: ghogen
manager: douge
ms.assetid: 324c9376-0225-4092-9825-5d1bd5550058
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 5a7c16e6ac565d1660fee02cb7df178344b195e7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122928"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Introdução ao Azure Blob storage e o Visual Studio ligados (projetos de trabalho Web) de serviços
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Descrição geral
Este artigo fornece c# exemplos de código que mostram como acionar um processo quando um blob do Azure é criado ou atualizado. O código de exemplo utilize o [SDK do WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki) versão 1.x. Quando adiciona uma conta de armazenamento para um projeto do trabalho Web utilizando o Visual Studio **adicionar serviços ligados** caixa de diálogo, o pacote NuGet de armazenamento do Azure apropriado está instalado, as referências apropriadas do .NET são adicionadas ao projeto, e cadeias de ligação para a conta de armazenamento são atualizadas no ficheiro App. config.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Como acionar uma função, quando um blob é criado ou atualizado
Esta secção mostra como utilizar o **BlobTrigger** atributo.

 **Nota:** O SDK do WebJobs analisa os ficheiros de registo para ver para blobs de novos ou alterados. Este processo é inerentemente lento; uma função pode não obter acionada por até vários minutos ou mais depois de criar o blob.  Se a sua aplicação precisa para processar blobs imediatamente, o método recomendado é criar uma mensagem de fila quando criar o blob e utilizar o **QueueTrigger** atributo em vez do **BlobTrigger** atributo na função que processa o blob.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Marcador de posição única para o nome do blob com a extensão
O código de exemplo seguinte copia os blobs de texto que aparecem na *entrada* contentor para o *saída* contentor:

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

O construtor de atributo assume um parâmetro de cadeia de caracteres que especifica o nome do contentor e um marcador de posição para o nome do blob. Neste exemplo, se o nome de um blob *Blob1.txt* é criado na *entrada* contentor, a função cria um blob com o nome *Blob1.txt* no *saída* contentor.

Pode especificar um padrão de nome com o marcador de posição do nome de blob, conforme mostrado no exemplo de código a seguir:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Esse código copia apenas os blobs que têm nomes a partir do "original-". Por exemplo, *original Blob1.txt* no *entrada* contentor é copiado para *cópia Blob1.txt* no *saída* contentor.

Se tiver de especificar um padrão de nome para nomes de BLOBs que têm chaves no nome, faça duplo das chaves. Por exemplo, se quiser localizar os blobs na *imagens* contentor que têm nomes como este:

        {20140101}-soundfile.mp3

Utilize esta opção para seu padrão de:

        images/{{20140101}}-{name}

No exemplo, o *name* valor do marcador de posição seria *soundfile.mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Marcadores de posição de BLOBs separado, nome e extensão
O código de exemplo seguinte altera a extensão de arquivo como copia blobs que aparecem no *entrada* contentor para o *saída* contentor. O código regista a extensão do *entrada* de BLOBs e define a extensão da *saída* blob para *. txt*.

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

## <a name="types-that-you-can-bind-to-blobs"></a>Tipos que podem ser associados aos blobs
Pode utilizar o **BlobTrigger** atributo nos seguintes tipos:

* **string**
* **TextReader**
* **Stream**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Outros tipos de anular a serialização pelo [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Se quiser trabalhar diretamente com a conta de armazenamento do Azure, também pode adicionar um **CloudStorageAccount** parâmetro para a assinatura do método.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Obter conteúdo do blob de texto com a ligação à cadeia de caracteres
Se os blobs de texto são esperadas **BlobTrigger** podem ser aplicados a um **cadeia de caracteres** parâmetro. O exemplo de código a seguir vincula um blob de texto para um **cadeia de caracteres** com o nome do parâmetro **logMessage**. A função utiliza esse parâmetro para escrever o conteúdo do blob para o dashboard de WebJobs SDK.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Introdução a serializados o conteúdo do blob usando ICloudBlobStreamBinder
O exemplo de código seguinte utiliza uma classe que implementa **ICloudBlobStreamBinder** para ativar a **BlobTrigger** atributo a vincular um blob para o **WebImage** tipo.

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

O **WebImage** código de enlace é fornecido num **WebImageBinder** classe que deriva **ICloudBlobStreamBinder**.

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

## <a name="how-to-handle-poison-blobs"></a>Como lidar com blobs não processáveis
Quando um **BlobTrigger** função falhar, o SDK chama novamente, no caso de falha foi causada por um erro transitório. Se a falha for provocada pelo conteúdo do blob, a função falhar sempre que tentar processar o blob. Por predefinição, o SDK chama uma função até 5 vezes para um determinado blob. Se o quinto tentar falhar, o SDK adiciona uma mensagem a uma fila com o nome *webjobs-blobtrigger-veneno*.

O número máximo de tentativas é configurável. O mesmo **MaxDequeueCount** definição é utilizada para manipulação de blob não processáveis e processamento de mensagens não processáveis da fila.

A mensagem de fila para blobs não processáveis é um objeto JSON que contém as seguintes propriedades:

* FunctionId (no formato *{nome do trabalho Web}*. Funções. *{Nome da função}*, por exemplo: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

No exemplo de código a seguir, o **CopyBlob** função tem código que faz com que ele falha sempre que é chamado. Depois do SDK chama-lo para o número máximo de tentativas, uma mensagem é criada na fila de blob não processáveis e essa mensagem é processada pela **LogPoisonBlob** função.

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

O SDK automaticamente desserializa a mensagem JSON. Aqui está o **PoisonBlobMessage** classe:

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Algoritmo de consulta de blob
O SDK do WebJobs analisa todos os contentores especificados por **BlobTrigger** atributos no início do aplicativo. Numa conta de armazenamento de grandes esta análise pode demorar algum tempo, pelo que pode ser um pouco antes de se encontram novos blobs e **BlobTrigger** as funções são executadas.

Para detectar blobs novos ou alterados após a aplicação é iniciada, o SDK lê periodicamente dos registos de armazenamento de Blobs. Os registos de blob são colocados em memória intermédia e só são fisicamente escritos de cada 10 minutos ou então, por isso, pode haver atraso significativo depois de um blob é criado ou atualizado antes do correspondente **BlobTrigger** função seja executada.

Existe uma exceção para blobs que criou utilizando o **BLOBs** atributo. Quando o WebJobs SDK cria um blob novo, ele passa o blob novo imediatamente para um correspondente **BlobTrigger** funções. Portanto, se tiver uma cadeia de blob entradas e saídas, o SDK pode processá-los com eficiência. Mas se quiser executar o blob de funções de processamento para blobs, que são criados ou atualizados por outros meios de baixa latência, recomendamos que utilize **QueueTrigger** vez **BlobTrigger**.

### <a name="blob-receipts"></a>Recibos de blob
O SDK de WebJobs torna-se de que não existem **BlobTrigger** função é chamada mais de uma vez para o mesmo blob novo ou atualizado. Ele faz isso ao manter *recibos de BLOBs* para determinar se uma versão de determinado blob foi processada.

Os recibos de BLOBs são armazenados num contentor com o nome *anfitriões de webjobs do azure* na conta de armazenamento do Azure especificada pela cadeia de ligação de AzureWebJobsStorage. Um recibo de blob tem as seguintes informações:

* A função que foi chamada para o blob ("*{nome do trabalho Web}*. Funções. *{Nome da função}*", por exemplo: "WebJob1.Functions.CopyBlob")
* O nome do contentor
* O tipo de blob ("BlockBlob" ou "PageBlob")
* O nome do blob
* O ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

Se pretender forçar o reprocessamento de um blob, pode eliminar manualmente o recebimento de BLOBs para esse blob a partir da *anfitriões de webjobs do azure* contentor.

## <a name="related-topics-covered-by-the-queues-article"></a>Tópicos relacionados abrangidos pelo artigo de filas
Para obter informações sobre como lidar com acionado por uma mensagem de fila de processamento de BLOBs ou para o SDK do WebJobs não específicos de BLOBs de cenários de processamento, consulte [como utilizar o armazenamento de filas do Azure com o SDK do WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).

Tópicos relacionados esse artigo incluem o seguinte:

* Funções de Async
* Múltiplas instâncias
* Encerramento correto
* Utilizar atributos do SDK do WebJobs no corpo de uma função
* Defina as cadeias de ligação do SDK no código.
* Definir valores para o SDK do WebJobs parâmetros do construtor no código
* Configurar **MaxDequeueCount** para tratar do blob não processáveis.
* Acionar manualmente uma função
* Escrever os registos

## <a name="next-steps"></a>Passos Seguintes
Este artigo fornece exemplos de código que mostram como lidar com cenários comuns para trabalhar com blobs do Azure. Para obter mais informações sobre como utilizar o WebJobs do Azure e o SDK de WebJobs, consulte [recursos de documentação de WebJobs do Azure](https://go.microsoft.com/fwlink/?linkid=390226).

