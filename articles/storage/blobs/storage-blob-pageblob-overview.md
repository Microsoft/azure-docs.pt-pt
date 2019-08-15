---
title: Visão geral dos BLOBs de páginas do Azure | Microsoft Docs
description: Uma visão geral dos BLOBs de páginas do Azure e suas vantagens, incluindo casos de uso com scripts de exemplo.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/13/2019
ms.author: tamram
ms.reviewer: wielriac
ms.subservice: blobs
ms.openlocfilehash: 060e1d01e5f078bad9852ae35d0af9142192a7b6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985624"
---
# <a name="overview-of-azure-page-blobs"></a>Visão geral dos BLOBs de páginas do Azure

O armazenamento do Azure oferece três tipos de armazenamento de BLOBs: Blobs de blocos, blobs de acréscimo e blobs de páginas. Blobs de blocos são compostos de blocos e são ideais para armazenar arquivos de texto ou binários e para carregar arquivos grandes com eficiência. Os blobs de acréscimo também são compostos de blocos, mas são otimizados para operações de acréscimo, tornando-os ideais para cenários de registro em log. Os blobs de páginas são compostos de páginas de 512 bytes de até 8 TB no tamanho total e são projetados para operações de leitura/gravação aleatórias frequentes. Os blobs de páginas são a base dos discos IaaS do Azure. Este artigo se concentra em explicar os recursos e os benefícios dos BLOBs de páginas.

Blobs de páginas são uma coleção de páginas de 512 bytes, que fornecem a capacidade de ler/gravar intervalos arbitrários de bytes. Portanto, OS blobs de páginas são ideais para armazenar estruturas de dados esparsas e baseadas em índice, como o sistema operacional e discos de dados para máquinas virtuais e bancos de dados. Por exemplo, o BD SQL do Azure usa blobs de página como o armazenamento persistente subjacente para seus bancos de dados. Além disso, os blobs de páginas também são geralmente usados para arquivos com atualizações baseadas em intervalos.  

Os principais recursos dos BLOBs de páginas do Azure são sua interface REST, a durabilidade do armazenamento subjacente e os recursos de migração direta para o Azure. Esses recursos serão discutidos mais detalhadamente na próxima seção. Além disso, no momento, os blobs de páginas do Azure têm suporte em dois tipos de armazenamento: Armazenamento Premium e armazenamento Standard. O armazenamento Premium é projetado especificamente para cargas de trabalho que exigem alto desempenho e baixa latência consistentes, tornando os blobs de páginas Premium ideais para cenários de armazenamento de alto desempenho. As contas de armazenamento standard são mais econômicas para executar cargas de trabalho que não fazem distinção de latência.

## <a name="sample-use-cases"></a>Casos de uso de exemplo

