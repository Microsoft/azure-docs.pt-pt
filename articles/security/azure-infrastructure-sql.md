---
title: Funcionalidades de segurança da base de dados SQL do Azure
description: Este artigo fornece uma descrição geral de como o SQL Database do Azure protege os dados do cliente no Azure.
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
ms.openlocfilehash: cd2ad16f910f5d2b3b801c8d54e9df7660751462
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62121661"
---
# <a name="azure-sql-database-security-features"></a>Funcionalidades de segurança da base de dados SQL do Azure    
Base de dados SQL do Azure fornece um serviço de base de dados relacionais no Azure. Para proteger os dados dos clientes e fornecer funcionalidades de segurança forte que clientes esperar de um serviço de base de dados relacional, a base de dados SQL tem seus próprios conjuntos de recursos de segurança. Esses recursos são criados após os controlos que são herdados do Azure.

## <a name="security-capabilities"></a>Funcionalidades de segurança

### <a name="usage-of-the-tds-protocol"></a>Utilização do protocolo TDS
Base de dados SQL do Azure suporta apenas o tabular data stream (TDS) protocolo, que requer a base de dados estar acessíveis através de apenas a predefinição porta de TCP/1433.

### <a name="azure-sql-database-firewall"></a>Firewall de base de dados SQL do Azure
Para ajudar a proteger os dados dos clientes, a base de dados SQL do Azure inclui uma funcionalidade de firewall, o que, por predefinição, impede todo o acesso ao servidor de base de dados SQL, conforme mostrado abaixo.

![Firewall de base de dados SQL do Azure][1]

O firewall do gateway pode limitar endereços, que permite aos clientes um controle granular especificar os intervalos de endereços IP aceitáveis. A firewall concede acesso com base no endereço IP de origem de cada pedido.

Os clientes podem obter a configuração de firewall ao utilizar um portal de gestão ou programaticamente usando a API de REST de gestão do Azure SQL da base de dados. A firewall do gateway de base de dados do Azure SQL por predefinição impede que todos os clientes de acesso recebido para instâncias de base de dados SQL do Azure. Os clientes tem de configurar acesso através de listas de controlo de acesso (ACLs) para permitir ligações de base de dados do Azure SQL, origem e endereços de internet de destino, protocolos e números de porta.

### <a name="dosguard"></a>DoSGuard
Ataques denial of service (DoS) são reduzidos por um serviço de gateway de base de dados SQL denominado DoSGuard. DoSGuard controla ativamente inícios de sessão falhados a partir de endereços IP. Se existirem vários inícios de sessão falhados de um endereço IP específico dentro de um período de tempo, o endereço IP está bloqueado de aceder a quaisquer recursos no serviço para um período de tempo predefinido.

Além disso, o gateway de base de dados do Azure SQL executa:

- Negociações de capacidade de canal seguro para implementar o TDS FIPS 140-2 validado ligações encriptadas para estabelecer a ligação para os servidores de base de dados.
- Inspeção de pacotes com monitoração de estado recebido enquanto aceita ligações de clientes. O gateway valida as informações de ligação e passa nos pacotes recebido para o servidor físico adequado com base no nome da base de dados especificado na cadeia de ligação.

O princípio abrangente para segurança de rede da oferta de base de dados do Azure SQL é permitir que apenas a ligação e a comunicação que é necessária para permitir que o serviço a funcionar. Todas as outras portas, protocolos e as ligações estão bloqueadas por predefinição. Redes de área local virtual (VLANs) e ACLs são usadas para restringir comunicações de rede, redes de origem e destino, protocolos e números de porta.

Mecanismos que são aprovados para implementar as ACLs baseadas em rede incluem as ACLs em routers e Balanceadores de carga. Estes mecanismos são geridos pelo sistema de rede do Azure, firewall de VM do convidado e regras de firewall do Azure SQL Database gateway, que são configuradas pelo cliente.

## <a name="data-segregation-and-customer-isolation"></a>Isolamento de cliente e segregação de dados
A rede de produção do Azure é estruturada de forma a que os componentes de sistema acessível publicamente são separados recursos internos. Existem limites físicos e lógicos entre servidores web que fornecem acesso ao portal do Azure destinados ao público e a infraestrutura do Azure subjacente virtual, onde residem os instâncias da aplicação de cliente e dados do cliente.

Todas as informações de publicamente acessíveis são gerenciadas dentro da rede de produção do Azure. A rede de produção está sujeito a autenticação de dois fatores e o limite de mecanismos de proteção, utiliza o conjunto de recursos de segurança e de firewall que é descrito na secção anterior e utiliza as funções de isolamento de dados conforme indicado nas próximas seções.

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>Sistemas não autorizados e isolar o FC
Uma vez que o controlador de malha (FC) é orquestradores centrais de recursos de infraestrutura do Azure, controles significativos foram in-loco para atenuar as ameaças, especialmente de FAs potencialmente comprometidos em aplicativos do cliente. O FC não reconhece qualquer hardware cujas informações de dispositivo (por exemplo, o endereço de MAC) não está previamente carregadas dentro do FC. Os servidores DHCP no FC configurou as listas de endereços MAC de nós são dispostos a efetuar o arranque. Mesmo que estejam ligados de sistemas não autorizados, eles não são incorporados ao inventário de recursos de infraestrutura e, portanto, não ligados ou autorizados a comunicar com qualquer sistema dentro do inventário de recursos de infraestrutura. Isso reduz o risco de não autorizados sistemas se comunicar com o FC e obter acesso à VLAN e do Azure.

