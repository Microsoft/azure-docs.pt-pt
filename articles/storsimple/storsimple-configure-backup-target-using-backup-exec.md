---
title: Série StorSimple 8000 como destino de cópia de segurança com Exec de cópia de segurança | Documentos da Microsoft
description: Descreve a configuração de destino de cópia de segurança do StorSimple com Exec de cópia de segurança Veritas.
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
ms.date: 12/05/2016
ms.author: hkanna
ms.openlocfilehash: e11d541f0450c0de4ba6d60f889fc7471b1fa1aa
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58011140"
---
# <a name="storsimple-as-a-backup-target-with-backup-exec"></a>StorSimple como um destino de cópia de segurança com Exec de cópia de segurança

## <a name="overview"></a>Descrição geral

O Azure StorSimple é uma solução de armazenamento na cloud híbrida da Microsoft. StorSimple resolve as complexidades de crescimento exponencial dos dados ao utilizar uma conta de armazenamento do Azure como uma extensão da solução no local e escalar automaticamente os dados entre armazenamento no local e o armazenamento na cloud.

Neste artigo, discutimos a integração do StorSimple com Exec de cópia de segurança Veritas e melhores práticas para integrar as duas soluções. Também podemos fazer recomendações sobre como configurar Exec de cópia de segurança para melhor integração com o StorSimple. Podemos diferir as práticas recomendadas Veritas, arquitetos de cópia de segurança e os administradores para a melhor forma de configurar a execução de Backup para atender aos requisitos de cópia de segurança individuais e contratos de nível de serviço (SLAs).

Embora ilustraremos passos de configuração e conceitos-chave, este artigo não constitui um guia passo a passo de configuração ou instalação. Partimos do princípio de que os componentes básicos e a infraestrutura estão na ordem de trabalho e está preparado para suportar os conceitos que descrevemos.

### <a name="who-should-read-this"></a>Quem deve ler isto?

As informações neste artigo serão mais úteis para administradores de cópia de segurança, os administradores de armazenamento e arquitetos de armazenamento que tenham conhecimento de armazenamento, Windows Server 2012 R2, Ethernet, serviços cloud e Exec de cópia de segurança.

## <a name="supported-versions"></a>Versões suportadas

