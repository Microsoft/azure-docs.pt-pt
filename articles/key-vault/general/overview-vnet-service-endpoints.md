---
title: Pontos finais de serviço de rede virtual para Azure Key Vault - Azure Key Vault / Microsoft Docs
description: Saiba como os pontos finais do serviço de rede virtual para O Azure Key Vault permitem restringir o acesso a uma rede virtual especificada, incluindo cenários de utilização.
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: a1159da782a8cb822bf726173ad0d0bcd9da844a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460888"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Pontos finais de serviço de rede virtual para Azure Key Vault

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

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Configurar firewalls e redes virtuais do Key Vault

Aqui estão os passos necessários para configurar firewalls e redes virtuais. Estes passos aplicam-se quer esteja a utilizar o PowerShell, o Azure CLI ou o portal Azure.

1. Ativar o [registo do Cofre de Chaves](logging.md) para ver registos de acesso detalhados. Isto ajuda nos diagnósticos, quando firewalls e regras de rede virtuais impedem o acesso a um cofre chave. (Este passo é opcional, mas altamente recomendado.)
2. Ativar **os pontos finais de serviço para o cofre-chave** para redes virtuais e sub-redes alvo.
3. Estabeleça firewalls e regras de rede virtuais para um cofre chave para restringir o acesso ao cofre de chaves a partir de redes virtuais específicas, sub-redes e intervalos de endereços IPv4.
4. Se este cofre de chaves precisar de ser acessível por quaisquer serviços da Microsoft confiáveis, ative a opção de permitir que os **Serviços Azure Fidedignos** se conectem ao Key Vault.

Para obter mais informações, consulte [as firewalls Configure Key Vault e redes virtuais](network-security.md).

> [!IMPORTANT]
> Após as regras de firewall estarem em vigor, os utilizadores só podem realizar operações [de plano de dados](secure-your-key-vault.md#data-plane-access-control) key Vault quando os seus pedidos são originários de redes virtuais permitidas ou intervalos de endereços IPv4. Isto também se aplica ao acesso ao Cofre de Chaves a partir do portal Azure. Embora os utilizadores possam navegar para um cofre chave a partir do portal Azure, podem não ser capazes de listar chaves, segredos ou certificados se a sua máquina cliente não estiver na lista permitida. Isto também afeta o Key Vault Picker por outros serviços Azure. Os utilizadores podem ser capazes de ver a lista de cofres chave, mas não listar chaves, se as regras de firewall impedirem a sua máquina de clientes.


> [!NOTE]
> Esteja atento às seguintes limitações de configuração:
> * São permitidas regras de rede virtual no máximo 127 e 127 regras IPv4. 
> * Não são suportadas pequenas gamas de endereços que utilizem os tamanhos do prefixo "/31" ou "/32". Em vez disso, configura estas gamas utilizando regras de endereço IP individuais.
> * As regras da rede IP só são permitidas para endereços IP públicos. Os intervalos de endereços IP reservados para redes privadas (tal como definido no RFC 1918) não são permitidos nas regras de IP. As redes privadas incluem endereços **172.16-31** que começam com **10.** **192.168.** 
> * Apenas os endereços IPv4 são suportados neste momento.

## <a name="trusted-services"></a>Serviços de confiança

Aqui está uma lista de serviços fidedignos que podem aceder a um cofre chave se a opção **de serviços fidedignos** estiver ativada.

|Serviço de confiança|Cenários de utilização apoiados|
| --- | --- |
|Serviço de implementação de máquinas virtuais Azure|[Implementar certificados para VMs a partir do Key Vault gerido pelo cliente](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault).|
|Serviço de implementação do modelo do Gestor de Recursos Azure|[Passe valores seguros durante a implantação](../../azure-resource-manager/templates/key-vault-parameter.md).|
|Gateway de aplicação Azure v2 SKU|[Rescisão de TLS com certificados Key Vault](../../application-gateway/key-vault-certs.md)|
|Serviço de encriptação de volume de encriptação de disco Azure|Permitir o acesso à Chave BitLocker (Windows VM) ou DM Passphrase (Linux VM) e à Chave de Encriptação, durante a implementação da máquina virtual. Isto permite [a encriptação do disco Azure](../../security/fundamentals/encryption-overview.md).|
|Azure Backup|Permitir a cópia de segurança e restaurar as chaves e segredos relevantes durante a cópia de segurança das Máquinas Virtuais Azure, utilizando [o Azure Backup](../../backup/backup-overview.md).|
|Troca Online & SharePoint Online|Permitir o acesso à chave do cliente para encriptação do serviço de armazenamento Azure com [a chave do cliente.](/microsoft-365/compliance/customer-key-overview)|
|Azure Information Protection|Permitir o acesso à chave do inquilino para [a Azure Information Protection.](/azure/information-protection/what-is-information-protection)|
|Serviço de Aplicações do Azure|[Implementar o Certificado de Aplicação Web Azure através do Cofre de Chaves](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Base de Dados SQL do Azure|[Encriptação de dados transparente com suporte de chave própria para Azure SQL Database e Azure Synapse Analytics](../../azure-sql/database/transparent-data-encryption-byok-overview.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current).|
|Storage do Azure|[Encriptação do serviço de armazenamento utilizando chaves geridas pelo cliente no Cofre da Chave Azure](../../storage/common/customer-managed-keys-configure-key-vault.md).|
|Azure Data Lake Store|[Encriptação de dados na Azure Data Lake Store](../../data-lake-store/data-lake-store-encryption.md) com uma chave gerida pelo cliente.|
|Azure Databricks|[Serviço de análise rápido, fácil e colaborativo apache spark baseado em Apache Spark](/azure/databricks/scenarios/what-is-azure-databricks)|
|Gestão de API do Azure|[Implementar certificados para domínio personalizado a partir do Cofre de Chaves usando MSI](../../api-management/api-management-howto-use-managed-service-identity.md#use-ssl-tls-certificate-from-azure-key-vault)|
|Azure Data Factory|[Buscar credenciais de loja de dados no Cofre de Dados da Data Factory](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[Permitir o acesso a um cofre chave para cenário de chaves geridas pelo cliente](../../event-hubs/configure-customer-managed-key.md)|
|Azure Service Bus|[Permitir o acesso a um cofre chave para cenário de chaves geridas pelo cliente](../../service-bus-messaging/configure-customer-managed-key.md)|
|Importar/Exportar do Microsoft Azure| [Utilize chaves geridas pelo cliente no Cofre chave Azure para o serviço de importação/exportação](../../storage/common/storage-import-export-encryption-key-portal.md)
|Registo de Contentores do Azure|[Encriptação do registo utilizando chaves geridas pelo cliente](../../container-registry/container-registry-customer-managed-keys.md)<br><br/>[Transferir artefactos para outro registo](../../container-registry/container-registry-transfer-images.md)

> [!NOTE]
> Tem de configurar as políticas de acesso ao Cofre chave relevantes para permitir que os serviços correspondentes tenham acesso ao Key Vault.

## <a name="next-steps"></a>Passos seguintes

* [Proteja o cofre da chave](secure-your-key-vault.md)
* [Configurar firewalls de cofre de chaves Azure e redes virtuais](network-security.md)