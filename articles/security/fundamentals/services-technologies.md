---
title: Serviços e Tecnologias de Segurança Azure Microsoft Docs
description: O artigo fornece uma lista com curadoria de serviços e tecnologias de Segurança Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726566"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Serviços e tecnologias de segurança disponíveis no Azure

Nas nossas discussões com os atuais e futuros clientes azure, perguntam-nos muitas vezes "tem uma lista de todos os serviços e tecnologias relacionados com a segurança que o Azure tem para oferecer?"

Ao avaliar as opções do fornecedor de serviços na nuvem, é útil ter esta informação. Por isso, disponibilizámos esta lista para começar.

Com o tempo, esta lista vai mudar e crescer, tal como o Azure. Certifique-se de verificar esta página regularmente para se manter atualizado sobre os nossos serviços e tecnologias relacionados com a segurança.

## <a name="general-azure-security"></a>Segurança general Azure
|Serviço|Descrição|
|--------|--------|
|[Centro&nbsp;de&nbsp;Segurança Azure](/azure/security-center/security-center-intro)| Uma solução de proteção de carga de trabalho em nuvem que fornece gestão de segurança e proteção avançada de ameaças através de cargas de trabalho híbridas em nuvem.|
|[Azure Key Vault](/azure/key-vault/key-vault-overview)| Uma loja de segredos seguros para as palavras-passe, cordas de ligação e outras informações que você precisa para manter as suas aplicações funcionando. |
|[Registos do Monitor Azure](/azure/log-analytics/log-analytics-overview)|Um serviço de monitorização que recolhe telemetria e outros dados, e fornece um motor de linguagem e análise de consulta para fornecer insights operacionais para as suas apps e recursos. Pode ser usado sozinho ou com outros serviços, como o Security Center. |
|[Laboratórios Azure Dev/Test](/azure/lab-services/devtest-lab-overview)|Um serviço que ajuda os desenvolvedores e testadores a criar rapidamente ambientes em Azure, minimizando os resíduos e controlando os custos.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](/azure/azure-security-disk-encryption-overview)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Segurança de armazenamento
|Serviço|Descrição|
|------|--------|
| [Encriptação&nbsp;&nbsp;do&nbsp;serviço de armazenamento Azure](/azure/storage/common/storage-service-encryption)|Uma funcionalidade de segurança que encripta automaticamente os seus dados no armazenamento do Azure.   |
|[Armazenamento híbrido encriptado StorSimple](/azure/storsimple/storsimple-ova-overview)| Uma solução de armazenamento integrada que gere tarefas de armazenamento entre dispositivos no local e armazenamento em nuvem Azure.|
|[Encriptação azure lado cliente](/azure/storage/common/storage-client-side-encryption)| Uma solução de encriptação do lado do cliente que encripta dados dentro das aplicações do cliente antes de enviar para o Armazenamento Azure; também desencripta os dados durante o download. |
| [Assinaturas de acesso partilhado de armazenamento azure](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)|Uma assinatura de acesso partilhado proporciona acesso delegado aos recursos na sua conta de armazenamento.  |
|[Chaves da conta de armazenamento azure](/azure/storage/common/storage-create-storage-account)| Um método de controlo de acesso para armazenamento Azure que é utilizado para autenticação quando a conta de armazenamento é acedida. |
|[Azure File partilha com encriptação SMB 3.0](/azure/storage/files/storage-files-introduction)|Uma tecnologia de segurança de rede que permite a encriptação automática da rede para o protocolo de partilha de ficheiros do Bloco de Mensagens do Servidor (SMB). |
|[Análise de Armazenamento Azure](/rest/api/storageservices/Storage-Analytics)| Uma tecnologia de exploração madeireira e geradora de métricas para dados na sua conta de armazenamento. |

<!------>

