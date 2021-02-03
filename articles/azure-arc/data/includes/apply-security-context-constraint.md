---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 6c8dbeea83cba306cfb788cf447236088045ffc9
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99494022"
---
Esta secção explica como aplicar uma restrição de contexto de segurança (SCC). Para o lançamento de pré-visualização, estes relaxam os constrangimentos de segurança. 

1. Descarregue a restrição de contexto de segurança personalizada (SCC). Utilize um dos seguintes: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - [Não processado](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml)
   - `curl`
   
      O seguinte comando descarrega arc-data-scc.yaml:

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

   > [!NOTE]
   > RedHat OpenShift 4.5 ou superior, altera a forma de aplicar o SCC na conta de serviço.
   > Use o mesmo espaço de nome aqui e no `azdata arc dc create` comando abaixo. O exemplo `arc` é. 
   > 
   > Se estiver a utilizar o RedHat OpenShift 4.5 ou superior, corra: 
   >
   >```console
   >oc create rolebinding arc-data-rbac --clusterrole=system:openshift:scc:arc-data-scc --serviceaccount=arc:default
   >```
