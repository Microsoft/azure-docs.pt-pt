---
title: Versão de atualização de SO no Centro de segurança do Azure | Documentos da Microsoft
description: Este artigo mostra-lhe como implementar a recomendação do Centro de segurança do Azure **atualizar versão do SO**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: aa372492-ecdb-4368-8fdd-d8ed31e216ee
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: a17bc3cf8c8f1ffbb2a91e17944bfc1c203f439d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60905857"
---
# <a name="update-os-version-in-azure-security-center"></a>Atualizar a versão de SO no Centro de segurança do Azure
Para máquinas virtuais (VMs) nos serviços cloud, o Centro de segurança do Azure irá recomendar que o sistema operativo (SO) ser atualizada se existir uma versão mais recente disponível.  Funções web e de trabalho em execução na produção são monitorizadas ranhuras de serviços de cloud única.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Não se trata de um guia passo-a-passo.
> 
> 

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Na **recomendações** painel, selecione **atualizar versão do SO**.
   ![Atualizar a versão do SO][1]
2. Esta ação abre o painel **atualizar versão do SO**. Siga os passos neste painel para atualizar a versão de SO.

## <a name="see-also"></a>Consulte também
Este artigo mostrou como implementar a recomendação do Centro de segurança "Versão de atualização de SO". Para saber mais sobre os serviços cloud e a atualizar a versão do SO para um serviço em nuvem, consulte:

* [Descrição geral dos Serviços Cloud](../cloud-services/cloud-services-choose-me.md)
* [Como atualizar um serviço em nuvem](../cloud-services/cloud-services-update-azure-service.md)
* [Como configurar um Serviços Cloud](../cloud-services/cloud-services-how-to-configure-portal.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como o recomendações o ajudam a proteger os seus recursos do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – obtenha as mais recentes notícias de segurança do Azure e informações.

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png
