---
title: Arquitetura de rede do Azure
description: Este artigo fornece uma descrição geral da rede de infraestrutura de Microsoft Azure.
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
ms.date: 02/20/2019
ms.author: terrylan
ms.openlocfilehash: c4756c36c2243840df69f3696e7ddac3628f3a00
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727179"
---
# <a name="azure-network-architecture"></a>Arquitetura de rede do Azure
A arquitetura de rede do Azure segue uma versão modificada do modelo de núcleo/distribuição/acesso padrão do setor, com camadas de hardware distintas. As camadas incluem:

- Núcleo (roteadores do Datacenter)
- Distribuição (roteadores de acesso e agregação de L2). A camada de distribuição separa o roteamento L3 da alternância de L2.
- Acesso (comutadores de host L2)

A arquitetura de rede tem dois níveis de comutadores de camada 2. Uma camada agrega o tráfego da outra camada. A segunda camada executa um loop para incorporar a redundância. A arquitetura fornece uma superfície de VLAN mais flexível e melhora o dimensionamento de porta. A arquitetura mantém o L2 e L3 distintos, o que permite o uso de hardware em cada uma das camadas distintas na rede e minimiza a falha em uma camada de afetar as outras camadas. O uso de troncos permite o compartilhamento de recursos, como a conectividade com a infraestrutura L3.

## <a name="network-configuration"></a>Configuração de rede
A arquitetura de rede de um cluster do Azure em um datacenter consiste nos seguintes dispositivos:

- Roteadores (datacenter, roteador de acesso e roteadores de folha de borda)
- Comutadores (agregação e comutadores de topo de rack)
- CMs Digi
- Unidades de distribuição de energia

O Azure tem duas arquiteturas separadas. Alguns clientes do Azure e serviços compartilhados existentes residem na DLA (arquitetura de LAN padrão), enquanto novas regiões e clientes virtuais residem na arquitetura Quantum 10 (P10). A arquitetura DLA é um design de árvore tradicional, com roteadores de acesso ativo/passivo e ACLs (listas de controle de acesso) de segurança aplicadas aos roteadores de acesso. A arquitetura Quantum 10 é um design de fechamento/malha de roteadores, em que as ACLs não são aplicadas nos roteadores. Em vez disso, as ACLs são aplicadas abaixo do roteamento, por meio do SLB (balanceamento de carga de software) ou de VLANs definidas pelo software.

O diagrama a seguir fornece uma visão geral de alto nível da arquitetura de rede em um cluster do Azure:

![Diagrama da rede do Azure](./media/infrastructure-network/network-arch.png)

### <a name="quantum-10-devices"></a>Dispositivos Quantum 10
O design da Quantum 10 conduz a comutação de camada 3 espalhada por vários dispositivos em um design de FEC/malha. As vantagens do design do P10 incluem maior capacidade e maior capacidade de dimensionar a infraestrutura de rede existente. O design emprega roteadores de folha de borda, comutadores de linhagem e roteadores de topo de rack para passar o tráfego para os clusters entre várias rotas, permitindo a tolerância a falhas. O balanceamento de carga de software, em vez de dispositivos de hardware, lida com serviços de segurança, como conversão de endereços de rede.

### <a name="access-routers"></a>Roteadores de acesso
Os roteadores L3 de distribuição/acesso (ARs) executam a funcionalidade de roteamento primário para as camadas de distribuição e acesso. Esses dispositivos são implantados como um par e são o gateway padrão para sub-redes. Cada par AR pode dar suporte a vários pares de comutador de agregação L2, dependendo da capacidade. O número máximo depende da capacidade do dispositivo, bem como dos domínios de falha. Um número típico é de três pares de comutador de agregação L2 por par AR.

### <a name="l2-aggregation-switches"></a>Opções de agregação de L2  
Esses dispositivos servem como um ponto de agregação para o tráfego de L2. Eles são a camada de distribuição para a malha L2 e podem lidar com grandes quantidades de tráfego. Como esses dispositivos agregam tráfego, eles exigem a funcionalidade 802.1 q e tecnologias de alta largura de banda, como agregação de porta e 10GE.

### <a name="l2-host-switches"></a>Comutadores de host L2
Os hosts se conectam diretamente a essas opções. Eles podem ser comutadores montados em rack ou implantações de chassi. O padrão 802.1 q permite a designação de uma VLAN como uma VLAN nativa, tratando essa VLAN como um enquadramento de Ethernet normal (sem marcas). Em circunstâncias normais, os quadros na VLAN nativa são transmitidos e recebidos não marcados em uma porta de tronco 802.1 q. Esse recurso foi projetado para migração para 802.1 q e compatibilidade com dispositivos com capacidade para não 802.1 q. Nessa arquitetura, somente a infraestrutura de rede usa a VLAN nativa.

Essa arquitetura especifica um padrão para a seleção de VLAN nativa. O padrão garante, sempre que possível, que os dispositivos AR tenham uma VLAN nativa exclusiva para cada tronco e o L2Aggregation para L2Aggregation troncos. Os troncos de comutador L2Aggregation a L2Host têm uma VLAN nativa não padrão.

