---
title: Registos descrição geral de alias de DNS do Azure
description: Descrição geral do suporte para registos de alias no DNS do Azure de Microsoft.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 3/21/2019
ms.author: victorh
ms.openlocfilehash: 87ca7cae8e9170c8c437d0961cb1acb2e0dd0eb1
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337651"
---
# <a name="azure-dns-alias-records-overview"></a>Registos descrição geral de alias de DNS do Azure

Registos de alias de DNS do Azure são qualificações num conjunto de registos de DNS. Eles podem fazer referência a outros recursos do Azure a partir de sua zona DNS. Por exemplo, pode criar um conjunto de registos de alias que faz referência a um endereço IP público do Azure em vez de um registo. Os pontos do conjunto de registos alias para um IP público do Azure endereços dinamicamente a instância de serviço. Como resultado, o conjunto de registos de alias perfeitamente atualiza automaticamente durante a resolução de DNS.

Um conjunto de registos de alias é suportado para os seguintes tipos de registos numa zona de DNS do Azure: 

- A
- AAAA
- CNAME

> [!NOTE]
> Se pretende usar um registo de alias para os tipos de registos A ou AAAA para apontar para um [perfil do Gestor de tráfego do Azure](../traffic-manager/quickstart-create-traffic-manager-profile.md) deve certificar-se de que o perfil do Gestor de tráfego tem apenas [pontos finais externos](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints). Tem de fornecer o endereço IPv4 ou IPv6 para pontos finais externos no Gestor de tráfego. O ideal é que utilize endereços IP estáticos.

## <a name="capabilities"></a>Capacidades

- **Ponto para um recurso IP público a partir de um conjunto de registos de A/AAAA de DNS.** Pode criar um conjunto de registos A/AAAA e torná-lo um conjunto de registos de alias para apontar para um recurso IP público. O conjunto de registos de DNS é automaticamente se o endereço IP público é alterado ou é eliminada. Os registos que apontam para endereços IP incorretos dangling DNS são evitados.

- **Ponto para um perfil do Gestor de tráfego a partir de um conjunto de registos DNS A/AAAA/CNAME.** Pode criar um A/AAAA ou registo CNAME defina e utilize registos de alias para apontar para um perfil do Gestor de tráfego. É especialmente útil quando precisa encaminhar o tráfego no apex de zona, como os registos CNAME tradicionais não são suportados para um vértice da zona. Por exemplo, digamos que seu perfil do Gestor de tráfego é myprofile.trafficmanager.net e sua zona DNS da empresa for contoso.com. Pode criar um conjunto de registos de alias do tipo A/AAAA para contoso.com (o vértice da zona) e aponte para myprofile.trafficmanager.net.
- **Aponte para um ponto de extremidade de rede de entrega conteúdo (CDN)**. Isto é útil quando criar os Web sites estáticos com o armazenamento do Azure e CDN do Azure.
- **Aponte para outro conjunto de registos de DNS na mesma zona.** Os registos de alias podem fazer referência a outros conjuntos de registos do mesmo tipo. Por exemplo, um conjunto de registos CNAME de DNS pode ser um alias para outro conjunto de registos CNAME. Essa disposição é útil se desejar alguns conjuntos de registos para ser aliases e alguns não aliases.

## <a name="scenarios"></a>Cenários

Existem alguns cenários comuns para os registos de Alias.

### <a name="prevent-dangling-dns-records"></a>Impedir dangling registos DNS

Um problema comum com registos DNS tradicionais é dangling registos. Por exemplo, registos DNS que não foram atualizados para refletir as alterações para endereços IP. O problema ocorre especialmente com A/AAAA ou CNAME tipos de registos.

