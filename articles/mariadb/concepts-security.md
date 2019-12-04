---
title: Segurança-banco de dados do Azure para MariaDB
description: Uma visão geral dos recursos de segurança no banco de dados do Azure para MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d23eabdacc57a3f5a10d9e3b132a6daac42fbd4d
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772155"
---
# <a name="security-in-azure-database-for-mariadb"></a>Segurança no Azure Database for MariaDB

Há várias camadas de segurança que estão disponíveis para proteger os dados no banco de dado do Azure para o servidor MariaDB. Este artigo descreve essas opções de segurança.

## <a name="information-protection-and-encryption"></a>Proteção de informações e criptografia

### <a name="in-transit"></a>Em trânsito
O banco de dados do Azure para o MariaDB protege seus dados criptografando os dados em trânsito com a segurança da camada de transporte. A criptografia (SSL/TLS) é imposta por padrão.

### <a name="at-rest"></a>Em repouso
O banco de dados do Azure para serviço MariaDB usa o módulo de criptografia FIPS 140-2 validado para criptografia de armazenamento de dados em repouso. Os dados, incluindo backups, são criptografados no disco, com exceção dos arquivos temporários criados durante a execução de consultas. O serviço usa a codificação AES de 256 bits incluída na criptografia de armazenamento do Azure e as chaves são gerenciadas pelo sistema. A encriptação de armazenamento está sempre ativada e não pode ser desativada.


## <a name="network-security"></a>Segurança da rede
As conexões com um banco de dados do Azure para o servidor MariaDB são roteadas primeiro por meio de um gateway regional. O gateway tem um IP acessível publicamente, enquanto os endereços IP do servidor são protegidos. Para obter mais informações sobre o gateway, visite o [artigo arquitetura de conectividade](concepts-connectivity-architecture.md).  

Um banco de dados do Azure criado recentemente para o servidor MariaDB tem um firewall que bloqueia todas as conexões externas. Embora eles atinjam o gateway, eles não têm permissão para se conectar ao servidor. 

### <a name="ip-firewall-rules"></a>Regras de firewall de IP
As regras de firewall IP concedem acesso a servidores com base no endereço IP de origem de cada solicitação. Consulte a [visão geral das regras de firewall](concepts-firewall-rules.md) para obter mais informações.

### <a name="virtual-network-firewall-rules"></a>Regras de firewall de rede virtual
Os pontos de extremidade de serviço de rede virtual estendem sua conectividade de rede virtual no backbone do Azure. Usando regras de rede virtual, você pode habilitar seu banco de dados do Azure para o servidor MariaDB para permitir conexões de sub-redes selecionadas em uma rede virtual. Para obter mais informações, consulte [visão geral do ponto de extremidade do serviço de rede virtual](concepts-data-access-security-vnet.md).


## <a name="access-management"></a>Gestão de acesso

Ao criar o banco de dados do Azure para o servidor MariaDB, você fornece credenciais para um usuário administrador. Esse administrador pode ser usado para criar usuários MariaDB adicionais.


## <a name="threat-protection"></a>Proteção contra ameaças

Você pode aceitar a [proteção avançada contra ameaças](concepts-data-access-and-security-threat-protection.md) , que detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar servidores.

O [log de auditoria](concepts-audit-logs.md) está disponível para acompanhar a atividade em seus bancos de dados. 


## <a name="next-steps"></a>Passos seguintes
- Habilitar regras de firewall para [IPS](concepts-firewall-rules.md) ou [redes virtuais](concepts-data-access-security-vnet.md)