---
title: Série StorSimple 8000 como alvo de reserva com Veeam Microsoft Docs
description: Descreve a configuração do alvo de backup StorSimple com Veeam.
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67875307"
---
# <a name="storsimple-as-a-backup-target-with-veeam"></a>StorSimple como alvo de reserva com Veeam

## <a name="overview"></a>Descrição geral

O Azure StorSimple é uma solução híbrida de armazenamento em nuvem da Microsoft. A StorSimple aborda as complexidades do crescimento exponencial de dados utilizando uma conta de Armazenamento Azure como uma extensão da solução no local e igualando automaticamente os dados através do armazenamento no local e armazenamento em nuvem.

Neste artigo, discutimos a integração storSimple com veeam, e as melhores práticas para integrar ambas as soluções. Também fazemos recomendações sobre como configurar a Veeam para melhor integrar com o StorSimple. Adiamos para as melhores práticas de Veeam, arquitetos de reserva e administradores para a melhor maneira de criar Veeam para satisfazer requisitos individuais de backup e acordos de nível de serviço (SLAs).

Embora ilustremos passos de configuração e conceitos-chave, este artigo não é de forma alguma uma configuração passo a passo ou um guia de instalação. Assumimos que os componentes básicos e as infraestruturas estão em ordem e prontos para apoiar os conceitos que descrevemos.

### <a name="who-should-read-this"></a>Quem deve ler isto?

As informações neste artigo serão mais úteis para administradores de backup, administradores de armazenamento e arquitetos de armazenamento que tenham conhecimento de armazenamento, Windows Server 2012 R2, Ethernet, serviços na nuvem e Veeam.

### <a name="supported-versions"></a>Versões suportadas

