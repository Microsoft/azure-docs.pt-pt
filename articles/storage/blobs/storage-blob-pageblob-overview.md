---
title: Descrição geral dos blobs de páginas do Azure | Documentos da Microsoft
description: Uma visão geral dos blobs de páginas do Azure e suas vantagens, incluindo casos de uso com scripts de exemplo.
services: storage
author: anasouma
ms.service: storage
ms.topic: article
ms.date: 01/03/2019
ms.author: wielriac
ms.subservice: blobs
ms.openlocfilehash: b03da04c97475dcb9ce15f2ed69d7ca333d6f431
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57456215"
---
# <a name="overview-of-azure-page-blobs"></a>Descrição geral dos blobs de páginas do Azure

O armazenamento do Azure oferece três tipos de armazenamento de BLOBs: Blobs de página, Blobs de acréscimo e Blobs de blocos. Blobs de blocos são compostos de blocos e são ideais para armazenar texto ou arquivos binários e para carregar ficheiros grandes de forma eficiente. Acrescentar blobs são também constituídos por blocos, mas estão otimizados para operações de acréscimo, tornando-os ideal para cenários de registo. Blobs de páginas são constituídos por páginas de 512 bytes cópia de segurança para 8 TB de tamanho total e são desenvolvidos para operações de leitura/escrita aleatórias frequentes. Blobs de páginas são a base de discos de IaaS do Azure. Este artigo se concentra em explicar os recursos e benefícios de blobs de página.

Blobs de páginas são uma coleção de páginas de 512 bytes, que fornecem a capacidade de leitura/escrita arbitrários intervalos de bytes. Por conseguinte, os blobs de páginas são ideais para armazenar as estruturas de dados de com base no índice e esparsos, como o SO e discos de dados para máquinas virtuais e bases de dados. Por exemplo, BD SQL do Azure utiliza os blobs de páginas como o armazenamento persistente subjacente para seus bancos de dados. Além disso, os blobs de páginas também geralmente são utilizados para os ficheiros com atualizações baseado em intervalo.  

As principais funcionalidades de blobs de páginas do Azure são a REST interface, a durabilidade de armazenamento subjacente e os recursos de migração perfeita para o Azure. Estas funcionalidades são abordadas mais detalhadamente na secção seguinte. Além disso, os blobs de páginas do Azure atualmente são suportados em dois tipos de armazenamento: Armazenamento Premium e o armazenamento Standard. O armazenamento Premium foi concebido especificamente para cargas de trabalho que requerem consistente de alto desempenho e latência baixa, tornando os blobs de páginas premium ideal para bases de dados de armazenamento de dados de elevado desempenho.  Armazenamento Standard é mais rentável para executar cargas de trabalho insensível à latência.

## <a name="sample-use-cases"></a>Casos de utilização de exemplo

