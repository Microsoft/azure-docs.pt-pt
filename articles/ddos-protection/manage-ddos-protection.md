---
title: Gerir o Padrão de Proteção Azure DDoS utilizando o portal Azure
description: Aprenda a usar o Azure DDoS Protection Standard para mitigar um ataque.
services: ddos-protection
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: ddos-protection
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: 358fccec56f81ee41acc2898751b8848beaeb168
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905448"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard"></a>Quickstart: Criar e configurar norma de proteção Azure DDoS

Começa com o Azure DDoS Protection Standard utilizando o portal Azure. 

Um plano de proteção DDoS define um conjunto de redes virtuais que têm o padrão de proteção DDoS ativado, através de subscrições. Pode configurar um plano de proteção DDoS para a sua organização e ligar redes virtuais de várias subscrições ao mesmo plano. 

Neste arranque rápido, irá criar um plano de proteção DDoS e ligá-lo a uma rede virtual. 

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Inicie sessão no portal do Azure em https://portal.azure.com. Certifique-se de que a sua conta é atribuída à [função de contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) de rede ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que é atribuída às ações apropriadas enumeradas no guia de como fazer [permissões.](manage-permissions.md)

## <a name="create-a-ddos-protection-plan"></a>Criar um plano de proteção DDoS

1. **Selecione Criar um recurso** no canto superior esquerdo do portal Azure.
2. Pesse o termo *DDoS* . Quando **o plano de proteção DDoS** aparecer nos resultados da pesquisa, selecione-o.
3. Selecione **Criar** .
4. Introduza ou selecione os seguintes valores e, em seguida, **selecione Criar** :

    |Definição        |Valor                                              |
    |---------      |---------                                          |
    |Nome           | Insira _o MyDdosProtectionPlan_ .                     |
    |Subscrição   | Selecione a sua subscrição.                         |
    |Grupo de recursos | Selecione **Criar novo** e insira _o MyResourceGroup_ .|
    |Localização       | Entre _no Leste dos EUA._                                  |

## <a name="enable-ddos-protection-for-a-virtual-network"></a>Ativar a proteção DDoS para uma rede virtual

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>Ativar a proteção DDoS para uma nova rede virtual

1. **Selecione Criar um recurso** no canto superior esquerdo do portal Azure.
2. Selecione **Redes** e, em seguida, selecione **Rede virtual** .
3. Insira ou selecione os seguintes valores, aceite os predefinidos restantes e, em seguida, **selecione Criar** :

    | Definição         | Valor                                           |
    | ---------       | ---------                                       |
    | Nome            | Insira _MyVnet_ .                                 |
    | Subscrição    | Selecione a sua subscrição.                                    |
    | Grupo de recursos  | Selecione **Utilizar a utilização existente** e, em seguida, selecione **MyResourceGroup** |
    | Localização        | Entre no _Leste dos EUA_                                                    |
    | Padrão de Proteção DDos | Selecione **Ativar** . O plano que seleciona pode ser na mesma, ou subscrição diferente da rede virtual, mas ambas as subscrições devem estar associadas ao mesmo inquilino do Azure Ative Directory.|

Não é possível mover uma rede virtual para outro grupo de recursos ou subscrição quando o DDoS Standard estiver ativado para a rede virtual. Se precisar de mover uma rede virtual com o DDoS Standard ativado, desative primeiro a DDoS Standard, mova a rede virtual e, em seguida, ative a norma DDoS. Após a mudança, os limiares de política afinados automaticamente para todos os endereços IP públicos protegidos na rede virtual são reiniciados.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>Ativar a proteção DDoS para uma rede virtual existente

1. Crie um plano de proteção DDoS preenchendo as etapas no [Criar um plano de proteção DDoS,](#create-a-ddos-protection-plan)se não tiver um plano de proteção DDoS existente.
2. **Selecione Criar um recurso** no canto superior esquerdo do portal Azure.
3. Insira o nome da rede virtual que pretende ativar a Norma de Proteção DDoS para os **recursos de pesquisa, serviços e caixa** de docs no topo do portal. Quando o nome da rede virtual aparecer nos resultados da pesquisa, selecione-o.
4. Selecione **a proteção DDoS,** em **DEFINIÇÕES** .
5. Selecione **Standard** . No âmbito **do plano de proteção DDoS,** selecione um plano de proteção DDoS existente ou o plano que criou no passo 1 e, em seguida, selecione **Save** . O plano que seleciona pode ser na mesma, ou subscrição diferente da rede virtual, mas ambas as subscrições devem estar associadas ao mesmo inquilino do Azure Ative Directory.

## <a name="validate-and-test"></a>Validar e testar

Primeiro, verifique os detalhes do seu plano de proteção DDoS:

1. Selecione **Todos os serviços** em cima, à esquerda do portal.
2. Introduza *o DDoS* na caixa **do filtro.** Quando **os planos de proteção DDoS** aparecerem nos resultados, selecione-os.
3. Selecione o seu plano de proteção DDoS da lista.

A rede virtual _MyVnet_ deve ser listada. 

Teste através de simulações

## <a name="clean-up-resources"></a>Limpar os recursos

Podes ficar com os teus recursos para o próximo tutorial. Se já não for necessário, elimine o grupo de recursos _MyResourceGroup._ Quando elimina o grupo de recursos, também elimina o plano de proteção DDoS e todos os seus recursos relacionados. Se não pretender utilizar este plano de proteção DDoS, deve remover recursos para evitar cargas desnecessárias.

   >[!WARNING]
   >Esta ação é irreversível.

1. No portal Azure, procure e selecione **grupos de Recursos,** ou selecione **grupos** de Recursos a partir do menu do portal Azure.

2. Filtre ou desloque para baixo para encontrar o grupo de recursos _MyResourceGroup._

3. Selecione o grupo de recursos e, em seguida, **selecione Eliminar grupo de recursos** .

4. Digite o nome do grupo de recursos para verificar e, em seguida, **selecione Delete** .

Para desativar a proteção DDoS para uma rede virtual: 

1. Introduza o nome da rede virtual para desativar o padrão de proteção DDoS na caixa de **recursos, serviços e docs de pesquisa** no topo do portal. Quando o nome da rede virtual aparecer nos resultados da pesquisa, selecione-o.
2. Selecione **de acordo com a norma de proteção DDoS,** selecione **Desativar** .

Se pretender eliminar um plano de proteção DDoS, tem primeiro de dissociar todas as redes virtuais do mesmo. 

## <a name="next-steps"></a>Próximas etapas

Para aprender a visualizar e configurar a telemetria para o seu plano de proteção DDoS, continue para os tutoriais.

> [!div class="nextstepaction"]
> [Ver e configurar telemetria de proteção DDoS](telemetry-monitoring-alerting.md)
