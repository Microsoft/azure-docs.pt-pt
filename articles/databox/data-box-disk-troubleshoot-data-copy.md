---
title: Problemas de cópia de dados de resolução de problemas de disco de caixa de dados do Azure | Documentos da Microsoft
description: Descreve como resolver problemas detetados durante a cópia de dados no disco do Azure Data Box com os registos.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/13/2019
ms.author: alkohli
ms.openlocfilehash: 760f5c6c929aa082993683d7a466a71c6484289a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148351"
---
# <a name="troubleshoot-data-copy-issues-in-azure-data-box-disk"></a>Resolução de problemas de cópia de dados no disco do Azure Data Box

Este artigo aplica-se para o disco do Microsoft Azure Data Box e descreve como resolver quaisquer problemas que vir ao copiar os dados para discos. O artigo também aborda problemas ao utilizar a ferramenta de cópia de divisão.


## <a name="data-copy-issues-when-using-a-linux-system"></a>Problemas de cópia de dados ao utilizar um sistema Linux

Esta secção fornece detalhes sobre alguns dos principais problemas enfrentados ao utilizar um cliente de Linux para copiar dados para discos.

### <a name="issue-drive-getting-mounted-as-read-only"></a>Problema: Unidade de introdução montada como só de leitura
 
**Causa** 

Isto pode dever-se um sistema de ficheiros unclean.

Remontar uma unidade como leitura / escrita não funciona com discos de caixa de dados. Este cenário não é suportado com unidades desencriptadas por dislocker. Podem ter nomes com êxito o dispositivo com o seguinte comando:

    `# mount -o remount, rw /mnt/DataBoxDisk/mountVol1`

Embora o remontando foi concluída com êxito, os dados não serão mantidas.

**Resolução**

Siga os passos seguintes no seu sistema Linux:

1. Instalar o `ntfsprogs` pacote para o utilitário de ntfsfix.
2. Desmonte os pontos de montagem fornecidos para a unidade pela ferramenta de desbloqueio. O número de pontos de montagem irá variar para unidades.

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. Executar `ntfsfix` no caminho correspondente. O número de realçado deve ser o mesmo que o passo 2.

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. Execute o seguinte comando para remover os metadados de hibernação que podem fazer com que o problema de montagem.

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. Fazer uma unmount limpa.

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. Fazer um desbloqueio limpo e monte.
7. Teste o ponto de montagem com a criação de um ficheiro.
8. Desmontar e remontar para validar a persistência de ficheiros.
9. Continue com a cópia de dados.
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>Problema: Erro com os dados não são mantidas após a cópia
 
**Causa** 

Se vir que a unidade não tem dados depois de estar desmontados (embora os dados foram copiados para o mesmo), em seguida, é possível que remontadas uma unidade como leitura / escrita após a unidade foi montada como só de leitura.

**Resolução**
 
Se for esse o caso, consulte a resolução para [unidades de introdução montado como só de leitura](#issue-drive-getting-mounted-as-read-only).

Se não for o caso, copie os registos da pasta que tenha a ferramenta de desbloqueio de disco de caixa de dados e [contacte o Microsoft Support](data-box-disk-contact-microsoft-support.md).


## <a name="data-box-disk-split-copy-tool-errors"></a>Erros da ferramenta de Cópia Dividida do Data Box Disk

Os problemas observados ao usar uma ferramenta de cópia de divisão para dividir os dados ao longo de vários discos estão resumidos na tabela seguinte.

|Mensagem de erro/avisos |Recomendações |
|---------|---------|
|[Informações de] A obter a palavra-passe do BitLocker do volume: m <br>[Erro] Detetada exceção ao obter a chave do BitLocker para m do volume:<br> A sequência não contém elementos.|Este erro é apresentado se o Data Box Disk de destino estiver offline. <br> Utilize a ferramenta `diskmgmt.msc` para os discos online.|
|[Erro] Excepção emitida: Falha na operação de WMI:<br> Method=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message = o formato da palavra-passe de recuperação fornecida é inválido. <br>As palavras-passe de recuperação do BitLocker têm 48 dígitos. <br>Certifique-se de que a palavra-passe de recuperação está no formato correto e, em seguida, tente novamente.|Utilize a ferramenta de Desbloqueio do Data Box Disk para desbloquear primeiro os discos e repita o comando. Para obter mais informações, aceda a <li> [Desbloquear o Data Box Disk para clientes Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client). </li><li> [Desbloquear o Data Box Disk para clientes Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client). </li>|
|[Erro] Excepção emitida: Existe um ficheiro de DriveManifest.xml na unidade de destino. <br> Isto indica que a unidade alvo pode ter sido preparada com um ficheiro de diário diferente. <br>Para adicionar mais dados à mesma unidade, utilize o ficheiro de diário anterior. Para eliminar os dados existentes e reutilizar o disco de destino para uma nova tarefa de importação, elimine o *DriveManifest.xml* na unidade. Execute novamente este comando com um novo ficheiro de diário.| Este erro é apresentado quando tenta utilizar o mesmo conjunto de unidades para várias sessões de importação. <br> Utilize um conjunto de unidades para uma só sessão de divisão e cópia.|
|[Erro] Excepção emitida: CopySessionId importdata-Setembro-teste-1 se refere a uma sessão de cópia anterior e não pode ser reutilizado para uma nova sessão de cópia.|Este erro é reportado quando tenta utilizar o mesmo nome de tarefa para uma nova tarefa como sendo uma tarefa anterior concluída com êxito.<br> Atribua um nome exclusivo à nova tarefa.|
|[Informações] O nome do ficheiro ou diretório de destino excede o limite de comprimento NTFS. |Esta mensagem é reportada quando o ficheiro de destino tiver sido renomeado devido ao caminho de ficheiro longo.<br> Modifique a opção de disposição no ficheiro `config.json` para controlar este comportamento.|
|[Erro] Excepção emitida: Sequência de escape JSON inválida. |Esta mensagem é reportada quando o config.json está num formato inválido. <br> Valide o `config.json` com [JSONlint](https://jsonlint.com/) antes de guardar o ficheiro.|


## <a name="next-steps"></a>Passos Seguintes

- Saiba como [resolver problemas da ferramenta de validação](data-box-disk-troubleshoot.md).
