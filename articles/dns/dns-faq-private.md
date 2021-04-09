---
title: FAQ do DNS Privado do Azure
description: Neste artigo, aprenda perguntas frequentes sobre Azure Private DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 01/15/2021
ms.author: rohink
ms.openlocfilehash: 3272f4fea4169fd5e91b1ba2b97ad72dfbb49080
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98247391"
---
# <a name="azure-private-dns-faq"></a>FAQ do DNS Privado do Azure

As seguintes perguntas são frequentemente feitas sobre o Azure private DNS.

## <a name="does-azure-dns-support-private-domains"></a>O Azure DNS suporta domínios privados?

Os domínios privados são suportados utilizando a funcionalidade Azure Private DNS. As zonas privadas de DNS só são resolúveis dentro de redes virtuais especificadas. Para mais informações, consulte a [visão geral.](private-dns-overview.md)

Para obter informações sobre outras opções internas de DNS em Azure, consulte [a resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>As zonas privadas de DNS do Azure funcionarão em todas as regiões de Azure?

Sim. As Zonas Privadas são apoiadas para a resolução de DNS entre redes virtuais em todas as regiões de Azure. Private Zones funciona mesmo sem olhar explicitamente para as redes virtuais. Todas as redes virtuais devem estar ligadas à zona privada de DNS.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>A conectividade com a internet a partir de redes virtuais é necessária para zonas privadas?

N.º Zonas privadas funcionam juntamente com redes virtuais. Utiliza-os para gerir domínios para máquinas virtuais ou outros recursos dentro e fora das redes virtuais. A conectividade na Internet não é necessária para a resolução de nomes.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>A mesma zona privada pode ser utilizada para várias redes virtuais para resolução?

Sim. Você pode ligar uma zona privada de DNS com milhares de redes virtuais. Para mais informações, consulte [limites de DNS do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits)

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>Uma rede virtual que pertence a uma subscrição diferente pode estar ligada a uma zona privada?

Sim. Deve ter permissão de operação nas redes virtuais e na zona privada do DNS. A permissão de escrita pode ser concedida a vários papéis de Azure. Por exemplo, o papel Azure do Contribuinte de Rede Clássica tem permissões de escrita para redes virtuais e o papel de contribuinte de zonas privadas de DNS tem permissões de escrita nas zonas privadas de DNS. Para obter mais informações sobre os papéis do Azure, consulte [o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Os registos DNS da máquina virtual automaticamente registados numa zona privada serão automaticamente eliminados quando eliminar a máquina virtual?

Sim. Se eliminar uma máquina virtual dentro de uma rede virtual ligada com auto-registo ativada, os registos registados são automaticamente eliminados.

## <a name="ive-reconfigured-the-os-in-my-virtual-machine-to-have-a-new-host-name-or-static-ip-address-why-dont-i-see-that-change-reflected-in-the-private-zone"></a>Reconfigurei o so na minha máquina virtual para ter um novo nome de hospedeiro ou endereço IP estático. Por que não vejo a mudança refletida na zona privada?

Os registos da zona privada são preenchidos pelo serviço Azure DHCP; as mensagens de registo do cliente são ignoradas. Se tiver desativado o suporte ao cliente DHCP no VM configurando um endereço IP estático, as alterações ao nome de anfitrião ou IP estático no VM não se refletem na zona.

## <a name="i-have-configured-a-preferred-dns-suffix-in-my-windows-virtual-machine-why-are-my-records-still-registered-in-the-zone-linked-to-the-virtual-network"></a>Configurei um sufixo de DNS preferido na minha máquina virtual do Windows. Porque é que os meus registos ainda estão registados na zona ligada à rede virtual?

O serviço Azure DHCP ignora qualquer sufixo DNS quando regista a zona privada de DNS. Por exemplo, se a sua máquina virtual estiver configurada `contoso.com` como o sufixo principal de DNS, mas a rede virtual está ligada à zona privada de `fabrikam.com` DNS, o registo da máquina virtual aparece na zona privada do `fabrikam.com` DNS.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Pode um registo de máquina virtual automaticamente registado numa zona privada a partir de uma rede virtual ligada ser apagado manualmente?

Sim. Pode substituir os registos DNS registados automaticamente com um registo DNS criado manualmente na zona. A seguinte pergunta e resposta abordam este tema.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>O que acontece quando tento criar manualmente um novo registo de DNS numa zona privada que tenha o mesmo nome de hospedeiro que uma máquina virtual registada automaticamente numa rede virtual ligada?

Você tenta criar manualmente um novo registo DNS em uma zona privada que tem o mesmo nome de hospedeiro que uma máquina virtual existente e automaticamente registada numa rede virtual ligada. Quando o fizer, o novo registo DNS substitui o registo de máquinas virtuais automaticamente registado. Se tentar apagar este registo DNS criado manualmente da zona novamente, o excluir terá sucesso. O registo automático volta a acontecer desde que a máquina virtual ainda exista e tenha um IP privado ligado à mesma. O registo DNS é recriada automaticamente na zona.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>O que acontece quando desvinculamos uma rede virtual ligada de uma zona privada? Os registos de máquinas virtuais automaticamente registados da rede virtual também serão removidos da zona?

Sim. Para desvincular uma rede virtual ligada de uma zona privada, atualiza a zona DNS para remover a ligação de rede virtual associada. Neste processo, os registos de máquinas virtuais que foram automaticamente registados são removidos da zona.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>O que acontece quando apagamos uma rede virtual ligada que está ligada a uma zona privada? Temos de atualizar manualmente a zona privada para desvincular a rede virtual como uma rede virtual ligada da zona?

N.º Quando elimina uma rede virtual ligada sem desvincular primeiro de uma zona privada, a sua operação de eliminação tem sucesso e os links para a zona DNS são automaticamente limpos.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>A resolução do DNS utilizando o FQDN predefinido (internal.cloudapp.net) ainda funciona mesmo quando uma zona privada (por exemplo, private.contoso.com) está ligada a uma rede virtual?

Sim. As Zonas Privadas não substituem a zona de internal.cloudapp.net fornecida pelo Azure. Quer confie na internal.cloudapp.net fornecida pelo Azure ou na sua própria zona privada, utilize o FQDN da zona contra a quais pretende resolver.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Será que o sufixo DNS em máquinas virtuais dentro de uma rede virtual ligada será alterado para o da zona privada?

N.º O sufixo DNS nas máquinas virtuais da sua rede virtual ligada permanece como o sufixo padrão fornecido pelo Azure ("*.internal.cloudapp.net"). Pode alterar manualmente este sufixo DNS nas suas máquinas virtuais para o da zona privada.
Para obter orientações sobre como alterar este sufixo [consulte-se Use DNS dinâmico para registar nomes de anfitriões no seu próprio servidor DNS](../virtual-network/virtual-networks-name-resolution-ddns.md#windows-clients)

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>Quais são os limites de utilização para as zonas privadas Azure DNS?

Consulte os [limites de DNS do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits) para obter mais informações sobre os limites de utilização das zonas privadas Azure DNS.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Porque é que as minhas zonas privadas de DNS não aparecem em novas experiências de portal?

Se a sua zona de DNS privada existente foi criada usando a API de pré-visualização, deve migrar estas zonas para um novo modelo de recursos. As zonas privadas de DNS criadas com a API de pré-visualização não aparecerão na experiência do novo portal. Veja abaixo as instruções sobre como migrar para um novo modelo de recursos.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Como posso migrar as minhas zonas privadas de DNS existentes para o novo modelo?

Recomendamos vivamente que migrar para o novo modelo de recursos o mais rapidamente possível. O modelo de recursos legados será suportado, no entanto, outras funcionalidades não serão desenvolvidas em cima deste modelo. No futuro, pretendemos depreciá-lo a favor de um novo modelo de recursos. Para obter orientações sobre como migrar as suas zonas privadas de DNS existentes para o novo modelo de recursos consulte[o guia de migração para as zonas privadas do Azure DNS](private-dns-migration-guide.md).

### <a name="does-azure-dns-private-zones-store-any-customer-content"></a>As zonas privadas Azure DNS armazenam algum conteúdo do cliente?

Não, as zonas privadas Azure DNS não armazenam nenhum conteúdo do cliente.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre o Azure Private DNS](private-dns-overview.md)
