---
title: Pontos finais de serviço de rede virtual para o Azure Key Vault
description: Saiba como os pontos finais do serviço de rede virtual para O Azure Key Vault permitem restringir o acesso a uma rede virtual especificada, incluindo cenários de utilização.
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 42d388aed618a5a92212152166573719c9e973c6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371027"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Pontos finais de serviço de rede virtual para o Azure Key Vault

Os pontos finais de serviço de rede virtual para Azure Key Vault permitem-lhe restringir o acesso a uma rede virtual especificada. Os pontos finais também permitem restringir o acesso a uma lista de intervalos de endereços IPv4 (versão 4 do protocolo de internet). Qualquer utilizador que se conecte ao cofre de fora dessas fontes é negado acesso.

Há uma exceção importante a esta restrição. Se um utilizador tiver optado por permitir serviços de confiança da Microsoft, as ligações desses serviços são deixadas através da firewall. Por exemplo, estes serviços incluem Office 365 Exchange Online, Office 365 SharePoint Online, Azure compute, Azure Resource Manager e Azure Backup. Estes utilizadores ainda precisam de apresentar um token de Diretório Azure Ative válido, e devem ter permissões (configuradas como políticas de acesso) para executar a operação solicitada. Para obter mais informações, consulte [os pontos finais do serviço de rede Virtual.](../../virtual-network/virtual-network-service-endpoints-overview.md)

## <a name="usage-scenarios"></a>Cenários de utilização

Pode configurar [firewalls key Vault e redes virtuais](network-security.md) para negar o acesso ao tráfego de todas as redes (incluindo o tráfego de internet) por padrão. Pode conceder acesso ao tráfego a partir de redes virtuais específicas do Azure e de intervalos de endereços IP de internet pública, permitindo-lhe construir um limite de rede seguro para as suas aplicações.

> [!NOTE]
> As firewalls key Vault e as regras de rede virtuais aplicam-se apenas ao plano de [dados](secure-your-key-vault.md#data-plane-access-control) do Key Vault. As operações do avião de controlo key Vault (tais como criar, excluir e modificar operações, definir políticas de acesso, definir firewalls e regras de rede virtuais) não são afetadas por firewalls e regras de rede virtuais.

Aqui estão alguns exemplos de como pode usar pontos finais de serviço:

* Está a usar o Key Vault para armazenar chaves de encriptação, segredos de aplicação e certificados, e pretende bloquear o acesso ao cofre da internet pública.
* Pretende bloquear o acesso ao cofre de chaves para que apenas a sua aplicação, ou uma pequena lista de anfitriões designados, possa ligar-se ao seu cofre de chaves.
* Tem uma aplicação em execução na sua rede virtual Azure, e esta rede virtual está bloqueada para todo o tráfego de entrada e saída. A sua aplicação ainda precisa de se ligar ao Key Vault para obter segredos ou certificados ou usar chaves criptográficas.

## <a name="trusted-services"></a>Serviços de confiança

Aqui está uma lista de serviços fidedignos que podem aceder a um cofre chave se a opção **de serviços fidedignos** estiver ativada.

|Serviço de confiança|Cenários de utilização apoiados|
| --- | --- |
|Serviço de implementação de máquinas virtuais Azure|[Implementar certificados para VMs a partir do Key Vault gerido pelo cliente](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault).|
|Serviço de implementação do modelo do Gestor de Recursos Azure|[Passe valores seguros durante a implantação](../../azure-resource-manager/templates/key-vault-parameter.md).|
|Gateway de aplicação Azure v2 SKU|[Terminação TLS com certificados do Key Vault](../../application-gateway/key-vault-certs.md)|
|Serviço de encriptação de volume de encriptação de disco Azure|Permitir o acesso à Chave BitLocker (Windows VM) ou DM Passphrase (Linux VM) e à Chave de Encriptação, durante a implementação da máquina virtual. Isto permite [a encriptação do disco Azure](../../security/fundamentals/encryption-overview.md).|
|Azure Backup|Permitir a cópia de segurança e restaurar as chaves e segredos relevantes durante a cópia de segurança das Máquinas Virtuais Azure, utilizando [o Azure Backup](../../backup/backup-overview.md).|
|Troca Online & SharePoint Online|Permitir o acesso à chave do cliente para encriptação do serviço de armazenamento Azure com [a chave do cliente.](/microsoft-365/compliance/customer-key-overview)|
|Azure Information Protection|Permitir o acesso à chave do inquilino para [a Azure Information Protection.](/azure/information-protection/what-is-information-protection)|
|Serviço de Aplicações do Azure|[Implementar o Certificado de Aplicação Web Azure através do Cofre de Chaves](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Base de Dados SQL do Azure|[Encriptação de dados transparente com suporte de chave própria para Azure SQL Database e Azure Synapse Analytics](../../azure-sql/database/transparent-data-encryption-byok-overview.md?view=sql-server-2017&preserve-view=true&viewFallbackFrom=azuresqldb-current).|
|Storage do Azure|[Encriptação do serviço de armazenamento utilizando chaves geridas pelo cliente no Cofre da Chave Azure](../../storage/common/customer-managed-keys-configure-key-vault.md).|
|Azure Data Lake Store|[Encriptação de dados na Azure Data Lake Store](../../data-lake-store/data-lake-store-encryption.md) com uma chave gerida pelo cliente.|
|Azure Synapse Analytics|[Encriptação de dados utilizando chaves geridas pelo cliente no Cofre da Chave Azure](../../synapse-analytics/security/workspaces-encryption.md)|
|Azure Databricks|[Serviço de análise rápido, fácil e colaborativo apache spark baseado em Apache Spark](/azure/databricks/scenarios/what-is-azure-databricks)|
|API Management do Azure|[Implementar certificados para domínio personalizado a partir do Cofre de Chaves usando MSI](../../api-management/api-management-howto-use-managed-service-identity.md#use-ssl-tls-certificate-from-azure-key-vault)|
|Azure Data Factory|[Buscar credenciais de loja de dados no Cofre de Dados da Data Factory](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[Permitir o acesso a um cofre chave para cenário de chaves geridas pelo cliente](../../event-hubs/configure-customer-managed-key.md)|
|Service Bus do Azure|[Permitir o acesso a um cofre chave para cenário de chaves geridas pelo cliente](../../service-bus-messaging/configure-customer-managed-key.md)|
|Importar/Exportar do Microsoft Azure| [Utilize chaves geridas pelo cliente no Cofre chave Azure para o serviço de importação/exportação](../../import-export/storage-import-export-encryption-key-portal.md)
|Registo de Contentores do Azure|[Encriptação do registo utilizando chaves geridas pelo cliente](../../container-registry/container-registry-customer-managed-keys.md)

> [!NOTE]
> Tem de configurar as políticas de acesso ao Cofre chave relevantes para permitir que os serviços correspondentes tenham acesso ao Key Vault.

## <a name="next-steps"></a>Passos seguintes

- Para obter instruções passo a passo, consulte [firewalls Configure Key Vault e redes virtuais](network-security.md)
- ver a visão geral de segurança do [Cofre chave Azure](security-overview.md)
