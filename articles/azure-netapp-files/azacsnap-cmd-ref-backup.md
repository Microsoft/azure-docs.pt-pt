---
title: Fazer o back up utilizando a ferramenta Azure Application Consistent Snapshot para ficheiros Azure NetApp | Microsoft Docs
description: Fornece um guia para executar o comando de backup da ferramenta Azure Application Consistent Snapshot que pode utilizar com ficheiros Azure NetApp.
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
ms.openlocfilehash: 17c29fdf88495f6ecc40963eda08858887173fd1
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730943"
---
# <a name="back-up-using-azure-application-consistent-snapshot-tool-preview"></a>Fazer o back up utilizando a ferramenta Azure Application Consistent Snapshot (pré-visualização)

Este artigo fornece um guia para executar o comando de backup da ferramenta Azure Application Consistent Snapshot que pode utilizar com ficheiros Azure NetApp.

## <a name="introduction"></a>Introdução

Uma cópia de segurança baseada em instantâneos de armazenamento é executada usando o `azacsnap -c backup` comando.  Este comando executa a orquestração de uma imagem de armazenamento consistente na base de dados nos volumes DATA, e um instantâneo de armazenamento (sem qualquer configuração de consistência da base de dados) nos volumes OUTROs.  

Para os volumes DATA `azacsnap` irá preparar a base de dados para uma imagem de armazenamento, em seguida, ele levará a imagem de armazenamento para todos os volumes configurados, finalmente irá aconselhar a base de dados que o instantâneo está completo.  Também gerirá quaisquer entradas de base de dados que gravem a atividade de backup snapshot (por exemplo, catálogo de backup SAP HANA).

## <a name="command-options"></a>Opções de comando

O `-c backup` comando toma os seguintes argumentos:

- `--volume=` tipo de volume para instantâneo, este parâmetro pode conter `data` ou `other`
  - `data` instantâneas os volumes dentro da estrofe de dadosVolume do ficheiro de configuração.
  - `other` instantâneas os volumes dentro da outra estrofe Volumosa do ficheiro de configuração.
  
  > [!NOTE]
  > Ao criar um ficheiro config separado com o volume de arranque como o outroVolume, é possível que `boot` os instantâneos sejam tomados num horário completamente diferente (por exemplo, diariamente).

