---
title: StorSimple 8000 Series como destino de backup com Veeam | Microsoft Docs
description: Descreve a configuração de destino de backup do StorSimple com Veeam.
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
ms.date: 12/06/2016
ms.author: matd
ms.openlocfilehash: 3ebf464fed1480e7452f246f04f3906faf0dd219
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "67875307"
---
# <a name="storsimple-as-a-backup-target-with-veeam"></a>StorSimple como um destino de backup com Veeam

## <a name="overview"></a>Descrição geral

O Azure StorSimple é uma solução de armazenamento de nuvem híbrida da Microsoft. O StorSimple aborda as complexidades do crescimento exponencial de dados usando uma conta de armazenamento do Azure como uma extensão da solução local e colocando automaticamente os dados em camadas no armazenamento local e no armazenamento em nuvem.

Neste artigo, discutiremos a integração do StorSimple com o Veeam e as práticas recomendadas para a integração de ambas as soluções. Também fazemos recomendações sobre como configurar o Veeam para se integrar melhor ao StorSimple. Adiamos para Veeam práticas recomendadas, arquitetos de backup e administradores para a melhor maneira de configurar o Veeam para atender aos requisitos de backup individuais e aos SLAs (contratos de nível de serviço).

Embora Ilustremos as etapas de configuração e os principais conceitos, este artigo não é, de maneira alguma, uma configuração passo a passo ou um guia de instalação. Supomos que os componentes básicos e a infraestrutura estejam em ordem de funcionamento e estejam prontos para dar suporte aos conceitos que descrevemos.

### <a name="who-should-read-this"></a>Quem deve ler isso?

As informações neste artigo serão mais úteis para administradores de backup, administradores de armazenamento e arquitetos de armazenamento que têm conhecimento de armazenamento, Windows Server 2012 R2, Ethernet, serviços de nuvem e Veeam.

### <a name="supported-versions"></a>Versões suportadas

