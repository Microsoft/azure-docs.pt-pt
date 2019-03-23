---
ms.assetid: ''
title: Pontos finais de serviço de rede virtual para o Azure Key Vault, Azure Key Vault | Documentos da Microsoft
description: Descrição geral dos pontos finais de serviço de rede virtual para o Key Vault
services: key-vault
author: amitbapat
ms.author: ambapat
manager: barbkess
ms.date: 01/02/2019
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.openlocfilehash: bb5770f4e09c0ed764d17998936c0fe97bdaebfb
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370640"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Pontos finais de serviço de rede virtual para o Azure Key Vault

Os pontos de extremidade de serviço de rede virtual para o Azure Key Vault permitem-lhe restringir o acesso a uma rede virtual especificada. Os pontos de extremidade também permitem-lhe restringir o acesso a uma lista de intervalos de endereços IPv4 (protocolo IP versão 4). Acesso é negado por qualquer utilizador ligar ao seu Cofre de chaves de fora essas origens.

Existe uma exceção importante para esta restrição. Se um utilizador tiver optado por participar para permitir que os serviços Microsoft fidedignos, ligações desses serviços são permitem através da firewall. Por exemplo, esses serviços incluem o Office 365 Exchange Online, SharePoint Online do Office 365, Azure computação, o Azure Resource Manager e o Azure Backup. Tais usuários ainda precisam apresentar um token válido do Azure Active Directory e tem de ter permissões (configuradas como políticas de acesso) para efetuar a operação pedida. Para obter mais informações, consulte [pontos finais de serviço de rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Cenários de utilização

Pode configurar [Key Vault firewalls e redes virtuais](key-vault-network-security.md) para negar acesso para o tráfego de todas as redes (incluindo o tráfego de internet) por predefinição. Pode conceder acesso para tráfego de redes virtuais do Azure específicas e a internet pública, endereço IP, intervalos, que lhe permite criar um limite de rede segura para as suas aplicações.

> [!NOTE]
> Firewalls de Key Vault e regras de rede virtual só se aplicam para o [plano de dados](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) do Cofre de chaves. Operações do painel de controle Cofre de chaves (como criar, eliminar e modificar as operações, políticas de acesso de definição, firewalls de definição e regras de rede virtual) não são afetadas por firewalls e regras de rede virtual.

Aqui estão alguns exemplos de como pode utilizar pontos finais de serviço:

* Estiver a utilizar o Key Vault para armazenar chaves de encriptação, segredos da aplicação e certificados e que pretende bloquear o acesso ao seu Cofre de chaves a partir da internet pública.
* Deseja bloquear o acesso ao seu Cofre de chaves para que apenas seu aplicativo, ou uma lista curta de hosts designados, pode ligar ao seu Cofre de chaves.
* Tem uma aplicação em execução na sua rede virtual do Azure, e esta rede virtual é bloqueada para todo o tráfego de entrada e saído. Seu aplicativo ainda precisa de ligar para o Key Vault para obter segredos ou certificados ou utilize chaves criptográficas.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Configurar o Cofre de chaves firewalls e redes virtuais

Aqui estão as etapas necessárias para configurar firewalls e redes virtuais. Estes passos aplicam-se se estiver a utilizar o PowerShell, a CLI do Azure ou o portal do Azure.

1. Ativar [registo de Cofre de chaves](key-vault-logging.md) para ver os registos de acesso detalhadas. Isto ajuda a diagnostics, quando os firewalls e regras de rede virtual impedem o acesso a um cofre de chaves. (Este passo é opcional, mas altamente recomendado.)
2. Ativar **pontos finais para o Cofre de chaves de serviço** para sub-redes e redes virtuais de destino.
3. Definir regras de rede virtual para um cofre de chaves restringir o acesso ao Cofre de chaves de redes virtuais específicos, sub-redes e intervalos de endereços IPv4 e de firewalls.
4. Se este Cofre de chaves tem de ser acessível para todos os serviços Microsoft fidedignos, ative a opção permitir **serviços do Azure confiáveis** para estabelecer ligação ao Cofre de chaves.

Para obter mais informações, consulte [configurar o Azure Key Vault firewalls e redes virtuais](key-vault-network-security.md).

> [!IMPORTANT]
> Depois de regras de firewall estão em vigor, os utilizadores apenas podem executar o Key Vault [plano de dados](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) permitidas de operações quando os pedidos têm origem nos intervalos de endereços IPv4 ou as redes virtuais. Isso também se aplica a aceder ao Cofre de chaves do portal do Azure. Embora os usuários podem navegar para um cofre de chaves do portal do Azure, eles poderão não conseguir listar chaves, segredos ou certificados se os respetivos computadores cliente não está na lista de permitidos. Isso também afeta o Seletor de Cofre de chave por outros serviços do Azure. Os utilizadores poderão ver a lista de cofres de chaves, mas não listar as chaves, se as regras de firewall impedem os respetivos computadores cliente.


> [!NOTE]
> Tenha em atenção as seguintes limitações de configuração:
> * É permitido um máximo de 127 regras de rede virtual e 127 regras de IPv4. 
> * Intervalos de endereços pequenos que utilizam o "/ 31" ou "/ 32" prefixo tamanhos não são suportados. Em vez disso, configure estes intervalos com regras de endereços IP individuais.
> * Regras de rede IP só são permitidas para endereços IP públicos. Intervalos de endereços IP reservados para redes privadas (conforme definido em RFC 1918) não são permitidos em regras de IP. Redes privadas incluem endereços que comecem com **10.**, **172.16 31**, e **192.168.**. 
> * Apenas endereços IPv4 são suportados neste momento.

## <a name="trusted-services"></a>Serviços confiáveis

Aqui está uma lista de serviços confiáveis que têm permissão para aceder um cofre de chaves, se o **Permitr serviços fidedignos** opção está ativada.

|Serviço de confiança|Cenários de utilização|
| --- | --- |
|Serviço de implementação de Máquinas Virtuais do Azure|[Implementar certificados para VMs a partir do Cofre de chaves geridas pelo cliente](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/).|
|Serviço de implementação de modelos do Azure Resource Manager|[Transmitir valores seguros durante a implementação](../azure-resource-manager/resource-manager-keyvault-parameter.md).|
|Azure Disk Encryption para serviço encriptação de volumes|Permita o acesso à chave do BitLocker (Windows VM) ou a frase de acesso do DM (Linux VM) e a chave de encriptação de chave, durante a implementação da máquina virtual. Isto permite [do Azure Disk Encryption](../security/azure-security-disk-encryption.md).|
|Azure Backup|Permitir cópia de segurança e restauro de relevantes chaves e segredos durante a cópia de segurança de máquinas virtuais do Azure, utilizando [cópia de segurança do Azure](../backup/backup-introduction-to-azure-backup.md).|
|Exchange Online & SharePoint Online|Permitir o acesso à chave do cliente para a encriptação do serviço de armazenamento do Azure com [chave de cliente](https://support.office.com/article/Controlling-your-data-in-Office-365-using-Customer-Key-f2cd475a-e592-46cf-80a3-1bfb0fa17697).|
|Azure Information Protection|Permitir o acesso à chave de inquilino para [do Azure Information Protection.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|Serviço de Aplicações do Azure|[Implementar o certificado de aplicação Web do Azure através do Key Vault](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/).|
|Base de Dados SQL do Azure|[Encriptação de dados transparente com suporte de traga a sua própria chave para a base de dados do Azure SQL e o armazém de dados](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current).|
|Storage do Azure|[Encriptação do serviço de armazenamento a utilizar chaves geridas pelo cliente no Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
|Azure Data Lake Store|[Encriptação de dados no Azure Data Lake Store](../data-lake-store/data-lake-store-encryption.md) com uma chave gerida pelo cliente.|
|O Azure databricks|[Serviço de análise com base no Apache Spark rápida, fácil e de colaboração](../azure-databricks/what-is-azure-databricks.md)|



> [!NOTE]
> Tem de configurar as políticas de acesso do Key Vault relevantes para permitir que os serviços correspondentes obter acesso ao Key Vault.

## <a name="next-steps"></a>Passos Seguintes

* [Proteger o seu cofre de chaves](key-vault-secure-your-key-vault.md)
* [Configurar o Azure Key Vault firewalls e redes virtuais](key-vault-network-security.md)
