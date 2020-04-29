---
title: 'Trabalhar remotamente: Redes De Aparelhos Virtuais (NVA) considerações para trabalhoremoto [ Azure VPN Gateway'
description: Este artigo ajuda-o a compreender as coisas que deve ter em conta o trabalho com a Rede De Aparelhos Virtuais (NVAs) em Azure durante a pandemia COVID-19.
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/21/2020
ms.author: scottnap
ms.openlocfilehash: 8a22e8f0bde2d13b4055566d96680fadc2db6e11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80337099"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>Trabalhar remotamente: Redes Virtuais (NVA) considerações para trabalhoremoto

>[!NOTE]
>Este artigo descreve como pode aproveitar a Rede de Aparelhos Virtuais, Azure, rede Microsoft e o ecossistema parceiro Azure para trabalhar remotamente e mitigar problemas de rede que está a enfrentar devido à crise covid-19.
>

Alguns clientes da Azure utilizam eletrodomésticos virtuais de rede de terceiros (NVAs) do Azure Marketplace para prestar serviços críticos, como a VPN point-to-site para os seus colaboradores que trabalham a partir de casa durante a epidemia COVID-19. Este artigo descreve algumas orientações de alto nível a ter em conta ao aproveitar os NVAs em Azure para fornecer soluções de acesso remoto.

## <a name="nva-performance-considerations"></a>Considerações de desempenho da NVA

Todos os principais fornecedores de NVA no Mercado Azure devem ter recomendações sobre o tamanho VM e o número de instâncias a utilizar ao implementar as suas soluções.  Embora quase todos os fornecedores de NVA lhe permitam escolher qualquer tamanho que esteja disponível numa determinada Região, é muito importante que siga as recomendações dos fornecedores para tamanhos de instância Sav, uma vez que estas recomendações são os tamanhos VM com que o fornecedor fez testes de desempenho em Azure.  

### <a name="consider-the-following"></a>Considere o seguinte

- **Capacidade e número de utilizadores simultâneos** - Este número é particularmente importante para os utilizadores DEVPN ponto-a-site, uma vez que cada utilizador conectado criará um túnel encriptado (IPSec ou SSL VPN).  
- **Agregar a entrada** - Qual é a largura de banda agregada que você precisará para acomodar o número de utilizadores que você precisa para fornecer acesso remoto.
- **O tamanho VM de que necessita -** Deve utilizar sempre os tamanhos VM recomendados pelo fornecedor NVA.  Para VPN ponto-a-site, se tiver ligações de utilizador muito simultâneas, deverá utilizar tamanhos vm maiores, como VMs da [série Dv2 e DSv2.](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series "Série Dv2 e Dsv2") Estes VMs tendem a ter mais VCPUs e podem lidar com sessões VPN mais simultâneas.  Além de ter mais núcleos virtuais, os tamanhos de VM maiores em Azure têm uma capacidade de largura de banda mais agregada do que tamanhos vm menores.
    > **Importante:** Cada fornecedor utiliza recursos de forma diferente.  Se não for claro quais os tamanhos de instância que deve utilizar para acomodar a sua carga estimada de utilizador, deve contactar diretamente o fornecedor de software e pedir-lhes uma recomendação.
- **Número de casos** - Se espera ter um grande número de utilizadores e ligações, existem limites para o que o aumento dos tamanhos das instâncias nva pode alcançar.  Considere implementar vários casos de VM.
- **IPSec VPN vs SSL VPN** - Em geral, as implementações de VPN IPSec têm um desempenho melhor do que as implementações vpN ssl.  
- **Licenciamento** - Certifique-se de que as licenças de software que adquiriu para a solução NVA cobrirão o crescimento repentino que poderá experimentar durante a epidemia COVID-19.  Muitos programas de licenciamento nva limitam o número de ligações ou largura de banda que a solução é capaz de fazer.
- **Networking Acelerado** - Considere uma solução NVA que tenha suporte para o Networking Acelerado.  A ligação acelerada em rede permite a virtualização de I/O de raiz única (SR-IOV) a um VM, melhorando consideravelmente o seu desempenho em rede. Este caminho de alto desempenho contorna o hospedeiro a partir da trajetória de dados, reduzindo a latência, o nervosismo e a utilização do CPU para utilização com as cargas de trabalho de rede mais exigentes em tipos de VM suportados. A rede acelerada é suportada na maioria dos tamanhos de instância otimizados para a computação com dois ou mais vCPUs.

## <a name="monitoring-resources"></a>Recursos de monitorização

Cada solução NVA tem as suas próprias ferramentas e recursos para monitorizar o desempenho da sua NVA.  Consulte a documentação dos seus fornecedores para se certificar de que compreende as limitações de desempenho e pode detetar quando o seu NVA está próximo ou a atingir a capacidade.  Além disso, pode ver o Azure Monitor Network Insights e ver informações básicas sobre o desempenho da sua rede de aparelhos virtuais, tais como:

- Utilização da CPU
- Entrada na Rede
- Saída da Rede
- Fluxos de Entrada
- Fluxos de saída

## <a name="next-steps"></a>Passos Seguintes

A maioria dos principais parceiros da NVA têm colocado orientação em torno da escala para um crescimento súbito e inesperado durante o COVID-19. Aqui estão algumas ligações úteis aos recursos dos parceiros.

[Barracuda Habilitar o Trabalho a partir de casa enquanto assegura os seus dados durante o COVID-19](https://www.barracuda.com/covid-19/work-from-home "Habilitar o Trabalho a partir de casa enquanto assegura os seus dados durante o COVID-19")

[Cisco AnyConnect Implementation and Performance/Scaling Reference for COVID-19 Preparation](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "Cisco AnyConnect Implementation and Performance/Scaling Reference for COVID-19 Preparation")

[Citrix COVID-19 Centro de Apoio à Resposta](https://www.citrix.com/support/covid-19-coronavirus.html "Citrix COVID-19 Centro de Apoio à Resposta")

[Orientação F5 para abordar o aumento dramático dos trabalhadores remotos](https://www.f5.com/business-continuity "Orientação F5 para abordar o aumento dramático dos trabalhadores remotos")

[Atualizações fortinet COVID-19 para Clientes e Parceiros](https://www.fortinet.com/covid-19.html "Atualizações COVID-19 para Clientes e Parceiros")

[Centro de Resposta Palo Alto Networks COVID-19](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "Centro de Resposta Palo Alto Networks COVID-19")
