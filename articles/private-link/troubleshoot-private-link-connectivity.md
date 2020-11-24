---
title: Resolver problemas de conectividade do Azure Private Link
description: Orientação passo a passo para diagnosticar conectividade de ligação privada
services: private-link
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 45a7a146dd929408b50a0045fe2598726ee05505
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544314"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>Resolver problemas de conectividade do Azure Private Link

Este artigo fornece orientações passo a passo para validar e diagnosticar conectividade para a sua configuração de Ligação Privada Azure.

Com o Azure Private Link, pode aceder à plataforma Azure como um serviço de serviço (PaaS), como a Azure Storage, Azure Cosmos DB e Azure SQL Database, e a Azure acolheu serviços de clientes ou parceiros através de um ponto final privado na sua rede virtual. O tráfego entre a sua rede virtual e o serviço atravessa a rede de espinha dorsal da Microsoft, que elimina a exposição da internet pública. Também pode criar o seu próprio serviço de ligação privada na sua rede virtual e entregá-lo em privado aos seus clientes.

Pode ativar o seu serviço que funciona atrás do nível padrão de Azure Load Balancer para acesso private Link. Os consumidores do seu serviço podem criar um ponto final privado dentro da sua rede virtual e mapeá-lo para este serviço para aceder a ele em privado.

Aqui estão os cenários de conectividade que estão disponíveis com Private Link:

- Rede virtual da mesma região
- Redes virtuais de olhos regionais
- Redes virtuais globalmente espreitadas
- Cliente no local sobre circuitos VPN ou Azure ExpressRoute

## <a name="deployment-troubleshooting"></a>Resolução de problemas de implantação

Reveja as informações sobre [as políticas de rede de desativação no serviço de ligação privada](./disable-private-link-service-network-policy.md) para casos de resolução de problemas em que não é capaz de selecionar o endereço IP de origem a partir da sub-rede da sua escolha para o seu serviço de ligação privada.

Certifique-se de que a definição **privadaLinkServiceNetworkPolicies** está desativada para a sub-rede a partir da qual está a selecionar o endereço IP de origem.

## <a name="diagnose-connectivity-problems"></a>Diagnosticar problemas de conectividade

Se sentir problemas de conectividade com a configuração do seu link privado, reveja estes passos para se certificar de que todas as configurações habituais são como esperado.

1. Reveja a configuração do Link Privado navegando no recurso.

    a. Vá ao **Private Link Center.**

      ![Centro de Ligação Privada](./media/private-link-tsg/private-link-center.png)

    b. No painel esquerdo, selecione **Serviços de ligação privada**.

      ![Serviços de ligação privada](./media/private-link-tsg/private-link-service.png)

    c. Filtre e selecione o serviço de ligação privada que pretende diagnosticar.

    d. Reveja as ligações privadas do ponto final.
     - Certifique-se de que o ponto final privado de onde procura conectividade está listado com um estado de ligação **aprovado.**
     - Se o Estado estiver **pendente,** selecione-o e aprove-o.

       ![Conexões de ponto final privado](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Vá ao ponto final privado de onde está a ligar selecionando o nome. Certifique-se de que o estado da ligação aparece como **aprovado**.

       ![Visão geral da conexão de ponto final privado](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - Depois de ambos os lados serem aprovados, tente novamente a conectividade.

    e. **Reveja o Alias** no separador **Visão Geral** e **ID de recursos** no **separador Propriedades.**
     - Certifique-se de que a informação de **ID** de **Alias** e Recursos corresponde ao **Alias** e **ao ID de recursos** que está a usar para criar um ponto final privado para este serviço.

       ![Verificar informações do Alias](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Verificar informações de ID de recursos](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. Reveja as informações de **visibilidade** no **separador Visão** Geral.
     - Certifique-se de que a sua subscrição se enquadra no âmbito **de visibilidade.**

       ![Verificar informações de visibilidade](./media/private-link-tsg/pls-overview-pane-visibility.png)

    exemplo, Reveja as informações do **balançador de** carga no **separador 'Visão Geral'.**
     - Pode ir ao equilibrador de carga selecionando o link do balançador de carga.

       ![Verificar informações do balançador de carga](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Certifique-se de que as definições do balançador de carga estão configuradas de acordo com as suas expectativas.
       - Rever **configuração IP frontend**.
       - Rever **piscinas backend**.
       - Rever **regras de equilíbrio de carga**.

       ![Verificar propriedades do balançador de carga](./media/private-link-tsg/pls-ilb-properties.png)

     - Certifique-se de que o balançador de carga está a funcionar de acordo com as definições anteriores.
       - Selecione um VM em qualquer sub-rede que não seja a sub-rede onde o pool back-end do balançador de carga está disponível.
       - Tente aceder à extremidade frontal do balançador de carga do VM anterior.
       - Se a ligação chegar à piscina traseira de acordo com as regras de equilíbrio de carga, o seu equilibrador de carga está operacional.
       - Também pode rever a métrica do balanceador de carga através do Azure Monitor para ver se os dados estão a fluir através do equilibrador de carga.

1. Utilize [o Monitor Azure](../azure-monitor/overview.md) para ver se os dados estão a fluir.

    a. No recurso de serviço de ligação privada, selecione **Métricas**.
     - Selecione **Bytes In** ou **Bytes out**.
     - Veja se os dados estão a fluir quando tenta ligar-se ao serviço de ligação privada. Espere um atraso de aproximadamente 10 minutos.

       ![Verifique as métricas do serviço de ligação privada](./media/private-link-tsg/pls-metrics.png)

1. Contacte a equipa [de Suporte Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) se o seu problema ainda não estiver resolvido e ainda existir um problema de conectividade.

## <a name="next-steps"></a>Passos seguintes

 * [Criar um serviço de ligação privada (CLI)](./create-private-link-service-cli.md)
 * [Guia de resolução de problemas do Azure Private Endpoint](troubleshoot-private-endpoint-connectivity.md)