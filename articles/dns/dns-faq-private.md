---
title: FAQ do DNS Privado do Azure
description: Neste artigo, saiba frequentemente perguntas sobre O DNS Privado azure
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: 4de585a965cfefa6399b0c0929a8f732d0712617
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939425"
---
# <a name="azure-private-dns-faq"></a>FAQ do DNS Privado do Azure

Seguem-se frequentemente perguntas sobre o DNS privado azure.

## <a name="does-azure-dns-support-private-domains"></a>O Azure DNS suporta domínios privados?

Os domínios privados são suportados utilizando a função de zonas DNS Privadas Azure. As zonas privadas de DNS são resoláveis apenas a partir de redes virtuais especificadas. Para mais informações, consulte a [visão geral](private-dns-overview.md).

Para obter informações sobre outras opções internas de DNS em Azure, consulte [a resolução de nomes para VMs e exemplos de papéis](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>As zonas privadas de DNS de Azure funcionarão em todas as regiões de Azure?

Sim. As Zonas Privadas são apoiadas para a resolução de DNS entre redes virtuais em todas as regiões de Azure. As Zonas Privadas funcionam mesmo sem espreitar explicitamente as redes virtuais. Todas as redes virtuais devem estar ligadas à zona privada de DNS.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>A conectividade com a Internet é necessária a partir de redes virtuais para zonas privadas?

Não. As zonas privadas funcionam juntamente com redes virtuais. Usa-os para gerir domínios para máquinas virtuais ou outros recursos dentro e fora das redes virtuais. A conectividade da Internet não é necessária para a resolução de nomes.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>A mesma zona privada pode ser utilizada para várias redes virtuais para resolução?

Sim. Você pode ligar uma zona privada de DNS com milhares de redes virtuais. Para mais informações, consulte [Limites DNS Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>Uma rede virtual que pertence a uma subscrição diferente pode estar ligada a uma zona privada?

Sim. Deve ter permissão de operação nas redes virtuais e na zona privada de DNS. A permissão de escrita pode ser concedida a vários papéis RBAC. Por exemplo, o papel rBAC do Classic Network Contributor tem permissões de escrita para redes virtuais e as zonas privadas de DNS O papel do Contribuinte tem permissões de escrita nas zonas privadas de DNS. Para obter mais informações sobre as funções RBAC, consulte [o controlo de acesso baseado em funções.](../role-based-access-control/overview.md)

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Os registos DNS da máquina virtual registados automaticamente numa zona privada serão automaticamente eliminados quando eliminar a máquina virtual?

Sim. Se eliminar uma máquina virtual dentro de uma rede virtual ligada com registo automático ativado, os registos registados são automaticamente eliminados.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Pode um registo de máquina virtual registado automaticamente numa zona privada a partir de uma rede virtual ligada ser eliminado manualmente?

Sim. Pode substituir os registos DNS registados automaticamente com um registo DNS criado manualmente na zona. A seguinte pergunta e resposta abordam este tema.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>O que acontece quando tento criar manualmente um novo registo DNS numa zona privada que tenha o mesmo nome de anfitrião que uma máquina virtual registada automaticamente numa rede virtual ligada?

Tenta criar manualmente um novo registo DNS numa zona privada com o mesmo nome de anfitrião que uma máquina virtual existente e registada automaticamente numa rede virtual ligada. Quando o fizer, o novo registo DNS substitui o registo de máquinavirtual registado automaticamente. Se tentar apagar este registo DNS criado manualmente da zona novamente, o exclusão tem sucesso. O registo automático volta a repetir-se enquanto a máquina virtual ainda existir e tiver um IP privado ligado à mesma. O registo DNS é recriado automaticamente na zona.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>O que acontece quando desligamos uma rede virtual ligada de uma zona privada? Os registos de máquinas virtuais registados automaticamente da rede virtual também serão removidos da zona?

Sim. Para desligar uma rede virtual ligada a partir de uma zona privada, atualiza a zona DNS para remover a ligação de rede virtual associada. Neste processo, os registos de máquinas virtuais que foram automaticamente registados são removidos da zona.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>O que acontece quando apagamos uma rede virtual ligada que está ligada a uma zona privada? Temos de atualizar manualmente a zona privada para desligar a rede virtual como uma rede virtual ligada a partir da zona?

Não. Ao eliminar uma rede virtual ligada sem a desvincular primeiro de uma zona privada, a sua operação de eliminação tem sucesso e as ligações à zona DNS são automaticamente desimpedidas.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>A resolução do DNS utilizando o FQDN padrão (internal.cloudapp.net) ainda funcionará mesmo quando uma zona privada (por exemplo, private.contoso.com) está ligada a uma rede virtual?

Sim. As Zonas Privadas não substituem a zona de internal.cloudapp.net fornecida por Azure. Quer confie no internal.cloudapp.net fornecido pelo Azure ou na sua própria zona privada, utilize o FQDN da zona contra a sua determinação.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Será que o Sufixo DNS em máquinas virtuais dentro de uma rede virtual ligada será alterado para o da zona privada?

Não. O sufixo DNS nas máquinas virtuais da sua rede virtual ligada permanece como o sufixo fornecido por Azure ("*.internal.cloudapp.net"). Pode alterar manualmente este sufixo DNS nas suas máquinas virtuais para a zona privada.
Para obter orientações sobre como alterar este sufixo consulte [use DNS dinâmicos para registar nomes de anfitriões no seu próprio servidor DNS](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-ddns#windows-clients)

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>Quais são os limites de utilização para as zonas privadas Azure DNS?

Consulte [os limites de DNS azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) para obter detalhes sobre os limites de utilização para as zonas privadas Azure DNS.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Porque é que as minhas zonas privadas de DNS não aparecem numa nova experiência de portal?

Se a sua zona privada de DNS existente foi criada através da Pré-Visualização API, tem de migrar essas zonas para um novo modelo de recursos. As zonas privadas de DNS criadas com recurso à Pré-visualização API não aparecerão em nova experiência de portal. Veja abaixo as instruções sobre como migrar para um novo modelo de recurso.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Como migrarei as minhas zonas privadas de DNS para o novo modelo?

Recomendamos vivamente que emigra para o novo modelo de recursos o mais rapidamente possível. No entanto, o modelo de recursos legados será suportado, no entanto, outras funcionalidades não serão desenvolvidas em cima deste modelo. No futuro, pretendemos depretá-lo a favor de um novo modelo de recursos. Para obter orientações sobre como migrar as suas zonas privadas de DNS existentes para um novo modelo de recursos consulte guia de[migração para zonas privadas Azure DNS](private-dns-migration-guide.md).

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre o DNS Privado Azure](private-dns-overview.md)
