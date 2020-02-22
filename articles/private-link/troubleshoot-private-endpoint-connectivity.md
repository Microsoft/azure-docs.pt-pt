---
title: Resolver problemas de conectividade do Ponto Final Privado do Azure
description: Orientação passo a passo para diagnosticar conectividade de ponto final privado
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
ms.openlocfilehash: fcc482e6231bbd925fd500a37989052765dede58
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77538539"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>Resolver problemas de conectividade do Ponto Final Privado do Azure

Este artigo fornece orientação passo a passo para validar e diagnosticar a sua configuração de conectividade Azure Private Endpoint.

Azure Private Endpoint é uma interface de rede que o liga de forma privada e segura a um serviço de ligação privada. Esta solução ajuda-o a garantir as suas cargas de trabalho no Azure, fornecendo conectividade privada aos recursos de serviço do Seu Azure a partir da sua rede virtual. Esta solução traz efetivamente esses serviços para a sua rede virtual.

Aqui estão os cenários de conectividade que estão disponíveis com private endpoint:

- rede virtual da mesma região
- redes virtuais regionalmente peered
- redes virtuais globalmente peered
- Cliente no local sobre circuitos VPN ou Azure ExpressRoute

## <a name="diagnose-connectivity-problems"></a>Diagnosticar problemas de conectividade 

Reveja estes passos para garantir que todas as configurações habituais são como esperado para resolver problemas de conectividade com a configuração do ponto final privado.

1. Reveja a configuração private endpoint navegando no recurso.

    a. Vá ao **Private Link Center.**

      ![Centro de Ligação Privada](./media/private-endpoint-tsg/private-link-center.png)

    b. No painel esquerdo, selecione **pontos finais privados**.
    
      ![Pontos finais privados](./media/private-endpoint-tsg/private-endpoints.png)

    c. Filtre e selecione o ponto final privado que pretende diagnosticar.

    d. Reveja a rede virtual e a informação do DNS.
     - Valide que o estado de ligação está **aprovado**.
     - Certifique-se de que o VM tem conectividade com a rede virtual que acolhe os pontos finais privados.
     - Verifique se as informações fQDN (cópia) e o endereço IP privado são atribuídos.
    
       ![Rede virtual e configuração DNS](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. Utilize o [Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/overview) para ver se os dados estão a fluir.

    a. No recurso de ponto final privado, selecione **Monitor**.
     - Selecione **Dados Dentro** ou **Dados Fora**. 
     - Veja se os dados estão a fluir quando tenta ligar-se ao ponto final privado. Espere um atraso de aproximadamente 10 minutos.
    
       ![Verifique a telemetria de pontofinal privado](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  Utilize o problema da **Ligação VM** do Observador da Rede Azure.

    a. Selecione o VM do cliente.

    b. Selecione A resolução de **problemas da Ligação**e, em seguida, selecione o separador de **ligações Outbound.**
    
      ![Observador de Rede - Testar ligações de saída](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c. Selecione Use O Observador da Rede para rastrear **a ligação detalhada**.
    
      ![Network Watcher - Sutura de Problemas de Ligação](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. Selecione **Teste por FQDN**.
     - Colar o FQDN a partir do recurso de ponto final privado.
     - Forneça uma porta. Normalmente, utilize 443 para armazenamento azure ou Azure Cosmos DB e 1336 para SQL.

    e. Selecione **Teste**e valide os resultados dos testes.
    
      ![Observador de Rede - Resultados dos testes](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. A resolução do DNS dos resultados dos testes deve ter o mesmo endereço IP privado atribuído ao ponto final privado.

    a. Se as definições de DNS estiverem incorretas, siga estes passos:
     - Se utilizar uma zona privada: 
       - Certifique-se de que a rede virtual VM do cliente está associada à zona privada.
       - Verifique se o registo privado de zona DNS existe. Se não existe, crie-o.
     - Se utilizar DNS personalizados:
       - Reveja as definições personalizadas de DNS e valide que a configuração DNS está correta.
       Para obter orientação, consulte a [visão geral do ponto final privado: Configuração DNS](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration).

    b. Se a conectividade falhar devido a grupos de segurança de rede (NSGs) ou rotas definidas pelo utilizador:
     - Reveja as regras de saída do NSG e crie as regras de saída apropriadas para permitir o tráfego.
    
       ![Regras de saída do NSG](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. Se a ligação tiver validado resultados, o problema de conectividade pode estar relacionado com outros aspetos, como segredos, fichas e senhas na camada de aplicação.
   - Neste caso, reveja a configuração do recurso de ligação privada associado ao ponto final privado. Para mais informações, consulte o guia de resolução de problemas do [Azure Private Link](troubleshoot-private-link-connectivity.md).

1. Contacte a equipa [de Suporte Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) se o seu problema ainda estiver por resolver e ainda existir um problema de conectividade.

## <a name="next-steps"></a>Passos seguintes

 * [Criar um ponto final privado na subnet atualizada (portal Azure)](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)
 * [Guia de resolução de problemas de Ligação Privada Azure](troubleshoot-private-link-connectivity.md)
