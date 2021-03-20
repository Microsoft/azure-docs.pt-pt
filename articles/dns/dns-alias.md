---
title: Alias grava visão geral - Azure DNS
description: Neste artigo, saiba mais sobre suporte a registos de pseudónimos no Microsoft Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 08/09/2019
ms.author: rohink
ms.openlocfilehash: 8b2576669357aae7e5fe423515933c2ce4a23a7d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954482"
---
# <a name="azure-dns-alias-records-overview"></a>Descrição geral dos registos de alias do DNS do Azure

Os registos de pseudónimos do Azure DNS são qualificações num conjunto de discos DNS. Podem fazer referência a outros recursos Azure dentro da sua zona de DNS. Por exemplo, pode criar um conjunto de registos de pseudónimos que faz referência a um endereço IP público Azure em vez de um registo A. O seu nome fixou pontos para uma instância de serviço de endereço IP público Azure dinamicamente. Como resultado, o registo de pseudónimos estabeleceu-se perfeitamente durante a resolução do DNS.

Um conjunto de registos de pseudónimos é suportado para os seguintes tipos de registo numa zona de DNS Azure: 

- A
- AAAA
- CNAME

> [!NOTE]
> Se pretender utilizar um registo de pseudónimos para os tipos de registo A ou AAAA para apontar para um [perfil do Gestor de Tráfego Azure,](../traffic-manager/quickstart-create-traffic-manager-profile.md) deve certificar-se de que o perfil do Gestor de Tráfego tem [apenas pontos finais externos.](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) Tem de fornecer o endereço IPv4 ou IPv6 para pontos finais externos no Traffic Manager. Não é possível utilizar nomes de domínio totalmente qualificados (FQDNs) em pontos finais. Idealmente, use endereços IP estáticos.

## <a name="capabilities"></a>Capacidades

- **Aponte para um recurso IP público a partir de um conjunto de registos DNS A/AAAA.** Pode criar um conjunto de registoS A/AAAA e torná-lo um registo de pseudónimo definido para apontar para um recurso IP público (padrão ou básico). O registo DNS altera automaticamente se o endereço IP público alterar ou for eliminado. Evitam-se registos DNS pendentes que apontam para endereços IP incorretos.

   Existe um limite atual de 20 conjuntos de registos de pseudónimos por recurso.

- **Aponte para um perfil de Gestor de Tráfego a partir de um conjunto de registos DNS A/AAAA/CNAME.** Pode criar um conjunto de registos A/AAAA ou CNAME e utilizar registos de pseudónimos para o apontar para um perfil de Gestor de Tráfego. É especialmente útil quando você precisa de encaminhar o tráfego em um ápice de zona, uma vez que os registos tradicionais da CNAME não são suportados para um ápice de zona. Por exemplo, digamos que o seu perfil de Gestor de Tráfego é myprofile.trafficmanager.net e que a zona de DNS de negócio é contoso.com. Pode criar um conjunto de registos de pseudónimos do tipo A/AAAA para contoso.com (o ápice da zona) e apontar para myprofile.trafficmanager.net.
- **Aponte para um ponto final da Rede de Entrega de Conteúdos Azure (CDN).** Isto é útil quando cria websites estáticos usando o armazenamento Azure e a Azure CDN.
- **Aponte para outro recorde de DNS estabelecido dentro da mesma zona.** Os registos de alias podem fazer referência a outros conjuntos de registos do mesmo tipo. Por exemplo, um conjunto de discos DNS CNAME pode ser um pseudónimo de outro conjunto de recordes cname. Este arranjo é útil se quiser que alguns recordes sejam pseudónimos e alguns não-pseudónimos.

## <a name="scenarios"></a>Cenários

Há alguns cenários comuns para os registos da Alias.

### <a name="prevent-dangling-dns-records"></a>Prevenir registos dns pendentes

Um problema comum com os registos tradicionais de DNS é pendurar registos. Por exemplo, registos DNS que não foram atualizados para refletir alterações nos endereços IP. O problema ocorre especialmente com os tipos de registo A/AAAA ou CNAME.

