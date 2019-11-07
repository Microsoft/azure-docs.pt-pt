---
title: Limites de Disco do Azure Data Box | Microsoft Docs
description: Descreve os limites do sistema e os tamanhos recomendados para o Microsoft Azure Disco do Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: cd40c5d11414c91ff2f2febc0621e1e06f79e9cf
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646970"
---
# <a name="azure-data-box-disk-limits"></a>Limites de Disco do Azure Data Box


Considere esses limites ao implantar e operar sua solução de Disco do Data Box Microsoft Azure.

## <a name="data-box-service-limits"></a>Limites de serviço Data Box

 - Data Box serviço está disponível nas regiões do Azure listadas em [disponibilidade de região](data-box-disk-overview.md#region-availability).
 - Há suporte para uma única conta de armazenamento com Disco do Data Box.

## <a name="data-box-disk-performance"></a>Desempenho de Disco do Data Box

Nos testes com discos ligados por USB 3.0, o desempenho dos discos foi de 430 MB/s. A velocidade real pode variar consoante o tamanho do ficheiro utilizado. Para ficheiros mais pequenos, o desempenho poderá ser mais lento.

## <a name="azure-storage-limits"></a>Limites de armazenamento do Azure

Esta seção descreve os limites do serviço de armazenamento do Azure e as convenções de nomenclatura necessárias para arquivos do Azure, blobs de blocos do Azure e blobs de páginas do Azure, conforme aplicável ao serviço de Data Box. Examine cuidadosamente os limites de armazenamento e siga todas as recomendações.

Para obter as informações mais recentes sobre os limites do serviço de armazenamento do Azure e as práticas recomendadas para nomear compartilhamentos, contêineres e arquivos, acesse:

- [Nomeando e referenciando contêineres](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Naming and referencing shares](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (Nomenclatura e referência a partilhas)
- [Blobs de bloco e convenções de blob de páginas](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Se houver arquivos ou diretórios que excedam os limites do serviço de armazenamento do Azure ou não estiverem em conformidade com as convenções de nomenclatura de blob/arquivos do Azure, esses arquivos ou diretórios não serão incluídos no armazenamento do Azure por meio do serviço de Data Box.

## <a name="data-upload-caveats"></a>Advertências de upload de dados

- Não copie dados diretamente para os discos. Copie os dados para as pastas *BlockBlob*,*PageBlob*e *azurefile* criadas previamente.
- Uma pasta em *BlockBlob* e *PageBlob* é um contêiner. Por exemplo, os contêineres são criados como *BlockBlob/container* e *PageBlob/container*.
- Se você tiver um objeto do Azure existente (como um blob) na nuvem com o mesmo nome que o objeto que está sendo copiado, Disco do Data Box renomeará o arquivo como arquivo (1) na nuvem.
- Cada arquivo gravado em compartilhamentos *BlockBlob* e *PageBlob* é carregado como blob de bloco e BLOB de página, respectivamente.
- Qualquer hierarquia de diretório vazia (sem nenhum arquivo) criada em pastas *BlockBlob* e *PageBlob* não é carregada.
- Se houver erros ao carregar dados no Azure, um log de erros será criado na conta de armazenamento de destino. O caminho para esse log de erros está disponível no portal quando o upload é concluído e você pode examinar o log para executar uma ação corretiva. Não exclua dados da fonte sem verificar os dados carregados.
- Os metadados de arquivo e as permissões NTFS não são preservados quando os dados são carregados nos arquivos do Azure. Por exemplo, o *último atributo modificado* dos arquivos não será mantido quando os dados forem copiados.
- Se você especificou Managed disks na ordem, examine as seguintes considerações adicionais:

    - Só é possível ter um disco gerido com um determinado nome num grupo de recursos em todas as pastas pré-criadas e em todo o Data Box Disk, o que implica que os VHDs carregados para as pastas pré-criadas devem ter nomes exclusivos. Verifique se o nome fornecido não corresponde a um disco gerido já existente num grupo de recursos. Se os VHDs tiverem os mesmos nomes, apenas um VHD será convertido em disco gerido com esse nome. Os outros VHDs serão carregados como blobs de páginas na conta de armazenamento de teste.
    - Copie sempre os VHDs para uma das pastas pré-criadas. Se copiar os VHDs para outras pastas ou para alguma pasta que tiver criado, os VHDs serão carregados na Conta de armazenamento do Azure como blobs de páginas e não como discos geridos.
    - Apenas os VHDs fixos podem ser carregados para criar discos geridos. Os VHDs dinâmicos, VHDs diferenciais ou ficheiros VHDX não são suportados.

## <a name="azure-storage-account-size-limits"></a>Limites de tamanho da conta de armazenamento do Azure

Aqui estão os limites do tamanho dos dados que são copiados na conta de armazenamento. Verifique se os dados carregados estão em conformidade com esses limites. Para obter as informações mais atualizadas sobre esses limites, acesse destinos de [escala do armazenamento de BLOBs do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) e [destinos de escala de arquivos do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Tamanho dos dados copiados para a conta de armazenamento do Azure                      | Limite predefinido          |
|---------------------------------------------------------------------|------------------------|
| Blob de blocos e BLOB de páginas                                            | 500 TB por conta de armazenamento. <br> Isso inclui dados de todas as fontes, incluindo Disco do Data Box.|


## <a name="azure-object-size-limits"></a>Limites de tamanho de objeto do Azure

Aqui estão os tamanhos dos objetos do Azure que podem ser gravados. Certifique-se de que todos os arquivos que são carregados estão em conformidade com esses limites.

| Tipo de objeto do Azure | Limite predefinido                                             |
|-------------------|-----------------------------------------------------------|
| Blob de blocos        | ~ 4,75 TiB                                                 |
| Blob de páginas         | 8 TiB <br> (Cada arquivo carregado no formato de blob de páginas deve ter 512 bytes alinhados, caso contrário, o carregamento falhará. <br> O VHD e o VHDX são 512 bytes alinhados.) |
|Ficheiros do Azure        | 1 TiB <br> Um máximo de o tamanho do compartilhamento é 5 TiB     |
| Managed disks     |4 TiB <br> Para obter mais informações sobre tamanho e limites, consulte: <li>[Metas de escalabilidade para discos gerenciados](../virtual-machines/windows/disk-scalability-targets.md#managed-virtual-machine-disks)</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Blob de blocos do Azure, BLOB de páginas e convenções de nomenclatura de arquivo

| Entidade                                       | Convenções                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nomes de contêiner para blob de blocos e BLOB de páginas <br> Nomes de FileShare para arquivos do Azure | Deve ser um nome DNS válido que tenha de 3 a 63 caracteres de comprimento. <br>  Tem de começar com uma letra ou um número. <br> Pode conter apenas letras minúsculas, números e hífen (-). <br> Cada hífen (-) tem de ser imediatamente precedido e seguido por uma letra ou um número. <br> Não são permitidos hífenes consecutivos em nomes. |
| Nomes de diretório e arquivo para arquivos do Azure     |<li> Preservar maiúsculas de minúsculas, não diferencia maiúsculas de minúsculas e não deve exceder 255 caracteres de comprimento. </li><li> Não é possível terminar com a barra (/). </li><li>Se fornecido, ele será removido automaticamente. </li><li> Os seguintes caracteres não são permitidos: <code>" \\ / : \| < > * ?</code></li><li> Os carateres de URL reservados devem ser escritos corretamente. </li><li> Caracteres de caminho de URL ilegais não são permitidos. Pontos de código como \\uE000 não são caracteres Unicode válidos. Alguns caracteres ASCII ou Unicode, como caracteres de controle (0x00 a 0x1F, \\u0081, etc.), também não são permitidos. Para regras que regem as cadeias de caracteres Unicode em HTTP/1.1, consulte RFC 2616, seção 2,2: regras básicas e RFC 3987. </li><li> Os seguintes nomes de arquivo não são permitidos: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK $, caractere de ponto (.) e dois caracteres de ponto (..).</li>|
| Nomes de blobs para blob de blocos e blob de páginas      | Os nomes de blobs são sensíveis a maiúsculas e minúsculas e podem conter qualquer combinação de carateres. <br> Um nome de blob tem de ter entre 1 e 1024 carateres de comprimento. <br> Os carateres de URL reservados devem ser escritos corretamente. <br>O número de segmentos de linha que inclui o nome do blob não pode exceder 254. Um segmento de linha é a cadeia de carateres entre os carateres delimitadores consecutivos (por exemplo, uma barra "/") que corresponde ao nome de um diretório virtual. |

## <a name="managed-disk-naming-conventions"></a>Convenções de nomenclatura de disco gerenciado

| Entidade | Convenções                                             |
|-------------------|-----------------------------------------------------------|
| Nomes de discos gerenciados       | <li> O nome deve ter de 1 a 80 caracteres de comprimento. </li><li> O nome deve começar com uma letra ou número, terminar com uma letra, número ou sublinhado. </li><li> O nome pode conter apenas letras, números, sublinhados, pontos ou hifens. </li><li>   O nome não deve ter espaços ou `/`.                                              |

## <a name="next-steps"></a>Passos seguintes

- Examinar [disco do data Box requisitos do sistema](data-box-disk-system-requirements.md)
