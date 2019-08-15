---
title: Visão geral dos registros de alias do DNS do Azure
description: Visão geral do suporte para registros de alias no DNS Microsoft Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 08/09/2019
ms.author: victorh
ms.openlocfilehash: 9a3cdb846921c2d73dd2cca5d679663c1ba9e192
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946888"
---
# <a name="azure-dns-alias-records-overview"></a>Visão geral dos registros de alias do DNS do Azure

Os registros de alias do DNS do Azure são qualificações em um conjunto de registros DNS. Eles podem fazer referência a outros recursos do Azure de dentro de sua zona DNS. Por exemplo, você pode criar um conjunto de registros de alias que faça referência a um endereço IP público do Azure em vez de um registro A. Seu conjunto de registros de alias aponta para uma instância de serviço de endereço IP público do Azure dinamicamente. Como resultado, o conjunto de registros de alias é totalmente atualizado durante a resolução DNS.

Um conjunto de registros de alias tem suporte para os seguintes tipos de registro em uma zona DNS do Azure: 

- A
- AAAA
- CNAME

> [!NOTE]
> Se você pretende usar um registro de alias para os tipos de registro a ou AAAA para apontar para um [perfil do Gerenciador de tráfego do Azure](../traffic-manager/quickstart-create-traffic-manager-profile.md) , verifique se o perfil do Gerenciador de tráfego tem apenas [pontos de extremidade externos](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints). Você deve fornecer o endereço IPv4 ou IPv6 para pontos de extremidade externos no Gerenciador de tráfego. Você não pode usar nomes de domínio totalmente qualificados (FQDNs) em pontos de extremidade. Idealmente, use endereços IP estáticos.

## <a name="capabilities"></a>Capacidades

- **Aponte para um recurso IP público de um conjunto de registros DNS A/AAAA.** Você pode criar um conjunto de registros A/AAAA e torná-lo um conjunto de registros de alias para apontar para um recurso de IP público (Standard ou Basic). O conjunto de registros DNS será alterado automaticamente se o endereço IP público for alterado ou excluído. Os registros DNS pendente que apontam para endereços IP incorretos são evitados.

   Há um limite atual de 20 conjuntos de registros de alias por recurso.

- **Aponte para um perfil do Gerenciador de tráfego de um conjunto de registros DNS A/AAAA/CNAME.** Você pode criar um conjunto de registros A/AAAA ou CNAME e usar registros de alias para apontar para um perfil do Gerenciador de tráfego. Ele é especialmente útil quando você precisa rotear o tráfego em um Apex de zona, já que os registros CNAME tradicionais não têm suporte para um Apex de zona. Por exemplo, digamos que seu perfil do Gerenciador de tráfego é myprofile.trafficmanager.net e sua zona DNS de negócios é contoso.com. Você pode criar um conjunto de registros de alias do tipo A/AAAA para contoso.com (o Apex da zona) e apontar para myprofile.trafficmanager.net.
- **Aponte para um ponto de extremidade da CDN (rede de distribuição de conteúdo) do Azure**. Isso é útil quando você cria sites estáticos usando o armazenamento do Azure e a CDN do Azure.
- **Aponte para outro conjunto de registros DNS dentro da mesma zona.** Os registos de alias podem fazer referência a outros conjuntos de registos do mesmo tipo. Por exemplo, um conjunto de registros DNS CNAME pode ser um alias para outro conjunto de registros CNAME. Essa organização será útil se você quiser que alguns conjuntos de registros sejam aliases e alguns não aliases.

## <a name="scenarios"></a>Cenários

Há alguns cenários comuns para registros de alias.

### <a name="prevent-dangling-dns-records"></a>Impedir registros DNS pendente

Um problema comum com registros DNS tradicionais são os registros de pendente. Por exemplo, os registros DNS que não foram atualizados para refletir alterações em endereços IP. O problema ocorre especialmente com os tipos de registro A/AAAA ou CNAME.

