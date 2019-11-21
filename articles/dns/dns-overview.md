---
title: O que é o Azure DNS?
description: Descrição geral do serviço de alojamento de DNS no Microsoft Azure. Aloje o seu domínio no Microsoft Azure.
author: asudbring
ms.service: dns
ms.topic: overview
ms.date: 3/21/2019
ms.author: allensu
ms.openlocfilehash: 6abdc48187950d9605bdc47b1c5d4f00e4e181f0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74211329"
---
# <a name="what-is-azure-dns"></a>O que é o Azure DNS?

O DNS do Azure é um serviço de alojamento dos domínios DNS que oferece resolução de nomes através da infraestrutura do Microsoft Azure. Ao alojar os seus domínios no Azure, pode gerir os recursos DNS com as mesmas credenciais, APIs, ferramentas e faturação dos seus outros serviços do Azure.

Não pode utilizar o DNS do Azure para comprar um nome de domínio. For an annual fee, you can buy a domain name by using [App Service domains](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain#buy-the-domain) or a third-party domain name registrar. Os domínios podem, então, ser alojados no DNS do Azure para a gestão de registos. Para obter mais informações, veja [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md).

As seguintes funcionalidades estão incluídas com o DNS do Azure.

## <a name="reliability-and-performance"></a>Desempenho e fiabilidade

Os domínios DNS no DNS do Azure estão alojados na rede global do Azure de servidores de nomes DNS. O DNS do Azure utiliza a rede de transmissão. Cada consulta DNS é respondida pelo servidor DNS disponível mais próximo, para um desempenho rápido e de elevada disponibilidade para o seu domínio.

## <a name="security"></a>Segurança

 O DNS do Azure baseia-se no Azure Resource Manager, que disponibiliza funcionalidades como:

* [Controlo de acesso baseado em funções](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para controlar quem tem acesso a ações específicas para a sua organização.

* [Registos de atividades](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para monitorizar a forma como um utilizador na sua organização alterou um recurso ou encontrar um erro quando resolver um problema.

* [Bloqueio de recurso](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) para bloquear uma subscrição, grupo de recursos ou recurso. O bloqueio impede que outros utilizadores na sua organização eliminem ou modifiquem acidentalmente recursos críticos.

Para obter mais informações, veja [Como proteger zonas DNS e registos](dns-protect-zones-recordsets.md). 

## <a name="dnssec"></a>DNSSEC

Azure DNS does not currently support DNSSEC. In most cases, you can reduce the need for DNSSEC by consistently using HTTPS/TLS in your applications. If DNSSEC is a critical requirement for your DNS zones, you can host these zones with third party DNS hosting providers.

## <a name="ease-of-use"></a>Facilidade de utilização

 O DNS do Azure pode gerir os registos DNS dos seus serviços do Azure e também conceder o DNS para os seus recursos externos. O DNS do Azure está integrado no portal do Azure e utiliza as mesmas credenciais, contrato de suporte e faturação como os outros serviços do Azure. 

A faturação do DNS é baseada no número de zonas DNS alojadas no Azure e no número de consultas DNS recebidas. Para obter mais informações sobre preços, veja [Preços do DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).

Os domínios e registos podem ser geridos com o portal do Azure, cmdlets do Azure PowerShell e a CLI do Azure de várias plataformas. As aplicações que exigem gestão automatizada de DNS podem ser integradas com o serviço através da API REST e SDKs.

## <a name="customizable-virtual-networks-with-private-domains"></a>Redes virtuais personalizáveis com domínios privados

Azure DNS also supports private DNS domains. Esta funcionalidade permite-lhe utilizar os seus próprios nomes de domínio personalizados nas suas redes virtuais privadas, em vez dos nomes apresentados pelo Azure atualmente disponíveis.

Para obter mais informações, veja [Utilizar o DNS do Azure para domínios privados](private-dns-overview.md).

## <a name="alias-records"></a>Registos de alias

O DNS do Azure suporta conjuntos de registos de alias. You can use an alias record set to refer to an Azure resource, such as an Azure public IP address, an Azure Traffic Manager profile, or an Azure Content Delivery Network (CDN) endpoint. Se o endereço IP do recurso subjacente for alterado, o conjunto de registos de alias atualiza-se automaticamente durante a resolução de DNS. O conjunto de registo de alias aponta para a instância do serviço e esta está associada a um endereço IP.

Also, you can now point your apex or naked domain to a Traffic Manager profile or CDN endpoint using an alias record. Um exemplo é contoso.com.

Para obter mais informações, consulte [Descrição geral do registo de alias do DNS do Azure](dns-alias.md).

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre zonas DNS e registos, veja [Descrição geral das zonas DNS e registos](dns-zones-records.md).

* Para saber como criar uma zona no DNS do Azure, veja [Criar uma zona DNS](./dns-getstarted-create-dnszone-portal.md).

* Para ver as perguntas mais frequentes sobre o DNS do Azure, veja [FAQ sobre DNS do Azure](dns-faq.md).

