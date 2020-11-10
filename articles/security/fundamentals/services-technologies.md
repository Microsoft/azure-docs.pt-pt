---
title: Serviços e Tecnologias de Segurança Azure ! Microsoft Docs
description: O artigo apresenta uma lista com curadoria dos serviços e tecnologias da Azure Security.
services: security
documentationcenter: na
author: terrylanfear
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
ms.author: terrylan
ms.openlocfilehash: 46b1cbbd620cf2de8d875927f5745e91fcf608c5
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410050"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Serviços de segurança e tecnologias disponíveis no Azure

Nas nossas discussões com os clientes Azure atuais e futuros, perguntam-nos muitas vezes "tem uma lista de todos os serviços e tecnologias relacionados com a segurança que o Azure tem para oferecer?"

Quando avalia as opções do fornecedor de serviços na nuvem, é útil ter esta informação. Então, nós fornecemos esta lista para começar.

Com o tempo, esta lista mudará e crescerá, tal como o Azure. Certifique-se de verificar esta página regularmente para se manter atualizado sobre os nossos serviços e tecnologias relacionados com a segurança.

## <a name="general-azure-security"></a>Segurança geral Azure
|Serviço|Description|
|--------|--------|
|[&nbsp;Centro de Segurança Azure &nbsp;](../../security-center/security-center-introduction.md)| Uma solução de proteção da carga de trabalho em nuvem que fornece gestão de segurança e proteção avançada de ameaças através de cargas de trabalho em nuvem híbrida.|
|[Azure Key Vault](../../key-vault/general/overview.md)| Uma loja de segredos seguro para as palavras-passe, cordas de conexão e outras informações que precisa para manter as suas apps funcionando. |
|[Registos do Azure Monitor](../../azure-monitor/log-query/log-query-overview.md)|Um serviço de monitorização que recolhe telemetria e outros dados, e fornece um motor de linguagem e análise de consulta para fornecer insights operacionais para as suas apps e recursos. Pode ser usado sozinho ou com outros serviços, como o Security Center. |
|[Laboratórios Azure Dev/Test](../../devtest-labs/devtest-lab-overview.md)|Um serviço que ajuda desenvolvedores e testadores a criar rapidamente ambientes em Azure, minimizando o desperdício e controlando os custos.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](/azure/azure-security-disk-encryption-overview)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Segurança de armazenamento
|Serviço|Description|
|------|--------|
| [&nbsp;Encriptação do Serviço de Armazenamento Azure &nbsp; &nbsp;](../../storage/common/storage-service-encryption.md)|Uma funcionalidade de segurança que encripta automaticamente os seus dados no armazenamento do Azure.   |
|[Armazenamento híbrido encriptado StorSimple](../../storsimple/storsimple-ova-overview.md)| Uma solução de armazenamento integrada que gere tarefas de armazenamento entre dispositivos no local e armazenamento em nuvem Azure.|
|[Encriptação Azure Client-Side](../../storage/common/storage-client-side-encryption.md)| Uma solução de encriptação do lado do cliente que encripta dados dentro das aplicações do cliente antes de enviar para o Azure Storage; também desencripta os dados durante o download. |
| [Assinaturas de acesso compartilhados de armazenamento Azure](../../storage/common/storage-sas-overview.md)|Uma assinatura de acesso partilhado proporciona acesso delegado aos recursos na sua conta de armazenamento.  |
|[Chaves da conta de armazenamento Azure](../../storage/common/storage-account-create.md)| Um método de controlo de acesso para armazenamento Azure que é usado para autenticação quando a conta de armazenamento é acedida. |
|[Azure File partilha com encriptação SMB 3.0](../../storage/files/storage-files-introduction.md)|Uma tecnologia de segurança de rede que permite encriptação automática da rede para o protocolo de partilha de ficheiros do Bloco de Mensagens do Servidor (SMB). |
|[Azure Storage Analytics](/rest/api/storageservices/Storage-Analytics)| Uma tecnologia geradora de registo e métricas para dados na sua conta de armazenamento. |

<!------>

