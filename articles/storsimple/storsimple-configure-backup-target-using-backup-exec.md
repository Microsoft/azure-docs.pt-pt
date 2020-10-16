---
title: StorSimple série 8000 como alvo de backup com Backup Exec Microsoft Docs
description: Descreve a configuração do alvo de backup StorSimple com o Veritas Backup Exec.
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
ms.date: 12/05/2016
ms.author: matd
ms.openlocfilehash: a35b4e398757cb3d4b17e4fd6a5e342fe3c82918
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710384"
---
# <a name="storsimple-as-a-backup-target-with-backup-exec"></a>StorSimple como alvo de backup com Backup Exec

## <a name="overview"></a>Descrição geral

Azure StorSimple é uma solução híbrida de armazenamento em nuvem da Microsoft. A StorSimple aborda as complexidades do crescimento exponencial dos dados utilizando uma conta de armazenamento Azure como uma extensão da solução no local, e ligando automaticamente os dados através do armazenamento no local e do armazenamento em nuvem.

Neste artigo, discutimos a integração StorSimple com o Veritas Backup Exec e as melhores práticas para integrar ambas as soluções. Também fazemos recomendações sobre como configurar o Backup Exec para melhor integrar com o StorSimple. Adiamos as melhores práticas da Veritas, arquitetos de backup e administradores para a melhor forma de criar o Backup Exec para cumprir os requisitos individuais de backup e acordos de nível de serviço (SLAs).

Embora ilustremos passos de configuração e conceitos-chave, este artigo não é de forma alguma uma configuração passo a passo ou guia de instalação. Assumimos que os componentes básicos e as infraestruturas estão em ordem e prontos a apoiar os conceitos que descrevemos.

### <a name="who-should-read-this"></a>Quem deveria ler isto?

As informações neste artigo serão mais úteis para administradores de backup, administradores de armazenamento e arquitetos de armazenamento que tenham conhecimento de armazenamento, Windows Server 2012 R2, Ethernet, serviços na nuvem e Backup Exec.

## <a name="supported-versions"></a>Versões suportadas

