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
ms.openlocfilehash: 2617ae7bd1c761ae7977eac518c2e40ca55da00c
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883232"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Pontos de extremidade de serviço de rede virtual para Azure Key Vault

Os pontos de extremidade de serviço de rede virtual para Azure Key Vault permitem restringir o acesso a uma rede virtual especificada. Os pontos de extremidade também permitem restringir o acesso a uma lista de intervalos de endereços IPv4 (protocolo IP versão 4). Qualquer usuário que se conecta ao cofre de chaves de fora dessas fontes tem o acesso negado.

Há uma exceção importante para essa restrição. Se um usuário tiver optado por permitir serviços confiáveis da Microsoft, as conexões desses serviços permitirão pelo firewall. Por exemplo, esses serviços incluem o Office 365 Exchange Online, Office 365 SharePoint Online, computação do Azure, Azure Resource Manager e backup do Azure. Esses usuários ainda precisam apresentar um token de Azure Active Directory válido e devem ter permissões (configuradas como políticas de acesso) para executar a operação solicitada. Para obter mais informações, consulte [pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Cenários de utilização

Você pode configurar [Key Vault firewalls e redes virtuais](key-vault-network-security.md) para negar acesso ao tráfego de todas as redes (incluindo o tráfego de Internet) por padrão. Você pode conceder acesso ao tráfego de redes virtuais do Azure específicas e intervalos de endereços IP públicos da Internet, permitindo que você crie um limite de rede seguro para seus aplicativos.

> [!NOTE]
> Key Vault firewalls e regras de rede virtual se aplicam somente ao [plano de dados](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) de Key Vault. Key Vault operações de plano de controle (como operações de criação, exclusão e modificação, definição de políticas de acesso, definição de firewalls e regras de rede virtual) não são afetadas por firewalls e regras de rede virtual.

Aqui estão alguns exemplos de como você pode usar pontos de extremidade de serviço:

* Você está usando Key Vault para armazenar chaves de criptografia, segredos de aplicativo e certificados e deseja bloquear o acesso ao cofre de chaves da Internet pública.
* Você deseja bloquear o acesso ao seu cofre de chaves para que somente seu aplicativo ou uma lista curta de hosts designados possam se conectar ao cofre de chaves.
* Você tem um aplicativo em execução em sua rede virtual do Azure e essa rede virtual está bloqueada para todo o tráfego de entrada e saída. Seu aplicativo ainda precisa se conectar ao Key Vault para buscar segredos ou certificados ou usar chaves criptográficas.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Configurar Key Vault firewalls e redes virtuais

Aqui estão as etapas necessárias para configurar firewalls e redes virtuais. Essas etapas se aplicam se você estiver usando o PowerShell, o CLI do Azure ou o portal do Azure.

1. Habilite o [log de Key Vault](key-vault-logging.md) para ver os logs de acesso detalhados. Isso ajuda no diagnóstico, quando as regras de rede virtual e firewalls impedem o acesso a um cofre de chaves. (Essa etapa é opcional, mas altamente recomendável.)
2. Habilite **pontos de extremidade de serviço para o Key Vault** para redes virtuais e sub-redes de destino.
3. Defina firewalls e regras de rede virtual para um cofre de chaves para restringir o acesso a esse cofre de chaves de redes virtuais, sub-redes e intervalos de endereços IPv4 específicos.
4. Se esse cofre de chaves precisar ser acessível por qualquer serviço confiável da Microsoft, habilite a opção para permitir que os **serviços confiáveis do Azure** se conectem ao key Vault.

Para obter mais informações, consulte [configurar Azure Key Vault firewalls e redes virtuais](key-vault-network-security.md).

> [!IMPORTANT]
> Depois que as regras de firewall estiverem em vigor, os usuários só poderão executar Key Vault operações do [plano de dados](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) quando suas solicitações se originarem de redes virtuais permitidas ou intervalos de endereços IPv4. Isso também se aplica ao acesso a Key Vault do portal do Azure. Embora os usuários possam navegar para um cofre de chaves do portal do Azure, eles podem não ser capazes de listar chaves, segredos ou certificados se o computador cliente não estiver na lista de permissões. Isso também afeta o seletor de Key Vault por outros serviços do Azure. Os usuários podem ver a lista de cofres de chaves, mas não as chaves de lista, se as regras de firewall impedirem seu computador cliente.


> [!NOTE]
> Lembre-se das seguintes limitações de configuração:
> * São permitidas no máximo 127 regras de rede virtual e 127 regras IPv4. 
> * Não há suporte para os intervalos de endereços pequenos que usam os tamanhos de prefixo "/31" ou "/32". Em vez disso, configure esses intervalos usando regras de endereço IP individuais.
> * As regras de rede IP só são permitidas para endereços IP públicos. Os intervalos de endereços IP reservados para redes privadas (conforme definido no RFC 1918) não são permitidos em regras de IP. Redes privadas incluem endereços que começam com **10.** , **172.16-31**e **192,168.** . 
> * Somente os endereços IPv4 têm suporte no momento.

## <a name="trusted-services"></a>Serviços confiáveis

Aqui está uma lista de serviços confiáveis que têm permissão para acessar um cofre de chaves se a opção **permitir serviços confiáveis** estiver habilitada.

|Serviço confiável|Cenários de utilização|
| --- | --- |
|Serviço de implementação de Máquinas Virtuais do Azure|[Implante certificados em VMs de Key Vault gerenciadas pelo cliente](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/).|
|Serviço de implantação de modelo do Azure Resource Manager|[Passe valores seguros durante a implantação](../azure-resource-manager/resource-manager-keyvault-parameter.md).|
|Azure Disk Encryption o serviço de criptografia de volume|Permitir o acesso à chave do BitLocker (VM do Windows) ou à senha do DM (VM do Linux) e chave de criptografia de chave durante a implantação da máquina virtual. Isso habilita [Azure Disk Encryption](../security/azure-security-disk-encryption.md).|
|Azure Backup|Permitir backup e restauração de chaves e segredos relevantes durante o backup de máquinas virtuais do Azure usando o [backup do Azure](../backup/backup-introduction-to-azure-backup.md).|
|Exchange Online & SharePoint Online|Permitir acesso à chave do cliente para o Azure Criptografia do Serviço de Armazenamento com a [chave do cliente](https://support.office.com/article/Controlling-your-data-in-Office-365-using-Customer-Key-f2cd475a-e592-46cf-80a3-1bfb0fa17697).|
|Azure Information Protection|Permitir o acesso à chave de locatário da [proteção de informações do Azure.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|Serviço de Aplicações do Azure|[Implante o certificado do aplicativo Web do Azure por meio de Key Vault](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Base de Dados SQL do Azure|[Transparent Data Encryption com suporte de Bring your own Key para o banco de dados SQL do Azure e o data warehouse](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current).|
|Storage do Azure|[Criptografia do serviço de armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
|Azure Data Lake Store|[Criptografia de dados em Azure data Lake Store](../data-lake-store/data-lake-store-encryption.md) com uma chave gerenciada pelo cliente.|
|Azure databricks|[Serviço de análise baseado em Apache Spark rápido, fácil e colaborativo](../azure-databricks/what-is-azure-databricks.md)|
|Gestão de API do Azure|[Implantar certificados para o domínio personalizado de Key Vault usando o MSI](../api-management/api-management-howto-use-managed-service-identity.md#use-the-managed-service-identity-to-access-other-resources)|



> [!NOTE]
> Você deve configurar as políticas de acesso Key Vault relevantes para permitir que os serviços correspondentes obtenham acesso ao Key Vault.

## <a name="next-steps"></a>Passos Seguintes

* [Proteger o seu cofre de chaves](key-vault-secure-your-key-vault.md)
* [Configurar Azure Key Vault firewalls e redes virtuais](key-vault-network-security.md)