Com um registro de zona DNS tradicional, se o IP de destino ou CNAME não existir mais, o registro DNS associado a ele deverá ser atualizado manualmente. Em algumas organizações, uma atualização manual pode não ocorrer em tempo devido a problemas de processo ou à separação de funções e níveis de permissão associados. Por exemplo, uma função pode ter autoridade para excluir um CNAME ou endereço IP que pertence a um aplicativo. Mas não tem autoridade suficiente para atualizar o registro DNS que aponta para esses destinos. Um atraso na atualização do registro DNS pode potencialmente causar uma interrupção para os usuários.

Os registros de alias impedem referências pendente acoplando rigidamente o ciclo de vida de um registro DNS com um recurso do Azure. Por exemplo, considere um registro DNS qualificado como um registro de alias para apontar para um endereço IP público ou um perfil do Gerenciador de tráfego. Se você excluir esses recursos subjacentes, o registro de alias de DNS se tornará um conjunto de registros vazio. Ele não faz mais referência ao recurso excluído.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>Atualizar o registro DNS-definido automaticamente quando os endereços IP do aplicativo são alterados

Esse cenário é semelhante ao anterior. Talvez um aplicativo seja movido ou a máquina virtual subjacente seja reiniciada. Um registro de alias é atualizado automaticamente quando o endereço IP é alterado para o recurso IP público subjacente. Isso evita possíveis riscos de segurança de direcionar os usuários para outro aplicativo que tenha sido atribuído ao endereço IP público antigo.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Hospedar aplicativos com balanceamento de carga no Apex da zona

O protocolo DNS impede a atribuição de registros CNAME no Apex da zona. Por exemplo, se seu domínio for contoso.com; Você pode criar registros CNAME para somelabel.contoso.com; Mas você não pode criar CNAME para contoso.com em si.
Essa restrição apresenta um problema para proprietários de aplicativos que têm aplicativos com balanceamento de carga por trás [do Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-overview.md). Como o uso de um perfil do Gerenciador de tráfego requer a criação de um registro CNAME, não é possível apontar para o perfil do Gerenciador de tráfego do Apex da zona.

Esse problema é resolvido usando registros de alias. Ao contrário dos registros CNAME, os registros de alias são criados no Apex da zona e os proprietários do aplicativo podem usá-lo para apontar o registro de Apex de zona para um perfil do Gerenciador de tráfego que tem pontos de extremidade externos. Os proprietários do aplicativo apontam para o mesmo perfil do Gerenciador de tráfego usado para qualquer outro domínio dentro de sua zona DNS.

Por exemplo, contoso.com e www\.contoso.com podem apontar para o mesmo perfil do Gerenciador de tráfego. Para saber mais sobre como usar registros de alias com perfis do Gerenciador de tráfego do Azure, consulte a seção próximas etapas.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Apontador da zona de ponto para pontos de extremidade da CDN do Azure

Assim como um perfil do Gerenciador de tráfego, você também pode usar registros de alias para apontar o Apex da zona DNS para pontos de extremidade da CDN do Azure. Isso é útil quando você cria sites estáticos usando o armazenamento do Azure e a CDN do Azure. Em seguida, você pode acessar o site sem ter prependência "www" para seu nome DNS.

Por exemplo, se seu site estático for denominado www.contoso.com, os usuários poderão acessar seu site usando o contoso.com sem a necessidade de colocar www no nome DNS.

Conforme descrito anteriormente, os registros CNAME não têm suporte no Apex da zona. Portanto, você não pode usar um registro CNAME para apontar contoso.com para o ponto de extremidade da CDN. Em vez disso, você pode usar um registro de alias para apontar a zona Apex para um ponto de extremidade CDN diretamente.

> [!NOTE]
> Atualmente, não há suporte para a indicação de um Apex de zona para pontos de extremidade CDN para a CDN do Azure da Akamai.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre os registros de alias, consulte os seguintes artigos:

- [Tutorial: Configurar um registro de alias para se referir a um endereço IP público do Azure](tutorial-alias-pip.md)
- [Tutorial: Configurar um registro de alias para dar suporte a nomes de domínio Apex com o Gerenciador de tráfego](tutorial-alias-tm.md)
- [FAQ sobre DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
