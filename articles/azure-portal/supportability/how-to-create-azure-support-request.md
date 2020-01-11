---
title: Como criar uma solicitação de suporte do Azure | Microsoft Docs
description: Os clientes que precisam de assistência podem usar o portal do Azure para encontrar soluções de autoatendimento e para criar e gerenciar solicitações de suporte.
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: article
ms.date: 11/13/2019
ms.author: kfollis
ms.openlocfilehash: 35d70d37b881c610d1a38a312d79f39c351c65a5
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897419"
---
# <a name="how-to-create-an-azure-support-request"></a>Como criar um pedido de suporte do Azure

## <a name="overview"></a>Visão geral

Os clientes do Azure podem criar e gerenciar solicitações de suporte no [portal do Azure](https://portal.azure.com).

> [!NOTE]
> A URL de portal do Azure é específica para a nuvem do Azure onde sua organização está implantada. 
>* Portal do Azure para uso comercial é: [https://portal.azure.com](https://portal.azure.com)
>* Portal do Azure para a Alemanha é: [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* Portal do Azure para o Estados Unidos governamental é: [https://portal.azure.us](https://portal.azure.us)
>
>

Com base nos comentários dos clientes, atualizamos a experiência de solicitação de suporte para se concentrar em três metas principais:

* **Simplificado**: tornar o suporte e a solução de problemas fáceis de localizar e simplificar a forma como você envia uma solicitação de suporte.
* **Integrado**: você pode abrir facilmente uma solicitação de suporte quando estiver solucionando um problema com um recurso do Azure, sem alternar o contexto.
* **Eficiente**: Reúna as principais informações que seu agente de suporte precisa para resolver o problema com eficiência.

## <a name="getting-started"></a>Introdução

Você pode obter **ajuda + suporte** no portal do Azure. Ele está disponível no cabeçalho global ou no menu de recursos de um serviço. Antes de poder arquivar uma solicitação de suporte, você deve ter as permissões apropriadas.

### <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Para criar uma solicitação de suporte, você deve ser um administrador ou ser atribuído à função de [colaborador de solicitação de suporte](../../role-based-access-control/built-in-roles.md#support-request-contributor) .

### <a name="go-to-help--support-from-the-global-header"></a>Vá para ajuda + suporte no cabeçalho global

Para iniciar uma solicitação de suporte de qualquer lugar no portal do Azure:

1. Selecione o ícone **?** no cabeçalho global. Em seguida, selecione **ajuda + suporte**.

   ![Ajuda e Suporte](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. Selecione **+ nova solicitação de suporte**. Siga os prompts para nos fornecer informações sobre o problema. Vamos sugerir algumas soluções possíveis, coletar detalhes sobre o problema e ajudá-lo a enviar e acompanhar a solicitação de suporte.

   ![Novo pedido de suporte](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Ir para ajuda + suporte em um menu de recursos

Para iniciar uma solicitação de suporte no contexto do recurso, você está trabalhando atualmente com:

1. No menu de recursos, na seção **suporte + solução de problemas** , selecione **nova solicitação de suporte**.

   ![No contexto](./media/how-to-create-azure-support-request/incontext2lower.png)

2. Siga os prompts para nos fornecer informações sobre o problema que você está tendo. Quando você inicia o processo de solicitação de suporte do recurso, algumas opções são previamente selecionadas para você.

## <a name="create-a-support-request"></a>Criar um pedido de suporte

Vamos orientá-lo em algumas etapas para coletar informações sobre o problema e ajudá-lo a solucioná-lo. Cada etapa é descrita nas seções a seguir.

### <a name="basics"></a>Noções básicas

A primeira etapa do processo de solicitação de suporte coleta informações básicas sobre seu problema e seu plano de suporte.

Na guia **noções básicas** de **nova solicitação de suporte**, use os seletores para começar a nos informar sobre o problema. Primeiro, você identificará algumas categorias gerais para o tipo de problema e escolherá a assinatura relacionada. Selecionar o serviço (por exemplo, "máquina virtual que executa o Windows") e o recurso (o nome da sua máquina virtual) é a próxima etapa para obter ajuda. Descreva brevemente o problema em suas próprias palavras e **selecione tipo de problema** para obter mais informações específicas.

![Painel Básico](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> O Azure fornece suporte ilimitado para gerenciamento de assinatura (itens como cobrança, ajustes de cota e transferências de conta). Para obter suporte técnico, você precisa de um plano de suporte. [Saiba mais sobre os planos de suporte](https://azure.microsoft.com/support/plans).
>
>

### <a name="solutions"></a>Soluções

Depois de coletar informações básicas, mostramos a você as soluções que você pode experimentar por conta própria. Em alguns casos, poderemos até mesmo executar um diagnóstico rápido. As soluções são escritas por engenheiros do Azure e resolverão os problemas mais comuns.

### <a name="details"></a>Detalhes

Em seguida, coletamos detalhes adicionais sobre o problema. Fornecer informações completas e detalhadas nesta etapa nos ajuda a rotear sua solicitação de suporte para o agente correto e permite que eles comecem a diagnosticar o problema.

É uma boa ideia nos informar quando o problema foi iniciado e as etapas para reproduzi-lo. Você também pode optar por carregar um arquivo, como um arquivo de log ou uma saída do diagnóstico.

Depois de ter todas as informações sobre o problema que você está tendo, você pode escolher como obter suporte. Na seção **método de suporte** da guia **detalhes** , selecione a gravidade do impacto. Forneça seu método de contato preferencial, um bom momento para entrar em contato com você e o idioma de suporte.

Em seguida, preencha a seção **informações de contato** para que saibamos como contatá-lo.

### <a name="review--create"></a>Rever + criar

Conclua todas as informações necessárias em cada guia e selecione **revisar + criar**. Verifique os detalhes que você enviará para dar suporte. Volte para qualquer guia para fazer uma alteração, se necessário. Quando estiver satisfeito, a solicitação de suporte será concluída, selecione **criar**.

Um agente de suporte entrará em contato com você usando o método que você indicou. Consulte o [escopo de suporte e a capacidade de resposta](https://azure.microsoft.com/support/plans/response/) para obter informações sobre o tempo de resposta inicial.

## <a name="all-support-requests"></a>Todas as solicitações de suporte

Você pode exibir os detalhes e o status das solicitações de suporte acessando **ajuda + suporte** >  **todas as solicitações de suporte**.

![Todas as solicitações de suporte](./media/how-to-create-azure-support-request/allrequestslower.png)

Nessa página, você pode filtrar as solicitações de suporte por assinatura, data de criação (UTC) e status. Além disso, você pode classificar e procurar por solicitações de suporte nesta página.

Selecione uma solicitação de suporte para exibir detalhes, incluindo gravidade e o tempo esperado que levará para que um agente de suporte responda.

Se você quiser alterar a severidade da solicitação, selecione **impacto comercial**. Uma lista de severidades disponíveis para atribuir é mostrada.

> [!NOTE]
> O nível de severidade máximo depende do seu plano de suporte. [Saiba mais sobre os planos de suporte](https://azure.microsoft.com/support/plans).
>
>
Para saber mais sobre as opções de suporte de auto-ajuda no Azure, Assista a este vídeo:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

## <a name="next-steps"></a>Passos seguintes
* [Envie-nos seus comentários e sugestões](https://feedback.azure.com/forums/266794-support-feedback)
* Entre em contato conosco no [Twitter](https://twitter.com/azuresupport)
* Obtenha ajuda de seus colegas nos [fóruns do MSDN](https://social.msdn.microsoft.com/Forums/azure)
* Saiba mais nas [perguntas frequentes sobre o suporte do Azure](https://azure.microsoft.com/support/faq)
