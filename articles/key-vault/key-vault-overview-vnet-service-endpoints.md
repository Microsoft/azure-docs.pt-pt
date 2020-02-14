---
title: Pontos de extremidade de serviço de rede virtual para Azure Key Vault Azure Key Vault | Microsoft Docs
description: Visão geral dos pontos de extremidade de serviço de rede virtual para Key Vault
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.topic: conceptual
ms.openlocfilehash: b9c458b42f7436377d41cf5095d404dedbe0ffd1
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191616"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Pontos de extremidade de serviço de rede virtual para Azure Key Vault

Os pontos de extremidade de serviço de rede virtual para Azure Key Vault permitem restringir o acesso a uma rede virtual especificada. Os pontos de extremidade também permitem restringir o acesso a uma lista de intervalos de endereços IPv4 (protocolo IP versão 4). Qualquer usuário que se conecta ao cofre de chaves de fora dessas fontes tem o acesso negado.

Há uma exceção importante para essa restrição. Se um usuário tiver optado por permitir serviços confiáveis da Microsoft, as conexões desses serviços permitirão pelo firewall. Por exemplo, esses serviços incluem o Office 365 Exchange Online, Office 365 SharePoint Online, computação do Azure, Azure Resource Manager e backup do Azure. Esses usuários ainda precisam apresentar um token de Azure Active Directory válido e devem ter permissões (configuradas como políticas de acesso) para executar a operação solicitada. Para mais informações, consulte [pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md)do serviço de rede Virtual .

## <a name="usage-scenarios"></a>Cenários de utilização

Pode configurar [firewalls key vault e redes virtuais](key-vault-network-security.md) para negar o acesso ao tráfego de todas as redes (incluindo o tráfego de internet) por padrão. Você pode conceder acesso ao tráfego de redes virtuais do Azure específicas e intervalos de endereços IP públicos da Internet, permitindo que você crie um limite de rede seguro para seus aplicativos.

> [!NOTE]
> As firewalls key vault e as regras de rede virtual aplicam-se apenas ao plano de [dados](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) do Key Vault. Key Vault operações de plano de controle (como operações de criação, exclusão e modificação, definição de políticas de acesso, definição de firewalls e regras de rede virtual) não são afetadas por firewalls e regras de rede virtual.

Aqui estão alguns exemplos de como você pode usar pontos de extremidade de serviço:

* Você está usando Key Vault para armazenar chaves de criptografia, segredos de aplicativo e certificados e deseja bloquear o acesso ao cofre de chaves da Internet pública.
* Você deseja bloquear o acesso ao seu cofre de chaves para que somente seu aplicativo ou uma lista curta de hosts designados possam se conectar ao cofre de chaves.
* Você tem um aplicativo em execução em sua rede virtual do Azure e essa rede virtual está bloqueada para todo o tráfego de entrada e saída. Seu aplicativo ainda precisa se conectar ao Key Vault para buscar segredos ou certificados ou usar chaves criptográficas.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Configurar Key Vault firewalls e redes virtuais

Aqui estão as etapas necessárias para configurar firewalls e redes virtuais. Essas etapas se aplicam se você estiver usando o PowerShell, o CLI do Azure ou o portal do Azure.

1. Ative a [exploração de registos](key-vault-logging.md) do Cofre chave para ver registos de acesso detalhados. Isso ajuda no diagnóstico, quando as regras de rede virtual e firewalls impedem o acesso a um cofre de chaves. (Essa etapa é opcional, mas altamente recomendável.)
2. Ative **pontos finais** de serviço para o cofre chave para redes virtuais-alvo e subredes.
3. Defina firewalls e regras de rede virtual para um cofre de chaves para restringir o acesso a esse cofre de chaves de redes virtuais, sub-redes e intervalos de endereços IPv4 específicos.
4. Se este cofre de chaves precisar de ser acessível por quaisquer serviços confiáveis da Microsoft, permita que a opção de permitir que os **Serviços Fidedignos do Azure** se conectem ao Key Vault.

Para mais informações, consulte as firewalls do [Cofre chave Configure Azure e as redes virtuais](key-vault-network-security.md).

