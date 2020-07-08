---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/10/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 427117fe47294a1db1fa8d3fa1e46ee1efb91b4d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79129433"
---
## <a name="limitations"></a>Limitações

- Os conjuntos de escala de máquinas virtuais não são suportados atualmente em anfitriões dedicados.
- Os tamanhos e tipos de hardware disponíveis para anfitriões dedicados variam por região. Consulte a [página de preços do](https://aka.ms/ADHPricing) anfitrião para saber mais.

## <a name="create-a-host-group"></a>Criar um grupo de anfitriões

Um **grupo anfitrião** é um novo recurso que representa uma coleção de anfitriões dedicados. Você cria um grupo de anfitriões em uma região e uma zona de disponibilidade, e adiciona anfitriões a ele. Ao planear uma elevada disponibilidade, existem opções adicionais. Pode utilizar uma ou ambas as seguintes opções com os seus anfitriões dedicados: 
- Abrangendo várias zonas de disponibilidade. Neste caso, você é obrigado a ter um grupo de anfitriões em cada uma das zonas que você deseja usar.
- Abrangem vários domínios de avaria que são mapeados para prateleiras físicas. 
 
Em qualquer dos casos, é necessário fornecer a contagem de domínio de avaria para o seu grupo anfitrião. Se não pretender abranger os domínios de avaria no seu grupo, utilize uma contagem de domínio de avaria de 1. 

Também pode decidir utilizar as zonas de disponibilidade e os domínios de avaria. 

Neste exemplo, criaremos um grupo anfitrião usando 1 zona de disponibilidade e 2 domínios de falha. 


1. Abra o [portal](https://portal.azure.com)Azure .
1. **Selecione Criar um recurso** no canto superior esquerdo.
1. Procure no **grupo Host** e, em seguida, selecione **Grupos anfitriões** a partir dos resultados.
1. Na página **Grupos anfitriões,** selecione **Criar**.
1. Selecione a subscrição que pretende utilizar e, em seguida, selecione **Criar novo** para criar um novo grupo de recursos.
1. Digite *o meu DedicatedHostsRG* como **nome** e, em seguida, selecione **OK**.
1. Para **o nome de grupo host**, escreva *myHostGroup*.
1. Para **localização**, selecione **East US**.
1. Para **Zona de Disponibilidade**, selecione **1**.
1. Para **a contagem de domínios de falha**, selecione **2**.
1. Selecione **Rever + criar** e, em seguida, esperar pela validação.
1. Assim que vir a mensagem **de validação passada,** selecione **Criar** para criar o grupo anfitrião.

Deve levar apenas alguns momentos para criar o grupo anfitrião.

## <a name="create-a-dedicated-host"></a>Criar um anfitrião dedicado

Agora crie um anfitrião dedicado no grupo anfitrião. Além de um nome para o anfitrião, você é obrigado a fornecer o SKU para o anfitrião. Host SKU captura a série VM suportada, bem como a geração de hardware para o seu anfitrião dedicado.

Para obter mais informações sobre os SKUs e preços do anfitrião, consulte [os preços do Anfitrião Dedicado Azure](https://aka.ms/ADHPricing).

Se definir uma contagem de domínio de avaria para o seu grupo anfitrião, será solicitado que especifique o domínio de avaria para o seu anfitrião.  

1. **Selecione Criar um recurso** no canto superior esquerdo.
1. Procure por **anfitriões dedicados** e, em seguida, selecione **Anfitriões dedicados** a partir dos resultados.
1. Na página **Anfitriões Dedicados,** selecione **Criar**.
1. Selecione a subscrição que pretende utilizar.
1. Selecione *o myDedicatedHostsRG* como o **grupo de Recursos**.
1. Em **caso de detalhes,** digite *myHost* for the **Name** e selecione *East US* para a localização.
1. No **perfil de Hardware**, selecione *a família Standard Es3 - Tipo 1* para a **família Size**, selecione *myHostGroup* para o **grupo Host** e, em seguida, selecione *1* para o **domínio Fault**. Deixe os defeitos para o resto dos campos.
1. Quando terminar, selecione **Rever + criar** e esperar pela validação.
1. Assim que vir a mensagem **de validação passada,** selecione **Criar** para criar o anfitrião.


