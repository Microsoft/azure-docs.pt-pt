---
title: Ligue os diagnósticos de bases de dados Azure SQL e os registos de auditoria ao Azure Sentinel
description: Saiba como ligar os registos de diagnóstico da base de dados Azure SQL e os registos de auditoria de segurança ao Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/06/2021
ms.author: yelevin
ms.openlocfilehash: a3a09ceffc75e2d396d7bd7aeedd97b7f2b6ec2b
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807738"
---
# <a name="connect-azure-sql-database-diagnostics-and-auditing-logs"></a>Ligar diagnósticos de base de dados Azure SQL e registos de auditoria

O Azure SQL é um motor de base de dados totalmente gerido, Plataforma-as-a-Service (PaaS) que lida com a maioria das funções de gestão de bases de dados, tais como upgrade, patching, backups e monitorização, sem o envolvimento do utilizador. 

O conector de base de dados Azure SQL permite-lhe transmitir os registos de auditoria e diagnóstico das suas bases de dados para o Azure Sentinel, permitindo-lhe monitorizar continuamente a atividade em todos os seus casos.

- A ligação de registos de diagnóstico permite-lhe enviar registos de diagnóstico de bases de dados de diferentes tipos de dados para o seu espaço de trabalho Azure Sentinel.

- A ligação de registos de auditoria permite-lhe transmitir registos de auditoria de segurança a partir de todas as bases de dados do Azure SQL ao nível do servidor.

Saiba mais sobre [a monitorização das bases de dados Azure SQL](../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissões no espaço de trabalho do Azure Sentinel.

- Para ligar registos de auditoria, deve ter lido e escrito permissões para as definições de auditoria do Azure SQL Server.

## <a name="connect-to-azure-sql-database"></a>Ligar à base de Dados SQL do Azure
    
1. A partir do menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. Selecione **Azure SQL Database** a partir da galeria de conectores de dados e, em seguida, selecione Abrir a Página do **Conector**  no painel de pré-visualização.

1. Na secção **de Configuração** da página do conector, note as duas categorias de registos que pode ligar.

### <a name="connect-diagnostics-logs"></a>Ligar registos de diagnósticos

1. Nos **registos de Diagnóstico**, **expanda manualmente os registos de diagnósticos em cada uma das suas bases de dados Azure SQL**.

1. Selecione o **open Azure SQL >** link para abrir a lâmina de recursos **Azure SQL.**

1. **(Opcional)** Para encontrar o seu recurso de base de dados facilmente, **selecione Adicione** o filtro na barra de filtros na parte superior.
    1. Na lista de recuo do **filtro,** selecione **o tipo de recurso**.
    1. Na  lista de drop-down value, desescolh-se **Selecione all** e, em seguida, selecione base **de dados SQL**.
    1. Clique em **Aplicar**.
    
1. Selecione o recurso de base de dados cujos registos de diagnóstico pretende enviar para Azure Sentinel.

    > [!NOTE]
    > Para cada recurso de base de dados cujos registos pretende recolher, deve repetir este processo, a partir deste passo.

1. A partir da página de recursos da base de dados selecionada, em **Monitorização** no menu de navegação, selecione **definições de Diagnóstico**.

    1. Selecione a ligação **de definição de diagnóstico + adicionar** na parte inferior da tabela.

    1. No ecrã de **definição de diagnóstico,** introduza um nome no campo **de definição de diagnóstico.**
    
    1. Na coluna **'Destino' detalha** a caixa de verificação do **espaço de trabalho Enviar para registar análise.** Dois novos campos serão exibidos por baixo. Escolha o espaço de trabalho **de Subscrição** e **Log Analytics** relevante (onde reside o Azure Sentinel).

    1. Na coluna de detalhes da **categoria,** marque as caixas de verificação dos tipos de registo e métrica que pretende ingerir. Recomendamos selecionar todos os tipos disponíveis tanto em **registo** como **em métrica.**

    1. **Selecione Guardar** na parte superior do ecrã.

- Em alternativa, pode utilizar o **script PowerShell** fornecido para ligar os registos de diagnóstico.
    1. Nos **registos de Diagnóstico**, expanda **ativar ativar o script PowerShell**.

    1. Copie o bloco de código e cole no PowerShell.

### <a name="connect-audit-logs"></a>Conecte registos de auditoria

1. Em **registos de auditoria (pré-visualização)**, expandir **Ative os registos de auditoria em todas as bases de dados Azure SQL (ao nível do servidor)**.

1. Selecione o **Open Azure SQL >** link para abrir a lâmina de recurso dos **servidores SQL.**

1. Selecione o servidor SQL cujos registos de auditoria pretende enviar para a Azure Sentinel.

    > [!NOTE]
    > Para cada recurso de servidor cujos registos pretende recolher, deve repetir este processo, a partir deste passo.

1. A partir da página de recursos do servidor selecionado, em **Segurança** no menu de navegação, selecione **Auditing**.

    1. Mover o **Enable Azure SQL Auditing alternar** para **ON**.

    1. No **destino de registo de auditoria**, selecione Log Analytics **(Preview)**.
    
    1. Da lista de espaços de trabalho que aparecem, escolha o seu espaço de trabalho (onde reside o Azure Sentinel).

    1. **Selecione Guardar** na parte superior do ecrã.

- Em alternativa, pode utilizar o **script PowerShell** fornecido para ligar os registos de diagnóstico.
    1. Em **registos de auditoria**, **expanda ativar o script Enable by PowerShell**.

    1. Copie o bloco de código e cole no PowerShell.


> [!NOTE]
>
> Com este conector de dados em particular, os indicadores de estado de conectividade (uma faixa de cor na galeria de conectores de dados e ícones de conexão ao lado dos nomes do tipo de dados) só mostrarão como *conectados* (verdes) se os dados ingerirem em algum ponto nas últimas duas semanas. Uma vez passadas duas semanas sem ingestão de dados, o conector mostrará como estando desligado. No momento em que mais dados chegam, o estado *ligado* voltará.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar os diagnósticos de base de dados do Azure SQL e a auditar registos ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)