-   [Backup Exec 16 e versões posteriores](https://www.veritas.com/content/support/en_US/article.100040087)
-   [StorSimple Update 3 e versões posteriores](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Por que StorSimple como um alvo de reserva?

StorSimple é uma boa escolha para um alvo de backup porque:

-   Fornece armazenamento local padrão para aplicações de backup para usar como um destino de backup rápido, sem quaisquer alterações. Também pode utilizar o StorSimple para uma rápida restauração de cópias de segurança recentes.
-   O seu tiering em nuvem está perfeitamente integrado com uma conta de armazenamento em nuvem Azure para utilizar o armazenamento Azure rentável.
-   Fornece automaticamente armazenamento fora do local para recuperação de desastres.

## <a name="key-concepts"></a>Conceitos-chave

Como em qualquer solução de armazenamento, uma avaliação cuidadosa do desempenho de armazenamento da solução, SLAs, taxa de mudança e necessidades de crescimento da capacidade é fundamental para o sucesso. A ideia principal é que ao introduzir um nível de nuvem, os seus tempos de acesso e posições para a nuvem desempenham um papel fundamental na capacidade de StorSimple fazer o seu trabalho.

O StorSimple foi concebido para fornecer armazenamento a aplicações que operam num conjunto de dados de trabalho bem definido (dados quentes). Neste modelo, o conjunto de dados de trabalho é armazenado nos níveis locais, e o restante conjunto de dados não funcionando/frio/arquivado é hierárquico para a nuvem. Este modelo está representado na seguinte figura. A linha verde quase plana representa os dados armazenados nos níveis locais do dispositivo StorSimple. A linha vermelha representa a quantidade total de dados armazenados na solução StorSimple em todos os níveis. O espaço entre a linha verde plana e a curva vermelha exponencial representa a quantidade total de dados armazenados na nuvem.

Tiering StorSimple **StorSimple tiering** 
 ![ Diagrama de tiering StorSimple](./media/storsimple-configure-backup-target-using-backup-exec/image1.jpg)

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

Para obter mais informações sobre o StorSimple, consulte [a série StorSimple 8000: Solução híbrida de armazenamento em nuvem](storsimple-overview.md). Além disso, pode rever as especificações técnicas da [série StorSimple 8000](storsimple-technical-specifications-and-compliance.md).

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

![StorSimple como um diagrama lógico alvo de backup primário](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Passos lógicos de backup do alvo primário

1.  O servidor de backup contacta o agente de backup do alvo e o agente de backup transmite dados para o servidor de backup.
2.  O servidor de backup escreve dados para os volumes tiered StorSimple.
3.  O servidor de backup atualiza a base de dados do catálogo e, em seguida, termina o trabalho de backup.
4.  Um script instantâneo ativa o gestor de instantâneos de nuvem StorSimple (iniciar ou excluir).
5.  O servidor de backup elimina cópias de segurança expiradas com base numa política de retenção.


### <a name="primary-target-restore-logical-steps"></a>Alvo primário restaurar passos lógicos

1.  O servidor de backup começa a restaurar os dados apropriados do repositório de armazenamento.
2.  O agente de backup recebe os dados do servidor de reserva.
3.  O servidor de reserva termina o trabalho de restauro.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple como alvo de backup secundário

Neste cenário, os volumes StorSimple são usados principalmente para retenção ou arquivamento a longo prazo.

A figura a seguir mostra uma arquitetura em que os backups iniciais e os restauros visam um volume de alto desempenho. Estas cópias de segurança são copiadas e arquivadas num volume de nível StorSimple numa programação definida.

É importante dimensionar o seu volume de alto desempenho para que possa lidar com a capacidade da sua política de retenção e os requisitos de desempenho.

![StorSimple como um diagrama lógico de backup secundário](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Passos lógicos de backup de alvo secundário

1.  O servidor de backup contacta o agente de backup do alvo e o agente de backup transmite dados para o servidor de backup.
2.  O servidor de backup escreve dados para armazenamento de alto desempenho.
3.  O servidor de backup atualiza a base de dados do catálogo e, em seguida, termina o trabalho de backup.
4.  O servidor de cópias de segurança copia cópias de segurança para storSimple com base numa política de retenção.
5.  Um script instantâneo ativa o gestor de instantâneos de nuvem StorSimple (iniciar ou excluir).
6.  O servidor de backup elimina cópias de segurança expiradas com base numa política de retenção.

### <a name="secondary-target-restore-logical-steps"></a>Alvo secundário restaurar passos lógicos

1.  O servidor de backup começa a restaurar os dados apropriados do repositório de armazenamento.
2.  O agente de backup recebe os dados do servidor de reserva.
3.  O servidor de reserva termina o trabalho de restauro.

## <a name="deploy-the-solution"></a>Implementar a solução

A implantação da solução requer três etapas:
1. Prepare a infraestrutura de rede.
2. Desloque o seu dispositivo StorSimple como alvo de reserva.
3. Implementar executivo de backup.

Cada passo é discutido em detalhe nas seguintes secções.

### <a name="set-up-the-network"></a>Configurar a rede

Como o StorSimple é uma solução integrada com a nuvem Azure, a StorSimple requer uma ligação ativa e de trabalho à nuvem Azure. Esta ligação é usada para operações como instantâneos em nuvem, transferência de gestão e metadados, e para tier mais antigo, dados menos acedidos para armazenamento em nuvem Azure.

Para que a solução funcione da melhor forma, recomendamos que siga estas boas práticas de networking:

-   O link que liga o seu tiering StorSimple ao Azure deve satisfazer os seus requisitos de largura de banda. Para isso, aplique o nível de Qualidade de Serviço (QoS) necessário aos seus interruptores de infraestrutura para combinar com os seus SLAs de RPO e tempo de recuperação (RTO).
-   As latências máximas de acesso ao armazenamento Azure Blob devem rondar os 80 ms.

### <a name="deploy-storsimple"></a>Implementar StorSimple

Para obter uma orientação de implementação passo a passo StorSimple, consulte [implementar o seu dispositivo StorSimple no local](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-backup-exec"></a>Implementar executivos de backup

Para obter as melhores práticas de instalação do Backup Exec, consulte [as melhores práticas para a instalação do Backup Exec](https://www.veritas.com/content/support/en_US/doc/72686287-131623464-0/v70444238-131623464).

## <a name="set-up-the-solution"></a>Configurar a solução

Nesta secção, demonstramos alguns exemplos de configuração. Os exemplos e recomendações que se seguem ilustram a implementação mais básica e fundamental. Esta implementação pode não se aplicar diretamente aos seus requisitos específicos de backup.

### <a name="set-up-storsimple"></a>Configurar storSimple

| Tarefas de implantação StorSimple  | Comentários adicionais |
|---|---|
| Coloque o dispositivo StorSimple no local. | Versões suportadas: Atualização 3 e versões posteriores. |
| Liga o alvo de reserva. | Utilize estes comandos para ligar ou desligar o modo alvo de backup e para obter o estado. Para obter mais informações, consulte [Ligar-se remotamente a um dispositivo StorSimple](storsimple-remote-connect.md).</br> Para ligar o modo de backup: `Set-HCSBackupApplianceMode -enable` . </br> Para desligar o modo de backup: `Set-HCSBackupApplianceMode -disable` . </br> Para obter o estado atual das definições do modo de backup: `Get-HCSBackupApplianceMode` . |
| Crie um recipiente de volume comum para o seu volume que armazena os dados de backup. Todos os dados num recipiente de volume são desduplicados. | Os recipientes de volume StorSimple definem domínios de desduplicação.  |
| Criar volumes StorSimple. | Crie volumes com tamanhos o mais próximo possível da utilização prevista, porque o tamanho do volume afeta o tempo de duração do instantâneo em nuvem. Para obter informações sobre como dimensionar um volume, leia sobre [as políticas de retenção.](#retention-policies)</br> </br> Utilize volumes hierárquicos StorSimple e selecione o Use this volume para uma caixa de verificação de **dados de arquivo com menos acesso frequente.** </br> A utilização de apenas volumes fixados localmente não é suportada. |
| Crie uma política de backup StorSimple única para todos os volumes de backup alvo. | Uma política de backup StorSimple define o grupo de consistência do volume. |
| Desative o horário à medida que as imagens expiram. | As imagens são ativadas como uma operação pós-processamento. |

### <a name="set-up-the-host-backup-server-storage"></a>Configurar o armazenamento do servidor de backup do anfitrião

Configurar o armazenamento do servidor de backup do anfitrião de acordo com estas diretrizes:  

- Não utilize volumes extensão (criados pela Gestão do Disco do Windows). Os discos de extensão não são suportados.
- Formate os seus volumes utilizando o NTFS com um tamanho de atribuição de 64 KB.
- Mapear os volumes StorSimple diretamente para o servidor Backup Exec.
    - Utilize o iSCSI para servidores físicos.
    - Utilize discos de passagem para servidores virtuais.

## <a name="best-practices-for-storsimple-and-backup-exec"></a>Boas práticas para StorSimple e Backup Exec

Desacione a sua solução de acordo com as diretrizes nas seguintes secções.

### <a name="operating-system-best-practices"></a>Melhores práticas do sistema operativo

- Desative a encriptação e a desduplica do Windows Server para o sistema de ficheiros NTFS.
- Desativar a desfragmentação do Servidor do Windows nos volumes StorSimple.
- Desativar a indexação do Servidor do Windows nos volumes StorSimple.
- Esclipire no hospedeiro de origem (não contra os volumes StorSimple).
- Desligue a [manutenção](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) padrão do Servidor do Windows no Gestor de Tarefas. Faça-o de uma das seguintes formas:
  - Desligue o configurador de manutenção no Programador de Tarefas do Windows.
  - Descarregue [o PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) a partir de Sysinternals windows. Depois de descarregar o PsExec, executar a Azure PowerShell como administrador e digite:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>StorSimple boas práticas

  -   Certifique-se de que o dispositivo StorSimple é atualizado para [a Atualização 3 ou posterior](storsimple-install-update-3.md).
  -   Isolar o iSCSI e o tráfego na nuvem. Utilize ligações iSCSI dedicadas para o tráfego entre o StorSimple e o servidor de backup.
  -   Certifique-se de que o seu dispositivo StorSimple é um alvo de reserva dedicado. As cargas de trabalho mistas não são suportadas porque afetam o seu RTO e RPO.

### <a name="backup-exec-best-practices"></a>Backup Exec boas práticas

-   O Backup Exec deve ser instalado numa unidade local do servidor e não num volume StorSimple.
-   Descreva as operações de **escrita simultâneas** de backup para o máximo permitido.
    -   Descreva o bloco de armazenamento do Backup Exec **e o tamanho do tampão** para 512 KB.
    -   Ligue o backup Exec armazenamento **buffered ler e escrever**.
-   StorSimple suporta backups completos e incrementais do Backup Exec. Recomendamos que não utilize cópias de segurança sintéticas e diferenciais.
-   Os ficheiros de dados de backup devem conter apenas dados para um trabalho específico. Por exemplo, não são permitidos apêndices de meios de comunicação em diferentes postos de trabalho.
-   Desativar a verificação do emprego. Se necessário, a verificação deve ser agendada após o último trabalho de reserva. É importante entender que este trabalho afeta a sua janela de reserva.
-   Selecione **armazenamento**As propriedades de detalhes  >  **do**  >  **Details**  >  **Properties**disco. Desligue **o espaço do disco pré-alocar.**

Para obter as definições e as melhores práticas do Backup Exec para a implementação destes requisitos, consulte [o site da Veritas](https://www.veritas.com).

## <a name="retention-policies"></a>Políticas de retenção

Um dos tipos de política de retenção de backup mais comum é uma política de avô, pai e filho (GFS). Numa política GFS, uma cópia de segurança incremental é realizada diariamente e as cópias de segurança completas são feitas semanalmente e mensais. Esta política resulta em seis volumes tiered StorSimple. Um volume contém os backups semanais, mensais e anualmente completos. Os outros cinco volumes armazenam cópias de segurança incrementais diárias.

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

## <a name="set-up-backup-exec-storage"></a>Configurar o armazenamento do Backup Exec

### <a name="to-set-up-backup-exec-storage"></a>Para configurar o armazenamento do Backup Exec

1.  Na consola de gestão backup Exec, selecione **Storage**  >  **Configure Storage**  >  **Storage-Based Storage-Based Storage**  >  **Next**.

    ![Consola de gestão de backup Exec, página de armazenamento de configuração](./media/storsimple-configure-backup-target-using-backup-exec/image4.png)

2.  Selecione **o armazenamento do disco**e, em seguida, selecione **Seguinte**.

    ![Consola de gestão do Backup Exec, selecione a página de armazenamento](./media/storsimple-configure-backup-target-using-backup-exec/image5.png)

3.  Insira um nome representativo, por exemplo, **Saturday Full,** e uma descrição. Selecione **Seguinte**.

    ![Consola de gestão, nome e descrição do Backup Exec](./media/storsimple-configure-backup-target-using-backup-exec/image7.png)

4.  Selecione o disco onde pretende criar o dispositivo de armazenamento do disco e, em seguida, selecione **Seguinte**.

    ![Consola de gestão de backup Exec, página de seleção de discos de armazenamento](./media/storsimple-configure-backup-target-using-backup-exec/image9.png)

5.  Incremente o número de operações de escrita para **16**e, em seguida, selecione **Seguinte**.

    ![Consola de gestão de backup Exec, página de definições de operações de escrita simultânea](./media/storsimple-configure-backup-target-using-backup-exec/image10.png)

6.  Reveja as definições e, em seguida, **selecione Terminar**.

    ![Consola de gestão de backup Exec, página de resumo de configuração de armazenamento](./media/storsimple-configure-backup-target-using-backup-exec/image11.png)

7.  No final de cada atribuição de volume, altere as definições do dispositivo de armazenamento para corresponder às recomendadas nas [melhores práticas para StorSimple e Backup Exec](#best-practices-for-storsimple-and-backup-exec).

    ![Consola de gestão de backup Exec, página de definições de dispositivo de armazenamento](./media/storsimple-configure-backup-target-using-backup-exec/image12.png)

8.  Repita os passos 1-7 até terminar atribuindo os seus volumes StorSimple ao Backup Exec.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Configurar o StorSimple como um alvo de backup primário

> [!NOTE]
> Os dados restaurados a partir de uma cópia de segurança que foi nivelada para a nuvem ocorre a velocidades de nuvem.

A figura a seguir mostra o mapeamento de um volume típico para um trabalho de reserva. Neste caso, todos os backups semanais mapeiam para o disco completo de sábado, e o mapa de backups incrementais para Monday-Friday discos incrementais. Todos os backups e restauros são de um volume de nível StorSimple.

![Diagrama lógico de configuração do alvo de backup primário](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple como um exemplo de agenda GFS alvo de backup primário

Aqui está um exemplo de um horário de rotação GFS para quatro semanas, mensalmente, e anualmente:

| Tipo de frequência/backup | Completa | Incremental (dias 1-5)  |   
|---|---|---|
| Semanal (semanas 1-4) | Saturday | Monday-Friday |
| Mensalmente  | Saturday  |   |
| Anualmente | Saturday  |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>Atribuir volumes StorSimple a um trabalho de backup do Executivo de backup

A seguinte sequência pressupõe que o Backup Exec e o anfitrião-alvo estão configurados de acordo com as diretrizes do agente executivo de reserva.

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>Para atribuir volumes StorSimple a um trabalho de backup do Executivo de backup

1.  Na consola de gestão backup Exec, selecione Backup backup do **anfitrião**  >  **Backup**  >  **para o disco**.

    ![Consola de gestão backup Exec, selecione host, backup e backup para o disco](./media/storsimple-configure-backup-target-using-backup-exec/image14.png)

2.  Na caixa de diálogo **De Definição de Cópia de Segurança,** em **Cópia de Segurança,** selecione **Editar**.

    ![Consola de gestão de backup Exec, caixa de diálogo Backup Definition Properties](./media/storsimple-configure-backup-target-using-backup-exec/image15.png)

3.  Crie as suas cópias de segurança completas e incrementais para que cumpram os seus requisitos de RPO e RTO e cumpram as melhores práticas da Veritas.

4.  Na caixa de diálogo **'Opções de Cópia de Segurança',** selecione **Storage**.

    ![Consola de gestão de backup Exec, caixa de diálogo de armazenamento de opções de backup](./media/storsimple-configure-backup-target-using-backup-exec/image16.png)

5.  Atribua volumes StorSimple correspondentes ao seu horário de backup.

    > [!NOTE]
    > O tipo **de compressão** e **encriptação** está definido para **Nenhum**.

6.  Em **Verificar**, selecione os **dados não verifique os dados desta** caixa de verificação de trabalho. A utilização desta opção pode afetar o tiering StorSimple.

    > [!NOTE]
    > A desfragmentação, a indexação e a verificação de antecedentes afetam negativamente o tiering StorSimple.

    ![Consola de gestão backup Exec, Opções de Backup verificam definições](./media/storsimple-configure-backup-target-using-backup-exec/image17.png)

7.  Quando tiver configurado o resto das suas opções de backup para satisfazer os seus requisitos, selecione **OK** para terminar.

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Configurar o StorSimple como um alvo de backup secundário

> [!NOTE]
>Os dados são restaurados a partir de uma cópia de segurança que foi nivelada para a nuvem ocorrem a velocidades de nuvem.

Neste modelo, deve dispor de um meio de armazenamento (com além do StorSimple) para servir de cache temporário. Por exemplo, pode utilizar uma matriz redundante de discos independentes (RAID) para acomodar o espaço, a entrada/saída (I/O) e a largura de banda. Recomendamos a utilização de RAID 5, 50 e 10.

A seguinte figura mostra volumes típicos de retenção de curto prazo locais (para o servidor) e volumes de arquivos de retenção a longo prazo. Neste cenário, todas as cópias de segurança são executadas no volume RAID local (para o servidor). Estas cópias de segurança são periodicamente duplicadas e arquivadas num volume de arquivos. É importante dimensionar o volume RAID local (para o servidor) para que possa lidar com os seus requisitos de capacidade de retenção e desempenho de curto prazo.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple como um alvo secundário de backup GFS exemplo

![StorSimple como diagrama lógico de backup secundário](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetdiagram.png)

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


### <a name="assign-storsimple-volumes-to-a-backup-exec-archive-and-deduplication-job"></a>Atribuir volumes StorSimple a um arquivo executivo de backup e trabalho de desduplica

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-archive-and-duplication-job"></a>Para atribuir volumes StorSimple a um trabalho de arquivo e duplicação de backup

1.  Na consola de gestão backup Exec, clique com o botão direito no trabalho que pretende arquivar para um volume StorSimple e, em seguida, selecione **Backup Definition Properties**  >  **Edit**.

    ![Consola de gestão de backup Exec, separador Backup Definition Properties](./media/storsimple-configure-backup-target-using-backup-exec/image19.png)

2.  Selecione **Adicionar Duplicado de Fase**para  >  **Duplicate to Disk**  >  **Editar**O Disco .

    ![Backup Exec consola de gestão, adicionar estágio](./media/storsimple-configure-backup-target-using-backup-exec/image20.png)

3.  Na caixa de diálogo **'Opções Duplicadas',** selecione os valores que pretende utilizar para **Origem** e **Agendamento**.

    ![Consola de gestão backup Exec, propriedades de definições de backup e opções duplicadas](./media/storsimple-configure-backup-target-using-backup-exec/image21.png)

4.  Na lista de drop-down **de Armazenamento,** selecione o volume StorSimple onde pretende que o trabalho de arquivo armazene os dados.

    ![Screenshot que mostra a lista onde precisa de selecionar Armazenamento.](./media/storsimple-configure-backup-target-using-backup-exec/image22.png)

5.  **Selecione Verificar**e, em seguida, selecione os **dados não verifique os dados desta** caixa de verificação de trabalho.

    ![Screenshot que mostra onde seleciona a não verificação de dados para esta opção de trabalho.](./media/storsimple-configure-backup-target-using-backup-exec/image23.png)

6.  Selecione **OK**.

    ![Screenshot que mostra as propriedades de definição de backup.](./media/storsimple-configure-backup-target-using-backup-exec/image24.png)

7.  Na coluna **Backup,** adicione um novo estágio. Para a fonte, utilize **incremental**. Para o alvo, escolha o volume StorSimple onde o trabalho de backup incremental é arquivado. Repita os passos 1-6.

## <a name="storsimple-cloud-snapshots"></a>StorSimple fotos em nuvem

As imagens em nuvem StorSimple protegem os dados que residem no seu dispositivo StorSimple. Criar uma imagem em nuvem equivale a enviar fitas de backup locais para uma instalação fora do local. Se utilizar o armazenamento geo-redundante do Azure, criar uma imagem em nuvem equivale a enviar fitas de backup para vários sites. Se precisar restaurar um dispositivo após um desastre, pode trazer online outro dispositivo StorSimple e fazer uma falha. Após o failover, você seria capaz de aceder aos dados (a velocidades de nuvem) a partir do mais recente instantâneo em nuvem.

A secção seguinte descreve como criar um script curto para iniciar e eliminar imagens em nuvem StorSimple durante o pós-processamento de backup.

> [!NOTE]
> As imagens que são criadas manualmente ou programáticamente não seguem a política de expiração do instantâneo StorSimple. Estas imagens devem ser apagadas manualmente ou programáticamente.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Iniciar e apagar instantâneos em nuvem usando um script

> [!NOTE]
> Avalie cuidadosamente as repercussões de conformidade e retenção de dados antes de eliminar uma imagem StorSimple. Para obter mais informações sobre como executar um script pós-cópia de segurança, consulte a [documentação do Backup Exec](https://www.veritas.com/support/en_US/article.100032497.html).

### <a name="backup-lifecycle"></a>Ciclo de vida de reserva

![Diagrama de ciclo de vida de reserva](./media/storsimple-configure-backup-target-using-backup-exec/backuplifecycle.png)

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
4. Adicione o script ao seu trabalho de backup no Backup Exec editando os comandos de pré-processamento e pós-processamento das suas opções de trabalho do Backup Exec.

   ![Consola de backup Exec, opções de backup, separador de comandos pré e pós-processamento](./media/storsimple-configure-backup-target-using-backup-exec/image25.png)

> [!NOTE]
> Recomendamos que execute a sua política de backup de imagens de nuvem StorSimple como um script pós-processamento no final do seu trabalho diário de backup. Para obter mais informações sobre como fazer backup e restaurar o ambiente de aplicação de backup para ajudá-lo a conhecer o seu RPO e RTO, consulte o seu arquiteto de reserva.

## <a name="storsimple-as-a-restore-source"></a>StorSimple como fonte de restauro

Os restauros de um dispositivo StorSimple funcionam como restauros de qualquer dispositivo de armazenamento de blocos. A restauração de dados que é hierárquico para a nuvem ocorre a velocidades de nuvem. Para os dados locais, as restaurações ocorrem na velocidade do disco local do dispositivo. Para obter informações sobre como realizar uma restauração, consulte a documentação do Backup Exec. Recomendamos que esteja em conformidade com o Backup Exec restaurar as melhores práticas.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple failover e recuperação de desastres

> [!NOTE]
> Para cenários de backup alvo, o StorSimple Cloud Appliance não é suportado como um alvo de restauro.

Um desastre pode ser causado por uma variedade de fatores. A tabela que se segue lista cenários comuns de recuperação de desastres.

| Cenário | Impacto | Como recuperar | Notas |
|---|---|---|---|
| Falha do dispositivo StorSimple | As operações de backup e restauro são interrompidas. | Substitua o dispositivo falhado e execute [a storSimple failover e a recuperação de desastres](storsimple-device-failover-disaster-recovery.md). | Se necessitar de efetuar uma restauração após a recuperação do dispositivo, os conjuntos completos de trabalho de dados são recuperados da nuvem para o novo dispositivo. Todas as operações estão a velocidades de nuvem. O processo de indexação e catalogação de rescanning pode fazer com que todos os conjuntos de backup sejam digitalizados e puxados do nível de nuvem para o nível do dispositivo local, o que pode ser um processo demorado. |
| Falha do servidor do Backup Exec | As operações de backup e restauro são interrompidas. | Reconstruir o servidor de backup e executar a restauração da base de dados conforme detalhado em [Como fazer uma base de dados manual de Backup e Restaurar o Backup Exec (BEDB).](http://www.veritas.com/docs/000041083) | Tem de reconstruir ou restaurar o servidor Backup Exec no local de recuperação de desastres. Restaurar a base de dados até ao ponto mais recente. Se a base de dados do Backup Exec restaurada não estiver sincronizada com os seus últimos trabalhos de backup, é necessário indexar e catalogar. Este processo de rescaning de índice e catálogo pode fazer com que todos os conjuntos de backup sejam digitalizados e retirados do nível de nuvem para o nível do dispositivo local. Isto torna-o mais intensivo em tempo. |
| Falha no site que resulta na perda do servidor de backup e StorSimple | As operações de backup e restauro são interrompidas. | Restaurar o StorSimple primeiro e, em seguida, restaurar o Backup Exec. | Restaurar o StorSimple primeiro e, em seguida, restaurar o Backup Exec. Se necessitar de efetuar uma restauração após a recuperação do dispositivo, os conjuntos completos de trabalho de dados são recuperados da nuvem para o novo dispositivo. Todas as operações estão a velocidades de nuvem. |

## <a name="references"></a>Referências

Para este artigo foram referenciados os seguintes documentos:

- [StorSimple configuração de E/S multipata](storsimple-configure-mpio-windows-server.md)
- [Cenários de armazenamento: Provisão fina](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Usando unidades GPT](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Configurar cópias-sombra para pastas partilhadas](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como restaurar a [partir de um conjunto de backup](storsimple-restore-from-backup-set-u2.md).
- Saiba mais sobre como executar o [failover do dispositivo e a recuperação de desastres](storsimple-device-failover-disaster-recovery.md).
