---
title: Medidas de utilizadores reais do Azure o Gestor de tráfego com páginas da web | Documentos da Microsoft
description: Configurar suas páginas da web para enviar medições de utilizador reais para o Gestor de tráfego
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: d6b669f5baae13f9fb57c5c58060e70a8f871e17
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58117273"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Como enviar medições de utilizador reais para o Azure Traffic Manager usando páginas da web

Pode configurar suas páginas da web para enviar medições de utilizador reais para o Gestor de tráfego ao obter uma chave de medições de utilizador Real (RUM) e incorporar o código gerado para a página da web.

## <a name="obtain-a-real-user-measurements-key"></a>Obter uma chave de medições de utilizador reais

As medidas a tomar e enviar para o Gestor de tráfego a partir da sua aplicação de cliente são identificadas pelo serviço usando uma cadeia de caracteres exclusiva, chamada de **chave de medições de utilizador Real (RUM)**. Pode obter uma chave de executar com o portal do Azure, uma API REST, ou utilizando o PowerShell ou a CLI do Azure.

Para obter a chave de rum do através do portal do Azure:
1. Num browser, inicie sessão no portal do Azure. Se ainda não tiver uma conta, pode inscrever-se numa avaliação gratuita de um mês.
2. Na barra de pesquisa do portal, procure o nome de perfil do Gestor de tráfego que pretende modificar e, em seguida, clique no perfil do Gestor de tráfego nos resultados que apresentados.
3. No painel de perfil do Gestor de tráfego, clique em **medidas de utilizadores reais** sob **definições**.
4. Clique em **gerar chave** para criar uma nova chave de rum do.
 
   ![Gerar chave de medições de utilizador reais](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Figura 1: Geração de chave de medições de utilizador real**

5. O painel apresenta agora gerar a chave de rum do e um trecho de código JavaScript que tem de ser incorporados em sua página HTML.
 
    ![Código de JavaScript para a chave de medições de utilizador reais](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **Figura 2: Chave de medições de utilizador real e JavaScript de medida**
 
6. Clique nas **cópia** botão para copiar o código JavaScript. 

>[!IMPORTANT]
> Utilize o JavaScript gerado para a funcionalidade de medições de utilizador reais para funcionar corretamente. Quaisquer alterações a este script ou os scripts utilizados por medidas de utilizadores reais podem levar a um comportamento imprevisível.

## <a name="embed-the-code-to-an-html-web-page"></a>Incorporar o código para uma página da web HTML

Após obter a chave de executar, a próxima etapa é incorporar esse JavaScript copiado numa página HTML que os utilizadores finais visitar. Edição de HTML pode ser feito de várias maneiras e usando diferentes ferramentas e fluxos de trabalho. Este exemplo mostra como atualizar uma página HTML para adicionar este script. Pode usar essa orientação para adaptá-lo para seu fluxo de trabalho de gestão de origem HTML.

1.  Abra a página HTML num editor de texto
2.  Cole o código de JavaScript que tinha copiou no passo anterior para a seção de corpo do HTML (o código copiado está na linha 8 e 9, veja a figura 3).
 
    ![Incorporar o código Javascript na página da web para medidas de utilizadores reais](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **Figura 3: HTML Simple com o embedded JavaScript de medições de utilizador Real**

3.  Salve o arquivo HTML e o host-lo num servidor Web conectado à internet. 
4. Próxima vez que esta página é renderizada no navegador da web, o JavaScript referenciado é transferido e o script será executado a medição e operações de relatórios.


## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [medidas de utilizadores reais](traffic-manager-rum-overview.md)
- Saiba [como funciona o Gestor de tráfego](traffic-manager-overview.md)
- Saiba mais sobre o [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md) suportada pelo Gestor de tráfego
- Saiba como [criar um perfil do Gestor de tráfego](traffic-manager-create-profile.md)

