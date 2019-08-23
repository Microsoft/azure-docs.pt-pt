---
title: Monitorizar a identidade e o acesso no Centro de Segurança do Azure | Microsoft Docs
description: Saiba como utilizar a capacidade de identidade e acesso no Centro de Segurança do Azure para monitorizar a atividade de acesso dos seus utilizadores e os problemas relacionados com identidades.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2018
ms.author: v-mohabe
ms.openlocfilehash: 1ef4d8e76b54b995a09decdfaf9974f0929c8ed9
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69905348"
---
# <a name="monitor-identity-and-access-in-azure-security-center-preview"></a>Monitorar identidade e acesso na central de segurança do Azure (versão prévia)
Este artigo ajuda-o a utilizar o Centro de Segurança do Azure para monitorizar a atividade de identidade e acesso dos utilizadores.

> [!NOTE]
> O link "exibir acesso à identidade *clássica* &" será desativado em 31 de julho de 2019. Clique [aqui](security-center-features-retirement-july2019.md#menu_classicidentity) para saber mais sobre serviços alternativos.

> [!NOTE]
> O monitoramento de identidade e acesso está em visualização e disponível somente na camada Standard da central de segurança. Veja [Preços](security-center-pricing.md) para saber mais sobre os escalões de preços do Centro de Segurança.
>

A identidade deve ser o plano de controlo da sua empresa e proteger a sua identidade deve ser a sua prioridade. O perímetro de segurança evoluiu de um perímetro de rede para um perímetro de identidade. A segurança se torna menos informando a defesa da sua rede e mais sobre como proteger seus dados, bem como gerenciar a segurança de seus aplicativos e usuários. Hoje em dia, com mais dados e mais aplicações a mudarem para a cloud, a identidade torna-se no novo perímetro.

Através da monitorização das atividades de identidade, poderá levar a cabo ações proativas antes da ocorrência de um incidente ou ações reativas para parar uma tentativa de ataque. O painel identidade & Access fornece recomendações como:

- Ativar o MFA para contas com privilégios na sua subscrição
- Remover contas externas com permissões de escrita da sua subscrição
- Remover as contas externas com privilégios da sua subscrição

> [!NOTE]
> Se sua assinatura tiver mais de 600 contas, a central de segurança não poderá executar as recomendações de identidade em relação à sua assinatura. As recomendações que não são executadas são listadas em "avaliações não disponíveis", que são discutidas abaixo.
A central de segurança não pode executar as recomendações de identidade em relação a agentes de administração do provedor de soluções de nuvem (CSP).
>

Consulte [recomendações](security-center-identity-access.md#recommendations) para obter uma lista das recomendações de identidade e acesso fornecidas pela central de segurança.

## <a name="monitoring-security-health"></a>Monitorizar o estado de funcionamento de segurança
Pode monitorizar o estado de segurança dos seus recursos na **Centro de segurança – descrição geral** dashboard. A seção de **recursos** é um indicador de integridade mostrando as severidades para cada tipo de recurso.

Pode ver uma lista de todos os problemas, selecionando **recomendações**. Em **recursos**, você pode exibir uma lista de problemas específicos para computação & aplicativos, segurança de dados, rede ou identidade & acesso. Para obter mais informações sobre como aplicar recomendações, veja [implementar recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md).

Para obter uma lista completa de recomendações de identidade e acesso, consulte [recomendações](security-center-identity-access.md#recommendations).

Para continuar, selecione **identidade & acesso** em **recursos** ou no menu principal da central de segurança.

![Dashboard Centro de Segurança][1]

## <a name="monitor-identity-and-access"></a>Monitorizar a identidade e o acesso
Em **identidade & acesso**, há duas guias:

- **Visão geral**: recomendações identificadas pela central de segurança.
- **Assinaturas**: lista de suas assinaturas e o estado de segurança atual de cada uma.

![Identidade e Acesso][2]

### <a name="overview-section"></a>Seção de visão geral
Em **visão geral**, há uma lista de recomendações. A primeira coluna indica a recomendação. A segunda coluna mostra o número total de assinaturas afetadas por essa recomendação. A terceira coluna mostra a gravidade do problema.

1. Selecione uma recomendação. A janela da recomendação é aberta e exibe:

   - Descrição da recomendação
   - Lista de assinaturas não íntegras e íntegras
   - Lista de recursos que não foram verificados devido a uma avaliação com falha ou que o recurso está em uma assinatura em execução na camada gratuita e não é avaliado

   ![Janela de recomendação][3]

1. Selecione uma assinatura na lista para obter detalhes adicionais.

### <a name="subscriptions-section"></a>Seção assinaturas
Em **assinaturas**, há uma lista de assinaturas. A primeira coluna lista as assinaturas. A segunda coluna mostra o número total de recomendações para cada assinatura. A terceira coluna mostra as severidades dos problemas.

![Guia da assinatura][4]

1. Selecione uma subscrição. É aberta uma vista de resumida com três separadores:

   - **Recomendações**: com base nas avaliações realizadas pelo centro de segurança que falharam.
   - **Passados avaliações**: lista de avaliações realizadas pelo centro de segurança passado.
   - **Avaliações indisponíveis**: lista de avaliações que não foram executadas devido a um erro ou porque a assinatura tem mais de 600 contas.

   Em **recomendações** é uma lista das recomendações para a assinatura e a severidade selecionadas de cada recomendação.

   ![Recomendações para selecionar assinatura][5]

1. Selecione uma recomendação para obter uma descrição da recomendação, uma lista de assinaturas não íntegras e íntegras e uma lista de recursos não verificados.

   ![Descrição da recomendação][6]

   Sob **transmitido avaliações** é uma lista de avaliações aprovadas.  Gravidade dessas avaliações é sempre verde.

   ![Avaliações aprovadas][7]

1. Selecione uma avaliação aprovada na lista para obter uma descrição da avaliação e uma lista de assinaturas íntegras. Há uma guia para assinaturas não íntegras que lista todas as assinaturas que falharam.

   ![Avaliações aprovadas][8]

## <a name="recommendations"></a>Recomendações
Use a tabela abaixo como referência para ajudá-lo a entender a identidade disponível & recomendações de acesso e o que cada uma delas faz se você a aplicar.

|Tipo de recurso|Classificação de segurança|Recomendação|Descrição|
|----|----|----|----|
|Subscription|50|A MFA deve ser habilitada em contas com permissões de proprietário em sua assinatura|Habilite a autenticação multifator (MFA) para todas as contas de assinatura com privilégios de administrador para evitar uma violação de contas ou recursos.|
|Subscription|40|A MFA deve ser habilitada em suas contas de assinatura com permissões de gravação|Habilite a autenticação multifator (MFA) para todas as contas de assinatura com privilégios de gravação para evitar uma violação de contas ou recursos.|
|Subscription|30|Contas externas com permissões de proprietário devem ser removidas da sua assinatura|Remova contas externas com permissões de proprietário da sua assinatura para impedir o acesso não monitorado.|
|Subscription|30|A MFA deve ser habilitada em suas contas de assinatura com permissões de leitura|Habilite a autenticação multifator (MFA) para todas as contas de assinatura com privilégios de leitura para evitar uma violação de contas ou recursos.|
|Subscription|25|As contas externas com permissões de escrita devem ser removidas da sua subscrição|Remova contas externas com permissões de gravação da sua assinatura para impedir o acesso não monitorado. |
|Subscription|20|Contas preteridas com permissões de proprietário devem ser removidas da sua assinatura|Remova contas preteridas com permissões de proprietário de suas assinaturas.|
|Subscription|5|As contas preteridas devem ser removidas da sua assinatura|Remova contas preteridas de suas assinaturas para habilitar o acesso somente aos usuários atuais. |
|Subscription|5|Deve haver mais de um proprietário atribuído à sua assinatura|Designe mais de um proprietário de assinatura para ter redundância de acesso de administrador.|
|Subscription|5|Um máximo de 3 proprietários deve ser designado para sua assinatura|Designe menos de 3 proprietários de assinatura para reduzir o potencial de violação por um proprietário comprometido.|
|Cofre de chaves|5|Os logs de diagnóstico no Key Vault devem ser habilitados|Ativar os registos e retenha-los até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida. |
|Subscription|15|Contas externas com permissões de leitura devem ser removidas da sua assinatura|Remova contas externas com privilégios de leitura de sua assinatura para impedir o acesso não monitorado.| 

> [!NOTE]
> Se você criou uma política de acesso condicional que exige MFA, mas tem exclusões definidas, a avaliação de recomendação do MFA da central de segurança considera a política como não compatível, pois permite que alguns usuários entrem no Azure sem MFA.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte o seguinte:

- [Proteger as máquinas e aplicações no Centro de Segurança do Azure](security-center-virtual-machine-protection.md)
- [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)
- [Protegendo o serviço e os dados do SQL Azure na central de segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerir alertas e responder a incidentes de segurança no Centro de Segurança.
* [Compreender os alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Centro de Segurança do Azure FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md). Encontre respostas para as perguntas mais frequentes sobre o Centro de Segurança.


<!--Image references-->
[1]: ./media/security-center-identity-access/overview.png
[2]: ./media/security-center-identity-access/identity-dashboard.png
[3]: ./media/security-center-identity-access/select-subscription.png
[4]: ./media/security-center-identity-access/subscriptions.png
[5]: ./media/security-center-identity-access/recommendations.png
[6]: ./media/security-center-identity-access/designate.png
[7]: ./media/security-center-identity-access/passed-assessments.png
[8]: ./media/security-center-identity-access/remove.png
