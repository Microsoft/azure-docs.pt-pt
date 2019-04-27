---
title: Rede de produção do Azure
description: Este artigo fornece uma descrição geral da rede de produção do Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: afae7cc6390ea4cd8c18c687e9d99400c8da9da4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611351"
---
# <a name="the-azure-production-network"></a>A rede de produção do Azure
Os utilizadores da rede de produção do Azure incluem ambos os clientes externos que acedem aos seus próprios aplicações do Azure e a equipe de suporte do Azure interno que gerem a rede de produção. Este artigo aborda os métodos de acesso de segurança e mecanismos de proteção para o estabelecimento de ligações à rede de produção do Azure.

## <a name="internet-routing-and-fault-tolerance"></a>Tolerância a falhas e encaminhamento de Internet
Uma globalmente redundantes internos e externos serviço de nomes de domínio (DNS) do Azure a infraestrutura, combinada com vários primários e secundários DNS server clusters, fornece tolerância a falhas. Ao mesmo tempo, os controlos de segurança de rede do Azure adicionais, como o NetScaler, são utilizados para impedir ataques denial of service (DDoS) distribuídos e proteger a integridade dos serviços de DNS do Azure.

Os servidores de DNS do Azure estão localizados em vários locais do Centro de dados. A implementação de DNS do Azure incorpora uma hierarquia de servidores DNS primários e secundários para publicamente resolver nomes de domínio do cliente do Azure. Normalmente, resolvem os nomes de domínio para um endereço de CloudApp.net, que encapsula o endereço IP virtual (VIP) para o serviço do cliente. Exclusivo para o Azure, o VIP que corresponde ao endereço de IP (DIP) dedicado interno da tradução de inquilino é feito com os balanceadores de carga do Microsoft, responsáveis por esse VIP.

Azure está alojado em datacenters do Azure distribuídos geograficamente, nos E.U.A. e tenha sido criada nas plataformas de encaminhamento de topo de gama que implementam as normas de arquitetura robustas e escaláveis. Entre os recursos importantes são:

- Engenharia de tráfego com base em MPLS de troca de etiqueta multiprotocolo, que fornece a utilização do link eficiente e redução gradual do serviço, se houver uma falha.
- Redes são implementadas com "necessidade de mais um" (N + 1) as arquiteturas de redundância ou superior.
- Externamente, centros de dados são servidos pelo circuitos de rede dedicado, de grande largura de banda que ligam adquiria propriedades com mais de 1.200 fornecedores de serviços de internet globalmente em vários pontos de peering. Esta ligação fornece em excesso de 2.000 gigabytes por segundo (GBps) da capacidade de borda.

Uma vez que a Microsoft possui seus próprio circuitos de rede entre os datacenters, esses atributos ajudam a alcançar + de 99,9% de disponibilidade de rede sem a necessidade de fornecedores de serviços de internet de terceiros tradicional de oferta do Azure.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Ligação à rede de produção e firewalls associados
A política de fluxo de tráfego de internet de rede do Azure direciona o tráfego para a rede de produção do Azure que está localizado no datacenter regional mais próximo dentro dos EUA. Uma vez que os datacenters de produção do Azure manter a arquitetura de rede consistente e de hardware, a descrição do fluxo de tráfego que se segue aplica-se consistente para todos os datacenters.

Depois do tráfego de internet para o Azure é encaminhado para o datacenter mais próximo, é estabelecida uma ligação para os routers de acesso. Estes routers acesso servem para isolar o tráfego entre nós do Azure e VMs instanciado por cliente. Dispositivos de infraestrutura de rede nas localizações de acesso e o edge são os pontos de limites nos quais os filtros de entrada e de saída são aplicados. Estes routers são configuradas através de uma lista de controle em camadas de acesso (ACL) para filtrar o tráfego de rede indesejado e aplicar limites de velocidade de tráfego, se necessário. Tráfego permitido por ACL é encaminhado para os balanceadores de carga. Routers de distribuição foram concebidos para permitir que apenas os endereços IP aprovado Microsoft, forneça o antisspoofing e estabelecer ligações TCP que usam ACLs.

Dispositivos de balanceamento de carga externos localizados atrás dos routers de acesso para efetuar a tradução de endereços de rede (NAT) de IPs encaminháveis para a internet para IPs interno do Azure. Os dispositivos também encaminham pacotes para a produção válida interno IPs e portas e eles atuam como um mecanismo de proteção para limitar a exposição o espaço de endereços de rede de produção interno.

Por predefinição, a Microsoft impõe o protocolo Secure HTTPS (Hypertext Transfer) para todo o tráfego que é transmitido a navegadores da web dos clientes, incluindo o início de sessão e todo o tráfego após esta data. A utilização da versão do TLS 1.2 permite que um túnel seguro para o tráfego através de. As ACLs em routers de acesso e core Certifique-se de que a origem do tráfego é consistente com o esperado.

Uma distinção importante nesta arquitetura, quando são comparados com a arquitetura de segurança tradicionais, é que existem outras aplicações de segurança que são normalmente ou dispositivos de prevenção, detecção de intrusão especializadas ou não firewalls de hardware dedicado era esperado antes das ligações são estabelecidas no ambiente de produção do Azure. Que os clientes exigem, normalmente, estes dispositivos de hardware do firewall na rede do Azure; No entanto, nenhum são empregados no Azure. Quase que exclusivamente, esses recursos de segurança são criados no software que executa o ambiente do Azure para fornecer mecanismos de segurança robusta, com várias camadas, incluindo capacidades de firewall. Além disso, o âmbito do limite e associada à expansão desordenada de dispositivos de segurança crítico é fácil de gerenciar e de inventário, conforme mostrado na ilustração anterior, porque é gerida pelo software que está a executar o Azure.

