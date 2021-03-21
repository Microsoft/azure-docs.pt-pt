---
title: Resolução de problemas das questões de importação e exportação em Azure Import/Export | Microsoft Docs
description: Aprenda a lidar com questões comuns ao utilizar a Azure Import/Export.
author: v-dalc
services: storage
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 555529b52d586078ba7e1832373ec126ba545c11
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98706686"
---
# <a name="troubleshoot-issues-in-azure-importexport"></a>Problemas de resolução de problemas na Importação/Exportação de Azure
Este artigo descreve como resolver problemas comuns ao importar e exportar dados em Azure Import/Export.

## <a name="a-copy-session-failed-what-i-should-do"></a>Uma sessão de cópia falhou. O que devo fazer?  

Quando uma sessão de cópia falha, tem duas opções:  
* Se o erro puder ser novamente julgado - por exemplo, se a partilha de rede estiver offline por um curto período de tempo e agora estiver novamente online - pode retomar a sessão de cópia.
* Se o erro não puder ser novamente julgado - por exemplo, se especificou o diretório de ficheiros de origem errado nos parâmetros da linha de comando - tem de abortar a sessão de cópia.
 
<!--For information about resuming and aborting copy sessions, see [Preparing Hard Drives for an Import Job](../storage-import-export-tool-preparing-hard-drives-import-v1.md  - Article we removed from TOC. File remains.-->

## <a name="i-cant-resume-or-abort-a-copy-session"></a>Não posso retomar ou abortar uma sessão de cópias.

Se a sessão de cópia for a primeira sessão de cópia para uma unidade, então a mensagem de erro deve indicar: "A primeira sessão de cópia não pode ser retomada ou abortada." Neste caso, pode eliminar o ficheiro do diário antigo e repetir o comando.  

Se uma sessão de cópia não for a primeira para uma unidade, a sessão pode sempre ser retomada ou abortada.  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Perdi o ficheiro do diário. Ainda posso criar o trabalho?

O ficheiro do diário para uma unidade contém informações completas da sessão a partir da cópia de dados. Quando adiciona ficheiros à unidade, o ficheiro do diário é usado para criar um trabalho de importação. Se perder o ficheiro do diário, terá de refazer todas as sessões de cópia para a unidade.

## <a name="next-steps"></a>Passos seguintes

* [Configurar a ferramenta de importação/exportação Azure](storage-import-export-tool-setup-v1.md)
* [Prepare discos rígidos para um trabalho de importação](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)
* [Rever o estado do trabalho com ficheiros de registo de cópias](storage-import-export-tool-reviewing-job-status-v1.md)
* [Reparar um trabalho de importação](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Reparar um trabalho de exportação](storage-import-export-tool-repairing-an-export-job-v1.md)