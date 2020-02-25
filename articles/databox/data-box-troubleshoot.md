---
title: Problemas de resolução de problemas na sua Caixa de Dados Azure, Caixa de Dados Azure Heavy
description: Descreve como resolver problemas vistos na Caixa de Dados Azure e na Caixa de Dados Azure Heavy ao copiar dados para estes dispositivos.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 51935516e26f263e44a926bf9b7d7ec24a5eeb9e
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560070"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Problemas relacionados com a Caixa de Dados Azure e a Caixa de Dados Azure Heavy

Este artigo detalha informações sobre como resolver problemas que poderá ver ao utilizar a Caixa de Dados Azure ou a Caixa de Dados Azure Heavy. O artigo inclui a lista de possíveis erros verificados quando os dados são copiados para a Caixa de Dados ou quando os dados são enviados a partir da Data Box.

## <a name="error-classes"></a>Aulas de erro

Os erros na Caixa de Dados e caixa de dados Heavy são resumidos da seguinte forma:

| Categoria de erro*        | Descrição        | Ação recomendada    |
|----------------------------------------------|---------|--------------------------------------|
| Nomes de contentores ou partilhas | O recipiente ou nomes de partilha não seguem as regras de nomeação do Azure.  |Descarregue as listas de erros. <br> Mude o nome dos recipientes ou ações. [Saiba mais](#container-or-share-name-errors).  |
| Limite de tamanho de contentor ou partilha | Os dados totais em contentores ou ações excedem o limite do Azure.   |Descarregue as listas de erros. <br> Reduza os dados globais no recipiente ou partilhe. [Saiba mais](#container-or-share-size-limit-errors).|
| Limite de tamanho de objeto ou ficheiro | O objeto ou ficheiros em contentores ou ações excede o limite Azure.|Descarregue as listas de erros. <br> Reduza o tamanho do ficheiro no recipiente ou partilhe. [Saiba mais](#object-or-file-size-limit-errors). |    
| Tipo de dados ou ficheiros | O formato de dados ou o tipo de ficheiro não são suportados. |Descarregue as listas de erros. <br> Para bolhas de página ou discos geridos, certifique-se de que os dados estão alinhados e copiados para as pastas pré-criadas. [Saiba mais](#data-or-file-type-errors). |
| Erros de blob ou ficheiros não críticos  | Os nomes blob ou ficheiros não seguem as regras de nomeação do Azure ou o tipo de ficheiro não é suportado. | Estas bolhas ou ficheiros podem não ser copiados ou os nomes podem ser alterados. [Aprenda a corrigir estes erros.](#non-critical-blob-or-file-errors) |

\* As primeiras quatro categorias de erros são erros críticos e devem ser corrigidas antes de poder avançar para a preparação para o navio.


## <a name="container-or-share-name-errors"></a>Erros de nome de contentores ou partilhas

Estes são erros relacionados com o contentor e compartilham nomes.

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Descrição do erro:** O recipiente ou o nome da partilha devem estar entre 3 e 63 caracteres. 

**Resolução sugerida:** A pasta sob a Caixa de Dados ou Caixa de Dados Parte Parte Parte de parte pesada (SMB/NFS) à qual copiou dados torna-se um recipiente Azure na sua conta de armazenamento. 

- Na página **Connect e cópia** do dispositivo web UI, descarregue e reveja os ficheiros de erro para identificar os nomes das pastas com problemas.
- Altere o nome da pasta na caixa de dados ou na parte pesada da caixa de dados para se certificar de que:

    - O nome tem entre 3 e 63 caracteres.
    - Os nomes só podem ter letras, números e hífenes.
    - Os nomes não podem começar ou terminar com hífenes.
    - Os nomes não podem ter hífenes consecutivos.
    - Exemplos de nomes válidos: `my-folder-1`, `my-really-extra-long-folder-111`
    - Exemplos de nomes que não são válidos: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Para mais informações, consulte as convenções de nomedo Azure para [nomes](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) de contentores e [nomes de partilha](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Descrição do erro:** O recipiente ou o nome da partilha devem consistir apenas em letras, números ou hífenes.

**Resolução sugerida:** A pasta sob a Caixa de Dados ou Caixa de Dados Parte Parte Parte de parte pesada (SMB/NFS) à qual copiou dados torna-se um recipiente Azure na sua conta de armazenamento. 

- Na página **Connect e cópia** do dispositivo web UI, descarregue e reveja os ficheiros de erro para identificar os nomes das pastas com problemas.
- Altere o nome da pasta na caixa de dados ou na parte pesada da caixa de dados para se certificar de que:

    - O nome tem entre 3 e 63 caracteres.
    - Os nomes só podem ter letras, números e hífenes.
    - Os nomes não podem começar ou terminar com hífenes.
    - Os nomes não podem ter hífenes consecutivos.
    - Exemplos de nomes válidos: `my-folder-1`, `my-really-extra-long-folder-111`
    - Exemplos de nomes que não são válidos: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Para mais informações, consulte as convenções de nomedo Azure para [nomes](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) de contentores e [nomes de partilha](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Descrição do erro:** Os nomes dos contentores e os nomes de partilha não podem começar ou terminar com hífenes e não podem ter hífenes consecutivos.

**Resolução sugerida:** A pasta sob a Caixa de Dados ou Caixa de Dados Parte Parte Parte de parte pesada (SMB/NFS) à qual copiou dados torna-se um recipiente Azure na sua conta de armazenamento. 

- Na página **Connect e cópia** do dispositivo web UI, descarregue e reveja os ficheiros de erro para identificar os nomes das pastas com problemas.
- Altere o nome da pasta na caixa de dados ou na parte pesada da caixa de dados para se certificar de que:

    - O nome tem entre 3 e 63 caracteres.
    - Os nomes só podem ter letras, números e hífenes.
    - Os nomes não podem começar ou terminar com hífenes.
    - Os nomes não podem ter hífenes consecutivos.
    - Exemplos de nomes válidos: `my-folder-1`, `my-really-extra-long-folder-111`
    - Exemplos de nomes que não são válidos: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Para mais informações, consulte as convenções de nomedo Azure para [nomes](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) de contentores e [nomes de partilha](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

## <a name="container-or-share-size-limit-errors"></a>Erros de limite de tamanho de contentores ou partilhas

Trata-se de erros relacionados com dados que excedam o tamanho dos dados permitidos num contentor ou numa parte.

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Descrição do erro:** A partilha de ficheiros Azure limita uma parte a 5 TB de dados. Este limite ultrapassou algumas ações.

**Resolução sugerida:** Na página **Connect e cópia** da UI web local, descarregue e reveja os ficheiros de erro.

Identifique as pastas que têm este problema a partir dos registos de erro e certifique-se de que os ficheiros dessa pasta estão abaixo de 5 TB.


## <a name="object-or-file-size-limit-errors"></a>Erros de limite de tamanho de objeto ou ficheiro

Trata-se de erros relacionados com dados que excedam o tamanho máximo do objeto ou o ficheiro que é permitido no Azure. 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Descrição do erro:** O tamanho do ficheiro excede o tamanho máximo do ficheiro para o upload.

**Resolução sugerida:** A bolha ou os tamanhos dos ficheiros excedem o limite máximo permitido para o upload.

- Na página **Connect e cópia** da UI web local, descarregue e reveja os ficheiros de erro.
- Certifique-se de que os tamanhos de bolha e de ficheironão excedem os limites do tamanho do objeto Azure.

## <a name="data-or-file-type-errors"></a>Erros de tipo de dados ou ficheiros

Trata-se de erros relacionados com o tipo de ficheiro não suportado ou o tipo de dados encontrados no recipiente ou partilha. 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Descrição do erro:** A bolha ou ficheiro está incorretamente alinhada.

**Resolução sugerida:** A página blob partilha na Data Box ou Data Box Apenas suporta ficheiros alinhados 512 bytes (por exemplo, VHD/VHDX). Quaisquer dados copiados para a partilha de blob página são enviados para O Azure como bolhas de página.

Remova quaisquer dados não VHD/VHDX da partilha de blob página. Pode utilizar ações para ficheiros block blob ou Azure para dados genéricos.

Para mais informações, consulte a [visão geral das bolhas da página](../storage/blobs/storage-blob-pageblob-overview.md).

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Descrição do erro:** Um tipo de ficheiro não suportado está presente numa parte de disco gerida. Só são permitidos VHDs fixos.

**Resolução sugerida:**

- Certifique-se de que apenas faz o upload dos VHDs fixos para criar discos geridos.
- Os ficheiros VHDX ou VHDs **dinâmicos** e **disferencing** não são suportados.

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Descrição do erro:** Não é permitido um diretório em nenhuma das pastas pré-existentes para os discos geridos. Só são permitidos VHDs fixos nestas pastas.

**Resolução sugerida:** Para discos geridos, dentro de cada ação, são criadas as seguintes três pastas que correspondem a contentores na sua conta de armazenamento: Premium SSD, Standard HDD e Standard SSD. Estas pastas correspondem ao nível de desempenho do disco gerido.

- Certifique-se de que copia os dados blob da página (VHDs) numa destas pastas existentes.
- Não é permitida uma pasta ou diretório nestas pastas existentes. Remova quaisquer pastas que tenha criado dentro das pastas pré-existentes.

Para mais informações, consulte [Copy para obter discos geridos](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**Descrição do erro:** Ligações simbólicas não são permitidas em Linux. 

**Resolução sugerida:** As ligações simbólicas são geralmente links, tubos e outros ficheiros. Ou remove os links, ou resolve os links e copia os dados.


## <a name="non-critical-blob-or-file-errors"></a>Erros de blob ou ficheiros não críticos

Todos os erros não críticos relacionados com nomes de bolhas, ficheiros ou contentores que são vistos durante a cópia de dados são resumidos na secção seguinte. Se estes erros estiverem presentes, os nomes serão modificados de acordo com as convenções de nomeação do Azure. O estado de encomenda correspondente para o upload de dados será **preenchido com avisos**.  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Descrição do erro:** Os nomes de blob ou ficheiros contêm caracteres de controlo não suportados.

**Resolução sugerida:** As bolhas ou os ficheiros que copiou contêm nomes com caracteres não suportados.

Na página **Connect e cópia** da UI web local, descarregue e reveja os ficheiros de erro.
Remova ou mude o nome dos ficheiros para remover caracteres não suportados.

Para mais informações, consulte as convenções de nomedo Azure para [nomes blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e nomes de [ficheiros](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Descrição do erro:** A bolha ou os nomes dos ficheiros contêm caracteres ilegais.

**Resolução sugerida:** As bolhas ou os ficheiros que copiou contêm nomes com caracteres não suportados.

Na página **Connect e cópia** da UI web local, descarregue e reveja os ficheiros de erro.
Remova ou mude o nome dos ficheiros para remover caracteres não suportados.

Para mais informações, consulte as convenções de nomedo Azure para [nomes blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e nomes de [ficheiros](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Descrição do erro:** Os nomes de blob ou arquivo saem com caracteres maus.

**Resolução sugerida:** As bolhas ou os ficheiros que copiou contêm nomes com caracteres não suportados.

Na página **Connect e cópia** da UI web local, descarregue e reveja os ficheiros de erro.
Remova ou mude o nome dos ficheiros para remover caracteres não suportados.

Para mais informações, consulte as convenções de nomedo Azure para [nomes blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e nomes de [ficheiros](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Descrição do erro:** O nome blob ou arquivo contém muitos segmentos de caminho.

**Resolução sugerida:** As bolhas ou os ficheiros que copiou excedem o número máximo de segmentos de caminho. Um segmento de caminho é a cadeia entre caracteres delimitadores consecutivos, por exemplo, o corte dianteiro /.

- Na página **Connect e cópia** da UI web local, descarregue e reveja os ficheiros de erro.
- Certifique-se de que os [nomes blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [os nomes](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) dos ficheiros estão em conformidade com as convenções de nomeação do Azure.

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Descrição do erro:** A bolha ou o nome do ficheiro são muito longos.

**Resolução sugerida:** A bolha ou os nomes dos ficheiros excedem o comprimento máximo.

- Na página **Connect e cópia** da UI web local, descarregue e reveja os ficheiros de erro.
- O nome blob não deve exceder 1.024 caracteres.
- Remova ou mude o nome da bolha ou dos ficheiros para que os nomes não excedam 1024 caracteres.

Para mais informações, consulte as convenções de nomedo Azure para nomes blob e nomes de ficheiros.

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Descrição do erro:** Um dos segmentos do nome blob ou arquivo é muito longo.

**Resolução sugerida:** Um dos segmentos de caminho no nome de blob ou ficheiro excede o número máximo de caracteres. Um segmento de caminho é a cadeia entre caracteres delimitadores consecutivos, por exemplo, o corte dianteiro /.

- Na página **Connect e cópia** da UI web local, descarregue e reveja os ficheiros de erro.
- Certifique-se de que os [nomes blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [os nomes](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) dos ficheiros estão em conformidade com as convenções de nomeação do Azure.


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Descrição do erro:** São especificados nomes de contentores impróprios para ações de disco geridas.

**Resolução sugerida:** Para discos geridos, dentro de cada ação, são criadas as seguintes pastas que correspondem a contentores na sua conta de armazenamento: Premium SSD, Standard HDD e Standard SSD. Estas pastas correspondem ao nível de desempenho do disco gerido.

- Certifique-se de que copia os dados blob da página (VHDs) numa destas pastas existentes. Apenas os dados destes contentores existentes são enviados para o Azure.
- Qualquer outra pasta que seja criada ao mesmo nível do SSD Premium, Standard HDD e Standard SSD não corresponde a um nível de desempenho válido e não pode ser usada.
- Remova ficheiros ou pastas criados fora dos níveis de desempenho.

Para mais informações, consulte [Copy para obter discos geridos](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).


## <a name="next-steps"></a>Passos seguintes

- Conheça os requisitos do sistema de [armazenamento Data Box Blob](data-box-system-requirements-rest.md).
