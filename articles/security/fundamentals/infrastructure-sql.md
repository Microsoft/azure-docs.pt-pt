---
title: Recursos de segurança do banco de dados SQL do Azure
description: Este artigo fornece uma descrição geral de como o banco de dados SQL do Azure protege os dados do cliente no Azure.
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
ms.openlocfilehash: 74b0fa4643907493904e77ce333d1ec1dba01f49
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727110"
---
# <a name="azure-sql-database-security-features"></a>Recursos de segurança do banco de dados SQL do Azure    
O banco de dados SQL do Azure fornece um serviço de banco de dados relacional no Azure. Para proteger os dados do cliente e fornecer recursos de segurança fortes que os clientes esperam de um serviço de banco de dados relacional, o banco de dados SQL tem seus próprios conjuntos de recursos de segurança. Esses recursos se baseiam nos controles que são herdados do Azure.

## <a name="security-capabilities"></a>Funcionalidades de segurança

### <a name="usage-of-the-tds-protocol"></a>Uso do protocolo TDS
O banco de dados SQL do Azure dá suporte apenas ao protocolo TDS, que exige que o banco de dado seja acessível somente à porta padrão de TCP/1433.

### <a name="azure-sql-database-firewall"></a>Firewall do banco de dados SQL do Azure
Para ajudar a proteger os dados do cliente, o banco de dado SQL do Azure inclui uma funcionalidade de firewall, que, por padrão, impede todo o acesso ao servidor do banco de dados SQL, como mostrado abaixo.

![Firewall do banco de dados SQL do Azure](./media/infrastructure-sql/sql-database-firewall.png)

O Firewall do gateway pode limitar endereços, o que permite que os clientes controlem granulares para especificar intervalos de endereços IP aceitáveis. O firewall concede acesso com base no endereço IP de origem de cada solicitação.

Os clientes podem obter a configuração do firewall usando um portal de gerenciamento ou programaticamente usando a API REST de gerenciamento do banco de dados SQL do Azure. O Firewall do gateway do banco de dados SQL do Azure, por padrão, impede o acesso de TDS a instâncias do banco de dados SQL do Azure. Os clientes devem configurar o acesso usando ACLs (listas de controle de acesso) para permitir conexões de banco de dados SQL do Azure por endereços de Internet de origem e destino, protocolos e números de porta.

### <a name="dosguard"></a>DoSGuard
Ataques de DoS (negação de serviço) são reduzidos por um serviço de gateway de banco de dados SQL chamado DoSGuard. DoSGuard controla ativamente logons com falha de endereços IP. Se houver vários logons com falha de um endereço IP específico em um período de tempo, o endereço IP será impedido de acessar quaisquer recursos no serviço por um período de tempo predefinido.

Além disso, o gateway do banco de dados SQL do Azure executa:

- Proteja as negociações de capacidade de canal para implementar conexões criptografadas do TDS FIPS 140-2 validadas ao se conectar aos servidores de banco de dados.
- Inspeção de pacote TDS com estado enquanto aceita conexões de clientes. O gateway valida as informações de conexão e passa os pacotes TDS para o servidor físico apropriado com base no nome do banco de dados especificado na cadeia de conexão.

O princípio abrangente da segurança de rede da oferta do banco de dados SQL do Azure é permitir apenas a conexão e a comunicação necessárias para permitir que o serviço opere. Todas as outras portas, protocolos e conexões são bloqueadas por padrão. As VLANs (redes locais virtuais) e ACLs são usadas para restringir as comunicações de rede por redes de origem e de destino, protocolos e números de porta.

Os mecanismos que são aprovados para implementar ACLs baseadas em rede incluem ACLs em roteadores e balanceadores de carga. Esses mecanismos são gerenciados pela rede do Azure, pelo firewall da VM convidada e por regras de firewall do gateway do banco de dados SQL do Azure, que são configuradas pelo cliente.

## <a name="data-segregation-and-customer-isolation"></a>Segregação de dados e isolamento de clientes
A rede de produção do Azure é estruturada de forma que os componentes do sistema publicamente acessíveis sejam separados dos recursos internos. Há limites físicos e lógicos entre servidores Web que fornecem acesso ao portal do Azure voltado ao público e à infraestrutura virtual do Azure subjacente, onde as instâncias de aplicativo do cliente e os dados do cliente residem.

Todas as informações publicamente acessíveis são gerenciadas na rede de produção do Azure. A rede de produção está sujeita aos mecanismos de autenticação de dois fatores e de proteção de limite, usa o firewall e o conjunto de recursos de segurança descritos na seção anterior e usa as funções de isolamento de dados, conforme observado nas próximas seções.

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>Sistemas não autorizados e isolamento do FC
Como o controlador de malha (FC) é o orquestrador central da malha do Azure, controles significativos estão em vigor para reduzir as ameaças, especialmente do FAs potencialmente comprometido nos aplicativos do cliente. O FC não reconhece nenhum hardware cujas informações de dispositivo (por exemplo, endereço MAC) não sejam pré-carregadas no FC. Os servidores DHCP no FC configuraram listas de endereços MAC dos nós que estão dispostos a ser inicializados. Mesmo se os sistemas não autorizados estiverem conectados, eles não serão incorporados ao inventário de malha e, portanto, não conectados ou autorizados a se comunicarem com qualquer sistema dentro do inventário de malha. Isso reduz o risco de os sistemas não autorizados se comunicar com o FC e obter acesso à VLAN e ao Azure.

