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
ms.openlocfilehash: 1e5253d617c87d5869cebc817da6d265ebfdfa7e
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539472"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>Resolver problemas de conectividade do Azure Private Link

Este artigo fornece orientações passo a passo para validar e diagnosticar conectividade para a sua configuração de Ligação Privada Azure.

Com o Azure Private Link, pode aceder à plataforma Azure como serviços de serviço (PaaS), como o Azure Storage, O Azure Cosmos DB e o Azure SQL Database, e o Azure acolheu serviços de clientes ou parceiros sobre um ponto final privado na sua rede virtual. O tráfego entre a sua rede virtual e o serviço atravessa a rede de espinha dorsal da Microsoft, o que elimina a exposição da internet pública. Também pode criar o seu próprio serviço de link privado na sua rede virtual e entregá-lo em privado aos seus clientes.

Pode ativar o seu serviço que corre atrás do nível Standard de Balancer de Carga Azure para acesso a Private Link. Os consumidores do seu serviço podem criar um ponto final privado dentro da sua rede virtual e mapeá-lo para este serviço para aceder em privado.

Aqui estão os cenários de conectividade que estão disponíveis com Private Link:

- rede virtual da mesma região
- redes virtuais regionalmente peered
- redes virtuais globalmente peered
- Cliente no local sobre circuitos VPN ou Azure ExpressRoute

## <a name="deployment-troubleshooting"></a>Resolução de problemas de implantação

Reveja as informações sobre as políticas de [rede dedesactivação no serviço](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) de ligação privada para casos de resolução de problemas em que não é possível selecionar o endereço IP fonte a partir da subnet da sua escolha para o seu serviço de ligação privada.

Certifique-se de que a definição **privadaLinkServiceNetworkPolicies** está desativada para a sub-rede a partir da qual está a selecionar o endereço IP de origem.

## <a name="diagnose-connectivity-problems"></a>Diagnosticar problemas de conectividade

Se tiver problemas de conectividade com a configuração do link privado, reveja estes passos para se certificar de que todas as configurações habituais são como esperado.

1. Reveja a configuração private link navegando no recurso.

    a. Vá ao **Private Link Center.**

      ![Centro de Ligação Privada](./media/private-link-tsg/private-link-center.png)

    b. No painel esquerdo, selecione serviços de **ligação privada**.

      ![Serviços de ligação privada](./media/private-link-tsg/private-link-service.png)

    c. Filtre e selecione o serviço de ligação privada que pretende diagnosticar.

    d. Reveja as ligações de ponto final privado.
     - Certifique-se de que o ponto final privado de que procura conectividade está listado com um estado de ligação **aprovado.**
     - Se o Estado estiver **Pendente,** selecione-o e aprove-o.

       ![Ligações de ponto final privado](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Vá ao ponto final privado de que está a ligar-se selecionando o nome. Certifique-se de que o estado da ligação aparece como **Aprovado**.

       ![Visão geral da ligação de ponto final privado](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - Depois de ambos os lados serem aprovados, tente novamente a conectividade.

    e. Reveja os **Pseudónimos** no separador **Overview** e no **Id de Recursos** no separador **Propriedades.**
     - Certifique-se de que as informações de **Identificação** de Recursos e **Alias** correspondem ao **Alias** e **ao ID** de recursos que está a usar para criar um ponto final privado para este serviço.

       ![Verifique as informações da Alias](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Verificar informações de ID de recursos](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. Reveja as informações de **visibilidade** no separador **Overview.**
     - Certifique-se de que a sua subscrição se enquadra no âmbito da **Visibilidade.**

       ![Verificar informações sobre visibilidade](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g. Reveja as informações do **balanceor** de carga no separador **Overview.**
     - Pode ir ao equilíbrio de carga selecionando a ligação do equilibrante de carga.

       ![Verificar informações sobre o balanço da carga](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Certifique-se de que as definições do equilíbrio de carga estão configuradas de acordo com as suas expectativas.
       - Rever **a configuração IP frontend**.
       - Reveja **as piscinas backend.**
       - Rever As regras de equilíbrio da **carga.**

       ![Verificar propriedades do equilíbrio résia](./media/private-link-tsg/pls-ilb-properties.png)

     - Certifique-se de que o equilibrador de carga está a funcionar de acordo com as definições anteriores.
       - Selecione um VM em qualquer sub-rede que não seja a sub-rede onde o conjunto de back-end do equilibrador de carga esteja disponível.
       - Tente aceder à extremidade frontal do equilíbrio de carga do VM anterior.
       - Se a ligação chegar ao pool de fundo de acordo com as regras de equilíbrio de carga, o seu equilibrador de carga está operacional.
       - Também pode rever a métrica do equilíbrio de carga através do Monitor Azure para ver se os dados estão a fluir através do equilibrista de carga.

1. Utilize o [Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/overview) para ver se os dados estão a fluir.

    a. No recurso de serviço de ligação privada, selecione **Métricas**.
     - Selecione **Bytes Dentro** ou **Bytes out**.
     - Veja se os dados estão a fluir quando tenta ligar-se ao serviço de ligação privada. Espere um atraso de aproximadamente 10 minutos.

       ![Verifique as métricas de serviço de ligação privada](./media/private-link-tsg/pls-metrics.png)

1. Contacte a equipa [de Suporte Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) se o seu problema ainda estiver por resolver e ainda existir um problema de conectividade.

## <a name="next-steps"></a>Passos seguintes

 * [Criar um serviço de ligação privada (CLI)](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)
 * [Guia de resolução de problemas de Azure Private Endpoint](troubleshoot-private-endpoint-connectivity.md)
