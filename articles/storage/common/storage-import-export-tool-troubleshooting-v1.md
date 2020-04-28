---
title: Resolução de problemas da Ferramenta de Importação/Exportação Azure [ Microsoft Docs
description: Conheça algumas das questões comuns vistas ao utilizar a Ferramenta de Importação/Exportação Azure e como lidar com elas.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 4eeeb538bcd39eed40a92dd45e7ba7bed25558e2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75978403"
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Resolver problemas da Ferramenta de Importação /Exportação do Azure
A Ferramenta de Importação/Exportação do Microsoft Azure devolve mensagens de erro se se depara com problemas. Este tópico enumera algumas questões comuns que os utilizadores podem encontrar.  

## <a name="a-copy-session-fails-what-i-should-do"></a>Uma sessão de cópiafalha, o que devo fazer?  
 Quando uma sessão de cópia falha, existem duas opções:  

 Se o erro for retível, por exemplo, se a parte da rede estiver offline por um curto período de tempo e agora estiver novamente on-line, pode retomar a sessão de cópia. Se o erro não for retível, por exemplo, se tiver especificado o diretório de ficheiros de origem errado nos parâmetros da linha de comando, terá de abortar a sessão de cópia. Consulte [a Preparação de Discos Rígidos para um Trabalho de Importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md) para obter mais informações sobre o reinício e aborto das sessões de cópia.  

## <a name="i-cant-resume-or-abort-a-copy-session"></a>Não posso retomar ou abortar uma sessão de cópias.  
 Se a sessão de cópia for a primeira sessão de cópia para uma unidade, então a mensagem de erro deve indicar: "A primeira sessão de cópia não pode ser retomada ou abortada." Neste caso, pode eliminar o ficheiro do diário antigo e reexecutar o comando.  

 Se uma sessão de cópia não for a primeira para uma unidade, pode sempre ser retomada ou abortada.  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Perdi o ficheiro do diário, ainda posso criar o emprego?  
 O ficheiro do diário para uma unidade contém a informação completa de copiar dados para esta unidade, e é necessário adicionar mais ficheiros à unidade e será usado para criar um emprego de importação. Se o ficheiro do diário estiver perdido, terá de refazer todas as sessões de cópia para a unidade.  

## <a name="next-steps"></a>Passos seguintes

* [Criação da ferramenta de importação/exportação azul](../storage-import-export-tool-setup-v1.md)   
* [Preparar as unidades de disco rígido para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Revisão do estado da tarefa com ficheiros de registo de cópia](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparação de uma tarefa de importação](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Reparação de uma tarefa de exportação](../storage-import-export-tool-repairing-an-export-job-v1.md)
