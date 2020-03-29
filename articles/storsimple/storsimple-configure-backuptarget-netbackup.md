---
title: Série StorSimple 8000 como alvo de backup com NetBackup / Microsoft Docs
description: Descreve a configuração do alvo de backup StorSimple com veritas NetBackup.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876215"
---
# <a name="storsimple-as-a-backup-target-with-netbackup"></a>StorSimple como alvo de backup com NetBackup

## <a name="overview"></a>Descrição geral

O Azure StorSimple é uma solução híbrida de armazenamento em nuvem da Microsoft. A StorSimple aborda as complexidades do crescimento exponencial de dados utilizando uma conta de armazenamento Azure como uma extensão da solução no local e enigentando automaticamente os dados através do armazenamento no local e armazenamento em nuvem.

Neste artigo, discutimos a integração storSimple com a Veritas NetBackup, e as melhores práticas para integrar ambas as soluções. Também fazemos recomendações sobre como configurar a Veritas NetBackup para melhor integrar com o StorSimple. Adiamos para veritas boas práticas, arquitetos de reserva e administradores para a melhor maneira de criar veritas NetBackup para satisfazer requisitos individuais de backup e acordos de nível de serviço (SLAs).

Embora ilustremos passos de configuração e conceitos-chave, este artigo não é de forma alguma uma configuração passo a passo ou um guia de instalação. Assumimos que os componentes básicos e as infraestruturas estão em ordem e prontos para apoiar os conceitos que descrevemos.

### <a name="who-should-read-this"></a>Quem deve ler isto?

As informações neste artigo serão mais úteis para administradores de backup, administradores de armazenamento e arquitetos de armazenamento que tenham conhecimento de armazenamento, Windows Server 2012 R2, Ethernet, serviços na nuvem e Veritas NetBackup.

### <a name="supported-versions"></a>Versões suportadas

