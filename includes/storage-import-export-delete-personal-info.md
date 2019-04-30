---
title: incluir ficheiro
description: incluir ficheiro
services: azure-policy
author: craigshoemaker
ms.service: azure-policy
ms.topic: include
ms.date: 05/18/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: e6a0ded137162328fd446b65ddb4a15fa6f1db88
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478790"
---
## <a name="deleting-personal-information"></a>A eliminar informações pessoais

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

Informações pessoais são relevantes para a importação/exportação (através do portal e a API) do serviço durante a importação e exportação de operações. Dados utilizados durante esses processos incluem:

- Nome do contacto
- Número de telefone
- Email
- Morada
- Localidade
- Código postal
- Estado
- País/Província/Região
- ID da unidade
- Número de conta da operadora
- Número de controlo de envio

Quando é criada uma tarefa de importação/exportação, os utilizadores de fornecer um endereço para remessa e informações de contacto. Informações pessoais são armazenadas em até duas localizações diferentes: a tarefa e, opcionalmente, nas definições do portal. Informações pessoais apenas são armazenadas nas definições do portal, se verificar o nome, a caixa de verificação **guardar a transportadora e devolver o endereço como predefinição** durante a *devolver informações sobre o envio* secção do processo de exportação.

Informações de contacto pessoais podem ser eliminadas das seguintes formas:

- Dados guardados com o trabalho são eliminados com a tarefa. Os utilizadores podem eliminar tarefas manualmente e tarefas concluídas são eliminadas automaticamente após 90 dias. Pode eliminar manualmente as tarefas através da API REST ou o portal do Azure. Para eliminar a tarefa no portal do Azure, aceda à sua tarefa de importação/exportação e clique em *eliminar* na barra de comandos. Para obter detalhes sobre como eliminar uma tarefa de importação/exportação através da REST API, consulte [eliminar uma tarefa de importação/exportação](../articles/storage/common/storage-import-export-cancelling-and-deleting-jobs.md).

- Informações de contacto guardadas nas definições do portal podem ser removidas ao eliminar as definições do portal. Pode eliminar as definições do portal, seguindo estes passos:
  - Inicie sessão no [portal do Azure](https://portal.azure.com).
  - Clique nas *configurações* ícone ![ícone de definições do Azure](media/storage-import-export-delete-personal-info/azure-settings-icon.png)
  - Clique em *exportar todas as definições* (para guardar as definições atuais para um `.json` ficheiro).
  - Clique em *eliminar todas as definições e dashboards privados* para eliminar todas as definições, incluindo informações de contacto guardadas.

Para obter mais informações, consulte a política de Microsoft Privacy ao [Centro de fidedignidade](https://www.microsoft.com/trustcenter)