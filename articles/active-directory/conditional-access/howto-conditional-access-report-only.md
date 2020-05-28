---
title: Configure uma política de acesso condicional em modo apenas de relatório - Diretório Ativo Azure
description: Utilização do modo apenas de relatório no acesso condicional aos auxílios na adoção
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46a00d55c58992be1009da1de5441ebe4e589a70
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83994974"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode"></a>Configure uma política de acesso condicional em modo apenas de relatório

Para configurar uma política de acesso condicional no modo apenas para relatórios:

> [!IMPORTANT]
> Se a sua organização ainda não o fez, instale a [integração do Monitor Azure com a Azure AD.](#set-up-azure-monitor-integration-with-azure-ad) Este processo deve ser realizado antes de os dados estarem disponíveis para revisão.

1. Assine no **portal Azure** como administrador de Acesso Condicional, administrador de segurança ou administrador global.
1. Navegue até ao Acesso Condicional de Segurança **do Diretório Ativo do Azure.**  >  **Security**  >  **Conditional Access**
1. Selecione **Nova política.**
1. Configure as condições de política e os controlos de subvenção necessários, se necessário.
1. No âmbito **da política enable,** deset o alternância para o modo apenas para **reportar.**
1. Selecione **Guardar**

> [!TIP]
> Pode editar o estado **político Enable** de uma política existente de **On-to** Report apenas, mas **fazê-lo** irá desativar a aplicação da política. 

Ver resultado apenas de relatório em registos de entrada de AD Azure.

Ver o resultado de uma política apenas de relatório para um determinado sign-in:

1. Assine no **portal Azure** como leitor de relatórios, leitor de segurança, administrador de segurança ou administrador global.
1. Navegue **Azure Active Directory**nos  >  **Sign-ins**de Diretório Ativo azure .
1. Selecione um sessão ou adicione filtros para obter resultados estreitos.
1. Na gaveta **detalhes,** selecione o separador **apenas** para ver as políticas avaliadas durante o sessão.

> [!NOTE]
> Ao descarregar os registos de login, escolha o formato JSON para incluir dados de resultados apenas de Acesso Condicional.

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>Criar integração do Monitor Azure com a Azure AD

Para ver o impacto agregado das políticas de Acesso Condicional utilizando o novo livro de informação de acesso condicional, deve integrar o Monitor Azure com a AD Azure e exportar os registos de entrada. Há dois passos para estabelecer esta integração: 

1. [Inscreva-se para uma subscrição do Monitor Azure e crie um espaço de trabalho.](/azure/azure-monitor/learn/quick-create-workspace)
1. [Exportar os registos de entrada de Azure AD para o Monitor Azure](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

Mais informações sobre os preços do Monitor Azure podem ser encontradas na página de preços do [Monitor Do Azure](https://azure.microsoft.com/pricing/details/monitor/). Os recursos para estimar os custos, definir um limite diário, ou personalizar o período de retenção de dados, podem ser encontrados no artigo, Gerir o [uso e os custos com os Registos do Monitor Do Azure.](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment)

## <a name="view-conditional-access-insights-workbook"></a>Ver livro de trabalho De acesso condicional Insights

Depois de ter integrado os seus registos De AD Azure com o Monitor Azure, pode monitorizar o impacto das políticas de Acesso Condicional utilizando os novos livros de informação de acesso condicional.

1. Assine no **portal Azure** como administrador de segurança ou administrador global.
1. Navegue nos livros de trabalho de **diretório ativo azure.**  >  **Workbooks**
1. Selecione **Insights de Acesso Condicional**.
1. Selecione uma ou mais políticas da política de **acesso condicional.** Todas as políticas ativadas são selecionadas por defeito.
1. Selecione um intervalo de tempo (se o intervalo de tempo exceder o conjunto de dados disponível, o relatório mostrará todos os dados disponíveis). Depois de definir os parâmetros da Política de **Acesso Condicional** e do Intervalo de **Tempo,** o relatório carregará.
   1. Opcionalmente, procure utilizadores **ou** **Apps** individuais para reduzir o âmbito do relatório.
1. Selecione entre visualizar os dados na faixa horcada pelo número de utilizadores ou pelo número de inscrições.
1. Dependendo da **visão de Dados,** o **Resumo** de Impacto apresenta o número de utilizadores ou inscrições no âmbito dos parâmetros escolhidos, agrupados pelo número total, **Sucesso,** **Falha,** **Ação do Utilizador necessária,** e não **aplicada.** Selecione um azulejo para examinar os sign-ins de um determinado tipo de resultado. 
   1. Se alterou os parâmetros do livro, pode optar por guardar uma cópia para uso futuro. Selecione o ícone de salvamento na parte superior do relatório e forneça um nome e localização para guardar.
1. Desça para ver a avaria dos sign-ins para cada condição.
1. Consulte os Detalhes de Início de **Sessão** na parte inferior do relatório para investigar eventos individuais de inscrição filtrados por seleções acima.

> [!TIP] 
> Precisa de aprofundar uma consulta específica ou exportar os detalhes de inscrição? Selecione o botão à direita de qualquer consulta para abrir a consulta no Log Analytics. Selecione Exportação para exportação para CSV ou Power BI.

## <a name="common-problems-and-solutions"></a>Problemas e soluções comuns

### <a name="why-are-the-queries-in-the-workbook-failing"></a>Porque é que as perguntas no livro estão a falhar?

Os clientes notaram que as consultas por vezes falham se os espaços de trabalho errados ou múltiplos estão associados ao livro. Para corrigir este problema, clique em **Editar** na parte superior do livro e, em seguida, no equipamento Definições. Selecione e, em seguida, remova espaços de trabalho que não estejam associados ao livro. Deve haver apenas um espaço de trabalho associado a cada livro.

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>Porque é que o parâmetro de abandono das políticas de acesso condicional não contém as minhas políticas?

A queda das Políticas de Acesso Condicional é povoada consultando os mais recentes sign-ins durante um período de 4 horas. Se um inquilino não tiver nenhum sinal nas últimas 4 horas, é possível que a entrega esteja vazia. Se este atraso for um problema persistente, como em pequenos inquilinos com inscrições pouco frequentes, os administradores podem editar a consulta para a queda das Políticas de Acesso Condicional e estender o tempo para a consulta para um tempo superior a 4 horas.

## <a name="next-steps"></a>Próximos passos

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

Para obter mais informações sobre os livros de adcôntos da Azure, consulte o artigo, [Como utilizar os livros de trabalho do Azure Monitor para relatórios de Diretório Ativo Azure.](../reports-monitoring/howto-use-azure-monitor-workbooks.md)
