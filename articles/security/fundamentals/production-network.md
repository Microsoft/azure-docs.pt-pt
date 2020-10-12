---
title: Rede de produção Azure
description: Conheça a rede de produção Azure. Consulte os métodos de acesso à segurança e os mecanismos de proteção para estabelecer uma ligação à rede.
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
ms.openlocfilehash: 724c2ee2d74ca9cab11280e92faac71c2cb2218e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87543814"
---
# <a name="the-azure-production-network"></a>A rede de produção Azure
Os utilizadores da rede de produção Azure incluem tanto clientes externos que acedem às suas próprias aplicações Azure como pessoal de suporte interno da Azure que gere a rede de produção. Este artigo discute os métodos de acesso à segurança e os mecanismos de proteção para o estabelecimento de ligações à rede de produção Azure.

## <a name="internet-routing-and-fault-tolerance"></a>Encaminhamento de internet e tolerância a falhas
Uma infraestrutura globalmente redundante do Serviço de Nome de Domínio Azure (DNS) interna e externa, combinada com múltiplos clusters de servidores DNS primários e secundários, proporciona tolerância à falha. Ao mesmo tempo, controlos adicionais de segurança da rede Azure, como o NetScaler, são utilizados para evitar ataques de negação de serviço distribuídos (DDoS) e proteger a integridade dos serviços Azure DNS.

Os servidores Azure DNS estão localizados em várias instalações do datacenter. A implementação do Azure DNS incorpora uma hierarquia de servidores DNS secundários e primários para resolver publicamente os nomes de domínio do cliente Azure. Os nomes de domínio geralmente resolvem-se para um endereço CloudApp.net, que envolve o endereço IP virtual (VIP) para o serviço do cliente. Exclusivo do Azure, o VIP que corresponde ao endereço IP (DIP) interno dedicado da tradução do inquilino é feito pelos esquilibradores de carga da Microsoft responsáveis por esse VIP.

O Azure está hospedado em centros de dados Azure distribuídos geograficamente nos EUA, e é construído em plataformas de encaminhamento de última geração que implementam padrões arquitetónicos robustos e escaláveis. Entre as características notáveis estão:

- Engenharia de tráfego baseada em multiprotocol label (MPLS), que proporciona uma utilização eficiente da ligação e uma degradação graciosa do serviço em caso de paragem.
- As redes são implementadas com arquiteturas de redundância "need plus one" (N+1) ou melhor.
- Externamente, os datacenters são servidos por circuitos dedicados de rede de alta largura de banda que ligam propriedades de forma redundante com mais de 1.200 fornecedores de serviços de internet em vários pontos de observação. Esta ligação fornece mais de 2.000 gigabytes por segundo (GBps) de capacidade de borda.

Uma vez que a Microsoft detém os seus próprios circuitos de rede entre datacenters, estes atributos ajudam a oferta Azure a alcançar a disponibilidade de rede de 99,9% sem a necessidade de fornecedores tradicionais de serviços de internet de terceiros.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Ligação à rede de produção e firewalls associadas
A política de fluxo de tráfego de internet da rede Azure direciona o tráfego para a rede de produção Azure que está localizada no centro de dados regional mais próximo dentro dos EUA. Como os datacenters de produção da Azure mantêm uma arquitetura e hardware de rede consistentes, a descrição do fluxo de tráfego que se segue aplica-se consistentemente a todos os datacenters.

Após o tráfego de internet para Azure é encaminhado para o datacenter mais próximo, uma ligação é estabelecida para os routers de acesso. Estes routers de acesso servem para isolar o tráfego entre os nós Azure e os VMs instantâneos do cliente. Os dispositivos de infraestrutura de rede nos locais de acesso e borda são os pontos de fronteira onde são aplicados filtros de entrada e saída. Estes routers são configurados através de uma lista de controlo de acesso hierarquizado (ACL) para filtrar o tráfego de rede indesejado e aplicar limites de taxa de tráfego, se necessário. O tráfego que é permitido pela ACL é encaminhado para os equilibradores de carga. Os routers de distribuição são projetados para permitir apenas endereços IP aprovados pela Microsoft, fornecer anti-falsificação e estabelecer ligações TCP que usam ACLs.

Os dispositivos externos de equilíbrio de carga estão localizados atrás dos routers de acesso para executar a tradução de endereços de rede (NAT) de IPs de encaminhamento de internet para IPs internos Azure. Os dispositivos também encaminham pacotes para iPs e portas internas de produção válidas, e funcionam como um mecanismo de proteção para limitar a exposição do espaço de endereço da rede de produção interna.

Por padrão, a Microsoft aplica o Hypertext Transfer Protocol Secure (HTTPS) para todo o tráfego que seja transmitido aos navegadores web dos clientes, incluindo a inscrição e todo o tráfego posteriormente. A utilização do TLS v1.2 permite que o tráfego flua. Os ACLs nos acessos e nos routers principais asseguram que a fonte do tráfego é consistente com o esperado.

Uma distinção importante nesta arquitetura, quando é comparada com a arquitetura de segurança tradicional, é que não existem firewalls de hardware dedicados, dispositivos especializados de deteção ou prevenção de intrusões, ou outros aparelhos de segurança que são normalmente esperados antes de serem feitas ligações ao ambiente de produção do Azure. Os clientes geralmente esperam estes dispositivos de firewall de hardware na rede Azure; no entanto, nenhum deles é empregado dentro de Azure. Quase exclusivamente, essas funcionalidades de segurança são incorporadas no software que executa o ambiente Azure para fornecer mecanismos de segurança robustos e multi-camadas, incluindo capacidades de firewall. Além disso, o âmbito da fronteira e a extensão associada de dispositivos de segurança críticos é mais fácil de gerir e inventariar, como mostra a ilustração anterior, porque é gerido pelo software que está em execução Azure.

