---
title: Recomendações de contêiner na central de segurança do Azure | Microsoft Docs
description: Este documento explica as recomendações da central de segurança do Azure sobre como ajudar a proteger seus contêineres.
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
ms.openlocfilehash: 96efe5271b91f87e30ba26aabe69a0de76e1a791
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202388"
---
# <a name="understand-azure-security-center-container-recommendations"></a>Entender as recomendações de contêiner da central de segurança do Azure

Ao migrar seus aplicativos monolíticos para executar aplicativos nativos de nuvem em contêineres de missão crítica em produção, você pode aproveitar os recursos dos contêineres, incluindo implantação e atualização fáceis e rápidas. À medida que o número de contêineres implantados continua aumentando, as soluções de segurança precisam estar em vigor para que você tenha visibilidade do estado de segurança de seus contêineres e ajude a protegê-los contra ameaças.

A central de segurança do Azure fornece os seguintes recursos para ajudá-lo a proteger seus contêineres:

- **Visibilidade em contêineres hospedados em computadores Linux IaaS**<br>Na central de segurança do Azure, a guia contêineres exibe todas as máquinas virtuais implantadas com o Docker. Ao explorar os problemas de segurança em uma máquina virtual, a central de segurança fornece informações adicionais relacionadas aos contêineres no computador, como a versão do Docker e o número de imagens em execução no host.

    ![guia contêiner](./media/security-center-container-recommendations/docker-recommendation.png)


- **Recomendações de segurança com base no parâmetro de comparação de CIS para o Docker**<br>O Centro de Segurança analisa as suas configurações do Docker e fornece-lhe visibilidade sobre as configurações incorretas ao disponibilizar uma lista de todas as regras com falhas que foram analisadas. A central de segurança fornece diretrizes para ajudá-lo a resolver esses problemas rapidamente e economizar tempo. O Centro de Segurança avalia continuamente as configurações do Docker e fornece o estado mais recente.

    ![guia contêiner](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **Detecção de ameaças de contêiner em tempo real**<br> A central de segurança fornece detecção de ameaças em tempo real para seus contêineres em computadores Linux com componente auditado. Os alertas identificam várias atividades suspeitas do Docker, como a criação de um contêiner com privilégios no host, uma indicação do servidor Secure Shell (SSH) em execução dentro de um contêiner do Docker ou o uso de crypto mineradores. Pode utilizar estas informações para remediar rapidamente problemas de segurança e aumentar a segurança dos contentores.

    ![guia contêiner](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>Recomendações
Use as tabelas abaixo como referência para ajudá-lo a entender os contêineres disponíveis hospedados em máquinas Linux IaaS e avaliação de segurança de suas configurações do Docker.

| Recomendação | Descrição | Remediação |
| --- | --- | --- |
|Corrigir vulnerabilidades em configurações de segurança de contêiner |Corrigir vulnerabilidades em configurações de segurança de contêiner com base nas práticas recomendadas de configuração.| Para corrigir as vulnerabilidades nas configurações de segurança do contêiner:<br>1. Examine a lista de regras com falha.<br>2. Corrija cada regra de acordo com as instruções especificadas.|


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte o seguinte:

* [Monitorizar a identidade e acesso no Centro de segurança do Azure](security-center-identity-access.md)
* [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)
* [Proteger o seu serviço do SQL do Azure no Centro de segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Proteger as máquinas e aplicações no Centro de Segurança do Azure](security-center-virtual-machine-protection.md)
* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.

