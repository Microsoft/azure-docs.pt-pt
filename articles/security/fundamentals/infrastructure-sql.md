---
title: Funcionalidades de segurança da Base de Dados Azure SQL
description: Este artigo fornece uma descrição geral de como a Azure SQL Database protege os dados dos clientes em Azure.
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
ms.openlocfilehash: e0e7089e7c674f324c2c3d293661c518b41731b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84021862"
---
# <a name="azure-sql-database-security-features"></a>Funcionalidades de segurança da Base de Dados Azure SQL    
A Azure SQL Database fornece um serviço de base de dados relacional em Azure. Para proteger os dados dos clientes e fornecer fortes funcionalidades de segurança que os clientes esperam de um serviço de base de dados relacional, a SQL Database tem os seus próprios conjuntos de capacidades de segurança. Estas capacidades baseiam-se nos controlos que são herdados de Azure.

## <a name="security-capabilities"></a>Funcionalidades de segurança

### <a name="usage-of-the-tds-protocol"></a>Utilização do protocolo TDS
A Azure SQL Database suporta apenas o protocolo de fluxo de dados tabular (TDS), que exige que a base de dados esteja acessível apenas sobre a porta predefinida de TCP/1433.

### <a name="azure-sql-database-firewall"></a>Firewall de base de dados Azure SQL
Para ajudar a proteger os dados dos clientes, a Base de Dados Azure SQL inclui uma funcionalidade de firewall, que por padrão impede todo o acesso à Base de Dados SQL, como mostrado abaixo.

![Firewall de base de dados Azure SQL](./media/infrastructure-sql/sql-database-firewall.png)

A firewall gateway pode limitar endereços, o que permite aos clientes o controlo granular especificar intervalos de endereços IP aceitáveis. A firewall concede acesso com base no endereço IP originário de cada pedido.

Os clientes podem alcançar a configuração de firewall utilizando um portal de gestão ou programáticamente utilizando a API de Gestão de Bases de Dados Azure SQL. A firewall de gateway de porta de dados Azure SQL por predefinição impede todo o acesso do cliente TDS à Base de Dados Azure SQL. Os clientes devem configurar o acesso utilizando listas de controlo de acesso (ACLs) para permitir ligações da Base de Dados Azure SQL através de endereços de internet de origem e destino, protocolos e números de porta.

### <a name="dosguard"></a>DoSGuard
Os ataques DoS (Denial of Service) são reduzidos por um serviço de gateway da Base de Dados SQL denominado DoSGuard. O DoSGuard monitoriza ativamente inícios de sessão sem êxito a partir dos endereços IP. Se houver vários logins falhados a partir de um endereço IP específico dentro de um período de tempo, o endereço IP é bloqueado de aceder a quaisquer recursos no serviço por um período de tempo pré-definido.

Além disso, o gateway Azure SQL Database realiza:

- Assegurar as negociações de capacidade do canal para implementar ligações encriptadas validadas TDS FIPS 140-2 quando se conecta aos servidores da base de dados.
- Inspeção de pacotes Stateful TDS enquanto aceita ligações de clientes. O gateway valida a informação de ligação e transmite os pacotes TDS para o servidor físico apropriado com base no nome da base de dados especificado na cadeia de ligação.

O princípio abrangente de segurança de rede da oferta Base de Dados SQL do Azure é permitir apenas a ligação e comunicação necessária para permitir a operação do serviço. Todos os restantes protocolos, portas e ligações são bloqueados por predefinição. As redes de áreas locais virtuais (VLANs) e ACLs são usadas para restringir as comunicações de rede por redes de origem e destino, protocolos e números de porta.

Os mecanismos aprovados para implementar ACLs baseados em rede incluem ACLs em routers e equilibradores de carga. Estes mecanismos são geridos pela rede Azure, firewall VM de hóspedes e regras de firewall de gateway de gateway Azure SQL, que são configuradas pelo cliente.

## <a name="data-segregation-and-customer-isolation"></a>Segregação de dados e isolamento do cliente
A rede de produção Azure está estruturada de modo a que os componentes do sistema acessíveis ao público sejam separados dos recursos internos. Existem fronteiras físicas e lógicas entre servidores web que fornecem acesso ao portal Azure virado para o público e à infraestrutura virtual Azure subjacente, onde residem as instâncias de aplicação do cliente e os dados dos clientes.

