---
title: Como criar um pedido de apoio azure [ Microsoft Docs
description: Os clientes que necessitem de assistência podem utilizar o portal Azure para encontrar soluções de self-service e para criar e gerir pedidos de apoio.
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: article
ms.date: 03/31/2020
ms.author: kfollis
ms.openlocfilehash: 0bd1191c0b92203b100b1713971119ec828352ea
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835551"
---
# <a name="how-to-create-an-azure-support-request"></a>Como criar um pedido de suporte do Azure

## <a name="overview"></a>Descrição geral

O Azure permite-lhe criar e gerir pedidos de apoio, também conhecidos como bilhetes de apoio. Pode criar e gerir pedidos no [portal Azure,](https://portal.azure.com)que está abrangido por este artigo. Também pode criar e gerir pedidos programáticamente, utilizando o bilhete de [apoio Azure REST API](/rest/api/support).

> [!NOTE]
> O URL do portal Azure é específico da nuvem Azure onde a sua organização está implantada.
>
>* O portal Azure para uso comercial é:[https://portal.azure.com](https://portal.azure.com)
>* Portal Azure para a Alemanha é:[https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* Portal azure para o governo dos Estados Unidos é:[https://portal.azure.us](https://portal.azure.us)
>
>

Com base no feedback do cliente, atualizámos a experiência de pedido de apoio para nos concentrarmos em três objetivos principais:

* **Streamlined**: Torne o suporte e a resolução de problemas fáceis de encontrar e simplificar a forma como submete um pedido de apoio.
* **Integrado:** Pode facilmente abrir um pedido de apoio quando estiver a resolver problemas com um recurso Azure, sem mudar de contexto.
* **Eficiente**: Recolha as informações-chave que o seu agente de suporte necessita para resolver eficazmente o seu problema.

## <a name="getting-started"></a>Introdução

Pode obter **suporte para Ajudar +** no portal Azure. Está disponível no menu do portal Azure, no cabeçalho global, ou no menu de recursos para um serviço. Antes de poder apresentar um pedido de apoio, deve ter permissões adequadas.

### <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Para criar um pedido de apoio, deve ser [proprietário,](../../role-based-access-control/built-in-roles.md#owner) [colaborador](../../role-based-access-control/built-in-roles.md#contributor) ou ser designado para o papel de [Colaborador de Pedido](../../role-based-access-control/built-in-roles.md#support-request-contributor) de Apoio ao nível da subscrição. Para criar um pedido de apoio sem subscrição, por exemplo, o cenário do Azure Ative Directory (AAD), deve ser um [Administrador](../../active-directory/users-groups-roles/directory-assign-admin-roles.md).

### <a name="go-to-help--support-from-the-global-header"></a>Vá para ajudar + apoio do cabeçalho global

Para iniciar um pedido de apoio de qualquer lugar do portal Azure:

1. Selecione o **?** no cabeçalho global. Em seguida, selecione **Ajuda + suporte**.

   ![Ajuda e Suporte](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. Selecione **Novo pedido de suporte**. Siga as instruções para nos fornecer informações sobre o seu problema. Sugerimos algumas soluções possíveis, reuniremos detalhes sobre o assunto e ajudá-lo-emos a submeter e rastrear o pedido de apoio.

   ![Novo pedido de suporte](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Vá para ajudar + suporte a partir de um menu de recursos

Para iniciar um pedido de apoio no contexto do recurso, está neste momento a trabalhar com:

1. A partir do menu de recursos, na secção **Suporte + Resolução de Problemas,** selecione Novo pedido de **suporte**.

   ![No contexto](./media/how-to-create-azure-support-request/incontext2lower.png)

2. Siga as instruções para nos fornecer informações sobre o problema que está tendo. Quando iniciar o processo de pedido de apoio a partir do recurso, algumas opções são pré-selecionadas para si.

## <a name="create-a-support-request"></a>Criar um pedido de suporte

Vamos acompanhá-lo por alguns passos para recolher informações sobre o seu problema e ajudá-lo a resolvê-lo. Cada passo é descrito nas seguintes secções.

### <a name="basics"></a>Noções básicas

O primeiro passo do processo de pedido de apoio reúne informações básicas sobre o seu problema e o seu plano de apoio.

No separador **Basics** do novo pedido de **suporte,** utilize os selecionadores para começar a falar-nos sobre o problema. Primeiro, identificará algumas categorias gerais para o tipo de emissão e escolherá a subscrição relacionada. Selecione o serviço, por exemplo, **Máquina Virtual que executa o Windows**. Selecione o recurso, como o nome da sua máquina virtual. Descreva o problema nas suas próprias palavras e, em seguida, **selecione o tipo** de problema para obter mais específico.

![Painel Básico](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> O Azure oferece um apoio ilimitado à gestão de subscrições, que inclui faturação, ajustes de quotas e transferências de contas. Para apoio técnico, precisa de um plano de apoio. [Saiba mais sobre planos](https://azure.microsoft.com/support/plans)de apoio.
>
>

### <a name="solutions"></a>Soluções

Depois de recolher informações básicas, mostramos-lhe soluções para experimentar por si mesmo. Em alguns casos, podemos até fazer um diagnóstico rápido. As soluções são escritas por engenheiros azure e resolverão os problemas mais comuns.

### <a name="details"></a>Detalhes

Em seguida, recolhemos detalhes adicionais sobre o problema. Fornecer informações completas e detalhadas neste passo ajuda-nos a encaminhar o seu pedido de apoio para o agente certo.

Se possível, diga-nos quando o problema começou e quaisquer passos para reproduzi-lo. Pode fazer o upload de um ficheiro, como um ficheiro de registo ou saída a partir de diagnósticos.

Depois de termos toda a informação sobre o problema, escolha como obter apoio. Na secção de **Detalhes**do método de **suporte,** selecione a gravidade do impacto. Forneça o seu método de contacto preferido, uma boa hora para contactá-lo e a sua linguagem de suporte.

Em seguida, complete a secção de **informações** de contacto para que saibamos como contactá-lo.

### <a name="review--create"></a>Rever + criar

Complete todas as informações necessárias em cada separador e, em seguida, selecione **Review + criar**. Verifique os detalhes que enviará para o Suporte. Volte a qualquer separador para fazer uma mudança, se necessário. Quando estiver satisfeito, o pedido de apoio está completo, selecione **Criar**.

Um agente de suporte entrará em contacto consigo utilizando o método indicado. Para obter informações sobre o tempo de resposta inicial, consulte o âmbito de [suporte e a capacidade de resposta](https://azure.microsoft.com/support/plans/response/).

## <a name="all-support-requests"></a>Todos os pedidos de apoio

Pode visualizar os detalhes e o estado dos pedidos de apoio **indo para Ajudar + apoiar**Todos os pedidos de  >   **suporte**.

![Todos os pedidos de apoio](./media/how-to-create-azure-support-request/allrequestslower.png)

Nesta página, pode filtrar pedidos de suporte por **Subscrição,** Data **Criada** (UTC) e **Status**. Além disso, pode ordenar e procurar pedidos de apoio nesta página.

Selecione um pedido de suporte para visualizar detalhes, incluindo a gravidade e o tempo esperado que levará para que um agente de suporte responda.

Se quiser alterar a gravidade do pedido, selecione o impacto do **Negócio.** Escolha entre uma lista de severidades para atribuir.

> [!NOTE]
> O nível máximo de gravidade depende do seu plano de apoio. [Saiba mais sobre planos](https://azure.microsoft.com/support/plans)de apoio.
>
>
Para saber mais sobre opções de suporte de autoajuda em Azure, veja este vídeo:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

## <a name="next-steps"></a>Passos seguintes

* [Envie-nos o seu feedback e sugestões](https://feedback.azure.com/forums/266794-support-feedback)
* Envolva-se connosco no [Twitter](https://twitter.com/azuresupport)
* Obtenha ajuda dos seus pares na página de [perguntas do Microsoft Q&A](https://docs.microsoft.com/answers/products/azure)
* Saiba mais em [FaQ de suporte azure](https://azure.microsoft.com/support/faq)
