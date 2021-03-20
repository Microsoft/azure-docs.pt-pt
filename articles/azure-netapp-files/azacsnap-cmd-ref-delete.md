---
title: Elimine a ferramenta Azure Application Consistent Snapshot para ficheiros Azure NetApp | Microsoft Docs
description: Fornece um guia para executar o comando de eliminação da ferramenta Azure Application Consistent Snapshot que pode utilizar com ficheiros Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 0e2e4beebedb93524da43c5a3fad750b0295f5cd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97632911"
---
# <a name="delete-using-azure-application-consistent-snapshot-tool-preview"></a>Eliminar utilizando a ferramenta Azure Application Consistent Snapshot (pré-visualização)

Este artigo fornece um guia para executar o comando de eliminação da ferramenta Azure Application Consistent Snapshot que pode utilizar com ficheiros Azure NetApp.

## <a name="introduction"></a>Introdução

É possível eliminar instantâneos de volume e entradas de catálogo de bases de dados com o `azacsnap -c delete` comando.

> [!IMPORTANT]
> As imagens criadas menos de 10 minutos antes de executar este comando não devem ser eliminadas devido ao potencial de interferência com a replicação instantânea.

## <a name="command-options"></a>Opções de comando

O `-c delete` comando tem as seguintes opções:

- `--delete hana` quando utilizado com as opções `--hanasid <SID>` e `--hanabackupid <HANA backup id>` eliminará as entradas do catálogo de backup SAP HANA correspondentes aos critérios.

- `--delete storage` quando utilizado com a `--snapshot <snapshot name>` opção, apagará a imagem do sistema de armazenamento de back-end.

- `--delete sync` quando usado com opções `--hanasid <SID>` e `--hanabackupid <HANA backup id>` obtém o nome instantâneo de armazenamento do catálogo de cópias de segurança para o `<HANA backup id>` , e, em seguida, elimina a entrada no catálogo de cópias de segurança _e_ a instantâneo de qualquer um dos volumes que contém o instantâneo nomeado.

- `--delete sync`quando `--snapshot <snapshot name>` utilizado, verificará se há entradas no catálogo de backup para o `<snapshot name>` , obtém o ID de backup SAP HANA e elimina tanto a entrada no catálogo de cópias de segurança como a imagem de qualquer um dos volumes que contenham o instantâneo nomeado. 

- `[--force]` (opcional) *Utilize com cuidado*.  Esta operação forçará a eliminação sem pedir confirmação.

- `[--configfile <config filename>]` é um parâmetro opcional que permite nomes de ficheiros de configuração personalizados.

### <a name="delete-a-snapshot-using-sync-option"></a>Eliminar um instantâneo utilizando `sync` a opção'

```bash
azacsnap -c delete --delete sync --hanasid H80 --hanabackupid 157979797979
```

> [!NOTE]
> Verifique se há entradas no catálogo de backup para o ID de backup SAP HANA 157979797979797979, obtém o nome do instantâneo de armazenamento e elimina tanto a entrada no catálogo de backup como a imagem de todos os volumes que contêm o instantâneo nomeado.

```bash
azacsnap -c delete --delete sync --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> Verifica se há entradas no catálogo de backup para o instantâneo denominado hana_hourly.2020-01-22_2358, obtém o ID de backup SAP HANA e elimina tanto a entrada no catálogo de backup como a imagem de qualquer um dos volumes que contêm o instantâneo nomeado.

### <a name="delete-a-snapshot-using-hana-option"></a>Eliminar um instantâneo utilizando `hana` a opção'

```bash
azacsnap -c delete --delete hana --hanasid H80 --hanabackupid 157979797979
```

> [!NOTE]
> Elimina o ID de backup SAP HANA 157979797979797999 do catálogo de backup para SID H80.

### <a name="delete-a-snapshot-using-storage-option"></a>Eliminar um instantâneo utilizando `storage` a opção'

```bash
azacsnap -c delete --delete storage --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> Elimina a imagem de todos os volumes que contenham instantâneos denominados hana_hourly.2020-01-22_2358.

**Saída utilizando a `--delete storage` opção**

Pede-se ao utilizador que confirme a eliminação.

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T221702.2535255Z
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z'.
Are you sure you want to permanently delete the snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z' from all storage volumes.? (y/n) [n]: y
Snapshot deletion completed
```

É possível evitar a confirmação do utilizador, utilizando o parâmetro opcional `--force` da seguinte forma:

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T222201.4988618Z --force
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T222201.4988618Z'.
Snapshot deletion completed
```

## <a name="next-steps"></a>Passos seguintes

- [Obtenha detalhes instantâneos](azacsnap-cmd-ref-details.md)
- [Pegue uma cópia de segurança.](azacsnap-cmd-ref-backup.md)
