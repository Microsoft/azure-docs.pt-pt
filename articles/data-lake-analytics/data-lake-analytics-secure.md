---
title: Secure Azure Data Lake Analytics para vários utilizadores
description: Saiba como configurar vários utilizadores para executar trabalhos no Azure Data Lake Analytics.
ms.service: data-lake-analytics
services: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: 9fbc94259d6fdfb6758204efd6e6f0a346dc58da
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "60813380"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>Configure o acesso dos utilizadores a informações sobre emprego sintetizar em Azure Data Lake Analytics 

No Azure Data Lake Analytics, pode utilizar várias contas de utilizador ou diretores de serviço para executar trabalhos. 

Para que esses mesmos utilizadores vejam as informações detalhadas sobre o trabalho, os utilizadores precisam de ser capazes de ler o conteúdo das pastas de trabalho. As pastas de `/system/` trabalho estão localizadas no diretório. 

Se as permissões necessárias não forem configuradas, o utilizador pode ver um erro:`Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>Configure o acesso do utilizador à informação sobre o trabalho

Pode utilizar o Assistente do **Utilizador adicionar** para configurar os ACLs nas pastas. Para mais informações, consulte [Adicionar um novo utilizador](data-lake-analytics-manage-use-portal.md#add-a-new-user).

Se precisar de mais controlo granular, ou precisar de scripts, então fixe as pastas da seguinte forma:

1. Conceder permissões de **execução** (através de uma ACL de acesso) na pasta raiz:
   - /
   
2. Conceder permissões de **execução** e **leitura** (através de uma ACL de acesso e de uma ACL padrão) nas pastas que contêm as pastas de trabalho. Por exemplo, para um trabalho específico que funcionou no dia 25 de maio de 2018, estas pastas precisam de ser acedidas:
   - /sistema
   - /sistema/serviço de emprego
   - /sistema/serviço de emprego/empregos
   - /sistema/serviço de emprego/emprego/Usql
   - /sistema/jobservice/jobs/Usql/2018
   - /sistema/jobservice/jobs/Usql/2018/05
   - /sistema/jobservice/jobs/Usql/2018/05/25
   - /sistema/jobservice/jobs/Usql/2018/05/25/11
   - /sistema/jobservice/jobs/Usql/2018/05/25/11/01
   - /sistema/jobservice/jobs/Usql/2018/05/25/11/01/b074bd7a-1448-d879-9d75-f562b101bd3d

## <a name="next-steps"></a>Passos seguintes
[Adicionar um novo utilizador](data-lake-analytics-manage-use-portal.md#add-a-new-user)
