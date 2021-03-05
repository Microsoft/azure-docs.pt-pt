---
title: Monitorização da integridade dos ficheiros no Centro de Segurança Azure | Microsoft Docs
description: Saiba como configurar a monitorização da integridade do ficheiro (FIM) no Azure Security Center utilizando este walkthrough.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: b858903b87dc6c4cc1f7c40338c09bf5b8204b3d
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176540"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Monitorização da integridade do ficheiro no Centro de Segurança Azure
Saiba como configurar a monitorização da integridade do ficheiro (FIM) no Azure Security Center utilizando este walkthrough.


## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Disponibilidade Geral (GA)|
|Preços:|Requer [Azure Defender para servidores](defender-for-servers-introduction.md).<br>O FIM envia dados para o espaço de trabalho do Log Analytics. Os encargos de dados aplicam-se, com base na quantidade de dados que envia. Consulte [os preços do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) para saber mais.|
|Funções e permissões necessárias:|**O proprietário do espaço de trabalho** pode ativar/desativar o FIM (para mais informações, consulte [Azure Roles for Log Analytics](/services-hub/health/azure-roles#azure-roles)).<br>**O leitor** pode ver resultados.|
|Nuvens:|![Sim ](./media/icons/yes-icon.png) nuvens comerciais<br>![Sim ](./media/icons/yes-icon.png) Nacional/Soberano (Eua Gov, China Gov, Outro Gov)<br>Suportado apenas em regiões onde a Azure Automation está disponível.<br>![Sim ](./media/icons/yes-icon.png) [Azure Arc](https://docs.microsoft.com/azure/azure-arc/servers/overview) dispositivos habilitados.<br>Consulte [regiões apoiadas para o espaço de trabalho do Log Analytics ligado.](../automation/how-to/region-mappings.md)<br>[Saiba mais sobre o rastreio de mudanças.](../automation/change-tracking/overview.md)|
|||

## <a name="what-is-fim-in-security-center"></a>O que é FIM no Centro de Segurança?
A monitorização da integridade dos ficheiros (FIM), também conhecida como monitorização de alterações, examina ficheiros do sistema operativo, registos do Windows, software de aplicações, ficheiros do sistema Linux e muito mais, para alterações que possam indicar um ataque. 

O Security Center recomenda às entidades que monitorizem com o FIM, podendo ainda definir as suas próprias políticas ou entidades FIM para monitorizar. A FIM alerta para atividades suspeitas como:

- Criação ou remoção de chaves de arquivo e registo
- Modificações de ficheiros (alterações no tamanho do ficheiro, listas de controlo de acesso e haxixe do conteúdo)
- Modificações de registo (alterações no tamanho, listas de controlo de acesso, tipo e conteúdo)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Reveja a lista de entidades sugeridas para monitorizar com o FIM
> * Defina as suas próprias regras FIM personalizadas
> * Alterações de auditoria às suas entidades monitorizadas
> * Use wildcards para simplificar o rastreio em todos os diretórios


## <a name="how-does-fim-work"></a>Como funciona o FIM?

Ao comparar o estado atual destes itens com o estado durante a verificação anterior, o FIM alerta-o se forem feitas modificações suspeitas.

A FIM utiliza a solução Azure Change Tracking para rastrear e identificar alterações no seu ambiente. Quando a monitorização da integridade do ficheiro estiver ativada, tem um recurso de Rastreio de **Alterações** da **Solução** tipo . Para obter detalhes da frequência de recolha de dados, consulte [os detalhes da recolha de dados de Change Tracking](../automation/change-tracking/overview.md#change-tracking-and-inventory-data-collection).

> [!NOTE]
> Se remover o recurso **Change Tracking,** também irá desativar a funcionalidade de monitorização da integridade do ficheiro no Centro de Segurança.

## <a name="which-files-should-i-monitor"></a>Que ficheiros devo monitorizar?
Ao escolher quais os ficheiros a monitorizar, considere quais os ficheiros que são críticos para o seu sistema e aplicações. Monitorize ficheiros que não esperas mudar sem planear. Se escolher ficheiros que são frequentemente alterados por aplicações ou sistema operativo (como ficheiros de registo e ficheiros de texto) criará muito ruído, dificultando a identificação de um ataque.

O Centro de Segurança fornece a seguinte lista de itens recomendados para monitorizar com base em padrões de ataque conhecidos.

|Ficheiros Linux|Ficheiros do Windows|Chaves de registo do Windows (HKLM = HKEY_LOCAL_MACHINE)|
|:----|:----|:----|
|/bin/login|C:\autoexec.bat|HKLM\SOFTWARE\Microsoft\Criptografia\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg \{ C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/bin/passwd|C:\boot.ini|HKLM\SOFTWARE\Microsoft\Criptografia\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg \{ 603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/*.conf|C:\config.sys|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\IniFileMapping\SYSTEM.ini\boot|
|/usr/bin|C:\Windows\system.ini|HKLM\SOFTWARE\Microsoft\Microsoft\Windows NT\CurrentVersion\Windows|
|/usr/sbin|C:\Windows\win.ini|HKLM\SOFTWARE\Microsoft\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/bin|C:\Windows\regedit.exe|HKLM\SOFTWARE\Microsoft\Microsoft\Windows\CurrentVersion\Explorer\Shell Pastas|
|/sbin|C:\Windows\System32\userinit.exe|HKLM\SOFTWARE\Microsoft\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders|
|/arranque|C:\Windows\explorer.exe|HKLM\SOFTWARE\Microsoft\Microsoft\Windows\CurrentVersion\Run|
|/usr/local/bin|C:\Ficheiros de programas\Microsoft Security Client\msseces.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce|
|/usr/local/sbin||HKLM\SOFTWARE\Microsoft\Microsoft\Windows\CurrentVersion\RunOnceEx|
|/opt/bin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServices|
|/opt/sbin||HKLM\SOFTWARE\Microsoft\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|/etc/crontab||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg \{ C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/etc/init.d||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg \{ 603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/cron.hourly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\IniFileMapping\system.ini\boot|
|/etc/cron.diariamente||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Windows|
|/etc/cron.weekly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/etc/cron.mensalmente||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnce|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnceEx|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServices|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|||HKLM\SYSTEM\CurrentControlSet\Control\Control\hivelist|
|||HKLM\SYSTEM\CurrentControlSet\Control\Control\Session Manager\KnownDLLs|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parâmetros\FirewallPolicy\DomainProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parâmetros\FirewallPolicy\PublicProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parâmetros\FirewallPolicy\StandardProfile|


## <a name="enable-file-integrity-monitoring"></a>Ativar a monitorização da integridade dos ficheiros 

O FIM só está disponível nas páginas do Security Center no portal Azure. Atualmente não existe API REST para trabalhar com a FIM.

1. A partir da área **de proteção avançada** do painel **Azure Defender,** selecione **monitorização da integridade do ficheiro**.

   :::image type="content" source="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png" alt-text="Lançamento FIM" lightbox="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png":::

    A página de configuração **de monitorização da integridade do ficheiro** abre.

    São fornecidas as seguintes informações para cada espaço de trabalho:

    - Número total de alterações ocorridas na última semana (pode ver um traço "-" se fim não estiver ativado no espaço de trabalho)
    - Número total de computadores e VMs reportando para o espaço de trabalho
    - Localização geográfica do espaço de trabalho
    - Azure subscrição que o espaço de trabalho está em baixo

1. Utilize esta página para:

    - Aceder e ver o estado e as configurações de cada espaço de trabalho

    - ![Atualizar o ícone do plano ][4] Atualize o espaço de trabalho para usar o Azure Defender. Este ícone indica que o espaço de trabalho ou subscrição não está protegido pelo Azure Defender. Para utilizar as funcionalidades FIM, a sua subscrição deve ser protegida pelo Azure Defender. Para mais informações, consulte [o Azure Security Center gratuitamente vs Azure Defender.](security-center-pricing.md)

    - ![Ativar o ícone][3] Ativar o FIM em todas as máquinas no espaço de trabalho e configurar as opções FIM. Este ícone indica que o FIM não está ativado para o espaço de trabalho.

        :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-list-fim.png" alt-text="Ativar o FIM para um espaço de trabalho específico":::


    > [!TIP]
    > Se não houver botão de ativação ou upgrade, e o espaço estiver em branco, significa que o FIM já está ativado no espaço de trabalho.


1. **Selecione ATIVA**. São apresentados os detalhes do espaço de trabalho, incluindo o número de máquinas Windows e Linux no espaço de trabalho.

    :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-fim-status.png" alt-text="Página de detalhes do espaço de trabalho FIM":::

   As definições recomendadas para Windows e Linux também estão listadas.  Expandir **ficheiros Windows,** **Registo** e **Ficheiros Linux** para ver a lista completa de itens recomendados.

1. Limpe as caixas de verificação para quaisquer entidades recomendadas que não queira ser monitorizada pela FIM.

1. **Selecione Aplicar a monitorização da integridade do ficheiro** para ativar o FIM.

> [!NOTE]
> Pode alterar as definições a qualquer momento. Consulte [as entidades monitorizadas](#edit-monitored-entities) de Editar abaixo para saber mais.



## <a name="audit-monitored-workspaces"></a>Espaçoes de trabalho monitorizados pela auditoria 

O **painel de monitorização da integridade** do ficheiro apresenta espaços de trabalho onde o FIM está ativado. O painel FIM abre depois de ativar o FIM num espaço de trabalho ou quando seleciona um espaço de trabalho na janela **de monitorização** da integridade do ficheiro que já tem o FIM ativado.

:::image type="content" source="./media/security-center-file-integrity-monitoring/fim-dashboard.png" alt-text="O dashboard FIM e os seus vários painéis informativos":::

O painel FIM para um espaço de trabalho apresenta os seguintes detalhes:

- Número total de máquinas ligadas ao espaço de trabalho
- Número total de alterações ocorridas durante o período de tempo selecionado
- Uma repartição do tipo de alteração (ficheiros, registo)
- Uma repartição da categoria de mudança (modificada, adicionada, removida)

Selecione **Filtro** na parte superior do painel de instrumentos para alterar o período de tempo para o qual são apresentadas alterações.

:::image type="content" source="./media/security-center-file-integrity-monitoring/dashboard-filter.png" alt-text="Filtro de período de tempo para o dashboard FIM":::

O separador **Servidores** lista as máquinas que reportam a este espaço de trabalho. Para cada máquina, o painel de instrumentos lista:

- Total de alterações que ocorreram durante o período de tempo selecionado
- Uma repartição do total de alterações à medida que as alterações de ficheiros ou alterações de registo

**A Procura de Registo** abre quando introduz um nome de máquina no campo de pesquisa ou seleciona uma máquina listada no separador Computadores. Pode expandir uma mudança para mais informações.

![Pesquisa de Registos][8]

O separador **Alterações** (mostrado abaixo) lista todas as alterações para o espaço de trabalho durante o período de tempo selecionado. Para cada entidade que foi alterada, o painel de instrumentos lista:

- Computador em que a mudança ocorreu
- Tipo de alteração (registo ou ficheiro)
- Categoria de alteração (modificada, adicionada, removida)
- Data e hora da mudança

![Alterações para o espaço de trabalho][9]

**Os detalhes** de alteração abrem quando introduz uma alteração no campo de pesquisa ou seleciona uma entidade listada no separador **Alterações.**

![Alterar detalhes][10]

## <a name="edit-monitored-entities"></a>Editar entidades monitorizadas

1. Volte ao **painel de monitorização da integridade** do ficheiro e selecione **Definições**.

   ![Definições][11]

   **A configuração do espaço de trabalho** abre apresentando três separadores: Registo do **Windows,** **Ficheiros Windows** e **Ficheiros Linux**. Cada separador lista as entidades que pode editar nessa categoria. Para cada entidade listada, o Centro de Segurança identifica se o FIM está ativado (verdadeiro) ou não ativado (falso).  A edição da entidade permite-lhe ativar ou desativar o FIM.

   ![Configuração da área de trabalho][12]

2. Selecione uma proteção de identidade. Neste exemplo, selecionamos um item no Registo do Windows. **A edição para Change Tracking** abre.

   ![Editar ou alterar o rastreio][13]

Em **Edição para Rastreio de Alterações** pode:

- Ativar (Verdadeiro) ou desativar a monitorização da integridade do ficheiro (falso)
- Fornecer ou alterar o nome da entidade
- Fornecer ou alterar o valor ou o caminho
- Eliminar a entidade, descartar a alteração ou guardar a alteração

## <a name="add-a-new-entity-to-monitor"></a>Adicione uma nova entidade para monitorizar
1. Volte ao **painel de monitorização da integridade** do ficheiro e selecione **Definições** na parte superior. **A configuração do espaço** de trabalho abre.
2. Na **Configuração do Espaço de Trabalho**, selecione o separador para o tipo de entidade que pretende adicionar: Registo do Windows, Ficheiros Windows ou Ficheiros Linux. Neste exemplo, selecionamos **Ficheiros Linux.**

   ![Adicione um novo item para monitorizar][14]

3. Selecione **Adicionar**. **Adicione para o Change Tracking** abre.

   ![Insira informações solicitadas][15]

4. Na página **Adicionar,** digite as informações solicitadas e **selecione Guardar**.

## <a name="disable-monitored-entities"></a>Desativar entidades monitorizadas
1. Regresso ao painel **de monitorização da integridade** do ficheiro.
2. Selecione um espaço de trabalho onde o FIM está atualmente ativado. Um espaço de trabalho está ativado para FIM se não estiver a perder o botão Ativar ou o botão Plano de Atualização.

   ![Selecione um espaço de trabalho onde o FIM está ativado][16]

3. Sob a monitorização da integridade do ficheiro, **selecione Definições**.

   ![selecionar as definições][17]

4. Na **Configuração do Espaço de Trabalho**, selecione um grupo onde o **Enabled** está definido para ser verdadeiro.

   ![Configuração do espaço de trabalho][18]

5. Em **edição para conjunto de** janela de rastreio de alteração **ativado** para falso.

   ![Conjunto Habilitado a falso][19]

6. Selecione **Guardar**.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Monitorização de pastas e caminhos utilizando wildcards

Use wildcards para simplificar o rastreio através de diretórios. Aplicam-se as seguintes regras quando configurar a monitorização das pastas utilizando wildcards:
-   Wildcards são necessários para rastrear vários ficheiros.
-   Wildcards só podem ser usados no último segmento de um caminho, como C:\pasta\file ou /etc/*.conf
-   Se uma variável ambiental incluir um caminho que não seja válido, a validação terá sucesso, mas o caminho falhará quando o inventário for executado.
-   Ao definir o caminho, evite caminhos gerais como c: \* .* o que resultará na passagem de demasiadas pastas.

## <a name="disable-fim"></a>Desativar o FIM
Pode desativar o FIM. A FIM utiliza a solução Azure Change Tracking para rastrear e identificar alterações no seu ambiente. Ao desativar o FIM, remove a solução de Rastreio de Alterações do espaço de trabalho selecionado.

1. Para desativar o FIM, volte ao painel **de monitorização da integridade** do Ficheiro.
2. Selecione uma área de trabalho.
3. No **âmbito da monitorização da integridade do ficheiro,** selecione **Desativar**.

   ![Desativar o FIM][20]

4. Selecione **Remover** para desativar.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a utilizar a monitorização da integridade dos ficheiros (FIM) no Centro de Segurança. Para saber mais sobre o Centro de Segurança, consulte as seguintes páginas:

* [Definição de políticas de segurança](tutorial-security-policy.md) -- Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos Azure.
* [Gerir recomendações de segurança-](security-center-recommendations.md) Saiba como as recomendações o ajudam a proteger os seus recursos Azure.
* [Blogue de Segurança do Azure](/archive/blogs/azuresecurity/) – Obtenha as notícias e informações mais recentes de segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
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
