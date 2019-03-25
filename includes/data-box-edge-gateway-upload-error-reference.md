---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: 05673885336dbfb9a70843bd0ccc4e700091ad7e
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58405967"
---
|     Código de erro     |      Descrição do erro     |
|--------------------|--------------------------|
|    100             | O nome do contentor ou da partilha tem de ter entre 3 e 63 carateres.|
|    101             | O nome do contentor ou da partilha só pode ter letras, números ou hífenes.|
|    102             | O nome do contentor ou da partilha só pode ter letras, números ou hífenes.|
|    103             | O nome do blob ou ficheiro contém carateres de controlo não suportados.|
|    104             | O nome do blob ou ficheiro contém carateres de controlo inválidos.|
|    105             | O nome blob ou ficheiro contém demasiados segmentos (cada segmento é separado por uma barra -/).|
|    106             | O nome do blob ou ficheiro é demasiado longo.|
|    107             | Um dos segmentos no nome do blob ou ficheiro é demasiado longo. |
|    108             | O tamanho do ficheiro excede o tamanho máximo de ficheiro para carregamento.    |
|    109             | O blob ou ficheiro está alinhado incorretamente.  |
|    110             | O nome do ficheiro ou blob codificado por Unicode não é válido.|
|    111             | O nome ou o prefixo do ficheiro ou blob é um nome reservado que não é suportado (por exemplo, COM1).|
|    2000            | Um erro de correspondência de etag indica que existe um conflito entre um blob de blocos na cloud e no dispositivo. Para resolver este conflito, elimine um desses ficheiros – a versão na cloud ou a versão no dispositivo.    |
|    2001            | Ocorreu um problema inesperado ao processar um ficheiro depois de o ficheiro ter sido carregado.    Se vir este erro, e o erro persistir durante mais de 24 horas, contacte o suporte. |
|    2002            | O ficheiro já está aberto noutro processo e não pode ser carregado até que o identificador seja fechado.|
|    2003            | Não foi possível abrir o ficheiro para carregamento. Se vir este erro, contacte o Suporte da Microsoft.|
|    2004            | Não foi possível ligar ao contentor para carregar dados para o mesmo.|
|    2005            | Não foi possível ligar ao contentor porque as permissões de conta estão erradas ou desatualizadas. Verifique o seu acesso.|
|    2006            | Não foi possível carregar dados para a conta porque a conta ou a partilha está desativada.|
|    2007            | Não foi possível ligar ao contentor porque as permissões de conta estão erradas ou desatualizadas. Verifique o seu acesso.|
|    2008            | Não foi possível adicionar novos dados porque o contentor está cheio. Verifique as especificações do Azure para tamanhos de contentor suportados com base no tipo. Por exemplo, o Azure Files só suporta um tamanho de ficheiro máximo de 5 TB.|
|    2009            | Não foi possível carregar os dados uma vez que o contentor associado a partilha não existe.|    
|    2997            | Ocorreu um erro inesperado. Este é um erro transitório que será resolvido de forma automática.|
|    2998            | Ocorreu um erro inesperado. O erro pode ser resolvido de forma automática mas se persistir durante mais de 24 horas, contacte o Suporte da Microsoft.|
|    16000           | Não foi possível atualizar este ficheiro.|
|    16001           | Não foi possível atualizar este ficheiro porque já existe no sistema local.|
|    16002           |Não foi possível atualizar este ficheiro porque não está totalmente carregado.|

