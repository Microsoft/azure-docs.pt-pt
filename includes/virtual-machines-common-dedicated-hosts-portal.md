---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/09/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 77eb54f5d7194f3006ce463fc5f905165bdfc659
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75833942"
---
## <a name="limitations"></a>Limitações

- Atualmente, não há suporte para conjuntos de dimensionamento de máquinas virtuais em hosts dedicados.
- A versão inicial dá suporte à seguinte série de VMs: DSv3 e ESv3. 

## <a name="create-a-host-group"></a>Criar um grupo de anfitriões

Um **grupo de hosts** é um novo recurso que representa uma coleção de hosts dedicados. Você cria um grupo de hosts em uma região e uma zona de disponibilidade e adiciona hosts a ele. Ao planejar a alta disponibilidade, há opções adicionais. Você pode usar uma ou ambas as opções a seguir com seus hosts dedicados: 
- Alcance entre várias zonas de disponibilidade. Nesse caso, é necessário ter um grupo de hosts em cada uma das zonas que você deseja usar.
- Alcance entre vários domínios de falha que são mapeados para racks físicos. 
 
Em ambos os casos, você precisa fornecer a contagem de domínios de falha para seu grupo de hosts. Se você não quiser abranger domínios de falha em seu grupo, use uma contagem de domínio de falha de 1. 

Você também pode optar por usar zonas de disponibilidade e domínios de falha. 

Neste exemplo, criaremos um grupo de hosts usando 1 zona de disponibilidade e 2 domínios de falha. 


1. Abra o [portal](https://portal.azure.com)do Azure.
1. Selecione **criar um recurso** no canto superior esquerdo.
1. Procure por **grupo de hosts** e, em seguida, selecione **grupos de hosts** nos resultados.

    ![Resultado da pesquisa de grupos de hosts.](./media/virtual-machines-common-dedicated-hosts-portal/host-group.png)
1. Na página **grupos de hosts** , selecione **criar**.
1. Selecione a assinatura que você deseja usar e, em seguida, selecione **criar nova** para criar um novo grupo de recursos.
1. Digite *myDedicatedHostsRG* como o **nome** e, em seguida, selecione **OK**.
1. Para **nome do grupo de hosts**, digite *myhost*Group.
1. Para **local**, selecione **leste dos EUA**.
1. Para **zona de disponibilidade**, selecione **1**.
1. Para **contagem de domínios de falha**, selecione **2**.
1. Selecione **revisar + criar** e aguarde a validação.

    ![Configurações do grupo de hosts](./media/virtual-machines-common-dedicated-hosts-portal/host-group-settings.png)
1. Depois de ver a mensagem **validação aprovada** , selecione **criar** para criar o grupo de hosts.

Só deve levar alguns minutos para criar o grupo de hosts.

## <a name="create-a-dedicated-host"></a>Criar um host dedicado

Agora, crie um host dedicado no grupo de hosts. Além de um nome para o host, você deve fornecer a SKU para o host. O SKU do host captura a série de VMs com suporte, bem como a geração de hardware para seu host dedicado. Há suporte para os seguintes valores de SKU do host: DSv3_Type1 e ESv3_Type1.

Para obter mais informações sobre os preços e as SKUs do host, consulte [preços do host dedicado do Azure](https://aka.ms/ADHPricing).

Se você definir uma contagem de domínios de falha para seu grupo de hosts, será solicitado que você especifique o domínio de falha para o host.  

1. Selecione **criar um recurso** no canto superior esquerdo.
1. Pesquise **host dedicado** e, em seguida, selecione **hosts dedicados** nos resultados.

    ![Resultado da pesquisa de grupos de hosts.](./media/virtual-machines-common-dedicated-hosts-portal/host.png)
1. Na página **hosts dedicados** , selecione **criar**.
1. Selecione a assinatura que você deseja usar.
1. Selecione *myDedicatedHostsRG* como o **grupo de recursos**.
1. Em **detalhes da instância**, digite *myhost* para o **nome** e selecione *leste dos EUA* para o local.
1. Em **perfil de hardware**, selecione *família de Es3 padrão-tipo 1* para a família de **tamanho**, selecione *myHostGrup* para o **grupo de hosts** e, em seguida, selecione *1* para o **domínio de falha**. Deixe os padrões para o restante dos campos.
1. Quando terminar, selecione **revisar + criar** e aguarde a validação.

    ![Configurações do host](./media/virtual-machines-common-dedicated-hosts-portal/host-settings.png)
1. Depois de ver a mensagem **validação aprovada** , selecione **criar** para criar o host.