Vamos discutir alguns casos de utilização para blobs de páginas, começando com discos de IaaS do Azure. Blobs de páginas do Azure são a estrutura principal da plataforma de discos virtuais de IaaS do Azure. SO do Azure e discos de dados são implementados como discos virtuais onde os dados são mantidos de maneira duradoura na plataforma do armazenamento do Azure e, em seguida, entregue para as máquinas virtuais para um desempenho máximo. Discos do Azure são mantidos no Hyper-V [formato VHD](https://technet.microsoft.com/library/dd979539.aspx) e armazenados como um [BLOBs de páginas](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) no armazenamento do Azure. Além de utilizar discos virtuais para VMs IaaS do Azure, blobs de páginas também ativar cenários de PaaS e DBaaS como o serviço de BD SQL do Azure, que utiliza atualmente a blobs de páginas para armazenar dados SQL, permitindo a operações de leitura-escrita aleatórias rápida para a base de dados. Outro exemplo seria se tiver um serviço de PaaS para acesso de suporte de dados partilhado para aplicações de edição de vídeo colaborativos, blobs de páginas permitem um acesso rápido em locais aleatórios no suporte de dados. Também permite a rápida e eficiente edição e intercalação do mesmo suporte de dados por vários utilizadores. 

Microsoft serviços proprietários, como o Azure Site Recovery, cópia de segurança do Azure, bem como muitos desenvolvedores de terceiros implementaram inovações líderes da indústria, usando a interface REST do blob de páginas. Seguem-se alguns dos cenários exclusivos implementados no Azure: 
* Gestão de aplicações direcionadas instantâneo incremental: Aplicativos podem tirar partido dos instantâneos de blob de página e REST APIs para salvar os pontos de verificação do aplicativo sem incorrer em duplicação dispendiosa de dados. O armazenamento do Azure suporta os instantâneos locais para blobs de páginas, que não necessitam de copiar o blob inteiro. Estes instantâneos pública APIs também permitem aceder e copiar de deltas entre os instantâneos.
* Migração de aplicativos e dados de em direto no local para a cloud: Copiar dados no local e utilizar REST APIs para gravar diretamente num blob de página do Azure ao mesmo tempo no local que VM continua a ser executado. Assim que o destino detetou, pode rapidamente ativação pós-falha para VM do Azure com esses dados. Dessa forma, pode migrar as suas VMs e discos virtuais a partir no local para a cloud com o tempo de inatividade mínimo, uma vez que a migração de dados ocorre em segundo plano enquanto continua a utilizar a VM e o tempo de inatividade necessário para a ativação pós-falha será curtos (em minutos).
* [Baseado em SAS](../common/storage-dotnet-shared-access-signature-part-1.md) acesso, o que permite cenários como vários leitores e único e redator com suporte para controlo de simultaneidade partilhado.

## <a name="page-blob-features"></a>Funcionalidades de blob de página

### <a name="rest-api"></a>API REST
Consulte o documento seguinte para começar com [desenvolver usando blobs de páginas](storage-dotnet-how-to-use-blobs.md). Por exemplo, ver como pode aceder a blobs de página com a biblioteca de cliente de armazenamento para .NET. 

O diagrama seguinte descreve as relações geral entre a conta, contentores e blobs de páginas.

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>Criar um blob de página vazia de um tamanho especificado
Para criar um blob de página, primeiro, vamos criar um **CloudBlobClient** objeto com o URI de base para aceder ao armazenamento de BLOBs para a sua conta de armazenamento (*pbaccount* na figura 1) juntamente com o  **StorageCredentialsAccountAndKey** de objeto, conforme mostrado no exemplo a seguir. O exemplo mostra, em seguida, criar uma referência a um **CloudBlobContainer** objeto e, em seguida, a criação do contentor (*testvhds*) se ainda não exista. Em seguida, utilizar o **CloudBlobContainer** de objeto, criar uma referência para um **CloudPageBlob** objeto, especificando o nome de blob de página (os4.vhd) para o acesso. Para criar blob de página, chame [CloudPageBlob.Create](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.create?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudPageBlob_Create_System_Int64_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) passando o tamanho máximo para o blob a criar. O *blobSize* tem de ser um múltiplo de 512 bytes.

```csharp
using Microsoft.WindowsAzure.StorageClient;
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

#### <a name="resizing-a-page-blob"></a>Redimensionar um blob de página
Para redimensionar um blob de página após a criação, utilize o [redimensionar](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.resize?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudPageBlob_Resize_System_Int64_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) API. O tamanho pedido deve ser um múltiplo de 512 bytes.
```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes); 
```

#### <a name="writing-pages-to-a-page-blob"></a>Escrever páginas para um blob de página
Para escrever páginas, utilize o [CloudPageBlob.WritePages](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.beginwritepages?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudPageBlob_BeginWritePages_System_IO_Stream_System_Int64_System_String_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_System_AsyncCallback_System_Object_) método.  Isto permite-lhe escrever um conjunto sequencial de páginas até 4MBs. Tem de começar o deslocamento a ser escrito num limite de 512 bytes (startingOffset % 512 = = 0) e de fim num limite de 512 - 1.  O exemplo de código seguinte mostra como chamar **WritePages** para um blob:

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

Assim que uma solicitação de gravação para um conjunto sequencial de páginas tem êxito no serviço blob e é replicada para durabilidade e resiliência, a gravação assumiu o compromisso e êxito é devolvido ao cliente.  

O abaixo diagrama mostra operações de escrita de 2 separadas:

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  Uma operação de escrita a partir de deslocamento 0 de comprimento de 1024 bytes 
2.  Uma operação de escrita a partir de deslocamento 4096 de comprimento 1024 

#### <a name="reading-pages-from-a-page-blob"></a>Páginas de leitura de um blob de página
Para ler páginas, utilize o [CloudPageBlob.DownloadRangeToByteArray](/dotnet/api/microsoft.windowsazure.storage.blob.icloudblob.downloadrangetobytearray?view=azure-dotnet) método para ler um intervalo de bytes a partir do blob de página. Isto permite-lhe transferir o blob inteiro ou um intervalo de bytes a partir de qualquer compensação no blob. Durante a leitura, o deslocamento não tem de iniciar num múltiplo de 512. Durante a leitura de bytes de uma página NUL, o serviço devolve zero bytes.
```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```
A figura seguinte mostra uma operação de leitura com o BlobOffSet de 256 e rangeSize de 4352. Dados devolvidos estão realçados na cor de laranja. Para páginas NUL, são devolvidos os zeros

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Se tiver um blob de forma escassa preenchido, pode querer transferir apenas as regiões de página válida para evitar pagar egressing de zero bytes e para reduzir a latência de download.  Para determinar quais páginas estão apoiadas por dados, utilize [CloudPageBlob.GetPageRanges](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.getpageranges?view=azure-dotnet). Em seguida, pode enumerar os intervalos de retornado e transferir os dados em cada intervalo. 
```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the rangeSize
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // TODO: use the buffer for the page range just read
} 
```

#### <a name="leasing-a-page-blob"></a>Leasing um blob de página
A operação de Blob de concessão estabelece e gere um bloqueio num blob para escrita e operações de eliminação. Esta operação é útil em cenários em que um blob de página está sendo acessado a partir de vários clientes para garantir que apenas um cliente pode escrever para o blob ao mesmo tempo. Discos do Azure, por exemplo, aproveita isso leasing mecanismo para garantir que o disco apenas é gerenciado por uma única VM. A duração do bloqueio pode ser 15 a 60 segundos, ou pode ser infinita. Consulte a documentação [aqui](/rest/api/storageservices/lease-blob) para obter mais detalhes.

Para além das APIs de REST avançado, blobs de páginas também fornecem acesso partilhado, durabilidade e segurança reforçada. Abordaremos esses benefícios mais detalhadamente nos parágrafos seguintes. 

### <a name="concurrent-access"></a>Acesso simultâneo
REST API de blobs de página e seu mecanismo de locação permite que os aplicativos aceder a BLOBs de página a partir de vários clientes. Por exemplo, digamos que precisa para criar um serviço cloud distribuída que compartilha os objetos de armazenamento com vários utilizadores. É possível que um aplicativo web que serve uma grande coleção de imagens a vários utilizadores. Uma opção para implementar isto é utilizar uma VM com discos anexados. Desvantagens de incluem, (i) a restrição de que um disco apenas pode ser anexado a uma única VM, assim, limitar a escalabilidade, flexibilidade e aumentando os riscos. Se houver um problema com a VM ou o serviço em execução na VM, em seguida, devido a concessão, a imagem não está acessível até que a concessão expira ou for interrompida; e (ii) custo adicional de ter uma VM de IaaS. 

Uma opção alternativa é usar os blobs de página diretamente através das APIs de REST de armazenamento do Azure. Esta opção elimina a necessidade de VMs de IaaS dispendiosa, oferece total flexibilidade de acesso direto a partir de vários clientes, simplifica o modelo de implementação clássica, eliminando a necessidade de anexar/desanexar discos e elimina o risco de problemas na VM. E, fornece o mesmo nível de desempenho para operações de leitura/escrita aleatórias como um disco

### <a name="durability-and-high-availability"></a>Durabilidade e elevada disponibilidade
Armazenamento Standard e premium são armazenamento durável, onde os dados de blob de página são sempre replicados para garantir a durabilidade e elevada disponibilidade. Para obter mais informações sobre a redundância de armazenamento do Azure, consulte esta [documentação](../common/storage-redundancy.md). Azure forneceu consistentemente durabilidade de nível empresarial para o IaaS discos e blobs, com um líder do setor de páginas ZERO % [anuais a taxa de falhas](https://en.wikipedia.org/wiki/Annualized_failure_rate). Ou seja, Azure nunca perdeu a dados de blob de página de um cliente. 

### <a name="seamless-migration-to-azure"></a>Migração direta para o azure
Para os clientes e desenvolvedores que estejam interessados na sua própria solução de cópia de segurança personalizada de implementação, o Azure também oferece instantâneos incrementais que apenas contêm os deltas. Esse recurso evita o custo da cópia completa inicial, que reduz significativamente o custo de cópia de segurança. Juntamente com a capacidade de dados diferenciais com eficiência leitura e de cópia, esta é outra funcionalidade poderosa que permite ainda mais inovações dos desenvolvedores, que leva a uma experiência de (DR recuperação) melhor na classe e cópia de segurança no Azure. Pode configurar a sua própria cópia de segurança ou solução de DR para as VMs no Azure ao utilizar [instantâneo de Blob](/rest/api/storageservices/snapshot-blob) juntamente com o [intervalos obter páginas](/rest/api/storageservices/get-page-ranges) API e o [BLOBs de cópia Incremental](/rest/api/storageservices/incremental-copy-blob) API, que pode Utilize para copiar facilmente os dados incrementais para o DR. 

Além disso, muitas empresas têm cargas de trabalho críticas já em execução em datacenters no local. Para migrar a carga de trabalho para a cloud, uma das principais preocupações seria a quantidade de tempo de inatividade necessário para copiar os dados e o risco de problemas imprevistos depois da alternância de. Em muitos casos, o tempo de inatividade pode ser um impedimento para a migração para a cloud. Usando a página de blobs de REST API, o Azure aborda este problema, permitindo que a migração para a cloud com a mínima interrupção para cargas de trabalho críticas. 

Para obter exemplos sobre como tirar um instantâneo e como restaurar um blob de página a partir de um instantâneo, consulte a [programa de configuração de um processo de cópia de segurança com instantâneos incrementais](../../virtual-machines/windows/incremental-snapshots.md) artigo.
