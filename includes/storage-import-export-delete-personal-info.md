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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184472"
---
## <a name="deleting-personal-information"></a>Apagar informações pessoais

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

As informações pessoais são relevantes para o serviço de importação/exportação (através do portal e da API) durante as operações de importação e exportação. Os dados utilizados durante estes processos incluem:

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

Quando é criado um trabalho de importação/exportação, os utilizadores fornecem informações de contacto e um endereço de envio. As informações pessoais são armazenadas em até dois locais diferentes: no trabalho e opcionalmente nas configurações do portal. As informações pessoais só são armazenadas nas definições do portal se verificar a caixa de verificação rotulada, guardar a transportadora e devolver o **endereço de forma predefinida** durante a secção de *informações* de envio de retorno do processo de exportação.

As informações de contacto pessoais podem ser eliminadas das seguintes formas:

- Os dados guardados com o trabalho são apagados com o trabalho. Os utilizadores podem eliminar os trabalhos manualmente e os trabalhos concluídos são automaticamente eliminados após 90 dias. Pode eliminar manualmente os trabalhos através da API REST ou do portal Azure. Para eliminar o trabalho no portal Azure, vá ao seu trabalho de importação/exportação e clique em *Apagar* a partir da barra de comando. Para obter informações sobre como eliminar um emprego de importação/exportação através da API REST, consulte a eliminação de um emprego de [importação/exportação](../articles/storage/common/storage-import-export-cancelling-and-deleting-jobs.md).

- As informações de contacto guardadas nas definições do portal podem ser removidas apagando as definições do portal. Pode eliminar as definições do portal seguindo estes passos:
  - Inicie sessão no [Portal do Azure](https://portal.azure.com).
  - Clique no ícone ![de definições do ícone *de definições* do Azure](media/storage-import-export-delete-personal-info/azure-settings-icon.png)
  - Clique *em Exportar todas as definições* (para guardar as definições atuais para um `.json` ficheiro).
  - Clique em *Apagar todas as definições e dashboards privados* para eliminar todas as definições, incluindo informações de contacto guardadas.

Para mais informações, reveja a política de privacidade da Microsoft no [Trust Center](https://www.microsoft.com/trustcenter)