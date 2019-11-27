---
title: Nenhum grupo de conectores de trabalho encontrado para um aplicativo de proxy de aplicativo
description: Problemas de endereço que você pode encontrar quando não há nenhum conector de trabalho em um grupo de conectores para seu aplicativo com o Proxy de Aplicativo do AD do Azure
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275626"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Nenhum grupo de conectores de trabalho encontrado para um aplicativo de proxy de aplicativo

Este artigo ajuda a resolver os problemas comuns enfrentados quando não há um conector detectado para um aplicativo de proxy de aplicativo integrado ao Azure Active Directory.

## <a name="overview-of-steps"></a>Visão geral das etapas
Se não houver nenhum conector de trabalho em um grupo de conectores para seu aplicativo, há algumas maneiras de resolver o problema:

-   Se você não tiver conectores no grupo, poderá:

    -   Baixar um novo conector à direita no servidor local e atribuí-lo a este grupo

    -   Mover um conector ativo para o grupo

-   Se não houver nenhum conector ativo no grupo, você poderá:

    -   Identificar o motivo pelo qual o conector está inativo e resolver

    -   Mover um conector ativo para o grupo

Para descobrir o problema, abra o menu "proxy de aplicativo" em seu aplicativo e examine a mensagem de aviso do grupo de conectores. Se não houver conectores no grupo, a mensagem de aviso especificará que o grupo precisa de pelo menos um conector. Se você não tiver conectores ativos, a mensagem de aviso explicará que. É comum ter conectores inativos. 

   ![Seleção de grupo de conectores no portal do Azure](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Para obter detalhes sobre cada uma dessas opções, consulte a seção correspondente abaixo. As instruções pressupõem que você está iniciando na página de gerenciamento de conector. Se você estiver olhando a mensagem de erro acima, poderá ir para essa página clicando na mensagem de aviso. Você também pode acessar a página acessando **Azure Active Directory**, clicando em **aplicativos empresariais**e proxy de **aplicativo.**

   ![Gerenciamento de grupo de conectores no portal do Azure](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Baixar um novo conector

Para baixar um novo conector, use o botão "baixar conector" na parte superior da página.

Instale o conector em um computador com a linha de visão direta para o aplicativo de back-end. Normalmente, o conector é instalado no mesmo servidor que o aplicativo. Após o download, o conector deve aparecer nesse menu. Clique no conector e use a lista suspensa "grupo de conectores" para verificar se ele pertence ao grupo correto. Salve a alteração.

   ![Baixe o conector do portal do Azure](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Mover um conector ativo

Se você tiver um conector ativo que deve pertencer ao grupo e tiver a linha de visão para o aplicativo de back-end de destino, você poderá mover o conector para o grupo atribuído. Para tal, clique o conector. No campo "grupo de conectores", use a lista suspensa para selecionar o grupo correto e clique em salvar.

## <a name="resolve-an-inactive-connector"></a>Resolver um conector inativo

Se os únicos conectores no grupo estiverem inativos, eles provavelmente estarão em um computador que não tenha todas as portas necessárias desbloqueadas.

consulte o documento de solução de problemas de portas para obter detalhes sobre como investigar esse problema.

## <a name="next-steps"></a>Passos seguintes
[Entender os conectores de Proxy de Aplicativo do AD do Azure](application-proxy-connectors.md)


