---
title: Funcionalidades de segurança da Base de Dados Azure SQL
description: Este artigo fornece uma descrição geral de como a Base de Dados Azure SQL protege os dados dos clientes em Azure.
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
ms.date: 03/09/2020
ms.author: terrylan
ms.openlocfilehash: ad6d3992f03802174eb03aa30b57b8d3dac1d6c4
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942959"
---
# <a name="azure-sql-database-security-features"></a>Funcionalidades de segurança da Base de Dados Azure SQL    
A Base de Dados Azure SQL fornece um serviço de base de dados relacional em Azure. Para proteger os dados dos clientes e fornecer fortes funcionalidades de segurança que os clientes esperam de um serviço de base de dados relacional, a SQL Database tem os seus próprios conjuntos de capacidades de segurança. Estas capacidades baseiam-se nos controlos herdados de Azure.

## <a name="security-capabilities"></a>Funcionalidades de segurança

### <a name="usage-of-the-tds-protocol"></a>Utilização do protocolo TDS
A Base de Dados Azure SQL suporta apenas o protocolo de fluxo de dados tabular (TDS), que exige que a base de dados seja acessível apenas sobre a porta padrão de TCP/1433.

### <a name="azure-sql-database-firewall"></a>Firewall de base de dados Azure SQL
Para ajudar a proteger os dados dos clientes, a Base de Dados Azure SQL inclui uma funcionalidade de firewall, que por padrão impede todo o acesso ao servidor de base de dados SQL, como mostrado abaixo.

![Firewall de base de dados Azure SQL](./media/infrastructure-sql/sql-database-firewall.png)

A firewall gateway pode limitar os endereços, o que permite aos clientes o controlo granular especificar gamas de endereços IP aceitáveis. A firewall concede acesso com base no endereço IP originário de cada pedido.

Os clientes podem obter a configuração da firewall utilizando um portal de gestão ou utilizando programáticamente a API de Gestão de Bases de Dados Azure SQL. A firewall de gateway da Base de Dados Azure SQL por padrão impede que todos os clientes acedam a instâncias de base de dados Azure SQL. Os clientes devem configurar o acesso utilizando listas de controlo de acesso (ACLs) para permitir as ligações de base de dados Azure SQL por endereços de internet, protocolos e números de porta de origem e destino.

### <a name="dosguard"></a>DoSGuard
Os ataques de negação de serviço (DoS) são reduzidos por um serviço de gateway SQL Database chamado DoSGuard. O DoSGuard rastreia ativamente os logins falhados dos endereços IP. Se existirem múltiplos logins falhados a partir de um endereço IP específico dentro de um período de tempo, o endereço IP está bloqueado de aceder a quaisquer recursos do serviço durante um período de tempo pré-definido.

Além disso, o portal de base de dados Azure SQL executa:

- As negociações de capacidade de canal segura para implementar ligações encriptadas tDS FIPS 140-2 validadas quando se conectam aos servidores de base de dados.
- Inspeção de pacotes TDS atemindo enquanto aceita ligações de clientes. O gateway valida as informações de ligação e passa nos pacotes TDS para o servidor físico apropriado com base no nome da base de dados especificado na cadeia de ligação.

O princípio geral para a segurança da rede da oferta de base de dados Azure SQL é permitir apenas a ligação e comunicação necessárias para permitir o funcionamento do serviço. Todas as outras portas, protocolos e ligações estão bloqueadas por defeito. As redes locais virtuais (VLANs) e ACLs são usadas para restringir as comunicações de rede através de redes de origem e destino, protocolos e números de portas.

Os mecanismos aprovados para implementar ACLs baseados em rede incluem ACLs em routers e equilibradores de carga. Estes mecanismos são geridos por networking Azure, firewall VM convidado e regras de firewall de gateway de gateway Azure SQL, que são configuradas pelo cliente.

## <a name="data-segregation-and-customer-isolation"></a>Segregação de dados e isolamento do cliente
A rede de produção azure está estruturada de modo a que os componentes do sistema acessíveis ao público sejam separados dos recursos internos. Existem fronteiras físicas e lógicas entre servidores web que fornecem acesso ao portal Azure virado para o público e à infraestrutura virtual azure subjacente, onde residem as instâncias de aplicação do cliente e os dados dos clientes.

Toda a informação acessível ao público é gerida dentro da rede de produção azure. A rede de produção está sujeita a mecanismos de autenticação e proteção de limites de dois fatores, utiliza o conjunto de firewall e funcionalidade de segurança descrito na secção anterior e utiliza funções de isolamento de dados, conforme se nota nas secções seguintes.

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>Sistemas não autorizados e isolamento do FC
Como o controlador de tecido (FC) é o orquestrador central do tecido Azure, existem controlos significativos para mitigar ameaças a ele, especialmente de FAs potencialmente comprometidos dentro das aplicações dos clientes. O FC não reconhece qualquer hardware cujainformação do dispositivo (por exemplo, endereço MAC) não esteja pré-carregada dentro do FC. Os servidores DHCP no FC têm listas configuradas de endereços MAC dos nós que estão dispostos a arrancar. Mesmo que os sistemas não autorizados estejam ligados, não são incorporados no inventário de tecidos e, portanto, não estão ligados ou autorizados a comunicar com qualquer sistema dentro do inventário do tecido. Isto reduz o risco de comunicação não autorizada de sistemas com o FC e de acesso ao VLAN e ao Azure.

