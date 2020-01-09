---
title: O que é um subrecurso de link de rede virtual de zonas privadas do DNS do Azure
description: Visão geral do subrecurso de link de rede virtual uma zona privada de DNS do Azure
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9181ef93dfedbc28b297bef48a0bc37ba6d69798
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646766"
---
# <a name="what-is-a-virtual-network-link"></a>O que é um link de rede virtual?

Depois de criar uma zona DNS privada no Azure, ela não é acessível imediatamente de qualquer rede virtual. Você deve vinculá-lo a uma rede virtual antes que uma VM hospedada na rede possa acessar a zona DNS privada.
Para vincular uma zona DNS privada a uma rede virtual, você deve criar um link de rede virtual na zona DNS privada. Cada zona DNS privada tem uma coleção de recursos filho de link de rede virtual. Cada um desses recursos representa uma conexão com uma rede virtual.

Você pode vincular uma rede virtual a uma zona DNS privada como uma rede virtual de registro ou como uma rede virtual de resolução.

## <a name="registration-virtual-network"></a>Rede virtual de registro

Ao [criar um link](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) entre uma zona DNS privada e uma rede virtual, você tem a opção de ativar o [registro](./private-dns-autoregistration.md) de registros DNS para máquinas virtuais. Se você escolher essa opção, a rede virtual se tornará uma rede virtual de registro para a zona DNS privada. Um registro DNS é criado automaticamente para as máquinas virtuais que você implanta na rede. Os registros DNS são criados para as máquinas virtuais que você já implantou na rede virtual. Da perspectiva da rede virtual, a zona DNS privada se torna a zona de registro para essa rede virtual.
Uma zona DNS privada pode ter várias redes virtuais de registro; no entanto, cada rede virtual pode ter exatamente uma zona de registro associada a ela.

## <a name="resolution-virtual-network"></a>Resolução de rede virtual

Quando você cria um link de rede virtual em uma zona DNS privada e opta por não habilitar o registro de DNS, a rede virtual é tratada como uma rede virtual somente de resolução. Os registros DNS para máquinas virtuais implantadas em tais redes não serão criados automaticamente na zona DNS privada vinculada. No entanto, as máquinas virtuais implantadas em uma rede desse tipo podem consultar com êxito os registros DNS da zona DNS privada. Esses registros podem ser criados manualmente por você ou podem ser preenchidos de outras redes virtuais que foram vinculadas como redes de registro com a zona DNS privada.
Uma zona DNS privada pode ter várias redes virtuais de resolução e uma rede virtual pode ter várias zonas de resolução associadas a ela.

## <a name="limits"></a>Limites

Para entender quantas redes de registro e de resolução, você pode vincular a zonas DNS privadas, ver [limites de DNS do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="other-considerations"></a>Outras considerações

* Não há suporte para redes virtuais implantadas usando o modelo de implantação clássico.

* Você pode criar apenas um link entre uma zona DNS privada e uma rede virtual.

* Cada link de rede virtual em uma zona DNS privada deve ter um nome exclusivo dentro do contexto da zona DNS privada. Você pode ter links com o mesmo nome em diferentes zonas DNS privadas.

* Depois de criar um link de rede virtual, verifique o campo "status do link" do recurso de link de rede virtual. Dependendo do tamanho da rede virtual, pode levar alguns minutos antes da operação do link e o status do link mudar para *concluído*.

* Quando você exclui uma rede virtual, todos os links de rede virtual e os registros DNS registrados automaticamente associados a ele em diferentes zonas DNS privadas são excluídos automaticamente.

## <a name="next-steps"></a>Passos seguintes

* Saiba como vincular uma rede virtual a uma zona DNS privada usando [portal do Azure](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network)

* Saiba como criar uma zona privada no DNS do Azure usando [Azure PowerShell](./private-dns-getstarted-powershell.md) ou [CLI do Azure](./private-dns-getstarted-cli.md).

* Leia sobre alguns cenários comuns de [zona privada](./private-dns-scenarios.md) que podem ser percebidos com zonas privadas no DNS do Azure.

* Para perguntas e respostas comuns sobre zonas privadas no DNS do Azure, incluindo um comportamento específico que você pode esperar para determinados tipos de operações, consulte [DNS privado perguntas frequentes](./dns-faq-private.md).