Com um registo de zona DNS tradicional, se o IP de destino ou CNAME já não existir, o registo DNS associado ao mesmo têm de ser manualmente atualizado. Em algumas organizações, uma atualização manual não poderá ocorrer no tempo devido a problemas de processo ou a separação de funções e níveis de permissão associada. Por exemplo, uma função pode ter a autoridade para eliminar um CNAME ou endereço IP que pertence a uma aplicação. Mas não tem autoridade suficiente para atualizar o registo DNS que aponta para esses destinos. Um atraso na atualização do registo DNS pode potencialmente causar uma falha para os utilizadores.

Registos de alias impedem dangling referências com a União totalmente o ciclo de vida de um registo DNS com um recurso do Azure. Por exemplo, considere um registo DNS que é qualificado como um registo de alias para apontar para um endereço IP público ou um perfil do Gestor de tráfego. Se esses recursos subjacentes forem excluídos, o registo de alias DNS é removido ao mesmo tempo.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>Atualizar o conjunto de registos DNS automaticamente quando são alterados endereços IP da aplicação

Este cenário é semelhante ao anterior. Talvez um aplicativo é movido, ou a máquina virtual subjacente é reiniciada. Um registo de alias, em seguida, atualiza automaticamente quando o endereço IP é alterado para o recurso IP público subjacente. Isso evita potenciais riscos de segurança de direcionar os utilizadores para outro aplicativo que lhe foi atribuído o endereço IP público antigo.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Alojar aplicações com balanceamento de carga no vértice da zona

O protocolo DNS impede a atribuição de registos CNAME no vértice da zona. Por exemplo, se o seu domínio é contoso.com; Pode criar registos CNAME para somelable.contoso.com; mas não é possível criar o CNAME para contoso.com em si.
Esta restrição apresenta um problema para os proprietários da aplicação que tenham aplicações com balanceamento de carga por trás [Gestor de tráfego do Azure](../traffic-manager/traffic-manager-overview.md). Uma vez que a utilização de um perfil do Gestor de tráfego requer a criação de um registo CNAME, não é possível apontar para o perfil do Gestor de tráfego do vértice da zona.

Esse problema pode ser resolvido utilizando registos de alias. Ao contrário de registos CNAME, os registos de alias podem ser criados no vértice da zona e proprietários de aplicativos podem utilizá-lo para apontar o respetivo registo de apex de zona para um perfil de Gestor de tráfego que tem pontos finais externos. Os proprietários de aplicativos podem apontar para o mesmo perfil de Gestor de tráfego, que é utilizado para qualquer outro domínio na sua zona DNS.

Por exemplo, contoso.com e www\.contoso.com pode apontar para o mesmo perfil de Gestor de tráfego. Para saber mais sobre como utilizar os registos de alias com perfis do Gestor de tráfego do Azure, veja a secção de passos seguinte.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Aponte o vértice da zona para pontos finais da CDN do Azure

Tal como um perfil do Gestor de tráfego, também pode utilizar os registos de alias para apontar o vértice da zona DNS para pontos finais da CDN do Azure. Isto é útil quando criar os Web sites estáticos com o armazenamento do Azure e CDN do Azure. Em seguida, pode aceder ao site sem "www" para o seu nome DNS de prefixação.

Por exemplo, se o seu Web site estático com o nome www.contoso.com, os utilizadores podem aceder a seu site usando o contoso.com sem que seja necessário preceder www para o nome DNS.

Conforme descrito anteriormente, os registos CNAME não são suportados no vértice da zona. Assim, não é possível utilizar um registo CNAME para apontar o contoso.com para o ponto final da CDN. Em vez disso, pode utilizar um registo de alias para apontar diretamente o vértice da zona para um ponto final da CDN.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre os registos de alias, veja os artigos seguintes:

- [Tutorial: Configurar um registo de alias para fazer referência a um endereço IP público do Azure](tutorial-alias-pip.md)
- [Tutorial: Configurar um registo de alias para oferecer suporte a nomes de domínio com o Gestor de tráfego do vértice](tutorial-alias-tm.md)
- [FAQ sobre DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
