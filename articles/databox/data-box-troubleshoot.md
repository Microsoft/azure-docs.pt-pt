---
title: Resolver problemas sobre o Azure Data Box, pesadas de caixa de dados do Azure | Documentos da Microsoft
description: Descreve como resolver problemas detetados no Azure Data Box e pesadas de caixa de dados do Azure quando se copiam dados para estes dispositivos.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: 0c454c5f19ebefc7f91df62511448dbedb93dfc4
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257292"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Resolver problemas relacionados com o Azure Data Box e pesadas de caixa de dados do Azure

Este artigo fornece detalhes sobre as informações sobre como resolver problemas, pode ver quando utiliza o Boxn de dados do Azure ou pesadas de caixa de dados do Azure.

## <a name="errors-during-data-copy"></a>Erros durante a cópia de dados

Todos os erros que são vistos durante a cópia de dados estão resumidos nas seções a seguir.

### <a name="errorcontainerorsharenamelength"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Descrição do erro:** O nome do contentor ou da partilha tem de ter entre 3 e 63 carateres. 

**Resolução sugerida:** A pasta na share(SMB/NFS) Data Box ou pesadas de caixa de dados para o qual copiou dados torna-se um contentor do Azure na sua conta de armazenamento. 

- Sobre o **Connect e a cópia** página da web local do dispositivo da interface do Usuário, download e reveja os ficheiros de erro para identificar a pasta de nomes com problemas.
- Altere o nome da pasta compartilhamento da caixa de dados ou dados de caixa pesadas para se certificar de que:

    - O nome tem entre 3 e 63 carateres.
    - Os nomes só podem ter letras, números e hífenes.
    - Os nomes não podem começar nem terminar com hífenes.
    - Os nomes não podem conter hífenes consecutivos.
    - Exemplos de nomes válidos: `my-folder-1`, `my-really-extra-long-folder-111`
    - Exemplos de nomes que não são válidos: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Para obter mais informações, veja as convenções de nomenclatura do Azure [os nomes dos contentores](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e [partilhar nomes](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).


### <a name="errorcontainerorsharenamealphanumericdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Descrição do erro:** O nome do contentor ou da partilha só pode ter letras, números ou hífenes.

**Resolução sugerida:** A pasta na share(SMB/NFS) Data Box ou pesadas de caixa de dados para o qual copiou dados torna-se um contentor do Azure na sua conta de armazenamento. 

- Sobre o **Connect e a cópia** página da web local do dispositivo da interface do Usuário, download e reveja os ficheiros de erro para identificar a pasta de nomes com problemas.
- Altere o nome da pasta compartilhamento da caixa de dados ou dados de caixa pesadas para se certificar de que:

    - O nome tem entre 3 e 63 carateres.
    - Os nomes só podem ter letras, números e hífenes.
    - Os nomes não podem começar nem terminar com hífenes.
    - Os nomes não podem conter hífenes consecutivos.
    - Exemplos de nomes válidos: `my-folder-1`, `my-really-extra-long-folder-111`
    - Exemplos de nomes que não são válidos: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Para obter mais informações, veja as convenções de nomenclatura do Azure [os nomes dos contentores](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e [partilhar nomes](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="errorcontainerorsharenameimproperdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Descrição do erro:** Os nomes de contentor e nomes de partilha não é possível iniciar ou terminar com hífenes e não pode conter hífenes consecutivos.

**Resolução sugerida:** A pasta na share(SMB/NFS) Data Box ou pesadas de caixa de dados para o qual copiou dados torna-se um contentor do Azure na sua conta de armazenamento. 

- Sobre o **Connect e a cópia** página da web local do dispositivo da interface do Usuário, download e reveja os ficheiros de erro para identificar a pasta de nomes com problemas.
- Altere o nome da pasta compartilhamento da caixa de dados ou dados de caixa pesadas para se certificar de que:

    - O nome tem entre 3 e 63 carateres.
    - Os nomes só podem ter letras, números e hífenes.
    - Os nomes não podem começar nem terminar com hífenes.
    - Os nomes não podem conter hífenes consecutivos.
    - Exemplos de nomes válidos: `my-folder-1`, `my-really-extra-long-folder-111`
    - Exemplos de nomes que não são válidos: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Para obter mais informações, veja as convenções de nomenclatura do Azure [os nomes dos contentores](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e [partilhar nomes](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="errorcontainerorsharenamedisallowedfortype"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Descrição do erro:** Os nomes dos contentores impróprias são especificados para partilhas de disco gerido.

**Resolução sugerida:** Para discos geridos, dentro de cada partilha, as seguintes pastas são criadas que correspondem aos contentores na sua conta de armazenamento: Premium SSD, padrão HDD e Standard SSD. Essas pastas correspondem para o escalão de desempenho para o disco gerido.

- Certifique-se de que copia os dados de blob de página (VHDs) em uma dessas pastas existentes. Apenas os dados destes contentores existentes são carregados para o Azure.
- Qualquer outra pasta criada no mesmo nível como Premium SSD e Standard HDD e Standard SSD não corresponde a um escalão de desempenho válido e não pode ser utilizada.
- Remova ficheiros ou pastas criadas fora os escalões de desempenho.

Para obter mais informações, consulte [cópia para os managed disks](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).

### <a name="errorcontainerorsharecapacityexceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Descrição do erro:** Partilha de ficheiros do Azure limita uma partilha de 5 TB de dados. Este limite foi excedido para alguns partilhas.

**Resolução sugerida:** Sobre o **Connect e a cópia** página da web local da interface do Usuário, transfira e reveja os ficheiros de erro.

Identifique as pastas que têm este problema dos registos de erros e certifique-se de que os ficheiros nessa pasta são menos de 5 TB.

### <a name="errorbloborfilenamecharactercontrol"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Descrição do erro:** Os nomes de BLOBs ou ficheiros contenham carateres de controlo não suportado.

**Resolução sugerida:** Os blobs ou os ficheiros que copiou contenham nomes com carateres não suportados.

Sobre o **Connect e a cópia** página da web local da interface do Usuário, transfira e reveja os ficheiros de erro.
Remova ou mude o nome de ficheiros para remover os carateres não suportados.

Para obter mais informações, veja as convenções de nomenclatura do Azure [nomes de BLOBs](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomes de ficheiros](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).

### <a name="errorbloborfilenamecharacterillegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Descrição do erro:** Os nomes de BLOBs ou ficheiros contém carateres ilegais.

**Resolução sugerida:** Os blobs ou os ficheiros que copiou contenham nomes com carateres não suportados.

Sobre o **Connect e a cópia** página da web local da interface do Usuário, transfira e reveja os ficheiros de erro.
Remova ou mude o nome de ficheiros para remover os carateres não suportados.

Para obter mais informações, veja as convenções de nomenclatura do Azure [nomes de BLOBs](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomes de ficheiros](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="errorbloborfilenameending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Descrição do erro:** Os nomes de BLOBs ou ficheiros são terminando com carateres danificados.

**Resolução sugerida:** Os blobs ou os ficheiros que copiou contenham nomes com carateres não suportados.

Sobre o **Connect e a cópia** página da web local da interface do Usuário, transfira e reveja os ficheiros de erro.
Remova ou mude o nome de ficheiros para remover os carateres não suportados.

Para obter mais informações, veja as convenções de nomenclatura do Azure [nomes de BLOBs](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomes de ficheiros](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="errorbloborfilenamesegmentcount"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Descrição do erro:** O nome de ficheiro ou blob contém muitos segmentos de caminho.

**Resolução sugerida:** Os blobs ou os ficheiros que copiou excederem o número máximo de segmentos de caminho. Um segmento de caminho é a cadeia de carateres de delimitador consecutivas, por exemplo, a barra /.

- Sobre o **Connect e a cópia** página da web local da interface do Usuário, transfira e reveja os ficheiros de erro.
- Certifique-se de que o [nomes de BLOBs](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomes de ficheiros](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) está em conformidade com as convenções de nomenclatura do Azure.

### <a name="errorbloborfilenameaggregatelength"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Descrição do erro:** O nome do blob ou ficheiro é demasiado longo.

**Resolução sugerida:** O blob ou os nomes dos ficheiros excederem o comprimento máximo.

- Sobre o **Connect e a cópia** página da web local da interface do Usuário, transfira e reveja os ficheiros de erro.
- O nome do blob não pode exceder os 1024 carateres.
- Remova ou mude o nome o blob ou ficheiros para que os nomes não excederem os 1024 carateres.

Para obter mais informações, veja as convenções de nomenclatura do Azure para nomes de ficheiros e nomes de Blobs.

### <a name="errorbloborfilenamecomponentlength"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Descrição do erro:** Um dos segmentos no nome do blob ou ficheiro é demasiado longo.

**Resolução sugerida:** Um dos segmentos de caminho no nome do ficheiro ou blob excede o número máximo de carateres. Um segmento de caminho é a cadeia de carateres de delimitador consecutivas, por exemplo, a barra /.

- Sobre o **Connect e a cópia** página da web local da interface do Usuário, transfira e reveja os ficheiros de erro.
- Certifique-se de que o [nomes de BLOBs](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomes de ficheiros](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) está em conformidade com as convenções de nomenclatura do Azure.

### <a name="errorbloborfilesizelimit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Descrição do erro:** O tamanho do ficheiro excede o tamanho máximo de ficheiro para carregamento.

**Resolução sugerida:** O blob ou os tamanhos de ficheiro excederem o limite máximo permitido para carregamento.

- Sobre o **Connect e a cópia** página da web local da interface do Usuário, transfira e reveja os ficheiros de erro.
- Certifique-se de que os tamanhos de ficheiro e blob excede os limites de tamanho do objeto do Azure.

### <a name="errorbloborfilesizealignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Descrição do erro:** O blob ou ficheiro está alinhado incorretamente.

**Resolução sugerida:** A partilha de blob de página em caixa de dados ou dados de caixa pesada só suporta ficheiros que são de 512 bytes alinhado (por exemplo, VHD/VHDX). Todos os dados copiados para a partilha de blob de página são carregados para o Azure, como blobs de páginas.

Remova todos os dados não-VHD/VHDX da partilha de blob de página. Pode utilizar partilhas de blob de blocos ou ficheiros do Azure para dados genéricos.

Para obter mais informações, consulte [blobs de página de descrição geral](../storage/blobs/storage-blob-pageblob-overview.md).

### <a name="errorbloborfiletypeunsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Descrição do erro:** Um tipo de ficheiro não suportado está presente numa partilha de disco gerido. Os VHDs fixos apenas são permitidos.

**Resolução sugerida:**

- Certifique-se de que carrega apenas os VHDs fixos para criar discos geridos.
- Ficheiros VHDX ou **dinâmica** e **diferenciação** VHDs não são suportados.

### <a name="errordirectorydisallowedfortype"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Descrição do erro:** Um diretório não é permitido em qualquer uma das pastas já existentes para os discos geridos. Os VHDs fixos só são permitidos nessas pastas.

**Resolução sugerida:** Para discos geridos, dentro de cada partilha, as seguintes três pastas são criadas que correspondem aos contentores na sua conta de armazenamento: Premium SSD, padrão HDD e Standard SSD. Essas pastas correspondem para o escalão de desempenho para o disco gerido.

- Certifique-se de que copia os dados de blob de página (VHDs) em uma dessas pastas existentes.
- Uma pasta ou diretório não é permitido nessas pastas existentes. Remova quaisquer pastas que criar dentro das pastas já existentes.

Para obter mais informações, consulte [cópia para os managed disks](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).


## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [requisitos de sistema de armazenamento de BLOBs de caixa de dados](data-box-system-requirements-rest.md).
