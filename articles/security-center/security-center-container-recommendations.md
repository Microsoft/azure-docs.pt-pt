---
title: Recomendações de contentores no Centro de Segurança Azure Microsoft Docs
description: Este documento explica as recomendações do Azure Security Center sobre como ajudar a proteger os seus contentores.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2e76c7f7-a3dd-4d9f-add9-7e0e10e9324d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: memildin
ms.openlocfilehash: b8b8b05f703a3eb05936ca95e2047a13650914cf
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604259"
---
# <a name="understand-azure-security-center-container-recommendations"></a>Compreender as recomendações do centro de segurança Azure

À medida que migra as suas aplicações monólitos para executar aplicações nativas em nuvem em produção, pode aproveitar as características dos contentores, incluindo fácil e rápida implantação e atualização. À medida que o número de contentores implantados continua a aumentar, as soluções de segurança precisam de estar em vigor para lhe proporcionar visibilidade para o estado de segurança dos seus contentores e ajudar a protegê-los de ameaças.

O Azure Security Center fornece as seguintes capacidades para o ajudar a proteger os seus contentores:

- **Visibilidade em recipientes alojados em máquinas IaaS Linux**<br>No Azure Security Center, o separador Contentores exibe todas as máquinas virtuais implantadas com o Docker. Ao explorar os problemas de segurança numa máquina virtual, o Security Center fornece informações adicionais relacionadas com os contentores da máquina, como a versão Docker e o número de imagens em execução no hospedeiro.

    ![separador de recipiente](./media/security-center-container-recommendations/docker-recommendation.png)


- **Recomendações de segurança baseadas no referencial do CIS para Docker**<br>O Centro de Segurança analisa as suas configurações do Docker e fornece-lhe visibilidade sobre as configurações incorretas ao disponibilizar uma lista de todas as regras com falhas que foram analisadas. O Centro de Segurança fornece diretrizes para ajudá-lo a resolver estes problemas rapidamente e economizar tempo. O Centro de Segurança avalia continuamente as configurações do Docker e fornece o estado mais recente.

    ![separador de recipiente](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **Deteção de ameaças de contentores em tempo real**<br> O Security Center fornece deteção de ameaças em tempo real para os seus contentores em máquinas Linux com componente AuditD. Os alertas identificam várias atividades suspeitas do Docker, como a criação de um contentor privilegiado no hospedeiro, uma indicação do servidor Secure Shell (SSH) que corre dentro de um contentor do Docker, ou o uso de mineiros cripto. Pode utilizar estas informações para remediar rapidamente problemas de segurança e aumentar a segurança dos contentores.

    ![separador de recipiente](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>Recomendações
Utilize as tabelas abaixo como referência para ajudá-lo a entender os recipientes disponíveis alojados nas máquinas IaaS Linux e a avaliação de segurança das suas configurações do Docker.

| Recomendação | Descrição | Remediação |
| --- | --- | --- |
|Remediar vulnerabilidades em configurações de segurança de contentores |Remediar vulnerabilidades em configurações de segurança de contentores com base nas melhores práticas de configuração.| Para remediar vulnerabilidades nas configurações de segurança do contentor:<br>1. Rever a lista de regras falhadas.<br>2. Fixar cada regra de acordo com as instruções especificadas.|


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte o seguinte:

* [Monitor identity and access in Azure Security Center](security-center-identity-access.md) (Monitorizar a identidade e o acesso no Centro de Segurança do Azure)
* [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)
* [Proteger o seu serviço Azure SQL no Azure Security Center](security-center-sql-service-recommendations.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Proteger as máquinas e aplicações no Centro de Segurança do Azure](security-center-virtual-machine-protection.md)
* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.