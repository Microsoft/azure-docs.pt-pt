---
title: Foi encontrado nenhum grupo do conector de trabalho para uma aplicação de Proxy de aplicações | Documentos da Microsoft
description: Resolver problemas que poderão surgir quando não existe nenhum trabalho conector num grupo de conectores para a sua aplicação com o Proxy de aplicações do Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96608853d2eec3cf90f1425992d755cbe6c2e8dd
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57437909"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Nenhum grupo de conectores de trabalho encontrado para uma aplicação de Proxy de aplicações

Este artigo ajuda a resolver os problemas comuns enfrentados quando não existe um conector detectado para um aplicativo de Proxy de aplicações integrado no Azure Active Directory.

## <a name="overview-of-steps"></a>Descrição geral dos passos
Se não houver nenhum trabalho conector num grupo de conectores para a sua aplicação, existem algumas formas de resolver o problema:

-   Se tiver não existem conectores no grupo, pode:

    -   Transferir um novo conector à direita server no local e atribuí-lo a este grupo

    -   Mova um conector do Active Directory para o grupo

-   Se tiver não existem conectores ativos no grupo, pode:

    -   Identificar o motivo pelo qual que o conector está inativo e resolver

    -   Mova um conector do Active Directory para o grupo

Para descobrir o problema, abra o menu de "Proxy da aplicação" em seu aplicativo e ver a mensagem de aviso de grupo do conector. Se não existirem não existem conectores no grupo, a mensagem de aviso Especifica que o grupo tem de, pelo menos, um conector. Se tiver não existem conectores ativos, a mensagem de aviso explica que. É comum ter conectores inativos. 

   ![Seleção de grupo do conector no portal do Azure](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Para obter detalhes sobre cada uma destas opções, consulte a seção correspondente abaixo. As instruções partem do princípio de que estão começando a partir da página de gestão do conector. Se examinar a mensagem de erro acima, pode aceder a esta página ao clicar na mensagem de aviso. Também pode obter para a página acedendo a **do Azure Active Directory**, clicar em **aplicações empresariais**, em seguida, **Proxy de aplicações.**

   ![Gestão de grupo do conector no portal do Azure](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Transferir um novo conector

Para transferir um novo conector, utilize o botão "Transferir o conector" na parte superior da página.

Instale o conector num computador com a linha de visão direta para a aplicação de back-end. Normalmente, o conector é instalado no mesmo servidor que o aplicativo. Depois de transferir, o conector deverá aparecer nesse menu. o conector de clique e utilize o menu pendente "conector do grupo" para se certificar de que ele pertence ao grupo certo. Guarde a alteração.

   ![Transferir o conector do portal do Azure](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Mover um conector do Active Directory

Se tiver um conector do Active Directory deve pertencer ao grupo e com a linha Visual para a aplicação de back-end de destino, pode mover o conector para o grupo atribuído. Para tal, clique o conector. No campo do "Grupo de conectores", use a menu pendente para selecionar o grupo correto e clique em Guardar.

## <a name="resolve-an-inactive-connector"></a>Resolver um conector inativo

Se os conectores apenas no grupo estão inativos, é provável que numa máquina que não tenha todas as portas necessárias desbloqueadas.

consulte o documento de resolução de problemas de portas para obter detalhes sobre como investigar esse problema.

## <a name="next-steps"></a>Passos Seguintes
[Compreender os conectores de Proxy de aplicações do Azure AD](application-proxy-connectors.md)


