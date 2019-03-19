---
title: Monitorizar a identidade e o acesso no Centro de Segurança do Azure | Microsoft Docs
description: Saiba como utilizar a capacidade de identidade e acesso no Centro de Segurança do Azure para monitorizar a atividade de acesso dos seus utilizadores e os problemas relacionados com identidades.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 6f8fadc42d1c725002f2552a3fc1fc98e8564437
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58098785"
---
# <a name="monitor-identity-and-access-in-azure-security-center-preview"></a>Monitorizar a identidade e acesso no Centro de segurança do Azure (pré-visualização)
Este artigo ajuda-o a utilizar o Centro de Segurança do Azure para monitorizar a atividade de identidade e acesso dos utilizadores.

> [!NOTE]
> Monitorizar a identidade e acesso está em pré-visualização e disponível apenas no escalão Standard do Centro de segurança. Veja [Preços](security-center-pricing.md) para saber mais sobre os escalões de preços do Centro de Segurança.
>

A identidade deve ser o plano de controlo da sua empresa e proteger a sua identidade deve ser a sua prioridade. O perímetro de segurança deixou de ser um perímetro de rede para um perímetro de identidade. A segurança se torna menos sobre defesa de rede e mais informações sobre a Defender seus dados, bem como gerir a segurança das suas aplicações e os utilizadores. Hoje em dia, com mais dados e mais aplicações a mudarem para a cloud, a identidade torna-se no novo perímetro.

Através da monitorização das atividades de identidade, poderá levar a cabo ações proativas antes da ocorrência de um incidente ou ações reativas para parar uma tentativa de ataque. O dashboard de identidade e acesso fornece recomendações, tais como:

- Ativar o MFA para contas com privilégios na sua subscrição
- Remover contas externas com permissões de escrita da sua subscrição
- Remover as contas externas com privilégios da sua subscrição

> [!NOTE]
> Se a sua subscrição tiver mais de 600 contas, o Centro de segurança não consegue executar as recomendações de identidade na sua subscrição. Recomendações que são executadas não estão listadas na "avaliações indisponíveis" que é abordada abaixo.
Centro de segurança não consegue executar as recomendações de identidade em agentes de administrador de um parceiro de fornecedor de soluções Cloud (CSP).
>
>

