---
title: Segurança na Base de Dados Azure para PostgreSQL - Servidor Único
description: Uma visão geral das funcionalidades de segurança na Base de Dados Azure para PostgreSQL - Servidor Único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 02dc9e1ad9ee46b1a400e44b6ef737e70571a17a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75972594"
---
# <a name="security-in-azure-database-for-postgresql---single-server"></a>Segurança na Base de Dados Azure para PostgreSQL - Servidor Único

Existem várias camadas de segurança disponíveis para proteger os dados na sua Base de Dados Azure para o servidor PostgreSQL. Este artigo descreve essas opções de segurança.

## <a name="information-protection-and-encryption"></a>Proteção de informação e encriptação

### <a name="in-transit"></a>Em trânsito
A Azure Database for PostgreSQL protege os seus dados encriptando dados em trânsito com a Segurança da Camada de Transporte. A encriptação (SSL/TLS) é aplicada por padrão.

### <a name="at-rest"></a>Em repouso
A Base de Dados Azure para o serviço PostgreSQL utiliza o módulo criptográfico validado FIPS 140-2 para encriptação de armazenamento de dados em repouso. Os dados, incluindo cópias de segurança, são encriptados no disco, com exceção dos ficheiros temporários criados durante a execução de consultas. O serviço utiliza a cifra AES de 256 bits incluída na encriptação de armazenamento Azure, e as teclas são geridas pelo sistema. A encriptação de armazenamento está sempre ativada e não pode ser desativada.


## <a name="network-security"></a>Segurança da rede
As ligações a uma base de dados Azure para servidor PostgreSQL são primeiramente encaminhada através de um gateway regional. O gateway tem um IP acessível ao público, enquanto os endereços IP do servidor estão protegidos. Para mais informações sobre a porta de entrada, visite o [artigo arquitetura de conectividade.](concepts-connectivity-architecture.md)  

Uma base de dados Azure recém-criada para servidor PostgreSQL tem uma firewall que bloqueia todas as ligações externas. Apesar de chegarem ao portal, não estão autorizados a ligar-se ao servidor. 

### <a name="ip-firewall-rules"></a>Regras de firewall IP
As regras de firewall IP concedem acesso a servidores com base no endereço IP originário de cada pedido. Consulte a [visão geral](concepts-firewall-rules.md) das regras da firewall para obter mais informações.

### <a name="virtual-network-firewall-rules"></a>Regras de firewall de rede virtual
Os pontos finais do serviço de rede virtual estendem a sua conectividade de rede virtual sobre a espinha dorsal do Azure. Utilizando regras de rede virtuais, pode ativar a sua Base de Dados Azure para servidor PostgreSQL para permitir ligações a partir de sub-redes selecionadas numa rede virtual. Para obter mais informações, consulte a [visão geral](concepts-data-access-and-security-vnet.md)do ponto final do serviço de rede virtual .

### <a name="private-ip"></a>IP privado
O Private Link permite-lhe ligar-se à sua Base de Dados Azure para servidor Single PostgreSQL em Azure através de um ponto final privado. O Azure Private Link essencialmente traz serviços Azure dentro da sua Rede Virtual privada (VNet). Os recursos PaaS podem ser acedidos usando o endereço IP privado como qualquer outro recurso no VNet. Para mais informações,consulte a [visão geral](concepts-data-access-and-security-private-link.md) do link privado


## <a name="access-management"></a>Gestão de acesso

Ao criar a Base de Dados Azure para o servidor PostgreSQL, fornece credenciais para um papel de administrador. Esta função de administrador pode ser usada para criar [funções postgreSQL adicionais.](https://www.postgresql.org/docs/current/user-manag.html)

Também pode ligar-se ao servidor utilizando a [autenticação do Azure Ative Directory (AAD).](concepts-aad-authentication.md)


## <a name="threat-protection"></a>Proteção contra ameaças

Pode optar pela [Advanced Threat Protection](concepts-data-access-and-security-threat-protection.md) que deteta atividades anómalas que indiquem tentativas invulgares e potencialmente nocivas de aceder ou explorar servidores.

[O registo de auditoria](concepts-audit.md) está disponível para acompanhar a atividade nas suas bases de dados. 


## <a name="next-steps"></a>Próximos passos
- Ativar regras de firewall para [IPs](concepts-firewall-rules.md) ou [redes virtuais](concepts-data-access-and-security-vnet.md)
- Saiba mais sobre [a autenticação do Azure Ative Directory](concepts-aad-authentication.md) na Base de Dados Azure para PostgreSQL
