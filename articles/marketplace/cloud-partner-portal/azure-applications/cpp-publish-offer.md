---
title: Publicar oferta de candidatura azure [ Mercado Azure
description: Descreve o processo e os passos para a publicação de uma oferta de aplicação Azure no Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: dsindona
ms.openlocfilehash: 9f89e31c2d17ef74971d2057ba58e9572e92184c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280104"
---
# <a name="publish-azure-application-offer"></a>Publicar oferta de aplicação do Azure

Depois de criar uma oferta fornecendo a informação na página **New Offer,** pode publicar a oferta. Selecione **Publicar** para iniciar o processo de publicação.

O diagrama seguinte mostra os principais passos no processo de publicação para uma oferta de "ir ao vivo".

![Oferecer etapas de publicação](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>Descrição detalhada dos passos de publicação

As listas de tabelas seguintes e descrevem cada passo editorial, e fornece uma estimativa de tempo para completar cada passo.  As estimativas dos tempos em "dias" são definidas como dias úteis, que excluem fins de semana e feriados.

|  **Passo editorial**           | **Tempo**    | **Descrição**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Validar pré-requisitos         | < 15 min    | As informações de oferta e as configurações de oferta são validadas.                        |
| Validar configurações de receitas influenciadas | < 15 min  | A atribuição de recursos Azure para a oferta é verificada.             |
| Certificação                  | < 1 dia     | A oferta é analisada pela Equipa de Certificação Azure. A oferta é analisada quanto a vírus, malware, conformidade de segurança e problemas de segurança. A oferta é verificada para ver se cumpre todos os critérios de elegibilidade. Para mais informações, consulte [os pré-requisitos.](./cpp-prerequisites.md) O feedback é fornecido se um problema for encontrado. |
| Validação test drive          | < 2 horas   | (Opcional) Se estiver presente um Test Drive, a Microsoft valida que pode ser implementado e replicado.  |
| Registo de embalagens e gerações de chumbo | < 1 hora  | Os ativos técnicos da oferta são embalados para uso do cliente e os sistemas de chumbo são configurados e implantados. |
|  Inscrição da editora             |  Manual    | Revisão final do editor e confirmação antes da oferta ir ao vivo. A oferta já está disponível para pré-visualização.  Pode implementar a sua oferta nas subscrições selecionadas (nas etapas de informação de oferta) para verificar se cumpre todos os seus requisitos.  Depois de verificar a oferta, selecione **Go Live** para que a sua oferta possa passar para o próximo passo. |
| Análise da Microsoft                | 7 - 14 dias | A Microsoft analisa holisticamente a sua aplicação Azure e envia-lhe e-mails caso sejam descobertos problemas.  A duração deste passo depende da complexidade da aplicação, das questões descobertas e da rapidez com que responde a eles.  |
| Em direto                           | < 1 dia | A oferta é lançada, replicada para as regiões especificadas, e disponibilizada ao público. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

Pode monitorizar o processo de publicação no separador **Status** para a sua oferta no Portal do Parceiro cloud.

![Separador de estado para uma oferta de aplicativo Sino](./media/offer-status-tab.png)

Depois de terminar o processo de publicação, a sua oferta será listada na categoria de [aplicação microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/).

>[!Note]
>O canal parceiro Cloud Solution Providers (CSP) já está disponível.  Consulte os [Fornecedores de Soluções cloud](../../cloud-solution-providers.md) para obter mais informações sobre o marketing da sua oferta através dos canais parceiros do Microsoft CSP.

## <a name="errors-and-review-feedback"></a>Erros e feedback de revisão

Além de apresentar o estado de publicação da sua oferta, o separador **Status** também exibe mensagens de erro e feedback de quaisquer etapas de publicação onde um problema é encontrado.  Se a questão for crítica, então a publicação é cancelada.  Em seguida, deve corrigir a ou as questões relatadas e republicar a oferta.  Uma vez que o passo de revisão da **Microsoft** representa uma revisão extensiva da sua oferta e dos seus ativos técnicos associados (especialmente o modelo do Gestor de Recursos Do Azure), as questões são normalmente apresentadas como links de pedido de puxão (PR).  Uma explicação de como ver e responder a estes PRs, ver feedback de [revisão de handling](./cpp-handling-review-feedback.md).


## <a name="next-steps"></a>Passos seguintes

Se encontrou erros numa ou mais das etapas editoriais, deve corrigi-los e reeditar a sua oferta.  Se os problemas críticos forem encontrados na etapa de revisão da **Microsoft,** deve [lidar com o feedback de revisão](./cpp-handling-review-feedback.md) acedendo ao repositório Azure DevOps da equipa de revisão da Microsoft.

Uma vez publicada uma aplicação Azure com sucesso, pode [atualizar a oferta existente](./cpp-update-existing-offer.md) para refletir a mudança de requisitos de negócio ou técnicos. 