Com um registo tradicional de zona de DNS, se o IP ou CNAME alvo já não existir, o registo DNS associado a ele deve ser atualizado manualmente. Em algumas organizações, uma atualização manual pode não acontecer a tempo devido a problemas de processo ou à separação de papéis e níveis de permissão associados. Por exemplo, uma função pode ter a autoridade para eliminar um endereço CNAME ou IP que pertença a uma aplicação. Mas não tem autoridade suficiente para atualizar o registo do DNS que aponta para esses alvos. Um atraso na atualização do registo DNS pode potencialmente causar uma paragem para os utilizadores.

Os registos de pseudónimos impedem referências pendentes, acoplando firmemente o ciclo de vida de um registo de DNS com um recurso Azure. Por exemplo, considere um registo DNS que seja qualificado como um registo de pseudónimo para apontar para um endereço IP público ou um perfil de Gestor de Tráfego. Se eliminar os recursos subjacentes, o registo de pseudónimos DNS torna-se um recorde vazio. Já não faz referência ao recurso eliminado.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>Atualizar o recorde de DNS automaticamente quando os endereços IP da aplicação mudam

Este cenário é semelhante ao anterior. Talvez uma aplicação seja movida, ou a máquina virtual subjacente seja reiniciada. Um registo de pseudónimo atualiza-se automaticamente quando o endereço IP muda para o recurso IP público subjacente. Isto evita potenciais riscos de segurança de direcionar os utilizadores para outra aplicação que tenha sido atribuída ao antigo endereço IP público.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Aplicações equilibradas de carga de hospedeiro no ápice da zona

O protocolo DNS impede a atribuição de registos CNAME no ápice da zona. Por exemplo, se o seu domínio for contoso.com; pode criar registos CNAME para somelabel.contoso.com; mas não se pode criar o CNAME para contoso.com si.
Esta restrição apresenta um problema para os proprietários de aplicações que têm aplicações equilibradas por trás [do Gestor de Tráfego Azure.](../traffic-manager/traffic-manager-overview.md) Uma vez que a utilização de um perfil de Gestor de Tráfego requer a criação de um registo CNAME, não é possível apontar para o perfil do Gestor de Tráfego a partir do ápice da zona.

Este problema é resolvido usando registos de pseudónimos. Ao contrário dos registos da CNAME, os registos de pseudónimos são criados no ápice da zona e os proprietários de aplicações podem usá-lo para apontar o seu registo de zona para um perfil de Gestor de Tráfego que tenha pontos finais externos. Os proprietários de aplicações apontam para o mesmo perfil de Gestor de Tráfego que é usado para qualquer outro domínio dentro da sua zona de DNS.

Por exemplo, contoso.com e www \. contoso.com podem apontar para o mesmo perfil de Gestor de Tráfego. Para saber mais sobre a utilização de registos de pseudónimos com perfis do Azure Traffic Manager, consulte a secção Etapas Seguintes.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Apex da zona de ponto para pontos finais do Azure CDN

Tal como um perfil de Traffic Manager, também pode usar registos de pseudónimos para apontar o ápice da zona DNS para os pontos finais do Azure CDN. Isto é útil quando cria websites estáticos usando o armazenamento Azure e a Azure CDN. Em seguida, pode aceder ao website sem pré-gastar "www" no seu nome DNS.

Por exemplo, se o seu website estático estiver `www.contoso.com` nomeado, os seus utilizadores podem aceder ao seu site utilizando `contoso.com` sem a necessidade de preparar www para o nome DNS.

Como descrito anteriormente, os registos da CNAME não são suportados no ápice da zona. Então, não pode usar um registo CNAME para apontar contoso.com para o seu ponto final CDN. Em vez disso, pode usar um registo de pseudónimo para apontar o ápice da zona diretamente para um ponto final da CDN.

> [!NOTE]
> Atualmente, não é suportado que aponte um ápice de zona para os pontos finais da CDN para o Azure CDN da Akamai.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os registos de pseudónimos, consulte os seguintes artigos:

- [Tutorial: Configurar um registo de pseudónimo para se referir a um endereço IP público azul](tutorial-alias-pip.md)
- [Tutorial: Configurar um registo de alias para suportar os nomes de domínio apex com o Gestor de Tráfego](tutorial-alias-tm.md)
- [FAQ sobre DNS](./dns-faq.md#alias-records)