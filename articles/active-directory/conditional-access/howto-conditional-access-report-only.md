---
title: Configurar uma política de acesso condicional no modo somente de relatório-Azure Active Directory
description: Usando o modo somente de relatório no acesso condicional para auxiliar na adoção
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3cd57c09c08ab4c86feeca27915639123b439d0
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180306"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode-preview"></a>Configurar uma política de acesso condicional no modo somente de relatório (visualização)

Para configurar uma política de acesso condicional no modo somente de relatório:

1. Entre no **portal do Azure** como administrador de acesso condicional, administrador de segurança ou administrador global.
1. Navegue até **Azure Active Directory** > **segurança** > **acesso condicional**.
1. Selecione **nova política**.
1. Configure as condições da política e os controles de concessão necessários, conforme necessário.
1. Em **habilitar política** , defina alternar para o modo **somente relatório** .
1. Selecione **Guardar**

> [!TIP]
> Você pode editar o estado da **política de habilitação** de uma política existente de **em** para **somente relatório** , mas isso desabilitará a imposição de política. 

Exibir o resultado somente de relatório nos logs de entrada do Azure AD.

Para exibir o resultado de uma política somente de relatório para uma entrada específica:

1. Entre no **portal do Azure** como um leitor de relatórios, leitor de segurança, administrador de segurança ou administrador global.
1. Navegue até **Azure Active Directory** > **entradas**.
1. Selecione uma entrada ou adicione filtros para restringir os resultados.
1. Na gaveta de **detalhes** , selecione a guia **somente relatório (visualização)** para exibir as políticas avaliadas durante a entrada.

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>Configurar a integração do Azure Monitor com o Azure AD

Para exibir o impacto agregado das políticas de acesso condicional usando a nova pasta de trabalho de informações de acesso condicional, você deve integrar o Azure Monitor ao Azure AD e exportar os logs de entrada. Há duas etapas para definir essa integração: 

1. [Inscreva-se em uma assinatura Azure monitor e crie um espaço de trabalho](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).
1. [Exporte os logs de entrada do Azure ad para Azure monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

Mais informações sobre preços de Azure Monitor podem ser encontradas na [página de preços de Azure monitor](https://azure.microsoft.com/pricing/details/monitor/). Os recursos para estimar os custos, definir um limite diário ou personalizar o período de retenção de dados podem ser encontrados no artigo [gerenciar o uso e os custos com logs de Azure monitor](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment).

## <a name="view-conditional-access-insights-workbook"></a>Exibir pasta de trabalho de informações de acesso condicional

Depois de integrar os logs do Azure AD ao Azure Monitor, você poderá monitorar o impacto das políticas de acesso condicional usando as novas pastas de trabalho de acesso condicional do insights.

1. Entre no **portal do Azure** como um administrador de segurança ou administrador global.
1. Navegue até **Azure Active Directory** **pastas de trabalho**do > .
1. Selecione **informações de acesso condicional (versão prévia)** .
1. Selecione uma ou mais políticas na lista suspensa **política de acesso condicional** . Todas as políticas habilitadas são selecionadas por padrão.
1. Selecione um intervalo de tempo (se o intervalo de tempo exceder o conjunto de dados disponível, o relatório mostrará todos os dados disponíveis). Depois de definir a **política de acesso condicional** e os parâmetros de **intervalo de tempo** , o relatório será carregado.
   1. Opcionalmente, pesquise por **usuários** ou **aplicativos** individuais para restringir o escopo do relatório.
1. Selecione entre exibir os dados no intervalo de tempo pelo número de usuários ou o número de entradas.
1. Dependendo da exibição de **dados**, o **Resumo de impacto** exibe o número de usuários ou de entradas no escopo dos parâmetros escolhidos, agrupados por número total, **êxito**, **falha**, **ação do usuário necessária**e **não aplicado** . Selecione um bloco para examinar as entradas de um tipo de resultado específico. 
   1. Se você alterou os parâmetros da pasta de trabalho, poderá optar por salvar uma cópia para uso futuro. Selecione o ícone salvar na parte superior do relatório e forneça um nome e um local para salvar.
1. Role para baixo para exibir a divisão de entradas para cada condição.
1. Exiba os **detalhes de entrada** na parte inferior do relatório para investigar eventos de entrada individuais filtrados por seleções acima.

> [!TIP] 
> Precisa fazer uma busca detalhada em uma consulta específica ou exportar os detalhes de entrada? Selecione o botão à direita de qualquer consulta para abrir a consulta em Log Analytics. Selecione exportar para exportar para CSV ou Power BI.

## <a name="common-problems-and-solutions"></a>Problemas comuns e soluções

### <a name="why-are-the-queries-in-the-workbook-failing"></a>Por que as consultas na pasta de trabalho falham?

Os clientes perceberam que as consultas às vezes falham se os espaços de trabalho incorretos ou múltiplos estiverem associados à pasta de trabalho. Para corrigir esse problema, clique em **Editar** na parte superior da pasta de trabalho e, em seguida, na engrenagem configurações. Selecione e remova espaços de trabalho que não estão associados à pasta de trabalho. Deve haver apenas um espaço de trabalho associado a cada pasta de trabalho.

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>Por que o parâmetro DropDown de políticas de acesso condicional não contém minhas políticas?

A lista suspensa políticas de acesso condicional é populada consultando as entradas mais recentes em um período de 4 horas. Se um locatário não tiver nenhuma entrada nas últimas 4 horas, é possível que a lista suspensa esteja vazia. Se esse atraso for um problema persistente, como em locatários pequenos com entradas infrequentes, os administradores poderão editar a consulta para o menu suspenso políticas de acesso condicional e estender o tempo para a consulta a um tempo maior que 4 horas.

## <a name="next-steps"></a>Passos seguintes

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

Para obter mais informações sobre pastas de trabalho do Azure AD, consulte o artigo [como usar Azure monitor pastas de trabalho para relatórios Azure Active Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md).