-   NetBackup 7.7.x e versões posteriores
-   [StorSimple Update 3 e versões posteriores](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Porquê o StorSimple como alvo de reserva?

StorSimple é uma boa escolha para um alvo de reserva porque:

-   Fornece armazenamento local padrão para aplicações de backup para usar como um destino de backup rápido, sem alterações. Também pode utilizar o StorSimple para uma rápida restauração de cópias de segurança recentes.
-   O seu tiering em nuvem está perfeitamente integrado com uma conta de armazenamento em nuvem Azure para utilizar armazenamento Azure rentável.
-   Fornece automaticamente armazenamento fora do local para recuperação de desastres.

## <a name="key-concepts"></a>Conceitos-chave

Como em qualquer solução de armazenamento, uma avaliação cuidadosa do desempenho de armazenamento da solução, SLAs, taxa de variação e necessidades de crescimento da capacidade é fundamental para o sucesso. A principal ideia é que, introduzindo um nível de nuvem, os seus tempos de acesso e os seus acessos à nuvem desempenham um papel fundamental na capacidade da StorSimple de fazer o seu trabalho.

O StorSimple foi concebido para fornecer armazenamento a aplicações que operam num conjunto de dados de trabalho bem definido (dados quentes). Neste modelo, o conjunto de dados de trabalho é armazenado nos níveis locais, e o conjunto de dados restantes não funcionais/frios/arquivados é hierárquico para a nuvem. Este modelo está representado na figura seguinte. A linha verde quase plana representa os dados armazenados nos níveis locais do dispositivo StorSimple. A linha vermelha representa a quantidade total de dados armazenados na solução StorSimple em todos os níveis. O espaço entre a linha verde plana e a curva vermelha exponencial representa a quantidade total de dados armazenados na nuvem.

**Diagrama**
![de tiering StorSimple StorSimple](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)

Com esta arquitetura em mente, você vai descobrir que o StorSimple é ideal para funcionar como um alvo de reserva. Pode utilizar o StorSimple para:
-   Execute os seus restauros mais frequentes a partir do conjunto de dados de trabalho local.
-   Use a nuvem para recuperação de desastres fora do local e dados mais antigos, onde os restauros são menos frequentes.

## <a name="storsimple-benefits"></a>Benefícios StorSimple

A StorSimple fornece uma solução no local que está perfeitamente integrada com o Microsoft Azure, aproveitando o acesso perfeito às instalações e armazenamento na nuvem.

O StorSimple utiliza o tiering automático entre o dispositivo no local, que possui um dispositivo de estado sólido (SSD) e armazenamento SCSI (SAS) em série, e armazenamento azure. O tiering automático mantém frequentemente acesso a dados locais, nos níveis SSD e SAS. Move dados pouco frequentemente acedidos ao Armazenamento Azure.

A StorSimple oferece estes benefícios:

-   Algoritmos de desduplicação e compressão únicos que usam a nuvem para atingir níveis de duplicação sem precedentes
-   Elevada disponibilidade
-   Geo-replicação utilizando geo-replicação Azure
-   Integração do Azure
-   Encriptação de dados na nuvem
-   Melhor recuperação e conformidade de desastres

Embora o StorSimple apresente dois principais cenários de implementação (alvo principal de backup e alvo secundário de backup), fundamentalmente, é um dispositivo de armazenamento simples e bloqueado. StorSimple faz toda a compressão e desduplicação. Envia e recupera perfeitamente dados entre a nuvem e o sistema de aplicação e ficheiros.

Para mais informações sobre o StorSimple, consulte a [série StorSimple 8000: Solução híbrida](storsimple-overview.md)de armazenamento em nuvem . Além disso, pode rever as especificações técnicas da [série StorSimple 8000](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> A utilização de um dispositivo StorSimple como alvo de backup é suportada apenas para versões StorSimple 8000 Update 3 e versões posteriores.

## <a name="architecture-overview"></a>Descrição geral da arquitetura

As tabelas seguintes mostram a orientação inicial modelo-arquitetura do dispositivo.

**Capacidades StorSimple para armazenamento local e em nuvem**

| Capacidade de armazenamento       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| Capacidade de armazenamento local | &lt;10 Tib\*  | &lt;20 Tib\*  |
| Capacidade de armazenamento em nuvem | &gt;200 Tib\* | &gt;500 Tib\* |

\*O tamanho do armazenamento não pressupõe qualquer desduplicação ou compressão.

**Capacidades StorSimple para backups primários e secundários**

| Cenário de backup  | Capacidade de armazenamento local  | Capacidade de armazenamento em nuvem  |
|---|---|---|
| Backup primário  | Backups recentes armazenados no armazenamento local para rápida recuperação para cumprir objetivo do ponto de recuperação (RPO) | O histórico de backup (RPO) encaixa na capacidade da nuvem |
| Backup secundário | Cópia secundária de dados de backup pode ser armazenada na capacidade da nuvem  | N/D  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple como alvo de reserva primário

Neste cenário, os volumes StorSimple são apresentados à aplicação de backup como o único repositório para backups. A figura que se segue mostra uma arquitetura de solução na qual todos os backups utilizam volumes storSimple tiered para backups e restauros.

![StorSimple como um diagrama lógico alvo de backup primário](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Passos lógicos de backup alvo primário

1.  O servidor de reserva contacta o agente de reserva do alvo e o agente de reserva transmite dados para o servidor de reserva.
2.  O servidor de backup escreve dados para os volumes storSimple tiered.
3.  O servidor de cópia de segurança atualiza a base de dados do catálogo e, em seguida, termina o trabalho de backup.
4.  Um script instantâneo aciona o gestor de instantâneos StorSimple (iniciar ou eliminar).
5.  O servidor de cópia de segurança elimina cópias de segurança expiradas com base numa política de retenção.

### <a name="primary-target-restore-logical-steps"></a>Alvo primário restaurar passos lógicos

1.  O servidor de backup começa a restaurar os dados apropriados do repositório de armazenamento.
2.  O agente de reserva recebe os dados do servidor de reserva.
3.  O servidor de reserva termina o trabalho de restauro.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple como um alvo secundário de backup

Neste cenário, os volumes StorSimple são usados principalmente para retenção ou arquivamento a longo prazo.

A figura que se segue mostra uma arquitetura em que os backups iniciais e os restauros visam um volume de alto desempenho. Estas cópias de segurança são copiadas e arquivadas para um volume storSimple tiered em um horário definido.

É importante dimensionar o seu volume de alto desempenho para que possa lidar com a sua capacidade de retenção e requisitos de desempenho.

![StorSimple como um diagrama lógico de alvo de backup secundário](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Passos lógicos secundários de backup de alvo

1.  O servidor de reserva contacta o agente de reserva do alvo e o agente de reserva transmite dados para o servidor de reserva.
2.  O servidor de cópia de segurança escreve dados para armazenamento de alto desempenho.
3.  O servidor de cópia de segurança atualiza a base de dados do catálogo e, em seguida, termina o trabalho de backup.
4.  O servidor de cópias de cópias de cópias de cópias de segurança para storSimple com base numa política de retenção.
5.  Um script instantâneo aciona o gestor de instantâneos StorSimple (iniciar ou eliminar).
6.  O servidor de cópia de segurança elimina as cópias de segurança expiradas com base numa política de retenção.

### <a name="secondary-target-restore-logical-steps"></a>Alvo secundário restaurar etapas lógicas

1.  O servidor de backup começa a restaurar os dados apropriados do repositório de armazenamento.
2.  O agente de reserva recebe os dados do servidor de reserva.
3.  O servidor de reserva termina o trabalho de restauro.

## <a name="deploy-the-solution"></a>Implementar a solução

A implementação desta solução requer três passos:
1. Prepare a infraestrutura de rede.
2. Implemente o seu dispositivo StorSimple como alvo de reserva.
3. Implementar veritas NetBackup.

Cada passo é discutido em detalhe nas seguintes secções.

### <a name="set-up-the-network"></a>Configurar a rede

Como o StorSimple é uma solução integrada com a nuvem Azure, a StorSimple requer uma ligação ativa e de trabalho à nuvem Azure. Esta ligação é usada para operações como instantâneos em nuvem, gestão de dados e transferência de metadados, e para tiertier dados mais antigos e menos acedidos ao armazenamento em nuvem Azure.

Para que a solução tenha um desempenho omelhor, recomendamos que siga estas boas práticas de networking:

-   O link que liga o tiering StorSimple ao Azure deve satisfazer os seus requisitos de largura de banda. Para tal, aplique o nível adequado de Qualidade de Serviço (QoS) aos seus interruptores de infraestrutura para corresponder ao seu RPO e objetivo de tempo de recuperação (RTO) SLAs.

-   As lanostências máximas de acesso ao armazenamento azure blob devem rondar os 80 ms.

### <a name="deploy-storsimple"></a>Implementar StorSimple

Para obter orientação de implementação passo a passo StorSimple, consulte [Implementar o seu dispositivo StorSimple no local](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-netbackup"></a>Implementar O Backup Líquido

Para obter orientação de implementação de 7.7.x NetBackup passo a passo, consulte a [documentação NetBackup 7.7.x](http://www.veritas.com/docs/000094423).

## <a name="set-up-the-solution"></a>Configurar a solução

Nesta secção, demonstramos alguns exemplos de configuração. Os seguintes exemplos e recomendações ilustram a implementação mais básica e fundamental. Esta implementação pode não se aplicar diretamente aos seus requisitos específicos de backup.

### <a name="set-up-storsimple"></a>Configurar O StorSimple

| Tarefas de implantação StorSimple  | Comentários adicionais |
|---|---|
| Implemente o seu dispositivo StorSimple no local. | Versões suportadas: Atualização 3 e versões posteriores. |
| Ligue o alvo de reserva. | Utilize estes comandos para ligar ou desligar o modo de alvo de reserva e obter o estado. Para mais informações, consulte [Ligar remotamente a um dispositivo StorSimple](storsimple-remote-connect.md).</br> Para ligar o `Set-HCSBackupApplianceMode -enable`modo de backup: . </br> Para desligar o `Set-HCSBackupApplianceMode -disable`modo de reserva: . </br> Para obter o estado atual `Get-HCSBackupApplianceMode`das definições do modo de backup: . |
| Crie um recipiente de volume comum para o seu volume que armazene os dados de backup. Todos os dados num recipiente de volume são duplicados. | Os recipientes de volume StorSimple definem domínios de duplicação.  |
| Crie volumes StorSimple. | Crie volumes com tamanhos o mais próximo possível do uso previsto, porque o tamanho do volume afeta o tempo de duração do instantâneo da nuvem. Para obter informações sobre como dimensionar um volume, leia sobre as políticas de [retenção.](#retention-policies)</br> </br> Utilize volumes storSimple tiered e selecione o Use este volume para uma caixa de verificação de dados de **arquivo menos acedida** com menos frequência. </br> A utilização apenas de volumes fixados localmente não é suportada. |
| Crie uma política de backup storSimple única para todos os volumes de alvo de backup. | Uma política de backup StorSimple define o grupo de consistência do volume. |
| Desative o horário à medida que as fotos expirem. | As imagens são ativadas como uma operação pós-processamento. |

### <a name="set-up-the-host-backup-server-storage"></a>Configurar o armazenamento do servidor de backup do anfitrião

Configurar o armazenamento do servidor de backup do anfitrião de acordo com estas diretrizes:  

- Não utilize volumes atolado (criados pela Windows Disk Management); Os volumes aparatos não são suportados.
- Forforte os seus volumes utilizando NTFS com tamanho de alocação de 64 KB.
- Mapeie os volumes StorSimple diretamente para o servidor NetBackup.
    - Utilize o iSCSI para servidores físicos.
    - Utilize discos pass-through para servidores virtuais.


## <a name="best-practices-for-storsimple-and-netbackup"></a>Boas práticas para StorSimple e NetBackup

Configurar a sua solução de acordo com as diretrizes nas seguintes secções.

### <a name="operating-system-best-practices"></a>Boas práticas do sistema operativo

- Desative a encriptação e a desduplicação do Windows Server para o sistema de ficheiros NTFS.
- Desative a desfragmentação do Servidor do Windows nos volumes StorSimple.
- Desative a indexação do Servidor windows nos volumes StorSimple.
- Ecorra uma varredura antivírus no hospedeiro de origem (não contra os volumes StorSimple).
- Desligue a manutenção padrão do Windows Server no Gestor de [Tarefas.](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) Faça-o de uma das seguintes formas:
  - Desligue o configurador de manutenção no Programador de Tarefas do Windows.
  - Baixe [o PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) a partir do Windows Sysinternals. Depois de baixar o PsExec, execute o Windows PowerShell como administrador e escreva:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>StorSimple boas práticas

-   Certifique-se de que o dispositivo StorSimple está atualizado para [atualizar 3 ou mais tarde](storsimple-install-update-3.md).
-   Isolar o iSCSI e o tráfego na nuvem. Utilize ligações iSCSI dedicadas para o tráfego entre o StorSimple e o servidor de backup.
-   Certifique-se de que o seu dispositivo StorSimple é um alvo de backup dedicado. As cargas de trabalho mistas não são suportadas porque afetam o seu RTO e RPO.

### <a name="netbackup-best-practices"></a>Boas práticas da NetBackup

-   A base de dados NetBackup deve ser local para o servidor e não residir num volume StorSimple.
-   Para recuperação de desastres, volte a fazer o backup da base de dados NetBackup num volume StorSimple.
-   Apoiamos backups full e incremental NetBackup (também referidos como backups incrementais diferenciais no NetBackup) para esta solução. Recomendamos que não utilize cópias de segurança incrementais sintéticas e cumulativas.
-   Os ficheiros de dados de cópia de segurança devem conter apenas os dados para um trabalho específico. Por exemplo, não são permitidos apêndices mediáticos em diferentes postos de trabalho.

Para obter as mais recentes definições do NetBackup e as melhores práticas para implementar estes requisitos, consulte a documentação NetBackup em [www.veritas.com](https://www.veritas.com).


## <a name="retention-policies"></a>Políticas de retenção

Um dos tipos mais comuns de política de retenção de backup é uma política de avô, pai e filho (GFS). Numa política gfs, uma cópia de segurança incremental é realizada diariamente e os backups completos são feitos semanalmente e mensalmente. Esta política resulta em seis volumes storSimple tiered: um volume contém as cópias de segurança semanais, mensais e anuais completas; os outros cinco volumes armazenam cópias de segurança incrementais diárias.

No exemplo seguinte, usamos uma rotação GFS. O exemplo pressupõe o seguinte:

-   São utilizados dados não desenganados ou comprimidos.
-   Os reforços completos são 1 TiB cada.
-   As cópias de segurança incrementais diárias são de 500 GiB cada.
-   Quatro reforços semanais são guardados por um mês.
-   Doze reforços mensais são mantidos por um ano.
-   Um reforço anual é mantido por 10 anos.

Com base nos pressupostos anteriores, crie um volume de 26 TiB StorSimple tiered para as cópias de segurança mensais e anuais completas. Crie um volume de 5 TiB StorSimple tiered para cada uma das cópias de segurança diárias incrementais.

| Retenção de tipo de cópia de segurança | Tamanho (TiB) | Multiplicador GFS\* | Capacidade total (TiB)  |
|---|---|---|---|
| Semanalmente cheio | 1 | 4  | 4 |
| Incremental diário | 0,5 | 20 (ciclos iguais de semanas por mês) | 12 (2 para quota adicional) |
| Mensalmente cheio | 1 | 12 | 12 |
| Anualmente cheio | 1  | 10 | 10 |
| Requisito gfs |   | 38 |   |
| Quota adicional  | 4  |   | 42 requisitos totais de GFS  |

\*O multiplicador GFS é o número de cópias que precisa para proteger e reter para satisfazer os seus requisitos de política de backup.

## <a name="set-up-netbackup-storage"></a>Configurar o armazenamento NetBackup

### <a name="to-set-up-netbackup-storage"></a>Para configurar o armazenamento NetBackup

1.  Na Consola de Administração NetBackup, selecione **Media e Dispositivos** > de Gestão de**Dispositivos** > **De Discos Pools**. No Assistente de Configuração da Piscina de Discos, selecione o tipo de servidor de armazenamento **AdvancedDisk**, e, em seguida, selecione **Next**.

    ![Consola de administração NetBackup, assistente de configuração de piscina de disco](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

2.  Selecione o seu servidor e, em seguida, selecione **Next**.

    ![Consola de administração NetBackup, selecione o servidor](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

3.  Selecione o volume StorSimple.

    ![Consola de administração NetBackup, selecione o disco de volume StorSimple](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

4.  Introduza um nome para o alvo de reserva e, em seguida, selecione **Next** > **Next** para terminar o assistente.

5.  Reveja as definições e, em seguida, selecione **Terminar**.

6.  No final de cada atribuição de volume, altere as definições do dispositivo de armazenamento para corresponder às recomendadas nas [melhores práticas para storSimple e NetBackup](#best-practices-for-storsimple-and-netbackup).

7. Repita os passos 1-6 até terminar de atribuir os seus volumes StorSimple.

    ![Consola de administração NetBackup, configuração do disco](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Configurar o StorSimple como alvo principal de backup

> [!NOTE]
> Os dados restauram de uma cópia de segurança que foi nivelada para a nuvem ocorrem a velocidades de nuvem.

A figura seguinte mostra o mapeamento de um volume típico para um trabalho de backup. Neste caso, todos os backups semanais mapeiam para o disco completo de sábado, e o mapa de backups incrementais para discos incrementais de segunda a sexta-feira. Todas as cópias de segurança e restauros são de um volume storSimple tiered.

![Diagrama lógico de configuração do alvo de backup primário](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple como um alvo de backup primário GFS exemplo de programação

Aqui está um exemplo de um horário de rotação GFS para quatro semanas, mensalmente e anualmente:

| Tipo de frequência/cópia de segurança | Completa | Incremental (dias 1-5)  |   
|---|---|---|
| Semanalmente (semanas 1-4) | Saturday | Segunda a Sexta-feira |
| Mensalmente  | Saturday  |   |
| Anual | Saturday  |   |

## <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>Atribuir volumes StorSimple a um trabalho de backup NetBackup

A sequência seguinte pressupõe que o NetBackup e o anfitrião alvo estão configurados de acordo com as diretrizes do agente NetBackup.

### <a name="to-assign-storsimple-volumes-to-a-netbackup-backup-job"></a>Para atribuir volumes StorSimple a um trabalho de backup NetBackup

1. Na Consola de Administração NetBackup, selecione **NetBackup Management**, right-click **Policies**, e, em seguida, selecione **New Policy**.

   ![Consola de administração NetBackup, crie uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

2. Na caixa de diálogo **Adicionar uma Nova Política,** introduza um nome para a apólice e, em seguida, selecione a caixa de verificação do Assistente de Configuração da **Política de Utilização.** Selecione **OK**.

   ![Consola de administração NetBackup, Adicione uma nova caixa de diálogo de política](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

3. No Assistente de Configuração da Política de Backup, eleja o tipo de cópia de segurança que pretende e, em seguida, selecione **Next**.

   ![Consola de administração NetBackup, selecione o tipo de backup](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

4. Para definir o tipo de política, selecione **Standard**, e, em seguida, selecione **Next**.

   ![Consola de administração NetBackup, selecionar tipo de política](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

5. Selecione o seu anfitrião, selecione a caixa de verificação do **sistema operativo do cliente Detete** e, em seguida, selecione **Adicionar**. Selecione **Next**.

   ![Consola de administração NetBackup, lista risa clientes numa nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

6. Selecione as unidades que pretende fazer recuar.

   ![Consola de administração NetBackup, seleções de backup para uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

7. Selecione os valores de frequência e retenção que satisfaçam os requisitos de rotação de cópia de segurança.

   ![Consola de administração NetBackup, frequência de backup e rotação para uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

8. Selecione **Next** > **Próximo** > **Acabamento**.  Pode modificar o horário após a criação da política.

9. Selecione para expandir a política que acabou de criar e, em seguida, selecione **Horários**.

   ![Consola de administração NetBackup, horários para uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

10. Clique no **Diferencial-Inc,** selecione **Copiar para novo**, e, em seguida, selecione **OK**.

    ![Consola de administração NetBackup, calendário de cópias para uma nova política](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

11. Clique na programação recém-criada e, em seguida, selecione **Alterar**.

12. No separador **Atributos,** selecione a caixa de verificação de verificação de armazenamento de políticas de **substituição** e, em seguida, selecione o volume para onde vão as cópias de segurança incrementais de segunda-feira.

    ![Consola de administração NetBackup, alterar horário](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

13. No separador **Janela de Arranque,** selecione a janela de tempo para as suas cópias de segurança.

    ![Consola de administração NetBackup, janela de início de alteração](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

14. Selecione **OK**.

15. Repita os passos 10-14 para cada backup incremental. Selecione o volume e o horário adequados para cada cópia de segurança que criar.

16. Clique na programação **Diferencial-Inc** e, em seguida, elimine-o.

17. Modifique o seu horário completo para satisfazer as suas necessidades de backup.

    ![Consola de administração NetBackup, alterar horário completo](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

18. Mude a janela de partida.

    ![Consola de administração NetBackup, mude a janela de partida](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

19. O horário final é o seguinte:

    ![Consola de administração NetBackup, horário final](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Configurar o StorSimple como um alvo secundário de backup

> [!NOTE]
>Os dados restauram de uma cópia de segurança que foi nivelada para a nuvem ocorrem a velocidades de nuvem.

Neste modelo, deve ter um meio de armazenamento (que não seja o StorSimple) para servir de cache temporário. Por exemplo, pode utilizar um conjunto redundante de discos independentes (RAID) para acomodar espaço, entrada/saída (I/O) e largura de banda. Recomendamos a utilização de RAID 5, 50 e 10.

O número seguinte mostra volumes típicos de retenção a curto prazo locais (para o servidor) e volumes de arquivos de retenção a longo prazo. Neste cenário, todas as cópias de segurança são executadas no volume RAID local (para o servidor). Estas cópias de segurança são periodicamente duplicadas e arquivadas num volume de arquivos. É importante dimensionar o volume RAID local (para o servidor) para que possa lidar com a sua capacidade de retenção de curto prazo e requisitos de desempenho.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple como um exemplo de GFS alvo de backup secundário

![StorSimple como um diagrama lógico de alvo de backup secundário](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

A tabela seguinte mostra como configurar backups para executar nos discos locais e StorSimple. Inclui requisitos individuais e totais de capacidade.

### <a name="backup-configuration-and-capacity-requirements"></a>Requisitos de configuração e capacidade de backup

| Tipo de cópia de segurança e retenção | Armazenamento configurado | Tamanho (TiB) | Multiplicador GFS | Capacidade total\* (TiB) |
|---|---|---|---|---|
| Semana 1 (completa e incremental) |Disco local (a curto prazo)| 1 | 1 | 1 |
| StorSimple semanas 2-4 |Disco StorSimple (a longo prazo) | 1 | 4 | 4 |
| Mensalmente cheio |Disco StorSimple (a longo prazo) | 1 | 12 | 12 |
| Anualmente cheio |Disco StorSimple (a longo prazo) | 1 | 1 | 1 |
|Requisito de tamanho de volumes GFS |  |  |  | 18*|

\*A capacidade total inclui 17 discos TiB de StorSimple e 1 TiB do volume RAID local.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>Horário de exemplo do GFS: Rotação GFS semanal, mensal e mensal

| Week (Semana) | Completa | Dia incremental 1 | Dia incremental 2 | Dia incremental 3 | Dia incremental 4 | Dia incremental 5 |
|---|---|---|---|---|---|---|
| Semana 1 | Volume raid local  | Volume raid local | Volume raid local | Volume raid local | Volume raid local | Volume raid local |
| Semana 2 | StorSimple semanas 2-4 |   |   |   |   |   |
| Semana 3 | StorSimple semanas 2-4 |   |   |   |   |   |
| Semana 4 | StorSimple semanas 2-4 |   |   |   |   |   |
| Mensalmente | StorSimple mensalmente |   |   |   |   |   |
| Anual | StorSimple anualmente  |   |   |   |   |   |


## <a name="assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Atribuir volumes StorSimple a um arquivo NetBackup e trabalho de duplicação

Uma vez que o NetBackup oferece uma vasta gama de opções para armazenamento e gestão de meios de comunicação, recomendamos que consulte a Veritas ou o seu arquiteto NetBackup para avaliar adequadamente os seus requisitos de política de ciclo de vida de armazenamento (SLP).

Depois de definir as piscinas iniciais do disco, precisa definir três políticas adicionais de ciclo de vida de armazenamento, para um total de quatro políticas:
* Volume local RAID
* StorSimpleWeek2-4
* StorSimpleMonthlyFulls
* StorSimpleYearlyFulls

### <a name="to-assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Para atribuir volumes StorSimple a um arquivo NetBackup e trabalho de duplicação

1. Na Consola de Administração NetBackup, selecione **Storage Storage** > **Lifecycle Policies** > **New Storage Lifecycle Policy**.

   ![Consola de administração NetBackup, nova política de ciclo de vida de armazenamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

2. Introduza um nome para o instantâneo e, em seguida, selecione **Adicionar**.

3. Na caixa de diálogo **New Operation,** no separador **Propriedades,** para **operação,** selecione **Backup**. Selecione os valores pretendidos para o armazenamento de **Destino,** tipo de **retenção**e período de **retenção.** Selecione **OK**.

   ![Consola de administração NetBackup, nova caixa de diálogo de operação](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

   Isto define a primeira operação de backup e repositório.

4. Selecione para realçar o funcionamento anterior e, em seguida, **selecione Adicionar**. Na caixa de diálogo **Change Storage Operation,** selecione os valores que deseja para armazenamento de **Destino,** tipo de **retenção**e período de **retenção**.

   ![Consola de administração NetBackup,caixa de diálogo de operação de armazenamento de alteração](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

5. Selecione para realçar o funcionamento anterior e, em seguida, **selecione Adicionar**. Na caixa de diálogo **New Storage Lifecycle Policy,** adicione backups mensais durante um ano.

   ![Consola de administração NetBackup, nova caixa de diálogo de política de ciclo de armazenamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

6. Repita os passos 4-5 até criar a política abrangente de retenção de SLP de que precisa.

   ![Consola de administração NetBackup, adicione políticas na nova caixa de diálogo de política de ciclo de armazenamento](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

7. Quando terminar de definir a sua política de retenção SLP, no âmbito da **Política,** defina uma política de backup seguindo os passos detalhados na atribuição de [volumes StorSimple a um trabalho](#assigning-storsimple-volumes-to-a-netbackup-backup-job)de backup NetBackup .

8. Em **Horários**, na caixa de diálogo **'Alterar Agenda',** clique à direita **Full**, e depois selecione **Change**.

   ![Consola de administração NetBackup, caixa de diálogo de horário de mudança](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

9. Selecione a caixa de verificação de armazenamento de políticas de **substituição** e, em seguida, selecione a política de retenção SLP que criou nos passos 1-6.

   ![Consola de administração NetBackup, sobreposição da seleção de armazenamento de políticas](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

10. Selecione **OK**e, em seguida, repita para o calendário de backup incremental.

    ![Consola de administração NetBackup, caixa de diálogo de horário de mudança para backups incrementais](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)


| Retenção de tipo de cópia de segurança | Tamanho (TiB) | Multiplicador GFS\* | Capacidade total (TiB)  |
|---|---|---|---|
| Semanalmente cheio |  1  |  4 | 4  |
| Incremental diário  | 0,5  | 20 (os ciclos são iguais ao número de semanas por mês) | 12 (2 para quota adicional) |
| Mensalmente cheio  | 1 | 12 | 12 |
| Anualmente cheio | 1  | 10 | 10 |
| Requisito gfs  |     |     | 38 |
| Quota adicional  | 4  |    | 42 requisitos totais de GFS |

\*O multiplicador GFS é o número de cópias que precisa para proteger e reter para satisfazer os seus requisitos de política de backup.

## <a name="storsimple-cloud-snapshots"></a>Snapshots de nuvem StorSimple

As imagens de nuvem StorSimple protegem os dados que residem no seu dispositivo StorSimple. Criar um instantâneo em nuvem equivale a enviar fitas de reserva locais para uma instalação fora do local. Se utilizar o armazenamento georedundant do Azure, criar um instantâneo em nuvem equivale ao envio de fitas de backup para vários sites. Se precisar de restaurar um dispositivo após um desastre, poderá trazer outro dispositivo StorSimple on-line e fazer uma falha. Após o failover, você seria capaz de aceder aos dados (a velocidades de nuvem) a partir do mais recente instantâneo em nuvem.

A secção seguinte descreve como criar um script curto para iniciar e eliminar imagens de nuvem StorSimple durante o pós-processamento de cópias de segurança.

> [!NOTE]
> Os instantâneos que são criados manualmente ou programáticamente não seguem a política de expiração do instantâneo StorSimple. Estas imagens devem ser manual ou programáticamente eliminadas.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Inicie e elimine imagens de nuvem usando um script

> [!NOTE]
> Avalie cuidadosamente as repercussões de conformidade e retenção de dados antes de eliminar um instantâneo StorSimple. Para obter mais informações sobre como executar um script pós-backup, consulte a [documentação netBackup](http://www.veritas.com/docs/000094423).

### <a name="backup-lifecycle"></a>Ciclo de vida de reserva

![Diagrama de ciclo de vida de reserva](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

### <a name="requirements"></a>Requisitos

-   O servidor que executa o script deve ter acesso aos recursos da nuvem Azure.
-   A conta de utilizador deve ter as permissões necessárias.
-   Uma política de backup StorSimple com os volumes StorSimple associados deve ser configurada, mas não ligada.
-   Você precisará do nome de recursos StorSimple, chave de registo, nome do dispositivo e ID da política de backup.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Para iniciar ou apagar um instantâneo em nuvem

1. [Instale o Azure PowerShell](/powershell/azure/overview).
2. Descarregue e configurar o script [Manage-CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) PowerShell.
3. No servidor que executa o script, execute o PowerShell como administrador. Certifique-se de que `-WhatIf $true` executa o script para ver que mudanças o script fará. Uma vez concluída a `-WhatIf $false`validação, passe . Executar o comando abaixo:
   ```powershell
   .\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
   ```
4. Adicione o script ao seu trabalho de reserva no NetBackup. Para tal, edite os comandos de pré-processamento e pós-processamento das suas opções de trabalho NetBackup.

> [!NOTE]
> Recomendamos que execute a sua política de backup de imagens de nuvem StorSimple como um script pós-processamento no final do seu trabalho de backup diário. Para mais informações sobre como fazer backup e restaurar o seu ambiente de aplicação de backup para o ajudar a conhecer o seu RPO e RTO, consulte o seu arquiteto de reserva.

## <a name="storsimple-as-a-restore-source"></a>StorSimple como fonte de restauro

As restaurações de um dispositivo StorSimple funcionam como restauros de qualquer dispositivo de armazenamento de blocos. Os restauros de dados que são nivelados para a nuvem ocorrem a velocidades de nuvem. Para os dados locais, as restaurações ocorrem à velocidade do disco local do dispositivo. Para obter informações sobre como realizar um restauro, consulte a [documentação NetBackup](http://www.veritas.com/docs/000094423). Recomendamos que se conformar com o NetBackup restaure as melhores práticas.

## <a name="storsimple-failover-and-disaster-recovery"></a>Falha storSimple e recuperação de desastres

> [!NOTE]
> Para cenários de alvo de backup, o StorSimple Cloud Appliance não é suportado como um alvo de restauro.

Um desastre pode ser causado por uma variedade de fatores. A tabela que se segue enumera cenários comuns de recuperação de desastres.

| Cenário | Impacto | Como recuperar | Notas |
|---|---|---|---|
| Falha do dispositivo StorSimple | As operações de reforço e restauro são interrompidas. | Substitua o dispositivo falhado e execute [a falha do StorSimple e](storsimple-device-failover-disaster-recovery.md)a recuperação de desastres . | Se necessitar de efetuar um restauro após a recuperação do dispositivo, os conjuntos de trabalho completos são recuperados da nuvem para o novo dispositivo. Todas as operações estão a velocidades de nuvens. O processo de rescanning do índice e do catálogo pode fazer com que todos os conjuntos de backup sejam digitalizados e puxados do nível de nuvem para o nível de dispositivo local, o que pode ser um processo demorado. |
| Falha no servidor NetBackup | As operações de reforço e restauro são interrompidas. | Reconstruir o servidor de backup e executar a restauração da base de dados. | Deve reconstruir ou restaurar o servidor NetBackup no local de recuperação de desastres. Restaure a base de dados para o ponto mais recente. Se a base de dados NetBackup restaurada não estiver sincronizada com os seus últimos trabalhos de backup, é necessário indexar e catalogar. Este processo de rescanning de índice e catálogo pode fazer com que todos os conjuntos de backup sejam digitalizados e puxados do nível de nuvem para o nível de dispositivo local. Isto torna-o mais intensivo em tempo. |
| Falha no site que resulta na perda do servidor de backup e do StorSimple | As operações de reforço e restauro são interrompidas. | Restaure o StorSimple primeiro e, em seguida, restaure o NetBackup. | Restaure o StorSimple primeiro e, em seguida, restaure o NetBackup. Se necessitar de efetuar um restauro após a recuperação do dispositivo, os conjuntos de trabalho completos são recuperados da nuvem para o novo dispositivo. Todas as operações estão a velocidades de nuvens. |

## <a name="references"></a>Referências

Os seguintes documentos foram referenciados para este artigo:

- [Configuração storSimple multipata I/O](storsimple-configure-mpio-windows-server.md)
- [Cenários de armazenamento: Fornecimento fino](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Usando unidades GPT](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Configurar cópias-sombra para pastas partilhadas](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como [restaurar a partir de um conjunto](storsimple-restore-from-backup-set-u2.md)de backup .
- Saiba mais sobre como executar o [failover do dispositivo e a recuperação de desastres.](storsimple-device-failover-disaster-recovery.md)
