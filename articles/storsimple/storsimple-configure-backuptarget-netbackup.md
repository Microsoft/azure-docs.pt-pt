---
title: StorSimple 8000 Series como destino de backup com o NetBackup | Microsoft Docs
description: Descreve a configuração de destino de backup do StorSimple com o Veritas NetBackup.
services: storsimple
documentationcenter: ''
author: harshakirank
manager: matd
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2017
ms.author: matd
ms.openlocfilehash: 957fff73f2406e0e057a7c978dd76a6bd9c156b7
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "67876215"
---
# <a name="storsimple-as-a-backup-target-with-netbackup"></a>StorSimple como um destino de backup com o NetBackup

## <a name="overview"></a>Descrição geral

O Azure StorSimple é uma solução de armazenamento de nuvem híbrida da Microsoft. O StorSimple aborda as complexidades do crescimento exponencial de dados usando uma conta de armazenamento do Azure como uma extensão da solução local e os dados em camadas automaticamente no armazenamento local e no armazenamento em nuvem.

Neste artigo, discutiremos a integração do StorSimple com o Veritas NetBackup e as práticas recomendadas para a integração de ambas as soluções. Também fazemos recomendações sobre como configurar o Veritas NetBackup para se integrar melhor ao StorSimple. Nós adiamos as práticas recomendadas da Veritas, arquitetos de backup e administradores para a melhor maneira de configurar o Veritas NetBackup para atender aos requisitos de backup individuais e aos SLAs (contratos de nível de serviço).

Embora Ilustremos as etapas de configuração e os principais conceitos, este artigo não é, de maneira alguma, uma configuração passo a passo ou um guia de instalação. Supomos que os componentes básicos e a infraestrutura estejam em ordem de funcionamento e estejam prontos para dar suporte aos conceitos que descrevemos.

### <a name="who-should-read-this"></a>Quem deve ler isso?

As informações neste artigo serão mais úteis para administradores de backup, administradores de armazenamento e arquitetos de armazenamento que têm conhecimento de armazenamento, Windows Server 2012 R2, Ethernet, serviços de nuvem e Veritas NetBackup.

### <a name="supported-versions"></a>Versões suportadas

