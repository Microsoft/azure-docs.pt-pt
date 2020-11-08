---
title: Como testar e publicar uma oferta de aplicação Azure
description: Use o Partner Center para submeter a sua oferta de aplicação Azure para pré-visualizar, pré-visualizar a sua oferta, testar e, em seguida, publicá-la no mercado comercial da Microsoft.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: b54e965188be51ec54110bb85d8cda8f01256836
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370231"
---
# <a name="how-to-test-and-publish-an-azure-application-offer"></a>Como testar e publicar uma oferta de aplicação Azure

Este artigo explica como usar o Partner Center para submeter a sua oferta de aplicação Azure para publicação, pré-visualização da sua oferta, testá-la e, em seguida, publicá-la ao vivo no mercado comercial. Já deve ter criado uma oferta que quer publicar.

## <a name="submit-your-offer-for-publishing"></a>Submeta a sua oferta para publicação

1. Inscreva-se no painel de instrumentos do mercado comercial no [Partner Center.](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)
1. Na página **'Vista Geral',** selecione a oferta que pretende publicar.
1. No canto superior direito do portal, selecione **'Rever e publicar**.
1. Certifique-se de que a coluna **'Estado'** de cada página diz **Complete**. Os três estatutos possíveis são os seguintes:
    - **Não começou** – A página está incompleta.
    - **Incompleto** – Falta informação necessária ou tem erros que precisam de ser corrigidos. Terá de voltar à página e atualizá-la.
    - **Completo** - A página está completa. Todos os dados necessários foram fornecidos e não há erros.
1. Se alguma das páginas tiver um estado diferente do **Complete** , selecione o nome da página, corrija o problema, guarde a página e, em seguida, selecione **'Rever e publicar** novamente para voltar a esta página.'
1. Depois de todas as páginas estarem completas, na **caixa de notas para certificação,** forneça instruções de teste à equipa de certificação para garantir que a sua aplicação seja testada corretamente. Forneça quaisquer notas suplementares úteis para entender a sua aplicação.
1. Para iniciar o processo de publicação da sua oferta, **selecione Publicar**. A página **geral da Oferta** aparece e mostra o estado da **publicação** da oferta.

O estado de publicação da sua oferta mudará à medida que se move através do processo de publicação. Para obter informações detalhadas sobre este processo, consulte [a validação e as etapas de publicação.](review-publish-offer.md#validation-and-publishing-steps)

## <a name="preview-and-test-your-offer"></a>Pré-visualizar e testar a sua oferta

Quando a oferta estiver pronta para a sua assinatura, enviaremos um e-mail para solicitar que reveja e aprove a sua pré-visualização da oferta. Também pode atualizar a página geral da **Oferta** no seu navegador para ver se a sua oferta chegou à fase de desinsusitada do Publisher. Se tiver, o botão **Go ao vivo** e o link de pré-visualização estarão disponíveis. Se optar por vender a sua oferta através da Microsoft, qualquer pessoa que tenha sido adicionada ao público de pré-visualização pode testar a aquisição e implementação da sua oferta para garantir que ela cumpre os seus requisitos durante esta fase.

A imagem seguinte mostra a página **geral da Oferta** para uma oferta, com dois links de pré-visualização no botão Go ao **vivo.** Os passos de validação que verá nesta página variam consoante as seleções que fez quando criou a oferta.

[![Ilustra a página geral da Oferta para uma oferta no Partner Center. São mostrados os botões Go ao vivo e os links de pré-visualização.](media/create-new-azure-app-offer/azure-app-publish-status.png)](media/create-new-azure-app-offer/azure-app-publish-status.png#lightbox)

Use os seguintes passos para pré-visualizar a sua oferta.

1. Na página **'Visão geral' da Oferta,** selecione um link de pré-visualização no botão **Go live.** 

1. Para validar o fluxo de compra e configuração de ponta a ponta, compre a sua oferta enquanto está em pré-visualização. Em primeiro lugar, notifique a Microsoft com um [bilhete de apoio](https://aka.ms/marketplacesupport) para garantir que não processamos uma taxa.

1. Se a sua aplicação Azure apoiar [a faturação medido utilizando o serviço de medição de mercado comercial,](./partner-center-portal/azure-app-metered-billing.md)reveja e siga as melhores práticas de teste detalhadas nas [APIs de faturação medidos](./partner-center-portal/marketplace-metering-service-apis.md#development-and-testing-best-practices)pelo Marketplace .

1. Se precisar de fazer alterações após a pré-visualização e teste da oferta, pode editar e reenviar para publicar uma nova pré-visualização. Para mais informações, consulte [Atualizar uma oferta existente no mercado comercial.](./partner-center-portal/update-existing-offer.md)

## <a name="publish-your-offer-live"></a>Publique a sua oferta ao vivo

Depois de completar todos os testes na sua pré-visualização, selecione **Go live** para publicar a sua oferta ao vivo no mercado comercial.

   > [!TIP]
   > Se a sua oferta já estiver ao vivo no mercado comercial, quaisquer atualizações que faça não serão transmitidas ao vivo até que selecione **Go live**.

Agora que escolheu disponibilizar a sua oferta no mercado comercial, realizamos uma série de verificações de validação final para garantir que a oferta ao vivo está configurada tal como a versão de pré-visualização da oferta. Para mais informações sobre estas verificações de validação, consulte [a fase de publicação](review-publish-offer.md#publish-phase).

Após estes cheques de validação estarem completos, a sua oferta será ao vivo no mercado.

### <a name="errors-and-review-feedback"></a>Erros e feedback de revisão

O passo **de validação manual** no processo de publicação representa uma revisão extensiva da sua oferta e os seus ativos técnicos associados (especialmente o modelo do Gestor de Recursos Azure) são normalmente apresentados como links de pedido de pull (PR). Uma explicação de como ver e responder a estes PRs, ver [feedback da revisão handling](partner-center-portal/azure-apps-review-feedback.md).

Se tiver erros numa ou mais das etapas de publicação, corrija-as antes de voltar a publicar a sua oferta.

## <a name="next-step"></a>Passo seguinte

- [Aceder a relatórios analíticos para o mercado comercial no Partner Center](partner-center-portal/analytics.md)
- Saiba [como comercializar a sua oferta de Aplicação Azure](create-new-azure-apps-offer-marketing.md) através da Co-venda com a Microsoft e Revenda através de programas CSPs.
