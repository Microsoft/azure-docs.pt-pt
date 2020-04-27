---
title: Visão geral das bolhas de página azure [ Microsoft Docs
description: Uma visão geral das bolhas de página azure e suas vantagens, incluindo casos de uso com scripts de amostra.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/13/2019
ms.author: tamram
ms.reviewer: wielriac
ms.subservice: blobs
ms.openlocfilehash: 060e1d01e5f078bad9852ae35d0af9142192a7b6
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "68985624"
---
# <a name="overview-of-azure-page-blobs"></a>Visão geral das bolhas de página azure

O Azure Storage oferece três tipos de armazenamento de bolhas: Block Blobs, Append Blobs e blobs de página. As bolhas de bloco são compostas por blocos e são ideais para armazenar texto ou ficheiros binários, e para carregar ficheiros grandes de forma eficiente. As bolhas de apêndice também são compostas por blocos, mas são otimizadas para operações de apêndice, tornando-as ideais para cenários de registo. As bolhas de página são compostas por 512 páginas de byte até 8 TB em tamanho total e são projetadas para operações frequentes de leitura/escrita aleatórias. As bolhas de página são a fundação da Azure IaaS Disks. Este artigo centra-se em explicar as funcionalidades e benefícios das bolhas de página.

As bolhas de página são uma coleção de 512 páginas byte, que fornecem a capacidade de ler/escrever gamas arbitrárias de bytes. Assim, as bolhas de página são ideais para armazenar estruturas de dados baseadas em índices e escassas, como OS e discos de dados para Máquinas Virtuais e Bases de Dados. Por exemplo, o Azure SQL DB utiliza as bolhas de página como o armazenamento persistente subjacente para as suas bases de dados. Além disso, as bolhas de página também são frequentemente usadas para ficheiros com atualizações baseadas em gama.  

As principais características das bolhas de página Azure são a sua interface REST, a durabilidade do armazenamento subjacente e as capacidades de migração perfeitas para o Azure. Estas características são discutidas mais detalhadamente na secção seguinte. Além disso, as bolhas de página Azure são atualmente suportadas em dois tipos de armazenamento: Armazenamento Premium e Armazenamento Padrão. O Armazenamento Premium é projetado especificamente para cargas de trabalho que requerem um desempenho consistente de alto desempenho e baixa latência tornando as bolhas de página premium ideais para cenários de armazenamento de alto desempenho. As contas de armazenamento padrão são mais rentáveis para o funcionamento de cargas de trabalho insensíveis à latência.

## <a name="sample-use-cases"></a>Casos de utilização de amostras

Vamos discutir alguns casos de uso para bolhas de página começando com Discos Azure IaaS. As bolhas de página azure são a espinha dorsal da plataforma de discos virtuais para o Azure IaaS. Tanto o Azure OS como os discos de dados são implementados como discos virtuais onde os dados são persistentes na plataforma de Armazenamento Azure e depois entregues às máquinas virtuais para o máximo desempenho. Os Discos Azure são persistidos em [formato Hyper-V VHD](https://technet.microsoft.com/library/dd979539.aspx) e armazenados como uma [página blob](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) no Armazenamento Azure. Além de utilizar discos virtuais para VMs Azure IaaS, as bolhas de página também permitem cenários paaS e DBaaS, como o serviço Azure SQL DB, que atualmente utiliza bolhas de página para armazenar dados SQL, permitindo operações de leitura aleatória rápida para a base de dados. Outro exemplo seria se tiver um serviço PaaS para acesso partilhado aos meios de comunicação para aplicações colaborativas de edição de vídeo, as bolhas de página permitem o acesso rápido a locais aleatórios nos meios de comunicação. Também permite a edição rápida e eficiente e a fusão dos mesmos meios por vários utilizadores. 

Os serviços da Microsoft de primeira parte como o Azure Site Recovery, o Azure Backup, bem como muitos desenvolvedores de terceiros implementaram inovações líderes no setor usando a interface REST da page blob. Seguem-se alguns dos cenários únicos implementados no Azure: 

* Gestão incremental de instantâneos direcionados para aplicações: As aplicações podem alavancar os instantâneos de página blob e ASREST PARA salvar os pontos de verificação da aplicação sem incorrer em duplicação dispendiosa de dados. O Azure Storage suporta instantâneos locais para bolhas de página, que não requerem copiar toda a bolha. Estas APIs de instantâneo público também permitem o acesso e a cópia de deltas entre instantâneos.
* Migração ao vivo de aplicações e dados de instalações para cloud: Copie os dados no local e use APIs REST para escrever diretamente para uma bolha de página Azure enquanto o VM no local continua a funcionar. Uma vez que o alvo tenha sido apanhado, pode rapidamente falhar com o Azure VM usando esses dados. Desta forma, pode migrar os seus VMs e discos virtuais de instalações para cloud com o mínimo de tempo de inatividade, uma vez que a migração de dados ocorre em segundo plano enquanto continua a utilizar o VM e o tempo de inatividade necessário para a falha será curto (em minutos).
* O acesso partilhado [baseado em SAS,](../common/storage-sas-overview.md) que permite cenários como múltiplos leitores e único escritor com apoio ao controlo da condivisa.

## <a name="page-blob-features"></a>Funcionalidades de blob de página

### <a name="rest-api"></a>API REST

Consulte o seguinte documento para começar [a desenvolver-se utilizando bolhas](storage-dotnet-how-to-use-blobs.md)de página . Como exemplo, veja como aceder a blobs de página usando a Biblioteca de Clientes de Armazenamento para .NET. 

O diagrama seguinte descreve as relações gerais entre contas, contentores e bolhas de página.

![Screenshot mostrando relações entre conta, contentores e bolhas de página](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>Criar uma bolha de página vazia de um tamanho especificado

Para criar uma bolha de página, criamos primeiro um objeto **CloudBlobClient,** com a base URI para aceder ao armazenamento blob para a sua conta de armazenamento *(pbaccount* na figura 1) juntamente com o objeto **StorageCredenciaaccountAndKey,** como mostra o exemplo seguinte. O exemplo mostra então a criação de uma referência a um objeto **CloudBlobContainer** e, em seguida, a criação do recipiente *(testvhds)* se já não existir. Em seguida, utilizando o objeto **CloudBlobContainer,** crie uma referência a um objeto **CloudPageBlob** especificando o nome blob da página (os4.vhd) para aceder. Para criar a bolha da página, ligue para [CloudPageBlob.Create](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.create), passando no tamanho máximo para a bolha criar. O *blobSize* deve ser um múltiplo de 512 bytes.

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

#### <a name="resizing-a-page-blob"></a>Redimensionando uma bolha de página

Para redimensionar uma bolha de página após a criação, use o método [Resize.](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.resize) O tamanho solicitado deve ser um múltiplo de 512 bytes.

```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes);
```

#### <a name="writing-pages-to-a-page-blob"></a>Escrever páginas para uma página blob

Para escrever páginas, utilize o método [CloudPageBlob.WritePages.](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.beginwritepages)  Isto permite-lhe escrever um conjunto sequencial de páginas até 4MBs. A contrapartida a que está a ser escrita deve começar num limite de 512 bytes (partidaOffset % 512 == 0), e terminar num limite de 512 - 1.  O exemplo de código que se segue mostra como chamar **WritePages** para uma bolha:

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

Assim que um pedido de escrita para um conjunto sequencial de páginas sucede no serviço de blob e é replicado para durabilidade e resiliência, a escrita cometeu, e o sucesso é devolvido ao cliente.  

O diagrama abaixo mostra 2 operações de escrita separadas:

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  Uma operação de escrita a partir de 0 de comprimento 1024 bytes 
2.  Uma operação de escrita a partir de 4096 de comprimento 1024 

#### <a name="reading-pages-from-a-page-blob"></a>Ler páginas de uma página blob

Para ler páginas, utilize o método [CloudPageBlob.DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.icloudblob.downloadrangetobytearray) para ler uma gama de bytes a partir da bolha da página. Isto permite-lhe descarregar a bolha completa ou gama de bytes a partir de qualquer contrapartida na bolha. Ao ler, a contrapartida não tem de começar num múltiplo de 512. Ao ler bytes de uma página NUL, o serviço devolve zero bytes.

```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```

O número seguinte mostra uma operação de Leitura com um offset de 256 e um tamanho de gama de 4352. Os dados devolvidos são destacados em laranja. Os zeros são devolvidos para as páginas NUL.

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Se tiver uma bolha pouco povoada, talvez queira apenas descarregar as regiões de página válidas para evitar pagar a saída de zero bytes e reduzir a latência de download.  Para determinar quais as páginas que são apoiadas por dados, utilize [cloudPageBlob.GetPageRanges](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.getpageranges). Em seguida, pode enumerar as gamas devolvidas e descarregar os dados em cada gama. 

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

#### <a name="leasing-a-page-blob"></a>Alugar uma bolha de página

A operação Lease Blob estabelece e gere um bloqueio numa bolha para escrever e apagar operações. Esta operação é útil em cenários em que uma página blob está sendo acessada de vários clientes para garantir que apenas um cliente pode escrever para a bolha de cada vez. A Azure Disks, por exemplo, aproveita este mecanismo de locação para garantir que o disco só é gerido por um único VM. A duração do bloqueio pode ser de 15 a 60 segundos, ou pode ser infinita. Consulte a documentação [aqui](/rest/api/storageservices/lease-blob) para mais detalhes.

Além das ricas APIs REST, as bolhas de página também fornecem acesso partilhado, durabilidade e segurança reforçada. Cobriremos mais pormenorizadamente esses benefícios nos próximos parágrafos. 

### <a name="concurrent-access"></a>Acesso simultâneo

A página blobs REST API e o seu mecanismo de leasing permitem que as aplicações acedam à página blob de vários clientes. Por exemplo, digamos que precisa de construir um serviço de nuvem distribuído que partilhe objetos de armazenamento com vários utilizadores. Pode ser uma aplicação web que serve uma grande coleção de imagens a vários utilizadores. Uma opção para implementar isto é utilizar um VM com discos anexados. As desvantagens desta situação incluem, (i) a restrição de que um disco só pode ser ligado a um único VM limitando assim a escalabilidade, flexibilidade e aumento dos riscos. Se houver algum problema com o VM ou com o serviço em funcionamento no VM, então devido ao arrendamento, a imagem fica inacessível até que o arrendamento expire ou seja quebrado; e (ii) Custo adicional de ter um VM IaaS. 

Uma opção alternativa é usar as bolhas de página diretamente através de APIs REST DE Armazenamento Azure. Esta opção elimina a necessidade de dispendiosos VMs IaaS, oferece total flexibilidade de acesso direto de vários clientes, simplifica o modelo de implementação clássico eliminando a necessidade de anexar/desprender discos, e elimina o risco de problemas no VM. E, fornece o mesmo nível de desempenho para operações aleatórias de leitura/escrita como um disco

### <a name="durability-and-high-availability"></a>Durabilidade e elevada disponibilidade

Tanto o armazenamento Standard como o premium são armazenamento suportável onde os dados blob da página são sempre replicados para garantir durabilidade e alta disponibilidade. Para mais informações sobre o Despedimento de Armazenamento Azure, consulte esta [documentação.](../common/storage-redundancy.md) O Azure tem consistentemente proporcionado durabilidade de nível empresarial para discos IaaS e bolhas de página, com uma taxa de [insucesso anualizada](https://en.wikipedia.org/wiki/Annualized_failure_rate)líder do setor de zero por cento.

### <a name="seamless-migration-to-azure"></a>Migração perfeita para Azure

Para os clientes e desenvolvedores que estão interessados em implementar a sua própria solução de backup personalizada, o Azure também oferece instantâneos incrementais que apenas detêm os deltas. Esta função evita o custo da cópia completa inicial, o que reduz consideravelmente o custo de backup. Juntamente com a capacidade de ler e copiar dados diferenciais de forma eficiente, esta é outra capacidade poderosa que permite ainda mais inovações por parte dos desenvolvedores, levando a uma experiência de melhor backup em classe e recuperação de desastres (DR) no Azure. Pode configurar a sua própria solução de backup ou DR para os seus VMs no Azure utilizando [o Blob Snapshot](/rest/api/storageservices/snapshot-blob) juntamente com a API get [Page Ranges](/rest/api/storageservices/get-page-ranges) e a Incremental [Copy Blob](/rest/api/storageservices/incremental-copy-blob) API, que pode utilizar para copiar facilmente os dados incrementais para DR. 

Além disso, muitas empresas têm cargas de trabalho críticas já em funcionamento em centros de dados no local. Para a migração da carga de trabalho para a nuvem, uma das principais preocupações seria a quantidade de tempo de inatividade necessário para copiar os dados, e o risco de problemas imprevistos após a transição. Em muitos casos, o tempo de inatividade pode ser um showstopper para a migração para a nuvem. Utilizando a página blobs REST API, Azure aborda este problema permitindo a migração em nuvem com o mínimo de perturbação para cargas de trabalho críticas. 

Por exemplo, sobre como tirar uma foto e como restaurar uma bolha de página a partir de um instantâneo, consulte a configuração de um processo de backup usando um artigo de [instantâneoincremental.](../../virtual-machines/windows/incremental-snapshots.md)