### <a name="vlan-isolation"></a>Isolamento de VLAN
A rede de produção do Azure logicamente é segregada para três VLANs primários:

- A VLAN principal: Interconexões nós não fidedignos do cliente.
- A VLAN de FC: Contém fidedigna FCs e sistemas de suporte.
- O dispositivo VLAN: Contém rede fidedigna e outros dispositivos de infraestrutura.

### <a name="packet-filtering"></a>Filtragem de pacotes
O IPFilter e as firewalls de software que são implementadas no sistema operacional raiz e sistema operacional convidado de nós de impor restrições de conectividade e impedir que o tráfego entre VMs não autorizado.

### <a name="hypervisor-root-os-and-guest-vms"></a>Hipervisor, sistema operacional raiz e máquinas virtuais convidadas
O isolamento de sistema operacional raiz de máquinas virtuais convidadas e das máquinas virtuais convidadas umas das outras é gerido pelo hipervisor e o sistema operacional raiz.

### <a name="types-of-rules-on-firewalls"></a>Tipos de regras em firewalls
Uma regra é definida como:

{Security Response Center (Src), porta de Src, destino IP, porta de destino, destino protocolo IP, de entrada/saída, tempo limite de fluxo com monitoração de estado/sem monitoração de estado, com monitoração de estado}.

Pacotes de caráter síncrona de inativa (SYN) são permitidos dentro ou para fora, apenas se qualquer uma das regras permite que o faça. Para TCP, o Azure utiliza regras sem monitoração de estado em que o princípio é que ela permite que apenas todos os pacotes não-SYN para dentro ou fora da VM. A premissa de segurança é que qualquer pilha de host é resiliente de ignorar um SYN-não se ele é não visto como um pacote SYN anteriormente. O próprio protocolo TCP é com monitoração de estado e, em combinação com a regra com base em SYN sem monitoração de estado atinge um comportamento geral de uma implementação com monitoração de estado.

Para o protocolo de datagrama de utilizador (UDP), o Azure utiliza uma regra com monitorização de estado. Sempre que um pacote UDP corresponde a uma regra, um fluxo inverso é criado na outra direção. Este fluxo tem um tempo limite interno.

Os clientes são responsáveis pela configuração de suas próprias firewalls sobre o que o Azure oferece. Aqui os clientes podem definir as regras para tráfego de entrada e saída.

### <a name="production-configuration-management"></a>Gestão de configuração de produção
As configurações padrão de seguras são mantidas por equipes de respectivas operações no Azure e base de dados do Azure SQL. Todas as alterações de configuração em sistemas de produção são documentadas e controladas por meio de um sistema de controle central. Alterações de software e hardware são controladas através do sistema de controle central. Alterações de rede que estão relacionadas com a ACL são controladas usando um serviço de gestão de ACL.

Todas as alterações de configuração para o Azure são desenvolvidas e testadas no ambiente de teste e, posteriormente, são implementadas no ambiente de produção. Compilações de software são revisadas como parte do teste. Verificações de segurança e privacidade são revisadas como parte dos critérios de lista de verificação de entrada. As alterações são implementadas em intervalos agendados pela equipe de implantação correspondentes. Versões são analisadas e aprovadas pela equipa da Equipe a respetiva implementação antes de eles serem implementados em produção.

As alterações são monitorizadas para o sucesso. Num cenário de falha, a alteração é revertida para o estado anterior ou uma correção é implementada para resolver a falha com a aprovação, o pessoal designado. Depósito de origem, o Git, TFS, Master Data Services (MDS), concorrentes, monitorização de segurança do Azure, o FC e a plataforma do WinFabric são utilizados para gerir centralmente, aplicam-se e verifique se as definições de configuração do ambiente virtual do Azure.

Da mesma forma, as alterações de hardware e de rede tem estabelecido as etapas de validação para avaliar seus aderência aos requisitos de compilação. As versões são revistas e autorizadas por meio de um Conselho Consultivo de alterações de coordenada (CAB), dos respetivos grupos em toda a pilha.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, veja:

- [Recursos do Azure, no local e a segurança física](azure-physical-security.md)
- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Componentes de sistema de informações do Azure e limites](azure-infrastructure-components.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitorização de infraestrutura do Azure](azure-infrastructure-monitoring.md)
- [Integridade de infraestrutura do Azure](azure-infrastructure-integrity.md)
- [Proteção de dados do cliente do Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-sql/sql-database-firewall.png
