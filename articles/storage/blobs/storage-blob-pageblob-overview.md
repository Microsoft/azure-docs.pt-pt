---
title: Visão geral das bolhas da página de Azure Microsoft Docs
description: Uma visão geral das bolhas da página de Azure e suas vantagens, incluindo a utilização de casos com scripts de amostra.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/15/2020
ms.author: tamram
ms.reviewer: wielriac
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: aada418b4f74c38a2a35c793deb85b94b703fb89
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629362"
---
# <a name="overview-of-azure-page-blobs"></a>Visão geral das bolhas da página de Azure

O Azure Storage oferece três tipos de armazenamento de bolhas: Blobs de Bloco, Blobs de Apêndi e bolhas de página. As bolhas de bloco são compostas por blocos e são ideais para armazenar texto ou ficheiros binários, e para o upload de ficheiros grandes de forma eficiente. As bolhas de apêndice também são compostas por blocos, mas são otimizadas para operações de apêndice, tornando-as ideais para cenários de registo. As bolhas de página são compostas por 512 páginas de byte até 8 TB no tamanho total e são concebidas para operações frequentes de leitura/escrita aleatórias. As bolhas de página são a fundação dos Discos Azure IaaS. Este artigo centra-se em explicar as funcionalidades e benefícios das bolhas de página.

As bolhas de página são uma coleção de páginas de 512 bytes, que fornecem a capacidade de ler/escrever gamas arbitrárias de bytes. Assim, as bolhas de página são ideais para armazenar estruturas de dados baseadas em índices e escassas, como o SO e discos de dados para Máquinas Virtuais e Bases de Dados. Por exemplo, o Azure SQL DB utiliza bolhas de página como o armazenamento persistente subjacente para as suas bases de dados. Além disso, as bolhas de página também são frequentemente usadas para ficheiros com Range-Based atualizações.  

As principais características das bolhas de página Azure são a sua interface REST, a durabilidade do armazenamento subjacente e as capacidades de migração perfeitas para Azure. Estas funcionalidades são discutidas mais detalhadamente na secção seguinte. Além disso, as bolhas de página Azure são atualmente suportadas em dois tipos de armazenamento: Armazenamento Premium e Armazenamento Standard. Premium Storage é projetado especificamente para cargas de trabalho que requerem um desempenho consistente de alto desempenho e baixa latência tornando blobs de página premium ideais para cenários de armazenamento de alto desempenho. As contas de armazenamento padrão são mais rentáveis para executar cargas de trabalho insensíveis à latência.

## <a name="restrictions"></a>Restrições

As bolhas de página só podem utilizar o nível de acesso **Hot,** não podem utilizar os níveis **Cool** ou **Archive.** Para obter mais informações sobre os níveis de acesso, consulte [os níveis de acesso para armazenamento Azure Blob - quente, fresco e arquivo](storage-blob-storage-tiers.md).

## <a name="sample-use-cases"></a>Exemplos de casos de utilização

Vamos discutir alguns casos de uso para bolhas de página começando com discos Azure IaaS. As bolhas de página Azure são a espinha dorsal da plataforma de discos virtuais para Azure IaaS. Tanto o Azure OS como os discos de dados são implementados como discos virtuais onde os dados são duramente persistidos na plataforma de Armazenamento Azure e depois entregues às máquinas virtuais para o máximo desempenho. Os Discos Azure são persistidos no [formato Hiper-VHD](/previous-versions/windows/it-pro/windows-7/dd979539(v=ws.10)) e [armazenados](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) como uma bolha de página no Azure Storage. Além de utilizar discos virtuais para VMs Azure IaaS, as bolhas de página também permitem cenários paaS e DBaaS, como o serviço Azure SQL DB, que atualmente utiliza bolhas de página para armazenar dados SQL, permitindo operações de leitura aleatória rápida para a base de dados. Outro exemplo seria se você tivesse um serviço PaaS para acesso partilhado de mídia para aplicações colaborativas de edição de vídeo, as bolhas de página permitem o acesso rápido a locais aleatórios nos meios de comunicação. Também permite a edição e fusão rápidas e eficientes dos mesmos meios por vários utilizadores. 