## <a name="database-security"></a>Segurança de bases de dados
|Serviço|Description|
|------|--------|
| [Azure &nbsp; SQL &nbsp; Firewall](../../azure-sql/database/firewall-configure.md)|Uma funcionalidade de controlo de acesso à rede que protege contra ataques baseados em rede para base de dados. |
|[&nbsp; &nbsp; &nbsp; Encriptação do nível da célula Azure SQL](/archive/blogs/sqlsecurity/recommendations-for-using-cell-level-encryption-in-azure-sql-database)| Uma tecnologia de segurança de base de dados que fornece encriptação a um nível granular.  |
| [Encriptação de &nbsp; conexão Azure SQL &nbsp;](../../azure-sql/database/logins-create-manage.md)|Para fornecer segurança, a Base de Dados SQL controla o acesso com regras de firewall que limitam a conectividade por endereço IP, mecanismos de autenticação que exigem que os utilizadores provem a sua identidade e mecanismos de autorização que limitam os utilizadores a ações e dados específicos. |
| [Azure SQL Sempre Encriptação](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Protege dados sensíveis, tais como números de cartões de crédito ou números de identificação nacionais (por exemplo, números de segurança social dos EUA), armazenados em bases de dados Azure SQL Database ou SQL Server.  |
| [&nbsp; &nbsp; Encriptação de dados transparentes Azure SQL](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Um recurso de segurança da base de dados que encripta o armazenamento de uma base de dados inteira. |
| [Auditoria da Base de Dados Azure SQL](../../azure-sql/database/auditing-overview.md)|Uma funcionalidade de auditoria de base de dados que rastreia eventos de base de dados e os escreve para um registo de auditoria na sua conta de armazenamento Azure.  |


## <a name="identity-and-access-management"></a>Gestão de identidades e acessos
|Serviço|Description|
|------|--------|
| [&nbsp;Controlo de &nbsp; acesso baseado em funções Azure &nbsp;](../../role-based-access-control/role-assignments-portal.md)|Uma funcionalidade de controlo de acessos concebida para permitir aos utilizadores acederem apenas aos recursos a que são necessários aceder com base nas suas funções dentro da organização.  |
| [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)|Um repositório de autenticação baseado na nuvem que suporta um multi-inquilino, diretório baseado em nuvem e múltiplos serviços de gestão de identidade dentro de Azure.  |
| [Azure Active Directory B2C](../../active-directory-b2c/overview.md)|Um serviço de gestão de identidade que permite controlar a forma como os clientes se inscrevem, se inscrevem e gerem os seus perfis ao utilizarem aplicações baseadas no Azure.   |
| [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md)| Uma versão baseada na nuvem e gerida dos Serviços de Domínio do Diretório Ativo. |
| [Multi-Factor Authentication do Azure](../../active-directory/authentication/concept-mfa-howitworks.md)| Uma disposição de segurança que emprega várias formas diferentes de autenticação e verificação antes de permitir o acesso a informações seguras. |

## <a name="backup-and-disaster-recovery"></a>Backup e recuperação de desastres
|Serviço|Description|
|------|--------|
| [&nbsp;Reserva Azure](../../backup/backup-overview.md)| Um serviço baseado em Azure usado para fazer o back up e restaurar dados na nuvem Azure. |
| [&nbsp;Recuperação do local de Azure &nbsp;](../../site-recovery/site-recovery-overview.md)|Um serviço online que replica cargas de trabalho em máquinas físicas e virtuais (VMs) de um site primário para um local secundário para permitir a recuperação de serviços após uma falha. |

## <a name="networking"></a>Rede
|Serviço|Description|
|------|--------|
| [&nbsp;Grupos de Segurança de &nbsp; Rede](../../virtual-network/virtual-network-vnet-plan-design-arm.md)| Uma funcionalidade de controlo de acesso baseada em rede utilizando um 5-tuple para tomar ou negar decisões.  |
| [Gateway de VPN do Azure](../../vpn-gateway/vpn-gateway-about-vpngateways.md)| Um dispositivo de rede utilizado como ponto final VPN para permitir o acesso das instalações cruzadas às Redes Virtuais Azure.  |
| [Gateway de Aplicação do Azure](../../application-gateway/overview.md)|Um equilibrador avançado de carga de aplicações web que pode fazer rota com base em URL e executar o descarregamento de SSL. |
|[Firewall de aplicação web](../../web-application-firewall/afds/afds-overview.md) (WAF)|Uma funcionalidade do Application Gateway que fornece proteção centralizada das suas aplicações web de explorações e vulnerabilidades comuns|
| [Balanceador de Carga do Azure](../../load-balancer/load-balancer-overview.md)|Um equilibrador de rede de aplicações TCP/UDP. |
| [Azure ExpressRoute](../../expressroute/expressroute-introduction.md)| Uma ligação WAN dedicada entre redes no local e redes virtuais Azure. |
| [Gestor de Tráfego do Azure](../../traffic-manager/traffic-manager-overview.md)| Um equilibrador global de carga de DNS.|
| [Procuração de aplicação Azure](../../active-directory/manage-apps/application-proxy.md)| Uma linha frontal autenticadora usada para garantir o acesso remoto para aplicações web hospedadas no local. |
|[Azure Firewall](../../firewall/overview.md)|Um serviço de segurança de rede gerido e baseado na nuvem que protege os seus recursos da Rede Virtual Azure.|
|[Azure DDoS Protection](../../virtual-network/ddos-protection-overview.md)|Combinado com as melhores práticas de design de aplicações, fornece defesa contra ataques DDoS.|
|[Pontos finais de serviço de rede virtual](../../virtual-network/virtual-network-service-endpoints-overview.md)|Estende o espaço de endereço privado da sua rede virtual e a identidade do seu VNet aos serviços Azure, através de uma ligação direta.|