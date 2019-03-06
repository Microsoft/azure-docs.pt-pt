---
title: Limites do Azure Data Box | Documentos da Microsoft
description: Descreve os limites de sistema e os tamanhos recomendados para os componentes do Microsoft Azure Data Box e ligações.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 02/22/2019
ms.author: alkohli
ms.openlocfilehash: 2776433f01cae6c32eddc262ab9d42dad1c3936f
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57407260"
---
# <a name="azure-data-box-limits"></a>Limites do Azure Data Box

Considere estes limites, como implantar e operar o Microsoft Azure Data Box. A tabela seguinte descreve estes limites para o Data Box.


## <a name="data-box-service-limits"></a>Limites do serviço de caixa de dados

 - Se utilizar várias contas de armazenamento com o serviço do Data Box, todas as contas de armazenamento devem pertencer à mesma região do Azure.
 - Recomendamos que utilize mais do que três contas de armazenamento. Utilizar contas de armazenamento mais pode vir a afetar o desempenho.

## <a name="data-box-limits"></a>Limites da caixa de dados

- Caixa de dados pode armazenar um máximo de 500 milhões de ficheiros.

## <a name="azure-storage-limits"></a>Limites de armazenamento do Azure

Esta secção descreve os limites para o serviço de armazenamento do Azure e as convenções de nomenclatura necessárias para ficheiros do Azure, blobs de blocos do Azure e blobs de páginas do Azure, conforme aplicável para o serviço do Data Box. Reveja os limites de armazenamento com atenção e siga as recomendações apresentadas.

Para obter as informações mais recentes sobre os limites de serviço de armazenamento do Azure e práticas recomendadas para a nomenclatura de partilhas, contentores e ficheiros de mensagens em fila, aceda a:

- [Nomenclatura e referência para contentores](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Naming and referencing shares](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (Nomenclatura e referência a partilhas)
- [Blobs de blocos e as convenções de blob de página](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Se existirem quaisquer ficheiros ou diretórios que excedem os limites de serviço de armazenamento do Azure ou não está em conformidade com as convenções de nomenclatura de ficheiros/BLOBs do Azure, em seguida, estes ficheiros ou diretórios não são ingeridos no armazenamento do Azure através do serviço do Data Box.

## <a name="data-upload-caveats"></a>Limitações de carregamento de dados

- Não copie os ficheiros diretamente a qualquer uma das partilhas de pré-criada. Precisa criar uma pasta sob a partilha e, em seguida, copiar ficheiros para essa pasta.
- Uma pasta sob o *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* é um contentor. Por exemplo, os contentores são criados como *StorageAccount_BlockBlob/contentores* e *StorageAccount_PageBlob/contentor*.
- Cada pasta criada diretamente sob *StorageAccount_AzureFiles* é convertida numa partilha de ficheiros do Azure.
- Se tiver um objeto do Azure existente (como um blob ou um arquivo) na cloud com o mesmo nome que o objeto que está a ser copiado, o Data Box irá substituir o ficheiro na cloud.
- Todos os ficheiros escritos na *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* partilhas é carregado como um blob de blocos e BLOBs de páginas, respectivamente.
- Armazenamento de Blobs do Azure não suporta diretórios. Se criar uma pasta sob o *StorageAccount_BlockBlob* pasta, em seguida, pastas virtuais são criadas no nome do blob. Para ficheiros do Azure, a estrutura de diretórios real é mantida.
- Esvaziar qualquer hierarquia de diretório (sem quaisquer ficheiros) criada *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* pastas não é carregado.
- Se houver algum erro ao carregar dados para o Azure, é criado um registo de erros na conta de armazenamento de destino. O caminho para este registo de erros está disponível quando o carregamento estiver concluído e pode rever o registo para tomar medidas corretivas. Não elimine dados da origem sem verificar os dados carregados.

## <a name="azure-storage-account-size-limits"></a>Limites de tamanho de conta de armazenamento do Azure

Aqui estão os limites no tamanho dos dados que são copiados para a conta de armazenamento. Certifique-se de que os dados carregados estão de acordo com estes limites. Para obter as informações mais atualizadas sobre estes limites, aceda a [metas de dimensionamento de armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) e [alvos de dimensionamento de ficheiros do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Tamanho dos dados copiados para a conta de armazenamento do Azure                      | Limite Predefinido          |
|---------------------------------------------------------------------|------------------------|
| Blob de blocos e BLOBs de páginas                                            | 500 TiB por conta de armazenamento. <br> Isto inclui dados de todas as origens, incluindo o Data Box.|
| Ficheiros do Azure                                                          | De 5 TiB por partilha.<br> Todas as pastas sob *StorageAccount_AzureFiles* tem de seguir este limite.       |

## <a name="azure-object-size-limits"></a>Limites de tamanho de objeto do Azure

Aqui estão os tamanhos dos objetos do Azure que podem ser gravados. Certifique-se de que todos os ficheiros que são carregados estão em conformidade com estes limites.

| Tipo de objeto do Azure | Limite Predefinido                                             |
|-------------------|-----------------------------------------------------------|
| Blob de bloco        | ~ 4.75 TiB                                                 |
| BLOBs de páginas         | 8 TiB <br> Todos os ficheiros carregados no formato de blob de página tem de ser 512 bytes alinhados (um múltiplo de integral), caso contrário o carregamento falha. <br> VHD e VHDX são alinhadas de 512 bytes. |
| Ficheiros do Azure        | 1 TiB                                                      |
| Managed disks     | 4 TiB <br> Para obter mais informações sobre o tamanho e limites, consulte: <li>[Destinos de escalabilidade do SSDs padrão](../virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[Destinos de escalabilidade do Premium SSDs](../virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[Destinos de escalabilidade de HDDs padrão](../virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[Preços e faturação de discos geridos](../virtual-machines/windows/disks-types.md#billing)</li>                                                     |

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Blob de blocos do Azure, BLOBs de páginas e convenções de nomenclatura de ficheiro

| Entidade                                       | Convenções                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Os nomes dos contentores de blob de blocos e BLOBs de páginas | Tem de ser um nome DNS válido é entre 3 e 63 carateres de comprimento. <br>  Tem de começar com uma letra ou um número. <br> Pode conter apenas letras minúsculas, números e hífen (-). <br> Cada hífen (-) tem de ser imediatamente precedido e seguido por uma letra ou um número. <br> Hífenes consecutivos não são permitidos em nomes. |
| Os nomes das partilhas de ficheiros do Azure                  | Mesmo que acima                                                                                                                                                                                                                                                                                                             |
| Nomes de diretórios e arquivos para os ficheiros do Azure     |<li> Preservação de caso, maiúsculas e minúsculas e não pode exceder os 255 carateres de comprimento. </li><li> Não pode terminar com a barra (/). </li><li>Se for fornecido, será automaticamente removido. </li><li> Os carateres seguintes não são permitidos: "" \ /: | < > * ?`</li><li> Os carateres de URL reservados devem ser escritos corretamente. </li><li> Não são permitidos carateres ilegais de caminho de URL. Pontos de código, como \uE000 não são carateres válidos do Unicode. Alguns caracteres ASCII ou Unicode, como carateres de controlo (0x00 para 0x1F \u0081, etc.), não também são permitidas. Para regras para Unicode cadeias de caracteres no HTTP/1.1, consulte RFC 2616, secção 2.2: Regras básicas e RFC 3987. </li><li> Não são permitidos os seguintes nomes de ficheiros: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, caráter de ponto (.) e dois pontos carateres (.).</li>|
| Nomes de blobs para blob de blocos e blob de páginas      | </li><li>Os nomes de blobs são sensíveis a maiúsculas e minúsculas e podem conter qualquer combinação de carateres. </li><li>Um nome de blob tem de ter entre 1 e 1024 carateres de comprimento. </li><li>Os carateres de URL reservados devem ser escritos corretamente. </li><li>O número de segmentos de linha que inclui o nome do blob não pode exceder 254. Um segmento de linha é a cadeia de carateres entre os carateres delimitadores consecutivos (por exemplo, uma barra "/") que corresponde ao nome de um diretório virtual.</li> |