Os serviços da Microsoft, como o Azure Site Recovery, o Azure Backup, bem como muitos desenvolvedores de terceiros implementaram inovações líderes do setor usando a interface REST da page blob. Seguem-se alguns dos cenários únicos implementados no Azure: 

* Gestão incremental de snapshots direcionados para aplicações: As aplicações podem alavancar as imagens blob de página e ASPIS de REST para guardar os pontos de verificação da aplicação sem incorrer em duplicação dispendiosa de dados. O Azure Storage suporta instantâneos locais para bolhas de página, que não requerem copiar toda a bolha. Estas APIs de instantâneo público também permitem o acesso e a cópia de deltas entre instantâneos.
* Migração ao vivo de aplicações e dados de nas instalações para nuvem: Copie os dados nas instalações e utilize ASPis de REST para escrever diretamente para uma bolha de página Azure enquanto o VM nas instalações continua a funcionar. Uma vez que o alvo tenha sido apanhado, pode rapidamente falhar para a Azure VM usando esses dados. Desta forma, pode migrar os seus VMs e discos virtuais de instalações para cloud com o mínimo de tempo de paragem, uma vez que a migração de dados ocorre em segundo plano enquanto continua a utilizar o VM e o tempo de inatividade necessário para a falha será curto (em minutos).
* Acesso partilhado [baseado em SAS,](../common/storage-sas-overview.md) que permite cenários como múltiplos leitores e um único escritor com suporte para o controlo da conuncy.

## <a name="pricing"></a>Preços

