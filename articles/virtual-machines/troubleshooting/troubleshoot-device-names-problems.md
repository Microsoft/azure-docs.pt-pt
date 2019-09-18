---
title: Solucionar problemas de alterações de nome do dispositivo VM do Linux no Azure | Microsoft Docs
description: Explica por que os nomes de dispositivo VM do Linux são alterados e como resolver o problema.
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 7d8a7e7e88837214042fb8f1c109c0b93bfe771b
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058210"
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Solucionar problemas de alterações de nome do dispositivo VM Linux

Este artigo explica por que os nomes de dispositivo são alterados depois que você reinicia uma VM do Linux ou anexa novamente os discos de dados. O artigo também fornece soluções para esse problema.

## <a name="symptoms"></a>Sintomas
Você pode enfrentar os seguintes problemas ao executar VMs do Linux no Microsoft Azure:

- A VM não é inicializada após uma reinicialização.
- Quando os discos de dados são desanexados e reanexados, os nomes de dispositivo de disco são alterados.
- Um aplicativo ou script que faz referência a um disco usando o nome do dispositivo falha porque o nome do dispositivo foi alterado.

## <a name="cause"></a>Causa

Os caminhos de dispositivo no Linux não têm garantia de serem consistentes entre as reinicializações. Os nomes de dispositivo consistem em números principais (letras) e números secundários. Quando o driver de dispositivo de armazenamento do Linux detecta um novo dispositivo, o driver atribui números principais e secundários do intervalo disponível para o dispositivo. Quando um dispositivo é removido, os números de dispositivo são liberados para reutilização.

O problema ocorre porque a verificação de dispositivo no Linux está agendada pelo subsistema SCSI para ocorrer de forma assíncrona. Como resultado, um nome de caminho de dispositivo pode variar entre as reinicializações.

## <a name="solution"></a>Solução

Para resolver esse problema, use a nomenclatura persistente. Há quatro maneiras de usar nomes persistentes: por rótulo FileSystem, por UUID, por ID ou por caminho. É recomendável usar o rótulo do sistema de arquivos ou UUID para VMs Linux do Azure.

A maioria das distribuições `fstab` fornece os parâmetros **nofail** ou **nobootwait** . Esses parâmetros permitem que um sistema seja inicializado quando o disco não é montado na inicialização. Verifique a documentação de distribuição para obter mais informações sobre esses parâmetros. Para obter informações sobre como configurar uma VM do Linux para usar um UUID ao adicionar um disco de dados, consulte [conectar-se à VM do Linux para montar o novo disco](../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk).

Quando o agente Linux do Azure é instalado em uma VM, o agente usa regras udev para construir um conjunto de links simbólicos no caminho/dev/disk/Azure. Aplicativos e scripts usam regras udev para identificar os discos anexados à VM, juntamente com o tipo de disco e LUNs de disco.

Se você já tiver editado o fstab de forma que sua VM não esteja inicializando e não seja possível usar o SSH para a VM, use o [console serial da VM](./serial-console-linux.md) para entrar no [modo de usuário único](./serial-console-grub-single-user-mode.md) e modificar seu fstab.

### <a name="identify-disk-luns"></a>Identificar LUNs de disco

Os aplicativos usam LUNs para localizar todos os discos anexados e para construir links simbólicos. O agente Linux do Azure inclui regras udev que configuram links simbólicos de um LUN para os dispositivos:

    $ tree /dev/disk/azure

    /dev/disk/azure
    ├── resource -> ../../sdb
    ├── resource-part1 -> ../../sdb1
    ├── root -> ../../sda
    ├── root-part1 -> ../../sda1
    └── scsi1
        ├── lun0 -> ../../../sdc
        ├── lun0-part1 -> ../../../sdc1
        ├── lun1 -> ../../../sdd
        ├── lun1-part1 -> ../../../sdd1
        ├── lun1-part2 -> ../../../sdd2
        └── lun1-part3 -> ../../../sdd3

As informações de LUN da conta de convidado do Linux são `lsscsi` recuperadas usando o ou uma ferramenta semelhante:

      $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

As informações de LUN convidado são usadas com metadados de assinatura do Azure para localizar o VHD no armazenamento do Azure que contém os dados da partição. Por exemplo, você pode usar a `az` CLI:

    $ az vm show --resource-group testVM --name testVM | jq -r .storageProfile.dataDisks
    [
    {
    "caching": "None",
      "createOption": "empty",
    "diskSizeGb": 1023,
      "image": null,
    "lun": 0,
    "managedDisk": null,
    "name": "testVM-20170619-114353",
    "vhd": {
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-114353.vhd"
    }
    },
    {
    "caching": "None",
    "createOption": "empty",
    "diskSizeGb": 512,
    "image": null,
    "lun": 1,
    "managedDisk": null,
    "name": "testVM-20170619-121516",
    "vhd": {
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-121516.vhd"
      }
      }
    ]

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Descobrir UUIDs do sistema de arquivos usando blkid

Aplicativos e scripts lêem a saída de `blkid`, ou fontes semelhantes de informações, para construir links simbólicos no caminho/dev. A saída mostra os UUIDs de todos os discos que estão anexados à VM e seu arquivo de dispositivo associado:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

As regras udev do agente Linux do Azure constroem um conjunto de links simbólicos no caminho/dev/disk/Azure:

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

Os aplicativos usam os links para identificar o dispositivo de disco de inicialização e o disco de recurso (efêmero). No Azure, os aplicativos devem examinar os caminhos/dev/disk/Azure/root-part1 ou/dev/disk/Azure-Resource-part1 para descobrir essas partições.

Todas as partições adicionais da `blkid` lista residem em um disco de dados. Os aplicativos mantêm o UUID para essas partições e usam um caminho para descobrir o nome do dispositivo em tempo de execução:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1


### <a name="get-the-latest-azure-storage-rules"></a>Obter as regras mais recentes do armazenamento do Azure

Para obter as regras de armazenamento do Azure mais recentes, execute os seguintes comandos:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>Consulte também

Para obter mais informações, veja os artigos seguintes:

- [Ubuntu Usando UUID](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat: Nomenclatura persistente](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux O que os UUIDs podem fazer por você](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev Introdução ao gerenciamento de dispositivos em um sistema Linux moderno](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)