-   NetBackup 7.7. x e versões posteriores
-   [StorSimple atualização 3 e versões posteriores](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Por que o StorSimple como um destino de backup?

O StorSimple é uma boa opção para um destino de backup porque:

-   Ele fornece armazenamento local padrão para aplicativos de backup para usar como um destino de backup rápido, sem nenhuma alteração. Você também pode usar o StorSimple para uma restauração rápida de backups recentes.
-   Sua disposição em camadas de nuvem é integrada diretamente a uma conta de armazenamento em nuvem do Azure para usar o armazenamento do Azure econômico.
-   Ele fornece automaticamente armazenamento externo para recuperação de desastres.

## <a name="key-concepts"></a>Conceitos-chave

Assim como acontece com qualquer solução de armazenamento, uma avaliação cuidadosa do desempenho de armazenamento da solução, dos SLAs, da taxa de alteração e das necessidades de aumento da capacidade é fundamental para o sucesso. A ideia principal é que, ao introduzir uma camada de nuvem, os tempos de acesso e as taxas de transferência para a nuvem desempenham um papel fundamental na capacidade do StorSimple de realizar seu trabalho.

O StorSimple foi projetado para fornecer armazenamento a aplicativos que operam em um conjunto de dados de trabalho bem definido (dados ativos). Nesse modelo, o conjunto de dados de trabalho é armazenado nas camadas locais e o conjunto restante de dados não trabalha/frio/arquivado é colocado em camadas na nuvem. Esse modelo é representado na figura a seguir. A linha verde quase simples representa os dados armazenados nas camadas locais do dispositivo StorSimple. A linha vermelha representa a quantidade total de dados armazenados na solução StorSimple em todas as camadas. O espaço entre a linha verde plana e a curva de vermelho exponencial representa a quantidade total de dados armazenados na nuvem.

**StorSimple tiering**
![Diagrama de camadas storsimple de camada do storsimple](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)

Com essa arquitetura em mente, você verá que o StorSimple é ideal para operar como um destino de backup. Você pode usar o StorSimple para:
-   Execute suas restaurações mais frequentes do conjunto de dados de trabalho local.
-   Use a nuvem para recuperação de desastres fora do local e dados mais antigos, em que as restaurações são menos frequentes.

## <a name="storsimple-benefits"></a>Benefícios do StorSimple

O StorSimple fornece uma solução local que é integrada diretamente com o Microsoft Azure, aproveitando o acesso contínuo ao armazenamento local e na nuvem.

O StorSimple usa camadas automáticas entre o dispositivo local, que tem o dispositivo de estado sólido (SSD) e o armazenamento de SCSI Serial anexado (SAS) e o armazenamento do Azure. A camada automática mantém os dados acessados com frequência locais, nas camadas SSD e SAS. Ele move dados raramente acessados para o armazenamento do Azure.

O StorSimple oferece estes benefícios:

-   Algoritmos exclusivos de eliminação de duplicação e compactação que usam a nuvem para atingir níveis de eliminação de duplicação sem precedentes
-   Elevada disponibilidade
-   Replicação geográfica usando a replicação geográfica do Azure
-   Integração do Azure
-   Criptografia de dados na nuvem
-   Melhor recuperação de desastres e conformidade

Embora o StorSimple apresente dois cenários de implantação principais (destino de backup primário e destino de backup secundário), fundamentalmente, é um dispositivo de armazenamento de blocos simples e em bloco. O StorSimple faz toda a compactação e eliminação de duplicação. Ele envia e recupera diretamente os dados entre a nuvem e o aplicativo e o sistema de arquivos.

Para obter mais informações sobre o storsimple [, consulte série 8000 do storsimple: Solução](storsimple-overview.md)de armazenamento de nuvem híbrida. Além disso, você pode examinar as [especificações técnicas da série 8000 do StorSimple](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> O uso de um dispositivo StorSimple como destino de backup só tem suporte para o StorSimple 8000 atualização 3 e versões posteriores.

## <a name="architecture-overview"></a>Descrição geral da arquitetura

As tabelas a seguir mostram as diretrizes iniciais de modelo para arquitetura do dispositivo.

**Capacidades do StorSimple para armazenamento local e na nuvem**

| Capacidade de armazenamento       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| Capacidade de armazenamento local | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Capacidade de armazenamento em nuvem | &gt; 200 TiB\* | &gt; 500 TiB\* |

\*O tamanho do armazenamento não assume nenhuma eliminação de duplicação ou compactação.

**Capacidades do StorSimple para backups primários e secundários**

| Cenário de backup  | Capacidade de armazenamento local  | Capacidade de armazenamento em nuvem  |
|---|---|---|
| Backup primário  | Backups recentes armazenados no armazenamento local para recuperação rápida para atender ao RPO (objetivo de ponto de recuperação) | O histórico de backup (RPO) cabe na capacidade da nuvem |
| Backup secundário | A cópia secundária dos dados de backup pode ser armazenada na capacidade da nuvem  | N/A  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple como um destino de backup primário

Nesse cenário, os volumes do StorSimple são apresentados ao aplicativo de backup como o único repositório para backups. A figura a seguir mostra uma arquitetura de solução na qual todos os backups usam volumes em camadas do StorSimple para backups e restaurações.

![StorSimple como um diagrama lógico de destino de backup primário](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Etapas lógicas de backup de destino principal

1.  O servidor de backup entra em contato com o agente de backup de destino e o agente de backup transmite dados para o servidor de backup.
2.  O servidor de backup grava dados nos volumes em camadas do StorSimple.
3.  O servidor de backup atualiza o banco de dados do catálogo e, em seguida, conclui o trabalho de backup.
4.  Um script de instantâneo dispara o StorSimple snapshot Manager (iniciar ou excluir).
5.  O servidor de backup exclui os backups expirados com base em uma política de retenção.

### <a name="primary-target-restore-logical-steps"></a>Etapas lógicas de restauração de destino primário

1.  O servidor de backup começa a restaurar os dados apropriados do repositório de armazenamento.
2.  O agente de backup recebe os dados do servidor de backup.
3.  O servidor de backup conclui o trabalho de restauração.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple como um destino de backup secundário

Nesse cenário, os volumes do StorSimple são usados principalmente para retenção ou arquivamento de longo prazo.

A figura a seguir mostra uma arquitetura na qual backups e restaurações iniciais visam um volume de alto desempenho. Esses backups são copiados e arquivados em um volume em camadas do StorSimple em um agendamento definido.

É importante dimensionar o volume de alto desempenho para que ele possa lidar com a capacidade e os requisitos de desempenho da sua política de retenção.

![StorSimple como um diagrama lógico de destino de backup secundário](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Etapas lógicas de backup de destino secundário

1.  O servidor de backup entra em contato com o agente de backup de destino e o agente de backup transmite dados para o servidor de backup.
2.  O servidor de backup grava dados no armazenamento de alto desempenho.
3.  O servidor de backup atualiza o banco de dados do catálogo e, em seguida, conclui o trabalho de backup.
4.  O servidor de backup copia backups para o StorSimple com base em uma política de retenção.
5.  Um script de instantâneo dispara o StorSimple snapshot Manager (iniciar ou excluir).
6.  O servidor de backup exclui os backups expirados com base em uma política de retenção.

### <a name="secondary-target-restore-logical-steps"></a>Etapas lógicas de restauração de destino secundário

1.  O servidor de backup começa a restaurar os dados apropriados do repositório de armazenamento.
2.  O agente de backup recebe os dados do servidor de backup.
3.  O servidor de backup conclui o trabalho de restauração.

## <a name="deploy-the-solution"></a>Implementar a solução

A implantação dessa solução requer três etapas:
1. Prepare a infraestrutura de rede.
2. Implante seu dispositivo StorSimple como um destino de backup.
3. Implante o Veritas NetBackup.

Cada etapa é discutida detalhadamente nas seções a seguir.

### <a name="set-up-the-network"></a>Configurar a rede

Como o StorSimple é uma solução integrada com a nuvem do Azure, o StorSimple requer uma conexão ativa e em funcionamento com a nuvem do Azure. Essa conexão é usada para operações como instantâneos de nuvem, gerenciamento de dados e transferência de metadados e para camadas de dados mais antigos e menos acessados para o armazenamento em nuvem do Azure.

Para que a solução seja executada de forma ideal, recomendamos que você siga estas práticas recomendadas de rede:

-   O link que conecta a camada do StorSimple ao Azure deve atender aos seus requisitos de largura de banda. Para conseguir isso, aplique o nível de QoS (qualidade de serviço) adequado a seus comutadores de infraestrutura para corresponder aos SLAs de RPO e RTO (objetivo de tempo de recuperação).

-   As latências máximas de acesso de armazenamento de blob do Azure devem ser cerca de 80 ms.

### <a name="deploy-storsimple"></a>Implantar o StorSimple

Para obter diretrizes passo a passo de implantação do StorSimple, consulte [implantar seu dispositivo StorSimple local](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-netbackup"></a>Implantar o NetBackup

Para obter orientações passo a passo de implantação do NetBackup 7.7. x, consulte a [documentação do NetBackup 7.7. x](http://www.veritas.com/docs/000094423).

## <a name="set-up-the-solution"></a>Configurar a solução

Nesta seção, demonstramos alguns exemplos de configuração. Os exemplos e as recomendações a seguir ilustram a implementação mais básica e fundamental. Essa implementação pode não se aplicar diretamente a seus requisitos de backup específicos.

### <a name="set-up-storsimple"></a>Configurar o StorSimple

| Tarefas de implantação do StorSimple  | Comentários adicionais |
|---|---|
| Implante seu dispositivo StorSimple local. | Versões com suporte: Atualização 3 e versões posteriores. |
| Ative o destino de backup. | Use estes comandos para ativar ou desativar o modo de destino de backup e para obter o status. Para obter mais informações, consulte [conectar-se remotamente a um dispositivo StorSimple](storsimple-remote-connect.md).</br> Para ativar o modo de backup `Set-HCSBackupApplianceMode -enable`:. </br> Para desligar o modo de backup `Set-HCSBackupApplianceMode -disable`:. </br> Para obter o estado atual das configurações do modo de `Get-HCSBackupApplianceMode`backup:. |
| Crie um contêiner de volume comum para o volume que armazena os dados de backup. Todos os dados em um contêiner de volume têm eliminação de duplicação. | Os contêineres de volume do StorSimple definem domínios de eliminação de duplicação.  |
| Crie volumes do StorSimple. | Crie volumes com tamanhos o mais próximo possível do uso antecipado, pois o tamanho do volume afeta o tempo de duração do instantâneo de nuvem. Para obter informações sobre como dimensionar um volume, leia sobre [as políticas de retenção](#retention-policies).</br> </br> Use os volumes em camadas do StorSimple e marque a caixa de seleção **usar este volume para dados de arquivamento acessados com menos frequência** . </br> Não há suporte para o uso somente de volumes fixados localmente. |
| Crie uma política exclusiva de backup do StorSimple para todos os volumes de destino de backup. | Uma política de backup do StorSimple define o grupo de consistência de volume. |
| Desabilite a agenda conforme os instantâneos expirarem. | Os instantâneos são disparados como uma operação de pós-processamento. |

### <a name="set-up-the-host-backup-server-storage"></a>Configurar o armazenamento do servidor de backup do host

Configure o armazenamento do servidor de backup do host de acordo com estas diretrizes:  

- Não use volumes estendidos (criados pelo gerenciamento de disco do Windows); Não há suporte para volumes estendidos.
- Formate seus volumes usando NTFS com tamanho de alocação de 64 KB.
- Mapeie os volumes do StorSimple diretamente para o servidor do NetBackup.
    - Use iSCSI para servidores físicos.
    - Use discos de passagem para servidores virtuais.


## <a name="best-practices-for-storsimple-and-netbackup"></a>Práticas recomendadas para o StorSimple e o NetBackup

Configure sua solução de acordo com as diretrizes nas seções a seguir.

### <a name="operating-system-best-practices"></a>Práticas recomendadas do sistema operacional

- Desabilite a criptografia e a eliminação de duplicação do Windows Server para o sistema de arquivos NTFS.
- Desabilite a desfragmentação do Windows Server nos volumes do StorSimple.
- Desabilite a indexação do Windows Server nos volumes do StorSimple.
- Execute uma verificação antivírus no host de origem (não nos volumes do StorSimple).
- Desative a manutenção padrão do [Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) no Gerenciador de tarefas. Faça isso de uma das seguintes maneiras:
  - Desative o configurador de manutenção no Windows Agendador de Tarefas.
  - Baixe o [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) do Windows Sysinternals. Depois de baixar o PsExec, execute o Windows PowerShell como administrador e digite:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>Práticas recomendadas do StorSimple

-   Certifique-se de que o dispositivo StorSimple seja atualizado para a [atualização 3 ou posterior](storsimple-install-update-3.md).
-   Isole o tráfego de iSCSI e de nuvem. Use conexões iSCSI dedicadas para o tráfego entre o StorSimple e o servidor de backup.
-   Certifique-se de que seu dispositivo StorSimple seja um destino de backup dedicado. Não há suporte para cargas de trabalho mistas porque elas afetam o RTO e o RPO.

### <a name="netbackup-best-practices"></a>Práticas recomendadas do NetBackup

-   O banco de dados do NetBackup deve ser local para o servidor e não residir em um volume do StorSimple.
-   Para a recuperação de desastre, faça backup do banco de dados do NetBackup em um volume do StorSimple.
-   Damos suporte a backups completos e incrementais do NetBackup (também conhecidos como backups incrementais diferenciais no NetBackup) para essa solução. Recomendamos que você não use backups incrementais sintéticos e cumulativos.
-   Os arquivos de dados de backup devem conter apenas os dados de um trabalho específico. Por exemplo, nenhum acréscimo de mídia entre trabalhos diferentes é permitido.

Para obter as configurações mais recentes do NetBackup e as práticas recomendadas para implementar esses requisitos, consulte a documentação do NetBackup em [www.Veritas.com](https://www.veritas.com).


## <a name="retention-policies"></a>Políticas de retenção

Um dos tipos de política de retenção de backup mais comuns é uma política de avô, pai e filho (GFS). Em uma política GFS, um backup incremental é executado diariamente e os backups completos são feitos semanalmente e mensalmente. Essa política resulta em seis volumes em camadas do StorSimple: um volume contém os backups completos semanais, mensais e anuais; os outros cinco volumes armazenam backups incrementais diários.

No exemplo a seguir, usamos uma rotação do GFS. O exemplo pressupõe o seguinte:

-   Dados não duplicados ou compactados são usados.
-   Os backups completos são 1 TiB cada.
-   Os backups incrementais diários são 500 GiB cada.
-   Quatro backups semanais são mantidos por um mês.
-   Doze backups mensais são mantidos por um ano.
-   Um backup anual é mantido por 10 anos.

Com base nas suposições anteriores, crie um volume em camadas StorSimple de 26 TiB para os backups completos mensais e anuais. Crie um volume em camadas StorSimple de 5 TiB para cada um dos backups diários incrementais.

| Retenção de tipo de backup | Tamanho (TiB) | Multiplicador GFS\* | Capacidade total (TiB)  |
|---|---|---|---|
| Semana completa | 1 | 4  | 4 |
| Incremental diária | 0,5 | 20 (ciclos de número igual de semanas por mês) | 12 (2 para cota adicional) |
| Mensal completo | 1 | 12 | 12 |
| Anual completo | 1  | 10 | 10 |
| Requisito do GFS |   | 38 |   |
| Cota adicional  | 4  |   | requisito total de GFS 42  |

\*O multiplicador GFS é o número de cópias que você precisa proteger e manter para atender aos seus requisitos de política de backup.

## <a name="set-up-netbackup-storage"></a>Configurar o armazenamento do NetBackup

### <a name="to-set-up-netbackup-storage"></a>Para configurar o armazenamento do NetBackup

1.  No console de administração do NetBackup, selecione**dispositivos** >  >  **de gerenciamento de mídia e dispositivo**pools de**discos**. No assistente de configuração de pool de discos, selecione o tipo de servidor de armazenamento **AdvancedDisk**e, em seguida, selecione **Avançar**.

    ![Console de administração do NetBackup, assistente de configuração de pool de discos](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

2.  Selecione o servidor e, em seguida, selecione **Avançar**.

    ![Console de administração do NetBackup, selecione o servidor](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

3.  Selecione o volume do StorSimple.

    ![Console de administração do NetBackup, selecione o disco de volume do StorSimple](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

4.  Insira um nome para o destino de backup e, em seguida, selecione **Avançar** > ao**lado** de concluir o assistente.

5.  Examine as configurações e, em seguida, selecione **concluir**.

6.  No final de cada atribuição de volume, altere as configurações do dispositivo de armazenamento para que elas correspondam às recomendadas nas [práticas recomendadas para o StorSimple e](#best-practices-for-storsimple-and-netbackup)o NetBackup.

7. Repita as etapas de 1-6 até que você tenha terminado de atribuir os volumes do StorSimple.

    ![Console de administração do NetBackup, configuração de disco](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Configurar o StorSimple como um destino de backup primário

> [!NOTE]
> Restaurações de dados de um backup que foi colocado em camadas na nuvem ocorrem em velocidades de nuvem.

A figura a seguir mostra o mapeamento de um volume típico para um trabalho de backup. Nesse caso, todos os backups semanais são mapeados para o disco inteiro sábado e os backups incrementais são mapeados para discos incrementais de segunda a sexta-feira. Todos os backups e restaurações são de um volume em camadas do StorSimple.

![Diagrama lógico de configuração de destino de backup primário](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple como um exemplo de agenda do GFS de destino de backup primário

Aqui está um exemplo de uma agenda de rotação do GFS para quatro semanas, mensalmente e anualmente:

| Frequência/tipo de backup | Completo | Incremental (dias 1-5)  |   
|---|---|---|
| Semanalmente (semanas 1-4) | Sábado | Segunda a sexta |
| Custo  | Sábado  |   |
| Anualmente | Sábado  |   |

## <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>Atribuindo volumes do StorSimple a um trabalho de backup do NetBackup

A sequência a seguir pressupõe que o NetBackup e o host de destino são configurados de acordo com as diretrizes do agente do NetBackup.

### <a name="to-assign-storsimple-volumes-to-a-netbackup-backup-job"></a>Para atribuir volumes do StorSimple a um trabalho de backup do NetBackup

1. No console de administração do NetBackup, selecione **Gerenciamento**do NetBackup, clique com o botão direito do mouse em **políticas**e selecione **nova política**.

   ![Console de administração do NetBackup, criar uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

2. Na caixa de diálogo **Adicionar uma nova política** , insira um nome para a política e marque a caixa de seleção **usar assistente de configuração de política** . Selecione **OK**.

   ![Console de administração do NetBackup, caixa de diálogo Adicionar uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

3. No assistente de configuração de política de backup, escolha o tipo de backup desejado e, em seguida, selecione **Avançar**.

   ![Console de administração do NetBackup, selecione o tipo de backup](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

4. Para definir o tipo de política, selecione **padrão**e, em seguida, selecione **Avançar**.

   ![Console de administração do NetBackup, selecionar tipo de política](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

5. Selecione o host, marque a caixa de seleção **detectar sistema operacional cliente** e, em seguida, selecione **Adicionar**. Selecione **Seguinte**.

   ![Console de administração do NetBackup, listar clientes em uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

6. Selecione as unidades que você deseja fazer backup.

   ![Console de administração do NetBackup, seleções de backup para uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

7. Selecione os valores de frequência e retenção que atendem aos seus requisitos de rotação de backup.

   ![Console de administração do NetBackup, frequência de backup e rotação para uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

8. Selecione **próxima** > próximaconclusão > .  Você pode modificar o agendamento após a criação da política.

9. Selecione para expandir a política que você acabou de criar e,em seguida, selecione agendas.

   ![Console de administração do NetBackup, agendas para uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

10. Clique com o botão direito do mouse em **Differential-Inc**, selecione **copiar para novo**e, em seguida, selecione **OK**.

    ![Console de administração do NetBackup, copiar agenda para uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

11. Clique com o botão direito do mouse na agenda criada recentemente e selecione **alterar**.

12. Na guia **atributos** , marque a caixa de seleção **substituir seleção de armazenamento de política** e, em seguida, selecione o volume em que os backups incrementais de segunda-feira vão.

    ![Console de administração do NetBackup, alterar agendamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

13. Na guia **Iniciar janela** , selecione a janela de tempo para seus backups.

    ![Console de administração do NetBackup, alterar janela inicial](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

14. Selecione **OK**.

15. Repita as etapas de 10-14 para cada backup incremental. Selecione o volume e a agenda apropriados para cada backup que você criar.

16. Clique com o botão direito do mouse no agendamento **Differential-Inc** e exclua-o.

17. Modifique seu agendamento completo para atender às suas necessidades de backup.

    ![Console de administração do NetBackup, alterar agenda completa](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

18. Alterar a janela inicial.

    ![Console de administração do NetBackup, alterar a janela inicial](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

19. A agenda final tem esta aparência:

    ![Console de administração do NetBackup, agenda final](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Configurar o StorSimple como um destino de backup secundário

> [!NOTE]
>Restaurações de dados de um backup que foi colocado em camadas na nuvem ocorrem em velocidades de nuvem.

Nesse modelo, você deve ter uma mídia de armazenamento (diferente do StorSimple) para servir como um cache temporário. Por exemplo, você pode usar um volume de RAID (matriz redundante de discos independentes) para acomodar espaço, entrada/saída (e/s) e largura de banda. É recomendável usar RAID 5, 50 e 10.

A figura a seguir mostra os volumes locais de retenção de curto prazo (para o servidor) e os volumes de arquivamento de retenção de longo prazo. Nesse cenário, todos os backups são executados no volume RAID local (para o servidor). Esses backups são periodicamente duplicados e arquivados em um volume de arquivamento. É importante dimensionar o volume RAID local (para o servidor) para que ele possa lidar com os requisitos de desempenho e capacidade de retenção de curto prazo.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple como um exemplo de GFS de destino de backup secundário

![StorSimple como um diagrama lógico de destino de backup secundário](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

A tabela a seguir mostra como configurar backups para execução nos discos locais e do StorSimple. Ele inclui requisitos de capacidade total e individuais.

### <a name="backup-configuration-and-capacity-requirements"></a>Requisitos de configuração e capacidade de backup

| Tipo e retenção de backup | Armazenamento configurado | Tamanho (TiB) | Multiplicador GFS | Capacidade\* total (TIB) |
|---|---|---|---|---|
| Semana 1 (completa e incremental) |Disco local (curto prazo)| 1 | 1 | 1 |
| StorSimple Weeks 2-4 |Disco StorSimple (longo prazo) | 1 | 4 | 4 |
| Mensal completo |Disco StorSimple (longo prazo) | 1 | 12 | 12 |
| Anual completo |Disco StorSimple (longo prazo) | 1 | 1 | 1 |
|Requisito de tamanho dos volumes do GFS |  |  |  | 18*|

\*A capacidade total inclui 17 TiB de discos do StorSimple e 1 TiB de volume RAID local.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>Agenda de exemplo do GFS: Agendamento semanal, mensal e anual de rotação do GFS

| Semana | Completo | Incremental dia 1 | Incremental dia 2 | Incremental dia 3 | Incremental dia 4 | Incremental dia 5 |
|---|---|---|---|---|---|---|
| Semana 1 | Volume RAID local  | Volume RAID local | Volume RAID local | Volume RAID local | Volume RAID local | Volume RAID local |
| Semana 2 | StorSimple Weeks 2-4 |   |   |   |   |   |
| Semana 3 | StorSimple Weeks 2-4 |   |   |   |   |   |
| Semana 4 | StorSimple Weeks 2-4 |   |   |   |   |   |
| Custo | StorSimple mensal |   |   |   |   |   |
| Anualmente | StorSimple anualmente  |   |   |   |   |   |


## <a name="assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Atribuir volumes do StorSimple a um trabalho de arquivamento e duplicação do NetBackup

Como o NetBackup oferece uma ampla gama de opções para armazenamento e gerenciamento de mídia, recomendamos que você consulte a VERITAS ou o arquiteto do NetBackup para avaliar corretamente seus requisitos de SLP (política de ciclo de vida de armazenamento).

Depois de definir os pools de discos iniciais, você precisa definir três políticas de ciclo de vida de armazenamento adicionais para um total de quatro políticas:
* LocalRAIDVolume
* StorSimpleWeek2-4
* StorSimpleMonthlyFulls
* StorSimpleYearlyFulls

### <a name="to-assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Para atribuir volumes do StorSimple a um trabalho de arquivamento e duplicação do NetBackup

1. No console de administração do NetBackup, selecione **armazenamento** > **políticas** > de ciclo de vida de armazenamento**nova política de ciclo de vida de armazenamento**.

   ![Console de administração do NetBackup, nova política de ciclo de vida de armazenamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

2. Insira um nome para o instantâneo e, em seguida, selecione **Adicionar**.

3. Na caixa de diálogo **nova operação** , na guia **Propriedades** , em **operação**, selecione **backup**. Selecione os valores desejados para **armazenamento de destino**, tipo de **retenção**e período de **retenção**. Selecione **OK**.

   ![Console de administração do NetBackup, caixa de diálogo nova operação](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

   Isso define a primeira operação e o repositório de backup.

4. Selecione para realçar a operação anterior e, em seguida, selecione **Adicionar**. Na caixa de diálogo **alterar operação de armazenamento** , selecione os valores desejados para **armazenamento de destino**, **tipo de retenção**e **período de retenção**.

   ![Console de administração do NetBackup, caixa de diálogo Alterar operação de armazenamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

5. Selecione para realçar a operação anterior e, em seguida, selecione **Adicionar**. Na caixa de diálogo **nova política de ciclo de vida de armazenamento** , adicione backups mensais por um ano.

   ![Console de administração do NetBackup, caixa de diálogo nova política de ciclo de vida de armazenamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

6. Repita as etapas de 4-5 até que você tenha criado a política de retenção de SLP abrangente de que precisa.

   ![Console de administração do NetBackup, adicionar políticas na caixa de diálogo nova política de ciclo de vida de armazenamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

7. Quando terminar de definir sua política de retenção de SLP, em **política**, defina uma política de backup seguindo as etapas detalhadas em [atribuindo volumes do StorSimple a um trabalho de backup](#assigning-storsimple-volumes-to-a-netbackup-backup-job)do NetBackup.

8. Em **agendamentos**, na caixa de diálogo **alterar agendamento** , clique com o botão direito do mouse em **completo**e selecione **alterar**.

   ![Console de administração do NetBackup, caixa de diálogo Alterar agenda](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

9. Marque a caixa de seleção **substituir seleção de armazenamento de política** e selecione a política de retenção de SLP criada nas etapas 1-6.

   ![Console de administração do NetBackup, substituir seleção de armazenamento de política](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

10. Selecione **OK**e, em seguida, repita para o agendamento de backup incremental.

    ![Console de administração do NetBackup, caixa de diálogo Alterar agenda para backups incrementais](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)


| Retenção de tipo de backup | Tamanho (TiB) | Multiplicador GFS\* | Capacidade total (TiB)  |
|---|---|---|---|
| Semana completa |  1  |  4 | 4  |
| Incremental diária  | 0,5  | 20 (os ciclos são iguais ao número de semanas por mês) | 12 (2 para cota adicional) |
| Mensal completo  | 1 | 12 | 12 |
| Anual completo | 1  | 10 | 10 |
| Requisito do GFS  |     |     | 38 |
| Cota adicional  | 4  |    | requisito total de GFS 42 |

\*O multiplicador GFS é o número de cópias que você precisa proteger e manter para atender aos seus requisitos de política de backup.

## <a name="storsimple-cloud-snapshots"></a>Instantâneos de nuvem do StorSimple

Os instantâneos de nuvem do StorSimple protegem os dados que residem em seu dispositivo StorSimple. A criação de um instantâneo de nuvem é equivalente a enviar fitas de backup local para um recurso externo. Se você usar o armazenamento com redundância geográfica do Azure, a criação de um instantâneo de nuvem é equivalente a enviar fitas de backup para vários sites. Se precisar restaurar um dispositivo após um desastre, você poderá colocar outro dispositivo StorSimple online e realizar um failover. Após o failover, você poderá acessar os dados (em velocidades de nuvem) do instantâneo de nuvem mais recente.

A seção a seguir descreve como criar um script curto para iniciar e excluir instantâneos de nuvem do StorSimple durante o pós-processamento do backup.

> [!NOTE]
> Instantâneos que são criados manual ou programaticamente não seguem a política de expiração do instantâneo do StorSimple. Esses instantâneos devem ser excluídos manual ou programaticamente.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Iniciar e excluir instantâneos de nuvem usando um script

> [!NOTE]
> Avalie cuidadosamente as repercussões de conformidade e retenção de dados antes de excluir um instantâneo do StorSimple. Para obter mais informações sobre como executar um script de pós-backup, consulte a [documentação](http://www.veritas.com/docs/000094423)do NetBackup.

### <a name="backup-lifecycle"></a>Ciclo de vida do backup

![Diagrama de ciclo de vida de backup](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

### <a name="requirements"></a>Requisitos

-   O servidor que executa o script deve ter acesso aos recursos de nuvem do Azure.
-   A conta de usuário deve ter as permissões necessárias.
-   Uma política de backup do StorSimple com os volumes associados do StorSimple deve ser configurada, mas não ativada.
-   Você precisará do nome do recurso do StorSimple, da chave de registro, do nome do dispositivo e da ID da política de backup.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Para iniciar ou excluir um instantâneo de nuvem

1. [Instalar o Azure PowerShell](/powershell/azure/overview).
2. Baixe e configure o script do PowerShell [Manage-CloudSnapshots. ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) .
3. No servidor que executa o script, execute o PowerShell como administrador. Certifique-se de executar o script `-WhatIf $true` com para ver as alterações que o script fará. Quando a validação for concluída, passe `-WhatIf $false`. Execute o comando abaixo:
   ```powershell
   .\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
   ```
4. Adicione o script ao seu trabalho de backup no NetBackup. Para fazer isso, edite os comandos de pré-processamento e pós-processamento das opções de trabalho do NetBackup.

> [!NOTE]
> Recomendamos que você execute sua política de backup de instantâneo de nuvem do StorSimple como um script de pós-processamento no final do seu trabalho de backup diário. Para obter mais informações sobre como fazer backup e restaurar seu ambiente de aplicativo de backup para ajudá-lo a atender ao RPO e ao RTO, consulte o seu arquiteto de backup.

## <a name="storsimple-as-a-restore-source"></a>StorSimple como uma fonte de restauração

Restaurações de um dispositivo StorSimple funcionam como restaurações de qualquer dispositivo de armazenamento em bloco. Restaurações de dados que estão em camadas na nuvem ocorrem em velocidades de nuvem. Para dados locais, as restaurações ocorrem na velocidade do disco local do dispositivo. Para obter informações sobre como executar uma restauração, consulte a [documentação](http://www.veritas.com/docs/000094423)do NetBackup. Recomendamos que você esteja em conformidade com as práticas recomendadas de restauração do NetBackup.

## <a name="storsimple-failover-and-disaster-recovery"></a>Failover e recuperação de desastre do StorSimple

> [!NOTE]
> Para cenários de destino de backup, o dispositivo de nuvem StorSimple não tem suporte como um destino de restauração.

Um desastre pode ser causado por uma variedade de fatores. A tabela a seguir lista os cenários comuns de recuperação de desastre.

| Cenário | Impacto | Como recuperar | Notas |
|---|---|---|---|
| Falha do dispositivo StorSimple | As operações de backup e restauração são interrompidas. | Substitua o dispositivo com falha e execute o [failover e a recuperação de desastre do StorSimple](storsimple-device-failover-disaster-recovery.md). | Se você precisar executar uma restauração após a recuperação do dispositivo, os conjuntos de trabalho de dados completos serão recuperados da nuvem para o novo dispositivo. Todas as operações estão em velocidades de nuvem. O processo de reexame de índice e catálogo pode fazer com que todos os conjuntos de backup sejam examinados e extraídos da camada de nuvem para a camada de dispositivo local, o que pode ser um processo demorado. |
| Falha do servidor do NetBackup | As operações de backup e restauração são interrompidas. | Recrie o servidor de backup e execute a restauração do banco de dados. | Você deve recompilar ou restaurar o servidor do NetBackup no site de recuperação de desastre. Restaure o banco de dados para o ponto mais recente. Se o banco de dados restaurado do NetBackup não estiver em sincronia com os trabalhos de backup mais recentes, a indexação e a catalogação serão necessárias. Esse processo de exame de índice e de catálogo pode fazer com que todos os conjuntos de backup sejam examinados e extraídos da camada de nuvem para a camada de dispositivo local. Isso o torna mais demorado. |
| Falha no site que resulta na perda do servidor de backup e do StorSimple | As operações de backup e restauração são interrompidas. | Restaure o StorSimple primeiro e, em seguida, restaure o NetBackup. | Restaure o StorSimple primeiro e, em seguida, restaure o NetBackup. Se você precisar executar uma restauração após a recuperação do dispositivo, os conjuntos de trabalho de dados completos serão recuperados da nuvem para o novo dispositivo. Todas as operações estão em velocidades de nuvem. |

## <a name="references"></a>Referências

Os documentos a seguir foram referenciados neste artigo:

- [Configuração de e/s de vários caminhos do StorSimple](storsimple-configure-mpio-windows-server.md)
- [Cenários de armazenamento: Provisionamento dinâmico](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Usando unidades GPT](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Configurar cópias de sombra para pastas compartilhadas](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre como [restaurar de um conjunto de backup](storsimple-restore-from-backup-set-u2.md).
- Saiba mais sobre como executar [failover de dispositivo e recuperação de desastre](storsimple-device-failover-disaster-recovery.md).
