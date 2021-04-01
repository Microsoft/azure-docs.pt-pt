---
title: Série StorSimple 8000 como alvo de backup com NetBackup | Microsoft Docs
description: Conheça a configuração de alvo de backup StorSimple com Veritas NetBackup e as melhores práticas para integrar ambas as soluções.
services: storsimple
documentationcenter: ''
author: harshakirank
manager: matd
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2017
ms.author: matd
ms.openlocfilehash: 4f71cf82b675222836a73eec12d68bd8f62a5538
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94967283"
---
# <a name="storsimple-as-a-backup-target-with-netbackup"></a>StorSimple como alvo de backup com NetBackup

## <a name="overview"></a>Descrição Geral

Azure StorSimple é uma solução híbrida de armazenamento em nuvem da Microsoft. A StorSimple aborda as complexidades do crescimento exponencial dos dados utilizando uma conta de armazenamento Azure como uma extensão da solução no local, e ligando automaticamente os dados através do armazenamento no local e do armazenamento em nuvem.

Neste artigo, discutimos a integração StorSimple com a Veritas NetBackup, e as melhores práticas para integrar ambas as soluções. Também fazemos recomendações sobre como configurar veritas NetBackup para melhor integrar com storSimple. Adiamos as melhores práticas da Veritas, arquitetos de backup e administradores para a melhor forma de criar a Veritas NetBackup para satisfazer os requisitos individuais de backup e acordos de nível de serviço (SLAs).

Embora ilustremos passos de configuração e conceitos-chave, este artigo não é de forma alguma uma configuração passo a passo ou guia de instalação. Assumimos que os componentes básicos e as infraestruturas estão em ordem e prontos a apoiar os conceitos que descrevemos.

### <a name="who-should-read-this"></a>Quem deveria ler isto?

As informações neste artigo serão mais úteis para administradores de backup, administradores de armazenamento e arquitetos de armazenamento que tenham conhecimento de armazenamento, Windows Server 2012 R2, Ethernet, serviços na nuvem e Veritas NetBackup.

### <a name="supported-versions"></a>Versões suportadas

