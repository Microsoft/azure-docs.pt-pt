---
title: Publicar oferta de aplicação do Azure - Azure Marketplace | Documentos da Microsoft
description: Descreve o processo e os passos para publicar uma oferta de aplicação do Azure no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: pbutlerm
ms.openlocfilehash: fdca47877d4cb2192eef0a26448cd21e8afe4b77
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57217942"
---
# <a name="publish-azure-application-offer"></a>Publicar oferta de aplicação do Azure

Depois de criar uma oferta, fornecendo as informações sobre o **nova oferta** página, pode publicar a oferta. Selecione **publicar** para iniciar o processo de publicação.

O diagrama seguinte mostra os passos principais no processo de publicação de uma oferta para a "ativação online".

![Passos de publicação de oferta](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>Descrição detalhada dos passos de publicação

A tabela seguinte apresenta uma lista e descreve cada etapa de publicação e fornece uma estimativa de tempo para concluir cada passo.  Vezes estimativas em "dias" são definidos como dias úteis, que exclui fins de semana e feriados.

|  **Etapa de publicação**           | **tempo**    | **Descrição**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Validar a pré-requisitos         | < 15 min    | Oferecer informações e oferecer definições são validadas.                        |
| Validar as definições de receita influenciada | < 15 min  | Atribuição de utilização de recursos do Azure para a oferta é verificada.             |
| Certificação                  | < 1 dia     | Oferta é analisada pela equipe de certificação do Azure. A oferta é procurada vírus, malware, conformidade de segurança e problemas de segurança. A oferta é verificada para ver de que cumpre todos os critérios de elegibilidade. Para obter mais informações, consulte [pré-requisitos](./cpp-prerequisites.md). Comentários é fornecido se não for encontrado um problema. |
| Validação de unidade de teste          | < a 2 horas   | (Opcional) Se uma versão de teste estiver presente, a Microsoft valida que pode ser implantado e replicada.  |
| Empacotamento e registo de geração de oportunidades potenciais | < 1 hora  | Ativos de técnicos da oferta são empacotados para uso do cliente e os sistemas de oportunidades potenciais são configurados e implementados. |
|  Aprovação do publicador             |  manual    | Revisão do publicador final e a confirmação antes da oferta entra no ar. A oferta está agora disponível para pré-visualização.  Pode implementar a sua oferta nas subscrições selecionadas (nos passos de informações oferta) para verificar que cumpre todos os seus requisitos.  Depois de verificar a oferta, selecione **Go Live** para que a sua oferta pode mover para o passo seguinte. |
| Revisão da Microsoft                | 7 - 14 dias | A Microsoft holística analisa a sua aplicação do Azure e envia um e-mail se são descobertos problemas.  O comprimento deste passo depende da complexidade do aplicativo, os problemas descobertos e imediatamente como responder a elas.  |
| Em direto                           | < 1 dia | Oferta é lançada, replicada para as regiões especificadas e disponibilizada ao público. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

Pode monitorizar o processo de publicação na **estado** separador para a sua oferta no Portal de parceiros de nuvem.

![Separador de estado para uma oferta de aplicação do Azure](./media/offer-status-tab.png)

Depois de concluir o processo de publicação, a sua oferta será listada na [categoria de aplicação do Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/).

>[!Note]
>Fornecedores de soluções (CSP) parceiro canal optar ativamente por participar na cloud agora está disponível.  Veja [fornecedores de soluções Cloud](../../cloud-solution-providers.md) para obter mais informações sobre a sua oferta através do CSP da Microsoft de marketing de parceiros canais.

## <a name="errors-and-review-feedback"></a>Erros e os comentários da revisão

Além de exibir o estado de publicação da sua oferta, o **estado** guia também exibe mensagens de erro e comentários a partir de quaisquer passos de publicação em que for encontrado um problema.  Se o problema é essencial, em seguida, publicando é cancelada.  Em seguida, tem de corrigir os problemas reportados e voltar a publicar a oferta.  Uma vez que o **revisão Microsoft** passo representa uma revisão extensiva da sua oferta e os seus ativos técnicos associados (especialmente o modelo Azure Resource Manager), problemas, normalmente, são apresentados como ligações de pedido (PR) de solicitação.  Obter uma explicação de como visualizar e responder a estes pedidos pull, consulte [comentários da revisão de manipulação de](./cpp-handling-review-feedback.md).


## <a name="next-steps"></a>Passos Seguintes

Se encontrou erros em uma ou mais dos passos de publicação, tem de corrigi-los e voltar a publicar a oferta.  Se a problemas críticos são encontrados no **revisão do Microsoft** passo, deve [lidar com os comentários da revisão](./cpp-handling-review-feedback.md) acessando o Microsoft rever o repositório de Azure DevOps da equipa.

Depois de uma aplicação do Azure é publicada com êxito, pode [atualizar a oferta existente](./cpp-update-existing-offer.md) para refletir a alteração de requisitos comerciais ou técnicos. 
