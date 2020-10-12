---
title: Implementar o Azure VMware Solution
description: Passos para implementar a Solução VMware Azure utilizando o portal Azure.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 4d0881721cd8c13d1b6c9fb3a29e4cdb6d6a753f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578325"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. **Selecione Criar um novo recurso.** No **tipo de** caixa de texto `Azure VMware Solution` Marketplace, e selecione **Azure VMware Solution** da lista. Na janela **Azure VMware Solution,** selecione **Criar**.

1. No separador **Básicos, insira** valores para os campos. A tabela a seguir lista as propriedades dos campos.

   | Campo   | Valor  |
   | ---| --- |
   | **Subscrição** | A subscrição que pretende utilizar para a implantação.|
   | **Grupo de recursos** | O grupo de recursos para os seus recursos em nuvem privada. |
   | **Localização** | Selecione uma localização, como **a leste de nós.**|
   | **Nome de recurso** | O nome da sua nuvem privada Azure VMware Solution. |
   | **SKU** | Selecione o seguinte valor SKU: AV36 |
   | **Anfitriões** | O número de anfitriões para adicionar ao aglomerado de nuvens privadas. O valor predefinido é 3, que pode ser aumentado ou reduzido após a implementação.  |
   | **vCenter senha de administração** | Introduza uma senha de administrador de nuvem. |
   | **Senha do gestor NSX-T** | Introduza uma senha de administrador NSX-T. |
   | **Bloco de endereços** | Introduza um bloco de endereços IP para a rede CIDR para a nuvem privada, por exemplo, 10.175.0.0/22. |
   | **Rede Virtual** | Selecione uma Rede Virtual ou crie uma nova para a nuvem privada Azure VMware Solution.  |

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="No separador Básicos, insira valores para os campos." border="true":::

1. Uma vez terminado, selecione **Review + Create**. No ecrã seguinte, verifique as informações inseridas. Se a informação estiver correta, **selecione Criar**.

   > [!NOTE]
   > Este passo leva cerca de duas horas. 

1. Verifique se a implementação foi bem sucedida. Navegue para o grupo de recursos que criou e selecione a sua nuvem privada.  Verá o estado de **Sucesso** quando a implantação estiver concluída. 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="No separador Básicos, insira valores para os campos." border="true":::