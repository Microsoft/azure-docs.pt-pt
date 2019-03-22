---
title: O que é o DNS do Azure?
description: Descrição geral do serviço de alojamento de DNS no Microsoft Azure. Aloje o seu domínio no Microsoft Azure.
author: vhorne
ms.service: dns
ms.topic: overview
ms.date: 3/21/2019
ms.author: victorh
ms.openlocfilehash: cea19ad2ba03d3e71df32912a1d7ee9e6171689a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339467"
---
# <a name="what-is-azure-dns"></a>O que é o DNS do Azure?

O DNS do Azure é um serviço de alojamento dos domínios DNS que oferece resolução de nomes através da infraestrutura do Microsoft Azure. Ao alojar os seus domínios no Azure, pode gerir os recursos DNS com as mesmas credenciais, APIs, ferramentas e faturação dos seus outros serviços do Azure.

Não pode utilizar o DNS do Azure para comprar um nome de domínio. Por uma taxa anual, pode comprar um nome de domínio, utilizando [domínios do serviço de aplicações](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain#buy-the-domain) ou uma entidade de registo de nome de domínio de terceiros. Os domínios podem, então, ser alojados no DNS do Azure para a gestão de registos. Para obter mais informações, veja [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md).

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

O DNS do Azure não suporta atualmente DNSSEC. Na maioria dos casos, pode reduzir a necessidade de DNSSEC utilizando consistentemente HTTPS/TLS no seu aplicativos. Se o DNSSEC é um requisito essencial para as zonas DNS, pode alojar essas zonas com fornecedores de alojamento de DNS de terceiros.

## <a name="ease-of-use"></a>Facilidade de utilização

 O DNS do Azure pode gerir os registos DNS dos seus serviços do Azure e também conceder o DNS para os seus recursos externos. O DNS do Azure está integrado no portal do Azure e utiliza as mesmas credenciais, contrato de suporte e faturação como os outros serviços do Azure. 

A faturação do DNS é baseada no número de zonas DNS alojadas no Azure e no número de consultas DNS recebidas. Para obter mais informações sobre preços, veja [Preços do DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).

Os domínios e registos podem ser geridos com o portal do Azure, cmdlets do Azure PowerShell e a CLI do Azure de várias plataformas. As aplicações que exigem gestão automatizada de DNS podem ser integradas com o serviço através da API REST e SDKs.

## <a name="customizable-virtual-networks-with-private-domains"></a>Redes virtuais personalizáveis com domínios privados

O DNS do Azure também suporta domínios DNS privados, com uma funcionalidade que se encontra atualmente em pré-visualização pública. Esta funcionalidade permite-lhe utilizar os seus próprios nomes de domínio personalizados nas suas redes virtuais privadas, em vez dos nomes apresentados pelo Azure atualmente disponíveis.

Para obter mais informações, veja [Utilizar o DNS do Azure para domínios privados](private-dns-overview.md).

## <a name="alias-records"></a>Registos de alias

O DNS do Azure suporta conjuntos de registos de alias. Pode utilizar um conjunto de registos de alias para fazer referência a um recurso do Azure, como um endereço IP público do Azure, um perfil do Gestor de tráfego do Azure ou um ponto de extremidade de rede de entrega conteúdo (CDN). Se o endereço IP do recurso subjacente for alterado, o conjunto de registos de alias atualiza-se automaticamente durante a resolução de DNS. O conjunto de registo de alias aponta para a instância do serviço e esta está associada a um endereço IP.

Além disso, agora pode apontar seu vértice ou domínio sem "www" a um perfil do Traffic Manager ou o ponto final da CDN através de um registo de alias. Um exemplo é contoso.com.

Para obter mais informações, consulte [Descrição geral do registo de alias do DNS do Azure](dns-alias.md).

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre zonas DNS e registos, veja [Descrição geral das zonas DNS e registos](dns-zones-records.md).

* Para saber como criar uma zona no DNS do Azure, veja [Criar uma zona DNS](./dns-getstarted-create-dnszone-portal.md).

* Para ver as perguntas mais frequentes sobre o DNS do Azure, veja [FAQ sobre DNS do Azure](dns-faq.md).