-   Veeam 9 e versões posteriores
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
![de tiering StorSimple StorSimple](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

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

| Capacidade de armazenamento | 8100 | 8600 |
|---|---|---|
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

![StorSimple como um diagrama lógico alvo de backup primário](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Passos lógicos de backup alvo primário

1.  O servidor de reserva contacta o agente de reserva do alvo e o agente de reserva transmite dados para o servidor de reserva.
2.  O servidor de backup escreve dados para os volumes storSimple tiered.
3.  O servidor de cópia de segurança atualiza a base de dados do catálogo e, em seguida, termina o trabalho de backup.
4.  Um script instantâneo aciona o gestor de instantâneos de nuvem StorSimple (iniciar ou eliminar).
5.  O servidor de cópia de segurança elimina cópias de segurança expiradas com base numa política de retenção.

### <a name="primary-target-restore-logical-steps"></a>Alvo primário restaurar passos lógicos

1.  O servidor de backup começa a restaurar os dados apropriados do repositório de armazenamento.
2.  O agente de reserva recebe os dados do servidor de reserva.
3.  O servidor de reserva termina o trabalho de restauro.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple como um alvo secundário de backup

Neste cenário, os volumes StorSimple são usados principalmente para retenção ou arquivamento a longo prazo.

A figura que se segue mostra uma arquitetura em que os backups iniciais e os restauros visam um volume de alto desempenho. Estas cópias de segurança são copiadas e arquivadas para um volume storSimple tiered em um horário definido.

É importante dimensionar o seu volume de alto desempenho para que possa lidar com a sua capacidade de retenção e requisitos de desempenho.

![StorSimple como um diagrama lógico de alvo de backup secundário](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Passos lógicos secundários de backup de alvo

1.  O servidor de reserva contacta o agente de reserva do alvo e o agente de reserva transmite dados para o servidor de reserva.
2.  O servidor de cópia de segurança escreve dados para armazenamento de alto desempenho.
3.  O servidor de cópia de segurança atualiza a base de dados do catálogo e, em seguida, termina o trabalho de backup.
4.  O servidor de cópias de cópias de cópias de cópias de segurança para storSimple com base numa política de retenção.
5.  Um script instantâneo aciona o gestor de instantâneos de nuvem StorSimple (iniciar ou eliminar).
6.  O servidor de cópia de segurança elimina cópias de segurança expiradas com base numa política de retenção.

### <a name="secondary-target-restore-logical-steps"></a>Alvo secundário restaurar etapas lógicas

1.  O servidor de backup começa a restaurar os dados apropriados do repositório de armazenamento.
2.  O agente de reserva recebe os dados do servidor de reserva.
3.  O servidor de reserva termina o trabalho de restauro.

## <a name="deploy-the-solution"></a>Implementar a solução

A implementação da solução requer três passos:

1. Prepare a infraestrutura de rede.
2. Implemente o seu dispositivo StorSimple como alvo de reserva.
3. Implante Veeam.

Cada passo é discutido em detalhe nas seguintes secções.

### <a name="set-up-the-network"></a>Configurar a rede

Como o StorSimple é uma solução que está integrada com a nuvem Azure, a StorSimple requer uma ligação ativa e de trabalho à nuvem Azure. Esta ligação é usada para operações como instantâneos em nuvem, gestão de dados e transferência de metadados, e para tiertier dados mais antigos e menos acedidos ao armazenamento em nuvem Azure.

Para que a solução tenha um desempenho omelhor, recomendamos que siga estas boas práticas de networking:

-   O link que liga o seu tiering StorSimple ao Azure deve satisfazer os seus requisitos de largura de banda. Obtenha-o aplicando o nível de Qualidade de Serviço (QoS) necessário aos seus interruptores de infraestrutura para corresponder ao seu RPO e objetivo de tempo de recuperação (RTO) SLAs.
-   As lanostências máximas de acesso ao armazenamento azure blob devem rondar os 80 ms.

### <a name="deploy-storsimple"></a>Implementar StorSimple

Para obter orientação de implementação passo a passo StorSimple, consulte [Implementar o seu dispositivo StorSimple no local](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-veeam"></a>Implementar Veeam

Para as melhores práticas de instalação veeam, consulte [veeam Backup & Replication Best Practices](https://bp.veeam.expert/), e leia o guia do utilizador no [Veeam Help Center (Documentação Técnica)](https://www.veeam.com/documentation-guides-datasheets.html).

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

- Não utilize volumes aparatos (criados pela Windows Disk Management). Os volumes aparatos não são suportados.
- Forforte os seus volumes utilizando ntfs com o tamanho da unidade de alocação de 64 KB.
- Mapeie os volumes StorSimple diretamente para o servidor Veeam.
    - Utilize o iSCSI para servidores físicos.


## <a name="best-practices-for-storsimple-and-veeam"></a>Boas práticas para StorSimple e Veeam

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

### <a name="veeam-best-practices"></a>Boas práticas veeam

-   A base de dados Veeam deve ser local para o servidor e não residir num volume StorSimple.
-   Para recuperação de desastres, volte a ser a base de dados veeam num volume StorSimple.
-   Apoiamos as cópias de segurança completas e incrementais da Veeam para esta solução. Recomendamos que não utilize backups sintéticos e diferenciais.
-   Os ficheiros de dados de cópia de segurança devem conter apenas os dados para um trabalho específico. Por exemplo, não são permitidos apêndices mediáticos em diferentes postos de trabalho.
-   Desligue a verificação de emprego. Se necessário, a verificação deve ser marcada após o último trabalho de backup. É importante entender que este trabalho afeta a sua janela de reserva.
-   Ligue a pré-atribuição dos meios de comunicação.
-   Certifique-se de que o processamento paralelo está ligado.
-   Desligue a compressão.
-   Desligue a duplicação do trabalho de reserva.
-   Definir otimização para **o alvo LAN**.
-   Ligue **Criar cópia de segurança ativa** (a cada 2 semanas).
-   No repositório de reserva, instale **ficheiros de backup Use per-VM**.
-   Set **Utilize vários fluxos de upload por trabalho** para **8** (é permitido um máximo de 16). Ajuste este número para cima ou para baixo com base na utilização do CPU no dispositivo StorSimple.

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

## <a name="set-up-veeam-storage"></a>Configurar o armazenamento Veeam

### <a name="to-set-up-veeam-storage"></a>Para configurar o armazenamento veeam

1.  Na consola Veeam Backup and Replication, em **Ferramentas de Repositório,** vá para **a Infraestrutura**de Backup . Clique no **repositório**de backup de reserva e, em seguida, selecione **Adicionar Repositório**de Backup .

    ![Consola de gestão Veeam, página de repositório de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

2.  Na caixa de diálogo de **repositório de backup new backup,** introduza um nome e descrição para o repositório. Selecione **Seguinte**.

    ![Consola de gestão Veeam, nome e página de descrição](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

3.  Para este tipo, selecione **o servidor Microsoft Windows**. Selecione o servidor Veeam. Selecione **Seguinte**.

    ![Consola de gestão Veeam, selecione tipo de repositório de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

4.  Para especificar **Localização,** navegue e selecione o volume. Selecione as **tarefas limite máximas simultâneas para:** verificar caixa e definir o valor para **4**. Isto garante que apenas quatro discos virtuais estão a ser processados simultaneamente enquanto cada máquina virtual (VM) é processada. Selecione o botão **Avançado.**

    ![Consola de gestão Veeam, selecione volume](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


5.  Na caixa de diálogo Definições de Compatibilidade de **Armazenamento,** selecione a caixa de verificação de ficheiros de **verificação de ficheiros de reserva Use per-VM.**

    ![Consola de gestão Veeam, definições de compatibilidade de armazenamento](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

6.  Na nova caixa de diálogo de **repositório** de backup, selecione o **serviço Enable vPower NFS na** caixa de verificação do servidor de montagem (recomendado). Selecione **Seguinte**.

    ![Consola de gestão Veeam, página de repositório de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

7.  Reveja as definições e, em seguida, selecione **Next**.

    ![Consola de gestão Veeam, página de repositório de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    Um repositório é adicionado ao servidor Veeam.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Configurar o StorSimple como alvo principal de backup

> [!IMPORTANT]
> A restauração de dados a partir de uma cópia de segurança que foi nivelada para a nuvem ocorre a velocidades de nuvem.

A figura seguinte mostra o mapeamento de um volume típico para um trabalho de backup. Neste caso, todos os backups semanais mapeiam para o disco completo de sábado, e o mapa de backups incrementais para discos incrementais de segunda a sexta-feira. Todas as cópias de segurança e restauros são de um volume storSimple tiered.

![Diagrama lógico de configuração do alvo de backup primário](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple como um alvo de backup primário GFS exemplo de programação

Aqui está um exemplo de um horário de rotação GFS para quatro semanas, mensalmente e anualmente:

| Tipo de frequência/cópia de segurança | Completa | Incremental (dias 1-5)  |   
|---|---|---|
| Semanalmente (semanas 1-4) | Saturday | Segunda a Sexta-feira |
| Mensalmente  | Saturday  |   |
| Anual | Saturday  |   |


### <a name="assign-storsimple-volumes-to-a-veeam-backup-job"></a>Atribuir volumes StorSimple a um trabalho de backup Veeam

Para o cenário principal de alvo de backup, crie um trabalho diário com o seu volume veeam StorSimple primário. Para um cenário de alvo secundário de backup, crie um trabalho diário utilizando armazenamento de anexos diretos (DAS), Armazenamento Ligado à Rede (NAS) ou apenas um monte de discos (JBOD).

#### <a name="to-assign-storsimple-volumes-to-a-veeam-backup-job"></a>Para atribuir volumes StorSimple a um trabalho de backup Veeam

1.  Na consola Veeam Backup and Replication, selecione **Backup & Replication**. Clique na cópia de **segurança**, e depois selecione **VMware** ou **Hyper-V,** dependendo do seu ambiente.

    ![Consola de gestão Veeam, novo trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

2.  Na caixa de diálogo **New Backup Job,** introduza um nome e descrição para o trabalho de backup diário.

    ![Consola de gestão Veeam, nova página de trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

3.  Selecione uma máquina virtual para fazer um back-up.

    ![Consola de gestão Veeam, nova página de trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

4.  Selecione os valores que deseja para **proxy de reserva** e **repositório**de backup . Selecione um valor para **pontos de Restauro para manter no disco** de acordo com as definições de RPO e RTO para o seu ambiente no armazenamento local. Selecione **Advanced**.

    ![Consola de gestão Veeam, nova página de trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

5. Na caixa de diálogo **Definições Avançadas,** no separador **'Backup',** selecione **Incremental**. Certifique-se de que as cópias de **segurança completas sintéticas Criar periodicamente** verificam a caixa. Selecione as **cópias de segurança completas ativas periodicamente,** verifique a caixa. Em **backup completo Ative,** selecione o **Semanário em dias selecionados** caixa de verificação para sábado.

    ![Consola de gestão Veeam, nova página de definições avançadas de trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

6. No **separador Armazenamento,** certifique-se de que a caixa de verificação de **deduplicação** de dados de linha ativa está limpa. Selecione os blocos de verificação de **ficheiros de troca de exclusão** e selecione a caixa de verificação de blocos de **ficheiros excluída.** Definir **o nível de compressão** para **nenhum**. Para um desempenho equilibrado e desduplicação, delineie **a otimização do armazenamento** para o alvo **LAN**. Selecione **OK**.

    ![Consola de gestão Veeam, nova página de definições avançadas de trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    Para obter informações sobre as definições de desduplicação e compressão de Veeam, consulte a [Compressão de Dados e a Deduplicação](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html).

7.  Na caixa de diálogo **'Backup Job',** pode selecionar a caixa de verificação **de processamento consciente da aplicação Ativa** (opcional).

    ![Consola de gestão Veeam, nova página de processamento de convidados de trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

8.  Detete a programação para ser executada uma vez por dia, de uma hora que possa especificar.

    ![Consola de gestão Veeam, nova página de horário de trabalho de backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Configurar o StorSimple como um alvo secundário de backup

> [!NOTE]
> Os dados restauram de uma cópia de segurança que foi nivelada para a nuvem ocorrem a velocidades de nuvem.

Neste modelo, deve ter um meio de armazenamento (que não seja o StorSimple) para servir de cache temporário. Por exemplo, pode utilizar um conjunto redundante de discos independentes (RAID) para acomodar espaço, entrada/saída (I/O) e largura de banda. Recomendamos a utilização de RAID 5, 50 e 10.

O número seguinte mostra volumes locais típicos de retenção a curto prazo (para o servidor) e volumes de arquivo de retenção a longo prazo. Neste cenário, todas as cópias de segurança são executadas no volume RAID local (para o servidor). Estas cópias de segurança são periodicamente duplicadas e arquivadas num volume de arquivo. É importante dimensionar o volume RAID local (para o servidor) para que possa lidar com a sua capacidade de retenção de curto prazo e requisitos de desempenho.

![StorSimple como diagrama lógico de alvo de backup secundário](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple como um exemplo de GFS alvo de backup secundário

A tabela seguinte mostra como configurar backups para executar nos discos locais e StorSimple. Inclui requisitos individuais e totais de capacidade.

| Tipo de cópia de segurança e retenção | Armazenamento configurado | Tamanho (TiB) | Multiplicador GFS | Capacidade total\* (TiB) |
|---|---|---|---|---|
| Semana 1 (completa e incremental) |Disco local (a curto prazo)| 1 | 1 | 1 |
| StorSimple semanas 2-4 |Disco StorSimple (a longo prazo) | 1 | 4 | 4 |
| Mensalmente cheio |Disco StorSimple (a longo prazo) | 1 | 12 | 12 |
| Anualmente cheio |Disco StorSimple (a longo prazo) | 1 | 1 | 1 |
|Requisito de tamanho de volumes GFS |  |  |  | 18*|

\*A capacidade total inclui 17 discos TiB de StorSimple e 1 TiB do volume RAID local.


### <a name="gfs-example-schedule"></a>Horário de exemplo gfs

Horário semanal, mensal e anual de rotação GFS

| Week (Semana) | Completa | Dia incremental 1 | Dia incremental 2 | Dia incremental 3 | Dia incremental 4 | Dia incremental 5 |
|---|---|---|---|---|---|---|
| Semana 1 | Volume raid local  | Volume raid local | Volume raid local | Volume raid local | Volume raid local | Volume raid local |
| Semana 2 | StorSimple semanas 2-4 |   |   |   |   |   |
| Semana 3 | StorSimple semanas 2-4 |   |   |   |   |   |
| Semana 4 | StorSimple semanas 2-4 |   |   |   |   |   |
| Mensalmente | StorSimple mensalmente |   |   |   |   |   |
| Anual | StorSimple anualmente  |   |   |   |   |   |

### <a name="assign-storsimple-volumes-to-a-veeam-copy-job"></a>Atribuir volumes StorSimple a um trabalho de cópia Veeam

#### <a name="to-assign-storsimple-volumes-to-a-veeam-copy-job"></a>Para atribuir volumes StorSimple a um trabalho de cópia Veeam

1.  Na consola Veeam Backup and Replication, selecione **Backup & Replication**. Clique na cópia de **segurança**, e depois selecione **VMware** ou **Hyper-V,** dependendo do seu ambiente.

    ![Consola de gestão Veeam, nova página de trabalho de cópia de cópia de cópia de cópia de cópia de cópia](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

2.  Na caixa de diálogo **New Backup Copy Job,** introduza um nome e descrição para o trabalho.

    ![Consola de gestão Veeam, nova página de trabalho de cópia de cópia de cópia de cópia de cópia de cópia](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

3.  Selecione os VMs que pretende processar. Selecione a partir de backups e, em seguida, selecione a cópia de segurança diária que criou anteriormente.

    ![Consola de gestão Veeam, nova página de trabalho de cópia de cópia de cópia de cópia de cópia de cópia](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

4.  Excluir objetos do trabalho de cópia de cópia de reserva, se necessário.

5.  Selecione o seu repositório de backup e detetete um valor para **os pontos restaurar em manter**. Certifique-se de que seleciona **os seguintes pontos** de restauro para fins de arquivo, verifique a caixa. Defina a frequência de cópia de segurança e, em seguida, **selecione Advanced**.

    ![Consola de gestão Veeam, nova página de trabalho de cópia de cópia de cópia de cópia de cópia de cópia](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

6.  Especifique as seguintes definições avançadas:

    * No separador **Manutenção,** desligue o sistema de corrupção do nível de armazenamento.

    ![Consola de gestão Veeam, nova página de definições avançadas de cópia de cópia de cópia de cópia de cópia de cópia de cópia](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    * No **separador Armazenamento,** certifique-se de que a duplicação e a compressão estão desligadas.

    ![Consola de gestão Veeam, nova página de definições avançadas de cópia de cópia de cópia de cópia de cópia de cópia de cópia](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

7.  Especifique que a transferência de dados é direta.

8.  Defina o calendário da janela de cópia de cópia de cópia de reserva de acordo com as suas necessidades e, em seguida, termine o assistente.

Para mais informações, consulte [Criar trabalhos](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html)de cópia de cópia de backup .

## <a name="storsimple-cloud-snapshots"></a>Snapshots de nuvem StorSimple

As imagens de nuvem StorSimple protegem os dados que residem no seu dispositivo StorSimple. Criar um instantâneo em nuvem equivale a enviar fitas de reserva locais para uma instalação fora do local. Se utilizar o armazenamento georedundant do Azure, criar um instantâneo em nuvem equivale ao envio de fitas de backup para vários sites. Se precisar de restaurar um dispositivo após um desastre, poderá trazer outro dispositivo StorSimple on-line e fazer uma falha. Após o failover, você seria capaz de aceder aos dados (a velocidades de nuvem) a partir do mais recente instantâneo em nuvem.

A secção seguinte descreve como criar um script curto para iniciar e eliminar imagens de nuvem StorSimple durante o pós-processamento de cópias de segurança.

> [!NOTE]
> Os instantâneos que são criados manualmente ou programáticamente não seguem a política de expiração do instantâneo StorSimple. Estas imagens devem ser manual ou programáticamente eliminadas.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Inicie e elimine imagens de nuvem usando um script

> [!NOTE]
> Avalie cuidadosamente as repercussões de conformidade e retenção de dados antes de eliminar um instantâneo StorSimple. Para obter mais informações sobre como executar um script pós-backup, consulte a documentação veeam.


### <a name="backup-lifecycle"></a>Ciclo de vida de reserva

![Diagrama de ciclo de vida de reserva](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

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
4. Para adicionar o script ao seu trabalho de backup, edite as suas opções avançadas de trabalho Veeam.

    ![Separador de configurações avançadas de backup Veeam](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

Recomendamos que execute a sua política de backup de imagens de nuvem StorSimple como um script pós-processamento no final do seu trabalho de backup diário. Para mais informações sobre como fazer backup e restaurar o seu ambiente de aplicação de backup para o ajudar a conhecer o seu RPO e RTO, consulte o seu arquiteto de reserva.

## <a name="storsimple-as-a-restore-source"></a>StorSimple como fonte de restauro

As restaurações de um dispositivo StorSimple funcionam como restauros de qualquer dispositivo de armazenamento de blocos. Os restauros de dados que são nivelados para a nuvem ocorrem a velocidades de nuvem. Para os dados locais, as restaurações ocorrem à velocidade do disco local do dispositivo.

Com veeam, obtém-se uma rápida recuperação, granular, de nível de ficheiro através do StorSimple através das vistas do explorador incorporado na consola Veeam. Utilize os Veeam Explorers para recuperar itens individuais, como mensagens de correio eletrónico, objetos de Diretório Ativo e itens SharePoint de backups. A recuperação pode ser feita sem perturbação vm no local. Também pode fazer uma recuperação pontual para bases de dados Azure SQL e Oracle. Veeam e StorSimple tornam o processo de recuperação ao nível do item do Azure rápido e fácil. Para obter informações sobre como realizar um restauro, consulte a documentação veeam:

- Para [servidor de intercâmbio](https://www.veeam.com/microsoft-exchange-recovery.html)
- Para [Diretório Ativo](https://www.veeam.com/microsoft-active-directory-explorer.html)
- Para [o Servidor SQL](https://www.veeam.com/microsoft-sql-server-explorer.html)
- Para [SharePoint](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)
- Para [o oráculo](https://www.veeam.com/oracle-backup-recovery-explorer.html)


## <a name="storsimple-failover-and-disaster-recovery"></a>Falha storSimple e recuperação de desastres

> [!NOTE]
> Para cenários de alvo de backup, o StorSimple Cloud Appliance não é suportado como um alvo de restauro.

Um desastre pode ser causado por uma variedade de fatores. A tabela que se segue enumera cenários comuns de recuperação de desastres.

| Cenário | Impacto | Como recuperar | Notas |
|---|---|---|---|
| Falha do dispositivo StorSimple | As operações de reforço e restauro são interrompidas. | Substitua o dispositivo falhado e execute [a falha do StorSimple e](storsimple-device-failover-disaster-recovery.md)a recuperação de desastres . | Se necessitar de efetuar um restauro após a recuperação do dispositivo, os conjuntos de trabalho completos são recuperados da nuvem para o novo dispositivo. Todas as operações estão a velocidades de nuvens. O processo de rescanning do índice e do catálogo pode fazer com que todos os conjuntos de backup sejam digitalizados e puxados do nível de nuvem para o nível de dispositivo local, o que pode ser um processo demorado. |
| Falha no servidor Veeam | As operações de reforço e restauro são interrompidas. | Reconstruir o servidor de backup e executar a restauração da base de dados conforme detalhado no Centro de [Ajuda Veeam (Documentação Técnica)](https://www.veeam.com/documentation-guides-datasheets.html).  | Deve reconstruir ou restaurar o servidor Veeam no local de recuperação de desastres. Restaure a base de dados para o ponto mais recente. Se a base de dados Veeam restaurada não estiver sincronizada com os seus últimos trabalhos de backup, é necessário indexar e catalogar. Este processo de rescanning de índice e catálogo pode fazer com que todos os conjuntos de backup sejam digitalizados e puxados do nível de nuvem para o nível de dispositivo local. Isto torna-o mais intensivo em tempo. |
| Falha no site que resulta na perda do servidor de backup e do StorSimple | As operações de reforço e restauro são interrompidas. | Restaure storSimple primeiro e, em seguida, restaurar Veeam. | Restaure storSimple primeiro e, em seguida, restaurar Veeam. Se necessitar de efetuar um restauro após a recuperação do dispositivo, os conjuntos de trabalho completos são recuperados da nuvem para o novo dispositivo. Todas as operações estão a velocidades de nuvens. |


## <a name="references"></a>Referências

Os seguintes documentos foram referenciados para este artigo:

- [Configuração storSimple multipata I/O](storsimple-configure-mpio-windows-server.md)
- [Cenários de armazenamento: Fornecimento fino](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Usando unidades GPT](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Configurar cópias-sombra para pastas partilhadas](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como [restaurar a partir de um conjunto](storsimple-restore-from-backup-set-u2.md)de backup .
- Saiba mais sobre como executar o [failover do dispositivo e a recuperação de desastres.](storsimple-device-failover-disaster-recovery.md)