### <a name="vlan-isolation"></a>Isolamento VLAN
A rede de produção azure é logicamente segregada em três VLANs primários:

- O VLAN principal: Interliga nódosos de cliente não confiáveis.
- O FC VLAN: Contém FCs fidedignos e sistemas de apoio.
- O dispositivo VLAN: Contém rede fidedigna e outros dispositivos de infraestrutura.

### <a name="packet-filtering"></a>Filtragem de pacotes
O IPFilter e as firewalls de software que são implementadas na raiz OS e os sos dos nós impõem restrições de conectividade e impedem o tráfego não autorizado entre VMs.

### <a name="hypervisor-root-os-and-guest-vms"></a>Hipervisor, root OS e VMs convidados
O isolamento da raiz dos VMs convidados e dos VMs convidados uns dos outros é gerido pelo hipervisor e pela raiz do OS.

### <a name="types-of-rules-on-firewalls"></a>Tipos de regras sobre firewalls
Uma regra é definida como:

{SRC IP, SRC Port, Destination IP, Destination Port, Destination Protocol, In/out, Stateful/Apátrida, Stateful, Stateful Flow Timeout}.

Os pacotes de caracteres ociosos sincronizados (SYN) só são permitidos dentro ou fora se qualquer uma das regras o permitir. Para a TCP, o Azure utiliza regras apátridas onde o princípio é que permite apenas todos os pacotes não-SYN dentro ou fora do VM. A premissa de segurança é que qualquer pilha hospedeira é resistente a ignorar um não-SYN se não tiver visto um pacote SYN anteriormente. O protocolo tCP em si é audato, e em combinação com a regra apátrida baseada em SYN consegue um comportamento geral de uma implementação imponente.

Para o Protocolo de Datagram do Utilizador (UDP), o Azure utiliza uma regra imponente. Sempre que um pacote uDP corresponde a uma regra, um fluxo inverso é criado na outra direção. Este fluxo tem um tempo insato.

Os clientes são responsáveis pela criação das suas próprias firewalls para além do que o Azure fornece. Aqui os clientes são capazes de definir as regras para o tráfego de entrada e saída.

### <a name="production-configuration-management"></a>Gestão de configuração de produção
As configurações seguras padrão são mantidas pelas respetivas equipas de operações na Base de Dados Azure e Azure SQL. Todas as alterações de configuração dos sistemas de produção são documentadas e rastreadas através de um sistema central de rastreio. As alterações de software e hardware são rastreadas através do sistema de rastreio central. As alterações de rede relacionadas com a ACL são rastreadas utilizando um serviço de gestão ACL.

Todas as alterações de configuração para o Azure são desenvolvidas e testadas no ambiente de encenação, e são posteriormente implantadas em ambiente de produção. As construções de software são revistas como parte dos testes. As verificações de segurança e privacidade são revistas como parte dos critérios da lista de verificação de entrada. As alterações são implementadas em intervalos programados pela respetiva equipa de implantação. Os lançamentos são revistos e assinados pelo respetivo pessoal da equipa de destacamento antes de serem implantados em produção.

As mudanças são monitorizadas para o sucesso. Num cenário de falha, a mudança é reposta para o seu estado anterior ou é implementado um hotfix para resolver a falha com a aprovação do pessoal designado. Source Depot, Git, TFS, Master Data Services (MDS), corredores, monitorização de segurança Azure, o FC e a plataforma WinFabric são usados para gerir, aplicar e verificar centralmente as configurações de configuração no ambiente virtual Do Azure.

Da mesma forma, as alterações de hardware e rede estabeleceram etapas de validação para avaliar a sua adesão aos requisitos de construção. As versões são revistas e autorizadas através de um conselho consultivo de mudança coordenada (CAB) dos respetivos grupos em toda a pilha.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o que a Microsoft faz para garantir a infraestrutura Azure, consulte:

- [Instalações, instalações e segurança física do Azure](physical-security.md)
- [Disponibilidade de infraestruturas azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informação Azure](infrastructure-components.md)
- [Arquitetura de rede Azure](infrastructure-network.md)
- [Rede de produção azure](production-network.md)
- [Operações e gestão de produção da Azure](infrastructure-operations.md)
- [Monitorização de infraestruturas Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura azure](infrastructure-integrity.md)
- [Proteção de dados dos clientes azure](protection-customer-data.md)
