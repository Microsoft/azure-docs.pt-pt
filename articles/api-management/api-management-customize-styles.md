---
title: Personalizar o estilo de página no portal do desenvolvedor herdado do gerenciamento de API do Azure | Microsoft Docs
description: Siga os passos neste início rápido para personalizar o estilo dos elementos no portal de programador da Gestão de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 779d1228393cdc88f88820d8a335cb2e32424c14
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176857"
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>Personalizar o estilo das páginas do portal do desenvolvedor

Há três maneiras mais comuns de personalizar o portal do desenvolvedor no gerenciamento de API do Azure:
 
* [Editar o conteúdo de páginas estáticas e elementos de esquema da página](api-management-modify-content-layout.md)
* Atualizar os estilos utilizados para elementos de página em todo o portal de programador (explicado neste guia)
* [Modificar os modelos utilizados para páginas geradas pelo portal](api-management-developer-portal-templates.md)(por exemplo, documentos de API, produtos, autenticação de utilizador)

Neste artigo, você aprenderá a personalizar o estilo de elementos em páginas do portal do **desenvolvedor** herdado e exibir suas alterações.

![personalizar estilo](./media/modify-developer-portal-style/developer_portal.png)

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Pré-requisitos

+ Conhecer a [terminologia da Gestão de API do Azure](api-management-terminology.md).
+ Conclua o guia de início rápido seguinte: [Criar uma instância da Gestão de API do Azure](get-started-create-service-instance.md).
+ Conclua também o tutorial seguinte: [Importar e publicar a sua primeira API](import-and-publish.md).

## <a name="customize-the-developer-portal"></a>Personalizar o portal do programador

1. Selecione **Descrição geral**.
2. Clique no botão **portal do desenvolvedor (Herdado)** na parte superior da janela **visão geral** .
3. No canto superior esquerdo do ecrã, verá um ícone com dois pincéis. Coloque o cursor sobre este ícone para abrir o menu de personalização do portal.

    ![personalizar estilo](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. Selecione **Estilos** no menu para abrir o painel de personalização de estilos.

    Todos os elementos que pode personalizar com **Estilos** aparecem na página
5. Introduza "headings-color" no campo **Alterar valores de variáveis para personalizar o aspeto do portal do programador:** .

    O elemento **\@Headers-Color** aparece na página. Esta variável controla a cor do texto.

    ![personalizar estilo](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. Clique no campo para a variável **\@títulos-cor** . 
    
    É aberto o menu pendente do selecionador de cores.
7. No menu pendente do selecionador de cores, selecione uma nova cor.

    > [!TIP]
    > A pré-visualização em tempo real está disponível para todas as alterações. Aparece um indicador de progresso na parte superior do painel de personalização. Após alguns segundos, o texto do cabeçalho muda de cor para a nova selecionada.

8. Selecione **Publicar** na parte superior esquerda do menu do painel de personalização.
9. Selecione **Publicar personalizações** para tornar as alterações publicamente disponíveis.

## <a name="view-your-change"></a>Ver a sua alteração

1. Navegue até o portal do desenvolvedor.
2. Pode ver a alteração que efetuou.

## <a name="next-steps"></a>Passos seguintes

Poderá também estar interessado em aprender [como personalizar o portal do programador de Gestão de API do Azure através de modelos](api-management-developer-portal-templates.md).