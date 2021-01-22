---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 17a8c9580a8e69213c7f34e8ec889f7e46c6d17d
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696074"
---
Esta secção explica como aplicar uma restrição de contexto de segurança (SCC). Para o lançamento de pré-visualização, estes relaxam os constrangimentos de segurança. 

1. Descarregue a restrição de contexto de segurança personalizada (SCC). Utilize um dos seguintes: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - [(Cru)](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml)
   - `curl` O seguinte comando descarrega arc-data-scc.yaml:

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. Criar SCC.

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. Aplique o SCC na conta de serviço.

   > [!NOTE]
   > Use o mesmo espaço de nome aqui e no `azdata arc dc create` comando abaixo. O exemplo `arc` é.

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```