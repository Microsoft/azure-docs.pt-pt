---
title: Nenhum grupo de conector de trabalho encontrado para uma aplicação de Proxy de aplicação
description: Resolver problemas que poderá encontrar quando não houver conector de funcionamento num Grupo de Conector para a sua aplicação com o Proxy de Aplicação AD AZure
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: cec028dec2bf2e6295f59aae9c2e243da2209827
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99257039"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Nenhum grupo de conector de trabalho encontrado para uma aplicação de Procuração de Aplicação

Este artigo ajuda a resolver as questões comuns enfrentadas quando não existe um conector detetado para uma aplicação Proxy de aplicação integrada com o Azure Ative Directory.

## <a name="overview-of-steps"></a>Visão geral dos passos
Se não houver um Conector funcionando num Grupo de Conector para a sua aplicação, existem algumas formas de resolver o problema:

-   Se não tiver conectores no grupo, pode:

    -   Descarregue um novo Conector à direita no servidor de instalações e atribua-o a este grupo

    -   Mover um Conector ativo para o grupo

-   Se não tiver conectores ativos no grupo, pode:

    -   Identifique a razão pela qual o seu Conector está inativo e resolve

    -   Mover um Conector ativo para o grupo

Para resolver o problema, abra o menu "Application Proxy" na sua Aplicação e olhe para a mensagem de aviso do Grupo Connector. Se não houver conectores no grupo, a mensagem de aviso especifica que o grupo necessita de pelo menos um Conector. Se não tiver Conectores ativos, a mensagem de aviso explica isso. É comum ter Conectores inativos. 

   ![Seleção do grupo de conector no portal Azure](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Para mais detalhes sobre cada uma destas opções, consulte a secção correspondente abaixo. As instruções pressupõem que está a partir da página de gestão do Conector. Se estiver a olhar para a mensagem de erro acima, pode ir a esta página clicando na mensagem de aviso. Também pode chegar à página indo ao **Azure Ative Directory,** clicando em **Aplicações empresariais,** em seguida, **Application Proxy.**

   ![Gestão do grupo de conector no portal Azure](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Descarregue um novo Conector

Para descarregar um novo Conector, utilize o botão "Transferir o Conector" no topo da página.

Instale o conector numa máquina com linha de visão direta para a aplicação backend. Normalmente, o conector é instalado no mesmo servidor que a aplicação. Após o download, o Conector deve aparecer neste menu. clique no Conector e use o drop-down "Connector Group" para se certificar de que pertence ao grupo certo. Guarde a alteração.

   ![Descarregue o conector a partir do portal Azure](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Mover um Conector Ativo

Se tiver um Conector ativo que deve pertencer ao grupo e tiver uma linha de visão para a aplicação de backend alvo, pode mover o Conector para o grupo designado. Para tal, clique no Conector. No campo "Grupo de Conector", utilize o drop-down para selecionar o grupo correto e clique em Guardar.

## <a name="resolve-an-inactive-connector"></a>Resolver um Conector inativo

Se os únicos Conectores do grupo estiverem inativos, é provável que estejam numa máquina que não tenha todas as portas necessárias desbloqueadas.

consulte o documento de resolução de problemas das portas para obter detalhes sobre a investigação deste problema.

## <a name="next-steps"></a>Passos seguintes
[Compreenda os conectores Proxy de aplicação AD Azure](application-proxy-connectors.md)


