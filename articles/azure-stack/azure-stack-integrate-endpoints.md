---
title: Azure Stack integração no datacenter - publicar pontos de extremidade | Documentos da Microsoft
description: Saiba como publicar pontos finais de pilha do Azure no seu datacenter
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/06/2019
ms.author: jeffgilb
ms.reviewer: wamota
ms.lastreviewed: 02/06/2019
ms.openlocfilehash: 0bfd280e2a0a63c8fb41d0813a0ac44784e7a055
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259182"
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure Stack integração no datacenter - publicar pontos de extremidade

O Azure Stack configura endereços IP virtuais (VIPs) para as respetivas funções de infraestrutura. Estes VIPs são alocadas a partir do conjunto de endereços IP público. Cada VIP é protegido por uma lista de controlo de acesso (ACL) na camada de rede definida pelo software. ACLs também são utilizadas entre os comutadores físicos (tores e BMC) para proteger ainda mais a solução. Uma entrada DNS é criada para cada ponto de extremidade na zona DNS externo que especificado no momento da implementação.


O diagrama de arquitetura seguinte mostra as camadas de rede diferentes e ACLs:

![Imagem estrutural](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Portas e protocolos (entrada)

Um conjunto de infraestrutura de VIPs é necessário para publicação do Azure Stack os pontos finais a redes externas. O *ponto final (VIP)* tabela mostra cada ponto de extremidade, a porta necessária e o protocolo. Consulte a documentação de implementação do Provedor de recurso específico para pontos finais que necessitam de fornecedores de recursos adicionais, tais como o fornecedor de recursos do SQL.

Infra-estrutura interna que vips não estão listados como não são necessários para a publicação do Azure Stack.

> [!Note]  
> Utilizador VIPs são dinâmicos, definidas pelos próprios usuários com nenhum controle pela operadora de rede do Azure Stack.

> [!Note]
> A partir da atualização 1811, portas no intervalo de 12495 30015 já não têm de estar abertas devido a adição do [extensão anfitrião](azure-stack-extension-host-prepare.md).

|Ponto final (VIP)|DNS host A record|Protocolo|Portas|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portal (administrador)|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Adminhosting | *.adminhosting.\<region>.\<fqdn> | HTTPS | 443 |
|O Azure Resource Manager (administrador)|Adminmanagement.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portal (utilizador)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure Resource Manager (user)|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Graph|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Lista de revogação de certificados|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP E UDP|53|
|Alojamento | *.hosting.\<region>.\<fqdn> | HTTPS | 443 |
|Cofre de chaves (utilizador)|&#42;.vault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Cofre de chaves (administrador)|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Fila de Armazenamento|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Tabela de armazenamento|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Blob de Armazenamento|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Fornecedor de Recursos SQL|sqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|Fornecedor de Recursos do MySQL|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|Serviço de Aplicações|&#42;.appservice.*&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (Azure Resource Manager)|
|  |ftp.appservice.*&lt;region>.&lt;fqdn>*|TCP, UDP|21, 1021, 10001-10100 (FTP)<br>990 (FTPS)|
|Gateways de VPN|     |     |[Consulte a FAQ do VPN gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability).|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>Portas e URLs (saídas)

O Azure Stack suporta apenas os servidores de proxy transparente. Numa implementação em que um uplinks de proxy transparente para um servidor proxy tradicional, tem de permitir os seguintes portas e URLs para comunicações de saída:

> [!Note]  
> O Azure Stack não suporta a utilizar o ExpressRoute para alcançar os serviços do Azure listados na tabela seguinte.

|Objetivo|Destination URL|Protocolo|Portas|Rede de Origem|
|---------|---------|---------|---------|---------|
|Identidade|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https:\//secure.aadcdn.microsoftonline-p.com<br>office.com|HTTP<br>HTTPS|80<br>443|VIP - /27 públicos<br>Infraestrutura de rede pública|
|Distribuição de mercado|https:\//management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|VIP - /27 públicos|
|& Atualizações de patch|https://&#42;.azureedge.net|HTTPS|443|VIP - /27 públicos|
|Registo|https:\//management.azure.com|HTTPS|443|VIP - /27 públicos|
|Utilização|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.net |HTTPS|443|VIP - /27 públicos|
|Windows Defender|.wdcp.microsoft.com<br>.wdcpalt.microsoft.com<br>*.updates.microsoft.com<br>*.download.microsoft.com<br>https:\//msdl.microsoft.com/download/symbols<br>https:\//www.microsoft.com/pkiops/crl<br>https:\//www.microsoft.com/pkiops/certs<br>https:\//crl.microsoft.com/pki/crl/products<br>https:\//www.microsoft.com/pki/certs<br>https:\//secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|VIP - /27 públicos<br>Infraestrutura de rede pública|
|NTP|(Servidor de IP do NTP fornecido para a implementação)|UDP|123|VIP - /27 públicos|
|DNS|(Servidor de IP de DNS fornecido para a implementação)|TCP<br>UDP|53|VIP - /27 públicos|
|CRL|(URL em pontos de distribuição de CRL no seu certificado)|HTTP|80|VIP - /27 públicos|
|LDAP|Floresta do Active Directory fornecido para a integração de gráfico|TCP<br>UDP|389|VIP - /27 públicos|
|LDAP SSL|Floresta do Active Directory fornecido para a integração de gráfico|TCP|636|VIP - /27 públicos|
|LDAP GC|Floresta do Active Directory fornecido para a integração de gráfico|TCP|3268|VIP - /27 públicos|
|SSL LDAP DE GC|Floresta do Active Directory fornecido para a integração de gráfico|TCP|3269|VIP - /27 públicos|
|AD FS|AD FS ponto final de metadados fornecido para a integração do AD FS|TCP|443|VIP - /27 públicos|
|     |     |     |     |     |

> [!Note]  
> URLs de saída são balanceado em carga utilizando o Gestor de tráfego do Azure para fornecer a melhor conectividade de possíveis com base na localização geográfica. Com os URLs com balanceamento de carga, a Microsoft pode atualizar e alterar os pontos finais de back-end sem afetar os clientes. A Microsoft não partilhar a lista de endereços IP para a carga balanceada URLs. Deve usar um dispositivo que suporte a filtragem por URL em vez de IP.

## <a name="next-steps"></a>Passos Seguintes

[Requisitos de PKI da pilha do Azure](azure-stack-pki-certs.md)