Vamos discutir alguns casos de uso para BLOBs de páginas começando com discos IaaS do Azure. Os blobs de páginas do Azure são o backbone da plataforma de discos virtuais para o Azure IaaS. O sistema operacional do Azure e os discos de dados são implementados como discos virtuais onde os dados são permanentemente persistidos na plataforma de armazenamento do Azure e, em seguida, entregues às máquinas virtuais para obter o máximo de desempenho. Os discos do Azure são persistidos no [formato VHD](https://technet.microsoft.com/library/dd979539.aspx) do Hyper-V e armazenados como um [blob de páginas](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) no armazenamento do Azure. Além de usar discos virtuais para VMs de IaaS do Azure, os blobs de páginas também habilitam cenários de PaaS e DBaaS, como o serviço de BD SQL do Azure, que atualmente usa blobs de página para armazenar dados SQL, permitindo operações rápidas de leitura/gravação aleatórias para o Database. Outro exemplo seria se você tiver um serviço PaaS para acesso de mídia compartilhada para aplicativos de edição de vídeo colaborativo, blobs de página permitem acesso rápido a locais aleatórios na mídia. Ele também permite a edição e a mesclagem rápidas e eficientes da mesma mídia por vários usuários. 

Os serviços da Microsoft de terceiros como Azure Site Recovery, o backup do Azure, bem como muitos desenvolvedores de terceiros implementaram as inovações líderes do setor usando a interface REST do blob de páginas. A seguir estão alguns dos cenários exclusivos implementados no Azure: 

* Gerenciamento de instantâneo incremental direcionado ao aplicativo: Os aplicativos podem aproveitar instantâneos de blob de páginas e APIs REST para salvar os pontos de verificação do aplicativo sem incorrer em uma duplicação dispendiosa de dados. O armazenamento do Azure dá suporte a instantâneos locais para BLOBs de páginas, que não exigem a cópia de todo o blob. Essas APIs de instantâneo público também permitem o acesso e a cópia de deltas entre instantâneos.
* Migração dinâmica de aplicativos e dados do local para a nuvem: Copie os dados locais e use as APIs REST para gravar diretamente em um blob de páginas do Azure enquanto a VM local continua a ser executada. Depois que o destino for atualizado, você poderá fazer failover rapidamente para a VM do Azure usando esses dados. Dessa forma, você pode migrar suas VMs e discos virtuais do local para a nuvem com tempo de inatividade mínimo, já que a migração de dados ocorre em segundo plano enquanto você continua a usar a VM e o tempo de inatividade necessário para o failover será curto (em minutos).
* Acesso compartilhado [baseado em SAS](../common/storage-sas-overview.md) , que permite cenários como vários leitores e gravador único com suporte para controle de simultaneidade.

## <a name="page-blob-features"></a>Funcionalidades de blob de página

### <a name="rest-api"></a>API REST

Consulte o documento a seguir para começar a [desenvolver usando blobs de página](storage-dotnet-how-to-use-blobs.md). Por exemplo, veja como acessar os blobs de página usando a biblioteca de cliente de armazenamento para .NET. 

O diagrama a seguir descreve as relações gerais entre a conta, os contêineres e os blobs de páginas.

![Captura de tela mostrando relações entre a conta, os contêineres e os blobs de páginas](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>Criando um blob de páginas vazio de um tamanho especificado

Para criar um blob de páginas, primeiro criamos um objeto **CloudBlobClient** , com o URI base para acessar o armazenamento de BLOBs da sua conta de armazenamento (*pbaccount* na Figura 1) junto com o objeto **StorageCredentialsAccountAndKey** , conforme mostrado no exemplo a seguir. Em seguida, o exemplo mostra a criação de uma referência a um objeto **CloudBlobContainer** e, em seguida, a criação do contêiner (*testvhds*), caso ele ainda não exista. Em seguida, usando o objeto **CloudBlobContainer** , crie uma referência a um objeto **CloudPageBlob** especificando o nome do blob de páginas (OS4. vhd) a ser acessado. Para criar o blob de páginas, chame [CloudPageBlob. Create](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.create), passando o tamanho máximo do blob a ser criado. O *blobSize* deve ser um múltiplo de 512 bytes.

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

#### <a name="resizing-a-page-blob"></a>Redimensionando um blob de páginas

Para redimensionar um blob de páginas após a criação [](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.resize) , use o método redimensionar. O tamanho solicitado deve ser um múltiplo de 512 bytes.

```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes);
```

#### <a name="writing-pages-to-a-page-blob"></a>Gravando páginas em um blob de páginas

Para gravar páginas, use o método [CloudPageBlob. WritePages](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.beginwritepages) .  Isso permite que você grave um conjunto sequencial de páginas até 4 MB. O deslocamento que está sendo gravado deve iniciar em um limite de 512 bytes (startingOffset% 512 = = 0) e terminar em um limite de 512-1.  O exemplo de código a seguir mostra como chamar **WritePages** para um blob:

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

Assim que uma solicitação de gravação para um conjunto sequencial de páginas for bem-sucedida no serviço BLOB e for replicada para durabilidade e resiliência, a gravação será confirmada e o sucesso retornará ao cliente.  

O diagrama abaixo mostra duas operações de gravação separadas:

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  Uma operação de gravação começando no deslocamento 0 de comprimento de 1024 bytes 
2.  Uma operação de gravação começando com o deslocamento 4096 de comprimento 1024 

#### <a name="reading-pages-from-a-page-blob"></a>Lendo páginas de um blob de páginas

Para ler páginas, use o método [CloudPageBlob. DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.icloudblob.downloadrangetobytearray) para ler um intervalo de bytes do blob de páginas. Isso permite que você baixe o blob completo ou o intervalo de bytes a partir de qualquer deslocamento no BLOB. Ao ler, o deslocamento não precisa iniciar em um múltiplo de 512. Ao ler bytes de uma página NUL, o serviço retorna zero bytes.

```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```

A figura a seguir mostra uma operação de leitura com um deslocamento de 256 e um tamanho de intervalo de 4352. Os dados retornados são realçados em laranja. Zeros são retornados para páginas NUL.

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Se você tiver um blob populado de forma grosseira, convém apenas baixar as regiões de página válidas para evitar pagar pela saída de zero bytes e para reduzir a latência de download.  Para determinar quais páginas são apoiadas pelos dados, use [CloudPageBlob. GetPageRanges](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.getpageranges). Em seguida, você pode enumerar os intervalos retornados e baixar os dados em cada intervalo. 

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

#### <a name="leasing-a-page-blob"></a>Leasing de um blob de páginas

A operação de blob de concessão estabelece e gerencia um bloqueio em um blob para operações de gravação e exclusão. Essa operação é útil em cenários em que um blob de páginas está sendo acessado de vários clientes para garantir que apenas um cliente possa gravar no blob de cada vez. Os discos do Azure, por exemplo, aproveitam esse mecanismo de concessão para garantir que o disco seja gerenciado apenas por uma única VM. A duração do bloqueio pode ser de 15 a 60 segundos ou pode ser infinita. Consulte a documentação [aqui](/rest/api/storageservices/lease-blob) para obter mais detalhes.

Além das APIs REST avançadas, os blobs de páginas também fornecem acesso compartilhado, durabilidade e segurança aprimorada. Abordaremos esses benefícios mais detalhadamente nos próximos parágrafos. 

### <a name="concurrent-access"></a>Acesso simultâneo

A API REST de blobs de páginas e seu mecanismo de concessão permitem que os aplicativos acessem o blob de páginas de vários clientes. Por exemplo, digamos que você precise criar um serviço de nuvem distribuído que compartilhe objetos de armazenamento com vários usuários. Pode ser um aplicativo Web que serve uma grande coleção de imagens para vários usuários. Uma opção para implementar isso é usar uma VM com discos anexados. As desvantagens disso incluem, (i) a restrição de que um disco só pode ser anexado a uma única VM, limitando, assim, a escalabilidade, a flexibilidade e o aumento de riscos. Se houver um problema com a VM ou o serviço em execução na VM, devido à concessão, a imagem ficará inacessível até que a concessão expire ou seja interrompida; e (II) custo adicional de ter uma VM IaaS. 

Uma opção alternativa é usar os blobs de página diretamente por meio de APIs REST do armazenamento do Azure. Essa opção elimina a necessidade de VMs de IaaS dispendiosas, oferece flexibilidade total de acesso direto de vários clientes, simplifica o modelo de implantação clássico eliminando a necessidade de anexar/desanexar discos e elimina o risco de problemas na VM. E fornece o mesmo nível de desempenho para operações de leitura/gravação aleatórias como um disco

### <a name="durability-and-high-availability"></a>Durabilidade e alta disponibilidade

O armazenamento Standard e Premium é um armazenamento durável em que os dados de blob de páginas sempre são replicados para garantir durabilidade e alta disponibilidade. Para obter mais informações sobre a redundância de armazenamento do Azure, consulte esta [documentação](../common/storage-redundancy.md). O Azure fornece consistentemente a durabilidade de nível empresarial para discos de IaaS e blobs de páginas, com uma [taxa de falha anual](https://en.wikipedia.org/wiki/Annualized_failure_rate)de porcentagem de zero por cento no setor.

### <a name="seamless-migration-to-azure"></a>Migração direta para o Azure

Para clientes e desenvolvedores interessados em implementar sua própria solução de backup personalizada, o Azure também oferece instantâneos incrementais que só mantêm os deltas. Esse recurso evita o custo da cópia inicial completa, o que reduz muito o custo de backup. Junto com a capacidade de ler e copiar dados diferenciais de maneira eficiente, esse é outro recurso poderoso que permite ainda mais inovações de desenvolvedores, levando a uma experiência de DR (recuperação de desastres) e de backup de melhor qualidade no Azure. Você pode configurar sua própria solução de backup ou recuperação de desastre para suas VMs no Azure usando o [instantâneo de blob](/rest/api/storageservices/snapshot-blob) junto com a API [obter intervalos de página](/rest/api/storageservices/get-page-ranges) e a API de blob de [cópia incremental](/rest/api/storageservices/incremental-copy-blob) , que pode ser usada para copiar facilmente os dados incrementais para Dr. 

Além disso, muitas empresas têm cargas de trabalho críticas já em execução em data centers locais. Para migrar a carga de trabalho para a nuvem, uma das principais preocupações seria a quantidade de tempo de inatividade necessária para copiar os dados e o risco de problemas imprevistos após a alternância. Em muitos casos, o tempo de inatividade pode ser um impedimento para a migração para a nuvem. Usando a API REST de blobs de páginas, o Azure resolve esse problema habilitando a migração de nuvem com o mínimo de interrupção para cargas de trabalho críticas. 

Para obter exemplos de como tirar um instantâneo e como restaurar um blob de páginas de um instantâneo, consulte o artigo [configurar um processo de backup usando instantâneos incrementais](../../virtual-machines/windows/incremental-snapshots.md) .
