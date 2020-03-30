---
title: Nenhum grupo de conector de trabalho encontrado para uma aplicação proxy de aplicação
description: Resolver problemas que poderá encontrar quando não houver um Connector a funcionar num Grupo de Conector para a sua aplicação com o Proxy de Aplicação AD Azure
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96ab0d1b3bbab9c97c04da4f918f3aaa2f1d07e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275626"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Nenhum grupo de conector de trabalho encontrado para uma aplicação de procuração de aplicação

Este artigo ajuda a resolver as questões comuns com que se defrontam quando não é detetado um conector para uma aplicação proxy de aplicação integrada com o Diretório Ativo azure.

## <a name="overview-of-steps"></a>Visão geral dos passos
Se não houver um Connector a funcionar num Grupo de Conector para a sua aplicação, existem algumas formas de resolver o problema:

-   Se não tiver conectores no grupo, pode:

    -   Descarregue um novo Conector à direita no servidor de instalações e atribua-o a este grupo

    -   Mova um Conector ativo para o grupo

-   Se não tiver conectores ativos no grupo, pode:

    -   Identifique a razão pela qual o seu Conector está inativo e resolve

    -   Mova um Conector ativo para o grupo

Para descobrir o problema, abra o menu "Application Proxy" na sua Aplicação e veja a mensagem de aviso do Grupo de Conectores. Se não houver conectores no grupo, a mensagem de aviso especifica que o grupo necessita de pelo menos um Conector. Se não tiver conectores ativos, a mensagem de aviso explica isso. É comum ter Conectores inativos. 

   ![Seleção de grupo de conector no portal Azure](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Para mais detalhes sobre cada uma destas opções, consulte a secção correspondente abaixo. As instruções pressupõem que está a partir da página de gestão do Connector. Se estiver a olhar para a mensagem de erro acima, pode ir a esta página clicando na mensagem de aviso. Também pode chegar à página indo para **o Azure Ative Directory,** clicando em **Aplicações Empresariais,** em **seguida, Application Proxy.**

   ![Gestão do grupo connector no portal Azure](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Descarregue um novo Conector

Para descarregar um novo Conector, utilize o botão "Descarregue o Conector" na parte superior da página.

Instale o conector numa máquina com linha direta de visão para a aplicação backend. Normalmente, o conector é instalado no mesmo servidor que a aplicação. Depois de descarregar, o Conector deve aparecer neste menu. clique no Conector e utilize a queda do "Connector Group" para se certificar de que pertence ao grupo certo. Guarde a alteração.

   ![Descarregue o conector do portal Azure](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Mova um Conector Ativo

Se tiver um Conector ativo que deve pertencer ao grupo e tiver uma linha de visão para a aplicação de backend do alvo, pode mover o Conector para o grupo designado. Para isso, clique no Conector. No campo "Connector Group", utilize a gota para selecionar o grupo correto e clique em Guardar.

## <a name="resolve-an-inactive-connector"></a>Resolver um Conector inativo

Se os únicos Conectores do grupo estiverem inativos, são prováveis numa máquina que não tenha todas as portas necessárias desbloqueadas.

consulte o documento de Troubleshoot para obter detalhes sobre a investigação deste problema.

## <a name="next-steps"></a>Passos seguintes
[Compreender os conectores de procuração de aplicação da AD Azure](application-proxy-connectors.md)