-   Veeam 9 e versões posteriores
-   [StorSimple atualização 3 e versões posteriores](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Por que o StorSimple como um destino de backup?

O StorSimple é uma boa opção para um destino de backup porque:

-   Ele fornece armazenamento local padrão para aplicativos de backup para usar como um destino de backup rápido, sem nenhuma alteração. Você também pode usar o StorSimple para uma restauração rápida de backups recentes.
-   Sua disposição em camadas de nuvem é integrada diretamente a uma conta de armazenamento em nuvem do Azure para usar o armazenamento do Azure econômico.
-   Ele fornece automaticamente armazenamento externo para recuperação de desastres.


## <a name="key-concepts"></a>Conceitos-chave

Assim como acontece com qualquer solução de armazenamento, uma avaliação cuidadosa do desempenho de armazenamento da solução, dos SLAs, da taxa de alteração e das necessidades de aumento da capacidade é fundamental para o sucesso. A ideia principal é que, ao introduzir uma camada de nuvem, os tempos de acesso e as taxas de transferência para a nuvem desempenham um papel fundamental na capacidade do StorSimple de realizar seu trabalho.

O StorSimple foi projetado para fornecer armazenamento a aplicativos que operam em um conjunto de dados de trabalho bem definido (dados ativos). Nesse modelo, o conjunto de dados de trabalho é armazenado nas camadas locais e o conjunto restante de dados não trabalha/frio/arquivado é colocado em camadas na nuvem. Esse modelo é representado na figura a seguir. A linha verde quase simples representa os dados armazenados nas camadas locais do dispositivo StorSimple. A linha vermelha representa a quantidade total de dados armazenados na solução StorSimple em todas as camadas. O espaço entre a linha verde plana e a curva de vermelho exponencial representa a quantidade total de dados armazenados na nuvem.


![Diagrama de camadas storsimple](./media/storsimple-configure-backup-target-using-veeam/image1.jpg) de camada do storsimple

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

| Capacidade de armazenamento | 8100 | 8600 |
|---|---|---|
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

![StorSimple como um diagrama lógico de destino de backup primário](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Etapas lógicas de backup de destino principal

1.  O servidor de backup entra em contato com o agente de backup de destino e o agente de backup transmite dados para o servidor de backup.
2.  O servidor de backup grava dados nos volumes em camadas do StorSimple.
3.  O servidor de backup atualiza o banco de dados do catálogo e, em seguida, conclui o trabalho de backup.
4.  Um script de instantâneo dispara o Gerenciador de instantâneos de nuvem do StorSimple (iniciar ou excluir).
5.  O servidor de backup exclui os backups expirados com base em uma política de retenção.

### <a name="primary-target-restore-logical-steps"></a>Etapas lógicas de restauração de destino primário

1.  O servidor de backup começa a restaurar os dados apropriados do repositório de armazenamento.
2.  O agente de backup recebe os dados do servidor de backup.
3.  O servidor de backup conclui o trabalho de restauração.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple como um destino de backup secundário

Nesse cenário, os volumes do StorSimple são usados principalmente para retenção ou arquivamento de longo prazo.

A figura a seguir mostra uma arquitetura na qual backups e restaurações iniciais visam um volume de alto desempenho. Esses backups são copiados e arquivados em um volume em camadas do StorSimple em um agendamento definido.

É importante dimensionar o volume de alto desempenho para que ele possa lidar com a capacidade e os requisitos de desempenho da sua política de retenção.

![StorSimple como um diagrama lógico de destino de backup secundário](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Etapas lógicas de backup de destino secundário

1.  O servidor de backup entra em contato com o agente de backup de destino e o agente de backup transmite dados para o servidor de backup.
2.  O servidor de backup grava dados no armazenamento de alto desempenho.
3.  O servidor de backup atualiza o banco de dados do catálogo e, em seguida, conclui o trabalho de backup.
4.  O servidor de backup copia backups para o StorSimple com base em uma política de retenção.
5.  Um script de instantâneo dispara o Gerenciador de instantâneos de nuvem do StorSimple (iniciar ou excluir).
6.  O servidor de backup exclui os backups expirados com base em uma política de retenção.

### <a name="secondary-target-restore-logical-steps"></a>Etapas lógicas de restauração de destino secundário

1.  O servidor de backup começa a restaurar os dados apropriados do repositório de armazenamento.
2.  O agente de backup recebe os dados do servidor de backup.
3.  O servidor de backup conclui o trabalho de restauração.

## <a name="deploy-the-solution"></a>Implementar a solução

A implantação da solução requer três etapas:

1. Prepare a infraestrutura de rede.
2. Implante seu dispositivo StorSimple como um destino de backup.
3. Implantar Veeam.

Cada etapa é discutida detalhadamente nas seções a seguir.

### <a name="set-up-the-network"></a>Configurar a rede

Como o StorSimple é uma solução integrada à nuvem do Azure, o StorSimple requer uma conexão ativa e em funcionamento com a nuvem do Azure. Essa conexão é usada para operações como instantâneos de nuvem, gerenciamento de dados e transferência de metadados e para camadas de dados mais antigos e menos acessados para o armazenamento em nuvem do Azure.

Para que a solução seja executada de forma ideal, recomendamos que você siga estas práticas recomendadas de rede:

-   O link que conecta sua camada do StorSimple ao Azure deve atender aos seus requisitos de largura de banda. Faça isso aplicando o nível de QoS (qualidade de serviço) necessário aos comutadores de infraestrutura para corresponder aos SLAs de RPO e RTO (objetivo de tempo de recuperação).
-   As latências máximas de acesso de armazenamento de blob do Azure devem ser cerca de 80 ms.

### <a name="deploy-storsimple"></a>Implantar o StorSimple

Para obter diretrizes passo a passo de implantação do StorSimple, consulte [implantar seu dispositivo StorSimple local](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-veeam"></a>Implantar Veeam

Para obter as práticas recomendadas de instalação do Veeam, confira [Veeam Backup & práticas recomendadas de replicação](https://bp.veeam.expert/)e leia o guia do usuário no [centro de ajuda do Veeam (documentação técnica)](https://www.veeam.com/documentation-guides-datasheets.html).

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

- Não use volumes estendidos (criados pelo gerenciamento de disco do Windows). Não há suporte para volumes estendidos.
- Formate seus volumes usando NTFS com tamanho de unidade de alocação de 64-KB.
- Mapeie os volumes do StorSimple diretamente para o servidor Veeam.
    - Use iSCSI para servidores físicos.


## <a name="best-practices-for-storsimple-and-veeam"></a>Práticas recomendadas para StorSimple e Veeam

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

### <a name="veeam-best-practices"></a>Práticas recomendadas do Veeam

-   O banco de dados Veeam deve ser local para o servidor e não residir em um volume StorSimple.
-   Para a recuperação de desastre, faça backup do banco de dados Veeam em um volume do StorSimple.
-   Damos suporte a backups completos e incrementais do Veeam para esta solução. Recomendamos que você não use backups sintéticos e diferenciais.
-   Os arquivos de dados de backup devem conter apenas os dados de um trabalho específico. Por exemplo, nenhum acréscimo de mídia entre trabalhos diferentes é permitido.
-   Desative a verificação do trabalho. Se necessário, a verificação deve ser agendada após o trabalho de backup mais recente. É importante entender que esse trabalho afeta a janela de backup.
-   Ative a pré-alocação de mídia.
-   Certifique-se de que o processamento paralelo esteja ativado.
-   Desative a compactação.
-   Desative a eliminação de duplicação no trabalho de backup.
-   Defina a otimização para o **destino de LAN**.
-   Ative **criar backup completo ativo** (a cada 2 semanas).
-   No repositório de backup, configure **usar arquivos de backup por VM**.
-   Defina **usar vários fluxos de carregamento por trabalho** para **8** (um máximo de 16 é permitido). Ajuste esse número para cima ou para baixo com base na utilização da CPU no dispositivo StorSimple.

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

## <a name="set-up-veeam-storage"></a>Configurar o armazenamento do Veeam

### <a name="to-set-up-veeam-storage"></a>Para configurar o armazenamento Veeam

1.  No console de backup e replicação do Veeam, em **ferramentas de repositório**, vá para infraestrutura de **backup**. Clique com o botão direito do mouse em repositórios de **backup**e selecione **Adicionar repositório de backup**.

    ![Console de gerenciamento do Veeam, página de repositório de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

2.  Na caixa de diálogo **novo repositório de backup** , insira um nome e uma descrição para o repositório. Selecione **Seguinte**.

    ![Console de gerenciamento do Veeam, página nome e descrição](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

3.  Para o tipo, selecione **Microsoft Windows Server**. Selecione o servidor Veeam. Selecione **Seguinte**.

    ![Console de gerenciamento do Veeam, selecione o tipo de repositório de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

4.  Para especificar o **local**, procure e selecione o volume. Marque a caixa de seleção **limitar máximo de tarefas simultâneas a:** e defina o valor como **4**. Isso garante que apenas quatro discos virtuais estejam sendo processados simultaneamente enquanto cada VM (máquina virtual) é processada. Selecione o botão **avançado** .

    ![Console de gerenciamento do Veeam, selecione volume](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


5.  Na caixa de diálogo **configurações de compatibilidade de armazenamento** , marque a caixa de seleção **usar arquivos de backup por VM** .

    ![Console de gerenciamento do Veeam, configurações de compatibilidade de armazenamento](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

6.  Na caixa de diálogo **novo repositório de backup** , marque a caixa de seleção **habilitar serviço NFS do vPower no servidor de montagem (recomendado)** . Selecione **Seguinte**.

    ![Console de gerenciamento do Veeam, página de repositório de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

7.  Examine as configurações e, em seguida, selecione **Avançar**.

    ![Console de gerenciamento do Veeam, página de repositório de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    Um repositório é adicionado ao servidor Veeam.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Configurar o StorSimple como um destino de backup primário

> [!IMPORTANT]
> A restauração de dados de um backup que foi colocado em camadas na nuvem ocorre em velocidades de nuvem.

A figura a seguir mostra o mapeamento de um volume típico para um trabalho de backup. Nesse caso, todos os backups semanais são mapeados para o disco inteiro sábado e os backups incrementais são mapeados para discos incrementais de segunda a sexta-feira. Todos os backups e restaurações são de um volume em camadas do StorSimple.

![Diagrama lógico de configuração de destino de backup primário](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple como um exemplo de agenda do GFS de destino de backup primário

Aqui está um exemplo de uma agenda de rotação do GFS para quatro semanas, mensalmente e anualmente:

| Frequência/tipo de backup | Completo | Incremental (dias 1-5)  |   
|---|---|---|
| Semanalmente (semanas 1-4) | Sábado | Segunda a sexta |
| Custo  | Sábado  |   |
| Anualmente | Sábado  |   |


### <a name="assign-storsimple-volumes-to-a-veeam-backup-job"></a>Atribuir volumes do StorSimple a um trabalho de backup do Veeam

Para o cenário de destino de backup primário, crie um trabalho diário com seu volume do StorSimple Veeam primário. Para um cenário de destino de backup secundário, crie um trabalho diário usando o DAS (armazenamento anexado direto), NAS (armazenamento conectado à rede) ou apenas um armazenamento de vários discos (JBOD).

#### <a name="to-assign-storsimple-volumes-to-a-veeam-backup-job"></a>Para atribuir volumes do StorSimple a um trabalho de backup do Veeam

1.  No console de backup e replicação do Veeam, selecione **backup & replicação**. Clique com o botão direito do mouse em **backup**e selecione **VMware** ou **Hyper-V**, dependendo do seu ambiente.

    ![Console de gerenciamento do Veeam, novo trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

2.  Na caixa de diálogo **novo trabalho de backup** , insira um nome e uma descrição para o trabalho de backup diário.

    ![Console de gerenciamento do Veeam, página novo trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

3.  Selecione uma máquina virtual para fazer backup.

    ![Console de gerenciamento do Veeam, página novo trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

4.  Selecione os valores desejados para o **proxy de backup** e o **repositório de backup**. Selecione um valor para os **pontos de restauração a serem mantidos no disco** de acordo com as definições de RPO e RTO para seu ambiente no armazenamento anexado localmente. Selecione **Avançadas**.

    ![Console de gerenciamento do Veeam, página novo trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

5. Na caixa de diálogo **Configurações avançadas** , na guia **backup** , selecione **incremental**. Certifique-se de que a caixa de seleção **criar backups completos sintéticos periodicamente** está desmarcada. Marque a caixa de seleção **criar backups completos ativos periodicamente** . Em **backup completo ativo**, marque a caixa de seleção **semanalmente nos dias selecionados** para sábado.

    ![Console de gerenciamento do Veeam, página novas configurações avançadas de trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

6. Na guia **armazenamento** , verifique se a caixa de seleção **habilitar eliminação** de duplicação de dados embutidos está desmarcada. Marque a caixa de seleção **excluir blocos de arquivos alternados** e marque a caixa de seleção **excluir blocos de arquivos excluídos** . Defina o **nível** de compactação como **nenhum**. Para desempenho e eliminação de duplicação equilibrados, defina **otimização de armazenamento** para **destino de LAN**. Selecione **OK**.

    ![Console de gerenciamento do Veeam, página novas configurações avançadas de trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    Para obter informações sobre as configurações de eliminação de duplicação e compactação do Veeam, consulte [compactação e eliminação de duplicação de dados](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html).

7.  Na caixa de diálogo **Editar trabalho de backup** , você pode marcar a caixa de seleção **habilitar processamento com reconhecimento de aplicativos** (opcional).

    ![Console de gerenciamento do Veeam, nova página de processamento de convidado de trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

8.  Defina a agenda para ser executada uma vez por dia, ao mesmo tempo que você pode especificar.

    ![Console de gerenciamento do Veeam, página novo agendamento de trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Configurar o StorSimple como um destino de backup secundário

> [!NOTE]
> Restaurações de dados de um backup que foi colocado em camadas na nuvem ocorrem em velocidades de nuvem.

Nesse modelo, você deve ter uma mídia de armazenamento (diferente do StorSimple) para servir como um cache temporário. Por exemplo, você pode usar um volume de RAID (matriz redundante de discos independentes) para acomodar espaço, entrada/saída (e/s) e largura de banda. É recomendável usar RAID 5, 50 e 10.

A figura a seguir mostra os volumes locais de retenção de curto prazo (para o servidor) e os volumes de arquivamento de retenção de longo prazo. Nesse cenário, todos os backups são executados no volume RAID local (para o servidor). Esses backups são duplicados e arquivados periodicamente em um volume de arquivamento. É importante dimensionar o volume RAID local (para o servidor) para que ele possa lidar com os requisitos de desempenho e capacidade de retenção de curto prazo.

![StorSimple como diagrama lógico de destino de backup secundário](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple como um exemplo de GFS de destino de backup secundário

A tabela a seguir mostra como configurar backups para execução nos discos locais e do StorSimple. Ele inclui requisitos de capacidade total e individuais.

| Tipo e retenção de backup | Armazenamento configurado | Tamanho (TiB) | Multiplicador GFS | Capacidade\* total (TIB) |
|---|---|---|---|---|
| Semana 1 (completa e incremental) |Disco local (curto prazo)| 1 | 1 | 1 |
| StorSimple Weeks 2-4 |Disco StorSimple (longo prazo) | 1 | 4 | 4 |
| Mensal completo |Disco StorSimple (longo prazo) | 1 | 12 | 12 |
| Anual completo |Disco StorSimple (longo prazo) | 1 | 1 | 1 |
|Requisito de tamanho dos volumes do GFS |  |  |  | 18*|

\*A capacidade total inclui 17 TiB de discos do StorSimple e 1 TiB de volume RAID local.


### <a name="gfs-example-schedule"></a>Agenda de exemplo do GFS

Agendamento semanal, mensal e anual de rotação do GFS

| Semana | Completo | Incremental dia 1 | Incremental dia 2 | Incremental dia 3 | Incremental dia 4 | Incremental dia 5 |
|---|---|---|---|---|---|---|
| Semana 1 | Volume RAID local  | Volume RAID local | Volume RAID local | Volume RAID local | Volume RAID local | Volume RAID local |
| Semana 2 | StorSimple Weeks 2-4 |   |   |   |   |   |
| Semana 3 | StorSimple Weeks 2-4 |   |   |   |   |   |
| Semana 4 | StorSimple Weeks 2-4 |   |   |   |   |   |
| Custo | StorSimple mensal |   |   |   |   |   |
| Anualmente | StorSimple anualmente  |   |   |   |   |   |

### <a name="assign-storsimple-volumes-to-a-veeam-copy-job"></a>Atribuir volumes do StorSimple a um trabalho de cópia Veeam

#### <a name="to-assign-storsimple-volumes-to-a-veeam-copy-job"></a>Para atribuir volumes do StorSimple a um trabalho de cópia Veeam

1.  No console de backup e replicação do Veeam, selecione **backup & replicação**. Clique com o botão direito do mouse em **backup**e selecione **VMware** ou **Hyper-V**, dependendo do seu ambiente.

    ![Console de gerenciamento do Veeam, página novo trabalho de cópia de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

2.  Na caixa de diálogo **novo trabalho de cópia de backup** , insira um nome e uma descrição para o trabalho.

    ![Console de gerenciamento do Veeam, página novo trabalho de cópia de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

3.  Selecione as VMs que você deseja processar. Selecione de backups e, em seguida, selecione o backup diário que você criou anteriormente.

    ![Console de gerenciamento do Veeam, página novo trabalho de cópia de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

4.  Exclua objetos do trabalho de cópia de backup, se necessário.

5.  Selecione o repositório de backup e defina um valor para os **pontos de restauração a serem mantidos**. Certifique-se de marcar a caixa de seleção **manter os seguintes pontos de restauração para fins de arquivamento** . Defina a frequência de backup e, em seguida, selecione **avançado**.

    ![Console de gerenciamento do Veeam, página novo trabalho de cópia de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

6.  Especifique as seguintes configurações avançadas:

    * Na guia **manutenção** , desative a proteção contra corrupção no nível de armazenamento.

    ![Console de gerenciamento do Veeam, nova página de configurações avançadas do trabalho de cópia de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    * Na guia **armazenamento** , verifique se a eliminação de duplicação e a compactação estão desativadas.

    ![Console de gerenciamento do Veeam, nova página de configurações avançadas do trabalho de cópia de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

7.  Especifique que a transferência de dados é direta.

8.  Defina o agendamento da janela de cópia de backup de acordo com suas necessidades e, em seguida, conclua o assistente.

Para obter mais informações, consulte [criar trabalhos de cópia de backup](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html).

## <a name="storsimple-cloud-snapshots"></a>Instantâneos de nuvem do StorSimple

Os instantâneos de nuvem do StorSimple protegem os dados que residem em seu dispositivo StorSimple. A criação de um instantâneo de nuvem é equivalente a enviar fitas de backup local para um recurso externo. Se você usar o armazenamento com redundância geográfica do Azure, a criação de um instantâneo de nuvem é equivalente a enviar fitas de backup para vários sites. Se precisar restaurar um dispositivo após um desastre, você poderá colocar outro dispositivo StorSimple online e realizar um failover. Após o failover, você poderá acessar os dados (em velocidades de nuvem) do instantâneo de nuvem mais recente.

A seção a seguir descreve como criar um script curto para iniciar e excluir instantâneos de nuvem do StorSimple durante o pós-processamento do backup.

> [!NOTE]
> Instantâneos que são criados manual ou programaticamente não seguem a política de expiração do instantâneo do StorSimple. Esses instantâneos devem ser excluídos manual ou programaticamente.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Iniciar e excluir instantâneos de nuvem usando um script

> [!NOTE]
> Avalie cuidadosamente as repercussões de conformidade e retenção de dados antes de excluir um instantâneo do StorSimple. Para obter mais informações sobre como executar um script de pós-backup, consulte a documentação do Veeam.


### <a name="backup-lifecycle"></a>Ciclo de vida do backup

![Diagrama de ciclo de vida de backup](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

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
4. Para adicionar o script ao seu trabalho de backup, edite suas opções avançadas de trabalho do Veeam.

    ![Guia scripts de configurações avançadas de backup do Veeam](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

Recomendamos que você execute sua política de backup de instantâneo de nuvem do StorSimple como um script de pós-processamento no final do seu trabalho de backup diário. Para obter mais informações sobre como fazer backup e restaurar seu ambiente de aplicativo de backup para ajudá-lo a atender ao RPO e ao RTO, consulte o seu arquiteto de backup.

## <a name="storsimple-as-a-restore-source"></a>StorSimple como uma fonte de restauração

Restaurações de um dispositivo StorSimple funcionam como restaurações de qualquer dispositivo de armazenamento em bloco. Restaurações de dados que estão em camadas na nuvem ocorrem em velocidades de nuvem. Para dados locais, as restaurações ocorrem na velocidade do disco local do dispositivo.

Com o Veeam, você obtém recuperação rápida, granular e em nível de arquivo por meio do StorSimple por meio de exibições internas do Explorer no console do Veeam. Use os Veeam Explorers para recuperar itens individuais, como mensagens de email, Active Directory objetos e itens do SharePoint de backups. A recuperação pode ser feita sem a interrupção da VM local. Você também pode fazer uma recuperação pontual para bancos de dados SQL do Azure e Oracle. O Veeam e o StorSimple tornam o processo de recuperação em nível de item do Azure rápido e fácil. Para obter informações sobre como executar uma restauração, consulte a documentação do Veeam:

- Para o [Exchange Server](https://www.veeam.com/microsoft-exchange-recovery.html)
- Para [Active Directory](https://www.veeam.com/microsoft-active-directory-explorer.html)
- Para [SQL Server](https://www.veeam.com/microsoft-sql-server-explorer.html)
- Para [SharePoint](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)
- Para [Oracle](https://www.veeam.com/oracle-backup-recovery-explorer.html)


## <a name="storsimple-failover-and-disaster-recovery"></a>Failover e recuperação de desastre do StorSimple

> [!NOTE]
> Para cenários de destino de backup, o dispositivo de nuvem StorSimple não tem suporte como um destino de restauração.

Um desastre pode ser causado por uma variedade de fatores. A tabela a seguir lista os cenários comuns de recuperação de desastre.

| Cenário | Impacto | Como recuperar | Notas |
|---|---|---|---|
| Falha do dispositivo StorSimple | As operações de backup e restauração são interrompidas. | Substitua o dispositivo com falha e execute o [failover e a recuperação de desastre do StorSimple](storsimple-device-failover-disaster-recovery.md). | Se você precisar executar uma restauração após a recuperação do dispositivo, os conjuntos de trabalho de dados completos serão recuperados da nuvem para o novo dispositivo. Todas as operações estão em velocidades de nuvem. O processo de reexame de índice e catálogo pode fazer com que todos os conjuntos de backup sejam examinados e extraídos da camada de nuvem para a camada de dispositivo local, o que pode ser um processo demorado. |
| Falha do servidor Veeam | As operações de backup e restauração são interrompidas. | Reconstrua o servidor de backup e execute a restauração do banco de dados conforme detalhado no [centro de ajuda do Veeam (documentação técnica)](https://www.veeam.com/documentation-guides-datasheets.html).  | Você deve recompilar ou restaurar o servidor Veeam no site de recuperação de desastre. Restaure o banco de dados para o ponto mais recente. Se o banco de dados Veeam restaurado não estiver em sincronia com os trabalhos de backup mais recentes, a indexação e a catalogação serão necessárias. Esse processo de exame de índice e de catálogo pode fazer com que todos os conjuntos de backup sejam examinados e extraídos da camada de nuvem para a camada de dispositivo local. Isso o torna mais demorado. |
| Falha no site que resulta na perda do servidor de backup e do StorSimple | As operações de backup e restauração são interrompidas. | Restaure o StorSimple primeiro e, em seguida, restaure Veeam. | Restaure o StorSimple primeiro e, em seguida, restaure Veeam. Se você precisar executar uma restauração após a recuperação do dispositivo, os conjuntos de trabalho de dados completos serão recuperados da nuvem para o novo dispositivo. Todas as operações estão em velocidades de nuvem. |


## <a name="references"></a>Referências

Os documentos a seguir foram referenciados neste artigo:

- [Configuração de e/s de vários caminhos do StorSimple](storsimple-configure-mpio-windows-server.md)
- [Cenários de armazenamento: Provisionamento dinâmico](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Usando unidades GPT](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Configurar cópias de sombra para pastas compartilhadas](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre como [restaurar de um conjunto de backup](storsimple-restore-from-backup-set-u2.md).
- Saiba mais sobre como executar [failover de dispositivo e recuperação de desastre](storsimple-device-failover-disaster-recovery.md).
