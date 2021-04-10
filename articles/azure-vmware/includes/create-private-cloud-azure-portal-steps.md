---
title: Criar uma nuvem privada Azure VMware Solution
description: Passos para criar uma nuvem privada Azure VMware Solution utilizando o portal Azure.
ms.topic: include
ms.date: 04/07/2021
ms.openlocfilehash: 6b4e5631d1a4b6c5bf56b01aba12752595ef63b8
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073329"
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
   | **Bloco de endereços** | Introduza um bloco de endereços IP para a rede CIDR para a nuvem privada, por exemplo, 10.175.0.0/22. |
   | **Rede Virtual** | Selecione uma Rede Virtual ou crie uma nova para a nuvem privada Azure VMware Solution.  |

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="No separador Básicos, insira valores para os campos." border="true":::

1. Uma vez terminado, selecione **Review + Create**. No ecrã seguinte, verifique as informações inseridas. Se a informação estiver correta, **selecione Criar**.

   > [!NOTE]
   > Este passo leva cerca de 3 a 4 horas. A adição de um único nó no cluster existente/mesmo demora entre 30 e 45 minutos.

1. Verifique se a implementação foi bem sucedida. Navegue para o grupo de recursos que criou e selecione a sua nuvem privada.  Verá o estado de **Sucesso** quando a implantação estiver concluída. 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Verifique se a implementação foi bem sucedida." border="true":::
