---
title: FAQ de DNS privado do Azure
description: Perguntas freqüentes sobre DNS privado do Azure
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: 480cf22491dbbfcb9fe1961b5c9a7aa6fe12a0cb
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274050"
---
# <a name="azure-private-dns-faq"></a>FAQ de DNS privado do Azure

> [!IMPORTANT]
> O DNS privado do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="does-azure-dns-support-private-domains"></a>O DNS do Azure suporta domínios privados?

Suporte para domínios privados é suportado com a funcionalidade de zonas de DNS privado do Azure. Zonas DNS privadas são geridas com as mesmas ferramentas como zonas de DNS do Azure para a internet. Eles são resolvíveis apenas a partir de dentro de suas redes virtuais especificados. Para obter mais informações, consulte a [descrição geral](private-dns-overview.md).

Para obter informações sobre outras opções de DNS internas no Azure, consulte [resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>As zonas privadas do DNS do Azure irá funcionar entre regiões do Azure?

Sim. As zonas privadas é suportada para a resolução DNS entre redes virtuais em regiões do Azure. As zonas privadas do funciona até mesmo sem peering explicitamente as redes virtuais. Todas as redes virtuais têm de ser especificadas como redes virtuais de resolução para a zona privada. Clientes podem ter as redes virtuais para ser executado o peering para o tráfego TCP/HTTP para o fluxo de uma região para outra.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>É a conectividade à Internet de redes virtuais necessárias para zonas privadas?

Não. Zonas privadas trabalham em conjunto com redes virtuais. Os clientes usá-los para gerir domínios para máquinas virtuais ou outros recursos dentro e entre redes virtuais. Conectividade com a Internet não é necessária para resolução de nomes.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>Da mesma zona privada pode ser utilizada para várias redes virtuais para a resolução?

Sim. Pode associar até 1000 de redes virtuais com uma única zona privada.

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-linked-virtual-network-to-a-private-zone"></a>Pode uma rede virtual que pertence a uma subscrição diferente ser adicionada como uma rede virtual ligada a uma zona privada?

Sim. Tem de ter permissão de operação de escrita sobre as redes virtuais e a zona DNS privada. A permissão de escrita pode ser concedida a várias funções do RBAC. Por exemplo, a função RBAC de contribuinte de rede clássica tem permissões de escrita para redes virtuais. Para obter mais informações sobre funções RBAC, veja [controlo de acesso baseado em funções](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Os registos DNS da máquina de virtual automaticamente registado numa zona privada sejam eliminados automaticamente ao eliminar a máquina virtual?

Sim. Se eliminar uma máquina virtual numa rede virtual ligada com o registo automático ativado, os registos registrados são automaticamente eliminados.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Um registo de máquina de virtual automaticamente registrados numa zona privada de uma rede virtual ligada pode ser eliminado manualmente?

Sim. Pode substituir os registos DNS registados automaticamente com um registo DNS criado manualmente na zona. A seguinte pergunta e resposta abordar neste tópico.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>O que acontece ao tentar criar manualmente um novo registo DNS para uma zona privada que tenha o mesmo nome do anfitrião como uma máquina virtual existente automaticamente registada numa rede virtual ligada?

Tente criar manualmente um novo registo DNS para uma zona privada que tenha o mesmo nome do anfitrião como uma máquina de virtual existente, é automaticamente registada numa rede virtual ligada. Quando o fizer, o novo registo DNS substitui o registo de máquina de virtual registado automaticamente. Se tentar eliminar este registo DNS criado manualmente a partir da zona da novamente, a eliminação for concluída com êxito. O registo automático voltar a ocorrer, desde que a máquina virtual ainda existe e tem um IP privado ligados ao mesmo. O registo DNS seja recriado automaticamente na zona.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>O que acontece quando podemos desassociar a uma rede virtual ligada a partir de uma zona privada? Serão os registos de máquina de virtual registado automaticamente da rede virtual removidos da zona demasiado?

Sim. Para desassociar a uma rede virtual ligada a partir de uma zona privada, atualize a zona DNS para remover a ligação de rede virtual associado. Nesse processo, os registos de máquina virtual que foram registados automaticamente são removidos da zona.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>O que acontece quando podemos eliminar uma rede virtual ligada que está ligada a uma zona privada? Temos de atualizar manualmente a zona privada ao desassociar a rede virtual como uma rede virtual ligada a partir da zona?

Sim. Quando elimina uma rede virtual ligada sem desassociar primeiro de uma zona privada, a operação de eliminação for concluída com êxito. Mas a rede virtual não é automaticamente desassociada da sua zona privada, se aplicável. Deve desassociar manualmente a rede virtual a partir da zona privada. Por esse motivo, desassocie a rede virtual da sua zona privada antes de o eliminar.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Resolução de DNS ao utilizar a predefinição FQDN (internal.cloudapp.net) continuarão a funcionar mesmo quando uma zona privada (por exemplo, private.contoso.com) está ligada a uma rede virtual?

Sim. As zonas privadas do não substitui as resoluções DNS padrão através da utilização da zona de internal.cloudapp.net fornecida pelo Azure. É oferecido como uma funcionalidade adicional ou um aprimoramento. Se se basear no internal.cloudapp.net fornecida pelo Azure ou em sua própria zona privada, utilize o FQDN da zona que pretende resolver contra.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Será alterado o sufixo DNS em máquinas virtuais numa rede virtual ligada para que a zona privada?

Não. O sufixo DNS nas máquinas virtuais na sua rede virtual ligado permanece como o sufixo predefinido de fornecida pelo Azure ("*. internal.cloudapp.net"). Pode alterar manualmente este sufixo DNS nas suas máquinas virtuais para que a zona privada.

## <a name="what-are-the-usage-limits-for-azure-private-dns"></a>Quais são os limites de utilização do DNS privado do Azure?

Os limites predefinidos seguintes aplicam-se ao utilizar o DNS privado do Azure.

| Resource | Limite predefinido |
| --- | --- |
|Zonas DNS privadas por subscrição|1000|
|Conjuntos de registos por zona de DNS privado|25,000|
|Registos por conjunto de registos|20|
|Ligações de rede virtual por zona DNS privada|1000|
|Links de redes virtuais por zonas DNS privadas com o registo automático ativado|100|
|Número de zonas DNS privadas, que uma rede virtual pode obter associada com o registo automático ativado|1|
|Número de zonas DNS privadas que pode obter associada uma rede virtual|1000|

## <a name="is-there-portal-support-for-private-zones"></a>Existe suporte para zonas privadas do portal?

Sim e as zonas privadas já criadas através de APIs, PowerShell, CLI e SDKs são visíveis no portal do Azure.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Por que motivo não meu zonas DNS privadas existentes é exibido na experiência do novo portal?

Como parte da versão de pré-visualização atualizar Lançamos um novo modelo de recurso para zonas privadas do DNS. As zonas privadas do DNS existentes tem de ser migrados para o novo modelo de recursos antes de estes apresentado na nova experiência de portal. Veja abaixo para obter instruções sobre como migrar para o novo modelo de recursos.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Como posso migrar meu zonas privadas do DNS existentes para o novo modelo?
Recomendamos vivamente que migrar para o novo modelo de recurso logo que possível. Modelo de recursos herdados será suportado, no entanto, ainda mais serão não ser desenvolvidas funcionalidades sobre este modelo. No futuro, pretendemos preteri-la em favor do novo modelo de recursos. Para obter orientações sobre como migrar seu DNS privado existente ver zonas para o novo modelo de recurso[guia de migração para zonas privadas do DNS do Azure](private-dns-migration-guide.md).

## <a name="next-steps"></a>Passos Seguintes

- [Saiba mais sobre DNS privado do Azure](private-dns-overview.md)