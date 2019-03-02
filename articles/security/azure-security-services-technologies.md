---
title: Tecnologias e serviços de segurança do Azure | Documentos da Microsoft
description: O artigo fornece uma lista organizada dos serviços de segurança do Azure e tecnologias.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/29/2019
ms.author: barclayn
ms.openlocfilehash: 13183282e5e607f0052194a474203f97e0160adb
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246139"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Serviços de segurança e as tecnologias disponíveis no Azure

Em nosso discussões com clientes do Azure, atuais e futuros, são, muitas vezes, perguntamos "tem uma lista de todos os serviços relacionados à segurança e tecnologias que o Azure tem para oferecer?"

Ao avaliar as opções de fornecedor de serviço de nuvem, é útil ter estas informações. Então, nós fornecemos esta lista para começar.

Ao longo do tempo, esta lista irá alterar e a crescer, tal como o Azure faz. Lembre-se de que consulte esta página regularmente para se manter atualizado em nossos serviços relacionados com segurança e tecnologias.

## <a name="general-azure-security"></a>Segurança do Azure geral
|Serviço|Descrição|
|--------|--------|
|[Azure&nbsp;segurança&nbsp;Center](../security-center/security-center-intro.md)| Uma solução de proteção de carga de trabalho na cloud que fornece gerenciamento de segurança e proteção avançada contra ameaças entre cargas de trabalho híbrida na cloud.|
|[Cofre de Chaves do Azure](../key-vault/key-vault-overview.md)| Um arquivo de segredos seguro para as palavras-passe, cadeias de ligação e outras informações de que precisa para manter as suas aplicações a funcionar. |
|[Registos do Azure Monitor](../log-analytics/log-analytics-overview.md)|Um serviço de monitoramento que recolhe telemetria e outros dados e oferece um mecanismo de análise e linguagem de consulta para fornecer as informações operacionais para as aplicações e recursos. Pode ser usada individualmente ou com outros serviços, como o Centro de segurança. |
|[Laboratórios Dev/Test do Azure](../devtest-lab/devtest-lab-overview.md)|Um serviço que ajuda os desenvolvedores e testadores rapidamente criar ambientes no Azure ao minimizar desperdícios e controla os custos.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](azure-security-disk-encryption-overview.md)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Segurança de armazenamento
|Serviço|Descrição|
|------|--------|
| [Azure&nbsp;Storage&nbsp;Service&nbsp;Encryption](../storage/common/storage-service-encryption.md)|Uma funcionalidade de segurança que encripta automaticamente os dados no armazenamento do Azure.   |
|[StorSimple encriptados armazenamento híbrido](../storsimple/storsimple-ova-overview.md)| Uma solução de armazenamento integrada que gere tarefas de armazenamento entre dispositivos no local e o armazenamento na cloud do Azure.|
|[Encriptação do lado do cliente do Azure](../storage/common/storage-client-side-encryption.md)| Uma solução de encriptação do lado do cliente, que criptografa dados dentro de aplicativos de cliente antes de carregar para o armazenamento do Azure; também desencripta os dados durante a transferência. |
| [Assinaturas de acesso partilhado de armazenamento do Azure](../storage/common/storage-dotnet-shared-access-signature-part-1.md)|Uma assinatura de acesso partilhado fornece acesso delegado a recursos na sua conta de armazenamento.  |
|[Chaves de conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md)| Um método de controle de acesso para o armazenamento do Azure que é utilizado para autenticação quando a conta de armazenamento é acessada. |
|[Partilhas de ficheiros do Azure com a encriptação do SMB 3.0](../storage/files/storage-files-introduction.md)|Uma tecnologia de segurança de rede que ativa a encriptação automática de rede para o protocolo de partilha de ficheiros do bloco de mensagem de servidor (SMB). |
|[Análise de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/Storage-Analytics)| Uma tecnologia de geração de métricas e de registo de dados na sua conta de armazenamento. |

<!------>

