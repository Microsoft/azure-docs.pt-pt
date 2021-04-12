---
title: Troca/Troca entre dois Serviços de Nuvem Azure (Suporte Alargado)
description: Troca/Troca entre dois Serviços de Nuvem Azure (Suporte Alargado)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: 6f96656af9afd9874cc6273a9cea9ed43e8c69cc
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294338"
---
# <a name="swapswitch-between-two-azure-cloud-services-extended-support"></a>Troca/Troca entre dois Serviços de Nuvem Azure (Suporte Alargado)
Com serviços na nuvem (suporte alargado) pode trocar entre duas implementações independentes de serviços em nuvem. Ao contrário dos serviços em nuvem (clássico), o conceito de slots não existe com o modelo Azure Resource Manager. Quando decidir implementar uma nova versão de um serviço de nuvem (suporte alargado), pode torná-lo "permutável" com outro serviço de nuvem existente (suporte alargado) que lhe permita encenar e testar a sua nova versão utilizando esta implementação. Um serviço de nuvem pode ser feito 'permutável' com outro serviço de nuvem apenas no momento de implantar o segundo serviço de nuvem (do par). Ao utilizar o método de implementação baseado no modelo ARM, isso é feito definindo a propriedade SwappableCloudService dentro do perfil de rede do objeto cloud service para o ID do serviço de nuvem emparelhado. 

```
"networkProfile": {
 "SwappableCloudService": {
              "id": "[concat(variables('swappableResourcePrefix'), 'Microsoft.Compute/cloudServices/', parameters('cloudServicesToBeSwappedWith'))]"
            },
```
> [!Note] 
> Não é possível trocar entre um serviço de nuvem (clássico) e um serviço de nuvem (suporte alargado)

Use **Swap** para mudar os URLs através dos quais os dois serviços em nuvem são endereçados, promovendo de facto um novo serviço de nuvem (encenado) para a libertação de produção.
Pode trocar as implementações da página Cloud Services ou do dashboard.

1. No [portal Azure,](https://portal.azure.com)selecione o serviço de cloud que pretende atualizar. Este passo abre a lâmina de placa de serviço de nuvem.
2. Na lâmina, selecione **Swap** 
    :::image type="content" source="media/swap-cloud-service-1.png" alt-text="Image mostra a opção de troca do serviço cloud":::
   
3. O seguinte pedido de confirmação abre
   
   :::image type="content" source="media/swap-cloud-service-2.png" alt-text="Imagem mostra troca do serviço na nuvem":::
   
4. Depois de verificar as informações de implantação, selecione OK para trocar as implementações.
A troca acontece rapidamente porque a única coisa que muda são os endereços IP virtuais (VIPs) para os dois serviços na nuvem.

Para poupar custos de cálculo, pode eliminar um dos serviços na nuvem (designado como um ambiente de preparação para a implementação da sua aplicação) depois de verificar se o seu serviço de cloud trocado está a funcionar como esperado.

O resto da API para realizar uma 'troca' entre dois serviços de nuvem implantações de suporte alargado é abaixo:
```http
POST https://management.azure.com/subscriptions/subId/providers/Microsoft.Network/locations/region/setLoadBalancerFrontendPublicIpAddresses?api-version=2020-11-01
```
```
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
```
## <a name="common-questions-about-swapping-deployments"></a>Questões comuns sobre a troca de implementações

### <a name="what-are-the-prerequisites-for-swapping-between-two-cloud-services"></a>Quais são os pré-requisitos para a troca entre dois serviços em nuvem?
Existem dois pré-requisitos fundamentais para uma troca bem sucedida de serviço na nuvem (suporte alargado):
* Se pretender utilizar um endereço IP estático/reservado para um dos serviços de cloud permutáveis, o outro serviço de nuvem também deve utilizar um IP reservado. Caso contrário, a troca falha.
* Todas as instâncias das suas funções devem estar a decorrer antes de poder efetuar a troca. Pode verificar o estado das suas instâncias na lâmina geral do portal Azure. Em alternativa, pode utilizar o comando Get-AzRole no Windows PowerShell.

As atualizações do Sistema de Atendimento do Hóspede e as operações de cura do serviço também podem causar a falha dos swaps de implementação. Para obter mais informações, consulte problemas de implementação do serviço de nuvem troubleshoot.

### <a name="can-i-perform-a-vip-swap-in-parallel-with-another-mutating-operation"></a>Posso realizar uma Troca VIP em paralelo com outra operação de mutação?
N.º O VIP Swap é uma alteração de rede que só precisa de ser concluída antes de qualquer outra operação de computação ser realizada no(s) serviço de cloud. Executar uma operação de atualização, eliminação ou autoescala no(s) serviço(s) cloud(s) enquanto um Swap VIP está em andamento ou desencadear uma troca VIP enquanto outra operação de computação está em andamento pode deixar o serviço de nuvem em um estado nãodesejável a partir do qual a recuperação pode não ser possível. 

### <a name="does-a-swap-incur-downtime-for-my-application-how-should-i-handle-it"></a>Uma troca incorre no tempo de inatividade para o meu pedido? Como devo lidar com isto?
Como descrito na secção anterior, uma troca de serviços em nuvem é tipicamente rápida porque é apenas uma alteração de configuração no equilibrador de carga Azure. Em alguns casos, pode levar 10 ou mais segundos e resultar em falhas de ligação transitórias. Para limitar o impacto aos seus clientes, considere implementar a lógica de relagem do cliente.

## <a name="next-steps"></a>Passos seguintes 
- Reveja os [pré-requisitos](deploy-prerequisite.md) de implantação para serviços em nuvem (suporte alargado).
- Reveja [perguntas frequentes](faq.md) para serviços cloud (suporte alargado).
- Implementar um Serviço de Cloud (suporte alargado) utilizando o [portal Azure](deploy-portal.md), [PowerShell,](deploy-powershell.md) [Modelo](deploy-template.md) ou [Estúdio Visual](deploy-visual-studio.md).
