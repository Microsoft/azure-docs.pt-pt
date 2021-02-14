---
title: Tutorial - Eliminar uma nuvem privada Azure VMware Solution
description: Saiba como eliminar uma nuvem privada Azure VMware Solution que já não necessita.
ms.topic: tutorial
ms.date: 02/09/2021
ms.openlocfilehash: b11b8f902691db4bd71fd3f52aaa67d46efea643
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101711"
---
# <a name="tutorial-delete-an-azure-vmware-solution-private-cloud"></a>Tutorial: Eliminar uma nuvem privada Azure VMware Solution

Se tiver uma nuvem privada Azure VMware Solution de que já não precisa, pode eliminá-la. A nuvem privada inclui um domínio de rede isolado, um ou mais aglomerados de vSphere a provisionados em anfitriões de servidores dedicados e várias máquinas virtuais (VMs). Quando elimina uma nuvem privada, todos os VMs, os seus dados e clusters são eliminados. Os anfitriões dedicados são limpos e devolvidos à piscina gratuita. O domínio de rede previsto para o cliente também é eliminado.  

> [!CAUTION]
> Apagar a nuvem privada é uma operação irreversível. Uma vez eliminada a nuvem privada, os dados não podem ser recuperados, uma vez que encerra todas as cargas de trabalho e componentes em execução e destrói todos os dados e configurações de nuvem privada, incluindo endereços IP públicos.

## <a name="prerequisites"></a>Pré-requisitos

Se necessitar dos VM e dos seus dados mais tarde, certifique-se de fazer o armazenamento dos dados antes de eliminar a nuvem privada.  Não há como recuperar os VMs e os seus dados.


## <a name="delete-the-private-cloud"></a>Apagar a nuvem privada

1. Aceda à consola Azure VMware Solutions no [portal Azure](https://portal.azure.com).

2. Selecione a nuvem privada a eliminar.
 
3. Insira o nome da nuvem privada e selecione **Sim**. 

>[!NOTE]
>O processo de eliminação leva algumas horas para ser concluído.  