Ver [recomendações](security-center-identity-access.md#recommendations) para obter uma lista das recomendações de acesso e identidades fornecidas pelo centro de segurança.

## <a name="monitoring-security-health"></a>Monitorizar o estado de funcionamento de segurança
Pode monitorizar o estado de segurança dos seus recursos na **Centro de segurança – descrição geral** dashboard. O **recursos** secção é um indicador de estado de funcionamento que mostra as gravidades para cada tipo de recurso.

Pode ver uma lista de todos os problemas, selecionando **recomendações**. Sob **recursos**, pode exibir uma lista dos problemas específicos de computação e aplicações, segurança de dados, redes, ou identidade e acesso. Para obter mais informações sobre como aplicar recomendações, veja [implementar recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md).

Para obter uma lista completa das recomendações de identidades e acessos, consulte [recomendações](security-center-identity-access.md#recommendations).

Para continuar, selecione **identidade e acesso** sob **recursos** ou o menu principal do Centro de segurança.

![Dashboard Centro de Segurança][1]

## <a name="monitor-identity-and-access"></a>Monitorizar a identidade e o acesso
Sob **identidade e acesso**, há duas guias:

- **Descrição geral**: recomendações identificado pelo centro de segurança.
- **Subscrições**: lista das suas subscrições e o estado de segurança atual de cada.

![Identidade e Acesso][2]

### <a name="overview-section"></a>Seção de visão geral
Sob **descrição geral**, há uma lista de recomendações. A primeira coluna indica a recomendação. A segunda coluna mostra o número total de subscrições são afetadas por essa recomendação. A terceira coluna mostra a gravidade do problema.

1. Selecione uma recomendação. A recomendação é apresentada a janela e apresenta:

   - Descrição da recomendação
   - Lista de subscrições de mau e bom estado de funcionamento
   - Lista de recursos não verificados devido a uma avaliação com falhas ou o recurso está sob a uma subscrição em execução no escalão gratuito e não é avaliada

   ![Janela da recomendação][3]

1. Selecione uma subscrição na lista para obter detalhes adicionais.

### <a name="subscriptions-section"></a>Secção de subscrições
Sob **subscrições**, há uma lista de subscrições. A primeira coluna apresenta uma lista de subscrições. A segunda coluna mostra o número total de recomendações para cada subscrição. A terceira coluna mostra a gravidade dos problemas.

![Separador da subscrição][4]

1. Selecione uma subscrição. É aberta uma vista de resumida com três separadores:

   - **Recomendações**: com base nas avaliações realizadas pelo centro de segurança que falharam.
   - **Passados avaliações**: lista de avaliações realizadas pelo centro de segurança passado.
   - **Avaliações indisponíveis**: lista de avaliações de que não foi possível executar devido a um erro ou porque a subscrição tiver mais de 600 contas.

   Sob **recomendações** é uma lista de recomendações para a subscrição selecionada e a gravidade de cada recomendação.

   ![Recomendações para selecionar a subscrição][5]

1. Selecione uma recomendação para obter uma descrição da recomendação, uma lista de subscrições mau e bom estado de funcionamento e uma lista de recursos não verificados.

   ![Descrição da recomendação][6]

   Sob **transmitido avaliações** é uma lista de avaliações aprovadas.  Gravidade dessas avaliações é sempre verde.

   ![Avaliações aprovadas][7]

1. Selecione uma avaliação com êxito da lista para obter uma descrição da avaliação e uma lista das inscrições em bom estado. Há uma guia para subscrições de mau estado de funcionamento que apresenta uma lista de todas as subscrições que falharam.

   ![Avaliações aprovadas][8]

## <a name="recommendations"></a>Recomendações
Utilize a tabela abaixo como referência para ajudar a compreender as recomendações de identidade e acesso disponíveis e o que cada um deles faz se as aplicar.

|Tipo de recurso|Classificação de segurança|Recomendação|Descrição|
|----|----|----|----|
|Subscrição|50|Ativar a MFA para contas de aplicação de gestão do Azure com permissões de proprietário na sua subscrição|Ative a multi-factor Authentication (MFA) para todas as contas de subscrição com privilégios de administrador para evitar falhas de segurança de contas ou recursos.|
|Subscrição|50|Ativar o Centro de segurança nas suas subscrições |Ativar o Centro de segurança em todas as suas subscrições para deteção de ameaças avançada, JIT, permissões de aplicação e recomendações avançadas |
|Subscrição|50|Ativar o escalão standard do Centro de segurança nas suas subscrições |Ative o Centro de segurança escalão Standard em todas as suas subscrições para deteção de ameaças avançada, JIT, permissões de aplicação e recomendações avançadas.|
|Subscrição|40|Ativar a MFA para contas de aplicação de gestão do Azure com permissões de escrita na sua subscrição|Ative a multi-factor Authentication (MFA) para todas as contas de subscrição com privilégios de escrita para evitar uma violação de contas ou recursos.|
|Subscrição|30|Remover contas externas com permissões de proprietário da sua subscrição|Remova contas externas com permissões de proprietário da sua subscrição para impedir o acesso não monitorizado. |
|Subscrição|30|Ativar a MFA para contas de aplicação de gestão do Azure com permissões de leitura na sua subscrição|Ative a multi-factor Authentication (MFA) para todas as contas de subscrição com privilégios de leitura para evitar uma violação de contas ou recursos.|
|Subscrição|25|Remover contas externas com permissões de escrita da sua subscrição|Remova contas externas com permissões de escrita da sua subscrição para impedir o acesso não monitorizado. |
|Subscrição|20|Remover contas preteridas com permissões de proprietário da sua subscrição|Remova contas preteridas com permissões de proprietário a partir das subscrições.|
|Subscrição|5|Remover contas preteridas da sua subscrição|Remova contas preteridas das suas subscrições para permitir o acesso aos utilizadores apenas atuais. |
|Subscrição|5|Designar mais do que um proprietário na sua subscrição|Designe mais do que um proprietário da subscrição para ter a redundância de acesso de administrador.|
|Subscrição|5|Designar até 3 proprietários na sua subscrição|Designe menos de 3 proprietários de subscrições para reduzir a possibilidade de violação por um proprietário comprometido.|
|Key Vault|5|Ativar registos de diagnóstico no Cofre de chaves|Ativar os registos e retenha-los até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida. |
|Subscrição|15|Remover contas externas com permissões de leitura da sua subscrição|Remova contas externas com privilégios de leitura da sua subscrição para impedir o acesso não monitorizado.|
|Subscrição|1|Disponibilizar detalhes de contacto de segurança|Fornece informações de contacto de segurança para cada uma das suas subscrições. Informações de contacto são um e-mail endereço e número de telefone. As informações são utilizadas para contactá-lo se a nossa equipa de segurança Descubra que os recursos estão comprometidos|

> ! [NOTA] Se tiver criado uma política de acesso condicional que exige o MFA, mas tem as exclusões definidas, a avaliação de recomendação do MFA do Centro de segurança considera a política de conformidade, porque ela permite que alguns usuários iniciar sessão no Azure sem MFA.
>

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte o seguinte:

- [Proteger as máquinas e aplicações no Centro de segurança do Azure](security-center-virtual-machine-recommendations.md)
- [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)
- [Proteger os dados no Centro de segurança do Azure e o serviço SQL do Azure](security-center-sql-service-recommendations.md)

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
