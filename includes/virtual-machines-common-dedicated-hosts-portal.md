---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/10/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 427117fe47294a1db1fa8d3fa1e46ee1efb91b4d
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129433"
---
## <a name="limitations"></a>Limitações

- Os conjuntos de escala de máquinas virtuais não são atualmente suportados em anfitriões dedicados.
- Os tamanhos e tipos de hardware disponíveis para anfitriões dedicados variam por região. Consulte a [página](https://aka.ms/ADHPricing) de preços do anfitrião para saber mais.

## <a name="create-a-host-group"></a>Criar um grupo de anfitriões

Um **grupo de anfitriões** é um novo recurso que representa uma coleção de anfitriões dedicados. Cria-se um grupo de acolhimento numa região e numa zona de disponibilidade e adiciona-lhe anfitriões. Ao planear uma alta disponibilidade, existem opções adicionais. Pode utilizar uma ou ambas as seguintes opções com os seus anfitriões dedicados: 
- Atravesse várias zonas de disponibilidade. Neste caso, é-lhe exigido que tenha um grupo de acolhimento em cada uma das zonas que deseja utilizar.
- Atravesse vários domínios de falha que são mapeados em prateleiras físicas. 
 
Em qualquer dos casos, é necessário fornecer a contagem de domínio de avaria para o seu grupo anfitrião. Se não quiser abranger domínios de avaria no seu grupo, utilize uma contagem de domínio de falha de 1. 

Também pode decidir utilizar ambas as zonas de disponibilidade e domínios de avaria. 

Neste exemplo, criaremos um grupo de anfitriões utilizando 1 zona de disponibilidade e 2 domínios de falha. 


1. Abra o [portal](https://portal.azure.com)Azure.
1. Selecione **Criar um recurso** no canto superior esquerdo.
1. Procure o **grupo anfitrião** e, em seguida, selecione **Grupos de Anfitriões** a partir dos resultados.
1. Na página grupos de **anfitriões,** selecione **Criar**.
1. Selecione a subscrição que gostaria de utilizar e, em seguida, selecione **Criar novo** para criar um novo grupo de recursos.
1. Digite *o myDedicatedHostsRG* como **nome** e, em seguida, selecione **OK**.
1. Para nome de **grupo anfitrião,** escreva *o meu HostGroup*.
1. Para **localização,** selecione **East US**.
1. Para **A Zona de Disponibilidade,** selecione **1**.
1. Para **a contagem de domínio seletiva,** selecione **2**.
1. Selecione **Review + crie** e, em seguida, aguarde a validação.
1. Assim que vir a **mensagem de validação passada,** selecione **Criar** para criar o grupo anfitrião.

Só deve levar alguns momentos para criar o grupo anfitrião.

## <a name="create-a-dedicated-host"></a>Criar um anfitrião dedicado

Agora crie um anfitrião dedicado no grupo anfitrião. Além de um nome para o anfitrião, é necessário fornecer o SKU para o anfitrião. Host SKU captura a série VM suportada, bem como a geração de hardware para o seu anfitrião dedicado.

Para obter mais informações sobre o anfitrião SKUs e preços, consulte [o preço do Anfitrião Dedicado Azure.](https://aka.ms/ADHPricing)

Se definir uma contagem de domínio de avaria para o seu grupo anfitrião, será-lhe pedido que especifique o domínio de avaria para o seu anfitrião.  

1. Selecione **Criar um recurso** no canto superior esquerdo.
1. Procure um **anfitrião dedicado** e, em seguida, selecione **anfitriões dedicados** a partir dos resultados.
1. Na página **Anfitriões Dedicados,** selecione **Criar**.
1. Selecione a subscrição que deseja utilizar.
1. Selecione *myDedicatedHostsRG* como o **grupo Derecursos**.
1. Em **caso de detalhes**, escreva o meu *Anfitrião* para o **Nome** e selecione *East US* para a localização.
1. No **perfil de Hardware**, selecione *a família Standard Es3 - Tipo 1* para a família **Size,** selecione *myHostGroup* para o **grupo Anfitrião** e, em seguida, selecione *1* para o **domínio Fault**. Deixe os incumprimentos para o resto dos campos.
1. Quando terminar, selecione **Review + crie** e aguarde a validação.
1. Assim que vir a **mensagem de validação passada,** selecione **Criar** para criar o anfitrião.


