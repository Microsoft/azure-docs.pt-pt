---
title: Solucionar problemas na sua Azure Data Box, Azure Data Box Heavy | Microsoft Docs
description: Descreve como solucionar problemas vistos em Azure Data Box e Azure Data Box Heavy ao copiar dados para esses dispositivos.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 83f6f7c7f8cd5155669f12fd6e426f86ef1c7baa
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848500"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Solucionar problemas relacionados a Azure Data Box e Azure Data Box Heavy

Este artigo fornece informações detalhadas sobre como solucionar problemas que você pode ver ao usar o Azure Data Box ou Azure Data Box Heavy. O artigo inclui a lista de possíveis erros vistos quando os dados são copiados para o Data Box ou quando os dados são carregados de Data Box.

## <a name="error-classes"></a>Classes de erro

Os erros em Data Box e Data Box Heavy são resumidos da seguinte maneira:

| Categoria do erro *        | Descrição        | Ação recomendada    |
|----------------------------------------------|---------|--------------------------------------|
| Nomes de contêiner ou compartilhamento | Os nomes de contêiner ou compartilhamento não seguem as regras de nomenclatura do Azure.  |Baixe as listas de erros. <br> Renomeie os contêineres ou compartilhamentos. [Saiba mais](#container-or-share-name-errors).  |
| Limite de tamanho de contêiner ou de compartilhamento | O total de dados em contêineres ou compartilhamentos excede o limite do Azure.   |Baixe as listas de erros. <br> Reduza os dados gerais no contêiner ou compartilhamento. [Saiba mais](#container-or-share-size-limit-errors).|
| Limite de tamanho de objeto ou arquivo | O objeto ou os arquivos em contêineres ou compartilhamentos excedem o limite do Azure.|Baixe as listas de erros. <br> Reduza o tamanho do arquivo no contêiner ou compartilhamento. [Saiba mais](#object-or-file-size-limit-errors). |    
| Tipo de dados ou arquivo | Não há suporte para o formato de dados ou o tipo de arquivo. |Baixe as listas de erros. <br> Para BLOBs de página ou Managed disks, verifique se os dados são 512-bytes alinhados e copiados para as pastas criadas previamente. [Saiba mais](#data-or-file-type-errors). |
| Erros não críticos de BLOB ou arquivo  | Os nomes de BLOB ou arquivo não seguem as regras de nomenclatura do Azure ou o tipo de arquivo não tem suporte. | Esses arquivos ou BLOB podem não ser copiados ou os nomes podem ser alterados. [Saiba como corrigir esses erros](#non-critical-blob-or-file-errors). |

\*As quatro primeiras categorias de erro são erros críticos e devem ser corrigidas para que você possa continuar a preparação para o envio.


## <a name="container-or-share-name-errors"></a>Erros de nome de contêiner ou compartilhamento

Esses são erros relacionados a nomes de contêiner e compartilhamento.

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Descrição do erro:** O nome do contentor ou da partilha tem de ter entre 3 e 63 carateres. 

**Resolução sugerida:** A pasta no Data Box ou no compartilhamento de Data Box Heavy (SMB/NFS) para o qual você copiou dados se torna um contêiner do Azure em sua conta de armazenamento. 

- Na página **conectar e copiar** da interface do usuário da Web local do dispositivo, baixe e examine os arquivos de erro para identificar os nomes das pastas com problemas.
- Altere o nome da pasta no Data Box ou Data Box Heavy compartilhamento para garantir que:

    - O nome tem entre 3 e 63 caracteres.
    - Os nomes só podem ter letras, números e hifens.
    - Os nomes não podem começar nem terminar com hifens.
    - Os nomes não podem ter hifens consecutivos.
    - Exemplos de nomes válidos: `my-folder-1`,`my-really-extra-long-folder-111`
    - Exemplos de nomes que não são válidos `my-folder_1`: `my` `--myfolder`, `myfolder--`,,,`myfolder!`

    Para obter mais informações, consulte Convenções de nomenclatura do Azure para nomes de [contêiner](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e [nomes de compartilhamento](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Descrição do erro:** O nome do contentor ou da partilha só pode ter letras, números ou hífenes.

**Resolução sugerida:** A pasta no Data Box ou no compartilhamento de Data Box Heavy (SMB/NFS) para o qual você copiou dados se torna um contêiner do Azure em sua conta de armazenamento. 

- Na página **conectar e copiar** da interface do usuário da Web local do dispositivo, baixe e examine os arquivos de erro para identificar os nomes das pastas com problemas.
- Altere o nome da pasta no Data Box ou Data Box Heavy compartilhamento para garantir que:

    - O nome tem entre 3 e 63 caracteres.
    - Os nomes só podem ter letras, números e hifens.
    - Os nomes não podem começar nem terminar com hifens.
    - Os nomes não podem ter hifens consecutivos.
    - Exemplos de nomes válidos: `my-folder-1`,`my-really-extra-long-folder-111`
    - Exemplos de nomes que não são válidos `my-folder_1`: `my` `--myfolder`, `myfolder--`,,,`myfolder!`

    Para obter mais informações, consulte Convenções de nomenclatura do Azure para nomes de [contêiner](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e [nomes de compartilhamento](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Descrição do erro:** Os nomes de contêiner e os nomes de compartilhamento não podem começar nem terminar com hifens e não podem ter hifens consecutivos.

**Resolução sugerida:** A pasta no Data Box ou no compartilhamento de Data Box Heavy (SMB/NFS) para o qual você copiou dados se torna um contêiner do Azure em sua conta de armazenamento. 

- Na página **conectar e copiar** da interface do usuário da Web local do dispositivo, baixe e examine os arquivos de erro para identificar os nomes das pastas com problemas.
- Altere o nome da pasta no Data Box ou Data Box Heavy compartilhamento para garantir que:

    - O nome tem entre 3 e 63 caracteres.
    - Os nomes só podem ter letras, números e hifens.
    - Os nomes não podem começar nem terminar com hifens.
    - Os nomes não podem ter hifens consecutivos.
    - Exemplos de nomes válidos: `my-folder-1`,`my-really-extra-long-folder-111`
    - Exemplos de nomes que não são válidos `my-folder_1`: `my` `--myfolder`, `myfolder--`,,,`myfolder!`

    Para obter mais informações, consulte Convenções de nomenclatura do Azure para nomes de [contêiner](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e [nomes de compartilhamento](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

## <a name="container-or-share-size-limit-errors"></a>Erros de limite de tamanho de contêiner ou de compartilhamento

Esses são erros relacionados a dados que excedem o tamanho dos dados permitidos em um contêiner ou compartilhamento.

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Descrição do erro:** O compartilhamento de arquivos do Azure limita um compartilhamento a 5 TB de dados. Esse limite foi excedido para alguns compartilhamentos.

**Resolução sugerida:** Na página **conectar e copiar** da interface do usuário da Web local, baixe e examine os arquivos de erro.

Identifique as pastas que têm esse problema nos logs de erros e certifique-se de que os arquivos nessa pasta estejam abaixo de 5 TB.


## <a name="object-or-file-size-limit-errors"></a>Erros de limite de tamanho de objeto ou arquivo

Estes são erros relacionados a dados que excedem o tamanho máximo do objeto ou do arquivo permitido no Azure. 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Descrição do erro:** O tamanho do ficheiro excede o tamanho máximo de ficheiro para carregamento.

**Resolução sugerida:** O BLOB ou os tamanhos de arquivo excedem o limite máximo permitido para upload.

- Na página **conectar e copiar** da interface do usuário da Web local, baixe e examine os arquivos de erro.
- Certifique-se de que os tamanhos de BLOB e arquivo não excedam os limites de tamanho de objeto do Azure.

## <a name="data-or-file-type-errors"></a>Erros de tipo de dados ou de arquivo

Estes são erros relacionados ao tipo de arquivo ou tipo de dados sem suporte encontrado no contêiner ou compartilhamento. 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Descrição do erro:** O blob ou ficheiro está alinhado incorretamente.

**Resolução sugerida:** O compartilhamento de blob de página no Data Box ou Data Box Heavy dá suporte apenas a arquivos que são 512 bytes alinhados (por exemplo, VHD/VHDX). Todos os dados copiados para o compartilhamento de blobs de páginas são carregados no Azure como BLOBs de páginas.

Remova os dados não VHD/VHDX do compartilhamento de blobs de páginas. Você pode usar compartilhamentos para blob de blocos ou arquivos do Azure para dados genéricos.

Para obter mais informações, consulte [visão geral dos BLOBs de página](../storage/blobs/storage-blob-pageblob-overview.md).

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Descrição do erro:** Um tipo de arquivo sem suporte está presente em um compartilhamento de disco gerenciado. Somente VHDs fixos são permitidos.

**Resolução sugerida:**

- Certifique-se de carregar somente os VHDs fixos para criar discos gerenciados.
- Não há suporte para arquivos VHDX ou VHDs dinâmicos e **diferenciais** .

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Descrição do erro:** Um diretório não é permitido em nenhuma das pastas preexistentes para os discos gerenciados. Somente VHDs fixos são permitidos nessas pastas.

**Resolução sugerida:** Para discos gerenciados, em cada compartilhamento, as três pastas a seguir são criadas, que correspondem aos contêineres em sua conta de armazenamento: SSD Premium, HDD Standard e SSD Standard. Essas pastas correspondem ao nível de desempenho do disco gerenciado.

- Certifique-se de copiar os dados de blob de página (VHDs) em uma dessas pastas existentes.
- Uma pasta ou diretório não é permitido nessas pastas existentes. Remova todas as pastas que você criou nas pastas preexistentes.

Para obter mais informações, consulte [copiar para discos gerenciados](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**Descrição do erro:** Links simbólicos não são permitidos no Linux. 

**Resolução sugerida:** Os links simbólicos geralmente são links, Pipes e outros arquivos. Remova os links ou resolva os links e copie os dados.


## <a name="non-critical-blob-or-file-errors"></a>Erros não críticos de BLOB ou arquivo

Todos os erros não críticos relacionados aos nomes de BLOBs, arquivos ou contêineres que são vistos durante a cópia de dados são resumidos na seção a seguir. Se esses erros estiverem presentes, os nomes serão modificados para estar em conformidade com as convenções de nomenclatura do Azure. O status do pedido correspondente para o carregamento de dados será **concluído com avisos**.  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Descrição do erro:** Os nomes de BLOB ou arquivo contêm caracteres de controle sem suporte.

**Resolução sugerida:** Os BLOBs ou os arquivos que você copiou contêm nomes com caracteres sem suporte.

Na página **conectar e copiar** da interface do usuário da Web local, baixe e examine os arquivos de erro.
Remova ou renomeie os arquivos para remover os caracteres sem suporte.

Para obter mais informações, consulte Convenções de nomenclatura do Azure para nomes de [blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomes de arquivo](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Descrição do erro:** Os nomes de BLOB ou arquivo contêm caracteres ilegais.

**Resolução sugerida:** Os BLOBs ou os arquivos que você copiou contêm nomes com caracteres sem suporte.

Na página **conectar e copiar** da interface do usuário da Web local, baixe e examine os arquivos de erro.
Remova ou renomeie os arquivos para remover os caracteres sem suporte.

Para obter mais informações, consulte Convenções de nomenclatura do Azure para nomes de [blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomes de arquivo](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Descrição do erro:** Os nomes de BLOB ou arquivo estão terminando com caracteres inválidos.

**Resolução sugerida:** Os BLOBs ou os arquivos que você copiou contêm nomes com caracteres sem suporte.

Na página **conectar e copiar** da interface do usuário da Web local, baixe e examine os arquivos de erro.
Remova ou renomeie os arquivos para remover os caracteres sem suporte.

Para obter mais informações, consulte Convenções de nomenclatura do Azure para nomes de [blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomes de arquivo](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Descrição do erro:** O BLOB ou o nome do arquivo contém muitos segmentos de caminho.

**Resolução sugerida:** Os BLOBs ou os arquivos que você copiou excedem o número máximo de segmentos de caminho. Um segmento de caminho é a cadeia entre caracteres delimitadores consecutivos, por exemplo, a barra/.

- Na página **conectar e copiar** da interface do usuário da Web local, baixe e examine os arquivos de erro.
- Verifique se os [nomes de blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e os [nomes de arquivo](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) estão em conformidade com as convenções de nomenclatura do Azure.

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Descrição do erro:** O nome do blob ou ficheiro é demasiado longo.

**Resolução sugerida:** O BLOB ou os nomes de arquivo excedem o comprimento máximo.

- Na página **conectar e copiar** da interface do usuário da Web local, baixe e examine os arquivos de erro.
- O nome do BLOB não deve exceder 1.024 caracteres.
- Remova ou renomeie o BLOB ou os arquivos para que os nomes não excedam 1024 caracteres.

Para obter mais informações, consulte Convenções de nomenclatura do Azure para nomes de BLOB e nomes de arquivo.

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Descrição do erro:** Um dos segmentos no nome do blob ou ficheiro é demasiado longo.

**Resolução sugerida:** Um dos segmentos de caminho no BLOB ou nome de arquivo excede o número máximo de caracteres. Um segmento de caminho é a cadeia entre caracteres delimitadores consecutivos, por exemplo, a barra/.

- Na página **conectar e copiar** da interface do usuário da Web local, baixe e examine os arquivos de erro.
- Verifique se os [nomes de blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e os [nomes de arquivo](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) estão em conformidade com as convenções de nomenclatura do Azure.


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Descrição do erro:** Nomes de contêiner inadequados são especificados para compartilhamentos de disco gerenciado.

**Resolução sugerida:** Para discos gerenciados, em cada compartilhamento, as seguintes pastas são criadas, que correspondem aos contêineres em sua conta de armazenamento: SSD Premium, HDD Standard e SSD Standard. Essas pastas correspondem ao nível de desempenho do disco gerenciado.

- Certifique-se de copiar os dados de blob de página (VHDs) em uma dessas pastas existentes. Somente os dados desses contêineres existentes são carregados no Azure.
- Qualquer outra pasta criada no mesmo nível que SSD Premium, HDD Standard e SSD Standard não corresponde a um nível de desempenho válido e não pode ser usada.
- Remova arquivos ou pastas criados fora dos níveis de desempenho.

Para obter mais informações, consulte [copiar para discos gerenciados](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).


## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre os [requisitos do sistema de armazenamento de blobs data Box](data-box-system-requirements-rest.md).
