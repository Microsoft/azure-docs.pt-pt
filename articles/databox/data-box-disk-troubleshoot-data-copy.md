---
title: Problemas de cópia de dados do Disco de Dados da Azure Data Disk Microsoft Docs
description: Descreve como resolver problemas vistos durante a cópia de dados no Disco de Caixas de Dados do Azure utilizando registos.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/13/2019
ms.author: alkohli
ms.openlocfilehash: 760f5c6c929aa082993683d7a466a71c6484289a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67148351"
---
# <a name="troubleshoot-data-copy-issues-in-azure-data-box-disk"></a>Problemas de cópia de dados em Disco de Caixa de Dados Azure

Este artigo aplica-se ao Microsoft Azure Data Box Disk e descreve como resolver problemas que vê ao copiar os dados para discos. O artigo também aborda questões ao utilizar a ferramenta de cópia dividida.


## <a name="data-copy-issues-when-using-a-linux-system"></a>Problemas de cópia de dados ao usar um sistema Linux

Esta secção detalha alguns dos principais problemas enfrentados ao usar um cliente Linux para copiar dados em discos.

### <a name="issue-drive-getting-mounted-as-read-only"></a>Edição: Unidade montada como apenas leitura
 
**Motivo** 

Isto pode ser devido a um sistema de ficheiros impuros.

Remontar uma unidade como leitura-escrita não funciona com discos de caixa de dados. Este cenário não é suportado com unidades desencriptadas por desarmário. Pode ter remontado com sucesso o dispositivo utilizando o seguinte comando:

    `# mount -o remount, rw /mnt/DataBoxDisk/mountVol1`

Embora o remontagem tenha sido bem sucedido, os dados não persistirão.

**Resolução**

Dê os seguintes passos no seu sistema Linux:

1. Instale `ntfsprogs` a embalagem para o utilitário ntfsfix.
2. Desmonte os pontos de montagem fornecidos para a unidade pela ferramenta de desbloqueio. O número de pontos de montagem variará para as unidades.

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. Corra `ntfsfix` no caminho correspondente. O número realçado deve ser o mesmo que o Passo 2.

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. Executar o seguinte comando para remover os metadados de hibernação que podem causar o problema do suporte.

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. Faça uma limpeza desmontada.

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. Faça um desbloqueio e montagem limpos.
7. Teste o ponto de montagem escrevendo um ficheiro.
8. Desmonte e remonte para validar a persistência do ficheiro.
9. Continue com a cópia de dados.
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>Problema: Erro com dados que não persistem após cópia
 
**Motivo** 

Se vir que a sua unidade não tem dados depois de ter sido desmontada (embora os dados tenham sido copiados), então é possível que tenha montado uma unidade como leitura-escrita depois de a unidade ter sido montada apenas como leitura.

**Resolução**
 
Se for esse o caso, consulte a resolução para [as unidades montadas como apenas leitura](#issue-drive-getting-mounted-as-read-only).

Se não for esse o caso, copie os registos da pasta que tem a ferramenta Data Box Disk Unlock e [contacte](data-box-disk-contact-microsoft-support.md)o Microsoft Support .


## <a name="data-box-disk-split-copy-tool-errors"></a>Erros da ferramenta de Cópia Dividida do Data Box Disk

Os problemas observados ao utilizar uma ferramenta Split Copy para dividir os dados em vários discos são resumidos na tabela seguinte.

|Mensagem de erro/avisos |Recomendações |
|---------|---------|
|[Informação] Recuperação da senha bitLocker para volume: m <br>[Erro] Exceção apanhada ao recuperar a chave BitLocker para o volume m:<br> A sequência não contém elementos.|Este erro é apresentado se o Data Box Disk de destino estiver offline. <br> Utilize a ferramenta `diskmgmt.msc` para os discos online.|
|[Erro] Exceção emitida: falha na operação de WMI:<br> Method=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message = o formato da palavra-passe de recuperação fornecida é inválido. <br>As palavras-passe de recuperação do BitLocker têm 48 dígitos. <br>Certifique-se de que a palavra-passe de recuperação está no formato correto e, em seguida, tente novamente.|Utilize a ferramenta de Desbloqueio do Data Box Disk para desbloquear primeiro os discos e repita o comando. Para obter mais informações, aceda a <li> [Desbloquear o Data Box Disk para clientes Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client). </li><li> [Desbloquear o Data Box Disk para clientes Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client). </li>|
|[Erro] Exceção emitida: existe um ficheiro DriveManifest.xml na unidade alvo. <br> Isto indica que a unidade alvo pode ter sido preparada com um ficheiro de diário diferente. <br>Para adicionar mais dados à mesma unidade, utilize o ficheiro de diário anterior. Para eliminar os dados existentes e reutilizar a unidade-alvo para um novo trabalho de importação, elimine o *DriveManifest.xml* na unidade. Execute novamente este comando com um novo ficheiro de diário.| Este erro é apresentado quando tenta utilizar o mesmo conjunto de unidades para várias sessões de importação. <br> Utilize um conjunto de unidades para uma só sessão de divisão e cópia.|
|[Erro] Exceção emitida: CopySessionId importdata-sept-test-1 refere-se a uma sessão de cópia anterior e não pode ser reutilizado para uma nova sessão de cópia.|Este erro é reportado quando tenta utilizar o mesmo nome de tarefa para uma nova tarefa como sendo uma tarefa anterior concluída com êxito.<br> Atribua um nome exclusivo à nova tarefa.|
|[Informações] O nome do ficheiro ou diretório de destino excede o limite de comprimento NTFS. |Esta mensagem é reportada quando o ficheiro de destino tiver sido renomeado devido ao caminho de ficheiro longo.<br> Modifique a opção de disposição no ficheiro `config.json` para controlar este comportamento.|
|[Erro] Exceção emitida: sequência de escape JSON inválida. |Esta mensagem é reportada quando o config.json está num formato inválido. <br> Valide o `config.json` com [JSONlint](https://jsonlint.com/) antes de guardar o ficheiro.|


## <a name="next-steps"></a>Passos seguintes

- Aprenda a [resolver problemas com problemas](data-box-disk-troubleshoot.md)com a ferramenta de validação.
