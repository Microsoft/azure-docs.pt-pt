---
title: Segurança - Base de Dados Azure para MariaDB
description: Uma visão geral das funcionalidades de segurança na Base de Dados Azure para MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8f41fe1005e96b428337bc73b9d468962a079596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527831"
---
# <a name="security-in-azure-database-for-mariadb"></a>Segurança no Azure Database for MariaDB

Existem várias camadas de segurança disponíveis para proteger os dados na sua Base de Dados Azure para o servidor MariaDB. Este artigo descreve essas opções de segurança.

## <a name="information-protection-and-encryption"></a>Proteção de informação e encriptação

### <a name="in-transit"></a>Em trânsito
A Base de Dados Azure para MariaDB assegura os seus dados encriptando os dados em trânsito com a Transport Layer Security. A encriptação (SSL/TLS) é executada por padrão.

### <a name="at-rest"></a>Descanse em repouso
A Base de Dados Azure para o serviço MariaDB utiliza o módulo criptográfico validado FIPS 140-2 para encriptação de armazenamento de dados em repouso. Os dados, incluindo cópias de segurança, são encriptados no disco, com exceção dos ficheiros temporários criados durante a execução de consultas. O serviço utiliza a cifra AES de 256 bits incluída na encriptação de armazenamento Azure, e as chaves são geridas pelo sistema. A encriptação de armazenamento está sempre ativada e não pode ser desativada.


## <a name="network-security"></a>Segurança da rede
As ligações a uma Base de Dados Azure para servidor MariaDB são primeiramente direcionadas através de um portal regional. O gateway tem um IP acessível ao público, enquanto os endereços IP do servidor estão protegidos. Para mais informações sobre o portal, visite o [artigo arquitetura de conectividade.](concepts-connectivity-architecture.md)  

Uma base de dados Azure recém-criada para o servidor MariaDB tem uma firewall que bloqueia todas as ligações externas. Apesar de chegarem à porta de entrada, não estão autorizados a ligar-se ao servidor. 

### <a name="ip-firewall-rules"></a>Regras de firewall IP
As regras de firewall IP concedem acesso aos servidores com base no endereço IP originário de cada pedido. Consulte as regras da [firewall](concepts-firewall-rules.md) para obter mais informações.

### <a name="virtual-network-firewall-rules"></a>Regras de firewall de rede virtual
Os pontos finais do serviço de rede virtual estendem a sua conectividade de rede virtual sobre a espinha dorsal azure. Utilizando regras de rede virtuais, pode ativar a sua Base de Dados Azure para o servidor MariaDB para permitir ligações a partir de subredes selecionadas numa rede virtual. Para mais informações, consulte a visão geral do ponto final do serviço de [rede virtual](concepts-data-access-security-vnet.md).


## <a name="access-management"></a>Gestão de acesso

Ao criar a Base de Dados Azure para o servidor MariaDB, fornece credenciais para um utilizador administrador. Este administrador pode ser utilizado para criar utilizadores mariadb adicionais.


## <a name="threat-protection"></a>Proteção contra ameaças

Pode optar pela [Advanced Threat Protection](concepts-data-access-and-security-threat-protection.md) que deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de acesso ou exploração de servidores.

[A exploração de registos de auditoria](concepts-audit-logs.md) está disponível para acompanhar a atividade nas suas bases de dados. 


## <a name="next-steps"></a>Passos seguintes
- Ativar regras de firewall para [IPs](concepts-firewall-rules.md) ou [redes virtuais](concepts-data-access-security-vnet.md)