## <a name="database-security"></a>Segurança de bases de dados
|Serviço|Descrição|
|------|--------|
| [Azure&nbsp;SQL&nbsp;Firewall](../sql-database/sql-database-firewall-configure.md)|Uma funcionalidade de controlo de acesso do rede protege contra ataques baseados na rede à base de dados. |
|[Azure&nbsp;SQL&nbsp;célula&nbsp;encriptação de nível](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| Uma tecnologia de segurança da base de dados, que fornece encriptação num nível granular.  |
| [Azure&nbsp;SQL&nbsp;encriptação da ligação](../sql-database/sql-database-control-access.md)|Para fornecer segurança, a Base de Dados SQL controla o acesso com regras de firewall que limitam a conectividade por endereço IP, mecanismos de autenticação que exigem que os utilizadores provem a sua identidade e mecanismos de autorização que limitam os utilizadores a ações e dados específicos. |
| [SQL do Azure sempre encriptação](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Protege os dados confidenciais, como números de cartão de crédito ou números de identificação nacional (por exemplo, EUA números de cpf), armazenados em bases de dados de base de dados do Azure SQL ou SQL Server.  |
| [Azure&nbsp;SQL&nbsp;encriptação de dados transparente](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Um recurso de segurança da base de dados, que criptografa o armazenamento de uma base de dados inteiro. |
| [Base de dados SQL do Azure de auditoria](../sql-database/sql-database-auditing.md)|Uma base de dados a auditoria de recurso que regista os eventos de base de dados e escreve-os para uma auditoria registo na sua conta de armazenamento do Azure.  |


## <a name="identity-and-access-management"></a>Gestão de identidades e acessos
|Serviço|Descrição|
|------|--------|
| [Azure&nbsp;função&nbsp;com base&nbsp;controlo de acesso](../active-directory/role-based-access-control-configure.md)|Uma funcionalidade de controlo de acesso concebido para permitir que os usuários acessem apenas os recursos são necessários para acesso com base nas respetivas funções dentro da organização.  |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)|Um repositório de autenticação com base na cloud que oferece suporte a um diretório de multi-inquilino, com base na cloud e vários serviços de gestão de identidade no Azure.  |
| [Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-overview.md)|Um serviço de gestão de identidade que permite o controle sobre como os clientes a inscrição, início de sessão e gerem os respetivos perfis quando se utilizam aplicações baseadas no Azure.   |
| [Serviços de domínio do Active Directory do Azure](../active-directory-domain-services/active-directory-ds-overview.md)| Uma versão baseada na cloud e gerida dos serviços de domínio do Active Directory. |
| [Autenticação Multifator do Azure](../active-directory/authentication/multi-factor-authentication.md)| Aprovisionamento de segurança que emprega várias formas diferentes de autenticação e verificação antes de permitir o acesso a informações seguras. |

## <a name="backup-and-disaster-recovery"></a>Cópia de segurança e recuperação após desastre
|Serviço|Descrição|
|------|--------|
| [Azure&nbsp;cópia de segurança](../backup/backup-introduction-to-azure-backup.md)| Um serviço baseado no Azure utilizado para criar cópias de segurança e restaurar dados na cloud do Azure. |
| [Azure&nbsp;Site&nbsp;recuperação](../site-recovery/site-recovery-overview.md)|Um serviço online que replica cargas de trabalho em execução em máquinas físicas e virtuais (VMs) de um site primário para uma localização secundária para ativar a recuperação dos serviços após uma falha. |

## <a name="networking"></a>Redes
|Serviço|Descrição|
|------|--------|
| [Rede&nbsp;segurança&nbsp;grupos](../virtual-network/virtual-networks-nsg.md)| Uma funcionalidade de controlo de acesso com base na rede usando uma 5 cadeias de identificação para permitir ou negar a decisões.  |
| [Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md)| Um dispositivo de rede utilizado como um ponto de extremidade VPN para permitir em vários locais acesso a redes virtuais do Azure.  |
| [Gateway de aplicação do Azure](../application-gateway/application-gateway-introduction.md)|Balanceador que possa encaminhar com base na URL e efetuar a descarga de SSL de carga de um aplicativo da web avançados. |
|[Firewall de aplicações Web](../application-gateway/waf-overview.md) (WAF)|Um recurso do Gateway de aplicação que fornece proteção centralizada das suas aplicações web de exploits e vulnerabilidades comuns|
| [Azure Load Balancer](../load-balancer/load-balancer-overview.md)|Um balanceador de carga do TCP/UDP aplicação rede. |
| [O Azure ExpressRoute](../expressroute/expressroute-introduction.md)| Ligar a uma WAN dedicada entre redes no local e redes virtuais do Azure. |
| [Gestor de Tráfego do Azure](../traffic-manager/traffic-manager-overview.md)| Um balanceador de carga global do DNS.|
| [Proxy de aplicações do Azure](../active-directory/active-directory-application-proxy-get-started.md)| Uma autenticação de front-end utilizado para proteger o acesso remoto para aplicações web alojadas no local. |
|[Firewall do Azure](../firewall/overview.md)|Um serviço de segurança de rede gerido e baseado na nuvem que protege os seus recursos de rede Virtual do Azure.|
|[Proteção contra DDoS do Azure](../virtual-network/ddos-protection-overview.md)|Combinado com práticas recomendadas de design de aplicativo, fornece a defesa contra ataques DDoS.|
|[Pontos finais de serviço de Rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md)|Expande o seu espaço de endereços privados da rede virtual e a identidade da sua VNet para os serviços do Azure, através de uma ligação direta.|