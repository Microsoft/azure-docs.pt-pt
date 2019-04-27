---
title: Resolver problemas da ferramenta de importação/exportação do Azure | Documentos da Microsoft
description: Saiba mais sobre alguns dos problemas comuns vistos ao utilizar a ferramenta de importação/exportação do Azure e como tratá-las.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 9a4e47143515c7f9c21d701809c35d61853d91ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320453"
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Resolver problemas da Ferramenta de Importação /Exportação do Azure
A ferramenta de importação/exportação do Microsoft Azure retorna mensagens de erro, se for executado com problemas. Este tópico apresenta alguns problemas comuns que os utilizadores poderão ocorrer.  
  
## <a name="a-copy-session-fails-what-i-should-do"></a>Uma sessão de cópia falha, o que posso fazer?  
 Quando uma sessão de cópia falha, existem duas opções:  
  
 Se o erro não permanente, por exemplo, se a partilha de rede estava offline durante um curto período e agora é voltar a ficar online, pode retomar a sessão de cópia. Se o erro não repetível, por exemplo, se especificou o diretório de ficheiros de origem errado nos parâmetros de linha de comandos, terá de anular a sessão de cópia. Ver [preparar os discos rígidos para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md) para obter mais informações sobre a retomar e abortar as sessões de cópia.  
  
## <a name="i-cant-resume-or-abort-a-copy-session"></a>Eu não é possível retomar ou abortar uma sessão de cópia.  
 Se a sessão de cópia for a primeira sessão de cópia para uma unidade, a mensagem de erro deve indicar: "A primeira sessão de cópia não pode ser retomada ou abortada." Neste caso, pode eliminar o ficheiro de diário antiga e volte a executar o comando.  
  
 Se tiver uma sessão de cópia não é o primeiro para uma unidade, pode ser retomado sempre ou abortada.  
  
## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Eu perdi o ficheiro de diário, pode ainda criar a tarefa?  
 O ficheiro de diário para uma unidade contém as informações completas de cópia de dados a esta unidade e são necessárias para adicionar mais arquivos na unidade e vai ser utilizado para criar uma tarefa de importação. Se o ficheiro de diário for perdido, terá de Refazer todas as sessões de cópia para a unidade.  
  
## <a name="next-steps"></a>Passos Seguintes
 
* [Configurar a ferramenta de importação/exportação do azure](../storage-import-export-tool-setup-v1.md)   
* [Preparar as unidades de disco rígido para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Revisão do estado da tarefa com ficheiros de registo de cópia](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparação de uma tarefa de importação](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Reparação de uma tarefa de exportação](../storage-import-export-tool-repairing-an-export-job-v1.md)
