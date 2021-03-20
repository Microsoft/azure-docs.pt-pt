---
title: Teste ferramenta de snapshot consistente da aplicação Azure para ficheiros Azure NetApp | Microsoft Docs
description: Explica como executar o comando de teste da ferramenta Azure Application Consistent Snapshot que pode utilizar com ficheiros Azure NetApp.
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
ms.openlocfilehash: 03ffa93a71ded40e033f2068ea23fc6b994a5bbb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97632818"
---
# <a name="test-azure-application-consistent-snapshot-tool-preview"></a>Teste Azure Aplicação Consistente Ferramenta Snapshot (pré-visualização)

Este artigo explica como executar o comando de teste da ferramenta Azure Application Consistent Snapshot que pode utilizar com ficheiros Azure NetApp.

## <a name="introduction"></a>Introdução

Um teste da configuração é feito usando o `azacsnap -c test` comando.

## <a name="command-options"></a>Opções de comando

O `-c test` comando tem as seguintes opções:

- `--test hana`  testa a ligação à instância SAP HANA.

- `--test storage` testa a comunicação com a interface de armazenamento subjacente, criando um instantâneo de armazenamento temporário em todos os volumes configurados `data` e, em seguida, removendo-os. 

- `--test all` executará tanto os `hana` `storage` testes em sequência.

- `[--configfile <config filename>]` é um parâmetro opcional que permite nomes de ficheiros de configuração personalizados.

## <a name="check-connectivity-with-sap-hana-azacsnap--c-test---test-hana"></a>Verifique a conectividade com o SAP HANA `azacsnap -c test --test hana`

Este comando verifica a conectividade HANA para todas as instâncias HANA no ficheiro de configuração. Utiliza a hdBuserstore para ligar ao SYSTEMDB e obter as informações sid.

Para a SSL, este comando pode assumir o seguinte argumento opcional:

- `--ssl=` força uma ligação encriptada com a base de dados e define o método de encriptação utilizado para comunicar com o SAP HANA, ou `openssl` `commoncrypto` . Se definido, então este comando espera encontrar dois ficheiros no mesmo diretório, estes ficheiros devem ser nomeados após o SID correspondente. Consulte a [Utilização de SSL para comunicação com o SAP HANA](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana).

### <a name="output-of-the-azacsnap--c-test---test-hana-command"></a>Saída do `azacsnap -c test --test hana` comando

```bash
azacsnap -c test --test hana
```

```output
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
PASSED: Successful connectivity to HANA version 2.00.032.00.1533114046
END   : Test process complete for 'hana'
```

## <a name="check-connectivity-with-storage-azacsnap--c-test---test-storage"></a>Verifique a conectividade com o armazenamento `azacsnap -c test --test storage`

O `azacsnap` comando irá tirar uma imagem de todos os volumes de dados configurados para verificar se tem o acesso correto aos volumes de cada instância SAP HANA. É criado um instantâneo temporário e depois removido para cada volume de dados para verificar o acesso instantâneo a cada sistema de ficheiros.

### <a name="output-of-the-azacsnap--c-test---test-storage-command"></a>Saída do `azacsnap -c test --test storage` comando

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 2 task(s) to Test Snapshots for Storage Volume Type 'data'
PASSED: Task#2/2 Storage test successful for Volume
PASSED: Task#1/2 Storage test successful for Volume
END   : Storage tests complete
END   : Test process complete for 'storage'
```

> [!NOTE]
> Para Azure Large Instance, `azacsnap -c test --test storage` o comando extrapola a geração de armazenamento e hli SKU.  Com base nesta informação, fornece orientação sobre a configuração de instantâneos de 'boot' (ver a linha a começar pela `Action:` saída).

```output
SID1   : Generation 4
Storage: ams07-a700s-saphan-1-01v250-client25-nprod
HLI SKU: S96
Action : Configure the 'boot' snapshots on ALL the servers.
```

## <a name="next-steps"></a>Passos seguintes

- [Backup instantâneo com ferramenta Azure Application Consistent Snapshot](azacsnap-cmd-ref-backup.md)
