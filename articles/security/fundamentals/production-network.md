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
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 7c0748e4ff1531649274834cb1e602c228f102e8
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726696"
---
# <a name="the-azure-production-network"></a>A rede de produção do Azure
Os usuários da rede de produção do Azure incluem clientes externos que acessam seus próprios aplicativos do Azure e a equipe de suporte interna do Azure que gerencia a rede de produção. Este artigo discute os métodos de acesso de segurança e os mecanismos de proteção para estabelecer conexões com a rede de produção do Azure.

## <a name="internet-routing-and-fault-tolerance"></a>Roteamento de Internet e tolerância a falhas
Uma infraestrutura de DNS (serviço de nomes de domínio) interna e externa globalmente redundante, combinada com vários clusters de servidores DNS primários e secundários, fornece tolerância a falhas. Ao mesmo tempo, os controles de segurança de rede do Azure adicionais, como o Netscaler, são usados para impedir ataques de DDoS (negação de serviço distribuído) e proteger a integridade dos serviços DNS do Azure.

Os servidores DNS do Azure estão localizados em vários recursos do datacenter. A implementação do DNS do Azure incorpora uma hierarquia de servidores DNS secundários e primários para resolver publicamente os nomes de domínio do cliente do Azure. Os nomes de domínio geralmente são resolvidos para um endereço CloudApp.net, que encapsula o endereço VIP (IP virtual) para o serviço do cliente. Exclusivo do Azure, o VIP que corresponde ao endereço IP dedicado interno (DIP) da tradução do locatário é feito pelos balanceadores de carga da Microsoft responsáveis pelo VIP.

O Azure é hospedado em data centers do Azure distribuídos geograficamente dentro dos EUA, e ele foi criado em plataformas de roteamento de ponta que implementam padrões arquitetônicos robustos e escalonáveis. Entre os recursos notáveis estão:

- Engenharia de tráfego baseada em MPLS (comutação de rótulo multiprotocolo), que fornece utilização eficiente de links e degradação normal do serviço se houver uma interrupção.
- As redes são implementadas com arquiteturas de redundância "necessidade mais uma" (N + 1) ou melhor.
- Externamente, os data centers são servidos por circuitos de rede dedicados e de alta largura de banda que conectam com redundância as propriedades com mais de 1.200 provedores de serviços de Internet globalmente em vários pontos de emparelhamento. Essa conexão fornece exceder 2.000 gigabytes por segundo (GBps) de capacidade de borda.

Como a Microsoft possui seus próprios circuitos de rede entre data centers, esses atributos ajudam a oferta do Azure para obter disponibilidade de rede de 99,9 +% sem a necessidade de provedores de serviços de Internet de terceiros tradicionais.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Conexão à rede de produção e a firewalls associados
A política de fluxo de tráfego da Internet de rede do Azure direciona o tráfego para a rede de produção do Azure que está localizada no datacenter regional mais próximo dentro dos EUA. Como os data centers de produção do Azure mantêm a arquitetura de rede e o hardware consistentes, a descrição do fluxo de tráfego a seguir aplica-se de forma consistente a todos os data centers.

Depois que o tráfego da Internet para o Azure é roteado para o datacenter mais próximo, uma conexão é estabelecida com os roteadores de acesso. Esses roteadores de acesso servem para isolar o tráfego entre os nós do Azure e as VMs instanciadas pelo cliente. Os dispositivos de infraestrutura de rede nos locais de acesso e de borda são os pontos de limite nos quais os filtros de entrada e saída são aplicados. Esses roteadores são configurados por meio de uma ACL (lista de controle de acesso) em camadas para filtrar o tráfego de rede indesejado e aplicar limites de taxa de tráfego, se necessário. O tráfego permitido pela ACL é roteado para os balanceadores de carga. Os roteadores de distribuição são projetados para permitir apenas endereços IP aprovados pela Microsoft, fornecer anti-falsificação e estabelecer conexões TCP que usam ACLs.

Os dispositivos externos de balanceamento de carga estão localizados atrás dos roteadores de acesso para executar a conversão de endereços de rede (NAT) de IPs roteáveis pela Internet para IPs internos do Azure. Os dispositivos também roteiam pacotes para portas e IPs internos de produção válidos e atuam como um mecanismo de proteção para limitar a exposição do espaço de endereço de rede de produção interno.

Por padrão, a Microsoft impõe o protocolo HTTPS para todo o tráfego transmitido aos navegadores da Web dos clientes, incluindo a entrada e todo o tráfego depois. O uso do TLS v 1.2 permite que um túnel seguro para o tráfego flua. As ACLs em roteadores de acesso e de núcleo garantem que a origem do tráfego seja consistente com o esperado.

Uma distinção importante nessa arquitetura, quando comparada à arquitetura de segurança tradicional, é que não há firewalls de hardware dedicados, dispositivos de prevenção ou detecção de intrusão especializados ou outros dispositivos de segurança que normalmente são esperado antes que as conexões sejam feitas ao ambiente de produção do Azure. Os clientes geralmente esperam esses dispositivos de firewall de hardware na rede do Azure; no entanto, nenhum é empregado no Azure. Quase exclusivamente, esses recursos de segurança são criados no software que executa o ambiente do Azure para fornecer mecanismos de segurança robustos e com várias camadas, incluindo recursos de firewall. Além disso, o escopo do limite e o acúmulo associado de dispositivos de segurança críticos é mais fácil de gerenciar e inventariar, conforme mostrado na ilustração anterior, porque ele é gerenciado pelo software que está executando o Azure.

