---
title: Vista geral dos registos da Alias - Azure DNS
description: Neste artigo, conheça o suporte para registos de pseudónimos no DNS do Microsoft Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 08/09/2019
ms.author: rohink
ms.openlocfilehash: 085e5fc20a6b5356e012eb2f674fafc00cef828f
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937332"
---
# <a name="azure-dns-alias-records-overview"></a>Descrição geral dos registos de alias do DNS do Azure

Os registos de pseudónimos do Azure DNS são qualificações num recorde de DNS. Podem fazer referência a outros recursos Azure a partir da sua zona DNS. Por exemplo, pode criar um conjunto de registos de pseudónimos que referencia um endereço IP público Azure em vez de um disco A. O seu recorde de identificação aponta para uma instância de serviço ip pública Azure de forma dinâmica. Como resultado, o registo de pseudónimos atualiza-se perfeitamente durante a resolução do DNS.

Um conjunto de recordes de pseudónimos é suportado para os seguintes tipos de registo numa zona DeNs Azure: 

- A
- AAAA
- CNAME

> [!NOTE]
> Se pretender utilizar um registo de pseudónimo si próprio para os tipos de registo A ou AAAA para apontar para um perfil do Gestor de [Tráfego Azure,](../traffic-manager/quickstart-create-traffic-manager-profile.md) deve certificar-se de que o perfil do Gestor de Tráfego tem apenas [pontos finais externos](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints). Deve fornecer o endereço IPv4 ou IPv6 para pontos finais externos no Traffic Manager. Não pode utilizar nomes de domínio totalmente qualificados (FQDNs) em pontos finais. Idealmente, utilize endereços IP estáticos.

## <a name="capabilities"></a>Funções

- **Aponte para um recurso IP público de um conjunto de registros DNS A/AAAA.** Pode criar um conjunto de registos A/AAAA e torná-lo um recorde de pseudónimo definido para apontar para um recurso IP público (padrão ou básico). O conjunto de registos DNS muda automaticamente se o endereço IP público mudar ou for eliminado. São evitados registos DNS pendentes que apontam para endereços IP incorretos.

   Existe um limite atual de 20 conjuntos de registos de pseudónimos por recurso.

- **Aponte para um perfil do Gerenciador de tráfego de um conjunto de registros DNS A/AAAA/CNAME.** Pode criar um conjunto de registos A/AAAA ou CNAME e utilizar registos de pseudónimos para o indicar para um perfil do Gestor de Tráfego. É especialmente útil quando você precisa de encaminhar o tráfego em um ápice de zona, uma vez que os registos cname tradicionais não são suportados para um ápice de zona. Por exemplo, digamos que o seu perfil de Gestor de Tráfego é myprofile.trafficmanager.net e a sua zona de DNS está contoso.com. Pode criar um conjunto de registos de tipo A/AAAA para contoso.com (o ápice da zona) e apontar para myprofile.trafficmanager.net.
- **Aponte para um ponto de extremidade da CDN (rede de distribuição de conteúdo) do Azure**. Isso é útil quando você cria sites estáticos usando o armazenamento do Azure e a CDN do Azure.
- **Aponte para outro conjunto de registros DNS dentro da mesma zona.** Os registos de alias podem fazer referência a outros conjuntos de registos do mesmo tipo. Por exemplo, um conjunto de registos DNS CNAME pode ser um pseudónimo para outro conjunto de registos CNAME. Essa organização será útil se você quiser que alguns conjuntos de registros sejam aliases e alguns não aliases.

## <a name="scenarios"></a>Cenários

Há alguns cenários comuns para os registos da Alias.

### <a name="prevent-dangling-dns-records"></a>Prevenir registos dNS pendentes

Um problema comum com os registos tradicionais do DNS é pendurar registos. Por exemplo, registos dNS que não foram atualizados para refletir alterações nos endereços IP. O problema ocorre especialmente com tipos de registo A/AAAA ou CNAME.

