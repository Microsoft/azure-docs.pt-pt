---
title: Configure uma política de acesso condicional no modo apenas de relatório - Azure Ative Directory
description: Utilização do modo de relatório apenas no acesso condicional à ajuda na adoção
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83994974"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode"></a>Configure uma política de acesso condicional no modo apenas de relatório

Para configurar uma política de acesso condicional no modo apenas de relatório:

> [!IMPORTANT]
> Se a sua organização ainda não o fez, [crie a integração do Azure Monitor com a Azure AD.](#set-up-azure-monitor-integration-with-azure-ad) Este processo deve ser realizado antes de os dados estarem disponíveis para revisão.

1. Inscreva-se no **portal Azure** como administrador de acesso condicional, administrador de segurança ou administrador global.
1. Navegue pelo Acesso Condicional de Segurança **do Diretório Ativo Azure**  >  **Security**  >  **Conditional Access**.
1. Selecione **Nova política**.
1. Configure as condições políticas e os controlos de subvenção necessários, se necessário.
1. No **modo "Ativar"** o alternância para o modo **apenas de relatório.**
1. Selecione **Guardar**

> [!TIP]
> Pode editar o estado **de política de Enable** de uma política existente apenas a **"On** to Report", mas fazê-lo irá desativar a aplicação da política. **Report-only** 

Ver resultado apenas do relatório em registos de inscrição AD Azure.

Para visualizar o resultado de uma política apenas de relatório para uma determinada entrada:

1. Inscreva-se no **portal Azure** como leitor de relatórios, leitor de segurança, administrador de segurança ou administrador global.
1. Navegue até a **Azure Ative Directory**  >  **Sign-ins**.
1. Selecione um insusição ou adicione filtros para reduzir os resultados.
1. Na gaveta **Detalhes,** selecione o **separador Relatório apenas** para visualizar as políticas avaliadas durante a sposição.

> [!NOTE]
> Ao descarregar os registos de login, escolha o formato JSON para incluir dados de resultados do relatório do Acesso Condicional.

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>Configurar a integração do Azure Monitor com a Azure AD

Para ver o impacto agregado das políticas de Acesso Condicional utilizando o novo manual de Acesso Condicional, deve integrar o Azure Monitor com Azure AD e exportar os registos de login. Há dois passos para estabelecer esta integração: 

1. [Inscreva-se para uma subscrição do Azure Monitor e crie um espaço de trabalho.](/azure/azure-monitor/learn/quick-create-workspace)
1. [Exporte os registos de login de Azure AD para Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

Mais informações sobre os preços do Azure Monitor podem ser encontradas na página de preços do [Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). Os recursos para estimar os custos, definir uma tampa diária, ou personalizar o período de retenção de dados, podem ser encontrados no artigo, [Gerir o uso e os custos com registos do Monitor Azure](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment).

## <a name="view-conditional-access-insights-workbook"></a>Ver livro de Insights de Acesso Condicional

Depois de integrar os seus registos AZure AD com o Azure Monitor, pode monitorizar o impacto das políticas de Acesso Condicional utilizando os novos livros de insights de acesso condicional.

1. Inscreva-se no **portal Azure** como administrador de segurança ou administrador global.
1. Consulte os livros de trabalho **do Azure Ative Directory**  >  **Workbooks**.
1. Selecione **Insights de Acesso Condicional**.
1. Selecione uma ou mais políticas a partir do dropdown **da Política de Acesso Condicional.** Todas as políticas ativadas são selecionadas por padrão.
1. Selecione um intervalo de tempo (se o intervalo de tempo exceder o conjunto de dados disponíveis, o relatório mostrará todos os dados disponíveis). Depois de definir os **parâmetros da Política de Acesso Condicional** e do intervalo de **tempo,** o relatório será carregado.
   1. Opcionalmente, procure por **Utilizadores** individuais ou **Apps** para reduzir o âmbito do relatório.
1. Selecione entre visualizar os dados no intervalo de tempo pelo número de utilizadores ou pelo número de inserções.
1. Dependendo da **visão de Dados,** o Resumo do **Impacto** apresenta o número de utilizadores ou inserções no âmbito dos parâmetros escolhidos, agrupados por número total, **sucesso,** **falha,** **ação do utilizador necessária,** e não **aplicada.** Selecione um azulejo para examinar as insuposições de um determinado tipo de resultado. 
   1. Se alterou os parâmetros do livro, pode optar por guardar uma cópia para utilização futura. Selecione o ícone de poupança no topo do relatório e forneça um nome e localização para guardar.
1. Percorra para baixo para ver a avaria de insiná-ins para cada condição.
1. Consulte os **Detalhes de Inscrição** na parte inferior do relatório para investigar eventos individuais de inscrição filtrados por seleções acima.

> [!TIP] 
> Precisa de aprofundar uma consulta específica ou exportar os detalhes de inscrição? Selecione o botão para o direito de qualquer consulta para abrir a consulta em Log Analytics. Selecione Exportação para exportação para CSV ou Power BI.

## <a name="common-problems-and-solutions"></a>Problemas e soluções comuns

### <a name="why-are-the-queries-in-the-workbook-failing"></a>Porque é que as consultas no livro estão a falhar?

Os clientes notaram que as consultas às vezes falham se os espaços de trabalho errados ou múltiplos estiverem associados ao livro. Para corrigir este problema, clique em **Editar** na parte superior do livro e, em seguida, na engrenagem Definições. Selecione e, em seguida, remova espaços de trabalho que não estejam associados ao livro. Deve haver apenas um espaço de trabalho associado a cada livro.

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>Porque é que o parâmetro de abandono das políticas de acesso condicional não contém as minhas políticas?

A queda das políticas de acesso condicional é povoada consultando os mais recentes acessos ao longo de um período de 4 horas. Se um inquilino não tiver nenhum sinal nas últimas 4 horas, é possível que a entrega esteja vazia. Se este atraso for um problema persistente, como em pequenos inquilinos com inscrições pouco frequentes, os administradores podem editar a consulta para as Políticas de Acesso Condicional e estender o tempo para a consulta para um tempo superior a 4 horas.

## <a name="next-steps"></a>Próximos passos

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

Para obter mais informações sobre os livros AZURE AD, consulte o artigo, [Como utilizar os livros do Azure Monitor para relatórios do Azure Ative Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md).