-   [Exec 16 e versões posteriores de cópia de segurança](http://backupexec.com/compatibility)
-   [O StorSimple Update 3 e versões posteriores](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Por que motivo StorSimple como um destino de cópia de segurança?

O StorSimple é uma boa opção para um destino de backup porque:

-   Ele fornece armazenamento standard e local para aplicativos de backup utilizar como um destino de cópia de segurança rapidamente, sem quaisquer alterações. Também pode utilizar o StorSimple para um restauro rápido de cópias de segurança recentes.
-   Sua cloud em camadas é perfeitamente integrada com uma conta de armazenamento na cloud do Azure para utilizar armazenamento económico do Azure.
-   Fornece automaticamente armazenamento externo para recuperação após desastre.

## <a name="key-concepts"></a>Conceitos-chave

Tal como acontece com qualquer solução de armazenamento, uma avaliação de cuidado de desempenho do armazenamento da solução, SLAs, a taxa de alteração e necessidades de capacidade de crescimento é essencial para o sucesso. A idéia principal é que, com a introdução de um escalão de nuvem, os tempos de acesso e taxas de transferência para a play na cloud um papel fundamental na capacidade do StorSimple para fazer seu trabalho.

StorSimple foi concebido para fornecer armazenamento para aplicações que funcionam num conjunto de trabalho bem definido de dados (dados de acesso frequente). Nesse modelo, o conjunto de trabalho de dados é armazenado nas camadas locais e o conjunto nonworking/frias/arquivados restante de dados está em camadas para a cloud. Esse modelo é representado na figura a seguir. A linha verde quase simples representa os dados armazenados nas camadas locais do dispositivo StorSimple. A linha vermelha representa a quantidade total de dados armazenados na solução StorSimple em todos os escalões. O espaço entre a linha verde simples e a curva de vermelha exponencial representa a quantidade total de dados armazenados na cloud.

**Criação de camadas do StorSimple**
![diagrama de camadas do StorSimple](./media/storsimple-configure-backup-target-using-backup-exec/image1.jpg)

Com essa arquitetura em mente, verá que o StorSimple é perfeitamente adequado para operar como um destino de cópia de segurança. Pode utilizar o StorSimple para:
-   Execute seus restauros mais frequentes do conjunto de trabalho local de dados.
-   Utilize a cloud para recuperação após desastre de fora do local e os dados mais antigos, onde são menos frequentes restauros.

## <a name="storsimple-benefits"></a>Benefícios do StorSimple

O StorSimple oferece uma solução de no local que está totalmente integrada com o Microsoft Azure, ao tirar partido de acesso totalmente integrado para o local e o armazenamento na nuvem.

O StorSimple utiliza a disposição em camadas automática entre o dispositivo no local, que tem o dispositivo de estado sólido (SSD) e ligados à série armazenamento SCSI (SAS) e o armazenamento do Azure. A disposição em camadas automática mantém os dados acedidos com frequência local, nas camadas SSD e SAS. Move os dados acedidos com pouca frequência para armazenamento do Azure.

O StorSimple oferece estes benefícios:

-   Algoritmos de eliminação de duplicados e compressão exclusivos que utilizam a cloud para alcançar níveis sem precedentes de eliminação de duplicados
-   Elevada disponibilidade
-   Georreplicação ao utilizar a georreplicação do Azure
-   Integração do Azure
-   Encriptação de dados na cloud
-   Recuperação de desastres aprimorados e conformidade

Embora o StorSimple apresenta dois cenários de implementação principal (destino de cópia de segurança primário e secundário destino de cópia de segurança), fundamentalmente, é um simples, dispositivo de armazenamento de blocos. StorSimple faz todas as a compressão e a eliminação de duplicados. Forma totalmente integrada envia e recupera dados entre a cloud e o aplicativo e sistema de ficheiros.

Para obter mais informações sobre o StorSimple, consulte [série 8000 do StorSimple: Solução de armazenamento na cloud híbrida](storsimple-overview.md). Além disso, pode rever o [as especificações da série StorSimple 8000 técnicas](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> Usando um StorSimple dispositivo como um destino de cópia de segurança só é suportado para atualização 3 do StorSimple 8000 e versões posteriores.

## <a name="architecture-overview"></a>Descrição geral da arquitetura

As tabelas seguintes mostram as orientações inicial da arquitetura do modelo de dispositivo.

**As capacidades do StorSimple para o local e o armazenamento na cloud**

| Capacidade de armazenamento       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| Capacidade de armazenamento local | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Capacidade de armazenamento na cloud | &gt; 200 TiB\* | &gt; 500 TiB\* |

\* Tamanho de armazenamento parte do princípio de não eliminação de duplicados ou compressão.

**Capacidades do StorSimple para cópias de segurança primárias e secundárias**

| Cenário de cópia de segurança  | Capacidade de armazenamento local  | Capacidade de armazenamento na cloud  |
|---|---|---|
| Cópia de segurança primária  | Cópias de segurança recentes armazenadas no armazenamento local para a recuperação rápida para cumprir o objetivo de ponto de recuperação (RPO) | Histórico de cópia de segurança (RPO) se encaixa na capacidade de nuvem |
| Cópia de segurança secundária | Segunda cópia dos dados de cópia de segurança pode ser armazenada na capacidade de nuvem  | N/A  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple como um destino de cópia de segurança primário

Neste cenário, os volumes do StorSimple são apresentados para o aplicativo de backup único repositório para cópias de segurança. A figura a seguir mostra uma arquitetura de solução em que qualquer utilização de cópias de segurança StorSimple volumes em camadas para cópias de segurança e restauros.

![StorSimple como um diagrama lógico de destino de cópia de segurança primário](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Passos lógicos de cópia de segurança primário de destino

1.  O servidor de cópia de segurança entra em contacto com o agente de cópia de segurança de destino e o agente de cópia de segurança transmite dados para o servidor de cópia de segurança.
2.  O servidor de cópia de segurança escreve dados para o StorSimple volumes em camadas.
3.  O servidor de cópia de segurança da base de dados do catálogo de atualizações e, em seguida, concluir a tarefa de cópia de segurança.
4.  Um script de instantâneo aciona cloud snapshot manager do StorSimple (iniciar ou eliminar).
5.  O servidor de cópia de segurança elimina expiradas cópias de segurança com base numa política de retenção.


### <a name="primary-target-restore-logical-steps"></a>Passos lógicos de restauro de destino principal

1.  O servidor de cópia de segurança começa a restaurar os dados apropriados do repositório de armazenamento.
2.  O agente de cópia de segurança recebe os dados do servidor de cópia de segurança.
3.  O servidor de cópia de segurança é concluída a tarefa de restauro.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple como um destino de cópia de segurança secundário

Neste cenário, volumes do StorSimple principalmente são utilizados para retenção de longa duração ou o arquivamento.

A figura a seguir mostra uma arquitetura na qual as cópias de segurança iniciais e restaura o volume de destino de alto desempenho. Estas cópias de segurança são copiadas e arquivadas para um StorSimple em camadas volume numa agenda definida.

É importante para o tamanho do volume de alto desempenho, de modo a que possa tratar seus requisitos de capacidade e desempenho da política de retenção.

![StorSimple como um diagrama lógico de destino de cópia de segurança secundário](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Passos lógicos de cópia de segurança secundário de destino

1.  O servidor de cópia de segurança entra em contacto com o agente de cópia de segurança de destino e o agente de cópia de segurança transmite dados para o servidor de cópia de segurança.
2.  O servidor de cópia de segurança escreve dados para o armazenamento de elevado desempenho.
3.  O servidor de cópia de segurança da base de dados do catálogo de atualizações e, em seguida, concluir a tarefa de cópia de segurança.
4.  O servidor de cópia de segurança copia as cópias de segurança para o StorSimple com base numa política de retenção.
5.  Um script de instantâneo aciona cloud snapshot manager do StorSimple (iniciar ou eliminar).
6.  O servidor de cópia de segurança elimina expiradas cópias de segurança com base numa política de retenção.

### <a name="secondary-target-restore-logical-steps"></a>Passos lógicos de restauro de destino secundária

1.  O servidor de cópia de segurança começa a restaurar os dados apropriados do repositório de armazenamento.
2.  O agente de cópia de segurança recebe os dados do servidor de cópia de segurança.
3.  O servidor de cópia de segurança é concluída a tarefa de restauro.

## <a name="deploy-the-solution"></a>Implementar a solução

Implementar a solução requer três passos:
1. Prepare a infraestrutura de rede.
2. Implemente o dispositivo StorSimple como um destino de cópia de segurança.
3. Implemente Exec de cópia de segurança.

Cada passo é abordado em detalhe nas secções seguintes.

### <a name="set-up-the-network"></a>Configurar a rede

Uma vez que o StorSimple é uma solução que está integrada com a cloud do Azure, StorSimple requer um Active Directory e a ligação de trabalho na cloud do Azure. Esta ligação é utilizada para operações como instantâneos de cloud, gestão e transferência de metadados e para o escalão mais antigo, menos dados acedidos para armazenamento na cloud do Azure.

Para a solução para desempenho ideal, recomendamos que siga estas práticas recomendadas de rede:

-   A ligação que liga o seu StorSimple disposição em camadas para o Azure tem de cumprir os requisitos de largura de banda. Para conseguir isso, aplicam-se o nível necessário de Quality of Service (QoS) para a infraestrutura de SLAs de objetivo (RTO) de tempo de comutadores de acordo com seus RPO e a recuperação.
-   Latências de acesso de armazenamento de Blobs do Azure máximas devem ser cerca de 80 ms.

### <a name="deploy-storsimple"></a>Implementar o StorSimple

Para uma orientação de implementação do StorSimple passo a passo, consulte [implementar no seu dispositivo StorSimple no local](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-backup-exec"></a>Implementar Exec de cópia de segurança

Para Exec de cópia de segurança melhores práticas de instalação, consulte [melhores práticas para a instalação de Exec de cópia de segurança](https://www.veritas.com/content/support/en_US/doc/72686287-131623464-0/v70444238-131623464).

## <a name="set-up-the-solution"></a>Configurar a solução

Nesta secção, vamos demonstrar alguns exemplos de configuração. Os exemplos e as recomendações seguintes ilustram a implementação mais básica e fundamental. Essa implementação não pode ser aplicadas diretamente aos seus requisitos de cópia de segurança específicos.

### <a name="set-up-storsimple"></a>Configurar o StorSimple

| Tarefas de implementação do StorSimple  | Comentários adicionais |
|---|---|
| Implemente o dispositivo StorSimple no local. | Versões suportadas: A atualização 3 e versões posteriores. |
| Ative o destino de cópia de segurança. | Utilize estes comandos para ativar ou desativar o modo de destino de cópia de segurança e ao obter o estado. Para obter mais informações, consulte [ligar remotamente ao dispositivo StorSimple](storsimple-remote-connect.md).</br> Para ativar o modo de cópia de segurança: `Set-HCSBackupApplianceMode -enable`. </br> Para desativar o modo de cópia de segurança: `Set-HCSBackupApplianceMode -disable`. </br> Para obter o estado atual de definições do modo de cópia de segurança: `Get-HCSBackupApplianceMode`. |
| Crie um contentor de volume comuns para o volume que armazena os dados de cópia de segurança. Todos os dados num contentor do volume tem eliminação de duplicados. | Contentores de volumes do StorSimple definem domínios de eliminação de duplicados.  |
| Crie volumes do StorSimple. | Crie volumes com tamanhos como próximo a utilização antecipada possível, porque o tamanho do volume afeta o tempo de duração do instantâneo de cloud. Para obter informações sobre como dimensionar um volume, leia sobre [políticas de retenção](#retention-policies).</br> </br> Utilize o StorSimple volumes em camadas e selecione o **utilizar este volume para menos dados de arquivo acedidos com frequência** caixa de verificação. </br> Não é suportada a utilizar apenas volumes afixados localmente. |
| Crie uma política de cópia de segurança StorSimple exclusiva para todos os volumes de destino de cópia de segurança. | Uma política de cópia de segurança do StorSimple define o grupo de consistência de volume. |
| Desactivar o agendamento de como os instantâneos de expirarem. | Os instantâneos são disparados como uma operação de pós-processamento. |

### <a name="set-up-the-host-backup-server-storage"></a>Configurar o armazenamento de cópia de segurança do servidor de anfitrião

Configure o armazenamento de cópia de segurança do servidor de anfitrião, de acordo com essas diretrizes:  

- Não utilize volumes expandidos (criados pelo gerenciamento de disco do Windows). Discos distribuídos não são suportados.
- Formate os volumes com NTFS com o tamanho de alocação de 64 KB.
- Os volumes do StorSimple são mapeados diretamente para o servidor de Exec de cópia de segurança.
    - Utilize iSCSI para servidores físicos.
    - Utilize discos pass-through para servidores virtuais.

## <a name="best-practices-for-storsimple-and-backup-exec"></a>Melhores práticas para o StorSimple e Exec de cópia de segurança

Configure a solução, de acordo com as diretrizes nas seções a seguir.

### <a name="operating-system-best-practices"></a>Práticas recomendadas do sistema operativo

- Desative a encriptação do Windows Server e a eliminação de duplicados para o sistema de ficheiros NTFS.
- Desative a desfragmentação do Windows Server nos volumes do StorSimple.
- Desative a indexação do Windows Server nos volumes do StorSimple.
- Execute uma verificação antivírus o anfitrião de origem (não em relação os volumes do StorSimple).
- Desativar a predefinição [manutenção do Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) no Gerenciador de tarefas. Fazer isso em uma das seguintes formas:
  - Desative o configurator de manutenção no agendador de tarefas do Windows.
  - Baixe [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) do Windows Sysinternals. Depois de transferir o PsExec, execute o Azure PowerShell como um administrador e escreva:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>Práticas recomendadas do StorSimple

  -   Certifique-se de que o dispositivo StorSimple está atualizado para [atualização 3 ou posterior](storsimple-install-update-3.md).
  -   Isolar tráfego iSCSI e na cloud. Utilização de ligações de iSCSI dedicado para o tráfego entre o StorSimple e o servidor de cópia de segurança.
  -   Certifique-se de que o dispositivo StorSimple é um destino de cópia de segurança dedicado. Cargas de trabalho mistas não são suportadas porque elas afetam o RTO e RPO.

### <a name="backup-exec-best-practices"></a>Melhores práticas de Exec de cópia de segurança

-   Exec de cópia de segurança tem de ser instalado numa unidade local do servidor e não num volume StorSimple.
-   Definir o armazenamento de cópia de segurança Exec **operações de escrita simultânea** para o máximo permitido.
    -   Definir o armazenamento de cópia de segurança Exec **tamanho de bloco e de memória intermédia** para 512 KB.
    -   Ativar o armazenamento de cópia de segurança Exec **colocados em memória intermédia de leitura e escrita**.
-   O StorSimple suporta cópias de segurança de Exec de cópia de segurança, completas e incrementais. Recomendamos que não utilize cópias de segurança diferenciais e sintéticas.
-   Ficheiros de dados de cópia de segurança devem conter dados apenas de uma tarefa específica. Por exemplo, nenhum suporte de dados acrescenta entre as diferentes tarefas são permitidas.
-   Desative a verificação de tarefa. Se necessário, a verificação deve ser agendada após a tarefa de cópia de segurança mais recente. É importante compreender que esta tarefa afeta sua janela de backup.
-   Selecione **armazenamento** > **seu disco** > **detalhes** > **propriedades**. Desativar **previamente alocar espaço em disco**.

Para as definições de Exec de cópia de segurança mais recentes e melhores práticas para implementar estes requisitos, consulte [o site do Veritas](https://www.veritas.com).

## <a name="retention-policies"></a>Políticas de retenção

Um dos tipos mais comuns para a política de retenção de cópia de segurança é uma política de avô, Pai e filho (GFS). Numa política GFS, uma cópia de segurança incremental é executada diariamente e cópias de segurança completas são feitas semanais e mensais. O resultado é política StorSimple seis volumes em camadas. Um volume contém as cópias de segurança completas semanais, mensais e anuais. Outros cinco volumes armazenam cópias de segurança incrementais diárias.

No exemplo a seguir, usamos uma rotação de GFS. O exemplo assume o seguinte:

-   Dados não-duplicados ou comprimidos são utilizados.
-   Cópias de segurança completas são 1 TiB.
-   Cópias de segurança incrementais diárias são 500 GiB.
-   Cópias de segurança semanais quatro são mantidas durante um mês.
-   Cópias de segurança mensais doze são mantidas durante um ano.
-   Uma cópia de segurança anual é mantida há 10 anos.

Com base nas suposições anteriores, crie um TiB de 26 volume para as cópias de segurança completas mensais e anuais de camadas do StorSimple. Criar um TiB de 5 StorSimple volume em camadas para cada um das cópias de segurança incrementais diárias.

| Retenção de cópia de segurança de tipo | Tamanho (TiB) | Multiplicador GFS\* | Capacidade total (TiB)  |
|---|---|---|---|
| Completa semanal | 1 | 4  | 4 |
| Diária incremental | 0,5 | 20 (ciclos igual número de semanas por mês) | 12 (2 para a quota adicional) |
| Total mensal | 1 | 12 | 12 |
| Total anual | 1  | 10 | 10 |
| Requisito de GFS |   | 38 |   |
| Quota adicional  | 4  |   | requisito de GFS 42 total  |

\* O multiplicador GFS é o número de cópias que tem de proteger e manter-se para cumprir os requisitos de política de cópia de segurança.

## <a name="set-up-backup-exec-storage"></a>Configurar o armazenamento de Exec de cópia de segurança

### <a name="to-set-up-backup-exec-storage"></a>Para configurar o armazenamento de Exec de cópia de segurança

1.  Na consola de gestão Exec de cópia de segurança, selecione **armazenamento** > **configurar o armazenamento** > **armazenamento em disco**  >   **Próxima**.

    ![Console de gerenciamento de Exec de cópia de segurança, configure a página de armazenamento](./media/storsimple-configure-backup-target-using-backup-exec/image4.png)

2.  Selecione **armazenamento de disco**e, em seguida, selecione **próxima**.

    ![Console de gerenciamento de Exec de cópia de segurança, página selecionar armazenamento](./media/storsimple-configure-backup-target-using-backup-exec/image5.png)

3.  Introduza um nome de representativo, por exemplo, **Sábado completa**e uma descrição. Selecione **Seguinte**.

    ![Página de consola, o nome e descrição do gerenciamento de Exec de cópia de segurança](./media/storsimple-configure-backup-target-using-backup-exec/image7.png)

4.  Selecione o disco para criar o dispositivo de armazenamento de disco e, em seguida, selecione onde pretende **seguinte**.

    ![Console de gerenciamento de Exec de cópia de segurança, página de seleção de disco de armazenamento](./media/storsimple-configure-backup-target-using-backup-exec/image9.png)

5.  Incrementar o número de operações de escrita para **16**e, em seguida, selecione **próxima**.

    ![Página de definições de operações de escrita de cópia de segurança Exec console de gerenciamento, em simultâneo](./media/storsimple-configure-backup-target-using-backup-exec/image10.png)

6.  Reveja as definições e, em seguida, selecione **concluir**.

    ![Console de gerenciamento de Exec de cópia de segurança, página de resumo de configuração de armazenamento](./media/storsimple-configure-backup-target-using-backup-exec/image11.png)

7.  No final de cada atribuição de volume, altere as definições de dispositivo de armazenamento de acordo com esses recomendada na [melhores práticas para o StorSimple e Exec de cópia de segurança](#best-practices-for-storsimple-and-backup-exec).

    ![Console de gerenciamento de Exec de cópia de segurança, página de definições do dispositivo de armazenamento](./media/storsimple-configure-backup-target-using-backup-exec/image12.png)

8.  Repita os passos 1 a 7 até terminar de atribuir os volumes do StorSimple para Exec de cópia de segurança.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Configurar o StorSimple como um destino de cópia de segurança primário

> [!NOTE]
> Restauro de dados a partir de uma cópia de segurança que tem sido em camadas para a cloud ocorre em velocidades de cloud.

A figura seguinte mostra o mapeamento de um volume normal para uma tarefa de cópia de segurança. Neste caso, todas as cópias de segurança semanais mapeiam para o disco completo de Sábado e as cópias de segurança incrementais mapeiam para discos de incrementais de segunda-feira a sexta-feira. Todos os backups e restaurações são de um StorSimple camadas volume.

![Diagrama lógico de configuração de destino de cópia de segurança primário](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple como um destino de cópia de segurança primário GFS agendar exemplo

Eis um exemplo de uma agenda de rotação GFS para quatro semanas, mensais e anuais:

| Tipo de frequência/cópia de segurança | Completo | Incremental (1 a 5 de dias)  |   
|---|---|---|
| Semanal (1 a 4 de semanas) | Sábado | De segunda a sexta-feira |
| Mensalmente  | Sábado  |   |
| Anualmente | Sábado  |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>Atribuir volumes do StorSimple para uma tarefa de cópia de segurança de Exec de cópia de segurança

A seguinte sequência parte do princípio de que Exec de cópia de segurança e o anfitrião de destino estão configurados de acordo com as diretrizes de agente Exec de cópia de segurança.

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>Para atribuir volumes do StorSimple para uma tarefa de cópia de segurança de Exec de cópia de segurança

1.  Na consola de gestão Exec de cópia de segurança, selecione **Host** > **cópia de segurança** > **cópia de segurança para disco**.

    ![Exec consola de gestão, selecione o anfitrião, cópia de segurança e cópia de segurança em disco de cópia de segurança](./media/storsimple-configure-backup-target-using-backup-exec/image14.png)

2.  Na **propriedades de definição de cópia de segurança** caixa de diálogo em **cópia de segurança**, selecione **editar**.

    ![Console de gerenciamento de Exec de cópia de segurança, caixa de diálogo de propriedades de definição de cópia de segurança](./media/storsimple-configure-backup-target-using-backup-exec/image15.png)

3.  Configure as cópias de segurança completas e incrementais, para que estes cumprem os requisitos de RPO e RTO e estar em conformidade com práticas recomendadas do Veritas.

4.  Na **opções de cópia de segurança** caixa de diálogo, selecione **armazenamento**.

    ![Console de gerenciamento de Exec de cópia de segurança, caixa de diálogo de armazenamento de opções de cópia de segurança](./media/storsimple-configure-backup-target-using-backup-exec/image16.png)

5.  Atribua volumes do StorSimple correspondentes à sua agenda de cópia de segurança.

    > [!NOTE]
    > **Compressão** e **tipo de encriptação** estão definidas como **nenhum**.

6.  Sob **Verifique se**, selecione a **não verificar os dados para esta tarefa** caixa de verificação. Utilizar esta opção poderá afetar a disposição em camadas do StorSimple.

    > [!NOTE]
    > A desfragmentação, indexação e verificação de segundo plano afetam negativamente o StorSimple disposição em camadas.

    ![Console de gerenciamento de Exec de cópia de segurança, opções de cópia de segurança, verifique as definições](./media/storsimple-configure-backup-target-using-backup-exec/image17.png)

7.  Quando configurou o restante das suas opções de cópia de segurança para satisfazer os seus requisitos, selecione **OK** para concluir.

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Configurar o StorSimple como um destino de cópia de segurança secundário

> [!NOTE]
>Restauros de dados de uma cópia de segurança que tem sido em camadas para a cloud ocorrerem em velocidades de cloud.

Nesse modelo, tem de ter um suporte de dados de armazenamento (que não seja o StorSimple) para ser usado como um cache temporário. Por exemplo, pode utilizar uma matriz redundante de volume de discos independentes (RAID) para acomodar o espaço, entrada/saída (e/s) e largura de banda. Recomendamos que utilize o RAID 5, 50 e 10.

A figura seguinte mostra a retenção de curta duração típica volumes locais (para o servidor) e volumes de arquivos de retenção de longa duração. Neste cenário, executam todas as cópias de segurança no volume RAID local (para o servidor). Estas cópias de segurança são periodicamente duplicadas e arquivadas para um volume de arquivos. É importante para o tamanho do volume RAID local (para o servidor), para que ele pode lidar com os requisitos de capacidade e desempenho de retenção curto prazo.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple como um exemplo GFS do destino de cópia de segurança secundário

![StorSimple como diagrama lógico de destino de cópia de segurança secundário](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetdiagram.png)

A tabela seguinte mostra como configurar as cópias de segurança para executar no local e discos de StorSimple. Ele inclui os requisitos de capacidade total e individuais.

### <a name="backup-configuration-and-capacity-requirements"></a>Configuração de cópia de segurança e os requisitos de capacidade

| Tipo de cópia de segurança e retenção | Armazenamento configurado | Tamanho (TiB) | Multiplicador GFS | Capacidade total\* (TiB) |
|---|---|---|---|---|
| Semana 1 (completas e incrementais) |Disco local (de curta duração)| 1 | 1 | 1 |
| 2 a 4 do StorSimple semanas |Disco do StorSimple (longo prazo) | 1 | 4 | 4 |
| Total mensal |Disco do StorSimple (longo prazo) | 1 | 12 | 12 |
| Total anual |Disco do StorSimple (longo prazo) | 1 | 1 | 1 |
|Requisito de tamanho de volumes GFS |  |  |  | 18*|

\* Capacidade total inclui 17 TiB de StorSimple discos e de 1 TiB de volume RAID local.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>Agenda de exemplo GFS: Rotação de GFS agenda semanal, mensal e anual

| Week (Semana) | Completo | Incremental dia 1 | Incremental dia 2 | Incremental dia 3 | Dia incremental 4 | Dia incremental 5 |
|---|---|---|---|---|---|---|
| Semana 1 | Volume RAID local  | Volume RAID local | Volume RAID local | Volume RAID local | Volume RAID local | Volume RAID local |
| Semana de 2 | 2 a 4 do StorSimple semanas |   |   |   |   |   |
| Semana de 3 | 2 a 4 do StorSimple semanas |   |   |   |   |   |
| Semana de 4 | 2 a 4 do StorSimple semanas |   |   |   |   |   |
| Mensalmente | StorSimple mensalmente |   |   |   |   |   |
| Anualmente | StorSimple anuais  |   |   |   |   |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-archive-and-deduplication-job"></a>Atribuir volumes do StorSimple para um arquivo de Exec de cópia de segurança e a tarefa de eliminação de duplicados

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-archive-and-duplication-job"></a>Para atribuir volumes do StorSimple para uma tarefa de arquivo e duplicação de Exec de cópia de segurança

1.  Na consola de gestão Exec de cópia de segurança, clique com botão direito a tarefa que pretende arquivar a um volume do StorSimple e, em seguida, selecione **propriedades de definição de cópia de segurança** > **editar**.

    ![Console de gerenciamento de Exec de cópia de segurança, separador de propriedades de definição de cópia de segurança](./media/storsimple-configure-backup-target-using-backup-exec/image19.png)

2.  Selecione **adicionar estágio** > **duplicado em disco** > **editar**.

    ![Console de gerenciamento de Exec de cópia de segurança, adicione o estágio](./media/storsimple-configure-backup-target-using-backup-exec/image20.png)

3.  Na **opções de duplicar** caixa de diálogo caixa, selecione os valores que pretende utilizar para **origem** e **agenda**.

    ![Consola de gestão, propriedades de definições de cópia de segurança e opções duplicadas Exec de cópia de segurança](./media/storsimple-configure-backup-target-using-backup-exec/image21.png)

4.  Na **armazenamento** pendente, selecione o volume do StorSimple onde pretende que a tarefa de arquivo para armazenar os dados.

    ![Consola de gestão, propriedades de definições de cópia de segurança e opções duplicadas Exec de cópia de segurança](./media/storsimple-configure-backup-target-using-backup-exec/image22.png)

5.  Selecione **Verifique se**e, em seguida, selecione a **não verificar os dados para esta tarefa** caixa de verificação.

    ![Consola de gestão, propriedades de definições de cópia de segurança e opções duplicadas Exec de cópia de segurança](./media/storsimple-configure-backup-target-using-backup-exec/image23.png)

6.  Selecione **OK**.

    ![Consola de gestão, propriedades de definições de cópia de segurança e opções duplicadas Exec de cópia de segurança](./media/storsimple-configure-backup-target-using-backup-exec/image24.png)

7.  Na **cópia de segurança** coluna, adicione uma fase de novo. Para a origem, utilize **incremental**. Para o destino, escolha o volume do StorSimple em que a tarefa de cópia de segurança incremental é arquivada. Repita os passos 1 a 6.

## <a name="storsimple-cloud-snapshots"></a>Instantâneos de cloud do StorSimple

Instantâneos de cloud do StorSimple proteger os dados que residem no seu dispositivo StorSimple. A criação de um instantâneo de cloud é equivalente a bandas de cópia de segurança locais para um recurso de fora do local de envio. Se utilizar o armazenamento com redundância geográfica do Azure, criar um instantâneo de cloud é equivalente ao envio de bandas de cópia de segurança para vários sites. Se tiver de restaurar um dispositivo após um desastre, pode colocar outro dispositivo com StorSimple online e fazer uma ativação pós-falha. Após a ativação pós-falha, seria capaz de acessar os dados (em velocidades de nuvem) a partir do instantâneo de cloud mais recente.

A seguinte secção descreve como criar um script curto para iniciar e eliminar instantâneos de cloud do StorSimple durante o pós-processamento de cópia de segurança.

> [!NOTE]
> Instantâneos que são criados manualmente ou através de programação não siga a política de expiração de instantâneos do StorSimple. Estes instantâneos têm de ser manual ou programaticamente eliminados.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Iniciar e eliminar instantâneos de cloud com um script

> [!NOTE]
> Avalie cuidadosamente o repercussões de retenção de dados e de conformidade antes de eliminar um instantâneo do StorSimple. Para obter mais informações sobre como executar um script pós-cópia de segurança, consulte a [documentação de Exec de cópia de segurança](https://www.veritas.com/support/en_US/article.100032497.html).

### <a name="backup-lifecycle"></a>Ciclo de vida de cópia de segurança

![Diagrama de ciclo de vida de cópia de segurança](./media/storsimple-configure-backup-target-using-backup-exec/backuplifecycle.png)

### <a name="requirements"></a>Requisitos

-   O servidor que executa o script tem de ter acesso a recursos na cloud do Azure.
-   A conta de utilizador tem de ter as permissões necessárias.
-   Uma política de cópia de segurança do StorSimple com os volumes do StorSimple associados deve ser configurar mas não ativada.
-   Será necessário o nome de recurso do StorSimple, a chave de registo, ID de política de cópia de segurança e nome do dispositivo.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Para iniciar ou eliminar um instantâneo de cloud

1. [Instalar o Azure PowerShell](/powershell/azure/overview).
2. Transferir e configurar [gerir CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) script do PowerShell.
3. No servidor que executa o script, execute o PowerShell como administrador. Certifique-se de que executa o script com `-WhatIf $true` ver quais alterações que o script fará. Depois de concluída a validação, passar `-WhatIf $false`. Execute o comando abaixo:
   ```powershell
   .\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
   ```
4. Adicione o script para a sua tarefa de cópia de segurança no Exec de cópia de segurança ao editar o processamento prévio de suas opções tarefa Exec de cópia de segurança e pós-processamento de comandos.

   ![Consola de Exec, opções de cópia de segurança, separador de comandos de pré e pós-processamento de cópia de segurança](./media/storsimple-configure-backup-target-using-backup-exec/image25.png)

> [!NOTE]
> Recomendamos que execute a sua política de cópia de segurança de instantâneos do StorSimple cloud como um script pós-processamento no final da sua tarefa de cópia de segurança diária. Para obter mais informações sobre como criar cópias de segurança e restaurar o ambiente de aplicativo de backup para o ajudar a cumprir o RPO e RTO, consulte com o arquiteto de cópia de segurança.

## <a name="storsimple-as-a-restore-source"></a>StorSimple como uma origem de restauro

Restaura a partir de um trabalho de dispositivos do StorSimple como restaura a partir de qualquer dispositivo de armazenamento de blocos. As restaurações de dados que estão em camadas para a cloud ocorre em velocidades de cloud. Para dados locais, restaurações ocorrerem com a rapidez de disco local do dispositivo. Para obter informações sobre como executar um restauro, consulte a documentação de Exec de cópia de segurança. Recomendamos que está em conformidade com práticas recomendadas de restauro de Exec de cópia de segurança.

## <a name="storsimple-failover-and-disaster-recovery"></a>Recuperação após desastre e de ativação pós-falha do StorSimple

> [!NOTE]
> Para cenários de destino de cópia de segurança, o StorSimple Cloud Appliance não é suportado como um destino de restauro.

Após um desastre pode ser causado por uma variedade de fatores. A tabela seguinte lista os cenários comuns de recuperação após desastre.

| Cenário | Impacto | Como recuperar | Notas |
|---|---|---|---|
| Falha de dispositivo do StorSimple | Operações de backup e restauração são interrompidas. | Substitua o dispositivo com falhas e realizar [StorSimple ativação pós-falha e recuperação após desastre](storsimple-device-failover-disaster-recovery.md). | Se precisar de executar uma restauração após a recuperação de dispositivo, os conjuntos de trabalho de dados completos são obtidos a partir da cloud para o novo dispositivo. Todas as operações são em velocidades de cloud. O processo de indexação e catalogação rescanning pode fazer com que todos os conjuntos de cópia de segurança a serem analisados e obtido a partir da camada de cloud para o escalão de dispositivo local, que pode ser um processo demorado. |
| Falha de servidor Exec de cópia de segurança | Operações de backup e restauração são interrompidas. | Reconstrua o servidor de cópia de segurança e efetuar o restauro de base de dados, conforme detalhado no [como fazer uma base de dados manual de cópia de segurança e restauro da cópia de segurança Exec (BEDB)](http://www.veritas.com/docs/000041083). | Tem de reconstruir ou restaurar o servidor de Exec de cópia de segurança no site de recuperação após desastre. Restaure a base de dados para o ponto mais recente. Se a base de dados restaurada Exec de cópia de segurança não está sincronizada com as tarefas de cópia de segurança mais recente, indexação e catalogação são necessário. Este índice e o catálogo de reanálise processo podem fazer com que todos os conjuntos de cópia de segurança a serem analisados e obtido a partir da camada de cloud para a camada do dispositivo local. Isto torna ainda mais tempo intensiva. |
| Falha do site que resulta na perda do servidor de cópia de segurança e o StorSimple | Operações de backup e restauração são interrompidas. | Restaurar o StorSimple primeiro e, em seguida, restaure Exec de cópia de segurança. | Restaurar o StorSimple primeiro e, em seguida, restaure Exec de cópia de segurança. Se precisar de executar uma restauração após a recuperação de dispositivo, conjuntos de dados completa de trabalho são obtidos a partir da cloud para o novo dispositivo. Todas as operações são em velocidades de cloud. |

## <a name="references"></a>Referências

Os seguintes documentos foram referenciados neste artigo:

- [Configuração do StorSimple multipath e/s](storsimple-configure-mpio-windows-server.md)
- [Cenários de armazenamento: Aprovisionamento dinâmico](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Utilizar o GPT unidades](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Configurar cópias de sombra para pastas partilhadas](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre como [restauro a partir de um conjunto de cópias de segurança](storsimple-restore-from-backup-set-u2.md).
- Saiba mais sobre como efetuar [dispositivo ativação pós-falha e recuperação após desastre](storsimple-device-failover-disaster-recovery.md).
