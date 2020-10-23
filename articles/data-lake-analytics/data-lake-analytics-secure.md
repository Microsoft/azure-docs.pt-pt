---
title: Secure Azure Data Lake Analytics para vários utilizadores
description: Saiba como configurar vários utilizadores para executar empregos no Azure Data Lake Analytics.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 05/30/2018
ms.openlocfilehash: 52bdd80dba1d5de150f403199fbd5c14d188a410
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220147"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>Configure o acesso do utilizador à informação de trabalho em Azure Data Lake Analytics 

No Azure Data Lake Analytics, pode utilizar várias contas de utilizadores ou diretores de serviço para executar empregos. 

Para que esses mesmos utilizadores possam ver as informações detalhadas do trabalho, os utilizadores precisam de ser capazes de ler o conteúdo das pastas de trabalho. As pastas de trabalho estão localizadas no `/system/` diretório. 

Se as permissões necessárias não forem configuradas, o utilizador poderá ver um erro: `Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>Configure o acesso dos utilizadores à informação do trabalho

Pode utilizar o **'Add User Wizard'** para configurar os ACLs nas pastas. Para mais informações, consulte [Adicionar um novo utilizador.](data-lake-analytics-manage-use-portal.md#add-a-new-user)

Se precisar de mais controlo granular ou precisar de scriptar as permissões, então prenda as pastas da seguinte forma:

1. Conceder permissões de **execução** (através de um ACL de acesso) na pasta raiz:
   - /
   
2. Conceder permissões de **execução** e **leitura** (através de um ACL de acesso e de um ACL predefinido) nas pastas que contêm as pastas de trabalho. Por exemplo, para um trabalho específico que decorreu no dia 25 de maio de 2018, estas pastas precisam de ser acedidas:
   - /sistema
   - /sistema/serviço de emprego
   - /sistema/serviço de emprego/empregos
   - /sistema/jobservice/jobs/Usql
   - /system/jobservice/jobs/Usql/2018
   - /system/jobservice/jobs/Usql/2018/05
   - /system/jobservice/jobs/Usql/2018/05/25
   - /sistema/jobservice/jobs/Usql/2018/05/25/11
   - /sistema/jobservice/jobs/Usql/2018/05/25/11/01
   - /system/jobservice/jobs/Usql/2018/05/25/11/01/b074bd7a-1448-d879-9d75-f562b101bd3d

## <a name="next-steps"></a>Passos seguintes
[Adicionar um novo utilizador](data-lake-analytics-manage-use-portal.md#add-a-new-user)
