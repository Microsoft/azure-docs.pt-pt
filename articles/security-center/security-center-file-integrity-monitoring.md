---
title: Ficheiro de monitorização da integridade no Centro de segurança do Azure | Documentos da Microsoft
description: " Aprenda a ativar a monitorização da integridade de ficheiros no Centro de segurança do Azure. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: bb987bcc38dee1f3d4ea2fce19e5e546ebfc8f7c
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57240240"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Monitorização no Centro de segurança do Azure da integridade de ficheiros
Saiba como configurar o monitoramento de integridade de ficheiros (FIM) no Centro de segurança do Azure com estas instruções.

## <a name="what-is-fim-in-security-center"></a>O que é o FIM no Centro de segurança?
Ficheiro de integridade de monitorização (FIM), também conhecida como monitoramento de alterações, examina os arquivos e registros de sistema operativo, software de aplicação e outras alterações que podem indicar um ataque. Um método de comparação é utilizado para determinar se o estado atual do ficheiro é diferente da última análise do ficheiro. Pode aproveitar esta comparação para determinar se foram efetuadas modificações válidas ou suspeitas aos seus ficheiros.

Monitorização da integridade de ficheiros do Centro de segurança valida a integridade de ficheiros do Windows, o Registro do Windows e o ficheiros do Linux. Selecione os ficheiros que pretende que sejam monitorizados, permitindo que o FIM. Centro de segurança monitoriza ficheiros com o FIM ativado para a atividade, tais como:

- Criação de arquivos e do Registro e remoção
- Modificações do ficheiro (alterações no tamanho do ficheiro, listas de controlo de acesso e de hash do conteúdo)
- Modificações no Registro (alterações no tamanho, listas de controlo de acesso, tipo e o conteúdo)

Centro de segurança recomenda entidades para monitorizar, que é possível habilitar facilmente FIM no. Também pode definir suas próprias políticas FIM ou entidades para monitorizar. Estas instruções mostram como.

