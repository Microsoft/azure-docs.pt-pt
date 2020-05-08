---
title: Pontos finais de serviço de rede virtual para Cofre chave Azure - Cofre chave Azure / Microsoft Docs
description: Visão geral dos pontos finais do serviço de rede virtual para key vault
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: bd67c3b7eed6b3ce3730bd48cda69d85aa276df4
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930595"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Pontos finais de serviço de rede virtual para cofre chave Azure

Os pontos finais do serviço de rede virtual para o Cofre de Chaves Azure permitem-lhe restringir o acesso a uma rede virtual especificada. Os pontos finais também permitem restringir o acesso a uma lista de gamas de endereços IPv4 (versão 4 do protocolo de internet). Qualquer utilizador que ligue ao seu cofre chave de fora dessas fontes é negado acesso.

Há uma importante exceção a esta restrição. Se um utilizador optou por permitir serviços fiáveis da Microsoft, as ligações desses serviços são deixadas através da firewall. Por exemplo, estes serviços incluem office 365 Exchange Online, Office 365 SharePoint Online, Azure compute, Azure Resource Manager e Azure Backup. Estes utilizadores ainda precisam de apresentar um símbolo de Diretório Ativo Azure válido, e devem ter permissões (configuradas como políticas de acesso) para executar a operação solicitada. Para mais informações, consulte [pontos finais](../../virtual-network/virtual-network-service-endpoints-overview.md)do serviço de rede Virtual .

## <a name="usage-scenarios"></a>Cenários de utilização

Pode configurar [firewalls key vault e redes virtuais](network-security.md) para negar o acesso ao tráfego de todas as redes (incluindo o tráfego de internet) por padrão. Pode conceder acesso ao tráfego a partir de redes virtuais específicas do Azure e de gamas de endereços IP da internet pública, permitindo-lhe construir um limite de rede seguro para as suas aplicações.

> [!NOTE]
> As firewalls key vault e as regras de rede virtual aplicam-se apenas ao plano de [dados](secure-your-key-vault.md#data-plane-access-control) do Key Vault. As operações de planode controlo do Cofre Chave (tais como criar, eliminar e modificar operações, definir políticas de acesso, definir firewalls e regras de rede virtual) não são afetadas por firewalls e regras de rede virtuais.

Aqui estão alguns exemplos de como pode utilizar pontos finais de serviço:

* Está a usar o Key Vault para armazenar chaves de encriptação, segredos de aplicação e certificados, e quer bloquear o acesso ao seu cofre chave a partir da internet pública.
* Você quer bloquear o acesso ao seu cofre chave para que apenas a sua aplicação, ou uma pequena lista de anfitriões designados, possa ligar-se ao seu cofre chave.
* Tem uma aplicação em execução na sua rede virtual Azure, e esta rede virtual está bloqueada para todo o tráfego de entrada e saída. A sua aplicação ainda precisa de se ligar ao Key Vault para obter segredos ou certificados, ou usar chaves criptográficas.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Configurar firewalls e redes virtuais do Key Vault

Aqui estão os passos necessários para configurar firewalls e redes virtuais. Estes passos aplicam-se quer esteja a utilizar o PowerShell, o Azure CLI ou o portal Azure.

1. Ativar a [exploração do cofre](logging.md)de chaves ) para ver registos de acesso detalhados. Isto ajuda nos diagnósticos, quando firewalls e regras de rede virtual impedem o acesso a um cofre chave. (Este passo é opcional, mas altamente recomendado.)
2. Ative **pontos finais** de serviço para o cofre chave para redes virtuais-alvo e subredes.
3. Detete as firewalls e as regras de rede virtual para um cofre chave para restringir o acesso a esse cofre chave a partir de redes virtuais específicas, subredes e intervalos de endereços IPv4.
4. Se este cofre de chaves precisar de ser acessível por quaisquer serviços confiáveis da Microsoft, permita que a opção de permitir que os **Serviços Fidedignos do Azure** se conectem ao Key Vault.

Para mais informações, consulte as firewalls do [Cofre chave Configure Azure e as redes virtuais](network-security.md).

