---
title: Medidas de Usuário Reais com páginas da Web-Gerenciador de tráfego do Azure
description: Neste artigo, saiba como configurar suas páginas da Web para enviar Medidas de Usuário Reais para o Gerenciador de tráfego do Azure.
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 6e3cf5af5aaa894b1595d67c0056073a458b0a88
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040305"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Como enviar Medidas de Usuário Reais para o Gerenciador de tráfego do Azure usando páginas da Web

Você pode configurar suas páginas da Web para enviar Medidas de Usuário Reais ao Gerenciador de tráfego, obtendo uma chave Medidas de Usuário Reais (RUM) e inserindo o código gerado na página da Web.

## <a name="obtain-a-real-user-measurements-key"></a>Obter uma chave de Medidas de Usuário Reais

As medidas que você usa e envia para o Gerenciador de tráfego do aplicativo cliente são identificadas pelo serviço usando uma cadeia de caracteres exclusiva, chamada de **chave de medidas de usuário reais (rum)** . Você pode obter uma chave RUM usando o portal do Azure, uma API REST ou o PowerShell ou o CLI do Azure.

Para obter a chave RUM usando portal do Azure:
1. Em um navegador, entre no portal do Azure. Se você ainda não tiver uma conta, poderá se inscrever para uma avaliação gratuita de um mês.
2. Na barra de pesquisa do portal, pesquise o nome do perfil do Gerenciador de tráfego que você deseja modificar e, em seguida, clique no perfil do Gerenciador de tráfego nos resultados exibidos.
3. Na folha perfil do Gerenciador de tráfego, clique em **medidas de usuário reais** em **configurações**.
4. Clique em **gerar chave** para criar uma nova chave rum.
 
   ![Gerar chave de Medidas de Usuário Reais](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Figura 1: geração de chave de Medidas de Usuário Reais**

5. A folha agora exibe a chave RUM gerada e um trecho de código JavaScript que precisa ser inserido em sua página HTML.
 
    ![Código JavaScript para chave de Medidas de Usuário Reais](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **Figura 2: Medidas de Usuário Reais o JavaScript de medida e de chave**
 
6. Clique no botão **copiar** para copiar o código JavaScript. 

>[!IMPORTANT]
> Use o JavaScript gerado para Medidas de Usuário Reais recurso para funcionar corretamente. Qualquer alteração nesse script ou nos scripts usados pelo Medidas de Usuário Reais pode levar a um comportamento imprevisível.

## <a name="embed-the-code-to-an-html-web-page"></a>Inserir o código em uma página da Web HTML

Depois de obter a chave RUM, a próxima etapa é incorporar esse JavaScript copiado em uma página HTML que os usuários finais visitam. A edição de HTML pode ser feita de várias maneiras e usando diferentes ferramentas e fluxos de trabalho. Este exemplo mostra como atualizar uma página HTML para adicionar esse script. Você pode usar essas diretrizes para adaptá-lo ao seu fluxo de trabalho de gerenciamento de origem HTML.

1.  Abrir a página HTML em um editor de texto
2.  Cole o código JavaScript que você copiou na etapa anterior para a seção BODY do HTML (o código copiado está na linha 8 & 9, consulte a Figura 3).
 
    ![Inserir código JavaScript na página da Web para Medidas de Usuário Reais](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **Figura 3: HTML simples com Embedded Medidas de Usuário Reais JavaScript**

3.  Salve o arquivo HTML e hospede-o em um servidor webconectado à Internet. 
4. Na próxima vez em que esta página for renderizada em um navegador da Web, o JavaScript referenciado será baixado e o script executará as operações de medição e relatório.


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre o [medidas de usuário reais](traffic-manager-rum-overview.md)
- Saiba [como funciona o Gerenciador de tráfego](traffic-manager-overview.md)
- Saiba mais sobre os [métodos de roteamento de tráfego](traffic-manager-routing-methods.md) com suporte pelo Gerenciador de tráfego
- Saiba como [criar um perfil do Gerenciador de tráfego](traffic-manager-create-profile.md)

