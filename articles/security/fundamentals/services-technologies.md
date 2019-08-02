---
title: Tecnologias e serviços de segurança do Azure | Microsoft Docs
description: O artigo fornece uma lista organizada de tecnologias e serviços de segurança do Azure.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/29/2019
ms.author: barclayn
ms.openlocfilehash: 741e6b9cad20645fdfc085623fd9492a12ac1dfc
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726566"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Serviços de segurança e tecnologias disponíveis no Azure

Em nossas discussões com clientes atuais e futuros do Azure, muitas vezes é perguntado: "você tem uma lista de todos os serviços e tecnologias relacionados à segurança que o Azure tem a oferecer?"

Ao avaliar as opções do provedor de serviços de nuvem, é útil ter essas informações. Nós fornecemos essa lista para você começar.

Ao longo do tempo, essa lista será alterada e crescerá da mesma forma que o Azure. Certifique-se de verificar esta página regularmente para manter-se atualizado sobre nossos serviços e tecnologias relacionados à segurança.

## <a name="general-azure-security"></a>Segurança geral do Azure
|Serviço|Descrição|
|--------|--------|
|[Central&nbsp;de&nbsp;segurança do Azure](/azure/security-center/security-center-intro)| Uma solução de proteção de carga de trabalho de nuvem que fornece gerenciamento de segurança e proteção avançada contra ameaças em cargas de trabalho de nuvem híbrida.|
|[Cofre de Chaves do Azure](/azure/key-vault/key-vault-overview)| Um repositório seguro de segredos para as senhas, cadeias de conexão e outras informações necessárias para manter seus aplicativos funcionando. |
|[Registos do Azure Monitor](/azure/log-analytics/log-analytics-overview)|Um serviço de monitoramento que coleta telemetria e outros dados e fornece uma linguagem de consulta e um mecanismo de análise para fornecer informações operacionais para seus aplicativos e recursos. Pode ser usado sozinho ou com outros serviços, como a central de segurança. |
|[Laboratórios de desenvolvimento/teste do Azure](/azure/lab-services/devtest-lab-overview)|Um serviço que ajuda os desenvolvedores e testadores a criarem rapidamente ambientes no Azure, minimizando o desperdício e controlando os custos.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](/azure/azure-security-disk-encryption-overview)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Segurança de armazenamento
|Serviço|Descrição|
|------|--------|
| [Criptografia&nbsp;do&nbsp;serviço&nbsp;de armazenamento do Azure](/azure/storage/common/storage-service-encryption)|Um recurso de segurança que criptografa automaticamente os dados no armazenamento do Azure.   |
|[Armazenamento híbrido criptografado do StorSimple](/azure/storsimple/storsimple-ova-overview)| Uma solução de armazenamento integrado que gerencia tarefas de armazenamento entre dispositivos locais e o armazenamento em nuvem do Azure.|
|[Criptografia do lado do cliente do Azure](/azure/storage/common/storage-client-side-encryption)| Uma solução de criptografia do lado do cliente que criptografa dados dentro de aplicativos cliente antes de carregar no armazenamento do Azure; também descriptografa os dados durante o download. |
| [Assinaturas de acesso compartilhado do armazenamento do Azure](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)|Uma assinatura de acesso partilhado fornece acesso delegado a recursos na sua conta de armazenamento.  |
|[Chaves de conta de armazenamento do Azure](/azure/storage/common/storage-create-storage-account)| Um método de controle de acesso para o armazenamento do Azure que é usado para autenticação quando a conta de armazenamento é acessada. |
|[Compartilhamentos de arquivos do Azure com criptografia SMB 3,0](/azure/storage/files/storage-files-introduction)|Uma tecnologia de segurança de rede que habilita a criptografia de rede automática para o protocolo de compartilhamento de arquivos SMB. |
|[Análise de Armazenamento do Azure](/rest/api/storageservices/Storage-Analytics)| Uma tecnologia de geração de logs e métricas para dados em sua conta de armazenamento. |

<!------>