-   NetBackup 7.7.x e versões posteriores
-   [StorSimple Update 3 e versões posteriores](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Por que StorSimple como um alvo de reserva?

StorSimple é uma boa escolha para um alvo de backup porque:

-   Fornece armazenamento local padrão para aplicações de backup para usar como um destino de backup rápido, sem quaisquer alterações. Também pode utilizar o StorSimple para uma rápida restauração de cópias de segurança recentes.
-   O seu tiering em nuvem está perfeitamente integrado com uma conta de armazenamento em nuvem Azure para utilizar o armazenamento Azure rentável.
-   Fornece automaticamente armazenamento fora do local para recuperação de desastres.

## <a name="key-concepts"></a>Conceitos-chave

Como em qualquer solução de armazenamento, uma avaliação cuidadosa do desempenho de armazenamento da solução, SLAs, taxa de mudança e necessidades de crescimento da capacidade é fundamental para o sucesso. A ideia principal é que ao introduzir um nível de nuvem, os seus tempos de acesso e posições para a nuvem desempenham um papel fundamental na capacidade de StorSimple fazer o seu trabalho.

O StorSimple foi concebido para fornecer armazenamento a aplicações que operam num conjunto de dados de trabalho bem definido (dados quentes). Neste modelo, o conjunto de dados de trabalho é armazenado nos níveis locais, e o restante conjunto de dados não funcionando/frio/arquivado é hierárquico para a nuvem. Este modelo está representado na seguinte figura. A linha verde quase plana representa os dados armazenados nos níveis locais do dispositivo StorSimple. A linha vermelha representa a quantidade total de dados armazenados na solução StorSimple em todos os níveis. O espaço entre a linha verde plana e a curva vermelha exponencial representa a quantidade total de dados armazenados na nuvem.

Tiering StorSimple  
 ![ Diagrama de tiering StorSimple](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)

Com esta arquitetura em mente, você vai descobrir que storSimple é ideal para funcionar como um alvo de backup. Pode utilizar o StorSimple para:
-   Execute as suas restaurações mais frequentes a partir do conjunto de dados de trabalho local.
-   Use a nuvem para a recuperação de desastres fora do local e dados mais antigos, onde as restaurações são menos frequentes.

## <a name="storsimple-benefits"></a>Benefícios StorSimple

A StorSimple fornece uma solução no local que está perfeitamente integrada com o Microsoft Azure, aproveitando o acesso sem emenda aos locais e armazenamento em nuvem.

A StorSimple utiliza um nivelamento automático entre o dispositivo no local, que tem dispositivo de estado sólido (SSD) e armazenamento SCSI (SAS) em série e armazenamento Azure. O tiering automático mantém os dados frequentemente acedidos locais, nos níveis SSD e SAS. Move-se com pouca frequência acesso a dados para o Azure Storage.

A StorSimple oferece estes benefícios:

-   Algoritmos únicos de desduplicação e compressão que usam a nuvem para atingir níveis de deduplica sem precedentes
-   Elevada disponibilidade
-   Geo-replicação utilizando a geo-replicação do Azure
-   Integração do Azure
-   Encriptação de dados na nuvem
-   Melhoria da recuperação e da conformidade com as catástrofes

Embora o StorSimple apresente dois cenários principais de implementação (alvo de backup primário e alvo de backup secundário), fundamentalmente, é um dispositivo de armazenamento simples e bloqueado. StorSimple faz toda a compressão e deduplicação. Envia e recupera dados perfeitamente entre a nuvem e o sistema de aplicação e ficheiro.

Para obter mais informações sobre o StorSimple, consulte [a série StorSimple 8000: Solução híbrida de armazenamento em nuvem](storsimple-overview.md). Além disso, pode rever as especificações técnicas da [série StorSimple 8000](./storsimple-8000-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> A utilização de um dispositivo StorSimple como alvo de backup é suportada apenas para versões StorSimple 8000 Update 3 e versões posteriores.

## <a name="architecture-overview"></a>Descrição geral da arquitetura

As tabelas a seguir mostram a orientação inicial do modelo-arquitectura do dispositivo.

**Capacidades StorSimple para armazenamento local e em nuvem**

| Capacidade de armazenamento       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| Capacidade de armazenamento local | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Capacidade de armazenamento em nuvem | &gt; 200 TiB\* | &gt; 500 TiB\* |

\* O tamanho do armazenamento não pressupõe nenhuma deduplicação ou compressão.

**Capacidades StorSimple para backups primários e secundários**

| Cenário de backup  | Capacidade de armazenamento local  | Capacidade de armazenamento em nuvem  |
|---|---|---|
| Backup primário  | Cópias de segurança recentes armazenadas no armazenamento local para uma rápida recuperação para cumprir o objetivo do ponto de recuperação (RPO) | O histórico de backup (RPO) encaixa na capacidade da nuvem |
| Backup secundário | Cópia secundária de dados de backup pode ser armazenada na capacidade da nuvem  | N/D  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple como alvo principal de backup

Neste cenário, os volumes StorSimple são apresentados à aplicação de backup como o único repositório para cópias de segurança. A figura a seguir mostra uma arquitetura de solução na qual todos os backups usam volumes tiered StorSimple para backups e restauros.

![StorSimple como um diagrama lógico alvo de backup primário](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Passos lógicos de backup do alvo primário

1.  O servidor de backup contacta o agente de backup do alvo e o agente de backup transmite dados para o servidor de backup.
2.  O servidor de backup escreve dados para os volumes tiered StorSimple.
3.  O servidor de backup atualiza a base de dados do catálogo e, em seguida, termina o trabalho de backup.
4.  Um script instantâneo ativa o gestor de instantâneo StorSimple (iniciar ou eliminar).
5.  O servidor de backup elimina cópias de segurança expiradas com base numa política de retenção.

### <a name="primary-target-restore-logical-steps"></a>Alvo primário restaurar passos lógicos

1.  O servidor de backup começa a restaurar os dados apropriados do repositório de armazenamento.
2.  O agente de backup recebe os dados do servidor de reserva.
3.  O servidor de reserva termina o trabalho de restauro.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple como alvo de backup secundário

Neste cenário, os volumes StorSimple são usados principalmente para retenção ou arquivamento a longo prazo.

A figura a seguir mostra uma arquitetura em que os backups iniciais e os restauros visam um volume de alto desempenho. Estas cópias de segurança são copiadas e arquivadas num volume de nível StorSimple numa programação definida.

É importante dimensionar o seu volume de alto desempenho para que possa lidar com a sua capacidade de capacidade de retenção e requisitos de desempenho.

![Diagrama que mostra uma arquitetura em que backups iniciais e restaura visam um alto volume de desempenho.](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Passos lógicos de backup de alvo secundário

1.  O servidor de backup contacta o agente de backup do alvo e o agente de backup transmite dados para o servidor de backup.
2.  O servidor de backup escreve dados para armazenamento de alto desempenho.
3.  O servidor de backup atualiza a base de dados do catálogo e, em seguida, termina o trabalho de backup.
4.  O servidor de cópias de segurança copia cópias de segurança para storSimple com base numa política de retenção.
5.  Um script instantâneo ativa o gestor de instantâneo StorSimple (iniciar ou eliminar).
6.  O servidor de backup elimina as cópias de segurança expiradas com base numa política de retenção.

### <a name="secondary-target-restore-logical-steps"></a>Alvo secundário restaurar passos lógicos

1.  O servidor de backup começa a restaurar os dados apropriados do repositório de armazenamento.
2.  O agente de backup recebe os dados do servidor de reserva.
3.  O servidor de reserva termina o trabalho de restauro.

## <a name="deploy-the-solution"></a>Implementar a solução

A implementação desta solução requer três etapas:
1. Prepare a infraestrutura de rede.
2. Desloque o seu dispositivo StorSimple como alvo de reserva.
3. Implementar Veritas NetBackup.

Cada passo é discutido em detalhe nas seguintes secções.

### <a name="set-up-the-network"></a>Configurar a rede

Como o StorSimple é uma solução integrada com a nuvem Azure, a StorSimple requer uma ligação ativa e de trabalho à nuvem Azure. Esta ligação é usada para operações como instantâneos em nuvem, gestão de dados e transferência de metadados, e para tierest de dados mais antigos e menos acedidos para armazenamento em nuvem Azure.

Para que a solução funcione da melhor forma, recomendamos que siga estas boas práticas de networking:

-   A ligação que liga o tiering StorSimple ao Azure deve satisfazer os seus requisitos de largura de banda. Para isso, aplique o nível de Qualidade de Serviço (QoS) adequado aos seus interruptores de infraestrutura para combinar com os seus SLAs de RPO e tempo de recuperação (RTO).

-   As latências máximas de acesso ao armazenamento Azure Blob devem rondar os 80 ms.

### <a name="deploy-storsimple"></a>Implementar StorSimple

Para obter orientação de implementação passo a passo StorSimple, consulte [implementar o seu dispositivo StorSimple no local](./storsimple-8000-deployment-walkthrough-u2.md).

### <a name="deploy-netbackup"></a>Implementar NetBackup

Para obter orientação de implementação passo a passo netBackup 7.7.x, consulte a [documentação NetBackup 7.7.x](http://www.veritas.com/docs/000094423).

## <a name="set-up-the-solution"></a>Configurar a solução

Nesta secção, demonstramos alguns exemplos de configuração. Os exemplos e recomendações que se seguem ilustram a implementação mais básica e fundamental. Esta implementação pode não se aplicar diretamente aos seus requisitos específicos de backup.

### <a name="set-up-storsimple"></a>Configurar storSimple

| Tarefas de implantação StorSimple  | Comentários adicionais |
|---|---|
| Coloque o dispositivo StorSimple no local. | Versões suportadas: Atualização 3 e versões posteriores. |
| Liga o alvo de reserva. | Utilize estes comandos para ligar ou desligar o modo alvo de backup e para obter o estado. Para obter mais informações, consulte [Ligar-se remotamente a um dispositivo StorSimple](./storsimple-8000-remote-connect.md).</br> Para ligar o modo de backup: `Set-HCSBackupApplianceMode -enable` . </br> Para desligar o modo de backup: `Set-HCSBackupApplianceMode -disable` . </br> Para obter o estado atual das definições do modo de backup: `Get-HCSBackupApplianceMode` . |
| Crie um recipiente de volume comum para o seu volume que armazena os dados de backup. Todos os dados num recipiente de volume são desduplicados. | Os recipientes de volume StorSimple definem domínios de desduplicação.  |
| Criar volumes StorSimple. | Crie volumes com tamanhos o mais próximo possível da utilização prevista, porque o tamanho do volume afeta o tempo de duração do instantâneo em nuvem. Para obter informações sobre como dimensionar um volume, leia sobre [as políticas de retenção.](#retention-policies)</br> </br> Utilize volumes hierárquicos StorSimple e selecione o Use this volume para uma caixa de verificação de **dados de arquivo com menos acesso frequente.** </br> A utilização de apenas volumes fixados localmente não é suportada. |
| Crie uma política de backup StorSimple única para todos os volumes de backup alvo. | Uma política de backup StorSimple define o grupo de consistência do volume. |
| Desative o horário à medida que as imagens expiram. | As imagens são ativadas como uma operação pós-processamento. |

### <a name="set-up-the-host-backup-server-storage"></a>Configurar o armazenamento do servidor de backup do anfitrião

Configurar o armazenamento do servidor de backup do anfitrião de acordo com estas diretrizes:  

- Não utilize volumes abrangedos (criados pela Windows Disk Management); volumes abrangedos não são suportados.
- Formate os seus volumes utilizando o NTFS com um tamanho de atribuição de 64 KB.
- Mapear os volumes StorSimple diretamente para o servidor NetBackup.
    - Utilize o iSCSI para servidores físicos.
    - Utilize discos de passagem para servidores virtuais.


## <a name="best-practices-for-storsimple-and-netbackup"></a>Melhores práticas para StorSimple e NetBackup

Desacione a sua solução de acordo com as diretrizes nas seguintes secções.

### <a name="operating-system-best-practices"></a>Melhores práticas do sistema operativo

- Desative a encriptação e a desduplica do Windows Server para o sistema de ficheiros NTFS.
- Desativar a desfragmentação do Servidor do Windows nos volumes StorSimple.
- Desativar a indexação do Servidor do Windows nos volumes StorSimple.
- Esclipire no hospedeiro de origem (não contra os volumes StorSimple).
- Desligue a [manutenção](/windows/win32/w8cookbook/automatic-maintenance) padrão do Servidor do Windows no Gestor de Tarefas. Faça-o de uma das seguintes formas:
  - Desligue o configurador de manutenção no Programador de Tarefas do Windows.
  - Descarregue [o PsExec](/sysinternals/downloads/psexec) a partir de Sysinternals windows. Depois de descarregar o PsExec, executar o Windows PowerShell como administrador e escrever:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>StorSimple boas práticas

-   Certifique-se de que o dispositivo StorSimple é atualizado para [a Atualização 3 ou posterior](./index.yml).
-   Isolar o iSCSI e o tráfego na nuvem. Utilize ligações iSCSI dedicadas para o tráfego entre o StorSimple e o servidor de backup.
-   Certifique-se de que o seu dispositivo StorSimple é um alvo de reserva dedicado. As cargas de trabalho mistas não são suportadas porque afetam o seu RTO e RPO.

### <a name="netbackup-best-practices"></a>Boas práticas da NetBackup

-   A base de dados NetBackup deve ser local para o servidor e não residir num volume StorSimple.
-   Para a recuperação de desastres, volte a subir a base de dados NetBackup num volume StorSimple.
-   Apoiamos backups completos e incrementais do NetBackup (também referidos como backups incrementais diferenciais no NetBackup) para esta solução. Recomendamos que não utilize cópias de segurança incrementais sintéticas e cumulativas.
-   Os ficheiros de dados de backup devem conter apenas os dados para um trabalho específico. Por exemplo, não são permitidos apêndices de meios de comunicação em diferentes postos de trabalho.

Para obter as definições e as melhores práticas do NetBackup para a implementação destes requisitos, consulte a documentação do NetBackup em [www.veritas.com](https://www.veritas.com).


## <a name="retention-policies"></a>Políticas de retenção

Um dos tipos de política de retenção de backup mais comum é uma política de avô, pai e filho (GFS). Numa política GFS, uma cópia de segurança incremental é realizada diariamente e as cópias de segurança completas são feitas semanalmente e mensais. Esta política resulta em seis volumes tiered StorSimple: um volume contém as cópias de segurança completas semanais, mensais e anuais; os outros cinco volumes armazenam cópias de segurança incrementais diárias.

No exemplo seguinte, utilizamos uma rotação GFS. O exemplo pressupõe o seguinte:

-   São utilizados dados não deduped ou comprimidos.
-   Os backups completos são 1 TiB cada.
-   Os backups incrementais diários são 500 GiB cada.
-   Quatro reforços semanais são mantidos por um mês.
-   Doze reforços mensais são mantidos por um ano.
-   Um apoio anual é mantido por 10 anos.

Com base nos pressupostos anteriores, crie um volume hierárquico de 26-TiB StorSimple para as cópias de segurança completas mensais e anual. Crie um volume de 5-TiB StorSimple para cada uma das cópias de segurança diárias incrementais.

| Retenção do tipo de backup | Tamanho (TiB) | Multiplicador GFS\* | Capacidade total (TiB)  |
|---|---|---|---|
| Semanalmente cheio | 1 | 4  | 4 |
| Incremental diário | 0,5 | 20 (ciclos igual número de semanas por mês) | 12 (2 para quota adicional) |
| Mensalmente cheio | 1 | 12 | 12 |
| Anualmente cheio | 1  | 10 | 10 |
| Requisito GFS |   | 38 |   |
| Quota adicional  | 4  |   | 42 requisitos totais de GFS  |

\* O multiplicador GFS é o número de cópias que precisa de proteger e reter para satisfazer os seus requisitos de política de backup.

## <a name="set-up-netbackup-storage"></a>Configurar o armazenamento NetBackup

### <a name="to-set-up-netbackup-storage"></a>Para configurar o armazenamento NetBackup

1.  Na consola de administração NetBackup, selecione Redes de Discos **de Mídia e Dispositivos** de Gestão de  >    >  **Dispositivos**. No Assistente de Configuração da Piscina do Disco, selecione o tipo de servidor de armazenamento **AdvancedDisk** e, em seguida, selecione **Seguinte**.

    ![Consola de administração NetBackup, Assistente de Configuração de Piscina de Disco](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

2.  Selecione o seu servidor e, em seguida, selecione **Seguinte**.

    ![Consola de administração NetBackup, selecione o servidor](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

3.  Selecione o seu volume StorSimple.

    ![Consola de administração NetBackup, selecione o disco de volume StorSimple](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

4.  Introduza um nome para o alvo de reserva e, em seguida, selecione  >  **Next** para terminar o assistente.

5.  Reveja as definições e, em seguida, **selecione Terminar**.

6.  No final de cada atribuição de volume, altere as definições do dispositivo de armazenamento para corresponder às recomendadas nas [melhores práticas para StorSimple e NetBackup](#best-practices-for-storsimple-and-netbackup).

7. Repita os passos 1-6 até terminar de atribuir os seus volumes StorSimple.

    ![Consola de administração NetBackup, configuração do disco](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Configurar o StorSimple como um alvo de backup primário

> [!NOTE]
> Os dados são restaurados a partir de uma cópia de segurança que foi nivelada para a nuvem ocorrem a velocidades de nuvem.

A figura a seguir mostra o mapeamento de um volume típico para um trabalho de reserva. Neste caso, todos os backups semanais mapeiam para o disco completo de sábado, e o mapa de backups incrementais para Monday-Friday discos incrementais. Todos os backups e restauros são de um volume de nível StorSimple.

![Diagrama lógico de configuração do alvo de backup primário](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple como um exemplo de agenda GFS alvo de backup primário

Aqui está um exemplo de um horário de rotação GFS para quatro semanas, mensalmente, e anualmente:

| Tipo de frequência/backup | Completa | Incremental (dias 1-5)  |   
|---|---|---|
| Semanal (semanas 1-4) | Saturday | Monday-Friday |
| Mensalmente  | Saturday  |   |
| Anualmente | Saturday  |   |

## <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>Atribuir volumes StorSimple a um trabalho de backup netBackup

A seguinte sequência pressupõe que o NetBackup e o anfitrião-alvo estão configurados de acordo com as diretrizes do agente NetBackup.

### <a name="to-assign-storsimple-volumes-to-a-netbackup-backup-job"></a>Para atribuir volumes StorSimple a um trabalho de backup netBackup

1. Na Consola de Administração NetBackup, selecione **NetBackup Management**, **Right-click Policies** e, em seguida, selecione New **Policy**.

   ![Consola de administração NetBackup, criar uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

2. Na caixa de diálogo **'Adicionar uma nova política',** introduza um nome para a apólice e, em seguida, selecione a caixa de **verificação 'Assistente de configuração' 'Utilização'.** Selecione **OK**.

   ![Consola de administração NetBackup, Adicione uma nova caixa de diálogo de política](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

3. No Assistente de Configuração de Política de Cópia de Segurança, eleja o tipo de cópia de segurança que deseja e, em seguida, selecione **Seguinte**.

   ![Consola de administração NetBackup, selecione o tipo de backup](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

4. Para definir o tipo de apólice, selecione **Standard** e, em seguida, selecione **Seguinte**.

   ![Consola de administração NetBackup, tipo de política selecionado](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

5. Selecione o seu anfitrião, selecione a caixa **de verificação do sistema operativo do cliente Detect** e, em seguida, selecione **Adicionar**. Selecione **Seguinte**.

   ![Consola de administração NetBackup, listar clientes numa nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

6. Selecione os discos que pretende fazer recuar.

   ![Consola de administração NetBackup, seleções de backup para uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

7. Selecione os valores de frequência e retenção que satisfaçam os seus requisitos de rotação de backup.

   ![Consola de administração NetBackup, frequência de backup e rotação para uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

8. Selecione   >  **Next**  >  **Next Finish**.  Pode modificar o calendário após a criação da apólice.

9. Selecione para expandir a política que acabou de criar e, em seguida, **selecione Agendas**.

   ![Consola de administração NetBackup, horários para uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

10. Clique à direita **Diferencial-Inc,** selecione **Copy para novo** e, em seguida, selecione **OK**.

    ![Consola de administração NetBackup, agenda de cópias para uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

11. Clique com o botão direito na programação recém-criada e, em seguida, selecione **Alterar**.

12. No separador **Atributos,** selecione a caixa **de verificação de seleção de armazenamento de política de Override** e, em seguida, selecione o volume para onde as cópias de segurança incrementais de segunda-feira vão.

    ![Consola de administração NetBackup, horário de mudança](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

13. No **separador Janela de Início,** selecione a janela de tempo para as suas cópias de segurança.

    ![Consola de administração NetBackup, janela de arranque de mudança](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

14. Selecione **OK**.

15. Repita os passos 10-14 para cada cópia de segurança incremental. Selecione o volume e o horário adequados para cada cópia de segurança que criar.

16. Clique com o botão direito na programação **Diferencial-Inc** e, em seguida, elimine-o.

17. Modifique o seu horário completo para atender às suas necessidades de backup.

    ![Consola de administração NetBackup, altere o horário completo](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

18. Mude a janela de partida.

    ![Consola de administração NetBackup, mude a janela de partida](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

19. A agenda final é assim:

    ![Consola de administração NetBackup, horário final](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Configurar o StorSimple como um alvo de backup secundário

> [!NOTE]
>Os dados são restaurados a partir de uma cópia de segurança que foi nivelada para a nuvem ocorrem a velocidades de nuvem.

Neste modelo, deve dispor de um meio de armazenamento (com além do StorSimple) para servir de cache temporário. Por exemplo, pode utilizar uma matriz redundante de discos independentes (RAID) para acomodar o espaço, a entrada/saída (I/O) e a largura de banda. Recomendamos a utilização de RAID 5, 50 e 10.

A seguinte figura mostra volumes típicos de retenção de curto prazo locais (para o servidor) e volumes de arquivos de retenção a longo prazo. Neste cenário, todas as cópias de segurança são executadas no volume RAID local (para o servidor). Estas cópias de segurança são periodicamente duplicadas e arquivadas num volume de arquivos. É importante dimensionar o volume RAID local (para o servidor) para que possa lidar com os seus requisitos de capacidade de retenção e desempenho de curto prazo.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple como um alvo secundário de backup GFS exemplo

![StorSimple como um diagrama lógico de backup secundário](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

A tabela seguinte mostra como configurar backups para executar nos discos locais e StorSimple. Inclui requisitos de capacidade individuais e totais.

### <a name="backup-configuration-and-capacity-requirements"></a>Configuração de backup e requisitos de capacidade

| Tipo de backup e retenção | Armazenamento configurado | Tamanho (TiB) | Multiplicador GFS | Capacidade total \* (TiB) |
|---|---|---|---|---|
| Semana 1 (completa e incremental) |Disco local (a curto prazo)| 1 | 1 | 1 |
| StorSimple semanas 2-4 |Disco StorSimple (a longo prazo) | 1 | 4 | 4 |
| Mensalmente cheio |Disco StorSimple (a longo prazo) | 1 | 12 | 12 |
| Anualmente cheio |Disco StorSimple (a longo prazo) | 1 | 1 | 1 |
|Requisito de tamanho de volumes GFS |  |  |  | 18*|

\* A capacidade total inclui 17 TiB de discos StorSimple e 1 TiB do volume raid local.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>Calendário de exemplo GFS: Rotação GFS semanal, mensal e horário anual

| Semana | Completa | Dia incremental 1 | Dia incremental 2 | Dia incremental 3 | Dia incremental 4 | Dia incremental 5 |
|---|---|---|---|---|---|---|
| Semana 1 | Volume de RAID local  | Volume de RAID local | Volume de RAID local | Volume de RAID local | Volume de RAID local | Volume de RAID local |
| Semana 2 | StorSimple semanas 2-4 |   |   |   |   |   |
| Semana 3 | StorSimple semanas 2-4 |   |   |   |   |   |
| Semana 4 | StorSimple semanas 2-4 |   |   |   |   |   |
| Mensalmente | StorSimple mensalmente |   |   |   |   |   |
| Anualmente | StorSimple anualmente  |   |   |   |   |   |


## <a name="assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Atribuir volumes StorSimple a um trabalho de arquivo e duplicação netBackup

Uma vez que o NetBackup oferece um vasto leque de opções para armazenamento e gestão de meios, recomendamos que consulte a Veritas ou o seu arquiteto NetBackup para avaliar adequadamente os requisitos da sua política de ciclo de vida de armazenamento (SLP).

Depois de definir as piscinas iniciais do disco, precisa definir três políticas adicionais de ciclo de vida de armazenamento, para um total de quatro políticas:
* LocalRAIDVolume
* StorSimpleWeek2-4
* StorSimpleMonthlyFulls
* StorSimpleYearlyFulls

### <a name="to-assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Para atribuir volumes StorSimple a um trabalho de arquivo e duplicação netBackup

1. Na consola administração NetBackup, selecione   >  **Storage Storage Lifecycle Policies** New Storage  >  **Lifecycle Policy**.

   ![Consola de administração NetBackup, nova política de ciclo de vida de armazenamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

2. Introduza um nome para o instantâneo e, em seguida, **selecione Adicionar**.

3. Na caixa de diálogo **'Nova Operação',** no **separador Propriedades,** para **operação**, selecione **Backup**. Selecione os valores que deseja para **armazenamento de destino,** **tipo de retenção** e **período de retenção.** Selecione **OK**.

   ![Consola de administração NetBackup, nova caixa de diálogo de operação](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

   Isto define a primeira operação de backup e repositório.

4. Selecione para realçar a operação anterior e, em seguida, **selecione Adicionar**. Na caixa de diálogo de **operação de armazenamento de alteração,** selecione os valores que deseja para **o armazenamento do destino,** **tipo de retenção** e **período de retenção**.

   ![Consola de administração NetBackup,Alterar caixa de diálogo de operação de armazenamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

5. Selecione para realçar a operação anterior e, em seguida, **selecione Adicionar**. Na caixa de diálogo **New Storage Lifecycle Policy,** adicione cópias de segurança mensais durante um ano.

   ![Consola de administração NetBackup, caixa de diálogo de política de ciclo de vida de armazenamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

6. Repita os passos 4-5 até criar a política abrangente de retenção SLP de que necessita.

   ![Consola de administração NetBackup, adicione políticas na caixa de diálogo de política de ciclo de vida de novo armazenamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

7. Quando terminar de definir a sua política de retenção SLP, no âmbito **da Política,** defina uma política de backup seguindo os passos detalhados na [atribuição de volumes StorSimple a um trabalho de backup netBackup](#assigning-storsimple-volumes-to-a-netbackup-backup-job).

8. Em **Horários**, na caixa de diálogo **de alteração,** clique à direita **Em cheio** e, em seguida, selecione **Alterar**.

   ![Consola de administração NetBackup, caixa de diálogo de alteração de horário](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

9. Selecione a caixa **de verificação de seleção de armazenamento de política de Override** e, em seguida, selecione a política de retenção SLP que criou nos passos 1-6.

   ![Consola de administração NetBackup, anular a seleção de armazenamento de políticas](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

10. Selecione **OK** e, em seguida, repita para o calendário de backup incremental.

    ![Consola de administração NetBackup, caixa de diálogo de alteração de agenda para backups incrementais](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)


| Retenção do tipo de backup | Tamanho (TiB) | Multiplicador GFS\* | Capacidade total (TiB)  |
|---|---|---|---|
| Semanalmente cheio |  1  |  4 | 4  |
| Incremental diário  | 0,5  | 20 (os ciclos são iguais ao número de semanas por mês) | 12 (2 para quota adicional) |
| Mensalmente cheio  | 1 | 12 | 12 |
| Anualmente cheio | 1  | 10 | 10 |
| Requisito GFS  |     |     | 38 |
| Quota adicional  | 4  |    | 42 requisitos totais de GFS |

\* O multiplicador GFS é o número de cópias que precisa de proteger e reter para satisfazer os seus requisitos de política de backup.

## <a name="storsimple-cloud-snapshots"></a>StorSimple fotos em nuvem

As imagens em nuvem StorSimple protegem os dados que residem no seu dispositivo StorSimple. Criar uma imagem em nuvem equivale a enviar fitas de backup locais para uma instalação fora do local. Se utilizar o armazenamento geo-redundante do Azure, criar uma imagem em nuvem equivale a enviar fitas de backup para vários sites. Se precisar restaurar um dispositivo após um desastre, pode trazer online outro dispositivo StorSimple e fazer uma falha. Após o failover, você seria capaz de aceder aos dados (a velocidades de nuvem) a partir do mais recente instantâneo em nuvem.

A secção seguinte descreve como criar um script curto para iniciar e eliminar imagens em nuvem StorSimple durante o pós-processamento de backup.

> [!NOTE]
> As imagens que são criadas manualmente ou programáticamente não seguem a política de expiração do instantâneo StorSimple. Estas imagens devem ser apagadas manualmente ou programáticamente.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Iniciar e apagar instantâneos em nuvem usando um script

> [!NOTE]
> Avalie cuidadosamente as repercussões de conformidade e retenção de dados antes de eliminar uma imagem StorSimple. Para obter mais informações sobre como executar um script pós-backup, consulte a [documentação netBackup](http://www.veritas.com/docs/000094423).

### <a name="backup-lifecycle"></a>Ciclo de vida de reserva

![Diagrama de ciclo de vida de reserva](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

### <a name="requirements"></a>Requisitos

-   O servidor que executa o script deve ter acesso aos recursos da nuvem Azure.
-   A conta de utilizador deve ter as permissões necessárias.
-   Deve ser criada uma política de backup StorSimple com os volumes StorSimple associados, mas não ligada.
-   Você precisará do nome de recurso StorSimple, chave de registo, nome do dispositivo e identificação de política de backup.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Para iniciar ou apagar uma imagem em nuvem

1. [Instalar o Azure PowerShell](/powershell/azure/).
2. Baixar e configurar [Manage-CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) script PowerShell.
3. No servidor que executa o script, executa o PowerShell como administrador. Certifique-se de que executa o script `-WhatIf $true` para ver quais as alterações que o script irá fazer. Uma vez concluída a validação, `-WhatIf $false` passe. Executar o comando abaixo:
   ```powershell
   .\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
   ```
4. Adicione o script ao seu trabalho de backup no NetBackup. Para isso, edite os comandos de pré-processamento e pós-processamento das suas opções de trabalho NetBackup.

> [!NOTE]
> Recomendamos que execute a sua política de backup de imagens de nuvem StorSimple como um script pós-processamento no final do seu trabalho diário de backup. Para obter mais informações sobre como fazer backup e restaurar o ambiente de aplicação de backup para ajudá-lo a conhecer o seu RPO e RTO, consulte o seu arquiteto de reserva.

## <a name="storsimple-as-a-restore-source"></a>StorSimple como fonte de restauro

Os restauros de um dispositivo StorSimple funcionam como restauros de qualquer dispositivo de armazenamento de blocos. A restauração de dados que é hierárquico para a nuvem ocorre a velocidades de nuvem. Para os dados locais, as restaurações ocorrem na velocidade do disco local do dispositivo. Para obter informações sobre como realizar uma restauração, consulte a [documentação netBackup](http://www.veritas.com/docs/000094423). Recomendamos que esteja em conformidade com o NetBackup para restaurar as melhores práticas.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple failover e recuperação de desastres

> [!NOTE]
> Para cenários de backup alvo, o StorSimple Cloud Appliance não é suportado como um alvo de restauro.

Um desastre pode ser causado por uma variedade de fatores. A tabela que se segue lista cenários comuns de recuperação de desastres.

| Scenario | Impacto | Como recuperar | Notas |
|---|---|---|---|
| Falha do dispositivo StorSimple | As operações de backup e restauro são interrompidas. | Substitua o dispositivo falhado e execute [a storSimple failover e a recuperação de desastres](./storsimple-8000-device-failover-disaster-recovery.md). | Se necessitar de efetuar uma restauração após a recuperação do dispositivo, os conjuntos completos de trabalho de dados são recuperados da nuvem para o novo dispositivo. Todas as operações estão a velocidades de nuvem. O processo de rescandisção de índices e catálogos pode fazer com que todos os conjuntos de backup sejam digitalizados e retirados do nível de nuvem para o nível do dispositivo local, o que pode ser um processo demorado. |
| Falha do servidor NetBackup | As operações de backup e restauro são interrompidas. | Reconstruir o servidor de backup e executar a restauração da base de dados. | Tem de reconstruir ou restaurar o servidor NetBackup no local de recuperação de desastres. Restaurar a base de dados até ao ponto mais recente. Se a base de dados NetBackup restaurada não estiver sincronizada com os seus últimos trabalhos de backup, é necessário indexar e catalogar. Este processo de rescaning de índice e catálogo pode fazer com que todos os conjuntos de backup sejam digitalizados e retirados do nível de nuvem para o nível do dispositivo local. Isto torna-o mais intensivo em tempo. |
| Falha no site que resulta na perda do servidor de backup e StorSimple | As operações de backup e restauro são interrompidas. | Restaurar o StorSimple primeiro e, em seguida, restaurar o NetBackup. | Restaurar o StorSimple primeiro e, em seguida, restaurar o NetBackup. Se necessitar de efetuar uma restauração após a recuperação do dispositivo, os conjuntos completos de trabalho de dados são recuperados da nuvem para o novo dispositivo. Todas as operações estão a velocidades de nuvem. |

## <a name="references"></a>Referências

Para este artigo foram referenciados os seguintes documentos:

- [StorSimple configuração de E/S multipata](./storsimple-8000-configure-mpio-windows-server.md)
- [Cenários de armazenamento: Provisão fina](/windows-hardware/drivers/storage/thin-provisioning)
- [Usando unidades GPT](/previous-versions/windows/hardware/design/dn653580(v=vs.85)#EHD)
- [Configurar cópias-sombra para pastas partilhadas](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771893(v=ws.11))

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como restaurar a [partir de um conjunto de backup](./storsimple-8000-restore-from-backup-set-u2.md).
- Saiba mais sobre como executar o [failover do dispositivo e a recuperação de desastres](./storsimple-8000-device-failover-disaster-recovery.md).