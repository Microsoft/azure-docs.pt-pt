---
title: Série StorSimple 8000 como destino de cópia de segurança com NetBackup | Documentos da Microsoft
description: Descreve a configuração de destino de cópia de segurança do StorSimple com Veritas NetBackup.
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
ms.author: hkanna
ms.openlocfilehash: 913df079b56e131a3120971b635c49c2c04b2b1e
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56871575"
---
# <a name="storsimple-as-a-backup-target-with-netbackup"></a>StorSimple como um destino de cópia de segurança com NetBackup

## <a name="overview"></a>Descrição geral

O Azure StorSimple é uma solução de armazenamento na cloud híbrida da Microsoft. StorSimple resolve as complexidades de crescimento exponencial dos dados ao utilizar uma conta de armazenamento do Azure como uma extensão da solução no local e escalar automaticamente os dados entre armazenamento no local e o armazenamento na cloud.

Neste artigo, discutimos a integração do StorSimple com Veritas NetBackup e melhores práticas para integrar as duas soluções. Também podemos fazer recomendações sobre como configurar a Veritas NetBackup, a melhor integração com o StorSimple. Podemos diferir as práticas recomendadas Veritas, arquitetos de cópia de segurança e os administradores para a melhor forma de configurar Veritas NetBackup para atender aos requisitos de cópia de segurança individuais e contratos de nível de serviço (SLAs).

Embora ilustraremos passos de configuração e conceitos-chave, este artigo não constitui um guia passo a passo de configuração ou instalação. Partimos do princípio de que os componentes básicos e a infraestrutura estão na ordem de trabalho e está preparado para suportar os conceitos que descrevemos.

### <a name="who-should-read-this"></a>Quem deve ler isto?

As informações neste artigo serão mais úteis para administradores de cópia de segurança, os administradores de armazenamento e arquitetos de armazenamento que tenham conhecimento de armazenamento, Windows Server 2012 R2, Ethernet, serviços cloud e Veritas NetBackup.

### <a name="supported-versions"></a>Versões suportadas

-   NetBackup 7.7.x e versões posteriores
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
![diagrama de camadas do StorSimple](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)

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

