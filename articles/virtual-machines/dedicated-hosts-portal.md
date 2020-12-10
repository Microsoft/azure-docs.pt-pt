---
title: Implementar anfitriões dedicados do Azure usando o portal Azure
description: Implemente VMs e conjuntos de escala para anfitriões dedicados utilizando o portal Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.openlocfilehash: b166363a8c64a4a4c5d34efa55dcaefa09d6df49
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007947"
---
# <a name="deploy-vms-and-scale-sets-to-dedicated-hosts-using-the-portal"></a>Implementar VMs e conjuntos de escala para anfitriões dedicados usando o portal 

Este artigo guia-o através da forma de criar um [anfitrião dedicado](dedicated-hosts.md) a Azure para hospedar as suas máquinas virtuais (VMs). 


## <a name="limitations"></a>Limitações

- Os tamanhos e tipos de hardware disponíveis para anfitriões dedicados variam por região. Consulte a [página de preços do](https://aka.ms/ADHPricing) anfitrião para saber mais.

## <a name="create-a-host-group"></a>Criar um grupo de anfitriões

Um **grupo anfitrião** é um recurso que representa uma coleção de anfitriões dedicados. Você cria um grupo de anfitriões em uma região e uma zona de disponibilidade, e adiciona anfitriões a ele. Ao planear uma elevada disponibilidade, existem opções adicionais. Pode utilizar uma ou ambas as seguintes opções com os seus anfitriões dedicados: 
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
1. Selecione **a colocação automática** para atribuir automaticamente VMs e classificar as instâncias a um anfitrião disponível neste grupo.
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

## <a name="create-a-vm"></a>Criar uma VM

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Na caixa de pesquisa acima da lista de recursos do Azure Marketplace, procure e selecione a imagem que deseja usar, em seguida, escolha **Criar**.
1. No **separador Basics,** nos **detalhes do Projeto,** certifique-se de que a subscrição correta é selecionada e, em seguida, selecione *o myDedicatedHostsRG* como o **grupo de Recursos**. 
1. Em **Detalhes da instância**, escreva *aminhaVM* para o **Nome da máquina virtual** e selecione *E.U.A. Leste* para a **Localização**.
1. Nas **opções disponibilidade** selecione **Zona de disponibilidade,** selecione *1* a partir do drop-down.
1. Para o tamanho, selecione **Alterar o tamanho**. Na lista de tamanhos disponíveis, escolha um da série Esv3, como **o Standard E2s v3**. Pode ser necessário limpar o filtro para ver todos os tamanhos disponíveis.
1. Complete o resto dos campos no separador **Básico,** conforme necessário.
1. Se pretender especificar qual o anfitrião a utilizar para o seu VM, em seguida, no topo da página, selecione o separador **Avançado** e na secção **Anfitrião,** selecione *myHostGroup* para **o grupo Host** e *myHost* para o **Anfitrião**. Caso contrário, o seu VM será automaticamente colocado num hospedeiro com capacidade.
    ![Selecione grupo de anfitriões e anfitrião](./media/dedicated-hosts-portal/advanced.png)
1. Mantenha as restantes predefinições e, em seguida, selecione o botão **Rever + criar** na parte inferior da página.
1. Quando vir a mensagem que a validação passou, **selecione Criar**.

Irá demorar alguns minutos até a VM ser implementada.

## <a name="create-a-scale-set"></a>Criar um conjunto de dimensionamento 

Quando implementar um conjunto de escala, especifique o grupo anfitrião.

1. Procure *conjuntos de escala* e selecione **conjuntos** de escala de máquina virtual da lista.
1. **Selecione Adicionar** para criar um conjunto de escala nova.
1. Complete os campos no separador **Básico como** normalmente faria, mas certifique-se de selecionar um tamanho VM que é da série que escolheu para o seu anfitrião dedicado, como **Standard E2s v3**.
1. No separador **Avançado,** para **espalhar algoritmo** selecione **Max spreading**.
1. No **grupo Host**, selecione o grupo anfitrião a partir do drop-down. Se criou recentemente o grupo, pode levar um minuto para ser adicionado à lista.

## <a name="add-an-existing-vm"></a>Adicione um VM existente 

Pode adicionar um VM de saída a um anfitrião dedicado, mas o VM tem primeiro de ser stop\Deallocated. Antes de mover um VM para um anfitrião dedicado, certifique-se de que a configuração VM é suportada:

- O tamanho VM deve ser do mesmo tamanho da família do hospedeiro dedicado. Por exemplo, se o seu anfitrião dedicado for DSv3, então o tamanho VM pode ser Standard_D4s_v3, mas não pode ser um Standard_A4_v2. 
- O VM precisa de ser localizado na mesma região que o hospedeiro dedicado.
- O VM não pode fazer parte de um grupo de colocação de proximidade. Retire o VM do grupo de colocação de proximidade antes de o mover para um hospedeiro dedicado. Para obter mais informações, consulte [mover um VM de um grupo de colocação de proximidade](./windows/proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)
- O VM não pode estar num conjunto de disponibilidade.
- Se o VM estiver numa zona de disponibilidade, deve ser a mesma zona de disponibilidade que o grupo anfitrião. As definições da zona de disponibilidade para o VM e o grupo anfitrião devem corresponder.

Mover o VM para um hospedeiro dedicado utilizando o [portal](https://portal.azure.com).

1. Abra a página para o VM.
1. **Selecione Stop** para parar\deallocate o VM.
1. Selecione **Configuração** a partir do menu esquerdo.
1. Selecione um grupo anfitrião e um anfitrião nos menus suspensos.
1. Quando terminar, **selecione Guardar** no topo da página.
1. Depois de adicionar o VM ao anfitrião, selecione **a visão geral** do menu esquerdo.
1. No topo da página, selecione **Iniciar** a reiniciar o VM.

## <a name="next-steps"></a>Passos seguintes

- Para mais informações, consulte a visão geral dos [anfitriões dedicados.](dedicated-hosts.md)

- Existe o modelo de amostra, encontrado [aqui,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)que usa ambas as zonas e domínios de avaria para máxima resiliência numa região.

- Também pode implantar um anfitrião dedicado utilizando o [Azure CLI](./linux/dedicated-hosts-cli.md) ou [o PowerShell](./windows/dedicated-hosts-powershell.md).
