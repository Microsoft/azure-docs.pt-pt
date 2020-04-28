---
title: Segurança na Base de Dados Azure para PostgreSQL - Servidor Único
description: Uma visão geral das funcionalidades de segurança na Base de Dados Azure para PostgreSQL - Servidor Único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 02dc9e1ad9ee46b1a400e44b6ef737e70571a17a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75972594"
---
# <a name="security-in-azure-database-for-postgresql---single-server"></a>Segurança na Base de Dados Azure para PostgreSQL - Servidor Único

Existem várias camadas de segurança disponíveis para proteger os dados na sua Base de Dados Azure para o servidor PostgreSQL. Este artigo descreve essas opções de segurança.

## <a name="information-protection-and-encryption"></a>Proteção de informação e encriptação

### <a name="in-transit"></a>Em trânsito
A Base de Dados Azure para PostgreSQL protege os seus dados encriptando os dados em trânsito com a Segurança da Camada de Transporte. A encriptação (SSL/TLS) é executada por padrão.

### <a name="at-rest"></a>Descanse em repouso
A Base de Dados Azure para o serviço PostgreSQL utiliza o módulo criptográfico validado FIPS 140-2 para encriptação de armazenamento de dados em repouso. Os dados, incluindo cópias de segurança, são encriptados no disco, com exceção dos ficheiros temporários criados durante a execução de consultas. O serviço utiliza a cifra AES de 256 bits incluída na encriptação de armazenamento Azure, e as chaves são geridas pelo sistema. A encriptação de armazenamento está sempre ativada e não pode ser desativada.


## <a name="network-security"></a>Segurança da rede
As ligações a uma Base de Dados Azure para servidor PostgreSQL são primeiramente direcionadas através de um portal regional. O gateway tem um IP acessível ao público, enquanto os endereços IP do servidor estão protegidos. Para mais informações sobre o portal, visite o [artigo arquitetura de conectividade.](concepts-connectivity-architecture.md)  

Uma base de dados Azure recém-criada para servidor PostgreSQL tem uma firewall que bloqueia todas as ligações externas. Apesar de chegarem à porta de entrada, não estão autorizados a ligar-se ao servidor. 

### <a name="ip-firewall-rules"></a>Regras de firewall IP
As regras de firewall IP concedem acesso aos servidores com base no endereço IP originário de cada pedido. Consulte as regras da [firewall](concepts-firewall-rules.md) para obter mais informações.

### <a name="virtual-network-firewall-rules"></a>Regras de firewall de rede virtual
Os pontos finais do serviço de rede virtual estendem a sua conectividade de rede virtual sobre a espinha dorsal azure. Utilizando regras de rede virtuais, pode ativar a sua Base de Dados Azure para o servidor PostgreSQL para permitir ligações a partir de subredes selecionadas numa rede virtual. Para mais informações, consulte a visão geral do ponto final do serviço de [rede virtual](concepts-data-access-and-security-vnet.md).

### <a name="private-ip"></a>IP privado
O Private Link permite-lhe ligar-se à sua Base de Dados Azure para o servidor Single PostgreSQL em Azure através de um ponto final privado. O Azure Private Link essencialmente traz serviços Azure dentro da sua rede virtual privada (VNet). Os recursos PaaS podem ser acedidos usando o endereço IP privado, como qualquer outro recurso no VNet. Para mais informações, consulte a visão geral do [link privado](concepts-data-access-and-security-private-link.md)


## <a name="access-management"></a>Gestão de acesso

Ao criar a Base de Dados Azure para servidor PostgreSQL, fornece credenciais para uma função de administrador. Esta função de administrador pode ser usada para criar [funções postgresQL](https://www.postgresql.org/docs/current/user-manag.html)adicionais.

Também pode ligar-se ao servidor utilizando a [autenticação do Diretório Ativo Azure (AAD).](concepts-aad-authentication.md)


## <a name="threat-protection"></a>Proteção contra ameaças

Pode optar pela [Advanced Threat Protection](concepts-data-access-and-security-threat-protection.md) que deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de acesso ou exploração de servidores.

[A exploração de registos de auditoria](concepts-audit.md) está disponível para acompanhar a atividade nas suas bases de dados. 


## <a name="next-steps"></a>Passos seguintes
- Ativar regras de firewall para [IPs](concepts-firewall-rules.md) ou [redes virtuais](concepts-data-access-and-security-vnet.md)
- Saiba mais sobre [a autenticação do Diretório Ativo Azure](concepts-aad-authentication.md) na Base de Dados Azure para PostgreSQL
