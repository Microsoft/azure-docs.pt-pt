---
title: Problemas de sucação de problemas de conectividade azure private endpoint
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
ms.openlocfilehash: df4ec6ddbba029eb29d2440717697968f8c79302
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191071"
---
# <a name="troubleshoot-private-endpoint-connectivity-problems"></a>Resolver problemas de conectividade do Ponto Final Privado

Este guia fornece orientação passo a passo para validar e diagnosticar a sua configuração de conectividade de ponto final privado. 

Azure Private Endpoint é uma interface de rede que o liga de forma privada e segura a um serviço de Link Privado. Esta solução ajuda-o a garantir as suas cargas de trabalho no Azure, fornecendo conectividade privada aos recursos de serviço do Seu Azure a partir da sua rede virtual. Isto efetivamente trazendo esses serviços para a sua rede virtual. 

Aqui estão os cenários de conectividade que estão disponíveis com Pontos Finais Privados 
- rede virtual da mesma região 
- redes virtuais regionalmente peered
- redes virtuais globalmente peered
- clientes no local sobre circuitos VPN ou Express Route

## <a name="diagnosing-connectivity-problems"></a>Diagnosticar problemas de conectividade 
Veja os passos listados abaixo para se certificar de que todas as configurações habituais são como esperado para resolver problemas de conectividade com a sua configuração de ponto final privado.

1. Reveja a configuração private endpoint navegando no recurso 

    a) Ir ao **Private Link Center**

      ![Centro de Ligação Privada](./media/private-endpoint-tsg/private-link-center.png)

    b) Selecione pontos finais privados do painel de navegação esquerdo
    
      ![Pontos Finais Privados](./media/private-endpoint-tsg/private-endpoints.png)

    c) Filtrar e selecionar o ponto final privado que pretende diagnosticar

    d) Rever a rede virtual e as informações do DNS
    
     - Estado de ligação **validado** é aprovado
     - Certifique-se de que o VM tem conectividade com o VNet que acolhe os Pontos Finais Privados
     - Informações fQDN (cópia) e endereço IP privado atribuído
    
       ![Configuração VNet e DNS](./media/private-endpoint-tsg/vnet-dns-configuration.png)    
    
2. Use [**o Monitor Azure**](https://docs.microsoft.com/azure/azure-monitor/overview) para rever os dados está a fluir

    a) Sobre o recurso Private Endpoint, selecione **Monitor**
     - Selecione data-in ou data-out e reveja se os dados estiverem a fluir quando tentar ligar-se ao Ponto Final Privado. Espere um atraso de cerca de 10 minutos.
    
       ![Verificar telemetria de ponto final privado](./media/private-endpoint-tsg/private-endpoint-monitor.png)

3. Utilize problemas de ligação VM do Observador da **Rede**

    a) Selecione o VM do cliente

    b) Selecione a secção de resolução de problemas da **Ligação,** separador **de ligação de saída**
    
      ![Observador de Rede - Testar ligações de saída](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c) Selecione **Use Network Watcher para rastrear a ligação detalhada**
    
      ![Network Watcher - Sutura de Problemas de Ligação](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d) Selecionar **teste por FQDN**
     - Colar o FQDN do recurso Private Endpoint
     - Forneça uma porta *(tipicamente 443 para Armazenamento Azure ou COSMOS, 1336 para Sql...* )

    e) Clique em **Testar** e validar os resultados dos testes
    
      ![Network Watcher - resultados dos testes](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
4. A resolução do DNS dos resultados dos testes deve ter o mesmo endereço IP privado atribuído ao Ponto Final Privado

    a) Se as definições de DNS estiverem incorretas, faça o seguinte
     - Utilização da Zona Privada: 
       - Certifique-se de que o cliente VM VNet está associado à Zona Privada
       - Rever o registo privado de zona dNS existe, criar se não existir
    
     - Utilizando DNS personalizados:
       - Reveja as definições de DNS do seu cliente e valide a configuração dNS correta.
       Consulte a [visão geral do Ponto final privado - Configuração DNS](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration) para orientação.

    b) Se a conectividade estiver a falhar por causa de NSG/UDRs
     - Rever as regras de saída do NSG e criar regras de saída adequadas para permitir o tráfego
    
       ![Regras de saída do NSG](./media/private-endpoint-tsg/nsg-outbound-rules.png)

5. Se a ligação tiver validado resultados, o problema da conectividade pode estar relacionado com outros aspetos, como segredos, fichas, senhas na camada de aplicação.
   - Neste caso, reveja a configuração do recurso Private Link associado ao ponto final privado. Consulte o guia de resolução de [problemas do Link Privado](troubleshoot-private-link-connectivity.md). 

6. Contacte a equipa [de Suporte Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) se o seu problema ainda estiver por resolver e o problema de conectividade ainda existir. 

## <a name="next-steps"></a>Passos seguintes

 * [Criar um Ponto Final Privado na subnet atualizada (portal Azure)](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)

 * [Guia de resolução de problemas de link privado](troubleshoot-private-link-connectivity.md)