Toda a informação acessível ao público é gerida dentro da rede de produção Azure. A rede de produção está sujeita a mecanismos de autenticação e proteção de limites de dois fatores, utiliza o conjunto de funcionalidades de firewall e segurança que é descrito na secção anterior e utiliza funções de isolamento de dados como indicado nas secções seguintes.

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>Sistemas não autorizados e isolamento do FC
Como o controlador de tecido (FC) é o orquestrador central do tecido Azure, existem controlos significativos para mitigar as ameaças a ele, especialmente de ACA potencialmente comprometidas dentro das aplicações do cliente. O FC não reconhece nenhum hardware cuja informação do dispositivo (por exemplo, endereço MAC) não esteja pré-carregada dentro do FC. Os servidores DHCP do FC têm listas configuradas de endereços MAC dos nós que estão dispostos a iniciar. Mesmo que os sistemas não autorizados estejam ligados, não são incorporados no inventário de tecidos e, portanto, não estão ligados ou autorizados a comunicar com qualquer sistema dentro do inventário de tecidos. Isto reduz o risco de comunicação não autorizada de sistemas com o FC e o acesso ao VLAN e Azure.

### <a name="vlan-isolation"></a>Isolamento VLAN
A rede de produção Azure está logicamente segregada em três VLANs primários:

- O VLAN principal: Interliga nós de clientes não fidedidos.
- O FC VLAN: Contém FCs de confiança e sistemas de suporte.
- O dispositivo VLAN: Contém rede fidedigna e outros dispositivos de infraestrutura.

### <a name="packet-filtering"></a>Filtragem de pacotes
O IPFilter e as firewalls de software que são implementadas no sistema operativo raiz e o SO convidado dos nós impõem restrições de conectividade e impedem o tráfego não autorizado entre VMs.

### <a name="hypervisor-root-os-and-guest-vms"></a>Hipervisor, raiz de OS e VMs convidados
O isolamento do sistema operativo raiz dos VMs e dos VMs convidados é gerido pelo hipervisor e pelo sistema operativo raiz.

### <a name="types-of-rules-on-firewalls"></a>Tipos de regras sobre firewalls
Uma regra é definida como:

{Src IP, Src Port, Destination IP, Destination Port, Destination Protocol, In/out, Stateful/Stateless, Stateful Flow Timeout}.

Os pacotes de caracteres inativos sincronizados (SYN) só são permitidos dentro ou fora se alguma das regras o permitir. Para a TCP, a Azure utiliza regras apátridas onde o princípio é que permite apenas todos os pacotes não SYN dentro ou fora do VM. A premissa de segurança é que qualquer pilha de hospedeiro é resiliente de ignorar um não-SYN se não tiver visto um pacote SYN anteriormente. O protocolo TCP em si é imponente, e em combinação com a regra baseada no sNU apátrida, consegue um comportamento geral de uma implementação imponente.

Para o Protocolo de Datagrama do Utilizador (UDP), o Azure utiliza uma regra imponente. Sempre que um pacote de UDP corresponde a uma regra, um fluxo inverso é criado na outra direção. Este fluxo tem um tempo limite incorporado.

Os clientes são responsáveis pela criação das suas próprias firewalls para além do que o Azure fornece. Aqui os clientes são capazes de definir as regras para o tráfego de entrada e saída.

### <a name="production-configuration-management"></a>Gestão da configuração da produção
As configurações standard de segurança são mantidas pelas respetivas equipas de operações na Base de Dados Azure e Azure SQL. Todas as alterações de configuração dos sistemas de produção são documentadas e rastreadas através de um sistema central de rastreio. As alterações de software e hardware são rastreadas através do sistema central de rastreio. As alterações de rede relacionadas com a ACL são rastreadas utilizando um serviço de gestão ACL.

Todas as alterações de configuração do Azure são desenvolvidas e testadas no ambiente de preparação, e são posteriormente implantadas em ambiente de produção. As construções de software são revistas como parte dos testes. Os controlos de segurança e privacidade são revistos como parte dos critérios da lista de verificação de entrada. As alterações são implementadas em intervalos programados pela respetiva equipa de implantação. Os lançamentos são revistos e assinados pelo respetivo pessoal da equipa de destacamento antes de serem colocados em produção.

As mudanças são monitorizadas para o sucesso. Num cenário de falha, a mudança é revertida para o seu estado anterior ou é implantado um hotfix para resolver a falha com a aprovação do pessoal designado. Source Depot, Git, TFS, Master Data Services (MDS), corredores, monitorização de segurança Azure, o FC e a plataforma WinFabric são utilizados para gerir, aplicar e verificar as definições de configuração no ambiente virtual Azure.

Da mesma forma, as alterações de hardware e de rede estabeleceram medidas de validação para avaliar a sua adesão aos requisitos de construção. As versões são revistas e autorizadas através de um conselho consultivo de mudança coordenada (CAB) dos respetivos grupos em toda a pilha.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que a Microsoft faz para garantir a infraestrutura Azure, consulte:

- [Instalações, instalações e segurança física](physical-security.md)
- [Disponibilidade de infraestrutura Azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informação azul](infrastructure-components.md)
- [Arquitetura da rede Azure](infrastructure-network.md)
- [Rede de produção Azure](production-network.md)
- [Operações e gestão de produção da Azure](infrastructure-operations.md)
- [Monitorização das infraestruturas do Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura azul](infrastructure-integrity.md)
- [Proteção de dados do cliente Azure](protection-customer-data.md)