## <a name="core-security-and-firewall-features"></a>Principais funcionalidades de segurança e firewall
O Azure implementa funcionalidades robustas de segurança de software e firewall a vários níveis para impor funcionalidades de segurança que são normalmente esperadas num ambiente tradicional para proteger o limite de Autorização de Segurança.

### <a name="azure-security-features"></a>Funcionalidades de segurança Azure
O Azure implementa firewalls de software baseados em hospedeiros dentro da rede de produção. Várias funcionalidades de segurança e firewall residem dentro do ambiente Azure central. Estas características de segurança refletem uma estratégia de defesa aprofundada dentro do ambiente Azure. Os dados do cliente em Azure estão protegidos pelas seguintes firewalls:

**Firewall hipervisor (filtro de pacote)**: Esta firewall é implementada no hipervisor e configurada pelo agente do controlador de tecido (FC). Esta firewall protege o inquilino que corre dentro do VM de acesso não autorizado. Por predefinição, quando um VM é criado, todo o tráfego é bloqueado e, em seguida, o agente FC adiciona regras e exceções no filtro para permitir o tráfego autorizado.

São programadas duas categorias de regras:

- **Máquina config ou regras de infraestrutura**: Por defeito, todas as comunicações estão bloqueadas. Existem exceções que permitem a um VM enviar e receber informações do Protocolo de Configuração dinâmica do Anfitrião (DHCP) e informações sobre DNS, e enviar tráfego para a internet "pública" de saída para outros VMs dentro do cluster FC e servidor de ativação de OS. Uma vez que a lista permitida pelos VM de destinos de saída não inclui sub-redes de router Azure e outras propriedades da Microsoft, as regras funcionam como uma camada de defesa para eles.
- **Regras de ficheiro de configuração de**papel : Define os ACLs de entrada com base no modelo de serviço dos inquilinos. Por exemplo, se um inquilino tiver uma extremidade frontal web na porta 80 em um determinado VM, a porta 80 é aberta para todos os endereços IP. Se o VM tiver um papel de trabalhador a desempenhar, o papel do trabalhador é aberto apenas ao VM dentro do mesmo inquilino.

**Firewall de anfitrião nativo**: Azure Service Fabric e Azure Storage funcionam num SISTEMA nativo, que não tem hipervisor e, portanto, o Windows Firewall está configurado com os dois conjuntos de regras anteriores.

**Firewall hospedeiro**: A firewall do anfitrião protege a partição do hospedeiro, que executa o hipervisor. As regras estão programadas para permitir que apenas o FC e as caixas de salto falem com a partição anfitriã numa porta específica. As outras exceções são permitir a resposta da DHCP e as respostas dns. O Azure usa um ficheiro de configuração da máquina, que contém um modelo de regras de firewall para a partição do anfitrião. Existe também uma exceção de firewall de anfitrião que permite que os VMs comuniquem para hospedar componentes, servidor de arame e servidor de metadados, através de protocolos/portas específicos.

**Firewall do hóspede**: A peça do Windows Firewall do SISTEMA convidado, que é configurável pelos clientes em VMs e armazenamento do cliente.

Funcionalidades de segurança adicionais que são incorporadas nas capacidades do Azure incluem:

- Componentes de infraestrutura que são atribuídos endereços IP que são de DIPs. Um intruso na internet não pode dirigir o tráfego a esses endereços porque não chegaria à Microsoft. Os routers de gateway da Internet filtram pacotes que são endereçados exclusivamente a endereços internos, para que não entrassem na rede de produção. Os únicos componentes que aceitam o tráfego que é direcionado para VIPs são os equilibradores de carga.
- As firewalls que são implementadas em todos os nós internos têm três considerações de arquitetura de segurança primária para qualquer cenário:

   - As firewalls são colocadas atrás do equilibrador de carga e aceitam pacotes de qualquer lugar. Estes pacotes destinam-se a ser expostos externamente e corresponderiam às portas abertas numa firewall de perímetro tradicional.
   - As firewalls aceitam pacotes apenas a partir de um conjunto limitado de endereços. Esta consideração faz parte da estratégia de fundo defensiva contra os ataques de DDoS. Tais ligações são criptograficamente autenticadas.
   - As firewalls só podem ser acedidas a partir de nós internos selecionados. Aceitam pacotes apenas a partir de uma lista enumerada de endereços IP de origem, todos eles DIPs dentro da rede Azure. Por exemplo, um ataque à rede corporativa poderia direcionar pedidos para estes endereços, mas os ataques seriam bloqueados a menos que o endereço de origem do pacote fosse um da lista enumerada dentro da rede Azure.
     - O router de acesso no perímetro bloqueia pacotes de saída que são endereçados a um endereço que está dentro da rede Azure devido às suas rotas estáticas configuradas.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que a Microsoft faz para garantir a infraestrutura Azure, consulte:

- [Instalações, instalações e segurança física](physical-security.md)
- [Disponibilidade de infraestrutura Azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informação azul](infrastructure-components.md)
- [Arquitetura da rede Azure](infrastructure-network.md)
- [Funcionalidades de segurança da Base de Dados Azure SQL](infrastructure-sql.md)
- [Operações e gestão de produção da Azure](infrastructure-operations.md)
- [Monitorização das infraestruturas do Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura azul](infrastructure-integrity.md)
- [Proteção de dados do cliente Azure](protection-customer-data.md)
