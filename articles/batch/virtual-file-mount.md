---
title: Monte um sistema de arquivo virtual em uma piscina
description: Saiba como montar um sistema de ficheiros virtuais numa piscina de Lote.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 03/26/2021
ms.openlocfilehash: dc5fbdf9ca0df8362a8999856c3f7163dd5e59b9
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626032"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Monte um sistema de ficheiros virtual numa piscina de Lote

O Azure Batch suporta a montagem de armazenamento em nuvem ou um sistema de ficheiros externos nos nós de computação Windows ou Linux nas piscinas do Batch. Quando um nó computacional se junta a uma piscina, o sistema de ficheiros virtuais é montado e tratado como uma unidade local nesse nó. Pode montar sistemas de ficheiros como ficheiros Azure, armazenamento Azure Blob, Sistema de Ficheiros de Rede (NFS), incluindo uma [cache Avere vFXT,](../avere-vfxt/avere-vfxt-overview.md)ou Sistema Comum de Ficheiros de Internet (CIFS).

Neste artigo, você aprenderá a montar um sistema de ficheiros virtuais em um conjunto de nós computacional usando a Biblioteca de Gestão de [Lotes para .NET](/dotnet/api/overview/azure/batch).

> [!NOTE]
> A montagem de um sistema de ficheiros virtuais só é suportada em piscinas de Batch criadas em ou depois de 8 de agosto de 2019. Os lotes criados antes dessa data não suportam esta funcionalidade.

## <a name="benefits-of-mounting-on-a-pool"></a>Benefícios da montagem em uma piscina

A montagem do sistema de ficheiros no pool, em vez de permitir que as tarefas recuperem os seus próprios dados a partir de um grande conjunto de dados, torna mais fácil e eficiente o acesso das tarefas aos dados necessários.

Considere um cenário com múltiplas tarefas que requerem acesso a um conjunto comum de dados, como a renderização de um filme. Cada tarefa apresenta um ou mais quadros de cada vez a partir dos ficheiros de cena. Ao montar uma unidade que contenha os ficheiros de cena, é mais fácil para os nós de computação acederem a dados partilhados.

Além disso, o sistema de ficheiros subjacente pode ser escolhido e dimensionado de forma independente com base no desempenho e escala (produção e IOPS) exigidos pelo número de nós computacional que acedem simultaneamente aos dados. Por exemplo, uma cache distribuída em memória [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) pode ser usada para suportar renderizações em grande escala de imagem com milhares de nós renderizados simultâneos, acedendo a dados de origem que residem no local. Em alternativa, para os dados que já residem no armazenamento blob baseado na nuvem, [o blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) pode ser usado para montar estes dados como um sistema de ficheiros local. O Blobfuse só está disponível nos nós Linux, embora [o Azure Files](../storage/files/storage-files-introduction.md) forneça um fluxo de trabalho semelhante e esteja disponível tanto no Windows como no Linux.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Monte um sistema de arquivo virtual em uma piscina  

A montagem de um sistema de ficheiros virtuais numa piscina torna o sistema de ficheiros disponível para todos os nós de computação da piscina. O sistema de ficheiros é configurado quer quando um nó de computação se junta a uma piscina, quer quando o nó é reiniciado ou reesmimageado.

Para montar um sistema de ficheiros numa piscina, crie um `MountConfiguration` objeto. Escolha o objeto que se adapta ao seu sistema de ficheiros virtuais: `AzureBlobFileSystemConfiguration` `AzureFileShareConfiguration` , , ou `NfsMountConfiguration` `CifsMountConfiguration` .

Todos os objetos de configuração de montagem precisam dos seguintes parâmetros de base. Algumas configurações de montagem têm parâmetros específicos do sistema de ficheiros que estão a ser utilizados, que são discutidos mais detalhadamente nos exemplos de código.