![StorSimple como um diagrama lógico de destino de cópia de segurança primário](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Passos lógicos de cópia de segurança primário de destino

1.  O servidor de cópia de segurança entra em contacto com o agente de cópia de segurança de destino e o agente de cópia de segurança transmite dados para o servidor de cópia de segurança.
2.  O servidor de cópia de segurança escreve dados para o StorSimple volumes em camadas.
3.  O servidor de cópia de segurança da base de dados do catálogo de atualizações e, em seguida, concluir a tarefa de cópia de segurança.
4.  Um script de instantâneo aciona o snapshot manager do StorSimple (iniciar ou eliminar).
5.  O servidor de cópia de segurança elimina expiradas cópias de segurança com base numa política de retenção.

### <a name="primary-target-restore-logical-steps"></a>Passos lógicos de restauro de destino principal

1.  O servidor de cópia de segurança começa a restaurar os dados apropriados do repositório de armazenamento.
2.  O agente de cópia de segurança recebe os dados do servidor de cópia de segurança.
3.  O servidor de cópia de segurança é concluída a tarefa de restauro.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple como um destino de cópia de segurança secundário

Neste cenário, volumes do StorSimple principalmente são utilizados para retenção de longa duração ou o arquivamento.

A figura a seguir mostra uma arquitetura na qual as cópias de segurança iniciais e restaura o volume de destino de alto desempenho. Estas cópias de segurança são copiadas e arquivadas para um StorSimple em camadas volume numa agenda definida.

É importante para o tamanho do volume de alto desempenho, de modo a que possa tratar seus requisitos de capacidade e desempenho da política de retenção.

![StorSimple como um diagrama lógico de destino de cópia de segurança secundário](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Passos lógicos de cópia de segurança secundário de destino

1.  O servidor de cópia de segurança entra em contacto com o agente de cópia de segurança de destino e o agente de cópia de segurança transmite dados para o servidor de cópia de segurança.
2.  O servidor de cópia de segurança escreve dados para o armazenamento de elevado desempenho.
3.  O servidor de cópia de segurança da base de dados do catálogo de atualizações e, em seguida, concluir a tarefa de cópia de segurança.
4.  O servidor de cópia de segurança copia as cópias de segurança para o StorSimple com base numa política de retenção.
5.  Um script de instantâneo aciona o snapshot manager do StorSimple (iniciar ou eliminar).
6.  O servidor de cópia de segurança elimina as cópias de segurança expiradas, com base numa política de retenção.

### <a name="secondary-target-restore-logical-steps"></a>Passos lógicos de restauro de destino secundária

1.  O servidor de cópia de segurança começa a restaurar os dados apropriados do repositório de armazenamento.
2.  O agente de cópia de segurança recebe os dados do servidor de cópia de segurança.
3.  O servidor de cópia de segurança é concluída a tarefa de restauro.

## <a name="deploy-the-solution"></a>Implementar a solução

Implementar esta solução requer três passos:
1. Prepare a infraestrutura de rede.
2. Implemente o dispositivo StorSimple como um destino de cópia de segurança.
3. Implemente Veritas NetBackup.

Cada passo é abordado em detalhe nas secções seguintes.

### <a name="set-up-the-network"></a>Configurar a rede

Uma vez que o StorSimple é uma solução que está integrada com a cloud do Azure, StorSimple requer um Active Directory e a ligação de trabalho na cloud do Azure. Esta ligação é utilizada para operações como instantâneos de cloud, gestão de dados e metadados de transferência e a camada mais antiga, menos dados acedidos para armazenamento na cloud do Azure.

Para a solução para desempenho ideal, recomendamos que siga estas práticas recomendadas de rede:

-   A hiperligação que se liga o StorSimple disposição em camadas para o Azure tem de cumprir os requisitos de largura de banda. Para conseguir isso, aplicam-se o nível de qualidade de serviço (QoS) adequado à sua infraestrutura de SLAs de objetivo (RTO) de tempo de comutadores de acordo com seus RPO e a recuperação.

-   Latências de acesso de armazenamento de Blobs do Azure máximas devem ser cerca de 80 ms.

### <a name="deploy-storsimple"></a>Implementar o StorSimple

Para orientações de implementação do StorSimple passo a passo, consulte [implementar no seu dispositivo StorSimple no local](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-netbackup"></a>Implementar NetBackup

Para orientações de implantação do 7.7.x de NetBackup passo a passo, consulte a [NetBackup 7.7.x documentação](http://www.veritas.com/docs/000094423).

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

- Não utiliza volumes expandidos (criados pelo gerenciamento de disco do Windows); volumes expandidos não são suportados.
- Formate os volumes com NTFS com o tamanho de alocação de 64 KB.
- Os volumes do StorSimple são mapeados diretamente para o servidor de NetBackup.
    - Utilize iSCSI para servidores físicos.
    - Utilize discos pass-through para servidores virtuais.


## <a name="best-practices-for-storsimple-and-netbackup"></a>Melhores práticas para o StorSimple e NetBackup

Configurar a solução, de acordo com as diretrizes a seguir algumas secções.

### <a name="operating-system-best-practices"></a>Práticas recomendadas do sistema operativo

-   Desative a encriptação do Windows Server e a eliminação de duplicados para o sistema de ficheiros NTFS.
-   Desative a desfragmentação do Windows Server nos volumes do StorSimple.
-   Desative a indexação do Windows Server nos volumes do StorSimple.
-   Execute uma verificação antivírus o anfitrião de origem (não em relação os volumes do StorSimple).
-   Desativar a predefinição [manutenção do Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) no Gerenciador de tarefas. Fazer isso em uma das seguintes formas:
    - Desative o configurator de manutenção no agendador de tarefas do Windows.
    - Baixe [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) do Windows Sysinternals. Depois de transferir o PsExec, execute o Windows PowerShell como um administrador e escreva:
      ```powershell
      psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
      ```

### <a name="storsimple-best-practices"></a>Práticas recomendadas do StorSimple

-   Certifique-se de que o dispositivo StorSimple está atualizado para [atualização 3 ou posterior](storsimple-install-update-3.md).
-   Isolar tráfego iSCSI e na cloud. Utilização de ligações de iSCSI dedicado para o tráfego entre o StorSimple e o servidor de cópia de segurança.
-   Certifique-se de que o dispositivo StorSimple é um destino de cópia de segurança dedicado. Cargas de trabalho mistas não são suportadas porque elas afetam o RTO e RPO.

### <a name="netbackup-best-practices"></a>Melhores práticas de NetBackup

-   A base de dados NetBackup deve ser local no servidor e reside num volume StorSimple.
-   Para a recuperação após desastre, criar cópias de segurança da base de dados NetBackup num volume do StorSimple.
-   Suportamos NetBackup completas e incrementais cópias de segurança (também referidas como diferenciais cópias de segurança incrementais em NetBackup) para esta solução. Recomendamos que não utilize sintéticas e cumulativas cópias de segurança incrementais.
-   Ficheiros de dados de cópia de segurança devem conter apenas os dados de uma tarefa específica. Por exemplo, nenhum suporte de dados acrescenta entre as diferentes tarefas são permitidas.

Para as definições de NetBackup mais recentes e melhores práticas para implementar estes requisitos, consulte a documentação de NetBackup em [www.veritas.com](https://www.veritas.com).


## <a name="retention-policies"></a>Políticas de retenção

Um dos tipos mais comuns para a política de retenção de cópia de segurança é uma política de avô, Pai e filho (GFS). Numa política GFS, uma cópia de segurança incremental é executada diariamente e cópias de segurança completas são feitas semanais e mensais. Volumes em camadas este resultados da política no StorSimple seis: um volume contém backups completos semanais, mensais e anuais; outros cinco volumes armazenam cópias de segurança incrementais diárias.

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

## <a name="set-up-netbackup-storage"></a>Configurar o armazenamento de NetBackup

### <a name="to-set-up-netbackup-storage"></a>Para configurar o armazenamento de NetBackup

1.  Na consola de administração NetBackup, selecione **suportes de dados e gestão de dispositivos** > **dispositivos** > **agrupamentos de discos**. No Assistente de configuração do conjunto de disco, selecione o tipo de servidor de armazenamento **AdvancedDisk**e, em seguida, selecione **próxima**.

    ![Consola de administração de NetBackup, o Assistente de configuração do conjunto de disco](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

2.  Selecione o seu servidor e, em seguida, selecione **seguinte**.

    ![Consola de administração de NetBackup, selecione o servidor](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

3.  Selecione o volume do StorSimple.

    ![Consola de administração de NetBackup, selecione o disco de volume do StorSimple](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

4.  Introduza um nome para o destino de cópia de segurança e, em seguida, selecione **próxima** > **seguinte** para concluir o assistente.

5.  Reveja as definições e, em seguida, selecione **concluir**.

6.  No final de cada atribuição de volume, altere as definições de dispositivo de armazenamento de acordo com esses recomendada na [melhores práticas para o StorSimple e NetBackup](#best-practices-for-storsimple-and-netbackup).

7. Repita os passos 1 a 6 até terminar de atribuir os volumes do StorSimple.

    ![Consola de administração de NetBackup, a configuração do disco](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Configurar o StorSimple como um destino de cópia de segurança primário

> [!NOTE]
> Restauros de dados de uma cópia de segurança que tem sido em camadas para a cloud ocorrerem em velocidades de cloud.

A figura seguinte mostra o mapeamento de um volume normal para uma tarefa de cópia de segurança. Neste caso, todas as cópias de segurança semanais mapeiam para o disco completo de Sábado e as cópias de segurança incrementais mapeiam para discos de incrementais de segunda-feira a sexta-feira. Todos os backups e restaurações são de um StorSimple camadas volume.

![Diagrama lógico de configuração de destino de cópia de segurança primário](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple como um destino de cópia de segurança primário GFS agendar exemplo

Eis um exemplo de uma agenda de rotação GFS para quatro semanas, mensais e anuais:

| Tipo de frequência/cópia de segurança | Completo | Incremental (1 a 5 de dias)  |   
|---|---|---|
| Semanal (1 a 4 de semanas) | Sábado | De segunda a sexta-feira |
| Custo  | Sábado  |   |
| Anualmente | Sábado  |   |   |

## <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>Atribuir volumes do StorSimple para uma tarefa de cópia de segurança NetBackup

A seguinte sequência parte do princípio de que NetBackup e o anfitrião de destino estão configurados de acordo com as diretrizes de agente NetBackup.

### <a name="to-assign-storsimple-volumes-to-a-netbackup-backup-job"></a>Para atribuir volumes do StorSimple para uma tarefa de cópia de segurança NetBackup

1.  Na consola de administração NetBackup, selecione **gestão NetBackup**, clique com botão direito **políticas**e, em seguida, selecione **nova política**.

    ![Consola de administração de NetBackup, criar uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

2.  Na **adicionar uma nova política** caixa de diálogo, introduza um nome para a política e, em seguida, selecione a **Assistente de configuração de política de uso** caixa de verificação. Selecione **OK**.

    ![Consola de administração de NetBackup, adicione uma caixa de diálogo nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

3.  No Assistente de configuração política de cópia de segurança, opte pelo tipo de cópia de segurança que pretende e, em seguida, selecione **seguinte**.

    ![Consola de administração de NetBackup, selecione tipo de cópia de segurança](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

4.  Para definir o tipo de política, selecione **padrão**e, em seguida, selecione **próxima**.

    ![Consola de administração de NetBackup, tipo de política selecione](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

5.  Selecione o seu anfitrião, selecione o **detetar o sistema operativo do cliente** caixa de verificação e, em seguida, selecione **Add**. Selecione **Seguinte**.

    ![Consola de administração de NetBackup, clientes de lista numa nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

6.  Selecione as unidades que pretende criar cópias de segurança.

    ![Consola de administração de NetBackup, seleções de cópia de segurança para uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

7.  Selecione os valores de frequência e de retenção que satisfazem os requisitos de rotação de cópia de segurança.

    ![Consola de administração de NetBackup, a frequência de cópia de segurança e a rotação para uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

8.  Selecione **próxima** > **próxima** > **concluir**.  Depois da política é criada, é possível modificar a agenda.

9.  Selecionar a opção para expandir a política que acabou de criar e, em seguida, selecione **agendas**.

    ![Consola de administração de NetBackup, agendas para uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

10.  Com o botão direito **diferenciais Inc**, selecione **copiar para o novo**e, em seguida, selecione **OK**.

    ![Consola de administração de NetBackup, agenda de cópia para uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

11.  Com o botão direito a agenda recém-criado e, em seguida, selecione **alteração**.

12.  Sobre o **atributos** separador, selecione a **substituir a seleção de armazenamento de política** caixa de verificação e, em seguida, selecione o volume de segunda-feira cópias de segurança incrementais aonde.

    ![NetBackup consola de administração, agendamento de alteração](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

13.  Sobre o **janela iniciar** separador, selecione a janela de tempo para as cópias de segurança.

    ![Consola de administração de NetBackup, janela de início de alteração](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

14.  Selecione **OK**.

15.  Repita os passos 10 a 14 para cada cópia de segurança incremental. Selecione o volume adequado e a agenda para cada cópia de segurança que cria.

16.  Com o botão direito a **diferenciais Inc** agendar e, em seguida, eliminá-lo.

17.  Modificar a agenda completa para satisfazer as necessidades de cópia de segurança.

    ![Consola de administração de NetBackup, agenda completa de alteração](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

18.  Altere a janela de início.

    ![Consola de administração de NetBackup, alterar a janela de início](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

19.  A agenda final tem esta aparência:

    ![Consola de administração de NetBackup, agenda final](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Configurar o StorSimple como um destino de cópia de segurança secundário

> [!NOTE]
>Restauros de dados de uma cópia de segurança que tem sido em camadas para a cloud ocorrerem em velocidades de cloud.

Nesse modelo, tem de ter um suporte de dados de armazenamento (que não seja o StorSimple) para ser usado como um cache temporário. Por exemplo, pode utilizar uma matriz redundante de volume de discos independentes (RAID) para acomodar o espaço, entrada/saída (e/s) e largura de banda. Recomendamos que utilize o RAID 5, 50 e 10.

A figura seguinte mostra a retenção de curta duração típica volumes locais (para o servidor) e volumes de arquivos de retenção de longa duração. Neste cenário, executam todas as cópias de segurança no volume RAID local (para o servidor). Estas cópias de segurança são periodicamente duplicadas e arquivadas para um volume de arquivos. É importante para o tamanho do volume RAID local (para o servidor), para que ele pode lidar com os requisitos de capacidade e desempenho de retenção curto prazo.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple como um exemplo GFS do destino de cópia de segurança secundário

![StorSimple como um diagrama lógico de destino de cópia de segurança secundário](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

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
| Custo | StorSimple mensalmente |   |   |   |   |   |
| Anualmente | StorSimple anuais  |   |   |   |   |   |   |


## <a name="assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Atribuir volumes do StorSimple para uma tarefa de arquivo e duplicação de NetBackup

Uma vez que NetBackup oferece um vasto leque de opções para a gestão de armazenamento e o suporte de dados, recomendamos que consulte com Veritas ou seu arquiteto NetBackup para avaliar corretamente os seus requisitos de política (SLP) do ciclo de vida de armazenamento.

Depois de definir os conjuntos de inicial do disco, é necessário definir três políticas do ciclo de vida de armazenamento adicional, num total de quatro políticas:
* LocalRAIDVolume
* StorSimpleWeek2-4
* StorSimpleMonthlyFulls
* StorSimpleYearlyFulls

### <a name="to-assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Para atribuir volumes do StorSimple para uma tarefa de arquivo e duplicação de NetBackup

1.  Na consola de administração NetBackup, selecione **armazenamento** > **políticas de ciclo de vida de armazenamento** > **nova política de ciclo de vida de armazenamento**.

    ![Consola de administração de NetBackup, nova política de ciclo de vida de armazenamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

2.  Introduza um nome para o instantâneo e, em seguida, selecione **adicionar**.

3.  Na **nova operação** caixa de diálogo a **propriedades** separador, para **operação**, selecione **cópia de segurança**. Selecione os valores que pretende para **armazenamento de destino**, **tipo de retenção**, e **período de retenção**. Selecione **OK**.

    ![Consola de administração de NetBackup, caixa de diálogo de operação de novo](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

    Isso define a primeira operação de cópia de segurança e o repositório.

4.  Selecionar a opção para realçar a operação anterior e, em seguida, selecione **adicionar**. Na **operação de armazenamento de alteração** caixa de diálogo caixa, selecione os valores pretendidos para **armazenamento de destino**, **tipo de retenção**, e **doperíododeretenção**.

    ![Consola de administração de NetBackup, caixa de diálogo de operação de armazenamento de alteração](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

5.  Selecionar a opção para realçar a operação anterior e, em seguida, selecione **adicionar**. Na **nova política de ciclo de vida de armazenamento** diálogo caixa, adicione as cópias de segurança mensais durante um ano.

    ![Consola de administração de NetBackup, caixa de diálogo nova política de ciclo de vida de armazenamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

6.  Repita os passos 4 e 5 até que criou a política de retenção SLP abrangente que precisa.

    ![Consola de administração de NetBackup, adicionar políticas na caixa de diálogo nova política de ciclo de vida de armazenamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

7.  Quando tiver terminado de definir a política de retenção do SLP, em **diretiva**, defina uma política de cópia de segurança ao seguir os passos detalhados [volumes do StorSimple de atribuir a uma tarefa de cópia de segurança NetBackup](#assigning-storsimple-volumes-to-a-netbackup-backup-job).

8.  Sob **agendas**, na **agenda de alteração** caixa de diálogo, com o botão direito **completa**e, em seguida, selecione **alteração**.

    ![Consola de administração de NetBackup, caixa de diálogo Schedule de alteração](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

9.  Selecione o **seleção de armazenamento de política de substituição** caixa de verificação e, em seguida, selecione a política de retenção do SLP que criou nos passos 1 a 6.

    ![Consola de administração de NetBackup, seleção de armazenamento de política de substituição](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

10.  Selecione **OK**e, em seguida, repita para a agenda de cópia de segurança incremental.

    ![Consola de administração de NetBackup, caixa de diálogo de alteração de agenda para cópias de segurança incrementais](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)


| Retenção de cópia de segurança de tipo | Tamanho (TiB) | Multiplicador GFS\* | Capacidade total (TiB)  |
|---|---|---|---|
| Completa semanal |  1  |  4 | 4  |
| Diária incremental  | 0,5  | 20 (ciclos são iguais ao número de semanas por mês) | 12 (2 para a quota adicional) |
| Total mensal  | 1 | 12 | 12 |
| Total anual | 1  | 10 | 10 |
| Requisito de GFS  |     |     | 38 |
| Quota adicional  | 4  |    | requisito de GFS 42 total |
\* O multiplicador GFS é o número de cópias que tem de proteger e manter-se para cumprir os requisitos de política de cópia de segurança.

## <a name="storsimple-cloud-snapshots"></a>Instantâneos de cloud do StorSimple

Instantâneos de cloud do StorSimple proteger os dados que residem no seu dispositivo StorSimple. A criação de um instantâneo de cloud é equivalente a bandas de cópia de segurança locais para um recurso de fora do local de envio. Se utilizar o armazenamento com redundância geográfica do Azure, criar um instantâneo de cloud é equivalente ao envio de bandas de cópia de segurança para vários sites. Se tiver de restaurar um dispositivo após um desastre, pode colocar outro dispositivo com StorSimple online e fazer uma ativação pós-falha. Após a ativação pós-falha, seria capaz de acessar os dados (em velocidades de nuvem) a partir do instantâneo de cloud mais recente.

A seguinte secção descreve como criar um script curto para iniciar e eliminar instantâneos de cloud do StorSimple durante o pós-processamento de cópia de segurança.

> [!NOTE]
> Instantâneos que são criados manualmente ou através de programação não siga a política de expiração de instantâneos do StorSimple. Estes instantâneos têm de ser manual ou programaticamente eliminados.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Iniciar e eliminar instantâneos de cloud com um script

> [!NOTE]
> Avalie cuidadosamente o repercussões de retenção de dados e de conformidade antes de eliminar um instantâneo do StorSimple. Para obter mais informações sobre como executar um script pós-cópia de segurança, consulte a [NetBackup documentação](http://www.veritas.com/docs/000094423).

### <a name="backup-lifecycle"></a>Ciclo de vida de cópia de segurança

![Diagrama de ciclo de vida de cópia de segurança](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

### <a name="requirements"></a>Requisitos

-   O servidor que executa o script tem de ter acesso a recursos na cloud do Azure.
-   A conta de utilizador tem de ter as permissões necessárias.
-   Uma política de cópia de segurança do StorSimple com os volumes do StorSimple associados deve ser configurar mas não ativada.
-   Será necessário o nome de recurso do StorSimple, a chave de registo, ID de política de cópia de segurança e nome do dispositivo.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Para iniciar ou eliminar um instantâneo de cloud

1.  [Instalar o Azure PowerShell](/powershell/azure/overview).
2. Transferir e configurar [gerir CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) script do PowerShell.
3. No servidor que executa o script, execute o PowerShell como administrador. Certifique-se de que executa o script com `-WhatIf $true` ver quais alterações que o script fará. Depois de concluída a validação, passar `-WhatIf $false`. Execute o comando abaixo:
```powershell
.\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
```
4.  Adicione o script para a sua tarefa de cópia de segurança no NetBackup. Para fazer isso, edite as opções tarefa NetBackup de pré-processamento e pós-processamento de comandos.

> [!NOTE]
> Recomendamos que execute a sua política de cópia de segurança de instantâneos do StorSimple cloud como um script pós-processamento no final da sua tarefa de cópia de segurança diária. Para obter mais informações sobre como criar cópias de segurança e restaurar o ambiente de aplicativo de backup para o ajudar a cumprir o RPO e RTO, consulte com o arquiteto de cópia de segurança.

## <a name="storsimple-as-a-restore-source"></a>StorSimple como uma origem de restauro

Restaura a partir de um trabalho de dispositivos do StorSimple como restaura a partir de qualquer dispositivo de armazenamento de blocos. As restaurações de dados que estão em camadas para a cloud ocorre em velocidades de cloud. Para dados locais, restaurações ocorrerem com a rapidez de disco local do dispositivo. Para obter informações sobre como executar um restauro, veja a [NetBackup documentação](http://www.veritas.com/docs/000094423). Recomendamos que está em conformidade com NetBackup restauro melhores práticas.

## <a name="storsimple-failover-and-disaster-recovery"></a>Recuperação após desastre e de ativação pós-falha do StorSimple

> [!NOTE]
> Para cenários de destino de cópia de segurança, o StorSimple Cloud Appliance não é suportado como um destino de restauro.

Após um desastre pode ser causado por uma variedade de fatores. A tabela seguinte lista os cenários comuns de recuperação após desastre.

| Cenário | Impacto | Como recuperar | Notas |
|---|---|---|---|
| Falha de dispositivo do StorSimple | Operações de backup e restauração são interrompidas. | Substitua o dispositivo com falhas e realizar [StorSimple ativação pós-falha e recuperação após desastre](storsimple-device-failover-disaster-recovery.md). | Se precisar de executar uma restauração após a recuperação de dispositivo, os conjuntos de trabalho de dados completos são obtidos a partir da cloud para o novo dispositivo. Todas as operações são em velocidades de cloud. O índice e o catálogo reanálise processo podem fazer com que todos os conjuntos de cópia de segurança a serem analisados e obtido a partir da camada de cloud para o escalão de dispositivo local, que pode ser um processo demorado. |
| Falha de servidor NetBackup | Operações de backup e restauração são interrompidas. | Reconstrua o servidor de cópia de segurança e efetuar o restauro de base de dados. | Tem de reconstruir ou restaurar o servidor de NetBackup no site de recuperação após desastre. Restaure a base de dados para o ponto mais recente. Se a base de dados restaurada NetBackup não está sincronizada com as tarefas de cópia de segurança mais recente, indexação e catalogação são necessário. Este índice e o catálogo de reanálise processo podem fazer com que todos os conjuntos de cópia de segurança a serem analisados e obtido a partir da camada de cloud para a camada do dispositivo local. Isto torna ainda mais tempo intensiva. |
| Falha do site que resulta na perda do servidor de cópia de segurança e o StorSimple | Operações de backup e restauração são interrompidas. | Restaurar o StorSimple primeiro e, em seguida, restaure NetBackup. | Restaurar o StorSimple primeiro e, em seguida, restaure NetBackup. Se precisar de executar uma restauração após a recuperação de dispositivo, conjuntos de dados completa de trabalho são obtidos a partir da cloud para o novo dispositivo. Todas as operações são em velocidades de cloud. |

## <a name="references"></a>Referências

Os seguintes documentos foram referenciados neste artigo:

- [Configuração do StorSimple multipath e/s](storsimple-configure-mpio-windows-server.md)
- [Cenários de armazenamento: Aprovisionamento dinâmico](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Utilizar o GPT unidades](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Configurar cópias de sombra para pastas partilhadas](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre como [restauro a partir de um conjunto de cópias de segurança](storsimple-restore-from-backup-set-u2.md).
- Saiba mais sobre como efetuar [dispositivo ativação pós-falha e recuperação após desastre](storsimple-device-failover-disaster-recovery.md).
