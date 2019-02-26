---
title: Criar e partilhar dashboards de portais do Azure | Documentos da Microsoft
description: Este artigo explica como criar e editar dashboards no portal do Azure.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: doubeby
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/06/2016
ms.author: kfollis
ms.openlocfilehash: 693e973fb988a57c15b4ea2fae47f16b4ff39011
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56818583"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Criar e partilhar dashboards no portal do Azure
Pode criar vários dashboards e partilhá-los com outras pessoas que têm acesso às suas subscrições do Azure.  Este artigo aborda as noções básicas de criação, edição, publicar e gerir o acesso aos dashboards.

## <a name="create-a-dashboard"></a>Criar um dashboard
Para criar um dashboard, selecione o botão **Novo dashboard** junto ao nome do dashboard atual.  

![criar dashboard](./media/azure-portal-dashboards/new-dashboard.png)

Esta ação cria um dashboard novo, vazio e privado, e coloca-o no modo de personalização onde pode nomear o seu dashboard e adicionar ou reorganizar os mosaicos.  Neste modo, a Galeria de mosaicos recolhível assume o menu de navegação à esquerda.  A Galeria de mosaicos permite-lhe encontrar mosaicos para os seus recursos do Azure de várias formas: pode procurar ao [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups), ao tipo de recurso, por [marca](../azure-resource-manager/resource-group-using-tags.md), ou ao procurar o seu recurso por nome.  

![Personalizar o dashboard](./media/azure-portal-dashboards/customize-dashboard.png)

Adicione mosaicos arrastando e soltando-os para a superfície de dashboard onde quiser.

Há uma nova categoria designada **gerais** para os mosaicos que não estão associados um recurso específico.  Neste exemplo, vamos afixar o mosaico de Markdown.  Utilize este mosaico para adicionar conteúdo personalizado ao seu dashboard.  O mosaico suporta texto simples, [sintaxe de Markdown](https://daringfireball.net/projects/markdown/syntax)e um conjunto limitado de HTML.  (Para segurança, não é possível fazer coisas como injetar `<script>` etiquetas ou utilize a determinado elemento de definição de estilo de CSS que podem interferir com o portal.) 

![adicionar o markdown](./media/azure-portal-dashboards/add-markdown.png)

## <a name="edit-a-dashboard"></a>Editar um dashboard
Depois de criar o seu dashboard, pode afixar mosaicos a partir da Galeria de mosaicos ou a representação de mosaico dos painéis. Vamos afixar a representação do nosso grupo de recursos. Pode um pin quando o item de navegação ou a partir do painel do grupo de recursos. As duas abordagens resultam no fixando a representação de mosaico do grupo de recursos.

![Afixar ao dashboard](./media/azure-portal-dashboards/pin-to-dashboard.png)

Depois de fixar o item, ele aparece no seu dashboard.

![Ver dashboard](./media/azure-portal-dashboards/view-dashboard.png)

Agora que temos um mosaico de Markdown e um grupo de recursos afixada ao dashboard, podemos redimensionar e reorganizar os mosaicos num layout adequado.

Ao pairar o rato e selecionar "..." ou clicando com o botão direito num mosaico, pode ver todos os comandos contextuais para esse mosaico. Por predefinição, existem dois itens:

1. **Remover do dashboard** – remove o mosaico do dashboard
2. **Personalizar** – entra em modo de personalização

![Personalizar o mosaico](./media/azure-portal-dashboards/customize-tile.png)

Selecionando personalizar, pode redimensionar e reorganizar os mosaicos. Para redimensionar um mosaico, selecione o novo tamanho do contextual menu, conforme mostrado na imagem seguinte.

![redimensionar o mosaico](./media/azure-portal-dashboards/resize-tile.png)

Em alternativa, se o mosaico suportar qualquer tamanho, pode arrastar o canto inferior direito para o tamanho pretendido.

![redimensionar o mosaico](./media/azure-portal-dashboards/resize-corner.png)

Depois de redimensionar mosaicos, ver o dashboard.

![mosaico de vista](./media/azure-portal-dashboards/view-tile.png)

Quando tiver terminado de personalizar um dashboard, basta selecionar o **personalização concluída** para sair do modo de personalização ou com o botão direito e selecione **personalização concluída** no menu de contexto.

## <a name="publish-a-dashboard-and-manage-access-control"></a>Publicar um dashboard e gerir o controlo de acesso
Quando cria um dashboard, é privado por predefinição, o que significa que é o único utilizador que o pode ver.  Para o tornar visível a outros utilizadores, utilize o botão **Partilhar** que se encontra junto aos outros comandos do dashboard.

![partilhar dashboard](./media/azure-portal-dashboards/share-dashboard.png)

É-lhe pedido para escolher uma subscrição e um grupo de recursos para o seu dashboard ser publicado. Integre o dashboards no ecossistema do, Implementámos dashboards partilhados como recursos do Azure (portanto, não é possível partilhar ao escrever um endereço de e-mail).  Acesso às informações apresentadas pela maioria dos mosaicos no portal são regidos pelos [controlo de acesso baseado em do Azure funções](../role-based-access-control/role-assignments-portal.md). Da perspectiva de controlo de acesso, dashboards partilhados não são diferentes de uma máquina virtual ou uma conta de armazenamento.  

Vamos supor que tem uma subscrição do Azure e membros de sua equipe foram atribuídos as funções de **proprietário**, **contribuinte**, ou **leitor** da subscrição.  Os utilizadores que são proprietários ou contribuidores são capazes de lista, visualizar, criar, modificar ou eliminar dashboards nessa subscrição.  Os utilizadores que são os leitores podem listar e ver dashboards, mas não é possível modificar ou eliminá-los.  Os utilizadores com acesso de leitor são capazes de fazer edições locais para um dashboard partilhado, mas não são possível publicar as alterações no servidor.  No entanto, eles podem fazer uma cópia privada do dashboard para utilização pessoal.  Como sempre, individuais mosaicos no dashboard impõem suas próprias regras de controlo de acesso com base nos recursos correspondem às.  

Para sua comodidade, a experiência de publicação do portal encaminha-o para um padrão onde coloca dashboards num grupo de recursos designado **dashboards**.  

![Publicar o dashboard](./media/azure-portal-dashboards/publish-dashboard.png)

Também pode optar por publicar um dashboard num grupo de recursos específico.  O controlo de acesso para esse dashboard coincide com o controlo de acesso para o grupo de recursos.  Os utilizadores que podem gerir os recursos nesse grupo de recursos também têm acesso aos dashboards.

![publicar o dashboard para o grupo de recursos](./media/azure-portal-dashboards/publish-to-resource-group.png)

Depois de seu dashboard é publicado, o **acesso + partilha** painel de controle irá atualizar e mostrar-lhe informações sobre o dashboard publicado, incluindo uma ligação para gerir o acesso de utilizador para o dashboard.  Isso inicia o painel de controlo de acesso baseado em funções padrão, utilizado para gerir o acesso para qualquer recurso do Azure.  Pode sempre voltar a esta vista selecionando **partilha**.

![Gerir controlo de acesso](./media/azure-portal-dashboards/manage-access.png)

## <a name="next-steps"></a>Passos Seguintes
* Para gerir os recursos, consulte [recursos de gerir o Azure com o portal do Azure](../azure-resource-manager/manage-resources-portal.md).
* Para implementar recursos, veja [implementar recursos com modelos do Resource Manager e o portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).