## <a name="core-security-and-firewall-features"></a>Principais recursos de segurança e firewall
O Azure implementa recursos de firewall e segurança de software robustos em vários níveis para impor recursos de segurança que geralmente são esperados em um ambiente tradicional para proteger o limite de autorização de segurança principal.

### <a name="azure-security-features"></a>Recursos de segurança do Azure
O Azure implementa firewalls de software baseados em host dentro da rede de produção. Vários recursos principais de segurança e firewall residem no ambiente principal do Azure. Esses recursos de segurança refletem uma estratégia de defesa aprofundada dentro do ambiente do Azure. Os dados do cliente no Azure são protegidos pelos seguintes firewalls:

**Firewall do hipervisor (filtro de pacote)** : Esse firewall é implementado no hipervisor e configurado pelo agente do controlador de malha (FC). Esse firewall protege o locatário que é executado dentro da VM contra acesso não autorizado. Por padrão, quando uma VM é criada, todo o tráfego é bloqueado e, em seguida, o agente de FC adiciona regras e exceções no filtro para permitir o tráfego autorizado.

Duas categorias de regras são programadas aqui:

- **Configuração da máquina ou regras de infraestrutura**: Por padrão, toda a comunicação é bloqueada. Existem exceções que permitem que uma VM envie e receba informações de DNS e comunicações do protocolo DHCP e envie tráfego para a saída da Internet "pública" para outras VMs no cluster FC e no servidor de ativação do sistema operacional. Como a lista de permitidos das VMs de destinos de saída não inclui sub-redes de roteador do Azure e outras propriedades da Microsoft, as regras atuam como uma camada de defesa para elas.
- **Regras de arquivo de configuração de função**: Define as ACLs de entrada com base no modelo de serviço dos locatários. Por exemplo, se um locatário tiver um front-end da Web na porta 80 em uma determinada VM, a porta 80 será aberta para todos os endereços IP. Se a VM tiver uma função de trabalho em execução, a função de trabalho será aberta somente para a VM dentro do mesmo locatário.

**Firewall de host nativo**: O Azure Service Fabric e o armazenamento do Azure são executados em um sistema operacional nativo, que não tem um hipervisor e, portanto, o Firewall do Windows está configurado com os dois conjuntos de regras anteriores.

**Firewall do host**: O Firewall do host protege a partição de host, que executa o hipervisor. As regras são programadas para permitir que apenas as caixas FC e salto se comuniquem com a partição de host em uma porta específica. As outras exceções são permitir resposta DHCP e respostas DNS. O Azure usa um arquivo de configuração de máquina, que contém um modelo de regras de firewall para a partição de host. Também existe uma exceção de firewall de host que permite que as VMs se comuniquem com componentes de host, servidor de transmissão e servidor de metadados, por meio de protocolos/portas específicos.

**Firewall de convidado**: A parte do firewall do Windows do sistema operacional convidado, que é configurável por clientes em VMs de clientes e armazenamento.

Os recursos de segurança adicionais que são criados nos recursos do Azure incluem:

- Componentes de infraestrutura que recebem endereços IP que são de DIPs. Um invasor na Internet não pode endereçar o tráfego para esses endereços porque ele não alcançaria a Microsoft. Os roteadores de gateway de Internet filtram pacotes que são endereçados exclusivamente a endereços internos, portanto, eles não entram na rede de produção. Os únicos componentes que aceitam o tráfego direcionado a VIPs são balanceadores de carga.
- Os firewalls implementados em todos os nós internos têm três considerações de arquitetura de segurança principais para qualquer cenário específico:

   - Os firewalls são colocados atrás do balanceador de carga e aceitam pacotes de qualquer lugar. Esses pacotes devem ser expostos externamente e corresponderão às portas abertas em um firewall de perímetro tradicional.
   - Os firewalls aceitam pacotes somente de um conjunto limitado de endereços. Essa consideração faz parte da estratégia defensiva aprofundada contra ataques de DDoS. Essas conexões são autenticadas criptograficamente.
   - Os firewalls podem ser acessados somente de nós internos selecionados. Eles aceitam pacotes somente de uma lista enumerada de endereços IP de origem, todos que são DIPs na rede do Azure. Por exemplo, um ataque na rede corporativa pode direcionar solicitações para esses endereços, mas os ataques seriam bloqueados, a menos que o endereço de origem do pacote fosse um na lista enumerada na rede do Azure.
     - O roteador de acesso no perímetro bloqueia os pacotes de saída que são endereçados a um endereço que está dentro da rede do Azure devido a suas rotas estáticas configuradas.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, consulte:

- [Instalações do Azure, local e segurança física](physical-security.md)
- [Disponibilidade de infraestrutura do Azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informações do Azure](infrastructure-components.md)
- [Arquitetura de rede do Azure](infrastructure-network.md)
- [Recursos de segurança do banco de dados SQL do Azure](infrastructure-sql.md)
- [Operações e gerenciamento de produção do Azure](infrastructure-operations.md)
- [Monitoramento de infraestrutura do Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura do Azure](infrastructure-integrity.md)
- [Proteção de dados do cliente do Azure](protection-customer-data.md)
