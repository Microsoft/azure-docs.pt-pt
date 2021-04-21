---
title: Trocar ou trocar implementações em Azure Cloud Services (suporte alargado)
description: Saiba como trocar ou alternar entre implementações nos Serviços Azure Cloud (suporte alargado).
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: f5e01075ffb460c7ddd70b40a6b19f7ea70dd776
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748839"
---
# <a name="swap-or-switch-deployments-in-azure-cloud-services-extended-support"></a>Trocar ou trocar implementações em Azure Cloud Services (suporte alargado)

Pode trocar entre duas implementações independentes de serviços em nuvem em Azure Cloud Services (suporte alargado). Ao contrário do que acontece no Azure Cloud Services (clássico), o modelo Azure Resource Manager em Azure Cloud Services (suporte alargado) não utiliza slots de implementação. Nos Azure Cloud Services (suporte alargado), quando implementa um novo lançamento de um serviço na nuvem, pode tornar o serviço na nuvem "permutável" com um serviço de nuvem existente nos Serviços Azure Cloud (suporte alargado).

Depois de trocar as implementações, pode encenar e testar a sua nova versão utilizando a nova implementação do serviço de nuvem. Com efeito, a troca promove um novo serviço na nuvem que é encenado para o lançamento da produção.

> [!NOTE]
> Não é possível trocar entre uma implantação dos Azure Cloud Services (clássico) e uma implantação dos Azure Cloud Services (suporte alargado).

Você deve fazer um serviço de nuvem permutável com outro serviço de nuvem quando você implementar o segundo de um par de serviços na nuvem.

Pode trocar as implementações utilizando um modelo de Gestor de Recursos Azure (modelo ARM), o portal Azure ou a API REST.

## <a name="arm-template"></a>Modelo ARM

Se utilizar um método de implementação do modelo ARM, para tornar os serviços de nuvem permutáveis, coloque a `SwappableCloudService` propriedade no objeto no `networkProfile` `cloudServices` iD do serviço de nuvem emparelhado:

```json
"networkProfile": {
 "SwappableCloudService": {
              "id": "[concat(variables('swappableResourcePrefix'), 'Microsoft.Compute/cloudServices/', parameters('cloudServicesToBeSwappedWith'))]"
            },
        }
```

## <a name="azure-portal"></a>Portal do Azure

Para trocar uma implantação no portal Azure:

1. No menu do portal, selecione **Cloud Services (suporte alargado)** ou **Dashboard**.
1. Selecione o serviço de nuvem que pretende atualizar.
1. Em **Visão Geral** para o serviço na nuvem, selecione **Swap**:

   :::image type="content" source="media/swap-cloud-service-portal-swap.png" alt-text="Screenshot que mostra o separador swap para o serviço na nuvem.":::

1. No painel de confirmação de permuta, verifique as informações de implementação e, em seguida, selecione **OK** para trocar as implementações:

   :::image type="content" source="media/swap-cloud-service-portal-confirm.png" alt-text="Screenshot que mostra confirmar a informação de troca de implementação.":::

As implementações trocam rapidamente porque a única coisa que muda é o endereço IP virtual para o serviço de cloud que está implantado.

Para poupar custos de cálculo, pode eliminar um dos serviços na nuvem (designado como um ambiente de preparação para a implementação da sua aplicação) depois de verificar se o seu serviço de nuvem trocada funciona como esperado.

## <a name="rest-api"></a>API REST

Para utilizar a API REST para trocar para uma nova implementação de serviços em nuvem em Azure Cloud Services (suporte alargado), utilize o seguinte comando e configuração JSON:

```http
POST https://management.azure.com/subscriptions/subId/providers/Microsoft.Network/locations/region/setLoadBalancerFrontendPublicIpAddresses?api-version=2020-11-01
```

```json
{
  "frontendIPConfigurations": [
    {
    "id": "#LBFE1#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP2#"
    }
      }
    },
   {
    "id": "#LBFE2#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP1#"
     }
       }
    }
  ]
 }
}
```

## <a name="common-questions-about-swapping-deployments"></a>Questões comuns sobre a troca de implementações

Reveja estas respostas a perguntas comuns sobre swaps de implementação em Azure Cloud Services (suporte alargado).

### <a name="what-are-the-prerequisites-for-swapping-to-a-new-cloud-services-deployment"></a>Quais são os pré-requisitos para a troca para uma nova implantação de serviços na nuvem?

Tem de cumprir dois pré-requisitos fundamentais para uma troca de implementação bem sucedida nos Serviços Azure Cloud (suporte alargado):

* Se pretender utilizar um endereço IP estático ou reservado para um dos serviços de cloud permutáveis, o outro serviço de cloud também deve utilizar um endereço IP reservado. Caso contrário, a troca falha.
* Todos os casos dos seus papéis devem estar a correr para que a troca tenha sucesso. Para verificar o estado das suas instâncias, no portal Azure, vá ao **Overview** para o serviço de nuvem recém-implantado ou utilize o `Get-AzRole` comando no Windows PowerShell.

As atualizações do SISTEMA do Hóspede e as operações de cura do serviço podem causar a falha de uma troca de implementação. Para obter mais informações, consulte [as implementações do serviço de cloud Troubleshoot](../cloud-services/cloud-services-troubleshoot-deployment-problems.md).

### <a name="can-i-make-a-vip-swap-in-parallel-with-another-mutating-operation"></a>Posso fazer uma troca VIP em paralelo com outra operação de mutação?

N.º Uma troca VIP é uma alteração apenas em rede que deve terminar antes de qualquer outra operação de computação ser iniciada num serviço de cloud. Iniciar uma atualização, excluir ou fazer uma operação de autoescala para um serviço de nuvem enquanto uma troca VIP está em andamento ou desencadear uma troca VIP enquanto outra operação de computação está em andamento pode colocar o serviço de nuvem num estado de erro irrecuperável.

### <a name="does-a-swap-incur-downtime-for-my-application-and-how-should-i-handle-it"></a>Uma troca incorre em tempo de inatividade para a minha candidatura, e como devo lidar com isso?

Uma troca de serviço em nuvem geralmente é rápida porque é apenas uma alteração de configuração no equilibrador de carga Azure. Em alguns casos, a troca pode demorar 10 ou mais segundos e resultar em falhas de ligação transitórias. Para limitar o efeito da permuta nos utilizadores, considere implementar a lógica de relagem do cliente.

## <a name="next-steps"></a>Passos seguintes 

* Rever [pré-requisitos](deploy-prerequisite.md) de implementação para serviços em nuvem Azure (suporte alargado).
* Reveja [perguntas frequentes](faq.md) para Azure Cloud Services (suporte alargado).
* Implementar um serviço de cloud Services Azure (suporte alargado) utilizando uma destas opções:
  * [Portal do Azure](deploy-portal.md)
  * [PowerShell](deploy-powershell.md)
  * [Modelo ARM](deploy-template.md)
  * [Visual Studio](deploy-visual-studio.md)