## <a name="database-security"></a>Segurança de bases de dados
|Serviço|Descrição|
|------|--------|
| [Azure&nbsp;SQL&nbsp;Firewall](/azure/sql-database/sql-database-firewall-configure)|Uma funcionalidade de controlo de acesso à rede que protege contra ataques baseados em rede à base de dados. |
|[Encriptação&nbsp;do&nbsp;nível de célula&nbsp;SQL Azure](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| Uma tecnologia de segurança de base de dados que fornece encriptação a um nível granular.  |
| [Encriptação&nbsp;de&nbsp;conexão Azure SQL](/azure/sql-database/sql-database-control-access)|Para fornecer segurança, a Base de Dados SQL controla o acesso com regras de firewall que limitam a conectividade por endereço IP, mecanismos de autenticação que exigem que os utilizadores provem a sua identidade e mecanismos de autorização que limitam os utilizadores a ações e dados específicos. |
| [Encriptação Azure SQL Sempre](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Protege dados sensíveis, tais como números de cartões de crédito ou números de identificação nacionais (por exemplo, números de segurança social dos EUA), armazenados em bases de dados Azure SQL ou SQL Server.  |
| [Encriptação&nbsp;de&nbsp;dados transparente sql azure](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Uma funcionalidade de segurança na base de dados que encripta o armazenamento de uma base de dados inteira. |
| [Auditoria de Base de Dados Azure SQL](/azure/sql-database/sql-database-auditing)|Uma funcionalidade de auditoria da base de dados que rastreia os eventos da base de dados e os escreve num registo de auditoria na sua conta de armazenamento Azure.  |


## <a name="identity-and-access-management"></a>Gestão de identidades e acessos
|Serviço|Descrição|
|------|--------|
| [Controlo&nbsp;de&nbsp;&nbsp;acesso baseado em funções azure](/azure/active-directory/role-based-access-control-configure)|Uma funcionalidade de controlo de acesso concebida para permitir aos utilizadores acederem apenas aos recursos a que são necessários aceder com base nas suas funções dentro da organização.  |
| [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)|Um repositório de autenticação baseado na nuvem que suporta um multi-inquilino, diretório baseado em nuvem e múltiplos serviços de gestão de identidade dentro do Azure.  |
| [Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview)|Um serviço de gestão de identidade que permite controlar a forma como os clientes se inscrevem, fazem o parto e gerem os seus perfis ao utilizarem aplicações baseadas no Azure.   |
| [Azure Active Directory Domain Services](/azure/active-directory-domain-services/overview)| Uma versão baseada na nuvem e gerida dos Serviços de Domínio do Diretório Ativo. |
| [Multi-Factor Authentication do Azure](/azure/active-directory/authentication/multi-factor-authentication)| Uma disposição de segurança que emprega várias formas diferentes de autenticação e verificação antes de permitir o acesso a informações seguras. |

## <a name="backup-and-disaster-recovery"></a>Backup e recuperação de desastres
|Serviço|Descrição|
|------|--------|
| [Azure&nbsp;Backup](/azure/backup/backup-introduction-to-azure-backup)| Um serviço baseado em Azure usado para fazer cópias de segurança e restaurar dados na nuvem Azure. |
| [Recuperação&nbsp;&nbsp;do site azure](/azure/site-recovery/site-recovery-overview)|Um serviço online que replica cargas de trabalho em máquinas físicas e virtuais (VMs) de um local primário para um local secundário para permitir a recuperação de serviços após uma falha. |

## <a name="networking"></a>Redes
|Serviço|Descrição|
|------|--------|
| [Grupos&nbsp;de Segurança da Rede&nbsp;](/azure/virtual-network/virtual-networks-nsg)| Uma função de controlo de acesso baseada em rede utilizando uma túnica de 5-tuple para tomar ou negar decisões.  |
| [Azure VPN Gateway](/azure/vpn-gateway/vpn-gateway-about-vpngateways)| Um dispositivo de rede utilizado como ponto final VPN para permitir o acesso transversal às Redes Virtuais Azure.  |
| [Gateway de Aplicação do Azure](/azure/application-gateway/application-gateway-introduction)|Um equilibrador de carga de aplicação web avançado que pode encaminhar com base em URL e realizar a descarga SSL. |
|[Firewall de aplicação web](/azure/frontdoor/waf-overview) (WAF)|Uma funcionalidade do Application Gateway que fornece proteção centralizada das suas aplicações web a partir de explorações e vulnerabilidades comuns|
| [Equilibrador de carga Azure](/azure/load-balancer/load-balancer-overview)|Um equilibrador de carga de rede de aplicações TCP/UDP. |
| [Microsoft Azure ExpressRoute](/azure/expressroute/expressroute-introduction)| Uma ligação WAN dedicada entre as redes no local e as Redes Virtuais Azure. |
| [Gestor de Tráfego Azure](/azure/traffic-manager/traffic-manager-overview)| Um equilibrador de carga DNS global.|
| [Procuração de aplicação Azure](/azure/active-directory/active-directory-application-proxy-get-started)| Uma extremidade frontal autenticada usada para garantir o acesso remoto para aplicações web alojadas no local. |
|[Azure Firewall](/azure/firewall/overview)|Um serviço de segurança de rede gerido e baseado na nuvem que protege os seus recursos da Rede Virtual Azure.|
|[Proteção contra DDoS do Azure](/azure/virtual-network/ddos-protection-overview)|Combinado com as melhores práticas de design de aplicações, fornece defesa contra ataques DDoS.|
|[Pontos finais do serviço de rede virtual](/azure/virtual-network/virtual-network-service-endpoints-overview)|Estende o espaço de endereços privados da sua rede virtual e a identidade do seu VNet aos serviços Azure, através de uma ligação direta.|