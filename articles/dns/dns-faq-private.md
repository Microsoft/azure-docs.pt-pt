---
title: FAQ do DNS Privado do Azure
description: Perguntas frequentes sobre o Azure DNS privado
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 09/20/2019
ms.author: victorh
ms.openlocfilehash: fca7359f9fa54899bb72be3b939e1a1839dbfbd1
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155714"
---
# <a name="azure-private-dns-faq"></a>FAQ do DNS Privado do Azure

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="does-azure-dns-support-private-domains"></a>O DNS do Azure dá suporte a domínios privados?

Há suporte para domínios privados usando o recurso de zonas de DNS privado do Azure. As zonas de DNS privado são gerenciadas usando as mesmas ferramentas que as zonas DNS do Azure voltadas para a Internet. Elas são resolvidas somente de dentro de suas redes virtuais especificadas. Para obter mais informações, consulte a [visão geral](private-dns-overview.md).

Para obter informações sobre outras opções de DNS interno no Azure, consulte [resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Zonas Privadas do DNS do Azure funcionará nas regiões do Azure?

Sim. As zonas privadas têm suporte para a resolução de DNS entre redes virtuais em regiões do Azure. As zonas privadas funcionam mesmo sem emparelhar explicitamente as redes virtuais. Todas as redes virtuais devem ser especificadas como redes virtuais de resolução para a zona privada. Talvez você precise que as redes virtuais sejam emparelhadas para o tráfego TCP/HTTP fluir de uma região para outra.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>A conectividade com a Internet de redes virtuais é necessária para zonas privadas?

Não. As zonas privadas funcionam junto com as redes virtuais. Você os usa para gerenciar domínios para máquinas virtuais ou outros recursos dentro e entre redes virtuais. A conectividade com a Internet não é necessária para a resolução de nomes.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>A mesma zona privada pode ser usada para várias redes virtuais para resolução?

Sim. Você pode associar até 1000 redes virtuais a uma única zona privada.

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-linked-virtual-network-to-a-private-zone"></a>Uma rede virtual que pertence a uma assinatura diferente pode ser adicionada como uma rede virtual vinculada a uma zona privada?

Sim. Você deve ter a permissão de operação de gravação nas redes virtuais e na zona DNS privada. A permissão de gravação pode ser concedida a várias funções de RBAC. Por exemplo, a função de RBAC de colaborador de rede clássica tem permissões de gravação para redes virtuais. Para obter mais informações sobre as funções RBAC, consulte [controle de acesso baseado em função](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Os registros DNS da máquina virtual automaticamente registrados em uma zona privada serão excluídos automaticamente quando você excluir a máquina virtual?

Sim. Se você excluir uma máquina virtual em uma rede virtual vinculada com o registro automático habilitado, os registros registrados serão excluídos automaticamente.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Um registro de máquina virtual registrado automaticamente em uma zona privada de uma rede virtual vinculada pode ser excluído manualmente?

Sim. Você pode substituir os registros DNS registrados automaticamente por um registro DNS criado manualmente na zona. A pergunta e a resposta a seguir abordam este tópico.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>O que acontece quando tento criar manualmente um novo registro DNS em uma zona privada que tenha o mesmo nome de host que uma máquina virtual existente registrada automaticamente em uma rede virtual vinculada?

Você tenta criar manualmente um novo registro DNS em uma zona privada que tem o mesmo nome de host que uma máquina virtual existente registrada automaticamente em uma rede virtual vinculada. Quando você fizer isso, o novo registro de DNS substituirá o registro de máquina virtual registrado automaticamente. Se você tentar excluir esse registro DNS criado manualmente da zona novamente, a exclusão terá sucesso. O registro automático ocorre novamente, desde que a máquina virtual ainda exista e tenha um IP privado anexado a ela. O registro DNS é recriado automaticamente na zona.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>O que acontece quando desvinculamos uma rede virtual vinculada de uma zona privada? Os registros da máquina virtual automaticamente registrados da rede virtual também serão removidos da zona?

Sim. Para desvincular uma rede virtual vinculada de uma zona privada, você atualiza a zona DNS para remover o link de rede virtual associado. Nesse processo, os registros de máquina virtual que foram registrados automaticamente são removidos da zona.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>O que acontece quando excluímos uma rede virtual vinculada que está vinculada a uma zona privada? Precisamos atualizar manualmente a zona privada para desvincular a rede virtual como uma rede virtual vinculada da zona?

Sim. Quando você exclui uma rede virtual vinculada sem desvinculá-la de uma zona privada primeiro, sua operação de exclusão é realizada com sucesso. Mas a rede virtual não será desvinculada automaticamente da sua zona privada, se houver. Você deve desvincular manualmente a rede virtual da zona privada. Por esse motivo, desvincule sua rede virtual da sua zona privada antes de excluí-la.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>A resolução de DNS usando o FQDN padrão (internal.cloudapp.net) ainda funcionará mesmo quando uma zona privada (por exemplo, private.contoso.com) estiver vinculada a uma rede virtual?

Sim. As zonas privadas não substituem as resoluções de DNS padrão usando a zona internal.cloudapp.net fornecida pelo Azure. Ele é oferecido como um recurso ou aprimoramento adicional. Se você se basear no internal.cloudapp.net fornecido pelo Azure ou em sua própria zona privada, use o FQDN da zona que você deseja resolver.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>O sufixo DNS em máquinas virtuais em uma rede virtual vinculada será alterado para o da zona privada?

Não. O sufixo DNS nas máquinas virtuais em sua rede virtual vinculada permanece como o sufixo padrão fornecido pelo Azure ("*. internal.cloudapp.net"). Você pode alterar manualmente esse sufixo DNS em suas máquinas virtuais para a da zona privada.

## <a name="what-are-the-usage-limits-for-azure-private-dns"></a>Quais são os limites de uso do Azure DNS privado?

Os limites padrão a seguir se aplicam quando você usa o Azure DNS privado.

| Resource | Limite predefinido |
| --- | --- |
|Zonas de DNS privado por assinatura|1000|
|Conjuntos de registros por zona de DNS privado|25,000|
|Registros por conjunto de registros|20|
|Links de rede virtual por zona DNS privada|1000|
|Links de redes virtuais por zonas DNS privadas com o registro automático habilitado|100|
|Número de zonas DNS privadas às quais uma rede virtual pode ser vinculada com o registro automático habilitado|1|
|Número de zonas DNS privadas que uma rede virtual pode obter vinculada|1000|

## <a name="is-there-portal-support-for-private-zones"></a>Há suporte do portal para zonas privadas?

Sim, e as zonas privadas que já foram criadas por meio de APIs, PowerShell, CLI e SDKs são visíveis na portal do Azure.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Por que minhas zonas DNS privadas existentes aparecem na nova experiência do portal?

Como parte da versão de atualização de visualização, enviamos um novo modelo de recurso para zonas DNS privadas. Suas zonas DNS privadas existentes precisarão ser migradas para o novo modelo de recurso antes que elas possam aparecer na nova experiência do Portal. Consulte abaixo para obter instruções sobre como migrar para o novo modelo de recurso.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Como fazer migrar minhas zonas DNS privadas existentes para o novo modelo?
É altamente recomendável que você migre para o novo modelo de recurso assim que possível. O modelo de recurso herdado terá suporte, no entanto, outros recursos não serão desenvolvidos sobre esse modelo. No futuro, pretendemos substituí-lo em favor do novo modelo de recurso. Para obter orientação sobre como migrar suas zonas DNS privadas existentes para o novo modelo de recurso, consulte[Guia de migração para zonas privadas do DNS do Azure](private-dns-migration-guide.md).

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre o Azure DNS privado](private-dns-overview.md)