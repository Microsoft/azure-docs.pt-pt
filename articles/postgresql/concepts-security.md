---
title: Segurança no banco de dados do Azure para PostgreSQL-servidor único
description: Uma visão geral dos recursos de segurança no banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: a1bd9b8cbcbc785425c2d1870dc555ff91f695f7
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485084"
---
# <a name="security-in-azure-database-for-postgresql---single-server"></a>Segurança no banco de dados do Azure para PostgreSQL-servidor único

Há várias camadas de segurança que estão disponíveis para proteger os dados no servidor do banco de dado do Azure para PostgreSQL. Este artigo descreve essas opções de segurança.

## <a name="information-protection-and-encryption"></a>Proteção de informações e criptografia

### <a name="in-transit"></a>Em trânsito
O banco de dados do Azure para PostgreSQL protege seus dados criptografando os dados em trânsito com a segurança da camada de transporte. A criptografia (SSL/TLS) é imposta por padrão.

### <a name="at-rest"></a>Em repouso
O serviço banco de dados do Azure para PostgreSQL usa o módulo de criptografia FIPS 140-2 validado para criptografia de armazenamento de dados em repouso. Os dados, incluindo backups, são criptografados no disco, com exceção dos arquivos temporários criados durante a execução de consultas. O serviço usa a codificação AES de 256 bits incluída na criptografia de armazenamento do Azure e as chaves são gerenciadas pelo sistema. A encriptação de armazenamento está sempre ativada e não pode ser desativada.


## <a name="network-security"></a>Segurança da rede
As conexões com um servidor de banco de dados do Azure para PostgreSQL são roteadas primeiro por meio de um gateway regional. O gateway tem um IP acessível publicamente, enquanto os endereços IP do servidor são protegidos. Para obter mais informações sobre o gateway, visite o [artigo arquitetura de conectividade](concepts-connectivity-architecture.md).  

Um banco de dados do Azure criado recentemente para o servidor PostgreSQL tem um firewall que bloqueia todas as conexões externas. Embora eles atinjam o gateway, eles não têm permissão para se conectar ao servidor. 

### <a name="ip-firewall-rules"></a>Regras de firewall de IP
As regras de firewall IP concedem acesso a servidores com base no endereço IP de origem de cada solicitação. Consulte a [visão geral das regras de firewall](concepts-firewall-rules.md) para obter mais informações.

### <a name="virtual-network-firewall-rules"></a>Regras de firewall de rede virtual
Os pontos de extremidade de serviço de rede virtual estendem sua conectividade de rede virtual no backbone do Azure. Usando regras de rede virtual, você pode habilitar seu banco de dados do Azure para o servidor PostgreSQL para permitir conexões de sub-redes selecionadas em uma rede virtual. Para obter mais informações, consulte [visão geral do ponto de extremidade do serviço de rede virtual](concepts-data-access-and-security-vnet.md).


## <a name="access-management"></a>Gestão de acesso

Ao criar o banco de dados do Azure para o servidor PostgreSQL, você fornece credenciais para uma função de administrador. Essa função de administrador pode ser usada para criar [funções](https://www.postgresql.org/docs/current/user-manag.html)adicionais do PostgreSQL.

Você também pode se conectar ao servidor usando a [autenticação do Azure Active Directory (AAD)](concepts-aad-authentication.md).


## <a name="threat-protection"></a>Proteção contra ameaças

Você pode aceitar a [proteção avançada contra ameaças](concepts-data-access-and-security-threat-protection.md) , que detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar servidores.

O [log de auditoria](concepts-audit.md) está disponível para acompanhar a atividade em seus bancos de dados. 


## <a name="next-steps"></a>Passos Seguintes
- Habilitar regras de firewall para [IPS](concepts-firewall-rules.md) ou [redes virtuais](concepts-data-access-and-security-vnet.md)
- Saiba mais sobre a [autenticação Azure Active Directory](concepts-aad-authentication.md) no banco de dados do Azure para PostgreSQL
