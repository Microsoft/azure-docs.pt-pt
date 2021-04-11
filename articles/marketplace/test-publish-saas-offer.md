---
title: Como testar e publicar uma oferta saaS para o mercado comercial da Microsoft
description: Use partner Center para submeter a sua oferta SaaS para pré-visualizar, pré-visualizar a sua oferta, testar e, em seguida, publicá-la no mercado comercial da Microsoft.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 03/25/2021
ms.openlocfilehash: aeab671c9283d64f9c1ca37cf184b80b1eca8f35
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045251"
---
# <a name="how-to-test-and-publish-a-saas-offer-to-the-commercial-marketplace"></a>Como testar e publicar uma oferta saaS para o mercado comercial

Este artigo explica como usar o Partner Center para submeter a sua oferta SaaS para publicação, pré-visualização da sua oferta, testá-la e, em seguida, publicá-la ao vivo no mercado comercial. Já deve ter criado uma oferta que quer publicar.

> [!NOTE]
> Recomendamos que crie uma oferta separada de teste e desenvolvimento (DEV) como uma forma de testar de baixo risco antes de publicar a sua oferta de produção (PROD). Siga estes passos para criar e testar a oferta DEV antes de publicar a sua oferta (PROD).

## <a name="submit-your-offer-for-publishing"></a>Submeta a sua oferta para publicação

1. Inscreva-se no painel de instrumentos do mercado comercial no [Partner Center.](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)
1. Na página **'Vista Geral',** selecione a oferta que pretende publicar.
1. No canto superior direito do portal, selecione **'Rever e publicar**.
2. Certifique-se de que a coluna **'Estado'** de cada página diz **Complete**. Os três estatutos possíveis são os seguintes:

   - **Não começou** – A página está incompleta.
   - **Incompleto** – Falta informação necessária ou tem erros que precisam de ser corrigidos. Terá de voltar à página e atualizá-la.
   - **Completo** - A página está completa. Todos os dados necessários foram fornecidos e não há erros.

1. Se alguma das páginas tiver um estado diferente do **Complete**, selecione o nome da página, corrija o problema, guarde a página e, em seguida, selecione **'Rever e publicar** novamente para voltar a esta página.'
1. Depois de todas as páginas estarem completas, na **caixa de notas para certificação,** forneça instruções de teste à equipa de certificação para garantir que a sua aplicação seja testada corretamente. Forneça quaisquer notas suplementares úteis para entender a sua aplicação.
1. Para iniciar o processo de publicação da sua oferta, **selecione Publicar**. A página **geral da Oferta** aparece e mostra o estado da **publicação** da oferta.

O estado de publicação da sua oferta mudará à medida que se move através do processo de publicação. Para obter informações detalhadas sobre este processo, consulte [a validação e as etapas de publicação.](review-publish-offer.md#validation-and-publishing-steps)

## <a name="preview-and-test-your-offer"></a>Pré-visualizar e testar a sua oferta

Quando a oferta estiver pronta para a sua assinatura, enviaremos um e-mail para solicitar que reveja e aprove a sua pré-visualização da oferta. Também pode atualizar a página geral da **Oferta** no seu navegador para ver se a sua oferta chegou à fase de desinsusitada do Publisher. Se tiver, o botão **Go ao vivo** e os links de pré-visualização estarão disponíveis. Haverá um link para a pré-visualização do Microsoft AppSource, pré-visualização do Azure Marketplace ou ambos dependendo das opções que escolheu ao criar a sua oferta. Se optar por vender a sua oferta através da Microsoft, qualquer pessoa que tenha sido adicionada ao público de pré-visualização pode testar a aquisição e implementação da sua oferta para garantir que ela cumpre os seus requisitos durante esta fase.

A imagem seguinte mostra a página **geral da Oferta** para uma oferta SaaS, com dois links de pré-visualização no botão Go **live.** Os passos de validação que verá nesta página variam consoante as seleções que fez quando criou a oferta.

![Ilustra a página geral da Oferta para uma oferta no Partner Center. São mostrados os botões Go ao vivo e os links de pré-visualização. O link do relatório de validação do Ver também é apresentado na validação automatizada.](./media/review-publish-offer/publish-status-saas.png)

Use os seguintes passos para pré-visualizar a sua oferta.

1. Na página **'Visão geral' da Oferta,** selecione um link de pré-visualização no botão **Go live.**

1. Para validar o fluxo de compra e configuração de ponta a ponta, compre os planos na sua oferta enquanto estiver em pré-visualização. Em primeiro lugar, notifique a Microsoft com um [bilhete de apoio](https://aka.ms/marketplacesupport) para garantir que não processamos uma taxa.

1. Se a sua oferta SaaS suporta [faturação medido utilizando o serviço de medição de mercado comercial,](./partner-center-portal/saas-metered-billing.md)reveja e siga as melhores práticas de teste detalhadas em [APIs de faturação medido](./partner-center-portal/marketplace-metering-service-apis.md#development-and-testing-best-practices)pelo Marketplace .

1. Reveja e siga as instruções de teste em [SaaS cumprir a versão 2 do SaaS no mercado comercial da Microsoft](./partner-center-portal/pc-saas-fulfillment-api-v2.md#development-and-testing) para garantir que a sua oferta está integrada com sucesso com as APIs antes de publicar a sua oferta ao vivo.

1. Se o passo de validação da Oferta resultou em advertências, aparece um link **de relatório de validação** de visualização da Visualização da Oferta na página geral da **Oferta.** Certifique-se de rever o relatório e abordar os problemas antes de selecionar o botão **Go live.** Caso contrário, a certificação provavelmente falhará e atrasará a sua oferta de ir ao vivo.

1. Se precisar de fazer alterações após a pré-visualização e teste da oferta, pode editar e reenviar para publicar uma nova pré-visualização. Para mais informações, consulte [Atualizar uma oferta existente no mercado comercial.](./partner-center-portal/update-existing-offer.md)

## <a name="publish-your-offer-live"></a>Publique a sua oferta ao vivo

Depois de completar todos os testes na sua pré-visualização, selecione **Go live** para publicar a sua oferta ao vivo no mercado comercial. Se a sua oferta já estiver ao vivo no mercado comercial, quaisquer atualizações que faça não serão transmitidas ao vivo até que selecione **Go live**.

> [!IMPORTANT]
> Nunca selecione **Go live** para uma oferta de [desenvolvimento/teste](create-saas-dev-test-offer.md).

Agora que escolheu disponibilizar a sua oferta no mercado comercial, realizamos uma série de verificações de validação final para garantir que a oferta ao vivo está configurada tal como a versão de pré-visualização da oferta. Para mais informações sobre estas verificações de validação, consulte [a fase de publicação](review-publish-offer.md#publish-phase).

Após estes cheques de validação estarem completos, a sua oferta será ao vivo no mercado.

## <a name="next-steps"></a>Passos seguintes

- [Aceder a relatórios analíticos para o mercado comercial no Partner Center](./partner-center-portal/analytics.md)
