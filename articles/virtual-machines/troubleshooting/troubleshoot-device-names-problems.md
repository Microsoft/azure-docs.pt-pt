---
title: Resolver problemas relacionados com alterações de nome de dispositivo de VM do Linux no Azure | Documentos da Microsoft
description: Explica por que o dispositivo de VM do Linux nomes a alteração e como resolver o problema.
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: bb33427712533e669ecf41f48474c02313e2a411
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57568903"
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Resolver problemas relacionados com alterações de nome de dispositivo de VM do Linux

Este artigo explica por que os nomes de dispositivo alterar depois de reiniciar uma VM do Linux ou voltar a ligá os discos de dados. O artigo também fornece soluções para este problema.

## <a name="symptoms"></a>Sintomas
Poderá ter os seguintes problemas quando executar VMs do Linux no Microsoft Azure:

- A VM não consegue efetuar o arranque após um reinício.
- Quando os discos de dados são desligados e voltar a ligar, os nomes de dispositivo de disco são alterados.
- Uma aplicação ou script que faça referência a um disco ao utilizar o nome do dispositivo não consegue porque o nome do dispositivo foi alterada.

## <a name="cause"></a>Causa

Caminhos de dispositivo no Linux não são garantidos para ser consistente entre reinícios. Os nomes de dispositivo é composto por números importantes (letras) e números menores. Quando o controlador de dispositivo de armazenamento de Linux Deteta um novo dispositivo, o driver atribui números principal e secundário do intervalo disponível para o dispositivo. Quando um dispositivo é removido, os números de dispositivos são liberados para reutilização.

O problema ocorre porque o dispositivo de análise no Linux é agendado pelo subsistema de SCSI para ocorrer de modo assíncrono. Como resultado, um nome de caminho do dispositivo pode variar entre reinícios. 

## <a name="solution"></a>Solução

Para resolver este problema, utilize a nomenclatura persistente. Existem quatro formas de utilizar a nomenclatura persistente: por etiqueta de sistema de ficheiros, por UUID, por ID ou pelo caminho. Recomendamos que utilize a etiqueta de sistema de ficheiros ou UUID para VMs Linux do Azure. 

A maioria das distribuições de fornecer a `fstab` **nofail** ou **nobootwait** parâmetros. Estes parâmetros permitem um sistema efetuar o arranque quando o disco não consegue montar na inicialização. Consulte a documentação de distribuição para obter mais informações sobre estes parâmetros. Para obter informações sobre como configurar uma VM do Linux utilizar um UUID quando adiciona um disco de dados, consulte [ligar à VM do Linux para montar o disco novo](../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk). 

Quando o agente Linux do Azure está instalado numa VM, o agente utiliza regras de Udev para construir um conjunto de links simbólicos no caminho /dev/disk/azure. Aplicativos e scripts utilizam regras de Udev para identificar discos que estão ligados à VM, juntamente com o tipo de disco e disco LUNs.

### <a name="identify-disk-luns"></a>Identificar os LUNs de disco

Aplicativos usam LUNs para localizar todos os discos anexados e construir links simbólicos. O agente Linux do Azure inclui regras de Udev que configurar links simbólicos de um LUN para os dispositivos:

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

Informações de LUN da conta de convidado do Linux são recuperadas usando `lsscsi` ou uma ferramenta semelhante:

      $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

As informações de LUN de convidado são utilizadas com metadados de subscrição do Azure para localizar o VHD no armazenamento do Azure que contém os dados de partição. Por exemplo, pode usar o `az` CLI:

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

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Detetar os UUIDs não do sistema de ficheiros utilizando blkid

Aplicativos e scripts ler a saída de `blkid`, ou semelhante fontes de informação, construir links simbólicos no caminho /dev. O resultado mostra o UUIDs não de todos os discos que estão ligados à VM e seu arquivo de dispositivo associados:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

As regras de Udev do agente Linux do Azure construir um conjunto de links simbólicos no caminho /dev/disk/azure:

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

Aplicativos use os links para identificar o dispositivo de disco de arranque e o disco (efémeras) de recursos. No Azure, os aplicativos devem ser nos caminhos /dev/disk/azure/root-part1 ou /dev/disk/azure-resource-part1 para detetar estas partições.

Quaisquer partições adicionais do `blkid` lista residir num disco de dados. Aplicativos manter o UUID destas partições e utilizam um caminho para detetar o nome do dispositivo em tempo de execução:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1

    
### <a name="get-the-latest-azure-storage-rules"></a>Obter as regras de armazenamento do Azure mais recente

Para obter as regras de armazenamento do Azure mais recente, execute os seguintes comandos:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>Consulte também

Para obter mais informações, veja os artigos seguintes:

- [Ubuntu: Usando o UUID](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat: Nomenclatura persistente](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux: O que UUIDs não pode fazer por](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev: Introdução à gestão de dispositivos num sistema moderno do Linux](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)

