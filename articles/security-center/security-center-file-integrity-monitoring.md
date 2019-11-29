---
title: Monitoramento de integridade de arquivo na central de segurança do Azure | Microsoft Docs
description: Saiba como configurar o FIM (monitoramento de integridade de arquivo) na central de segurança do Azure usando este passo a passos.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: memildin
ms.openlocfilehash: cfa767a42800ceaf78fe007a8468fe9113a2d412
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74558792"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Monitoramento de integridade de arquivo na central de segurança do Azure
Saiba como configurar o FIM (monitoramento de integridade de arquivo) na central de segurança do Azure usando este passo a passos.

## <a name="what-is-fim-in-security-center"></a>O que é o FIM na central de segurança?
O FIM (monitoramento de integridade de arquivo), também conhecido como monitoramento de alterações, examina arquivos e registros de sistema operacional, software de aplicativo e outros para alterações que possam indicar um ataque. Um método de comparação é usado para determinar se o estado atual do arquivo é diferente do último exame do arquivo. Você pode aproveitar essa comparação para determinar se foram feitas modificações válidas ou suspeitas em seus arquivos.

O monitoramento de integridade de arquivo da central de segurança valida a integridade dos arquivos do Windows, do registro do Windows e dos arquivos do Linux. Você seleciona os arquivos que deseja monitorar habilitando o FIM. A central de segurança monitora arquivos com o FIM habilitado para atividades como:

- Criação e remoção de arquivo e registro
- Modificações de arquivo (alterações no tamanho do arquivo, listas de controle de acesso e hash do conteúdo)
- Modificações no registro (alterações no tamanho, listas de controle de acesso, tipo e conteúdo)

A central de segurança recomenda as entidades para monitorar, que você pode habilitar o FIM facilmente. Você também pode definir suas próprias políticas ou entidades do FIM para monitorar. Este passo a passos mostra como.

