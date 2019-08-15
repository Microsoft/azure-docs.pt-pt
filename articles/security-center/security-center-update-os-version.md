---
title: Atualizar versão do sistema operacional na central de segurança do Azure | Microsoft Docs
description: Este artigo mostra como implementar a versão de atualização de recomendação do **sistema operacional**da central de segurança do Azure.
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
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "60905857"
---
# <a name="update-os-version-in-azure-security-center"></a>Atualizar versão do sistema operacional na central de segurança do Azure
Para VMs (máquinas virtuais) em serviços de nuvem, a central de segurança do Azure recomendará que o sistema operacional (SO) seja atualizado se houver uma versão mais recente disponível.  Somente as funções Web e de trabalho dos serviços de nuvem em execução nos slots de produção são monitoradas.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Não se trata de um guia passo-a-passo.
> 
> 

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Na folha **recomendações** , selecione **Atualizar versão do sistema operacional**.
   ![Atualizar a versão do SO][1]
2. Isso abre a **versão do sistema operacional de atualização**de folha. Siga as etapas nesta folha para atualizar a versão do sistema operacional.

## <a name="see-also"></a>Consulte também
Este artigo mostrou como implementar a recomendação da central de segurança "Atualizar versão do sistema operacional". Para saber mais sobre os serviços de nuvem e atualizar a versão do sistema operacional para um serviço de nuvem, consulte:

* [Descrição geral dos Serviços Cloud](../cloud-services/cloud-services-choose-me.md)
* [Como atualizar um serviço de nuvem](../cloud-services/cloud-services-update-azure-service.md)
* [Como configurar um Serviços Cloud](../cloud-services/cloud-services-how-to-configure-portal.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerenciando recomendações de segurança na central de segurança do Azure](security-center-recommendations.md) – saiba como as recomendações ajudam a proteger seus recursos do Azure.
* [Monitoramento de integridade de segurança na central de segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blog de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – obtenha as últimas notícias e informações de segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png
