---
title: Monte um sistema de arquivo virtual em uma piscina
description: Aprenda a montar um sistema de ficheiros virtual numa piscina de Lote.
ms.topic: how-to
ms.date: 08/13/2019
ms.openlocfilehash: 4e51e8a1f11d670515893a83398a0c6d7c6e9a46
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/24/2020
ms.locfileid: "83816034"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Monte um sistema de ficheiros virtual em uma piscina de lote

O Azure Batch suporta agora a montagem do armazenamento em nuvem ou um sistema de ficheiros externo sintetizador espartilho do Windows ou do Linux nas suas piscinas de Lote. Quando um nó computacional se junta a uma piscina, o sistema de ficheiros virtuais é montado e tratado como uma unidade local nesse nó. Pode montar sistemas de ficheiros como Ficheiros Azure, armazenamento Azure Blob, Sistema de Ficheiros de Rede (NFS), incluindo uma [cache Avere vFXT,](../avere-vfxt/avere-vfxt-overview.md)ou Sistema Comum de Ficheiros de Internet (CIFS).

Neste artigo, você aprenderá a montar um sistema de ficheiros virtuais em um conjunto de nós de computação usando a Biblioteca de Gestão de [Lotes para .NET](https://docs.microsoft.com/dotnet/api/overview/azure/batch?view=azure-dotnet).

> [!NOTE]
> A montagem de um sistema de ficheiros virtuais é suportada em piscinas de Lote criadas em ou depois de 2019-08-19. As piscinas de lote criadas antes de 2019-08-19 não suportam esta funcionalidade.
> 
> As APIs para a montagem de sistemas de ficheiros num nó de cálculo fazem parte da biblioteca [Batch .NET.](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch?view=azure-dotnet)

## <a name="benefits-of-mounting-on-a-pool"></a>Benefícios de montar em uma piscina

A montagem do sistema de ficheiros para o pool, em vez de permitir que as tarefas recuperem os seus próprios dados a partir de um grande conjunto de dados, torna mais fácil e eficiente para as tarefas aceder aos dados necessários.

Considere um cenário com múltiplas tarefas que requerem acesso a um conjunto comum de dados, como renderizar um filme. Cada tarefa torna um ou mais quadros de cada vez a partir dos ficheiros da cena. Ao montar uma unidade que contém os ficheiros da cena, é mais fácil para os nós de computação aceder em dados partilhados. Adicionalmente, o sistema de ficheiros subjacente pode ser escolhido e dimensionado independentemente com base no desempenho e escala (entrada e IOPS) exigidos pelo número de nós computacionais que acedem simultaneamente aos dados. Por exemplo, um [cache Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) distribuído na memória pode ser usado para suportar grandes renderizações à escala de imagem de movimento com milhares de nós renderizados simultâneos, acedendo a dados de origem que residem no local. Alternativamente, para dados que já residem no armazenamento blob baseado na nuvem, [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) pode ser usado para montar estes dados como um sistema de ficheiros local. A Blobfuse só está disponível nos nós linux, no entanto, o [Azure Files](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/) fornece um fluxo de trabalho semelhante e está disponível tanto no Windows como no Linux.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Monte um sistema de arquivo virtual em uma piscina  

A montagem de um sistema de ficheiros virtual numa piscina disponibiliza o sistema de ficheiros a todos os nódosos de computação na piscina. O sistema de ficheiros é configurado quando um nó computacional se junta a uma piscina, ou quando o nó é reiniciado ou reimagem.

Para montar um sistema de ficheiros numa piscina, crie um `MountConfiguration` objeto. Escolha o objeto que se adequa ao seu sistema de ficheiros virtual: `AzureBlobFileSystemConfiguration` `AzureFileShareConfiguration` , ou `NfsMountConfiguration` `CifsMountConfiguration` .

Todos os objetos de configuração do suporte precisam dos seguintes parâmetros de base. Algumas configurações de montagem têm parâmetros específicos para o sistema de ficheiros que estão a ser utilizados, que são discutidos com mais detalhes nos exemplos de código.

- **Nome da conta ou fonte**: Para montar uma parte de ficheiro virtual, precisa do nome da conta de armazenamento ou da sua fonte.
- **Caminho de montagem relativo ou Fonte**: A localização do sistema de ficheiros montado no nó da computação, em relação ao diretório padrão `fsmounts` acessível no nó via `AZ_BATCH_NODE_MOUNTS_DIR` . A localização exata varia consoante o sistema operativo utilizado no nó. Por exemplo, a localização física num nó Ubuntu é mapeada para `mnt\batch\tasks\fsmounts` , e num nó CentOS é mapeada para `mnt\resources\batch\tasks\fsmounts` .
- **Opções de montagem ou blobfuse**: Estas opções descrevem parâmetros específicos para a montagem de um sistema de ficheiros.

Assim que o `MountConfiguration` objeto for criado, atribua o objeto à `MountConfigurationList` propriedade quando criar a piscina. O sistema de ficheiros é montado quando um nó se junta a uma piscina ou quando o nó é reiniciado ou reimagem.

Quando o sistema de ficheiros é montado, é criada uma variável ambiental `AZ_BATCH_NODE_MOUNTS_DIR` que aponta para a localização dos sistemas de ficheiros montados, bem como ficheiros de registo, que são úteis para resolução de problemas e depuração. Os ficheiros de registo são explicados mais detalhadamente na secção de erros de [montagem diagnosticar.](#diagnose-mount-errors)  

> [!IMPORTANT]
> O número máximo de sistemas de ficheiros montados numa piscina é de 10. Consulte [quotas e limites](batch-quota-limit.md#other-limits) de serviço do Lote para detalhes e outros limites.

## <a name="examples"></a>Exemplos

Os seguintes exemplos de código demonstram a montagem de uma variedade de partilhas de ficheiros num conjunto de nódosos computacionais.

### <a name="azure-files-share"></a>Partilha de Ficheiros Azure

O Azure Files é a oferta padrão do sistema de ficheiros em nuvem Azure. Para saber mais sobre como obter qualquer um dos parâmetros na amostra do código de configuração do suporte, consulte Use uma partilha de [Ficheiros Azure](../storage/files/storage-how-to-use-files-windows.md).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "{storage-account-name}",
                AzureFileUrl = "https://{storage-account-name}.file.core.windows.net/{file-share-name}",
                AccountKey = "{storage-account-key}",
                RelativeMountPath = "S",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>Sistema de ficheiros Azure Blob

Outra opção é utilizar o armazenamento Azure Blob via [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md). A montagem de um sistema de ficheiros blob requer uma ou para a sua conta de `AccountKey` `SasKey` armazenamento. Para obter estas chaves, consulte Gerir as chaves de acesso à conta de [armazenamento,](../storage/common/storage-account-keys-manage.md)ou utilizar assinaturas de [acesso partilhado (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md). Para obter mais informações sobre o uso de blobfuse, consulte o blobfuse [Troubleshoot FAQ](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ). Para obter acesso padrão ao diretório montado blobfuse, execute a tarefa como **Administrador**. Blobfuse monta o diretório no espaço do utilizador, e na criação da piscina é montado como raiz. Em Linux todas as tarefas **do Administrador** são fundamentais. Todas as opções para o módulo FUSE são descritas na [página de referência FUSE](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html).

Além do guia de resolução de problemas, as questões do GitHub no repositório blobfuse são uma forma útil de verificar as questões e resoluções atuais da blobfuse. Para mais informações, consulte [os problemas de blobfuse](https://github.com/Azure/azure-storage-fuse/issues).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "",
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

### <a name="network-file-system"></a>Sistema de Ficheiros de Rede

Os Sistemas de Ficheiros de Rede (NFS) também podem ser montados para piscina sinuosos permitindo que os sistemas de ficheiros tradicionais sejam facilmente acedidos por nós do Lote Azure. Este pode ser um único servidor NFS implantado na nuvem, ou um servidor NFS no local acedido sobre uma rede virtual. Alternativamente, aproveite a solução de cache [avere vFXT](../avere-vfxt/avere-vfxt-overview.md) distribuída na memória, que proporciona uma conectividade perfeita ao armazenamento no local, lendo dados a pedido na sua cache, e fornece alto desempenho e escala para nós de computação baseados em nuvem.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=1.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>Sistema comum de ficheiros de Internet

Os sistemas comuns de ficheiros de Internet (CIFS) também podem ser montados para piscinas de nós permitindo que os sistemas de ficheiros tradicionais sejam facilmente acedidos por nós do Lote Azure. O CIFS é um protocolo de partilha de ficheiros que fornece um mecanismo aberto e transversal para solicitar ficheiros e serviços de servidores de rede. O CIFS baseia-se na versão melhorada do protocolo do Bloco de Mensagens de Servidor (SMB) da Microsoft para a partilha de ficheiros de internet e intranet e é utilizado para montar sistemas de ficheiros externos em nós do Windows. Para saber mais sobre sMB, consulte [File Server e SMB](https://docs.microsoft.com/windows-server/storage/file-server/file-server-smb-overview).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>Diagnosticar erros de montagem

Se uma configuração de montagem falhar, o nó de computação na piscina falhará e o estado do nó torna-se inutilizável. Para diagnosticar uma falha de configuração do suporte, inspecione a [`ComputeNodeError`](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) propriedade para obter detalhes sobre o erro.

Para obter os ficheiros de registo para depuração, utilize o [OutputFiles](batch-task-output-files.md) para fazer o upload dos `*.log` ficheiros. Os `*.log` ficheiros contêm informações sobre o suporte do sistema de ficheiros no `AZ_BATCH_NODE_MOUNTS_DIR` local. Os ficheiros de registo de montagem têm o formato: `<type>-<mountDirOrDrive>.log` para cada montagem. Por exemplo, um `cifs` suporte num diretório de montagem nomeado terá um ficheiro de registo de montagem `test` chamado: `cifs-test.log` .

## <a name="supported-skus"></a>SKUs suportados

| Publisher | Oferta | SKU | Partilha de ficheiros Azure | Blobfuse | Montagem nFS | Montagem CIFS |
|---|---|---|---|---|---|---|
| lote | renderização-centos73 | renderização | :heavy_check_mark: <br>Nota: Compatível com CentOS 7.7</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canónico | UbuntuServer | 16.04-LTS, 18.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8| :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-anúncios | linux-data-science-vm | linuxdsvm | :heavy_check_mark: <br>Nota: Compatível com CentOS 7.4. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-lote | centos-contentor | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-lote | centos-contentor-rdma | 7.4 | :heavy_check_mark: <br>Nota: Suporta A_8 ou 9 armazenamento</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-lote | ubuntu-servidor-contentor | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-science-vm-ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7.4, 7.3, 7.1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oráculo-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais detalhes sobre a montagem de uma partilha de Ficheiros Azure com [windows](../storage/files/storage-how-to-use-files-windows.md) ou [Linux](../storage/files/storage-how-to-use-files-linux.md).
- Aprenda a utilizar e a montar sistemas de ficheiros virtuais [blobfuse.](https://github.com/Azure/azure-storage-fuse)
- Consulte a visão geral do Sistema de [Ficheiros](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview) de Rede para saber mais sobre o NFS e as suas aplicações.
- Consulte o protocolo Microsoft SMB e a visão geral do [protocolo CIFS](https://docs.microsoft.com/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) para saber mais sobre o CIFS.