> [!NOTE]
> O recurso de monitoramento de integridade de arquivo (FIM) funciona para computadores e VMs com Windows e Linux e está disponível na camada Standard da central de segurança. Veja [Preços](security-center-pricing.md) para saber mais sobre os escalões de preços do Centro de Segurança. O FIM carrega dados no espaço de trabalho Log Analytics. Os encargos de dados se aplicam, com base na quantidade de dados que você carrega. Confira [log Analytics preços](https://azure.microsoft.com/pricing/details/log-analytics/) para saber mais.

O FIM usa a solução de Controle de Alterações do Azure para rastrear e identificar alterações no seu ambiente. Quando o monitoramento de integridade de arquivo estiver habilitado, você terá um recurso de **controle de alterações** do tipo **solução**. Para obter detalhes de frequência de coleta de dados, consulte [controle de alterações detalhes da coleta de dados](https://docs.microsoft.com/azure/automation/automation-change-tracking#change-tracking-data-collection-details) para controle de alterações do Azure.

> [!NOTE]
> Se você remover o recurso de **controle de alterações** , desabilitará também o recurso de monitoramento de integridade de arquivo na central de segurança.

## <a name="which-files-should-i-monitor"></a>Quais arquivos devo monitorar?
Você deve pensar nos arquivos que são críticos para seu sistema e aplicativos ao escolher quais arquivos monitorar. Considere escolher os arquivos que você não espera alterar sem planejamento. Escolher arquivos que são frequentemente alterados por aplicativos ou pelo sistema operacional (como arquivos de log e arquivos de texto) cria muitos ruídos que dificultam a identificação de um ataque.

A central de segurança recomenda quais arquivos você deve monitorar como padrão de acordo com os padrões de ataque conhecidos que incluem alterações de arquivo e registro.

## <a name="using-file-integrity-monitoring"></a>Usando o monitoramento de integridade de arquivo
1. Abra o dashboard **Centro de Segurança**.
2. No painel esquerdo, em **defesa de nuvem avançada**, selecione **monitoramento de integridade de arquivo**.
![painel da central de segurança][1]

O **monitoramento de integridade de arquivo** é aberto.
  ![painel da central de segurança][2]

As informações a seguir são fornecidas para cada espaço de trabalho:

- Número total de alterações ocorridas na última semana (você poderá ver um traço "-" se o FIM não estiver habilitado no espaço de trabalho)
- Número total de computadores e VMs relatando para o espaço de trabalho
- Localização geográfica do espaço de trabalho
- Assinatura do Azure em que o espaço de trabalho está

Os botões a seguir também podem ser mostrados para um espaço de trabalho:

- ![Habilitar ícone][3] Indica que o FIM não está habilitado para o espaço de trabalho. Selecionar o espaço de trabalho permite habilitar o FIM em todos os computadores no espaço de trabalho.
- ![ícone de plano de atualização][4] indica que o espaço de trabalho ou a assinatura não está em execução na camada Standard da central de segurança. Para usar o recurso FIM, sua assinatura deve estar executando o Standard.  Selecionar o espaço de trabalho permite que você atualize para o padrão. Para saber mais sobre a camada Standard e como atualizar, consulte [atualizar para a camada Standard da central de segurança para aumentar a segurança](security-center-pricing.md).
- Um espaço em branco (não há nenhum botão) significa que o FIM já está habilitado no espaço de trabalho.

Em **monitoramento de integridade de arquivo**, você pode selecionar um espaço de trabalho para habilitar o fim desse espaço de trabalho, exibir o painel de monitoramento de integridade de arquivo para esse espaço de trabalho ou [Atualizar](security-center-pricing.md) o espaço de trabalho para Standard.

## <a name="enable-fim"></a>Habilitar FIM
Para habilitar o FIM em um espaço de trabalho:

1. Em **monitoramento de integridade de arquivo**, selecione um espaço de trabalho com o botão **habilitar** .
2. **Habilitar o monitoramento de integridade de arquivo** abre exibindo o número de computadores Windows e Linux no espaço de trabalho.

   ![Habilitar o monitoramento de integridade de arquivo][5]

   As configurações recomendadas para Windows e Linux também estão listadas.  Expanda **arquivos do Windows**, o **registro**e os **arquivos do Linux** para ver a lista completa de itens recomendados.

3. Desmarque as entidades recomendadas às quais você não deseja aplicar o FIM.
4. Selecione **aplicar monitoramento de integridade de arquivo** para habilitar o fim.

> [!NOTE]
> Você pode alterar as configurações a qualquer momento. Consulte Editar entidades monitoradas abaixo para saber mais.
>
>

## <a name="view-the-fim-dashboard"></a>Exibir o painel do FIM
O painel **monitoramento de integridade de arquivo** é exibido para espaços de trabalho em que o fim está habilitado. O painel do FIM é aberto depois que você habilita o FIM em um espaço de trabalho ou quando seleciona um espaço de trabalho na janela **monitoramento de integridade de arquivo** que já tem o fim habilitado.

![Painel de monitoramento de integridade de arquivo][6]

O painel do FIM de um espaço de trabalho exibe os seguintes detalhes:

- Número total de computadores conectados ao espaço de trabalho
- Número total de alterações ocorridas durante o período de tempo selecionado
- Uma análise do tipo de alteração (arquivos, registro)
- Uma divisão da categoria de alteração (modificada, adicionada, removida)

Selecionar filtrar na parte superior do painel permite que você aplique o período de tempo para o qual você deseja ver as alterações.

![Filtro de período de tempo][7]

A guia **computadores** (mostrada acima) lista todos os computadores que se reportam a este espaço de trabalho. Para cada computador, o painel lista:

- Total de alterações ocorridas durante o período de tempo selecionado
- Uma divisão do total de alterações como alterações no arquivo ou alterações no registro

A **pesquisa de logs** é aberta quando você insere um nome de computador no campo de pesquisa ou seleciona um computador listado na guia computadores. a pesquisa de log exibe todas as alterações feitas durante o período de tempo selecionado para o computador. Você pode expandir uma alteração para obter mais informações.

![Pesquisa de Registos][8]

A guia **alterações** (mostrada abaixo) lista todas as alterações do espaço de trabalho durante o período de tempo selecionado. Para cada entidade que foi alterada, o painel lista o:

- Computador no qual a alteração ocorreu
- Tipo de alteração (registro ou arquivo)
- Categoria de alteração (modificada, adicionada, removida)
- Data e hora da alteração

![Alterações para o espaço de trabalho][9]

A **alteração de detalhes** é aberta quando você insere uma alteração no campo de pesquisa ou seleciona uma entidade listada na guia **alterações** .

![Alterar detalhes][10]

## <a name="edit-monitored-entities"></a>Editar entidades monitoradas

1. Retorne ao **painel Monitoramento de integridade de arquivo** e selecione **configurações**.

   ![Definições][11]

   A **configuração do espaço de trabalho** é aberta exibindo três guias: **registro do Windows**, **arquivos do Windows**e **arquivos do Linux**. Cada guia lista as entidades que você pode editar nessa categoria. Para cada entidade listada, a central de segurança identifica se o FIM está habilitado (true) ou não está habilitado (false).  A edição da entidade permite habilitar ou desabilitar o FIM.

   ![Configuração do espaço de trabalho][12]

2. Selecione uma proteção de identidade. Neste exemplo, selecionamos um item no registro do Windows. **Editar para controle de alterações** é aberto.

   ![Editar ou controlar alterações][13]

Em **Editar para controle de alterações** você pode:

- Habilitar (verdadeiro) ou desabilitar (falso) monitoramento de integridade do arquivo
- Fornecer ou alterar o nome da entidade
- Fornecer ou alterar o valor ou o caminho
- Excluir a entidade, descartar a alteração ou salvar a alteração

## <a name="add-a-new-entity-to-monitor"></a>Adicionar uma nova entidade a ser monitorada
1. Retorne ao **painel Monitoramento de integridade de arquivo** e selecione **configurações** na parte superior. A **configuração do espaço de trabalho** é aberta.
2. Em **configuração do espaço de trabalho**, selecione a guia para o tipo de entidade que você deseja adicionar: registro do Windows, arquivos do Windows ou arquivos do Linux. Neste exemplo, selecionamos **arquivos do Linux**.

   ![Adicionar um novo item a ser monitorado][14]

3. Selecione **Adicionar**. **Adicionar para controle de alterações** é aberto.

   ![Inserir informações solicitadas][15]

4. Na página **Adicionar** , digite as informações solicitadas e selecione **salvar**.

## <a name="disable-monitored-entities"></a>Desabilitar entidades monitoradas
1. Retorne ao painel **monitoramento de integridade de arquivo** .
2. Selecione um espaço de trabalho em que o FIM esteja habilitado no momento. Um espaço de trabalho será habilitado para o FIM se ele não tiver o botão habilitar ou o botão atualizar plano.

   ![Selecione um espaço de trabalho onde o FIM está habilitado][16]

3. Em monitoramento de integridade de arquivo, selecione **configurações**.

   ![Selecionar configurações][17]

4. Em **configuração do espaço de trabalho**, selecione um grupo onde **habilitado** está definido como true.

   ![Configuração do espaço de trabalho][18]

5. Em **Editar para controle de alterações conjunto de** janelas **habilitado** como falso.

   ![Definir habilitado como falso][19]

6. Selecione **Guardar**.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Monitoramento de pasta e caminho usando curingas

Use curingas para simplificar o rastreamento em diretórios. As regras a seguir se aplicam quando você configura o monitoramento de pasta usando curingas:
-   Caracteres curinga são necessários para acompanhar vários arquivos.
-   Os curingas só podem ser usados no último segmento de um caminho, como C:\folder\file ou/etc/*. conf
-   Se uma variável de ambiente incluir um caminho que não seja válido, a validação terá êxito, mas o caminho falhará quando o inventário for executado.
-   Ao definir o caminho, evite caminhos gerais, como c:\*. *, o que resultará em muitas pastas sendo percorridas.

## <a name="disable-fim"></a>Desabilitar FIM
Você pode desabilitar o FIM. O FIM usa a solução de Controle de Alterações do Azure para rastrear e identificar alterações no seu ambiente. Ao desabilitar o FIM, você remove a solução de Controle de Alterações do espaço de trabalho selecionado.

1. Para desabilitar o FIM, retorne ao painel de **monitoramento de integridade de arquivo** .
2. Selecione uma área de trabalho.
3. Em **monitoramento de integridade de arquivo**, selecione **desabilitar**.

   ![Desabilitar FIM][20]

4. Selecione **remover** para desabilitar.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, você aprendeu a usar o FIM (monitoramento de integridade de arquivo) na central de segurança. Para saber mais sobre a central de segurança, consulte as seguintes páginas:

* [Configurando políticas de segurança](tutorial-security-policy.md) – saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciando recomendações de segurança](security-center-recommendations.md) – saiba como as recomendações ajudam a proteger seus recursos do Azure.
* [Monitoramento de integridade de segurança](security-center-monitoring.md)– saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança](security-center-managing-and-responding-alerts.md)– saiba como gerenciar e responder a alertas de segurança.
* [Monitorando soluções de parceiros](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiros.
* [Perguntas frequentes da central de segurança](security-center-faq.md)– encontre perguntas frequentes sobre como usar o serviço.
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – Obtenha as notícias e informações mais recentes de segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[2]: ./media/security-center-file-integrity-monitoring/file-integrity-monitoring.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[6]: ./media/security-center-file-integrity-monitoring/fim-dashboard.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png
