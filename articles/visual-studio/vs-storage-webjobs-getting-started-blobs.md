---
title: Começar com o armazenamento de blob usando visual studio (projetos WebJob)
description: Como começar a usar o armazenamento Blob num projeto WebJob depois de ligar a um armazenamento Azure usando serviços conectados visual Studio.
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
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 90aa824b7df575eb2783ece5bd88322f0b55f0a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299975"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Inicie com serviços conectados de armazenamento Azure Blob e Visual Studio (projetos WebJob)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Descrição geral
Este artigo fornece amostras de código C# que mostram como desencadear um processo quando uma bolha Azure é criada ou atualizada. As amostras de código utilizam a versão 1.x do [WebJobs SDK.](https://github.com/Azure/azure-webjobs-sdk/wiki) Quando adiciona uma conta de armazenamento a um projeto WebJob utilizando o diálogo Visual Studio **Add Connected Services,** o pacote Dearmazenamento NuGet apropriado é instalado, as referências .NET apropriadas são adicionadas ao projeto, e as cordas de ligação para a conta de armazenamento são atualizadas no ficheiro App.config.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Como desencadear uma função quando uma bolha é criada ou atualizada
Esta secção mostra como usar o atributo **BlobTrigger.**

 **Nota:** O WebJobs SDK digitaliza ficheiros de registo para ver por bolhas novas ou alteradas. Este processo é inerentemente lento; uma função pode não ser acionada até alguns minutos ou mais após a criação da bolha.  Se a sua aplicação precisar de processar bolhas imediatamente, o método recomendado é criar uma mensagem de fila quando criar a bolha, e usar o atributo **QueueTrigger** em vez do atributo **BlobTrigger** na função que processa a bolha.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Espaço reservado único para o nome blob com extensão
A amostra de código seguinte copia bolhas de texto que aparecem no recipiente de *entrada* para o recipiente de *saída:*

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

O construtor de atributos tem um parâmetro de corda que especifica o nome do recipiente e um espaço reservado para o nome blob. Neste exemplo, se uma bolha chamada *Blob1.txt* for criada no recipiente de *entrada,* a função cria uma bolha chamada *Blob1.txt* no recipiente de *saída.*

Pode especificar um padrão de nome com o espaço reservado do nome blob, como mostra a seguinte amostra de código:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Este código copia apenas bolhas que têm nomes a começar por "original". Por exemplo, o *blob1.txt original* no recipiente de *entrada* é copiado para *copy-Blob1.txt* no recipiente de *saída.*

Se precisar especificar um padrão de nome para nomes blob que tenham aparelho sinuoso no nome, duplique os aparelhos encaracolados. Por exemplo, se quiser encontrar bolhas no recipiente de *imagens* que têm nomes como este:

        {20140101}-soundfile.mp3

use isto para o seu padrão:

        images/{{20140101}}-{name}

No exemplo, o valor do espaço reservado do *nome* seria *soundfile.mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Separados espaços reservados de nome blob e extensão
A amostra de código seguinte altera a extensão do ficheiro à medida que copia bolhas que aparecem no recipiente de *entrada* para o recipiente de *saída.* O código regista a extensão da bolha de *entrada* e define a extensão da bolha de *saída* para *.txt*.

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

## <a name="types-that-you-can-bind-to-blobs"></a>Tipos que pode ligar a bolhas
Pode utilizar o atributo **BlobTrigger** nos seguintes tipos:

