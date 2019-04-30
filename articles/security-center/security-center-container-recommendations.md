---
title: Recomendações de contentor no Centro de segurança do Azure | Documentos da Microsoft
description: Este documento explica as recomendações do Centro de segurança do Azure para saber como ajudar a proteger os seus contentores.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 2e76c7f7-a3dd-4d9f-add9-7e0e10e9324d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: rkarlin
ms.openlocfilehash: 782c769bc7825dc9b6bd3ba3b8e36885bf150eaa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60705300"
---
# <a name="understand-azure-security-center-container-recommendations"></a>Compreender as recomendações do Centro de segurança do Azure container

Ao migrar as suas aplicações de monólito para executar de missão crítica, aplicações nativas da cloud em contentores em produção, pode tirar partido das funcionalidades de contentores, incluindo as atualizações e Implantações simples e rápida. Como o número de contentores implementados continua a aumentar, soluções de segurança tem de estar no local para fornecer-lhe visibilidade para o estado de segurança dos seus contentores e ajudar a protegê-los contra ameaças.

Centro de segurança do Azure fornece as seguintes capacidades para o ajudar a proteger os seus contentores:

- **Visibilidade sobre contentores alojados no IaaS, máquinas do Linux**<br>No Centro de segurança do Azure, no separador de contentores Apresenta todas as máquinas virtuais implementadas com o Docker. Ao explorar os problemas de segurança numa máquina virtual, o Centro de segurança fornece informações adicionais relacionadas com os contentores na máquina, por exemplo, a versão do Docker e o número de imagens em execução no anfitrião.

    ![separador de contentor](./media/security-center-container-recommendations/docker-recommendation.png)


- **Recomendações de segurança com base no parâmetro de comparação de itens de configuração para o Docker**<br>Centro de segurança analisa as configurações do Docker e permite-lhe ver configurações incorretas ao fornecer uma lista de todas as regras com falhas que foram analisadas. Centro de segurança fornece diretrizes para ajudar a resolver rapidamente estes problemas e poupar tempo. Centro de segurança avalia as configurações de Docker e fornece-lhe o estado mais recente continuamente.

    ![separador de contentor](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **Deteção de ameaças de contentor em tempo real**<br> Centro de segurança fornece deteção de ameaças em tempo real para os seus contentores em máquinas do Linux com o componente de AuditD. Os alertas identificam várias atividades suspeitas do Docker como, por exemplo, a criação de um contentor com privilégios no anfitrião, uma indicação de servidor de Secure Shell (SSH) em execução dentro de um contentor de Docker ou o uso dos extratores de criptografia. Pode usar essas informações para resolver problemas de segurança e aumente a segurança dos seus contentores rapidamente.

    ![separador de contentor](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>Recomendações
Utilize as tabelas abaixo como referência para ajudar a compreender os contentores disponíveis alojados em máquinas do IaaS Linux e a avaliação de segurança de suas configurações de Docker.

| Recomendação | Descrição | Remediação |
| --- | --- | --- |
|Remediar vulnerabilidades nas configurações de segurança do contentor |Remediar vulnerabilidades em configurações de segurança do contentor com base nas melhores práticas de configuração.| Remediar vulnerabilidades nas configurações de segurança do contentor:<br>1. Reveja a lista de regras com falhas.<br>2. Corrigi cada regra de acordo com as instruções especificadas.|


## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte o seguinte:

* [Monitor identity and access in Azure Security Center](security-center-identity-access.md) (Monitorizar a identidade e o acesso no Centro de Segurança do Azure)
* [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)
* [Proteger o seu serviço do SQL do Azure no Centro de segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Proteger as máquinas e aplicações no Centro de Segurança do Azure](security-center-virtual-machine-protection.md)
* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.

