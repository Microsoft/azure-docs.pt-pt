---
title: Medições reais do utilizador com páginas web - Azure Traffic Manager
description: Neste artigo, aprenda a configurar as suas páginas web para enviar Medições reais do utilizador ao Gestor de Tráfego Azure.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: kumud
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/06/2021
ms.author: duau
ms.openlocfilehash: 7ec91945c901f46d7036e8c474f9f5f0714ce65e
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580354"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Como enviar medições reais do utilizador para O Gestor de Tráfego Azure usando páginas web

Pode configurar as suas páginas web para enviar Medições reais do utilizador para o Gestor de Tráfego, obtendo uma chave real de medições do utilizador (RUM) e incorporando o código gerado na página web.

## <a name="obtain-a-real-user-measurements-key"></a>Obtenha uma chave de medição real do utilizador

As medições que faz e envia para o Gestor de Tráfego a partir da sua aplicação de cliente é identificada pelo serviço utilizando uma corda única, chamada Chave de Medidas reais do **Utilizador (RUM).** Você pode obter uma chave RUM usando o portal Azure, um REST API, PowerShell ou Azure CLI.

Para obter a chave RUM utilizando o portal Azure:
1. Num browser, inicie sessão no portal do Azure. Se ainda não tiver uma conta, pode inscrever-se para obter uma avaliação gratuita durante um mês.

1. Na barra de pesquisa do portal, procure o nome do perfil do Gestor de Tráfego que pretende modificar e, em seguida, selecione o perfil de Gestor de Tráfego nos resultados que o apresentado.

1. Na página de perfil do Gestor de Tráfego, selecione **As Medições reais do utilizador** em **Definições**.

1. **Selecione 'Gerar Tecla'** para criar uma nova chave RUM.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/generate-rum-key.png" alt-text="Screenshot de como gerar chave."::: 

   **Figura 1: Geração de chaves de medição real do utilizador**

1. A página apresenta agora a chave RUM gerada e um corte de código JavaScript que precisa de ser incorporado na sua página HTML.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png" alt-text="Screenshot do código JavaScript gerado."::: 

    **Figura 2: Chave de medição do utilizador real e medição JavaScript**
 
1. Selecione o botão **Copiar** para copiar o código JavaScript. 

> [!IMPORTANT]
> Utilize a função JavaScript gerada para medições reais do utilizador para funcionar corretamente. Quaisquer alterações a este script ou aos scripts utilizados pelas Medições Reais do Utilizador podem levar a comportamentos imprevisíveis.

## <a name="embed-the-code-to-an-html-web-page"></a>Incorporar o código numa página web HTML

Depois de ter obtido a tecla RUM, o próximo passo é incorporar este JavaScript copiado numa página HTML que os seus utilizadores finais visitam. A edição de HTML pode ser feita de muitas maneiras e utilizando diferentes ferramentas e fluxos de trabalho. Este exemplo mostra como atualizar uma página HTML para adicionar este script. Pode utilizar esta orientação para adaptá-la ao seu fluxo de trabalho de gestão de fonte html.

1. Abra a página HTML num editor de texto.

1. Cole o código JavaScript que copiou na última secção na secção BODY do HTML. O código copiado está na linha 8 & 9, ver figura 3.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png" alt-text="Screenshot do JavaScript gerado incorporado na página web."::: 

    **Figura 3: HTML simples com medidas reais incorporadas do utilizador JavaScript**

1. Guarde o ficheiro HTML e hospede-o num webserver ligado à internet.

1. Da próxima vez que esta página renderizar num navegador web, o JavaScript referenciado é descarregado e o script executará as operações de medição e reporte.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [as Medições Reais do Utilizador](traffic-manager-rum-overview.md)
- Saiba [como funciona o Gestor de Tráfego](traffic-manager-overview.md)
- Saiba mais sobre os [métodos de encaminhamento de tráfego suportados](traffic-manager-routing-methods.md) pelo Traffic Manager
- Saiba como [criar um perfil de Gestor de Tráfego](./quickstart-create-traffic-manager-profile.md)
