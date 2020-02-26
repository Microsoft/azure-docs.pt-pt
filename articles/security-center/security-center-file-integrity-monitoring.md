---
title: Monitorização da Integridade do Arquivo no Centro de Segurança Azure Microsoft Docs
description: Saiba como configurar a Monitorização da Integridade do Ficheiro (FIM) no Centro de Segurança Azure utilizando esta passagem.
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
ms.openlocfilehash: 4d65ca8d97e1cca81886259d4f15cc880e45be9c
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604282"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Monitorização da integridade do ficheiro no Centro de Segurança Azure
Saiba como configurar a Monitorização da Integridade do Ficheiro (FIM) no Centro de Segurança Azure utilizando esta passagem.

## <a name="what-is-fim-in-security-center"></a>O que é fim no Centro de Segurança?
Monitorização da Integridade do Ficheiro (FIM), também conhecida como monitorização de alterações, examina ficheiros e registos de sistema operativo, software de aplicação e outros para alterações que possam indicar um ataque. Um método de comparação é usado para determinar se o estado atual do ficheiro é diferente da última digitalização do ficheiro. Pode aproveitar esta comparação para determinar se foram feitas modificações válidas ou suspeitas nos seus ficheiros.

A Monitorização da Integridade do Ficheiro do Security Center valida a integridade dos ficheiros Windows, registo do Windows e ficheiros Linux. Selecione os ficheiros que deseja monitorizados, permitindo o FIM. O Security Center monitoriza ficheiros com O FIM habilitados para atividades como:

- Criação e remoção de ficheiros e registos
- Modificações de ficheiros (alterações no tamanho dos ficheiros, listas de controlo de acesso e hash do conteúdo)
- Modificações de registo (alterações de tamanho, listas de controlo de acesso, tipo e conteúdo)

O Security Center recomenda que as entidades monitorizem, que pode facilmente ativar o FIM. Também pode definir as suas próprias políticas ou entidades DO FIM para monitorizar. Esta passagem mostra-lhe como.

> [!NOTE]
> A funcionalidade de Monitorização da Integridade do Ficheiro (FIM) funciona para computadores Windows e Linux e VMs e está disponível no nível Standard de Security Center. Veja [Preços](security-center-pricing.md) para saber mais sobre os escalões de preços do Centro de Segurança. O FIM envia dados para o espaço de trabalho do Log Analytics. Aplicam-se os encargos com os dados que envia. Consulte os preços do [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) para saber mais.

