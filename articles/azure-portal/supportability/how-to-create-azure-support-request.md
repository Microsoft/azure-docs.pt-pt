---
title: Como criar um pedido de suporte do Azure
description: Os clientes que precisam de assistência podem usar o portal Azure para encontrar soluções de self-service e para criar e gerir pedidos de suporte.
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.topic: how-to
ms.date: 06/25/2020
ms.openlocfilehash: 11ca7925ce1664b5586ab8ec0fb523a2d562ee80
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745592"
---
# <a name="create-an-azure-support-request"></a>Criar um pedido de suporte do Azure

O Azure permite-lhe criar e gerir pedidos de apoio, também conhecidos como bilhetes de apoio. Pode criar e gerir pedidos no [portal Azure](https://portal.azure.com), que está abrangido por este artigo. Também pode criar e gerir pedidos programáticamente, utilizando o [bilhete de apoio Azure REST API.](/rest/api/support)

> [!NOTE]
> O URL do portal Azure é específico da nuvem Azure onde a sua organização está implantada.
>
>* O portal Azure para uso comercial é: [https://portal.azure.com](https://portal.azure.com)
>* O portal Azure para a Alemanha é: [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* O portal Azure para o governo dos Estados Unidos é: [https://portal.azure.us](https://portal.azure.us)

A experiência de pedido de apoio centra-se em três objetivos principais:

* **Simplificado**: Tornar o suporte e a resolução de problemas fáceis de encontrar e simplificar a forma como submete um pedido de apoio.
* **Integrado**: Pode abrir facilmente um pedido de suporte quando está a resolver problemas com um recurso Azure, sem alterar o contexto.
* **Eficiente**: Recolha as informações-chave de que o seu engenheiro de suporte necessita para resolver eficazmente o seu problema.

O Azure oferece apoio ilimitado para a gestão de subscrições, que inclui faturação, ajustamentos de quotas e transferências de conta. Para apoio técnico, precisa de um plano de apoio. Para obter mais informações, veja [Comparar planos de suporte](https://azure.microsoft.com/support/plans).

## <a name="getting-started"></a>Introdução

Pode obter o **suporte Help +** no portal Azure. Está disponível no menu do portal Azure, no cabeçalho global ou no menu de recursos para um serviço. Antes de poder apresentar um pedido de apoio, tem de ter permissões apropriadas.

### <a name="azure-role-based-access-control"></a>Controlo de acesso baseado em funções do Azure

Para criar um pedido de apoio, tem de ser [Proprietário,](../../role-based-access-control/built-in-roles.md#owner) [Colaborador](../../role-based-access-control/built-in-roles.md#contributor) ou ser designado para a função [de Contribuinte de Pedido de Apoio](../../role-based-access-control/built-in-roles.md#support-request-contributor) ao nível da subscrição. Para criar um pedido de suporte sem subscrição, por exemplo, um cenário de Diretório Ativo Azure, tem de ser um [Administrador](../../active-directory/roles/permissions-reference.md).

### <a name="go-to-help--support-from-the-global-header"></a>Ir para a Ajuda + apoio do cabeçalho global

Para iniciar um pedido de apoio a partir de qualquer lugar do portal Azure:

1. Selecione **o?** no cabeçalho global. Em seguida, **selecione Ajuda + suporte**.

   ![Ajuda e Suporte](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

1. Selecione **Novo pedido de suporte**. Siga as instruções para fornecer informações sobre o seu problema. Sugerimos algumas soluções possíveis, recolhemos detalhes sobre o assunto e ajudamos-te a submeter e rastrear o pedido de apoio.

   ![Novo pedido de suporte](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Ir para ajudar + suporte a partir de um menu de recursos

Para iniciar um pedido de apoio no contexto do recurso, está neste momento a trabalhar com:

1. A partir do menu de recursos, na secção **Suporte + Resolução de Problemas,** selecione **Novo pedido de suporte**.

   ![No contexto](./media/how-to-create-azure-support-request/incontext2lower.png)

1. Siga as instruções para nos fornecer informações sobre o problema que está tendo. Quando inicia o processo de pedido de suporte a partir do recurso, algumas opções são pré-selecionadas para si.

## <a name="create-a-support-request"></a>Criar um pedido de suporte

Vamos acompanhá-lo através de alguns passos para recolher informações sobre o seu problema e ajudá-lo a resolvê-lo. Cada passo é descrito nas seguintes secções.

### <a name="basics"></a>Noções básicas

O primeiro passo do processo de pedido de apoio reúne informações básicas sobre o seu problema e o seu plano de apoio.

No **separador Basics** do Novo pedido de **suporte,** utilize os selecionadores para começar a falar-nos do problema. Primeiro, identificará algumas categorias gerais para o tipo de emissão e escolherá a subscrição relacionada. Selecione o serviço, por **exemplo, Máquina Virtual que executa o Windows**. Selecione o recurso, como o nome da sua máquina virtual. Descreva o problema nas suas próprias palavras e, em seguida, selecione **o tipo de problema** e o **subtipo de problema** para obter mais específico.

![Painel Básico](./media/how-to-create-azure-support-request/basics2lower.png)

### <a name="solutions"></a>Soluções

Depois de recolher informações básicas, mostramos-lhe soluções para experimentar por conta própria. Em alguns casos, podemos até fazer um diagnóstico rápido. As soluções são escritas por engenheiros da Azure e resolverão os problemas mais comuns.

### <a name="details"></a>Detalhes

Em seguida, recolhemos detalhes adicionais sobre o problema. Fornecer informações detalhadas e detalhadas neste passo ajuda-nos a encaminhar o seu pedido de apoio para o engenheiro certo.

1. Se possível, diga-nos quando o problema começou e quaisquer passos para reproduzi-lo. Pode fazer o upload de um ficheiro, como um ficheiro de registo ou saída a partir de diagnósticos. Para obter mais informações sobre uploads de ficheiros, consulte [as diretrizes de upload de ficheiros](how-to-manage-azure-support-request.md#file-upload-guidelines).

1. Depois de termos toda a informação sobre o problema, escolha como obter apoio. Na secção **método de suporte** de **Detalhes,** selecione a gravidade do impacto. O nível máximo de gravidade depende do seu [plano de suporte](https://azure.microsoft.com/support/plans).

    Por predefinição, é selecionada a opção **de informação de diagnóstico Partilhar.** Isto permite ao suporte do Azure recolher [informações](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) de diagnóstico dos seus recursos Azure. Em alguns casos, há uma segunda questão que não é selecionada por padrão, como solicitar o acesso à memória de uma máquina virtual.

1. Forneça o seu método de contacto preferido, uma boa altura para o contactar e o seu idioma de suporte.

1. Em seguida, preencha a secção **de informações de contato** para que saibamos como contactá-lo.

### <a name="review--create"></a>Rever + criar

Preencha todas as informações necessárias em cada separador e, em seguida, selecione **Review + create**. Verifique os detalhes que enviará para suporte. Volte a qualquer separador para fazer uma mudança, se necessário. Quando estiver satisfeito, o pedido de apoio está completo, selecione **Criar**.

Um engenheiro de suporte entrará em contacto consigo utilizando o método indicado. Para obter informações sobre os tempos de resposta iniciais, consulte [o âmbito de suporte e a capacidade de resposta](https://azure.microsoft.com/support/plans/response/).


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre opções de autoajuda em Azure, veja este vídeo:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

Siga estes links para saber mais:

* [Como gerir um pedido de apoio do Azure](how-to-manage-azure-support-request.md)
* [API REST de pedidos de suporte do Azure](/rest/api/support)
* [Envie-nos o seu feedback e sugestões](https://feedback.azure.com/forums/266794-support-feedback)
* Envolva-se connosco no [Twitter](https://twitter.com/azuresupport)
* Obtenha ajuda dos seus pares na página de perguntas da [Microsoft Q&](/answers/products/azure)
* Saiba mais em [Azure Support FAQ](https://azure.microsoft.com/support/faq)