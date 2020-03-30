---
title: Rede de produção azure
description: Este artigo fornece uma descrição geral da rede de produção azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 7c0748e4ff1531649274834cb1e602c228f102e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726696"
---
# <a name="the-azure-production-network"></a>A rede de produção azure
Os utilizadores da rede de produção Azure incluem tanto clientes externos que acedem às suas próprias aplicações Azure como pessoal de apoio interno do Azure que gerem a rede de produção. Este artigo discute os métodos de acesso à segurança e os mecanismos de proteção para estabelecer ligações à rede de produção do Azure.

## <a name="internet-routing-and-fault-tolerance"></a>Encaminhamento de Internet e tolerância à falha
Uma infraestrutura globalmente redundante do Serviço de Nome de Domínio Azure (DNS), combinada com múltiplos clusters de servidores DNS primários e secundários, proporciona tolerância a falhas. Ao mesmo tempo, são utilizados controlos adicionais de segurança da rede Azure, como o NetScaler, para evitar ataques de negação de serviço distribuídos (DDoS) e proteger a integridade dos serviços DoDNs do Azure.

Os servidores DNS Azure estão localizados em várias instalações do datacenter. A implementação do DNS Azure incorpora uma hierarquia de servidores DNS secundários e primários para resolver publicamente os nomes de domínio dos clientes azure. Os nomes de domínio geralmente resolvem-se num endereço CloudApp.net, que envolve o endereço IP virtual (VIP) para o serviço do cliente. Exclusivo do Azure, o VIP que corresponde ao endereço IP (DIP) dedicado interno da tradução do inquilino é feito pelos equilibradores de carga da Microsoft responsáveis por esse VIP.

O Azure está alojado em datacenters Azure distribuídos geograficamente dentro dos EUA, e é construído em plataformas de encaminhamento de última geração que implementam padrões arquitetónicos robustos e escaláveis. Entre as características notáveis estão:

- Engenharia de tráfego baseada em etiquetas multiprotocolo (MPLS), que proporciona uma utilização eficiente da ligação e uma degradação graciosa do serviço em caso de interrupção.
- As redes são implementadas com arquiteturas de redundância "need plus one" (N+1) ou melhor.
- Externamente, os centros de dados são servidos por circuitos de rede dedicados e de alta largura de banda que conectam redundantemente propriedades com mais de 1.200 fornecedores de serviços de internet globalmente em vários pontos de observação. Esta ligação fornece mais de 2.000 gigabytes por segundo (GBps) de capacidade de borda.

Uma vez que a Microsoft detém os seus próprios circuitos de rede entre datacenters, estes atributos ajudam o Azure a obter uma disponibilidade de rede de 99,9% sem a necessidade de fornecedores tradicionais de serviços de internet de terceiros.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Ligação à rede de produção e firewalls associados
A política de fluxo de tráfego de tráfego de internet da rede Azure direciona o tráfego para a rede de produção Azure que está localizada no centro de dados regional mais próximo dos EUA. Uma vez que os datacenters de produção do Azure mantêm uma arquitetura e hardware consistentes de rede, a descrição do fluxo de tráfego que se segue aplica-se consistentemente a todos os datacenters.

Após o tráfego de internet para azure ser encaminhado para o centro de dados mais próximo, uma ligação é estabelecida para os routers de acesso. Estes routers de acesso servem para isolar o tráfego entre nós Azure e VMs instantâneos do cliente. Os dispositivos de infraestrutura de rede nos locais de acesso e borda são os pontos de fronteira onde são aplicados filtros de entrada e saída. Estes routers são configurados através de uma lista de controlo de acesso (ACL) para filtrar o tráfego de rede indesejado e aplicar limites de taxa de tráfego, se necessário. O tráfego que é permitido pela ACL é encaminhado para os equilibradores de carga. Os routers de distribuição são projetados para permitir apenas endereços IP aprovados pela Microsoft, fornecer anti-falsificação e estabelecer ligações TCP que utilizam ACLs.

Os dispositivos externos de equilíbrio de carga estão localizados atrás dos routers de acesso para realizar a tradução de endereços de rede (NAT) de IPs de saída da Internet para IPs internos Azure. Os dispositivos também encaminham pacotes para iPs internos de produção válidos e portas, e funcionam como um mecanismo de proteção para limitar a exposição do espaço de endereço da rede de produção interna.

Por padrão, a Microsoft aplica o Hypertext Transfer Protocol Secure (HTTPS) para todo o tráfego que seja transmitido aos navegadores web dos clientes, incluindo o início de sessão e todo o tráfego posterior. A utilização de TLS v1.2 permite que um túnel seguro para o tráfego flua. Os ACLs no acesso e nos routers centrais asseguram que a fonte do tráfego é consistente com o esperado.

Uma distinção importante nesta arquitetura, quando é comparada com a arquitetura de segurança tradicional, é que não existem firewalls de hardware dedicados, dispositivos especializados de deteção de intrusões ou prevenção, ou outros aparelhos de segurança que normalmente são esperado antes de serem feitas ligações ao ambiente de produção azure. Os clientes geralmente esperam estes dispositivos de firewall de hardware na rede Azure; no entanto, nenhum está empregado dentro de Azure. Quase exclusivamente, essas funcionalidades de segurança são incorporadas no software que gere o ambiente Azure para fornecer mecanismos de segurança robustos e multi-camadas, incluindo capacidades de firewall. Além disso, o âmbito do limite e a extensão associada de dispositivos de segurança críticos é mais fácil de gerir e inventar, como mostra a ilustração anterior, porque é gerido pelo software que está a executar o Azure.

