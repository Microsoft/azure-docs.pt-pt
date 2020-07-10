---
title: Limites da Caixa de Dados Azure / Microsoft Docs
description: Descreve os limites do sistema e os tamanhos recomendados para os componentes e ligações da Microsoft Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 7d699fc47fa0a0cb57d103ff42ff17bdc0f3a82b
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86202689"
---
# <a name="azure-data-box-limits"></a>Limites da Caixa de Dados Azure

Considere estes limites à medida que implementa e opera a sua Caixa de Dados Microsoft Azure. A tabela seguinte descreve estes limites para a Caixa de Dados.

## <a name="data-box-service-limits"></a>Limites de serviço data box

[!INCLUDE [data-box-service-limits](../../includes/data-box-service-limits.md)]

## <a name="data-box-limits"></a>Limites da Caixa de Dados

- A Data Box pode armazenar um máximo de 500 milhões de ficheiros para importação e exportação.
- A Data Box suporta um máximo de 512 contentores ou partilhas na nuvem. Os diretórios de alto nível dentro da partilha do utilizador tornam-se contentores ou partilhas de ficheiros Azure na nuvem. 
- A capacidade de utilização da Caixa de Dados pode ser inferior a 80 TB devido ao consumo de metadados reFS.

## <a name="azure-storage-limits"></a>Limites de armazenamento Azure

[!INCLUDE [data-box-storage-limits](../../includes/data-box-storage-limits.md)]

## <a name="data-upload-caveats"></a>Limitações do carregamento de dados


### <a name="for-import-order"></a>Para encomenda de importação

As ressalvas da Caixa de Dados para uma ordem de importação incluem:

[!INCLUDE [data-box-data-upload-caveats](../../includes/data-box-data-upload-caveats.md)]

## <a name="for-export-order"></a>Para encomenda de exportação

As ressalvas da Caixa de Dados para uma encomenda de exportação incluem:

- Data Box é um dispositivo baseado no Windows e não suporta nomes de ficheiros sensíveis a casos. Por exemplo, pode ter dois ficheiros diferentes em Azure com nomes que apenas diferem no invólucro. Não utilize a caixa de dados para exportar ficheiros como estes serão substituídos no dispositivo.
- Se tiver etiquetas duplicadas em ficheiros de entrada ou tags referentes aos mesmos dados, a exportação da Data Box poderá saltar ou substituir os ficheiros. O número de ficheiros e o tamanho dos dados apresentados no portal Azure podem diferir do tamanho real dos dados presentes no dispositivo. 
- A Data Box exporta dados para o sistema baseado no Windows sobre o SMB e é limitado por limitações SMB para ficheiros e pastas. Os ficheiros e pastas com nomes não suportados não são exportados.
- Há um mapeamento 1:1 do prefixo ao recipiente.
- O tamanho máximo do nome de ficheiros é de 1024 ficheiros de caracteres, os nomes de ficheiros que excedam este comprimento não são exportados.
- São exportados prefixos duplicados no ficheiro *xml* (carregado durante a criação da encomenda). Os prefixos duplicados não são ignorados.
- As bolhas de página e os nomes dos recipientes são sensíveis à caixa, pelo que, se o invólucro for desajustado, a bolha e/ou o recipiente não serão encontrados.
 

## <a name="azure-storage-account-size-limits"></a>Limites de tamanho da conta de armazenamento Azure

[!INCLUDE [data-box-storage-account-size-limits](../../includes/data-box-storage-account-size-limits.md)]

## <a name="azure-object-size-limits"></a>Limites de tamanho de objeto azul

[!INCLUDE [data-box-object-size-limits](../../includes/data-box-object-size-limits.md)]

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Blob bloco Azure, bolha de página e convenções de nomeação de arquivos

[!INCLUDE [data-box-naming-conventions](../../includes/data-box-naming-conventions.md)]
