---
title: Medições reais do utilizador com páginas web - Gestor de Tráfego Azure
description: Neste artigo, aprenda a configurar as suas páginas web para enviar Medições reais de Utilizador para o Gestor de Tráfego do Azure.
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: 927d774ee30a291607a8a47fc2fd6878c1bc6fee
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938686"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Como enviar Medições reais de utilizador para o Gestor de Tráfego Do Azure usando páginas web

Pode configurar as suas páginas web para enviar Medições reais do Utilizador ao Gestor de Tráfego, obtendo uma chave real de medição de utilizador (RUM) e incorporando o código gerado na página web.

## <a name="obtain-a-real-user-measurements-key"></a>Obtenha uma chave real de medição do utilizador

As medições que efetua e envia ao Traffic Manager a partir da sua aplicação cliente são identificadas pelo serviço utilizando uma cadeia única, chamada Chave de Medições reais **do Utilizador (RUM).** Pode obter uma chave RUM utilizando o portal Azure, uma API REST ou utilizando o PowerShell ou o Azure CLI.

Para obter a chave RUM utilizando o portal Azure:
1. A partir de um browser, inscreva-se no portal Azure. Se ainda não tem uma conta, pode inscrever-se para um julgamento gratuito de um mês.
2. Na barra de pesquisa do portal, procure o nome de perfil do Gestor de Tráfego que pretende modificar e, em seguida, clique no perfil do Gestor de Tráfego nos resultados apresentados.
3. Na lâmina de perfil do Gestor de Tráfego, clique em **Medições reais do Utilizador** em **Definições**.
4. Clique em **Criar** uma nova chave RUM.
 
   ![Gerar chave de medição real do utilizador](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Figura 1: Geração chave de medição real do utilizador**

5. A lâmina apresenta agora a Chave RUM gerada e um corte de código JavaScript que precisa de ser incorporado na sua página HTML.
 
    ![Código javascript para chave de medição real do utilizador](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **Figura 2: Chave de medição real do utilizador e javaScript de medição**
 
6. Clique no botão **Copiar** para copiar o código JavaScript. 

>[!IMPORTANT]
> Utilize a função JavaScript gerada para medições reais do utilizador para funcionar corretamente. Quaisquer alterações a este script ou aos scripts utilizados pelas Medições reais do Utilizador podem levar a comportamentos imprevisíveis.

## <a name="embed-the-code-to-an-html-web-page"></a>Incorporar o código para uma página web HTML

Depois de ter obtido a chave RUM, o próximo passo é incorporar este JavaScript copiado numa página HTML que os seus utilizadores finais visitam. Editar HTML pode ser feito de muitas maneiras e usando diferentes ferramentas e fluxos de trabalho. Este exemplo mostra como atualizar uma página HTML para adicionar este script. Pode utilizar esta orientação para adaptá-la ao fluxo de trabalho de gestão de fontes HTML.

1.  Abra a página HTML num editor de texto
2.  Colar o código JavaScript que copiou no passo anterior para a secção BODY do HTML (o código copiado está na linha 8 e 9, ver figura 3).
 
    ![Inserido código Javascript na página web para medições reais do utilizador](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **Figura 3: Html simples com medições de utilizador real incorporadas JavaScript**

3.  Guarde o ficheiro HTML e guarde-o num webserver ligado à internet. 
4. Da próxima vez que esta página for renderizada num navegador web, o JavaScript referenciado é descarregado e o script executará as operações de medição e reporte.


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [as medições reais do utilizador](traffic-manager-rum-overview.md)
- Saiba como funciona o [Gestor de Tráfego](traffic-manager-overview.md)
- Saiba mais sobre os [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md) suportados pelo Traffic Manager
- Saiba como [criar um perfil de Gestor](traffic-manager-create-profile.md) de Tráfego

