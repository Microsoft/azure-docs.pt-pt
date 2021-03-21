---
title: Conecte-se a uma conta de armazenamento segura do seu espaço de trabalho Azure Synapse
description: Este artigo irá ensiná-lo a conectar-se a uma conta de armazenamento segura a partir do seu espaço de trabalho Azure Synapse
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 02/10/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5d43d6f56b48a34fa34baf727508ad8f1c151aa7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674317"
---
# <a name="connect-to-a-secure-azure-storage-account-from-your-synapse-workspace"></a>Conecte-se a uma conta de armazenamento Azure segura a partir do seu espaço de trabalho Synapse

Este artigo irá ensiná-lo a conectar-se a uma conta de armazenamento Azure segura do seu espaço de trabalho Azure Synapse. Pode ligar uma conta de armazenamento Azure ao seu espaço de trabalho Synapse quando criar o seu espaço de trabalho. Pode ligar mais contas de armazenamento depois de criar o seu espaço de trabalho.


## <a name="secured-azure-storage-accounts"></a>Contas de armazenamento Azure seguras
O armazenamento Azure fornece um modelo de segurança em camadas que lhe permite proteger e controlar o acesso às suas contas de armazenamento. Pode configurar as regras de firewall IP para conceder tráfego a partir de intervalos de endereços IP públicos selecionados acesso à sua conta de armazenamento. Também pode configurar regras de rede para conceder o acesso ao tráfego de redes virtuais selecionadas à sua conta de armazenamento. Pode combinar regras de firewall IP que permitem o acesso a partir de intervalos de endereços IP selecionados e regras de rede que concedem acesso a redes virtuais selecionadas na mesma conta de armazenamento. Estas regras aplicam-se ao ponto final público de uma conta de armazenamento. Não precisa de nenhuma regra de acesso para permitir o tráfego de pontos finais privados geridos criados no seu espaço de trabalho para uma conta de armazenamento. As regras de firewall de armazenamento podem ser aplicadas às contas de armazenamento existentes ou a novas contas de armazenamento quando as cria. Pode saber mais sobre a segurança da sua conta de armazenamento [aqui.](../../storage/common/storage-network-security.md)

## <a name="synapse-workspaces-and-virtual-networks"></a>Espaços de trabalho da Sinapse e redes virtuais
Quando criar um espaço de trabalho synapse, pode optar por permitir que uma rede virtual gerida seja associada a ela. Se não ativar a rede virtual gerida para o seu espaço de trabalho quando o cria, o seu espaço de trabalho está numa rede virtual partilhada juntamente com outros espaços de trabalho da Synapse que não têm uma rede virtual gerida associada a ela. Se ativou a rede virtual gerida quando criou o espaço de trabalho, então o seu espaço de trabalho está associado a uma rede virtual dedicada gerida pela Azure Synapse. Estas redes virtuais não são criadas na subscrição do seu cliente. Portanto, não será possível conceder ao tráfego destas redes virtuais acesso à sua conta de armazenamento segura usando as regras de rede acima descritas.  

## <a name="access-a-secured-storage-account"></a>Aceda a uma conta de armazenamento segura
A Sinapse opera a partir de redes que não podem ser incluídas nas suas regras de rede. O seguinte precisa ser feito para permitir o acesso do seu espaço de trabalho à sua conta de armazenamento segura.

* Crie um espaço de trabalho Azure Synapse com uma rede virtual gerida associada a ele e crie pontos finais privados geridos a partir dele para a conta de armazenamento seguro
* Conceda ao seu espaço de trabalho Azure Synapse acesso à sua conta de armazenamento seguro como um serviço Azure de confiança. Como um serviço de confiança, o Azure Synapse utilizará então uma autenticação forte para se ligar de forma segura à sua conta de armazenamento.   

### <a name="create-a-synapse-workspace-with-a-managed-virtual-network-and-create-managed-private-endpoints-to-your-storage-account"></a>Crie um espaço de trabalho synapse com uma rede virtual gerida e crie pontos finais privados geridos para a sua conta de armazenamento
Pode seguir [estes passos](./synapse-workspace-managed-vnet.md) para criar um espaço de trabalho da Synapse que tenha uma rede virtual gerida associada a ele. Uma vez criado o espaço de trabalho com uma rede virtual gerida associada, pode criar um ponto final privado gerido para a sua conta de armazenamento seguro, seguindo os passos listados [aqui.](./how-to-create-managed-private-endpoints.md) 

### <a name="grant-your-azure-synapse-workspace-access-to-your-secure-storage-account-as-a-trusted-azure-service"></a>Conceda ao seu espaço de trabalho Azure Synapse acesso à sua conta de armazenamento seguro como um serviço Azure de confiança
As capacidades analíticas, tais como piscina de SQL dedicada e piscina Serverless SQL utilizam infraestruturas multi-inquilinos que não são implantadas na rede virtual gerida. Para que o tráfego destas capacidades aceda à conta de armazenamento segura, deve configurar o acesso à sua conta de armazenamento com base na identidade gerida do sistema atribuído pelo espaço de trabalho, seguindo os passos abaixo.

No portal Azure, navegue para a sua conta de armazenamento segura. Selecione **rede a** partir do painel de navegação esquerdo. Na secção **de instâncias de Recursos,** selecione *Microsoft.Synapse/workspaces* como o **tipo de recurso** e insira o nome do seu espaço de trabalho para o nome **exemplo**. Selecione **Guardar**.

![Configuração da rede de conta de armazenamento.](./media/connect-to-a-secure-storage-account/secured-storage-access.png)

Deverá agora aceder à sua conta de armazenamento segura a partir do espaço de trabalho.


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a rede virtual do espaço de trabalho gerido.](./synapse-workspace-managed-vnet.md)

Saiba mais sobre [os pontos finais privados geridos.](./synapse-workspace-managed-private-endpoints.md)