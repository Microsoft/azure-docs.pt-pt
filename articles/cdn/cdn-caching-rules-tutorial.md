---
title: Tutorial – Definir regras de colocação em cache da CDN do Azure | Microsoft Docs
description: Neste tutorial, irá definir uma regra de colocação em cache global do CDN do Azure e uma regra personalizada de colocação em cache.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/20/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 3d52b9a6842c13e9141fd081e10156e3866c163f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81254313"
---
# <a name="tutorial-set-azure-cdn-caching-rules"></a>Tutorial: Definir regras de colocação em cache da CDN do Azure

> [!NOTE] 
> As regras de caching estão disponíveis apenas para **Azure CDN Standard da Verizon** e **Azure CDN Standard a partir de** perfis Akamai. Para **o Azure CDN a partir dos** perfis da Microsoft, tem de utilizar o motor de [regras Standard](cdn-standard-rules-engine-reference.md) Para **Azure CDN Premium a partir de** perfis Verizon, deve utilizar o motor de [regras Verizon Premium](cdn-rules-engine.md) no portal **Manage** para obter funcionalidades semelhantes.
 

Este tutorial descreve como pode utilizar regras de colocação em cache da Rede de Entrega de Conteúdos (CDN) do Azure para definir ou modificar o comportamento predefinido de expiração da cache globalmente e com condições personalizadas, tais como um caminho de URL e uma extensão de ficheiro. A CDN do Azure dispõe de dois tipos de regras de colocação em cache:
- Regras globais de colocação em cache: pode definir uma regra global de colocação em cache para cada ponto final no seu perfil, o que afeta todos os pedidos para o ponto final. A regra global de colocação em cache substitui quaisquer cabeçalhos de diretivas de cache HTTP, se for definido algum.

- Regras personalizadas de colocação em cache: pode configurar uma ou mais regras personalizadas de colocação em cache para cada ponto final no seu perfil. As regras personalizadas de colocação em cache correspondem a extensões de ficheiro e a caminhos específicos, são processadas por ordem e substituem a regra global de colocação em cache, se definida. 

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> - Abrir a página de regras de colocação em cache.
> - Criar uma regra global de colocação em cache.
> - Criar uma regra personalizada de colocação em cache.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de concluir os passos neste tutorial, tem primeiro de criar um perfil da CDN e, pelo menos, um ponto final da CDN. Para obter mais informações, veja [Início Rápido: Criar um perfil e um ponto final da CDN do Azure](cdn-create-new-endpoint.md).

## <a name="open-the-azure-cdn-caching-rules-page"></a>Abrir a página de regras de colocação em cache da CDN do Azure

1. No [portal do Azure](https://portal.azure.com), selecione um perfil de CDN e, em seguida, selecione um ponto final.

2. No painel esquerdo, em Definições, selecione **Regras de colocação em cache**.

   ![Botão Regras de colocação em cache da CDN](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   É apresentada a página **Regras de colocação em cache**.

   ![Página Regras de colocação em cache da CDN](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="set-global-caching-rules"></a>Definir regras globais de colocação em cache

Crie uma regra global de colocação em cache da seguinte forma:

1. Em **Regras globais de colocação em cache**, defina o **Comportamento de colocação em cache de cadeias de consulta** para **Ignorar cadeias de consulta**.

2. Defina o **Comportamento de colocação em cache** para **Definir se em falta**.
       
3. Para a **Duração de expiração da cache**, introduza 10 no campo **Dias**.

    A regra global de colocação em cache afeta todos os pedidos para o ponto final. Esta regra respeita os cabeçalhos de diretivas de cache de origem, caso existam (`Cache-Control` ou `Expires`). Caso contrário, se não forem especificados, define a cache para 10 dias. 

    ![Regras globais de colocação em cache](./media/cdn-caching-rules/cdn-global-caching-rules.png)

## <a name="set-custom-caching-rules"></a>Definir regras personalizadas de colocação em cache

Crie uma regra personalizada de colocação em cache da seguinte forma:

1. Em **Regras personalizadas de colocação em cache**, defina a **Condição de correspondência** para **Caminho** e o **Valor de correspondência** para `/images/*.jpg`.
    
2. Defina o **Comportamento de colocação em cache** para **Substituir** e introduza 30 no campo **Dias**.
       
    Esta regra personalizada de colocação em cache define uma duração de cache de 30 dias em quaisquer ficheiros de imagem `.jpg` na pasta `/images` do seu ponto final. Substitui quaisquer cabeçalhos HTTP `Cache-Control` ou `Expires` enviados pelo servidor de origem.

    ![Regras personalizadas de colocação em cache](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

    
## <a name="clean-up-resources"></a>Limpar os recursos

Nos passos anteriores, criou regras de colocação em cache. Se já não quiser utilizar estas regras de colocação em cache, pode removê-las ao seguir os seguintes passos:
 
1. Selecione um perfil de CDN e, em seguida, selecione o ponto final com as regras de colocação em cache que pretende remover.

2. No painel esquerdo, em Definições, selecione **Regras de colocação em cache**.

3. Em **Regras globais de colocação em cache**, defina o **Comportamento de colocação em cache** para **Não definido**.
 
4. Em **Regras personalizadas de colocação em cache**, selecione a caixa de verificação junto à regra que pretende eliminar.

5. Selecione **Eliminar**.

6. Na parte superior da página, selecione **Guardar**.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> - Abrir a página de regras de colocação em cache.
> - Criar uma regra global de colocação em cache.
> - Criar uma regra personalizada de colocação em cache.

Avance para o artigo seguinte para saber como configurar definições de regras de colocação em cache adicionais.

> [!div class="nextstepaction"]
> [Controlar o comportamento da colocação em cache da CDN do Azure com as regras de colocação em cache](cdn-caching-rules.md)



