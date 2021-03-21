---
title: 'Funcionando remotamente: Considerações de Aparelho Virtual de Rede (NVA) para trabalho remoto | Azure VPN Gateway'
description: Este artigo ajuda-o a compreender as coisas que deve ter em conta trabalhando com aparelhos virtuais de rede (NVAs) em Azure durante a pandemia COVID-19.
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: scottnap
ms.openlocfilehash: 70b5732e1293e35127a19fbe736d8562056a870b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96499684"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>Funcionando remotamente: Considerações de Aparelho Virtual de Rede (NVA) para trabalhos remotos

>[!NOTE]
>Este artigo descreve como pode aproveitar a rede de aparelhos virtuais da Rede, o Azure, a rede Microsoft e o ecossistema parceiro Azure para trabalhar remotamente e mitigar problemas de rede que está a enfrentar devido à crise covid-19.
>

Alguns clientes da Azure utilizam aparelhos virtuais de rede de terceiros (NVAs) do Azure Marketplace para fornecer serviços críticos, como a VPN Point-to-site para os seus funcionários que trabalham a partir de casa durante a epidemia covid-19. Este artigo descreve algumas orientações de alto nível a ter em conta ao aproveitar as NVAs em Azure para fornecer soluções de acesso remoto.

## <a name="nva-performance-considerations"></a>Considerações de desempenho da NVA

Todos os principais fornecedores de NVA no Azure Marketplace devem ter recomendações sobre o Tamanho VM e o número de casos a utilizar ao implementar as suas soluções.  Embora quase todos os fornecedores de NVA lhe permitam escolher qualquer tamanho que esteja disponível para si numa determinada Região, é muito importante que siga as recomendações dos fornecedores para tamanhos de instância Azure VM, uma vez que estas recomendações são os tamanhos VM com que o fornecedor fez testes de desempenho em Azure.  

### <a name="consider-the-following"></a>Considere o seguinte

- **Capacidade e número de utilizadores simultâneos** - Este número é particularmente importante para os utilizadores de VPN ponto-a-local, uma vez que cada utilizador conectado criará um túnel encriptado (IPSec ou SSL VPN).  
- **Produção agregada** - Qual é a largura de banda agregada que precisa para acomodar o número de utilizadores a que precisa para fornecer acesso remoto.
- **O tamanho VM que vai precisar** - Deve utilizar sempre os tamanhos VM recomendados pelo fornecedor NVA.  Para a VPN ponto-a-local, se tiver muitas ligações de utilizador simultâneas, deve utilizar tamanhos VM maiores, como [Dv2 e Série VMs da série DSv2.](../virtual-machines/dv2-dsv2-series.md "Série Dv2 e Dsv2") Estes VMs tendem a ter mais vCPUs e podem lidar com sessões VPN mais simultâneas.  Além de ter mais núcleos virtuais, os tamanhos de VM maiores em Azure têm uma capacidade de largura de banda mais agregada do que os tamanhos de VM menores.
    > **Importante:** Cada fornecedor utiliza os recursos de forma diferente.  Se não estiver claro quais os tamanhos de instância que deve utilizar para acomodar a sua carga estimada do utilizador, deve contactar diretamente o fornecedor de software e pedir-lhes uma recomendação.
- **Número de casos** - Se espera ter um grande número de utilizadores e ligações, existem limites para o que o escalonamento dos tamanhos de instância do NVA pode alcançar.  Considere implementar várias instâncias de VM.
- **IPSec VPN vs SSL VPN** - Em geral, as implementações ipSec VPN têm um desempenho melhor do que as implementações de VPN SSL.  
- **Licenciamento** - Certifique-se de que as licenças de software que adquiriu para a solução NVA cobrirão o crescimento súbito que poderá experimentar durante a epidemia COVID-19.  Muitos programas de licenciamento NVA limitam o número de ligações ou largura de banda de que a solução é capaz.
- **Rede Acelerada** - Considere uma solução NVA que tenha suporte para Networking Acelerado.  A rede acelerada permite a virtualização de E/S de raiz única (SR-IOV) a um VM, melhorando consideravelmente o seu desempenho em rede. Este percurso de alto desempenho contorna o hospedeiro do caminho dos dados, reduzindo a latência, o nervosismo e a utilização do CPU para utilização com as cargas de trabalho de rede mais exigentes em tipos de VM suportados. A rede acelerada é suportada na maioria dos tamanhos de instância otimizados para o cálculo com dois ou mais vCPUs.

## <a name="monitoring-resources"></a>Recursos de monitorização

Cada solução NVA tem as suas próprias ferramentas e recursos para monitorizar o desempenho do seu NVA.  Consulte a documentação dos seus fornecedores para se certificar de que compreende as limitações de desempenho e pode detetar quando o seu NVA está próximo ou a capacidade de alcançar.  Além disso, pode consultar o Azure Monitor Network Insights e ver informações básicas sobre o desempenho dos seus Aparelhos Virtuais de Rede, tais como:

- Utilização da CPU
- Entrada na Rede
- Saída da Rede
- Fluxos de Entrada
- Fluxos de saída

## <a name="next-steps"></a>Passos Seguintes

A maioria dos principais parceiros da NVA têm colocado orientações em torno da escala para um crescimento súbito e inesperado durante o COVID-19. Aqui estão algumas ligações úteis aos recursos dos parceiros.

[Barracuda Enable Work from home while secureing your data during COVID-19](https://www.barracuda.com/covid-19/work-from-home "Ativar o Trabalho a partir de casa enquanto protege os seus dados durante o COVID-19")

[Check Point Secure Remote Workforce Durante Coronavirus](https://www.checkpoint.com/solutions/secure-remote-workforce-during-coronavirus/ "Proteger mão de obra remota durante o coronavírus")

[Cisco AnyConnect Implementação e Performance/Referência de Escala para preparação COVID-19](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "Cisco AnyConnect Implementação e Performance/Referência de Escala para preparação COVID-19")

[Centro de Suporte de Resposta Citrix COVID-19](https://www.citrix.com/support/covid-19-coronavirus.html "Centro de Suporte de Resposta Citrix COVID-19")

[Orientação F5 para abordar o aumento dramático dos trabalhadores remotos](https://www.f5.com/business-continuity "Orientação F5 para abordar o aumento dramático dos trabalhadores remotos")

[Atualizações Fortinet COVID-19 para Clientes e Parceiros](https://www.fortinet.com/covid-19.html "Atualizações COVID-19 para Clientes e Parceiros")

[Centro de Resposta PALO Alto Networks COVID-19](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "Centro de Resposta PALO Alto Networks COVID-19")

[Kemp Enable Remote Work and Always-On App Experience for Business Continuity](https://kemptechnologies.com/remote-work-always-on-application-experience-business-continuity/ "Kemp Enable Remote Work and Always-On App Experience for Business Continuity")

