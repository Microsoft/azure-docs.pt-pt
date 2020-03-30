---
title: Limites do disco da caixa de dados azure Microsoft Docs
description: Descreve os limites do sistema e os tamanhos recomendados para o Disco de Caixa de Dados do Microsoft Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 1bb8300f1e54cf03563704cf00549ce9e09a3916
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260166"
---
# <a name="azure-data-box-disk-limits"></a>Limites do disco de caixa de dados azure


Considere estes limites à medida que implementa e opera a sua solução de Disco de Caixa de Dados Microsoft Azure.

## <a name="data-box-service-limits"></a>Limites de serviço caixa de dados

 - O serviço Data Box está disponível nas regiões azure listadas na disponibilidade da [Região.](data-box-disk-overview.md#region-availability)
 - Uma única conta de armazenamento é suportada com o Disco da Caixa de Dados.

## <a name="data-box-disk-performance"></a>Desempenho do Disco de Caixa de Dados

Nos testes com discos ligados por USB 3.0, o desempenho dos discos foi de 430 MB/s. A velocidade real pode variar consoante o tamanho do ficheiro utilizado. Para ficheiros mais pequenos, o desempenho poderá ser mais lento.

## <a name="azure-storage-limits"></a>Limites de armazenamento azure

Esta secção descreve os limites para o serviço de armazenamento Azure, e as convenções de nomeação necessárias para Ficheiros Azure, blobs de blocos Azure e blobs de página Azure, conforme aplicável ao serviço Data Box. Reveja cuidadosamente os limites de armazenamento e siga todas as recomendações.

Para obter as mais recentes informações sobre os limites do serviço de armazenamento Azure e as melhores práticas para nomear ações, contentores e ficheiros, vá a:

- [Nomeação e referenciação de recipientes](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Naming and referencing shares](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (Nomenclatura e referência a partilhas)
- [Bloco sinuosos e convenções de página blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Se existirem ficheiros ou diretórios que excedam os limites do serviço de armazenamento azure, ou não estejam em conformidade com as convenções de nomeação Azure Files/Blob, então estes ficheiros ou diretórios não são ingeridos no Armazenamento Azure através do serviço Data Box.

## <a name="data-upload-caveats"></a>Limitações do carregamento de dados

- Não copie os dados diretamente nos discos. Copiar dados para pastas *blockBlob,**PageBlob*e *AzureFile* pré-criadas.
- Uma pasta sob o *BlockBlob* e *PageBlob* é um recipiente. Por exemplo, os recipientes são criados como *BlockBlob/contentor* e *PageBlob/contentor*.
- Se tiver um objeto Azure existente (como uma bolha) na nuvem com o mesmo nome que o objeto que está a ser copiado, o Disco da Caixa de Dados renomeará o ficheiro como ficheiro(1) na nuvem.
- Todos os ficheiros escritos nas ações *do BlockBlob* e *do PageBlob* são carregados como uma bolha de bloco e uma bolha de página, respectivamente.
- Qualquer hierarquia de diretório vazia (sem ficheiros) criada sob as pastas *BlockBlob* e *PageBlob* não é carregada.
- Se houver erros ao enviar dados para o Azure, é criado um registo de erro na conta de armazenamento do alvo. O caminho para este registo de erro está disponível no portal quando o upload estiver completo e pode rever o registo para tomar medidas corretivas. Não elimine os dados da fonte sem verificar os dados enviados.
- Os metadados de ficheiros e as permissões NTFS não são preservados quando os dados são enviados para ficheiros Azure. Por exemplo, o último atributo *modificado* dos ficheiros não será conservado quando os dados forem copiados.
- Se especificou os discos geridos na ordem, reveja as seguintes considerações adicionais:

    - Só é possível ter um disco gerido com um determinado nome num grupo de recursos em todas as pastas pré-criadas e em todo o Data Box Disk, o que implica que os VHDs carregados para as pastas pré-criadas devem ter nomes exclusivos. Verifique se o nome fornecido não corresponde a um disco gerido já existente num grupo de recursos. Se os VHDs tiverem os mesmos nomes, apenas um VHD será convertido em disco gerido com esse nome. Os outros VHDs serão carregados como blobs de páginas na conta de armazenamento de teste.
    - Copie sempre os VHDs para uma das pastas pré-criadas. Se copiar os VHDs para outras pastas ou para alguma pasta que tiver criado, os VHDs serão carregados na Conta de armazenamento do Azure como blobs de páginas e não como discos geridos.
    - Apenas os VHDs fixos podem ser carregados para criar discos geridos. Os VHDs dinâmicos, VHDs diferenciais ou ficheiros VHDX não são suportados.

## <a name="azure-storage-account-size-limits"></a>Limites de tamanho da conta de armazenamento azure

Aqui estão os limites do tamanho dos dados que são copiados para a conta de armazenamento. Certifique-se de que os dados que envia estão em conformidade com estes limites. Para obter informações mais atualizadas sobre estes limites, vá aos alvos da escala de [armazenamento de blob Azure](https://docs.microsoft.com/azure/storage/blobs/scalability-targets#scale-targets-for-blob-storage) e aos alvos de [escala de Azure Files](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account#scale-targets-for-standard-storage-accounts).

| Tamanho dos dados copiados na conta de armazenamento do Azure                      | Limite predefinido          |
|---------------------------------------------------------------------|------------------------|
| Bloco Blob e página blob                                            | 500 TB por conta de armazenamento. <br> Isto inclui dados de todas as fontes, incluindo o Disco de Caixa de Dados.|


## <a name="azure-object-size-limits"></a>Limites de tamanho do objeto azul

Aqui estão os tamanhos dos objetos Azure que podem ser escritos. Certifique-se de que todos os ficheiros que são carregados estão em conformidade com estes limites.

| Tipo de objeto azul | Limite predefinido                                             |
|-------------------|-----------------------------------------------------------|
| Bloco Blob        | ~ 4.75 Tib                                                 |
| Página Blob         | 8 TiB <br> (Todos os ficheiros enviados no formato Page Blob devem estar alinhados 512 bytes, caso contrário o upload falha. <br> Tanto o VHD como o VHDX estão 512 bytes alinhados.) |
|Ficheiros do Azure        | 1 TiB <br> Um máximo de o tamanho da parte é 5 TiB     |
| Managed disks     |4 TiB <br> Para obter mais informações sobre tamanho e limites, consulte: <li>[Metas de escalabilidade para discos geridos](../virtual-machines/windows/disk-scalability-targets.md#managed-virtual-machine-disks)</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Blob de bloco sintetiza, página blob e convenções de nomeação de ficheiros

| Entidade                                       | Convenções                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nomes de contentores para bloco blob e página blob <br> Nomes de fileshare para Ficheiros Azure | Deve ser um nome DNS válido que tenha 3 a 63 caracteres de comprimento. <br>  Tem de começar com uma letra ou um número. <br> Só pode conter letras minúsculas, números e o hífen (-). <br> Cada hífen (-) tem de ser imediatamente precedido e seguido por uma letra ou um número. <br> Não são permitidos hífenes consecutivos em nomes. |
| Nomes de diretório e arquivo para ficheiros Azure     |<li> Conservação de casos, insensível a casos e não deve exceder 255 caracteres de comprimento. </li><li> Não pode terminar com o corte dianteiro (/). </li><li>Se fornecido, será automaticamente removido. </li><li> Os seguintes caracteres não são permitidos:<code>" \\ / : \| < > * ?</code></li><li> Os carateres de URL reservados devem ser escritos corretamente. </li><li> Não são permitidos caracteres ilegais de caminhos de URL. Pontos \\de código como o uE000 não são caracteres Unicode válidos. Alguns caracteres ASCII ou Unicode, como caracteres de controlo \\(0x00 a 0x1F, u0081, etc.), também não são permitidos. Para as regras que regem as cordas Unicode em HTTP/1.1 ver RFC 2616, Secção 2.2: Regras Básicas e RFC 3987. </li><li> Não são permitidos os seguintes nomes de ficheiros: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, dot character (.), e dois caracteres pontos (..</li>|
| Nomes de blobs para blob de blocos e blob de páginas      | Os nomes de blobs são sensíveis a maiúsculas e minúsculas e podem conter qualquer combinação de carateres. <br> Um nome de blob tem de ter entre 1 e 1024 carateres de comprimento. <br> Os carateres de URL reservados devem ser escritos corretamente. <br>O número de segmentos de linha que inclui o nome do blob não pode exceder 254. Um segmento de linha é a cadeia de carateres entre os carateres delimitadores consecutivos (por exemplo, uma barra "/") que corresponde ao nome de um diretório virtual. |

## <a name="managed-disk-naming-conventions"></a>Convenções de nomeação de discos geridos

| Entidade | Convenções                                             |
|-------------------|-----------------------------------------------------------|
| Nomes de discos geridos       | <li> O nome deve ter 1 a 80 caracteres de comprimento. </li><li> O nome deve começar com uma letra ou número, terminar com uma letra, número ou sublinhado. </li><li> O nome pode conter apenas letras, números, sublinhados, períodos ou hífenes. </li><li>   O nome não deve `/`ter espaços ou .                                              |

## <a name="next-steps"></a>Passos seguintes

- Rever os [requisitos](data-box-disk-system-requirements.md) do sistema de disco de caixa de dados