Com um registo tradicional de zona DNS, se o IP ou CNAME alvo já não existir, o registo DNS associado ao mesmo deve ser atualizado manualmente. Em algumas organizações, uma atualização manual pode não acontecer a tempo devido a problemas de processo ou à separação de funções e níveis de permissão associados. Por exemplo, uma função pode ter a autoridade para eliminar um endereço CNAME ou IP que pertença a uma aplicação. Mas não tem autoridade suficiente para atualizar o registo do DNS que aponta para esses alvos. Um atraso na atualização do registo DNS pode potencialmente causar uma falha para os utilizadores.

Os registos de alias impedem referências penduradas, acoplamento firmemente ao ciclo de vida de um registo DNS com um recurso Azure. Por exemplo, considere um registo de DNS qualificado como um registo de pseudónimos para apontar para um endereço IP público ou um perfil de Gestor de Tráfego. Se eliminar os recursos subjacentes, o registo de pseudónimos do DNS torna-se um recorde vazio. Já não faz referência ao recurso eliminado.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>Atualizar o conjunto de registos DNS automaticamente quando os endereços IP da aplicação mudam

Este cenário é semelhante ao anterior. Talvez uma aplicação seja movida, ou a máquina virtual subjacente seja reiniciada. Um registo de pseudónimoactualiza-se automaticamente quando o endereço IP muda para o recurso IP público subjacente. Isto evita potenciais riscos de segurança de direcionar os utilizadores para outra aplicação que tenha sido atribuída ao antigo endereço IP público.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Aplicações equilibradas de carga no ápice da zona

O protocolo DNS impede a atribuição de registos CNAME no ápice da zona. Por exemplo, se o seu domínio estiver contoso.com; pode criar registos CNAME para somelabel.contoso.com; mas não se pode criar CNAME para contoso.com si mesmo.
Esta restrição apresenta um problema para os proprietários de aplicações que têm aplicações equilibradas por trás do [Azure Traffic Manager.](../traffic-manager/traffic-manager-overview.md) Uma vez que a utilização de um perfil do Gestor de Tráfego requer a criação de um registo CNAME, não é possível apontar para o perfil do Gestor de Tráfego a partir do ápice da zona.

Este problema é resolvido usando registos de pseudónimos. Ao contrário dos registos CNAME, os registos de pseudónimos são criados na zona ápice e os proprietários de aplicações podem usá-lo para apontar o seu registo ápice de zona para um perfil do Gestor de Tráfego que tem pontos finais externos. Os proprietários de aplicações apontam para o mesmo perfil do Traffic Manager que é usado para qualquer outro domínio dentro da sua zona DNS.

Por exemplo, contoso.com e www\.contoso.com podem apontar para o mesmo perfil do Gestor de Tráfego. Para saber mais sobre a utilização de registos de pseudónimos com perfis do Gestor de Tráfego Azure, consulte a secção Depassos Seguintes.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Ponto ápice para pontos finais azure CDN

Tal como um perfil do Traffic Manager, também pode utilizar registos de pseudónimos para indicar o seu ápice da zona DNS para os pontos finais do Azure CDN. Isso é útil quando você cria sites estáticos usando o armazenamento do Azure e a CDN do Azure. Em seguida, pode aceder ao site sem pré-gastar "www" para o seu nome DNS.

Por exemplo, se o seu site estático for nomeado www.contoso.com, os seus utilizadores podem aceder ao seu site usando contoso.com sem a necessidade de preparar www com o nome DNS.

Como descrito anteriormente, os registos CNAME não são suportados no ápice da zona. Então, não pode usar um disco CNAME para apontar contoso.com para o seu ponto final do CDN. Em vez disso, pode utilizar um registo de pseudónimos para apontar o ápice da zona diretamente para um ponto final de CDN.

> [!NOTE]
> Apontar um ápice de zona para os pontos finais da CDN para o Azure CDN da Akamai não é atualmente suportado.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os registos de pseudónimos, consulte os seguintes artigos:

- [Tutorial: Configure um registo de pseudónimo para se referir a um endereço IP público do Azure](tutorial-alias-pip.md)
- [Tutorial: Configure um registo de pseudónimo para apoiar nomes de domínio apex com Traffic Manager](tutorial-alias-tm.md)
- [FAQ sobre DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
