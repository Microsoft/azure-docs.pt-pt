---
title: Publicar a oferta de aplicativo do Azure | Azure Marketplace
description: Descreve o processo e as etapas para publicar uma oferta de aplicativo do Azure no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: pabutler
ms.openlocfilehash: 0005760a16f5109ca3555df5c5c5137facc84c40
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826151"
---
# <a name="publish-azure-application-offer"></a>Publicar oferta de aplicação do Azure

Depois de criar uma oferta fornecendo as informações na nova página de **oferta** , você pode publicar a oferta. Selecione **publicar** para iniciar o processo de publicação.

O diagrama a seguir mostra as etapas principais no processo de publicação para uma oferta para "entrar ao vivo".

![Etapas de publicação da oferta](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>Descrição detalhada das etapas de publicação

A tabela a seguir lista e descreve cada etapa de publicação e fornece uma estimativa de tempo para concluir cada etapa.  As estimativas de horários em "dias" são definidas como dias úteis, o que exclui os fins de semana e feriados.

|  **Etapa de publicação**           | **Momento**    | **Descrição**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Validar pré-requisitos         | < 15 min    | As informações da oferta e as configurações da oferta são validadas.                        |
| Validar configurações de receita influenciadas | < 15 min  | A atribuição de uso de recursos do Azure para a oferta está marcada.             |
| Certificação                  | < 1 dia     | A oferta é analisada pela equipe de certificação do Azure. A oferta é verificada em busca de vírus, malware, conformidade de segurança e problemas de segurança. A oferta é verificada para ver se ela atende a todos os critérios de qualificação. Para obter mais informações, consulte [pré-requisitos](./cpp-prerequisites.md). Os comentários serão fornecidos se um problema for encontrado. |
| Validação da unidade de teste          | < 2 horas   | Adicional Se um test drive estiver presente, a Microsoft validará que ele pode ser implantado e replicado.  |
| Empacotamento e registro de geração de cliente potencial | < 1 hora  | Os ativos técnicos da oferta são empacotados para uso do cliente e os sistemas de Lead são configurados e implantados. |
|  Aprovação do Publicador             |  Manual    | Revisão final do Publicador e confirmação antes que a oferta fique ativa. A oferta agora está disponível para visualização.  Você pode implantar sua oferta nas assinaturas selecionadas (nas etapas de informações da oferta) para verificar se ela atende a todos os seus requisitos.  Depois de verificar a oferta, selecione **Go Live** para que sua oferta possa passar para a próxima etapa. |
| Análise da Microsoft                | 7-14 dias | A Microsoft revisará seu aplicativo do Azure e enviará um email para você se os problemas forem descobertos.  O comprimento dessa etapa depende da complexidade do aplicativo, dos problemas descobertos e de como você responde a eles imediatamente.  |
| Em direto                           | < 1 dia | A oferta é liberada, replicada para as regiões especificadas e disponibilizada para o público. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

Você pode monitorar o processo de publicação na guia **status** para sua oferta no portal do Cloud Partner.

![Guia status de uma oferta de aplicativo do Azure](./media/offer-status-tab.png)

Depois de concluir o processo de publicação, sua oferta será listada na [categoria Microsoft Azure Marketplace aplicativo](https://azuremarketplace.microsoft.com/marketplace/apps/).

>[!Note]
>A aceitação do canal de parceiros do CSP (provedores de soluções na nuvem) já está disponível.  Consulte os [provedores de soluções de nuvem](../../cloud-solution-providers.md) para obter mais informações sobre como comercializar sua oferta por meio dos canais de parceiros do Microsoft CSP.

## <a name="errors-and-review-feedback"></a>Erros e comentários de revisão

Além de exibir o status de publicação da sua oferta, a guia **status** também exibe mensagens de erro e comentários de quaisquer etapas de publicação nas quais um problema é encontrado.  Se o problema for crítico, a publicação será cancelada.  Em seguida, você deve corrigir os problemas relatados e republicar a oferta.  Como a etapa de **revisão da Microsoft** representa uma análise extensiva de sua oferta e seus ativos técnicos associados (especialmente o modelo de Azure Resource Manager), os problemas normalmente são apresentados como links de solicitação de pull (PR).  Uma explicação de como exibir e responder a esses PRs, consulte [lidando com comentários de revisão](./cpp-handling-review-feedback.md).


## <a name="next-steps"></a>Passos seguintes

Se você encontrou erros em uma ou mais das etapas de publicação, você deve corrigi-los e republicar sua oferta.  Se forem encontrados problemas críticos na etapa de **revisão da Microsoft** , você deverá [lidar com os comentários de revisão](./cpp-handling-review-feedback.md) acessando o repositório do Azure DevOps da equipe de revisão da Microsoft.

Depois que um aplicativo do Azure for publicado com êxito, você poderá [atualizar a oferta existente](./cpp-update-existing-offer.md) para refletir a alteração dos requisitos técnicos ou de negócios. 