## <a name="core-security-and-firewall-features"></a>Características de segurança e firewall do núcleo
O Azure implementa funcionalidades robustas de segurança de software e firewall a vários níveis para impor funcionalidades de segurança que normalmente são esperadas num ambiente tradicional para proteger o limite de autorização de segurança central.

### <a name="azure-security-features"></a>Funcionalidades de segurança azure
O Azure implementa firewalls de software baseados em hospedeiros dentro da rede de produção. Várias funcionalidades de segurança e firewall do núcleo residem dentro do ambiente core Azure. Estas características de segurança refletem uma estratégia de defesa aprofundada dentro do ambiente Azure. Os dados dos clientes em Azure estão protegidos pelas seguintes firewalls:

**Firewall hipervisor (filtro**de pacote) : Esta firewall é implementada no hipervisor e configurada pelo agente do controlador de tecido (FC). Esta firewall protege o inquilino que corre dentro do VM de acesso não autorizado. Por predefinição, quando um VM é criado, todo o tráfego é bloqueado e, em seguida, o agente FC adiciona regras e exceções no filtro para permitir o tráfego autorizado.

Estão aqui programadas duas categorias de regras:

- **Regras de config ou infraestrutura**da máquina : Por padrão, toda a comunicação está bloqueada. Existem exceções que permitem que um VM envie e receba comunicações do Dynamic Host Configuration Protocol (DHCP) e informações dNS, e envie tráfego para a internet "pública" de saída para outros VMs dentro do cluster FC e servidor de Ativação de OS. Como a lista permitida pelos VMs de destinos de saída não inclui subredes de router Azure e outras propriedades da Microsoft, as regras funcionam como uma camada de defesa para eles.
- Regras de **ficheirode configuração**de funções : Define os ACLs de entrada com base no modelo de serviço dos inquilinos. Por exemplo, se um inquilino tiver uma extremidade frontal web na porta 80 em um determinado VM, a porta 80 é aberta a todos os endereços IP. Se o VM tiver uma função de trabalhador em execução, o papel do trabalhador é aberto apenas ao VM dentro do mesmo inquilino.

**Firewall de hospedeiro nativo**: Azure Service Fabric e Azure Storage funcionam num SISTEMA nativo, que não tem hipervisor e, portanto, o Windows Firewall está configurado com os dois conjuntos de regras anteriores.

**Firewall**do hospedeiro : A firewall hospedeira protege a divisória do hospedeiro, que executa o hipervisor. As regras são programadas para permitir que apenas o FC e as caixas de salto falem com a partição do hospedeiro numa porta específica. As outras exceções são permitir a resposta do DHCP e respostas do DNS. O Azure utiliza um ficheiro de configuração da máquina, que contém um modelo de regras de firewall para a partição do hospedeiro. Existe também uma exceção de firewall hospedeira que permite aos VMs comunicarem para hospedar componentes, servidor de fios e servidor de metadados, através de protocolos/portas específicos.

**Firewall do hóspede**: A peça do Windows Firewall do OS de hóspedes, que é configurável pelos clientes em VMs de clientes e armazenamento.

As funcionalidades de segurança adicionais incorporadas nas capacidades do Azure incluem:

- Componentes de infraestrutura que são atribuídos endereços IP que são de DIPs. Um intruso na internet não pode endereçar o tráfego a esses endereços porque não chegaria à Microsoft. Os routers de gateway da Internet filtram pacotes que são endereçados exclusivamente a endereços internos, para que não entrem na rede de produção. Os únicos componentes que aceitam o tráfego direcionado para VIPs são os equilibradores de carga.
- Firewalls que são implementados em todos os nós internos têm três considerações primárias de arquitetura de segurança para qualquer cenário:

   - As firewalls são colocadas atrás do equilibrador de carga e aceitam pacotes de qualquer lugar. Estes pacotes destinam-se a ser expostos externamente e corresponderiam às portas abertas numa firewall tradicional do perímetro.
   - As firewalls aceitam pacotes apenas a partir de um conjunto limitado de endereços. Esta consideração faz parte da estratégia defensiva e aprofundada contra os ataques do DDoS. Tais ligações são criptograficamente autenticadas.
   - As firewalls só podem ser acedidas a partir de nós internos selecionados. Aceitam pacotes apenas a partir de uma lista enumerada de endereços IP de origem, todos eles DIPs dentro da rede Azure. Por exemplo, um ataque à rede corporativa poderia direcionar os pedidos para estes endereços, mas os ataques seriam bloqueados a menos que o endereço de origem do pacote fosse um na lista enumerada dentro da rede Azure.
     - O router de acesso no perímetro bloqueia pacotes de saída que são endereçados a um endereço que está dentro da rede Azure devido às suas rotas estáticas configuradas.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que a Microsoft faz para garantir a infraestrutura Azure, consulte:

- [Instalações, instalações e segurança física do Azure](physical-security.md)
- [Disponibilidade de infraestruturas azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informação Azure](infrastructure-components.md)
- [Arquitetura de rede Azure](infrastructure-network.md)
- [Funcionalidades de segurança da Base de Dados Azure SQL](infrastructure-sql.md)
- [Operações e gestão de produção da Azure](infrastructure-operations.md)
- [Monitorização de infraestruturas Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura azure](infrastructure-integrity.md)
- [Proteção de dados dos clientes azure](protection-customer-data.md)
