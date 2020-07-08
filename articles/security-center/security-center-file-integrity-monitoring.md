---
title: Monitorização da Integridade do Ficheiro no Centro de Segurança Azure Microsoft Docs
description: Saiba como configurar a Monitorização da Integridade do Ficheiro (FIM) no Azure Security Center utilizando esta passagem.
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
ms.openlocfilehash: c58f70126c72a84b09f6eadc251949a0f0021657
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84628298"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>File Integrity Monitoring in Azure Security Center (Monitorização da Integridade dos Ficheiros no Centro de Segurança do Azure)
Saiba como configurar a Monitorização da Integridade do Ficheiro (FIM) no Azure Security Center utilizando esta passagem.


## <a name="availability"></a>Disponibilidade

- Estado de libertação: **Geralmente disponível**
- Funções necessárias: **O proprietário do espaço de trabalho** pode ativar/desativar o FIM (para mais informações, consulte [Azure Roles for Log Analytics](https://docs.microsoft.com/services-hub/health/azure-roles#azure-roles)). **O leitor** pode ver resultados.
- Nuvens:
    - nuvens comerciais ✔
    - ✔ nuvem gov dos EUA
    - ✘ China Gov / Outros Gov


## <a name="what-is-fim-in-security-center"></a>O que é FIM no Centro de Segurança?
O File Integrity Monitoring (FIM), também conhecido como monitorização de alterações, examina ficheiros e registos de sistema operativo, software de aplicação e outros para alterações que possam indicar um ataque. Um método de comparação é usado para determinar se o estado atual do ficheiro é diferente da última digitalização do ficheiro. Pode aproveitar esta comparação para determinar se foram feitas modificações válidas ou suspeitas nos seus ficheiros.

O Monitor de Integridade de Ficheiros do Security Center valida a integridade dos ficheiros Windows, registo do Windows e ficheiros Linux. Selecione os ficheiros que pretende monitorizar ativando o FIM. O Security Center monitoriza ficheiros com FIM habilitados para atividades como:

- Criação e remoção de arquivos e registos
- Modificações de ficheiros (alterações no tamanho do ficheiro, listas de controlo de acesso e haxixe do conteúdo)
- Modificações de registo (alterações no tamanho, listas de controlo de acesso, tipo e conteúdo)

O Security Center recomenda às entidades que monitorizem, o que pode facilmente permitir o FIM. Também pode definir as suas próprias políticas ou entidades FIM para monitorizar. Este walkthrough mostra-lhe como.

> [!NOTE]
> A funcionalidade De Monitorização da Integridade do Ficheiro (FIM) funciona para computadores Windows e Linux e VMs e está disponível no nível padrão do Centro de Segurança. Veja [Preços](security-center-pricing.md) para saber mais sobre os escalões de preços do Centro de Segurança. O FIM envia dados para o espaço de trabalho do Log Analytics. Os encargos de dados aplicam-se, com base na quantidade de dados que envia. Consulte [os preços do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) para saber mais.

A FIM utiliza a solução Azure Change Tracking para rastrear e identificar alterações no seu ambiente. Quando a Monitorização da Integridade do Ficheiro estiver ativada, tem um recurso de rastreio de **alteração** da **solução**tipo . Para obter detalhes sobre a frequência de recolha de dados, veja [Detalhes da recolha de dados do Controlo de Alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking#change-tracking-data-collection-details) do Controlo de Alterações do Azure.

> [!NOTE]
> Se remover o recurso **Change Tracking,** também irá desativar a função de Monitorização da Integridade do Ficheiro no Centro de Segurança.

## <a name="which-files-should-i-monitor"></a>Que ficheiros devo monitorizar?
Deve pensar nos ficheiros que são fundamentais para o seu sistema e aplicações ao escolher quais os ficheiros a monitorizar. Considere escolher ficheiros que não espera mudar sem planear. A escolha de ficheiros que são frequentemente alterados por aplicações ou sistema operativo (como ficheiros de registo e ficheiros de texto) criam muito ruído que dificulta a identificação de um ataque.

O Centro de Segurança fornece a seguinte lista de itens recomendados para monitorizar com base em padrões de ataque conhecidos. Estes incluem ficheiros e chaves de registo do Windows. Todas as chaves estão em HKEY_LOCAL_MACHINE ("HKLM" na tabela.)

|**Ficheiros Linux**|**Ficheiros windows**|**Chaves de registo do Windows**|
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

## <a name="using-file-integrity-monitoring"></a>Usando a monitorização da integridade do ficheiro
1. Abra o dashboard **Centro de Segurança**.
2. No painel esquerdo sob **defesa de nuvem avançada,** selecione **Monitoriação de Integridade do Ficheiro**.
![Dashboard Centro de Segurança][1]

**A Monitorização da Integridade do Ficheiro** abre.
  ![Dashboard Centro de Segurança][2]

São fornecidas as seguintes informações para cada espaço de trabalho:

- Número total de alterações ocorridas na última semana (pode ver um traço "-" se fim não estiver ativado no espaço de trabalho)
- Número total de computadores e VMs reportando para o espaço de trabalho
- Localização geográfica do espaço de trabalho
- Azure subscrição que o espaço de trabalho está em baixo

Podem também ser mostrados os seguintes botões para um espaço de trabalho:

- ![Ativar o ícone][3] Indica que o FIM não está ativado para o espaço de trabalho. A seleção do espaço de trabalho permite ativar o FIM em todas as máquinas sob o espaço de trabalho.
- ![O ícone do plano ][4] de atualização indica que o espaço de trabalho ou subscrição não está a funcionar sob o nível padrão do Security Center. Para utilizar a funcionalidade FIM, a sua subscrição deve estar a executar o Standard.  A seleção do espaço de trabalho permite-lhe fazer upgrade para Standard. Para saber mais sobre o nível Standard e como fazer o upgrade, consulte [o nível Standard do Security Center para uma maior segurança](security-center-pricing.md).
- Um branco (não há botão) significa que o FIM já está ativado no espaço de trabalho.

No **âmbito da Monitorização da Integridade do Ficheiro,** pode selecionar um espaço de trabalho para permitir o FIM para esse espaço de trabalho, ver o painel de monitorização da integridade do ficheiro para esse espaço de trabalho ou [atualizar](security-center-pricing.md) o espaço de trabalho para Standard.

## <a name="enable-fim"></a>Ativar FIM
Para ativar o FIM num espaço de trabalho:

1. No **âmbito da Monitorização da Integridade do Ficheiro,** selecione um espaço de trabalho com o botão **Ativar.**
2. **Ativar a monitorização da integridade** do ficheiro, apresentando o número de máquinas Windows e Linux no espaço de trabalho.

   ![Ativar a monitorização da integridade dos ficheiros][5]

   As definições recomendadas para Windows e Linux também estão listadas.  Expandir **ficheiros Windows,** **Registo**e **Ficheiros Linux** para ver a lista completa de itens recomendados.

3. Desmarque quaisquer entidades recomendadas a que não queira aplicar FIM.
4. **Selecione Aplicar a monitorização da integridade do ficheiro** para ativar o FIM.

> [!NOTE]
> Pode alterar as definições a qualquer momento. Consulte as entidades monitorizadas de Editar abaixo para saber mais.


## <a name="view-the-fim-dashboard"></a>Ver o painel FIM
O **painel de monitorização da integridade** do ficheiro apresenta espaços de trabalho onde o FIM está ativado. O painel FIM abre depois de ativar o FIM num espaço de trabalho ou quando seleciona um espaço de trabalho na janela **de Monitorização da Integridade** do Ficheiro que já tem o FIM ativado.

![Painel de monitorização da integridade do ficheiro][6]

O painel FIM para um espaço de trabalho apresenta os seguintes detalhes:

- Número total de máquinas ligadas ao espaço de trabalho
- Número total de alterações ocorridas durante o período de tempo selecionado
- Uma repartição do tipo de alteração (ficheiros, registo)
- Uma repartição da categoria de mudança (modificada, adicionada, removida)

A seleção do Filtro na parte superior do painel permite aplicar o período de tempo para o qual deseja ver alterações.

![Filtro do período de tempo][7]

O **separador Computadores** (acima indicado) lista todas as máquinas que reportam a este espaço de trabalho. Para cada máquina, o painel de instrumentos lista:

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

   **A configuração do espaço de trabalho** abre apresentando três separadores: Registo do **Windows,** **Ficheiros Windows**e **Ficheiros Linux**. Cada separador lista as entidades que pode editar nessa categoria. Para cada entidade listada, o Centro de Segurança identifica se o FIM está ativado (verdadeiro) ou não ativado (falso).  A edição da entidade permite-lhe ativar ou desativar o FIM.

   ![Configuração do espaço de trabalho][12]

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

3. No âmbito da monitorização da integridade do ficheiro, selecione **Definições**.

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

1. Para desativar o FIM, volte ao painel **de monitorização da integridade** do ficheiro.
2. Selecione uma área de trabalho.
3. No **âmbito da Monitorização da Integridade dos Ficheiros,** selecione **Desativar**.

   ![Desativar o FIM][20]

4. Selecione **Remover** para desativar.

## <a name="next-steps"></a>Próximos passos
Neste artigo, aprendeu a utilizar o Monitor de Integridade do Ficheiro (FIM) no Centro de Segurança. Para saber mais sobre o Centro de Segurança, consulte as seguintes páginas:

* [Definição de políticas de segurança](tutorial-security-policy.md) -- Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos Azure.
* [Gerir recomendações de segurança-](security-center-recommendations.md) Saiba como as recomendações o ajudam a proteger os seus recursos Azure.
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