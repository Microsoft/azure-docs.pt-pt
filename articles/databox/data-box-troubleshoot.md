---
title: Problemas de resolução de problemas na sua Caixa de Dados Azure, Caixa de Dados Azure Pesada
description: Descreve como resolver problemas vistos na Azure Data Box e na Azure Data Box Heavy ao copiar dados para estes dispositivos.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 09/10/2020
ms.author: alkohli
ms.openlocfilehash: bb70946fda4fad7a42fd885a2515cb0d82698eca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92124683"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Problemas relacionados com Azure Data Box e Azure Data Box Heavy

Este artigo detalha informações sobre como resolver problemas que pode ver ao utilizar a Caixa de Dados Azure ou a Caixa de Dados Azure Pesada para encomendas de importação. O artigo inclui a lista de possíveis erros vistos quando os dados são copiados para a Caixa de Dados ou quando os dados são enviados da Caixa de Dados para uma ordem de importação.

A informação neste artigo não se aplica às encomendas de exportação criadas para a Caixa de Dados.

## <a name="error-classes"></a>Aulas de erro

Os erros na Caixa de Dados e na Caixa de Dados Pesadas são resumidos da seguinte forma:

| Categoria de erro*        | Description        | Ação recomendada    |
|----------------------------------------------|---------|--------------------------------------|
| Contentor ou nomes de partilha | O contentor ou os nomes de partilha não seguem as regras de nomeação Azure.  |Descarregue as listas de erros. <br> Mude o nome dos recipientes ou partilhas. [Saiba mais](#container-or-share-name-errors).  |
| Limite de tamanho de contentor ou de partilha | O total de dados em contentores ou ações excede o limite de Azure.   |Descarregue as listas de erros. <br> Reduza os dados globais no recipiente ou partilhe. [Saiba mais](#container-or-share-size-limit-errors).|
| Limite de tamanho de objeto ou arquivo | O objeto ou ficheiros em contentores ou ações excedem o limite de Azure.|Descarregue as listas de erros. <br> Reduza o tamanho do ficheiro no recipiente ou partilhe. [Saiba mais](#object-or-file-size-limit-errors). |    
| Dados ou tipo de ficheiro | O formato de dados ou o tipo de ficheiro não são suportados. |Descarregue as listas de erros. <br> Para bolhas de página ou discos geridos, certifique-se de que os dados são 512 bytes alinhados e copiados para as pastas pré-criadas. [Saiba mais](#data-or-file-type-errors). |
| Erros de bolha ou ficheiro não críticos  | Os nomes de bolhas ou ficheiros não seguem as regras de nomeação Azure ou o tipo de ficheiro não é suportado. | Estas bolhas ou ficheiros podem não ser copiados ou os nomes podem ser alterados. [Saiba como corrigir estes erros.](#non-critical-blob-or-file-errors) |

\* As quatro primeiras categorias de erros são erros críticos e devem ser corrigidas antes de poder proceder à preparação para o envio.


## <a name="container-or-share-name-errors"></a>Erros de nome de contentor ou de partilha

Estes são erros relacionados com o contentor e nomes de partilha.

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Descrição do erro:** O recipiente ou o nome da partilha devem ter entre 3 e 63 caracteres. 

**Resolução sugerida:** A pasta sob a Caixa de Dados ou Caixa de Dados A quota pesada (SMB/NFS) para a qual copiou dados torna-se um recipiente Azure na sua conta de armazenamento. 

- Na página **De Ligar e copiar** do dispositivo web UI local, descarregue e reveja os ficheiros de erro para identificar os nomes das pastas com problemas.
- Altere o nome da pasta na caixa de dados ou na caixa de dados apesar de ter a certeza de que:

    - O nome tem entre 3 e 63 caracteres.
    - Os nomes só podem ter letras, números e hífens.
    - Os nomes não podem começar ou acabar com hífens.
    - Os nomes não podem ter hífens consecutivos.
    - Exemplos de nomes válidos: `my-folder-1` , `my-really-extra-long-folder-111`
    - Exemplos de nomes que não são válidos: `my-folder_1` `my` . . `--myfolder` . `myfolder--``myfolder!`

    Para mais informações, consulte as convenções de nomeação do Azure para [nomes de contentores](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e [nomes de partilha.](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Descrição do erro:** O recipiente ou nome da partilha deve consistir apenas em letras, números ou hífenes.

**Resolução sugerida:** A pasta sob a Caixa de Dados ou Caixa de Dados A quota pesada (SMB/NFS) para a qual copiou dados torna-se um recipiente Azure na sua conta de armazenamento. 

- Na página **De Ligar e copiar** do dispositivo web UI local, descarregue e reveja os ficheiros de erro para identificar os nomes das pastas com problemas.
- Altere o nome da pasta na caixa de dados ou na caixa de dados apesar de ter a certeza de que:

    - O nome tem entre 3 e 63 caracteres.
    - Os nomes só podem ter letras, números e hífens.
    - Os nomes não podem começar ou acabar com hífens.
    - Os nomes não podem ter hífens consecutivos.
    - Exemplos de nomes válidos: `my-folder-1` , `my-really-extra-long-folder-111`
    - Exemplos de nomes que não são válidos: `my-folder_1` `my` . . `--myfolder` . `myfolder--``myfolder!`

    Para mais informações, consulte as convenções de nomeação do Azure para [nomes de contentores](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e [nomes de partilha.](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Descrição do erro:** Os nomes dos contentores e os nomes de partilha não podem começar ou acabar com hífens e não podem ter hífenes consecutivos.

**Resolução sugerida:** A pasta sob a Caixa de Dados ou Caixa de Dados A quota pesada (SMB/NFS) para a qual copiou dados torna-se um recipiente Azure na sua conta de armazenamento. 

- Na página **De Ligar e copiar** do dispositivo web UI local, descarregue e reveja os ficheiros de erro para identificar os nomes das pastas com problemas.
- Altere o nome da pasta na caixa de dados ou na caixa de dados apesar de ter a certeza de que:

    - O nome tem entre 3 e 63 caracteres.
    - Os nomes só podem ter letras, números e hífens.
    - Os nomes não podem começar ou acabar com hífens.
    - Os nomes não podem ter hífens consecutivos.
    - Exemplos de nomes válidos: `my-folder-1` , `my-really-extra-long-folder-111`
    - Exemplos de nomes que não são válidos: `my-folder_1` `my` . . `--myfolder` . `myfolder--``myfolder!`

    Para mais informações, consulte as convenções de nomeação do Azure para [nomes de contentores](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e [nomes de partilha.](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)
    
### <a name="error_file_or_directory_name_illegal"></a>ERROR_FILE_OR_DIRECTORY_NAME_ILLEGAL

**Descrição do erro**: O diretório ou os nomes dos contentores contêm caracteres ilegais.

**Resolução sugerida**: O diretório ou os nomes dos contentores que copiou contêm caracteres não suportados.

- Na página De Ligar e copiar da UI web local, faça o download e reveja os ficheiros de erro para identificar os nomes das pastas com problemas. 
- Mude o nome do diretório ou dos contentores para garantir que estão em conformidade com as convenções de nomeação Azure.

Para mais informações, consulte as convenções de nomeação Azure para [diretórios](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) e [contentores.](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)

## <a name="container-or-share-size-limit-errors"></a>Erros de limite de tamanho de contentor ou de partilha

Trata-se de erros relacionados com dados que excedem o tamanho dos dados permitidos num contentor ou numa parte.

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Descrição do erro:** A ação de ficheiro azure limita uma ação a 5 TiB de dados, e grandes ações de ficheiros não estão ativadas na conta de armazenamento. Este limite foi ultrapassado para algumas ações.

**Resolução sugerida:** Na página **De Ligar e copiar** da UI web local, faça o download e reveja os ficheiros de erro.

- Identifique as pastas que têm este problema a partir dos registos de erro e certifique-se de que os ficheiros dessa pasta estão abaixo de 5 TiB.
- O limite de 5 TiB não se aplica a uma conta de armazenamento que permita grandes ações de ficheiros. No entanto, deve ter grandes ações de ficheiros configuradas quando efente o seu pedido. 
  - Contacte [o Microsoft Support](data-box-disk-contact-microsoft-support.md) e solicite uma nova etiqueta de envio.
  - [Ativar grandes ações de ficheiros na conta de armazenamento.](../storage/files/storage-files-how-to-create-large-file-share.md#enable-large-files-shares-on-an-existing-account)
  - [Expandir as ações de ficheiros na conta de armazenamento](../storage/files/storage-files-how-to-create-large-file-share.md#expand-existing-file-shares) e definir a quota para 100 TiB.
  
  
## <a name="object-or-file-size-limit-errors"></a>Erros de limite de objeto ou tamanho de ficheiro

Estes são erros relacionados com dados que excedem o tamanho máximo do objeto ou o ficheiro que é permitido no Azure. 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Descrição do erro:** O tamanho do ficheiro excede o tamanho máximo do ficheiro para o upload.

**Resolução sugerida:** O blob ou os tamanhos do ficheiro excedem o limite máximo permitido para o upload.

- Na página **De Ligar e copiar** da UI web local, faça o download e reveja os ficheiros de erro.
- Certifique-se de que os tamanhos da bolha e do ficheiro não excedem os limites do tamanho do objeto Azure.

## <a name="data-or-file-type-errors"></a>Erros de tipo de dados ou de ficheiros

Trata-se de erros relacionados com o tipo de ficheiro ou tipo de dados não suportados encontrados no recipiente ou partilha. 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Descrição do erro:** A bolha ou o ficheiro estão incorretamente alinhados.

**Resolução sugerida:** A partilha de blob de página na Data Box ou data box heavy suporta apenas ficheiros que são 512 bytes alinhados (por exemplo, VHD/VHDX). Quaisquer dados copiados para a partilha de blob de página são enviados para a Azure como bolhas de página.

Remova quaisquer dados não VHD/VHDX da partilha da página blob. Pode utilizar ações para ficheiros block blob ou Azure para obter dados genéricos.

Para obter mais informações, consulte [a visão geral das bolhas de página](../storage/blobs/storage-blob-pageblob-overview.md).

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Descrição do erro:** Um tipo de ficheiro não suportado está presente numa partilha de disco gerida. Só são permitidos VHDs fixos.

**Resolução sugerida:**

- Certifique-se de que apenas carrega os VHDs fixos para criar discos geridos.
- Os ficheiros VHDX ou VHDs **dinâmicos** e **diferentes** não são suportados.

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Descrição do erro:** Não é permitido um diretório em nenhuma das pastas pré-existentes para os discos geridos. Só são permitidos VHDs fixos nestas pastas.

**Resolução sugerida:** Para discos geridos, dentro de cada ação, são criadas as seguintes três pastas que correspondem a contentores na sua conta de armazenamento: Premium SSD, Standard HDD e Standard SSD. Estas pastas correspondem ao nível de desempenho do disco gerido.

- Certifique-se de que copia os dados da sua página blob (VHDs) numa destas pastas existentes.
- Não é permitida uma pasta ou diretório nestas pastas existentes. Remova quaisquer pastas que tenha criado dentro das pastas pré-existentes.

Para obter mais informações, consulte [Copy para discos geridos.](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**Descrição do erro:** Não são permitidas ligações simbólicas em Linux. 

**Resolução sugerida:** As ligações simbólicas são geralmente ligações, tubos e outros ficheiros. Remova os links ou resolva os links e copie os dados.


## <a name="non-critical-blob-or-file-errors"></a>Erros de bolha ou ficheiro não críticos

Todos os erros não críticos relacionados com nomes de bolhas, ficheiros ou contentores que são vistos durante a cópia de dados são resumidos na secção seguinte. Se estes erros estiverem presentes, os nomes serão modificados de acordo com as convenções de nomeação Azure. O estado de encomenda correspondente para o upload de dados será **preenchido com avisos**.  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Descrição do erro:** Os nomes de bolhas ou ficheiros contêm caracteres de controlo não suportados.

**Resolução sugerida:** As bolhas ou os ficheiros que copiou contêm nomes com caracteres não suportados.

Na página **De Ligar e copiar** da UI web local, faça o download e reveja os ficheiros de erro.
Remova ou rebatize os ficheiros para remover caracteres não suportados.

Para obter mais informações, consulte as convenções de nomeação Azure para [nomes blob](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomes de ficheiros](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Descrição do erro:** Os nomes da bolha ou dos ficheiros contêm caracteres ilegais.

**Resolução sugerida:** As bolhas ou os ficheiros que copiou contêm nomes com caracteres não suportados.

Na página **De Ligar e copiar** da UI web local, faça o download e reveja os ficheiros de erro.
Remova ou rebatize os ficheiros para remover caracteres não suportados.

Para obter mais informações, consulte as convenções de nomeação Azure para [nomes blob](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomes de ficheiros](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Descrição do erro:** A bolha ou os nomes dos ficheiros estão a acabar com personagens maus.

**Resolução sugerida:** As bolhas ou os ficheiros que copiou contêm nomes com caracteres não suportados.

Na página **De Ligar e copiar** da UI web local, faça o download e reveja os ficheiros de erro.
Remova ou rebatize os ficheiros para remover caracteres não suportados.

Para obter mais informações, consulte as convenções de nomeação Azure para [nomes blob](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [nomes de ficheiros](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Descrição do erro:** O nome blob ou ficheiro contém demasiados segmentos de caminhos.

**Resolução sugerida:** As bolhas ou os ficheiros que copiou excedem o número máximo de segmentos de caminhos. Um segmento de caminho é a cadeia entre caracteres delimiter consecutivos, por exemplo, o corte para a frente /.

- Na página **De Ligar e copiar** da UI web local, faça o download e reveja os ficheiros de erro.
- Certifique-se de que os [nomes blob](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [os nomes dos ficheiros](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) estão em conformidade com as convenções de nomeação Azure.

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Descrição do erro:** O nome da bolha ou do ficheiro é muito longo.

**Resolução sugerida:** A bolha ou os nomes dos ficheiros excedem o comprimento máximo.

- Na página **De Ligar e copiar** da UI web local, faça o download e reveja os ficheiros de erro.
- O nome blob não deve exceder 1.024 caracteres.
- Remova ou rebatize a bolha ou os ficheiros para que os nomes não excedam 1024 caracteres.

Para mais informações, consulte as convenções de nomeação Azure para nomes de bolhas e nomes de ficheiros.

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Descrição do erro:** Um dos segmentos do nome do blob ou do ficheiro é demasiado longo.

**Resolução sugerida:** Um dos segmentos de caminho na bolha ou nome de ficheiro excede o número máximo de caracteres. Um segmento de caminho é a cadeia entre caracteres delimiter consecutivos, por exemplo, o corte para a frente /.

- Na página **De Ligar e copiar** da UI web local, faça o download e reveja os ficheiros de erro.
- Certifique-se de que os [nomes blob](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e [os nomes dos ficheiros](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) estão em conformidade com as convenções de nomeação Azure.


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Descrição do erro:** São especificados nomes impróprios de contentores para ações de discos geridos.

**Resolução sugerida:** Para discos geridos, dentro de cada ação, são criadas as seguintes pastas que correspondem a contentores na sua conta de armazenamento: Premium SSD, Standard HDD e Standard SSD. Estas pastas correspondem ao nível de desempenho do disco gerido.

- Certifique-se de que copia os dados da sua página blob (VHDs) numa destas pastas existentes. Apenas os dados destes contentores existentes são enviados para Azure.
- Qualquer outra pasta que seja criada ao mesmo nível que o Premium SSD, Standard HDD e Standard SSD não corresponde a um nível de desempenho válido e não pode ser utilizado.
- Remova ficheiros ou pastas criadas fora dos níveis de desempenho.

Para obter mais informações, consulte [Copy para discos geridos.](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os requisitos do [sistema de armazenamento de caixa de dados.](data-box-system-requirements-rest.md)