### <a name="vlan-isolation"></a>Isolamento de VLAN
A rede de produção do Azure é logicamente segregada em três VLANs primárias:

- A VLAN principal: Interconecta nós de clientes não confiáveis.
- A VLAN FC: Contém FCs confiável e sistemas de suporte.
- A VLAN do dispositivo: Contém rede confiável e outros dispositivos de infraestrutura.

### <a name="packet-filtering"></a>Filtragem de pacotes
O IPFilter e os firewalls de software que são implementados no so raiz e no SO convidado dos nós impõem restrições de conectividade e impedem o tráfego não autorizado entre as VMs.

### <a name="hypervisor-root-os-and-guest-vms"></a>Hipervisor, so raiz e VMs convidadas
O isolamento do sistema operacional raiz das VMs convidadas e das VMs convidadas uma da outra é gerenciado pelo hipervisor e pelo sistema operacional raiz.

### <a name="types-of-rules-on-firewalls"></a>Tipos de regras em firewalls
Uma regra é definida como:

{IP de src (Security Response Center), porta de src, IP de destino, porta de destino, protocolo de destino, entrada/saída, com estado/sem estado, tempo limite de fluxo com estado}.

Os pacotes de caracteres ociosos síncronos (SYN) só serão permitidos ou desativados se qualquer uma das regras permitir. Para o TCP, o Azure usa regras sem monitoração de estado em que o princípio é que ele permite apenas todos os pacotes não SYN dentro ou fora da VM. A premissa de segurança é que qualquer pilha de hosts é resiliente de ignorar um não-SYN se ele não tiver visto um pacote SYN anteriormente. O próprio protocolo TCP é com estado e, em combinação com a regra baseada em SYN sem estado, atinge um comportamento geral de uma implementação com estado.

Para UDP (User Datagram Protocol), o Azure usa uma regra com estado. Toda vez que um pacote UDP corresponde a uma regra, um fluxo inverso é criado na outra direção. Esse fluxo tem um tempo limite interno.

Os clientes são responsáveis por configurar seus próprios firewalls sobre o que o Azure fornece. Aqui, os clientes podem definir as regras para o tráfego de entrada e de saída.

### <a name="production-configuration-management"></a>Gerenciamento de configuração de produção
As configurações seguras padrão são mantidas pelas respectivas equipes de operações no Azure e no banco de dados SQL do Azure. Todas as alterações de configuração em sistemas de produção são documentadas e controladas por meio de um sistema de controle central. As alterações de software e hardware são controladas por meio do sistema de controle central. As alterações de rede relacionadas à ACL são rastreadas usando um serviço de gerenciamento de ACL.

Todas as alterações de configuração no Azure são desenvolvidas e testadas no ambiente de preparo e, depois disso, são implantadas no ambiente de produção. As compilações de software são revisadas como parte do teste. As verificações de segurança e privacidade são revisadas como parte dos critérios de lista de verificação de entrada. As alterações são implantadas em intervalos agendados pela respectiva equipe de implantação. As versões são revisadas e desconectadas pela respectiva equipe de implantação antes de serem implantadas na produção.

As alterações são monitoradas para obter êxito. Em um cenário de falha, a alteração é revertida para seu estado anterior ou um hotfix é implantado para resolver a falha com a aprovação da equipe designada. Depósito de origem, git, TFS, Master Data Services (MDS), executores, monitoramento de segurança do Azure, o FC e a plataforma WinFabric são usados para gerenciar, aplicar e verificar centralmente as definições de configuração no ambiente virtual do Azure.

Da mesma forma, as alterações de hardware e rede estabeleceram etapas de validação para avaliar sua adesão aos requisitos de compilação. As versões são revisadas e autorizadas por meio de um CAB (Conselho Consultivo de alterações) coordenado dos respectivos grupos na pilha.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, consulte:

- [Instalações do Azure, local e segurança física](physical-security.md)
- [Disponibilidade de infraestrutura do Azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informações do Azure](infrastructure-components.md)
- [Arquitetura de rede do Azure](infrastructure-network.md)
- [Rede de produção do Azure](production-network.md)
- [Operações e gerenciamento de produção do Azure](infrastructure-operations.md)
- [Monitoramento de infraestrutura do Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura do Azure](infrastructure-integrity.md)
- [Proteção de dados do cliente do Azure](protection-customer-data.md)


