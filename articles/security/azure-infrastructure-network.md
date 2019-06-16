---
title: Arquitetura de rede do Azure
description: Este artigo fornece uma descrição geral da rede de infraestrutura do Microsoft Azure.
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
ms.date: 02/20/2019
ms.author: terrylan
ms.openlocfilehash: 48a7e52d4284e5c2db1d77d24d91fd4701aad8d7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60587160"
---
# <a name="azure-network-architecture"></a>Arquitetura de rede do Azure
A arquitetura de rede do Azure segue uma versão modificada do setor core/distribuição/acesso modelo padrão, com camadas de hardware distintos. As camadas incluem:

- Núcleo (routers do Centro de dados)
- Distribuição (roteadores de acesso e a agregação de L2). A camada de distribuição separa o encaminhamento de L3 da mudança de L2.
- Acesso (comutadores de anfitrião de L2)

A arquitetura de rede tem dois níveis de comutadores de camada 2. Tráfego de agregados de uma camada da outra camada. A segunda camada faz um loop para incorporar a redundância. A arquitetura fornece uma pegada VLAN mais flexível e melhora a porta de dimensionamento. A arquitetura mantém L2 e L3 distintos, que permite a utilização de hardware em cada uma das camadas distintas na rede e minimiza o índice de falhas numa camada afetem a outra camada (s). Permite a utilização de ramais da partilha, por exemplo, a conectividade com a infraestrutura de L3 de recursos.

## <a name="network-configuration"></a>Configuração da rede
A arquitetura de rede de um cluster do Azure num centro de dados é composta pelos seguintes dispositivos:

- Routers (Centro de dados, o roteador de acesso e os routers de folha de limite)
- Comutadores (comutadores de agregação e top-of-rack)
- Digi CMs
- Unidades de distribuição de energia

O Azure tem duas arquiteturas separadas. Alguns os clientes do Azure e serviços partilhados residem na arquitetura de rede local padrão (DLA), ao passo que as novas regiões e os clientes virtual residem na arquitetura de Quantum 10 (P10). A arquitetura DLA é uma estrutura de árvore tradicional, com roteadores ativo/passivo de acesso e segurança acesso listas de controle (ACLs) aplicadas para os routers de acesso. A arquitetura de Quantum 10 é uma estrutura de fechar/malha de roteadores, onde as ACLs não são aplicadas nos routers. Em vez disso, as ACLs são aplicadas abaixo o encaminhamento, através do Software de balanceamento de carga (SLB) ou VLANs definidas por software.

O diagrama seguinte fornece uma visão geral da arquitetura de rede dentro de um cluster do Azure:

![Diagrama de rede do Azure][1]

### <a name="quantum-10-devices"></a>Dispositivos de quantum 10
O design de 10 de Quantum realiza disseminação de camada 3 mudar ao longo de vários dispositivos de um design de Clos/malha. As vantagens do design P10 incluem a capacidade maior e maior capacidade de dimensionar a infraestrutura de rede existente. O design emprega routers de folha de limite, spine comutadores e routers de top-of-rack para passar o tráfego para clusters em várias rotas, permitindo a tolerância a falhas. Software balanceamento de carga, em vez de dispositivos de hardware, lida com os serviços de segurança, tais como a tradução de endereços de rede.

### <a name="access-routers"></a>Routers de acesso
Os routers de distribuição/acesso L3 (ARs) executam a funcionalidade de roteamento principal para as camadas de acesso e de distribuição. Estes dispositivos são implementados como um par e são a porta predefinida para sub-redes. Cada par de AR pode suportar vários pares de comutador de agregação L2, dependendo da capacidade. O número máximo depende da capacidade do dispositivo, bem como os domínios de falha. Um número de típico é três pares de comutador de agregação de L2 por par de AR.

### <a name="l2-aggregation-switches"></a>Comutadores de agregação de L2  
Estes dispositivos servem como um ponto de agregação para o tráfego de L2. Eles são a camada de distribuição para os recursos de infraestrutura de L2 e podem processar grandes quantidades de tráfego. Uma vez que estes dispositivos agregam tráfego, elas exigem 802.1q funcionalidades e tecnologias de largura de banda alta, como a agregação de porta e 10GE.

### <a name="l2-host-switches"></a>Comutadores de anfitrião de L2
Ligar anfitriões diretamente a esses comutadores. Eles podem ser montado em Bastidor comutadores ou implementações de chassis. 802\.1q padrão permite que a designação de uma VLAN como uma VLAN nativa, tratando essa VLAN como normal (não marcada) delimitação de quadros do Ethernet. Em circunstâncias normais, os quadros na VLAN nativo são transmitidos e recebidos não marcados num 802.1q porta de ramal. Esta funcionalidade foi projetada para migração para o 802.1q e compatibilidade com o não-802.1q dispositivos compatíveis com. Nesta arquitetura, a infraestrutura de rede utiliza a VLAN nativa.

Esta arquitetura Especifica um padrão para a seleção de VLAN nativo. O padrão garante que, sempre que possível, que os dispositivos de AR têm uma VLAN nativa, exclusiva para cada ramal e L2Aggregation para ramais de L2Aggregation da. L2Aggregation para ramais de comutador de L2Host ter uma VLAN nativa não predefinido.

