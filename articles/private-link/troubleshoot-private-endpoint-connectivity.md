---
title: Resolver problemas de conectividade do Ponto Final Privado do Azure
description: Orientação passo a passo para diagnosticar conectividade privada de ponto final
services: private-endpoint
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
ms.openlocfilehash: 90831c0e8d5ab73f65dc801319a357d59799cbc6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97807557"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>Resolver problemas de conectividade do Ponto Final Privado do Azure

Este artigo fornece orientações passo a passo para validar e diagnosticar a sua configuração de conectividade Azure Private Endpoint.

Azure Private Endpoint é uma interface de rede que o liga de forma privada e segura a um serviço de ligação privada. Esta solução ajuda-o a proteger as suas cargas de trabalho em Azure, fornecendo conectividade privada aos seus recursos de serviço Azure a partir da sua rede virtual. Esta solução traz efetivamente esses serviços para a sua rede virtual.

Aqui estão os cenários de conectividade que estão disponíveis com Private Endpoint:

- Rede virtual da mesma região
- Redes virtuais de olhos regionais
- Redes virtuais globalmente espreitadas
- Cliente no local sobre circuitos VPN ou Azure ExpressRoute

## <a name="diagnose-connectivity-problems"></a>Diagnosticar problemas de conectividade 

Reveja estes passos para se certificar de que todas as configurações habituais são esperadas para resolver problemas de conectividade com a configuração do seu ponto final privado.

1. Reveja a configuração private Endpoint navegando no recurso.

    a. Vá ao **Private Link Center.**

      ![Centro de Ligação Privada](./media/private-endpoint-tsg/private-link-center.png)

    b. No painel esquerdo, selecione **Pontos finais privados**.
    
      ![Pontos finais privados](./media/private-endpoint-tsg/private-endpoints.png)

    c. Filtre e selecione o ponto final privado que pretende diagnosticar.

    d. Reveja a rede virtual e informações sobre DNS.
     - Validar que o estado de ligação está **aprovado.**
     - Certifique-se de que o VM tem conectividade com a rede virtual que acolhe os pontos finais privados.
     - Verifique se as informações FQDN (cópia) e o endereço IP privado são atribuídos.
    
       ![Rede virtual e configuração de DNS](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. Utilize [o Monitor Azure](../azure-monitor/overview.md) para ver se os dados estão a fluir.

    a. No recurso ponto final privado, selecione **Monitor**.
     - Selecione **dados dentro** ou **fora dos dados**. 
     - Veja se os dados estão a fluir quando tenta ligar-se ao ponto final privado. Espere um atraso de aproximadamente 10 minutos.
    
       ![Verifique a telemetria de ponto final privado](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  Utilize a resolução de problemas da **VM Connection** do Observador de Redes Azure.

    a. Selecione o VM cliente.

    b. Selecione **A resolução de problemas** de ligação e, em seguida, selecione o separador **de ligações de saída.**
    
      ![Network Watcher - Testar ligações de saída](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c. Selecione **Use Network Watcher para rastreio de ligação detalhado**.
    
      ![Observador de Rede - Resolução de problemas de conexão](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. Selecione **Teste por FQDN**.
     - Cole o FQDN a partir do recurso de ponto final privado.
     - Providenciar um porto. Normalmente, use 443 para Azure Storage ou Azure Cosmos DB e 1336 para SQL.

    e. Selecione **Teste** e valide os resultados do teste.
    
      ![Observador de Rede - Resultados do teste](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. A resolução do DNS a partir dos resultados dos testes deve ter o mesmo endereço IP privado atribuído ao ponto final privado.

    a. Se as definições de DNS estiverem incorretas, siga estes passos:
     - Se utilizar uma zona privada: 
       - Certifique-se de que a rede virtual VM do cliente está associada à zona privada.
       - Verifique se existe o registo privado da zona de DNS. Se não existe, crie-o.
     - Se utilizar DNS personalizados:
       - Reveja as definições de DNS personalizadas e valide que a configuração do DNS está correta.
       Para obter orientação, consulte [a visão geral do ponto final privado: configuração DNS](./private-endpoint-overview.md#dns-configuration).

    b. Se a conectividade estiver a falhar devido a grupos de segurança de rede (NSGs) ou a rotas definidas pelo utilizador:
     - Reveja as regras de saída da NSG e crie as regras de saída apropriadas para permitir o tráfego.
    
       ![Regras de saída da NSG](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. A Máquina Virtual de Origem deve ter a rota para o next hop do Ponto Final Privado como InterfaceEndpoints nas Rotas Eficazes do NIC. 

    a. Se não conseguir ver a Rota do Ponto Final Privada na Fonte VM, verifique se 
     - O VM de Origem e o Ponto Final Privado pertencem ao mesmo VNET. Se sim, então precisa de apoio. 
     - O VM de Origem e o Ponto Final Privado fazem parte de diferentes VNETs e, em seguida, verifique a conectividade IP entre os VNETS. Se houver conectividade IP e ainda não conseguir ver a rota, engate o suporte. 

1. Se a ligação tiver resultados validados, o problema de conectividade pode estar relacionado com outros aspetos como segredos, fichas e senhas na camada de aplicação.
   - Neste caso, reveja a configuração do recurso de ligação privada associado ao ponto final privado. Para mais informações, consulte o [guia de resolução de problemas do Azure Private Link](troubleshoot-private-link-connectivity.md)
   
1. É sempre bom reduzir antes de levantar o bilhete de apoio. 

    a. Se a Fonte estiver ligada ao Ponto Final Privado em Azure com problemas, então tente ligar 
      - Para outra Máquina Virtual a partir de Instalações e verifique se tem conectividade IP com a Rede Virtual a partir de Instalações. 
      - De uma máquina virtual na Rede Virtual até ao Ponto Final Privado.
      
    b. Se a Fonte é Azure e Private Endpoint está em rede virtual diferente, então tente ligar 
      - Para o Ponto Final Privado de uma Fonte diferente. Ao fazê-lo, pode isolar quaisquer problemas específicos da Máquina Virtual. 
      - Para qualquer Máquina Virtual que faça parte da mesma Rede Virtual da Private Endpoint.  

1. Contacte a equipa [de Suporte Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) se o seu problema ainda não estiver resolvido e ainda existir um problema de conectividade.

## <a name="next-steps"></a>Passos seguintes

 * [Criar um ponto final privado na sub-rede atualizada (portal Azure)](./create-private-endpoint-portal.md)
 * [Guia de resolução de problemas do Azure Private Link](troubleshoot-private-link-connectivity.md)