## <a name="database-security"></a>Segurança de bases de dados
|Serviço|Descrição|
|------|--------|
| [Azure&nbsp;SQL&nbsp;Firewall](/azure/sql-database/sql-database-firewall-configure)|Um recurso de controle de acesso à rede que protege contra ataques baseados em rede ao banco de dados. |
|[Criptografia&nbsp;de&nbsp;nível&nbsp;de célula do SQL do Azure](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| Uma tecnologia de segurança de banco de dados que fornece criptografia em um nível granular.  |
| [Criptografia&nbsp;de&nbsp;conexão SQL do Azure](/azure/sql-database/sql-database-control-access)|Para fornecer segurança, a Base de Dados SQL controla o acesso com regras de firewall que limitam a conectividade por endereço IP, mecanismos de autenticação que exigem que os utilizadores provem a sua identidade e mecanismos de autorização que limitam os utilizadores a ações e dados específicos. |
| [Criptografia do SQL do Azure Always](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Protege dados confidenciais, como números de cartão de crédito ou números de identificação nacional (por exemplo, números de previdência social dos EUA), armazenados no banco de dados SQL do Azure ou em bancos de SQL Server.  |
| [Transparent Data Encryption&nbsp;do&nbsp;SQL do Azure](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Um recurso de segurança de banco de dados que criptografa o armazenamento de um banco de dados inteiro. |
| [Auditoria do banco de dados SQL do Azure](/azure/sql-database/sql-database-auditing)|Um recurso de auditoria de banco de dados que rastreia eventos de banco de dados e os grava em um log de auditoria em sua conta de armazenamento do Azure.  |


## <a name="identity-and-access-management"></a>Gestão de acesso e identidades
|Serviço|Descrição|
|------|--------|
| [Controle&nbsp;de&nbsp;acesso&nbsp;baseado em função do Azure](/azure/active-directory/role-based-access-control-configure)|Um recurso de controle de acesso projetado para permitir que os usuários acessem apenas os recursos que precisam acessar com base em suas funções dentro da organização.  |
| [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)|Um repositório de autenticação baseado em nuvem que dá suporte a um diretório multilocatário baseado em nuvem e a vários serviços de gerenciamento de identidade no Azure.  |
| [Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview)|Um serviço de gerenciamento de identidade que permite o controle sobre como os clientes se inscrevem, entram e gerenciam seus perfis ao usar aplicativos baseados no Azure.   |
| [Azure Active Directory Domain Services](/azure/active-directory-domain-services/overview)| Uma versão gerenciada e baseada em nuvem do Active Directory Domain Services. |
| [Autenticação Multifator do Azure](/azure/active-directory/authentication/multi-factor-authentication)| Uma provisão de segurança que emprega várias formas diferentes de autenticação e verificação antes de permitir o acesso a informações protegidas. |

## <a name="backup-and-disaster-recovery"></a>Cópia de segurança e recuperação após desastre
|Serviço|Descrição|
|------|--------|
| [Backup&nbsp;do Azure](/azure/backup/backup-introduction-to-azure-backup)| Um serviço baseado no Azure usado para fazer backup e restaurar dados na nuvem do Azure. |
| [Recuperação&nbsp;de&nbsp;site do Azure](/azure/site-recovery/site-recovery-overview)|Um serviço online que Replica cargas de trabalho em execução em máquinas físicas e virtuais (VMs) de um site primário para um local secundário para habilitar a recuperação de serviços após uma falha. |

## <a name="networking"></a>Redes
|Serviço|Descrição|
|------|--------|
| [Grupos&nbsp;de&nbsp;segurança de rede](/azure/virtual-network/virtual-networks-nsg)| Um recurso de controle de acesso baseado em rede usando um 5 tupla para tomar decisões permitidas ou negadas.  |
| [Gateway de VPN do Azure](/azure/vpn-gateway/vpn-gateway-about-vpngateways)| Um dispositivo de rede usado como um ponto de extremidade de VPN para permitir acesso entre instalações a redes virtuais do Azure.  |
| [Aplicativo Azure gateway](/azure/application-gateway/application-gateway-introduction)|Um balanceador de carga de aplicativo Web avançado que pode rotear com base na URL e executar o descarregamento de SSL. |
|[Firewall do aplicativo Web](/azure/frontdoor/waf-overview) WAF|Um recurso do gateway de aplicativo que fornece proteção centralizada de seus aplicativos Web contra explorações e vulnerabilidades comuns|
| [Azure Load Balancer](/azure/load-balancer/load-balancer-overview)|Um balanceador de carga de rede de aplicativo TCP/UDP. |
| [ExpressRoute do Azure](/azure/expressroute/expressroute-introduction)| Um link WAN dedicado entre redes locais e redes virtuais do Azure. |
| [Gestor de Tráfego do Azure](/azure/traffic-manager/traffic-manager-overview)| Um balanceador de carga DNS global.|
| [Aplicativo Azure proxy](/azure/active-directory/active-directory-application-proxy-get-started)| Um front-end de autenticação usado para proteger o acesso remoto para aplicativos Web hospedados localmente. |
|[Firewall do Azure](/azure/firewall/overview)|Um serviço de segurança de rede gerenciado baseado em nuvem que protege os recursos de rede virtual do Azure.|
|[Proteção contra DDoS do Azure](/azure/virtual-network/ddos-protection-overview)|Combinado com as práticas recomendadas de design de aplicativo, o fornece defesa contra ataques de DDoS.|
|[Pontos finais de serviço de Rede Virtual](/azure/virtual-network/virtual-network-service-endpoints-overview)|Estende o espaço de endereço privado da rede virtual e a identidade de sua VNet para os serviços do Azure, em uma conexão direta.|