- **Nome da conta ou fonte**: Para montar uma partilha de ficheiros virtuais, precisa do nome da conta de armazenamento ou da sua fonte.
- **Percurso de montagem relativo ou fonte**: A localização do sistema de ficheiros montado no nó de computação, relativamente ao diretório padrão `fsmounts` acessível no nó através de `AZ_BATCH_NODE_MOUNTS_DIR` . A localização exata varia consoante o sistema operativo utilizado no nó. Por exemplo, a localização física de um nó Ubuntu está mapeada para `mnt\batch\tasks\fsmounts` , e num nó CentOS está mapeada para `mnt\resources\batch\tasks\fsmounts` .
- **Opções de montagem ou opções blobfuse**: Estas opções descrevem parâmetros específicos para a montagem de um sistema de ficheiros.

Uma vez criado o `MountConfiguration` objeto, atribua o objeto à `MountConfigurationList` propriedade quando criar a piscina. O sistema de ficheiros é montado quando um nó une uma piscina ou quando o nó é reiniciado ou remimagem.

Quando o sistema de ficheiros é montado, é criada uma variável ambiental `AZ_BATCH_NODE_MOUNTS_DIR` que aponta para a localização dos sistemas de ficheiros montados, bem como para ficheiros de registo, que são úteis para a resolução de problemas e depuração. Os ficheiros de registo são explicados mais detalhadamente na secção [de erros de montagem](#diagnose-mount-errors) do diagnóstico.  

> [!IMPORTANT]
> O número máximo de sistemas de ficheiros montados numa piscina é de 10. Consulte [as quotas de serviço do Lote e limites](batch-quota-limit.md#other-limits) para mais detalhes e outros limites.

## <a name="examples"></a>Exemplos

Os seguintes exemplos de código demonstram a montagem de uma variedade de partilhas de ficheiros num conjunto de nós computacional.

### <a name="azure-files-share"></a>Partilha de Ficheiros Azure

Azure Files é a oferta padrão do sistema de ficheiros em nuvem Azure. Para saber mais sobre como obter qualquer um dos parâmetros na amostra de código de configuração de montagem, consulte [use uma partilha de Ficheiros Azure](../storage/files/storage-how-to-use-files-windows.md).

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

Outra opção é utilizar o armazenamento Azure Blob via [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md). A montagem de um sistema de ficheiros blob requer uma `AccountKey` ou para a sua conta de `SasKey` armazenamento. Para obter informações sobre a obtenção destas chaves, consulte [as teclas de acesso](../storage/common/storage-account-keys-manage.md) à conta de armazenamento de armazenamento ou o Grant acesso limitado aos recursos de armazenamento [Azure utilizando assinaturas de acesso partilhado (SAS)](../storage/common/storage-sas-overview.md). Para obter mais informações e dicas sobre a utilização de blobfuse, consulte o blobfuse .

Para obter acesso predefinido ao diretório montado blobfuse, executar a tarefa como **Administrador**. Blobfuse monta o diretório no espaço do utilizador, e na criação da piscina é montado como raiz. No Linux todas as tarefas **do Administrador** são raiz. Todas as opções para o módulo FUSE são descritas na página de referência do [FUSE](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html).

Reveja as [FAQ de resolução de problemas](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ) para obter mais informações e dicas sobre a utilização de blobfuse. Também pode rever [as questões do GitHub no repositório blobfuse](https://github.com/Azure/azure-storage-fuse/issues) para verificar as questões e resoluções atuais do blobfuse.

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

Os Sistemas de Ficheiros de Rede (NFS) podem ser montados em nós de piscina, permitindo que os sistemas de ficheiros tradicionais sejam acedidos pelo Azure Batch. Este pode ser um único servidor NFS implantado na nuvem, ou um servidor NFS no local acedido por uma rede virtual. Em alternativa, pode utilizar a solução de cache distribuída na memória [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) para tarefas de computação de alto desempenho intensiva em dados (HPC)

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

### <a name="common-internet-file-system"></a>Sistema comum de ficheiros de internet

A montagem [de sistemas comuns de ficheiros de internet (CIFS)](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) para os nós de piscina é outra forma de fornecer acesso aos sistemas de ficheiros tradicionais. O CIFS é um protocolo de partilha de ficheiros que fornece um mecanismo aberto e transversal para solicitar ficheiros e serviços de servidores de rede. O CIFS baseia-se na versão melhorada do protocolo Bloco de Mensagens do [Servidor (SMB)](/windows-server/storage/file-server/file-server-smb-overview) para a partilha de ficheiros na Internet e intranet, e pode ser utilizado para montar sistemas de ficheiros externos nos nós windows.

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

Se uma configuração de montagem falhar, o nó de computação na piscina falhará e o estado do nó será definido para `unusable` . Para diagnosticar uma falha de configuração de montagem, inspecione a [`ComputeNodeError`](/rest/api/batchservice/computenode/get#computenodeerror) propriedade para obter detalhes sobre o erro.

Para obter os ficheiros de registo para depurar, utilize [o OutputFiles](batch-task-output-files.md) para fazer o upload dos `*.log` ficheiros. Os `*.log` ficheiros contêm informações sobre o suporte do sistema de ficheiros no `AZ_BATCH_NODE_MOUNTS_DIR` local. Os ficheiros de registo de montagem têm o formato: `<type>-<mountDirOrDrive>.log` para cada montagem. Por exemplo, um `cifs` suporte num diretório de montagem nomeado `test` terá um ficheiro de registo de montagem nomeado: `cifs-test.log` .

## <a name="supported-skus"></a>SKUs apoiados

| Publisher | Oferta | SKU | Partilha de ficheiros Azure | Rio Blobfuse | Montagem NFS | Montagem CIFS |
|---|---|---|---|---|---|---|
| lote | renderização-centos73 | renderização | :heavy_check_mark: <br>Nota: Compatível com CentOS 7.7</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canónico | UbuntuServer | 16.04-LTS, 18.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8| :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-anúncios | linux-data-science-vm | linuxdsvm | :heavy_check_mark: <br>Nota: Compatível com CentOS 7.4. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-lote | centos-contentor | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-lote | centos-contentor-rdma | 7.4 | :heavy_check_mark: <br>Nota: Suporta A_8 ou 9 armazenamento</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-lote | ubuntu-servidor-container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-science-vm-ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7.4, 7.3, 7.1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="networking-requirements"></a>Requisitos de rede

Ao utilizar suportes de ficheiros virtuais com [piscinas Azure Batch numa rede virtual,](batch-virtual-network.md)tenha em mente os seguintes requisitos e certifique-se de que não há tráfego necessário bloqueado.

- **Ficheiros Azure:**
  - Exige que a porta TCP 445 esteja aberta para o tráfego de/para a etiqueta de serviço "armazenamento". Para obter mais informações, consulte Utilize uma partilha de [ficheiros Azure com o Windows](../storage/files/storage-how-to-use-files-windows.md#prerequisites).
- **Blobfuse:**
  - Exige que a porta TCP 443 esteja aberta para o tráfego de/para a etiqueta de serviço "armazenamento".
  - Os VMs devem ter acesso https://packages.microsoft.com para descarregar os pacotes blobfuse e GPG. Dependendo da sua configuração, também poderá precisar de acesso a outros URLs para descarregar pacotes adicionais.
- **Sistema de ficheiros de rede (NFS):**
  - Requer acesso à porta 2049 (por defeito; a sua configuração pode ter outros requisitos).
  - Os VMs devem ter acesso ao gestor de pacotes apropriado para descarregar o pacote nfs-common (para Debian ou Ubuntu) ou nfs-utils (para CentOS). Este URL pode variar em função da sua versão SO. Dependendo da sua configuração, também poderá precisar de acesso a outros URLs para descarregar pacotes adicionais.
- **Sistema comum de ficheiros de internet (CIFS):**
  - Requer acesso à porta TCP 445.
  - Os VMs devem ter acesso ao ou gestor de pacotes apropriados para descarregar o pacote cifs-utils. Este URL pode variar em função da sua versão SO.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a montagem de uma partilha de Ficheiros Azure com [o Windows](../storage/files/storage-how-to-use-files-windows.md) ou [o Linux](../storage/files/storage-how-to-use-files-linux.md).
- Saiba como utilizar e montar sistemas de ficheiros virtuais [blobfuse.](https://github.com/Azure/azure-storage-fuse)
- Consulte [a visão geral do Sistema de Ficheiros de Rede](/windows-server/storage/nfs/nfs-overview) para saber mais sobre o NFS e as suas aplicações.
- Consulte [o protocolo microsoft SMB e a visão geral do protocolo CIFS](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) para saber mais sobre o CIFS.