O FIM utiliza a solução Azure Change Tracking para rastrear e identificar mudanças no seu ambiente. Quando a monitorização da integridade do ficheiro estiver ativada, tem um recurso de rastreio de **alteração** de tipo **solution**. Para obter detalhes de frequência de recolha de dados, consulte os detalhes da recolha de [dados de Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking#change-tracking-data-collection-details) para o Rastreio de Alterações De Azure.

> [!NOTE]
> Se remover o recurso **Change Tracking,** também irá desativar a função de monitorização da integridade do ficheiro no Centro de Segurança.

## <a name="which-files-should-i-monitor"></a>Que ficheiros devo monitorizar?
Deve pensar nos ficheiros que são fundamentais para o seu sistema e aplicações ao escolher quais ficheiros monitorizar. Considere escolher ficheiros que não espera mudar sem planear. A escolha de ficheiros que são frequentemente alterados por aplicações ou sistema operativo (como ficheiros de registo e ficheiros de texto) criam muito ruído que dificulta a identificação de um ataque.

O Security Center recomenda quais os ficheiros que deve monitorizar como padrão de acordo com padrões de ataque conhecidos que incluem alterações de ficheiros e registos.

## <a name="using-file-integrity-monitoring"></a>Utilização da monitorização da integridade do ficheiro
1. Abra o dashboard **Centro de Segurança**.
2. No painel esquerdo sob **a Advanced Cloud Defense,** selecione **Monitorização da Integridade**do Ficheiro .
![Centro de Segurança][1]

**Abre-se a Monitorização da Integridade do Ficheiro.**
  ![Centro de Segurança][2]

São fornecidas as seguintes informações para cada espaço de trabalho:

- Número total de alterações ocorridas na última semana (pode ver um traço "-" se o FIM não estiver ativado no espaço de trabalho)
- Número total de computadores e VMs reportando ao espaço de trabalho
- Localização geográfica do espaço de trabalho
- Assinatura Azure que o espaço de trabalho está em

Os seguintes botões também podem ser mostrados para um espaço de trabalho:

- ![Ativar o ícone][3] Indica que o FIM não está ativado para o espaço de trabalho. A seleção do espaço de trabalho permite ativar o FIM em todas as máquinas sob o espaço de trabalho.
- ![ícone do plano de upgrade][4] Indica que o espaço de trabalho ou subscrição não está a funcionar sob o nível Padrão do Security Center. Para utilizar a funcionalidade FIM, a sua subscrição deve estar a executar o Standard.  A seleção do espaço de trabalho permite-lhe atualizar para Standard. Para saber mais sobre o nível Standard e como fazer upgrade, consulte upgrade para o [nível Standard do Security Center para uma maior segurança](security-center-pricing.md).
- Um espaço em branco (não há botão) significa que o FIM já está ativado no espaço de trabalho.

Sob **monitorização da integridade**do ficheiro, pode selecionar um espaço de trabalho para ativar o FIM para esse espaço de trabalho, ver o painel de monitorização da integridade do ficheiro para esse espaço de trabalho ou [atualizar](security-center-pricing.md) o espaço de trabalho para Standard.

## <a name="enable-fim"></a>Ativar fim
Para permitir o FIM num espaço de trabalho:

1. Sob **monitorização da integridade**do ficheiro, selecione um espaço de trabalho com o botão **Ativa.**
2. Ativar a **monitorização da integridade** do ficheiro abre com o número de máquinas Windows e Linux sob o espaço de trabalho.

   ![Ativar a monitorização da integridade do ficheiro][5]

   As definições recomendadas para Windows e Linux também estão listadas.  Expandir **ficheiros Windows**, **Registo**e **Ficheiros Linux** para ver a lista completa de itens recomendados.

3. Desfaça o controlo de quaisquer entidades recomendadas a que não pretenda aplicar o FIM.
4. Selecione **Aplicar a monitorização da integridade do ficheiro** para ativar o FIM.

> [!NOTE]
> Pode alterar as definições a qualquer momento. Consulte as entidades monitorizadas do Edit abaixo para saber mais.
>
>

## <a name="view-the-fim-dashboard"></a>Ver o painel fim
O painel de monitorização da **integridade** do Ficheiro apresenta-se para espaços de trabalho onde o FIM está ativado. O painel fim abre depois de ativar o FIM num espaço de trabalho ou quando seleciona um espaço de trabalho na janela de monitorização da **integridade** do ficheiro que já tem o FIM ativado.

![Painel de monitorização da integridade do ficheiro][6]

O painel FIM para um espaço de trabalho apresenta os seguintes detalhes:

- Número total de máquinas ligadas ao espaço de trabalho
- Número total de alterações ocorridas durante o período de tempo selecionado
- Uma repartição do tipo de alteração (ficheiros, registo)
- Uma repartição da categoria de alteração (modificada, adicionada, removida)

Selecionar filtro na parte superior do painel de instrumentos permite-lhe aplicar o período de tempo para o qual pretende ver alterações.

![Filtro de período de tempo][7]

O separador **Computadores** (mostrado acima) lista todas as máquinas que reportam a este espaço de trabalho. Para cada máquina, o painel de instrumentos lista:

- Alterações totais ocorridas durante o período de tempo selecionado
- Uma repartição das alterações totais à medida que alterações de ficheiros ou alterações no registo

**Iniciar o registo A pesquisa** abre-se quando introduz um nome de máquina no campo de pesquisa ou selecione uma máquina listada no separador Computadores. Pode expandir uma mudança para mais informações.

![Pesquisa de Registos][8]

O separador **Alterações** (mostrado abaixo) lista todas as alterações para o espaço de trabalho durante o período de tempo selecionado. Para cada entidade que foi alterada, o painel de instrumentos enumera o:

- Computador que a mudança ocorreu em
- Tipo de alteração (registo ou ficheiro)
- Categoria de alteração (modificada, adicionada, removida)
- Data e hora da mudança

![Alterações para o espaço de trabalho][9]

**Alterar os detalhes** abre-se quando introduzir uma alteração no campo de pesquisa ou selecione uma entidade listada no separador **Alterações.**

![Alterar detalhes][10]

## <a name="edit-monitored-entities"></a>Editar entidades monitorizadas

1. Volte ao painel de monitorização da **integridade** do ficheiro e selecione **Definições**.

   ![Definições][11]

   **Workspace Configuração** abre exibindo três separadores: **Registo do Windows,** **Ficheiros Windows**e **Ficheiros Linux**. Cada separador lista as entidades que pode editar nessa categoria. Para cada entidade listada, o Security Center identifica se o FIM está ativado (verdadeiro) ou não (falso).  Editar a entidade permite-lhe ativar ou desativar o FIM.

   ![Configuração de área de trabalho][12]

2. Selecione uma proteção de identidade. Neste exemplo, selecionamos um item no Windows Registry. **Edição para Change Tracking** abre.

   ![Editar ou alterar rastreio][13]

Em **Editar para Rastreio de Alterações** pode:

- Ativar (Verdadeiro) ou desativar (Falso) monitorização da integridade do ficheiro
- Fornecer ou alterar o nome da entidade
- Fornecer ou alterar o valor ou o caminho
- Eliminar a entidade, descartar a alteração ou guardar a alteração

## <a name="add-a-new-entity-to-monitor"></a>Adicione uma nova entidade para monitorizar
1. Volte ao painel de monitorização da **integridade** do Ficheiro e selecione **Definições** na parte superior. **Spacespace Configuração** abre.
2. No âmbito da **Configuração Workspace,** selecione o separador para o tipo de entidade que pretende adicionar: Registo do Windows, Ficheiros Windows ou Ficheiros Linux. Neste exemplo, selecionamos **ficheiros Linux**.

   ![Adicione um novo item para monitorizar][14]

3. Selecione **Adicionar**. **Adicione para change tracking** abre.

   ![Insira as informações solicitadas][15]

4. Na página **Adicionar,** escreva as informações solicitadas e selecione **Guardar**.

## <a name="disable-monitored-entities"></a>Desativar entidades monitorizadas
1. Volte ao painel de monitorização da integridade do **ficheiro.**
2. Selecione um espaço de trabalho onde o FIM esteja atualmente ativado. Um espaço de trabalho está ativado para o FIM se não tiver o botão Ativar ou o botão 'Plano de Actualização'.

   ![Selecione um espaço de trabalho onde o FIM está ativado][16]

3. Sob monitorização da integridade do ficheiro, selecione **Definições**.

   ![Selecione definições][17]

4. Sob **configuração do espaço**de trabalho, selecione um grupo onde o **Habilitado** estiver definido como verdadeiro.

   ![Configuração do espaço de trabalho][18]

5. Em **editar para alterar** o conjunto de janelas de rastreio **ativado** para falso.

   ![Conjunto habilitado a falso][19]

6. Selecione **Guardar**.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Monitorização de pastas e caminhos utilizando wildcards

Use wildcards para simplificar o rastreio através de diretórios. Aplicam-se as seguintes regras quando configura a monitorização de pastas utilizando wildcards:
-   Os wildcards são necessários para rastrear vários ficheiros.
-   Os wildcards só podem ser utilizados no último segmento de um caminho, como C:\folder\file ou /etc/*.conf
-   Se uma variável ambiental incluir um caminho que não é válido, a validação terá sucesso, mas o caminho falhará quando o inventário correr.
-   Ao definir o caminho, evite caminhos gerais como c:\*.* o que resultará em demasiadas pastas a serem atravessadas.

## <a name="disable-fim"></a>Desativar fim
Pode desativar o FIM. O FIM utiliza a solução Azure Change Tracking para rastrear e identificar mudanças no seu ambiente. Ao desativar o FIM, remova a solução Change Tracking do espaço de trabalho selecionado.

1. Para desativar o FIM, volte ao painel de monitorização da integridade do **ficheiro.**
2. Selecione uma área de trabalho.
3. Sob **monitorização da integridade**do ficheiro, selecione **Desativar**.

   ![Desativar fim][20]

4. Selecione **Remover** para desativar.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a usar a Monitorização da Integridade do Ficheiro (FIM) no Centro de Segurança. Para saber mais sobre o Centro de Segurança, consulte as seguintes páginas:

* [Definir políticas](tutorial-security-policy.md) de segurança -- Aprenda a configurar políticas de segurança para as suas subscrições e grupos de recursos Do Azure.
* [Gerir recomendações](security-center-recommendations.md) de segurança -- Saiba como as recomendações ajudam a proteger os seus recursos Azure.
* [Monitorização da saúde](security-center-monitoring.md)de segurança -- Aprenda a monitorizar a saúde dos seus recursos Azure.
* [Gerir e responder a alertas](security-center-managing-and-responding-alerts.md)de segurança --Aprender a gerir e responder a alertas de segurança.
* [Monitorizar as soluções](security-center-partner-solutions.md) de parceiros -- Aprenda a monitorizar o estado de saúde das soluções do seu parceiro.
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
