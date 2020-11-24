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
ms.openlocfilehash: 137aca7c6c857ee6e833c359b710e1c1848d15ed
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95560690"
---
## <a name="deleting-personal-information"></a>Apagar informações pessoais

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

As informações pessoais são relevantes para o serviço de importação/exportação (através do portal e da API) durante as operações de importação e exportação. Os dados utilizados durante estes processos incluem:

- Nome do contacto
- Número de telefone
- E-mail
- Morada
- City
- Código postal
- Estado
- País/Província/Região
- ID da unidade
- Número de conta da operadora
- Número de controlo de envio

Quando um trabalho de importação/exportação é criado, os utilizadores fornecem informações de contacto e um endereço de envio. As informações pessoais são armazenadas em até dois locais diferentes: no trabalho e opcionalmente nas definições do portal. As informações pessoais só são armazenadas nas definições do portal se verificar a caixa de verificação etiquetada, **guardar a transportadora e o endereço de devolução como padrão** durante a secção de *informações* de envio de retorno do processo de exportação.

As informações pessoais de contacto podem ser eliminadas das seguintes formas:

- Os dados guardados com o trabalho são apagados com o trabalho. Os utilizadores podem eliminar os postos de trabalho manualmente e os trabalhos concluídos são automaticamente eliminados após 90 dias. Pode eliminar manualmente os trabalhos através da API REST ou do portal Azure. Para eliminar o trabalho no portal Azure, vá ao seu trabalho de importação/exportação e clique em *Eliminar* a partir da barra de comando. Para obter informações sobre como eliminar um trabalho de importação/exportação através da REST API, consulte [a Eliminação de um trabalho de importação/exportação](/previous-versions/azure/storage/common/storage-import-export-cancelling-and-deleting-jobs).

- As informações de contacto guardadas nas definições do portal podem ser removidas eliminando as definições do portal. Pode eliminar as definições do portal seguindo estes passos:
  - Inicie sessão no [portal do Azure](https://portal.azure.com).
  - Clique no ícone de *definições* ![ Azure Definições de Definições](media/storage-import-export-delete-personal-info/azure-settings-icon.png)
  - Clique *em Exportar todas as definições* (para guardar as definições atuais para um `.json` ficheiro).
  - Clique *em Eliminar todas as definições e dashboards privados* para eliminar todas as definições, incluindo informações de contacto guardadas.

Para mais informações, reveja a política de privacidade da Microsoft no [Trust Center](https://www.microsoft.com/trustcenter)