### <a name="link-aggregation-8023ad"></a>Agregação de link (802.3 ad)
A agregação de link permite que vários links individuais sejam agrupados juntos e tratados como um único link lógico. Para facilitar a depuração operacional, o número usado para designar interfaces de canal de porta deve ser padronizado. O restante da rede usa o mesmo número em ambas as extremidades de um canal de porta.

Os números especificados para a opção L2Agg para L2Host são os números de canal de porta usados no lado do L2Agg. Como o intervalo de números é mais limitado no lado do L2Host, o padrão é usar os números 1 e 2 no lado do L2Host. Eles se referem ao canal de porta que vai para o lado "a" e o "b", respectivamente.

### <a name="vlans"></a>VLANs
A arquitetura de rede usa VLANs para agrupar servidores em um único domínio de difusão. Números de VLAN estão em conformidade com o padrão 802.1 q, que dá suporte a VLANs numeradas de 1 a 4094.

### <a name="customer-vlans"></a>VLANs do cliente
Você tem várias opções de implementação de VLAN que podem ser implantadas por meio do portal do Azure para atender às necessidades de separação e arquitetura da sua solução. Você implanta essas soluções por meio de máquinas virtuais. Para obter exemplos de arquitetura de referência do cliente, consulte arquiteturas de [referência do Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Arquitetura de borda
Os data centers do Azure são criados com base em infraestruturas de rede altamente redundantes e bem provisionadas. A Microsoft implementa redes dentro dos data centers do Azure com as arquiteturas de redundância "precisa de mais uma" (N + 1) ou melhor. Recursos de failover completo dentro e entre data centers ajudam a garantir a disponibilidade da rede e do serviço. Externamente, os data centers são servidos por circuitos de rede dedicados e de alta largura de banda. Esses circuitos conectam com redundância as propriedades com mais de 1200 provedores de serviços de Internet globalmente em vários pontos de emparelhamento. Isso proporciona exceder 2.000 Gbps de capacidade de borda potencial na rede.

A filtragem de roteadores na camada de borda e de acesso da rede do Azure fornece uma segurança bem estabelecida no nível de pacote e ajuda a impedir tentativas não autorizadas de se conectar ao Azure. Os roteadores ajudam a garantir que o conteúdo real dos pacotes contenha dados no formato esperado e esteja em conformidade com o esquema de comunicação de cliente/servidor esperado. O Azure implementa uma arquitetura em camadas, consistindo nos seguintes componentes de separação de rede e de controle de acesso:

- **Roteadores de borda.** Elas segregam o ambiente de aplicativos da Internet. Os roteadores de borda são projetados para fornecer proteção antifalsificação e limitar o acesso usando ACLs.
- **Roteadores de distribuição (acesso).** Eles permitem apenas endereços IP aprovados da Microsoft, fornecem antifalsificação e estabelecem conexões usando ACLs.

### <a name="ddos-mitigation"></a>Mitigação de DDOS
Ataques de DDoS (negação de serviço distribuído) continuam a apresentar uma ameaça real à confiabilidade do serviços online. À medida que os ataques se tornam mais direcionados e sofisticados, e como os serviços que a Microsoft fornece se tornam mais diversificados geograficamente, a identificação e a minimização do impacto desses ataques é uma prioridade alta.

A [proteção contra DDoS do Azure Standard](../../virtual-network/ddos-protection-overview.md) fornece defesa contra ataques de DDoS. Consulte [proteção contra DDoS do Azure: Práticas recomendadas e arquiteturas](ddos-best-practices.md) de referência para saber mais.

> [!NOTE]
> A Microsoft fornece proteção contra DDoS por padrão para todos os clientes do Azure.
>
>

## <a name="network-connection-rules"></a>Regras de conexão de rede
Em sua rede, o Azure implanta roteadores de borda que fornecem segurança no nível de pacote para evitar tentativas não autorizadas de se conectar ao Azure. Roteadores de borda garantem que o conteúdo real dos pacotes contenha dados no formato esperado e esteja em conformidade com o esquema de comunicação de cliente/servidor esperado.

Roteadores de borda segregam o ambiente de aplicativos da Internet. Esses roteadores são projetados para fornecer proteção antifalsificação e limitar o acesso usando ACLs. A Microsoft configura roteadores de borda usando uma abordagem de ACL em camadas para limitar os protocolos de rede que têm permissão para transitar os roteadores de borda e roteadores de acesso.

A Microsoft posiciona dispositivos de rede em locais de acesso e de borda para atuar como pontos de limite onde os filtros de entrada ou de saída são aplicados.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o que a Microsoft faz para ajudar a proteger a infraestrutura do Azure, consulte:

- [Instalações do Azure, local e segurança física](physical-security.md)
- [Disponibilidade de infraestrutura do Azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informações do Azure](infrastructure-components.md)
- [Rede de produção do Azure](production-network.md)
- [Recursos de segurança do banco de dados SQL do Azure](infrastructure-sql.md)
- [Operações e gerenciamento de produção do Azure](infrastructure-operations.md)
- [Monitoramento de infraestrutura do Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura do Azure](infrastructure-integrity.md)
- [Proteção de dados do cliente do Azure](protection-customer-data.md)