> [!IMPORTANT]
> Após a aplicação das regras de firewall, os utilizadores só podem realizar operações de plano de [dados](secure-your-key-vault.md#data-plane-access-control) key Vault quando os seus pedidos têm origem em redes virtuais permitidas ou em intervalos de endereços IPv4. Isto também se aplica ao acesso ao Cofre chave do portal Azure. Embora os utilizadores possam navegar para um cofre chave a partir do portal Azure, eles podem não ser capazes de listar chaves, segredos ou certificados se a sua máquina cliente não estiver na lista permitida. Isto também afeta o Key Vault Picker por outros serviços Azure. Os utilizadores podem ser capazes de ver a lista de cofres chave, mas não listar chaves, se as regras de firewall impedirem a sua máquina de clientes.


> [!NOTE]
> Esteja atento às seguintes limitações de configuração:
> * São permitidas 127 regras de rede virtual e 127 regras iPv4. 
> * Não são suportadas gamas de endereços pequenos que utilizem os tamanhos de prefixo "/31" ou "/32". Em vez disso, configure estas gamas utilizando regras individuais de endereço IP.
> * As regras da rede IP só são permitidas para endereços IP públicos. As gamas de endereços IP reservadas para redes privadas (tal como definidas no RFC 1918) não são permitidas nas regras ip. As redes privadas incluem endereços que começam com **10.**, **172.16-31**e **192.168.** 
> * Apenas os endereços IPv4 são suportados neste momento.

## <a name="trusted-services"></a>Serviços de confiança

Aqui está uma lista de serviços fidedignos que são autorizados a aceder a um cofre chave se a opção **de serviços fidedignos permitir.**

|Serviço de confiança|Cenários de utilização suportados|
| --- | --- |
|Serviço de implantação de Máquinas Virtuais Azure|[Implementar certificados para VMs a partir do Cofre chave gerido pelo cliente](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/).|
|Serviço de implementação de modelo de gestor de recursos Azure|[Passe valores seguros durante a implantação](../../azure-resource-manager/templates/key-vault-parameter.md).|
|Serviço de encriptação de volume de encriptação de disco azure|Permitir o acesso à Chave BitLocker (Windows VM) ou dM Passphrase (Linux VM) e chave de encriptação de chaves, durante a implementação da máquina virtual. Isto permite a encriptação do [disco Azure](../../security/fundamentals/encryption-overview.md).|
|Azure Backup|Permitir a cópia de segurança e restaurar as chaves e segredos relevantes durante a cópia de segurança das Máquinas Virtuais Azure, utilizando [o Backup Azure](../../backup/backup-introduction-to-azure-backup.md).|
|Troca online & SharePoint Online|Permitir o acesso à chave do cliente para encriptação do serviço de armazenamento Azure com [chave do cliente](/microsoft-365/compliance/customer-key-overview).|
|Azure Information Protection|Permitir o acesso à chave de inquilinos para [a Proteção de Informação Azure.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|Serviço de Aplicações do Azure|[Implemente o certificado de aplicação web Azure através do cofre da chave.](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html)|
|Base de Dados SQL do Azure|[Encriptação transparente de dados com trazer o seu próprio suporte chave para base de dados Azure SQL e Armazém de Dados](../../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current).|
|Storage do Azure|[Encriptação do serviço de armazenamento utilizando chaves geridas pelo cliente no Cofre de Chaves Azure](../../storage/common/storage-service-encryption-customer-managed-keys.md).|
|Azure Data Lake Store|[Encriptação de dados na Azure Data Lake Store](../../data-lake-store/data-lake-store-encryption.md) com uma chave gerida pelo cliente.|
|Azure Databricks|[Serviço de análise rápido, fácil e colaborativo apache spark](../../azure-databricks/what-is-azure-databricks.md)|
|API Management do Azure|[Implementar certificados para domínio personalizado a partir do cofre de chaves usando MSI](../../api-management/api-management-howto-use-managed-service-identity.md#use-the-managed-service-identity-to-access-other-resources)|
|Azure Data Factory|[Buscar credenciais de loja de dados no Cofre chave da Fábrica de Dados](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[Permitir o acesso a um cofre chave para o cenário de chaves gerida pelo cliente](https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key)|
|Service Bus do Azure|[Permitir o acesso a um cofre chave para o cenário de chaves gerida pelo cliente](https://docs.microsoft.com/azure/service-bus-messaging/configure-customer-managed-key)|
|Importar/Exportar do Microsoft Azure| [Utilize chaves geridas pelo cliente no Cofre chave Azure para serviço de importação/exportação](https://docs.microsoft.com/azure/storage/common/storage-import-export-encryption-key-portal)
|Registo de Contentores do Azure|[Encriptação de registo utilizando chaves geridas pelo cliente](../../container-registry/container-registry-customer-managed-keys.md)

> [!NOTE]
> Deve configurar as políticas de acesso ao Cofre chave relevantes para permitir que os serviços correspondentes tenham acesso ao Key Vault.

## <a name="next-steps"></a>Passos seguintes

* [Proteja o seu cofre de chaves)](secure-your-key-vault.md)
* [Configure firewalls e redes virtuais do Cofre de Chaves](network-security.md)