* **string**
* **Leitor de Texto**
* **Transmitir em fluxo**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Outros tipos desserializados por [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Se quiser trabalhar diretamente com a conta de armazenamento Azure, também pode adicionar um parâmetro **CloudStorageAccount** à assinatura do método.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Obter conteúdo blob de texto ligando à corda
Se forem esperadas bolhas de texto, o **BlobTrigger** pode ser aplicado a um parâmetro de **corda.** A amostra de código que se segue liga uma bolha de texto a um parâmetro de **cadeia** chamado **logMessage**. A função utiliza esse parâmetro para escrever o conteúdo da bolha no painel WebJobs SDK.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Obter conteúdo blob serializado usando iCloudBlobStreamBinder
A seguinte amostra de código utiliza uma classe que implementa **o ICloudBlobStreamBinder** para permitir que o atributo **BlobTrigger** ligue uma bolha ao tipo **WebImage.**

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

O código de ligação **WebImage** é fornecido numa classe **WebImageBinder** que deriva do **ICloudBlobStreamBinder**.

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

## <a name="how-to-handle-poison-blobs"></a>Como lidar com bolhas venenosas
Quando uma função **BlobTrigger** falha, o SDK volta a chamar-lhe, caso a falha tenha sido causada por um erro transitório. Se a falha for causada pelo conteúdo da bolha, a função falha sempre que tenta processar a bolha. Por predefinição, o SDK chama uma função até 5 vezes para uma determinada bolha. Se a quinta tentativa falhar, o SDK adiciona uma mensagem a uma fila chamada *webjobs-blobtrigger-poison*.

O número máximo de repetições é configurável. A mesma definição **MaxDequeueCount** é usada para manuseamento de bolhas venenosas e tratamento de mensagens de fila venenosa.

A mensagem de fila para bolhas venenosas é um objeto JSON que contém as seguintes propriedades:

* FunçãoId (no formato *{Nome WebJob}*. Funções. *{Nome de função}*, por exemplo: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão blob, por exemplo: "0x8D1DC6E70A277EF")

Na seguinte amostra de código, a função **CopyBlob** tem código que faz com que falhe sempre que é chamado. Depois de o SDK lhe chamar para o número máximo de repetições, uma mensagem é criada na fila de bolhas venenosas, e essa mensagem é processada pela função **LogPoisonBlob.**

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

O SDK desserializa automaticamente a mensagem JSON. Aqui está a aula **de PoisonBlobMessage:**

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Algoritmo de sondagem blob
O WebJobs SDK digitaliza todos os recipientes especificados pelos atributos **BlobTrigger** no início da aplicação. Numa grande conta de armazenamento, esta varredura pode demorar algum tempo, por isso pode demorar algum tempo até que novas bolhas sejam encontradas e as funções **BlobTrigger** sejam executadas.

Para detetar bolhas novas ou alteradas após o início da aplicação, o SDK lê periodicamente a partir dos registos de armazenamento de blob. Os registos de bolha são tamponados e só são escritos fisicamente a cada 10 minutos, pelo que pode haver um atraso significativo após a criação ou atualização da função **BlobTrigger** correspondente.

Há uma exceção para bolhas que cria si usando o atributo **Blob.** Quando o WebJobs SDK cria uma nova bolha, passa imediatamente a nova bolha para quaisquer funções **blobTrigger** correspondentes. Portanto, se tiver uma cadeia de inputs e saídas blob, o SDK pode processá-las de forma eficiente. Mas se quiser uma baixa latência a executar as suas funções de processamento de bolhas criadas ou atualizadas por outros meios, recomendamos a utilização de **QueueTrigger** em vez de **BlobTrigger**.

### <a name="blob-receipts"></a>Recibos blob
O WebJobs SDK garante que nenhuma função **BlobTrigger** é chamada mais de uma vez para a mesma bolha nova ou atualizada. Fá-lo mantendo *recibos blob* para determinar se uma determinada versão blob foi processada.

Os recibos blob são armazenados num contentor chamado *azure-webjobs-hosts* na conta de armazenamento Azure especificada pela cadeia de ligação AzureWebJobsStorage. Um recibo blob tem as seguintes informações:

* A função que foi chamada para a bolha ("*{WebJob name}*. Funções. *{Nome de função}*", por exemplo: "WebJob1.Functions.CopyBlob")
* O nome do recipiente
* O tipo de bolha ("BlockBlob" ou "PageBlob")
* O nome blob
* O ETag (um identificador de versão blob, por exemplo: "0x8D1DC6E70A277EF")

Se pretender forçar o reprocessamento de uma bolha, pode eliminar manualmente o recibo de bolha desse recipiente de anfitriões de *teias azure.*

## <a name="related-topics-covered-by-the-queues-article"></a>Tópicos relacionados abrangidos pelo artigo de filas
Para obter informações sobre como lidar com o processamento de blob desencadeado por uma mensagem de fila, ou para cenários SDK WebJobs não específicos para o processamento de blob, consulte Como utilizar o armazenamento de [fila Azure com o WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Os tópicos relacionados abordados nesse artigo incluem o seguinte:

* Funções de asincronização
* Múltiplas instâncias
* Encerramento gracioso
* Use webJobs SDK atributos no corpo de uma função
* Descoloque as cordas de ligação SDK em código.
* Definir valores para os parâmetros do construtor SDK WebJobs em código
* Configure **MaxDequeueCount** para manuseamento de bolhas venenosas.
* Desencadear uma função manualmente
* Escrever registos

## <a name="next-steps"></a>Passos seguintes
Este artigo forneceu amostras de código que mostram como lidar com cenários comuns para trabalhar com bolhas Azure. Para obter mais informações sobre como utilizar o Azure WebJobs e o WebJobs SDK, consulte [os recursos de documentação azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).

