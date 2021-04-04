---
title: Medições reais do utilizador com páginas web - Azure Traffic Manager
description: Neste artigo, aprenda a configurar as suas páginas web para enviar Medições reais do utilizador ao Gestor de Tráfego Azure.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 2bb104fd9cfc3c4ffddb82e4cf442e94c4650550
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98184563"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Como enviar medições reais do utilizador para O Gestor de Tráfego Azure usando páginas web

Pode configurar as suas páginas web para enviar Medições reais do utilizador para o Gestor de Tráfego, obtendo uma chave real de medições do utilizador (RUM) e incorporando o código gerado na página web.

## <a name="obtain-a-real-user-measurements-key"></a>Obtenha uma chave de medição real do utilizador

As medições que faz e envia para o Gestor de Tráfego da sua aplicação de cliente são identificadas pelo serviço utilizando uma cadeia única, chamada Chave de Medidas reais do **Utilizador (RUM).** Pode obter uma chave RUM utilizando o portal Azure, uma API REST ou utilizando o PowerShell ou O CLI Azure.

Para obter a chave RUM utilizando o portal Azure:
1. Num browser, inicie sessão no portal do Azure. Se ainda não tiver uma conta, pode inscrever-se para obter uma avaliação gratuita durante um mês.
2. Na barra de pesquisa do portal, procure o nome do perfil do Gestor de Tráfego que pretende modificar e, em seguida, clique no perfil do Gestor de Tráfego nos resultados apresentados.
3. Na lâmina de perfil do Gestor de Tráfego, clique em **"Real User Measurements"** em **Definições**.
4. Clique **em Gerar Tecla** para criar uma nova chave RUM.
 
   ![Gerar chave de medições reais do utilizador](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Figura 1: Geração de chaves de medição real do utilizador**

5. A lâmina apresenta agora a chave RUM gerada e um corte de código JavaScript que precisa de ser incorporado na sua página HTML.
 
    ![Código Javascript para chave de medições reais do utilizador](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **Figura 2: Chave de medição do utilizador real e medição JavaScript**
 
6. Clique no botão **Copiar** para copiar o código JavaScript. 

>[!IMPORTANT]
> Utilize a função JavaScript gerada para medições reais do utilizador para funcionar corretamente. Quaisquer alterações a este script ou aos scripts utilizados pelas Medições Reais do Utilizador podem levar a comportamentos imprevisíveis.

## <a name="embed-the-code-to-an-html-web-page"></a>Incorporar o código numa página web HTML

Depois de ter obtido a tecla RUM, o próximo passo é incorporar este JavaScript copiado numa página HTML que os seus utilizadores finais visitam. A edição de HTML pode ser feita de muitas maneiras e utilizando diferentes ferramentas e fluxos de trabalho. Este exemplo mostra como atualizar uma página HTML para adicionar este script. Pode utilizar esta orientação para adaptá-la ao seu fluxo de trabalho de gestão de fonte html.

1.  Abra a página HTML num editor de texto
2.  Cole o código JavaScript que tinha copiado no passo anterior para a secção BODY do HTML (o código copiado está na linha 8 & 9, ver figura 3).
 
    ![Incorporar código Javascript na página web para medições reais do utilizador](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **Figura 3: HTML simples com medidas reais incorporadas do utilizador JavaScript**

3.  Guarde o ficheiro HTML e hospede-o num webserver ligado à internet. 
4. Da próxima vez que esta página for renderizada num navegador web, o JavaScript referenciado é descarregado e o script executará as operações de medição e reporte.


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [as Medições Reais do Utilizador](traffic-manager-rum-overview.md)
- Saiba [como funciona o Gestor de Tráfego](traffic-manager-overview.md)
- Saiba mais sobre os [métodos de encaminhamento de tráfego suportados](traffic-manager-routing-methods.md) pelo Traffic Manager
- Saiba como [criar um perfil de Gestor de Tráfego](./quickstart-create-traffic-manager-profile.md)