---
title: Troubleshoot Linux VM nome de nome muda em Azure Microsoft Docs
description: Explica porque é que os nomes dos dispositivos VM linux mudam e como resolver o problema.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "71058210"
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Alterações no nome do dispositivo Linux VM

Este artigo explica porque é que os nomes do dispositivo mudam depois de reiniciar um VM Linux ou de voltar a ligar os discos de dados. O artigo também fornece soluções para este problema.

## <a name="symptoms"></a>Sintomas
Pode experimentar os seguintes problemas ao executar VMs Linux no Microsoft Azure:

- O VM não arranca depois de um recomeço.
- Quando os discos de dados são separados e religados, os nomes do dispositivo do disco são alterados.
- Uma aplicação ou script que referencia um disco utilizando o nome do dispositivo falha porque o nome do dispositivo mudou.

## <a name="cause"></a>Causa

Os caminhos dos dispositivos em Linux não são garantidos para serem consistentes em reinícios. Os nomes dos dispositivos consistem em números importantes (letras) e números menores. Quando o controlador do dispositivo de armazenamento Linux detetar um novo dispositivo, o condutor atribui números importantes e menores da gama disponível para o dispositivo. Quando um dispositivo é removido, os números do dispositivo são libertados para reutilização.

O problema ocorre porque a digitalização do dispositivo em Linux está programada pelo subsistema SCSI para acontecer assíncronamente. Como resultado, um nome de caminho do dispositivo pode variar entre reinícios.

## <a name="solution"></a>Solução

Para resolver este problema, use o nome persistente. Existem quatro formas de usar o nome persistente: por etiqueta de sistema de ficheiros, por UUID, por ID ou por caminho. Recomendamos a utilização da etiqueta do sistema de ficheiros ou UUID para VMs Azure Linux.

A maioria das `fstab` distribuições fornecem os parâmetros **nofail** ou **nobootwait.** Estes parâmetros permitem que um sistema arranque quando o disco não se monta no arranque. Consulte a sua documentação de distribuição para obter mais informações sobre estes parâmetros. Para obter informações sobre como configurar um VM Linux para utilizar um UUID quando adicionar um disco de dados, consulte [Connect to the Linux VM para montar o novo disco](../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk).

Quando o agente Azure Linux é instalado num VM, o agente utiliza regras udev para construir um conjunto de ligações simbólicas sob o caminho /dev/disco/azure. Aplicações e scripts usam regras udev para identificar discos que estão ligados ao VM, juntamente com o tipo de disco e lNs do disco.

Se já editou a sua fstab de tal forma que o seu VM não está a arrancar e não consegue sSH no seu VM, pode utilizar a [Consola Série VM](./serial-console-linux.md) para entrar no [modo de utilizador único](./serial-console-grub-single-user-mode.md) e modificar a sua fstab.

### <a name="identify-disk-luns"></a>Identificar o disco LUNs

As aplicações utilizam LUNs para encontrar todos os discos anexados e para construir ligações simbólicas. O agente Azure Linux inclui regras udev que estabelecem ligações simbólicas de um LUN aos dispositivos:

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

As informações lun da conta de `lsscsi` hóspedes Linux são recuperadas usando ou uma ferramenta semelhante:

      $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

A informação lun convidada é usada com metadados de subscrição Azure para localizar o VHD no Armazenamento Azure que contém os dados da partilha. Por exemplo, pode `az` utilizar o CLI:

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

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Descubra uUIDs do sistema de ficheiros usando blkid

Aplicações e scripts lêem a saída de `blkid`, ou fontes de informação semelhantes, para construir ligações simbólicas no caminho /dev. A saída mostra os UUIDs de todos os discos que estão ligados ao VM e ao ficheiro do dispositivo associado:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

As regras udev do agente Azure Linux constroem um conjunto de ligações simbólicas sob o caminho /dev/disco/azure:

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

As aplicações utilizam as ligações para identificar o dispositivo do disco de arranque e o disco de recursos (efémeros). Em Azure, as aplicações devem olhar para os caminhos /dev/disco/azure/root-part1 ou /dev/disk/azure-resource-part1 caminhos para descobrir estas divisórias.

Quaisquer divisórias adicionais da `blkid` lista residem num disco de dados. As aplicações mantêm o UUID para estas divisórias e utilizam um caminho para descobrir o nome do dispositivo em tempo de execução:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1


### <a name="get-the-latest-azure-storage-rules"></a>Obtenha as mais recentes regras de armazenamento azure

Para obter as mais recentes regras de Armazenamento Azure, executar os seguintes comandos:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>Consulte também

Para obter mais informações, veja os artigos seguintes:

- [Ubuntu: Utilização do UUID](https://help.ubuntu.com/community/UsingUUID)
- [Chapéu Vermelho: Nomeação persistente](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux: O que os UUIDs podem fazer por si](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev: Introdução à gestão de dispositivos num sistema moderno de Linux](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)