> [!IMPORTANT]
> Após a aplicação das regras de firewall, os utilizadores só podem realizar operações de plano de [dados](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) key Vault quando os seus pedidos têm origem em redes virtuais permitidas ou em intervalos de endereços IPv4. Isso também se aplica ao acesso a Key Vault do portal do Azure. Embora os usuários possam navegar para um cofre de chaves do portal do Azure, eles podem não ser capazes de listar chaves, segredos ou certificados se o computador cliente não estiver na lista de permissões. Isso também afeta o seletor de Key Vault por outros serviços do Azure. Os usuários podem ver a lista de cofres de chaves, mas não as chaves de lista, se as regras de firewall impedirem seu computador cliente.


> [!NOTE]
> Lembre-se das seguintes limitações de configuração:
> * São permitidas no máximo 127 regras de rede virtual e 127 regras IPv4. 
> * Não há suporte para os intervalos de endereços pequenos que usam os tamanhos de prefixo "/31" ou "/32". Em vez disso, configure esses intervalos usando regras de endereço IP individuais.
> * As regras de rede IP só são permitidas para endereços IP públicos. Os intervalos de endereços IP reservados para redes privadas (conforme definido no RFC 1918) não são permitidos em regras de IP. As redes privadas incluem endereços que começam com **10.** , **172.16-31**e **192.168.** 
> * Somente os endereços IPv4 têm suporte no momento.

## <a name="trusted-services"></a>Serviços confiáveis

Aqui está uma lista de serviços fidedignos que são autorizados a aceder a um cofre chave se a opção **de serviços fidedignos permitir.**

|Serviço confiável|Cenários de uso com suporte|
| --- | --- |
|Serviço de implantação de máquinas virtuais do Azure|[Implementar certificados para VMs a partir do Cofre chave gerido pelo cliente](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/).|
|Serviço de implantação de modelo do Azure Resource Manager|[Passe valores seguros durante a implantação](../azure-resource-manager/templates/key-vault-parameter.md).|
|Azure Disk Encryption o serviço de criptografia de volume|Permitir o acesso à chave do BitLocker (VM do Windows) ou à senha do DM (VM do Linux) e chave de criptografia de chave durante a implantação da máquina virtual. Isto permite a encriptação do [disco Azure](../security/fundamentals/encryption-overview.md).|
|Azure Backup|Permitir a cópia de segurança e restaurar as chaves e segredos relevantes durante a cópia de segurança das Máquinas Virtuais Azure, utilizando [o Backup Azure](../backup/backup-introduction-to-azure-backup.md).|
|Exchange Online & SharePoint Online|Permitir o acesso à chave do cliente para encriptação do serviço de armazenamento Azure com [chave do cliente](/microsoft-365/compliance/customer-key-overview).|
|Azure Information Protection|Permitir o acesso à chave de inquilinos para [a Proteção de Informação Azure.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|Serviço de Aplicações do Azure|[Implemente o certificado de aplicação web Azure através do cofre da chave.](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html)|
|Base de Dados SQL do Azure|[Encriptação transparente de dados com trazer o seu próprio suporte chave para base de dados Azure SQL e Armazém de Dados](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current).|
|Storage do Azure|[Encriptação do serviço de armazenamento utilizando chaves geridas pelo cliente no Cofre de Chaves Azure](../storage/common/storage-service-encryption-customer-managed-keys.md).|
|Azure Data Lake Store|[Encriptação de dados na Azure Data Lake Store](../data-lake-store/data-lake-store-encryption.md) com uma chave gerida pelo cliente.|
|Azure Databricks|[Serviço de análise rápido, fácil e colaborativo apache spark](../azure-databricks/what-is-azure-databricks.md)|
|API Management do Azure|[Implementar certificados para domínio personalizado a partir do cofre de chaves usando MSI](../api-management/api-management-howto-use-managed-service-identity.md#use-the-managed-service-identity-to-access-other-resources)|
|Azure Data Factory|[Buscar credenciais de loja de dados no Cofre chave da Fábrica de Dados](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[Permitir o acesso a um cofre chave para o cenário de chaves gerida pelo cliente](https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key)|
|Service Bus do Azure|[Permitir o acesso a um cofre chave para o cenário de chaves gerida pelo cliente](https://docs.microsoft.com/azure/service-bus-messaging/configure-customer-managed-key)|


> [!NOTE]
> Você deve configurar as políticas de acesso Key Vault relevantes para permitir que os serviços correspondentes obtenham acesso ao Key Vault.

## <a name="next-steps"></a>Passos seguintes

* [Proteger o seu cofre de chaves](key-vault-secure-your-key-vault.md)
* [Configure firewalls e redes virtuais do Cofre de Chaves](key-vault-network-security.md)
