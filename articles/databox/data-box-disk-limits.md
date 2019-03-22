---
title: Limites de disco do Azure Data Box | Documentos da Microsoft
description: Descreve os limites de sistema e os tamanhos recomendados para o disco do Microsoft Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 02/19/2019
ms.author: alkohli
ms.openlocfilehash: 9cad48eeadc06c84e326cbc5f19f1c97e151a795
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880454"
---
# <a name="azure-data-box-disk-limits"></a>Limites de disco de caixa de dados do Azure


Considere estes limites, como implantar e operar a sua solução de disco do Microsoft Azure Data Box.

## <a name="data-box-service-limits"></a>Limites do serviço de caixa de dados

 - Serviço do Data Box está disponível nas regiões do Azure listado na [disponibilidade das regiões](data-box-disk-overview.md#region-availability).
 - Uma conta de armazenamento única é suportada com disco Data Box.

## <a name="data-box-disk-performance"></a>Desempenho de disco de caixa de dados

Nos testes com discos ligados por USB 3.0, o desempenho dos discos foi de 430 MB/s. A velocidade real pode variar consoante o tamanho do ficheiro utilizado. Para ficheiros mais pequenos, o desempenho poderá ser mais lento.

## <a name="azure-storage-limits"></a>Limites de armazenamento do Azure

Esta secção descreve os limites para o serviço de armazenamento do Azure e as convenções de nomenclatura necessárias para ficheiros do Azure, blobs de blocos do Azure e blobs de páginas do Azure, conforme aplicável para o serviço do Data Box. Reveja os limites de armazenamento com atenção e siga as recomendações apresentadas.

Para obter as informações mais recentes sobre os limites de serviço de armazenamento do Azure e práticas recomendadas para a nomenclatura de partilhas, contentores e ficheiros de mensagens em fila, aceda a:

- [Nomenclatura e referência para contentores](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Naming and referencing shares](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (Nomenclatura e referência a partilhas)
- [Blobs de blocos e as convenções de blob de página](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Se existirem quaisquer ficheiros ou diretórios que excedem os limites de serviço de armazenamento do Azure ou não está em conformidade com as convenções de nomenclatura de ficheiros/BLOBs do Azure, em seguida, estes ficheiros ou diretórios não são ingeridos no armazenamento do Azure através do serviço do Data Box.

## <a name="data-upload-caveats"></a>Limitações de carregamento de dados

- Não copie dados diretamente para os discos. Copiar dados para pré-criada *BlockBlob*,*PageBlob*, e *AzureFile* pastas.
- Uma pasta sob o *BlockBlob* e *PageBlob* é um contentor. Por exemplo, os contentores são criados como *BlockBlob/contentores* e *PageBlob/contentor*.
- Se tiver um objeto existente do Azure (por exemplo, um blob) na cloud com o mesmo nome que o objeto que está a ser copiado, o disco Data Box renomeie o arquivo como file(1) na cloud.
- Todos os ficheiros escritos na *BlockBlob* e *PageBlob* partilhas é carregado como um blob de blocos e BLOBs de páginas, respectivamente.
- Esvaziar qualquer hierarquia de diretório (sem quaisquer ficheiros) criada *BlockBlob* e *PageBlob* pastas não é carregado.
- Se houver algum erro ao carregar dados para o Azure, é criado um registo de erros na conta de armazenamento de destino. O caminho para este registo de erros está disponível no portal do quando o carregamento estiver concluído e pode rever o registo para tomar medidas corretivas. Não elimine dados da origem sem verificar os dados carregados.
- Se tiver especificado os discos geridos na ordem, reveja as seguintes considerações adicionais:

    - Só pode ter um disco gerido com um determinado nome num grupo de recursos em todas as pastas pré-criada e em todo o disco de caixa de dados. Isso implica que os VHDs carregados para as pastas pré-criada devem ter nomes exclusivos. Certifique-se de que o nome fornecido não corresponde a um disco gerido já existente num grupo de recursos. Se os VHDs têm os mesmos nomes, em seguida, apenas um VHD é convertido para disco gerido com esse nome. Os outros VHDs são carregados como blobs de páginas a conta de armazenamento de teste.
    - Copie sempre os VHDs a uma das pastas pré-criada. Se copiar os VHDs fora essas pastas ou numa pasta que criou, os VHDs são carregados para a conta de armazenamento do Azure, como blobs de páginas e discos não geridos.
    - Apenas os VHDs fixos podem ser carregados para criar discos geridos. VHDs dinâmicos, VHDs diferenciais ou VHDX não são suportados ficheiros.

## <a name="azure-storage-account-size-limits"></a>Limites de tamanho de conta de armazenamento do Azure

Aqui estão os limites no tamanho dos dados que são copiados para a conta de armazenamento. Certifique-se de que os dados carregados estão de acordo com estes limites. Para obter as informações mais atualizadas sobre estes limites, aceda a [metas de dimensionamento de armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) e [alvos de dimensionamento de ficheiros do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Tamanho dos dados copiados para a conta de armazenamento do Azure                      | Limite predefinido          |
|---------------------------------------------------------------------|------------------------|
| Blob de página e BLOBs de blocos                                            | 500 TB por conta de armazenamento. <br> Isto inclui dados de todas as origens, incluindo disco Data Box.|


## <a name="azure-object-size-limits"></a>Limites de tamanho de objeto do Azure

Aqui estão os tamanhos dos objetos do Azure que podem ser gravados. Certifique-se de que todos os ficheiros que são carregados estão em conformidade com estes limites.

| Tipo de objeto do Azure | Limite predefinido                                             |
|-------------------|-----------------------------------------------------------|
| Blob de Blocos        | ~ 4.75 TiB                                                 |
| Blob de Página         | 8 TiB <br> (Todos os ficheiros carregados no formato de Blob de páginas tem de ser 512 bytes alinhados, caso contrário o carregamento falha. <br> O VHD e VHDX são alinhadas de 512 bytes.) |
|Ficheiros do Azure        | 1 TiB <br> Um máximo de tamanho da partilha de é de 5 TiB     |
| Managed disks     |4 TiB <br> Para obter mais informações sobre o tamanho e limites, consulte: <li>[Destinos de escalabilidade para discos geridos](../virtual-machines/windows/disk-scalability-targets.md#managed-virtual-machine-disks)</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Blob de blocos do Azure, BLOBs de páginas e convenções de nomenclatura de ficheiro

| Entidade                                       | Convenções                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Os nomes dos contentores de blob de blocos e BLOBs de páginas <br> Nomes de partilha de ficheiros para ficheiros do Azure | Tem de ser um nome DNS válido é entre 3 e 63 carateres de comprimento. <br>  Tem de começar com uma letra ou um número. <br> Pode conter apenas letras minúsculas, números e hífen (-). <br> Cada hífen (-) tem de ser imediatamente precedido e seguido por uma letra ou um número. <br> Não são permitidos hífenes consecutivos em nomes. |
| Nomes de diretórios e arquivos para os ficheiros do Azure     |<li> Preservação de caso, maiúsculas e minúsculas e não pode exceder os 255 carateres de comprimento. </li><li> Não pode terminar com a barra (/). </li><li>Se for fornecido, será automaticamente removido. </li><li> Carateres seguintes não são permitidos: <code>" \\ / : \| < > * ?</code></li><li> Os carateres de URL reservados devem ser escritos corretamente. </li><li> Não são permitidos carateres ilegais de caminho de URL. Código pontos como \\uE000 não são carateres válidos do Unicode. Alguns caracteres ASCII ou Unicode, como carateres de controlo (0x00 para 0x1F \\u0081, etc.), não também são permitidas. Para regras para Unicode cadeias de caracteres no HTTP/1.1, consulte RFC 2616, secção 2.2: Regras básicas e RFC 3987. </li><li> Os nomes de ficheiros seguintes não são permitidos: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, caráter de ponto (.) e dois pontos carateres (.).</li>|
| Nomes de blobs para blob de blocos e blob de páginas      | Os nomes de blobs são sensíveis a maiúsculas e minúsculas e podem conter qualquer combinação de carateres. <br> Um nome de blob tem de ter entre 1 e 1024 carateres de comprimento. <br> Os carateres de URL reservados devem ser escritos corretamente. <br>O número de segmentos de linha que inclui o nome do blob não pode exceder 254. Um segmento de linha é a cadeia de carateres entre os carateres delimitadores consecutivos (por exemplo, uma barra "/") que corresponde ao nome de um diretório virtual. |

## <a name="managed-disk-naming-conventions"></a>Gerido convenções de nomenclatura de disco

| Entidade | Convenções                                             |
|-------------------|-----------------------------------------------------------|
| Gerido nomes de disco       | <li> O nome tem de ser 1 para 80 carateres de comprimento. </li><li> O nome tem de começar com uma letra ou número, terminar com uma letra, número ou caráter de sublinhado. </li><li> O nome pode conter apenas letras, números, carateres de sublinhado, pontos ou hífenes. </li><li>   O nome não deve ter espaços ou `/`.                                              |

## <a name="next-steps"></a>Passos Seguintes

- Revisão [requisitos de sistema do Data Box](data-box-system-requirements.md)