> [!NOTE]
> A funcionalidade de monitoramento de integridade de ficheiros (FIM) funciona para VMs e computadores Windows e Linux e está disponível no escalão Standard do Centro de segurança. Veja [Preços](security-center-pricing.md) para saber mais sobre os escalões de preços do Centro de Segurança.
FIM carrega dados para a área de trabalho do Log Analytics. Aplicam os encargos de dados, com base na quantidade de dados que carrega. Ver [preços do log analytics](https://azure.microsoft.com/pricing/details/log-analytics/) para saber mais.
>
>

> [!NOTE]
> FIM utiliza a solução de controlo de alterações do Azure para monitorizar e identificar alterações no seu ambiente. Quando a monitorização da integridade de ficheiros está ativada, tem um **controlo de alterações** recurso do tipo de solução. Se remover o **controlo de alterações** recurso, desativar a funcionalidade no Centro de segurança de monitorização da integridade de ficheiros.
>
>

## <a name="which-files-should-i-monitor"></a>Os ficheiros que posso monitorizar?
Deve pensar sobre os ficheiros que são críticos para o seu sistema e aplicativos, ao escolher quais os ficheiros a monitorizar. Considere a escolha de arquivos que não quiser que a alteração sem planejamento. Ao escolher ficheiros que são alterados frequentemente por aplicações ou sistema operacional (como ficheiros de registo e ficheiros de texto) criam muito ruído que torna difícil identificar um ataque.

Recomenda do Centro de segurança que os ficheiros deve monitorizar como uma predefinição de acordo com padrões de ataques conhecidos que incluem alterações de arquivo e registro.

## <a name="using-file-integrity-monitoring"></a>Através de monitorização da integridade de ficheiros
1. Abra o dashboard **Centro de Segurança**.
2. No painel esquerdo, em **defesa de Cloud avançada**, selecione **monitorização da integridade de ficheiros**.
![Dashboard do Centro de segurança][1]

**Monitorização da integridade de ficheiros** abre.
  ![Dashboard do Centro de segurança][2]

As seguintes informações são fornecidas para cada área de trabalho:

- Número total de alterações que ocorreram na última semana (pode ver um travessão "-" se o FIM não está ativado na área de trabalho)
- Número total de computadores e VMs de geração de relatórios para a área de trabalho
- Localização geográfica da área de trabalho
- Subscrição do Azure que está a ser a área de trabalho

Também poderão ser apresentados os botões seguintes para uma área de trabalho:

- ![Ativar o ícone][3] Indica que o FIM não está ativado para a área de trabalho. Selecionar a área de trabalho permite-lhe ativar FIM em todas as máquinas na área de trabalho.
- ![Ícone de plano de atualização][4] indica que a área de trabalho ou a subscrição não está em execução no escalão Standard do Centro de segurança. Para utilizar a funcionalidade FIM, a sua subscrição tem de executar padrão.  Selecionar a área de trabalho permite-lhe atualizar para Standard. Para saber mais sobre o escalão Standard e como atualizar, veja [atualizar para o escalão Standard do Centro de segurança para uma maior segurança](security-center-pricing.md).
- Um espaço em branco (não há nenhum botão) significa que o FIM já está ativado na área de trabalho.

Sob **monitorização da integridade de ficheiros**, pode selecionar uma área de trabalho para ativar o FIM para essa área de trabalho, ver o dashboard de monitorização da integridade de ficheiros de mensagens em fila para essa área de trabalho, ou [atualizar](security-center-pricing.md) a área de trabalho standard.

## <a name="enable-fim"></a>Ativar o FIM
Para ativar o FIM numa área de trabalho:

1. Sob **monitorização da integridade de ficheiros**, selecione uma área de trabalho com o **ativar** botão.
2. **Ativar a monitorização da integridade de ficheiros** abre-se apresentar o número de máquinas Windows e Linux, na área de trabalho.

   ![Ativar a monitorização da integridade de ficheiros][5]

   As definições recomendadas para Windows e Linux são também apresentadas.  Expanda **ficheiros do Windows**, **Registro**, e **ficheiros do Linux** para ver a lista completa de itens recomendados.

3. Desmarcar qualquer recomendadas entidades que não pretende aplicar a FIM de.
4. Selecione **aplicam-se de que a monitorização da integridade dos ficheiros** para ativar o FIM.

> [!NOTE]
> Pode alterar as definições em qualquer altura. Consulte a que edição monitorizados entidades abaixo para saber mais.
>
>

## <a name="view-the-fim-dashboard"></a>Ver o dashboard FIM
O **monitorização da integridade de ficheiros** dashboard é apresentado para áreas de trabalho em que o FIM está ativado. É aberto o dashboard FIM depois de ativar a FIM de uma área de trabalho ou ao selecionar uma área de trabalho do **monitorização da integridade de ficheiros** janela que já tenha FIM ativada.

![Dashboard de monitorização da integridade do ficheiro][6]

O dashboard do FIM de uma área de trabalho apresenta o seguinte:

- Número total de máquinas ligadas à área de trabalho
- Número total de alterações que ocorreram durante o período de tempo selecionado
- Uma análise detalhada do tipo de alteração (arquivos, registro)
- Uma análise detalhada da categoria da alteração (modificadas, adicionadas, removidas)

Selecionar o filtro na parte superior do dashboard permite-lhe aplicar o período de tempo que pretende ver as alterações.

![Filtro de período de tempo][7]

O **computadores** separador (mostrado acima) apresenta uma lista de todas as máquinas que relatam a esta área de trabalho. Para cada máquina, o dashboard de lista:

- Total de alterações que ocorreram durante o período de tempo selecionado
- Uma análise detalhada das alterações total como alterações de ficheiros ou as alterações de registo

**Pesquisa de registos** abre-se ao introduzir um nome de máquina na pesquisa campo ou selecione uma máquina listada no separador computadores. Pesquisa de registos apresenta todas as alterações feitas durante o período de tempo selecionado para a máquina. Pode expandir uma alteração para obter mais informações.

![Pesquisa de Registos][8]

O **alterações** separador (mostrado abaixo) apresenta uma lista de todas as alterações para a área de trabalho durante o período de tempo selecionado. Para cada entidade que foi alterada, as listas de dashboard a:

- Computador que a alteração ocorreu no
- Tipo de alteração (Registro ou arquivo)
- Categoria da alteração (modificadas, adicionadas, removidas)
- Data e hora da alteração

![Alterações para a área de trabalho][9]

**Alterar detalhes** abre-se ao introduzir uma alteração na pesquisa campo ou selecionar uma entidade listada sob os **alterações** separador.

![Detalhes de alteração][10]

## <a name="edit-monitored-entities"></a>Entidades de edição monitorizada

1. Retorno para o **dashboard de monitorização da integridade de ficheiros** e selecione **definições**.

  ![Definições][11]

  **Configuração de área de trabalho** abre-se apresentar os três separadores: **Registo do Windows**, **arquivos do Windows**, e **ficheiros do Linux**. Cada separador lista as entidades que possa editar nessa categoria. Para cada entidade listada, o Centro de segurança identifica se o FIM é ativado (true) ou não ativada (false).  Editar a entidade permite-lhe ativar ou desativar o FIM.

  ![Configuração de área de trabalho][12]

2. Selecione um identityprotection. Neste exemplo, selecionamos um item no Registro do Windows. **Editar para controlo de alterações** abre.

  ![Editar ou controlo de alterações][13]

Sob **Editar para controlo de alterações** , pode:

- Ativar (True) ou desativar a monitorização da integridade de ficheiros (FALSO)
- Fornecer ou alterar o nome da entidade
- Fornecer ou alterar o valor ou o caminho
- Eliminar a entidade, eliminar a alteração ou guardar a alteração

## <a name="add-a-new-entity-to-monitor"></a>Adicionar uma nova entidade para monitorizar
1. Retorno para o **dashboard de monitorização da integridade de ficheiros** e selecione **definições** na parte superior. **Configuração de área de trabalho** abre.
2. Sob **configuração da área de trabalho**, selecione o separador para o tipo de entidade que pretende adicionar: Registo do Windows, arquivos do Windows ou ficheiros do Linux. Neste exemplo, selecionamos **ficheiros do Linux**.

  ![Adicione um novo item para monitorizar][14]

3. Selecione **Adicionar**. **Adicionar para controlo de alterações** abre.

  ![Introduza as informações pedidas][15]

4. Sobre o **Add** página, escreva as informações pedidas e selecione **guardar**.

## <a name="disable-monitored-entities"></a>Entidades de desativar monitorizado
1. Retorno para o **monitorização da integridade de ficheiros** dashboard.
2. Selecione uma área de trabalho em que o FIM está atualmente ativado. Uma área de trabalho está ativada para o FIM, se ele está em falta o botão Ativar ou botão Atualizar plano.

  ![Selecione uma área de trabalho em que o FIM está ativado][16]

3. Em monitorização da integridade de ficheiros, selecione **definições**.

  ![Selecionar definições][17]

4. Sob **configuração da área de trabalho**, selecione um grupo em que **ativado** está definido como true.

  ![Configuração da Área de Trabalho][18]

5. Sob **Editar para controlo de alterações** conjunto de janela **ativado** como False.

  ![Conjunto ativado como false][19]

6. Selecione **Guardar**.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Pasta e o caminho de monitorização utilizando carateres universais

Utilize carateres universais para simplificar o rastreamento entre diretórios. As seguintes regras aplicam-se ao configurar a pasta de monitorização utilizando carateres universais:
-   Carateres universais são necessários para vários ficheiros de controlo.
-   Carateres universais só podem ser utilizado no último segmento do caminho, como C:\folder\file ou /etc/*.conf
-   Se uma variável de ambiente inclui um caminho que não é válido, validação será concluída com êxito, mas o caminho irão falhar quando o inventário é executado.
-   Ao definir o caminho, evitar caminhos gerais como c:\*. * que irá resultar em demasiados pastas a ser percorridas.

## <a name="disable-fim"></a>Desativar o FIM
Pode desativar o FIM. FIM utiliza a solução de controlo de alterações do Azure para monitorizar e identificar alterações no seu ambiente. Desabilitando o FIM, é possível remover a solução de controlo de alterações da área de trabalho selecionada.

1. Para desativar o FIM, volte para o **monitorização da integridade de ficheiros** dashboard.
2. Selecione uma área de trabalho.
3. Sob **monitorização da integridade de ficheiros**, selecione **desativar**.

  ![Desativar o FIM][20]

4. Selecione **remover** para desativar.

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a utilizar o monitoramento de integridade de ficheiros (FIM) no Centro de segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições do Azure e grupos de recursos.
* [Gerir recomendações de segurança](security-center-recommendations.md) – Saiba como o recomendações o ajudam a proteger os seus recursos do Azure.
* [Monitorização de estado de funcionamento de segurança](security-center-monitoring.md)– Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança](security-center-managing-and-responding-alerts.md)– Saiba como gerir e responder a alertas de segurança.
* [Monitorizar soluções de parceiros](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [FAQ do Centro de segurança](security-center-faq.md)– encontre as perguntas mais frequentes sobre como utilizar o serviço.
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