## <a name="core-security-and-firewall-features"></a>Principais recursos de segurança e de firewall
O Azure implementa recursos de firewall em vários níveis para impor a recursos de segurança que, normalmente, espera-se num ambiente tradicional para proteger os limites de autorização de segurança de principais de segurança de software robusto e.

### <a name="azure-security-features"></a>Recursos de segurança do Azure
O Azure implementa firewalls de software baseados em host dentro da rede de produção. Vários núcleos de segurança e recursos de firewall residem no núcleo do ambiente do Azure. Estas funcionalidades de segurança refletem uma estratégia de defesa em profundidade dentro do ambiente do Azure. Dados do cliente no Azure estão protegidos pelos firewalls seguintes:

**Firewall de hipervisor (filtro de pacotes)**: Esta firewall é implementada no hipervisor e configurada pelo agente de controlador (FC) de recursos de infraestrutura. Esta firewall protege o inquilino que é executado dentro da VM contra acesso não autorizado. Por predefinição, quando é criada uma VM, todo o tráfego é bloqueado e, em seguida, o agente FC adiciona regras e exceções no filtro para permitir tráfego autorizado.

Duas categorias de regras são programadas aqui:

- **Regras de configuração ou a infraestrutura do computador**: Por predefinição, todas as comunicações é bloqueada. Exceções existem que permitem que uma VM enviar e receber comunicações de anfitrião configuração protocolo DHCP (Dynamic) e informações de DNS e enviar tráfego de saída para outras VMs dentro do cluster FC e o servidor de ativação de sistema operacional para a internet "pública". Uma vez que as VMs permitido a lista de saída destinos não inclui sub-redes de router do Azure e outras propriedades da Microsoft, as regras de atuam como uma camada de defesa para eles.
- **Regras de ficheiro de configuração de função**: Define as ACLs de entrada com base no modelo de serviço dos inquilinos. Por exemplo, se um inquilino tiver um front-end da web na porta 80 de uma determinada VM, a porta 80 está aberta para todos os endereços IP. Se a VM tem uma função de trabalho em execução, é aberta a função de trabalho apenas para a VM no mesmo inquilino.

**Firewall de host nativo**: O Azure Service Fabric e o armazenamento do Azure, que são executados num SO nativo sem hipervisor e, portanto, o Firewall do Windows está configurado com os anteriores dois conjuntos de regras.

**Firewall do anfitrião**: A firewall do anfitrião protege a partição de anfitrião, que executa o hipervisor. As regras são programadas para permitir apenas o FC e as caixas para comunicar com a partição de anfitrião numa porta específica de ligação. As restantes exceções encontram-se permitir que a resposta DHCP e respostas DNS. O Azure utiliza um ficheiro de configuração de máquina, que contém um modelo de regras de firewall para a partição do anfitrião. Uma exceção de firewall do anfitrião existe que permite às VMs comunicarem para alojar componentes do servidor durante a transmissão e servidor de metadados como, por meio de protocolo/portas específicas.

**Firewall de convidado**: Parte de Firewall do Windows do que o SO convidado, que é configurável por parte dos clientes em VMs de cliente e de armazenamento.

Recursos de segurança adicionais que são criados para as capacidades do Azure incluem:

- Componentes de infraestrutura que são atribuídos endereços IP que pertençam a DIPs. Um invasor na internet não consegue resolver o tráfego para esses endereços porque ele não seria chegam à Microsoft. Routers de gateway de Internet filtrar pacotes que são resolvidos exclusivamente para endereços internos, para que eles não deve introduzir a rede de produção. Os componentes de apenas aceitam o tráfego é direcionado para VIPs são balanceadores de carga.
- Firewalls que estão implementadas em todos os nós internos tem três considerações de arquitetura de segurança principal para qualquer cenário determinado:

   - Firewalls são colocadas atrás do Balanceador de carga e aceitam os pacotes a partir de qualquer lugar. Estes pacotes destinam-se para ser exposto externamente e correspondem a abrir portas na firewall do perímetro tradicional.
   - Firewalls aceitam pacotes apenas a partir de um conjunto limitado de endereços. Isso é parte da estratégia de defesa aprofundada contra ataques DDoS. Tais conexões criptograficamente são autenticados.
   - Firewalls podem ser acedidos apenas a partir de nós internos selecionados. Eles aceitam os pacotes apenas a partir de uma lista enumerada de endereços IP de origem, todos eles DIPs dentro da rede do Azure. Por exemplo, um ataque na rede da empresa pode direcionar os pedidos para estes endereços, mas os ataques seriam bloqueados, a menos que o endereço de origem do pacote foi um na lista enumerada dentro da rede do Azure.
     - O router de acesso no perímetro bloqueia os pacotes de saída que são resolvidos para um endereço que está dentro da rede do Azure devido a seu encaminhamentos estáticos configurados.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, veja:

- [Recursos do Azure, no local e a segurança física](azure-physical-security.md)
- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Componentes de sistema de informações do Azure e limites](azure-infrastructure-components.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Funcionalidades de segurança da base de dados SQL do Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitorização de infraestrutura do Azure](azure-infrastructure-monitoring.md)
- [Integridade de infraestrutura do Azure](azure-infrastructure-integrity.md)
- [Proteção de dados do cliente do Azure](azure-protection-of-customer-data.md)
