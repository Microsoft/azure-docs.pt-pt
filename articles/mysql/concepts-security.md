---
title: Segurança - Base de Dados Azure para MySQL
description: Uma visão geral das funcionalidades de segurança na Base de Dados Azure para o MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5caed64beb43cbb1e884a3dde6bc0052c617f83e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89378785"
---
# <a name="security-in-azure-database-for-mysql"></a>Segurança na Base de Dados do Azure para MySQL

Existem várias camadas de segurança disponíveis para proteger os dados na base de dados Azure para o servidor MySQL. Este artigo descreve essas opções de segurança.

## <a name="information-protection-and-encryption"></a>Proteção de informação e encriptação

### <a name="in-transit"></a>Em trânsito
A Azure Database for MySQL protege os seus dados encriptando dados em trânsito com a Segurança da Camada de Transporte. A encriptação (SSL/TLS) é aplicada por padrão.

### <a name="at-rest"></a>Em repouso
A Base de Dados Azure para o serviço MySQL utiliza o módulo criptográfico validado FIPS 140-2 para encriptação de armazenamento de dados em repouso. Os dados, incluindo cópias de segurança, são encriptados no disco, incluindo os ficheiros temporários criados durante a execução de consultas. O serviço utiliza a cifra AES de 256 bits incluída na encriptação de armazenamento Azure, e as teclas são geridas pelo sistema. A encriptação de armazenamento está sempre ativada e não pode ser desativada.


## <a name="network-security"></a>Segurança da rede
As ligações a uma base de dados Azure para o servidor MySQL são primeiramente encaminhada através de um gateway regional. O gateway tem um IP acessível ao público, enquanto os endereços IP do servidor estão protegidos. Para mais informações sobre a porta de entrada, visite o [artigo arquitetura de conectividade.](concepts-connectivity-architecture.md)  

Uma base de dados Azure recentemente criada para o servidor MySQL tem uma firewall que bloqueia todas as ligações externas. Apesar de chegarem ao portal, não estão autorizados a ligar-se ao servidor. 

### <a name="ip-firewall-rules"></a>Regras de firewall IP
As regras de firewall IP concedem acesso a servidores com base no endereço IP originário de cada pedido. Consulte a [visão geral](concepts-firewall-rules.md) das regras da firewall para obter mais informações.

### <a name="virtual-network-firewall-rules"></a>Regras de firewall de rede virtual
Os pontos finais do serviço de rede virtual estendem a sua conectividade de rede virtual sobre a espinha dorsal do Azure. Utilizando regras de rede virtuais, pode ativar a sua Base de Dados Azure para o servidor MySQL para permitir ligações a partir de sub-redes selecionadas numa rede virtual. Para obter mais informações, consulte a [visão geral](concepts-data-access-and-security-vnet.md)do ponto final do serviço de rede virtual .

### <a name="private-ip"></a>IP privado
O Private Link permite-lhe ligar-se à sua Base de Dados Azure para o MySQL em Azure através de um ponto final privado. O Azure Private Link essencialmente traz os serviços do Azure dentro da sua Rede Privada Virtual (VNet). Os recursos de PaaS podem ser acedidos através do endereço IP privado, como qualquer outro recurso na VNet. Para mais informações,consulte a [visão geral](concepts-data-access-security-private-link.md) do link privado

## <a name="access-management"></a>Gestão de acesso

Ao criar a Base de Dados Azure para o servidor MySQL, fornece credenciais para um utilizador administrador. Este administrador pode ser usado para criar utilizadores adicionais do MySQL.


## <a name="threat-protection"></a>Proteção contra ameaças

Pode optar pela [Advanced Threat Protection](concepts-data-access-and-security-threat-protection.md) que deteta atividades anómalas que indiquem tentativas invulgares e potencialmente nocivas de aceder ou explorar servidores.

[O registo de auditoria](concepts-audit-logs.md) está disponível para acompanhar a atividade nas suas bases de dados. 


## <a name="next-steps"></a>Passos seguintes
- Ativar regras de firewall para [IPs](concepts-firewall-rules.md) ou [redes virtuais](concepts-data-access-and-security-vnet.md)