### <a name="link-aggregation-8023ad"></a>Agregação de ligação (802.3ad)
Agregação de ligação permite que várias ligações individuais para ser agrupadas e tratadas como uma única ligação lógica. Para facilitar a depuração operacional, o número utilizado para designar as interfaces de canal de porta deve ser padronizado. O restante da rede utiliza o mesmo número em ambas as extremidades de um canal de porta.

Os números especificados para o L2Agg ao comutador L2Host são os números de canal de porta utilizados no lado do L2Agg. Uma vez que o intervalo de números é mais limitado na lateral L2Host, o padrão é utilizar números de 1 e 2 na lateral L2Host. Os quais se referem para o canal de porta vai a "a" e do lado do "b", respectivamente.

### <a name="vlans"></a>VLANs
A arquitetura de rede usa VLANs agrupar servidores em conjunto num único domínio de difusão. Números VLAN estão em conformidade com 802.1q padrão, que suporte VLANs numerados 1 – 4094.

### <a name="customer-vlans"></a>VLANs de cliente
Tem várias opções de implementação de VLAN pode implementar através do portal do Azure para satisfazer as necessidades de separação e arquitetura da sua solução. Implementar estas soluções através de máquinas virtuais. Para exemplos de arquitetura de referência do cliente, veja [arquiteturas de referência do Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Arquitetura do Edge
Os datacenters do Azure foram criados sobre infraestruturas de rede altamente redundantes e bem aprovisionado. Microsoft implementa redes dentro dos datacenters do Azure com "necessidade de mais um" (N + 1) as arquiteturas de redundância ou superior. Ativação pós-falha completa dentro e entre datacenters ajudá-lo a garantir a disponibilidade de rede e o serviço. Externamente, centros de dados são servidos pelo circuitos de rede dedicado, largura de banda alta. Estes circuitos adquiria ligar propriedades com mais de 1.200 fornecedores de serviços de internet globalmente em vários pontos de peering. Esta opção fornece em excesso de 2.000 Gbps da capacidade de borda potenciais através da rede.

A filtragem de roteadores na camada de acesso e de borda da rede do Azure fornece bem estabelecida segurança ao nível do pacote e ajuda a evitar tentativas não autorizadas para ligar ao Azure. Os routers ajudam a garantir que o conteúdo real dos pacotes contêm dados no formato esperado e estar em conformidade com o esquema de comunicação de cliente/servidor esperado. O Azure implementa uma arquitetura em camadas, que consiste dos seguintes segregação de rede e componentes de controle de acesso:

- **Routers de limite.** Estes segregar o ambiente de aplicativos da internet. Routers de periferia foram concebidas para fornecer proteção de spoof anti e limitar o acesso ao utilizar ACLs.
- **Routers de distribuição (acesso).** Estas permitem que apenas a Microsoft aprovado endereços IP, fornecem o antisspoofing e estabelecer ligações ao utilizar ACLs.

### <a name="ddos-mitigation"></a>Mitigação de DDOS
Ataques denial of service (DDoS) distribuídos continuar a apresentar uma ameaça real à fiabilidade dos serviços online. Como a ataques se tornam mais direcionadas e sofisticadas e como os serviços a Microsoft fornece a se tornarem mais geograficamente dispersas, identificar e minimizar o impacto desses ataques é uma prioridade alta.

[Padrão de proteção de DDoS do Azure](../virtual-network/ddos-protection-overview.md) fornece defesa contra ataques DDoS. Consulte [proteção contra DDoS do Azure: Melhores práticas e arquiteturas de referência](azure-ddos-best-practices.md) para saber mais.

> [!NOTE]
> A Microsoft fornece proteção contra DDoS por predefinição para todos os clientes do Azure.
>
>

## <a name="network-connection-rules"></a>Regras de ligação de rede
Na sua rede, o Azure implementa routers de limite que fornecem segurança ao nível do pacote para evitar tentativas não autorizadas para ligar ao Azure. Routers de periferia Certifique-se de que o conteúdo real dos pacotes contêm dados no formato esperado e estar em conformidade com o esquema de comunicação de cliente/servidor esperado.

Routers de periferia segregar o ambiente de aplicativos da internet. Estes routers foram concebidos para fornecer proteção de spoof anti e limitar o acesso ao utilizar ACLs. Microsoft configura os routers de periferia com uma abordagem ACL em camadas, limite a protocolos de rede que têm permissão para os routers de limite de trânsito e routers de acesso.

Microsoft posiciona dispositivos de rede nas localizações de acesso e o edge, para funcionar como pontos de limites nos quais os filtros de entrada ou saída são aplicados.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o que a Microsoft faz para ajudar a proteger a infraestrutura do Azure, veja:

- [Recursos do Azure, no local e a segurança física](azure-physical-security.md)
- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Componentes de sistema de informações do Azure e limites](azure-infrastructure-components.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Funcionalidades de segurança da base de dados SQL do Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitorização de infraestrutura do Azure](azure-infrastructure-monitoring.md)
- [Integridade de infraestrutura do Azure](azure-infrastructure-integrity.md)
- [Proteção de dados do cliente do Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-network/network-arch.png