Ambos os tipos de armazenamento oferecidos com bolhas de página têm o seu próprio modelo de preços. As bolhas de página premium seguem o modelo de preços dos discos geridos, enquanto as bolhas de página padrão são faturadas no tamanho usado e em cada transação. Para obter mais informações, consulte a página de preços da [Azure Page Blobs](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

## <a name="page-blob-features"></a>Funcionalidades de blob de página

### <a name="rest-api"></a>API REST

Consulte o seguinte documento para começar [a desenvolver-se utilizando bolhas de página](./storage-quickstart-blobs-dotnet.md). Como exemplo, veja como aceder a bolhas de página usando a Biblioteca do Cliente de Armazenamento para .NET. 

O diagrama seguinte descreve as relações globais entre contas, contentores e bolhas de página.

![Screenshot mostrando relações entre conta, recipientes e bolhas de página](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>Criando uma bolha de página vazia de um tamanho especificado

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Primeiro, faça referência a um contentor. Para criar uma bolha de página, ligue para o método GetPageBlobClient e, em seguida, chame o método [PageBlobClient.Create.](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.create) Passe no tamanho máximo para a bolha criar. Esse tamanho deve ser um múltiplo de 512 bytes.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_CreatePageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Para criar uma bolha de página, criamos primeiro um objeto **CloudBlobClient,** com o URI base para aceder ao armazenamento de bolhas para a sua conta de armazenamento *(pbaccount* na figura 1) juntamente com o objeto **StorageCredentialsAccountAndKey,** como mostrado no exemplo seguinte. O exemplo mostra então a criação de uma referência a um objeto **CloudBlobContainer** e, em seguida, a criação do recipiente *(testvhds*) se já não existir. Em seguida, utilizando o objeto **CloudBlobContainer,** crie uma referência a um objeto **CloudPageBlob** especificando o nome blob da página (os4.vhd) para aceder. Para criar a bolha de página, ligue para [CloudPageBlob.Create,](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.create)passando no tamanho máximo para a bolha criar. O *blobSize* deve ser um múltiplo de 512 bytes.

```csharp
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

long OneGigabyteAsBytes = 1024 * 1024 * 1024;
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("testvhds");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();

CloudPageBlob pageBlob = container.GetPageBlobReference("os4.vhd");
pageBlob.Create(16 * OneGigabyteAsBytes);
```

---

#### <a name="resizing-a-page-blob"></a>Redimensionar uma bolha de página

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Para redimensionar uma bolha de página após a criação, utilize o método [Resize.](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.resize) O tamanho solicitado deve ser um múltiplo de 512 bytes.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ResizePageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Para redimensionar uma bolha de página após a criação, utilize o método [Resize.](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.resize) O tamanho solicitado deve ser um múltiplo de 512 bytes.

```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes);
```

---

#### <a name="writing-pages-to-a-page-blob"></a>Escrever páginas para uma bolha de página

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Para escrever páginas, utilize o método [PageBlobClient.UploadPages.](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.uploadpages)  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_WriteToPageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Para escrever páginas, utilize o método [CloudPageBlob.WritePages.](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.beginwritepages)  

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

---

Isto permite-lhe escrever um conjunto sequencial de páginas até 4MBs. A compensação a que está escrita deve começar com um limite de 512 byte (startoff % 512 == 0) e terminar num limite 512 - 1. 

Assim que um pedido de escrita para um conjunto sequencial de páginas sucede no serviço blob e é replicado para durabilidade e resiliência, a escrita cometeu, e o sucesso é devolvido ao cliente.  

O diagrama abaixo mostra 2 operações de escrita separadas:

![Um diagrama mostrando as duas opções de escrita separadas.](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  Uma operação de escrita a partir de offset 0 de comprimento 1024 bytes 
2.  Uma operação de escrita a partir do offset 4096 de comprimento 1024 

#### <a name="reading-pages-from-a-page-blob"></a>Ler páginas de uma bolha de página

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Para ler páginas, utilize o método [PageBlobClient.Descarregue](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.download) para ler uma série de bytes a partir da bolha de página. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ReadFromPageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Para ler páginas, utilize o método [CloudPageBlob.DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.icloudblob.downloadrangetobytearray) para ler uma série de bytes a partir da bolha de página. 

```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```

---

Isto permite-lhe descarregar a bolha completa ou a gama de bytes a partir de qualquer offset na bolha. Ao ler, o offset não tem de começar com um múltiplo de 512. Ao ler bytes a partir de uma página NUL, o serviço devolve zero bytes.

O seguinte número mostra uma operação de Leitura com uma compensação de 256 e um tamanho de alcance de 4352. Os dados devolvidos são destacados em laranja. Zeros são devolvidos para páginas NUL.

![Um diagrama mostrando uma operação de Leitura com uma compensação de 256 e um tamanho de alcance de 4352](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Se tiver uma bolha pouco povoada, talvez queira apenas baixar as regiões de página válidas para evitar pagar a saída de bytes zero e reduzir a latência do download.  

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Para determinar quais páginas são apoiadas por dados, utilize [PageBlobClient.GetPageRanges](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.getpageranges). Em seguida, pode enumerar as gamas devolvidas e descarregar os dados em cada intervalo. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ReadValidPageRegionsFromPageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Para determinar quais páginas são apoiadas por dados, utilize [CloudPageBlob.GetPageRanges](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.getpageranges). Em seguida, pode enumerar as gamas devolvidas e descarregar os dados em cada intervalo. 

```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the range size
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and
    // place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // Then use the buffer for the page range just read
}
```

---

#### <a name="leasing-a-page-blob"></a>Alugar uma bolha de página

A operação Lease Blob estabelece e gere um bloqueio numa bolha para as operações de escrita e eliminação. Esta operação é útil em cenários em que uma bolha de página está a ser acedida a vários clientes para garantir que apenas um cliente pode escrever para a bolha de cada vez. A Azure Disks, por exemplo, aproveita este mecanismo de locação para garantir que o disco é gerido apenas por um único VM. A duração do bloqueio pode ser de 15 a 60 segundos, ou pode ser infinita. Consulte a documentação [aqui](/rest/api/storageservices/lease-blob) para mais detalhes.

Além das RICAS APIs de REST, as bolhas de página também fornecem acesso partilhado, durabilidade e segurança reforçada. Cobriremos mais pormenorizadamente esses benefícios nos próximos parágrafos. 

### <a name="concurrent-access"></a>Acesso simultâneo

A página blobs REST API e o seu mecanismo de leasing permite que as aplicações acedam à bolha de página de vários clientes. Por exemplo, digamos que é necessário construir um serviço de cloud distribuído que partilhe objetos de armazenamento com vários utilizadores. Poderia ser uma aplicação web que serve uma grande coleção de imagens para vários utilizadores. Uma opção para implementar isto é usar um VM com discos anexados. As desvantagens desta incluem, (i) a restrição de que um disco só pode ser ligado a um único VM, limitando assim a escalabilidade, a flexibilidade e o aumento dos riscos. Se houver algum problema com o VM ou o serviço em execução no VM, então devido ao arrendamento, a imagem é inacessível até que o contrato de arrendamento expire ou seja quebrado; e (ii) Custo adicional de ter um IaaS VM. 

Uma opção alternativa é utilizar as bolhas de página diretamente através de Azure Storage REST APIs. Esta opção elimina a necessidade de dispendiosos IaaS VMs, oferece total flexibilidade de acesso direto de vários clientes, simplifica o modelo de implementação clássico, eliminando a necessidade de anexar/desprender discos, e elimina o risco de problemas no VM. E, fornece o mesmo nível de desempenho para operações de leitura/escrita aleatórias como um disco

### <a name="durability-and-high-availability"></a>Durabilidade e elevada disponibilidade

Tanto o armazenamento standard como o armazenamento premium são um armazenamento durável onde os dados blob da página são sempre replicados para garantir a durabilidade e alta disponibilidade. Para obter mais informações sobre a Redundância de Armazenamento Azure, consulte esta [documentação.](../common/storage-redundancy.md) O Azure tem proporcionado consistentemente durabilidade de qualidade empresarial para discos IaaS e bolhas de página, com uma taxa de [insucesso anualizada](https://en.wikipedia.org/wiki/Annualized_failure_rate)líder do setor em 0%.

### <a name="seamless-migration-to-azure"></a>Migração sem emenda para Azure

Para os clientes e desenvolvedores que estão interessados em implementar a sua própria solução de backup personalizada, a Azure também oferece instantâneos incrementais que apenas detêm os deltas. Esta funcionalidade evita o custo da cópia inicial completa, o que reduz consideravelmente o custo de backup. Juntamente com a capacidade de ler e copiar dados diferenciais de forma eficiente, esta é outra poderosa capacidade que permite ainda mais inovações dos desenvolvedores, levando a uma experiência de backup e recuperação de desastres (DR) de melhor classe no Azure. Pode configurar a sua própria solução de backup ou DR para os seus VMs no Azure utilizando o [Blob Snapshot](/rest/api/storageservices/snapshot-blob) juntamente com a [API Get Page Ranges](/rest/api/storageservices/get-page-ranges) e a [API incremental blob,](/rest/api/storageservices/incremental-copy-blob) que pode utilizar para copiar facilmente os dados incrementais para DR. 

Além disso, muitas empresas têm cargas de trabalho críticas já em funcionamento em centros de dados no local. Para migrar a carga de trabalho para a nuvem, uma das principais preocupações seria a quantidade de tempo de inatividade necessário para a cópia dos dados, e o risco de problemas imprevistos após a transição. Em muitos casos, o tempo de inatividade pode ser um showstopper para a migração para a nuvem. Utilizando as bolhas de página REST API, a Azure aborda este problema, permitindo a migração em nuvem com o mínimo de perturbação para cargas de trabalho críticas. 

Por exemplo, sobre como tirar uma fotografia e como restaurar uma mancha de página a partir de uma imagem, consulte a [configuração de um processo de backup utilizando um](../../virtual-machines/windows/incremental-snapshots.md) artigo de instantâneo incremental.