- `--prefix=` o prefixo instantâneo do cliente para o nome do instantâneo. Este parâmetro tem dois propósitos. Em primeiro lugar, o objetivo é fornecer um nome único para o agrupamento de instantâneos. Em segundo lugar, determinar o `--retention` número de instantâneos de armazenamento que são conservados para o especificado `--prefix` .

    > [!IMPORTANT]
    > Apenas caracteres alfa numéricos ("A-Z,a-z,0-9"), sublinham ("_") e traços ("-"").

- `--retention` o número de instantâneos do definido `--prefix` a manter. Quaisquer instantâneos adicionais são removidos depois de um novo instantâneo ser tirado para isto `--prefix` .

- `--trim` disponível para SAP HANA v2 e mais tarde, esta opção mantém o catálogo de backup e no catálogo de discos e cópias de segurança. O número de entradas a manter no catálogo de backup é determinado pela `--retention` opção acima e elimina entradas mais antigas para o prefixo definido ( `--prefix` ) do catálogo de backup, e a cópia de segurança dos registos físicos relacionados. Também elimina quaisquer entradas de backup de registo que sejam mais antigas do que a entrada de backup não registo mais antiga. Estas operações ajudam a evitar que as cópias de segurança dos registos utilizem todo o espaço disponível em disco.

  > [!NOTE]
  > O seguinte exemplo de comando manterá 9 instantâneos de armazenamento e garantirá que o catálogo de cópias de segurança é continuamente aparado para corresponder às 9 imagens de armazenamento que estão a ser mantidas.

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
    ```

- `[--ssl=]` um parâmetro opcional que define o método de encriptação utilizado para comunicar com o SAP HANA, ou `openssl` `commoncrypto` . Se definido, então o `azacsnap -c backup` comando espera encontrar dois ficheiros no mesmo diretório, estes ficheiros devem ser nomeados após o SID correspondente. Consulte a [Utilização de SSL para comunicação com o SAP HANA](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana). O exemplo a seguir toma um `hana` tipo instantâneo com um prefixo de e `hana_TEST` `9` manterá-os a comunicar com o SAP HANA utilizando sSL ( `openssl` ).

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim --ssl=openssl
    ```

- `[--configfile <config filename>]` é um parâmetro opcional que permite nomes de ficheiros de configuração personalizados.

## <a name="snapshot-backups-are-fast"></a>As cópias de segurança instantâneas são rápidas

A duração de uma cópia de segurança instantânea é independente do tamanho do volume, com um volume de 10 TB a ser encaixado no mesmo tempo aproximado que um volume de 10 GB.  

Os principais fatores que afetam o tempo global de execução são o número de volumes a serem instantâneos e quaisquer alterações no `--retention` parâmetro (onde uma redução pode aumentar o tempo de execução à medida que os instantâneos em excesso são removidos).

Na configuração de exemplo acima (para **Azure Large Instance),** as imagens para os dois volumes demoraram menos de 5 segundos a ser completadas. Para **os ficheiros Azure NetApp,** as imagens dos dois volumes demorariam cerca de 60 segundos.

> [!NOTE]
> Se o `--retention` tempo anterior for significativamente reduzido `azacsnap` (por exemplo, `--retention 50` de `--retention 5` para), o tempo tomado aumentará conforme `azacsnap` necessário para remover as imagens extras.

## <a name="example-with-data-parameter"></a>Exemplo com `data` parâmetro

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
```

O comando não sai para a consola, mas escreve para um ficheiro de registo, um ficheiro de resultados, e `/var/log/messages` .

O *ficheiro de registo* é composto pelo nome de comando + a opção -c + o nome de ficheiro config. Por predefinição, um nome de ficheiro de registo para uma `-c backup` execução com um nome de ficheiro config padrão `azacsnap-backup-azacsnap.log` padrão .

O ficheiro *de resultados* tem o mesmo nome base que o ficheiro de registo, com `.result` o seu sufixo, por exemplo, `azacsnap-backup-azacsnap.result` que contém a seguinte saída:

```bash
cat logs/azacsnap-backup-azacsnap.result
```

```output
Database # 1 (H80) : completed ok
```

O `/var/log/messages` ficheiro contém a mesma saída que o `.result` ficheiro. Consulte o exemplo seguinte (corra como raiz):

```bash
grep "azacsnap.*Database" /var/log/messages | tail -n10
```

```output
Jul  2 05:22:07 server01 azacsnap[183868]: Database # 1 (H80) : completed ok
Jul  2 05:27:06 server01 azacsnap[4069]: Database # 1 (H80) : completed ok
Jul  2 05:32:07 server01 azacsnap[19769]: Database # 1 (H80) : completed ok
Jul  2 05:37:06 server01 azacsnap[35312]: Database # 1 (H80) : completed ok
Jul  2 05:42:06 server01 azacsnap[50877]: Database # 1 (H80) : completed ok
Jul  2 05:47:06 server01 azacsnap[66429]: Database # 1 (H80) : completed ok
Jul  2 05:52:06 server01 azacsnap[82964]: Database # 1 (H80) : completed ok
Jul  2 05:57:06 server01 azacsnap[98522]: Database # 1 (H80) : completed ok
Jul  2 05:59:13 server01 azacsnap[105519]: Database # 1 (H80) : completed ok
Jul  2 06:02:06 server01 azacsnap[114280]: Database # 1 (H80) : completed ok
```

## <a name="example-with-other-parameter"></a>Exemplo com `other` parâmetro

```bash
azacsnap -c backup --volume other --prefix logs_TEST --retention 9
```

O comando não é de saída para a consola, mas escreve apenas para um ficheiro de registo.  _Não_ escreve para um ficheiro de resultados ou `/var/log/messages` .

O *ficheiro de registo* é composto pelo nome de comando + a opção -c + o nome de ficheiro config. Por predefinição, um nome de ficheiro de registo para uma `-c backup` execução com um nome de ficheiro config padrão `azacsnap-backup-azacsnap.log` padrão .

## <a name="example-with-other-parameter-to-backup-host-os"></a>Exemplo com `other` parâmetro (para o hospedeiro de reserva OS)

> [!NOTE]
> A utilização de outro ficheiro de configuração ( `--configfile bootVol.json` ) que contém apenas os volumes de arranque.

```bash
azacsnap -c backup --volume other --prefix boot_TEST --retention 9 --configfile bootVol.json
```

O comando não é de saída para a consola, mas escreve apenas para um ficheiro de registo.  _Não_ escreve para um ficheiro de resultados ou `/var/log/messages` .

O nome *do ficheiro de registo* neste exemplo é `azacsnap-backup-bootVol.log` .

> [!NOTE]
> O nome do ficheiro de registo é constituído pelo "(nome de comando-(a `-c` opção)-(o nome de ficheiro config)".  Por exemplo, se utilizar a `-c backup` opção com o nome de ficheiro de registo de `h80.json` , então o ficheiro de registo será chamado `azacsnap-backup-h80.log` .  Ou se utilizar a `-c test` opção com o mesmo ficheiro de configuração, o ficheiro de registo será chamado `azacsnap-test-h80.log` .

- Tipo de grande instância HANA: Existem dois valores válidos com `TYPEI` ou `TYPEII` dependentes da Unidade de Grande Instância HANA.
- Consulte [SKUs disponíveis para HANA Grandes Instâncias](../virtual-machines/workloads/sap/hana-available-skus.md) para confirmar os SKUs disponíveis.

## <a name="next-steps"></a>Próximos passos

- [Obtenha detalhes instantâneos](azacsnap-cmd-ref-details.md)
- [Eliminar instantâneos](azacsnap-cmd-ref-delete.md)