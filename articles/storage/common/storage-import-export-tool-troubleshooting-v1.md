---
title: Solução de problemas da ferramenta de importação/exportação do Azure | Microsoft Docs
description: Saiba mais sobre alguns dos problemas comuns vistos ao usar a ferramenta de importação/exportação do Azure e como tratá-los.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 7e463e1cdd340f852af46e39cca0dd9bfce7b8da
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978939"
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Resolver problemas da Ferramenta de Importação /Exportação do Azure
A ferramenta de Importação/Exportação do Microsoft Azure retornará mensagens de erro se ele for executado em problemas. Este tópico lista alguns problemas comuns que os usuários podem encontrar.  
  
## <a name="a-copy-session-fails-what-i-should-do"></a>Uma sessão de cópia falha, o que devo fazer?  
 Quando uma sessão de cópia falha, há duas opções:  
  
 Se o erro for com nova tentativa, por exemplo, se o compartilhamento de rede estava offline por um curto período e agora está online novamente, você pode retomar a sessão de cópia. Se o erro não for repetido, por exemplo, se você especificou o diretório de arquivo de origem errado nos parâmetros de linha de comando, será necessário anular a sessão de cópia. Consulte [preparando discos rígidos para um trabalho de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md) para obter mais informações sobre como retomar e anular sessões de cópia.  
  
## <a name="i-cant-resume-or-abort-a-copy-session"></a>Não consigo retomar ou anular uma sessão de cópia.  
 Se a sessão de cópia for a primeira sessão de cópia para uma unidade, a mensagem de erro deverá indicar: "a primeira sessão de cópia não pode ser retomada ou abortada". Nesse caso, você pode excluir o arquivo de diário antigo e executar o comando novamente.  
  
 Se uma sessão de cópia não for a primeira para uma unidade, ela sempre poderá ser retomada ou abortada.  
  
## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Perdi o arquivo de diário, ainda posso criar o trabalho?  
 O arquivo de diário de uma unidade contém as informações completas de cópia de dados para essa unidade e é necessário para adicionar mais arquivos à unidade e será usado para criar um trabalho de importação. Se o arquivo de diário for perdido, você precisará refazer todas as sessões de cópia da unidade.  
  
## <a name="next-steps"></a>Passos seguintes
 
* [Configurando a ferramenta de importação/exportação do Azure](../storage-import-export-tool-setup-v1.md)   
* [Preparar as unidades de disco rígido para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Revisão do estado da tarefa com ficheiros de registo de cópia](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparação de uma tarefa de importação](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Reparação de uma tarefa de exportação](../storage-import-export-tool-repairing-an-export-job-v1.md)
